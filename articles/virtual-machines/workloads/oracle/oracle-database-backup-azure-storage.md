---
title: Oracle Database 19c-adatbázis biztonsági mentése Azure-beli Linux rendszerű virtuális gépen a Oláh Anna és az Azure Storage szolgáltatással
description: Ismerje meg, hogyan készíthet biztonsági mentést egy Oracle Database 19c-adatbázisról az Azure Cloud Storage-ba.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670009"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Oracle Database 19c-adatbázis biztonsági mentése és helyreállítása Azure-beli linuxos virtuális gépen Azure Storage használatával

Ez a cikk bemutatja, hogyan használhatja az Azure Storage-t adathordozóként egy Azure-beli virtuális gépen futó Oracle-adatbázis biztonsági mentésére és visszaállítására. Az adatbázis biztonsági mentését az Oracle Oláh Anna használatával végezheti el az SMB protokoll használatával a virtuális géphez csatlakoztatott Azure file Storage-hoz. A biztonsági mentési adathordozók Azure Storage szolgáltatással való használata rendkívül költséghatékony és teljesíthető. A nagy méretű adatbázisok esetében azonban a Azure Backup jobb megoldást nyújt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy linuxos virtuális gépet, amelyen Oracle Database 19c telepített példánya van. A virtuális gép létrehozásához jelenleg használt Piactéri rendszerkép az  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Latest**. Az oktatóanyag elvégzéséhez kövesse az [Oracle Create Database](./oracle-database-quick-create.md) rövid útmutatójának lépéseit az Oracle-adatbázis létrehozásához.

## <a name="prepare-the-database-environment"></a>Az adatbázis-környezet előkészítése

1. Ha Secure Shell-(SSH-) munkamenetet szeretne létrehozni a virtuális géppel, használja a következő parancsot. Cserélje le az IP-címet és az állomásnév kombinációját a `publicIpAddress` virtuális gép értékére.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Váltson a ***root*** felhasználóra:
 
   ```bash
   sudo su -
   ```
    
3. Adja hozzá az Oracle-felhasználót a ***/etc/sudoers*** fájlhoz:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Ez a lépés azt feltételezi, hogy rendelkezik egy *vmoracle19c* nevű virtuális gépen futó Oracle-példánnyal (teszttel).

   Felhasználó váltása az *Oracle* -felhasználóra:

   ```bash
   sudo su - oracle
   ```
    
5. A kapcsolódás előtt be kell állítania a környezeti változót ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   A következő parancs használatával adja hozzá a ORACLE_SID változót a `oracle` felhasználói `.bashrc` fájlhoz a jövőbeli bejelentkezésekhez:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Indítsa el az Oracle-figyelőt, ha még nem fut:
    
   ```bash
   $ lsnrctl start
   ```

    A kimenetnek a következőképpen kell kinéznie:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  A gyors helyreállítási körzet (FRA) helyének létrehozása:

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Kapcsolódás az adatbázishoz:

    ```bash
    sqlplus / as sysdba
    ```

9.  Indítsa el az adatbázist, ha még nem fut:

    ```bash
    SQL> startup
    ```

10. Adatbázis-környezeti változók beállítása a gyors helyreállítási körzethez:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Az online biztonsági mentések engedélyezéséhez ellenőrizze, hogy az adatbázis archivált napló módban van-e.
    Először keresse meg a naplózási Archívum állapotát:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Ha NOARCHIVELOG módban van, futtassa a következő parancsokat a SQLPlus-ben:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Hozzon létre egy táblázatot a biztonsági mentési és visszaállítási műveletek teszteléséhez:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Biztonsági mentés Azure Files

A Azure Filesra történő biztonsági mentéshez hajtsa végre az alábbi lépéseket:

1. Az Azure File Storage beállítása.
1. Csatlakoztassa az Azure Storage-fájlmegosztást a virtuális géphez.
1. Az adatbázis biztonsági mentése.
1. Az adatbázis visszaállítása és helyreállítása.

### <a name="set-up-azure-file-storage"></a>Az Azure File Storage beállítása

Ebben a lépésben az Oracle-adatbázis biztonsági mentését az Oracle Recovery Manager (Oláh Anna) használatával végezheti el az Azure file Storage szolgáltatásban. Az Azure-fájlmegosztás teljes körűen felügyelt fájlmegosztás, amely a felhőben él. A kiszolgáló-üzenetblokk (SMB) protokoll vagy a hálózati fájlrendszer (NFS) protokoll használatával érhetők el. Ez a lépés egy olyan fájlmegosztás létrehozását ismerteti, amely az SMB protokollt használja a virtuális géphez való csatlakoztatáshoz. További információ az NFS használatával történő csatlakoztatásról: [blob Storage csatlakoztatása az nfs 3,0 protokoll használatával](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

A Azure Files csatlakoztatásakor a használatával `cache=none` letiltja a fájlmegosztás-adatmennyiség gyorsítótárazását. Továbbá annak érdekében, hogy a megosztásban létrehozott fájlok az Oracle-felhasználó tulajdonában legyenek, a és a beállítások is megadhatók `uid=oracle` `gid=oinstall` . 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Először állítsa be a Storage-fiókját.

1. File Storage konfigurálása a Azure Portal

    A Azure Portal válassza a ***+ erőforrás létrehozása** _ lehetőséget, és keresse meg és válassza ki az _ *_Storage-fiókot_**
    
    ![Képernyőkép, amely bemutatja, hol kell létrehozni egy erőforrást, és ki kell választania a Storage-fiókot.](./media/oracle-backup-recovery/storage-1.png)
    
2. A Storage-fiók létrehozása lapon válassza ki a meglévő erőforráscsoportot ***RG-Oracle** _, nevezze el a Storage-fiók _*_oracbkup1_*_ , és válassza a _*_Storage v2 (GeneralPurpose v2)_*_ fiókot a fiók típusa beállításnál. Módosítsa a replikációt _*_helyileg redundáns tárolóra (LRS)_*_ , és állítsa be a teljesítményt _ *_standard_* * értékre. Győződjön meg arról, hogy a hely ugyanahhoz a régióhoz van beállítva, mint az erőforráscsoport összes többi erőforrása. 
    
    ![Képernyőkép, amely bemutatja, hol válassza ki a meglévő erőforráscsoportot.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Kattintson a ***speciális** _ fülre, és a Azure Files a _*_nagyméretű fájlmegosztás_*_ beállítása _ *_engedélyezve_* * értékre. Kattintson a felülvizsgálat + Létrehozás elemre, majd a Létrehozás gombra.
    
    ![Képernyőfelvétel: a nagyméretű fájlmegosztás engedélyezésének helyét mutatja be.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. A Storage-fiók létrehozása után nyissa meg az erőforrást, és válassza a ***fájlmegosztás*** lehetőséget.
    
    ![Képernyőkép a fájlmegosztás kiválasztásának helyéről.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Kattintson a ***+ fájlmegosztás** _ elemre, majd az _*_új fájlmegosztás_*_ panelen nevezze el a fájlmegosztás _*_orabkup1_*_. Állítsa be a _*_kvótát_*_ _*_10240_*_ GIB értékre, és tekintse meg a szintként _*_optimalizált tranzakciót_*_ . A kvóta azt a felső határt tükrözi, amelyet a fájlmegosztás képes növelni. Mivel a standard szintű tárolást használjuk, az erőforrások TB, és nincs kiépítve, így a 10 TiB-ra való beállítás nem terheli a ténylegesen felhasznált költségeket. Ha a biztonsági mentési stratégiának több tárterületre van szüksége, a kvótát megfelelő szintre kell állítania az összes biztonsági mentés tárolásához.   Ha elvégezte az új fájlmegosztás panelt, kattintson az _ * Create * * (_Létrehozás_* *) elemre.
    
    ![Képernyőfelvétel: új fájlmegosztás hozzáadásának helye.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. A létrehozáskor kattintson a ***orabkup1*** elemre a fájlmegosztás beállításai lapon. 
    Kattintson a ***Kapcsolódás** _ lapra a kapcsolódás panel megnyitásához, majd kattintson a _ *_Linux_** fülre. Másolja a megadott parancsokat a fájlmegosztás SMB protokollal történő csatlakoztatásához. 
    
    ![Storage-fiók hozzáadása lap](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Storage-fiók és a fájlmegosztás beállításához futtassa az alábbi parancsokat az Azure CLI-ben.

1. Hozza létre a Storage-fiókot ugyanabban az erőforráscsoportban és helyen, mint a virtuális gép:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Fájlmegosztás létrehozása a Storage-fiókban 10 TiB-os kvótával:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Szerezze be a fájlmegosztást a virtuális géphez való csatlakoztatásakor szükséges key1 (Storage-fiók elsődleges kulcsát):

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Az Azure Storage-fájlmegosztás csatlakoztatása a virtuális géphez

1. A csatlakoztatási pont létrehozása:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Hitelesítő adatok beállítása:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   A `<Your Storage Account Key1>` korábban lekért Storage-fiók kulcsának helyettesítése a következő parancs futtatása előtt:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Módosítsa a hitelesítő adatok fájljának engedélyeit:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Adja hozzá a csatlakoztatást az/etc/fstab-hez:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Futtassa a parancsokat az Azure Files Storage SMB protokollal való csatlakoztatásához:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Ha a következőhöz hasonló hibaüzenetet kap:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   Előfordulhat, hogy a CIFS-csomag nem települ a Linux-gazdagépre. 
   
   A következő parancs futtatásával ellenőrizze, hogy telepítve van-e a CIS:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Ha a parancs nem ad vissza kimenetet, telepítse a CIFS-csomagot a következő parancs használatával:

   ```bash 
   sudo yum install cifs-utils
   ```

   Most futtassa újra a fenti csatlakoztatási parancsot a Azure Files Storage csatlakoztatásához.

6. Győződjön meg arról, hogy a fájlmegosztás megfelelően van csatlakoztatva a következő paranccsal:

   ```bash
   df -h
   ```

   A kimenetnek a következőképpen kell kinéznie:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Az adatbázis biztonsági mentése

Ebben a szakaszban Oracle Recovery Manager (Oláh Anna) használatával készítünk teljes biztonsági mentést az adatbázisról, és archiváljuk a naplókat, és a biztonsági mentést a korábban csatlakoztatott Azure-fájlmegosztás biztonsági mentési készletének megfelelően írjuk be. 

1. A Oláh Anna konfigurálása a Azure Files csatlakoztatási pontra történő biztonsági mentéshez:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Mivel az Azure standard fájlmegosztás maximális fájlmérete 1 TiB, a Oláh Anna biztonsági mentési darabok mérete 1 TiB-ra lesz korlátozva. (Vegye figyelembe, hogy a prémium szintű fájlmegosztás legfeljebb 4 TiB-os fájlméretet tartalmazhat. További információ: [Azure Files skálázhatósági és teljesítményi célok](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Erősítse meg a konfigurációs változás részleteit:

    ```bash
    RMAN> show all;
    ```

4. Most futtassa a biztonsági mentést. A következő parancs teljes adatbázis-biztonsági mentést készít, beleértve az archiválási naplófájlokat is, biztonságimásolat tömörített formátumban:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Az Azure file Storage-ban található biztonsági mentést követően már készített biztonsági másolatot az adatbázisról az Oracle Oláh Anna használatával. Ennek a módszernek az az előnye, hogy kihasználja a Oláh Anna funkcióit, miközben az Azure file Storage-ban tárolja a biztonsági mentéseket, ahol más virtuális gépekről is hozzáférhetnek, és ez akkor hasznos, ha az adatbázis klónozására van szükség.  
    
A Oláh Anna és az Azure file Storage használata az adatbázis biztonsági mentéséhez számos előnnyel jár, a biztonsági mentési és visszaállítási idő az adatbázis méretétől függ, így a nagyon nagy méretű adatbázisok esetében ez a művelet jelentős időt vehet igénybe.  Egy alternatív biztonsági mentési mechanizmus, amely Azure Backup alkalmazás-konzisztens virtuálisgép-biztonsági mentést használ, a pillanatkép-technológiát használja a biztonsági mentések elvégzésére, ami az adatbázis méretétől függetlenül nagyon gyors biztonsági mentés előnyeit is kihasználhatja. Az Recovery Services-tárolóval való integráció az Azure Cloud Storage-ban más virtuális gépekről és Azure-régiókból elérhető biztonsági másolatok biztonságos Oracle Database tárolását teszi lehetővé. 

### <a name="restore-and-recover-the-database"></a>Az adatbázis visszaállítása és helyreállítása

1.  Az Oracle-példány leállítása:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Távolítsa el az adatfájlokat és a biztonsági másolatokat:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. A következő parancsok a Oláh Anna használatával állítják vissza a hiányzó adatfájlokat, és helyreállítják az adatbázist:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Győződjön meg arról, hogy az adatbázis tartalma teljesen helyre lett állítva:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


A Oracle Database 19c-adatbázis biztonsági mentése és helyreállítása egy Azure Linux rendszerű virtuális gépen befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális gépre, a következő paranccsal távolíthatja el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)