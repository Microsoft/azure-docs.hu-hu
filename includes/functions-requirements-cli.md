---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7a390c0d19a37ea18f9eac8636683ec35ecbc844
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880697"
---
## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd elég:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ A [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846-os vagy újabb verziója.
::: zone-end  
::: zone pivot="programming-language-python"
+ A Azure Functions Core Tools Telepített Python-verziónak megfelelő Azure Functions Core Tools verziója:

   | Python-verzió | Core Tools-verzió |
   | -------------- | ------------------ |
   | Python 3.8     | [3.x verzió](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [2.7.1846-os vagy újabb verzió](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS és Maintenance LTS verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bites)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bites)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bites)](https://www.python.org/downloads/release/python-368/), amelyeket a Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ A [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ A [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója. 

+ [Apache Maven,](https://maven.apache.org)3.0-s vagy újabb verzió.

::: zone-end
::: zone pivot="programming-language-other"
+ Fejlesztői eszközök az Ön által használt nyelvhez. Ez az oktatóanyag az [R programozási nyelvet](https://www.r-project.org/) használja példaként.
::: zone-end