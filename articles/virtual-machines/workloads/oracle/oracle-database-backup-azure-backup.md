---
title: Oracle Database 19c-adatbázis biztonsági mentése és helyreállítása Azure-beli linuxos virtuális gépeken Azure Backup használatával
description: Megtudhatja, hogyan készíthet biztonsági másolatot egy Oracle Database 19c-adatbázisról a Azure Backup szolgáltatás használatával.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: ac045694e8975509635e03221a8cb9cc84446b55
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806409"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Oracle Database 19c-adatbázis biztonsági mentése és helyreállítása Azure-beli linuxos virtuális gépeken Azure Backup használatával

Ez a cikk Azure Backup használatát mutatja be a virtuálisgép-lemezek lemezes pillanatképének készítéséhez, beleértve az adatbázisfájlok és a gyors helyreállítási területet is. A Azure Backup használatával a [Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md)-tárolóban tárolt biztonsági másolatként használható teljes lemezes pillanatképeket készíthet.  A Azure Backup az alkalmazással konzisztens biztonsági mentéseket is biztosít, amelyek biztosítják, hogy a további javítások nem szükségesek az adatvisszaállításhoz. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.

> [!div class="checklist"]
>
> * Az adatbázis biztonsági mentése az alkalmazással konzisztens biztonsági mentéssel
> * Az adatbázis visszaállítása és helyreállítása helyreállítási pontról
> * A virtuális gép visszaállítása helyreállítási pontról

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy linuxos virtuális gépet, amelyen Oracle Database 19c telepített példánya van. A virtuális gép létrehozásához jelenleg használt Piactéri rendszerkép az  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Latest**. Az oktatóanyag elvégzéséhez kövesse az [Oracle Create Database](./oracle-database-quick-create.md) rövid útmutatójának lépéseit az Oracle-adatbázis létrehozásához.


## <a name="prepare-the-environment"></a>A környezet előkészítése

A környezet előkészítéséhez végezze el a következő lépéseket:

1. Csatlakozzon a virtuális géphez.
1. Készítse elő az adatbázist.

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. Ha Secure Shell-(SSH-) munkamenetet szeretne létrehozni a virtuális géppel, használja a következő parancsot. Cserélje le az IP-címet és az állomásnév kombinációját a `<publicIpAddress>` virtuális gép értékére.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Váltson a *root* felhasználóra:

   ```bash
   sudo su -
   ```
    
1. Adja hozzá az Oracle-felhasználót a */etc/sudoers* fájlhoz:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Az adatbázis előkészítése

Ez a lépés azt feltételezi, hogy rendelkezik egy *vmoracle19c* nevű virtuális gépen futó Oracle-példánnyal (*teszttel*).

1. Felhasználó váltása az *Oracle* -felhasználóra:
 
   ```bash
    sudo su - oracle
    ```
    
2. A kapcsolódás előtt be kell állítania a környezeti változót ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    A következő parancs használatával adja hozzá a ORACLE_SID változót a `oracle` felhasználói `.bashrc` fájlhoz a jövőbeli bejelentkezésekhez:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Indítsa el az Oracle-figyelőt, ha még nem fut:

    ```output
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

4.  A gyors helyreállítási körzet (FRA) helyének létrehozása:

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Kapcsolódás az adatbázishoz:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Indítsa el az adatbázist, ha még nem fut:

    ```bash
    SQL> startup
    ```

7.  Adatbázis-környezeti változók beállítása a gyors helyreállítási körzethez:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Az online biztonsági mentések engedélyezéséhez ellenőrizze, hogy az adatbázis archivált napló módban van-e.

    Először keresse meg a naplózási Archívum állapotát:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Ha NOARCHIVELOG módban van, futtassa a következő parancsokat:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Hozzon létre egy táblázatot a biztonsági mentési és visszaállítási műveletek teszteléséhez:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Konfigurálja a Oláh Anna a virtuális gép lemezén található gyors helyreállítási körzetre történő biztonsági mentéshez:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Erősítse meg a konfigurációs változás részleteit:

    ```bash
    RMAN> show all;
    ```    

12.  Most futtassa a biztonsági mentést. A következő parancs teljes adatbázis-biztonsági mentést készít, beleértve az archiválási naplófájlokat is, biztonságimásolat tömörített formátumban:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Az Azure Backup használata

Az Azure Backup szolgáltatás egyszerű, biztonságos és költséghatékony megoldásokat kínál adatai biztonsági mentéséhez és a Microsoft Azure-felhőből történő helyreállításához. A Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén egyszerűen visszaállíthatók.

A Azure Backup szolgáltatás olyan [keretrendszert](../../../backup/backup-azure-linux-app-consistent.md) biztosít a Windows és Linux rendszerű virtuális gépek biztonsági mentése során az alkalmazások konzisztenciájához, mint például az Oracle, a MySQL, a Mongo db, a SAP HANA és a PostGreSQL. Ebbe beletartozik egy előzetes parancsfájl meghívása (az alkalmazások fokozatos leválasztása), mielőtt pillanatképet készít a lemezekről, és meghívja az utólagos parancsfájlt (az alkalmazások feloldásához szükséges parancsokat) a pillanatkép befejezése után, hogy az alkalmazásokat a normál módba adja vissza. A minta előtti parancsfájlokat és a parancsfájlokat a GitHubon is elérhetővé teheti, így a parancsfájlok létrehozása és karbantartása az Ön felelőssége. 

Most Azure Backup egy továbbfejlesztett, előre megírt parancsfájlokat és parancsfájl-közzétételi keretrendszert biztosít, ahol a Azure Backup szolgáltatás a kiválasztott alkalmazásokhoz csomagolt előkészítő parancsfájlokat és parancsfájlok futtatását teszi lehetővé. Azure Backup felhasználó csak az alkalmazás nevét kell megadnia, majd az Azure virtuális gép biztonsági mentése automatikusan meghívja a megfelelő utólagos parancsfájlokat. A becsomagolt előzetes parancsfájlokat és a parancsfájlok utáni parancsfájlokat a Azure Backup csapata tartja karban, így a felhasználók biztosíthatják a parancsfájlok támogatását, tulajdonlását és érvényességét. Jelenleg a továbbfejlesztett keretrendszer támogatott alkalmazásai az *Oracle* és a *MySQL*.

Ebben a szakaszban Azure Backup továbbfejlesztett keretrendszert használ a futó virtuális gép és az Oracle-adatbázis alkalmazás-konzisztens pillanatképének készítéséhez. Az adatbázis biztonsági mentési módba kerül, amely lehetővé teszi a tranzakciós szempontból konzisztens online biztonsági mentést, miközben Azure Backup pillanatképet készít a virtuális gépek lemezéről. A pillanatkép a tárterület teljes másolata, és nem növekményes vagy másolási írási pillanatkép, ezért ez egy hatékony médium az adatbázis visszaállításához. A Azure Backup alkalmazás-konzisztens pillanatképek használatának előnye, hogy rendkívül gyorsan elvégezhetik az adatbázis nagy mennyiségét, és a pillanatképek a létrehozásuk után azonnal használhatók a visszaállítási műveletekhez anélkül, hogy meg kellene várni a Recovery Services-tárolóba való átvitelre.

Az adatbázis biztonsági mentésének Azure Backup használatához hajtsa végre a következő lépéseket:

1. A környezet előkészítése az alkalmazással konzisztens biztonsági mentésre.
1. Alkalmazás-konzisztens biztonsági másolatok beállítása.
1. Aktiválja a virtuális gép egy alkalmazással konzisztens biztonsági mentését.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>A környezet előkészítése az alkalmazással konzisztens biztonsági mentésre

1. Váltson a *root* felhasználóra:

   ```bash
   sudo su -
   ```

1. Új biztonsági mentési felhasználó létrehozása:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. A biztonsági mentési felhasználói környezet beállítása:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Külső hitelesítés beállítása az új biztonsági mentési felhasználóhoz. A biztonsági mentési felhasználónak külső hitelesítéssel kell tudnia hozzáférni az adatbázishoz, így nem kell jelszót feltennie.

   Először váltson vissza az *Oracle* -felhasználóra:

   ```bash
   su - oracle
   ```

   Jelentkezzen be az adatbázisba a SQLPlus használatával, és keresse meg a külső hitelesítés alapértelmezett beállításait:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   A kimenetnek az alábbi példához hasonlóan kell kinéznie: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Most hozzon létre egy adatbázis-felhasználói *azbackup* külsőleg, és adja meg a sysbackup jogosultságot:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Ha `ORA-46953: The password file is not in the 12.2 format.`  az utasítás futtatásakor hibaüzenetet kap `GRANT` , kövesse az alábbi lépéseket a orapwd-fájl 12,2 formátumra való áttelepítéséhez:
   >
   > 1. Kilépés a SQLPlus.
   > 1. Helyezze át a jelszavas fájlt a régi formátummal egy új névre.
   > 1. Telepítse át a jelszót tartalmazó fájlt.
   > 1. Távolítsa el a régi fájlt.
   > 1. Futtassa az alábbi parancsot:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Futtassa újra a `GRANT` műveletet a SQLPlus-ben.
   >
   
4. Tárolt eljárás létrehozása a biztonsági mentési üzenetek naplózásához az adatbázis riasztási naplójába:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Alkalmazás-konzisztens biztonsági másolatok beállítása  

1. Váltson a *root* felhasználóra:

   ```bash
   sudo su -
   ```

2. Hozza létre az alkalmazás-konzisztens biztonsági mentési munkakönyvtárat:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Hozzon létre egy fájlt a *munkaterhelés. conf* nevű */etc/Azure* könyvtárban az alábbi tartalommal, amelynek a következővel kell kezdődnie: `[workload]` . A következő parancs létrehozza majd a fájlt, és feltölti a tartalmat:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

4. Töltse le a preOracleMaster. SQL és a postOracleMaster. SQL parancsfájlt a [GitHub-tárházból](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) , és másolja őket a */etc/Azure* könyvtárba.

5. A fájlengedélyek módosítása

```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Alkalmazás-konzisztens biztonsági másolat elindítása a virtuális gépen

# <a name="portal"></a>[Portál](#tab/azure-portal)

1.  A Azure Portal lépjen az erőforráscsoport **RG-Oracle** elemre, és kattintson a virtuális gép **vmoracle19c**.

2.  A **Backup (biztonsági mentés** ) panelen hozzon létre egy új **Recovery Services** tárolót a **RG-Oracle** nevű erőforráscsoport **myVault** néven.
    A **biztonsági mentési szabályzat kiválasztásához** használja az **(új) DailyPolicy**. Ha módosítani szeretné a biztonsági mentés gyakoriságát vagy a megőrzési tartományt, válassza az **új szabályzat létrehozása** lehetőséget.

    ![Recovery Services-tárolók hozzáadása lap](./media/oracle-backup-recovery/recovery-service-01.png)

3.  A folytatáshoz kattintson a **biztonsági mentés engedélyezése** elemre.

    > [!IMPORTANT]
    > Miután rákattintott a **biztonsági mentés engedélyezése** lehetőségre, a biztonsági mentési folyamat nem indul el, amíg az ütemezett idő lejár. Ha azonnali biztonsági mentést szeretne beállítani, hajtsa végre a következő lépést.

4. Az erőforráscsoport lapon kattintson az újonnan létrehozott Recovery Services Vault **myVault**. Tipp: Előfordulhat, hogy frissítenie kell a lapot, hogy megtekintse.

5.  A **myVault-Backup elemek** panelen, a biztonsági másolati elemek **száma** területen válassza ki a biztonsági másolati elemek darabszámát.

    ![Recovery Services-tárolók myVault részletei lap](./media/oracle-backup-recovery/recovery-service-02.png)

6.  A **biztonsági mentési elemek (Azure-beli virtuális gép)** panel jobb oldalán kattintson a három pontra (**..**.), majd a **biztonsági mentés** elemre.

    ![Recovery Services-tárolók biztonsági mentése – parancs](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Fogadja el az alapértelmezett megőrzési biztonsági mentést, és kattintson az **OK** gombra. Várjon, amíg a biztonsági mentési folyamat befejeződik. 

    A biztonsági mentési feladat állapotának megtekintéséhez kattintson a **biztonsági mentési feladatok** lehetőségre.

    ![Recovery Services-tárolók feladatainak lapja](./media/oracle-backup-recovery/recovery-service-04.png)

    A biztonsági mentési feladatok állapota a következő képen jelenik meg:

    ![Recovery Services-tárolók feladatainak állapota](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Vegye figyelembe, hogy amíg a pillanatkép végrehajtásához csak másodpercek szükségesek, eltarthat egy ideig, amíg át nem fejeződik az átvitel a tárolóba, és a biztonsági mentési feladatok nem fejeződött be.

8. Egy alkalmazással konzisztens biztonsági mentés esetén a naplófájlban felmerülő hibák elhárítása. A naplófájl a következő helyen található:/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Recovery Services-tároló létrehozása:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Biztonsági mentési védelem engedélyezése a virtuális gép számára:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Indítsa el a biztonsági mentést, és ne várja meg a biztonsági mentést az alapértelmezett menetrend szerint (5 UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   A és a használatával nyomon követheti a biztonsági mentési feladatok állapotát `az backup job list` `az backup job show` .

---

## <a name="recovery"></a>Helyreállítási

Az adatbázis helyreállításához hajtsa végre az alábbi lépéseket:

1. Távolítsa el az adatbázisfájlok.
1. Visszaállítási parancsfájl létrehozása a Recovery Services-tárolóból.
1. Csatlakoztassa a visszaállítási pontot.
1. Végezze el a helyreállítást.

### <a name="remove-the-database-files"></a>Adatbázisfájlok eltávolítása 

A cikk későbbi részében megtudhatja, hogyan tesztelheti a helyreállítási folyamatot. A helyreállítási folyamat tesztelése előtt el kell távolítania az adatbázisfájlok fájljait.

1.  Az Oracle-példány leállítása:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Távolítsa el az adatfájlokat és a biztonsági másolatokat:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Visszaállítási parancsfájl létrehozása a Recovery Services-tárból

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresse meg a *myVault* Recovery Services-tárolók elemet, és válassza ki.

    ![Recovery Services-tárolók myVault biztonsági másolati elemei](./media/oracle-backup-recovery/recovery-service-06.png)

2. Az **Áttekintés** panelen válassza a **biztonsági másolati elemek elemet** , majd az **Azure virtuális gép** kiválasztása lehetőséget, amelynek az Anon – nulla biztonsági mentési elemek száma szerepel a listában.

    ![Recovery Services tárolók Azure-beli virtuális gép biztonsági mentési elemeinek száma](./media/oracle-backup-recovery/recovery-service-07.png)

3. A biztonsági mentések elemei (Azure Virtual Machines) lapon megjelenik a virtuális gép **vmoracle19c** . Kattintson a jobb oldalon található három pontra a menü megnyitásához, majd válassza a **fájl helyreállítása** lehetőséget.

    ![Képernyőkép a Recovery Services-tárolók fájljának helyreállítási oldaláról](./media/oracle-backup-recovery/recovery-service-08.png)

4. A **fájl-helyreállítás (előzetes verzió)** panelen kattintson a **parancsfájl letöltése** elemre. Ezután mentse a letöltés (. file) fájlt egy mappába az ügyfélszámítógépen. A parancsfájl futtatásához a rendszer jelszót hoz létre. Másolja a jelszót egy fájlba, hogy később használhassa. 

    ![Parancsfájl-mentési beállítások letöltése](./media/oracle-backup-recovery/recovery-service-09.png)

5. Másolja a. Copy fájlt a virtuális gépre.

    Az alábbi példa bemutatja, hogyan helyezheti át a fájlt a virtuális gépre a biztonságos másolás (SCP) parancs használatával. A vágólapra másolhatja is a tartalmat, majd beillesztheti a tartalmat a virtuális gépen beállított új fájlba.

    > [!IMPORTANT]
    > Az alábbi példában ellenőrizze, hogy az IP-cím és a mappa értékét frissíti-e. Az értékeknek arra a mappára kell leképezni, ahová a fájlt mentette.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A virtuális gép helyreállítási pontjainak listázásához használja az az Backup helyreállítási pontok listáját. Ebben a példában a myVM nevű virtuális gép legutóbbi helyreállítási pontját választjuk, amelyet a myRecoveryServicesVault véd:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

A helyreállítási pontot a virtuális géphez kapcsoló vagy csatlakoztató szkript beszerzéséhez használja az az backup restore files mount-rp parancsot. A következő példa a myVM nevű virtuális gép parancsfájlját szerzi be a myRecoveryServicesVault-ben védettként.

Cserélje le a myRecoveryPointName kifejezést az előző paranccsal beszerzett helyreállítási pont nevére:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

A rendszer az alábbi példában látható módon letölti a szkriptet, és megjeleníti a jelszót:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Másolja a. Copy fájlt a virtuális gépre.

Az alábbi példa bemutatja, hogyan helyezheti át a fájlt a virtuális gépre a biztonságos másolás (SCP) parancs használatával. A vágólapra másolhatja is a tartalmat, majd beillesztheti a tartalmat a virtuális gépen beállított új fájlba.

> [!IMPORTANT]
> Az alábbi példában ellenőrizze, hogy az IP-cím és a mappa értékét frissíti-e. Az értékeknek arra a mappára kell leképezni, ahová a fájlt mentette.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>A visszaállítási pont csatlakoztatása

1. Hozzon létre egy visszaállítási csatlakoztatási pontot, és másolja a parancsfájlt.

    A következő példában hozzon létre egy */Restore* könyvtárat ahhoz, hogy csatlakoztatni lehessen a pillanatképet, helyezze át a fájlt a könyvtárba, és módosítsa a fájlt úgy, hogy az a legfelső szintű felhasználó és a végrehajtható fájl tulajdonosa legyen.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Most futtassa a parancsfájlt a biztonsági mentés visszaállításához. A rendszer kérni fogja, hogy adja meg a Azure Portalban generált jelszót. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Az alábbi példa azt mutatja be, hogy mit kell látni az előző szkript futtatása után. Ha a rendszer felszólítja a folytatásra, írja be az **Y** értéket.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. A csatlakoztatott kötetekhez való hozzáférés megerősítve.

    A kilépéshez írja be a **q** kifejezést, majd keresse meg a csatlakoztatott köteteket. A hozzáadott kötetek listájának létrehozásához a parancssorba írja be a következőt: **DF-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Helyreállítás végrehajtása

1. Másolja vissza a hiányzó biztonságimásolat-fájlokat a gyors helyreállítási körzetbe:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. A következő parancsok a Oláh Anna használatával állítják vissza a hiányzó adatfájlokat, és helyreállítják az adatbázist:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Győződjön meg arról, hogy az adatbázis tartalma teljesen helyre lett állítva:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Válassza le a visszaállítási pontot.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    A Azure Portal a **fájl-helyreállítás (előzetes verzió) panelen** kattintson a **lemezek leválasztása** elemre.

    ![Lemezek leválasztása parancs](./media/oracle-backup-recovery/recovery-service-10.png)
    
    A helyreállítási köteteket úgy is leválaszthatja, hogy a **-Clean** kapcsolóval újra futtatja a Python-szkriptet.

## <a name="restore-the-entire-vm"></a>A teljes virtuális gép visszaállítása

A törölt fájlok Recovery Services tárolóból való visszaállítása helyett visszaállíthatja a teljes virtuális gépet.

A teljes virtuális gép visszaállításához hajtsa végre a következő lépéseket:

1. Vmoracle19c leállítása és törlése.
1. Állítsa helyre a virtuális gépet.
1. Állítsa be a nyilvános IP-címet.
1. Csatlakozzon a virtuális géphez.
1. Indítsa el az adatbázist a csatlakoztatási fázishoz, és végezze el a helyreállítást.

### <a name="stop-and-delete-vmoracle19c"></a>Vmoracle19c leállítása és törlése

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal lépjen a **vmoracle19c** virtuális gépre, majd válassza a **Leállítás** lehetőséget.

1. Ha a virtuális gép már nem fut, válassza a **Törlés** , majd az **Igen** lehetőséget.

   ![Tár törlési parancsa](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A virtuális gép leállítása és felszabadítása:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Törölje a virtuális gépet. Ha a rendszer kéri, adja meg az "y" értéket:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>A virtuális gép helyreállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Hozzon létre egy Storage-fiókot az előkészítéshez a Azure Portal.

   1. A Azure Portal válassza az **+ erőforrás létrehozása** elemet, és keresse meg és válassza ki a **Storage-fiók** elemet.
    
      ![Az erőforrás létrehozásának helyét bemutató képernyőkép.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. A Storage-fiók létrehozása lapon válassza ki a meglévő erőforráscsoport **RG-Oracle** nevet, nevezze el a Storage-fiók **oracrestore** , és válassza a **Storage v2 (GeneralPurpose v2)** fiókot a fiók típusa beállításnál. Módosítsa a replikációt **helyileg redundáns tárolóra (LRS)** , és állítsa be a teljesítményt a **standard** értékre. Győződjön meg arról, hogy a hely ugyanahhoz a régióhoz van beállítva, mint az erőforráscsoport összes többi erőforrása. 
    
      ![Storage-fiók hozzáadása lap](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Kattintson a felülvizsgálat + Létrehozás elemre, majd a Létrehozás gombra.

2. A Azure Portal keresse meg a *myVault* Recovery Services-tárolók elemét, és kattintson rá.

    ![Recovery Services-tárolók myVault biztonsági másolati elemei](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Az **Áttekintés** panelen válassza a **biztonsági másolati elemek elemet** , majd az **Azure virtuális gép** kiválasztása lehetőséget, amelynek az Anon – nulla biztonsági mentési elemek száma szerepel a listában.

    ![Recovery Services tárolók Azure-beli virtuális gép biztonsági mentési elemeinek száma](./media/oracle-backup-recovery/recovery-service-07.png)

4.  A biztonsági másolati elemek (Azure Virtual Machines) lapon a virtuális gép **vmoracle19c** látható. Kattintson a virtuális gép nevére.

    ![Helyreállítási virtuális gép lapja](./media/oracle-backup-recovery/recover-vm-02.png)

5.  A **vmoracle19c** panelen válassza ki azt a visszaállítási pontot, amelynek konzisztencia-típusa **konzisztens az alkalmazásnak** , majd kattintson a jobb oldalon található három pontra (**...**) a menü megnyitásához.  A menüben kattintson a **virtuális gép visszaállítása** elemre.

    ![VM-parancs visszaállítása](./media/oracle-backup-recovery/recover-vm-03.png)

6.  A **virtuális gép visszaállítása** panelen válassza az **új létrehozása** lehetőséget, és **hozzon létre új virtuális gépet**. Adja meg a virtuális gép nevét **vmoracle19c** , és válassza ki a VNet **vmoracle19cVNET**, az alhálózat automatikusan ki lesz töltve a VNet-kiválasztás alapján. A virtuális gép visszaállítása folyamathoz egy Azure Storage-fiókra van szükség ugyanabban az erőforráscsoporthoz és régióban. Kiválaszthatja a korábban beállított **orarestore** .

    ![Konfigurációs értékek visszaállítása](./media/oracle-backup-recovery/recover-vm-04.png)

7.  A virtuális gép visszaállításához kattintson a **visszaállítás** gombra.

8.  A visszaállítási folyamat állapotának megtekintéséhez kattintson a **feladatok**, majd a **biztonsági mentési feladatok** elemre.

    ![Biztonsági mentési feladatok állapotának parancsa](./media/oracle-backup-recovery/recover-vm-05.png)

    A visszaállítási folyamat állapotának megjelenítéséhez kattintson a **folyamatban lévő** visszaállítás műveletre:

    ![A visszaállítási folyamat állapota](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Storage-fiók és a fájlmegosztás beállításához futtassa az alábbi parancsokat az Azure CLI-ben.

1. Hozza létre a Storage-fiókot ugyanabban az erőforráscsoportban és helyen, mint a virtuális gép:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Az elérhető helyreállítási pontok listájának beolvasása. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Állítsa vissza a helyreállítási pontot a Storage-fiókra. Helyettesítse `<myRecoveryPointName>` be a helyreállítási pontot az előző lépésben létrehozott listából:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. A visszaállítási feladatok részleteinek beolvasása. A következő parancs részletesen ismerteti az aktivált visszaállított feladatot, beleértve a nevét is, amely a sablon URI-azonosítójának lekéréséhez szükséges. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   A kimenet a következőhöz hasonlóan fog kinézni `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Kérje le a virtuális gép újbóli létrehozásához használandó URI részleteit. Helyettesítse be a visszaállítási feladatot az előző lépésből a következőre: `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   A kimenet a következőhöz hasonló:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   A sablon neve, amely a sablon blob URI-ja végén található, amely ebben a példában `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` a és a blob-tároló neve, amely `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` szerepel a felsorolásban. 

   Használja ezeket az értékeket a következő parancsban a virtuális gép létrehozásához szükséges változók hozzárendeléséhez. A Storage-tárolóhoz a 30 perces időtartamú SAS-kulcs jön létre.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Most telepítse a sablont a virtuális gép létrehozásához.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   A rendszer kérni fogja a virtuális gép nevének megadását.

---

### <a name="set-the-public-ip-address"></a>A nyilvános IP-cím beállítása

A virtuális gép visszaállítása után újra hozzá kell rendelnie az eredeti IP-címet az új virtuális géphez.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1.  A Azure Portal nyissa meg a virtuális gép **vmoracle19c**. Megfigyelheti, hogy a vmoracle19c-NIC-XXXXXXXXXXXX-hez hasonló új nyilvános IP-címet és hálózati adaptert rendelt hozzá, de nem rendelkezik DNS-címmel. Ha az eredeti virtuális gépet törölték a nyilvános IP-címével és a hálózati adaptert, a következő lépésekkel pedig újra csatolja őket az új virtuális géphez.

    ![Nyilvános IP-címek listája](./media/oracle-backup-recovery/create-ip-01.png)

2.  A virtuális gép leállítása

    ![IP-cím létrehozása](./media/oracle-backup-recovery/create-ip-02.png)

3.  **Hálózatra** ugrás

    ![IP-cím hozzárendelése](./media/oracle-backup-recovery/create-ip-03.png)

4.  Kattintson a **hálózati adapter csatolása** lehetőségre, válassza ki az eredeti hálózati adapter * * vmoracle19cVMNic, amelyhez az eredeti nyilvános IP-cím továbbra is társítva van, majd kattintson **az OK** gombra.

    ![Adja meg az erőforrás típusát és a NIC-értékeket](./media/oracle-backup-recovery/create-ip-04.png)

5.  Most le kell választania a virtuális gép visszaállítása művelettel létrehozott hálózati adaptert, mivel az elsődleges illesztőfelületként van konfigurálva. Kattintson a **hálózati adapter leválasztása** lehetőségre, és válassza az **vmoracle19c-NIC-XXXXXXXXXXXX** hasonló új hálózati adaptert, majd kattintson az **OK** gombra.

    ![A hálózati adapter leválasztásának helyét bemutató képernyőkép.](./media/oracle-backup-recovery/create-ip-05.png)
    
    Az újból létrehozott virtuális gép most már az eredeti IP-címhez és a hálózati biztonsági csoport szabályaihoz társított eredeti hálózati adapterrel rendelkezik.
    
    ![IP-cím értéke](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Térjen vissza az **áttekintéshez** , és kattintson a **Start** gombra. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A virtuális gép leállítása és felszabadítása:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. A jelenlegi, helyreállított virtuális gép hálózati adapterének listázása

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   A kimenet a következőhöz hasonlóan fog kinézni, amely felsorolja a visszaállításhoz létrehozott hálózati adapter nevét `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Csatolja az eredeti hálózati adaptert, amelynek a neve `<VMName>VMNic` , ebben az esetben `vmoracle19cVMNic` . Az eredeti nyilvános IP-cím továbbra is ehhez a hálózati adapterhez van csatolva, és a rendszer visszaállítja a virtuális géphez, amikor a hálózati adapter újra csatlakozik. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. A Restore generált hálózati adapter leválasztása

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. A virtuális gép elindítása:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

A virtuális géphez való kapcsolódáshoz használja a következő parancsfájlt:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Az adatbázis elindítása a csatlakoztatási fázishoz és a helyreállítás elvégzéséhez

1. Előfordulhat, hogy a példány fut, mert az Automatikus indítás megpróbálta elindítani az adatbázist a virtuális gép rendszerindításához. Az adatbázisnak azonban helyreállításra van szüksége, és valószínűleg csak a csatlakoztatási fázisban lesz, ezért először az előkészítő leállítás fut.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Győződjön meg arról, hogy az adatbázis tartalma helyreállt:

    ```bash
    SQL> select * from scott.scott_table;
    ```

A Oracle Database 19c-adatbázis biztonsági mentése és helyreállítása egy Azure Linux rendszerű virtuális gépen befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális gépre, a következő paranccsal távolíthatja el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](../../linux/cli-samples.md)
