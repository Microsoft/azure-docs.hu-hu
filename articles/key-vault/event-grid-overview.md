---
title: Key Vault figyelése Azure Event Grid
description: Azure Event Grid használata Key Vault eseményekre való előfizetéshez
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033497"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Key Vault figyelése Azure Event Grid (előzetes verzió)

Key Vault a Event Grid-integráció jelenleg előzetes verzióban érhető el. Lehetővé teszi a felhasználók számára, hogy értesítést kapjanak, ha a Key vaultban tárolt titkos kód állapota megváltozott. Az állapotváltozás olyan titokként van definiálva, amely hamarosan lejár (a lejárat napjától számított 30 napon belül), egy lejárt titkos kulcsot vagy egy új verziót tartalmazó titkos kulcsot. Az értesítések mind a három titkos típus (kulcs, tanúsítvány és titkos) esetében támogatottak.

Az alkalmazások a modern kiszolgáló nélküli architektúrák segítségével reagálnak ezekre az eseményekre, anélkül, hogy bonyolult programkódot vagy költséges és nem hatékony lekérdezési szolgáltatásokat kellene igénybe venniük. Az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Események és sémák Key Vault

Az Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Key Vault események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Key Vault eseményt, mert a eventType tulajdonság a "Microsoft. kulcstartó" karakterrel kezdődik.

További információ: [Key Vault esemény sémája](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Az események csak az előfizetés beállítása után létrehozott titkos verzióknál (mindhárom típusnál) aktiválódnak.
>
> A meglévő titkok esetében új verziókat kell előállítani.

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Key Vault eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Több előfizetés is konfigurálható az események ugyanahhoz az eseménykezelőhöz való továbbítására. Fontos, hogy ne tegyük fel, hogy az események egy adott forrásból származnak, de az üzenet témaköreinek ellenőrzésével biztosíthatja, hogy a várt kulcstartóból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault áttekintése](key-vault-overview.md)
- [Azure Event Grid áttekintése](../event-grid/overview.md)
- [Útmutató: Key Vault-események átirányítása az Automation Runbook (előzetes verzió)](event-grid-tutorial.md).
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../event-grid/event-schema-key-vault.md)
- [Az Azure Automation áttekintése](../automation/index.yml)
