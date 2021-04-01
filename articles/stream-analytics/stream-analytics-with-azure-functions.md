---
title: Oktatóanyag – Azure Functions futtatása Azure Stream Analytics feladatokban
description: Ebben az oktatóanyagban megtanulhatja, hogy hogyan konfigurálhatja az Azure Functionst a Stream Analytics-feladatok kimeneti fogadójaként.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 74e09e61a6132858d716686bdb6687bb670f0d33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879511"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Oktatóanyag: Azure Functions futtatása Azure Stream Analytics feladatokból 

Az Azure Functions az Azure Stream Analytics szolgáltatásból való futtatásához konfigurálja a Functionst a Stream Analytics-feladat kimeneti fogadóinak egyikeként. A Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amellyel az Azure-ban vagy külső szolgáltatásokban bekövetkező események által aktivált kódok implementálhatók. Az eseményindítókra való reagálás képessége a Stream Analytics-feladatok természetes kimenetévé teszi a Functionst.

A Stream Analytics HTTP-eseményindítókkal hívja meg a Functionst. A Functions kimeneti adaptere segítségével a felhasználók a Stream Analyticshez csatlakoztathatják a Functionst, és így az események a Stream Analytics-lekérdezések alapján aktiválhatók. 

> [!NOTE]
> A Azure Functions egy virtuális hálózaton (VNet) belüli, egy több-bérlős fürtön futó Stream Analytics feladatokból való kapcsolódás nem támogatott.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Stream Analytics-feladatok létrehozása és futtatása
> * Azure cache létrehozása a Redis-példányhoz
> * Azure-függvény létrehozása
> * Az Azure cache Redis keresése az eredményekhez

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Stream Analytics-feladat konfigurálása függvény futtatására 

Ez a szakasz azt mutatja be, hogyan konfigurálható egy Stream Analytics feladat olyan függvény futtatásához, amely az Azure cache-be írja az Redis. A Stream Analytics-feladat eseményeket olvas be az Azure Event Hubsból, és futtat egy lekérdezést, amely meghívja a függvényt. Ez a függvény beolvassa az adatokat a Stream Analytics feladatból, és az Azure cache-be írja azt a Redis számára.

![Az Azure-szolgáltatások közötti kapcsolatokat bemutató ábra](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Stream Analytics-feladat létrehozása az Event Hubs szolgáltatást megadva bemenetként

[Az adathamisítások azonnali felismerése](stream-analytics-real-time-fraud-detection.md) oktatóanyag lépéseit követve hozzon létre egy eseményközpontot, indítsa el az eseménylétrehozó alkalmazást, és hozzon létre egy Stream Analytics-feladatot. Ugorja át a lekérdezés és a kimenet létrehozásához szükséges lépéseket. Ehelyett tekintse meg a következő részeket egy Azure Functions kimenet beállításához.

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz

1. Hozzon létre egy gyorsítótárat az Azure cache-ben a Redis számára a [gyorsítótár létrehozása](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)című témakörben ismertetett lépések segítségével.  

2. Miután létrehozta a gyorsítótárat, a **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Jegyezze fel az **Elsődleges kapcsolati sztring** értékét.

   ![Képernyőkép az Azure cache-ről a Redis-kapcsolatok karakterláncához](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Olyan függvény létrehozása Azure Functionsban, amely az Azure cache-be tud írni egy Redis

1. Tekintse meg a Functions dokumentációjának [Függvényalkalmazás létrehozása](../azure-functions/functions-get-started.md) című szakaszát. Ez a szakasz bemutatja, hogyan hozhat létre egy Function-alkalmazást és egy [http által aktivált függvényt Azure Functionsban](../azure-functions/functions-get-started.md)a csharp nyelvének használatával.  

2. Keresse meg a **run.csx** függvényt. Frissítse az alábbi kóddal. Cserélje le a **" \<your Azure Cache for Redis connection string goes here\> "** karakterláncot az Azure cache Redis az előző szakaszban lekért elsődleges kapcsolatok karakterláncára. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Ha a Stream Analytics a „HTTP Request Entity Too Large” (HTTP-kérelemegység túl nagy) kivételt kapja vissza a függvénytől, csökkenti a Functionsbe küldött kötegek méretét. A következő kód biztosítja, hogy Stream Analytics ne küldjön túlméretes kötegeket. Ellenőrizze, hogy a függvényben használt maximális kötegszám- és kötegméretértékek egyeznek a Stream Analytics-portálon megadott értékekkel.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Hozzon létre a **project.json** nevű JSON-fájlt egy tetszőleges szövegszerkesztőben. Illessze be a következő kódot, és mentse a helyi számítógépen. Ez a fájl tartalmazza a C#-függvény által igényelt NuGet-csomagfüggőségeket.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Lépjen vissza az Azure Portalra. A **Platformfunkciók** lapon keresse meg a függvényt. A **Fejlesztési eszközök** részen válassza az **App Service Editor** elemet. 
 
   ![Képernyőfelvétel: a platform szolgáltatásai lapot jeleníti meg App Service Editor kiválasztva.](./media/stream-analytics-with-azure-functions/image3.png)

5. Az App Service Editorban kattintson a jobb gombbal a gyökérkönyvtárba, és töltse fel a **project.json** fájlt. Ha a feltöltés sikeres, frissítse az oldalt. Meg kell jelennie egy automatikusan létrehozott fájlnak **project.lock.json** néven. Az automatikusan létrehozott fájl a project.json fájlban megadott .dll fájlokra mutató hivatkozásokat tartalmaz.  

   ![A képernyőképen a menüből kiválasztott fájlok feltöltése látható.](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>A Stream Analytics-feladat frissítése a kimenetként megadott függvénnyel

1. Nyissa meg a Stream Analytics-feladatot az Azure Portalon.  

2. Keresse meg a függvényt, és válassza az **Áttekintés**  >  **kimenetek**  >  **Hozzáadás** lehetőséget. Új kimenet hozzáadásához válassza az **Azure-függvény** lehetőséget a fogadó beállításánál. A functions kimeneti adapter a következő tulajdonságokkal rendelkezik:  

   |**Tulajdonság neve**|**Leírás**|
   |---|---|
   |Kimeneti alias| A feladat lekérdezésében a kimenetre való hivatkozáshoz használt rövid név. |
   |Importálási beállítás| A függvényt használhatja az aktuális előfizetésből, vagy megadhatja manuálisan a beállításokat, ha a függvény egy másik előfizetésben található. |
   |Függvényalkalmazás| A Functions-alkalmazás neve. |
   |Függvény| A függvény neve a Functions-alkalmazásban (a run.csx függvény neve).|
   |Maximális kötegméret|Beállítja az egyes kimeneti kötegek maximális méretét, amelyet a függvénynek bájtban kell elküldeni. Alapértelmezés szerint ez az érték 262 144 bájt (256 KB).|
   |Maximális kötegszám|A függvénybe küldött egyes kötegekben lévő események maximális számának megadására szolgál. Az alapértelmezett érték 100. Ez a tulajdonság nem kötelező.|
   |Kulcs|Más előfizetésből származó függvény használatát teszi lehetővé. A függvény eléréséhez adja meg a kulcs értékét. Ez a tulajdonság nem kötelező.|

3. Adja meg a kimeneti alias nevét. Ebben az oktatóanyagban a neve **saop1**, de bármilyen nevet használhat. Adja meg az egyéb adatokat.

4. Nyissa meg a Stream Analytics-feladatot, és frissítse a lekérdezést az alábbiakra. Ha nem a kimeneti fogadó **saop1** nevet adta, ne felejtse el módosítani a lekérdezésben.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Indítsa el a telcodatagen.exe alkalmazást a parancssorban a következő parancs futtatásával. A parancs a formátumot használja `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` .  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Indítsa el a Stream Analytics-feladatot.

## <a name="check-azure-cache-for-redis-for-results"></a>Az Azure cache Redis keresése az eredményekhez

1. Keresse meg a Azure Portal, és keresse meg az Azure cache-t a Redis. Válassza a **Konzol** lehetőséget.  

2. Használja az [Azure cache-t a Redis parancsaihoz](https://redis.io/commands) annak ellenőrzéséhez, hogy az adatai a Redis Azure cache-ben vannak-e. (A parancs a Get {Key} formátumot veszi át.) Például:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Ennek a parancsnak a megadott kulcshoz tartozó értéket kell kiírnia:

   ![Az Azure cache Redis kimenetének képernyőképe](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Ha hiba történik az események Azure Functionsba való küldésekor, Stream Analytics újrapróbálkozik a legtöbb művelettel. A rendszer minden http-kivételt újrapróbálkozik, amíg a 413-es HTTP-hiba (az entitás túl nagy) kivételével sikertelen lesz. Az entitás túl nagy hibája olyan Adathiba, amely az [újrapróbálkozási vagy a eldobási szabályzat](stream-analytics-output-error-policy.md)hatálya alá esik.

> [!NOTE]
> A Stream Analytics és Azure Functions közötti HTTP-kérések időtúllépése 100 másodpercre van állítva. Ha a Azure Functions-alkalmazás több mint 100 másodpercet vesz igénybe egy köteg feldolgozásához, Stream Analytics a hibák, és a köteg rety válik.

Az időtúllépések újrapróbálása ismétlődő eseményeket eredményezhet a kimeneti fogadóba. Ha Stream Analytics újrapróbálkozik egy sikertelen kötegtel, a kötegben lévő összes esemény újrapróbálkozik. Vegyünk például egy 20 eseményből álló köteget, amelyet a rendszer a Stream Analytics Azure Functions eljuttat. Tegyük fel, hogy a köteg első 10 eseményének feldolgozásához a Azure Functions 100 másodpercet vesz igénybe. Az 100 másodperces átadást követően Stream Analytics felfüggeszti a kérelmet, mert nem kapott pozitív választ a Azure Functionstól, és egy másik kérelmet küld ugyanarra a kötegre. A köteg első 10 eseményét Azure Functions újra feldolgozzák, ami ismétlődést okoz. 

## <a name="known-issues"></a>Ismert problémák

Amikor az Azure Portalon megpróbálja üres (alapértelmezett) értékre visszaállítani a Maximális kötegméret/Maximális kötegszám értékét, mentéskor az érték visszavált a korábban megadott értékre. Ebben az esetben manuálisan adja meg az alapértelmezett értékeket ezekben a mezőkben.

Az Stream Analytics jelenleg nem támogatja a [http-útválasztás](/sandbox/functions-recipes/routes?tabs=csharp) használatát a Azure Functionson.

A virtuális hálózatban üzemeltetett Azure Functionshoz való kapcsolódás támogatása nincs engedélyezve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az útmutatóban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy egyszerű Stream Analytics feladatot, amely egy Azure-függvényt futtat. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Felhasználói JavaScript-függvények futtatása Stream Analytics-feladatokban](stream-analytics-javascript-user-defined-functions.md)
