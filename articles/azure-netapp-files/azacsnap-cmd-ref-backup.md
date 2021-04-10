---
title: Biztonsági mentés az Azure Application konzisztens pillanatkép-eszköz használatával a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz Backup parancsának futtatásához, amelyet a Azure NetApp Files használhat.
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
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730938"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Biztonsági mentés az Azure Application konzisztens Snapshot Tool eszközzel (előzetes verzió)

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz Backup parancsának futtatásához, amelyet a Azure NetApp Files használatával használhat.

## <a name="introduction"></a>Bevezetés

A tárolási pillanatkép-alapú biztonsági mentést a parancs használatával futtathatja `azacsnap -c backup` .  Ez a parancs egy adatbázis-konzisztens tárolási pillanatkép előkészítését hajtja végre az adatköteteken, valamint egy tárolási pillanatképet (az adatbázis konzisztenciájának beállítása nélkül) a többi köteten.  

Az adatkötetek `azacsnap` előkészítik az adatbázist egy tárolási pillanatképhez, majd az összes konfigurált kötethez elvégzi a tárolási pillanatképet, végül pedig a pillanatkép befejezését biztosító adatbázist fogja tájékoztatni.  Emellett minden olyan adatbázis-bejegyzést is kezelni fog, amely a pillanatképek biztonsági mentési tevékenységeit rögzíti (például SAP HANA biztonsági mentési katalógus).

## <a name="command-options"></a>Parancs beállításai

A `-c backup` parancs a következő argumentumokat veszi figyelembe:

- `--volume=` a pillanatképhez tartozó kötet típusa, ez a paraméter tartalmazhat `data` vagy `other`
  - `data` a konfigurációs fájl dataVolume eredményeiben lévő kötetek pillanatképe.
  - `other` a konfigurációs fájl otherVolume eredményeiben lévő kötetek pillanatképe.
  
  > [!NOTE]
  > Ha külön konfigurációs fájlt hoz létre a rendszerindító kötettel a otherVolume, akkor lehetséges, hogy a `boot` pillanatképek teljes mértékben eltérőek lesznek (például naponta).

- `--prefix=` a pillanatkép-név ügyfél-pillanatképének előtagja. Ennek a paraméternek két célja van. Az első cél az, hogy egyedi nevet adjon a pillanatképek csoportosításához. Másodszor a `--retention` megadott tárolási Pillanatképek számának megállapításához `--prefix` .

    > [!IMPORTANT]
    > Csak alfanumerikus numerikus ("A-Z, a-z, 0-9"), aláhúzás ("_") és kötőjel ("-") karakterek megengedettek.

- `--retention` a megtartani kívánt Pillanatképek száma `--prefix` . A további Pillanatképek el lesznek távolítva az új pillanatkép létrehozása után `--prefix` .

- `--trim` a SAP HANA v2 és újabb verziók esetében ez a beállítás a biztonsági mentési katalógust és a lemezes katalógust, valamint a naplók biztonsági másolatait tárolja. A biztonsági mentési katalógusban megőrizni kívánt bejegyzések számát a fenti lehetőség határozza meg `--retention` , és törli a definiált előtag () korábbi bejegyzéseit `--prefix` a biztonsági mentési katalógusból, valamint a kapcsolódó fizikai naplók biztonsági mentését. Emellett törli a naplóbeli biztonsági mentési bejegyzéseket, amelyek régebbiek, mint a legrégebbi nem naplózott biztonsági mentési bejegyzés. Ez a művelet segít megakadályozni, hogy a napló biztonsági mentései ne használják fel az összes rendelkezésre álló lemezterületet.

  > [!NOTE]
  > A következő példában a parancs 9 tároló-pillanatképet tart fenn, és gondoskodik arról, hogy a biztonsági mentési katalógus folyamatosan legyen kimetszve, hogy megfeleljen a megőrzött 9 tárolási pillanatképeknek.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` egy választható paraméter, amely meghatározza a SAP HANAsal való kommunikációhoz használt titkosítási módszert, `openssl` vagy vagy `commoncrypto` . Ha meg van adva, akkor a `azacsnap -c backup` parancs két fájlt vár ugyanabban a címtárban, ezeket a fájlokat a megfelelő SID után kell elnevezni. Tekintse meg az [SSL használatát a SAP HANAsal való kommunikációhoz](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). Az alábbi példa egy olyan `hana` típusú pillanatképet készít, amely a előtaggal rendelkezik, és a SAP HANA az `hana_TEST` `9` SSL () használatával folytatja a kommunikációt `openssl` .

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` a (z) egy opcionális paraméter, amely lehetővé teszi az egyéni konfigurációs fájlnevek nevét.

## <a name="snapshot-backups-are-fast"></a>A pillanatképek biztonsági mentései gyorsak

A pillanatkép biztonsági mentésének időtartama független a kötet méretétől, és egy 10 TB-os kötet a 10 GB-os kötettel megegyező hozzávetőleges időtartamon belülre van pattintva.  

A teljes végrehajtási időt befolyásoló elsődleges tényezők a pillanatképet tartalmazó kötetek száma, valamint a `--retention` paraméter változásai (ahol a csökkentés növelheti a végrehajtási időt, mivel a rendszer eltávolítja a felesleges pillanatképeket).

A fenti konfigurációban (a **nagyméretű Azure-példány** esetében) a két kötethez tartozó Pillanatképek végrehajtása kevesebb, mint 5 másodperc volt. **Azure NetApp Files** esetén a két kötet pillanatképei körülbelül 60 másodpercet vesznek igénybe.

> [!NOTE]
> Ha a `--retention` rendszer az előző időpontból jelentősen csökkenti a `azacsnap` futást (például: `--retention 50` – `--retention 5` ), akkor az igénybe vett idő növekedni fog, ahogy a `azacsnap` további pillanatképeket el kell távolítani.

## <a name="example-with-data-parameter"></a>Példa `data` paraméterrel

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

A parancs kimenete nem a konzolra mutat, de a naplófájlba, egy eredmény-fájlba és a fájlba ír `/var/log/messages` .

A naplófájl a parancs neve + a-c kapcsoló + a config filename *fájlból* áll. Alapértelmezés szerint egy `-c backup` alapértelmezett konfigurációs fájlnévvel rendelkező futtatáshoz tartozó naplófájl-fájlnév `azacsnap-backup-azacsnap.log` .

Az *eredmény* fájljának neve megegyezik a naplófájl utótagjának nevével, `.result` például `azacsnap-backup-azacsnap.result` a következő kimenetet tartalmazza:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

A `/var/log/messages` fájl a fájllal megegyező kimenetet tartalmaz `.result` . Tekintse meg a következő példát (futtató gyökér):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Példa `other` paraméterrel

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

A parancs kimenete nem a konzolra mutat, de csak a naplófájlba ír.  _Nem_ ír egy eredményhalmaz fájlba vagy `/var/log/messages` .

A naplófájl a parancs neve + a-c kapcsoló + a config filename *fájlból* áll. Alapértelmezés szerint egy `-c backup` alapértelmezett konfigurációs fájlnévvel rendelkező futtatáshoz tartozó naplófájl-fájlnév `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Példa `other` paraméterrel (a gazdagép operációs rendszerének biztonsági mentése)

> [!NOTE]
> Egy másik konfigurációs fájl () használata, `--configfile bootVol.json` amely csak a rendszerindító köteteket tartalmazza.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

A parancs kimenete nem a konzolra mutat, de csak a naplófájlba ír.  _Nem_ ír egy eredményhalmaz fájlba vagy `/var/log/messages` .

Ebben a példában a *naplófájl* neve `azacsnap-backup-bootVol.log` .

> [!NOTE]
> A naplófájl neve a "(parancs neve – (a `-c` beállítás) – (a konfigurációs fájlnév).  Ha például a `-c backup` paraméterrel a naplófájl nevét használja `h80.json` , a rendszer a naplófájlt fogja meghívni `azacsnap-backup-h80.log` .  Ha a `-c test` beállítást ugyanazzal a konfigurációs fájllal használja, akkor a rendszer meghívja a naplófájlt `azacsnap-test-h80.log` .

- HANA nagyméretű példány típusa: két érvényes érték van a `TYPEI` `TYPEII` Hana nagyméretű példány-egységgel, vagy attól függ.
- Tekintse meg az elérhető SKU-ket [a HANA nagy példányok](../virtual-machines/workloads/sap/hana-available-skus.md) számára az elérhető SKU-azonosítók ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

- [Pillanatkép részleteinek beolvasása](azacsnap-cmd-ref-details.md)
- [Pillanatképek törlése](azacsnap-cmd-ref-delete.md)