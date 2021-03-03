---
title: 'Rövid útmutató: QnA Maker ügyféloldali kódtár a Go-hoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el a QnA Maker ügyféloldali függvénytárát.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 39df309a4c4c9c1db181a6081afe00498d449957
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730972"
---
A ugráshoz használja a QnA Maker ügyféloldali függvénytárat:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése
* Tudásbázis törlése

[Referenciák (szerzői műveletek)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker)  |  [Hivatkozás (futtatókörnyezet)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime)  |  [Könyvtár forráskódja (szerzői műveletek)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker)  |  [Könyvtár forráskódja (futtatókörnyezet)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Ugrás](https://golang.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-go-application"></a>Új Go-alkalmazás létrehozása

Hozzon létre egy nevű új fájlt `kb_sample.go` , és importálja a következő könyvtárakat.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés** területen találhatók.
> A Tudásbázis létrehozásához a teljes kulcsra van szükség. Csak az erőforrás nevére van szükség a végpontból. A formátum `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>Objektummodell

QnA Maker két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../how-to/use-active-learning.md)részeként).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

Használja a [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient) a Tudásbázis létrehozásához, frissítéséhez, közzétételéhez, letöltéséhez és törléséhez. Ezeket a műveleteket a következő részben találja.

A [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient) használatával megtekintheti a hosszan futó műveletek állapotát, például a tudásbázisok létrehozását és frissítését.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

Miután közzétette a tudásbázist, használja a [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) a Tudásbázis lekérdezéséhez. Ez a művelet a [Tudásbázis lekérdezésével](#query-the-knowledge-base)foglalkozik.

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz** használja a [QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok** esetében használja a [FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek** esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

Hívja meg a [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create) metódust, majd adja át a `operationId` visszaadott művelet tulajdonságát a [getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora a Tudásbázis AZONOSÍTÓját adja vissza.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-AZONOSÍTÓban és egy [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO) objektumban a [frissítés](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update) és az átadás meghívásával frissítheti. Ez az objektum a következőket tartalmazhatja:
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [frissítése](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

Adja át a `operationId` visszaadott művelet tulajdonságát a [getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download) módszer használatával letöltheti az adatbázist a [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>A Tudásbázis lekérdezése

Miután közzétette a tudásbázist, szüksége lesz a futásidejű végponti kulcsra az ismeretek lekérdezéséhez. Ez nem ugyanaz, mint az authoring Client létrehozásához használt előfizetési kulcs.

Hozzon létre egy [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient) , és a [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys) metódus használatával szerezzen be egy [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO) objektumot.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

Hozzon létre egy [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) , és hívja meg a [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer) metódust a Tudásbázis lekérdezéséhez.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

Ez egy egyszerű példa egy Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet AZONOSÍTÓját a lekérdezésben (az újrapróbálkozási logikával).

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>Hibakezelés

A következő kód bemutatja, hogyan kezelheti az SDK függvények által visszaadott hibákat.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>Az alkalmazás futtatása

Itt látható az alkalmazás fő metódusa.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

Futtassa az alkalmazást a Gyorsindítás-fájl go parancsával.

```console
go run kb_sample.go
```

A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/QnAMaker/sdk/kb_sample.go)
