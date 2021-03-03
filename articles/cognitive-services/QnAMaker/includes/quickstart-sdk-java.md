---
title: 'Gyors útmutató: QnA Maker a Javához készült ügyféloldali kódtár'
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a Javához készült QnA Maker ügyféloldali kódtárat.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 7c14391ca565105809666362a1c452d910140c64
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730951"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A Javához készült QnA Maker ügyféloldali kódtár a következőre használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  [Csomag](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A Javához készült QnA Maker ügyféloldali kódtár a következőre használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése egy Tudásbázisból
* Tudásbázis törlése

[Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker)  |  [Csomag](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez.
    * Megjegyzés: Ügyeljen arra, hogy jelölje be a **felügyelt** jelölőnégyzetet.
    * A QnA Maker erőforrás üzembe helyezése után válassza az **Ugrás erőforráshoz** lehetőséget. Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

---

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-libraries"></a>Az ügyféloldali kódtárak telepítése

A Java telepítése után a [MVN adattárból](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker)telepítheti az ügyféloldali kódtárakat a [Maven](https://maven.apache.org/) használatával.

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Hozzon létre egy nevű új fájlt `quickstart.java` , és importálja a következő könyvtárakat.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés** területen találhatók.

- Hozzon létre QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT és QNA_MAKER_RUNTIME_ENDPOINT nevű környezeti változókat az értékek tárolásához.
- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- A QNA_MAKER_RUNTIME_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés** területen találhatók.

- Hozzon létre QNA_MAKER_SUBSCRIPTION_KEY és QNA_MAKER_ENDPOINT nevű környezeti változót az értékek tárolásához.
- A QNA_MAKER_ENDPOINT értékének formátuma `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

QnA Maker két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../how-to/use-active-learning.md)részeként).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

QnA Maker a következő objektummodell-modellt használja:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez, letöltéséhez és lekérdezéséhez szükséges objektum.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) objektum, amely a kulcsot tartalmazó MsRest:: ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után használja az ügyfél [Tudásbázis](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) -tulajdonságának metódusait a Tudásbázis létrehozásához, kezeléséhez és közzétételéhez.

Az azonnali műveletekhez a metódus általában az eredményt adja vissza, ha van ilyen. A hosszú ideig futó műveletek esetében a válasz egy [művelet](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) objektum. A [](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) `operation.operationId` [kérelem állapotának](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java)meghatározásához hívja meg a getDetails metódust a megadott értékkel.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A futásidejű QnA Maker ügyfél egy [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) objektum.

Miután közzétette a tudásbázist az authoring Client használatával, a futásidejű ügyfél [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) metódusával kaphat választ a Tudásbázisból.

Egy futtatókörnyezet-ügyfelet a QnAMakerRuntimeManager meghívásával hozhat létre [. hitelesítés](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) és a futásidejű végponti kulcs átadása. A futásidejű végpont kulcsának beszerzéséhez használja az authoring ügyfelet a [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30)meghívásához.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A QnA Maker felügyelt erőforrásokhoz nincs szükség a QnAMakerRuntimeClient objektum használatára. Ehelyett közvetlenül a [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) objektumon hívja meg a [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) .

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

Ügyfelet hoz létre az authoring Endpoint és az előfizetés kulcsával.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz** használja a [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok** esetében használja a [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek** esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

Hívja meg a [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) metódust, majd adja át a `operationId` visszaadott művelet tulajdonságát a [getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora a Tudásbázis AZONOSÍTÓját adja vissza.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-AZONOSÍTÓban és egy [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java) objektumban a [frissítés](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) és az átadás meghívásával frissítheti. Ez az objektum a következőket tartalmazhatja:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [frissítése](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Adja át a `operationId` visszaadott művelet tulajdonságát a [getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) módszer használatával letöltheti az adatbázist a [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

Miután közzétett egy tudásbázist, szüksége lesz a futásidejű végponti kulcsra a Tudásbázis lekérdezéséhez. Ez nem ugyanaz, mint az authoring Client létrehozásához használt előfizetési kulcs.

[EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java) objektum beszerzéséhez használja a [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) metódust.

Hozzon létre egy futtatókörnyezet-ügyfelet a QnAMakerRuntimeManager meghívásával [. hitelesítse](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) a futásidejű végponti kulcsot a EndpointKeysDTO objektumból.

Egy közzétett Tudásbázisból származó válasz létrehozása a [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) metódus használatával. Ez a metódus fogadja a Tudásbázis-azonosítót és egy [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) objektumot.

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

Egy közzétett Tudásbázisból származó válasz létrehozása a [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) metódus használatával. Ez a metódus fogadja a Tudásbázis-azonosítót és egy [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) objektumot.

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Ez egy egyszerű példa egy Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet AZONOSÍTÓját a lekérdezésben (az újrapróbálkozási logikával).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Itt látható az alkalmazás fő metódusa.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Futtassa az alkalmazást az alábbiak szerint. Ez feltételezi, hogy az osztály neve, a függőségei pedig az `Quickstart` aktuális mappa alatti almappában találhatók `lib` .

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/version-1)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java)található.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/version-2)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java)található.

---
