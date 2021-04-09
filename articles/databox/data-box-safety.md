---
title: A Azure Data Box biztonsága | Microsoft Docs
description: Ismerteti a biztonsági konvenciókat, irányelveket és szempontokat, valamint ismerteti a Azure Data Box biztonságos telepítését és üzemeltetését.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: f4a0739eb1ab14155a98b17e2427c5b08c9db788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020670"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>A Azure Data Box biztonságos telepítése és működtetése
![Figyelmeztető ikon ](./media/data-box-safety/warning_icon.png)
 ![ olvassa el a biztonsági értesítés ikonját ](./media/data-box-safety/read_safety_and_health_information_icon.png) **, olvassa el a biztonsági és egészségvédelmi információkat**

A Azure Data Box használata előtt olvassa el az ebben a cikkben található összes biztonsági információt. Az utasítások követésének elmulasztása tüzet, áramütést vagy más sérüléseket eredményezhet, illetve a tulajdonságok károsodását okozhatja.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói
Itt láthatja azokat az ikonokat, amelyekkel megtekintheti a Data Box beállításakor és futtatásakor betartandó biztonsági óvintézkedéseket.

| Ikon | Leírás |
|:--- |:--- |
| ![Veszélyforrás ikon ](./media/data-box-safety/warning_icon.png) **veszélye!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. Ezt a jelet a legszélsőségesebb helyzetekre kell korlátozni. |
| ![Figyelmeztetés ikon ](./media/data-box-safety/warning_icon.png) **Figyelmeztetés!** |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. |
| ![Figyelmeztető ikon ](./media/data-box-safety/warning_icon.png)  figyelmeztetése |Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet. |
| ![Értesítés ikonjának ](./media/data-box-safety/notice_icon.png) **megjegyzése:** |A fontosnak tartott információkat jelöli, de nem kapcsolódik a veszélyforráshoz. |
| ![Elektromos áramütés ikon áramütési ](./media/data-box-safety/electrical_shock_hazard_icon.png) **kockázat** |Magas feszültségű. |
| ![Nagy súly ikon ](./media/data-box-safety/heavy_weight_hazard_icon.png) **nagy súlya** | |
| ![Nincs felhasználó által szervizelhető rész ikonja ](./media/data-box-safety/no_user_serviceable_parts_icon.png) **nem rendelkezik felhasználó által szervizelhető részekkel** |A megfelelő képzés hiányában ne legyen hozzáférés. |
| ![Biztonsági értesítés ikonjának beolvasása – ](./media/data-box-safety/read_safety_and_health_information_icon.png) **első lépések** | |
| ![Tipp kockázati ikon ](./media/data-box-safety/tip_hazard_icon.png) **Tipp – kockázat** | |

## <a name="handling-precautions"></a>Óvintézkedések feldolgozása

![Figyelmeztető ikon ](./media/data-box-safety/warning_icon.png) ![ áramütés ikonja ](./media/data-box-safety/electrical_shock_hazard_icon.png)![ nem rendelkezik a felhasználó által szervizelhető részek ikonjának ](./media/data-box-safety/no_user_serviceable_parts_icon.png)  figyelmeztetésével 

* A *kapott* eszköz bevizsgálása károkért. Ha az eszköz bekerítése sérült, [forduljon a Microsoft ügyfélszolgálatahoz](data-box-disk-contact-microsoft-support.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz üzemeltetését. 
* Az eszköz illetéktelen hozzáférést biztosító csavarokkal van ellátva. Ha azt gyanítja, hogy az eszköz meghibásodik, [forduljon a Microsoft ügyfélszolgálatahoz](data-box-disk-contact-microsoft-support.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz kiszolgálását. 
* Az eszköz nem tartalmaz felhasználó által szervizelhető részt. A veszélyes feszültség, a jelenlegi és az energia szintje belül van. Ne nyissa meg. Az eszköz visszaküldése a Microsoftnak a karbantartáshoz.

![Figyelmeztetés ikon ](./media/data-box-safety/warning_icon.png) ![ nagy súly ikon ](./media/data-box-safety/heavy_weight_hazard_icon.png) **Figyelmeztetés!** 

* Egy teljesen konfigurált ház súlya 22,7 kg (50 lbs) lehet. Ne próbálja meg önállóan feloldani.
* A ház áthelyezése előtt mindig győződjön meg arról, hogy két személy áll rendelkezésre a súlyozás kezelésére. Ügyeljen arra, hogy a súlyozást megkísérlő egyik személy is fenntartsa a sérüléseket.


![Figyelmeztetés ikon ](./media/data-box-safety/warning_icon.png) ![ Tipp kockázati ikon ](./media/data-box-safety/tip_hazard_icon.png) **Figyelmeztetés!**
* Helyezze az eszközt egy sima, kemény és stabil felületen, hogy elkerülje a lehetséges tip-veszélyforrást.
* Az állványra csatlakoztatott berendezések nem használhatók polcok vagy munkaterületek számára. Ne helyezze a Data Box az állványra szerelt berendezések fölé. Ha bármilyen betöltési típust hozzáad egy kiterjesztett állványhoz csatlakoztatott egységhez, létrehozhat egy lehetséges tipp-veszélyforrást, amely sérülést, halált vagy kárt okozhat.

![Figyelmeztetés ikon ](./media/data-box-safety/warning_icon.png) **Figyelmeztetés!**

* Állítsa be az eszközt olyan munkaterületen, amely lehetővé teszi az eszköz megfelelő légforgalmának biztosítását.
* Az eszközt egy hőmérséklet által vezérelt fedett területre kell telepíteni, amely mentes a vezető szennyezőanyagok, és lehetővé teszi az eszközön a megfelelő légáramlást.
* Tartsa meg az eszközt a folyékony és túlzottan párás környezetek forrásaiból.


## <a name="electrical-precautions"></a>Elektromos óvintézkedések

![Figyelmeztető ikon ](./media/data-box-safety/warning_icon.png) ![ áramütés ikonja ](./media/data-box-safety/electrical_shock_hazard_icon.png) **Figyelmeztetés!**

* Biztonságos elektromos földi csatlakozást biztosít a tápkábelhez. Az AC-kábel egy három vezetékes földelő csatlakozóval (egy földelt PIN-kóddal rendelkező csatlakozóval) rendelkezik. Ez a beépülő modul csak egy megalapozott hálózati konnektorhoz illeszkedik. Ne győzje le a földelő PIN-kód célját.
* Mivel a tápkábel csatlakoztatása a fő leválasztási eszköz, győződjön meg arról, hogy a szoftvercsatorna-kimenetek az eszköz közelében találhatók, és könnyen elérhetők.
* Húzza ki a tápkábelt (a dugó kihúzásával, ne a kábel nélkül), és válassza le az összes kábelt, ha az alábbi feltételek bármelyike fennáll:

    - A tápkábel vagy a beépülő modul kikopott vagy más módon sérült lesz.
    - Az eszköz házába való kifolyása.
    - Az eszköz az eső vagy a felesleges nedvesség számára van kitéve.
    - Az eszköz el lett dobva, és az eszköz burkolata sérült.
    - Azt gyanítja, hogy az eszköznek szolgáltatásra vagy javításra van szüksége.
* Véglegesen kihúzza az egységet, mielőtt áthelyezi, vagy ha úgy gondolja, hogy bármilyen módon megsérült.
* Adjon meg egy megfelelő áramforrást elektromos túlterhelés elleni védelemmel a következő energiaellátási követelmények teljesítéséhez:

    - Feszültség: 100 V AC – 240 V AC
    - Jelenlegi: 6 A, maximum
    - Gyakoriság: 50 Hz – 60 Hz

![Figyelmeztető ikon figyelmeztetése ](./media/data-box-safety/warning_icon.png) **:**

* Ez az eszköz érme cella akkumulátorokat tartalmaz. Ne kísérelje meg az eszköz kiszolgálását. Az eszközön lévő elemek nem a felhasználó által szervizelhető. 
* **Csak a szolgáltatási munkatársak esetében**: a robbanás kockázata, ha az akkumulátort helytelen típus váltja fel. A használt elemek használata az utasításoknak megfelelően.

![Értesítés ikonjának ](./media/data-box-safety/notice_icon.png) **megjegyzése:**

Az eszköz megfelelő működéséhez és a termékek károsodásának megakadályozásához:

* Győződjön meg arról, hogy az első és a hátsó ajtó teljesen nyitva van, amíg az eszköz fut.

## <a name="regulatory-information"></a>Szabályozási információk

Ez a szakasz a Azure Data Box és a szabályozási modell DB010 vonatkozó szabályozási információkat tartalmazza.

Az eszköz a következő:

- Kiértékelve informatikai berendezésként, amely egy jellemző adatközpont-környezetben való működésre szolgál. A termék más környezeteknek való megfelelősége további értékelést igényelhet.
- A NRTL (UL, CSA, ETL stb.), valamint az IEC/EN 60950-1 vagy az IEC/EN 62368-1 kompatibilis (CE jelölésű) információtechnológiai berendezésekkel való használatra tervezték.
- A következő környezetben való működésre lett tervezve. 
    - Működési hőmérséklet: 50 ° – 95 ° F (10 ° – 35 ° C)
    - Tárolási hőmérséklet:-4 ° – 122 ° F (– 20 ° – 50 ° C)
    - Relatív páratartalom: 15% – 85% (nem lecsapódó) 
    - Működési magasság: tesztelt akár 6500 méter (0 – 2000 méter)

Az elektromos ellátási minősítések esetében tekintse meg az egységhez megadott eszköz minősítési címkét. 

![Értesítés ikonjának ](./media/data-box-safety/notice_icon.png) **megjegyzése:** 

Az eszközön a Microsoft által kifejezetten nem jóváhagyott módosításokat vagy módosításokat érvénytelenítheti a felhasználó hatósága az eszköz üzemeltetéséhez.

**Kanada és USA:**

![Értesítés ikonjának ](./media/data-box-safety/notice_icon.png) **megjegyzése:** 

A rendszer tesztelte a készüléket, és úgy találta, hogy megfelel az "a" osztályú digitális eszköz korlátainak, az FCC-szabályok 15. részének megfelelően. Ezek a korlátok úgy vannak kialakítva, hogy ésszerű védelmet biztosítanak a káros interferenciák ellen, ha a berendezés kereskedelmi környezetben üzemel. Ez a berendezés a rádiófrekvenciás energia előállítására, felhasználására és kisugárzására szolgál, és ha nincs telepítve és használatban a használati utasítással összhangban, a rádiófrekvenciás kommunikáció káros hatással lehet. A berendezésnek a lakossági környezetben való működése valószínűleg káros interferencia okoz, amely esetben a felhasználónak a saját költségén kell kijavítania a beavatkozást.

Ez az eszköz megfelel az FCC-szabályok és az iparági kanadai licencek alól mentesített RSS-szabványoknak. A művelet a következő két feltétel hatálya alá esik: (1) Ez az eszköz nem okoz káros interferenciát, és (2) az eszköznek el kell fogadnia a kapott interferenciát, beleértve az interferenciát is, ami az eszköz nemkívánatos működéséhez vezethet.

![A képernyőképen a Kanada számára szükséges értesítés látható.](./media/data-box-safety/canada.png)

A KÖVETKEZŐ LEHET: ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.

Egyesült Államok: (800) 426-9400

Kanada: (800) 933-4750

**EURÓPAI UNIÓ:**

[Az EU megfelelőségi nyilatkozatának másolatának kérése](mailto:CSI_Compliance@microsoft.com).

![Figyelmeztetés ikon ](./media/data-box-safety/warning_icon.png) **Figyelmeztetés:** 

Ez egy termék osztálya. Belföldi környezetben Ez a termék a rádiófrekvenciás interferencia okozhatja, amely esetben a felhasználónak szükség lehet a megfelelő intézkedések meghozatalára.

**A hulladék akkuinak és elektromos és elektronikus berendezéseinek ártalmatlanítása:**

![Akkumulátor kivezetésének ikonja](./media/data-box-safety/battery_disposal_icon.png)

A termékre vagy annak elemeire vagy csomagolására vonatkozó szimbólum azt jelenti, hogy a terméket és a benne található elemeket nem szabad a háztartási hulladékból ártalmatlanítani. Ehelyett az Ön felelőssége, hogy ezt átadja a megfelelő gyűjtési pontnak az elemek és az elektromos és elektronikus berendezések újrahasznosításához. Ez a különálló gyűjtemény és újrahasznosítás segíti a természeti erőforrások megőrzését, és megelőzheti az emberi egészségre és a környezetre vonatkozó lehetséges negatív következményeket a veszélyes anyagok lehetséges jelenléte miatt az akkumulátorok és az elektromos és elektronikus berendezések esetében, amelyeket a nem megfelelő ártalmatlanítás okozhat. Ha többet szeretne megtudni az akkumulátorok és az elektromos és elektronikus hulladékok eltávolításának helyéről, forduljon a helyi városi/helyhatósági irodához, a háztartási hulladék-ártalmatlanítási szolgáltatáshoz, illetve a terméket megvásárló áruházhoz. Az WEEE-ről további információért forduljon a *erecycle \@ Microsoft.com* .

Ez a termék érme cella akkumulátort (IES) tartalmaz.

Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL

Telefonszám: + 353 1 295 3826

Faxszám: + 353 1 706 4110 

**Japán**

![A képernyőképen egy, a japán számára szükséges értesítés jelenik meg.](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Miután áttekintette ezeket a biztonsági értesítéseket, beállíthatja és elvégezheti az eszköz csatlakoztatását.

## <a name="next-steps"></a>Következő lépések

* [Kábel és csatlakoztassa a Data Box](data-box-deploy-set-up.md)


