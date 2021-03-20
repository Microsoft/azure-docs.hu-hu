---
title: StorSimple 8000 sorozatú biztonsági mentési szabályzatok kezelése | Microsoft Docs
description: A cikk azt ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás manuális biztonsági mentések, biztonsági mentési ütemezések és a biztonsági másolatok megőrzésének a StorSimple 8000 sorozatú eszközön való létrehozásához és kezeléséhez.
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515623"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>A StorSimple Eszközkezelő szolgáltatás használata Azure Portal a biztonsági mentési házirendek kezeléséhez


## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás **biztonsági mentési házirendjének** paneljén a biztonsági mentési folyamatok és a biztonsági másolatok megőrzésének vezérléséhez a StorSimple kötetek esetében. Azt is leírja, hogyan lehet elvégezni a manuális biztonsági mentést.

A kötetek biztonsági mentésekor dönthet úgy, hogy létrehoz egy helyi pillanatképet vagy egy Felhőbeli pillanatképet. Ha helyileg rögzített kötetről készít biztonsági mentést, javasoljuk, hogy egy Felhőbeli pillanatképet határozzon meg. Ha nagy számú helyi pillanatképet készít egy helyileg rögzített kötetről, és egy olyan adatkészlettel párosul, amely sok időt vesz igénybe, akkor olyan helyzetet eredményezhet, amelyben gyorsan elfogyott a helyi terület. Ha úgy dönt, hogy helyi pillanatképeket szeretne készíteni, javasoljuk, hogy készítsen kevesebb napi pillanatképet a legutóbbi állapot biztonsági mentésére, és őrizze meg őket egy napig, majd törölje őket.

Amikor Felhőbeli pillanatképet készít egy helyileg rögzített kötetről, a rendszer csak a módosított adatokról másolja a felhőbe, és deduplikálja és tömöríti.

## <a name="the-backup-policy-blade"></a>A biztonsági mentési szabályzat panel

A StorSimple-eszköz **biztonsági mentési** szabályzatának panelje lehetővé teszi a biztonsági mentési házirendek kezelését, valamint a helyi és a Felhőbeli Pillanatképek ütemtervét. A biztonsági mentési házirendek a biztonsági mentési ütemtervek és a biztonsági másolatok megőrzésének konfigurálására szolgálnak a kötetek gyűjteménye számára. A biztonsági mentési házirendek lehetővé teszik, hogy egyszerre több kötetről készítsen pillanatképet. Ez azt jelenti, hogy a biztonsági mentési szabályzat által létrehozott biztonsági másolatok összeomlás-konzisztens másolatok lesznek.

A biztonsági mentési szabályzatok táblázatos listája lehetővé teszi a meglévő biztonsági mentési házirendek szűrését is a következő mezők valamelyikével:

* **Házirend neve** – a házirendhez társított név. A különböző típusú házirendek a következők:

  * A felhasználó által explicit módon létrehozott ütemezett szabályzatok.
  * Az importált házirendek, amelyek eredetileg a StorSimple Snapshot Manager lettek létrehozva. Ezek rendelkeznek egy címkével, amely leírja azt a StorSimple Snapshot Manager gazdagépet, amelyen a házirendek importálása megtörtént.

  > [!NOTE]
  > Az automatikus vagy az alapértelmezett biztonsági mentési házirendek a kötetek létrehozásakor már nem engedélyezettek.

* **Utolsó sikeres biztonsági mentés** – a szabályzattal végrehajtott utolsó sikeres biztonsági mentés dátuma és időpontja.

* **Következő biztonsági mentés** – a szabályzat által kezdeményezett következő ütemezett biztonsági mentés dátuma és időpontja.

* **Kötetek** – a Szabályzathoz társított kötetek. A biztonsági mentési szabályzathoz társított kötetek a biztonsági másolatok létrehozásakor vannak csoportosítva.

* **Ütemtervek** – a biztonsági mentési szabályzathoz társított ütemtervek száma.

A biztonsági mentési szabályzatok által elvégezhető gyakran használt műveletek a következők:

* Biztonsági mentési szabályzat hozzáadása
* Ütemterv hozzáadása vagy módosítása
* Kötet hozzáadása vagy eltávolítása
* Biztonsági mentési szabályzat törlése
* Manuális biztonsági mentés készítése

## <a name="add-a-backup-policy"></a>Biztonsági mentési szabályzat hozzáadása

Biztonsági mentési szabályzat hozzáadásával automatikusan ütemezheti a biztonsági mentéseket. Amikor először hoz létre kötetet, a kötethez nem tartozik alapértelmezett biztonsági mentési szabályzat. A kötetek adatainak védelme érdekében hozzá kell adnia és hozzá kell rendelnie egy biztonsági mentési szabályzatot.

A StorSimple-eszközhöz tartozó biztonsági mentési szabályzat hozzáadásához hajtsa végre az alábbi lépéseket a Azure Portalban. A szabályzat hozzáadása után megadhat egy ütemtervet (lásd: [ütemterv hozzáadása vagy módosítása](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Ütemterv hozzáadása vagy módosítása

Hozzáadhat vagy módosíthat egy olyan ütemtervet, amely egy meglévő biztonsági mentési szabályzathoz van csatolva a StorSimple-eszközön. Az ütemterv hozzáadásához vagy módosításához hajtsa végre a következő lépéseket a Azure Portalban.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Kötet hozzáadása vagy eltávolítása

Hozzáadhat vagy eltávolíthat egy biztonsági mentési szabályzathoz rendelt kötetet a StorSimple-eszközön. Kötet hozzáadásához vagy eltávolításához hajtsa végre a következő lépéseket a Azure Portalban.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Biztonsági mentési szabályzat törlése

A StorSimple-eszközre vonatkozó biztonsági mentési szabályzat törléséhez hajtsa végre a következő lépéseket a Azure Portal.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Hajtsa végre az alábbi lépéseket a Azure Portal az igény szerinti (manuális) biztonsági másolat létrehozásához egyetlen köteten.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

