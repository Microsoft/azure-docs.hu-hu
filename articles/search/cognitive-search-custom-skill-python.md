---
title: Egyéni szakértelem – példa (Python)
titleSuffix: Azure Cognitive Search
description: Python-fejlesztők számára a Azure Functions és a Visual Studio használatával megismerheti az egyéni képességek létrehozásához szükséges eszközöket és technikákat. Az egyéni szaktudás olyan felhasználó által definiált modelleket vagy logikát tartalmaz, amelyeket hozzáadhat egy mesterséges intelligenciával bővített indexelési folyamathoz az Azure Cognitive Searchban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936129"
---
# <a name="example-create-a-custom-skill-using-python"></a>Példa: egyéni szakértelem létrehozása a Python használatával

Ebben az Azure Cognitive Search készségkészlet példaként megtudhatja, hogyan hozhat létre egyéni webes API-képességet a Python és a Visual Studio Code használatával. A példa egy [Azure-függvényt](https://azure.microsoft.com/services/functions/) használ, amely megvalósítja az [Egyéni ügyességi felületet](cognitive-search-custom-skill-interface.md).

Az egyéni képességet a kialakítás egyszerűvé teszi (két karakterlánc összefűzése), így a Pythonban az egyéni szaktudás fejlesztéséhez használt eszközökre és technológiákra koncentrálhat. Ha egy egyszerű képességgel sikerül, összetettebb forgatókönyvekkel is foglalkozhat.

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át az [Egyéni ügyességi felületet](cognitive-search-custom-skill-interface.md) az egyéni képességek megvalósításához szükséges bemeneti/kimeneti interfész bevezetéséhez.

+ Állítsa be a környezetet. Ezt az [oktatóanyagot](/azure/python/tutorial-vs-code-serverless-python-01) követjük teljes körűen a kiszolgáló nélküli Azure-függvények beállításához a Visual Studio Code és a Python Extensions használatával. Az oktatóanyag végigvezeti Önt a következő eszközök és összetevők telepítésén: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Azure Functions-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ez a példa egy Azure-függvényt használ a webes API-k üzemeltetésére, de más megközelítések is lehetségesek. Ha megfelel a [kognitív képességek felületi követelményeinek](cognitive-search-custom-skill-interface.md), az Ön által felhasználható megközelítés nem lényeges. Azure Functions azonban egyszerűvé teheti az egyéni képességek létrehozását.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create new project...` .

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ezért ne válasszon olyan projekt-mappát, amely a munkaterület részét képezi.

1. Válassza ki a Function app-projekt nyelvét. Ebben az oktatóanyagban válassza a **Python** lehetőséget.
1. Válassza ki a Python-verziót (a 3.7.5 verzióját a Azure Functions támogatja)
1. Válassza ki a projekt első függvényének sablonját. Válassza a **http-trigger** lehetőséget egy http által aktivált függvény létrehozásához az új Function alkalmazásban.
1. Adja meg a függvény nevét. Ebben az esetben használja az **összefűzést** 
1. Engedélyezési szintként válassza a **függvény** lehetőséget. Ez azt jelenti, hogy egy [funkcióbillentyű](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) megadásával hívja meg a függvény http-végpontját. 
1. Válassza ki, hogyan szeretné megnyitni a projektet. Ehhez a lépéshez válassza a **Hozzáadás a munkaterülethez** lehetőséget a Function alkalmazás létrehozásához az aktuális munkaterületen.

A Visual Studio Code létrehozza a függvényalkalmazást egy új munkaterületen. Ez a projekt a [host.json](../azure-functions/functions-host-json.md) és a [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) konfigurációs fájlokat tartalmazza, valamint az esetleges nyelvspecifikus projektfájlokat is. 

A Function app projekt **összefűzési** mappájába egy új, http-triggerrel aktivált függvény is létrejön. \_ \_ Ebben a tartalomban a "init__. a" nevű fájl lesz:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Most módosítsa ezt a kódot az [egyéni képességek felületének](cognitive-search-custom-skill-interface.md)követéséhez. Módosítsa a kódot a következő tartalommal:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

A **transform_value** metódus egyetlen rekordon hajt végre műveletet. Az adott igényeknek megfelelően módosíthatja a módszert. Ne felejtse el megtenni a szükséges bemeneti ellenőrzéseket, és adja vissza az előállított hibákat és figyelmeztetéseket, ha a művelet nem fejeződött be a rekordhoz.

### <a name="debug-your-code-locally"></a>A kód hibakeresése helyileg

A Visual Studio Code megkönnyíti a kód hibakeresését. Nyomja le az F5 billentyűt, vagy lépjen a **hibakeresés** menüre, és válassza a **hibakeresés indítása** lehetőséget.

A kód bármely töréspontját beállíthatja úgy, hogy az "F9" elemre kattint az adott sorban.

A hibakeresés megkezdése után a függvény helyileg fog futni. Olyan eszközt használhat, mint a Poster vagy a Hegedűs, hogy kiadja a kérést a localhost-nak. Jegyezze fel a helyi végpont helyét a terminál ablakban. 

## <a name="publish-your-function"></a>A függvény közzététele

Ha elégedett a funkció működésével, közzéteheti.

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza a **telepítés függvényalkalmazás.**.. lehetőséget. 

1. Válassza ki azt az Azure-előfizetést, amelyre telepíteni szeretné az alkalmazást.

1. Válassza **az + új Függvényalkalmazás létrehozása az Azure-ban** lehetőséget.

1. Adja meg a Function alkalmazás globálisan egyedi nevét.

1. Válassza a Python-verzió (Python 3.7. x működik ehhez a függvényhez) lehetőséget.

1. Válassza ki az új erőforrás helyét (például: USA 2. nyugati régiója).

Ezen a ponton a szükséges erőforrások az Azure-előfizetésben lesznek létrehozva az új Azure-függvény üzemeltetéséhez az Azure-ban. Várjon, amíg az üzembe helyezés befejeződik. A kimeneti ablakban megjelenik a telepítési folyamat állapota.

1. A [Azure Portal](https://portal.azure.com)navigáljon az **összes erőforráshoz** , és keresse meg a neve alapján közzétett függvényt. Ha elnevezte az **összefűzést**, válassza ki az erőforrást.

1. Kattintson a **</> függvény URL-címének beolvasása** gombra. Ez lehetővé teszi az URL-cím másolását a függvény meghívásához.

## <a name="test-the-function-in-azure"></a>A függvény tesztelése az Azure-ban

Most, hogy rendelkezik az alapértelmezett gazdagép kulccsal, tesztelje a függvényt a következőképpen:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Kérelem törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Ebben a példában ugyanazt az eredményt kell megadnia, amelyet korábban a függvény a helyi környezetben való futtatásakor látott.

## <a name="connect-to-your-pipeline"></a>Kapcsolódás a folyamathoz

Most, hogy már rendelkezik egy új egyéni képességgel, hozzáadhatja a készségkészlet. Az alábbi példa azt mutatja be, hogyan hívhatja meg a képességet, hogy összefűzse a dokumentum címét és szerzőjét egyetlen olyan mezőbe, amelyet merged_title_author hívunk meg. Cserélje le az `[your-function-url-here]` címet az új Azure-függvény URL-címére.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Következő lépések
Gratulálunk! Létrehozta az első egyéni szaktudását. Mostantól ugyanezt a mintát követheti saját egyéni funkcióinak hozzáadásával is. További információért kattintson az alábbi hivatkozásokra.

+ [Energiaellátási készségek: az egyéni képességek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Egyéni képesség hozzáadása egy mesterséges intelligencia-bővítési folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)