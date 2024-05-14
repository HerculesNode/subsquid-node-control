```shell
#!/bin/bash

# Kullanılacak klasör
TARGET_DIRECTORY="/root/sub_data"

# Sunucudaki mevcut betik dosyası
LOCAL_SCRIPT_FILE="run_worker.sh"

# Kontrol etmek istediğiniz URL
REMOTE_SCRIPT_URL="https://cdn.subsquid.io/worker/run_worker.sh"

# Yeni betik dosyasını geçici olarak indir
TEMP_REMOTE_SCRIPT=$(mktemp)
curl -s "$REMOTE_SCRIPT_URL" -o "$TEMP_REMOTE_SCRIPT"

# Mevcut betik ile indirilen betik arasındaki farkı kontrol et
if cmp -s "$LOCAL_SCRIPT_FILE" "$TEMP_REMOTE_SCRIPT"; then
    echo "Betikte değişiklik yok, işlem yapmaya gerek yok."
else
    echo "Betikte değişiklik var!"
    # Yeni betiği çalıştırmak için mevcut betiği güncelle
    mv "$TEMP_REMOTE_SCRIPT" "$LOCAL_SCRIPT_FILE"
    chmod +x "$LOCAL_SCRIPT_FILE"

    # Belirtilen komutları çalıştır
    ./"$LOCAL_SCRIPT_FILE" "$TARGET_DIRECTORY" down
    ./"$LOCAL_SCRIPT_FILE" "$TARGET_DIRECTORY" up -d
fi

# Geçici dosyayı sil
rm "$TEMP_REMOTE_SCRIPT"
```
