---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "72793131"
---
A eszközök internetes hálózata-(IoT-) infrastruktúra biztonságossá tételéhez szigorú, részletes biztonsági stratégiát kell megadni. Ennek a stratégiának szüksége van a felhőben tárolt adatok védelmére, az adatok integritásának biztosítására a nyilvános interneten keresztül, és biztonságosan kiépíteni az eszközöket. Minden réteg nagyobb biztonsági garanciát hoz létre a teljes infrastruktúrában.

## <a name="secure-an-iot-infrastructure"></a>IoT-infrastruktúra biztonságossá tétele

Ez a biztonsági részletes stratégia a IoT-eszközök és-infrastruktúra előállításával, fejlesztésével és üzembe helyezésével foglalkozó különböző játékosok aktív részvételével fejleszthető és hajtható végre. A következő a játékosok magas szintű leírása.

* **IoT hardver gyártója/integrátora**: általában ezek a játékosok a IoT-hardverek gyártói, a különböző gyártóktól származó hardverek összevonása, illetve a más szállítók által gyártott vagy integrált IoT-üzembe helyezést biztosító szolgáltatók hardvert biztosítanak.

* **IoT-megoldás fejlesztője**: az IoT-megoldások fejlesztését általában egy megoldás fejlesztője végzi. Ez a fejlesztő egy házon belüli csapat vagy rendszerintegrátor (SI) része lehet, amely a tevékenységre specializálódott. A IoT-megoldás fejlesztője a IoT-megoldás különböző összetevőit fejlesztheti a semmiből, integrálhatja a különböző, a polcon kívüli vagy nyílt forráskódú összetevőket, vagy olyan megoldás-gyorssegédeket fogadhat, amelyek kisebb módosításokkal rendelkeznek.

* **IoT-megoldás üzembe helyezése**: a IoT-megoldás fejlesztése után telepíteni kell a mezőt a mezőbe. Ez a folyamat magában foglalja a hardverek telepítését, az eszközök összekapcsolását, valamint a megoldások hardveres vagy Felhőbeli üzembe helyezését.

* **IoT-megoldás kezelője**: a IoT-megoldás üzembe helyezése után hosszú távú műveleteket, figyelést, frissítést és karbantartást igényel. Ezeket a feladatokat egy házon belüli csapat hajthatja végre, amely információs technológiai szakemberek, hardveres műveletek és karbantartási csapatok, valamint olyan tartományi szakemberek számára készült, akik a teljes IoT-infrastruktúra megfelelő viselkedését figyelik.

A következő szakaszokban az ajánlott eljárások nyújtanak segítséget az egyes játékosok számára a biztonságos IoT-infrastruktúra fejlesztéséhez, üzembe helyezéséhez és üzemeltetéséhez.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártója/integrátora

A következő gyakorlati tanácsok a IoT és a hardveres integrátorok számára ajánlott eljárások.

* A **minimális követelmények hatókörének hardvere**: a hardver kialakításának tartalmaznia kell a hardver működéséhez szükséges minimális funkciókat, és semmi más nem. Ilyen például, ha az eszköz működéséhez szükség van az USB-portokra. Ezek a további funkciók megnyitják az eszközt a nemkívánatos támadási vektorok számára, amelyeket el kell kerülni.

* **Hardveres illetéktelenség igazolása**: a fizikai illetéktelen módosítások észleléséhez, például az eszköz egy részének megnyitásához vagy az eszköz egy részének eltávolításához hozzon létre mechanizmusokat. Ezek a jogosulatlan jelek a felhőbe feltöltött adatfolyamba kerülhetnek, ami az események operátorait is felhasználhatja.

* A **biztonságos hardverek kiépítése**: Ha az ELÁBÉ lehetővé teszi, olyan biztonsági funkciókat építhet ki, mint például a biztonságos és titkosított tárolás vagy a rendszerindítási funkció PLATFORMMEGBÍZHATÓSÁGI modul (TPM) alapján. Ezek a funkciók biztonságosabbá teszik az eszközöket, és segítenek megvédeni a teljes IoT-infrastruktúrát.

* **Frissítések biztonságossá tétele**: a belső vezérlőprogram frissítése az eszköz élettartama során elkerülhetetlen. A biztonságos elérési úttal rendelkező eszközök és a belső vezérlőprogram-verziók titkosítási garanciájának kiépítése lehetővé teszi, hogy az eszköz biztonságban legyen a frissítés során és után is.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztője

A IoT-megoldás fejlesztőknek szóló ajánlott eljárások a következők:

* A **biztonságos szoftverfejlesztés módszerének követése**: a biztonságos szoftverek fejlesztéséhez a projekt kezdetétől a teljes körű működést, tesztelést és üzembe helyezést lehetővé kell tenni. A platformokra, nyelvekre és eszközökre vonatkozó döntéseket mind a módszertan befolyásolja. A Microsoft biztonsági fejlesztési életciklusa részletes megközelítést biztosít a biztonságos szoftverek létrehozásához.

* **Válassza ki a nyílt forráskódú szoftvereket: a** nyílt forráskódú szoftverek lehetőséget biztosítanak a megoldások gyors fejlesztésére. Ha nyílt forráskódú szoftvert választ, vegye figyelembe a Közösség tevékenységi szintjét az egyes nyílt forráskódú összetevőknél. Az aktív Közösség gondoskodik a szoftver támogatásáról, valamint a problémák felderítéséről és megoldásáról. Másik lehetőségként előfordulhat, hogy a rendszer nem támogatja az ismeretlen és inaktív nyílt forráskódú szoftverek használatát, és a problémák valószínűleg nem észlelhetők.

* **Integráció a Care szolgáltatással**: a tárak és API-k határán számos szoftveres biztonsági hiba létezik. Előfordulhat, hogy az aktuális telepítéshez esetlegesen nem szükséges funkciók egy API-rétegen keresztül is elérhetők. Az általános biztonság érdekében győződjön meg arról, hogy a biztonsági hibákhoz integrált összetevők összes felületét ellenőrizni kell.

## <a name="iot-solution-deployer"></a>IoT-megoldás telepítője

A következő gyakorlati tanácsok a IoT-megoldások üzembe helyezéséhez:

* **Hardver biztonságos üzembe helyezése**: a IoT-telepítésekhez hardvert kell telepíteni a nem biztonságos helyeken, például a nyilvános helyeken vagy a nem felügyelt területi beállításokban. Ilyen helyzetekben győződjön meg arról, hogy a hardver központi telepítése a maximális mértékben illetéktelenül módosítható. Ha az USB-vagy más portok elérhetők a hardveren, győződjön meg róla, hogy azok biztonságosan vannak leképezve. Számos támadási vektor használhatja ezeket belépési pontként.

* **Hitelesítő kulcsok biztonságos megőrzése**: az üzembe helyezés során minden eszközön a Cloud Service által generált eszköz-azonosítók és társított hitelesítési kulcsok szükségesek. Ezeket a kulcsokat a telepítés után is fizikailag biztonságban kell tartani. A kártékony eszköz feltört kulcsát meglévő eszközként való maszkolásra is használhatja.

## <a name="iot-solution-operator"></a>IoT-megoldás operátora

Az IoT-megoldás kezelői számára ajánlott eljárások a következők:

* **Tartsa naprakészen a rendszert**: gondoskodjon arról, hogy az eszköz operációs rendszere és az összes eszközillesztő a legújabb verzióra legyen frissítve. Ha bekapcsolja az automatikus frissítéseket a Windows 10-es verzióban (IoT vagy más SKU-ban), a Microsoft naprakészen tartja, és biztonságos operációs rendszert biztosít a IoT-eszközök számára. A többi operációs rendszer (például a Linux) naprakészen tartása révén biztosítható, hogy a kártékony támadásokkal szemben is védve legyenek.

* **Rosszindulatú tevékenység elleni védelem**: Ha az operációs rendszer engedélyezi, telepítse a legújabb vírusvédelmi és antimalware-képességeket minden eszköz operációs rendszeren. Ez a gyakorlat segít enyhíteni a legtöbb külső fenyegetést. A legmodernebb operációs rendszereket a megfelelő lépések végrehajtásával biztosíthatja a fenyegetések elleni védelemhez.

* **Gyakori ellenőrzés**: a biztonsági incidensekre való válaszadáskor a IoT-infrastruktúra naplózása a biztonsággal kapcsolatos problémák esetén kulcsfontosságú. A legtöbb operációs rendszer olyan beépített eseménynaplózást biztosít, amelyet gyakran kell felülvizsgálni, hogy ne legyenek biztonsági rések. A naplózási információk elküldése külön telemetria streamként is elvégezhető a Cloud Service-ben, ahol elemezni lehet.

* **A IoT-infrastruktúra fizikai védelme**: az IoT-infrastruktúra legrosszabb biztonsági támadásait az eszközök fizikai elérésével lehet elindítani. Az egyik fontos biztonsági eljárás az USB-portok és egyéb fizikai hozzáférések rosszindulatú használata elleni védelem. Az esetlegesen előforduló szabálysértések felfedésének egyik kulcsa a fizikai hozzáférés naplózása, például az USB-port használata. A Windows 10 (IoT és más SKU-EK) is lehetővé teszi az események részletes naplózását.

* **Felhőbeli hitelesítő adatok biztosítása**: a IoT-telepítés konfigurálásához és üzemeltetéséhez használt Felhőbeli hitelesítési hitelesítő adatok valószínűleg a legegyszerűbben a IoT rendszer elérésének és sérülésének megtámadására szolgálnak. Védi a hitelesítő adatokat a jelszó módosításával, és ne használja a hitelesítő adatokat a nyilvános gépeken.

A különböző IoT-eszközök képességei eltérőek. Előfordulhat, hogy egyes eszközök olyan számítógépek, amelyek közös asztali operációs rendszereket futtatnak, és néhány eszközön nagyon kis teljesítményű operációs rendszerek futnak. A korábban ismertetett ajánlott biztonsági eljárások különböző mértékben alkalmazhatók ezekre az eszközökre. Ha meg van adni, az eszközök gyártóinak további biztonsági és üzembe helyezési ajánlott eljárásait kell követni.

Előfordulhat, hogy egyes örökölt és korlátozott eszközök nem lettek kialakítva kifejezetten a IoT telepítéséhez. Előfordulhat, hogy ezek az eszközök nem képesek titkosítani az adattitkosítást, csatlakozhatnak az internethez, vagy speciális naplózást biztosítanak. Ezekben az esetekben a modern és biztonságos adatátjárók a régi eszközökről származó adatokat összesítik, és az eszközök interneten keresztüli csatlakoztatásához szükséges biztonságot biztosítják. A helyszíni átjárók biztonságos hitelesítést, titkosított munkamenetek egyeztetését, a felhőből érkező parancsok fogadását és számos más biztonsági funkciót biztosítanak.