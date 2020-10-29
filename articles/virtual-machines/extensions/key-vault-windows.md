---
title: Azure Key Vault virtuálisgép-bővítmény a Windowshoz
description: Telepítsen egy ügynököt, amely a virtuális gépek Key Vault titkainak automatikus frissítését végzi a virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f06c5f2b2938505380ea668a7c4113015c852b1d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913959"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>A Windows rendszerhez készült virtuálisgép-bővítmény Key Vault

A Key Vault virtuálisgép-bővítmény az Azure Key vaultban tárolt tanúsítványok automatikus frissítését teszi lehetővé. Pontosabban, a bővítmény figyeli a Key vaultban tárolt megfigyelt tanúsítványok listáját, és a módosítások észlelése, lekérése és a megfelelő tanúsítványok telepítése után. Ez a dokumentum a Windows rendszerhez készült Key Vault virtuálisgép-bővítmény támogatott platformait, konfigurációit és központi telepítési lehetőségeit részletezi. 

### <a name="operating-system"></a>Operációs rendszer

A Key Vault virtuálisgép-bővítmény a Windows alábbi verzióit támogatja:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

A Key Vault VM-extensio az egyéni helyi virtuális gépeken is támogatott, amelyeket az Azure-ban a Windows Server 2019 Core install használatával tölthet fel és alakít át egy speciális rendszerképbe.

### <a name="supported-certificate-content-types"></a>Támogatott tanúsítvány-tartalomtípusok

- PKCS #12
- PEM

## <a name="prerequisities"></a>Prerequisities
  - Key Vault példány tanúsítvánnyal. Lásd: [Key Vault létrehozása](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)
  - A virtuális gépnek vagy VMSS hozzá kell rendelni a [felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  - Az Key Vault hozzáférési szabályzatot titkos kulcsokkal `get` és engedélyekkel kell beállítani a `list` virtuális gép/VMSS által felügyelt identitáshoz, hogy beolvassák a tanúsítvány egy részét. Tekintse meg, [Hogyan hitelesítheti Key Vault](/azure/key-vault/general/authentication) és [hozzárendelhet egy Key Vault hozzáférési szabályzatot](/azure/key-vault/general/assign-access-policy-cli).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault virtuálisgép-bővítmény sémáját jeleníti meg. A bővítmény nem igényel védett beállításokat – az összes beállítás nyilvános információnak minősül. A bővítményhez meg kell adni a figyelt tanúsítványok listáját, a lekérdezés gyakoriságát és a célhely tanúsítványtárolóját. Ezek konkrétan a következők:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> A megfigyelt tanúsítványok URL-címeinek űrlapnak kell lenniük `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Ennek az az oka, hogy az `/secrets` elérési út a teljes tanúsítványt adja vissza, beleértve a titkos kulcsot is, míg az `/certificates` elérési út nem. A tanúsítványokkal kapcsolatos további információkért tekintse meg a következőt: [Key Vault tanúsítványok](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> A "authenticationSettings" tulajdonság csak a **felhasználó által hozzárendelt identitással** rendelkező virtuális gépek esetében **szükséges** .
> Meghatározza az Key Vault hitelesítéshez használandó identitást.


### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| közzétevő | Microsoft.Azure.KeyVault | sztring |
| típus | KeyVaultForWindows | sztring |
| typeHandlerVersion | 1,0 | int |
| pollingIntervalInS | 3600 | sztring |
| certificateStoreName | MY | sztring |
| linkOnRenewal | hamis | boolean |
| certificateStoreLocation  | LocalMachine vagy CurrentUser (kis-és nagybetűk megkülönböztetése) | sztring |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | karakterlánc-tömb
| msiEndpoint | http://169.254.169.254/metadata/identity | sztring |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | sztring |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a tanúsítványok telepítés utáni frissítését igénylik. A bővítmény az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra is telepíthető. A séma és a konfiguráció a sablonok típusainál is gyakori. 

A virtuálisgép-bővítmények JSON-konfigurációját a sablon virtuálisgép-erőforrás szilánkján belül kell beágyazni, kifejezetten `"resources": []` a virtuálisgép-sablonhoz, és az objektum alatt található virtuálisgép-méretezési csoport esetében `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` .

 > [!NOTE]
> A virtuálisgép-bővítmény megkövetelheti a rendszer vagy a felhasználó által felügyelt identitás hozzárendelését a Key vaulthoz való hitelesítéshez.  Tekintse meg, [Hogyan hitelesítheti Key Vault és hozzárendelhet egy Key Vault hozzáférési szabályzatot.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell üzemelő példány
> [!WARNING]
> A PowerShell-ügyfelek gyakran felvesznek `\` `"` a settings.jsba, amelynek hatására a akvvm_service hibát jelez: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

A Azure PowerShell használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 

* A bővítmény üzembe helyezése egy virtuális gépen:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 
 
* A bővítmény üzembe helyezése egy virtuális gépen:
    
    ```azurecli
       # Start the deployment
         az vm extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Vegye figyelembe a következő korlátozásokat/követelményeket:
- Key Vault korlátozások:
  - A telepítés időpontjában léteznie kell 
  - A Key Vault hozzáférési szabályzatot felügyelt identitás használatával kell beállítani a VM/VMSS identitáshoz. Tekintse meg, [Hogyan hitelesítheti Key Vault](../../key-vault/general/authentication.md) és [hozzárendelhet egy Key Vault hozzáférési szabályzatot](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="frequently-asked-questions"></a>Gyakori kérdések

* A beállítható observedCertificates száma korlátozott?
  Nem, Key Vault virtuálisgép-bővítmény nem korlátozza a observedCertificates számát.

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból és a Azure PowerShell használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell használatával.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Naplók és konfiguráció

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
