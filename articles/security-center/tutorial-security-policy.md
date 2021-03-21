---
title: A biztonsági szabályzatok használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a biztonsági házirendek a Azure Security Centerban.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101324"
---
# <a name="manage-security-policies"></a>Biztonsági szabályzatok kezelése

Ez a cikk ismerteti a biztonsági házirendek konfigurálásának módját, valamint azt, hogy miként lehet megtekinteni őket a Security Centerban. 

## <a name="who-can-edit-security-policies"></a>Kik módosíthatják a biztonsági házirendeket?

A biztonsági szabályzatokat a Azure Policy-portálon, REST API vagy a Windows PowerShell használatával szerkesztheti.

A Security Center Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ, amely beépített szerepköröket biztosít az Azure-felhasználók,-csoportok és-szolgáltatások számára. Security Center megnyitásakor a felhasználók csak az elérhető erőforrásokhoz kapcsolódó információkat látják. Ez azt jelenti, hogy a felhasználók a *tulajdonos*, *közreműködő* vagy *olvasó* szerepkört rendelik hozzá az erőforrás előfizetéséhez. Két konkrét Security Center szerepkör is létezik:

- **Biztonsági olvasó**: jogosult a Security Center elemek, például javaslatok, riasztások, szabályzatok és állapotok megtekintésére. Nem lehet módosítani.
- **Biztonsági rendszergazda**: a *biztonsági olvasóval* megegyező megtekintési jogosultsággal rendelkezik. A a biztonsági házirend frissítése és a riasztások elvetése is megtehető.

## <a name="manage-your-security-policies"></a>Biztonsági szabályzatok kezelése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. A **Security Center** irányítópulton válassza a **biztonsági házirend** elemet.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="A házirend-kezelés lap":::

   A **házirend kezelése** képernyőn láthatja a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleti csoport struktúráját.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a szabályzatait meg szeretné tekinteni.

1. Megjelenik az adott előfizetés vagy felügyeleti csoport biztonsági szabályzat lapja. Megjeleníti az elérhető és hozzárendelt házirendeket.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Security Center biztonsági házirend lapja" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Ha az alapértelmezett szabályzat mellett "MG örökölt" címkével rendelkezik, az azt jelenti, hogy a szabályzat hozzá van rendelve egy felügyeleti csoporthoz, és a megtekintett előfizetés örökli.

1. Válasszon az ezen az oldalon elérhető lehetőségek közül:

    1. Az iparági szabványokkal való együttműködéshez válassza a **további szabványok hozzáadása** lehetőséget. További információ: [a szabványok készletének testreszabása a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).

    1. Egyéni kezdeményezések hozzárendeléséhez és kezeléséhez válassza az **Egyéni kezdeményezések hozzáadása** lehetőséget. További információ: [egyéni biztonsági kezdeményezések és házirendek használata](custom-security-policies.md).

    1. Az alapértelmezett kezdeményezés megtekintéséhez és szerkesztéséhez válassza a **hatályos házirend megtekintése** lehetőséget, és folytassa az alább leírtak szerint. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Érvényes házirend képernyő":::

       Ez a **biztonsági házirend** képernyő a kiválasztott előfizetéshez vagy felügyeleti csoporthoz hozzárendelt szabályzatok által végrehajtott műveletet tükrözi.
       
       * A felül található hivatkozásokkal nyisson meg egy szabályzat- **hozzárendelést** , amely az előfizetésre vagy a felügyeleti csoportra vonatkozik. Ezekkel a hivatkozásokkal hozzáférhet a hozzárendeléshez, és szerkesztheti vagy letilthatja a szabályzatot. Ha például úgy látja, hogy egy adott házirend-hozzárendelés ténylegesen megtagadja az Endpoint Protectiont, akkor a hivatkozásra kattintva szerkesztheti vagy tilthatja le a házirendet.
       
       * A szabályzatok listájában megtekintheti az előfizetéséhez vagy a felügyeleti csoportjához tartozó házirend hatályos alkalmazását. A rendszer figyelembe veszi a hatókörre vonatkozó egyes szabályzatok beállításait, és a szabályzat által végrehajtott műveletek összesített eredményét jeleníti meg. Ha például a házirend egyetlen hozzárendelésében le van tiltva, de egy másikban a AuditIfNotExist értékre van állítva, akkor a kumulatív hatás a AuditIfNotExist alkalmazza. Az aktívabb hatás mindig elsőbbséget élvez.
       
       * A házirendek hatása a következő lehet: Hozzáfűzés, naplózás, AuditIfNotExists, megtagadás, DeployIfNotExists, letiltva. További információ a hatások alkalmazásáról: a [szabályzatok hatásainak megismerése](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > A hozzárendelt házirendek megtekintésekor több hozzárendelést láthat, és megtekintheti, hogy az egyes hozzárendelések hogyan legyenek konfigurálva.


## <a name="disable-security-policies-and-disable-recommendations"></a>Biztonsági házirendek letiltása és javaslatok letiltása

Ha a biztonsági kezdeményezés olyan javaslatot indít, amely a környezet szempontjából lényegtelen, megakadályozhatja, hogy a javaslat újból megjelenjen. Egy javaslat letiltásához tiltsa le a javaslatot létrehozó konkrét szabályzatot.

A letiltani kívánt javaslat továbbra is megjelenik, ha a Security Center szabályozási megfelelőségi eszközeivel alkalmazott szabályozási szabványhoz szükséges. Még ha a szabályzatot is letiltotta a beépített kezdeményezésben, a szabályozási standard kezdeményezési szabályzata továbbra is elindítja a javaslatot, ha a megfelelőséghez szükséges. A szabályzatokat nem lehet letiltani a szabályozási standard kezdeményezésekben.

A javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

1. Security Center a **házirend & megfelelőség** szakaszban válassza a **biztonsági házirend** elemet.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="A házirend-kezelési folyamat elindítása Azure Security Center":::

2. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez le szeretné tiltani a javaslatot.

   > [!NOTE]
   > A felügyeleti csoport az előfizetésekre alkalmazza a szabályzatokat. Így ha letiltja egy előfizetés szabályzatát, az előfizetés pedig egy olyan felügyeleti csoporthoz tartozik, amely ugyanazt a szabályzatot alkalmazza, továbbra is megkapja a szabályzatra vonatkozó javaslatokat. A rendszer továbbra is a felügyeleti szinten alkalmazza a szabályzatot, valamint létrehoz javaslatokat.

1. Válassza a **hatályos szabályzat megtekintése** lehetőséget.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Az előfizetéshez hozzárendelt hatályos szabályzat megnyitása":::

1. Válassza ki a hozzárendelt szabályzatot.

   ![házirend kiválasztása](./media/tutorial-security-policy/security-policy.png)

1. A **Parameters (paraméterek** ) szakaszban keresse meg a letiltani kívánt javaslatot meghívó házirendet, és a legördülő listából válassza a **Letiltva** lehetőséget.

   ![házirend letiltása](./media/tutorial-security-policy/disable-policy.png)

1. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A házirend-letiltási módosítások érvénybe léptetéséhez akár 12 órát is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
Ez az oldal a biztonsági szabályzatokat ismerteti. A kapcsolódó információkat a következő lapokon tekintheti meg:

- [Ismerje meg, hogyan állíthatja be a szabályzatokat a PowerShell használatával](../governance/policy/assign-policy-powershell.md)
- [Megtudhatja, hogyan szerkesztheti a biztonsági házirendeket Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Megtudhatja, hogyan állíthatja be a szabályzatot az előfizetések és a felügyeleti csoportok között a Azure Policy használatával](../governance/policy/overview.md)
- [Megtudhatja, hogyan engedélyezheti Security Center a felügyeleti csoportban lévő összes előfizetéshez](onboard-management-group.md)