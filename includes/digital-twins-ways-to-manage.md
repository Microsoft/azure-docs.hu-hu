---
author: baanders
description: fájl belefoglalása az Azure digitális Twins-ba – a példány kezelésének módjai
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303760"
---
Ez a cikk bemutatja, hogyan végezheti el a különböző felügyeleti műveleteket az [Azure Digital Twins .net (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management)használatával. Ugyanezeket a felügyeleti hívásokat a következő témakörben ismertetett egyéb nyelvi SDK-kkal is elvégezheti [*: az Azure Digital Twins API-k és SDK*](../articles/digital-twins/how-to-use-apis-sdks.md)-k használatával.

> [!TIP] 
> Ne feledje, hogy minden SDK-módszer szinkron és aszinkron verziókban érhető el. A lapozási hívások esetében az aszinkron metódusok a `AsyncPageable<T>` szinkron verziók visszaadása után térnek vissza `Pageable<T>` .

Egy másik felügyeleti lehetőség az Azure Digital Twins [**REST API**](/rest/api/azure-digitaltwins/) -k meghívása a témakör területére közvetlenül, egy Rest-ügyféllel, például a Poster használatával. Ennek módjáról a következő témakörben talál útmutatást [*: a kérelmek küldése a Poster használatával*](../articles/digital-twins/how-to-use-postman.md).

Végül az Azure Digital Twins **CLI** használatával is végrehajthatja ugyanezeket a kezelési műveleteket. Ha többet szeretne megtudni a parancssori felület használatáról, olvassa el a következő témakört [*: útmutató: az Azure digitális Twins parancssori*](../articles/digital-twins/how-to-use-cli.md)felületének használata.