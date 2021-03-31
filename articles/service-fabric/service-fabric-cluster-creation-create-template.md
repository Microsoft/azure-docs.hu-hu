---
title: Azure Service Fabric-fürt sablonjának létrehozása
description: Megtudhatja, hogyan hozhat létre Resource Manager-sablont egy Service Fabric-fürthöz. A biztonság, a Azure Key Vault és a Azure Active Directory (Azure AD) konfigurálása az ügyfél-hitelesítéshez.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260496"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric fürterőforrás-kezelő sablon létrehozása

Az [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Az Azure-ban futó Service Fabric fürt egy Azure-erőforrás, amelyet a Resource Manager használatával helyeztek üzembe, felügyelnek és figyelnek.  Ez a cikk azt ismerteti, hogyan hozhat létre Resource Manager-sablont az Azure-ban futó Service Fabric-fürtökhöz.  A sablon befejezésekor üzembe helyezheti [a fürtöt az Azure](service-fabric-cluster-creation-via-arm.md)-ban.

A fürt biztonsága a fürt első beállításakor van konfigurálva, és később nem módosítható. Fürt beállítása előtt olvassa el [Service Fabric fürt biztonsági forgatókönyveit][service-fabric-cluster-security]. Az Azure-ban a Service Fabric x509-tanúsítvánnyal védi a fürtöt és a végpontokat, hitelesíti az ügyfeleket és titkosítja az adatait. Azure Active Directory ajánlott a felügyeleti végpontokhoz való hozzáférés biztonságossá tétele is. A fürt létrehozása előtt létre kell hozni az Azure AD-bérlőket és a felhasználókat.  További információért olvassa el az [Azure ad beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című témakört.

Mielőtt éles környezetben üzembe helyezi a termelési feladatokat, először olvassa el az [üzemi készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
A minta Resource Manager-sablonok a [githubon elérhető Azure-mintákon](https://github.com/Azure-Samples/service-fabric-cluster-templates)érhetők el. Ezek a sablonok a fürt sablonjának kiindulási pontként használhatók.

Ez a cikk az [öt csomópontos biztonságos fürt][service-fabric-secure-cluster-5-node-1-nodetype] – példa sablont és a sablon paramétereit használja. Töltse le a *azuredeploy.jst* , és *azuredeploy.parameters.jsa* számítógépre, és nyissa meg mindkét fájlt a kedvenc szövegszerkesztőben.

> [!NOTE]
> Az országos felhők (Azure Government, az Azure China, az Azure Germany) esetében a következőt is hozzá kell adnia a `fabricSettings` sablonhoz: `AADLoginEndpoint` `AADTokenEndpointFormat` és `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Tanúsítványok hozzáadása
Tanúsítványokat adhat hozzá egy fürterőforrás-kezelő sablonhoz a tanúsítvány kulcsait tartalmazó kulcstartóra való hivatkozással. Adja hozzá ezeket a Key-Vault paramétereket és értékeket egy Resource Manager-sablon paramétereinek fájljában (*azuredeploy.parameters.js*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Az összes tanúsítvány hozzáadása a virtuálisgép-méretezési csoport osProfile
A fürtön telepített összes tanúsítványt konfigurálni kell a méretezési csoport erőforrásának **osProfile** szakaszában (Microsoft. számítás/virtualMachineScaleSets). Ez a művelet arra utasítja az erőforrás-szolgáltatót, hogy telepítse a tanúsítványt a virtuális gépekre. Ez a telepítés tartalmazza a fürt tanúsítványát és az alkalmazásokhoz használni kívánt összes biztonsági tanúsítványt:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>A Service Fabric-fürt tanúsítványának konfigurálása

A fürt hitelesítési tanúsítványát mind a Service Fabric fürterőforrás (Microsoft. ServiceFabric/fürtök), mind a virtuálisgép-méretezési csoportokhoz tartozó Service Fabric-bővítménynek kell konfigurálnia a virtuálisgép-méretezési csoport erőforrásaiban. Ez a megoldás lehetővé teszi, hogy a Service Fabric erőforrás-szolgáltató konfigurálja a fürt hitelesítéséhez és a kiszolgálói hitelesítéshez a felügyeleti végpontok számára.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adja hozzá a tanúsítvány adatait a virtuálisgép-méretezési csoport erőforrásához

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>A tanúsítvány adatainak hozzáadása a Service Fabric fürterőforrás számára

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD-konfiguráció hozzáadása az Azure AD ügyfél-hozzáféréshez való használatához

Adja hozzá az Azure AD-konfigurációt egy fürterőforrás-kezelő sablonhoz a tanúsítvány kulcsait tartalmazó kulcstartóra való hivatkozással. Adja hozzá ezeket az Azure AD-paramétereket és-értékeket egy Resource Manager-sablon paramétereinek fájljában (*azuredeploy.parameters.js*). 

> [!NOTE]
> Linux rendszeren a fürt létrehozása előtt létre kell hozni az Azure AD-bérlőket és a felhasználókat.  További információért olvassa el az [Azure ad beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című témakört.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>A paramétert tartalmazó fájl feltöltése az értékekkel

Végül a Key vaultból és az Azure AD PowerShell-parancsokból származó kimeneti értékeket használva töltse fel a paramétereket tartalmazó fájlt.

Ha azt tervezi, hogy az Azure Service Fabric RM PowerShell-modulokat használja, akkor nem kell kitöltenie a fürt tanúsítványával kapcsolatos információkat. Ha azt szeretné, hogy a rendszer a fürt biztonsága érdekében önaláírt tanúsítványt állítson elő, csak nullát kell megőriznie. 

> [!NOTE]
> Ahhoz, hogy az RM-modulok felveszik és feltöltsék ezeket az üres paramétereket, a paraméterek nevei nagyjából megegyeznek az alábbi nevekkel

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Ha alkalmazás-tanúsítványokat használ, vagy egy meglévő fürtöt használ, amelyet a kulcstartóba töltött fel, le kell kérnie ezeket az információkat, és fel kell töltenie azt.

Az RM-modulok nem képesek létrehozni az Azure AD-konfigurációt az Ön számára, így ha az Azure AD-t szeretné használni az ügyfél-hozzáféréshez, fel kell töltenie azt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>A sablon tesztelése
A következő PowerShell-paranccsal tesztelheti a Resource Manager-sablont egy paraméter-fájllal:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Ha problémákba ütközik, és titokzatos üzeneteket kap, használja a "-debug" lehetőséget.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Az alábbi ábra azt szemlélteti, hogy a Key Vault és az Azure AD konfigurációja hogyan illeszkedik a Resource Manager-sablonhoz.

![Resource Manager-függőségi Térkép][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Következő lépések
Most, hogy van egy sablonja a fürthöz, Ismerje meg, hogyan [helyezheti üzembe a fürtöt az Azure](service-fabric-cluster-creation-via-arm.md)-ban.  Ha még nem tette meg, olvassa el az [üzemi készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md) az éles fürt üzembe helyezése előtt.

A cikkben üzembe helyezett erőforrások JSON-szintaxisáról és tulajdonságairól további információt a következő témakörben talál:

* [Microsoft. ServiceFabric/fürtök](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
