---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: f102a5dd5b7dccba6643176d06d17a2a65171c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904072"
---
## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ A [Azure functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) verziója 2.7.1846 vagy újabb verzió.
::: zone-end  
::: zone pivot="programming-language-python"
+ Az Azure Functions Core Tools verziója, amely megfelel a telepített Python-verziónak:

   | Python-verzió | A Core Tools verziója |
   | -------------- | ------------------ |
   | Python 3.8     | [3. x verzió](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [2.7.1846 vagy újabb verzió](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64 bites)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), amelyet a Azure functions támogat. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ A [.NET Core SDK 3,1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ A [Java fejlesztői csomag](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója. 

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

::: zone-end
::: zone pivot="programming-language-other"
+ Fejlesztői eszközök a használt nyelvhez. Ez az oktatóanyag az [R programozási nyelvet](https://www.r-project.org/) használja példaként.
::: zone-end