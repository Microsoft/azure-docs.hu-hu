---
title: Azure-beli ipari IoT-adatgyűjtési ADX
description: Ebből az oktatóanyagból megtudhatja, hogyan hívhat le IIoT-információkat a ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787697"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Oktatóanyag: az Azure Industrial IoT-beli ADX

Az Azure Industrial IoT (IIoT) platform számos Azure Pásti-szolgáltatással ötvözi az Edge-modulokat és a Cloud Service-szolgáltatásokat, így biztosítva az ipari eszközök felderítésének képességeit, és adatokat gyűjthet az eszközökről az OPC UA használatával. Az [Azure adatkezelő (ADX)](https://docs.microsoft.com/azure/data-explorer) az adatelemzési funkciókkal rendelkező IIoT-adatok természetes célja, amely lehetővé teszi, hogy rugalmas lekérdezéseket futtasson az OPC-közzétevőn keresztül a IoT hubhoz kapcsolódó OPC ua-kiszolgálókról. Bár a ADX-fürtök közvetlenül a IoT Hub tudják beolvasni az adatmennyiséget, a IIoT-platform az adatok további feldolgozását végzi, hogy az az Event hub-on való üzembe helyezése előtt még hasznosabb legyen (lásd a IIoT platform architektúráját).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tábla létrehozása a ADX-ben
> * Az Event hub összekötése a ADX-fürttel
> * A ADX-ben tárolt adatelemzés

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>A ADX-fürtben elérhetővé teheti az adatfeldolgozást a hatékony lekérdezés érdekében 

Ha az Event hub üzenet formátumát tekintjük meg (a Microsoft. Azure. IIoT. OpcUa. előfizető. models. MonitoredItemMessageModel osztály által meghatározottak szerint), láthatunk egy mutatót arra a szerkezetre, amelyre a ADX tábla sémájának szüksége van.

![Struktúra](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Az alábbiakban azokat a lépéseket mutatjuk be, amelyeket a ADX-fürtön elérhetővé kell tenni, és az adatlekérdezéseket hatékonyan le kell kérdezni.  
1. Hozzon létre egy ADX-fürtöt. Ha még nem rendelkezik a IIoT platformmal kiépített ADX-fürttel, vagy ha másik fürtöt szeretne használni, kövesse az [alábbi lépéseket.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
2. Az [itt](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)leírtak szerint engedélyezze a folyamatos átvitelt a ADX-fürtön. 
3. Hozzon létre egy ADX-adatbázist az [itt](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)leírt lépéseket követve.

A következő lépésben a [ADX webes felületét](https://docs.microsoft.com/azure/data-explorer/web-query-data) fogjuk használni a szükséges lekérdezések futtatásához. Győződjön meg arról, hogy a hivatkozást a hivatkozásban leírt módon adja hozzá a fürthöz a webes felületen.  
 
4. Hozzon létre egy táblázatot a ADX, hogy a betöltött adatmennyiséget a alkalmazásban helyezze el.  Bár a MonitoredItemMessageModel osztály használható a ADX tábla sémájának definiálásához, először egy [dinamikus](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)típusú oszlopot tartalmazó előkészítési táblába ajánlott bevenni az adatot. Ez nagyobb rugalmasságot biztosít az adatkezelésben és a feldolgozásban más táblákba (más adatforrásokkal együtt), amelyek több felhasználási esetet is igénybe vehetik. A következő ADX-lekérdezés a (z) "iiot_stage" előkészítési táblát hozza létre egy "hasznos adat" oszlopával.

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Emellett hozzá kell adnia egy JSON-betöltési leképezést, amely arra utasítja a fürtöt, hogy az Event hub teljes JSON-üzenetét előkészítési táblába helyezze.

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. A táblázat most már készen áll az Event hub adatainak fogadására. 
6. Az [alábbi](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) útmutatást követve csatlakoztathatja az Event hub-t a ADX-fürthöz, és megkezdheti az adatfeldolgozást az előkészítési táblába. Csak akkor kell létrehozni a kapcsolódást, mert már van egy IIoT platform által kiépített Event hub.  
7. A kapcsolódás ellenőrzése után az adatforgalom a táblázatba kerül, és rövid időn belül elkezdjük megvizsgálni a táblázatban lévő adatelemzést. A ADX webes felületén a következő lekérdezéssel tekintse meg a 10 sorból álló adatmintát. Itt láthatjuk, hogy az adattartalomban lévő adatok a korábban említett MonitoredItemMessageModel osztályhoz hasonlítanak.

![Lekérdezés](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Most futtasson elemzéseket ezen az adatokon, ha közvetlenül az adattartalom oszlopban lévő dinamikus adatokat elemzi. Ebben a példában a "DisplayName": "PositiveTrendData" által azonosított telemetria átlagát számítjuk fel 10 percen belül, az összes olyan rekord esetében, amely egy adott időpontnál (min_t) változóban van megadva, a min_t = datetime (2020-10-23) értékre. iiot_stage | ahol a ToDateTime (hasznos adat). Időbélyeg) > min_t | ahol a ToString (hasznos adat). DisplayName) = = ' PositiveTrendData ' | összefoglalás event_avg = AVG (ToDouble (hasznos adat). Value)) raktárhely alapján (ToDateTime (hasznos adat). Időbélyeg), 10 m)
 
Mivel a "hasznos adatok" oszlop dinamikus adattípust tartalmaz, a lekérdezés időpontjában végre kell hajtani az adatok átalakítását, hogy a számítások a megfelelő adattípusokon legyenek végrehajtva.

![Hasznos adatok időbélyege](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Ahogy korábban már említettük, az OPC UA-adatot egy "dinamikus" oszlopot tartalmazó előkészítési táblába bevittük, ami rugalmasságot biztosít számunkra. Azonban a lekérdezési időpontnál az adattípus-konverziók futtatásával késést okozhat a lekérdezések végrehajtása, különösen akkor, ha az adatmennyiség nagy, és ha sok egyidejű lekérdezés van. Ebben a szakaszban létrehozhatunk egy másik táblázatot a már meghatározott adattípusokkal, hogy elkerüljük a lekérdezési idő adattípusának konverzióját.
 
9. Hozzon létre egy új táblázatot az elemzett adatokhoz, amely az előkészítési táblában lévő dinamikus "hasznos adat" tartalmából korlátozott választást tartalmaz. Vegye figyelembe, hogy a telemetria várt adattípusokhoz létrehozott egy Value (érték) oszlopot.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Hozzon létre egy függvényt (az adatbázis szintjén) az előkészítési táblából a szükséges adatok kivetítéséhez. Itt az "adattartalom" oszlopból válassza ki a "Timestamp", a "PublisherId", a "DisplayName", az "adattípus" és a "NodeId" elemet, majd a projektből a következőt: "Tag_Timestamp", "Tag_PublisherId", "tag", "Tag_Datatype", "Tag_NodeId". Az "érték" elem három különböző részre van kiképezve az "adattípus" alapján.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Az adattípusok a ADX-ben való leképezésével kapcsolatos további információkért lásd [itt](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic), valamint a ADX függvényeit [itt kezdheti meg.](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions)
 
11. Alkalmazza az előző lépés függvényét az elemzett táblára egy frissítési házirend használatával. A frissítési [szabályzat](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) arra UTASÍTJA a ADX, hogy automatikusan fűzze hozzá az adatforrást egy céltábla számára, amikor új adatbevitelt szúrnak be a forrástáblaba a forrás táblába beszúrt Adatátalakítási lekérdezés alapján. A következő lekérdezéssel rendelhető hozzá az elemzett tábla célhelyként és a szakasz táblaként az előző lépésben létrehozott függvény által meghatározott frissítési szabályzat forrásaként.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Amint a fenti lekérdezés végrehajtása megtörtént, az adatok elindítják a (z) "iiot_parsed" céltábla táblázatát, és feltöltik azt. A "iiot_parsed a következőképpen" lehet megtekinteni az adathalmazt.

![Elemzett tábla](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Nézzük meg, hogyan lehet megismételni az elemzést, amelyet az előző lépésben tettünk elérhetővé; a "DisplayName": "PositiveTrendData" által azonosított telemetria átlagának kiszámítása 10 percen belül, a megadott időpontok óta betöltött összes rekord esetében (a min_t változó által meghatározottak szerint). Mivel most már az "PositveTrendData" címke értékei szerepelnek egy dupla adattípusú oszlopban, a lekérdezés teljesítményének javulását várjuk.

![Ismételt elemzés](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Tegyük fel, hogy mindkét esetben összehasonlítjuk a lekérdezési teljesítményt. A lekérdezés végrehajtásához szükséges időt a ADX felhasználói felületén található "stats" (amely a lekérdezési eredmények felett lehet) használatával érheti el.  

![Lekérdezési teljesítmény 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Lekérdezési teljesítmény 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Láthatjuk, hogy az elemzett táblázatot használó lekérdezés nagyjából kétszer olyan gyors, mint az előkészítési tábla. Ebben a példában egy kis adatkészlettel rendelkezünk, és nincsenek olyan egyidejű lekérdezések, amelyek a lekérdezés végrehajtási időpontjára hatással vannak, azonban a reális számítási feladatok esetében nagy hatással lehet a teljesítményre. Ezért fontos, hogy a különböző adattípusokat különböző oszlopokra lehessen elválasztani.

> [!NOTE] 
> A frissítési szabályzat csak az előkészítési táblába betöltött, a házirend beállítása után betöltött adatmennyiségen működik, és nem vonatkozik a már meglévő adatmennyiségekre. Ezt figyelembe kell venni, ha például módosítani kell a frissítési szabályzatot. A teljes részletek a ADX dokumentációjában találhatók.

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta, hogyan módosíthatja a konfiguráció alapértelmezett értékeit, 

> [!div class="nextstepaction"]
> [Ipari IoT-összetevők konfigurálása](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Az Time Series Insights használatával megjelenítheti és elemezheti az adataikat](tutorial-visualize-data-time-series-insights.md)