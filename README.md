# GHOSTmine

Configure a door using your BBS' outbound RLOGIN capability or use the `rlogin-connect` application below.

## rlogin-connect
The program can be obtained from `https://github.com/apamment/rlogin-connect`. Clone the repository and then build it.

### Build instructions
- cmake
- make

### Usage
Create a `launch.sh` file and do chmod +x on it.

```
#!/bin/bash

# This script is used to launch a remote connection to a BBS using the rlogin-connect client

export TERM=ansi-bbs

NODE_NUMBER=$1            # pass the node number from your BBS
URL="ghost-mine.com"
PORT="3513"
BBS_TAG="SJK"             # CHANGE to your BBS tag
DOOR_CODE="XTRN=CODE"     # CHANGE "CODE" to a valid door code, or disable to land user at menu menu

# CHANGE to the path to your BBS' DOOR32.SYS file
DOOR32_SYS_PATH=/talisman/temp/${NODE_NUMBER}/door32.sys

# Extract the alias from line 7, replace spaces with underscores, and remove trailing whitespace/newlines
USER_ALIAS=$(sed -n '7p' "$DOOR32_SYS_PATH" | tr ' ' '-' | tr -d '\r' | awk '{$1=$1};1')

# Prepend the BBS_TAG with brackets to the username
USER_ALIAS="[${BBS_TAG}]${USER_ALIAS}"

# Define the password explicitly
PASSWORD="gh0stM1nE677!"

# CHANGE to path of rlogin-connect
CMD="/talisman/doors/rlogin/rlogin-connect -H $URL -P $PORT -R $USER_ALIAS"

# Add arguments
[ -n "$PASSWORD" ] && CMD="$CMD -L $PASSWORD"
[ -n "$DOOR_CODE" ] && CMD="$CMD -T $DOOR_CODE"

# Execute the command
$CMD
```



