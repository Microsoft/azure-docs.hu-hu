---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645356"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Ha még nem használta korábban az Event Gridet az Azure-előfizetésében, lehetséges, hogy regisztrálnia kell az Event Grid erőforrás-szolgáltatót. Futtassa az alábbi parancsot:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Lehetséges, hogy a regisztráció végrehajtása eltart néhány pillanatig. Az állapot ellenőrzéséhez futtassa a következőt:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Ha a `RegistrationStatus``Registered` értékű, készen áll a folytatásra.
