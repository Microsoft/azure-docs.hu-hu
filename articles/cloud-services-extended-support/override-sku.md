---
title: SKU-információk felülbírálása az Azure Cloud Services CSCFG/CSDEF (bővített támogatás)
description: SKU-információk felülbírálása az Azure Cloud Services CSCFG/CSDEF (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387318"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>SKU-információk felülbírálása Cloud Services CSCFG/CSDEF (kiterjesztett támogatás) 

Ez a funkció lehetővé teszi a felhasználó számára, hogy frissítse a szerepkör méretét és a példányszámot a **allowModelOverride** tulajdonsággal anélkül, hogy frissítenie kellene a szolgáltatás konfigurációját és a szolgáltatás-definíciós fájlokat, így a felhőalapú szolgáltatás a kicsomagolás és az újbóli üzembe helyezés nélkül bővítheti a Felhőbeli szolgáltatást.

## <a name="set-allowmodeloverride-property"></a>AllowModelOverride tulajdonság beállítása
A allowModelOverride tulajdonság a következő módokon állítható be:
* Ha a allowModelOverride = True értékre van állítva, az API-hívás frissíti a felhőalapú szolgáltatás szerepkör-méretét és-példányszámát anélkül, hogy az értékeket a csdef és a cscfg-fájlokkal érvényesíti. 
> [!Note]
> A rendszer frissíti a cscfg a szerepkör-példányok számának megfelelően, de a csdef (a cspkg belül) megőrzi a régi értékeket.
* Ha a allowModelOverride = False érték jelenik meg, az API-hívás hibát jelez, ha a szerepkör mérete és a példányok száma nem egyezik a csdef és a cscfg-fájllal

Az alapértelmezett érték false (hamis). Ha a tulajdonság értéke TRUE (hamis) értékre van állítva, a rendszer ismét ellenőrzi a csdef és a cscfg-fájlokat.

Tekintse át az alábbi mintákat a tulajdonság a PowerShellben, a sablonban és az SDK-ban való alkalmazásához

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ha a "allowModelOverride" = true tulajdonságot állítja be, akkor a rendszer frissíti a Cloud Service-t a roleProfile szakaszban meghatározott szerepkör-tulajdonságokkal.
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Ha a "AllowModelOverride" kapcsolót az új New-AzCloudService parancsmagon állítja be, a a RoleProfile megadott SKU-tulajdonságokkal frissíti a Cloud Service-t.
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Ha a AllowModelOverride = True változót állítja be, akkor a RoleProfile megadott SKU-tulajdonságokkal frissíti a Cloud Service-t.

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure Portal
A portál nem engedélyezi, hogy a fenti tulajdonság felülbírálja a szerepkör méretét és a példányszámot a csdef és a cscfg. 


## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
