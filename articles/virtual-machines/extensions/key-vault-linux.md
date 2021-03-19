---
title: A Linux rendszerhez készült virtuálisgép-bővítmény Azure Key Vault
description: Telepítsen egy ügynököt, amely a virtuális gépek Key Vault tanúsítványainak automatikus frissítését végzi a virtuálisgép-bővítmény használatával.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582170"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>A Linux rendszerhez készült virtuálisgép-bővítmény Key Vault

A Key Vault virtuálisgép-bővítmény az Azure Key vaultban tárolt tanúsítványok automatikus frissítését teszi lehetővé. Pontosabban a bővítmény figyeli a Key vaultban tárolt megfigyelt tanúsítványok listáját.  A módosítás észlelése után a bővítmény lekéri és telepíti a megfelelő tanúsítványokat. A Key Vault VM-bővítményt a Microsoft közzétette és támogatja, jelenleg Linux rendszerű virtuális gépeken. Ez a dokumentum részletesen ismerteti a Linux rendszerhez készült Key Vault virtuálisgép-bővítmény támogatott platformokat, konfigurációkat és telepítési lehetőségeit. 

### <a name="operating-system"></a>Operációs rendszer

A Key Vault virtuálisgép-bővítmény támogatja ezeket a Linux-disztribúciókat:

- Ubuntu – 1804
- SUSE – 15 

> [!NOTE]
> A bővített biztonsági funkciók beszerzéséhez készítse elő az Ubuntu-1604 és a Debian-9 rendszerek frissítését, mivel ezek a verziók elérik a kijelölt támogatási időszak végét.
> 

### <a name="supported-certificate-content-types"></a>Támogatott tanúsítvány-tartalomtípusok

- PKCS #12
- PEM


## <a name="prerequisities"></a>Prerequisities
  - Key Vault példány tanúsítvánnyal. Lásd: [Key Vault létrehozása](../../key-vault/general/quick-create-portal.md)
  - A virtuális gépnek vagy VMSS hozzá kell rendelni a [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md)
  - Az Key Vault hozzáférési szabályzatot titkos kulcsokkal `get` és engedélyekkel kell beállítani a `list` virtuális gép/VMSS által felügyelt identitáshoz, hogy beolvassák a tanúsítvány egy részét. Tekintse meg, [Hogyan hitelesítheti Key Vault](../../key-vault/general/authentication.md) és [hozzárendelhet egy Key Vault hozzáférési szabályzatot](../../key-vault/general/assign-access-policy-cli.md).
  -  A VMSS a következő identitás-beállítással kell rendelkeznie: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - A AKV-bővítménynek a következő beállítással kell rendelkeznie: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="key-vault-vm-extension-version"></a>Key Vault virtuálisgép-bővítmény verziója
* Az Ubuntu-18,04 és a SUSE-15 felhasználók a Key Vault virtuálisgép-bővítmény verzióját is frissíthetik, hogy `V2.0` a teljes tanúsítványlánc-letöltési funkciót használják. A kiállítói tanúsítványok (köztes és gyökér) a PEM-fájlban lévő levél-tanúsítványhoz lesznek hozzáfűzve.

* Ha a verzióra szeretne frissíteni `v2.0` , először törölnie kell `v1.0` , majd telepítenie kell `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  A (2,0-es) jelző nem kötelező, mert a legújabb verzió alapértelmezés szerint telepítve lesz.   

* Ha a virtuális gépen a v 1.0 által letöltött tanúsítványok vannak, akkor a v 1.0 AKVVM-bővítmény törlése nem törli a letöltött tanúsítványokat.  A 2.0-s verzió telepítése után a meglévő tanúsítványok nem lesznek módosítva.  Törölnie kell a tanúsítvány fájljait, vagy át kell adnia a tanúsítványt, hogy a PEM-fájl teljes láncú legyen a virtuális gépen.




## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault virtuálisgép-bővítmény sémáját jeleníti meg. A kiterjesztéshez nincs szükség védett beállításokra – az összes beállítás biztonsági hatás nélkül tekinthető információnak. A bővítményhez meg kell adni a figyelt titkok listáját, a lekérdezés gyakoriságát és a célhely tanúsítványtárolóját. Ezek konkrétan a következők:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
| típus | KeyVaultForLinux | sztring |
| typeHandlerVersion | 2.0 | int |
| pollingIntervalInS | 3600 | sztring |
| certificateStoreName | A Linuxon figyelmen kívül lesz hagyva | sztring |
| linkOnRenewal | hamis | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | sztring |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | karakterlánc-tömb
| msiEndpoint | http://169.254.169.254/metadata/identity | sztring |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | sztring |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a tanúsítványok telepítés utáni frissítését igénylik. A bővítmény az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra is telepíthető. A séma és a konfiguráció a sablonok típusainál is gyakori. 

A virtuálisgép-bővítmények JSON-konfigurációját a sablon virtuálisgép-erőforrás szilánkján belül kell beágyazni, kifejezetten `"resources": []` a virtuálisgép-sablonhoz, és az objektum alatt található virtuálisgép-méretezési csoport esetében `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` .

 > [!NOTE]
> A virtuálisgép-bővítmény megkövetelheti a rendszer vagy a felhasználó által felügyelt identitás hozzárendelését a Key vaulthoz való hitelesítéshez.  Tekintse meg, [Hogyan hitelesítheti Key Vault és hozzárendelhet egy Key Vault hozzáférési szabályzatot.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Bővítmény függőségi sorrendje
A Key Vault virtuálisgép-bővítmény támogatja a bővítmények megrendelését, ha be van állítva. Alapértelmezés szerint a bővítmény azt jelenti, hogy az sikeresen elindult, amint megkezdődött a lekérdezés. Azonban beállítható úgy, hogy megvárná, amíg sikeresen letöltötte a tanúsítványok teljes listáját, mielőtt bejelenti a sikeres indítást. Ha más bővítmények attól függnek, hogy a tanúsítványok teljes készlete telepítve van-e az indítás előtt, akkor ez a beállítás lehetővé teszi, hogy ezek a bővítmények a Key Vault-bővítménytől függő függőséget állapítsanak meg. Ezzel megakadályozhatja, hogy ezek a bővítmények csak akkor legyenek elindítva, ha az összes függőben lévő tanúsítvány telepítve lett. A bővítmény újra próbálkozik a kezdeti letöltéssel, és a végleges `Transitioning` állapotban marad.

A beállítás bekapcsolásához állítsa be a következőt:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Megjegyzés A szolgáltatás használata nem kompatibilis egy ARM-sablonnal, amely létrehoz egy rendszerhez rendelt identitást, és frissíti az adott identitással rendelkező Key Vault hozzáférési szabályzatot. Ha így tesz, a rendszer holtpontot eredményez, mivel a tár hozzáférési szabályzata nem frissíthető, amíg az összes bővítmény el nem indul. Ehelyett az üzembe helyezés előtt *egyetlen felhasználó által hozzárendelt MSI-identitást* kell használnia, és a tárolókat előzetesen ACL-ként kell megadnia az identitáshoz.

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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az Azure CLI használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 
 
* A bővítmény üzembe helyezése egy virtuális gépen:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Vegye figyelembe a következő korlátozásokat/követelményeket:
- Key Vault korlátozások:
  - A telepítés időpontjában léteznie kell 
  - A Key Vault hozzáférési szabályzatot felügyelt identitás használatával kell beállítani a VM/VMSS identitáshoz. Tekintse meg, [Hogyan hitelesítheti Key Vault](../../key-vault/general/authentication.md) és [hozzárendelhet egy Key Vault hozzáférési szabályzatot](../../key-vault/general/assign-access-policy-cli.md).

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
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Symlink használata

A szimbolikus hivatkozások vagy a Symlinkek alapvetően speciális billentyűparancsok. Ha el szeretné kerülni a mappa figyelését és a legújabb tanúsítvány automatikus lekérését, ezzel `([VaultName].[CertificateName])` a symlink-vel hozzájuthat a Linux rendszerű tanúsítvány legújabb verziójához.

### <a name="frequently-asked-questions"></a>Gyakori kérdések

* A beállítható observedCertificates száma korlátozott?
  Nem, Key Vault virtuálisgép-bővítmény nem korlátozza a observedCertificates számát.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
