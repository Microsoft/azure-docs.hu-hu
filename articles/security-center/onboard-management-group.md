---
title: Felügyeleti csoport előkészítése Azure Security Center
description: Megtudhatja, hogyan használhatja a megadott Azure Policy definíciót a felügyeleti csoportba tartozó összes előfizetés Azure Security Centerének engedélyezéséhez.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016559"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Security Center engedélyezése egy felügyeleti csoportban lévő összes előfizetésben

A Azure Policy használatával engedélyezheti a Azure Security Centert az ugyanazon felügyeleti csoportban (MG) belül található összes Azure-előfizetéshez. Ez kényelmesebb, mint a portálon való hozzáférés, és akkor is működik, ha az előfizetések különböző tulajdonosokhoz tartoznak. 

Egy felügyeleti csoport és az összes előfizetése bevezetéséhez:

1. A **biztonsági rendszergazdai** engedélyekkel rendelkező felhasználóként nyissa meg Azure Policy és keresse meg a definíció **engedélyezése Azure Security Center az előfizetésében**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="A Azure Policy definíciója engedélyezi a Azure Security Center az előfizetésen":::

1. Válassza a **hozzárendelés** lehetőséget, és győződjön meg róla, hogy a hatókört mg szintre állítja be.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="A definíció kiosztásának engedélyezése Azure Security Center az előfizetésen":::

    > [!TIP]
    > A hatókörön kívül nincsenek kötelező paraméterek.

1. Válassza **a Szervizelési feladat létrehozása** lehetőséget annak biztosításához, hogy az összes olyan előfizetést, amelynél nincs Security Center engedélyezve, be legyen vezetve.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Szervizelési feladat létrehozása a Azure Policy-definícióhoz Azure Security Center engedélyezése az előfizetésen":::

1. A definíció hozzárendelésekor a következőket fogja megadni:

    1. Az összes olyan előfizetés észlelése az MG-ban, amely még nincs regisztrálva a Security Centerban.
    1. Ezeket az előfizetéseket "nem megfelelő" értékre kell megjelölni.
    1. Megjelölés "megfelelőként" minden regisztrált előfizetésnek (függetlenül attól, hogy az Azure Defender be van-e kapcsolva vagy ki van-e kapcsolva).

    A Szervizelési feladat ezután engedélyezi a nem megfelelő előfizetések Security Centerét ingyen.

> [!IMPORTANT]
> A házirend-definíció csak a **meglévő** előfizetések Security centerét teszi lehetővé. Az újonnan létrehozott előfizetések regisztrálásához nyissa meg a megfelelőség lapot, válassza ki a megfelelő nem megfelelő előfizetéseket, és hozzon létre egy szervizelési feladatot. Ismételje meg ezt a lépést, ha egy vagy több új előfizetéssel szeretne figyelni Security Center.

## <a name="optional-modifications"></a>Nem kötelező módosítások

A Azure Policy definíciójának módosítására többféle lehetőség is van: 

- A **megfelelőség meghatározása eltérően** – a megadott házirend az összes olyan előfizetést osztályozza, amely még nincs regisztrálva a Security Center "nem megfelelő" értékkel. Dönthet úgy is, hogy az összes előfizetésre beállítja az Azure Defender nélkül.

    A megadott definíció az alábbi "díjszabási" beállítások *egyikét* határozza meg a megfelelőként. Azt jelenti, hogy az előfizetés "standard" vagy "Free" értékre van beállítva.

    > [!TIP]
    > Ha engedélyezve van egy Azure Defender-csomag, azt egy szabályzat-definíció írja le a "standard" beállításnál. Ha le van tiltva, az "ingyenes". A csomagok közötti különbségekről további információt a [Security Center Free vs Azure Defender engedélyezése](security-center-pricing.md)című témakörben talál. 

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
> [Az Azure Defender engedélyezése](enable-azure-defender.md)