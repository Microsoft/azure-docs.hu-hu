---
title: Az Azure Application konzisztens pillanatkép-eszköz konfigurálása a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz configure parancsának futtatásához, amelyet a Azure NetApp Files használhat.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632757"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Az Azure Application konzisztens pillanatkép-eszköz konfigurálása (előzetes verzió)

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz configure parancsának futtatásához, amelyet a Azure NetApp Files használhat.

## <a name="introduction"></a>Bevezetés

A konfigurációs fájl a parancs használatával hozható létre vagy szerkeszthető `azacsnap -c configure` .

## <a name="command-options"></a>Parancs beállításai

A `-c configure` parancs a következő beállításokkal rendelkezik

- `--configuration new` új konfigurációs fájl létrehozásához.

- `--configuration edit` egy meglévő konfigurációs fájl szerkesztéséhez.

- `[--configfile <config filename>]` a (z) egy opcionális paraméter, amely lehetővé teszi az egyéni konfigurációs fájlnevek nevét.

## <a name="configuration-file-for-snapshot-tools"></a>A pillanatkép-eszközök konfigurációs fájlja

A futtatásával létrehozhat egy konfigurációs fájlt `azacsnap -c configure --configuration new` .  Alapértelmezés szerint a konfigurációs fájlnév `azacsnap.json` .  Egyéni fájlnév használható a `--configfile=` paraméterrel (például `--configfile=<customname>.json` :) a következő példa az Azure-beli nagyméretű példányok konfigurálására szolgál:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>A szükséges értékek részletei

A következő szakaszokban részletes útmutatást talál a konfigurációs fájlhoz szükséges különböző értékekhez.

### <a name="sap-hana-values"></a>Értékek SAP HANA

Amikor *adatbázist* ad hozzá a konfigurációhoz, a következő értékek szükségesek:

- **HANA-kiszolgáló címe** = a SAP HANA kiszolgáló állomásneve vagy IP-címe.
- **HANA SID** = a SAP HANA rendszerazonosító.
- **HANA-példány száma** = a SAP HANA példányának száma.
- **HANA HDB felhasználói tároló kulcsa** = az adatbázis biztonsági másolatainak futtatásához engedéllyel konfigurált SAP HANA felhasználó.

- Egyetlen csomópont: a csomópont IP-címe és állomásneve
- HSR STONITH: a csomópont IP-címe és állomásneve
- Kibővíthető (N + N, N + M): jelenlegi fő csomópont IP-címe és állomásneve
- HSR nélküli STONITH: a csomópont IP-címe és állomásneve
- Többszörös SID egyetlen csomóponton: a biztonsági azonosítókat üzemeltető csomópont állomásneve és IP-címe

### <a name="azure-large-instance-hli-storage-values"></a>Azure nagyméretű példányok (HLI) tárolási értékei

Ha *HLI-tárolót* ad hozzá egy adatbázis szakaszhoz, a következő értékek szükségesek:

- **Storage-Felhasználónév** = ez az érték a tárolóhoz való SSH-kapcsolat létesítéséhez használt Felhasználónév.
- **Storage IP-címe** = a tárolási rendszerek címe.
- **Tároló kötetének neve** = a kötet neve a pillanatképhez.  Ez az érték több módon is meghatározható, lehet, hogy a legegyszerűbb a következő rendszerhéj-parancs kipróbálása:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp Files (ANF) tárolási értékei

Ha *ANF-tárolót* ad hozzá egy adatbázis szakaszhoz, a következő értékek szükségesek:

- **Egyszerű szolgáltatásnév-hitelesítés fájlnév** = ez az a fájl, amelyet a rendszer a `authfile.json` Cloud Shell generál a Azure NetApp Files Storage szolgáltatással való kommunikáció konfigurálásakor.
- **Teljes ANF tárolási kötet erőforrás-azonosítója** = a pillanatképet tároló kötet teljes erőforrás-azonosítója.  Ez a következő helyről kérhető le: Azure Portal – > ANF – > Volume – > beállítások/tulajdonságok – > erőforrás azonosítója

## <a name="configuration-file-overview-azacsnapjson"></a>Konfigurációs fájl áttekintése ( `azacsnap.json` )

A következő példában a az `azacsnap.json` egy SID-sel van konfigurálva.

A paraméterek értékeit az ügyfél adott SAP HANA környezetére kell beállítani.
Az **Azure nagyméretű példányrendszer** esetében ezeket az információkat a Microsoft Service Management nyújtja a bevezetési/átadási hívás során, és elérhetővé válik az átadás során biztosított Excel-fájlban. Nyisson meg egy szolgáltatási kérelmet, ha újra meg kell adni ezt az információt.

A következő példa csak az összes értéket frissíti.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> Olyan DR-forgatókönyv esetében, ahol a biztonsági mentéseket a DR helyen kell futtatni, a Dr-helyen a Dr-kiszolgálón konfigurált HANA-kiszolgáló nevét (például `DR.json` :) meg kell egyeznie az üzemi kiszolgáló nevével.

> [!NOTE]
> Az Azure-beli nagyméretű példány esetén a tárolási IP-címnek ugyanabban az alhálózatban kell lennie, mint a kiszolgáló-készletnek. Ebben az esetben például a kiszolgáló készletének alhálózata 172. 18. 18.0/24 és a hozzárendelt tárolási IP-172.18.18.11.

## <a name="next-steps"></a>Következő lépések

- [Azure Application konzisztens pillanatkép-eszköz tesztelése](azacsnap-cmd-ref-test.md)
