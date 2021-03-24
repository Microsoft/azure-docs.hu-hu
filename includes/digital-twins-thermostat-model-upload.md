---
author: baanders
description: Az Azure Digital Twins-példányba feltöltendő modell feltöltésére szolgáló fájl belefoglalása
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950594"
---
A modell így néz ki:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

A **modell az ikrek-példányba való feltöltéséhez** futtassa az alábbi Azure CLI-parancsot, amely a fenti MODELLT beágyazott JSON-ként tölti fel. A (z) [Azure Cloud Shell](../articles/cloud-shell/overview.md) a böngészőben futtathatja a parancsot, vagy a gépen, ha a CLI telepítve van a [helyi](/cli/azure/install-azure-cli)számítógépen.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Ha Cloud Shellt használ a PowerShell-környezetben, akkor előfordulhat, hogy el kell kerülnie az idézőjelek karaktereit a beágyazott JSON-mezőkben az értékek megfelelő elemzéséhez. Az alábbi paranccsal töltheti fel a modellt a következő módosítással:
>
> Modell feltöltése:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```