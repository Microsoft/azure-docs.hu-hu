---
title: Felügyelt identitás egyszerű szolgáltatásnév megtekintése a Azure Portal-Azure AD-ben
description: Részletes útmutató a felügyelt identitások szolgáltatásának a Azure Portalban való megtekintéséhez.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003735"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Felügyelt identitás egyszerű szolgáltatásnév megtekintése a Azure Portal

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását a Azure Portal használatával.

 > [!NOTE] 
 > Az egyszerű szolgáltatások vállalati alkalmazások. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) vagy [alkalmazáson](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez az eljárás azt mutatja be, hogyan lehet megtekinteni egy virtuális gép egyszerű szolgáltatását, amelyen engedélyezve van a rendszer hozzárendelt identitása (az alkalmazásokra ugyanazok a lépések érvényesek).

1. Kattintson a **Azure Active Directory** , majd a **vállalati alkalmazások** elemre.
2. Az **alkalmazás típusa** területen válassza a **minden alkalmazás** lehetőséget, majd kattintson az **alkalmaz** gombra.
3. A keresési szűrő mezőbe írja be annak az Azure-erőforrásnak a nevét, amelyen engedélyezve van a felügyelt identitás, vagy válassza ki a megjelenített listából.

   ![Felügyelt identitás szolgáltatásnév megtekintése a portálon](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Következő lépések

[Azure-erőforrások felügyelt identitásai](./overview.md)