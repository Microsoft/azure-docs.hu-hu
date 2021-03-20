---
title: Az Azure Application konzisztens pillanatkép-eszköz hibáinak megoldása a Azure NetApp Fileshoz | Microsoft Docs
description: Hibaelhárítási tartalmat biztosít az Azure Application konzisztens pillanatkép-eszköz használatához, amelyet a Azure NetApp Files használhat.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632685"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Az Azure Application konzisztens pillanatkép-eszköz (előzetes verzió) – problémamegoldás

Ez a cikk az Azure Application konzisztens pillanatkép-eszköz használatáról nyújt hibaelhárítási információkat, amelyeket Azure NetApp Files használhat.

A következő gyakori problémák merülhetnek fel a parancsok futtatása során. A probléma megoldásához kövesse a fentiekben ismertetett megoldási útmutatót. Ha a probléma továbbra is fennáll, nyisson meg egy Azure Portal, és rendelje hozzá a kérést a SAP HANA nagyméretű példány-várólistához Microsoft ügyfélszolgálata válaszként.

## <a name="log-files"></a>Naplófájlok

Az egyik legjobb információforrás a AzAcSnap kapcsolatos hibák hibakereséséhez.  

### <a name="log-file-location"></a>Naplófájl helye

A naplófájlokat a rendszer a `logPath` AzAcSnap konfigurációs fájlban megadott paraméterben konfigurált könyvtárban tárolja.  Az alapértelmezett konfigurációs fájlnév, `azacsnap.json` a pedig az alapértelmezett érték `logPath` , `"./logs"` ami azt jelenti, hogy a naplófájlok a `./logs` parancs futtatásához viszonyítva lesznek beírva a könyvtárba `azacsnap` .  Az `logPath` abszolút hely (pl.) létrehozása `/home/azacsnap/logs` biztosítja a `azacsnap` naplók kimenetét `/home/azacsnap/logs` , függetlenül attól, hogy a `azacsnap` parancs hol volt futtatva.

### <a name="log-file-naming"></a>Naplófájlok elnevezése

A naplófájl neve az alkalmazás nevén (pl. `azacsnap` ), a parancs () által használt (például:, `-c` `backup` `test` `details` stb.) és a konfigurációs filename (például default = `azacsnap.json` ) alapján van megadva.  Így ha a `-c backup` parancsot használja, a rendszer alapértelmezés szerint a naplófájl fájlnevét fogja `azacsnap-backup-azacsnap.log` írni, és a által konfigurált könyvtárba írja `logPath` .  

Ez az elnevezési konvenció úgy lett kialakítva, hogy több konfigurációs fájlt, egy adatbázison belül egy adatbázist, és megkönnyítse a kapcsolódó naplófájlok megtalálását.  Ezért ha a konfigurációs fájlnév `SID.json` , akkor a beállítások használatakor az eredmény fájlneve lesz `azacsnap -c backup --configfile SID.json` `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Eredmény fájl és syslog

A `-c backup` parancs AzAcSnap a parancs használatával ír ki egy `*.result` fájlt és a rendszernaplót ( `/var/log/messages` ) `logger` .  A `*.result` fájlnév neve megegyezik a [naplófájl](#log-file-naming) nevével, és a [naplófájlban](#log-file-location)megegyező helyre kerül.  A következő példákban egy egyszerű, egy sor kimeneti fájl.

Példa kimenet `*.result` fájlból.
```output
Database # 1 (PR1) : completed ok
```

Példa kimenet `/var/log/messages` fájlból.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>Nem sikerült kommunikálni a SAP HANA

Ha a SAP HANAával folytatott kommunikációt egy teszt futtatásával ellenőrzi, `azacsnap -c test --test hana` és a következő hibaüzenetet jeleníti meg:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Megoldás:**

1. Ellenőrizze az egyes HANA-példányok konfigurációs fájlját (például `azacsnap.json` ), és győződjön meg arról, hogy a SAP HANA adatbázis értékei helyesek.
1. Az alábbi parancs futtatásával ellenőrizze, hogy a `hdbsql` parancs az elérési útban van-e, és hogy tud-e csatlakozni a SAP HANA-kiszolgálóhoz. A következő példa a parancs helyes futtatását és kimenetét mutatja be.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    Ebben a példában a `hdbsql` parancs nem szerepel a felhasználókban `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    Ebben a példában a `hdbsql` parancs ideiglenesen hozzá van adva a felhasználóhoz `$PATH` , de ha a Futtatás azt mutatja, hogy a kapcsolati kulcs nem lett megfelelően beállítva a `hdbuserstore Set` paranccsal (a részletekért tekintse meg első lépések útmutatót):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Ha véglegesen hozzá kíván adni a felhasználóhoz `$PATH` , frissítse a felhasználó `$HOME/.profile` fájlját

## <a name="the-hdbuserstore-location"></a>A `hdbuserstore` hely

A SAP HANAával folytatott kommunikáció beállításakor a `hdbuserstore` program a biztonságos kommunikációs beállítások létrehozására szolgál.  A `hdbuserstore` program általában a vagy a alatt található `/usr/sap/<SID>/SYS/exe/hdb/` `/usr/sap/hdbclient` .  A telepítő általában a megfelelő helyet adja hozzá a `azacsnap` felhasználóhoz `$PATH` .

## <a name="failed-test-with-storage"></a>Sikertelen tesztelés a Storage szolgáltatással

A parancs `azacsnap -c test --test storage` végrehajtása nem fejeződött be sikeresen.

### <a name="azure-large-instance"></a>Nagyméretű Azure-példány

Az alábbi példa az Azure-beli `azacsnap` nagyméretű példányon SAP HANA fut:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Megoldás:** A fenti hiba általában akkor jelenik meg, ha az Azure nagyméretű példány-tároló felhasználója nem fér hozzá a mögöttes tárolóhoz. Ha ellenőrizni szeretné a tárterület hozzáférését a megadott Storage-felhasználóval, futtassa a `ssh` parancsot a Storage platformmal folytatott kommunikáció ellenőrzéséhez.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Példa a várt kimenetre:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Nem lehet létrehozni a (z) 172.18.18.11 (172.18.18.11) gazdagép eredetiségét

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Megoldás:** Ne válassza az Igen lehetőséget. Győződjön meg arról, hogy a tároló IP-címe helyes. Ha a probléma továbbra is fennáll, erősítse meg a tárolási IP-címet a Microsoft Operations csapatával.

### <a name="azure-netapp-files"></a>Azure NetApp Files

A következő példa `azacsnap` egy virtuális gépen fut Azure NetApp Files használatával:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Megoldás:**

1. Keresse meg az egyszerű szolgáltatásnév fájljának létezését `azureauth.json` a `azacsnap.json` konfigurációs fájlban megadott módon.
1. Ellenőrizze a naplófájlban (például `logs/azacsnap-test-azacsnap.log` :), hogy az egyszerű szolgáltatásnév ( `azureauth.json` ) rendelkezik-e a megfelelő tartalommal.  Példa a naplóból a következőképpen:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Ellenőrizze a naplófájlban (például `logs/azacsnap-test-azacsnap.log` :), hogy az egyszerű szolgáltatásnév ( `azureauth.json` ) lejárt-e. Példa a naplóból a következőképpen:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Következő lépések

- [Tippek](azacsnap-tips.md)
