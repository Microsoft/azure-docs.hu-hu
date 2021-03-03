---
title: 'Rövid útmutató: QnA Maker ügyféloldali kódtár a Rubyhoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a Ruby QnA Maker ügyféloldali függvénytárát.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: cfdd1595ffb21b0f567f5299ebd6257b3462b671
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730947"
---
A Ruby QnA Maker ügyféloldali kódtára a következőre használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése
* Tudásbázis törlése

[Könyvtár forráskódja (szerzői műveletek)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated)  |  [Könyvtár forráskódja (futtatókörnyezet)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated)  |  [Csomag (szerzői műveletek)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker)  |  [Csomag (futtatókörnyezet)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime)  |  [Ruby-minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2. x](https://www.ruby-lang.org/)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz** lehetőséget.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás QnA Maker APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-libraries"></a>Az ügyféloldali kódtárak telepítése

A Ruby telepítése után az ügyfél-kódtárakat a következővel telepítheti:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Új Ruby-alkalmazás létrehozása

Hozzon létre egy nevű új fájlt `quickstart.rb` , és importálja a következő könyvtárakat.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés** területen találhatók.
> A Tudásbázis létrehozásához a teljes kulcsra van szükség. Csak az erőforrás nevére van szükség a végpontból. A formátum `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](../../../key-vault/general/overview.md) például biztonságos kulcstároló-tárolót biztosít.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Objektummodell

QnA Maker két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../how-to/use-active-learning.md)részeként).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) objektum, amely a kulcsot tartalmazó MsRest:: ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után használja az ügyfél [Tudásbázis](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) -tulajdonságának metódusait a Tudásbázis létrehozásához, kezeléséhez és közzétételéhez.

Az azonnali műveletekhez a metódus általában az eredményt adja vissza, ha van ilyen. A hosszú ideig futó műveletek esetében a válasz egy [művelet](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) objektum. A [](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) `operation.operation_id` [kérelem állapotának](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb)meghatározásához hívja meg a Operations.get_details metódust a következő értékkel:.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

A futásidejű QnA Maker ügyfél egy [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) objektum.

Miután közzétette a tudásbázist az authoring ügyfél használatával, a futtatókörnyezet [Runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) metódusával választ kaphat a tudásbázistól.

A hívásakor `generate_answer` adjon meg egy kivonatot a `custom_headers` választható paraméterhez. A kivonatnak tartalmaznia kell egy kulcsot `Authorization` és egy értéket `EndpointKey YOUR_ENDPOINT_KEY` . A YOUR_ENDPOINT_KEY értékeként az authoring Client használatával hívhatja meg a [endpoint_keys. Get _keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32).

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

Ügyfelet hoz létre az authoring Endpoint és az előfizetés kulcsával.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz** használja a [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok** esetében használja a [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek** esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

Hívja meg a [create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) metódust, majd adja át a `operation_id` visszaadott művelet tulajdonságát a [Operations.get_details](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora a Tudásbázis AZONOSÍTÓját adja vissza.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis meghívásával, a Tudásbázis-AZONOSÍTÓban és egy [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) objektumban lehet frissíteni [.](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) Ez az objektum a következőket tartalmazhatja:
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [frissítése](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Adja át a `operation_id` visszaadott művelet tulajdonságát a [Operations.get_details](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [Tudásbázis. download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) metódus használatával töltse le az adatbázist a [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [Tudásbázis. publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Lekérdezés futásidejű kulcsának beolvasása

Miután közzétette a tudásbázist, szüksége lesz a futásidejű végponti kulcsra az ismeretek lekérdezéséhez. Ez nem ugyanaz, mint az authoring Client létrehozásához használt előfizetési kulcs.

Használja a [endpoint_keys. Get _keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) metódust egy [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) objektum beolvasásához.

Használja az objektumban visszaadott kulcs-tulajdonságok egyikét a Tudásbázis lekérdezéséhez.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

Hozzon létre egy [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) , amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

A QnAMakerRuntimeClient segítségével választ kaphat az ismeretekből, vagy új, javasolt kérdéseket küldhet az [Active learning](../concepts/active-learning-suggestions.md)tudásbázisában.

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Válasz létrehozása közzétett Tudásbázisból a RuntimeClient. Runtime. generateAnswer metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a QueryDTO. Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Ez egy egyszerű példa egy Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist az Tudásbázis [. Delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) metódussal a TUDÁSBÁZIS-azonosító paraméterének használatával.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet AZONOSÍTÓját a lekérdezésben (az újrapróbálkozási logikával).

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>Az alkalmazás futtatása

Itt látható az alkalmazás fő metódusa.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Futtassa az alkalmazást a Gyorsindítás-fájl Ruby parancsával.

```console
ruby quickstart.rb
```

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb)található.
