---
title: Azure DSC-bővítmény Linux rendszerhez
description: A a következő módon telepíti a típusú és DSC-csomagokat, hogy az Azure Linux rendszerű virtuális gép a kívánt állapot-konfiguráció alapján legyen konfigurálva.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.openlocfilehash: 3a9478f93a2a6f8e31be39a58a18e0e394b9d5c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560105"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-bővítmény Linuxra (Microsoft. OSTCExtensions. DSCForLinux)

A kívánt állapot-konfiguráció (DSC) egy olyan felügyeleti platform, amelynek segítségével az informatikai és fejlesztési infrastruktúra felügyelhető kódként való konfigurálásával.

> [!NOTE]
> A Linux rendszerhez készült DSC-bővítmény és a [linux log Analytics virtuálisgép-bővítménye](./oms-linux.md) jelenleg ütközik, és nem támogatott egymás melletti konfigurációban. Ne használja együtt a két megoldást ugyanazon a virtuális gépen.

A DSCForLinux bővítményt a Microsoft közzétette és támogatja. A bővítmény telepíti a és a DSC-ügynököt az Azure Virtual Machines szolgáltatásban. A DSC bővítmény a következő műveleteket is elvégezheti:

- Regisztrálja a linuxos virtuális gépet egy Azure Automation-fiókba a konfigurációk Azure Automation szolgáltatásból való lekéréséhez (ExtensionAction-regisztráció).
- A MOF-konfigurációk leküldése a Linux rendszerű virtuális gépre (leküldéses ExtensionAction).
- Alkalmazza a meta MOF-konfigurációt a Linux rendszerű virtuális gépre egy lekérési kiszolgáló konfigurálásához a csomópont-konfiguráció lekéréséhez (lekéréses ExtensionAction).
- Telepítsen egyéni DSC-modulokat a Linux rendszerű virtuális gépre (telepítse a ExtensionAction-t).
- Távolítsa el az egyéni DSC-modulokat a Linux rendszerű virtuális gépről (távolítsa el a ExtensionAction).

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Linux rendszerű csomópontok esetében a DSC Linux bővítmény támogatja a [POWERSHELL DSC dokumentációjában](/powershell/scripting/dsc/getting-started/lnxgettingstarted)felsorolt összes Linux-disztribúciót.

### <a name="internet-connectivity"></a>Internetkapcsolat

A DSCForLinux-bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakozni lehessen az internethez. A regisztrálási bővítménynek például kapcsolódnia kell az Automation szolgáltatáshoz.
Más műveletek, például a lekéréses, a lekéréses telepítéshez az Azure Storage-hoz és a GitHubhoz kell kapcsolódnia. Ez az ügyfél által megadott beállításoktól függ.

## <a name="extension-schema"></a>Bővítményséma

### <a name="public-configuration"></a>Nyilvános konfiguráció

Az összes támogatott nyilvános konfigurációs paraméter:

* `FileUri`: (nem kötelező, karakterlánc) a MOF-fájl, a meta MOF-fájl vagy az egyéni erőforrás zip-fájljának URI-ja.
* `ResourceName`: (nem kötelező, karakterlánc) az egyéni erőforrás-modul neve.
* `ExtensionAction`: (nem kötelező, karakterlánc) megadja a bővítményt. Az érvényes értékek: regisztrálás, leküldés, lekérés, telepítés és eltávolítás. Ha nincs megadva, a rendszer alapértelmezés szerint leküldéses műveletet számít.
* `NodeConfigurationName`: (nem kötelező, karakterlánc) az alkalmazandó csomópont-konfiguráció neve.
* `RefreshFrequencyMins`: (nem kötelező, int) Megadja, hogy a DSC milyen gyakran próbálja meg beolvasni a konfigurációt a lekérési kiszolgálóról.
       Ha a lekérési kiszolgálón a konfiguráció eltér az aktuálistól, a rendszer átmásolja a függőben lévő tárolóba, és alkalmazza azokat.
* `ConfigurationMode`: (nem kötelező, karakterlánc) Megadja, hogy a DSC hogyan alkalmazza a konfigurációt. Az érvényes értékek a következők: ApplyOnly, ApplyAndMonitor és ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcionális, int) Megadja, hogy milyen gyakran (percben) a DSC biztosítja, hogy a konfiguráció a kívánt állapotban legyen.

> [!NOTE]
> Ha 2,3-nál korábbi verziót használ, a mode paraméter ugyanaz, mint a ExtensionAction. A mód úgy tűnik, hogy túlterhelt kifejezés. A félreértések elkerülése érdekében a ExtensionAction a 2,3-es verziótól kezdődően használatos. A visszamenőleges kompatibilitás érdekében a bővítmény támogatja a módot és a ExtensionAction is.
>

### <a name="protected-configuration"></a>Védett konfiguráció

A támogatott védett konfigurációs paraméterek a következők:

* `StorageAccountName`: (nem kötelező, karakterlánc) a fájlt tartalmazó Storage-fiók neve
* `StorageAccountKey`: (nem kötelező, karakterlánc) a fájlt tartalmazó Storage-fiók kulcsa
* `RegistrationUrl`: (nem kötelező, karakterlánc) a Azure Automation-fiók URL-címe
* `RegistrationKey`: (nem kötelező, karakterlánc) a Azure Automation fiók hozzáférési kulcsa

## <a name="scenarios"></a>Forgatókönyvek

### <a name="register-an-azure-automation-account"></a>Azure Automation fiók regisztrálása

protected.jsbekapcsolva
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.jsbekapcsolva
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>MOF konfigurációs fájl (Azure Storage-fiókban) alkalmazása a virtuális gépre

protected.jsbekapcsolva
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsbekapcsolva
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>MOF konfigurációs fájl (nyilvános tárolóban) alkalmazása a virtuális gépre

public.jsbekapcsolva
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell-formátum
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Meta MOF konfigurációs fájl alkalmazása (Azure Storage-fiókban) a virtuális géphez

protected.jsbekapcsolva
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsbekapcsolva
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Meta MOF konfigurációs fájl (nyilvános tárolóban) alkalmazása a virtuális gépre

public.jsbekapcsolva

```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```

PowerShell-formátum

```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Egyéni erőforrás-modul (egy Azure Storage-fiókban lévő zip-fájl) telepítése a virtuális gépre

protected.jsbekapcsolva

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsbekapcsolva

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell-formátum
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Egyéni erőforrás-modul (egy nyilvános tárolóban lévő zip-fájl) telepítése a virtuális gépre

public.jsbekapcsolva

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}

```

PowerShell-formátum

```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Egyéni erőforrás-modul eltávolítása a virtuális gépről

public.jsbekapcsolva

```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```

PowerShell-formátum

```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. A sablonok ideálisak, ha egy vagy több olyan virtuális gépet telepít, amely a telepítés utáni konfigurálást igényli, például Azure Automation bevezetését.

A minta Resource Manager-sablon a [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) és [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

További információ a Azure Resource Manager sablonnal kapcsolatban: [Azure Resource Manager sablonok készítése](../../azure-resource-manager/templates/template-syntax.md).

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

### <a name="use-azure-cliazure-cli"></a>Az [Azure CLI] [Azure-CLI] használata

A DSCForLinux-bővítmény üzembe helyezése előtt konfigurálja a `public.json` és `protected.json` a 3. szakasz különböző forgatókönyvei alapján.

#### <a name="classic"></a>Klasszikus

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A klasszikus üzembe helyezési módot Azure Service Management üzemmódnak is nevezik. A következő futtatásával válthat:

```
$ azure config mode asm
```

A DSCForLinux-bővítményt a futtatásával is telepítheti:

```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Az elérhető legújabb bővítmény-verzió megismeréséhez futtassa a következőt:

```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager

A Azure Resource Manager módba való váltáshoz futtassa a következőt:

```
$ azure config mode arm
```

A DSCForLinux-bővítményt a futtatásával is telepítheti:

```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

> [!NOTE]
> Azure Resource Manager módban `azure vm extension list` jelenleg nem érhető el.
>

### <a name="use-azure-powershellazure-powershell"></a>A [Azure PowerShell] [Azure-PowerShell] használata

#### <a name="classic"></a>Klasszikus

Az Azure-fiókba való bejelentkezéshez az Azure Service Management módban is bejelentkezhet:

```powershell
Add-AzureAccount
```

És a DSCForLinux-bővítmény üzembe helyezéséhez futtassa a következőket:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosítsa $privateConfig tartalmát, és $publicConfig az előző szakaszban szereplő különböző forgatókönyvek szerint.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Azure Resource Manager módban is bejelentkezhet az Azure-fiókjába a következő futtatásával:

```powershell
Login-AzAccount
```

Ha többet szeretne megtudni a Azure Resource Manager Azure PowerShell használatáról, tekintse meg az [Azure-erőforrások kezelése Azure PowerShell használatával](../../azure-resource-manager/management/manage-resources-powershell.md)című témakört.

A DSCForLinux-bővítményt a futtatásával is telepítheti:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Módosítsa $privateConfig tartalmát, és $publicConfig az előző szakaszban szereplő különböző forgatókönyvek szerint.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portal és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény végrehajtásának kimenete a következő fájlba van naplózva:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Hibakód: a 51 nem támogatott terjesztési vagy nem támogatott kiterjesztési műveletet jelöl.
Bizonyos esetekben a DSC Linux bővítmény nem tudja telepíteni a következőt, ha már létezik a következő, a számítógépen már létező, a-es és újabb változat. [hiba válasza: (000003) a visszalépés nem engedélyezett]

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/community/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések

További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
