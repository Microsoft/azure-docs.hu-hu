---
title: A PIM használatának megkezdése – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti és kezdheti a Azure AD Privileged Identity Management (PIM) használatát a Azure Portalban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c00824e7fb0529cd14e3d7b757d513ee739fe283
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504413"
---
# <a name="start-using-privileged-identity-management"></a>A Privileged Identity Management használatának első lépései

Ez a cikk a Privileged Identity Management (PIM) engedélyezését és a használatának első lépéseit ismerteti.

A Privileged Identity Management (PIM) használatával felügyelheti, vezérelheti és figyelheti a hozzáférést a Azure Active Directory (Azure AD) szervezeten belül. A PIM segítségével igény szerinti és igény szerint férhet hozzá az Azure-erőforrásokhoz, az Azure AD-erőforrásokhoz és más Microsoft-online szolgáltatásokokhoz, például Microsoft 365okhoz vagy Microsoft Intuneokhoz.

## <a name="prerequisites"></a>Előfeltételek

Privileged Identity Management használatához a következő licencek egyike szükséges:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

> [!Note]
> Ha egy prémium P2 licenccel rendelkező Azure AD-szervezet Kiemelt szerepkörben aktív felhasználója az Azure AD-ben **szerepköröket és rendszergazdákat** keres, és kiválasztja a szerepkört (vagy akár csak Privileged Identity Management):
>
> - Automatikusan engedélyezzük a PIM-t a szervezet számára
> - A saját tapasztalataink szerint a "normál" szerepkör-hozzárendelés vagy egy jogosult szerepkör-hozzárendelés rendelhető hozzá
>
> Ha a PIM engedélyezve van, nincs semmilyen más hatással a szervezetre, hogy aggódnia kell. További hozzárendelési lehetőségeket biztosít, például az aktív és a befejezési időpontot. A PIM Emellett lehetővé teszi a szerepkör-hozzárendelések hatókörének meghatározását a felügyeleti egységek és az egyéni szerepkörök használatával. Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, néhány további e-mailt is indíthat, például a PIM heti kivonatát. Előfordulhat, hogy az MS-PIM szolgáltatásnév is megjelenik a szerepkör-hozzárendeléshez kapcsolódó naplóban. Ez egy várt változás, amelynek nincs hatása a munkafolyamatra.

## <a name="prepare-pim-for-azure-ad-roles"></a>A PIM előkészítése Azure AD-szerepkörökhöz

Az Azure AD-szerepkörök kezeléséhez Privileged Identity Management előkészítéséhez ajánlott feladatok:

1. [Azure ad-szerepkör beállításainak konfigurálása](pim-how-to-change-default-settings.md).
1. [Adja meg a jogosult hozzárendeléseket](pim-how-to-add-role-to-user.md).
1. [Engedélyezze a jogosult felhasználók számára az Azure ad-szerepkör igény szerinti aktiválását](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>A PIM előkészítése az Azure-szerepkörökhöz

Az alábbiakban azokat a feladatokat javasoljuk, amelyekkel előkészítheti Privileged Identity Management az előfizetéshez tartozó Azure-szerepkörök kezeléséhez:

1. [Az Azure-erőforrások felfedezése](pim-resource-roles-discover-resources.md)
1. [Konfigurálja az Azure-szerepkör beállításait](pim-resource-roles-configure-role-settings.md).
1. [Adja meg a jogosult hozzárendeléseket](pim-resource-roles-assign-roles.md).
1. [Lehetővé teheti a jogosult felhasználók számára az Azure-szerepkörök aktiválását](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

A Privileged Identity Management beállítása után megismerheti a módját.

![Navigációs ablak Privileged Identity Management feladatok és kezelési beállítások megjelenítése](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelés | Description |
| --- | --- |
| **Saját szerepkörök**  | Megjeleníti az Önhöz hozzárendelt jogosult és aktív szerepkörök listáját. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Függőben lévő kérések** | Megjeleníti a jogosult szerepkör-hozzárendelések aktiválásához függőben lévő kéréseit. |
| **Kérések jóváhagyása** | Megjeleníti azoknak a kéréseknek a listáját, amelyekkel az Ön által jóváhagyott címtárban lévő felhasználók jogosult szerepköröket aktiválhat. |
| **Hozzáférés áttekintése** | Felsorolja a befejezéshez hozzárendelt aktív hozzáférési felülvizsgálatokat, függetlenül attól, hogy saját maga vagy valaki másnak tekinti-e meg a hozzáférést. |
| **Azure AD-szerepkörök** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure AD szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A saját nézet irányítópult csak az irányítópultot elérő felhasználó adatait jeleníti meg, nem pedig a teljes szervezetet. |
| **Azure-erőforrások** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure-erőforrás szerepkör-hozzárendeléseinek kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A saját nézet irányítópult csak az irányítópultot elérő felhasználó adatait jeleníti meg, nem pedig a teljes szervezetet. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>PIM-csempe hozzáadása az irányítópulthoz

A Privileged Identity Management megnyitásának megkönnyítéséhez adjon hozzá egy PIM-csempét a Azure Portal irányítópulthoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Válassza ki a Privileged Identity Management **gyors üzembe helyezést**.

1. Válassza a **panel rögzítése az irányítópulton** lehetőséget, hogy rögzítse a Privileged Identity Management **gyors üzembe helyezés** lapját az irányítópulton.

    ![Gombostű ikonja Privileged Identity Management lap rögzítése az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton a következőhöz hasonló csempe látható:

    ![Privileged Identity Management gyors üzembe helyezési csempe az irányítópulton](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése Privileged Identity Management](pim-resource-roles-discover-resources.md)
