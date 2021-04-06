---
title: Szolgáltatási korlátozások
titleSuffix: Azure Machine Learning
description: A kapacitás megtervezésére, valamint a kérelmek és a Azure Machine Learningre adott válaszok maximális korlátaira vonatkozó szolgáltatási korlátok.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97930803"
---
# <a name="service-limits-in-azure-machine-learning"></a>Szolgáltatási korlátozások a Azure Machine Learning

Ez a szakasz az alapszintű kvótákat és a szabályozás küszöbértékeit sorolja fel Azure Machine Learningban. Az erőforrás-kvóták növelésének megismeréséhez tekintse meg a ["erőforrások kvótáinak kezelése és növelése"](how-to-manage-quotas.md) című témakört.

## <a name="workspaces"></a>Munkaterületek
| Korlát | Érték |
| --- | --- |
| Munkaterület neve | 2-32 karakter |

## <a name="runs"></a>Futtatás
| Korlát | Érték |
| --- | --- |
| Futtatások száma munkaterületen | 10 millió |
| RunId/ParentRunId | 256 karakter |
| DataContainerId | 261 karakter |
| DisplayName |256 karakter|
| Leírás |5 000 karakter|
| Tulajdonságok száma |50 |
| Tulajdonság-kulcs hossza |100 karakter |
| Tulajdonság értékének hossza |1 000 karakter |
| Címkék száma |50 |
| Címke kulcsának hossza |100 |
| Címke értékének hossza |1 000 karakter |
| CancelUri / CompleteUri / DiagnosticsUri |1 000 karakter |
| Hibaüzenet hossza |3 000 karakter |
| Figyelmeztető üzenet hossza |300 karakter |
| Bemeneti adatkészletek száma |200 |
| Kimeneti adatkészletek száma |20 |


## <a name="metrics"></a>Mérőszámok
| Korlát | Érték |
| --- | --- |
| Metrikák nevei/futtatása |50|
| Metrikus sorok/metrika neve |10 millió|
| Oszlopok/metrikus sorok |15|
| Metrika oszlopának neve hossz |255 karakter |
| Metrika oszlopának értékének hossza |255 karakter |
| Metrikus sorok száma feltöltve | 250 |

> [!NOTE]
> Ha futtatja a metrikák neveinek korlátját, mert a változókat a metrika nevére formázza, érdemes inkább egy sor mérőszámot használni, ahol az egyik oszlop a változó értéke, a második oszlop pedig a metrika értéke.

## <a name="artifacts"></a>Artifacts

| Korlát | Érték |
| --- | --- |
| Összetevők száma futtatáskor |10 millió|
| Az összetevő elérési útjának maximális hossza |5 000 karakter |

## <a name="limit-increases"></a>Korlátok számának növelése
Bizonyos korlátok megnövelhető az egyes munkaterületek esetében, ha [kapcsolatba lép a támogatási szolgálattal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Következő lépések

- [A Azure Machine Learning környezet konfigurálása](how-to-configure-environment.md)
- Ismerje meg, hogyan növelheti az erőforrás-kvótákat a ["erőforrások kvótáinak kezelése és növelése" című](how-to-manage-quotas.md)témakörben.

