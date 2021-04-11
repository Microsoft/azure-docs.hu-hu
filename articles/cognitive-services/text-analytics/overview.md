---
title: Adatbányászat és-elemzés a Text Analytics API-Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a Text Analytics APIával. Használhatja az érzelmek elemzéséhez, a nyelvfelismerés és a természetes nyelvi feldolgozás más formáihoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/29/2021
ms.author: aahi
keywords: szöveg-adatbányászat, érzelmek elemzése, szöveges elemzés
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: b586478b6b3943fb0154ed6c50bade6fd8b08b76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219501"
---
# <a name="what-is-the-text-analytics-api"></a>Mi a Text Analytics API?

A Text Analytics API egy felhőalapú szolgáltatás, amely természetes nyelvi feldolgozási (NLP) funkciókat biztosít a szöveg-és szöveges elemzésekhez, például a következőkhöz: érzelmek elemzése, közvélemény-kutatás, kulcsfontosságú kifejezés kinyerése, nyelvfelismerés és elnevezett entitások felismerése.

Az API az [Azure Cognitive Services](../index.yml)része, amely a felhőben a gépi tanulási és AI-algoritmusok gyűjteménye a fejlesztési projektekhez. Ezek a szolgáltatások a REST API [3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/) -es vagy [3,1-](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/)es verziójának vagy az [ügyféloldali függvénytárnak](quickstarts/client-libraries-rest-api.md)a használatával használhatók.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* [A gyors](./quickstarts/client-libraries-rest-api.md) útmutatók részletes útmutatást tesznek lehetővé, amelyekkel hívásokat indíthat a szolgáltatásba, és rövid idő alatt elérheti az eredményeket. 
* A [útmutatók](./how-tos/text-analytics-how-to-call-api.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.
* A [fogalmak](text-analytics-user-scenarios.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.
* Az [oktatóanyagok](./tutorials/tutorial-power-bi-key-phrases.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="sentiment-analysis"></a>Hangulatelemzés

Használja ki a [hangulat elemzését](how-tos/text-analytics-how-to-sentiment-analysis.md) , és Ismerje meg, hogy az emberek mit gondolnak a márka vagy a téma alapján, hogy kibányászják a pozitív vagy negatív gondolkodással kapcsolatos nyomokat. 

A szolgáltatás a szolgáltatás által a legmagasabb szintű megbízhatósági pontszám alapján, a mondatok és a dokumentumok szintjén is megadja a hangulati címkéket (például "negatív", "semleges" és "pozitív"). Ez a funkció az egyes dokumentumokhoz tartozó 0 és 1 közötti megbízhatósági pontszámokat is visszaadja, & mondatokat a pozitív, semleges és negatív hangulatra. A szolgáltatást [tároló használatával](how-tos/text-analytics-how-to-install-containers.md)is futtathatja a helyszínen.

A 3.1-es verziótól kezdődően a vélemény bányászati funkciója Hangulatelemzés. Ez a funkció a természetes nyelvi feldolgozásban (például a termékek vagy szolgáltatások attribútumaival) kapcsolatos vélemények részletesebb információit tartalmazza a szövegben található, Aspect-alapú Hangulatelemzésként.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

A [Key kifejezés kinyerésével](how-tos/text-analytics-how-to-keyword-extraction.md) gyorsan azonosíthatja a szövegben szereplő főbb fogalmakat. Például "az élelmiszer finom volt, és csodálatos volt a személyzet", Kulcsszókeresés a fő beszélő pontokat fogja visszaadni: "Food" és "csodálatos személyzet".

## <a name="language-detection"></a>Nyelvfelismerés

A nyelvfelismerés képes [észlelni, hogy egy szövegbeviteli szöveg beírásra kerül](how-tos/text-analytics-how-to-language-detection.md) , és egyetlen nyelvi kódot jelentsen a kérelemben elküldött minden dokumentumhoz a különböző nyelvek, változatok, dialektusok és egyes regionális/kulturális nyelvek esetében. A nyelvi kód egy megbízhatósági pontszámmal van párosítva.

## <a name="named-entity-recognition"></a>Nevesített entitások felismerése

Az elnevezett entitások felismerése képes [azonosítani és kategorizálni az entitásokat](how-tos/text-analytics-how-to-entity-linking.md) a szövegben, mivel a felhasználók, a helyek, a szervezetek, a mennyiségek és a jól ismert entitások is felismerhetők, és a weben található további információkhoz kapcsolódnak.

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

[Text Analytics tárolók használatával](how-tos/text-analytics-how-to-install-containers.md) telepítheti a helyszíni API-szolgáltatásokat. Ezek a Docker-tárolók lehetővé teszik, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz. Text Analytics a következő tárolókat kínálja:

* hangulat elemzése
* fő kifejezés kibontása (előzetes verzió)
* nyelvfelismerés (előzetes verzió)
* Text Analytics for Health (előzetes verzió)

## <a name="asynchronous-operations"></a>Aszinkron műveletek

A `/analyze` végpont lehetővé teszi, hogy [aszinkron módon](how-tos/text-analytics-how-to-call-api.md)válassza ki a Text Analytics API funkcióit, például az egyrészes és a kulcsfontosságú kifejezés kinyerését.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: benyújtjuk az adatokat elemzésre és a kódban kezeljük a kimeneteket. Az elemzők használatra készek, esetükben nincs szükség további konfigurációs beállításokra vagy testreszabásra.

1. [Hozzon létre egy Azure-erőforrást](how-tos/text-analytics-how-to-call-api.md) a Text Analyticshoz. Ezt követően [szerezze be a](how-tos/text-analytics-how-to-call-api.md) kérések hitelesítéséhez létrehozott kulcsot.

2. [Állítson össze egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema), amely az adatokat nyers, strukturálatlan szövegként tartalmazza, JSON formátumban.

3. Tegye közzé a kérelmet a regisztráció során létrejött végponton, fűzze hozzá a kívánt erőforrást: érzelmek elemzése, kulcsfontosságú kifejezés kinyerése, nyelvfelismerés vagy elnevezett entitások felismerése.

4. A válasz streamelhető vagy helyileg is tárolható. A kéréstől függően az eredmény lehet egy véleménypontszám, kinyert kulcskifejezések gyűjteménye vagy egy nyelvkód.

A kimenetet a rendszer egyetlen JSON-dokumentumban adja vissza, amely tartalmazza az összes elküldött szöveges dokumentum eredményeit, azok azonosítói alapján. Az eredmények ezt követően elemezhetők, vizualizálhatók vagy kategorizálhatók a gyakorlatban használható megállapításokká.

Az adatok nem lesznek tárolva a fiókjában. A Text Analytics API által végrehajtott műveletek állapot nélküliek, ami azt jelenti, hogy a szöveg feldolgozása és az eredmények visszaadása azonnal megtörténik.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics a programozási élmény különböző szintjeihez

Elkezdheti használni a Text Analytics API a folyamatokban, még akkor is, ha nem sok tapasztalattal rendelkezik a programozásban. Ezekkel az oktatóanyagokkal megtudhatja, hogyan használhatja az API-t a szöveg különböző módokon történő elemzéséhez a felhasználói élmény szintjéhez. 

* Minimális programozás szükséges:
    * [Adatok kinyerése az Excelben a Text Analytics és a Power automatizálás használatával](tutorials/extract-excel-information.md)
    * [A Text Analytics API és az MS flow használatával azonosíthatja a Yammer-csoportba tartozó megjegyzések hangulatát](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Power BI integrálása a Text Analytics APIekkel az ügyfelek visszajelzésének elemzéséhez](tutorials/tutorial-power-bi-key-phrases.md)
* Ajánlott programozási élmény:
    * [Streamelési adatok hangulatelemzése az Azure Databricks használatával](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Hozzon létre egy lombik-alkalmazást a szöveg fordításához, a hangulat elemzéséhez és a beszédfelismerés hangszintéziséhez](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. A tartalomhoz [a Text Analytics API támogatott nyelveket](./language-support.md) tekintheti meg.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. További információért tekintse meg az [adatokra vonatkozó korlátozásokat](concepts/data-limits.md) ismertető cikket.

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) a karakterek számának beolvasására használja, ugyanazt a módszert használja az adatméret mérésére.

## <a name="next-steps"></a>Következő lépések

+ [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) az Text Analytics számára az alkalmazások kulcsának és végpontjának beszerzéséhez.

+ A rövid útmutató segítségével megkezdheti [az API-hívások](quickstarts/client-libraries-rest-api.md) küldését. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással.

+ Az új kiadásokkal és szolgáltatásokkal kapcsolatos információkért tekintse [meg a Text Analytics API újdonságait](whats-new.md) .

+ A Azure Databricks használatával mélyebben kihasználhatja ezt az [érzelmi elemzést ismertető oktatóanyagot](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) .

+ Tekintse meg a blogbejegyzések listáját, valamint további videókat arról, hogyan használhatja a Text Analytics APIt a [külső & közösségi tartalom oldalán](text-analytics-resource-external-community.md)található egyéb eszközökkel és technológiákkal.
