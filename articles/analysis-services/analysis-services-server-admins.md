---
title: Kiszolgáló-rendszergazdák kezelése a Azure Analysis Services | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kezelheti egy Azure Analysis Services-kiszolgáló kiszolgálói rendszergazdáit a Azure Portal, a PowerShell vagy a REST API-k használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769192"
---
# <a name="manage-server-administrators"></a>A kiszolgálók rendszergazdáinak kezelése

A kiszolgáló-rendszergazdáknak érvényes felhasználónak, szolgáltatásnévnek vagy biztonsági csoportnak kell lennie a Azure Active Directory -bérlő (Azure AD) szolgáltatásában, amelyben a kiszolgáló található. A kiszolgáló rendszergazdái **Analysis Services** a Azure Portal, a Kiszolgáló tulajdonságai az SSMS-ban, a PowerShellben vagy a REST API kezelhetik. 

Biztonsági csoport **hozzáadásakor használja** a et. `obj:groupid@tenantid` A szolgáltatásnév nem támogatott a kiszolgálói rendszergazdai szerepkörhöz hozzáadott biztonsági csoportokban.

További információ szolgáltatásnév kiszolgálói rendszergazdai szerepkörhöz való hozzáadásáról: Szolgáltatásnév hozzáadása a kiszolgálói [rendszergazdai szerepkörhöz.](analysis-services-addservprinc-admins.md)

Ha a kiszolgálói tűzfal engedélyezve van, a kiszolgáló-rendszergazdai ügyfélszámítógép IP-címeit bele kell foglalni egy tűzfalszabályba. További információ: [Kiszolgálói tűzfal konfigurálása.](analysis-services-qs-firewall.md)

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Kiszolgáló-rendszergazdák hozzáadása a Azure Portal

1. A portálon a kiszolgálóhoz kattintson a Analysis Services **elemre.**
2. A **\<servername> - Analysis Services oldalon kattintson az** Add **(Hozzáadás) gombra.**
3. A **Kiszolgálói rendszergazdák hozzáadása beállításnál** válasszon ki felhasználói fiókokat az Azure AD-ból, vagy hívjon meg külső felhasználókat e-mail-cím alapján.

    ![Kiszolgálói rendszergazdák a Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Kiszolgáló-rendszergazdák hozzáadása az SSMS használatával

1. Kattintson a jobb gombbal a > **elemre.**
2. A **Analysis Server tulajdonságok között kattintson** a Biztonság **elemre.**
3. Kattintson **az Add (Hozzáadás)** gombra, majd adja meg az Azure AD-ben egy felhasználó vagy csoport e-mail-címét.
   
    ![Kiszolgáló-rendszergazdák hozzáadása az SSMS-hez](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Új kiszolgáló létrehozásakor használja a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancsmagot a Rendszergazda paraméter megadásához. <br>
Meglévő kiszolgáló Rendszergazda paraméterének módosításához használja a [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) parancsmagot.

## <a name="rest-api"></a>REST API

Új [kiszolgáló](/rest/api/analysisservices/servers/create) létrehozásakor használja a Létrehozást az asAdministrator tulajdonság megadásához. <br>
Meglévő [kiszolgáló](/rest/api/analysisservices/servers/update) módosításakor használja az Update (Frissítés) tulajdonságot az asAdministrator tulajdonság megadásához. <br>



## <a name="next-steps"></a>Következő lépések 

[Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md)  
[Adatbázis-szerepkörök és -felhasználók kezelése](analysis-services-database-users.md)  
[Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)
