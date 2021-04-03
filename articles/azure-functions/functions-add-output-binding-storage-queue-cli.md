---
title: Azure Functions összekötése az Azure Storage-hoz a parancssori eszközök használatával
description: Megtudhatja, hogyan csatlakoztathatja a Azure Functionst egy Azure Storage-várólistához egy kimeneti kötés hozzáadásával a parancssori projekthez.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94920202"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure Functions összekötése az Azure Storage-hoz a parancssori eszközök használatával

Ebben a cikkben az Azure Storage-üzenetsor integrálását az előző rövid útmutató cikkében létrehozott függvény-és Storage-fiókkal végezheti el. Ezt az integrációt olyan *kimeneti kötés* használatával érheti el, amely http-kérésből adatokat ír a várólistában lévő üzenetbe. Ennek a cikknek a végrehajtásával az előző rövid útmutató néhány USD százalékán felül nem jár további költségekkel. További információ a kötésekről: [Azure functions eseményindítók és kötések fogalmai](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

::: zone pivot="programming-language-csharp"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-csharp.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-node.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end   
::: zone pivot="programming-language-java"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-java.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-typescript.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end   
::: zone pivot="programming-language-python"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-python.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Mielőtt elkezdené, be kell fejeznie a cikket, gyors [útmutató: Azure functions projekt létrehozása a parancssorból](create-first-function-cli-powershell.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

További információ a kötések részleteiről: [Azure functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md) és [üzenetsor kimenetének konfigurálása](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötés használatához

Ha a várólista-kötés definiálva van, most frissítheti a függvényt, hogy megkapja a `msg` kimeneti paramétert, és üzeneteket írjon a várólistába.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Figyelje meg, hogy *nem* kell kódot írnia a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. Az összes ilyen integrációs feladat kényelmesen kezelhető a Azure Functions futtatókörnyezetben és a várólista kimeneti kötésében.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-várólistában

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>A projekt újbóli üzembe helyezése az Azure-ban

Most, hogy ellenőrizte a helyileg, hogy a függvény üzenetet írt az Azure Storage-várólistába, a projekt újbóli üzembe helyezésével frissítheti az Azure-on futó végpontot.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
A *LocalFunctionsProj* mappában használja a parancsot a [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) projekt újbóli üzembe helyezéséhez, `<APP_NAME>` az alkalmazás nevével.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

A helyi projekt mappában a következő Maven-paranccsal tegye közzé újra a projektet:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Ellenőrzés az Azure-ban

1. Ahogy az előző rövid útmutatóban is, az újratelepített függvény teszteléséhez használjon böngészőt vagy CURL-t.

    # <a name="browser"></a>[Böngésző](#tab/browser)
    
    Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions` . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

    ![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **Meghívási URL-címmel**, és illessze be a paramétert `&name=Functions` . A parancs kimenetének a "Hello functions" szövegnek kell lennie.
    
    ![A függvény kimenete az Azure-on a CURL használatával fut](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Az előző szakaszban leírtak szerint vizsgálja meg újra a tárolási várólistát annak ellenőrzéséhez, hogy az tartalmazza-e a várólistára írt új üzenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett, a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP által aktivált függvényt az adattárolási várólistába való íráshoz. Most már többet is megtudhat a függvények parancssorból való fejlesztéséről a Core Tools és az Azure CLI használatával:

+ [Az Azure Functions Core Tools használata](functions-run-local.md)  

+ [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Példák a teljes körű függvények projektjeire a C#-ban](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Functions C# – fejlesztői dokumentáció](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Példák a teljes körű függvények projektjeire a JavaScriptben](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript fejlesztői útmutató](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Példák a teljes körű Function-projektekre az írógéppel](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions írógéppel – fejlesztői útmutató](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Példák a Pythonban elérhető teljes körű függvények projektjeire](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Functions Python fejlesztői útmutató](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Példák a PowerShellben elérhető teljes függvények projektjeire](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions PowerShell fejlesztői útmutató](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
