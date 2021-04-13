---
title: Azure Policy vendégkonfigurációs bővítmény
description: Tudnivalók a virtuális gépeken belüli beállítások naplózására/konfigurálására használt bővítményről
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368663"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>A vendégkonfiguráció Azure Policy bővítmény áttekintése

A Vendégkonfiguráció bővítmény egy olyan összetevő Azure Policy amely napló- és konfigurációs műveleteket hajt végre a virtuális gépeken.
A Linux és a [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) [](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) biztonsági alapkonfiguráció-definícióihoz hasonló szabályzatok nem ellenőrizhetik a beállításokat a gépeken, amíg a bővítmény nincs telepítve.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a gép hitelesítse magát a vendégkonfigurációs szolgáltatásban, a gépnek rendszer által hozzárendelt felügyelt identitással [kell lennie.](../../active-directory/managed-identities-azure-resources/overview.md)
A virtuális gép identitáskövetelménye akkor teljesül, ha a következő tulajdonság van beállítva.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Operációs rendszerek

A Vendégkonfiguráció bővítmény támogatása ugyanaz, mint a végpontok között megoldáshoz dokumentált operációs rendszer [támogatása.](../../governance/policy/concepts/guest-configuration.md#supported-client-types)

### <a name="internet-connectivity"></a>Internetkapcsolat

A Vendégkonfiguráció bővítmény által telepített ügynöknek el kell érnie a Vendégkonfiguráció-hozzárendelések által felsorolt tartalomcsomagokat, és jelentenie kell az állapotot a Vendégkonfiguráció szolgáltatásnak.
A gép csatlakozhat kimenő HTTPS-kapcsolattal a 443-as TCP-porton keresztül, vagy ha a kapcsolat magánhálózaton keresztül van megtéve.
A magánhálózatokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Vendégkonfiguráció, privát kapcsolaton keresztüli kommunikáció az Azure-ban](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Privát végpontok használata az Azure Storage-hoz](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Hogyan telepítheti a bővítményt?

Ha a bővítmény legújabb verzióját szeretné nagy méretben telepíteni, beleértve az identitásra vonatkozó követelményeket is, rendelje hozzá a következő Azure Policy: Előfeltételek telepítése a vendégkonfigurációs szabályzatok engedélyezéséhez a [virtuális gépeken.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)
Az egyes gépeken a bővítmény üzembe helyezhető az Azure CLI, a PowerShell, Resource Manager sablonok vagy külső eszközök használatával.

A bővítmény példánynevét "AzurePolicyforWindows" vagy "AzurePolicyforLinux" névre kell állítani, mert a fent hivatkozott szabályzatok igénylik ezeket a sztringeket.

Alapértelmezés szerint minden üzemelő példány a legújabb verzióra frissül. Az _autoUpgradeMinorVersion_ tulajdonság értéke alapértelmezés szerint "true" (igaz) értékű, hacsak másként nincs megadva. Nem kell aggódnia a kód frissítése miatt a bővítmény új verzióinak kiadott frissítésekkor.

### <a name="azure-cli"></a>Azure CLI

A bővítmény üzembe helyezése Linux rendszeren:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

A bővítmény központi telepítése Windows rendszeren:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

A bővítmény üzembe helyezése Linux rendszeren:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

A bővítmény központi telepítése Windows rendszeren:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager-sablon

A bővítmény üzembe helyezése Linux rendszeren:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

A bővítmény központi telepítése Windows rendszeren:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

A bővítmény üzembe helyezése Linux rendszeren:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

A bővítmény központi telepítése Windows rendszeren:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Beállítások

A bővítményhez nem kell beállításokat vagy védett beállításokat is tartalmaznia.
Ezeket az adatokat az ügynök a Vendégkonfiguráció [hozzárendelési erőforrásaiból olvassa](/rest/api/guestconfiguration/guestconfigurationassignments) be. A [ConfigurationUri,](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation) [a Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)és a [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) tulajdonságok például konfigurációnként vannak felügyeltek a virtuálisgép-bővítmény helyett.

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy konfigurálásról: A Azure Policy [vendégkonfigurációja](../../governance/policy/concepts/guest-configuration.md)
* A Linux-ügynök és -bővítmények használatával kapcsolatos további információkért lásd: Azure-beli virtuálisgép-bővítmények [és -funkciók Linux rendszeren.](features-linux.md)
* A Windows vendégügynök és a bővítmények használatával kapcsolatos további információkért lásd: [Azure vm extensions and features for Windows (Azure virtuálisgép-bővítmények és -funkciók Windows rendszeren).](features-windows.md)  
* A Windows-vendégügynök telepítéséhez lásd: [Az Azure Windows rendszerű virtuálisgép-ügynök áttekintése.](agent-windows.md)  
* A Linux-ügynök telepítéséhez lásd: [Az Azure Linux rendszerű virtuálisgép-ügynök áttekintése.](agent-linux.md)  
