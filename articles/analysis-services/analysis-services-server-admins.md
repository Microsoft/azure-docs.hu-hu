---
title: Kiszolgáló-rendszergazdák kezelése a Azure Analysis Servicesban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kezelhetők a kiszolgálók rendszergazdái egy Azure Analysis Services-kiszolgálón a Azure Portal, a PowerShell vagy a REST API-k használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62acb526a247362b17c4dfd4e26c52760deecd71
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573500"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése

A kiszolgálói rendszergazdáknak érvényes felhasználó, szolgáltatásnév vagy biztonsági csoportnak kell lenniük a Azure Active Directoryban (Azure AD) azon bérlő számára, amelyben a kiszolgáló található. A kiszolgálói rendszergazdák kezeléséhez **Analysis Services rendszergazdákat** használhat a Azure Portal, a SSMS, a PowerShellben vagy a REST API kiszolgálói tulajdonságaiban. 

**Biztonsági csoport** hozzáadásakor használja a t `obj:groupid@tenantid` . Az egyszerű szolgáltatások nem támogatottak a kiszolgálói rendszergazdai szerepkörhöz hozzáadott biztonsági csoportokban.

Ha szeretne többet megtudni arról, hogyan adhat hozzá egy egyszerű szolgáltatást a kiszolgálói rendszergazdai szerepkörhöz, tekintse meg [az egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazda szerepkörhöz](analysis-services-addservprinc-admins.md)című témakört.

Ha a kiszolgáló tűzfala engedélyezve van, a kiszolgáló-rendszergazda ügyfélszámítógépek IP-címeinek szerepelniük kell egy tűzfalszabály számára. További információ: a [kiszolgáló tűzfalának konfigurálása](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása a Azure Portal használatával

1. A portálon, a-kiszolgálónál kattintson a **Analysis Services rendszergazdák** elemre.
2. **\<servername> Analysis Services-rendszergazdák** területen kattintson a **Hozzáadás** gombra.
3. A **kiszolgáló-rendszergazdák hozzáadása** lapon válassza ki az Azure ad-beli felhasználói fiókokat, vagy hívja meg a külső felhasználókat e-mail-cím alapján.

    ![Kiszolgálói rendszergazdák a Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása a SSMS használatával

1. Kattintson a jobb gombbal a kiszolgáló > **tulajdonságai** elemre.
2. A **Analysis Server tulajdonságok** területen kattintson a **Biztonság** elemre.
3. Kattintson a **Hozzáadás** gombra, majd adja meg az Azure ad-beli felhasználó vagy csoport e-mail-címét.
   
    ![Kiszolgáló-rendszergazdák hozzáadása a SSMS-ben](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Új kiszolgáló létrehozásakor a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancsmag használatával adhatja meg a rendszergazda paramétert. <br>
A [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) parancsmag használatával módosíthatja egy meglévő kiszolgáló rendszergazdai paraméterét.

## <a name="rest-api"></a>REST API

Új kiszolgáló létrehozásakor a [create](/rest/api/analysisservices/servers/create) paranccsal adhatja meg a asAdministrator tulajdonságot. <br>
Egy meglévő kiszolgáló módosításakor a [frissítés](/rest/api/analysisservices/servers/update) használatával adhatja meg a asAdministrator tulajdonságot. <br>



## <a name="next-steps"></a>Következő lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és-felhasználók kezelése](analysis-services-database-users.md)  
[Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)
