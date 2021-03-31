---
title: Egyéni témakörök a Azure Event Grid
description: A Azure Event Grid egyéni témaköreinek ismertetése.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86113785"
---
# <a name="custom-topics-in-azure-event-grid"></a>Egyéni témakörök a Azure Event Grid
Az Event Grid-témakör olyan végpontot biztosít, amelyben a forrás eseményeket küld. A közzétevő létrehozza az Event Grid-témakört, és eldönti, hogy egy adott eseményforrás egy vagy több témakörre van-e szüksége. A témakörök a kapcsolódó események gyűjteményéhez használatosak. Bizonyos típusú események megválaszolásához az előfizetők eldönthetik, hogy mely témakörökre kell előfizetni.

Az **Egyéni témakörök** az alkalmazások és a harmadik féltől származó témakörök. Amikor létrehoz vagy hozzáférést kap egy egyéni témakörhöz, akkor az megjelenik a saját előfizetésében. 

Az alkalmazás tervezésekor rugalmasságot biztosít, amikor eldönti, hány témakört kell létrehoznia. Nagyméretű megoldások esetében hozzon létre egy **Egyéni témakört** a **kapcsolódó események egyes kategóriáira**. Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetében érdemes lehet az összes eseményt egyetlen témakörbe elküldeni. Az esemény-előfizetők szűrhetik a kívánt típusú eseményeket.

## <a name="event-schema"></a>Eseményséma
Az Event Schema részletes áttekintését itt tekintheti meg: [Azure Event Grid Event Schema](event-schema.md). Az egyéni témakörök esetében az esemény-közzétevő határozza meg az **adatobjektumot** . A legfelső szintű adatnak ugyanazokkal a mezőkkel kell rendelkeznie, mint a szabványos erőforrás-definíciós események.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

A következő szakaszok a Azure Portal, a CLI, a PowerShell és a Azure Resource Manager (ARM) sablonok használatával egyéni témakörök létrehozására szolgáló oktatóanyagokra mutató hivatkozásokat tartalmaznak. 


## <a name="azure-portal-tutorials"></a>Oktatóanyagok Azure Portal
|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események létrehozása és irányítása a Azure Portal](custom-event-quickstart-portal.md) | Bemutatja, hogyan küldhet egyéni eseményeket a portál használatával. |
| [Gyors útmutató: egyéni események irányítása az Azure üzenetsor-tárolóba](custom-event-to-queue-storage.md) | Útmutató egyéni események üzenetsor-tárolóba való küldéséhez. |
| [Útmutató: közzététel egyéni témakörbe](post-to-custom-topic.md) | Bemutatja, hogyan lehet elküldeni egy eseményt egy egyéni témakörbe. |


## <a name="azure-cli-tutorials"></a>Azure CLI-oktatóanyagok
|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események létrehozása és irányítása az Azure CLI-vel](custom-event-quickstart.md) | Bemutatja, hogyan küldhet egyéni eseményeket az Azure CLI használatával. |
| [Azure CLI: Event Grid egyéni témakör létrehozása](./scripts/event-grid-cli-create-custom-topic.md)|Egyéni témakört létrehozó minta parancsfájl. A parancsfájl lekéri a végpontot és a kulcsot.|
| [Azure CLI: előfizetés egyéni témakör eseményeire](./scripts/event-grid-cli-subscribe-custom-topic.md)|Egy egyéni témakör előfizetését létrehozó parancsfájl. Eseményeket küld egy webhooknak.|

## <a name="azure-powershell-tutorials"></a>Oktatóanyagok Azure PowerShell
|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események létrehozása és irányítása Azure PowerShell](custom-event-quickstart-powershell.md) | Bemutatja, hogyan lehet a Azure PowerShell használatával egyéni eseményeket küldeni. |
| [PowerShell: Event Grid egyéni témakör létrehozása](./scripts/event-grid-powershell-create-custom-topic.md)|Egyéni témakört létrehozó minta parancsfájl. A parancsfájl lekéri a végpontot és a kulcsot.|
| [PowerShell: előfizetés egy egyéni témakör eseményeire](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Egy egyéni témakör előfizetését létrehozó parancsfájl. Eseményeket küld egy webhooknak.|

## <a name="arm-template-tutorials"></a>ARM-sablon oktatóanyagok
|Cím  |Leírás  |
|---------|---------|
| [Resource Manager-sablon: egyéni témakör és webhook-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Egy Resource Manager-sablon, amely egyéni témakört és előfizetést hoz létre az adott egyéni témakörhöz. Eseményeket küld egy webhooknak. |
| [Resource Manager-sablon: egyéni témakör és Event Hubs végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablon, amely létrehoz egy egyéni témakörhöz tartozó előfizetést. Eseményeket küld egy Azure-Event Hubs. |

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Rendszerrel kapcsolatos témakörök](system-topics.md)
- [Tartományok](event-domains.md)