---
title: 'Oktatóanyag: űrlapos adatok tömeges kinyerése Azure Data Factory-űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Azure Data Factory tevékenységek beállításával aktiválhatja az űrlap-felismerő modellek betanítását és futtatását, és digitalizálhatja a dokumentumok nagy számát.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: d0c95312e1794e2f78bbbef217ef5530a993146d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040906"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Oktatóanyag: adatok tömeges kinyerése Azure Data Factory használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure-szolgáltatásokat egy nagy mennyiségű űrlap digitális médiába való betöltésére. Az oktatóanyag bemutatja, hogyan automatizálható a dokumentumok Azure-beli adatfeldolgozása az Azure SQL Database-be. Gyorsan betaníthatja a modelleket, és néhány kattintással feldolgozhatja az új dokumentumokat.

## <a name="business-need"></a>Üzleti igények

A legtöbb szervezet már ismeri a különböző formátumokban (PDF, képek, videók) tárolt adatmennyiséget. Az ajánlott eljárásokat és a legtöbb költséghatékony módszert keresik az eszközök digitalizálásához.

Emellett ügyfeleink gyakran különböző típusú űrlapokkal rendelkeznek, amelyek számos ügyfélből és ügyfélből származnak. A rövid útmutatóktól eltérően ez az oktatóanyag azt mutatja [be, hogyan](./quickstarts/client-library.md)lehet automatikusan betanítani egy modellt új és különböző típusú űrlapokkal a metaadatokon alapuló megközelítés használatával. Ha nem rendelkezik meglévő modellel a megadott típusú űrlaphoz, akkor a rendszer létrehoz egyet, és megadja a modell AZONOSÍTÓját. 

Az űrlapokból származó adatok kinyerése és a meglévő operatív rendszerek és adattárházak kombinálásával való összevonásával a vállalatok bepillantást nyerhetnek, és az ügyfelek és az üzleti felhasználók számára értékes eredményeket szerezhetnek.

Az Azure űrlap-felismerő szolgáltatás segít a szervezeteknek az adatok felhasználásában, a folyamatok automatizálásában (számlázás, adófizetési műveletek stb.), pénzt takaríthat meg és időt takaríthat meg, és jobb adatpontosságot érhet el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Data Lake beállítása az űrlapok tárolásához.
> * Parametrization-tábla létrehozásához használjon Azure-adatbázist.
> * A bizalmas hitelesítő adatok tárolásához használjon Azure Key Vault.
> * Az űrlap-felismerő modell betanítása Azure Databricks notebookon.
> * Kinyerheti az űrlapadatokat egy Databricks notebook használatával.
> * Automatizálja az űrlapok betanítását és kinyerését Azure Data Factory használatával.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/).
* Az Azure <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" -előfizetés létrehozása után hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az erőforrás üzembe helyezése után válassza az **Erőforrás megnyitása** lehetőséget.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához a létrehozott erőforrás kulcsára és végpontra lesz szüksége. Ebben a rövid útmutatóban később beillesztheti a kulcsot és a végpontot a kódjába.
    * A szolgáltatás kipróbálásához az ingyenes díjszabási szintet (F0) használhatja. Ezt követően később is frissítheti az éles környezetben futó fizetős csomagot.
* Legalább öt azonos típusú űrlap. Ideális esetben ez a munkafolyamat nagy méretű dokumentumok támogatását jelenti. A betanítási adatkészlet összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg [a képzési adatkészlet létrehozása](./build-training-data-set.md) című témakört. Ebben az oktatóanyagban a [minta adatkészlethez](https://go.microsoft.com/fwlink/?linkid=2128080)tartozó Train mappában található fájlokat használhatja.

## <a name="project-architecture"></a>Projekt architektúrája 

Ez a projekt Azure Data Factory folyamatokból áll, amelyek olyan Python-jegyzetfüzetek kiváltására szolgálnak, amelyek egy Azure Data Lake Storage-fiókban lévő dokumentumokból betanítják, elemzik és kinyerik az adatokból.

Az űrlap-felismerő REST APIhoz bemenetként paramétereket kell megadni. Biztonsági okokból ezek a paraméterek egy Azure Key vaultban lesznek tárolva. A más, kevésbé bizalmas paramétereket, például a Storage blob-mappájának nevét, egy Azure SQL Database-adatbázis egy paraméterezés-táblájában tárolja a rendszer.

Minden elemezni kívánt űrlap esetében adatmérnökök vagy adatszakértők töltik fel a paraméter táblázat egy sorát. Ezután a Azure Data Factory segítségével megismétlik az észlelt Űrlapbeállítások listáját, és átadhatják a releváns paramétereket egy Databricks-jegyzetfüzetbe az űrlap-felismerő modellek betanításához vagy újratanításához. Itt is használhatja az Azure-függvényeket.

A Azure Databricks jegyzetfüzet ezután a betanított modelleket használja az űrlapadatok kinyeréséhez. Ezt az adatexportálást egy Azure SQL Database-adatbázisba exportálja.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="A projekt architektúráját bemutató diagram.":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake beállítása

Előfordulhat, hogy az űrlapon várakozó fájlok a helyszíni környezetben vagy az FTP-kiszolgálón vannak. Ez az oktatóanyag egy Azure Data Lake Storage Gen2-fiók űrlapjait használja. A fájlokat Azure Data Factory, Azure Storage Explorer vagy AzCopy használatával is átviheti. A betanítási és pontozási adatkészletek különböző tárolókban lehetnek, de az összes adattípus betanítási adatkészletének ugyanabban a tárolóban kell lennie. (Lehetnek különböző mappákban.)

Új adattó létrehozásához kövesse a [Storage-fiók létrehozása a Azure Data Lake Storage Gen2 használatával való használatra](../../storage/blobs/create-data-lake-storage-account.md)című témakör utasításait.

## <a name="create-a-parameterization-table"></a>Paraméterezés-tábla létrehozása

Ezután létrehozunk egy metaadat-táblázatot egy Azure SQL-adatbázisban. Ez a táblázat az űrlap-felismerő REST API által igényelt nem bizalmas adatokat tartalmazza. Minden alkalommal, amikor új típusú űrlap szerepel az adatkészletben, beszúrunk egy új rekordot a táblázatba, és Kiváltjuk a betanítási és pontozási folyamatokat. (Később fogjuk megvalósítani ezeket a folyamatokat.)

Ezek a mezők a következő táblázatban lesznek felhasználva:

* **form_description**. A képzés részeként nem szükséges. Ez a mező leírja, hogy milyen típusú űrlapokat tanítunk a modellnek (például "ügyfél A Forms", "Hotel B Forms").
* **training_container_name**. Annak a Storage-fióknak a neve, amelyben a betanítási adatkészletet tároljuk. Ez lehet ugyanaz a tároló, mint **scoring_container_name**.
* **training_blob_root_folder**. A Storage-fiókban található mappa, ahol a rendszer a modell képzéséhez tartozó fájlokat tárolja.
* **scoring_container_name**. Annak a Storage-fióknak a neve, amelyben a fájlokat tároljuk, és a kulcs/érték párokat szeretnénk kinyerni. Ez lehet ugyanaz a tároló, mint **training_container_name**.
* **scoring_input_blob_folder**. Az a mappa a Storage-fiókban, ahol a fájlokat tároljuk az adatok kinyeréséhez.
* **model_id**. A kitanítani kívánt modell azonosítója. Az első futtatásnál az értéket a-1 értékre kell beállítani, ami azt eredményezi, hogy a betanítási jegyzetfüzet új egyéni modellt hoz létre a betanításhoz. A betanítási jegyzetfüzet az új modell AZONOSÍTÓját az Azure Data Factory-példányhoz fogja visszaadni. Tárolt eljárási tevékenység használatával frissítjük ezt az értéket az Azure SQL Database-ben.

  Ha új típusú űrlapot szeretne bevenni, manuálisan kell átállítania a modell AZONOSÍTÓját a-1 értékre a modell betanítása előtt.

* **file_type**. A támogatott típusú űrlapok:,, `application/pdf` `image/jpeg` `image/png` és `image/tif` .

  Ha más fájltípusokban is vannak űrlapok, akkor módosítania kell ezt az értéket, és a **model_id** új típusú űrlap betanításakor.
* **form_batch_group_id**. Az idő múlásával több, ugyanazon a modellen betanított oldaltükör is lehet. A **form_batch_group_id** mező lehetővé teszi az összes olyan adattípus megadását, amely egy adott modellel betanításra került.

### <a name="create-the-table"></a>A tábla létrehozása


[Hozzon létre egy Azure SQL Database-adatbázist](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), majd futtassa ezt az SQL-parancsfájlt a [lekérdezés-szerkesztőben](../../azure-sql/database/connect-query-portal.md) a szükséges tábla létrehozásához:


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Futtassa ezt a parancsfájlt a **model_id** automatikus frissítését követően.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Bizalmas hitelesítő adatok tárolása a Azure Key Vault használatával

Biztonsági okokból nem szeretnénk bizonyos bizalmas adatokat tárolni az Azure SQL Database paraméterezés táblájában. A bizalmas paramétereket Azure Key Vault titokként tároljuk.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása

[Key Vault erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Ezután nyissa meg a Key Vault erőforrást a létrehozása után, és a **Beállítások** szakaszban válassza a **titkok** lehetőséget a paraméterek hozzáadásához.

Megjelenik egy új ablak. Válassza a **készítés/importálás** lehetőséget. Adja meg a paraméter nevét és értékét, majd válassza a **Létrehozás** lehetőséget. Hajtsa végre ezeket a lépéseket a következő paraméterekkel:

* **CognitiveServiceEndpoint**. Az űrlap-felismerő API végpontjának URL-címe.
* **CognitiveServiceSubscriptionKey**. Az űrlap-felismerő szolgáltatás elérési kulcsa. 
* **StorageAccountName**. A Storage-fiók, amelyben a betanítási adatkészletet és a kulcs/érték párokat tartalmazó űrlapokat tárolja. Ha ezek az elemek különböző fiókokban találhatók, adja meg az egyes fiókok nevét külön titokként. Ne feledje, hogy a betanítási adatkészleteknek ugyanabban a tárolóban kell lenniük az összes adattípus esetében. Különböző mappákban lehetnek.
* **StorageAccountSasKey**. A Storage-fiók közös hozzáférésű aláírása (SAS). Az SAS URL-cím lekéréséhez nyissa meg a Storage-erőforrást. A **Storage Explorer** lapon nyissa meg a tárolót, kattintson a jobb gombbal, majd válassza a **közös hozzáférésű aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély van kiválasztva, majd válassza a **Létrehozás** lehetőséget. Ezután másolja az értéket az **URL** szakaszban. A következő űrlapot kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Az űrlap-felismerő modell betanítása Databricks-jegyzetfüzetbe

A Azure Databricks használatával tárolhatja és futtathatja az űrlap-felismerő szolgáltatással kommunikáló Python-kódot.

### <a name="create-a-notebook-in-databricks"></a>Jegyzetfüzet létrehozása a Databricks-ben

[Hozzon létre egy Azure Databricks erőforrást](https://ms.portal.azure.com/#create/Microsoft.Databricks) a Azure Portalban. A létrehozás után nyissa meg az erőforrást, és nyissa meg a munkaterületet.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Azure Key Vault által támogatott titkos hatókör létrehozása


Ha a fent létrehozott Azure Key vaultban lévő titkos kulcsokra szeretne hivatkozni, létre kell hoznia egy titkos hatókört a Databricks-ben. Kövesse az alábbi lépéseket: [hozzon létre egy Azure Key Vault által támogatott titkos kulcsot](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Databricks-fürt létrehozása

A fürt Databricks számítási erőforrások gyűjteménye. Fürt létrehozása:

1. A bal oldali ablaktáblán kattintson a **fürtök** gombra.
1. A **fürtök** lapon válassza a **fürt létrehozása** lehetőséget.
1. A **fürt létrehozása** lapon adja meg a fürt nevét, majd válassza a **7,2 (Scala 2,12, Spark 3.0.0)** elemet a **Databricks Runtime verziók** listájában.
1. Válassza a **Fürt létrehozása** lehetőséget.

### <a name="write-a-settings-notebook"></a>Beállítások megírása jegyzetfüzet

Most már készen áll a Python-jegyzetfüzetek hozzáadására. Először hozzon létre egy **Beállítások** nevű jegyzetfüzetet. Ez a jegyzetfüzet a paraméterezés táblában lévő értékeket rendeli hozzá a parancsfájlban szereplő változókhoz. A Azure Data Factory később adja át az értékeket paraméterként. A Key vaultban található titkos kódok értékeit is a változókhoz rendeljük. 

1. A **Beállítások** jegyzetfüzet létrehozásához kattintson a **munkaterület** gombra. Az új lapon válassza ki a legördülő listát, és válassza a **Létrehozás** , majd a **Jegyzetfüzet** lehetőséget.
1. Az előugró ablakban adja meg a jegyzetfüzet nevét, majd válassza a **Python** alapértelmezett nyelvként lehetőséget. Válassza ki a Databricks-fürtöt, majd válassza a **Létrehozás** lehetőséget.
1. Az első jegyzetfüzet-cellában lekéri a Azure Data Factory által átadott paramétereket:

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. A második cellában beolvasjuk a titkokat a Key Vaultból, és hozzárendeljük őket a változókhoz:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Betanítási jegyzetfüzet írása

Most, hogy elvégezte a **Beállítások** jegyzetfüzetet, létrehozhatunk egy jegyzetfüzetet a modell betanításához. A fentiekben leírtak szerint egy Azure Data Lake Storage Gen2 fiókban (**training_blob_root_folder**) található mappában tárolt fájlokat fogjuk használni. A mappa neve változóként lett átadva. Az egyes objektumtípusok ugyanabban a mappában lesznek. Ahogy a paraméter táblázatára mutatunk, a modell tanítása az összes oldaltükör típus használatával történik. 

1. Hozzon létre egy **TrainFormRecognizer** nevű jegyzetfüzetet. 
1. Az első cellában futtassa a **Beállítások** jegyzetfüzetet:

    ```python
    %run "./Settings"
    ```

1. A következő cellában rendeljen hozzá változókat a beállítási fájlból, és az egyes típusú típusok dinamikusan betanítsa a modellt, és alkalmazza a kódot a [Rest](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)rövid útmutatóban.

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. A betanítási folyamat utolsó lépése a betanítási eredmény beszerzése JSON formátumban:

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>Űrlap-adatok kinyerése jegyzetfüzet használatával

### <a name="mount-the-azure-data-lake-storage"></a>A Azure Data Lake tároló csatlakoztatása

A következő lépés a betanított modellen alapuló különféle űrlapok kiértékelése. Csatlakoztatni fogjuk a Azure Data Lake Storage fiókot a Databricks-ben, és a betöltési folyamat során a csatlakoztatásra hivatkozunk.

Ahogy a betanítási szakaszban tettük, a Azure Data Factory segítségével meghívjuk a kulcs/érték párok kinyerését az űrlapokból. Az űrlapokat a paraméter táblázatban megadott mappákban fogjuk átadni.

1. Hozza létre a notebookot a Storage-fiók csatlakoztatásához a Databricks-ben. Hívja meg a **MountDataLake**. 
1. Először meg kell hívnia a **Beállítások** jegyzetfüzetet:

    ```python
    %run "./Settings"
    ```

1. A második cellában definiálja a bizalmas paraméterek változóit, amelyeket a Key Vault titkaink közül fogunk beolvasni:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Próbálja meg leválasztani a Storage-fiókot abban az esetben, ha korábban már csatlakoztatták:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. A Storage-fiók csatlakoztatása:


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Csak a betanítási Storage-fiókot csatlakoztatta. Ebben az esetben a betanítási fájlok és a kulcs/érték párokat kinyerni kívánt fájlok ugyanabban a Storage-fiókban találhatók. Ha a pontozási és a betanítási tárolási fiókok eltérőek, mindkét Storage-fiókot csatlakoztatni kell. 

### <a name="write-the-scoring-notebook"></a>Pontozási jegyzetfüzet írása

Most már létrehozhatunk egy pontozási jegyzetfüzetet is. A betanítási jegyzetfüzetben a következőhöz hasonló műveleteket végezünk: az imént csatlakoztatott Azure Data Lake Storage-fiók mappáiban tárolt fájlokat fogjuk használni. A mappa nevét változóként adja át a rendszer. A megadott mappában lévő összes űrlapot átemeljük, majd kinyerjük a kulcs/érték párokat. 

1. Hozzon létre egy jegyzetfüzetet, és hívja meg a **ScoreFormRecognizer**. 
1. A **Beállítások** és a **MountDataLake** jegyzetfüzetek futtatása:

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Adja hozzá ezt a kódot, amely meghívja az [elemzés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API-t:

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. A következő cellában megkapjuk a kulcs/érték párok kinyerésének eredményét. Ennek a cellának a kimenete lesz az eredmény. Azt szeretnénk, hogy az eredmény JSON formátumú legyen, hogy tovább lehessen feldolgozni Azure SQL Database vagy Azure Cosmos DB. Ezért egy. JSON fájlba írjuk az eredményt. A kimeneti fájl neve lesz a "_output.jsbekapcsolva" értékkel összefűzött kilőtt fájl neve. A fájl ugyanabban a mappában lesz tárolva, mint a forrásfájl.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Végezze el a fájl írásakor követendő eljárást egy új cellában:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>A képzés és a pontozás automatizálása Azure Data Factory használatával

Az egyetlen fennmaradó lépés az Azure Data Factory szolgáltatás beállítása a betanítási és pontozási folyamatok automatizálására. Először kövesse az [adat-előállító létrehozása](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)című témakör lépéseit. A Azure Data Factory erőforrás létrehozása után három folyamatot kell létrehoznia: az egyiket a képzéshez, a kettőt pedig a pontozáshoz. (Később fogjuk megmagyarázni.)

### <a name="training-pipeline"></a>Betanítási folyamat

A betanítási folyamat első tevékenysége az Azure SQL Database paraméterezés táblájában lévő értékek olvasására és visszaküldésére szolgáló keresés. A betanítási adatkészletek ugyanabban a Storage-fiókban és tárolóban (de esetleg más mappákban) is szerepelnek. Ezért az attribútum alapértelmezett értékeként csak a keresési tevékenység beállításaiban kell megtartani az **első sort** . A modellnek az egyes típusú űrlapokra való betanításához a **training_blob_root_folder** összes fájljának használatával betanítjuk a modellt.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Képernyőkép, amely a Data Factory betanítási folyamatát mutatja be.":::

A tárolt eljárás két paramétert használ: **model_id** és **form_batch_group_id**. A Databricks jegyzetfüzetből a modell AZONOSÍTÓját visszaadó kód `dbutils.notebook.exit(model_id)` . A kód, amely beolvassa a kódot az Data Factory tárolt eljárási tevékenységében `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Pontozási folyamatok

A kulcs/érték párok kinyeréséhez a paraméterezés tábla összes mappáját ellenőrizni fogjuk. Minden mappához kinyerjük a benne lévő összes fájl kulcs/érték párokat. A Azure Data Factory jelenleg nem támogatja a beágyazott ForEach hurkokat. Ehelyett két folyamatot hozunk létre. Az első folyamat végrehajtja a keresést a paraméterezés táblából, és a mappák listáját a második folyamat paraméterként adja át.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="A Data Factory első pontozási folyamatát bemutató képernyőkép.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Képernyőkép, amely a Data Factory első pontozási folyamatának részleteit jeleníti meg.":::

A második folyamat egy GetMeta tevékenységet fog használni a mappában található fájlok listájának lekéréséhez, és paraméterként adja át a pontozási Databricks notebooknak.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="A Data Factory második pontozási folyamatát bemutató képernyőkép.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="A Data Factory második pontozási folyamatának részleteit bemutató képernyőkép.":::

### <a name="specify-a-degree-of-parallelism"></a>A párhuzamossági fok meghatározása

A betanítási és pontozási folyamatokban megadhatja a párhuzamosság fokát, így egyszerre több űrlapot is feldolgozhat.

A párhuzamosság mértékének beállítása a Azure Data Factory folyamatban:

1. Válassza ki a **foreach** tevékenységet.
1. Törölje a **szekvenciális** mezőt.
1. Állítsa be a párhuzamosság fokát a **kötegek száma** mezőben. A pontozáshoz legfeljebb 15 batch-értéket ajánlunk.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="A Azure Data Factory pontozási tevékenységének párhuzamossági beállításait bemutató képernyőkép.":::

## <a name="how-to-use-the-pipeline"></a>A folyamat használata

Mostantól egy automatizált folyamattal digitalizálhatja az űrlapokat, és egy elemzést is futtathat rajtuk. Ha egy ismerős típusú új űrlapokat ad hozzá egy meglévő Storage-mappához, egyszerűen futtassa újra a pontozási folyamatokat. A rendszer frissíti az összes kimeneti fájlt, beleértve az új űrlapok kimeneti fájljait is. 

Ha új típusú új űrlapokat ad hozzá, egy betanítási adatkészletet is fel kell töltenie a megfelelő tárolóba. Ezután új sort fog hozzáadni a paraméterezés táblában, megadhatja az új dokumentumok helyét és a betanítási adatkészletet. Adja meg az-1 értéket a **model_ID** számára, hogy jelezze, hogy az űrlapokhoz új modellt kell képezni. Ezután futtassa a betanítási folyamatot Azure Data Factory. A folyamat beolvassa a táblázatot, betanítja a modellt, és felülírja a modell AZONOSÍTÓját a táblában. Ezután meghívhatja a pontozási folyamatokat a kimeneti fájlok írásának megkezdéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban Azure Data Factory folyamatokat állít be, hogy aktiválja az űrlap-felismerő modellek betanítását és futtatását, és digitalizálja a fájlok nagy számát. Ezután tekintse át az űrlap-felismerő API-t, hogy megtudja, mi a teendő.

* [Űrlap-felismerő REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
