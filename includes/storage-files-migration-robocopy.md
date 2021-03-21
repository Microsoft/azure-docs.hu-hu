---
title: Mappa szerkezetének leképezése Azure File Sync topológiára
description: Egy meglévő fájl-és mappa struktúrájának leképezése az Azure-fájlmegosztás számára a Azure File Sync való használatra. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547550"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Kapcsoló (switch)              | Értelmezés |
|---------------------|---------|
| /MT                 | Lehetővé teszi, hogy a RoboCopy több szálon fusson. Az alapértelmezett érték 8, a maximális érték 128. Ezt az értéket a processzor alapszámának és a szálak darabszámának megfelelően adja meg. Gondolja át, hogy a magoknak le kell-e foglalni az üzemi kiszolgáló más feladataihoz. |
| /NP                 | Az egyes fájlok és mappák másolásának folyamata nem jelenik meg. Az előrehaladás megjelenítése jelentősen csökkenti a másolási teljesítményt. |
| /NFL                | Megadja, hogy a fájlnevek ne legyenek naplózva. Javítja a másolási teljesítményt. |
| /NDL                | Megadja, hogy a könyvtárnevek ne legyenek naplózva. Javítja a másolási teljesítményt. |
| /B                  | A RoboCopy szolgáltatást ugyanazon a módban futtatja, amikor a biztonságimásolat-készítő alkalmazás használni fogja. Lehetővé teszi, hogy a RoboCopy olyan fájlokat helyezzen át, amelyekhez az aktuális felhasználónak nincs engedélye. |
| /MIR                | A *cél a forrás és a* cél között, hogy a Robocopy csak a forrás és a cél közötti különbözeteket másolja. Az üres alkönyvtárakat a rendszer átmásolja. A rendszer átmásolja a célhelyen módosított vagy nem létező elemeket (fájlokat vagy mappákat). A célhelyen lévő, de a forráson nem szereplő elemek törlődnek a célhelyről. Ha ezt a kapcsolót használja, a forrás-és a célmappa szerkezetének pontosan egyeznie kell. A "megfeleltetés" azt jelenti, hogy a megfelelő forrás-és mappa-szintről másolja a célhelyen a megfelelő mappa szintjére. A "felzárkózás" sikeres másolás csak ezután lehetséges. Ha a forrás és a cél nem egyezik, a használata `/MIR` nagy léptékű törlést és újramásolást eredményez. |
| /IT                 | Gondoskodik arról, hogy a hűség megőrzése bizonyos tükrözési helyzetekben megmaradjon. </br>*Példa* – ha két Robocopy között egy fájl egy ACL-változást és egy attribútum-frissítést futtat, a rendszer *elrejti* a következőt:. /IT nélkül az ACL változása kihagyható a RoboCopy által, és nem továbbítható a célhelyre. |
|Másolja`[copyflags]`  | A fájl másolatának hűsége (ha nincs megadva, az alapértelmezett érték `/COPY:DAT` ), a másolási jelzők: `D` = adat, `A` = attribútumok, `T` = timestamps, `S` = Biztonság = NTFS ACL-ek, `O` = tulajdonos adatai, `U` = egy<u>u</u>-megjegyzési információ. A naplózási adatok nem tárolhatók Azure-fájlmegosztás esetén. |
| /DCOPY:`[copyflags]`| A címtárak másolatának hűsége (ha nincs megadva, az alapértelmezett érték `/DCOPY:DA` ), a másolási jelzők: `D` = érték, `A` = attribútumok, `T` = időbélyeg. |
| /UNILOG:<file name> | Az állapotot a NAPLÓFÁJLba UNICODE-ként adja vissza (felülírja a meglévő naplót). |
| /LFSM               | **csak a többplatformos tárolással rendelkező célok esetében** </br>A/LFSM-kérelmek használata a RoboCopy használatával alacsony szabad lemezterület módban működik. Ez a kapcsoló csak a többplatformos tárolással rendelkező célok esetében hasznos, amelyek elfuthatnak a helyi kapacitással a RoboCopy befejezése előtt. Ezt a kapcsolót kifejezetten a Azure File Sync felhőalapú rétegek használatát engedélyező célhoz adta hozzá. Azure File Synctól függetlenül is használható. Ebben a módban a RoboCopy akkor szünetel, amikor egy fájlmásolás azt eredményezi, hogy a cél kötetének szabad területe egy "emelet" érték alá kerül. Ezt az értéket `/LFSM:n` a jelző formájában lehet megadni. A paraméter `n` meg van adva a 2. alap: `nKB` , `nMB` , vagy `nGB` . Ha a `/LFSM` nincs megadva explicit alapértékkel, a padló a cél kötet méretének 10 százalékára van beállítva. Az alacsony szabad terület mód nem kompatibilis a/MT, a/EFSRAW, a/B és a/ZB. |
| /Z                  | **körültekintő használat** </br>Újraindítási módban másolja a fájlokat, a használatát csak instabil hálózati környezetben ajánlott használni. Ez a beállítás jelentősen csökkenti a másolási teljesítményt a további naplózás miatt. |
| /ZB                 | **körültekintő használat** </br>Újraindítási módot használ. Ha a hozzáférés megtagadva, ez a beállítás biztonsági mentési módot használ. Ez a beállítás jelentősen csökkenti a másolási teljesítményt az ellenőrzőpontok miatt. |
   