---
title: Gyakori használati esetek és forgatókönyvek Azure Cosmos DB
description: 'Ismerkedjen meg az első öt használati esettel Azure Cosmos DB: a felhasználó által generált tartalom, az eseménynaplózás, a katalógus adatai, a felhasználói beállítások adatai és a eszközök internetes hálózata (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 4aed956fc7052f844080b830aa4cc3b2a07235a1
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799394"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Az Azure Cosmos DB gyakori használati helyzetei
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk áttekintést nyújt Azure Cosmos DB számos gyakori használati esetéről.  Az ebben a cikkben szereplő javaslatok kiindulási pontként szolgálnak az alkalmazás Cosmos DB való fejlesztésekor.

A cikk elolvasása után a következő kérdésekre tud válaszolni: 

* Melyek a Azure Cosmos DB gyakori használati esetei?
* Milyen előnyökkel jár a Azure Cosmos DB használata a kereskedelmi alkalmazások esetében?
* Milyen előnyökkel jár a Azure Cosmos DB használata eszközök internetes hálózata (IoT) rendszerek adattárában?
* Milyen előnyökkel jár a webes és mobil alkalmazások Azure Cosmos DB használata?

## <a name="introduction"></a>Bevezetés

[Azure Cosmos db](../cosmos-db/introduction.md) a Microsoft gyors NoSQL-adatbázisa, amely bármilyen méretű nyitott API-val rendelkezik. A szolgáltatás úgy lett kialakítva, hogy az ügyfelek rugalmasan (és egymástól függetlenül) méretezhetik az átviteli sebességet és a tárterületet tetszőleges számú földrajzi régión belül. A Azure Cosmos DB az első globálisan elosztott adatbázis-szolgáltatás a piacon, amely átfogó [szolgáltatási szintű szerződéseket](https://azure.microsoft.com/support/legal/sla/cosmos-db/) kínál, beleértve az átviteli sebességet, a késést, a rendelkezésre állást és a konzisztenciát.

A Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis, amely számos alkalmazás és használati eset esetében használatos. Célszerű bármely olyan [kiszolgáló](https://azure.com/serverless) nélküli alkalmazáshoz, amelynél alacsony az ezredmásodperces válaszidő, és gyorsan és globálisan kell méretezni. Több adatmodellt (kulcs-érték, dokumentumok, gráfok és oszlopos) és számos API-t támogat az adathozzáféréshez, beleértve a [Azure Cosmos db API-ját a MongoDB, az](mongodb-introduction.md) [SQL API](./introduction.md)-t, a [Gremlin API](graph-introduction.md)-t és a [Table API](table-introduction.md) -t natív módon, és bővíthető módon. 

Az alábbiakban néhány olyan Azure Cosmos DB attribútumot ismertetünk, amelyek kiválóan alkalmasak a globális ambícióval rendelkező nagy teljesítményű alkalmazásokhoz.

* Azure Cosmos DB natív módon particionálja az adatait a magas rendelkezésre állás és a méretezhetőség érdekében. A Azure Cosmos DB 99,99%-os garanciát biztosít a rendelkezésre állásra, az átviteli sebességre, az alacsony késésre és az összes egyrégiós fiókra, valamint az összes többrégiós fiókra, amely nyugodt következetességgel rendelkezik, valamint a 99,999%-os olvasási rendelkezésre állást a több
* Azure Cosmos DB rendelkezik az SSD-alapú biztonsági mentéssel, és az alacsony késésű időkorlátot (ezredmásodperc) választ.
* Azure Cosmos DB támogatja a konzisztens, konzisztens előtagot, munkamenetet és határokat, így a teljes rugalmasságot és az alacsony költséghatékonyságot is lehetővé teszi. Egyetlen adatbázis-szolgáltatás sem biztosít olyan nagy rugalmasságot, mint a szintek konzisztenciája Azure Cosmos DB. 
* A Azure Cosmos DB rugalmas, adatkezelési díjszabási modellel rendelkezik, amely egymástól függetlenül, a tárolást és az átviteli sebességet is lehetővé teszi.
* Azure Cosmos DB fenntartott átviteli sebessége lehetővé teszi az olvasási/írási műveletek számát az alapul szolgáló hardver CPU/memória/IOPs helyett.
* A Azure Cosmos DB kialakítása lehetővé teszi a nagy mennyiségű kérelmekre való skálázást napi billió kérések sorrendjében.

Ezek az attribútumok hasznosak olyan webes, mobil-, játék-és IoT-alkalmazásokban, amelyeknek alacsony a válaszideje, és nagy mennyiségű olvasást és írást kell kezelniük.

## <a name="iot-and-telematics"></a>IoT és telematika

A IoT-használati esetek gyakran osztoznak bizonyos mintákban az adatfeldolgozásuk, feldolgozása és tárolása során.  Először is ezeknek a rendszereknek kell bevenniük a különböző területi beállítások eszköz-érzékelőkből származó adatmennyiséget. Ezután ezek a rendszerek a valós idejű elemzések elvégzése érdekében dolgozzák fel és elemzik a streaming-adatforrásokat. Az adatok ezután archiválva vannak a a Batch Analytics szolgáltatásban található hűtőházi tárolóba. A Microsoft Azure számos olyan szolgáltatást kínál, amely IoT-használati esetekre alkalmazható, például Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification hub, Azure Machine Learning, Azure HDInsight és Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Azure Cosmos DB IoT-hivatkozás architektúrája" border="false":::

Az Azure Event Hubs betöltheti az adatvesztést, mivel a nagy átviteli sebességű adatfeldolgozást biztosít kis késéssel. A valós idejű betekintésre feldolgozható, beolvasott adatmennyiséget a valós idejű elemzések Azure Stream Analyticséhez lehet tölcsérbe állítani. Az Azure Cosmos DB az ad hoc lekérdezésekhez tölthetők be. Az adatAzure Cosmos DBba való betöltését követően az adatlekérdezés készen áll. Emellett az új adatok és a meglévő adatok módosítása is olvasható a változási hírcsatornán. A Change feed egy állandó, csak hozzáfűzési napló, amely szekvenciális sorrendben tárolja a Cosmos-tárolók változásait. Ezután a valós idejű elemzések részeként a rendszer az összes adatmennyiséget vagy a Azure Cosmos DB lévő adatváltozásokat is használhatja hivatkozási adatként. Emellett az adatok tovább finomítható és feldolgozhatók, ha összekapcsolja Azure Cosmos DB adatok HDInsight a Pig, a kaptár vagy a Térkép/feladatok csökkentése érdekében.  A rendszer ezután visszahelyezi a finomított adatgyűjtést a jelentéskészítés Azure Cosmos DB.   

Azure Cosmos DB, EventHubs és Storm rendszerű minta IoT-megoldáshoz tekintse [meg a hdinsight-Storm-példákat tartalmazó tárházat a githubon](https://github.com/hdinsight/hdinsight-storm-examples/).

A IoT Azure-ajánlatokkal kapcsolatos további információkért lásd: [az eszközök internetének létrehozása](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Kereskedelmi és marketing
Azure Cosmos DB széles körben használható a Microsoft saját e-kereskedelmi platformján, amely a Windows áruházat és az XBox Live-t futtatja. A kiskereskedelmi iparágban is használatos a katalógus adatainak tárolására, valamint az események beszerzésére a feldolgozási folyamatok sorrendjében.

Az adatkatalógus adatfelhasználási forgatókönyvei olyan attribútumok halmazának tárolását és lekérdezését foglalják magukban, mint például a személyek, a helyek és a termékek. Néhány példa a Katalógus adataira: felhasználói fiókok, termékkatalógusok, IoT-eszközök és anyagjegyzék-rendszerek. Az ehhez az adathoz tartozó attribútumok változhatnak, és idővel változhatnak az alkalmazás követelményeinek megfelelően.

Vegyünk példaként egy katalógust egy autóipari alkatrész szállítójának. Minden rész rendelkezhet saját attribútumokkal az összes rész közös attribútumain kívül. Továbbá egy adott rész attribútumai a következő évben is megváltoztathatják az új modellek kiadását. A Azure Cosmos DB támogatja a rugalmas sémákat és a hierarchikus adatok használatát, így a termékkatalógus-adatok tárolására is alkalmas.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Azure Cosmos DB a kiskereskedelmi katalógus referenciájának architektúrája" border="false":::

A Azure Cosmos DB gyakran használják az események beszerzésére a Power Event vezérelt architektúrák esetében, a [módosítási hírcsatorna](change-feed.md) funkciójának használatával. A változási hírcsatorna lehetővé teszi, hogy az alárendelt szolgáltatások megbízhatóan és Növekményesen olvassák a lapkákat és a frissítéseket (például rendezési eseményeket) egy Azure Cosmos DB. Ez a funkció kihasználható úgy, hogy egy állandó eseménynaplót biztosítson az üzenetsor-kezelés állapotának megváltoztatására szolgáló események és a megrendelések feldolgozási munkafolyamata között (amely [kiszolgáló nélküli Azure Functionsként](https://azure.com/serverless)is megvalósítható).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Azure Cosmos DB rendelési folyamat-hivatkozási architektúra" border="false":::

Emellett a Azure Cosmos DBban tárolt adatok Apache Spark feladatokon keresztül integrálhatók a big data Analytics HDInsight is. A Azure Cosmos DB Spark-összekötőről további részleteket a [Spark-feladatok futtatása Cosmos db és HDInsight](spark-connector.md)című témakörben talál.

## <a name="gaming"></a>Játékok
Az adatbázis a játékalkalmazások kulcsfontosságú eleme. A modern játékok a grafikus feldolgozást a mobilos és a konzolos ügyfeleken végzik, de a felhőt használják a testreszabott és személyre szabott tartalmakhoz, például a játékon belüli statisztikákhoz, a közösségi média integrációjához és a ranglistákhoz. A játékok gyakran egyszeri, ezredmásodperces késleltetést igényelnek az olvasási és írási műveletekhez, így biztosítva a játékon belüli élményt. A játékadatbázisnak gyorsnak kell lennie, és képesnek kell lennie arra, hogy kezelni tudja a csúcsterheléseket például egy új játék indításakor vagy egy funkciófrissítés esetén.

A Azure Cosmos DBt olyan játékok használják, mint [a The Walking Dead: No Man 's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by [Next Games](https://www.nextgames.com/), és a [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB a játékok fejlesztői számára a következő előnyöket biztosítja:

* Azure Cosmos DB lehetővé teszi, hogy rugalmasan méretezhető legyen a teljesítmény. Ez lehetővé teszi a játékok számára, hogy egyetlen API-hívással kezelje a több tucat és több millió egyidejű játékos frissítési profilját és statisztikáit.
* Azure Cosmos DB támogatja az ezredmásodperces olvasási és írási műveleteket, hogy elkerülje a játék közbeni lemaradást.
* Azure Cosmos DB automatikus indexelése lehetővé teszi, hogy valós időben több különböző tulajdonságot lehessen szűrni, például megkeresi a játékosokat a belső lejátszó-azonosítóik, a GameCenter, a Facebook, a Google ID-k vagy a-lekérdezés alapján, a Guild játékos-tagsága alapján. Ez összetett indexelési vagy horizontális skálázási infrastruktúra kiépítése nélkül lehetséges.
* A közösségi funkciók, például a játékon belüli csevegések, a Player Guild-tagságok, a kihívások, a magas pontszámú ranglisták és a közösségi diagramok könnyebben valósíthatók meg rugalmas sémával.
* Azure Cosmos DB felügyelt platformként (Péter) a gyors iteráció és a piacra kerülési idő csökkentése érdekében minimális beállítási és felügyeleti munka szükséges.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Azure Cosmos DB játék-referenciák architektúrája" border="false":::

## <a name="web-and-mobile-applications"></a>Webes és mobilalkalmazások
Az Azure Cosmos DB-t gyakran használják webes és mobilalkalmazásokban is, mert kiválóan alkalmas a közösségi interakciók modellezésére, a harmadik féltől származó szolgáltatások integrációjára és sokoldalú személyre szabott élmények megvalósítására. A Cosmos DB SDK-k széles körű iOS-és Android-alkalmazásokat hozhatnak létre a népszerű [Xamarin keretrendszer](mobile-apps-with-xamarin.md)használatával.  

### <a name="social-applications"></a>Közösségi alkalmazások
A Azure Cosmos DB gyakori használati esete a felhasználók által generált tartalom (UGC) tárolása és lekérdezése webes, mobil és közösségi alkalmazásokhoz. Néhány példa a csevegési munkamenetek, a tweetek, a blogbejegyzések, a minősítések és a megjegyzések UGC. A közösségi UGC gyakran olyan ingyenes szöveg, tulajdonságok, címkék és kapcsolatok keveréke, amelyeket nem a merev struktúra kötött. Az olyan tartalmak, mint a csevegések, a megjegyzések és a hozzászólások Cosmos DB tárolhatók anélkül, hogy átalakításokat vagy összetett objektumokat kellene összehasonlítani a kapcsolódó leképezési rétegekkel.  Az Adattulajdonságok egyszerűen hozzáadhatók vagy módosíthatók úgy, hogy azok megfeleljenek a követelményeknek, mivel a fejlesztők megismétlik az alkalmazás kódját, így a gyors fejlesztést is segítik.  

A harmadik féltől származó közösségi hálózatokkal integrált alkalmazásoknak válaszolniuk kell a sémák változására ezekből a hálózatokból. Mivel az Cosmos DB alapértelmezés szerint automatikusan indexeli az adatgyűjtést, a rendszer bármikor lekérdezheti az adatlekérdezéseket. Ezért ezek az alkalmazások rugalmasan tudják beolvasni a kivetítéseket saját igényeik szerint.

Számos közösségi alkalmazás globális szinten fut, és nem kiszámítható használati mintákat mutathatnak. Az adattár méretezésének rugalmassága elengedhetetlen, mivel az alkalmazási réteg a használati igényeknek megfelelően méretezhető.  A Cosmos DB fiókban további adatpartíciók hozzáadásával felskálázást hajthat végre.  Emellett további Cosmos DB fiókokat is létrehozhat több régióban. Cosmos DB szolgáltatási régió elérhetőségét lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="A Azure Cosmos DB webalkalmazás-hivatkozási architektúrát bemutató diagram." border="false":::

### <a name="personalization"></a>Személyre szabás
Napjainkban a modern alkalmazások összetett nézetekkel és tapasztalatokkal rendelkeznek. Ezek jellemzően dinamikusak, a felhasználói preferenciák, a hangulatok és a márkaépítési igények kielégítésére szolgálnak. Ezért az alkalmazásoknak képesnek kell lenniük a személyre szabott beállítások hatékony beolvasására a felhasználói felületi elemek és a tapasztalatok gyors megjelenítéséhez. 

A JSON, a Cosmos DB által támogatott formátum érvényes formátum a felhasználói felületi elrendezési adattároláshoz, mivel az nem csupán egyszerű, hanem a JavaScript által is könnyen értelmezhető. Cosmos DB olyan hangolt konzisztencia-szinteket biztosít, amelyek lehetővé teszik az alacsony késésű írási műveletek gyors olvasását. Ezért a felhasználói felületi elrendezési és a személyre szabott beállítások, például a Cosmos DB JSON-dokumentumok tárolásának hatékony módja, ha ezeket az adatátvitelt a hálózaton keresztül szeretné lekérni.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Azure Cosmos DB webalkalmazás-referenciák architektúrája" border="false":::

## <a name="next-steps"></a>Következő lépések

* A Azure Cosmos DB használatának megkezdéséhez kövesse a [gyors üzembe](create-sql-api-dotnet.md)helyezést, amely végigvezeti a fiók létrehozásán és a Cosmos db használatának első lépésein.

* Ha többet szeretne megtudni a Azure Cosmos DBt használó ügyfelekről, tekintse meg az [ügyfél](https://azure.microsoft.com/case-studies/?service=cosmos-db) -esettanulmányok lapot.
