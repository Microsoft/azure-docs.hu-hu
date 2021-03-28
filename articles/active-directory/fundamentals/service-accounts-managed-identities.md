---
title: Felügyelt identitások biztonságossá tétele a Azure Active Directoryban
description: A felügyelt identitások biztonságának megállapítása, felmérése és javítása.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b08231f254ea47fc3c9d65de42966301bd3378f
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640052"
---
# <a name="securing-managed-identities"></a>Felügyelt identitások biztonságossá tétele

A fejlesztők gyakran a különböző szolgáltatások közötti kommunikáció biztonságossá tételéhez használt titkok és hitelesítő adatok kezelésével szembesülnek. A felügyelt identitások biztonságos Azure Active Directory (Azure AD) identitások, amelyek az Azure-erőforrások identitásának biztosításához lettek létrehozva.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitások használatának előnyei

A felügyelt identitások használatának előnyei a következők:

* Nem kell kezelnie a hitelesítő adatokat. A felügyelt identitásokkal a hitelesítő adatok teljes mértékben kezelhetők, elforgatva és az Azure által védettek. Az identitások automatikusan elérhetők és törlődnek az Azure-erőforrásokkal. A felügyelt identitások lehetővé teszik az Azure-erőforrások számára az Azure AD-hitelesítést támogató összes szolgáltatás közötti kommunikációt.

* A hitelesítő adatokhoz nem lehet hozzáférni (beleértve a globális rendszergazdát is), így a nem tudja véletlenül kiszivárogni, például a kód részét képezi.

## <a name="when-to-use-managed-identities"></a>Mikor kell felügyelt identitásokat használni?

A felügyelt identitások leginkább az Azure AD-hitelesítést támogató szolgáltatások közötti kommunikációhoz használatosak. 

A forrásoldali rendszer hozzáférést kér a célhoz tartozó szolgáltatáshoz. Bármely Azure-erőforrás lehet egy forrásoldali rendszer. Például egy Azure-beli virtuális gép, az Azure Function példány és az Azure App Services-példányok támogatják a felügyelt identitásokat.

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>A hitelesítés és az engedélyezés működése

Felügyelt identitások esetén a forrásrendszer az Azure AD-ből származó jogkivonatot szerezhet be anélkül, hogy a forrás tulajdonosának a hitelesítő adatokat kellene kezelnie. Az Azure kezeli a hitelesítő adatokat. A forrásrendszer által beszerzett jogkivonatot a rendszer a hitelesítési célra mutatja be. 

A hozzáférés engedélyezése előtt a célként megadott rendszeren hitelesítenie kell magát (azonosítania kell), és engedélyeznie kell a forrásrendszer számára. Ha a TARGET szolgáltatás támogatja az Azure AD-alapú hitelesítést, akkor elfogad egy Azure AD által kiadott hozzáférési jogkivonatot. 

Az Azure vezérlési síkon és adatsíkon is rendelkezik. A vezérlő síkon erőforrásokat hoz létre, és az adatsíkon, amelyhez hozzáfér. Létrehozhat például egy Cosmos-adatbázist a vezérlési síkon, de lekérdezheti azt az adatsíkon.

Ha a célként megadott rendszer elfogadja a jogkivonatot a hitelesítéshez, a vezérlési síkon és az adatsíkon eltérő mechanizmusokat is támogathat.

Az Azure összes vezérlési síkra vonatkozó műveletét a [Azure Resource Manager](../../azure-resource-manager/management/overview.md) felügyeli, és az [Azure szerepköralapú Access Control](../../role-based-access-control/overview.md)használja. Az adatsíkon minden célrendszer saját engedélyezési mechanizmussal rendelkezik. Az Azure Storage támogatja az Azure-RBAC az adatsíkon. Az Azure App Servicest használó alkalmazások például beolvashatják az Azure Storage-ból származó adatokat, és az Azure Kubernetes szolgáltatást használó alkalmazások beolvashatják a Azure Key Vaultban tárolt titkos kódokat.

További információ a vezérlési és az adatsíkokról: [vezérlési sík és adatsík műveletek – Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md).

Az összes Azure-szolgáltatás végül támogatni fogja a felügyelt identitásokat. További információ: [felügyelt identitásokat támogató szolgáltatások az Azure-erőforrásokhoz](../managed-identities-azure-resources/services-support-managed-identities.md).

##  

## <a name="types-of-managed-identities"></a>A felügyelt identitások típusai

A felügyelt identitások két típusa létezik – a rendszer által hozzárendelt és a felhasználó által hozzárendelt.

A rendszer által hozzárendelt felügyelt identitás a következő tulajdonságokkal rendelkezik:

* 1:1 kapcsolattal rendelkeznek az Azure-erőforrással. Például egy egyedi felügyelt identitás van társítva az egyes virtuális gépekhez.

* Ezek az Azure-erőforrások életciklusával vannak társítva. Az erőforrás törlésekor a hozzá társított felügyelt identitás automatikusan törlődik, ami kiküszöböli az árva fiókokkal kapcsolatos kockázatokat. 

A felhasználó által hozzárendelt felügyelt identitások a következő tulajdonságokkal rendelkeznek:

* Ezen identitások életciklusa független az Azure-erőforrástól, és az életciklust kell kezelnie. Az Azure-erőforrás törlésekor a hozzárendelt felhasználóhoz rendelt felügyelt identitás nem törlődik automatikusan.

* Egyetlen felhasználó által hozzárendelt felügyelt identitás nulla vagy több Azure-erőforráshoz is hozzárendelhető.

* Ezek az idő előtt létrehozhatók, majd hozzárendelhetők egy erőforráshoz.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Felügyelt identitási egyszerű szolgáltatások keresése az Azure AD-ben

A felügyelt identitások többféleképpen is megtalálhatók:

* A Azure Portal vállalati alkalmazások lapjának használata

* Microsoft Graph használata

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

1. A Azure Active Directory területen válassza a vállalati alkalmazások lehetőséget.

2. A "felügyelt identitások" szűrő kiválasztása 

   ![A minden alkalmazás képernyő képe, amelyen az alkalmazás típusa legördülő menü "felügyelt identitások" elemét kiemelve jelenik meg.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph használata

A bérlő összes felügyelt identitásának listáját a következő GET-kéréssel érheti el Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Ezeket a kéréseket szűrheti. További információ: a [servicePrincipal beolvasása](/graph/api/serviceprincipal-get)a Graph dokumentációjában.

## <a name="assess-the-security-of-managed-identities"></a>A felügyelt identitások biztonságának felmérése 

A felügyelt identitások biztonságát a következő módokon tudja értékelni:

* Vizsgálja meg a jogosultságokat, és győződjön meg arról, hogy a legalacsonyabb jogosultsági szintű modell van kiválasztva. Használja a következő PowerShell-parancsmagot a felügyelt identitásokhoz hozzárendelt engedélyek beszerzéséhez.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Győződjön meg arról, hogy a felügyelt identitás nem tagja a Kiemelt csoportok, például a rendszergazdák csoportnak.  
Ezt a magas jogosultsági szintű csoportok tagjainak a PowerShell-lel való enumerálásával teheti meg.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Gondoskodjon arról, hogy a felügyelt identitás milyen erőforrásokhoz férhet hozzá](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>Áthelyezés a felügyelt identitásokra

Ha egyszerű szolgáltatást vagy Azure AD-felhasználói fiókot használ, akkor értékelje ki, hogy egy felügyelt identitás használatával nem szükséges-e a hitelesítő adatok védetté, elforgatását és kezelését végezni. 

## <a name="next-steps"></a>Következő lépések

**A felügyelt identitások létrehozásával kapcsolatos információkért lásd:** 

[Hozzon létre egy felhasználóhoz rendelt felügyelt identitást](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

[Rendszerhez rendelt felügyelt identitás engedélyezése az erőforrások létrehozásakor](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[Rendszerhez rendelt felügyelt identitás engedélyezése meglévő erőforráson](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**A szolgáltatásfiókok további információit lásd:**

[Azure Active Directory szolgáltatásfiókok bemutatása](service-accounts-introduction-azure.md)

[Az egyszerű szolgáltatások biztonságossá tétele](service-accounts-principal.md)

[Az Azure-szolgáltatásfiókok szabályozása](service-accounts-governing-azure.md)

[A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

 

 

