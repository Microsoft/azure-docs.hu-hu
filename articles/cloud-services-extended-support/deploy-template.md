---
title: Azure Cloud Service létrehozása (bővített támogatás) – Sablonok
description: Azure Cloud Service (bővített támogatás) létrehozása ARM-sablonok használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: aeb53e722eae588b7d5e2963afe67f299c2c3565
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745058"
---
# <a name="create-a-cloud-service-extended-support-using-arm-templates"></a>Cloud Service (bővített támogatás) létrehozása ARM-sablonok használatával

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag bemutatja, hogyan hozhat létre felhőalapú szolgáltatást (kiterjesztett támogatás) az [ARM-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)használatával. 

## <a name="before-you-begin"></a>Előkészületek
1. Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozza létre a kapcsolódó erőforrásokat. 

2. Hozzon létre egy új erőforráscsoportot a [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) vagy a [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)használatával. Ez a lépés nem kötelező, ha meglévő erőforráscsoportot használ. 
 
3. Hozzon létre egy új Storage-fiókot a [Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) vagy a [PowerShell](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-powershell)használatával. Ez a lépés nem kötelező, ha meglévő Storage-fiókot használ. 

4. Töltse fel a szolgáltatás definíciós (. csdef) és a szolgáltatás konfigurációs (. cscfg) fájljait a Storage-fiókba a [Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob), a [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) vagy a [PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container)használatával. Szerezze be mindkét fájl SAS URI azonosítóját az oktatóanyag későbbi részében, az ARM-sablonhoz való hozzáadáshoz. 

5. Választható Hozzon létre egy kulcstartót, és töltse fel a tanúsítványokat. 
    -  A tanúsítványok a szolgáltatással és a szolgáltatásba való biztonságos kommunikáció lehetővé tételéhez csatolhatók a Cloud Serviceshez. A tanúsítványok használatához a ujjlenyomatai megfelelnek meg kell adni a szolgáltatás konfigurációs (. cscfg) fájljában, és fel kell tölteni a Kulcstartóba. Key Vault hozható létre a [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) vagy a [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)használatával. 
    - A társított Key Vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Service.   
    - A kapcsolódó Key Vaultnak engedélyezni kell a megfelelő engedélyeket, hogy a Cloud Services (bővített támogatás) erőforrás lekérje a tanúsítványt a Key Vaultból. További információ: [tanúsítványok és Key Vault](certificates-and-key-vault.md)
    - A Key vaultot az alábbi lépésekben bemutatott ARM-sablon OsProfile szakaszában kell hivatkozni.

## <a name="create-a-cloud-service-extended-support"></a>Felhőalapú szolgáltatás létrehozása (bővített támogatás) 
1. Hozzon létre egy virtuális hálózatot. A virtuális hálózat nevének meg kell egyeznie a szolgáltatás konfigurációs (. cscfg) fájljának hivatkozásaival. Ha meglévő virtuális hálózatot használ, hagyja ki ezt a szakaszt az ARM-sablonból.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Új virtuális hálózat létrehozásakor adja hozzá a következőt a `dependsOn` szakaszhoz, és győződjön meg arról, hogy a platform létrehozza a virtuális hálózatot a Cloud Service létrehozása előtt. 

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Hozzon létre egy nyilvános IP-címet, és (opcionálisan) állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. Ha statikus IP-címet használ, hivatkoznia kell rá Fenntartott IPként a szolgáltatás konfigurációs (. cscfg) fájljában. Ha meglévő IP-címet használ, hagyja ki ezt a lépést, és adja hozzá az IP-cím adatait közvetlenül az ARM-sablon terheléselosztó konfigurációs beállításaihoz.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Ha új IP-címet hoz létre, adja hozzá a következőt a `dependsOn` szakaszhoz, és győződjön meg arról, hogy a platform létrehozza az IP-címet a felhőalapú szolgáltatás létrehozása előtt. 
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Hozzon létre egy hálózati profil objektumot, és rendelje hozzá a nyilvános IP-címet a terheléselosztó felületéhez. A platform automatikusan létrehoz egy Load balancert.  

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Adja hozzá a Key Vault-referenciát az  `OsProfile`   ARM-sablon szakaszában. A Key Vault a Cloud Serviceshoz társított tanúsítványok (kiterjesztett támogatás) tárolására szolgál. Adja hozzá a tanúsítványokat a Key Vaulthoz, majd hivatkozzon a tanúsítvány ujjlenyomatai megfelelnek (. cscfg). Emellett engedélyeznie kell Key Vault a megfelelő engedélyekhez, hogy a Cloud Services (bővített támogatás) erőforrás beolvassa a titkos kulcsként tárolt tanúsítványt Key Vault. További információ: [tanúsítványok használata Cloud Services használatával (kiterjesztett támogatás)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > A SourceVault a Key Vault ARM-erőforrás azonosítója. Ezeket az információkat a Key Vault tulajdonságok szakaszában található erőforrás-azonosító megkeresésével érheti el. 
    > - a certificateUrl a **titkos azonosítóként** megjelölt kulcstartóban található tanúsítványhoz való navigálással érhető el.  
   >  - a certificateUrl https://{kulcstartó-endpoin}/Secrets/{secretname}/{Secret-ID} formátumúnak kell lenniük.

5. Hozzon létre egy szerepkör-profilt. Győződjön meg arról, hogy a szerepkörök száma, a szerepkör neve, a példányok száma az egyes szerepkörökben és méretek azonos a szolgáltatás konfigurációja (. cscfg), a Service Definition (. csdef) és a role Profile szakaszban az ARM-sablonban. 
    
    ```json
    "roleProfile": { 
          "roles": { 
          "value": [ 
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
        } 
    ```

6. Választható Hozzon létre egy bővítmény-profilt a bővítmények Felhőbeli szolgáltatáshoz való hozzáadásához. Ebben a példában a távoli asztal és a Windows Azure Diagnostics bővítményt vesszük fel. 
    
    ```json
    "extensionProfile": { 
              "extensions": [ 
                 { 
                  "name": "RDPExtension", 
                  "properties": { 
                    "autoUpgradeMinorVersion": true, 
                    "publisher": "Microsoft.Windows.Azure.Extensions", 
                    "type": "RDP", 
                    "typeHandlerVersion": "1.2.1", 
                    "settings": " <PublicConfig>\r\n  <UserName>>[Insert Password]</UserName>\r\n  <Expiration>1/15/2022 12:00:00 AM</Expiration>\r\n</PublicConfig> ", 
                    "protectedSettings": "<PrivateConfig>\r\n  <Password>[Insert Password]</Password>\r\n</PrivateConfig>" 
                  } 
                } 
              ] 
            },

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
            }
    ```    

7. Tekintse át a teljes sablont. 

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "rdpPublicConfig": {
          "type": "string",
          "metadata": {
            "description": "Public config of remote desktop extension"
          }
        },
        "rdpPrivateConfig": {
          "type": "securestring",
          "metadata": {
            "description": "Private config of remote desktop extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
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
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
        "extensionProfile": {
              "extensions": [
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                    ]
                  }
                },
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "[parameters('rdpPublicConfig')]",
                    "protectedSettings": "[parameters('rdpPrivateConfig')]"
                  }
                }
              ]
            }
          }
        }
      ]
    ```
 
8. Telepítse a sablont, és hozza létre a Cloud Service (bővített támogatás) központi telepítését. 

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName “ContosOrg -TemplateFile "file path to your template file”  
    ```
 
## <a name="next-steps"></a>További lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).