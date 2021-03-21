---
title: Ismerkedés az Azure Application konzisztens Snapshot Tool eszközzel a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz telepítéséhez, amelyet a Azure NetApp Files használhat.
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
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736362"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Ismerkedés az Azure Application konzisztens pillanatkép-eszközzel (előzetes verzió)

Ez a cikk útmutatást nyújt az Azure Application konzisztens pillanatkép-eszköz telepítéséhez, amelyet a Azure NetApp Files használhat.

## <a name="getting-the-snapshot-tools"></a>A pillanatkép-eszközök beolvasása

Javasoljuk, hogy a Microsofttól szerezze be a [AzAcSnap telepítőjének](https://aka.ms/azacsnapdownload) legújabb verzióját.

Az öntelepítő fájlhoz egy társított [AzAcSnap-telepítő aláírási fájl](https://aka.ms/azacsnapdownloadsignature) tartozik, amely a Microsoft nyilvános kulcsával van aláírva, hogy lehetővé váljon a letöltött telepítő GPG-hitelesítése.

A letöltések befejezése után kövesse az útmutató lépéseit a telepítéshez.

### <a name="verifying-the-download"></a>A letöltés ellenőrzése

A fentiekben letölthető telepítőhöz hozzá van rendelve egy, a fájlnév kiterjesztését tartalmazó PGP-aláírási fájl `.asc` . Ezzel a fájllal ellenőrizheti, hogy a telepítő letöltött-e egy ellenőrzött Microsoft által biztosított fájlt. A Linux-csomagok aláírásához használt Microsoft PGP nyilvános kulcs itt () érhető el <https://packages.microsoft.com/keys/microsoft.asc> , és az aláírási fájl aláírására szolgál.

A Microsoft PGP nyilvános kulcsa a következőképpen importálható a felhasználó helyi részére:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

A következő parancsok megbíznak a Microsoft PGP nyilvános kulcsában:

1. Az áruházban található kulcsok listázása.
2. Szerkessze a Microsoft-kulcsot.
3. Ujjlenyomat keresése a `fpr`
4. A kulcs aláírásával bízza meg a kulcsot.

```bash
gpg --list-keys
```

Felsorolt kulcsok:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Kimenet az interaktív `gpg` munkamenetből a Microsoft nyilvános kulcsának aláírása:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

A telepítő PGP-aláírási fájlját a következőképpen lehet ellenőrizni:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

A GPG használatával kapcsolatos további információkért lásd [a GNU adatvédelmi kézikönyvét](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Támogatott esetek

A pillanatkép-eszközök a következő esetekben használhatók.

- Egyetlen SID
- Több SID
- HSR
- Bővítés
- MDC (csak egyetlen bérlő támogatott)
- Egyetlen tároló
- SUSE operációs rendszer
- RHEL operációs rendszer
- SKU-TÍPUS
- SKU TYPE II

Lásd: [a HANA nagyméretű példányainak támogatott forgatókönyvei](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Pillanatkép-támogatás mátrixa az SAP-ból

A következő mátrix olyan útmutatást biztosít, amely az SAP által támogatott SAP HANA-verziókat támogatja a Storage pillanatképes biztonsági mentések esetén.

| Adatbázis-verziók       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Egyetlen tároló adatbázisa| √       | √      | -      | -      | -      | -      |
|Egyetlen bérlő MDC        | -       | -      | √      | √      | √      | √      |
|Több bérlő MDC     | -       | -      | -      | -      | -      | √      |
> √ = <small>SAP által támogatott tárolási Pillanatképek</small>

## <a name="important-things-to-remember"></a>Fontos Tudnivaló

- A pillanatkép-eszközök beállítása után folyamatosan figyelje a rendelkezésre álló tárterületet, és ha szükséges, törölje a régi pillanatképeket rendszeresen a tárterület kitöltésének elkerüléséhez.
- Mindig a legújabb pillanatkép-eszközöket használja.
- A pillanatkép-eszközök ugyanazon verzióját használja a környezeten belül.
- Tesztelje a pillanatkép-eszközöket, és ismerkedjen meg a szükséges paraméterekkel és a parancs kimenetével az éles rendszeren való használat előtt.
- Ha a HANA-felhasználót a biztonsági mentéshez állítja be (a jelen dokumentum részletes adatai), minden HANA-példányhoz be kell állítania a felhasználót. Hozzon létre egy SAP HANA felhasználói fiókot a HANA-példány eléréséhez a SYSTEMDB (és nem a SID-adatbázisban) a MDC számára. Az egytárolós környezetben a bérlői adatbázis alatt lehet beállítani.
- Az ügyfeleknek meg kell adniuk a tárterület-hozzáféréshez szükséges nyilvános SSH-kulcsot. Ezt a műveletet minden csomóponton egyszer kell elvégezni, és minden olyan felhasználónál, amelynél a parancs végrehajtása történik.
- A pillanatképek másodpercenkénti száma a 250-ra korlátozódik.
- Ha manuálisan szerkeszti a konfigurációs fájlt, mindig egy Linux-szövegszerkesztőt, például a "VI"-t használja, nem pedig a Windows-szerkesztőket, mint a Jegyzettömb. A Windows szerkesztő használata megsérült lehet a fájlformátumban.
  - Beállíthatja, `hdbuserstore` hogy a SAP HANA felhasználó kommunikáljon a SAP HANAval.
- DR: a pillanatkép-eszközöket Dr-csomóponton kell tesztelni a Dr beállítása előtt.
- A lemezterület rendszeres figyelése, az automatikus napló törlése a `--trim`   `azacsnap -c backup` SAP HANA 2 és újabb kiadásokra vonatkozó beállításával felügyelhető.
- A **nem végrehajtott Pillanatképek kockázata** – a pillanatkép-eszközök csak a konfigurációs fájlban megadott SAP HANA rendszer csomópontját használják.  Ha ez a csomópont elérhetetlenné válik, nincs olyan mechanizmus, amely automatikusan elindítja egy másik csomóponttal folytatott kommunikációt.  
  - Ha egy **SAP HANA készenléti állapotú Scale-Out** , akkor jellemző a pillanatkép-eszközök telepítése és konfigurálása a fő csomóponton. Ha azonban a főcsomópont elérhetetlenné válik, a készenléti csomópont átveszi a fő csomópont szerepkört. Ebben az esetben a megvalósítási csapatnak a pillanatkép-eszközöket mindkét csomóponton (fő és készenléti) kell konfigurálnia a kihagyott Pillanatképek elkerülése érdekében. Normál állapotban a fő csomópont a crontab által kezdeményezett HANA-pillanatképeket fogja elkészíteni, de a főcsomópont feladatátvétele után a pillanatképeket egy másik csomópontból kell végrehajtani, például az új főcsomópontról (korábbi készenléti állapot). Ennek az eredménynek a megvalósításához a készenléti csomópontnak telepítve kell lennie a pillanatkép-eszköz telepítésének, a Storage-kommunikáció engedélyezésének, a hdbuserstore konfigurálásának, a `azacsnap.json` konfigurálásnak és a crontab-parancsoknak a feladatátvétel előtt.
  - HA egy **SAP HANA HSR** , akkor javasoljuk, hogy telepítse, konfigurálja és ütemezze a pillanatkép-eszközöket mindkét (elsődleges és másodlagos) csomóponton. Ezt követően, ha az elsődleges csomópont elérhetetlenné válik, a másodlagos csomópont átveszi a Másodlagosnál készített pillanatképeket. A normál állapotban az elsődleges csomópont a crontab által kezdeményezett HANA-pillanatképeket fogja használni, és a másodlagos csomópont megkísérli a pillanatképek elvégzését, de az elsődleges működése megfelelően meghiúsul.  Az elsődleges csomópont feladatátvétele után azonban ezek a pillanatképek a másodlagos csomópontról lesznek végrehajtva. Ennek az eredménynek a megvalósításához a másodlagos csomópontnak telepítve kell lennie a pillanatkép-eszköznek, a Storage-kommunikáció engedélyezve van, `hdbuserstore` konfigurálva, azacsnap.jskonfigurálva, a crontab pedig engedélyezve van a feladatátvétel előtt.

## <a name="guidance-provided-in-this-document"></a>A dokumentumban szereplő útmutatás

A pillanatkép-eszközök használatának szemléltetésére a következő útmutatás nyújt segítséget.

### <a name="taking-snapshot-backups"></a>Pillanatképek biztonsági mentésének készítése

- [A tárolási pillanatkép előfeltételei](azacsnap-installation.md#prerequisites-for-installation)
  - [Kommunikáció engedélyezése a Storage szolgáltatással](azacsnap-installation.md#enable-communication-with-storage)
  - [Kommunikáció engedélyezése SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Pillanatképek manuális készítése](azacsnap-tips.md#take-snapshots-manually)
- [Az automatikus pillanatkép biztonsági mentésének beállítása](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [A pillanatképek figyelése](azacsnap-tips.md#monitor-the-snapshots)
- [Pillanatkép törlése](azacsnap-tips.md#delete-a-snapshot)
- [Pillanatkép visszaállítása](azacsnap-tips.md#restore-a-snapshot)
- [`boot`Pillanatkép visszaállítása](azacsnap-tips.md#restore-a-boot-snapshot)
- [A pillanatképekkel kapcsolatos legfontosabb tudnivalók](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> A pillanatképeket az egyszeri SID és a többszörös SID is teszteli.

### <a name="performing-disaster-recovery"></a>Vész-helyreállítás végrehajtása

- [A DR telepítésének előfeltételei](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Vész-helyreállítás beállítása](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Az adatok replikációjának figyelése az elsődlegesről a DR webhelyre](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Hogyan hajtható végre feladatátvétel a DR webhelyre?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Application konzisztens pillanatkép-eszköz telepítése](azacsnap-installation.md)