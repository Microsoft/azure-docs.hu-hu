---
title: 'Oktatóanyag: űrlapos adatok tömeges kinyerése Azure Data Factory-űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Azure Data Factory tevékenységek beállításával elindíthatja az űrlap-felismerő modellek betanítását és futtatását a dokumentumok nagy lemaradásának digitalizálásához.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 6faa612f55b4114b4242c48d43aae9aac8c56582
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699997"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Oktatóanyag: adatok tömeges kinyerése Azure Data Factory használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure-szolgáltatásokat egy nagy mennyiségű űrlap digitális médiába való betöltésére. Ez az oktatóanyag bemutatja, hogyan automatizálható az adatok betöltése egy Azure Data Lake dokumentumból egy Azure SQL Database-adatbázisba. Gyorsan betaníthatja a modelleket, és néhány kattintással feldolgozhatja az új dokumentumokat.

## <a name="business-need"></a>Üzleti igények

A legtöbb szervezet most már tisztában van azzal, hogy milyen értékesek a különböző formátumokban tárolt adatmennyiségek (PDF, képek, videók). Az ajánlott eljárásokat és a legtöbb költséghatékony módszert keresik az eszközök digitalizálásához.

Emellett ügyfeleink gyakran különböző típusú űrlapokkal rendelkeznek, amelyek számos ügyféltől és ügyféltől származnak. A rövid útmutatóktól eltérően ez az oktatóanyag azt mutatja [be, hogyan](./quickstarts/client-library.md)lehet automatikusan betanítani egy modellt új és különböző típusú űrlapokkal a metaadatokon alapuló megközelítés használatával. Ha nem rendelkezik meglévő modellel a megadott típusú űrlaphoz, akkor a rendszer létrehoz egyet, és megadja a modell AZONOSÍTÓját. 

Az űrlapokból származó adatok kinyerése és a meglévő operatív rendszerek és adattárházak kombinálásával való összevonásával a vállalatok bepillantást nyerhetnek, és az ügyfelek és az üzleti felhasználók számára értékes eredményeket szerezhetnek.

Az Azure Form felismerő segítségével a szervezetek kihasználhatják az adatmennyiséget, automatizálják a folyamatokat (a számla kifizetését, az adózási feldolgozást stb.), megtakarítják a pénzt és az időt, és jobb adatpontossággal rendelkeznek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Data Lake beállítása az űrlapok tárolására
> * Parametrization-tábla létrehozása Azure-adatbázis használatával
> * Bizalmas hitelesítő adatok tárolása a Azure Key Vault használatával
> * Az űrlap-felismerő modell betanítása Databricks-jegyzetfüzetbe
> * Űrlap-adatok kinyerése Databricks notebook használatával
> * Automatizálja az űrlapok betanítását és kinyerését Azure Data Factory

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához a létrehozott erőforrás kulcsára és végpontra lesz szüksége. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Legalább öt azonos típusú űrlap. Ideális esetben ez a munkafolyamat nagy méretű dokumentumok támogatását jelenti. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekkel kapcsolatban lásd: [képzési adatkészlet létrehozása](./build-training-data-set.md) . Ebben az oktatóanyagban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2128080) **vonattal** mappában található fájlokat.

## <a name="project-architecture"></a>Projekt architektúrája 

Ez a projekt Azure Data Factory folyamatokból áll, amelyek olyan Python-jegyzetfüzetek kiváltására szolgálnak, amelyek egy Azure Data Lake Storage-fiókban lévő dokumentumokból betanítják, elemzik és kinyerik az adatokból.

Az űrlap-felismerő REST AP-nek bizonyos paramétereket kell megadnia bemenetként. Biztonsági okokból a paraméterek némelyike egy Azure Key Vault lesz tárolva, míg más kevésbé érzékeny paraméterek, például a Storage blob-mappa neve, egy Azure SQL Database-adatbázis paraméterezés-táblájába kerülnek.

Az elemezni kívánt űrlap típusa az adatmérnökök vagy az adatszakértők a paraméter táblázat egy sorát töltik fel. Ezután a Azure Data Factory segítségével megismétlik az észlelt Űrlapbeállítások listáját, és átadhatják a releváns paramétereket egy Databricks-jegyzetfüzetbe az űrlap-felismerő modellek betanításához vagy átképzéséhez. Itt is használhatja az Azure-függvényeket.

A Azure Databricks jegyzetfüzet ezután a betanított modellekkel kinyeri az űrlapadatokat, és exportálja ezt a dátumot egy Azure SQL Database-adatbázisba.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="projekt architektúrája":::


## <a name="set-up-azure-data-lake"></a>Azure Data Lake beállítása

Előfordulhat, hogy a várakozó űrlapok a helyszíni környezetben vagy egy FTP-kiszolgálón vannak. Ez az oktatóanyag egy Azure Data Lake Gen 2 Storage-fiók űrlapjait használja. A fájlokat Azure Data Factory, Azure Storage Explorer vagy AzCopy használatával is átviheti. A betanítási és pontozási adatkészletek különböző tárolókban lehetnek, de az összes adattípus betanítási adatkészletének ugyanabban a tárolóban kell lennie (bár különböző mappákban lehetnek).

Új Data Lake létrehozásához kövesse a [Storage-fiók létrehozása a Azure Data Lake Storage Gen2 használatával](../../storage/blobs/create-data-lake-storage-account.md)című témakör utasításait.

## <a name="create-a-parameterization-table"></a>Paraméterezés-tábla létrehozása

Ezután létrehozunk egy metaadat-táblázatot egy Azure SQL Database. Ez a táblázat az űrlap-felismerő REST API által igényelt nem bizalmas adatokat tartalmazza. Amikor új típusú űrlap szerepel az adatkészletben, egy új rekordot szúrunk be a táblázatba, és aktiváljuk a betanítási és pontozási folyamatot (később kell megvalósítani).

A táblázat a következő mezőket fogja használni:

* **form_description**: Ez a mező nem szükséges a betanítás részeként. Leírja, hogy milyen típusú űrlapokat tanítunk a modellhez (például "ügyfél A Forms", "Hotel B Forms").
* **training_container_name**: Ez a mező a Storage-fiók tárolójának neve, ahol a betanítási adatkészletet tároljuk. Ez lehet ugyanaz a tároló, mint **scoring_container_name**.
* **training_blob_root_folder**: a Storage-fiókban található mappa, ahol a rendszer a modell képzéséhez tartozó fájlokat tárolja.
* **scoring_container_name**: Ez a mező a Storage-fiók tárolójának neve, ahol tároltuk azokat a fájlokat, amelyekről a kulcs érték párokat szeretnénk kinyerni. Ez lehet ugyanaz a tároló, mint **training_container_name**.
* **scoring_input_blob_folder**: a Storage-fiókban található mappa, ahol a fájlokat tároljuk az adatok kinyeréséhez.
* **model_id**: az újratanítani kívánt modell azonosítója. Az első futtatásnál az értéket a-1 értékre kell beállítani, ami azt eredményezi, hogy a betanítási jegyzetfüzet új egyéni modellt hoz létre a betanításhoz. A betanítási jegyzetfüzet visszaküldi az újonnan létrehozott modell AZONOSÍTÓját a Azure Data Factory példánynak, és egy tárolt eljárási tevékenység használatával frissítjük ezt az értéket az Azure SQL Database-ben.

  Amikor új űrlapot szeretne bevenni, manuálisan kell átállítania a modell AZONOSÍTÓját a-1 értékre a modell betanítása előtt.

* **file_type**: a támogatott típusú űrlapok a következők:,, `application/pdf` `image/jpeg` `image/png` és `image/tif` .

  Ha különböző fájltípusokkal rendelkezik, akkor módosítania kell ezt az értéket, és **model_id** új oldaltükör betanításakor.
* **form_batch_group_id**: az idő múlásával több, ugyanazon a modellen betanítási típussal rendelkezhet. A **form_batch_group_id** lehetővé teszi az összes olyan adattípus megadását, amely egy adott modell használatával lett betanítva.

### <a name="create-the-table"></a>A tábla létrehozása

[Hozzon létre egy Azure SQL Database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), majd futtassa a következő SQL-parancsfájlt a [lekérdezés-szerkesztőben](../../azure-sql/database/connect-query-portal.md) a szükséges tábla létrehozásához.

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

Futtassa az alábbi szkriptet a **model_id** automatikus frissítéséhez, miután betanítják.

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

### <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

[Key Vault erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Ezután a létrehozás után navigáljon a Key Vault erőforráshoz, és a **Beállítások** szakaszban válassza a **titkok** lehetőséget a paraméterek hozzáadásához.

Ekkor megjelenik egy új ablak, majd válassza a **Létrehozás/importálás** lehetőséget. Adja meg a paraméter nevét és értékét, majd kattintson a Létrehozás gombra. Tegye ezt a következő paraméterekkel:

* **CognitiveServiceEndpoint**: az űrlap-felismerő API végpontjának URL-címe.
* **CognitiveServiceSubscriptionKey**: az űrlap-felismerő szolgáltatás elérési kulcsa. 
* **StorageAccountName**: az a Storage-fiók, amelyben a betanítási adatkészletet és űrlapokat a kulcs-érték párokat szeretnénk kinyerni. Ha ezek különböző fiókokban vannak, adja meg az egyes fiókok nevét külön titokként. Ne feledje, hogy a betanítási adatkészleteknek ugyanabban a tárolóban kell lenniük az összes adattípus esetében, de lehetnek különböző mappákban.
* **StorageAccountSasKey**: a Storage-fiók közös hozzáférésű aláírása (SAS). Az SAS URL-cím lekéréséhez nyissa meg a Storage-erőforrást, és válassza a **Storage Explorer** lapot. Navigáljon a tárolóhoz, kattintson a jobb gombbal, majd válassza a **közös hozzáférési aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás** gombra. Ezután másolja az értéket az **URL** szakaszban. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Az űrlap-felismerő modell betanítása Databricks-jegyzetfüzetbe

A Azure Databricks használatával tárolhatja és futtathatja az űrlap-felismerő szolgáltatással kommunikáló Python-kódot.

### <a name="create-a-notebook-in-databricks"></a>Jegyzetfüzet létrehozása a Databricks-ben

[Hozzon létre egy Azure Databricks erőforrást](https://ms.portal.azure.com/#create/Microsoft.Databricks) a Azure Portalban. Nyissa meg az erőforrást a létrehozása után, és indítsa el a munkaterületet.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Azure Key Vault által támogatott titkos hatókör létrehozása

Ahhoz, hogy a fent létrehozott Azure Key Vault titkára hivatkozzon, létre kell hoznia egy titkos hatókört a Databricks-ben. Kövesse a [Azure Key Vault által támogatott titkos kulcs létrehozása](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)című témakör lépéseit.

### <a name="create-a-databricks-cluster"></a>Databricks-fürt létrehozása

A fürt Databricks számítási erőforrások gyűjteménye. Fürt létrehozása:

1. Az oldalsávon kattintson a **fürtök** gombra.
1. A **fürtök** lapon kattintson a **fürt létrehozása** elemre.
1. A **fürt létrehozása** lapon adja meg a fürt nevét, és válassza a **7,2 (Scala 2,12, Spark 3.0.0)** elemet a Databricks Runtime verzió legördülő menüben.
1. Kattintson a **fürt létrehozása** elemre.

### <a name="write-a-settings-notebook"></a>Beállítások megírása jegyzetfüzet

Most már készen áll a Python-jegyzetfüzetek hozzáadására. Először hozzon létre egy **Beállítások** nevű jegyzetfüzetet; Ez a jegyzetfüzet a paraméterezés táblában lévő értékeket rendeli hozzá a parancsfájlban szereplő változókhoz. Az értékeket a rendszer később paraméterként adja át Azure Data Factory. Az értékeket a Key Vault titkos kulcsaihoz is hozzárendeljük a változókhoz. 

1. A **Beállítások** jegyzetfüzet létrehozásához kattintson a **munkaterület** gombra, az új lapon kattintson a legördülő listára, majd válassza a **Létrehozás** , majd a **Jegyzetfüzet** lehetőséget.
1. Az előugró ablakban adja meg a jegyzetfüzethez adni kívánt nevet, és válassza a **Python** alapértelmezett nyelvként lehetőséget. Válassza ki a Databricks-fürtöt, és válassza a **Létrehozás** lehetőséget.
1. Az első jegyzetfüzet-cellában lekéri a Azure Data Factory által átadott paramétereket.

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

1. A második cellában beolvasjuk a titkokat a Key Vaultból, és hozzárendeljük őket a változókhoz.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Betanítási jegyzetfüzet írása

Most, hogy elvégezte a **Beállítások** jegyzetfüzetet, létrehozhatunk egy jegyzetfüzetet a modell betanításához. A fentiekben leírtaknak megfelelően egy Azure Data Lake 2. generációs Storage-fiók (**training_blob_root_folder**) mappájában tárolt fájlokat fogjuk használni. A mappa neve változóként lett átadva. Az egyes objektumtípusok ugyanabban a mappában lesznek, és a paraméter táblázatán keresztül a modell az összes típusú űrlap használatával betanítjuk a modellt. 

1. Hozzon létre egy új, **TrainFormRecognizer** nevű jegyzetfüzetet. 
1. Az első cellában hajtsa végre a beállítások jegyzetfüzetet:

    ```python
    %run "./Settings"
    ```

1. A következő cellában rendeljen hozzá változókat a **beállítási** fájlból, és az egyes típusú típusok dinamikusan betanítsa a modellt, és alkalmazza a kódot a [Rest](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)rövid útmutatóban.

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
        # Request headers
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
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
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
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. A betanítási folyamat utolsó lépése a betanítási eredmény JSON formátumban való beszerzése.

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

## <a name="extract-form-data-using-a-notebook"></a>Űrlap-adatok kinyerése jegyzetfüzet használatával

### <a name="mount-the-azure-data-lake-storage"></a>A Azure Data Lake tároló csatlakoztatása

A következő lépés a betanított modellt használó különböző űrlapok kiértékelése. Csatlakoztatni fogjuk a Azure Data Lake Storage-fiókot a Databricks-ben, és a betöltési folyamat során a csatlakoztatásra hivatkozunk.

Akárcsak a betanítási szakaszban, a Azure Data Factory segítségével meghívjuk a kulcs-érték párok kinyerését az űrlapokból. Az űrlapokat a paraméter táblázatban megadott mappákban fogjuk átadni.

1. Hozzuk létre a notebookot a Storage-fiók csatlakoztatásához a Databricks-ben. Meghívjuk a **MountDataLake**. 
1. Először meg kell hívnia a **Beállítások** jegyzetfüzetet:

    ```python
    %run "./Settings"
    ```

1. A második cellában definiáljuk a bizalmas paraméterek változóit, amelyeket a Key Vault titkaink közül fogunk lekérdezni.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Ezután megpróbáljuk leválasztani a Storage-fiókot arra az esetre, ha korábban már csatlakoztatták.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Végül csatlakoztatni fogjuk a Storage-fiókot.


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
    > Ebben az esetben csak a betanítási tárterületet csatlakoztatjuk &mdash; , a betanítási fájlokat és a kulcs-érték párokat. Ha a pontozási és a betanítási tárolási fiókok eltérőek, akkor ide kell csatlakoztatnia mindkét Storage-fiókot. 

### <a name="write-the-scoring-notebook"></a>Pontozási jegyzetfüzet írása

Most létrehozhatunk egy pontozási jegyzetfüzetet. A betanítási jegyzetfüzethez hasonlóan az imént csatlakoztatott Azure Data Lake Storage-fiók mappáiban tárolt fájlokat fogjuk használni. A mappa nevét változóként adja át a rendszer. A megadott mappában lévő összes űrlapot átemeljük, majd kinyerjük a kulcs-érték párokat. 

1. Hozzon létre egy új jegyzetfüzetet, és hívja meg a **ScoreFormRecognizer**. 
1. Hajtsa végre a **beállításokat** és a **MountDataLake** -jegyzetfüzeteket.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Ezután adja hozzá a következő kódot, amely meghívja az [elemzés](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API-t.

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
        # Request headers
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

1. A következő cellában megkapjuk a kulcs-érték párok kinyerésének eredményét. Ennek a cellának a kimenete lesz az eredmény. Mivel az eredményt JSON formátumban szeretnénk feldolgozni a Azure SQL Database vagy Cosmos DB, az eredményt egy. JSON fájlba írjuk. A kimeneti fájl neve lesz a "_output.json" összefűzött fájl neve. A fájl ugyanabban a mappában lesz tárolva, mint a forrásfájl.

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

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>A képzés és a pontozás automatizálása Azure Data Factory

Az egyetlen fennmaradó lépés a Azure Data Factory (ADF) szolgáltatás beállítása a betanítási és pontozási folyamatok automatizálására. Először kövesse az [adat-előállító létrehozása](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)című témakör lépéseit. Az ADF-erőforrás létrehozása után három folyamatot kell létrehoznia: az egyiket a képzéshez és kettőt a pontozáshoz (lásd alább).

### <a name="training-pipeline"></a>Betanítási folyamat

A betanítási folyamat első tevékenysége az Azure SQL Database paraméterezés táblájában lévő értékek olvasására és visszaküldésére szolgáló keresés. Mivel a betanítási adatkészletek ugyanabban a Storage-fiókban és tárolóban (de esetleg más mappákban) is szerepelnek, a keresési tevékenység beállításaiban megtartjuk az alapértelmezett érték **első sora** attribútumát. A modellnek az egyes típusú űrlapokra való betanításához a **training_blob_root_folder** összes fájljának használatával betanítjuk a modellt.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="az adatfeldolgozó folyamata":::

A tárolt eljárás két paramétert vesz igénybe: **model_id** és a **form_batch_group_id**. A Databricks-jegyzetfüzetből a modell AZONOSÍTÓjának visszaküldésére szolgáló kód `dbutils.notebook.exit(model_id)` , valamint a kód, amely beolvassa a kódot a tárolt eljárási tevékenységben a adat-előállítóban `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Pontozási folyamatok

A kulcs-érték párok kinyeréséhez a paraméterezés tábla összes mappáját ellenőrizni fogjuk, az egyes mappák esetében pedig kinyerjük az összes fájl kulcs-érték párokat. A mai naptól kezdve az ADF nem támogatja a beágyazott ForEach hurkokat. Tehát Ehelyett két folyamat jön létre. Az első folyamat végrehajtja a keresést a paraméterezés táblából, és a mappák listáját a második folyamat paraméterként adja át.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="első pontozási folyamat a adatgyárban":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="első pontozási folyamat a adat-előállítóban, részletek":::

A második folyamat egy GetMeta tevékenységet fog használni a mappában található fájlok listájának lekéréséhez, és paraméterként adja át a pontozási Databricks notebooknak.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="második pontozási folyamat a The Factoryban":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="második pontozási folyamat a adat-előállítóban, részletek":::

### <a name="specify-a-degree-of-parallelism"></a>A párhuzamossági fok meghatározása

A betanítási és pontozási folyamatokban megadhatja, hogy a párhuzamosság foka egyszerre több űrlapot is feldolgozzon.

Az ADF-folyamat párhuzamossági fokának beállítása:

* Válassza ki a foreach tevékenységet.
* Törölje a **szekvenciális** mezőt.
* Állítsa be a párhuzamosság fokát a **kötegek száma** szövegmezőbe. A pontozáshoz legfeljebb 15 batch-értéket ajánlunk.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="a pontozási tevékenység párhuzamossági konfigurációja az ADF-ben":::

## <a name="how-to-use"></a>Használat

Mostantól egy automatizált folyamattal digitalizálhatja az űrlapokat, és egy elemzést is futtathat rajtuk. Ha egy ismerős típusú új űrlapokat ad hozzá egy meglévő Storage-mappához, egyszerűen futtassa újra a pontozási folyamatokat, és frissíti az összes kimeneti fájlt, beleértve az új űrlapok kimeneti fájljait is. 

Ha új típusú új űrlapokat ad hozzá, egy betanítási adatkészletet is fel kell töltenie a megfelelő tárolóba. Ezután adjon hozzá egy új sort a paraméterezés táblához, és adja meg az új dokumentumok helyét és a betanítási adatkészletet. Adja meg az-1 értéket a **model_ID** számára, hogy jelezze, hogy egy új modellt kell képezni ezeknek az űrlapoknak. Ezután futtassa az automatikus betanítási folyamatot az ADF-ben. Beolvassa a táblázatot, betanítja a modellt, és felülírja a modell AZONOSÍTÓját a táblában. Ezután meghívhatja a pontozási folyamatokat a kimeneti fájlok írásának megkezdéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban Azure Data Factory folyamatokat állít be, hogy kiváltsa az űrlap-felismerő modellek betanítását és futtatását a fájlok nagy számainak digitalizálásához. Ezután tekintse át az űrlap-felismerő API-t, hogy megtudja, mi a teendő.

* [Űrlap-felismerő REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)