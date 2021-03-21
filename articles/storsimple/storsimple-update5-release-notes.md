---
title: StorSimple 8000 sorozat Update 5 kibocsátási megjegyzések
description: A StorSimple 8000 Series Update 5 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021044"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 sorozat Update 5 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series Update 5-ös verziójának kritikus megnyitási problémáit. Emellett tartalmazzák az ebben a kiadásban található StorSimple szoftverfrissítések listáját is.

Az 5. frissítés bármely, a 0,1-es frissítést futtató StorSimple-eszközre alkalmazható a 4. frissítéssel. Az 5. frissítéshez társított 6.3.9600.17845.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
> * Az 5. frissítés egy kötelező frissítés, amelyet azonnal telepíteni kell. További információ: az [5. frissítés alkalmazása](storsimple-8000-install-update-5.md).
> * Az 5. frissítés az eszköz szoftverét, a lemez belső vezérlőprogramot, az operációs rendszer biztonságát és az egyéb operációsrendszer-frissítéseket tartalmaz. A frissítés telepítése körülbelül 4 órát vesz igénybe. A lemezes belső vezérlőprogram frissítése zavaró frissítés, és az eszköz állásidőjét eredményezi. Javasoljuk, hogy az 5. frissítést alkalmazza az eszköz naprakészen tartásához.
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, és ismételje meg a frissítések keresését, mivel ezek a frissítések hamarosan elérhetővé válnak.

## <a name="whats-new-in-update-5"></a>Az 5. frissítés újdonságai

Az 5. frissítés az alábbi főbb javításokat és hibajavításokat tartalmazza.

* **Azure Active Directory (HRE) használata a StorSimple Eszközkezelő Service** -ben való hitelesítéshez – az 5. frissítéstől kezdve Azure Active Directory a rendszer a StorSimple Eszközkezelő szolgáltatással történő hitelesítésre szolgál. A régi hitelesítési mechanizmus elavulttá válik a 2017 decemberében. Az összes felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályok szabályaiban. További információ: [a StorSimple-eszköz hálózati követelményeiben felsorolt hitelesítési URL-címek](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Ha a tűzfalszabályok nem tartalmazzák a hitelesítési URL-címet, a felhasználók kritikus riasztást kapnak arról, hogy a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatást. Ha a felhasználók ezt a riasztást látják, fel kell venniük az új hitelesítési URL-címet. További információkért keresse fel a [StorSimple hálózati riasztások](storsimple-8000-manage-alerts.md#networking-alerts)című témakört.

* A **StorSimple új verziója Snapshot Manager** – a StorSimple Snapshot Manager új verziója jelenik meg az 5. frissítéssel, és kompatibilis a 4-es vagy újabb frissítést futtató összes StorSimple-eszközzel. Javasoljuk, hogy frissítsen erre a verzióra. A StorSimple Snapshot Manager előző verziója a 3. frissítést vagy a korábbi verziót futtató StorSimple-eszközökön használható. [Töltse le a StorSimple Snapshot Manager megfelelő verzióját](https://www.microsoft.com/en-us/download/details.aspx?id=44220) , és tekintse meg a [StorSimple Snapshot Manager üzembe helyezése című témakört](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Az 5. frissítésben rögzített problémák

Az alábbi táblázat az 5. frissítésben rögzített problémák összegzését tartalmazza.

| Nem | Funkció | Probléma | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell – távelérés |Az előző kiadásban a felhasználó hibaüzenetet kap, amikor távoli kapcsolatot próbál létesíteni a StorSimple Cloud Appliance a Windows PowerShell használatával. Ezt a problémát a jelen kiadásban a gyökér okozta és javítottuk. |Nem |Igen |
| 2 |Sávszélesség-sablonok |A korábbi kiadásokban probléma merült fel olyan sávszélesség-sablonokkal, amelyek alacsonyabb sávszélességet eredményeztek, mint amit az eszközhöz konfiguráltak. Ez a probléma ebben a kiadásban van feloldva. |Igen |Yes |
| 3 |Feladatátvétel |Ha a korábbi kiadásban nagy mennyiségű kötettel rendelkező eszközt hajtottak végre a 4. frissítést futtató másik eszközre, a folyamat sikertelen lesz, amikor megpróbálja alkalmazni a hozzáférés-vezérlési rekordokat. Ez a probléma ebben a kiadásban van kijavítva. |Igen |Yes |



## <a name="known-issues-in-update-5-from-previous-releases"></a>A korábbi kiadások 5. frissítésének ismert problémái

Nincsenek új ismert problémák az 5. frissítésben. A korábbi kiadásokban az 5. frissítés során végrehajtott problémák listáját a [3. frissítéssel kapcsolatos megjegyzésekben](storsimple-update3-release-notes.md#known-issues-in-update-3)keresse meg.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Sorosan csatlakoztatott SCSI (SAS) vezérlő és belső vezérlőprogram frissítései az 5. frissítésben

Ez a kiadás SAS-vezérlővel és LSI-illesztőprogrammal és belső vezérlőprogram-frissítésekkel rendelkezik. A frissítések telepítésével kapcsolatos további információkért lásd: az [5. frissítés telepítése](storsimple-8000-install-update-5.md) a StorSimple-eszközön.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Az 5. frissítés StorSimple Cloud Appliance frissítései

Ez a frissítés nem alkalmazható a StorSimple Cloud Appliancera (más néven virtuális eszközre). Az új felhőalapú berendezéseket az 5. frissítés rendszerkép használatával kell létrehozni. StorSimple Cloud Appliance létrehozásával kapcsolatos információkért lépjen a [StorSimple Cloud Appliance üzembe helyezése és kezelése](storsimple-8000-cloud-appliance-u2.md)című témakörben.

## <a name="next-step"></a>Következő lépés

Ismerje meg, hogyan [telepítheti az 5. frissítést](storsimple-8000-install-update-5.md) a StorSimple-eszközön.

