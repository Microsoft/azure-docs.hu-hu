---
title: Azure AD-szerepkörök hozzáférésének áttekintése a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan tekintheti át Azure Active Directory szerepkörök hozzáférését Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd0b63e08f44167b89b6e2d491722f3708b4036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90055435"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Tekintse át az Azure AD-szerepkörökhöz való hozzáférést Privileged Identity Management

A Privileged Identity Management (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik a Azure Active Directory (AD) és más Microsoft-online szolgáltatások, például a Microsoft 365 vagy Microsoft Intune erőforrásaihoz való emelt szintű hozzáférést. A cikkben ismertetett lépéseket követve sikeresen áttekintheti a hozzárendelt szerepköröket.

Ha rendszergazdai szerepkörhöz van rendelve, a szervezet Kiemelt szerepkörű rendszergazdája megkérheti, hogy rendszeresen erősítse meg, hogy továbbra is szüksége van erre a szerepkörre a feladathoz. Előfordulhat, hogy olyan e-mailt kap, amely hivatkozást tartalmaz, vagy egyenesen a [Azure Portalra](https://portal.azure.com) , és megkezdheti.

Ha a hozzáférési felülvizsgálatok érdeklik a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazda, akkor további részleteket tudhat meg a [hozzáférési felülvizsgálat elindításáról](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>PIM-irányítópult csempe hozzáadása

Ha nem rendelkezik az irányítópultra rögzített Privileged Identity Management szolgáltatással a Azure Portalban, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a felhasználónevet a Azure Portal jobb felső sarkában, és válassza ki azt az Azure AD-szervezetet, ahol Ön is működni fog.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása

Ha jóváhagyja vagy megtagadja a hozzáférést, csak azt közli a véleményezővel, hogy továbbra is használja-e ezt a szerepkört. Válassza a **jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, **vagy ha** nem szeretné, hogy a hozzáférés többé nem szükséges. Az állapot azonnal nem változik, amíg a felülvizsgáló nem alkalmazza az eredményeket.
Az alábbi lépéseket követve megkeresheti és elvégezheti a hozzáférési felülvizsgálatot:

1. A Privileged Identity Management szolgáltatásban válassza az emelt **szintű hozzáférés áttekintése** elemet. Ha rendelkezik függőben lévő hozzáférési felülvizsgálatokkal, azok megjelennek az Azure AD **hozzáférési felülvizsgálatok** oldalán.
2. Válassza ki a végrehajtani kívánt felülvizsgálatot.
3. Hacsak nem hozta létre a felülvizsgálatot, az egyetlen felhasználóként jelenik meg a felülvizsgálatban. Jelölje be a neve melletti pipa.
4. Válassza a **jóváhagyás** vagy a **Megtagadás** lehetőséget. Előfordulhat, hogy a döntés okát is meg kell adnia az **indoklás** szövegmezőben.  
5. Az **Azure ad-szerepkörök áttekintése** panel bezárásához.

## <a name="next-steps"></a>Következő lépések

- [A saját Azure-erőforrásszerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-resource-roles-perform-access-review.md)
