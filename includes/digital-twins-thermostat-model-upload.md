---
author: baanders
description: Az Azure Digital Twins-példányba feltöltendő modell feltöltésére szolgáló fájl belefoglalása
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304297"
---
A modell így néz ki:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

A **modell az ikrek-példányba való feltöltéséhez** futtassa az alábbi Azure CLI-parancsot, amely a fenti MODELLT beágyazott JSON-ként tölti fel. A (z) [Azure Cloud Shellban](../articles/cloud-shell/overview.md) futtathatja a parancsot a böngészőben (a **bash** -környezettel) vagy a GÉPEN, ha a CLI [telepítve](/cli/azure/install-azure-cli)van a helyi számítógépen.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```