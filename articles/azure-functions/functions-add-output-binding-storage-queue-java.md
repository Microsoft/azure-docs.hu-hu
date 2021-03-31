---
title: A Java-függvény csatlakoztatható az Azure Storage-hoz
description: Megtudhatja, hogyan csatlakoztatható a HTTP-triggert használó Java-függvények az Azure Storage-hoz egy üzenetsor-tároló kimeneti kötésének használatával.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422654"
---
# <a name="connect-your-java-function-to-azure-storage"></a>A Java-függvény csatlakoztatható az Azure Storage-hoz

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan integrálhatja az előző rövid útmutató [cikkében](./create-first-function-cli-java.md?tabs=bash,browser) létrehozott függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A kapcsolódás egyszerűbbé tételéhez használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű alkalmazás-beállításban van tárolva `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt végezze el a [Java rövid útmutató 1. részében](./create-first-function-cli-java.md?tabs=bash,browser)ismertetett lépéseket.

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Most már készen áll az új kimeneti kötés helyi kipróbálására.

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Ahogy korábban is, használja a következő parancsot a projekt felépítéséhez és a functions Runtime helyi elindításához:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Mivel a host.json engedélyezte a bővítményi csomagokat, a rendszer letölti és telepítette a [tárolási kötési bővítményt](functions-bindings-storage-blob.md#add-to-your-functions-app) az indítás során, valamint a többi Microsoft-kötési bővítményt is.

Ahogy korábban is, aktiválja a függvényt a parancssorból a cURL használatával egy új terminál ablakban:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Ebben az esetben a kimeneti kötés is létrehoz egy nevű várólistát a `outqueue` Storage-fiókban, és hozzáadja az ugyanezt a karakterláncot tartalmazó üzenetet.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizheti, hogy hozzá lett-e adva üzenet. Az üzenetsor a [Microsoft Azure Storage Explorer][Azure Storage Explorer] vagy a [Azure Portal](https://portal.azure.com)használatával is megtekinthető.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>A projekt újbóli üzembe helyezése 

A közzétett alkalmazás frissítéséhez futtassa újra a következő parancsot:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Újra használhatja a cURLot az üzembe helyezett függvény teszteléséhez. Ahogy korábban is, adja át a `AzureFunctions` post kérelem törzsében lévő értéket az URL-címre, az alábbi példában látható módon:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Megvizsgálhatja a tárolási üzenetsor üzenetét](#query-the-storage-queue) annak ellenőrzéséhez, hogy a kimeneti kötés a várt módon létrehoz egy új üzenetet a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP-triggert a függvényt, hogy az adatait egy tárolási várólistába írja. Ha többet szeretne megtudni a Java-Azure Functions fejlesztéséről, tekintse meg a [Java fejlesztői útmutató Azure functions](functions-reference-java.md) és [Azure functions eseményindítók és kötések](functions-triggers-bindings.md)című témakört. A Java-funkciók teljes körű funkciós projektjeivel kapcsolatos példákért tekintse meg a [Java functions mintákat](/samples/browse/?products=azure-functions&languages=Java). 

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
