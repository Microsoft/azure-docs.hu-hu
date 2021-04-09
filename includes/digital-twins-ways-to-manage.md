---
author: baanders
description: fájl belefoglalása az Azure digitális Twins-ba – a példány kezelésének módjai
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473657"
---
Ez a cikk bemutatja, hogyan végezheti el a különböző felügyeleti műveleteket az [Azure Digital Twins .net (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management)használatával. Ugyanezeket a felügyeleti hívásokat a következő témakörben ismertetett egyéb nyelvi SDK-kkal is elvégezheti [*: az Azure Digital Twins API-k és SDK*](../articles/digital-twins/how-to-use-apis-sdks.md)-k használatával.

> [!TIP] 
> Ne feledje, hogy minden SDK-módszer szinkron és aszinkron verziókban érhető el. A lapozási hívások esetében az aszinkron metódusok a `AsyncPageable<T>` szinkron verziók visszaadása után térnek vissza `Pageable<T>` .

Egy másik felügyeleti lehetőség a témakörben található Azure digitális ikrek [**REST API**](/rest/api/azure-digitaltwins/) -k hívása közvetlenül egy Rest-ügyfél, például a Poster használatával. Ennek módjáról a következő témakörben talál útmutatást [*: a kérelmek küldése a Poster használatával*](../articles/digital-twins/how-to-use-postman.md).

Végül az Azure Digital Twins **CLI** használatával is végrehajthatja ugyanezeket a kezelési műveleteket. Ha többet szeretne megtudni a parancssori felület használatáról, olvassa el a következő témakört [*: útmutató: az Azure digitális Twins parancssori*](../articles/digital-twins/how-to-use-cli.md)felületének használata.