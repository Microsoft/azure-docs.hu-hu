---
title: Tippek és trükkök az Azure Application konzisztens pillanatkép-eszköz használatához a Azure NetApp Fileshoz | Microsoft Docs
description: Tippeket és trükköket nyújt az Azure Application konzisztens pillanatkép-eszköz használatához, amelyet a Azure NetApp Files használhat.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869939"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Tippek és trükkök az Azure Application konzisztens pillanatkép-eszköz (előzetes verzió) használatához

Ez a cikk olyan tippeket és trükköket tartalmaz, amelyek hasznosak lehetnek a AzAcSnap használatakor.

## <a name="limit-service-principal-permissions"></a>Egyszerű szolgáltatásnév engedélyeinek korlátozása

Előfordulhat, hogy korlátozni kell a AzAcSnap egyszerű szolgáltatásának hatókörét.  Az Azure-erőforrások részletes hozzáférés-kezelésével kapcsolatos további információkért tekintse át az [Azure RBAC dokumentációját](../role-based-access-control/index.yml) .  

A következő példa egy szerepkör-definíciót mutat be a AzAcSnap működéséhez szükséges minimálisan szükséges műveletekkel.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Ahhoz, hogy a visszaállítási lehetőségek sikeresen működjenek, a AzAcSnap szolgáltatásnak is képesnek kell lennie kötetek létrehozására.  Ebben az esetben a szerepkör-definíciónak további műveletre van szüksége, ezért a teljes szolgáltatásnevet a következő példához hasonlóan kell kinéznie.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Pillanatképek manuális elkészítése

A biztonsági mentési parancsok () végrehajtása előtt `azacsnap -c backup` ellenőrizze a konfigurációt a tesztelési parancsok futtatásával, és ellenőrizze, hogy sikeresen végrehajtották-e a műveleteket.  A tesztek helyes végrehajtása a bizonyítottan `azacsnap` képes kommunikálni a telepített SAP HANA adatbázissal és az Azure-beli **nagyméretű példányon** vagy **Azure NetApp Files** rendszeren található SAP HANA mögöttes tárolási rendszerrel.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

A manuális adatbázis-pillanatkép biztonsági mentésének elvégzéséhez futtassa a következő parancsot:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Automatikus pillanatkép biztonsági mentésének beállítása

Gyakori eljárás UNIX/Linux rendszereken a `cron` parancsok futtatásának automatizálására a rendszeren. A pillanatkép-eszközök standard gyakorlata a felhasználó beállítása `crontab` .

Alább látható egy példa arra, `crontab` hogy a felhasználók `azacsnap` automatizálják a pillanatképeket.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

A fenti crontab magyarázata.

- `MAILTO=""`: Ha üres értékkel rendelkezik, ezzel megakadályozza, hogy a cron automatikusan e-mailt küldjön a felhasználónak, amikor a crontab-bejegyzést futtatja, mivel valószínűleg a helyi felhasználó levelezési fájljában lenne.
- A crontab-bejegyzések időzítésének rövidített verziói magától értetődőek:
  - `@monthly` = Havonta egyszer fut, azaz "0 0 1 * *".
  - `@weekly`  = Hetente egyszer fut, azaz "0 0 * * 0".
  - `@daily`   = Naponta egyszer fut, azaz "0 0 * * *".
  - `@hourly`  = Óránként egyszer fut, azaz "0 * * * *".
- A rendszer az első öt oszlopot használja az időpontok kijelölésére, az alábbi példákat lásd:
  - `0,15,30,45`: 15 percenként
  - `0-23`: Óránként
  - `*` : Minden nap
  - `*` : Havonta
  - `*` : A hét minden napján
- A (z) "()" szögletes zárójelek között végrehajtandó parancssor
  - `. /home/azacsnap/.profile` = a felhasználó. profiljának lekérése a környezet beállításához, beleértve a $PATH stb.
  - `cd /home/azacsnap/bin` = a végrehajtási könyvtár módosítása a "/Home/azacsnap/bin" helyre, ahol a konfigurációs fájlok vannak.
  - `azacsnap -c .....` = a teljes azacsnap parancs futtatása, beleértve az összes beállítást is.

A cron további magyarázata és a crontab-fájl formátuma itt található: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> A felhasználók feladata a cron-feladatok figyelése, hogy a pillanatképek létrehozása sikeres legyen.

## <a name="monitor-the-snapshots"></a>A pillanatképek figyelése

Az egészséges rendszer biztosításához a következő feltételeket kell figyelni:

1. Rendelkezésre álló lemezterület. A pillanatképek lassan felhasználják a lemezterületet, mivel a rendszer megőrzi a régebbi lemezes blokkokat a pillanatképben.
    1. A lemezterület-kezelés automatizálásához használja a `--retention` és a `--trim` beállításokat a régi Pillanatképek és az adatbázis naplófájljainak automatikus törléséhez.
1. A pillanatkép-eszközök sikeres végrehajtása
    1. Győződjön meg arról, `*.result` hogy a fájl a legújabb futtatása sikeres vagy sikertelen volt `azacsnap` .
    1. `/var/log/messages`A parancs kimenetének keresése `azacsnap` .
1. A pillanatképek konzisztenciája úgy, hogy rendszeres időközönként visszaállítja őket egy másik rendszeren.

> [!NOTE]
> A pillanatkép részleteinek listázásához hajtsa végre a parancsot `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Pillanatkép törlése

Pillanatkép törléséhez hajtsa végre a parancsot `azacsnap -c delete` . A pillanatképeket nem lehet az operációs rendszer szintjéről törölni. A tárolási Pillanatképek törléséhez a megfelelő parancsot () kell használnia `azacsnap -c delete` .

> [!IMPORTANT]
> Legyen körültekintő, amikor töröl egy pillanatképet. Törlés után nem **lehet** helyreállítani a törölt pillanatképeket.

## <a name="restore-a-snapshot"></a>Pillanatkép visszaállítása

A tárolási kötetek pillanatképét új kötetre () lehet visszaállítani `-c restore --restore snaptovol` .  Az Azure nagyméretű példányai esetében a kötet visszaállítható egy pillanatképre ( `-c restore --restore revertvolume` ).

> [!NOTE]
> **Nincs megadva adatbázis-** helyreállítási parancs.

A pillanatképek visszamásolhatók a SAP HANA adatterületre, de a SAP HANA nem futhatnak másolat készítésével ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

Az Azure-beli nagyméretű példányok esetében a Microsoft operatív csapatának megnyitásával kérheti a kívánt pillanatkép visszaállítását a meglévő elérhető pillanatképekről. Megnyithat egy szolgáltatási kérelmet Azure Portal: <https://portal.azure.com>

Ha úgy dönt, hogy elvégzi a vész-helyreállítási feladatátvételt, a `azacsnap -c restore --restore revertvolume` Dr helyen található parancs automatikusan elérhetővé teszi a legújabb ( `/hana/data` és `/hana/logbackups` ) kötet-pillanatképeket a SAP HANA helyreállításhoz. Ezt a parancsot körültekintően kell használni, mivel megszakítja a replikációt a termelési és a DR-helyek között.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Pillanatképek beállítása csak a "rendszerindító" kötetekhez

> [!IMPORTANT]
> Ez a művelet csak az Azure nagyméretű példányára vonatkozik.

Bizonyos esetekben az ügyfeleknek már vannak olyan eszközei, amelyek védik a SAP HANAt, és csak a rendszerindítási kötetek pillanatképeit szeretnék konfigurálni.  Ebben az esetben a feladat egyszerűsítve van, és a következő lépéseket kell elvégeznie.

1. Hajtsa végre a telepítéshez szükséges előfeltételek 1-4. lépéseit.
1. Engedélyezze a kommunikációt a Storage szolgáltatással.
1. A pillanatkép-eszközök telepítéséhez töltse le a telepítő futtatását.
1. A pillanatkép-eszközök telepítésének befejezése.
1. Hozzon létre egy új konfigurációs fájlt az alábbiak szerint. A rendszerindító kötet részleteinek szerepelniük kell a OtherVolume-számban (a felhasználói bejegyzések <span style="color:red">vörös színnel</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Tekintse meg a konfigurációs fájlt, és tekintse meg a következő példát:

    A következő `cat` parancs használatával jelenítse meg a konfigurációs fájl tartalmát:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
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

1. Rendszerindító kötet biztonsági mentésének tesztelése

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Ellenőrizze, hogy szerepel-e a listán, vegye figyelembe a `--snapshotfilter` visszaadott pillanatkép-lista korlátozásának lehetőségét.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Parancs kimenete:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Állítsa be az automatikus pillanatkép biztonsági mentését.

> [!NOTE]
> Nem szükséges a SAP HANAval való kommunikáció beállítása.

## <a name="restore-a-boot-snapshot"></a>Rendszerindító pillanatkép visszaállítása

> [!IMPORTANT]
> Ez a művelet az Azure nagyméretű példányára vonatkozó ony.
> A rendszer visszaállítja a kiszolgálót arra a pontra, amikor a pillanatkép készült.

A "rendszerindítási" pillanatkép a következőképpen állítható vissza:

1. Az ügyfélnek le kell állítania a kiszolgálót.
1. A kiszolgáló leállítása után az ügyfélnek meg kell nyitnia egy szolgáltatási kérelmet, amely tartalmazza a visszaállítani kívánt számítógép-azonosítót és pillanatképet.
    > Az ügyfelek megnyitnak egy szolgáltatási kérelmet a Azure Portal: <https://portal.azure.com>
1. A Microsoft a megadott számítógép-azonosító és pillanatkép használatával állítja vissza az operációs rendszer logikai egységét, majd elindítja a kiszolgálót.
1. Az ügyfélnek ezután meg kell erősítenie a kiszolgálót, és kifogástalan állapotban kell lennie.

A visszaállítás után nem kell végrehajtani további lépéseket.

## <a name="key-facts-to-know-about-snapshots"></a>A pillanatképekkel kapcsolatos fontos tudnivalók

A tárolási kötetek pillanatképének főbb attribútumai:

- **Pillanatképek helye**: a pillanatképek a `.snapshot` köteten belüli virtuális könyvtárban () találhatók.  Tekintse meg az alábbi példákat a **nagyméretű Azure-példányhoz**:
  - Adatbázis `/hana/data/<SID>/mnt00001/.snapshot`
  - Megosztott `/hana/shared/<SID>/.snapshot`
  - Naplók `/hana/logbackups/<SID>/.snapshot`
  - Rendszerindítás: a HLI tartozó rendszerindítási Pillanatképek **nem láthatók** az operációs rendszer szintjéről, de a használatával is listázható `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` a csak olvasási jogosultsággal rendelkező rejtett *virtuális* mappa, amely csak olvasási hozzáférést biztosít a pillanatképekhez.

- **Pillanatkép maximális száma:** A hardver legfeljebb 250 pillanatképet képes fenntartani köteten. A Pillanatkép-parancs maximális számú pillanatképet tart fenn az előtaghoz a parancssorban lévő megőrzési készlet alapján, és törli a legrégebbi pillanatképet, ha az túllépi a megőrzendő maximális számot.
- **Pillanatkép neve:** A pillanatkép neve tartalmazza az ügyfél által megadott előtag-címkét.
- **A pillanatkép mérete:** Az adatbázis szintjének méretétől/változásaitól függ.
- **Naplófájl helye:** A parancsok által generált naplófájlok a JSON konfigurációs fájlban meghatározott mappákba kerülnek, ami alapértelmezés szerint egy almappa, amelyben a parancs fut (például: `./logs` ).

## <a name="next-steps"></a>Következő lépések

- [Hibaelhárítás](azacsnap-troubleshoot.md)
