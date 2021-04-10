---
title: Azure Stack Edge mini R biztonsági útmutatója | Microsoft Docs
description: Ismerteti a biztonsági konvenciókat, az irányelveket, a szempontokat és ismerteti a Azure Stack Edge mini R-eszköz biztonságos telepítését és üzemeltetését.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382624"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge mini R biztonsági utasítások

![1. figyelmeztetési ikon – ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ a biztonsági értesítés ikonjának olvasása ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **– biztonsági és egészségvédelmi információk**

Olvassa el az ebben a cikkben található összes biztonsági információt, mielőtt a Azure Stack Edge mini R-eszközt használja, amely egy teleptöltöttség-csomag, egy AC/DC-beli áramforrás, egy modul-adapter és egy kiszolgálói modul. Az utasítások követésének elmulasztása tüzet, áramütést, sérüléseket vagy a tulajdonságok sérülését eredményezheti. Az Azure Stack Edge mini R használata előtt olvassa el az összes biztonsági információt alább.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciói

A következő jelzési szavak a veszélyforrások riasztási jelei:

| Ikon | Leírás |
|:--- |:--- |
| ![Kockázati szimbólum](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Veszély:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Figyelmeztetés:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – halált vagy súlyos sérülést eredményezhet. <br> **Vigyázat:** Olyan veszélyes helyzetet jelez, amely – ha nem kerül elkerülésre – kisebb vagy mérsékelt sérülést eredményezhet.|
|

Az Azure Stack Edge mini R-eszköz beállításakor és futtatásakor a következő veszélyforrási ikonokat kell figyelembe venni:

| Ikon | Leírás |
|:--- |:--- |
| ![Először olvassa el az összes utasítást](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Először olvassa el az összes utasítást |
| ![Értesítés ikonjának ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **megjegyzése:** | A fontosnak tartott információkat jelöli, de nem kapcsolódik a veszélyforráshoz. |
| ![Kockázati szimbólum](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Kockázati szimbólum |
| ![Áramütés ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Áramütés veszélye |
| ![Csak beltéri használatra](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Csak beltéri használatra |
| ![Nincs felhasználó által szervizelhető rész ikon](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nincsenek felhasználó által szervizelhető részek. A megfelelő képzés hiányában ne legyen hozzáférés. |
|

## <a name="handling-precautions-and-site-selection"></a>Az óvintézkedések és a hely kijelölésének feldolgozása

Az Azure Stack Edge mini R-eszköz a következő kezelési óvintézkedésekkel és hely-kiválasztási feltételekkel rendelkezik:

![Figyelmeztetés ikon 2 az áramütés ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ikonja nem rendelkezik a ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ felhasználó által szervizelhető részek ikonjának figyelmeztetésével ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* A *kapott* eszköz bevizsgálása károkért. Ha az eszköz bekerítése sérült, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-placeholder.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz üzemeltetését.
* Ha azt gyanítja, hogy az eszköz meghibásodik, [forduljon a Microsoft ügyfélszolgálatahoz](azure-stack-edge-placeholder.md) , és szerezzen be egy cserét. Ne kísérelje meg az eszköz kiszolgálását.
* Az eszköz nem tartalmaz felhasználó által szervizelhető részt. A veszélyes feszültség, a jelenlegi és az energia szintje belül van. Ne nyissa meg. Az eszköz visszaküldése a Microsoftnak a karbantartáshoz.

![Figyelmeztető ikon 3 figyelmeztetés ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

A rendszer üzemeltetése ajánlott:

* A hő forrása, beleértve a közvetlen napfényt és a fűtőtesteket.
* A nem nedvességnek vagy csapadéknak kitett helyen.
* Egy olyan térben található, amely a vibrációt és a fizikai sokkot is lekicsinyíti.  A rendszer a MIL-STD-810G szerint a sokk és a vibráció számára lett kialakítva.
* Elszigetelten a villamossági eszközök által előállított erős elektromágneses mezőkből.
* Ne engedélyezzen semmilyen folyadékot vagy idegen objektumot a rendszeren való belépéshez. Ne helyezzen italokat vagy más folyadék-tárolókat a rendszeren vagy annak közelében.

![Figyelmeztetés ikon 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ nincs felhasználó által szervizelhető rész ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Figyelmeztetés:**

* Ez a berendezés lítium-akkumulátort tartalmaz. Ne kísérelje meg az akkumulátor csomagjának kiszolgálását. A berendezésben lévő elemek nem a felhasználó által szervizelhető. A robbanás kockázata, ha az akkumulátort helytelen típus váltja fel.

![5 figyelmeztető ikon figyelmeztetése ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

Csak akkor számítson fel díjat az akkumulátornak, ha az Azure Stack Edge mini R-eszköz részét képezi, nem számít külön eszköznek.

![6. figyelmeztetési ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **– Figyelmeztetés:**

* Az akkumulátor be-és kikapcsolási kapcsolója az akkumulátor csak a kiszolgálói modulnak való kitöltésére szolgál. Ha a Power adapter csatlakoztatva van az akkumulátor-csomaghoz, a rendszer akkor is továbbítja a teljesítményt a kiszolgálói modulnak, ha a kapcsoló ki van kapcsolva.

![Figyelmeztető ikon 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **. figyelmeztetés:**

* Ne égesse el vagy rövidzárlat nélkül az akkumulátoros csomagot. Ennek megfelelően újra kell telepíteni vagy ártalmatlanítani.

![Figyelmeztetési ikon 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **– Vigyázat:**

* Ha a megadott AC/DC tápegységet használja, akkor a rendszer az 2590-as típusú mezőt is használhatja. Ebben az esetben a végfelhasználónak ellenőriznie kell, hogy az megfelel-e az összes vonatkozó biztonsági, szállítási, környezeti és egyéb nemzeti/helyi előírásnak.
* Ha a rendszer 2590-as típusú akkumulátorral működik, az akkumulátor gyártója által megadott használati feltételeken belül hajtsa végre az akkumulátort.

![Figyelmeztető ikon 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **. figyelmeztetés:**

Ez az eszköz két SFP + portot tartalmaz, amelyek optikai adó-vevővel is használhatók. A veszélyes lézeres sugárzás elkerülése érdekében csak az 1. osztályba tartozó adóvevőket használja.

## <a name="electrical-precautions"></a>Elektromos óvintézkedések

Az Azure Stack Edge mini R-eszköz a következő elektromos óvintézkedésekkel rendelkezik:

![Figyelmeztetés ikon 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ elektromos áramütés ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:**

Tápegység-adapterrel való használat esetén:

* Biztonságos elektromos földi csatlakozást biztosít a tápkábelhez. A váltakozó áramú (AC) kábel három vezetékes (egy földelt PIN-kódot tartalmazó) csatlakozóval rendelkezik. Ez a beépülő modul csak egy megalapozott hálózati konnektorhoz illeszkedik. Ne győzje le a földelő PIN-kód célját.
* Mivel a tápkábel csatlakoztatása a fő leválasztási eszköz, győződjön meg arról, hogy a szoftvercsatorna-kimenetek az eszköz közelében találhatók, és könnyen elérhetők.
* Bontsa ki a tápkábel (eke) t (a dugasz húzásával, ne a kábelt), és válassza le az összes kábelt, ha az alábbi feltételek bármelyike fennáll:

  * A tápkábel vagy a beépülő modul kikopott vagy más módon sérült lesz.
  * Az eszköz az eső, a felesleges nedvesség vagy más folyadékok számára van kitéve.
  * Az eszköz el lett dobva, és az eszköz burkolata sérült.
  * Azt gyanítja, hogy az eszköznek szolgáltatásra vagy javításra van szüksége.
* Véglegesen kihúzza az egységet, mielőtt áthelyezi, vagy ha úgy gondolja, hogy bármilyen módon megsérült.

* Adjon meg egy megfelelő áramforrást elektromos túlterhelés elleni védelemmel a következő energiaellátási követelmények teljesítéséhez:

* Feszültség: 100 – 240 v AC
* Jelenlegi: 1,7 amper
* Gyakoriság: 50 – 60 Hz

![Figyelmeztetés ikon 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektromos áramütés ikon ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Figyelmeztetés:**

* Ne kísérelje meg a nem a berendezéshez biztosított hálózati adapterek (ok) módosítását vagy használatát.

![Figyelmeztető ikon 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ – az elektromos áramütés ikonja ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ csak beltéri használatra ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **figyelmeztető:**

* A szimbólummal jelölt tápegység csak beltéri használatra van minősítve.

## <a name="regulatory-information"></a>Szabályozási információk

Az alábbi szabályok a Azure Stack Edge mini R-eszköz, a szabályozási modell száma: TMA01 című témakörben találhatók.

Az Azure Stack Edge mini R-eszköz úgy van kialakítva, hogy a felsorolt NRTL (UL, CSA, ETL stb.), valamint az IEC/EN 60950-1 vagy az IEC/EN 62368-1 kompatibilis (CE jelölésű) információtechnológiai berendezésekkel használható legyen.

Az Egyesült Államokban és Kanadában kívüli országokban a hálózati kábelek (a berendezéshez nem biztosított) nem telepíthetők ezzel a berendezéssel, ha a hossza nagyobb, mint 3 méter.

A berendezés a következő környezetekben való működésre lett tervezve:

| Környezet | Specifikációk |
|:---  |:--- |
| Rendszerhőmérséklet-specifikációk | <ul><li>Tárolási hőmérséklet: – 20 &deg; c – 50 &deg; c (– 4 &deg; F-122 &deg; f)</li><li>Folyamatos művelet: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; f)</li></ul> |
| Relatív páratartalom (RH) előírásai | <ul><li>Tárolás: 5% – 95% relatív páratartalom</li><li>Üzemeltetés: 10% – 90% relatív páratartalom</li></ul>|
| Maximális magassági specifikációk | <ul><li>Üzemeltetés: 15 000 méter (4 572 méter)</li><li>Nem működő: 40 000 méter (12 192 méter)</li></ul>|

> ![Értesítés ikonja – 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **. Megjegyzés:** &nbsp; a Microsoft által kifejezetten nem jóváhagyott berendezésen végrehajtott módosítások vagy módosítások érvénytelenítik a felhasználó hatóságát a berendezés működtetéséhez.

#### <a name="canada-and-usa"></a>Kanada és USA:

> ![Értesítés ikonja – 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **. Megjegyzés:** &nbsp; a rendszer tesztelte a készüléket, és úgy találta, hogy megfelel a digitális eszközre vonatkozó KORLÁTOKnak, az FCC-szabályok 15. része alapján. Ezek a korlátok úgy vannak kialakítva, hogy ésszerű védelmet biztosítanak a káros interferenciák ellen, ha a berendezés kereskedelmi környezetben üzemel. Ez a berendezés a rádiófrekvenciás energia előállítására, felhasználására és kisugárzására szolgál, és ha nincs telepítve és használatban a használati utasítással összhangban, a rádiófrekvenciás kommunikáció káros hatással lehet. A berendezésnek a lakossági környezetben való működése valószínűleg káros interferencia okoz, amely esetben a felhasználónak a saját költségén kell kijavítania a beavatkozást.

Az ezzel a berendezéssel biztosított Netgear A6150 Wi-Fi-adaptert az emberi törzsnek megfelelően kell működtetni, a törzs által kezelt, meghatározott abszorpciós ráta (SAR) megfelelőségének tesztelésére. Az FCC által meghatározott SAR-határérték 1,6 W/kg, ha átlagosan 1 g szövetet mutat. A terméknek a törzsön való viselése során történő elvégzése során, a törzstől függően 10 mm távolságot kell fenntartania, hogy biztosítsa a RÁDIÓFREKVENCIÁs expozícióra vonatkozó követelményeket.

A Netgear A6150 Wi USB-adapter megfelel az ANSI/IEEE C 95.1-1999-nek, és a OET Bulletin 65 C. kiegészítésében megadott mérési módszereknek és eljárásoknak megfelelően tesztelték.

Netgear A6150-specifikus abszorpciós arány (SAR): 1,18 W/kg átlagosan 1 g szövet

A Netgear A6150 Wi USB-adapter csak jóváhagyott antennákkal használható. Ez az eszköz és az antenna (ek) nem lehet közös helyen vagy más antennával vagy jeladóval együtt működni, kivéve az FCC több-adó termék eljárásainak megfelelően. Az Egyesült államokbeli piacon elérhető termékek esetében csak az 1 ~ 11. csatornát lehet üzemeltetni. Más csatornák kiválasztása nem lehetséges.

A Band 5150 – 5250 MHz-ben végzett művelet csak beltéri használatra alkalmas, hogy csökkentse a közös csatornás mobil műhold-rendszerek káros zavarásának lehetőségét.

![Szabályozási információk figyelmeztetés – beltéri használat](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

A felhasználók azt tanácsolják, hogy a nagy teljesítményű radarok a 5250-5350 MHz-es és a 5650-5850 MHz-es sávok elsődleges felhasználóinak (Kiemelt felhasználóinak) legyenek lefoglalva, és ezek a radarok interferenciát és/vagy sérülést okozhatnak a LE-LAN eszközökön.

Ez a berendezés a rádiófrekvenciás energia előállítására, felhasználására és kisugárzására szolgál, és ha nincs telepítve és használatban az utasításoknak megfelelően, akkor káros interferencia okozhat a rádiós kommunikációban. Azonban nincs garancia arra, hogy az interferencia nem fog megtörténni egy adott telepítésben.

Ha ez a berendezés káros interferenciát okoz a rádió-vagy televíziós fogadásban, amely a berendezés kikapcsolásával és bekapcsolásával határozható meg, a felhasználónak javasoljuk, hogy próbálja meg kijavítani a beavatkozást az alábbi mértékek közül egy vagy több alapján:

- Vigye át vagy helyezze át a fogadó antennát.
- Növelje a berendezés és a fogadó közötti elkülönítést.
- Csatlakoztassa a készüléket egy olyan áramkörhöz, amely nem azonos a fogadóval.
- Segítségért forduljon a kereskedőhöz vagy egy tapasztalt rádió/TV-technikushoz.

Az interferenciával kapcsolatos problémákról további információt az FCC webhelyén talál a következő címen: [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Az FCC-t a 1-888 – CALL FCC néven is meghívhatja interferencia és telefonos beavatkozási tájékoztatók kéréséhez.

A rádiófrekvenciás biztonsággal kapcsolatos további információkért tekintse meg a (z) [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) és az Industry Canada webhelyén található FCC webhelyét [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Ez a termék az EMC-megfelelőséget mutatja be olyan feltételek mellett, amelyek a megfelelő perifériák és a rendszerösszetevők közötti védett kábelek használatát foglalják magukban. Fontos, hogy a megfelelő perifériák és a védett kábelek a rendszerösszetevők között legyenek, így csökkenthető a rádiók, a televízió-készletek és az egyéb elektronikus eszközök zavarásának lehetősége.

Ez az eszköz megfelel az FCC-szabályok és az iparági kanadai licencek alól mentesített RSS-szabványoknak. A művelet a következő két feltétel hatálya alá esik: (1) Ez az eszköz nem okoz káros interferenciát, és (2) az eszköznek el kell fogadnia a kapott interferenciát, beleértve az interferenciát is, ami az eszköz nemkívánatos működéséhez vezethet.

![Szabályozási információ – 1. figyelmeztetés](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

A KÖVETKEZŐ LEHET: ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

Egyesült Államok: (800) 426-9400

Kanada: (800) 933-4750

Netgear A6150 Wi-Fi USB-adapter FCC-azonosító: PY318300429
 
Netgear A6150 WiFi USB-adapter IC-azonosítója: 4054A-18300429

Az ezzel a berendezéssel biztosított Netgear A6150 Wi-Fi-adapter megfelel az általános populáció/nem ellenőrzött expozíciós határértékek esetében az IC RSS-102-ben, és az IEEE 1528-ben megadott mérési módszereknek és eljárásoknak megfelelően lett tesztelve. Legalább 10 mm távolságot kell fenntartani a törzs által hordott feltételhez.

A Netgear A6150 Wi USB-adapter megfelel a nem ellenőrzött környezethez tartozó, a kanadai hordozható RF-re vonatkozó korlátozásoknak, és biztonságos a kívánt művelethez a manuális módon leírtak szerint. A további RF-expozíció csökkentése úgy valósítható meg, hogy a lehető legnagyobb mértékben tartja a terméket a törzstől, vagy ha az eszköz alacsonyabb kimeneti teljesítményre van állítva, ha egy ilyen funkció elérhető.

Minden termék esetében az 1 g-nál átlagosan meghatározott abszorpciós aránnyal (SAR) rendelkező tábla látható az USA fenti részében.

![A szabályozási információkra vonatkozó figyelmeztetés 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>EURÓPAI UNIÓ:

Kérje le az EU megfelelőségi nyilatkozatának másolatát ehhez a berendezéshez. Küldjön egy e-mailt a következő címre: [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

Az ezzel a berendezéssel biztosított Netgear A6150 Wi-Fi-adapter a 2014/53/EU direktíva megfelel, és kérésre is megadható.

![Figyelmeztetés ikon 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **– Figyelmeztetés:**  

Ez egy termék osztálya. Belföldi környezetben Ez a termék a rádiófrekvenciás interferencia okozhatja, amely esetben a felhasználónak szükség lehet a megfelelő intézkedések meghozatalára.

A hulladék akkuinak és elektromos és elektronikus berendezéseinek ártalmatlanítása:

![Figyelmeztetési ikon 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

A termékre vagy annak elemeire vagy csomagolására vonatkozó szimbólum azt jelenti, hogy a terméket és a benne található elemeket nem szabad a háztartási hulladékból ártalmatlanítani. Ehelyett az Ön felelőssége, hogy ezt átadja a megfelelő gyűjtési pontnak az elemek és az elektromos és elektronikus berendezések újrahasznosításához. Ez a különálló gyűjtemény és újrahasznosítás segíti a természeti erőforrások megőrzését, és megelőzheti az emberi egészségre és a környezetre vonatkozó lehetséges negatív következményeket a veszélyes anyagok lehetséges jelenléte miatt az akkumulátorok és az elektromos és elektronikus berendezések esetében, amelyeket a nem megfelelő ártalmatlanítás okozhat. Ha többet szeretne megtudni az akkumulátorok és az elektromos és elektronikus hulladékok eltávolításának helyéről, forduljon a helyi városi/helyhatósági irodához, a háztartási hulladék-ártalmatlanítási szolgáltatáshoz, illetve a terméket megvásárló áruházhoz. erecycle@microsoft.comTovábbi információért forduljon az WEEE-ről.

Ez a termék érme cella akkumulátort (IES) tartalmaz.

Az ezzel a berendezéssel biztosított Netgear A6150 Wi-Fi-adaptert az emberi törzs közelébe kell működtetni, és a test által kezelt meghatározott abszorpciós ráta (SAR) megfelelőségének tesztelésére kerül sor (lásd az alábbi értékeket). A terméknek a törzsön való viselése során történő elvégzése közben, a törzstől függően a testtől mért távolságot kell fenntartani, hogy biztosítsa a RÁDIÓFREKVENCIÁk expozíciós követelményeinek való megfelelést.

**Netgear A6150-specifikus abszorpciós arány (SAR):** 0,54 W/kg átlagosan több mint 10g szövet

 
Ez az eszköz az EU összes tagállamában működhet. Figyelje meg a nemzeti és a helyi szabályozást, ahol az eszközt használják. Ez az eszköz csak akkor használható beltéri használatra, ha a következő országokban 5150-5350 MHz-es frekvenciatartományban üzemel:  

![Csak beltéri használatra szoruló EU-országok](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Az alábbi táblázat a RED 10.8 (a) és 10.8 (b) cikkével összhangban ismerteti a használt frekvenciasávok és a Netgear vezeték nélküli termékek maximális RF-továbbítási teljesítményét az EU-ban:

**Fi**

| Frekvenciatartomány (MHz) | Használt csatornák | Maximális átviteli teljesítmény (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400 – 2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) TPC-vel <br> 19,9 dBm (97,7 mW) nem TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) TPC-vel <br> 29,6 dBm (490 mW) nem TPC |

Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL

Telefonszám: + 353 1 295 3826

Faxszám: + 353 1 706 4110

#### <a name="singapore"></a>Szingapúr:

Az ezzel a berendezéssel biztosított Netgear A6150 Wi USB-adapter megfelel a IMDA-szabványoknak.


## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Stack Edge mini R üzembe helyezésére](azure-stack-edge-mini-r-deploy-prep.md)
