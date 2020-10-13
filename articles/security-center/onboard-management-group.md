---
title: Felügyeleti csoport előkészítése Azure Security Center
description: Megtudhatja, hogyan használhatja a megadott Azure Policy definíciót a felügyeleti csoportba tartozó összes előfizetés Azure Security Centerének engedélyezéséhez.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: ce0858f61ca1fe3b81c3d0c8a3c97954827def80
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950618"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Security Center engedélyezése egy felügyeleti csoportban lévő összes előfizetésben

A Azure Policy használatával engedélyezheti a Azure Security Centert az ugyanazon felügyeleti csoportban (MG) belül található összes Azure-előfizetéshez. Ez kényelmesebb, mint a portálon való hozzáférés, és akkor is működik, ha az előfizetések különböző tulajdonosokhoz tartoznak. 

Egy felügyeleti csoport és az összes előfizetése bevezetéséhez:

1. A **biztonsági rendszergazdai** engedélyekkel rendelkező felhasználóként nyissa meg Azure Policy és keresse meg a definíció **engedélyezése Azure Security Center az előfizetésében**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="A Azure Policy definíciója engedélyezi a Azure Security Center az előfizetésen":::

1. Válassza a **hozzárendelés** lehetőséget, és győződjön meg róla, hogy a hatókört mg szintre állítja be.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="A Azure Policy definíciója engedélyezi a Azure Security Center az előfizetésen":::

    > [!TIP]
    > A hatókörön kívül nincsenek kötelező paraméterek.

1. Válassza **a Szervizelési feladat létrehozása** lehetőséget annak biztosításához, hogy az összes olyan előfizetést, amelynél nincs Security Center engedélyezve, be legyen vezetve.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="A Azure Policy definíciója engedélyezi a Azure Security Center az előfizetésen" minden regisztrált előfizetésnek (függetlenül attól, hogy az Azure Defender be van-e kapcsolva vagy ki van-e kapcsolva).

    A Szervizelési feladat ezután engedélyezi a nem megfelelő előfizetések Security Centerét ingyen.

> [!IMPORTANT]
> A házirend-definíció csak a **meglévő** előfizetések Security centerét teszi lehetővé. Az újonnan létrehozott előfizetések regisztrálásához az újonnan létrehozott előfizetések regisztrálásához nyissa meg a megfelelőség lapot, válassza ki a megfelelő nem megfelelő előfizetéseket, és hozzon létre egy szervizelési feladatot. Ismételje meg ezt a lépést, ha egy vagy több új előfizetéssel szeretne figyelni Security Center.

## <a name="optional-modifications"></a>Nem kötelező módosítások

A Azure Policy definíciójának módosítására többféle lehetőség is van: 

- A **megfelelőség meghatározása eltérően** – a megadott házirend az összes olyan előfizetést osztályozza, amely még nincs regisztrálva a Security Center "nem megfelelő" értékkel. Dönthet úgy is, hogy az összes előfizetésre beállítja az Azure Defender nélkül.

    A megadott definíció az alábbi "díjszabási" beállítások *egyikét* határozza meg a megfelelőként. Azt jelenti, hogy az előfizetés "standard" vagy "Free" értékre van beállítva.

    > [!TIP]
    > Ha engedélyezve van egy Azure Defender-csomag, azt a "standard" beállításban ismertetjük. Ha le van tiltva, az "ingyenes". [További információ az Azure Defender-csomagokról](security-center-pricing.md).

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Ha a következőre módosítja, csak a "standard" értékre beállított előfizetések minősülnek megfelelőnek:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- A **Security Center engedélyezésekor alkalmazandó Azure Defender-csomagok meghatározása** – a megadott szabályzat lehetővé teszi, hogy a választható Azure Defender-csomagok nélkül is Security Center. Dönthet úgy, hogy egy vagy több lehetőséget is engedélyez.

    A megadott definíció `deployment` szakasza paraméterrel rendelkezik `pricingTier` . Alapértelmezés szerint ez a érték `free` , de módosíthatja is. 


## <a name="next-steps"></a>Következő lépések:

Most, hogy betöltött egy teljes felügyeleti csoportot, engedélyezze az Azure Defender speciális védelmét. 

> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](security-center-pricing.md)