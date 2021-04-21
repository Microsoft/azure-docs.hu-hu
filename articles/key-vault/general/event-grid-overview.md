---
title: Az Key Vault monitorozása Azure Event Grid
description: Feliratkozás Azure Event Grid eseményekre a Key Vault használatával
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753361"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Az Key Vault monitorozása Azure Event Grid

Key Vault integrációja Event Grid lehetővé teszi, hogy a felhasználók értesítést kapnak, ha a Key Vaultban tárolt titkos kulcsok állapota megváltozott. Az állapotváltozás olyan titkos kulcsként van definiálva, amely hamarosan lejár (30 nappal a lejárat előtt), lejárt titkos kulcsként vagy egy új elérhető verzióval rendelkező titkos kulcsként. Mindhárom titkos kulcstípus (kulcs, tanúsítvány és titkos kulcs) értesítései támogatottak.

Az alkalmazások modern kiszolgáló nélküli architektúrák használatával reagálnak ezekre az eseményekre anélkül, hogy bonyolult kódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra lenne szükség. Az események [](https://azure.microsoft.com/services/event-grid/) a Azure Event Grid eseménykezelőkbe ( például [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)) vagy akár a saját webhookjukba is lekértek, és csak azért kell fizetnie, amit használ. További információ a díjszabásról: Event Grid [díjszabása.](https://azure.microsoft.com/pricing/details/event-grid/)

## <a name="key-vault-events-and-schemas"></a>Key Vault és sémák

Az Event Grid [esemény-előfizetésekkel](../../event-grid/concepts.md#event-subscriptions) irányít eseményüzeneteket az előfizetőknek. Key Vault események tartalmazzák az összes olyan információt, amely az adatok változásaira való válaszadáshoz szükséges. Az eseménytípus-esemény Key Vault, mert az eventType tulajdonság a "Microsoft.KeyVault" szóval kezdődik.

További információkért lásd a Key Vault [eseménysémát.](../../event-grid/event-schema-key-vault.md)

> [!WARNING]
> Az értesítési események csak a titkos kulcsok, kulcsok és tanúsítványok új verzióiban aktiválódnak, és az értesítések fogadása érdekében először elő kell iratkoznunk a kulcstartó eseményére.

## <a name="practices-for-consuming-events"></a>Eljárások az események fogyasztására

A Key Vault eseményeket kezelő alkalmazásoknak követnie kell néhány ajánlott gyakorlatot:

* Több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanabba az eseménykezelőbe irányítják. Fontos, hogy az események egy adott forrásból származiknak, de ellenőrizze az üzenet témakörét, hogy az a várt kulcstartóból származik-e.
* Hasonlóképpen ellenőrizze, hogy az eventType az, amelyet fel tud-e készíteni, és ne feltételezzék, hogy minden kapott esemény a várt típusú lesz.
* Hagyja figyelmen kívül a nem érthető mezőket.  Ez a gyakorlat segít rugalmasan alkalmazkodni az új funkciókhoz, amelyek a jövőben hozzáadhatóak.
* Az események egy adott eseményre való korlátozására használja a "tárgy" előtagot és utótagot.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault áttekintése](overview.md)
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- How to: [Route Key Vault Events to Automation Runbook](event-grid-tutorial.md).
- How to: Receive email when a Key Vault secret changes (Hogyan lehet: [E-mail fogadása a Key Vault titkos kulcsának módosulás esetén)](event-grid-logicapps.md)
- [Azure Event Grid eseménysémát a Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Automation áttekintés](../../automation/index.yml)
