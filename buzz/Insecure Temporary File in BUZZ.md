## Description
Chidivilliams/buzz uses the unsafe deprecated function tempfile. mktem() in number of files
This is very unsafe because during the time between calling mktemp() and the first process attempting to create a file, different processes may create a file with this name.

﻿
Functions that create temporary file names (such as tempfile.mktemp()) are fundamentally insecure, as they do not ensure exclusive access to a file with the temporary name they return. The file name returned by these functions is guaranteed to be unique on creation but the file must be opened in a separate operation. There is no guarantee that the creation and open operations will happen atomically. This provides an opportunity for an attacker to interfere with the file before it is opened.
﻿
## How to fix
Use mkstemp() instead of tempfile.mktemp().

## POC1  

unsecure Tempfile example
```
import os
import tempfile

# Use tempfile.mktemp() to generate a temporary file path
temp_path = tempfile.mktemp()
print(f"Generated temp file path: {temp_path}")

# Simulate the time window between generating the file path and creating the file,
# during which another program could create a file with the same name.
# An attacker might create a malicious file with the same path.

# The program then tries to use this path to create a file
with open(temp_path, 'w') as f:
    f.write("Sensitive data.")
```

## Impact
![image](https://github.com/user-attachments/assets/6408ce56-e454-42b4-96a9-87a505de0645)

Availability will get affected because of this vulnerability.
According to the code: https://github.com/chidiwilliams/buzz/blob/b35267d422dd0cfe6ea31c77987a9fa24cd87ed3/buzz/model_loader.py#L508
We can replace the evil model with the url model so that can be caused into code executed and  data leaks

## Refer 
[CodeRefer](https://github.com/chidiwilliams/buzz/blob/b35267d422dd0cfe6ea31c77987a9fa24cd87ed3/buzz/model_loader.py#L531)  

[VulnRefer](https://huntr.com/bounties/a3867b4e-6701-4418-8c20-3c6e7084a44a)
[BUZZ Origin issue](https://github.com/chidiwilliams/buzz/issues/949)
