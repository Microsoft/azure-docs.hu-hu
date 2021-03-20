---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97826686"
---
## <a name="enable-the-event-grid-resource-provider"></a>A Event Grid erőforrás-szolgáltató engedélyezése

Ha korábban még nem használta Event Grid az Azure-előfizetésében, előfordulhat, hogy regisztrálnia kell a Event Grid erőforrás-szolgáltatót. A szolgáltató regisztrálásához futtassa az alábbi parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Eltarthat egy kis ideig, amíg a regisztráció befejeződik. Az állapot ellenőrzéséhez futtassa a következőt:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Ha a `registrationState``Registered` értékű, készen áll a folytatásra.
