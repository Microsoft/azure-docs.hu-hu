---
title: SKU-információk felülbírálása CSCFG-/CSDEF-Azure Cloud Services (kiterjesztett támogatás)
description: SKU-információk felülbírálása CSCFG-/CSDEF-Azure Cloud Services (kiterjesztett támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739259"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>SKU-információk felülbírálása CSCFG-/CSDEF-Cloud Services (kiterjesztett támogatás) 

Ez a funkció lehetővé teszi a felhasználó számára, hogy anélkül frissítse a szerepkörméretet és a példányok számát a Felhőszolgáltatásban az **allowModelOverride** tulajdonság használatával, hogy frissítenie kell a szolgáltatáskonfigurációt és a szolgáltatásdefiníciós fájlokat, így a felhőszolgáltatás újracsomagolás és újratelepítés nélkül skálázható fel/le/le/ki.

## <a name="set-allowmodeloverride-property"></a>Az allowModelOverride tulajdonság beállítása
Az allowModelOverride tulajdonság a következő módokon beállítható:
* Ha az allowModelOverride = true , az API-hívás anélkül frissíti a felhőszolgáltatás szerepkörméretét és példányszámát, hogy érvényesíti az értékeket a csdef- és cscfg-fájlokkal. 
> [!Note]
> A cscfg frissül a szerepkörpéldányok számának megfelelően, de a csdef (a cspkg-ben) megőrzi a régi értékeket
* Ha az allowModelOverride = false , az API-hívás hibát ad vissza, ha a szerepkörméret és a példányszám értékei nem egyeznek meg a csdef- és cscfg-fájlokkal

Az alapértelmezett érték false (hamis). Ha a tulajdonság visszaáll true (igaz) értékről false (hamis) értékre, a rendszer ismét ellenőrzi a csdef- és cscfg-fájlok érvényesítését.

Tekintse át az alábbi mintákat a tulajdonság PowerShellben, sablonban és SDK-ban való alkalmazásához

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Az "allowModelOverride" = true tulajdonság beállítása itt frissíti a felhőszolgáltatást a roleProfile szakaszban meghatározott szerepkör-tulajdonságokkal
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
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
Az "AllowModelOverride" kapcsoló új New-AzCloudService-parancsmagon való beállítása frissíti a felhőszolgáltatást a RoleProfile-ban meghatározott termékváltozat-tulajdonságokkal
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
Az AllowModelOverride= true változó beállítása frissíti a felhőszolgáltatást a RoleProfile-ban meghatározott termékváltozat-tulajdonságokkal

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
A portál nem engedélyezi, hogy a fenti tulajdonság felülbírálja a csdef és a cscfg szerepkörméretét és példányszámát. 


## <a name="next-steps"></a>Következő lépések 
- Tekintse át [az üzembe helyezés előfeltételeit](deploy-prerequisite.md) a Cloud Services (kiterjesztett támogatás).
- Tekintse [át a gyakori kérdéseket a](faq.md) Cloud Services (kiterjesztett támogatás).
