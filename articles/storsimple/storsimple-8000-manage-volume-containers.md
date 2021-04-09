---
title: Mennyiségi tárolók kezelése a StorSimple 8000 Series-eszközökhöz
description: Ez a cikk azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás mennyiségi tárolói lapon hogyan lehet hozzáadni, módosítani vagy törölni a mennyiségi tárolókat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545280"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple-kötettárolók kezelése a StorSimple-eszközkezelő szolgáltatással

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás a StorSimple-tárolók létrehozásához és kezeléséhez.

Egy Microsoft Azure StorSimple eszközön lévő mennyiségi tároló egy vagy több olyan kötetet tartalmaz, amelyek osztoznak a Storage-fiók, a titkosítás és a sávszélesség-felhasználás beállításaiban. Egy eszközön több kötet is tárolható az összes kötethez. 

A kötetek tárolója a következő tulajdonságokkal rendelkezik:

* **Kötetek** – a mennyiségi tárolóban található, lépcsőzetes vagy helyileg rögzített StorSimple-kötetek. 
* **Titkosítás** – az egyes kötet-tárolók számára definiálható titkosítási kulcs. Ez a kulcs a StorSimple-eszközről a felhőbe továbbított adatok titkosítására szolgál. A felhasználó által megadott kulccsal egy katonai szintű AES-256 bites kulcsot használunk. Az adatai védelme érdekében javasoljuk, hogy mindig engedélyezze a Felhőbeli tárolás titkosítását.
* **Storage-fiók** – az adattároláshoz használt Azure Storage-fiók. A mennyiségi tárolóban lévő összes kötet megosztotta ezt a Storage-fiókot. Kiválaszthat egy meglévő listából a Storage-fiókot, vagy létrehozhat egy új fiókot a kötet-tároló létrehozásakor, majd megadhatja a fiókhoz tartozó hozzáférési hitelesítő adatokat.
* **Felhőbeli sávszélesség** – az eszköz által az eszközről a felhőbe küldött adatokhoz felhasznált sávszélesség. A sávszélesség-vezérlés kikényszeríthető úgy, hogy a tároló létrehozásakor 1 Mbps és 1 000 Mbps közötti értéket ad meg. Ha azt szeretné, hogy az eszköz az összes rendelkezésre álló sávszélességet használja, állítsa ezt a mezőt **korlátlanra**. Emellett sávszélesség-sablont is létrehozhat és alkalmazhat a sávszélességnek az ütemterv alapján való lefoglalásához.

A következő eljárások azt ismertetik, hogyan használható a StorSimple **Volume containers** panel a következő gyakori műveletek végrehajtásához:

* Mennyiségi tároló hozzáadása
* Mennyiségi tároló módosítása
* Mennyiségi tároló törlése

## <a name="add-a-volume-container"></a>Mennyiségi tároló hozzáadása
Egy mennyiségi tároló hozzáadásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Mennyiségi tároló módosítása
A kötetek tárolójának módosításához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Mennyiségi tároló törlése
A mennyiségi tárolóban kötetek találhatók. Csak akkor törölhető, ha az összes benne található kötetet először törli. A mennyiségi tárolók törléséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple-kötetek kezeléséről](storsimple-8000-manage-volumes-u2.md). 
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

