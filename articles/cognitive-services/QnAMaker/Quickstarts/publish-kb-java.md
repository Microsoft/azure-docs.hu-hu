---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, Java – QnA Maker'
description: Ez a Java REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 8e2e902e0563e0f4ae8c0c3d0dc795a8260c62db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351163"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Java használatával

Ez a REST-alapú rövid útmutató végigvezeti a Tudásbázis (KB) programozott közzétételének lépésein. A Publishing leküldi a Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegési robotban.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support) (Java fejlesztői készlet, Standard Edition)
* Ez a példa az Apache [http-ügyfelet](https://hc.apache.org/httpcomponents-client-ga/) használja a http-összetevőkből. Hozzá kell adnia a következő Apache HTTP-ügyféloldali kódtárakat a projekthez:
    * httpclient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.
* QnA Maker Tudásbázis-azonosító a `kbid` lekérdezési karakterlánc paraméterben az alább látható módon található.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-Java** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy nevű új fájlt `PublishKB.java` .

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Az osztály felső részén `PublishKB.java` adja hozzá a következő sorokat a szükséges függőségek a projekthez való hozzáadásához:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="dependencies":::

## <a name="create-publishkb-class-with-main-method"></a>PublishKB osztály létrehozása a Main metódussal

A függőségek után adja hozzá a következő osztályt:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **Main** metódusban adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját értékeire.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>POST kérelem hozzáadása a Tudásbázis közzétételéhez

A szükséges konstansok után adja hozzá a következő kódot, amely HTTPS-kérést küld a QnA Maker API egy Tudásbázis közzétételéhez, és fogadja a választ:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="post":::

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot a parancssorból. A szolgáltatás automatikusan elküldi a kérést a QnA Maker APInak, majd a konzol ablakba fog nyomtatni.

1. Hozza létre a fájlt:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)