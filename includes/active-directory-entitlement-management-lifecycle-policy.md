---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98052923"
---
## <a name="lifecycle"></a>Életciklus

Az **életciklus** lapon megadhatja, hogy mikor járjon le egy felhasználó a hozzáférési csomaghoz. Megadhatja azt is, hogy a felhasználók ki tudják-e terjeszteni a hozzárendeléseiket.

1. A **lejárat** szakaszban adja meg a **hozzáférési csomag hozzárendeléseinek érvényességét** **dátum**, **nap** vagy **soha** értékre.

    A **on Date (dátum**) beállításnál válassza ki a lejárati dátumot a jövőben.

    A **napok számának** megadásához 0 és 3660 nap közötti számot kell megadni.

    A kiválasztás alapján a felhasználó a hozzáférési csomaghoz való hozzárendelése egy adott napon lejár, bizonyos számú nappal a jóváhagyás után, vagy soha nem.

1. További beállítások megjelenítéséhez kattintson a **speciális lejárati beállítások megjelenítése** lehetőségre.

    ![Hozzáférési csomag – életciklus-lejárati beállítások](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Annak engedélyezéséhez, hogy a felhasználó kiterjessze a hozzárendeléseket, állítsa a **felhasználók számára az** **Igen** értékre való kiterjesztést.

    Ha a kiterjesztések engedélyezve vannak a szabályzatban, a felhasználó 14 napig, a hozzáférési csomag hozzárendelésének lejárta előtt pedig egy nappal kéri le a hozzárendelés kiterjesztését. A felhasználónak továbbra is a szabályzat hatókörében kell lennie, amikor a bővítményt kérik. Továbbá, ha a házirend a hozzárendelések explicit befejezési dátumával rendelkezik, és a felhasználó a hozzáférés kiterjesztésére vonatkozó kérést küld, a kérelemben szereplő kiterjesztési dátumnak a hozzárendelések lejárta előtt vagy előtt kell lennie, a hozzáférési csomaghoz való hozzáférés biztosításához használt Szabályzatban meghatározottak szerint. Ha például a házirend azt jelzi, hogy a hozzárendelések június 30-án lejárnak, a felhasználó által igényelhető maximális kiterjesztés június 30-ig tart.

    Ha a felhasználó hozzáférése ki van bővítve, a megadott kiterjesztési dátum (a szabályzatot létrehozó felhasználó időzónájában beállított dátum) után nem fogja tudni kérni a hozzáférési csomagot.

1. Ha jóváhagyásra van szüksége a bővítmény megadásához, állítsa a **jóváhagyás megkövetelése lehetőséget az** **Igen** értékre való kiterjesztés megadásához.

    A rendszer a **kérelmek** lapon megadott jóváhagyási beállításokat fogja használni.

1. Kattintson a **tovább** vagy a **frissítés** gombra.
