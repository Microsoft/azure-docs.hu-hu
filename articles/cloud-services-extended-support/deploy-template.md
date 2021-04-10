---
title: Azure Cloud Service üzembe helyezése (bővített támogatás) – Sablonok
description: Azure Cloud Service (kiterjesztett támogatás) üzembe helyezése ARM-sablonok használatával
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9849648c8a0a76ff89a6f95e64eeade791e7135c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381774"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Cloud Service (kiterjesztett támogatás) üzembe helyezése ARM-sablonok használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre felhőalapú szolgáltatást (kiterjesztett támogatás) az [ARM-sablonok](../azure-resource-manager/templates/overview.md)használatával. 

## <a name="before-you-begin"></a>Előkészületek

1. Tekintse át a Cloud Services [telepítésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás), és hozza létre a kapcsolódó erőforrásokat.

2. Hozzon létre egy új erőforráscsoportot a [Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) vagy a [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md)használatával. Ez a lépés nem kötelező, ha meglévő erőforráscsoportot használ.

3. Hozzon létre egy nyilvános IP-címet, és állítsa be a nyilvános IP-cím DNS-címke tulajdonságát. A Cloud Services (kiterjesztett támogatás) csak az [alapszintű] ( https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU nyilvános IP-címek) használatát támogatja. A standard SKU nyilvános IP-címei nem működnek Cloud Services.
Ha statikus IP-címet használ, akkor a szolgáltatás konfigurációs (. cscfg) fájljában Fenntartott IP kell hivatkozni. Ha meglévő IP-címet használ, hagyja ki ezt a lépést, és adja hozzá az IP-cím adatait közvetlenül az ARM-sablon terheléselosztó konfigurációs beállításaihoz.

4. Hozzon létre egy hálózati profil objektumot, és rendelje hozzá a nyilvános IP-címet a terheléselosztó felületéhez. Az Azure platform automatikusan létrehoz egy "klasszikus" SKU Load Balancer-erőforrást ugyanabban az előfizetésben, mint a Cloud Service-erőforrás. A terheléselosztó erőforrás egy írásvédett erőforrás az ARM-ben. Az erőforrás frissítései csak a Cloud Service telepítési fájljain (. cscfg &. csdef) keresztül támogatottak.
 
5. Hozzon létre egy új Storage-fiókot a [Azure Portal](../storage/common/storage-account-create.md?tabs=azure-portal) vagy a [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)használatával. Ez a lépés nem kötelező, ha meglévő Storage-fiókot használ.

6. Töltse fel a szolgáltatás definíciós (. csdef) és a szolgáltatás konfigurációs (. cscfg) fájljait a Storage-fiókba a [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), a [AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) vagy a [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container)használatával. Szerezze be mindkét fájl SAS URI azonosítóját az oktatóanyag későbbi részében, az ARM-sablonhoz való hozzáadáshoz.

6. Választható Hozzon létre egy kulcstartót, és töltse fel a tanúsítványokat.

    -  A tanúsítványok a szolgáltatással és a szolgáltatásba való biztonságos kommunikáció lehetővé tételéhez csatolhatók a Cloud Serviceshez. A tanúsítványok használatához a ujjlenyomatai megfelelnek meg kell adni a szolgáltatás konfigurációs (. cscfg) fájljában, és fel kell tölteni a kulcstartóba. Kulcstartó hozható létre a [Azure Portal](../key-vault/general/quick-create-portal.md) vagy a [PowerShell](../key-vault/general/quick-create-powershell.md)használatával.
    - A társított Key vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Service.
    - A társított Key vaultnak engedélyezve kell lennie ahhoz, hogy a Cloud Services (kiterjesztett támogatás) erőforrás beolvassa a tanúsítványokat a Key Vaultból. További információ: [tanúsítványok és Key Vault](certificates-and-key-vault.md)
    - A Key vaultot az alábbi lépésekben bemutatott ARM-sablon OsProfile szakaszában kell hivatkozni.

## <a name="deploy-a-cloud-service-extended-support"></a>Cloud Service üzembe helyezése (kiterjesztett támogatás)

> [!NOTE]
> Az ARM-sablon és a paraméter-fájl előállításának egyszerűbb és gyorsabb módja a [Azure Portalon](https://portal.azure.com)keresztül. A [létrehozott ARM-sablon](generate-template-portal.md) a portálon keresztül tölthető le a Cloud Service PowerShell-lel való létrehozásához.
 
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
 

4. Adja hozzá a Key Vault-referenciát az  `OsProfile`   ARM-sablon szakaszában. A Key Vault a Cloud Serviceshoz társított tanúsítványok (kiterjesztett támogatás) tárolására szolgál. Adja hozzá a tanúsítványokat a Key Vaulthoz, majd hivatkozzon a tanúsítvány ujjlenyomatai megfelelnek (. cscfg). Emellett engedélyeznie kell Key Vault "hozzáférési szabályzatokat" az "Azure Virtual Machines for Deployment" (a portálon), hogy a Cloud Services (bővített támogatás) erőforrás lekérheti a titkos kulcsként tárolt tanúsítványt Key Vault. A Key vaultnak ugyanabban a régióban és előfizetésben kell lennie, mint a Cloud Service, és egyedi névvel kell rendelkeznie. További információ: [tanúsítványok használata Cloud Services használatával (kiterjesztett támogatás)](certificates-and-key-vault.md).
     
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
    > A SourceVault az ARM-erőforrás azonosítója a kulcstartóhoz. Ezeket az információkat a kulcstartó tulajdonságok szakaszában található erőforrás-azonosító megkeresésével érheti el.
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
        ]
      }
    }   
    ```

6. Választható Hozzon létre egy bővítmény-profilt a bővítmények Felhőbeli szolgáltatáshoz való hozzáadásához. Ebben a példában a távoli asztal és a Windows Azure Diagnostics bővítményt vesszük fel.
   > [!Note] 
   > A távoli asztal jelszavának 8-123 karakter hosszúnak kell lennie, és meg kell felelnie legalább hármat a jelszó bonyolultsági követelményei közül a következők közül: 1) a 2. kisbetűs karaktert tartalmazza, amely a 3. számjegyet tartalmaz, amely a 4. speciális karaktert tartalmazza, és az 5. különleges karakter nem megengedett.

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
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
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
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
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
          "apiVersion": "2021-03-01",
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
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
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
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Telepítse a sablont és a paramétert (a sablonfájl paramétereit definiálva) a Cloud Service (bővített támogatás) központi telepítésének létrehozásához. Kérjük, szükség szerint tekintse át ezeket a [sablonokat](https://github.com/Azure-Samples/cloud-services-extended-support) .

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Következő lépések 

- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
- Látogasson el a [Cloud Services (bővített támogatás) minták tárházára](https://github.com/Azure-Samples/cloud-services-extended-support)
