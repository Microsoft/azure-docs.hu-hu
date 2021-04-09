---
title: StorSimple 8000 sorozat Update 4 kibocsátási megjegyzések | Microsoft Docs
description: A StorSimple 8000 Series Update 4 új funkcióit, problémáit és megkerülő megoldásait ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954052"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 sorozat Update 4 kibocsátási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 Series 4. frissítésével kapcsolatos kritikus nyitott problémákat. Emellett tartalmazzák az ebben a kiadásban található StorSimple szoftverfrissítések listáját is. 

A 4-es frissítés a 3,1-es frissítéssel bármely StorSimple-eszközön, vagy a 0,1-es frissítésen is alkalmazható. A 4-es frissítéshez társított 6.3.9600.17820.

Mielőtt telepítené a frissítést a StorSimple-megoldásban, tekintse át a kibocsátási megjegyzésekben található információkat.

> [!IMPORTANT]
> * A 4. frissítéshez eszköz szoftver, USM belső vezérlőprogram, LSI-illesztőprogram és belső vezérlőprogram, a lemez belső vezérlőprogramja, a Storport és a ûrkikötõt, a biztonság és más operációsrendszer-frissítések szükségesek. A frissítés telepítése körülbelül 4 órát vesz igénybe. A lemezes belső vezérlőprogram frissítése zavaró frissítés, és az eszköz állásidőjét eredményezi. Javasoljuk, hogy a 4. frissítést alkalmazza az eszköz naprakészen tartásához. 
> * Az új kiadások esetében előfordulhat, hogy a frissítések nem jelennek meg azonnal, mert a frissítések fokozatos bevezetését hajtjuk végre. Várjon néhány napot, majd próbálkozzon újra a frissítések keresésével, mivel ezek hamarosan elérhetővé válnak.

## <a name="whats-new-in-update-4"></a>A 4. frissítés újdonságai

A 4. frissítés a következő főbb javításokat és hibajavításokat tartalmazza.

* **Intelligensebb automatizált lemezterület-visszanyerési algoritmusok** – a 4. frissítésben az automatizált lemezterület-visszanyerési algoritmusok a felhőben elérhető várható visszaigényelt terület alapján módosítják a lemezterület-kinyerési ciklusokat. 

* A **helyileg rögzített kötetek teljesítményének továbbfejlesztése** – a 4. frissítés javította a helyileg rögzített kötetek teljesítményét olyan helyzetekben, amelyekben nagy adatfeldolgozás történt (a kötetek méretétől hasonló adatok).

* **Hő-alapú visszaállítás** – a korábbi kiadásokban, a vész-helyreállítást követően (Dr) az adatok a felhőből lettek visszaállítva a hozzáférési minták alapján, ami lassú teljesítményt eredményezett. 

    Egy új funkció a 4. frissítésben van megvalósítva, amely nyomon követi a gyakran használt és a hő létrehozását, ha az eszköz a DR előtt használatban van DR. után a StorSimple a hő használatával automatikusan visszaállítja és kihasználja az adatok felhőből történő helyreállítását. 

    A visszaállítások mostantól hő-alapú visszaállítások. A hő-alapú visszaállítási és rehidratáló feladatok lekérdezésével és megszakításával kapcsolatos további információkért nyissa meg [Windows PowerShell StorSimple-bővítménye parancsmag-referenciát](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

* A **StorSimple Diagnostics eszköz** – a 4. frissítésben egy StorSimple diagnosztikai eszközt bocsát ki, amely lehetővé teszi a rendszer, a hálózat, a teljesítmény és a hardver-összetevők állapotával kapcsolatos problémák egyszerű diagnosztizálását és hibaelhárítását. Ez az eszköz a Windows PowerShell StorSimple-bővítményeon keresztül fut. További információ: [hibakeresés a StorSimple Diagnostics eszközzel](storsimple-8000-diagnostics.md).

* **Felhasználói felület-alapú StorSimple áttelepítési eszköz** – a jelen kiadás előtt az 5000-7000-es adatsorozatból származó adatok áttelepítéséhez a felhasználóknak a Azure PowerShell felületen kell végrehajtaniuk az áttelepítési munkafolyamat egy részét. Ebben a kiadásban egy könnyen használható, felhasználói felületen alapuló StorSimple áttelepítési eszköz érhető el, amely lehetővé teszi az azonos áttelepítési munkafolyamatok használatát. Ez az eszköz a helyreállítási gyűjtők összevonását is lehetővé teszi. 

* **FIPS-vel kapcsolatos változások** – ebben a kiadásban a FIPS alapértelmezés szerint engedélyezve van az összes StorSimple 8000 Series-eszközön mind a Microsoft Azure Government, mind az Azure-beli nyilvános Felhőbeli fiók esetében.

* **Változások frissítése** – ebben a kiadásban a frissítési hibákkal kapcsolatos hibák megoldódott.

* **Lemez meghibásodására figyelmeztető riasztás** – új riasztás, amely figyelmezteti a felhasználót, hogy a közelgő lemezhibák bekerülnek ebben a kiadásban. Ha ezt a riasztást tapasztalja, lépjen kapcsolatba Microsoft ügyfélszolgálata a helyettesítő lemez szállításához. További információkért keresse fel a [StorSimple-eszköz hardveres riasztásait](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Vezérlő-helyettesítési változások** – ez a kiadás lehetővé teszi, hogy a felhasználó lekérdezze a vezérlő helyettesítési folyamatának állapotát. További információért lépjen a [parancsmagra a vezérlő helyettesítési állapotának lekérdezéséhez](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).


## <a name="issues-fixed-in-update-4"></a>A 4. frissítésben rögzített problémák

A következő táblázat a 4. frissítésben rögzített problémák összegzését tartalmazza.    

| Nem | Funkció | Probléma | A fizikai eszközre vonatkozik | A virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Feladatátvétel |A korábbi kiadásban a feladatátvétel után az ügyfél webhelyén megfigyelt törléssel kapcsolatos probléma merült fel. Ez a probléma ebben a kiadásban van kijavítva. |Igen |Yes |
| 2 |Helyileg rögzített kötetek |Az előző kiadásban probléma merült fel a kapcsolódó kötetek létrehozásakor a helyileg rögzített kötetek esetében, amelyek mennyiségi létrehozási hibákhoz vezethetnek. Ezt a problémát a jelen kiadásban a gyökér okozta és javítottuk. |Igen |Nem |
| 3 |Támogatási csomag |A korábbi kiadásokban a támogatási csomaggal kapcsolatos problémák merültek fel, amelyek egy rendszer. OutOfMemory kivételt vagy más hibát eredményeznek, ami a támogatási csomag létrehozásakor meghiúsult. Ezek a hibák ebben a kiadásban vannak kijavítva. |Igen |Yes |
| 4 |Figyelés |A korábbi kiadásokban a helyileg rögzített kötetekhez kapcsolódó figyelési diagramokkal kapcsolatos probléma merült fel, ahol a felhasználás az EB-ben volt látható. Ez a hiba a jelen kiadásban van feloldva. |Igen |Yes |
| 5 |Migrálás |A korábbi kiadásokban számos probléma merült fel az 5000-7000-es sorozatból a 8000 sorozatú eszközökre való Migrálás megbízhatóságával kapcsolatban. Ezek a problémák ebben a kiadásban lettek feldolgozva. |Igen |Yes |
| 6 |Frissítés |A korábbi kiadásokban, ha frissítési hiba történt, a vezérlők helyreállítási módba kerülhetnek, így a felhasználó nem folytathatja a frissítést, és kapcsolatba kell lépnie Microsoft ügyfélszolgálataval. <br> Ebben a kiadásban ez a viselkedés megváltozott. Ha a felhasználó olyan frissítési hibát észlelt, hogy mindkét vezérlő ugyanazt a verziót futtatja (4. frissítés), akkor a vezérlők nem kerülnek helyreállítási módba. Ha a felhasználó ezt a hibát tapasztalja, javasoljuk, hogy várjon egy kicsit, majd próbálkozzon újra a frissítéssel. Az újrapróbálkozás sikeres volt. Ha az újrapróbálkozás sikertelen, vegye fel a kapcsolatot a Microsoft ügyfélszolgálatakal. |Igen |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Az előző verziók 4. frissítésének ismert problémái

Nincsenek új ismert problémák a 4. frissítésben. Az előző kiadásokban a 4. frissítés során végrehajtott problémák listáját a [3. frissítéssel kapcsolatos megjegyzésekben](storsimple-update3-release-notes.md#known-issues-in-update-3)keresse meg.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Sorosan csatlakoztatott SCSI (SAS) vezérlő és belső vezérlőprogram frissítései a 4. frissítésben

Ez a kiadás SAS-vezérlővel és LSI-illesztőprogrammal és belső vezérlőprogram-frissítésekkel rendelkezik. A frissítések telepítésével kapcsolatos további információkért lásd: a [4. frissítés telepítése](./storsimple-8000-install-update-4.md) a StorSimple-eszközön.

## <a name="virtual-device-updates-in-update-4"></a>Virtuális eszközök frissítései a 4. frissítésben

Ez a frissítés nem alkalmazható a StorSimple Cloud Appliancera (más néven virtuális eszközre). Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés

Megtudhatja, hogyan [telepítheti a 4. frissítést](./storsimple-8000-install-update-4.md) a StorSimple-eszközre.