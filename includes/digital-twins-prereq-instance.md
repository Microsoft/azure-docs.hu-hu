---
author: baanders
description: fájl belefoglalása az Azure digitális Twins szolgáltatásba – példány beállításának előfeltétele
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2be13256ff0fcc93aa40db3bcb2f38aa0cf58cbc
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463757"
---
Ha az Azure Digital Twins-t szeretné használni ebben a cikkben, először **be kell állítania egy Azure digitális Twins-példányt** és a használatához szükséges engedélyeket. Ha már rendelkezik egy Azure Digital Twins-példánnyal, amely az előző munkafolyamatból lett beállítva, akkor ezt a példányt használhatja.

Ellenkező esetben kövesse az útmutató [*: példány és hitelesítés beállítása*](../articles/digital-twins/how-to-set-up-instance-portal.md)című témakör utasításait. Az utasítások emellett azt is tartalmazzák, hogy az egyes lépések sikeresen befejeződtek, és készen állnak arra, hogy az új példány használatával belépjenek.

Miután beállította az Azure Digital Twins-példányt, jegyezze fel a következő értékeket, amelyekhez később csatlakoznia kell a példányhoz:
* a példány **_állomásneve_**. Ez az érték a Azure Portalban ([utasítások](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) található.
* a példány létrehozásához használt **Azure-előfizetés** (a neve vagy azonosítója fog működni). Megtekintheti, hogy milyen előfizetést tartalmaz az Azure Digital Twins-példány, ha a [Azure Portalban](https://portal.azure.com)megtekinti az adott példányhoz tartozó *áttekintő* oldalt.
