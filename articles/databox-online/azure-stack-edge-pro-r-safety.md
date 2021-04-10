---
title: Azure Stack Edge Pro R biztonsági útmutató | Microsoft Docs
description: Ismerteti a biztonsági konvenciókat, az irányelveket, a szempontokat és ismerteti a Azure Stack Edge Pro R-eszköz biztonságos telepítését és üzemeltetését.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363066"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R biztonsági utasítások

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ olvassa el a biztonsági értesítés ikonját ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **, olvassa el a biztonsági és egészségvédelmi információkat**

Az Azure Stack Edge Pro R-eszköz használata előtt olvassa el az ebben a cikkben található összes biztonsági információt. Az utasítások követésének elmulasztása tüzet, áramütést, sérüléseket vagy a tulajdonságok sérülését eredményezheti. Az Azure Stack Edge Pro R használata előtt olvassa el az összes biztonsági információt.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói

A következő jelzési szavak a veszélyforrások riasztási jelei:

| Ikon | Leírás |
|:--- |:--- |
| ![Kockázati szimbólum](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Veszély:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Figyelmeztetés:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Vigyázat:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet.|
|

Az Azure Stack Edge Pro R Edge-eszköz beállításakor és futtatásakor a következő kockázati ikonokat kell figyelembe venni:

| Ikon | Leírás |
|:--- |:--- |
| ![Először olvassa el az összes utasítást](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Először olvassa el az összes utasítást |
| ![Értesítés ikonjának ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **megjegyzése:** | A fontosnak tartott információkat jelöli, de nem kapcsolódik a veszélyforráshoz. || ![Kockázati szimbólum](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Kockázati szimbólum |
| ![Tipp veszélyességének ikonja](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Tipp kockázati tényező|
| ![Nagy súly ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Nagy súlyú kockázati tényező|
| ![Áramütés ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Áramütés veszélye |
| ![Nincs felhasználó által szervizelhető rész ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Nincsenek felhasználó által szervizelhető részek. A megfelelő képzés hiányában ne legyen hozzáférés. |
| ![Több áramforrás ikon](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Több áramforrás. Válassza le az összes tápkábelt a berendezés összes áramellátásának eltávolításához. |
| ![Csípés pontok ikon](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | A csípés pontok jelennek meg. |
| ![Forró összetevők vagy felületek ikon](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Forró összetevőket vagy felületeket jelez. |
|

## <a name="handling-precautions-and-site-selection"></a>Az óvintézkedések és a hely kijelölésének feldolgozása

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Figyelmeztetés:**

* A megfelelő berendezést (például a raklap-dugaszolóaljzatot) és a személyes védőeszközöket (PPE), például kesztyűket kell használni a szállított eszköz áthelyezésekor és kezelésekor.

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ Tipp kockázati ikonjának ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **figyelmeztetése:**

* Helyezze a készüléket egy sima, kemény és stabil felületre, hogy elkerülje a lehetséges tip vagy zúzás veszélyét.
* Ne verem kettőnél több eszközt egymás után.
* Győződjön meg arról, hogy a rendszer a halmozás előtt biztonságos.
* Ne helyezze át és ne helyezze át a halmozott eszközöket.
* Ne verem a külső kábelekkel energikus eszközöket.
* Gondoskodjon arról, hogy az energiaellátási kábelek ne legyenek zúzva vagy sérültek a halmozási műveletek során.
* Az eszközök nem használhatók táblázatként vagy munkaterületként.

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ áramütés ikonja nem rendelkezik a ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ felhasználó által szervizelhető részek ikonjának figyelmeztetésével ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **:**

* A *kapott* eszköz bevizsgálása károkért. Ha az eszköz bekerítése sérült, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-contact-microsoft-support.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz üzemeltetését.
* Ha azt gyanítja, hogy az eszköz meghibásodik, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-contact-microsoft-support.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz kiszolgálását.
* Az eszköz nem tartalmaz felhasználó által szervizelhető részt. A veszélyes feszültség, a jelenlegi és az energia szintje belül van. Ne nyissa meg. Az eszköz visszaküldése a Microsoftnak a karbantartáshoz.

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ nagy súly ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **Figyelmeztetés:**

* Az UPS tápegység-moduljának eltávolítása az UPS-n belüli feszültséggel rendelkező részeket teszi elérhetővé. Ne helyezzen be idegen objektumokat az energiaellátási modul rekeszében.
* Ne próbálja meg önállóan feloldani egy Azure Stack Edge Pro R Edge-eszközt. Egy ház súlya 52 kg és 93 kg között lehet (115 lbs és 205 lbs); a berendezések áthelyezéséhez és emeléséhez használjon mechanikus segítséget vagy más megfelelő segítséget. Megfelel a helyi munkahelyi egészségügyi és biztonsági követelményeknek a berendezések áthelyezése és emelése során.
* Ne próbálja meg a megfelelő mechanikus támogatás nélkül kiemelni a készüléket. Vegye figyelembe, hogy a súlyozás feloldására irányuló kísérlet súlyos sérüléseket eredményezhet.

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Figyelmeztetés:**  

* A rendszer felügyelt környezetben való működésre lett tervezve. Válasszon egy helyet:
  * Jól szellőző és távolsági forrásból, beleértve a közvetlen napfényt és a radiátorokat is.
  * Nincs kitéve nedvességnek vagy csapadéknak.
  * Egy olyan térben található, amely a vibrációt és a fizikai sokkot is lekicsinyíti.  A rendszer a MIL-STD-810G szerint a sokk és a vibráció számára lett kialakítva.
  * Elszigetelten a villamossági eszközök által előállított erős elektromágneses mezőkből.
  * Megfelelően megalapozott kivezetéssel biztosítva.
  * Elegendő lemezterülettel rendelkezik a tápkábel (ek) eléréséhez, mert a termék fő energiaellátási leválasztását szolgálják.
* Az Ethernet-kábelek nincsenek megadva a termékhez. Az elektromágneses interferencia csökkentése érdekében ajánlott a Cat 6 védett sodrott (STP) kábelezés használata.
* Állítsa be a készüléket olyan munkaterületre, amely lehetővé teszi a megfelelő légáramlást a berendezések körül; Győződjön meg arról, hogy az első és a hátsó borító teljesen el van távolítva az eszköz futása közben.
* Az Ethernet-kábelek nincsenek megadva a termékhez. Az elektromágneses interferencia csökkentése érdekében ajánlott a Cat 6 védett (STP) kábelezés használata.
* A berendezést hőmérséklet által vezérelt területre kell telepíteni, amely nem szennyező anyagokból áll, és lehetővé teszi a megfelelő légáramlást a berendezések körül.
* A berendezéseket a folyadék és a túlzottan párás környezetek forrásaiból is megtarthatja.
* Ne engedélyezzen semmilyen folyadékot vagy idegen objektumot a rendszeren való belépéshez. Ne helyezzen italokat vagy más folyadék-tárolókat a rendszeren vagy annak közelében.

## <a name="heater-precautions"></a>Melegítő óvintézkedések

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ – gyors összetevők vagy felületek ikon 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:** 

* Ha a rendszer bekapcsolja a rendszer bekapcsolását, az automatikus fűtési művelet nagy felszíni hőmérsékletek okozta érintéses veszélyforrást eredményezhet. Ne érintse meg ezt a felületet a rendszer bekapcsolása közben. A rendszer kikapcsolását követően 10 perces lehűtési időszak engedélyezése.

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ csípés pontok ikon 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:** 

* Ha a rendszer bekapcsolja a szolgáltatást, a hátsó csatlakozó ajtajának automatikus működtetése egy csipetnyi veszélyt eredményezhet. Ha a rendszer bekapcsolja a funkciót, tartsa tisztán a területét.

## <a name="electrical-precautions"></a>Elektromos óvintézkedések

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ áramütés ikonjának ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **figyelmeztetése:**

* Biztonságos elektromos földi csatlakozást biztosít a tápkábelhez. A váltakozó áramú (AC) kábel három vezetékes (egy földelt PIN-kódot tartalmazó) csatlakozóval rendelkezik. Ez a beépülő modul csak egy megalapozott hálózati konnektorhoz illeszkedik. Ne győzje le a földelő PIN-kód célját.
* Mivel a tápkábel csatlakoztatása a fő leválasztási eszköz, győződjön meg arról, hogy a szoftvercsatorna-kimenetek az eszköz közelében találhatók, és könnyen elérhetők.
* Bontsa ki a tápkábel (eke) t (a dugasz húzásával, ne a kábelt), és válassza le az összes kábelt, ha az alábbi feltételek bármelyike fennáll:

  * A tápkábel vagy a beépülő modul kikopott vagy más módon sérült lesz.
  * Az eszköz házába való kifolyása.
  * Az eszköz az eső vagy a felesleges nedvesség számára van kitéve.
  * Az eszköz el lett dobva, és az eszköz burkolata sérült.
  * Azt gyanítja, hogy az eszköznek szolgáltatásra vagy javításra van szüksége.
* Véglegesen kihúzza az egységet, mielőtt áthelyezi, vagy ha úgy gondolja, hogy bármilyen módon megsérült.
* A nagy szivárgások elkerülése érdekében, ha egyetlen továbbítási eset egynél több megszakítás nélküli tápegységgel rendelkezik (UPS), azt javasoljuk, hogy mindegyik UPS független fiókirodához kapcsolódjon. Azonban abban az esetben, ha az egyik elosztó egység (PDU) vagy más eszköz is használatban van, ahol az egyes UPS-ek biztonsági alapjai a PDU egyetlen feeder-kivezetési karmesterén alapulnak, az egyes UPS-ket tartalmazó terminált is fel kell használni egy kiegészítő épületbe.

  > [!NOTE]
  > Ha olyan PDU-t használ, amely már rendelkezik egy kiegészítő bevezetési vezetővel, nem szükséges a további, az UPS-hez tartozó terminált használni.

* Adjon meg egy megfelelő áramforrást elektromos túlterhelés elleni védelemmel a következő energiaellátási követelmények teljesítéséhez:

  * Feszültség: 100 – 240 v AC
  * Jelenlegi: 20 A, maximum/tápkábel. A tápkábel (ek) be vannak biztosítva.
  * Gyakoriság: 50 – 60 Hz

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ – az elektromos áramütés ikonja ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ több áramforrás ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **Figyelmeztetés:**

* A szünetmentes tápegység (UPS) nélküli rendszerek esetében az összes hálózati adapter kihúzásával távolítsa el a hálózati adaptert a készülékről.
* A UPS-rendszerek esetében az összes hálózati adaptert ki kell kapcsolni, és az UPS-kapcsoló használatával a rendszer megszüntetni. A UPS veszélyes AC-és DC-feszültségeket tartalmaz.
* Ha a rendszer UPS-t tartalmaz, a UPS védett bemeneti tápkábelt kapott. A védett bemeneti tápkábelt kell használnia, ne cserélje vagy módosítsa a kábelt.

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ áramütés ikonjának ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **figyelmeztetése:**

* A UPS, AC és/vagy DC feszültséggel ellátott rendszerek esetében mindig lehetséges az AC feszültség kockázata az akkumulátorokból vagy segédprogramokból generált UPS-kimenetben. A berendezések sérülésének vagy személyes sérülésének elkerülése érdekében mindig vegye figyelembe, hogy az UPS-kimenetnél feszültség is lehet, még akkor is, ha az elsődleges áramforrásról le van választva.
* A UPS-vel felszerelt rendszerek esetében az összes UPS-energia külső kapcsolat nő. Ne távolítsa el a kis-és nagybetűket, vagy szúrjon be semmit a UPS-be a kívánt összekötőbe.

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ áramütés ikonjának ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **figyelmeztetése:**

* Ne kísérelje meg a nem a berendezéshez biztosított hálózati adapterek (ok) módosítását vagy használatát.

![Figyelmeztető ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ nincs felhasználó által kiszolgálható rész ikonja figyelmeztetés ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **:**

* Ez a berendezés lítium-érme cellát és/vagy lítium-vas-foszfát elemeket tartalmaz. Ne kísérelje meg a berendezések kiszolgálását. A berendezésben lévő elemek nem a felhasználó által szervizelhető. A robbanás kockázata, ha az akkumulátort helytelen típus váltja fel.
* Az UPS akkumulátor-moduljának eltávolítása az UPS-n belüli feszültséggel rendelkező részeket teszi elérhetővé. Ne helyezzen be idegen objektumokat az akkumulátor-modul rekeszbe.

## <a name="regulatory-information"></a>Szabályozási információk

Ez a szakasz a Azure Stack Edge Pro R-eszköz, a szabályozási modell száma: Azure Stack Edge Pro R rendszerhez tartozó szabályozási információkat tartalmazza.

Az Azure Stack Edge Pro R Edge-eszköz a felsorolt NRTL (UL, CSA, ETL stb.), valamint az IEC/EN 60950-1 vagy az IEC/EN 62368-1 kompatibilis (CE jelölésű) információtechnológiai berendezésekkel való használatra készült.

Az eszköz az alábbi környezetekben való működésre lett tervezve:

| Környezet | Specifikációk |
|:--- |:--- |
|Hőmérséklet-specifikációk | <ul><li>Tárolási hőmérséklet: – 33 &deg; c – 63 &deg; C (– 28 &deg; f – 145 &deg; f) </li><li>Folyamatos művelet: 5 &deg; c – 43 &deg; c (41 &deg; f – 110 &deg; f)</li><li>Maximális hőmérséklet-átmenet (működési és tárolási): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Relatív páratartalom-specifikációk | <ul><li>Tárolás: 5% – 95% RH és 33 &deg; C (91 &deg; F) maximális harmatpont. A légkörnek mindig nem lecsapódó kell lennie.</li><li>Üzemeltetés: 5% – 85% relatív páratartalom 29 &deg; C-vel (84,2 &deg; F) maximális harmatpont</li></ul> |
| Maximális magassági specifikációk | <ul><li>Operációs (UPS nélkül): 15 000 Ft (4 572 méter)</li><li>Üzemeltetés (UPS-vel): 10 000 Ft (3 048 méter)</li><li>Tárolás: 40 000 Ft (12 192 méter)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Értesítés ikonja – 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **. Megjegyzés:** &nbsp; a Microsoft által kifejezetten nem jóváhagyott berendezésen végrehajtott módosítások vagy módosítások érvénytelenítik a felhasználó hatóságát a berendezés működtetéséhez.

#### <a name="canada-and-usa"></a>Kanada és USA:

> ![Értesítés ikonja – 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **. Megjegyzés:** &nbsp; a rendszer tesztelte a készüléket, és úgy találta, hogy megfelel a digitális eszközre vonatkozó KORLÁTOKnak, az FCC-szabályok 15. részének megfelelően. Ezek a korlátok úgy vannak kialakítva, hogy ésszerű védelmet biztosítanak a káros interferenciák ellen, ha a berendezés kereskedelmi környezetben üzemel. Ez a berendezés a rádiófrekvenciás energia előállítására, felhasználására és kisugárzására szolgál, és ha nincs telepítve és használatban a használati utasítással összhangban, a rádiófrekvenciás kommunikáció káros hatással lehet. A berendezésnek a lakossági környezetben való működése valószínűleg káros interferencia okoz, amely esetben a felhasználónak a saját költségén kell kijavítania a beavatkozást.

Ez az eszköz megfelel az FCC-szabályok és az iparági kanadai licencek alól mentesített RSS-szabványoknak. A művelet a következő két feltétel hatálya alá esik: (1) Ez az eszköz nem okoz káros interferenciát, és (2) az eszköznek el kell fogadnia a kapott interferenciát, beleértve az interferenciát is, ami az eszköz nemkívánatos működéséhez vezethet.

![Szabályozási információ – 1. figyelmeztetés](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


LEHET ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA Egyesült Államok: (800) 426-9400 Kanada: (800) 933-4750

#### <a name="european-union"></a>EURÓPAI UNIÓ:

Az EU megfelelőségi nyilatkozatának másolatának kérése. Küldjön egy e-mailt a következő címre: [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

![Figyelmeztetés ikon ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **Figyelmeztetés!**

Ez egy termék osztálya. Belföldi környezetben Ez a termék a rádiófrekvenciás interferencia okozhatja, amely esetben a felhasználónak szükség lehet a megfelelő intézkedések meghozatalára.

A hulladék akkuinak és elektromos és elektronikus berendezéseinek ártalmatlanítása:

![Figyelmeztetési ikon 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

A termékre vagy annak elemeire vagy csomagolására vonatkozó szimbólum azt jelenti, hogy a terméket és a benne található elemeket nem szabad a háztartási hulladékból ártalmatlanítani. Ehelyett az Ön felelőssége, hogy ezt átadja a megfelelő gyűjtési pontnak az elemek és az elektromos és elektronikus berendezések újrahasznosításához. Ez a különálló gyűjtemény és újrahasznosítás segíti a természeti erőforrások megőrzését, és megelőzheti az emberi egészségre és a környezetre vonatkozó lehetséges negatív következményeket a veszélyes anyagok lehetséges jelenléte miatt az akkumulátorok és az elektromos és elektronikus berendezések esetében, amelyeket a nem megfelelő ártalmatlanítás okozhat. Ha többet szeretne megtudni az akkumulátorok és az elektromos és elektronikus hulladékok eltávolításának helyéről, forduljon a helyi városi/helyhatósági irodához, a háztartási hulladék-ártalmatlanítási szolgáltatáshoz, illetve a terméket megvásárló áruházhoz. erecycle@microsoft.comTovábbi információért forduljon az WEEE-ről.

Ez a termék érme cella akkumulátort (IES) tartalmaz.

Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL telefon száma: + 353 1 295 3826 fax száma: + 353 1 706 4110


## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Stack Edge Pro R Edge üzembe helyezésére](azure-stack-edge-pro-r-deploy-prep.md)
