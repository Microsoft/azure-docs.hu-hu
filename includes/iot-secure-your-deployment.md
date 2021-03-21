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
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010698"
---
Ez a cikk az Azure IoT-alapú eszközök internetes hálózata (IoT) infrastruktúra biztonságossá tételének következő részletességi szintjét ismerteti. Az egyes összetevők konfigurálásának és telepítésének megvalósítási szintjére mutató hivatkozásokat tartalmaz. Emellett összehasonlításokat és választási lehetőségeket is biztosít a különböző versengő módszerek között.

Az Azure IoT üzembe helyezésének biztonságossá tételét a következő három biztonsági területre lehet osztani:

* **Eszköz biztonsága**: a IoT-eszköz védelme a vadonban történő üzembe helyezés közben.

* **Kapcsolat biztonsága**: a IoT-eszköz és a IoT hub között továbbított összes adat bizalmas és illetéktelen hozzáférést biztosít.

* **Felhőbeli biztonság**: az adatátvitelt és a felhőben tárolt adatvédelmet biztosító eszköz.

![Három biztonsági terület](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Biztonságos eszközök kiépítés és hitelesítés

A IoT-megoldás gyorsítja a IoT-eszközök biztonságos használatát a következő két módszer használatával:

* Egyedi azonosító kulcs (biztonsági jogkivonatok) biztosításával minden eszközhöz, amelyet az eszköz használhat a IoT Hubsal való kommunikációhoz.

* Az eszközön az [X. 509 tanúsítvány](https://www.itu.int/rec/T-REC-X.509-201210-S) és a titkos kulcs használatával hitelesítheti az eszközt a IoT hub. Ez a hitelesítési módszer biztosítja, hogy az eszközön található titkos kulcs ne legyen ismert az eszközön kívül, magasabb szintű biztonságot biztosítva.

A biztonsági jogkivonat módszere hitelesítést biztosít az eszköz által a IoT Hubhoz végrehajtott minden egyes híváshoz, ha a szimmetrikus kulcsot az egyes hívásokhoz társítja. Az X. 509-alapú hitelesítés lehetővé teszi egy IoT-eszköz hitelesítését a fizikai rétegen a TLS-kapcsolat létesítésének részeként. A biztonsági jogkivonat-alapú módszer az X. 509 hitelesítés nélkül is használható, amely kevésbé biztonságos minta. A két módszer közötti választást elsősorban az eszköz hitelesítésének biztonságossá tételére és az eszközön rendelkezésre álló biztonságos tárterületre (a titkos kulcs biztonságos tárolására) kell meghatározni.

## <a name="iot-hub-security-tokens"></a>Biztonsági jogkivonatok IoT Hub

A IoT Hub biztonsági jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a hálózaton. Emellett a biztonsági jogkivonatok időbeli érvényessége és hatóköre korlátozott. Az Azure IoT SDK-k automatikusan állítanak elő jogkivonatokat anélkül, hogy speciális konfigurációra lenne szükség. Bizonyos esetekben azonban a felhasználónak a biztonsági jogkivonatok közvetlen létrehozását és használatát kell megkövetelni. Ezek a forgatókönyvek magukban foglalják a MQTT, a AMQP vagy a HTTP-felületek közvetlen használatát, vagy a jogkivonat-szolgáltatási minta megvalósítását.

A biztonsági jogkivonat struktúrájával és használatával kapcsolatos további részletek a következő cikkekben találhatók:

* [Biztonsági jogkivonat szerkezete](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [SAS-tokenek használata eszközként](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Minden IoT Hub rendelkezik egy [azonosító-beállításjegyzékkel](../articles/iot-hub/iot-hub-devguide-identity-registry.md) , amely a szolgáltatásban eszközönkénti erőforrások létrehozására használható, például egy üzenetsor, amely a felhőből az eszközre irányuló üzeneteket tartalmaz, és lehetővé teszi az eszközre irányuló végpontok elérését. A IoT Hub Identity Registry biztonságos tárolót biztosít az eszközök identitásai és biztonsági kulcsai számára a megoldáshoz. Az eszközök hozzáférésének teljes körű szabályozása lehetővé teszi, hogy egy engedélyezési listához vagy egy adott csoporthoz tartozó identitásokat lehessen hozzáadni. A következő cikkek további részleteket tartalmaznak az Identity Registry és a támogatott műveletek struktúrájáról.

[A IoT hub olyan protokollokat támogat, mint például a MQTT, a AMQP és a http](../articles//iot-hub/iot-hub-devguide-security.md). A protokollok mindegyike biztonsági jogkivonatokat használ a IoT-eszközről, hogy IoT Hub másképpen:

* AMQP: SASL PLAIN és AMQP ( `{policyName}@sas.root.{iothubName}` IoT hub-szintű jogkivonatokkal rendelkező) jogcímek alapú biztonság `{deviceId}` .

* MQTT: a csomag a `{deviceId}` `{ClientId}` Jelszó mezőben lévő, a `{IoThubhostname}/{deviceId}` **Felhasználónév** és a sas-token  használatával kapcsolja össze a csomagot.

* HTTP: az érvényes jogkivonat az engedélyezési kérelem fejlécében található.

A IoT Hub Identity Registry használatával eszközönkénti biztonsági hitelesítő adatokat és hozzáférés-vezérlést konfigurálhat. Ha azonban egy IoT-megoldás már jelentős befektetéssel rendelkezik egy [Egyéni eszköz-identitási beállításjegyzékben és/vagy hitelesítési sémában](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), akkor a IoT hub a jogkivonat-szolgáltatás létrehozásával integrálható egy meglévő infrastruktúrába.

### <a name="x509-certificate-based-device-authentication"></a>X. 509 tanúsítvány alapú eszköz hitelesítése

Az [eszközön alapuló X. 509 tanúsítvány](../articles/iot-hub/iot-hub-devguide-security.md) és a hozzá tartozó magán-és nyilvános kulcspár további hitelesítést tesz lehetővé a fizikai rétegben. A titkos kulcsot a rendszer biztonságosan tárolja az eszközön, és az eszközön kívül nem felderíthető. Az X. 509 tanúsítvány az eszközre vonatkozó információkat, például az eszköz AZONOSÍTÓját és egyéb szervezeti adatokat tartalmaz. A tanúsítvány aláírása a titkos kulcs használatával jön létre.

Magas szintű eszközök kiépítési folyamata:

* Rendeljen egy azonosítót egy fizikai eszközhöz – eszköz-identitás és/vagy X. 509 tanúsítvány társítva az eszközhöz a gyártás vagy az üzembe helyezés során.

* Hozzon létre egy megfelelő identitás bejegyzést a IoT Hub – eszköz identitása és a hozzá tartozó eszköz adatai között a IoT Hub Identity registryben.

* Az X. 509 tanúsítvány ujjlenyomatának biztonságos tárolása IoT Hub Identity registryben.

### <a name="root-certificate-on-device"></a>Főtanúsítvány az eszközön

A biztonságos TLS-kapcsolat IoT Hub használatával történő létrehozása közben a IoT-eszköz hitelesíti IoT Hub az eszköz SDK részét képező főtanúsítvánnyal. A C Client SDK esetében a tanúsítvány a tárház \\ gyökerében található "C \\ tanúsítványok" mappában található. Habár ezek a főtanúsítványok hosszú életűek, azok továbbra is lejárnak vagy visszavonhatók. Ha a tanúsítvány nem frissíthető az eszközön, előfordulhat, hogy az eszköz nem tud majd csatlakozni a IoT Hubhoz (vagy bármely más felhőalapú szolgáltatáshoz). Ha azt szeretné, hogy a IoT-eszköz üzembe helyezése után a főtanúsítványt frissíteni lehessen, a rendszer hatékonyan csökkenti ezt a kockázatot.

## <a name="securing-the-connection"></a>A kapcsolatok biztonságossá tétele

A IoT-eszköz és a IoT Hub közötti internetkapcsolat a Transport Layer Security (TLS) szabvány használatával biztosítva van. Az Azure IoT ebben a sorrendben támogatja a [tls 1,2](https://tools.ietf.org/html/rfc5246), a TLS 1,1 és a TLS 1,0 használatát. A TLS 1,0-támogatás csak visszamenőleges kompatibilitás érdekében biztosított. Ellenőrizze a [TLS-támogatást IoT Hubban](../articles/iot-hub/iot-hub-tls-support.md) , hogy megtudja, hogyan konfigurálhatja a HUBOT a TLS 1,2 használatára, mivel ez biztosítja a legtöbb biztonságot.

## <a name="securing-the-cloud"></a>A felhő biztonságossá tétele

Az Azure IoT Hub lehetővé teszi az egyes biztonsági kulcsok [hozzáférés-vezérlési házirendjeinek](../articles/iot-hub/iot-hub-devguide-security.md) meghatározását. A következő engedélyeket használja az egyes IoT Hub végpontokhoz való hozzáférés biztosításához. Az engedélyek a funkciók alapján korlátozzák az IoT Hubhoz való hozzáférést.

* **RegistryRead**. Olvasási hozzáférést biztosít az identitás-beállításjegyzékhez. További információ: [Identity Registry](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Olvasási és írási hozzáférést biztosít az identitás-beállításjegyzékhez. További információ: [Identity Registry](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Hozzáférést biztosít a felhőalapú szolgáltatások felé irányuló kommunikációs és figyelési végpontokhoz. Például engedélyt ad a háttérbeli felhőalapú szolgáltatásoknak az eszközről a felhőbe irányuló üzenetek fogadására, a felhőből az eszközre irányuló üzenetek küldésére és a megfelelő kézbesítési visszaigazolások beolvasására.

* **DeviceConnect**. Hozzáférést biztosít az eszköz felé irányuló végpontokhoz. Engedélyt ad például az eszközről a felhőbe irányuló üzenetek küldésére és a felhőből az eszközre irányuló üzenetek fogadására. Ezt az engedélyt az eszközök használják.

A **DeviceConnect** -engedélyeket kétféleképpen lehet megszerezni IoT hub [biztonsági jogkivonatokkal](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): eszköz-azonosító kulcs vagy megosztott elérési kulcs használatával. Azt is fontos megjegyezni, hogy az eszközökről elérhető összes funkció az előtaggal rendelkező végpontokon van kitéve `/devices/{deviceId}` .

A szolgáltatás-összetevők csak a megfelelő engedélyeket biztosító megosztott hozzáférési szabályzatok segítségével hozhatnak [elő biztonsági jogkivonatokat](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) .

Az Azure IoT Hub és más, a megoldás részét képező szolgáltatások lehetővé teszik a felhasználók felügyeletét a Azure Active Directory használatával.

Az Azure IoT Hub által betöltött adatmennyiség számos szolgáltatás, például a Azure Stream Analytics és az Azure Blob Storage használatával is felhasználható. Ezek a szolgáltatások lehetővé teszik a felügyeleti hozzáférést. További információ ezekről a szolgáltatásokról és a rendelkezésre álló lehetőségekről:

* [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/): méretezhető, teljes mértékben indexelt adatbázis-szolgáltatás a részben strukturált adatokhoz, amelyek a kiépített eszközök metaadatait kezelik, például attribútumok, konfiguráció és biztonsági tulajdonságok. A Azure Cosmos DB nagy teljesítményű és nagy adatátviteli sebességű feldolgozást, sémát és részletes SQL-lekérdezési felületet kínál.

* [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/): valós idejű adatfolyam-feldolgozás a felhőben, amely lehetővé teszi, hogy gyorsan fejlesszen és helyezzen üzembe egy alacsony költséghatékonyságú elemzési megoldást az eszközökről, érzékelőkről, infrastruktúrából és alkalmazásokból származó valós idejű elemzések felfedéséhez. A teljes körűen felügyelt szolgáltatásból származó adatok bármilyen kötetre méretezhetők, miközben továbbra is nagy átviteli sebességet, kis késést és rugalmasságot biztosítanak.

* [Azure app Services](https://azure.microsoft.com/services/app-service/): felhőalapú platform olyan hatékony webes és mobil alkalmazások létrehozásához, amelyek bárhonnan csatlakozhatnak az adatkapcsolatokhoz; a felhőben vagy a helyszínen. Vonzó mobil alkalmazások készítése iOS, Android és Windows rendszerre. A szolgáltatásként (SaaS) és nagyvállalati alkalmazásokkal is integrálható, és a beépített kapcsolattal több tucat felhőalapú szolgáltatáshoz és nagyvállalati alkalmazáshoz is csatlakozhat. A kód a kedvenc nyelvén és az IDE (.NET, Node.js, PHP, Python vagy Java) használatával minden eddiginél gyorsabban hozhat létre webalkalmazásokat és API-kat.

* [Logic apps](https://azure.microsoft.com/services/app-service/logic/): a Azure app Service Logic apps szolgáltatása segít a IoT-megoldás integrálásában a meglévő üzletági rendszerekre, és automatizálni a munkafolyamatok folyamatait. Logic Apps lehetővé teszi a fejlesztők számára, hogy olyan munkafolyamatokat tervezzenek, amelyek egy eseményindítóból kezdődnek, majd végrehajtanak egy sor lépést – olyan szabályokat és műveleteket, amelyek hatékony összekötőket használnak az üzleti folyamatokkal való integrációhoz. A Logic Apps a SaaS-, felhőalapú és helyszíni alkalmazások hatalmas ökoszisztémájának megfelelő kapcsolattal rendelkezik.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): megbízható, gazdaságos Felhőbeli tárolás az eszközök által a felhőbe küldött adatai számára.

## <a name="conclusion"></a>Összegzés

Ez a cikk áttekintést nyújt a IoT-infrastruktúra Azure IoT használatával történő tervezéséről és üzembe helyezéséről. Az egyes összetevők biztonságossá tétele kulcsfontosságú a teljes IoT-infrastruktúra biztonságossá tételéhez. Az Azure IoT elérhető tervezési lehetőségek bizonyos fokú rugalmasságot és választékot biztosítanak; azonban minden választási lehetőség biztonsági következményekkel járhat. Javasoljuk, hogy ezeket a döntéseket a kockázat/költséghatékonyság értékelésével értékelje ki.
