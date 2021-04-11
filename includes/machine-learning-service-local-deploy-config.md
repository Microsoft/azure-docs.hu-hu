---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508095"
---
A dokumentumban szereplő bejegyzések `deploymentconfig.json` a [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration)paramétereinek felelnek meg. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Helyi célok esetén az értéknek a következőnek kell lennie: `local` . |
| `port` | `port` | A helyi port, amelyen elérhetővé kívánja tenni a szolgáltatás HTTP-végpontját. |

Ez a JSON egy példaként szolgáló telepítési konfiguráció a parancssori felülettel való használatra:

```json
{
    "computeType": "local",
    "port": 32267
}
```