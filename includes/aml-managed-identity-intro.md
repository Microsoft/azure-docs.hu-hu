---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025441"
---
 Azure Machine Learning a számítási fürtök támogatják az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez szükséges [felügyelt identitásokat](../articles/active-directory/managed-identities-azure-resources/overview.md) is, a kódban szereplő hitelesítő adatok nélkül. A felügyelt identitásoknak két típusa létezik:

* Egy **rendszerhez rendelt felügyelt identitás** közvetlenül a Azure Machine learning számítási fürtön van engedélyezve. A rendszerhez rendelt identitás életciklusa közvetlenül a számítási fürthöz van kötve. Ha a számítási fürt törölve lett, az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
* A **felhasználó által hozzárendelt felügyelt identitás** egy önálló Azure-erőforrás, amelyet az Azure felügyelt identitás szolgáltatás biztosít. A felhasználókhoz rendelt felügyelt identitásokat több erőforráshoz is hozzárendelheti, és a kívánt ideig továbbra is fennáll.