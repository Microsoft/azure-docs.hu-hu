---
title: fájlbefoglalás
description: fájlbefoglalás
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91605897"
---
A Azure Policyhoz tartozó egyes objektumtípusok maximális száma. A definíciók esetében a _hatókör_ egy bejegyzése a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) vagy előfizetést jelenti.
A hozzárendelések és kivételek esetében a _hatókör_ bejegyzése a [felügyeleti csoportot](../articles/governance/management-groups/overview.md), az előfizetést, az erőforráscsoportot vagy az egyes erőforrásokat jelenti.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 500 |
| Hatókör | Kezdeményezési definíciók | 200 |
| Bérlő | Kezdeményezési definíciók | 2500 |
| Hatókör | Házirend-vagy kezdeményezési hozzárendelések | 200 |
| Hatókör | Kivételek | 1000 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 1000 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Házirend-vagy kezdeményezési hozzárendelések | Kizárások (notScope) | 400 |
| Házirend-szabály | Beágyazott feltételesség | 512 |
| Szervizelési feladat | További források | 500 |
