---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020353"
---
#### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

A `logstream` paraméterrel elindíthatja az Azure-ban futó adott függvényalkalmazások streamnaplóinak fogadását, ahogyan az az alábbi példában látható:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>A beépített naplózási adatfolyam még nincs engedélyezve a (z) Linux rendszeren futó Function apps alapeszközein a használati tervben. Ezen üzemeltetési csomagok esetében Ehelyett a Élő metrikastreamt kell használnia a naplók közel valós idejű megtekintéséhez.

#### <a name="live-metrics-stream"></a>Élő metrikastream

A Function alkalmazás [élő metrikastreamét](../articles/azure-monitor/app/live-stream.md) egy új böngészőablakban tekintheti meg `--browser` , a következő példában látható módon:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
