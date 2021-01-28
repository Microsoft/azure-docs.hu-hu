---
title: Oracle-adatbázis létrehozása Azure-beli virtuális gépen | Microsoft Docs
description: Gyorsan beszerezhet egy Oracle Database 12c-adatbázist az Azure-környezetben.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: d16153a7dc9a3164a5127b80a474bf9c398684ac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945145"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Oracle Database létrehozása Azure-beli virtuális gépen

Ez az útmutató részletesen ismerteti, hogyan helyezhet üzembe egy Azure-beli virtuális gépet az Oracle Marketplace katalógusból a [lemezképből](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) egy Oracle 12c-adatbázis létrehozásához az Azure CLI használatával. A kiszolgáló üzembe helyezését követően SSH-kapcsolaton keresztül fog csatlakozni az Oracle-adatbázis konfigurálásához. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Virtuális gép (VM) létrehozásához használja az az [VM Create](/cli/azure/vm) parancsot. 

Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre. Emellett SSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcs helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a értékét `publicIpAddress` . Ennek a címnek a használatával férhet hozzá a virtuális géphez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Ha SSH-munkamenetet szeretne létrehozni a virtuális géppel, használja a következő parancsot. Cserélje le az IP-címet a `publicIpAddress` virtuális gép értékére.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle-szoftver már telepítve van a Piactéri rendszerképre. Hozzon létre egy minta-adatbázist az alábbiak szerint. 

1.  Váltson az *Oracle* -felhasználóra, majd indítsa el az Oracle-figyelőt:

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    A kimenet a következő példához hasonló:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```
2. Adatkönyvtár létrehozása az Oracle-adatfájlokhoz

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Hozza létre az adatbázist:

    ```bash
    dbca -silent \
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
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Az adatbázis létrehozása néhány percet vesz igénybe.

    A következőhöz hasonló kimenet jelenik meg:

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

4. Oracle-változók beállítása

    A kapcsolódás előtt két környezeti változót kell beállítania: *ORACLE_HOME* és *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    Emellett ORACLE_HOME és ORACLE_SID változókat is hozzáadhat a. bashrc fájlhoz. Ez a jövőbeli bejelentkezések környezeti változóit fogja menteni. Győződjön meg arról, hogy a következő utasítások lettek hozzáadva a `~/.bashrc` fájlhoz az Ön által választott szerkesztőprogram használatával.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
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

3. Nyissa meg a tároló PDB1, ha még nincs megnyitva, de először ellenőrizze az állapotot:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    A kimenet a következő példához hasonló:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Ha a OPEN_MODE `PDB1` nem ÍRHATÓ írás, akkor futtassa az alábbi parancsokat a PDB1 megnyitásához:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Be kell írnia a `quit` SQLPlus-munkamenet befejezéséhez, és be kell írnia `exit` az Oracle-felhasználó kijelentkezését.

## <a name="automate-database-startup-and-shutdown"></a>Adatbázis indításának és leállításának automatizálása

Az Oracle-adatbázis alapértelmezés szerint nem indul el automatikusan a virtuális gép újraindításakor. Az Oracle-adatbázis automatikus indításának beállításához először jelentkezzen be root-ként. Ezután hozzon létre és frissítsen néhány rendszerfájlt.

1. Bejelentkezés root-ként

    ```bash
    sudo su -
    ```

2.  A kedvenc szerkesztője segítségével szerkessze a fájlt, `/etc/oratab` és módosítsa az alapértelmezett értéket `N` `Y` :

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Hozzon létre egy nevű fájlt `/etc/init.d/dbora` , és illessze be a következő tartalmakat:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

## <a name="open-ports-for-connectivity"></a>Portok megnyitása a kapcsolathoz

A végső feladat egy külső végpontok konfigurálása. A virtuális gépet védő Azure hálózati biztonsági csoport beállításához először lépjen ki az SSH-munkamenetből a virtuális gépen (az előző lépésben újraindításkor az SSH-val kell kiindulni). 

1.  Az Oracle-adatbázis távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule) paranccsal a következőképpen: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Az Oracle EM Express távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule) paranccsal a következőképpen:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Ha szükséges, a virtuális gép nyilvános IP-címét az [az Network Public-IP show](/cli/azure/network/public-ip) paranccsal szerezheti be a következő módon:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Az EM Express csatlakoztatható a böngészőből. Győződjön meg arról, hogy a böngésző kompatibilis az EM Express szolgáltatással (Flash install szükséges): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Jelentkezzen be a **sys** -fiók használatával, és jelölje be a **as SYSDBA** jelölőnégyzetet. Használja a telepítés során beállított jelszó **OraPasswd1** . 

![Az Oracle OEM Express bejelentkezési oldalának képernyőképe](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az Azure-beli első Oracle-adatbázis vizsgálatát, és a virtuális gép már nincs rá szükség, az az [Group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az Azure-beli egyéb [Oracle-megoldásokkal](./oracle-overview.md). 

Próbálja ki az [Oracle automatizált Storage kezelési oktatóanyag telepítését és konfigurálását](configure-oracle-asm.md) .
