---
title: Valós idejű IoT-adatfolyamok feldolgozása Azure Stream Analytics
description: IoT-érzékelőcímkék és -adatfolyamok streamelemzéssel és valós idejű adatfeldolgozással
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012682"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Valós idejű IoT-adatfolyamok feldolgozása Azure Stream Analytics

Ebből a cikkből megtudhatja, hogyan hozhat létre adatfolyam-feldolgozási logikát az adatok összegyűjtéséhez eszközök internetes hálózata (IoT) eszközökről. A valós eszközök internetes hálózata (IoT) használati esettel bemutatjuk, hogyan hozhat létre gyorsan és gazdaságosan a megoldást.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy ingyenes [Azure-előfizetést](https://azure.microsoft.com/pricing/free-trial/).
* Mintául szolgáló lekérdezési és adatfájlok letöltése a [githubról](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Eset

A Contoso egy vállalat az ipari automatizálás területén, amely teljesen automatizálta a gyártási folyamatait. A gyár gépeinek érzékelői valós idejű adatstreamek létrehozására képesek. Ebben a forgatókönyvben a termelési szint egyik igazgatója valós idejű elemzéseket szeretne kapni az érzékelők adataiból, hogy mintákat keressen bennük, és ezek alapján tegyen további lépéseket. Az érzékelő adatainak használatával Stream Analytics lekérdezési nyelvet (SAQL) is használhat, így érdekes mintákat találhat a bejövő adatfolyamból.

Ebben a példában az adatok egy Texas Instruments Sensor címke eszközből jönnek létre. A hasznos adatok JSON formátumban vannak, és a következőképpen néznek ki:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

A valós forgatókönyvekben több száz ilyen érzékelő állíthat elő eseményeket streamként. Ideális esetben van egy átjáróeszköz, amely egy bizonyos kód futtatásával elküldi ezeket az eseményeket az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy az [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) részére. A Stream Analytics-feladatok felhasználják az Event Hubs eseményeit, és a streameken valós idejű lekérdezéseket futtatnak. Ezután elküldheti az eredményeket az egyik [támogatott kimenetre](stream-analytics-define-outputs.md).

A használat megkönnyítése érdekében ez a Kezdeti lépések útmutató valódi SensorTag eszközökről származó mintaadatfájlokat biztosít. Ezeken lekérdezéseket futtathat, és megtekintheti az eredményeiket. A következő útmutatókból megtudhatja, hogyan csatlakoztathatja a feladatait bemenetekhez és kimenetekhez, és hogyan helyezheti üzembe őket az Azure szolgáltatásban.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali navigációs menüből. Ezután válassza ki **stream Analytics feladatot** az **elemzésből**.
   
    ![Új Stream Analytics-feladat létrehozása](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Adjon meg egy egyedi feladat nevet, és ellenőrizze, hogy az előfizetés megfelelő a feladatához. Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt az előfizetésből.

1. Válassza ki a feladatokhoz tartozó helyet. Használja ugyanazt a helyet az erőforráscsoport és az összes erőforrás számára a feldolgozási sebesség növelése és a költségek csökkentése érdekében. A konfigurációk **elkészítése** után válassza a létrehozás lehetőséget.
   
    ![Új Stream Analytics-feladat létrehozásának részletei](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Stream Analytics-lekérdezés létrehozása
A következő lépés a feladatok létrehozása után egy lekérdezés írása. A mintaadatok lekérdezése a feladathoz való csatlakozás nélkül is tesztelhető.

Töltse le a [HelloWorldASA-InputStream.jst](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) a githubról. Ezután navigáljon a Azure Stream Analytics feladatokhoz a Azure Portal.

A bal oldali menüben válassza a **lekérdezés** lehetőséget a **feladatok topológiája** alatt. Ezután válassza a **minta bemenet feltöltése** lehetőséget. Töltse fel a `HelloWorldASA-InputStream.json` fájlt, majd kattintson **az OK gombra**.

![Stream Analytics irányítópult-lekérdezés csempe](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Figyelje meg, hogy az adatok előnézete automatikusan fel van töltve a **bemeneti előnézet** táblába.

![Minta bemeneti adatok előnézete](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Lekérdezés: Nyers adatok archiválása

A lekérdezések legegyszerűbb formája a továbbított lekérdezés, amely a kijelölt kimeneten archiválja az összes bemeneti adatot. Ez a lekérdezés egy új Azure Stream Analytics feladatokban kitöltött alapértelmezett lekérdezés.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Válassza a **lekérdezés tesztelése** lehetőséget, és tekintse meg az eredményeket a **teszt eredményei** táblázatban.

![Stream Analytics lekérdezés eredményeinek tesztelése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Lekérdezés: Adatok szűrése feltétel alapján

Próbálja meg egy feltétel alapján szűrni az eredményeket. Azt szeretnénk, hogy csak azok az események jelenjenek meg eredményként, amelyek az "sensora" kifejezésből származnak.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Illessze be a lekérdezést a Szerkesztőbe, és válassza a **lekérdezés tesztelése** lehetőséget az eredmények áttekintéséhez.

![Adatstream szűrése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Lekérdezés: Riasztás az üzleti munkafolyamat elindítására

Tegyük részletesebbé a lekérdezést. Az átlaghőmérsékletet minden érzékelőtípus esetében 30 másodperces időközönként szeretnénk mérni, és csak akkor szeretnénk eredményeket megjeleníteni, ha az átlaghőmérséklet meghaladja a 100 fokot.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 másodperces szűrőlekérdezés](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Azokat az eredményeket kell megjelennie, amelyek csak 245 sort tartalmaznak, valamint az érzékelők nevét, ahol az átlagos mérsékelt érték nagyobb, mint 100. Ez a lekérdezés az események streamjét a **dspl** szerint csoportosítja, ami az érzékelő neve, az **Átfedésmentes ablak** pedig 30 másodperc. Az időbeli lekérdezéseknek meg kell jelölniük, hogyan szeretné elsajátítani az időt. A **timestamp by** záradék használatával megadta a **OUTPUTTIME** oszlopot, hogy az összes időbeli számításhoz társítsa az időpontokat. Részletes információkat az [Időkezelési](/stream-analytics-query/time-management-azure-stream-analytics) és az ablak-kezelési [függvények](/stream-analytics-query/windowing-azure-stream-analytics)című témakörben olvashat.

### <a name="query-detect-absence-of-events"></a>Lekérdezés: Események hiányának észlelése

Hogyan írható olyan lekérdezés, amely a bemeneti események hiányát keresi? Megtalálhatja az utolsó alkalommal, amikor egy érzékelő elküldte az adatküldést, majd a következő 5 másodpercre nem küldött eseményeket.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Események hiányának észlelése](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Itt egy **BAL OLDALI KÜLSŐ** illesztést alkalmazunk ugyanarra az adatstreamre (önillesztés). **BELSŐ** illesztés esetén csak akkor kapunk eredményt, ha van egyezés.  **BAL OLDALI KÜLSŐ** illesztés esetében azonban, ha az illesztés bal oldalán lévő eseményhez nincs egyezés, akkor a rendszer olyan oszlopot ad vissza, amely a jobb oldal összes oszlopában NULL értéket tartalmaz. Ez a módszer nagyon hasznos, ha események hiányára kíván rákeresni. További információ: [JOIN](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Összegzés

Ennek a cikknek a célja, hogy bemutassa, hogyan írhat különböző Stream Analytics lekérdezési nyelvi lekérdezéseket, és hogyan tekintheti meg az eredményeket a böngészőben. Ez azonban csak a kezdéshez szükséges. A Stream Analytics számos bemenetet és kimenetet támogat, és még az Azure Machine Learning függvényeinek előnyeit is ki tudja használni, ami az adatstreamek elemzésének hatékony eszközévé teszi. Ha szeretne többet megtudni arról, hogyan kell lekérdezéseket írni, olvassa el a [Gyakori lekérdezési minták](stream-analytics-stream-analytics-query-patterns.md) című cikket.