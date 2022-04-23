# Форматирование диска в macOS
## ExFAT


По yмолчанию, при форматировании диска в ExFAT средствами macOS - Disk Utility, нет возможности указать размер кластера, что приводит к тому, что размер устанавливается чрезмерно большим. Размер кластера определяет минимальный размер блока данных на диске. Разница в размере кластера становится очевидной при размещении на диске большого количества файлов малого размера.


Альтернативным средством форматирования диска является cli утилита [newfs_exfat](https://www.unix.com/man-page/osx/8/NEWFS_EXFAT/).


Получение списка доступних дисковых устройств.
```bash
diskutil list
```

Пример вывода.
```bash
/dev/disk0 (internal):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                         500.3 GB   disk0
   1:             Apple_APFS_ISC                         524.3 MB   disk0s1
   2:                 Apple_APFS Container disk3         494.4 GB   disk0s2
   3:        Apple_APFS_Recovery                         5.4 GB     disk0s3

/dev/disk3 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +494.4 GB   disk3
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            15.2 GB    disk3s1
   2:              APFS Snapshot com.apple.os.update-... 15.2 GB    disk3s1s1
   3:                APFS Volume Preboot                 371.2 MB   disk3s2
   4:                APFS Volume Recovery                799.4 MB   disk3s3
   5:                APFS Volume Data                    228.9 GB   disk3s5
   6:                APFS Volume VM                      20.5 KB    disk3s6

/dev/disk4 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *240.1 GB   disk4
   1:                        EFI NO NAME                 536.9 MB   disk4s1
   2:       Microsoft Basic Data SSD_box                 239.5 GB   disk4s2
```

Получение информации о конкретном устройстве. В данном случае `/dev/disk4` - `SSD_box`
```bash
sudo newfs_exfat -N /dev/disk4
```

Пример вывода.
```bash
Partition offset : 0 sectors (0 bytes)
Volume size      : 468862128 sectors (240057409536 bytes)
Bytes per sector : 512
Bytes per cluster: 131072
FAT offset       : 2048 sectors (1048576 bytes)
# FAT sectors    : 14336
Number of FATs   : 1
Cluster offset   : 16384 sectors (8388608 bytes)
# Clusters       : 1831428
Volume Serial #  : 62642b5f
Bitmap start     : 2
Bitmap file size : 228929
Upcase start     : 4
Upcase file size : 5836
Root start       : 5
```

Данный диск отформатирован в ExFAT утилитой Disk Utility, и значение `Bytes per cluster` указывает на размер кластера, в данном случае он составляет 128 Килобайт.


Для форматирования диска в ExFAT но с принудительным определением размера кластера следует выполнить.
```bash
sudo newfs_exfat -b 4096 -v SSD_box /dev/disk4
```

Пример вывода резальтата.
```bash
Volume name      : SSD_box
Partition offset : 0 sectors (0 bytes)
Volume size      : 468862128 sectors (240057409536 bytes)
Bytes per sector : 512
Bytes per cluster: 4096
FAT offset       : 2048 sectors (1048576 bytes)
# FAT sectors    : 458752
Number of FATs   : 1
Cluster offset   : 460800 sectors (235929600 bytes)
# Clusters       : 58550166
Volume Serial #  : 62642cfb
Bitmap start     : 2
Bitmap file size : 7318771
Upcase start     : 1789
Upcase file size : 5836
Root start       : 1791
```

Параметр -b определяет размер кластера, цитата из официальной документации. Оптимальным, наиболее часто употребляемым, является размер в 4096 Байт.
```
-b bytes-per-cluster
	     File system block size (bytes per cluster).  Acceptable values are powers of 2 in the range 512 through 33554432.
```

Парметр -v определяет имя тома.
```
-v volume-name
	     Volume name (label).  The name will be converted to UTF-16, and must be no longer than 11 UTF-16 characters.  ASCII control charac-
	     ters and some punctuation characters are not allowed (similar to DOS 8.3-style names).  NOTE: The volume name may be an empty (zero-
	     length) string.
```

Полный перечень доступных параметров.
```
man newfs_exfat
```
