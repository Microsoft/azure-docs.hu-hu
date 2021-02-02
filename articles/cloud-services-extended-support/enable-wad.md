---
title: A Windows Azure Diagnostics bővítmény alkalmazása Cloud Servicesban (bővített támogatás)
description: A Windows Azure Diagnostics bővítmény alkalmazása a Cloud Serviceshoz (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ad2a27d1e41ba8e589aa98542c4a0cb3d92afbea
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430865"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>A Windows Azure Diagnostics bővítmény alkalmazása Cloud Servicesban (bővített támogatás) 
Bármely felhőalapú szolgáltatás fő teljesítménymutatóit nyomon követheti. Minden Cloud Service-szerepkör minimális adatmennyiséget gyűjt: a CPU-használatot, a hálózati használatot és a lemez kihasználtságát. Ha a Cloud Service-ben a Microsoft. Azure. Diagnostics bővítmény van alkalmazva egy szerepkörre, akkor a szerepkör további adatpontokat gyűjthet. További információ: a [bővítmények áttekintése](extensions.md)

A Windows Azure Diagnostics bővítmény engedélyezhető Cloud Services (kiterjesztett támogatás) számára a [PowerShell](deploy-powershell.md) vagy [ARM sablon](deploy-template.md) használatával

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Windows Azure Diagnostics-bővítmény alkalmazása a PowerShell használatával

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFile = "<WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>A Windows Azure Diagnostics bővítmény alkalmazása ARM-sablonnal
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
