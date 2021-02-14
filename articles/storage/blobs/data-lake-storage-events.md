---
title: 'Oktatóanyag: a Azure Databricks különbözeti táblázat frissítéséhez a adattó rögzítési mintájának implementálása | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhat egy Event Grid-előfizetést, egy Azure-függvényt és egy Azure Databricks feladatot az adatok sorainak az Azure DataLake Storage Gen2 tárolt táblába való beszúrásához.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516396"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Oktatóanyag: a Databricks-különbözeti tábla frissítéséhez a adattó rögzítési mintájának megvalósítása

Ez az oktatóanyag bemutatja, hogyan kezelheti az eseményeket egy hierarchikus névteret tartalmazó Storage-fiókban.

Egy kis megoldást fog kiépíteni, amely lehetővé teszi, hogy a felhasználó feltöltse egy Databricks-különbözeti táblázatot egy olyan vesszővel tagolt (CSV) fájl feltöltésével, amely leírja az értékesítési sorrendet. Ezt a megoldást egy Event Grid-előfizetés, egy Azure-függvény és egy Azure Databricks- [feladat](/azure/databricks/jobs) összekapcsolásával fogja felépíteni.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Hozzon létre egy Event Grid-előfizetést, amely meghív egy Azure-függvényt.
> * Hozzon létre egy Azure-függvényt, amely értesítést kap egy eseményről, majd Azure Databricks futtatja a feladatot.
> * Hozzon létre egy Databricks-feladatot, amely beszúr egy vevői rendelést egy Databricks-különbözeti táblába, amely a Storage-fiókban található.

Ezt a megoldást fordított sorrendben fogjuk felépíteni, kezdve a Azure Databricks munkaterülettel.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Hozzon létre egy hierarchikus névteret (Azure Data Lake Storage Gen2) tartalmazó Storage-fiókot. Ez az oktatóanyag egy nevű Storage-fiókot használ `contosoorders` . Győződjön meg arról, hogy a felhasználói fiókja rendelkezik a [Storage blob adatközreműködői szerepkörhöz](../common/storage-auth-aad-rbac-portal.md) hozzárendelve.

   Lásd: [Azure Data Lake Storage Gen2 használatával használandó Storage-fiók létrehozása](create-data-lake-storage-account.md).

* Egyszerű szolgáltatásnév létrehozása. [Útmutató: a portál használatával létrehozhat egy Azure ad-alkalmazást és egy egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md).

  A cikk lépéseinek elvégzése során néhány konkrét dolgot is el kell végeznie.

  : heavy_check_mark: Ha az [alkalmazás társítása szerepkörhöz](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) című szakasz lépéseit hajtja végre, akkor ügyeljen arra, hogy a szolgáltatáshoz hozzárendelje a **tárolási blob adatközreműködői** szerepkört.

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a szerepkört a Data Lake Storage Gen2 Storage-fiók hatókörében rendelje hozzá. Hozzárendelhet egy szerepkört a szülő erőforráscsoporthoz vagy az előfizetéshez, de az engedélyekkel kapcsolatos hibákat addig kapja, amíg a szerepkör-hozzárendelések el nem terjednek a Storage-fiókba.

  : heavy_check_mark: a cikk [beolvasási értékek beolvasása](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) szakaszában szereplő lépések végrehajtásakor illessze be a bérlői azonosítót, az alkalmazás azonosítóját és a jelszó értékét egy szövegfájlba. Ezekre az értékekre később szüksége lesz.

## <a name="create-a-sales-order"></a>Értékesítési rendelés létrehozása

Először hozzon létre egy CSV-fájlt, amely leírja az értékesítési sorrendet, majd töltse fel a fájlt a Storage-fiókba. Később a fájl adatait használva feltöltheti a Databricks-különbözeti táblázat első sorát.

1. Nyissa meg Azure Storage Explorer. Ezután navigáljon a Storage-fiókjához, és a **blob-tárolók** szakaszban hozzon létre egy **új tárolót.**

   ![adatmappa](./media/data-lake-storage-events/data-container.png "adatmappa")

   További információ a Storage Explorer használatáról: [Azure Storage Explorer használata az adatok Azure Data Lake Storage Gen2 fiókban való kezeléséhez](data-lake-storage-explorer.md).

2. Az **adattárolóban** hozzon létre egy **bemenet** nevű mappát.

3. Illessze be a következő szöveget egy szövegszerkesztőbe.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Mentse ezt a fájlt a helyi számítógépre, és adjon neki nevet **data.csvnak**.

5. A Storage Explorerban töltse fel ezt a fájlt a **bemeneti** mappába.  

## <a name="create-a-job-in-azure-databricks"></a>Feladatok létrehozása Azure Databricks

Ebben a szakaszban a következő feladatokat hajtja végre:

* Hozzon létre egy Azure Databricks munkaterületet.
* Hozzon létre egy notebookot.
* Databricks-különbözeti tábla létrehozása és feltöltése.
* Olyan kód hozzáadása, amely beszúrja a sorokat a Databricks különbözeti táblába.
* Hozzon létre egy feladatot.

### <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Azure Databricks** lehetőséget.

    ![Databricks Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks Azure Portal")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-events/new-databricks-service.png "Azure Databricks-munkaterület létrehozása")

    A munkaterület létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a felső folyamatjelző sávot.

### <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. A [Azure Portal](https://portal.azure.com)lépjen a létrehozott Azure Databricks munkaterületre, majd válassza a **munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **új**  >  **fürt** lehetőséget.

    ![Databricks az Azure-ban](./media/data-lake-storage-events/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](/azure/databricks/clusters/create) ismertető szakaszban talál.

### <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

1. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Python** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![A jegyzetfüzet létrehozása párbeszédpanelt és a Python nyelvként való kiválasztásának helyét bemutató képernyőkép.](./media/data-lake-storage-events/new-databricks-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Válassza a **Létrehozás** lehetőséget.

### <a name="create-and-populate-a-databricks-delta-table"></a>Databricks-különbözeti tábla létrehozása és feltöltése

1. A létrehozott jegyzetfüzetben másolja és illessze be az alábbi kódrészletet az első cellába, de még ne futtassa ezt a kódot.  

   Cserélje le a `appId` , `password` , `tenant` helyőrző értékeit a kód blokkban az oktatóanyag előfeltételeinek teljesítése során összegyűjtött értékekre.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Ez a kód létrehoz egy **source_file** nevű widgetet. Később létrehoz egy Azure-függvényt, amely meghívja ezt a kódot, és átadja egy fájl elérési útját az adott widgetnek.  Ez a kód is hitelesíti a szolgáltatásnevet a Storage-fiókkal, és létrehoz néhány változót, amelyet más cellákban fog használni.

    > [!NOTE]
    > Éles környezetben érdemes megfontolni a hitelesítési kulcs tárolását Azure Databricks-ben. Ezután adjon hozzá egy megkeresési kulcsot a kódjához a hitelesítési kulcs helyett. <br><br>Például a következő kódrészlet használata helyett: `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` , az alábbi kódrészletet fogja használni: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` . <br><br>Az oktatóanyag elvégzése után tekintse meg a Azure Databricks webhelyén található [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) cikket, ahol megtekintheti a megközelítés példáit.

2. Nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

3. Másolja és illessze be az alábbi kódrészletet egy másik cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához ebben a blokkban.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Ez a kód létrehozza a Databricks különbözeti táblát a Storage-fiókban, majd betölti a korábban feltöltött csv-fájlból a kezdeti adatok betöltését.

4. A kód sikeres futtatása után távolítsa el ezt a kódot a jegyzetfüzetből.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Olyan kód hozzáadása, amely beszúrja a sorokat a Databricks különbözeti táblába

1. Másolja és illessze be az alábbi kódrészletet egy másik cellába, de ne futtassa ezt a cellát.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Ez a kód egy csv-fájlból származó adatok használatával szúr be adatait egy ideiglenes táblázatos nézetbe. A CSV-fájl elérési útja a korábbi lépésben létrehozott input widgetből származik.

2. Adja hozzá a következő kódot az ideiglenes tábla nézet tartalmának egyesítéséhez a Databricks különbözeti táblával.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Feladatok létrehozása

Hozzon létre egy feladatot, amely a korábban létrehozott jegyzetfüzetet futtatja. Később létre fog hozni egy Azure-függvényt, amely egy esemény bekövetkezésekor futtatja ezt a feladatot.

1. Kattintson a **feladatok** lehetőségre.

2. A **feladatok** lapon kattintson a **feladat létrehozása** elemre.

3. Adjon nevet a feladatoknak, majd válassza ki a `upsert-order-data` munkafüzetet.

   ![Feladat létrehozása](./media/data-lake-storage-events/create-spark-job.png "Feladat létrehozása")

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Hozzon létre egy Azure-függvényt, amely futtatja a feladatot.

1. A Databricks munkaterület felső sarkában válassza a személyek ikont, majd válassza a **felhasználói beállítások** lehetőséget.

   ![Fiók kezelése](./media/data-lake-storage-events/generate-token.png "Felhasználói beállítások")

2. Kattintson az **új jogkivonat előállítása** gombra, majd kattintson a **Létrehozás** gombra.

   Ügyeljen arra, hogy a tokent biztonságos helyre másolja. Az Azure-függvénynek szüksége van erre a tokenre a Databricks való hitelesítéshez, hogy az képes legyen futtatni a feladatot.
  
3. Válassza a Azure Portal bal felső sarkában található **erőforrás létrehozása** gombot, majd válassza a **számítási > függvényalkalmazás** lehetőséget.

   ![Azure-függvény létrehozása](./media/data-lake-storage-events/function-app-create-flow.png "Azure-függvény létrehozása")

4. A függvényalkalmazás **Létrehozás** lapján győződjön meg arról, hogy a futásidejű veremhöz a **.net Core** elemet választja, és konfigurálja a Application Insights-példányt.

   ![A függvényalkalmazás konfigurálása](./media/data-lake-storage-events/new-function-app.png "A függvényalkalmazás konfigurálása")

5. A függvényalkalmazás **Áttekintés** lapján kattintson a **konfiguráció** elemre.

   ![Képernyőkép, amely kiemeli a konfigurált funkciók területen található konfigurációs lehetőséget.](./media/data-lake-storage-events/configure-function-app.png "A függvényalkalmazás konfigurálása")

6. Az **Alkalmazásbeállítások** lapon válassza az **új alkalmazás beállítása** gombot az egyes beállítások hozzáadásához.

   ![Konfigurációs beállítás hozzáadása](./media/data-lake-storage-events/add-application-setting.png "Konfigurációs beállítás hozzáadása")

   Adja hozzá a következő beállításokat:

   |Beállítás neve | Érték |
   |----|----|
   |**DBX_INSTANCE**| A databricks-munkaterület régiója. Például: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| A korábban létrehozott személyes hozzáférési jogkivonat. |
   |**DBX_JOB_ID**|A futó feladathoz tartozó azonosító. Ebben az esetben ez az érték a következő: `1` .|
7. A Function app áttekintés lapján kattintson az **új függvény** gombra.

   ![Új függvény](./media/data-lake-storage-events/new-function.png "Új függvény")

8. Válassza ki **Azure Event Grid triggert**.

   Ha a rendszer kéri, telepítse a **Microsoft. Azure. webjobs. Extensions. EventGrid** kiterjesztést. Ha telepítenie kell, a függvény létrehozásához ki kell választania **Azure Event Grid triggert** .

   Megjelenik az **új függvény** panel.

9. Az **új függvény** ablaktáblán nevezze el a függvény **UpsertOrder**, majd kattintson a **Létrehozás** gombra.

10. Cserélje le a kód tartalmát a kódnak megfelelően, majd kattintson a **Save (Mentés** ) gombra:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Ez a kód elemzi a kiváltott tárolási esemény adatait, majd egy, az eseményt kiváltó fájl URL-címét tartalmazó kérelem üzenetet hoz létre. Az üzenet részeként a függvény átadja a korábban létrehozott **source_file** widget értékét. a függvény kódja elküldi az üzenetet a Databricks feladatnak, és a korábban hitelesítéssel beszerzett jogkivonatot használja.

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Ebben a szakaszban olyan Event Grid-előfizetést hoz létre, amely meghívja az Azure-függvényt, amikor fájlokat töltenek fel a Storage-fiókba.

1. A függvény kódja lapon kattintson az **Event Grid előfizetés hozzáadása** gombra.

   ![Képernyőkép, amely kiemeli az Event Grid előfizetés hozzáadása gombot.](./media/data-lake-storage-events/new-event-subscription.png "Új esemény-előfizetés")

2. Az **esemény-előfizetés létrehozása** lapon nevezze el az előfizetést, majd használja a lapon található mezőket a Storage-fiók kiválasztásához.

   ![Új esemény-előfizetés](./media/data-lake-storage-events/new-event-subscription-2.png "Új esemény-előfizetés")

3. A **szűrő az események típusai** legördülő listából válassza ki a **létrehozott blobot**, és a **blob által törölt** eseményeket, majd kattintson a **Létrehozás** gombra.

## <a name="test-the-event-grid-subscription"></a>A Event Grid előfizetés tesztelése

1. Hozzon létre egy nevű fájlt `customer-order.csv` , illessze be a következő adatokat a fájlba, majd mentse a helyi számítógépre.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. A Storage Explorerban töltse fel ezt a fájlt a Storage-fiókja **bemeneti** mappájába.

   A fájlok feltöltése a **Microsoft. Storage. BlobCreated** eseményt emeli ki. Event Grid értesíti az adott eseményhez tartozó összes előfizetőt. Ebben az esetben az Azure-függvény az egyetlen előfizető. Az Azure-függvény elemzi az esemény paramétereit annak meghatározásához, hogy melyik esemény történt. Ezután átadja a fájl URL-címét a Databricks feladatoknak. A Databricks-feladatsor beolvassa a fájlt, és hozzáadja a Databricks különbözeti táblához, amely a Storage-fiókban található.

3. A feladat sikerességének vizsgálatához nyissa meg a databricks-munkaterületet, kattintson a **feladatok** gombra, majd nyissa meg a feladatot.

4. Válassza ki a feladatot a feladatok oldal megnyitásához.

   ![Spark-feladatok](./media/data-lake-storage-events/spark-job.png "Spark-feladatok")

   Amikor a feladatok befejeződik, megjelenik egy befejezési állapot.

   ![A feladatot sikerült befejezni](./media/data-lake-storage-events/spark-job-completed.png "A feladatot sikerült befejezni")

5. Egy új munkafüzet cellában futtassa ezt a lekérdezést egy cellában a frissített különbözeti táblázat megjelenítéséhez.

   ```
   %sql select * from customer_data
   ```

   A visszaadott tábla a legújabb rekordot jeleníti meg.

   ![A legfrissebb rekord megjelenik a táblában](./media/data-lake-storage-events/final_query.png "A legfrissebb rekord megjelenik a táblában")

6. A rekord frissítéséhez hozzon létre egy nevű fájlt `customer-order-update.csv` , illessze be a következő adatokat a fájlba, majd mentse a helyi számítógépre.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Ez a CSV-fájl majdnem azonos az előzővel, kivéve, hogy a megrendelés mennyisége a `228` -ból értékre módosult `22` .

7. A Storage Explorerban töltse fel ezt a fájlt a Storage-fiókja **bemeneti** mappájába.

8. Futtassa `select` újra a lekérdezést a frissített különbözeti táblázat megtekintéséhez.

   ```
   %sql select * from customer_data
   ```

   A visszaadott tábla a frissített rekordot jeleníti meg.

   ![A frissített rekord megjelenik a táblában](./media/data-lake-storage-events/final_query-2.png "A frissített rekord megjelenik a táblában")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a Storage-fiókhoz tartozó erőforráscsoportot, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)