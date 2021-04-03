---
title: Egyszerű szolgáltatásnév hozzáadása Azure Analysis Services rendszergazdai szerepkörhöz | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá egy Automation-szolgáltatásnevet a Azure Analysis Services kiszolgálói rendszergazdai szerepkörhöz
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100728"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Egyszerű szolgáltatásnév hozzáadása a kiszolgáló-rendszergazdai szerepkörhöz 

 A felügyelet nélküli PowerShell-feladatok automatizálásához egy egyszerű szolgáltatásnak **rendszergazdai** jogosultságokkal kell rendelkeznie a felügyelt Analysis Services kiszolgálón. Ez a cikk azt ismerteti, hogyan adhat hozzá egy egyszerű szolgáltatást egy Azure-beli kiszolgáló-rendszergazdák szerepkörhöz. Ezt a SQL Server Management Studio vagy egy Resource Manager-sablon használatával teheti meg. 

> [!NOTE]
> Az egyszerű szolgáltatásokat közvetlenül a kiszolgálói rendszergazdai szerepkörhöz kell hozzáadni. Egyszerű szolgáltatásnév hozzáadása egy biztonsági csoporthoz, és a biztonsági csoport hozzáadása a kiszolgálói rendszergazdai szerepkörhöz nem támogatott. 

## <a name="before-you-begin"></a>Előkészületek
A feladat elvégzése előtt rendelkeznie kell egy Azure Active Directoryban regisztrált egyszerű szolgáltatással.

[Szolgáltatásnév létrehozása – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Az SQL Server Management Studio használata

A kiszolgáló-rendszergazdákat SQL Server Management Studio (SSMS) használatával is konfigurálhatja. A feladat elvégzéséhez [kiszolgálói rendszergazdai](analysis-services-server-admins.md) engedélyekkel kell rendelkeznie az Azure-ban kiszolgálóként. 

1. A SSMS-ben kapcsolódjon az Azure-hoz kiszolgálóként.
2. A **kiszolgáló tulajdonságai**  >  **Biztonság** területen kattintson a **Hozzáadás** gombra.
3. A **felhasználó vagy csoport kiválasztása** területen keresse meg a regisztrált alkalmazást név szerint, válassza ki, majd kattintson a **Hozzáadás** gombra.

    ![Egyszerű szolgáltatásnév-fiók keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze az egyszerű szolgáltatás fiókjának AZONOSÍTÓját, majd kattintson **az OK** gombra.
    
    ![Képernyőkép, amely megjeleníti az egyszerű szolgáltatásnév AZONOSÍTÓját, és kiemeli az OK gombot.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Resource Manager-sablon használata

A kiszolgáló-rendszergazdákat úgy is konfigurálhatja, hogy Azure Resource Manager sablonnal telepíti a Analysis Services kiszolgálót. Az üzembe helyezést futtató identitásnak az erőforrás **közreműködői** szerepköréhez kell tartoznia az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)szolgáltatásban.

> [!IMPORTANT]
> Az egyszerű szolgáltatásnevet a formátum használatával kell hozzáadni `app:{service-principal-client-id}@{azure-ad-tenant-id}` .

A következő Resource Manager-sablon központilag telepít egy Analysis Services kiszolgálót egy megadott egyszerű szolgáltatással a Analysis Services rendszergazdai szerepkörhöz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Felügyelt identitások használata

Felügyelt identitást is hozzáadhat a Analysis Services rendszergazdák listájához. Előfordulhat például, hogy egy [logikai alkalmazás egy rendszerhez rendelt felügyelt identitással](../logic-apps/create-managed-service-identity.md)rendelkezik, és biztosítani szeretné a Analysis Services-kiszolgáló felügyeletének lehetőségét.

A Azure Portal és API-k legtöbb részében a felügyelt identitások az egyszerű szolgáltatásnév alapján azonosíthatók. A Analysis Services azonban azt igényli, hogy az ügyfél-AZONOSÍTÓjuk alapján azonosíthatók legyenek. Egy egyszerű szolgáltatás ügyfél-AZONOSÍTÓjának beszerzéséhez használhatja az Azure CLI-t:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Másik lehetőségként használhatja a PowerShellt:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Ezt az ügyfél-azonosítót azután használhatja a bérlői AZONOSÍTÓval együtt, hogy hozzáadja a felügyelt identitást a Analysis Services rendszergazdák listájához a fent leírtak szerint.

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell-modul letöltése](/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](/sql/ssms/download-sql-server-management-studio-ssms)