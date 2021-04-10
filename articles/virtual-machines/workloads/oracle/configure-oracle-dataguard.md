---
title: Oracle-adatvédelem megvalósítása Azure Linux rendszerű virtuális gépen | Microsoft Docs
description: Gyorsan lekérheti az Oracle-adatvédelmet az Azure-környezetben.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 1b04ef24ff01787c6904db0e288c23d4434e7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673820"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Oracle-adatvédelem megvalósítása Azure Linux rendszerű virtuális gépen 

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a cikk azt ismerteti, hogyan használható az Azure CLI egy Oracle Database 12c-adatbázis üzembe helyezéséhez az Azure Marketplace-rendszerképből. Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja az adatvédelmet egy Azure-beli virtuális gépen (VM).

Mielőtt elkezdené, győződjön meg arról, hogy az Azure CLI telepítve van. További információ: az [Azure CLI telepítési útmutatója](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése
### <a name="assumptions"></a>Feltételezések

Az Oracle-adatvédelem telepítéséhez két Azure-beli virtuális gépet kell létrehoznia ugyanazon rendelkezésre állási csoporton belül:

- Az elsődleges virtuális gép (myVM1) egy futó Oracle-példánnyal rendelkezik.
- A készenléti virtuális gépen (myVM2) csak az Oracle-szoftverek vannak telepítve.

A virtuális gépek létrehozásához használt Piactéri lemezkép Oracle: Oracle-Database-EE: 12.1.0.2: Latest.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az az [login](/cli/azure/reference-index) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik. 

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

A rendelkezésre állási csoport létrehozása nem kötelező, de ajánlott. További információ: az [Azure rendelkezésre állási készletek irányelveinek útmutatója](/previous-versions/azure/virtual-machines/windows/infrastructure-example).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm) paranccsal. 

A következő példa két virtuális gépet hoz létre `myVM1` , és a nevet `myVM2` . Emellett SSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcs helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

MyVM1 létrehozása (elsődleges):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a értékét `publicIpAddress` . Ennek a címnek a használatával férhet hozzá a virtuális géphez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

MyVM2 létrehozása (készenlét):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

A myVM2 létrehozása után jegyezze fel a értékét `publicIpAddress` .

### <a name="open-the-tcp-port-for-connectivity"></a>A TCP-port megnyitása a kapcsolathoz

Ez a lépés külső végpontokat konfigurál, amelyek lehetővé teszik a távoli hozzáférést az Oracle-adatbázishoz.

Nyissa meg a myVM1 portját:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Nyissa meg a myVM2 portját:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le az IP-címet a `publicIpAddress` virtuális gép értékére.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Adatbázis létrehozása a myVM1 (elsődleges)

Az Oracle-szoftver már telepítve van a Piactéri rendszerképre, ezért a következő lépés az adatbázis telepítése. 

Váltson az Oracle-adminisztrátorra:

```bash
$ sudo su - oracle
```

Hozza létre az adatbázist:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

A kimeneteknek a következőhöz hasonlóan kell kinézniük:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

ORACLE_SID és ORACLE_HOME változók beállítása:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Szükség esetén ORACLE_HOME és ORACLE_SID is hozzáadhat a/Home/Oracle/.bashrc-fájlhoz, így ezek a beállítások a jövőbeli bejelentkezésekhez lesznek mentve:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Az adatvédelem konfigurálása

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Archiválási napló üzemmódjának engedélyezése a myVM1 (elsődleges)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```

Engedélyezze a kényszerített naplózást, és győződjön meg arról, hogy legalább egy naplófájl létezik:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Hozzon létre készenléti visszavonási naplókat, és állítsa be ugyanazt a méretet és mennyiséget, mint az elsődleges adatbázis-visszaállítási naplók

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 200M;
```

A flashback bekapcsolása (ami sokkal könnyebbé teszi a helyreállítást) és a KÉSZENLÉTi \_ Fájlkezelés beállítása \_ automatikusra. Kilépés az SQL * Plus szolgáltatásból.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Szolgáltatás beállítása a myVM1 (elsődleges)

Szerkessze vagy hozza létre a tnsnames. ora fájlt, amely a $ORACLE _HOME \network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Szerkessze vagy hozza létre a figyelő. ora fájlt, amely a $ORACLE _HOME \network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Az adatőr-közvetítő engedélyezése:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

A figyelő elindítása:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Szolgáltatás beállítása a myVM2-on (készenlét)

SSH – myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Bejelentkezés Oracle-ként:

```bash
$ sudo su - oracle
```

Szerkessze vagy hozza létre a tnsnames. ora fájlt, amely a $ORACLE _HOME \network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Szerkessze vagy hozza létre a figyelő. ora fájlt, amely a $ORACLE _HOME \network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

A figyelő elindítása:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Az adatbázis visszaállítása myVM2 (készenléti állapotba)

Hozza létre a/tmp/initcdb1_stby. ora fájlt a következő tartalommal:

```bash
*.db_name='cdb1'
```

Mappák létrehozása:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Hozzon létre egy jelszavas fájlt:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Az adatbázis elindítása a myVM2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Állítsa vissza az adatbázist a Oláh Anna eszköz használatával:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Futtassa a következő parancsokat a Oláh Anna:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

A parancs befejezésekor a következőhöz hasonló üzeneteket kell látnia. Kilépés a Oláh Anna.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

Szükség esetén ORACLE_HOME és ORACLE_SID is hozzáadhat a/Home/Oracle/.bashrc-fájlhoz, így ezek a beállítások a jövőbeli bejelentkezésekhez lesznek mentve:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Az adatőr-közvetítő engedélyezése:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Adatőr-átvitelszervező konfigurálása a myVM1 (elsődleges)

Indítsa el az adatőr-kezelőt, és jelentkezzen be a SYS és a Password használatával. (Ne használja az operációs rendszer hitelesítését.) Hajtsa végre a következőket:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Tekintse át a konfigurációt:

```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Elvégezte az Oracle-adatvédelem beállítását. A következő szakasz bemutatja, hogyan tesztelheti a kapcsolatot, és hogyan válthat át.

### <a name="connect-the-database-from-the-client-machine"></a>Az adatbázis összekötése az ügyfélgépről

Frissítse vagy hozza létre az tnsnames. ora fájlt az ügyfélszámítógépen. Ez a fájl általában $ORACLE _HOME \network\admin.

Cserélje le az IP-címeket a `publicIpAddress` myVM1 és a myVM2 értékére:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Az SQL * Plus elindítása:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Az adatvédelemi konfiguráció tesztelése

### <a name="switch-over-the-database-on-myvm1-primary"></a>Váltás az adatbázison a myVM1 (elsődleges)

Váltás az elsődlegesről készenléti állapotra (cdb1 – cdb1_stby):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Most már csatlakozhat a készenléti adatbázishoz.

Az SQL * Plus elindítása:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Váltás az adatbázison a myVM2 (készenlét)

A váltáshoz futtassa a következő parancsot a myVM2:

```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Most már csatlakozhat az elsődleges adatbázishoz.

Az SQL * Plus elindítása:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Elvégezte a Oracle Linux adatvédelmek telepítését és konfigurálását.


## <a name="delete-the-virtual-machine"></a>Törölje a következő virtuális gépet:

Ha már nincs szüksége a virtuális gépre, a következő paranccsal távolíthatja el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
