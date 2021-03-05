---
title: Mi az az Azure API for FHIR? – Azure API a FHIR-hez
description: A FHIR készült Azure API lehetővé teszi a gyors adatcserét a FHIR API-kon keresztül. A védett állapotadatok betöltése, kezelése és megőrzése egy felügyelt felhőalapú szolgáltatással.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/13/2020
ms.author: matjazl
ms.openlocfilehash: 3b51a07281c7d6d9307cc802dfc9f149442e88b7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177487"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Mi az a FHIR készült Azure API &reg; ?

A FHIR készült Azure API gyors adatcserét tesz lehetővé a gyors egészségügyi együttműködési erőforrások (FHIR®) API-k segítségével, melyeket a felhőben felügyelt, szolgáltatásként nyújtott szolgáltatások ("Pásti") támogatnak. Megkönnyíti az egészségügyi adatokkal dolgozó személyek számára, hogy a felhőben [tárolt védett](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) állapotadatok betöltésére, kezelésére és megőrzésére is alkalmasak legyenek. 

- Felügyelt FHIR szolgáltatás, amely percek alatt kiépítve a felhőben 
- Nagyvállalati szintű, FHIR®-alapú végpont az Azure-ban adathozzáféréshez és tárolás FHIR® formátumban
- Nagy teljesítményű, kis késleltetésű
- A védett egészségügyi információk (PHI) biztonságos kezelése megfelelő felhőalapú környezetben
- INTELLIGENS on FHIR mobil-és webes megvalósításokhoz
- Saját adatai méretezése a szerepköralapú hozzáférés-vezérléssel (RBAC)
- Naplózási naplók nyomon követése az egyes adattárokban való hozzáféréshez, létrehozáshoz, módosításhoz és olvasáshoz

A FHIR készült Azure API lehetővé teszi, hogy percek alatt hozzon létre és helyezzen üzembe egy FHIR szolgáltatást, hogy kihasználja a felhő rugalmas méretét.  Csak a szükséges átviteli sebességért és tárterületért kell fizetnie. A Power Azure API-t a FHIR-hez készült Azure-szolgáltatások gyors teljesítményre tervezték, függetlenül attól, hogy milyen méretű adatkészleteket kezel.

A FHIR API és a megfelelő adattár lehetővé teszi a biztonságos kapcsolódást és a FHIR API-kat használó rendszerekkel való kommunikációt.  A Microsoft igénybe veszi a Pásti-ajánlat üzemeltetési, karbantartási, frissítési és megfelelőségi követelményeit, így a saját működési és Fejlesztési erőforrásai szabadon felhasználhatók. 

Az alábbi videó áttekintést nyújt a FHIR készült Azure API-ról:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Az FHIR használatával kihasználhatja az adatai hatékonyságát

Az egészségügyi ágazat gyorsan átalakítja az egészségügyi adatmennyiséget a feltörekvő [FHIR &reg; ](https://hl7.org/fhir) (gyors egészségügyi együttműködési erőforrások). A FHIR egy robusztus, bővíthető adatmodellt biztosít szabványosított szemantikai és adatcsere-szolgáltatással, amely lehetővé teszi, hogy az összes rendszer a FHIR használatával működjenek együtt.  Az adatok FHIR történő átalakításával gyorsan összekapcsolhatók a meglévő adatforrások, például az elektronikus egészségügyi nyilvántartási rendszerek vagy a kutatási adatbázisok. A FHIR Emellett lehetővé teszi a gyors adatcserét a mobil-és webes fejlesztés modern implementációjában. A FHIR a legfontosabb, hogy egyszerűsítse az adatfeldolgozást, és gyorsítsa fel a fejlesztést az elemzési és gépi tanulási eszközökkel.  

### <a name="securely-manage-health-data-in-the-cloud"></a>A felhőben tárolt állapotadatok biztonságos kezelése

Az Azure API for FHIR lehetővé teszi az adatok egységes, REST-alapú, FHIR API-kkal való cseréjét a HL7 FHIR specifikációja alapján. Az Azure-ban felügyelt, Pásti által nyújtott támogatással is méretezhető és biztonságos környezetet biztosít a védett egészségügyi információ (PHI) adatainak a natív FHIR-formátumban történő kezeléséhez és tárolásához.  

### <a name="free-up-your-resources-to-innovate"></a>Erőforrások felszabadítása innovációra

A saját FHIR-szolgáltatás létrehozásához és futtatásához, de a FHIR készült Azure API-hoz a Microsoft igénybe veszi a műveletek, a karbantartás, a frissítések és a megfelelőségi követelmények számítási feladatait, lehetővé téve a saját működési és fejlesztési erőforrásainak felszabadítását.

### <a name="enable-interoperability-with-fhir"></a>Együttműködés engedélyezése a FHIR

A FHIR készült Azure API használata lehetővé teszi a kapcsolódást bármely olyan rendszerhez, amely a FHIR API-kat használja az olvasási, írási, keresési és egyéb funkciókhoz.  Hatékony eszközként használható az elektronikus egészségügyi adatokból, az orvosokból és a beteg irányítópultokból, a távoli figyelési programokból, illetve a FHIR API-kat használó rendszereken kívüli adatbázisokkal végzett gépi tanulás összevonásához, normalizálása és alkalmazása során.

### <a name="control-data-access-at-scale"></a>Az adathozzáférés szabályozása méretezéskor

Ön szabályozhatja az adatait. A szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi az adattárolás és-hozzáférés kezelését.  A fokozott biztonság és a felügyeleti munkaterhelés csökkentése révén meghatározhatja, hogy ki férhet hozzá a létrehozott adatkészletekhez a környezetéhez létrehozott szerepkör-definíciók alapján.  

### <a name="audit-logs-and-tracking"></a>Naplók és nyomkövetés naplózása 

Gyorsan nyomon követheti, hogy az adatai hogyan lesznek a beépített naplók. Nyomon követheti a hozzáférést, a létrehozást, a módosítást és az olvasást az egyes adattárokon belül.

### <a name="secure-your-data"></a>Az adatok védelme

A PHI védelme páratlan biztonsági intelligenciával.  Az adatai el vannak különítve egy egyedi adatbázisra API-példányon, és a többrégiós feladatátvételsel védettek. A FHIR készült Azure API egy rétegzett, részletes védelmi és komplex veszélyforrások elleni védelmet valósít meg az adataihoz.  

## <a name="applications-for-a-fhir-service"></a>FHIR-szolgáltatás alkalmazásai

A FHIR-kiszolgálók az állapotadatok együttműködési képességének legfontosabb eszközei.  A FHIR készült Azure API API-ként és szolgáltatásként lett kialakítva, amellyel gyorsan létrehozhatja, üzembe helyezheti és megkezdheti a használatát.  Mivel a FHIR standard kibővül az egészségügyi ellátásban, a használati esetek továbbra is növekedni fognak, de néhány olyan kezdeti ügyfél-alkalmazás esetében, ahol az Azure API for FHIR hasznosak, a következők: 

- **Indítás/IoT és alkalmazások fejlesztése:**  A beteg vagy szolgáltató központú alkalmazást (mobil vagy webes) fejlesztő ügyfelek teljes körűen felügyelt FHIR használhatják az Azure API-t. Az Azure API for FHIR egy értékes erőforrást biztosít az ügyfelek számára az adatkezeléshez és az adatcseréhez az állapot-ellenőrzésre tervezett biztonságos felhőalapú környezetben, a FHIR-megvalósítási irányelvek intelligens használatát, és lehetővé teszi, hogy a technológiájuk az összes szolgáltatói rendszer számára elérhető legyen (például a legtöbb EHRs engedélyezve van a FHIR olvasási API-k).   
- **Egészségügyi ökoszisztémák:**  Habár a EHRs a legtöbb klinikai beállításban az igazság forrásaként szerepel, nem ritka, hogy a szolgáltatók több olyan adatbázissal rendelkeznek, amelyek nem csatlakoznak egymáshoz, vagy különböző formátumokban tárolják az adattárolást.  Ha az Azure API-t használja a FHIR-hez, az ezen rendszerek egyikén alapuló szolgáltatásként FHIR formátumban szabványosíthatja az adatmennyiséget.  Ez lehetővé teszi, hogy egységes adatformátummal engedélyezze az adatcserét több rendszer között. 

- **Kutatás:** Az egészségügyi kutatók az általános FHIR standard és az Azure API for FHIR hasznosak, mivel az adatok normalizálják a közös FHIR adatmodelleket, és csökkentik a gépi tanulás és az adatmegosztás munkaterhelését.
A FHIR készült Azure API-n keresztüli adatcsere naplózási naplókat és hozzáférés-vezérléseket biztosít, amelyek segítségével szabályozható az adatáramlás, és ki férhet hozzá az adattípusokhoz. 

## <a name="fhir-from-microsoft"></a>FHIR a Microsofttól

A Microsoft FHIR képességei két konfigurációban érhetők el:

* Azure API for FHIR – az Azure-beli, a Azure Portal által könnyen kiépített és a Microsoft által felügyelt.
* FHIR Server for Azure – egy nyílt forráskódú projekt, amely üzembe helyezhető az Azure-előfizetésében, amely elérhető a GitHubon https://github.com/Microsoft/fhir-server .

Olyan használati esetekhez, amelyek a FHIR-kiszolgáló kiterjesztését vagy testreszabását igénylik, vagy ha a mögöttes szolgáltatásokat (például az adatbázist) szeretnék elérni a FHIR API-k használata nélkül, a fejlesztőknek ki kell választaniuk az Azure nyílt forráskódú FHIR-kiszolgálóját.   Kulcsrakész, éles használatra kész FHIR API-és háttér-szolgáltatás megvalósításához, ahol a megőrzött adatmennyiséget csak a FHIR API-n keresztül szabad elérni, a fejlesztőknek ki kell választaniuk az Azure API-t a FHIR

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT-összekötő a FHIR (előzetes verzió)

Az Azure IoT-összekötő a gyors egészségügyi együttműködési erőforrásokhoz (FHIR&#174;) * az Azure API opcionális funkciója a FHIR-hez, amely lehetővé teszi az orvosi eszközök internetes hálózatáról (IoMT) származó adatok betöltését. Az orvosi eszközök internetes hálózata olyan IoT-eszközök kategóriája, amelyek az egészségügyi & a wellness-és egyéb egészségügyi informatikai rendszerekkel együtt, hálózaton keresztül. Néhány példa a IoMT-eszközökre: fitnesz és klinikai wearables, monitorozási érzékelők, tevékenység-követési funkciók, vagy akár egy intelligens pirula. A FHIR szolgáltatáshoz készült Azure IoT-összekötő lehetővé teszi, hogy gyorsan beállítson egy szolgáltatást a IoMT-alapú Azure API-ba való betöltéséhez a FHIR méretezhető, biztonságos és megfelelő módon.

A FHIR készült Azure IoT-összekötők elfogadják a IoMT-eszközök által küldött JSON-alapú üzeneteket. Ezek az információk először a megfelelő FHIR-alapú [megfigyelési](https://www.hl7.org/fhir/observation.html) erőforrásokra vannak átalakítva, majd a FHIR Azure API-ban is megmaradnak. Az Adatátalakítási logikát az FHIR és a követelmények alapján konfigurált, leképezési sablonokból álló párok határozzák meg. Az eszközökről közvetlenül az Azure IoT Connector FHIR vagy az Azure-beli IoT-megoldásokkal ([azure IoT hub](../iot-hub/index.yml) és az [Azure IoT Central](../iot-central/index.yml)) való gördülékenyen használhatók. A FHIR készült Azure IoT-összekötő biztonságos adatfolyamatot biztosít, és lehetővé teszi, hogy az Azure IoT-megoldások kezelhesse a fizikai eszközök kiépítését és karbantartását.

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Az Azure IoT Connector for FHIR (előzetes verzió) alkalmazásai

A IoMT-eszközök használata gyorsan bővül az egészségügyben, és az Azure IoT-összekötő a FHIR-hez úgy lett kialakítva, hogy áthidalja a több eszköznek a biztonsággal és megfelelőséggel való ellátását a FHIR Azure API-ba. A IoMT-alapú FHIR-kiszolgálókra való betekintéssel átfogó adatelemzések és innovatív klinikai munkafolyamatok is elérhetők. A FHIR készült Azure IoT-összekötő néhány gyakori forgatókönyve a következő:
- **Távoli fekvőbeteg-figyelés/-állapot:** A távoli fekvőbeteg-figyelés lehetővé teszi a beteg egészségügyi adatok gyűjtését a hagyományos egészségügyi beállításokon kívül. Az egészségügyi intézmények használhatják az Azure IoT Connectort a FHIR-hoz, hogy a távoli eszközök által generált állapotadatok a FHIR készült Azure API-hoz legyenek létrehozva. Ezek az adatszolgáltatások a betegek állapotának nyomon követéséhez használhatók, a betegeknek a kezelési tervbe való betartásával és személyre szabott felügyelettel.
- **Kutatási és élettudományok:** A klinikai vizsgálatok gyorsan bevezetik a IoMT-eszközöket, például a bio-érzékelőket, a hordható, a mobil alkalmazásokat a próbaverziók rögzítéséhez. Ezek a kísérletek kihasználhatják az Azure IoT-összekötőt a FHIR számára, hogy biztonságos, hatékony és hatékony módon továbbítsák az adatokat az Azure API-nak FHIR. A FHIR készült Azure API-ban a próbaverziós adatai valós idejű elemzésének futtatására is felhasználhatók.
- **Speciális elemzések:** A IoMT-eszközök nagy mennyiségű és változatos adatforgalomot biztosítanak nagy sebességgel, így kiválóan alkalmasak a gépi tanulási modellek képzésének és tesztelésének biztosítására. A FHIR készült Azure IoT-összekötő a legkülönfélébb adatgyakorisággal, rugalmas adatsémával és Felhőbeli skálázással, alacsony késéssel működik. Ezek az attribútumok teszik elérhetővé az Azure IoT-összekötőt a FHIR számára, amely kiváló választás a speciális elemzési igényeknek megfelelő eszközök rögzítéséhez.
- **Intelligens kórházak/klinikák:** Napjainkban az intelligens kórházak és klinikák összekapcsolású digitális eszközök infrastruktúráját állítják össze. A FHIR készült Azure IoT-összekötők a csatlakoztatott összetevőkből származó adatok rögzítésére és integrálására használhatók. Az ilyen adathalmazok gyakorlatban hasznos információkhoz juthat a betegellátás és a működési hatékonyság érdekében.

## <a name="next-steps"></a>Következő lépések

A FHIR készült Azure API használatának megkezdéséhez kövesse az 5 perces rövid útmutatót a FHIR készült Azure API üzembe helyezéséhez.

>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)

Ha szeretné kipróbálni az Azure IoT-összekötőt a FHIR szolgáltatáshoz, tekintse meg a gyors üzembe helyezési útmutatót az Azure IoT Connector a FHIR-hez való telepítéséhez Azure Portal.

>[!div class="nextstepaction"]
>[Az Azure IoT-összekötő üzembe helyezése a FHIR](iot-fhir-portal-quickstart.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban. 
