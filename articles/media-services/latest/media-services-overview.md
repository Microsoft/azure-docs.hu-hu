---
title: Azure Media Services v3 Áttekintés: Azure Media Services Leírás: az Azure Media Services v3 magas szintű áttekintése, amely rövid útmutatókra, oktatóanyagokra és mintakód-mintákra mutató hivatkozásokat tartalmaz.
szolgáltatások: Media-Services documentationcenter: na Author: IngridAtMicrosoft Manager: femila Editor: ' ' Tags: ' ' Keywords: Azure Media Services, stream, Broadcast, Live, offline

MS. Service: Media-Services MS. devlang: több MS. topic: Áttekintés ms.tgt_pltfrm: több MS. munkaterhelés: Media MS. Date: 08/31/2020 MS. Author: inhenkel MS. Custom: MVC
#<a name="customer-intent-as-a-developer-or-a-content-provider-i-want-to-encode-stream-on-demand-or-live-analyze-my-media-content-so-that-my-customers-can-view-the-content-on-a-wide-variety-of-browsers-and-devices-gain-valuable-insights-from-recorded-content"></a>Ügyfél szándéka: fejlesztőként vagy tartalomszolgáltatóként szeretnék kódolni, adatfolyamként (igény szerint vagy élő verzióban) elemezni a médiatartalom tartalmát, hogy az ügyfeleim: megtekintheti a tartalmakat a böngészők és az eszközök széles köréről, értékes információkhoz juthat a rögzített tartalmakról.
---

# <a name="azure-media-services-v3-overview"></a>Az Azure Media Services v3 áttekintése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Akár egy app Developer, egy Call Center, egy kormányzati szerv vagy egy szórakoztató cég, Media Services segítségével olyan alkalmazásokat hozhat létre, amelyek kiváló minőségű multimédiás élményt biztosítanak a nagy közönségnek a mai legnépszerűbb mobileszközök és böngészők számára.

A Media Services v3 SDK [-k Media Services v3 OpenAPI-specifikáción (henceg)](https://aka.ms/ams-v3-rest-sdk)alapulnak.

> [!NOTE]
> Jelenleg a [Azure Portal](https://portal.azure.com/) használható a következőhöz: Media Services v3 [élő események](live-events-outputs-concept.md), View (nem felügyelt) v3- [eszközök](assets-concept.md)kezelése, az API-k [elérésére vonatkozó információk lekérése](./access-api-howto.md). Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](/rest/api/media/), a [CLI](/cli/azure/ams)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos megjegyezni, hogy be kell tartania az összes vonatkozó törvényt a Azure Media Services használatára vonatkozóan, és Ön nem használhatja Media Services vagy bármely Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy amelyek károsak lehetnek mások számára.

A videók vagy rendszerképek Media Servicesba való feltöltése előtt minden megfelelő jogosultsággal kell rendelkeznie a videó/rendszerkép használatához, beleértve a törvény által előírt jogokat, a videóban/képben, az adatok felhasználásához, feldolgozásához és tárolásához szükséges összes hozzájárulást a Media Services és az Azure-ban. Bizonyos joghatóságok bizonyos adatkategóriák, például biometrikus adatok gyűjtésére, online feldolgozására és tárolására vonatkozó különleges jogi követelményeket állapíthatnak meg. Mielőtt a Media Services és az Azure-t használja a különleges jogi követelmények hatálya alá eső bármilyen adat feldolgozásához és tárolásához, meg kell győződnie arról, hogy megfelel az Ön számára esetlegesen felmerülő jogi követelményeknek.

A megfelelőség, az adatvédelem és a biztonság megismeréséhez Media Services látogasson el a Microsoft [adatvédelmi központba](https://www.microsoft.com/trust-center/?rtc=1). A Microsoft adatvédelmi kötelezettségei, az adatkezelési és adatmegőrzési eljárások, beleértve az adatok törlésének módját is, tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Media Services használatával Ön vállalja, hogy az OST, a DPA és az adatvédelmi nyilatkozat köti.
 
## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

Media Services a felhőben különböző adathordozó-munkafolyamatokat hozhat létre. Néhány példa arra, hogy mit tehet a Media Services a következők:

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A videó-és hangtartalmat a különböző ügyfelek (mobileszközök, televízió, számítógépek stb.) esetében igény szerinti és élő közvetítéssel is el kell kódolni, és megfelelően be kell csomagolni. Az ilyen tartalmak továbbításával és streamelésével kapcsolatban tekintse meg a [fájlok kódolásával és streamelésével foglalkozó rövid útmutatót](stream-files-dotnet-quickstart.md).
* Élő sporteseményeket közvetít egy nagy online közönség, például a futball, a baseball, a Kollégium és a középiskolás sportok terén.
* Nyilvános találkozók és események, például a városháza, a városi tanács ülései és a törvényhozó szervek közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Oktatási e-learning platformot hozhat létre a Azure Media Services és az [Azure Cognitive Services API-k](../../index.yml?pivot=products&panel=ai) a beszéd-szöveges feliratozáshoz, a több nyelvre való fordításhoz és így tovább.
* A Azure Media Services és az [Azure Cognitive Services API-k](../../index.yml?pivot=products&panel=ai) együttes használatával feliratokat és feliratokat adhat hozzá a videókhoz, hogy a szélesebb közönség számára is elérhető legyen (például a fogyatékkal élők vagy más nyelveken elolvasni kívánt személyek).
* Lehetővé teszi, hogy a Azure CDN nagyobb skálázást érjenek el a pillanatnyi magas terhelések jobb kezelése érdekében (például egy termék indítási eseményének megkezdése).

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát?

Megtudhatja, hogyan kódolhat és csomagolhat tartalmakat, közvetítheti a videókat igény szerint, hogyan közvetítheti élőben, és hogyan elemezheti videóit a Media Services v3 használatával. Az oktatóanyagok, API-referenciák és az egyéb dokumentációk bemutatják, hogyan közvetíthet biztonságosan igény szerinti vagy élő videó- és audióstreameket akár felhasználók millióira méretezhetően is.

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át az [alapvető fogalmakat](concepts-overview.md) , amelyek olyan fontos fogalmakat tartalmaznak, mint például a csomagolás, a kódolás és a védelem, valamint [a Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztés, amely az API-k, elnevezési konvenciók és így tovább.

### <a name="sdks"></a>SDK-k

Kezdje el a fejlesztést [Azure Media Services v3 ügyféloldali SDK](media-services-apis-overview.md#sdks)-val.

### <a name="quickstarts"></a>Rövid útmutatók  

A gyors üzembe helyezési útmutatóban az új ügyfelek gyorsan kipróbálhatják a Media Services.

* [Videófájlok streamelése – .NET](stream-files-dotnet-quickstart.md)
* [Videofájlok továbbítása – parancssori felület](stream-files-cli-quickstart.md)
* [Videófájlok streamelése – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Oktatóanyagok

Az oktatóanyagok a leggyakoribb Media Services feladatokhoz tartozó forgatókönyv-alapú eljárásokat mutatják be.

* [Távoli fájl kódolása és videó streamelése – REST](stream-files-tutorial-with-rest.md)
* [Feltöltött fájl kódolása és videó streamelése – .NET](stream-files-tutorial-with-api.md)
* [Élő stream – .NET](stream-live-tutorial-with-api.md)
* [Videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)

### <a name="samples"></a>Példák

Használja [ezt a minta böngészőt](/samples/browse/?products=azure-media-services) Azure Media Services kódok tallózásához.

### <a name="how-to-guides"></a>Útmutatók

A útmutatók olyan mintakód-mintákat tartalmaznak, amelyek bemutatják a feladatok végrehajtásának módját. Ebben a szakaszban számos példát talál. Íme néhány közülük:

* [Fiók létrehozása – parancssori felület](./create-account-howto.md)
* [Adatelérési API-k – parancssori felület](./access-api-howto.md)
* [Kódolás HTTPS-sel, a feladathoz megadott bemenetként – .NET](job-input-from-http-how-to.md)  
* [Események monitorozása – Portál](monitor-events-portal-how-to.md)
* [Dinamikus titkosítás a multi-DRM-.NET használatával](protect-with-drm.md) 
* [Kódolás egyéni átalakítással – parancssori felület](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók az alapvető fogalmakról](concepts-overview.md)
