---
title: 'Gyors útmutató: QnA Maker ügyféloldali kódtár a Node.js'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a Node.js QnA Maker ügyféloldali kódtár használatának első lépéseit.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0d0522be53ec9b1008f18725308c91a19ee24156
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609412"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A Node.js a QnA Maker ügyféloldali kódtár használatával:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Dokumentáció](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A Node.js a QnA Maker ügyféloldali kódtár használatával:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Dokumentáció](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portalban a szerzői kulcs és az erőforrás beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Az alkalmazásnak a QnA Maker APIhoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcs-és erőforrás-nevére. A kulcs és az erőforrás nevét a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez.
    * Megjegyzés: Ügyeljen arra, hogy jelölje be a **felügyelt** jelölőnégyzetet.
    * A QnA Maker erőforrás üzembe helyezése után válassza az **Ugrás erőforráshoz** lehetőséget. Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

---

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Futtassa a `npm init -y` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` .

```console
npm init -y
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

Telepítse a következő NPM-csomagokat:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

Telepítse a következő NPM-csomagokat:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

Az alkalmazás `package.json` fájlját a rendszer a függőségekkel frissíti.

Hozzon létre egy index.js nevű fájlt, és importálja a következő könyvtárakat:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Hozzon létre egy változót az erőforrás Azure-kulcsához és az erőforrás nevéhez.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

- Az előfizetési kulcsot és a szerzői kulcsot is felhasználjuk. A kulcs létrehozásával kapcsolatos további részletekért kövesse [a QnA Maker kulcsait](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **kulcsok és végpontok** lapra, majd az **Erőforrás-kezelés** elemre, és keresse meg a szerzői (előfizetési) kulcsot és a QnA Maker végpontot.

 ![QnA Maker szerzői végpont](../media/keys-endpoint.png)

- A QNA_MAKER_RUNTIME_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.azurewebsites.net` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **sablon exportálása** lap **automatizálás** területén a futásidejű végpont megkereséséhez.

 ![QnA Maker futtatókörnyezet végpontja](../media/runtime-endpoint.png)
   
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

- Az előfizetési kulcsot és a szerzői kulcsot is felhasználjuk. A kulcs létrehozásával kapcsolatos további részletekért kövesse [a QnA Maker kulcsait](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker-erőforrást. Kattintson a **kulcsok és végpontok** lapra, majd az **Erőforrás-kezelés** elemre, és keresse meg a szerzői (előfizetési) kulcsot és a QnA Maker végpontot.

 ![QnA Maker szerzői végpont](../media/keys-endpoint.png)
 
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../how-to/use-active-learning.md)részeként).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) a következő objektummodell-modellt használja:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez, letöltéséhez és lekérdezéséhez szükséges objektum.

---

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) objektum, amely hitelesíti az Azure-t a kulcsát tartalmazó hitelesítő adataival.

Miután létrehozta az ügyfelet [, a Tudásbázis használatával hozza](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient#knowledgebase) létre, kezelje és tegye közzé a tudásbázist.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. A [kérelem állapotának](/javascript/api/@azure/cognitiveservices-qnamaker/operation)meghatározásához hívja meg a [Client. Operations. getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations#getdetails-string--msrest-requestoptionsbase-) METÓDUSt a műveleti azonosítóval.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

Az előrejelzési QnA Maker ügyfél egy QnAMakerRuntimeClient objektum, amely a Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t, amely tartalmazza az előrejelzési futtatókörnyezet kulcsát, amelyet a szerzői ügyfél hívásakor, az [ügyféltől kapott vissza. EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys#getkeys-msrest-requestoptionsbase-) a Tudásbázis közzététele után.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A QnA Maker felügyelt erőforrásokhoz nincs szükség a QnAMakerRuntimeClient objektum használatára. Ehelyett közvetlenül a [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) objektumon hívja meg a [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) .

---

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket az QnA Maker .NET-hez készült ügyféloldali kódtár használatával:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

* [A szerzői ügyfél hitelesítése](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Lekérdezés futásidejű kulcsának beolvasása](#get-query-runtime-key)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)
* [A lekérdezési futtatókörnyezet ügyfelének hitelesítése](#authenticate-the-runtime-for-generating-an-answer)
* [Válasz létrehozása a Tudásbázisból](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

* [A szerzői ügyfél hitelesítése](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)
* [Válasz létrehozása a Tudásbázisból](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy ServiceClientCredentials objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) objektum létrehozásához.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz** használja a [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok** esetében használja a [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek** esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

A létrehozási lépés a tudásbázishoz tartozó tulajdonságokat is tartalmazza:
* `defaultAnswerUsedForExtraction` -Ha nem talál választ, a rendszer a visszaadott értéket adja vissza
* `enableHierarchicalExtraction` -a kinyert QnA-párok közötti gyors kapcsolatok automatikus létrehozása
* `language` – Amikor létrehoz egy erőforrás első tudásbázisát, állítsa be a nyelvet a Azure Search indexben való használatra.

Hívja meg a [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#create-createkbdto--servicecallback-operation--) metódust a Tudásbázis információi alapján. A Tudásbázis adatai alapvetően egy JSON-objektum.

Ha a Create metódus visszatér, adja át a visszaadott művelet AZONOSÍTÓját a [wait_for_operation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez. A wait_for_operation metódus a művelet befejeződése után tér vissza. Elemezheti a `resourceLocation` visszaadott művelet fejlécének értékét az új Tudásbázis-azonosító lekéréséhez.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Győződjön meg arról, hogy a [`wait_for_operation`](#get-status-of-an-operation) fenti kódban hivatkozott függvényt is tartalmazza, hogy sikeresen létrehozzon egy tudásbázist.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto) használatával frissítheti, amely a DTO objektumok [hozzáadását](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#add), [frissítését](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#update)és [törlését](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#deleteproperty) tartalmazza a [frissítési](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#update-string--updatekboperationdto--msrest-requestoptionsbase-) metódushoz. A DTO is alapvetően JSON-objektumok. A [wait_for_operation](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Győződjön meg arról, hogy a [`wait_for_operation`](#get-status-of-an-operation) fenti kódban hivatkozott függvény belefoglalása a Tudásbázis sikeres frissítéséhez.

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#download-string--models-environmenttype--msrest-requestoptionsbase-) módszer használatával letöltheti az adatbázist a [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#publish-string--msrest-requestoptionsbase-) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé. A HTTP-válasz kódjának ellenőrzésével ellenőrizze, hogy a közzététel sikeres volt-e.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Tudásbázis lekérdezése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Lekérdezés futásidejű kulcsának beolvasása

Miután közzétette a tudásbázist, szüksége lesz a lekérdezés futásidejű kulcsára a futtatókörnyezet lekérdezéséhez. Ez nem ugyanaz a kulcs, amely az eredeti ügyfél-objektum létrehozásához használatos.

A [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto) osztály beszerzéséhez használja a [EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys) metódust.

Használja az objektumban visszaadott kulcs-tulajdonságok egyikét a Tudásbázis lekérdezéséhez.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

Hozzon létre egy QnAMakerRuntimeClient, amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

A QnAMakerRuntimeClient segítségével választ kaphat az ismeretekből, vagy új, javasolt kérdéseket küldhet az [Active learning](../concepts/active-learning-suggestions.md)tudásbázisában.

### <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Válasz létrehozása közzétett Tudásbázisból a RuntimeClient. Runtime. generateAnswer metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Egy közzétett Tudásbázis válaszának létrehozása a QnAMakerClient. Tudásbázis. generateAnswer metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Ez egy egyszerű példa a Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#deletemethod-string--msrest-requestoptionsbase-) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](/javascript/api/@azure/cognitiveservices-qnamaker/operations) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](/javascript/api/@azure/cognitiveservices-qnamaker/operation#operationid) a lekérdezésben (az újrapróbálkozási logikával).

Az újrapróbálkozási logikát az alábbi _delayTimer_ hívásával szimulálhatja. Cserélje le ezt a saját újrapróbálkozási logikára.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node index.js` paranccsal az alkalmazás könyvtárából.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)található.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)található.

---
