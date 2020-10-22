---
title: Csoport törlése – Azure Active Directory | Microsoft Docs
description: A csoportok Azure Active Directory használatával történő törlésére vonatkozó utasítások.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aed5f282ff7b5d573a6f8511f2fc4dbfd27135d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371817"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Csoport törlése Azure Active Directory használatával
A Azure Active Directory (Azure AD) csoport tetszőleges számú okból törölhető, de általában az alábbiak miatt lesz lehetséges:

- A **csoport típusának** helytelen beállítása nem megfelelő.

- Helytelen vagy ismétlődő csoportot hozott létre tévedésből. 

- Már nincs szüksége a csoportra.

## <a name="to-delete-a-group"></a>Csoport törlése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**, majd a **csoportok**lehetőséget.

3. A **csoportok – minden csoport** lapon keresse meg és válassza ki a törölni kívánt csoportot. Ezekben a lépésekben a **Mdm Policy-East**használatát fogjuk használni.

    ![Groups-All csoportok lap, csoport neve kiemelve](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. A **Mdm házirend – Kelet áttekintése** lapon válassza a **Törlés**lehetőséget.

    A csoport törölve lett a Azure Active Directory bérlőből.

    ![MDM házirend – Kelet – áttekintés oldal, törlési lehetőség kiemelve](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Következő lépések

- Ha véletlenül töröl egy csoportot, azt újra létrehozhatja. További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása](active-directory-groups-create-azure-portal.md).

- Ha véletlenül töröl egy Microsoft 365 csoportot, lehetséges, hogy vissza tudja állítani. További információ: [törölt Office 365-csoport visszaállítása](../enterprise-users/groups-restore-deleted.md).
