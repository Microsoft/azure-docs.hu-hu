---
title: 'Rövid útmutató: QnA Maker a Pythonhoz készült ügyféloldali kódtár'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a QnA Maker a Pythonhoz készült ügyféloldali kódtárat.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 7c897b524f7038f4ff7b18e273202bd02886c0e8
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609521"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A Pythonhoz készült QnA Maker ügyféloldali kódtára a következőre használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Dokumentáció](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/)  |  [Python-minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A Pythonhoz készült QnA Maker ügyféloldali kódtára a következőre használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Dokumentáció](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Python-minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez.
    * Megjegyzés: Ügyeljen arra, hogy jelölje be a **felügyelt** jelölőnégyzetet.
    * A QnA Maker erőforrás üzembe helyezése után válassza az **Ugrás erőforráshoz** lehetőséget. Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

---

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új, nevű Python `quickstart-file.py` -fájlt, és importálja a következő könyvtárakat.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

- Az előfizetési kulcsot és a szerzői kulcsot is felhasználjuk. A kulcs létrehozásával kapcsolatos további részletekért kövesse [a QnA Maker kulcsait](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **kulcsok és végpontok** lapra, majd az **Erőforrás-kezelés** elemre, és keresse meg a szerzői (előfizetési) kulcsot és a QnA Maker végpontot.

 ![QnA Maker szerzői végpont](../media/keys-endpoint.png)

- A QNA_MAKER_RUNTIME_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.azurewebsites.net` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **sablon exportálása** lap **automatizálás** területén a futásidejű végpont megkereséséhez.

 ![QnA Maker futtatókörnyezet végpontja](../media/runtime-endpoint.png)
   
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

- Az előfizetési kulcsot és a szerzői kulcsot is felhasználjuk. A kulcs létrehozásával kapcsolatos további részletekért kövesse [a QnA Maker kulcsait](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **kulcsok és végpontok** lapra, majd az **Erőforrás-kezelés** elemre, és keresse meg a szerzői (előfizetési) kulcsot és a QnA Maker végpontot.

 ![QnA Maker szerzői végpont](../media/keys-endpoint.png)
 
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../how-to/use-active-learning.md)részeként).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) a következő objektummodell-modellt használja:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez, letöltéséhez és lekérdezéséhez szükséges objektum.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után a [Tudásbázis tulajdonság használatával](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) hozhatja létre, kezelheti és teheti közzé a tudásbázist.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. A [kérelem állapotának](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype)meghatározásához hívja meg a [Operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-) METÓDUSt a műveleti azonosítóval.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

Az előrejelzési QnA Maker ügyfél egy olyan `QnAMakerRuntimeClient` objektum, amely a Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t [ EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) a Tudásbázis közzétételekor.

A `generate_answer` metódus használatával kaphat választ a lekérdezési futtatókörnyezettől.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A QnA Maker felügyelt erőforrásokhoz nincs szükség a QnAMakerRuntimeClient objektum használatára. Ehelyett a [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) közvetlenül a [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) objektumon hívja meg.

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy CognitiveServicesCredentials objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) objektum létrehozásához.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

Az ügyfél-objektum segítségével szerezzen be egy [Tudásbázis-műveleti](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) objektumot.

A Tudásbázis a [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz** használja a [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok** esetében használja a [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek** esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

Hívja meg a [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) metódust, majd adja át a visszaadott művelet azonosítóját a [Operations. getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora visszaadja a Tudásbázis AZONOSÍTÓját a MonitorOperation válaszból.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

A [`_monitor_operation`](#get-status-of-an-operation) Tudásbázis sikeres létrehozásához győződjön meg arról, hogy a fenti kódban hivatkozott függvény belefoglalása.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) használatával frissítheti, amely a DTO objektumok [hozzáadását](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [frissítését](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)és [törlését](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) tartalmazza a [frissítési](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) metódushoz. A [Operation. getDetail](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Győződjön meg arról, hogy a [`_monitor_operation`](#get-status-of-an-operation) fenti kódban hivatkozott függvény belefoglalása a Tudásbázis sikeres frissítéséhez.

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) módszer használatával letöltheti az adatbázist a [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Tudásbázis lekérdezése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Lekérdezés futásidejű kulcsának beolvasása

Miután közzétette a tudásbázist, szüksége lesz a lekérdezés futásidejű kulcsára a futtatókörnyezet lekérdezéséhez. Ez nem ugyanaz a kulcs, amely az eredeti ügyfél-objektum létrehozásához használatos.

A [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) osztály beszerzéséhez használja a [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) metódust.

Használja az objektumban visszaadott kulcs-tulajdonságok egyikét a Tudásbázis lekérdezéséhez.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

Hozzon létre egy [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient) , amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

A QnAMakerRuntimeClient segítségével választ kaphat az ismeretekből, vagy új, javasolt kérdéseket küldhet az [Active learning](../concepts/active-learning-suggestions.md)tudásbázisában.

### <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Egy közzétett Tudásbázis válaszának létrehozása a QnAMakerRuntimeClient.runtime.generate_answer metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Egy közzétett Tudásbázis válaszának létrehozása a [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Ez egy egyszerű példa a Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet AZONOSÍTÓját a lekérdezésben (az újrapróbálkozási logikával).

A következő _setTimeout_ hívásával szimulálható az aszinkron kód. Cserélje le ezt az újrapróbálkozási logikával.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési fájl Python parancsával.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)található.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)található.

---
