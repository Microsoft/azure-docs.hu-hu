---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557376"
---
A eszközök internetes hálózata (IoT) egyedi biztonsági, adatvédelmi és megfelelőségi kihívásokat jelent a vállalkozások számára világszerte. A hagyományos számítógépes technológiától eltérően, ahol ezek a problémák a szoftvereket és azok megvalósítását tárgyalják, IoT, hogy mi történik, ha a Cyber és a fizikai világ konvergál. A IoT-megoldások védelme megköveteli az eszközök biztonságos kihelyezését, az eszközök és a felhő közötti biztonságos csatlakozást, valamint a Felhőbeli adatvédelem védelmét a feldolgozás és a tárolás során. Az ilyen funkciókkal való munka azonban erőforrás-korlátozott eszközök, a központi telepítések földrajzi eloszlása és a megoldáson belüli számos eszköz.

Ez a cikk azt mutatja be, hogy a IoT-megoldás gyorsítása hogyan nyújt biztonságos és privát eszközök internetes hálózata felhőalapú megoldást. A megoldás-gyorsítók teljes körű megoldást biztosítanak, és az alapoktól kezdve minden fázisban beépített biztonsággal rendelkeznek. A Microsoftnál a biztonságos szoftverek fejlesztése a szoftver mérnöki gyakorlatának része, amely a Microsoft évtizedes, biztonságos szoftverek fejlesztésével kapcsolatos tapasztalataiban gyökerezik. Ennek érdekében a biztonsági fejlesztési életciklus (SDL) az alapszintű fejlesztési módszer, amely az infrastruktúra-szintű biztonsági szolgáltatások (például az OSA) és a Microsoft Digital Crimes Unit, a Microsoft Security Response Center és a Microsoft Malware Protection Center szolgáltatással párosul.

A megoldás-gyorsítók olyan egyedi funkciókat kínálnak, amelyek lehetővé teszik a IoT-eszközökről történő üzembe helyezést, csatlakozást és adattárolást egyszerűen és átlátható módon, valamint a legtöbbet a biztonság érdekében. Ez a cikk a biztonság, az adatvédelem és a megfelelőségi problémák megoldása érdekében megvizsgálja az Azure IoT megoldás-gyorsító biztonsági funkcióit és központi telepítési stratégiáit.

## <a name="introduction"></a>Bevezetés

A eszközök internetes hálózata (IoT) a jövő hulláma, amely azonnali és valós lehetőségeket kínál a vállalatok számára a költségek csökkentéséhez, a bevétel növeléséhez és a vállalat átalakításához. Sok vállalat azonban vonakodik üzembe helyezni a IoT a szervezetében a biztonsággal, az adatvédelemmel és a megfelelőséggel kapcsolatos probléma miatt. Fontos szempont a IoT-infrastruktúra egyedisége, amely egyesíti a Cyber-és a fizikai világokat, és összekeveri a két világ különböző kockázatait. A IoT biztonsága az eszközökön futó kódok integritásának biztosítására, az eszközök és a felhasználók hitelesítésének megadására, az eszközök (és az eszközök által létrehozott adatok) törlésére, valamint a számítógépes és fizikai támadások elleni védelemre vonatkozik.

Ezt követően az adatvédelem probléma merül fel. A vállalatok az adatok gyűjtésével kapcsolatos átláthatóságot szeretnének elérni, ahogy az adatgyűjtés és miért, ki láthatja, hogy ki és hogyan szabályozza a hozzáférést. Végezetül a berendezések általános biztonsági problémái vannak az azokat működtető személyekkel együtt, valamint az iparági szabványok fenntartásával kapcsolatos problémák.

A biztonság, az adatvédelem, az átláthatóság és a megfelelőség tekintetében a megfelelő IoT megoldás-szolgáltató kiválasztása továbbra is kihívást jelent. A különböző gyártók által biztosított egyedi IoT-szoftverek és-szolgáltatások összefűzése hiányosságokat jelent a biztonság, az adatvédelem, az átláthatóság és a megfelelőség terén, ami nehezen észlelhető, és nem is oldható meg. A megfelelő IoT szoftver és szolgáltató kiválasztása olyan szolgáltatók megtalálásán alapul, amelyek széleskörű tapasztalattal rendelkeznek a vertikális és földrajzi területekre kiterjedő szolgáltatások futtatásában, de a méretezésük is biztonságos és transzparens módon történik. Hasonlóképpen, a kiválasztott szolgáltató több évtizedes tapasztalattal rendelkezik a világszerte több milliárd gépen futó biztonságos szoftverek fejlesztéséhez, és képes arra, hogy értékelje a eszközök internetes hálózata új világának által jelentett fenyegetési tájat.

## <a name="secure-infrastructure-from-the-ground-up"></a>Biztonságos infrastruktúra az alapoktól

A [Microsoft Cloud](https://azure.microsoft.com) -infrastruktúra több mint 1 000 000 000 ügyfelet támogat a 127 országokban/régiókban. A Microsoft évtizedes tapasztalatának kialakítása a nagyvállalati szoftverek fejlesztése és a világ egyik legnagyobb online szolgáltatásokának futtatása során a Microsoft Cloud magasabb szintű biztonsági, adatvédelmi, megfelelőségi és veszélyforrás-enyhítési eljárásokat biztosít, mint a legtöbb ügyfél számára.

A [biztonsági fejlesztési életciklus (SDL)](https://www.microsoft.com/sdl/) egy kötelező, vállalati szintű fejlesztési folyamatot biztosít, amely a biztonsági követelményeket a teljes szoftver-életciklusba ágyazza. Annak érdekében, hogy az operatív tevékenységek ugyanazt a biztonsági gyakorlatot kövessék, az SDL a Microsoft Operational Security Assurance (OSA) folyamatában meghatározott szigorú biztonsági irányelveket alkalmazza. A Microsoft emellett együttműködik a harmadik féltől származó könyvvizsgáló cégekkel a megfelelőségi kötelezettségek teljesítésének folyamatos ellenőrzéséhez, és a Microsoft széleskörű biztonsági erőfeszítéseket tesz a kiválósági központok létrehozásával, beleértve a Microsoft digitális bűncselekmények egységét, a Microsoft Security Response centert és a Microsoft kártevő-kezelési központját.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – biztonságos IoT infrastruktúra a vállalat számára

A Microsoft Azure egy teljes körű felhőalapú megoldást kínál, amely az integrált Cloud Services, az elemzés, a gépi tanulás, a tárolás, a biztonság, a hálózat és a web folyamatosan bővülő gyűjteményét ötvözi, és piacvezető elkötelezettséggel rendelkezik az adatok védelme és védelme terén. A Microsoft által elkövetett [szabálysértési](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) stratégia egy dedikált, a támadásokat szimuláló biztonsági szakértőkből álló *vörös csapatot* használ, amely az Azure által észlelt, a felmerülő fenyegetések elleni védekezést és a behatolások elleni védelmet vizsgálja. A Microsoft [globális incidensekre reagáló](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) csapata éjjel-nappal működik a támadások és a rosszindulatú tevékenységek hatásának enyhítése érdekében. A csapat az incidensek kezelésével, a kommunikációval és a helyreállítással kapcsolatos eljárásokat követi, és a belső és külső partnerekkel használható, felderíthető és kiszámítható interfészeket használ.

A Microsoft rendszerei folyamatos behatolás-észlelést és-megelőzést, a szolgáltatások támadásának megelőzését, a rendszeres behatolás-tesztelést, valamint a fenyegetések azonosítását és enyhítését segítő kriminalisztikai eszközöket biztosítanak. A [többtényezős hitelesítés](../articles/active-directory/authentication/concept-mfa-howitworks.md) további biztonsági réteget biztosít a végfelhasználók számára a hálózat eléréséhez. Az alkalmazás és a gazdagép szolgáltatója esetében a Microsoft hozzáférést biztosít a hozzáférés-vezérléshez, a figyeléshez, a kártevő szoftverekhez, a sebezhetőségek vizsgálatához, a javításokhoz és a konfiguráció kezeléséhez.

A megoldás-gyorsítók kihasználják az Azure platformon beépített biztonságot és adatvédelmet, valamint az SDL-és OSA-folyamatokat az összes Microsoft-szoftver biztonságos fejlesztéséhez és működtetéséhez. Ezek az eljárások biztosítják az infrastruktúra védelmét, a hálózati védelmet, valamint az olyan identitás-és felügyeleti funkciókat, amelyek alapvető fontosságúak a megoldások biztonsága szempontjából.

Az [azure IoT hub](../articles/iot-hub/about-iot-hub.md) a [IoT megoldás-gyorsító](../articles/iot-fundamentals/iot-introduction.md) szolgáltatásban egy teljes körűen felügyelt szolgáltatást kínál, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé a IoT-eszközök és az Azure-szolgáltatások, például a [Azure Machine learning](../articles/machine-learning/classic/index.yml) és a [Azure stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) között, eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés használatával.

A jelen cikk az Azure IoT-megoldás-gyorsítók beépített biztonsági és adatvédelmi funkcióinak lehető legjobb kommunikációját ismerteti. Ez a cikk a három elsődleges biztonsági területre bontja fel a csomagot.

![Azure IoT-megoldásgyorsítók](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Biztonságos eszközök kiépítés és hitelesítés

A megoldás-gyorssegédek biztonságos eszközöket biztosítanak a mezőkben, mivel mindegyik eszközhöz egyedi azonosító kulcsot biztosítanak, amelyet a IoT-infrastruktúra a működés közbeni kommunikációhoz használhat az eszközzel. A folyamat gyorsan és egyszerűen beállítható. A felhasználó által kiválasztott eszköz AZONOSÍTÓjának generált kulcsa az eszköz és az Azure IoT Hub közötti összes kommunikációban használt jogkivonat alapja.

Az eszközök azonosítói társítva lehetnek egy eszközhöz a gyártás során (azaz egy hardveres megbízhatósági modulban), vagy használhatnak meglévő rögzített identitást proxyként (például CPU-sorozatszámok). Mivel az eszközön az azonosítási adatok módosítása nem egyszerű, fontos, hogy a logikai eszközök azonosítóit bevezesse, ha a mögöttes eszköz hardvere megváltozik, de a logikai eszköz változatlan marad. Bizonyos esetekben előfordulhat, hogy az eszköz identitásának társítása az eszköz központi telepítésének idejére történik (például egy hitelesített mező mérnöke fizikailag konfigurálja az új eszközt a megoldási háttérrel való kommunikáció során). Az [Azure IoT hub Identity Registry](../articles/iot-hub/iot-hub-devguide.md) biztonságos tárolót biztosít az eszközök identitásai és biztonsági kulcsai számára a megoldáshoz. Az eszközök hozzáférésének teljes körű szabályozása lehetővé teszi, hogy egy engedélyezési listához vagy egy adott csoporthoz tartozó identitásokat lehessen hozzáadni.

Az Azure IoT Hub a Felhőbeli hozzáférés-vezérlési szabályzatok lehetővé teszik az aktiválást, és letiltják az eszközök identitását, így szükség esetén leválaszthatja az eszköz IoT-telepítésből való hozzárendelését. A társítás és az eszközök társítása minden eszköz identitásán alapul.

Az eszközök további biztonsági funkciói a következők:

* Az eszközök nem fogadnak kéretlen hálózati kapcsolatokat. A kapcsolatok és útvonalak csak kimenő módon jönnek létre. Ahhoz, hogy egy eszköz parancsot kapjon a háttérből, az eszköznek csatlakoznia kell az összes függőben lévő utasítás ellenőrzéséhez. Az eszköz és a IoT Hub közötti kapcsolat biztonságos létrehozása után a felhőből az eszközre és az eszközről a felhőbe irányuló üzenetküldést transzparens módon lehet elküldeni.

* Az eszközök csak olyan jól ismert szolgáltatások eléréséhez csatlakoznak vagy jönnek létre útvonalak, mint például az Azure IoT Hub.

* A rendszerszintű hitelesítés és a hitelesítés eszközönkénti identitások használatával történik, így a hozzáférési hitelesítő adatok és engedélyek azonnal visszavonhatók.

### <a name="secure-connectivity"></a>Biztonságos kapcsolat

Az üzenetküldés tartóssága bármely IoT-megoldás fontos funkciója. Az eszközökről érkező parancsok és/vagy adatok fogadásának szükségességét az tartósan, hogy a IoT-eszközök az interneten keresztül csatlakoznak, vagy más hasonló, megbízhatatlan hálózatok. Az Azure IoT Hub a felhő és az eszközök közötti üzenetkezelés tartósságát kínálja az üzenetekre adott válaszként. Az üzenetküldés további tartóssága a IoT Hub gyorsítótárazási üzeneteinek elérésével, a telemetria legfeljebb hét napig, a parancsok esetében pedig két napig.

A hatékonyság fontos az erőforrások és a művelet erőforrás-korlátozott környezetben való megőrzésének biztosításához. A HTTPS (HTTP Secure), a népszerű HTTP protokoll iparági szabványnak megfelelő biztonságos verziója az Azure IoT Hub támogatja, és lehetővé teszi a hatékony kommunikációt. Az Azure IoT Hub által támogatott Advanced Message Queueing Protocol (AMQP) és Message Queuing telemetria Transport (MQTT) nem csupán az erőforrás-használat, hanem az üzenetek kézbesítésének hatékonyságát is megtervezték. 

A méretezhetőséghez az eszközök széles körének biztonságos együttműködésre van szükségük. Az Azure IoT hub lehetővé teszi a biztonságos kapcsolódást az IP-kompatibilis és a nem IP-kompatibilis eszközökhöz. Az IP-kompatibilis eszközök képesek közvetlenül csatlakozni a IoT Hubhoz, és kommunikálni a biztonságos kapcsolaton keresztül. A nem IP-kompatibilis eszközök erőforrás-korlátozottak, és csak a rövid távolságú kommunikációs protokollok, például a Zwave, a ZigBee és a Bluetooth protokollon keresztül csatlakoznak. A helyszíni átjáró segítségével összesítheti ezeket az eszközöket, és elvégezheti a protokollok fordítását a felhővel való biztonságos kétirányú kommunikáció érdekében.

A további kapcsolatbiztonsági funkciók a következők:

* Az eszközök és az Azure-IoT Hub közötti, illetve az átjárók és az Azure IoT Hub közötti kommunikációs útvonal védelme az iparági szabványnak megfelelő Transport Layer Security (TLS) és az X. 509 protokollal hitelesített Azure IoT Hub használatával biztosítható.

* Az eszközök kéretlen bejövő kapcsolatokból való ellátása érdekében az Azure IoT Hub nem nyit meg kapcsolatot az eszközzel. Az eszköz minden kapcsolatot kezdeményez.

* Az Azure IoT Hub tartósan az eszközökön tárolja az üzeneteket, és megvárja, amíg az eszköz csatlakozik. Ezeket a parancsokat két napig tárolja a rendszer, így az eszközök az áramellátás vagy a kapcsolódási problémák miatt szórványosan csatlakoznak a parancsok fogadásához. Az Azure IoT Hub minden eszközön egy eszközönkénti várólistát tart fenn.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Biztonságos feldolgozás és tárolás a felhőben

A titkosított kommunikációról a Felhőbeli adatok feldolgozásához a megoldás-gyorsítók segítenek megőrizni az adatok biztonságát. Rugalmasságot biztosít a biztonsági kulcsok további titkosításának és kezelésének megvalósításában.

A Azure Active Directory (HRE) használata a felhasználói hitelesítéshez és az engedélyezéshez az Azure IoT megoldás-gyorsító a házirend-alapú hitelesítési modellt biztosít a felhőben tárolt adataihoz, és lehetővé teszi az egyszerű hozzáférés-kezelést, amely naplózható és ellenőrizhető. Ez a modell azt is lehetővé teszi, hogy közel azonnali visszavonást biztosítson a felhőben tárolt adathozzáféréshez és az Azure IoT-megoldási gyorssegédekhez csatlakozó eszközökhöz.

Ha az adatfelhőben van, akkor a felhasználó által definiált munkafolyamatokban feldolgozható és tárolható. Az egyes részekhez való hozzáférést a használt tárolási szolgáltatástól függően Azure Active Directory szabályozza.

A IoT-infrastruktúra által használt összes kulcsot a felhőben tároljuk a biztonságos tárolóban, és a kulcsokat át kell helyezni a kulcsok újraépítésének lehetőségével. Az [Azure Cosmos db](../articles/cosmos-db/introduction.md) vagy [SQL Database](../articles/azure-sql/database/sql-database-paas-overview.md)tárolt adatmennyiséget a kívánt biztonsági szint definíciójának engedélyezésével lehet tárolni. Emellett az Azure lehetővé teszi az adataihoz való összes hozzáférés monitorozását és naplózását, hogy riasztást küldjön bármilyen behatolási vagy jogosulatlan hozzáférésről.

## <a name="conclusion"></a>Összegzés

A eszközök internetes hálózata a legfontosabb dolgokkal kezdődik, ami a vállalkozások számára is fontos. A IoT a költségek csökkentésével, a bevételek növelésével és a vállalkozások átalakításával csodálatos értéket biztosíthat a vállalatoknak. Az átalakítás sikere nagyban függ a megfelelő IoT szoftver és szolgáltató kiválasztásának. Ez azt jelenti, hogy olyan szolgáltatót keres, amely nem csupán az üzleti igények és követelmények megismerésével katalizálja ezt az átalakítást, hanem a biztonsági, adatvédelmi, átláthatósági és megfelelőségi szempontokat is biztosítja a fő tervezési megfontolások alapján. A Microsoft széleskörű tapasztalattal rendelkezik a biztonságos szoftverek és szolgáltatások fejlesztésében és üzembe helyezésében, és a eszközök internetes hálózata új korszakában is vezető szerepet játszik.

A megoldás-gyorsítók olyan biztonsági intézkedéseket hoznak létre, amelyek lehetővé teszik az eszközök biztonságos figyelését a hatékonyság növelése, az operatív teljesítmény megtervezése és az innováció lehetővé tétele érdekében, és fejlett adatelemzést alkalmaznak a vállalkozások átalakításához. A megoldás a biztonság, a több biztonsági funkció és a tervezési minták felé irányuló, többszintű megközelítésével segít olyan infrastruktúrák üzembe helyezésében, amelyek megbízhatók a vállalkozások átalakításához.

## <a name="additional-information"></a>További információ

Minden megoldás-gyorsító az Azure-szolgáltatások példányait hozza létre, például:

* [**Azure IoT hub**](https://azure.microsoft.com/services/iot-hub/): az átjáró, amely összeköti a felhőt az eszközökhöz. Az egyes hubokon több millió kapcsolatra méretezhető, és az eszközönkénti hitelesítés támogatásával nagy mennyiségű adattal dolgozhat fel, így biztosítva a megoldás védelmét.

* [**Azure Cosmos db**](https://azure.microsoft.com/services/cosmos-db/): méretezhető, teljes mértékben indexelt adatbázis-szolgáltatás a részben strukturált adatokhoz, amelyek a kiépített eszközök metaadatait kezelik, például attribútumok, konfiguráció és biztonsági tulajdonságok. A Azure Cosmos DB nagy teljesítményű és nagy adatátviteli sebességű feldolgozást, sémát és részletes SQL-lekérdezési felületet kínál.

* [**Azure stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): valós idejű adatfolyam-feldolgozás a felhőben, amely lehetővé teszi, hogy gyorsan fejlesszen és helyezzen üzembe egy alacsony költséghatékonyságú elemzési megoldást az eszközökről, érzékelőkről, infrastruktúrából és alkalmazásokból származó valós idejű elemzések felfedéséhez. A teljes körűen felügyelt szolgáltatásból származó adatok bármilyen kötetre méretezhetők, miközben továbbra is nagy átviteli sebességet, kis késést és rugalmasságot biztosítanak.

* [**Azure app Services**](https://azure.microsoft.com/services/app-service/): felhőalapú platform olyan hatékony webes és mobil alkalmazások létrehozásához, amelyek bárhonnan csatlakozhatnak az adatkapcsolatokhoz; a felhőben vagy a helyszínen. Vonzó mobil alkalmazások készítése iOS, Android és Windows rendszerre. A szolgáltatásként (SaaS) és nagyvállalati alkalmazásokkal is integrálható, és a beépített kapcsolattal több tucat felhőalapú szolgáltatáshoz és nagyvállalati alkalmazáshoz is csatlakozhat. A kód a kedvenc nyelvén és IDE – .NET, Node.js, PHP, Python vagy Java – használatával minden eddiginél gyorsabban hozhat létre webalkalmazásokat és API-kat.

* [**Logic apps**](https://azure.microsoft.com/services/app-service/logic/): a Azure app Service Logic apps szolgáltatása segít a IoT-megoldás integrálásában a meglévő üzletági rendszerekre, és automatizálni a munkafolyamatok folyamatait. Logic Apps lehetővé teszi a fejlesztők számára, hogy olyan munkafolyamatokat tervezzenek, amelyek egy eseményindítóból kezdődnek, majd végrehajtanak egy sor lépést – olyan szabályokat és műveleteket, amelyek hatékony összekötőket használnak az üzleti folyamatokkal való integrációhoz. A Logic Apps a SaaS-, felhőalapú és helyszíni alkalmazások hatalmas ökoszisztémájának megfelelő kapcsolattal rendelkezik.

* [**Azure Blob Storage**](https://azure.microsoft.com/services/storage/): megbízható, gazdaságos Felhőbeli tárolás az eszközök által a felhőbe küldött adatai számára.