---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-modellek érvényesítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558121"
---
> [!TIP]
> A modell létrehozása után ajánlott a modelleket offline állapotba helyezni, mielőtt feltölti őket az Azure Digital Twins-példányba.

A modell dokumentumainak érvényesítéséhez egy nyelvtől független minta áll rendelkezésre, amely biztosítja, hogy a DTDL helyes legyen, mielőtt feltölti azt a példányba. Itt található: [**DTDL validator minta**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* A DTDL-érvényesítő minta egy .NET DTDL-elemző könyvtárra épül, amely ügyféloldali kódtárként érhető el a NuGet-ben: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). A könyvtárat közvetlenül is használhatja a saját ellenőrzési megoldás megtervezéséhez. Az elemző függvénytár használatakor ügyeljen arra, hogy olyan verziót használjon, amely kompatibilis az Azure Digital ikrek által futtatott verziójával. Jelenleg ez a verzió *3.12.4*.

További információ az érvényesítő mintáról és az elemző könyvtárról, beleértve a használati példákat is, a [*útmutatóban: modellek elemzése és érvényesítése*](../articles/digital-twins/how-to-parse-models.md).