---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431846"
---
|Nyelv                                 |1. x         |2. x| 3. x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET-keretrendszer 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1)<br/>[Előzetes verzió (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (6. csomópont)|GA (csomópont 10 & 8)| GA (12. csomópont & 10)<br />Előzetes verzió (14. csomópont) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET-keretrendszer 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/A|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/A|GA (PowerShell mag 6)| GA (PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/A|GA (Python 3,7 & 3,6)| GA (Python 3,8, 3,7, & 3,6) <br/> Előzetes verzió (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/A|<sup>2</sup> . ga| <sup>2</sup> . ga |


<sup>1</sup> . Előfordulhat, hogy a .net-es verziójú függvénytár-alkalmazások 3,1 a (z) 2. x verziójú futtatókörnyezetet futtatják a .net Core 2. x kompatibilitási módban. További információ: [functions v2. x megfontolások](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> támogatott a transpiling használatával a javascripthez.

A támogatott nyelvi verziókkal kapcsolatos további információkért tekintse meg a Language-specifikus fejlesztői útmutató című cikket.   
További információ a nyelvi támogatás tervezett változásairól: [Azure-ütemterv](https://azure.microsoft.com/roadmap/?tag=functions).
