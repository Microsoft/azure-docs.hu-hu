---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-modellek érvényesítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303761"
---
> [!TIP]
> A modell létrehozása után ajánlott a modelleket offline állapotba helyezni, mielőtt feltölti őket az Azure Digital Twins-példányba.

A modell dokumentumainak érvényesítéséhez egy nyelvtől független minta áll rendelkezésre, amely biztosítja, hogy a DTDL helyes legyen, mielőtt feltölti azt a példányba. Itt található: [**DTDL validator minta**](/samples/azure-samples/dtdl-validator/dtdl-validator).

A DTDL-érvényesítő minta egy .NET DTDL-elemző könyvtárra épül, amely ügyféloldali kódtárként érhető el a NuGet-ben: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). A könyvtárat közvetlenül is használhatja a saját ellenőrzési megoldás megtervezéséhez. Az elemző függvénytár használatakor ügyeljen arra, hogy olyan verziót használjon, amely kompatibilis az Azure Digital ikrek által futtatott verziójával. Jelenleg ez a verzió *3.12.4*.

További információ az érvényesítő mintáról és az elemző könyvtárról, beleértve a használati példákat is, a [*útmutatóban: modellek elemzése és érvényesítése*](../articles/digital-twins/how-to-parse-models.md).