---
title: Oracle-adatbázis létrehozása Azure-beli virtuális gépen | Microsoft Docs
description: Gyorsan beszerezhet egy Oracle Database 12c-adatbázist az Azure-környezetben.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: ec6a8382e2c0ce2cb359a62dd3f80fc977c4b1c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674659"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Oracle Database létrehozása Azure-beli virtuális gépen

Ez az útmutató részletesen ismerteti, hogyan helyezhet üzembe egy Azure-beli virtuális gépet az Oracle Marketplace katalógusból a [lemezképből](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) egy Oracle 19c-adatbázis létrehozásához az Azure CLI használatával. A kiszolgáló üzembe helyezését követően SSH-kapcsolaton keresztül fog csatlakozni az Oracle-adatbázis konfigurálásához. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *RG-Oracle* nevű erőforráscsoportot a *eastus* helyen.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Virtuális gép (VM) létrehozásához használja az az [VM Create](/cli/azure/vm) parancsot. 

Az alábbi példa egy `vmoracle19c` nevű virtuális gépet hoz létre. Emellett SSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcs helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a értékét `publicIpAddress` . Ennek a címnek a használatával férhet hozzá a virtuális géphez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Új lemez létrehozása és csatlakoztatása Oracle-adatfájlokhoz és FRA-hoz

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Portok megnyitása a kapcsolathoz
Ebben a feladatban konfigurálnia kell néhány külső végpontot az adatbázis-figyelőhöz és az EM expresshez, amelyet a virtuális gépet védő Azure hálózati biztonsági csoport beállításával kell használni. 

1. Az Oracle-adatbázis távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a következőképpen:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Az Oracle EM Express távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az az Network NSG Rule Create paranccsal a következőképpen:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Ha szükséges, a virtuális gép nyilvános IP-címét az az Network Public-IP show paranccsal szerezheti be a következő módon:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>A VM-környezet előkészítése

1. Kapcsolódás a virtuális géphez

   Ha SSH-munkamenetet szeretne létrehozni a virtuális géppel, használja a következő parancsot. Cserélje le az IP-címet a `publicIpAddress` virtuális gép értékére.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Váltás a root felhasználóra

   ```bash
   sudo su -
   ```

3. Az Oracle-adatfájlok tárolásához használni kívánt legutóbb létrehozott lemez-eszköz keresése

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   A kimenet a következőhöz hasonló lesz:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formázza az eszközt. 
   Mivel a root felhasználó az eszközön fut, 
   
   Először hozzon létre egy lemez címkét:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Ezután hozzon létre egy elsődleges partíciót, amely a teljes lemezt felöleli:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Végül a metaadatok kinyomtatásával keresse meg az eszköz adatait:
   ```bash
   parted /dev/sdc print
   ```
   A kimenetnek a következőképpen kell kinéznie:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Fájlrendszer létrehozása az eszköz partícióján

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Csatlakoztatási pont létrehozása
   ```bash
   mkdir /u02
   ```

7. A lemez csatlakoztatása

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Engedélyek módosítása a csatlakoztatási ponton

   ```bash
   chmod 777 /u02
   ```

9. Adja hozzá a csatlakoztatást az/etc/fstab fájlhoz. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Frissítse a ***/etc/hosts*** -fájlt a nyilvános IP-címmel és állomásnévvel.

    Módosítsa a ***nyilvános IP-címet és a VMname*** , hogy az tükrözze a tényleges értékeket:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Az állomásnév fájljának frissítése
    
    A következő parancs használatával adja hozzá a virtuális gép tartománynevét a **/etc/hostname** -fájlhoz. Ez azt feltételezi, hogy létrehozta az erőforráscsoportot és a virtuális gépet a **eastus** régióban:
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Tűzfal portjainak megnyitása
    
    Mivel a SELinux alapértelmezés szerint engedélyezve van a Piactéri képen, meg kell nyitnia a tűzfalat a 1521-as és a Enterprise Manager Express port 5502-es verziójának forgalmához. Futtassa a következő parancsokat root felhasználóként:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle-szoftver már telepítve van a Piactéri rendszerképre. Hozzon létre egy minta-adatbázist az alábbiak szerint. 

1.  Váltson az **Oracle** -felhasználóra:

    ```bash
    $ sudo su - oracle
    ```
2. Az adatbázis-figyelő elindítása

   ```bash
   $ lsnrctl start
   ```
   A kimenet a következő példához hasonló:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Hozzon létre egy adatkönyvtárat az Oracle-adatfájlokhoz:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Az adatbázis-létrehozási segéd futtatása:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Az adatbázis létrehozása néhány percet vesz igénybe.

    A következőhöz hasonló kimenet jelenik meg:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Oracle-változók beállítása

    A kapcsolódás előtt be kell állítania a környezeti változót *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    A következő parancs használatával adja hozzá a ORACLE_SID változót a `oracle` felhasználói `.bashrc` fájlhoz a jövőbeli bejelentkezésekhez:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-kapcsolat

A grafikus felhasználói felülettel rendelkező felügyeleti eszközökhöz, amelyekkel megtekintheti az adatbázist, beállíthatja az Oracle EM Expresst. Az Oracle EM expresshez való csatlakozáshoz először be kell állítania a portot az Oracle-ben. 

1. Kapcsolódjon az adatbázishoz a SQLPlus használatával:

    ```bash
    sqlplus sys as sysdba
    ```

2. Ha csatlakoztatva van, állítsa be az 5502-es portot a EM expresshez.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Az EM Express csatlakoztatható a böngészőből. Győződjön meg arról, hogy a böngésző kompatibilis az EM Express szolgáltatással (Flash install szükséges): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Jelentkezzen be a **sys** -fiók használatával, és jelölje be a **as SYSDBA** jelölőnégyzetet. Használja a telepítés során beállított jelszó **OraPasswd1** . 

    ![Az Oracle OEM Express bejelentkezési oldalának képernyőképe](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Adatbázis indításának és leállításának automatizálása

Az Oracle-adatbázis alapértelmezés szerint nem indul el automatikusan a virtuális gép újraindításakor. Az Oracle-adatbázis automatikus indításának beállításához először jelentkezzen be root-ként. Ezután hozzon létre és frissítsen néhány rendszerfájlt.

1. Bejelentkezés root-ként

    ```bash
    sudo su -
    ```

2.  A következő parancs futtatásával módosítsa az automatikus indítási jelzőt `N` a `Y` `/etc/oratab` fájlból a fájlba:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Hozzon létre egy nevű fájlt `/etc/init.d/dbora` , és illessze be a következő tartalmakat:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Módosítsa a fájlok engedélyeit a *chmod* paranccsal a következőképpen:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Hozzon létre szimbolikus hivatkozásokat az indításhoz és a leállításhoz az alábbiak szerint:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  A módosítások teszteléséhez indítsa újra a virtuális gépet:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az Azure-beli első Oracle-adatbázis vizsgálatát, és a virtuális gép már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan védhető az Azure-beli adatbázis az [Oracle Backup-stratégiákkal](./oracle-database-backup-strategies.md)

Ismerkedjen meg az Azure-beli egyéb [Oracle-megoldásokkal](./oracle-overview.md). 

Próbálja ki az [Oracle automatizált Storage kezelési oktatóanyag telepítését és konfigurálását](configure-oracle-asm.md) .
