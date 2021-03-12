---
title: 'Oktatóanyag: tárolt dokumentumok feldolgozásához használja az Azure-függvényt'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan használhatja az Azure-függvényeket az Azure Blob Storage-tárolóba feltöltött dokumentumok feldolgozásának elindításához.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: pafarley
ms.openlocfilehash: ee91a9e881ee5e8707feb862162b4272e3552d5f
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102663337"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Oktatóanyag: tárolt dokumentumok feldolgozásához használja az Azure-függvényt

Az űrlap-felismerőt a Azure Functions beépített automatizált adatfeldolgozási folyamat részeként használhatja. Ez az útmutató bemutatja, hogyan használható egy Azure-függvény az Azure Blob Storage-tárolóba feltöltött dokumentumok feldolgozásához. Ez a munkafolyamat a tárolt dokumentumokból származó táblázatos adatok kinyerését az űrlap-felismerő elrendezési szolgáltatással, és a tábla adatait egy. csv-fájlban tárolja az Azure-ban. Ezután a Microsoft Power BI használatával jelenítheti meg az adatmegjelenítést (itt nem szerepel).

> [!div class="mx-imgBorder"]
> ![Azure-szolgáltatás munkafolyamat-diagramja](./media/tutorial-azure-function/workflow-diagram.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Storage-fiók létrehozása
> * Azure Functions-projekt létrehozása
> * Elrendezési adatok kinyerése a feltöltött űrlapokból
> * Elrendezési adatok feltöltése az Azure Storage-ba

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Űrlap-felismerő erőforrás létrehozásához "  target="_blank"> hozzon létre egy űrlap-felismerő erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal az űrlap felismerő kulcsának és végpontjának lekéréséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához a létrehozott erőforrás kulcsára és végpontra lesz szüksége. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Az elemezni kívánt helyi PDF-dokumentum. Ezt a [dokumentumtípust](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) letöltheti a használatra.
* A [Python 3.8. x](https://www.python.org/downloads/) telepítve van.
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) telepítve.
* [Azure functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools) telepítve.
* A Visual Studio Code a következő bővítményekkel van telepítve:
  * [Azure Functions bővítmény](https://docs.microsoft.com/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python-bővítmény](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) a Azure Portal. Válassza a **StorageV2** lehetőséget a fiók típusaként.

A bal oldali panelen válassza a **CORS** lapot, és ha létezik, távolítsa el a meglévő CORS-házirendet.

A üzembe helyezést követően hozzon létre két üres blob Storage-tárolót, a **test** and **output** nevet.

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

Nyissa meg a Visual Studio Code-ot. Ha telepítette a Azure Functions bővítményt, akkor a bal oldali navigációs ablaktáblán egy Azure-embléma jelenik meg. Válassza ki. Hozzon létre egy új projektet, és amikor a rendszer kéri, hozzon létre egy helyi mappát **coa_new** a projekt tárolására.

![VSCode-létrehozási funkció gomb](./media/tutorial-azure-function/vs-code-create-function.png)


A rendszer megkéri, hogy állítson be számos beállítást:
* A **nyelv kiválasztása** parancssorban válassza a Python lehetőséget.
* A **sablon kiválasztása** parancssorban válassza az Azure Blob Storage trigger lehetőséget. Ezután adja meg az alapértelmezett trigger nevét.
* A **beállítási kérés kiválasztása párbeszédpanelen válassza** az új helyi Alkalmazásbeállítások létrehozása lehetőséget.
* Válassza ki az Azure-előfizetését a létrehozott Storage-fiókkal. Ezután meg kell adnia a Storage-tároló nevét (ebben az esetben `test/{name}` )
* Válassza a projekt megnyitását az aktuális ablakban. 

![VSCode-létrehozási kérés – példa](./media/tutorial-azure-function/vs-code-prompt.png)

Ha elvégezte ezeket a lépéseket, a VSCode egy új Azure functions-projektet ad hozzá egy *\_ \_ init \_ \_ .* a Python-parancsfájllal. Ez a szkript akkor aktiválódik, ha a rendszer feltölt egy fájlt a **test** Storage-tárolóba, de nem végez semmit.

## <a name="test-the-function"></a>A függvény tesztelése

Az alapszintű függvény futtatásához nyomja le az F5 billentyűt. A VSCode megkéri, hogy válasszon ki egy Storage-fiókot az-hoz való kapcsolódáshoz. Válassza ki a létrehozott és folytatott Storage-fiókot.

Nyisson meg Azure Storage Explorer, és töltsön fel egy minta PDF-dokumentumot a **teszt** tárolóba. Ezután jelölje be a VSCode terminált. A szkriptnek be kell jelentkeznie, hogy a PDF-feltöltés elindította.

![VSCode-terminál tesztelése](./media/tutorial-azure-function/vs-code-terminal-test.png)


A folytatás előtt állítsa le a parancsfájlt.

## <a name="add-form-processing-code"></a>Űrlap-feldolgozási kód hozzáadása

Ezután adja hozzá a saját kódját a Python-szkripthez az űrlap-felismerő szolgáltatás meghívásához és a feltöltött dokumentumok elemzéséhez az űrlap-felismerő [elrendezési API](concept-layout.md)használatával.

A VSCode-ben navigáljon a függvény *requirements.txt* fájljához. Ez határozza meg a parancsfájl függőségeit. Adja hozzá a következő Python-csomagokat a fájlhoz:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Ezután nyissa meg az *\_ \_ init \_ \_ .* a-szkriptet. Adja hozzá a következő `import`-utasításokat:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

A generált függvényt a következőképpen hagyhatja: `main` . Ehhez a függvényhez hozzá kell adnia az egyéni kódot.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

A következő kódrészlet meghívja az űrlap-felismerőt az [elrendezési API elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) a feltöltött dokumentumon. Adja meg a végpont és a kulcs értékeit. 


# <a name="version-20"></a>[2,0-es verzió](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott űrlap-felismerő erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információ: [Cognitive Services biztonsági](../cognitive-services-security.md) cikk.

Ezután adja hozzá a kódot a szolgáltatás lekérdezéséhez és a visszaadott érték lekéréséhez. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Ezután adja hozzá a következő kódot az Azure Storage **kimeneti** tárolóhoz való kapcsolódáshoz. Adja meg a saját értékeit a Storage-fiók neve és kulcsa számára. A kulcsot a Storage-erőforrás **hozzáférési kulcsok** lapján érheti el a Azure Portal.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

A következő kód elemzi a visszaadott űrlap-felismerő választ, létrehoz egy. csv-fájlt, és feltölti azt a **kimeneti** tárolóba. 


> [!IMPORTANT]
> Valószínűleg módosítania kell ezt a kódot, hogy az megfeleljen a saját űrlapos dokumentumok struktúrájának.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Végül a kód utolsó blokkja feltölti a kinyert táblát és a szöveges adatait a blob Storage-elembe.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>A függvény futtatása

Nyomja le az F5 billentyűt a függvény ismételt futtatásához. A Azure Storage Explorer használatával tölthet fel egy minta PDF-űrlapot a **test** Storage-tárolóba. A műveletnek futnia kell a szkript futtatásához, és ekkor látnia kell az eredményül kapott. csv fájlt (táblázatként jelenik meg) a **kimeneti** tárolóban.

Ezt a tárolót úgy is összekapcsolhatja Power BI, hogy a benne található adatvizualizációk széles tárházát hozza létre.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhat egy Pythonban írt Azure-függvényt, amely automatikusan feldolgozza a feltöltött PDF-dokumentumokat, és a tartalmukat egy adatformátumban jeleníti meg. Következő lépésként megtudhatja, hogyan jelenítheti meg az információkat a Power BI használatával.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Mi a Form Recognizer?](overview.md)
* További információ az [elrendezési API](concept-layout.md) -ról
