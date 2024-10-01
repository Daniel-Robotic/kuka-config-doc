# Монтирование HDD/SSD

Если ваш жесткий диск постоянно требует монтирования при работе в Ubuntu, 
это может быть связано с тем, что он не настроен для автоматического монтирования при загрузке системы. 
Чтобы настроить автоматическое монтирование диска, вы можете использовать файл `/etc/fstab`. 

Определим UUID диска, для этого выполним команду:
```Bash
sudo blkid

# Output(example)
/dev/nvme0n1p2: UUID="a433bb63-1b12-4cc1-86d0-7b19ae0d51fd" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c79d9691-6086-4491-a788-18f54d51057f"
/dev/loop1: TYPE="squashfs"
/dev/nvme0n1p1: UUID="DBC9-8460" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="cc9c0d8e-f1a7-4ba8-93ce-c3f81444edb8"
/dev/loop6: TYPE="squashfs"
/dev/loop4: TYPE="squashfs"
/dev/loop2: TYPE="squashfs"
/dev/loop0: TYPE="squashfs"
/dev/loop7: TYPE="squashfs"
/dev/sda: LABEL="/home" UUID="2bf4877e-7c74-4bb7-b6ca-2ab2861412e5" BLOCK_SIZE="4096" TYPE="ext4"
/dev/loop5: TYPE="squashfs"
/dev/loop3: TYPE="squashfs"
```

Выберите место, где вы хотите монтировать диск. Например, `/mnt/bigdata_disk`
```Bash
sudo mkdir -p /mnt/bigdata_disk
```

Откройте файл `/etc/fstab` в текстовом редакторе с правами суперпользователя:
```Bash
sudo nano /etc/fstab
```

Добавьте новую строку в конец файла, используя следующий формат:
```
UUID=ваш_UUID /mnt/mydisk тип_файловой_системы опции 0 2
```

Например, если ваш UUID `1234-5678` и тип файловой системы `ext4`, строка будет выглядеть так:
```
UUID=1234-5678 /mnt/bigdata_disk ext4 defaults 0 2
```

Сохраните изменения и закройте редактор.
В **nano** нажмите **Ctrl+O** для сохранения и **Ctrl+X** для выхода.

Выполните команду:
```Bash
sudo mount -a
```

Если команда выполнена без ошибок, диск будет автоматически монтироваться при следующей загрузке системы.

Теперь ваш жесткий диск должен автоматически монтироваться при загрузке Ubuntu. 
Если у вас возникнут проблемы, убедитесь, что UUID и тип файловой системы указаны правильно.