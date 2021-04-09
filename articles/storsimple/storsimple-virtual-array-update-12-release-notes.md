---
title: Microsoft Azure StorSimple Virtual Array Update 1,2 kibocsátási megjegyzések | Microsoft Docs
description: Az 1,2-es frissítést futtató StorSimple virtuális tömb kritikus megnyitási problémáit és megoldásait ismerteti.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960190"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1,2 kibocsátási megjegyzések

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtuális tömb frissítéseinek megoldott problémáit.

A kibocsátási megjegyzések folyamatosan frissülnek. Ahogy ismertté válnak a megoldást igénylő kritikus fontosságú problémák, hozzáadjuk őket a dokumentumhoz. A StorSimple virtuális tömb üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Az 1,2-es frissítés megfelel a szoftver verziójának **10.0.10311.0**.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz inkurzív állásidőt okoz. A frissítés alkalmazásához használt csomagokra vonatkozó részletes utasításokért keresse fel az 1,2-es [frissítés letöltését](#download-update-12)ismertető témakört.
> - Az 1,2-es frissítés csak akkor érhető el a Azure Portalon keresztül, ha az eszközön a 1,0-es vagy a 1,1-es frissítés fut.

## <a name="whats-new-in-update-12"></a>Az 1,2-es frissítés újdonságai

Ez a frissítés a következő hibajavításokat tartalmazza:

- Javított rugalmasság a törölt fájlok feldolgozásakor.
- Továbbfejlesztett kezelési kivételek a kód indítási útvonalán a biztonsági mentések, a visszaállítás, a Felhőbeli olvasások és az automatizált lemezterület-visszanyerési hibák csökkenése miatt.

## <a name="download-update-12"></a>Az 1,2-es frissítés letöltése

A frissítés letöltéséhez nyissa meg a [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) Servert, és töltse le a KB4502035 csomagot. Ez a csomag a következő csomagokat tartalmazza:

 - **KB4493446** , amely összesített Windows-frissítéseket tartalmaz 2012 R2 és április 2019 között. További információ a jelen összesítésben található adatokról: [április havi biztonsági összesítés](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - A **KB3011067** , amely egy Microsoft Update önálló csomagfájl WINDOWSTH-KB3011067-x64. Ez a fájl az eszköz szoftverének frissítésére szolgál.

Töltse le a KB4502035, és kövesse ezeket az utasításokat a [frissítés helyi webes felületen való alkalmazásához](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Az 1,2-es frissítésben rögzített problémák

A következő táblázat az ebben a kiadásban kijavított problémák összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Törlés| A szoftver korábbi verzióiban probléma merült fel, ha az eszköz használata még a fájlok törlésekor sem változott. Ez a probléma ebben a verzióban van kijavítva. A kibővíthető kód elérési útja rugalmasabb volt a törölt fájlok feldolgozásakor.|
| 2 |Kivételkezelés| A szoftver korábbi verzióiban a rendszer újraindítása után probléma merült fel, amely a biztonsági mentések, a visszaállítás, a felhőből való olvasás és az automatizált lemezterület-visszanyerési hibákhoz vezethet. Ez a kiadás a kivételek indítási útvonalon való kezelésének változásait mutatja be.|

## <a name="known-issues-in-update-12"></a>Az 1,2-es frissítés ismert problémái

Az 1,2-es frissítésben nincsenek megjelent új problémák. Az összes kiadási feljegyzett probléma a korábbi kiadásokból történik. Az előző kiadásokban található ismert problémák összegzésének megtekintéséhez lépjen az [1,1-es frissítés ismert problémáira](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Következő lépések

Töltse le a KB4502035, és [alkalmazza a frissítést helyi webes felületen keresztül](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Hivatkozások

Régebbi kiadási megjegyzést keres? Ugrás:
* [StorSimple Virtual Array Update 1,1 kibocsátási megjegyzések](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1,0 kibocsátási megjegyzések](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 kibocsátási megjegyzések](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 és 0,2 kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános elérhetőségi kibocsátási megjegyzései](./storsimple-virtual-array-update-06-release-notes.md)