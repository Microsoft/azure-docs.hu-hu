---
title: Mi az Azure Application konzisztens pillanatkép-eszköz a Azure NetApp Fileshoz | Microsoft Docs
description: Bevezetőt biztosít az Azure Application konzisztens pillanatkép-eszközhöz, amelyet a Azure NetApp Files használatával használhat.
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
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869956"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Mi az Azure Application konzisztens pillanatkép-eszköz (előzetes verzió)

Az Azure Application konzisztens pillanatkép-eszköz (AzAcSnap) egy olyan parancssori eszköz, amely lehetővé teszi a harmadik féltől származó adatbázisok adatvédelmét úgy, hogy az alkalmazás konzisztens állapotba helyezi őket, mielőtt a tárolási pillanatképet elvégezte.

## <a name="supported-platforms-and-os"></a>Támogatott platformok és operációs rendszer

- **Adatbázisok**
  - SAP HANA (részletekért tekintse meg a [támogatási mátrixot](azacsnap-get-started.md#snapshot-support-matrix-from-sap) )

- **Operációs rendszerek**
  - SUSE Linux Enterprise Server 12 +
  - Red Hat Enterprise Linux 7 +

## <a name="benefits-of-using-azacsnap"></a>A AzAcSnap használatának előnyei

A AzAcSnap kihasználja a kötet-pillanatfelvételt és a replikálási funkciókat Azure NetApp Files és az Azure nagyméretű példányain.  A következő előnyöket biztosítja:

- **Alkalmazás – konzisztens adatvédelem** A AzAcSnap egy központosított megoldás a kritikus adatbázisfájlok biztonsági mentéséhez. Biztosítja az adatbázis konzisztenciáját a tárolási kötetek pillanatképének végrehajtása előtt. Ennek eredményeképpen biztosítható, hogy a tárolási kötet pillanatképe használható legyen az adatbázis-helyreállításhoz.
- **Adatbázis-katalógus kezelése** Ha a AzAcSnap-t olyan adatbázissal használja, amely beépített biztonsági mentési katalógussal rendelkezik, a katalógusban lévő rekordok a tároló-pillanatképekkel együtt maradnak.  Ez a funkció lehetővé teszi, hogy az adatbázis rendszergazdája megtekintse a biztonsági mentési tevékenységet.
- **Ad hoc mennyiségi védelem** Ez a képesség olyan nem adatbázis-kötetek esetében hasznos, amelyeknek nincs szükségük az alkalmazás űrlapsablon fokozatos leválasztása a tárolási pillanatkép elkészítése előtt.  Ilyenek például a SAP HANA log-Backup kötetek vagy a SAPTRANS kötetek.
- **A tárolási kötetek klónozása** Ez a funkció lehetővé teszi a tárolási kötetek klónozását a fejlesztési és tesztelési célokra.
- **A vész-helyreállítási támogatás** A AzAcSnap kihasználja a tárolási kötetek replikálását, így lehetőséget biztosít a replikált alkalmazás-konzisztens Pillanatképek helyreállítására egy távoli helyen.

A AzAcSnap egyetlen bináris fájl.  Nincs szükség további ügynökökre vagy beépülő modulokra az adatbázis vagy a tároló (Azure NetApp Files Azure Resource Manager és az Azure nagyméretű példány SSH-n keresztül) való használatához.  A AzAcSnap olyan rendszerre kell telepíteni, amely kapcsolódik az adatbázishoz és a tárolóhoz.  A telepítés és a konfiguráció rugalmassága azonban lehetővé teszi egyetlen központi telepítés vagy teljes körű, az egyes adatbázis-telepítésekre telepített példányok telepítését.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A AzAcSnap telepíthető ugyanarra a gazdagépre, mint az adatbázis (SAP HANA), vagy telepíthető központi rendszerre is.  Azonban hálózati kapcsolatnak kell lennie az adatbázis-kiszolgálókkal és a tároló háttérrel (Azure Resource Manager Azure NetApp Files vagy SSH esetében az Azure nagyméretű példánya esetében).

A AzAcSnap egy egyszerűsített alkalmazás, amelyet általában egy külső ütemező hajt végre.  A legtöbb Linux rendszeren ez a művelet az `cron` , amit a dokumentáció fog összpontosítani.  Az ütemező azonban egy másik eszköz is lehet, ha importálja a `azacsnap` felhasználó rendszerhéj-profilját.  A felhasználó környezeti beállításainak importálása biztosítja a fájlelérési utak és engedélyek megfelelő inicializálását.

## <a name="command-synopsis"></a>Parancs-áttekintés

A parancsok általános formátuma a következő: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Parancs beállításai

A parancs beállításai a következők: a fő felsorolásjelek és a hozzájuk tartozó alparancsok behúzott felsorolásjelként való megadására szolgáló parancsok.

- **`-h`** a kiterjesztett parancssori segítséget nyújt a AzAcSnap-használattal kapcsolatos példákkal.
- **`-c configure`** Ez a parancs interaktív Q&biztosít a konfigurációs fájl létrehozásához vagy módosításához `azacsnap` (alapértelmezett = `azacsnap.json` ).
  - **`--configuration new`** létrehoz egy új konfigurációs fájlt.
  - **`--configuration edit`** egy meglévő konfigurációs fájl szerkesztését fogja végrehajtani.
  - Tekintse át a [parancssor konfigurálását ismertető témakört](azacsnap-cmd-ref-configure.md).
- **`-c test`** ellenőrzi a konfigurációs fájlt, és teszteli a kapcsolatot.
  - **`--test hana`**  a SAP HANA-példányhoz való kapcsolódás tesztelése.
  - **`--test storage`** az alapul szolgáló tárolási felülettel folytatott kommunikációt úgy ellenőrzi, hogy létrehoz egy ideiglenes tárolási pillanatképet az összes konfigurált `data` köteten, majd eltávolítja őket.
  - **`--test all`** a és a teszteket is végrehajtja egymás `hana` `storage` után.
  - Tekintse át a [tesztelési parancs referenciáját](azacsnap-cmd-ref-test.md).
- **`-c backup`** az elsődleges parancs, amely az adatbázis konzisztens tárolási pillanatképeit (SAP HANA adatköteteket) hajtja végre, & más (például megosztott, naplóbeli biztonsági mentéseket vagy rendszerindító) köteteket.
  - **`--volume data`** a konfigurációs fájl összes kötetének pillanatképét `dataVolume` .
  - **`--volume other`** a konfigurációs fájl összes kötetének pillanatképét `otherVolume` .
  - Tekintse át a [biztonsági mentési parancs referenciáját](azacsnap-cmd-ref-backup.md).
- **`-c details`** a pillanatképekkel és a replikálással kapcsolatos információkat tartalmaz.
  - **`--details snapshots`** Az egyes konfigurált kötetekhez tartozó Pillanatképek alapvető részleteinek listáját jeleníti meg.
  - **`--details replication`** Alapszintű részleteket biztosít az üzemi helyről a vész-helyreállítási helyre irányuló replikálási állapot körül.
  - Tekintse meg a [részletek parancs hivatkozását](azacsnap-cmd-ref-details.md).
- **`-c delete`** Ez a parancs törli a tárolási pillanatképet vagy Pillanatképek készletét. Használhatja a SAP HANA biztonsági mentési azonosítót a HANA Studióban vagy a tárolási pillanatkép neve alatt. A biztonsági mentési azonosító csak a `hana` pillanatképekhez van kötve, amelyek az adatés a megosztott kötetek számára jönnek létre. Ellenkező esetben, ha a pillanatkép nevét megadta, a megkeresi az összes pillanatképet, amely megfelel a megadott pillanatkép nevének.
  - Lásd: [Törlés](azacsnap-cmd-ref-delete.md).
- **`-c restore`** a két módszert biztosít a pillanatképek kötetre való visszaállításához, vagy új kötetet hoz létre a pillanatkép alapján, vagy visszaállítja a kötetet az előzetes verzió állapotára.
  - **`--restore snaptovol`** Létrehoz egy új kötetet a célként megadott köteten lévő legújabb pillanatkép alapján.
  - **`-c restore --restore revertvolume`** A legutóbbi pillanatkép alapján visszaállít egy korábbi állapotba a célként megadott kötetet.
  - Tekintse át a [Restore parancs referenciáját](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** A választható parancssori paraméter a JSON-konfiguráció eltérő fájlnevének megadásához.  Ez különösen akkor hasznos, ha SID-ként (például) külön konfigurációs fájlt kíván létrehozni `--configfile H80.json` .

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés az Azure Application konzisztens pillanatkép-eszközzel](azacsnap-get-started.md)
