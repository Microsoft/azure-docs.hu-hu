---
title: Tekintse át az Azure-erőforrás szerepköreinek hozzáférését a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át Azure AD Privileged Identity Management (PIM) Azure Resource szerepköreinek hozzáférését.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743677"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Tekintse át az Azure erőforrás-szerepkörökhöz való hozzáférést Privileged Identity Management

Privileged Identity Management (PIM) hozzáférési felülvizsgálatok segíthetnek a Azure Active Directory (Azure AD) Kiemelt szerepköreihez való hozzáférésben. Ebből a cikkből megtudhatja, hogyan végezheti el a Kiemelt szerepkör-hozzárendelések áttekintését az Azure AD hozzáférési felülvizsgálatában.

Ha rendszergazdai szerepkörhöz van rendelve, előfordulhat, hogy a rendszergazda hozzáférési felülvizsgálatát kell végrehajtania, hogy erősítse meg a szükséges szerepkört. A megerősítő kérelem elküldheti a hivatkozást tartalmazó e-mailt, vagy ellenőrizheti a [Azure Portal](https://portal.azure.com).

Ha Ön a hozzáférési felülvizsgálatok iránt érdeklődő Kiemelt szerepkörű rendszergazda, további információt talál a [hozzáférési felülvizsgálat elindításáról](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása

Jóváhagyhatja vagy megtagadhatja a hozzáférést attól függően, hogy továbbra is használja-e ezt a szerepkört. Válassza a **jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, **vagy ha** nem szeretné, hogy a hozzáférés többé nem szükséges. Az állapot csak akkor változik, ha a felülvizsgáló alkalmazza az eredményeket.

Az alábbi lépéseket követve megkeresheti és elvégezheti a hozzáférési felülvizsgálatot:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **Azure Active Directory** és nyissa meg **Privileged Identity Management**.
1. Válassza a **hozzáférés áttekintése** lehetőséget.

   ![Képernyőkép a Privileged Identity Management alkalmazásról, a hozzáférés megtekintése panel kijelölésével](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Válassza ki a végrehajtani kívánt felülvizsgálatot.
1. Válassza a **jóváhagyás** vagy a **Megtagadás** lehetőséget. Az **adjon meg egy okot mezőben** adjon meg egy üzleti indoklást a döntéshez, ha szükséges.

   ![A részletek áttekintése lap képernyőképe](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök hozzáférési felülvizsgálatának elvégzése Privileged Identity Management](pim-how-to-perform-security-review.md)
