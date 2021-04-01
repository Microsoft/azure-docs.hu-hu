---
title: Támogatott nyelvek a Azure Functions
description: Megtudhatja, hogy mely nyelvek támogatottak (GA), és melyek előzetes verzióban érhetők el, és hogyan bővíthetők a függvények fejlesztése más nyelvekre.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108551"
---
# <a name="supported-languages-in-azure-functions"></a>Támogatott nyelvek a Azure Functions

Ez a cikk a Azure Functions használatával használható nyelvekhez kínált támogatási szinteket ismerteti. Emellett ismerteti a függvények a natív módon nem támogatott nyelvekkel való létrehozására vonatkozó stratégiákat is.

## <a name="levels-of-support"></a>Támogatási szintek

Két támogatási szint létezik:

* **Általánosan elérhető (GA)** – teljes mértékben támogatott és jóváhagyott éles használatra.
* **Előzetes** verzió – még nem támogatott, de a jövőben várhatóan elérhető lesz a ga állapota.

## <a name="languages-by-runtime-version"></a>Nyelvek futásidejű verzió szerint 

[A Azure functions futtatókörnyezet három verziója](functions-versions.md) érhető el. Az alábbi táblázat azt mutatja, hogy az egyes futásidejű verziók Milyen nyelveket támogatnak.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Egyéni kezelők

Az egyéni kezelők olyan egyszerű webkiszolgálók, amelyek a Azure Functions gazdagépről kapnak eseményeket. Minden olyan nyelv, amely támogatja a HTTP primitíveket, egyéni kezelőt valósíthat meg. Ez azt jelenti, hogy az egyéni kezelők a nem hivatalosan támogatott nyelveken is létrehozhatnak függvényeket. További információ: [Azure functions egyéni kezelők](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Nyelvi bővíthetőség

A 2. x verziótól kezdődően a futtatókörnyezet [nyelvi bővíthetőséget](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)kínál. A 2. x futtatókörnyezetben található JavaScript-és Java-nyelvek ezzel a bővíthetőséggel vannak felépítve.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a függvények a támogatott nyelveken való fejlesztéséről, tekintse meg a következő forrásokat:

+ [C# Class Library – fejlesztői segédlet](functions-dotnet-class-library.md)
+ [C# parancsfájl-fejlesztői dokumentáció](functions-reference-csharp.md)
+ [Java fejlesztői segédlet](functions-reference-java.md)
+ [JavaScript fejlesztői segédlet](functions-reference-node.md)
+ [A PowerShell fejlesztői referenciája](functions-reference-powershell.md)
+ [Python fejlesztői segédlet](functions-reference-python.md)
+ [Írógéppel – fejlesztői segédlet](functions-reference-node.md#typescript)
