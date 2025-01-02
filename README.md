# GHOSTmine

GHOSTmine allows Bulletin Board System (BBS) operators to configure and use doors (external programs or games) with outbound RLOGIN capabilities. This setup enables seamless user connections to remote services from within the BBS environment.

## Prerequisites

Before setting up GHOSTmine, ensure the following tools are installed on your system:

- **Bash**: A Unix-compatible shell.
- **`sed`**: For text processing in scripts.
- **`awk`**: For text manipulation in scripts.
- **`rlogin-connect`**: A lightweight RLOGIN client (or use a BBS' built-in, outbound rlogin)

---

## Setting Up `rlogin-connect`

To enable RLOGIN connections, follow these steps:

### 1. Download `rlogin-connect`
Clone the repository:
```bash
git clone https://github.com/apamment/rlogin-connect.git
cd rlogin-connect
```

### 2. Build the Program
Ensure `cmake` and `make` are installed on your system. Then build the program:
```bash
cmake .
make
```

---

## Configuring the `launch.sh` Script

The `launch.sh` script is used to establish outbound RLOGIN connections. Follow the steps below to configure it:

### Create the Script

1. Create the script file:
   ```bash
   touch launch.sh
   chmod +x launch.sh
   ```

2. Edit `launch.sh` with the following content:
   ```bash
   #!/bin/bash

   # This script is used to launch a remote connection to a BBS using the rlogin-connect client

   export TERM=ansi-bbs

   NODE_NUMBER=$1            # Pass the node number from your BBS
   URL="ghost-mine.com"      # Remote server hostname
   PORT="3513"               # Remote server port
   BBS_TAG="SJK"             # Replace with your BBS tag
   DOOR_CODE="XTRN=CODE"     # Replace "CODE" with a valid door code or leave empty for the main menu

   # Path to the DOOR32.SYS file for your BBS
   DOOR32_SYS_PATH=/talisman/temp/${NODE_NUMBER}/door32.sys

   # Extract the alias from line 7, replace spaces with underscores, and remove trailing whitespace/newlines
   USER_ALIAS=$(sed -n '7p' "$DOOR32_SYS_PATH" | tr ' ' '-' | tr -d '\r' | awk '{$1=$1};1')

   # Prepend the BBS_TAG with brackets to the username
   USER_ALIAS="[${BBS_TAG}]${USER_ALIAS}"

   # Define the password explicitly
   PASSWORD="gh0stM1nE677!"  # Replace with a password to use with all your users for the remote server

   # Replace with path to the rlogin-connect binary
   CMD="/talisman/doors/rlogin/rlogin-connect -H $URL -P $PORT -R $USER_ALIAS"

   # Add arguments
   [ -n "$PASSWORD" ] && CMD="$CMD -L $PASSWORD"
   [ -n "$DOOR_CODE" ] && CMD="$CMD -T $DOOR_CODE"

   # Execute the command
   $CMD
   ```

---

## Parameters

### Script Variables
- **`NODE_NUMBER`**: Passed as an argument to identify the active node in your BBS system in order to find the drop file
- **`URL`**: Remote server hostname (default: `ghost-mine.com`).
- **`PORT`**: The port used for the RLOGIN connection (default: `3513`).
- **`BBS_TAG`**: A unique identifier for your BBS. Replace `"SJK"` with your BBS's specific tag.
- **`DOOR_CODE`**: Optional parameter for specifying a door code. Comment out this line to land at the main menu.
- **`DOOR32_SYS_PATH`**: Path to the `DOOR32.SYS` file used by your BBS. Update this to the correct path for your setup.
- **`PASSWORD`**: Password to set for the RLOGIN connection. Replace `"gh0stM1nE677!"` but don't change it!

---

## Running the Script

To connect to a remote service via RLOGIN:
```bash
./launch.sh <node_number>
```
Replace `<node_number>` with the actual node number from your BBS.

---

## Additional Notes

- Ensure `rlogin-connect` is in the same directory as `launch.sh` or provide its full path in the script.
- Customize the `URL`, `PORT`, `BBS_TAG`, and `DOOR_CODE` values based on your BBS configuration and remote server details.
- Verify the path to `DOOR32.SYS` matches your BBS setup.

---

## Troubleshooting

- **`rlogin-connect` not found**: Ensure the binary is in the specified path or add it to your system's `PATH`.
- **Connection timeout**: Verify the `URL` and `PORT` are correct and accessible from your BBS server.

---

For more details, visit the [GHOSTmine GitHub repository](https://github.com/robbiew/GHOSTmine).
