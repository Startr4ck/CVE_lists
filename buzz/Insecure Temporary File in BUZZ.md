## Description
Chidivilliams/buzz uses the unsafe deprecated function tempfile. mktem() in number of files
This is very unsafe because during the time between calling mktemp() and the first process attempting to create a file, different processes may create a file with this name.

﻿
Functions that create temporary file names (such as tempfile.mktemp()) are fundamentally insecure, as they do not ensure exclusive access to a file with the temporary name they return. The file name returned by these functions is guaranteed to be unique on creation but the file must be opened in a separate operation. There is no guarantee that the creation and open operations will happen atomically. This provides an opportunity for an attacker to interfere with the file before it is opened.
﻿
Use mkstemp() instead of tempfile.mktemp().

## Impact
Availability will get affected because of this vulnerability.
