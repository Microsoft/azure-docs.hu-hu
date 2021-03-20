---
title: Key Vault figyelése Azure Event Grid
description: Azure Event Grid használata Key Vault eseményekre való előfizetéshez
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087436"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Key Vault figyelése Azure Event Grid

Key Vault integrációja Event Grid lehetővé teszi a felhasználók számára, hogy a Key vaultban tárolt titkos kód állapotának változásakor értesítést kapjanak. Az állapotváltozás olyan titokként van meghatározva, amely hamarosan lejár (30 nappal a lejárat előtt), egy lejárt titkos kulcsot vagy egy új verziót tartalmazó titkos kulcsot. Az értesítések mind a három titkos típus (kulcs, tanúsítvány és titkos) esetében támogatottak.

Az alkalmazások a modern kiszolgáló nélküli architektúrák segítségével reagálnak ezekre az eseményekre, anélkül, hogy bonyolult programkódot vagy költséges és nem hatékony lekérdezési szolgáltatásokat kellene igénybe venniük. Az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Események és sémák Key Vault

Az Event Grid [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Key Vault események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Key Vault eseményt, mert a eventType tulajdonság a "Microsoft. kulcstartó" karakterrel kezdődik.

További információ: [Key Vault esemény sémája](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Az értesítési események csak a titkok, kulcsok és tanúsítványok új verzióin aktiválódnak, és az értesítések fogadásához először elő kell fizetnie az eseményre a kulcstartóban.

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Key Vault eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására. Fontos, hogy ne tegyük fel, hogy az események egy adott forrásból származnak, de az üzenet témaköreinek ellenőrzésével biztosíthatja, hogy a várt kulcstartóból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault áttekintése](overview.md)
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- Útmutató: [Key Vault-események átirányítása az Automation-Runbook](event-grid-tutorial.md).
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája](../../event-grid/event-schema-key-vault.md)
- [Azure Automation áttekintése](../../automation/index.yml)
