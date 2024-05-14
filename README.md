# subsquid node version control

- this code checks for version changes. If the version has changed, it automatically downloads and runs the script. Checks every 48 hours automatically. 


### Links
 * [Hercules Telegram](https://t.me/HerculesNode)
 * [Hercules Twitter](https://twitter.com/Herculesnode)


## 🟢 How to use it
-  Open a new screen
-  Create a file named `subsquid_control.sh` and copy the following codes into it and save it.
-  TARGET_DIRECTORY = specify your folder path


## 1-

```shell
screen -S subcontrol
```

## 2-

```shell
nano subsquid_control.sh
```

## 3-

```shell
#!/bin/bash

# Specify your folder path. If it is the same, do not change it.
TARGET_DIRECTORY="/root/sub_data"
LOCAL_SCRIPT_FILE="run_worker.sh"
REMOTE_SCRIPT_URL="https://cdn.subsquid.io/worker/run_worker.sh"
CURRENT_STATE_FILE=$(mktemp)
TEMP_REMOTE_SCRIPT=$(mktemp)

while true; do
   
    curl -s "$REMOTE_SCRIPT_URL" -o "$TEMP_REMOTE_SCRIPT"

    
    if cmp -s "$LOCAL_SCRIPT_FILE" "$TEMP_REMOTE_SCRIPT"; then
        echo "No change in the script."
    else
        echo "There's a change in the script!"
        echo "$(date): There is a change in the script, update is in progress. Please wait.." >> script.log

        
        mv "$TEMP_REMOTE_SCRIPT" "$LOCAL_SCRIPT_FILE"
        chmod +x "$LOCAL_SCRIPT_FILE"

        
        ./"$LOCAL_SCRIPT_FILE" "$TARGET_DIRECTORY" down
        ./"$LOCAL_SCRIPT_FILE" "$TARGET_DIRECTORY" up -d
    fi

    
    rm "$TEMP_REMOTE_SCRIPT"

    # 48 hours (172800 seconds) standby time
    for ((i=172800; i>0; i--)); do
        echo -ne "time remaining for control: $i seconds\033[0K\r"
        sleep 1
    done
    echo -ne "Remaining time: 0 seconds\033[0K\r"
    echo -e "\nHello, this code will run every 48 hours! HerculesNode"
done
```

```shell
chmod +x subsquid_control.sh
```

```shell
./subsquid_control.sh
```

