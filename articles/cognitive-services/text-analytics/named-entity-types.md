---
title: Megnevezett entitások felismeréséhez támogatott kategóriák
titleSuffix: Azure Cognitive Services
description: A Text Analytics API támogatott entitások kategóriáinak megismerése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599339"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Támogatott entitások kategóriái a Text Analytics API v3-ban

Ebből a cikkből megtudhatja, hogy mely entitás-kategóriákat lehet visszaadni, [névvel ellátott entitás-felismeréssel](how-tos/text-analytics-how-to-entity-linking.md) . A rendszer egy prediktív modellt futtat, amellyel azonosíthatja és kategorizálhatja az elnevezett entitásokat egy bemeneti dokumentumból.

Az Egypéldányos v 3.1 előzetes verziója is elérhető, amely magában foglalja a személyes ( `PII` ) és az állapotadatok ( `PHI` ) adatainak észlelését. Emellett a **Health (állapot** ) lapra kattintva megtekintheti a támogatott kategóriák listáját a Text Analytics for Health szolgáltatásban. 

Az 2,1-es verzióban visszaadott típusok listáját az [áttelepítési útmutatóban](migration-guide.md?tabs=named-entity-recognition) találja.

## <a name="entity-categories"></a>Entitások kategóriái

#### <a name="general"></a>[Általános](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Egészségügy](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Következő lépések

* [Elnevezett entitások felismerésének használata a Text Analyticsban](how-tos/text-analytics-how-to-entity-linking.md)
