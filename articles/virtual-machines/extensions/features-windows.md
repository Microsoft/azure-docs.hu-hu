---
title: Azure virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez
description: Ismerje meg, hogy mely bővítmények érhetők el az Azure Virtual Machines szolgáltatásban, az általuk biztosított vagy a fejlesztésük szerint csoportosítva.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/30/2018
ms.openlocfilehash: eb1b05afbaa57fb86033c568142da32443adc1f9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559782"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával futtathatók. A bővítmények egy kötegben hajthatók végre az új virtuális gépek üzembe helyezésével, vagy meglévő rendszereken is futtathatók.

Ez a cikk áttekintést nyújt a virtuálisgép-bővítményekről, az Azure virtuálisgép-bővítmények használatára vonatkozó előfeltételekről, valamint útmutatást nyújt a virtuálisgép-bővítmények észleléséhez, kezeléséhez és eltávolításához. Ez a cikk általánosított információkat nyújt, mivel számos virtuálisgép-bővítmény elérhető, amelyek mindegyike potenciálisan egyedi konfigurációval rendelkezik. A bővítményekre vonatkozó részletek az egyes bővítményekre jellemző minden dokumentumban találhatók.

 

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure-beli virtuálisgép-bővítmény érhető el, amelyek mindegyike egy adott használati esettel rendelkezik. Néhány példa:

- PowerShell kívánt állapot-konfiguráció alkalmazása egy virtuális gépre a Windows DSC bővítménnyel. További információ: az [Azure desired State Configuration bővítménye](dsc-overview.md).
- Egy virtuális gép figyelésének konfigurálása a Log Analytics ügynök virtuálisgép-bővítményével. További információ: [Azure-beli virtuális gépek Összekapcsolásának Azure monitor naplók](../../azure-monitor/vm/quick-collect-azurevm.md).
- Azure-beli virtuális gép konfigurálása a Chef használatával. További információ: az Azure-beli [virtuális gépek üzembe helyezésének automatizálása a Chef segítségével](/azure/developer/chef/windows-vm-configure).
- Konfigurálja az Azure-infrastruktúra figyelését az Datadoggal bővítménnyel. További információ: [datadoggal blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


A folyamat-specifikus bővítmények mellett egyéni szkriptek is elérhetők a Windows-és Linux-alapú virtuális gépekhez. A Windowshoz készült egyéni szkriptek lehetővé teszik a PowerShell-parancsfájlok futtatását egy virtuális gépen. Az egyéni parancsfájlok olyan Azure-beli központi telepítések tervezésekor hasznosak, amelyek a natív Azure-eszközök által biztosított konfigurációt igénylik. További információ: a [Windows virtuális gép egyéni parancsfájl-bővítménye](custom-script-windows.md).

## <a name="prerequisites"></a>Előfeltételek

A bővítménynek a virtuális gépen való kezeléséhez telepítenie kell az Azure Windows-ügynököt. Egyes bővítmények előfeltételei, például az erőforrásokhoz vagy függőségekhez való hozzáférés.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure-beli virtuálisgép-ügynök az Azure-beli virtuális gép és az Azure Fabric Controller közötti interakciókat kezeli. A virtuálisgép-ügynök feladata az Azure-beli virtuális gépek üzembe helyezésének és felügyeletének számos funkcionális aspektusa, beleértve a virtuálisgép-bővítmények futtatását is. Az Azure-beli virtuálisgép-ügynök előre telepítve van az Azure Marketplace-lemezképeken, és manuálisan is telepíthető a támogatott operációs rendszereken. A Windows rendszerhez készült Azure-beli virtuálisgép-ügynök a Windows vendég ügynöknek is ismert.

További információ a támogatott operációs rendszerekről és a telepítési utasításokról: [Azure Virtual Machine Agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Támogatott ügynök-verziók

A lehető legjobb élmény biztosítása érdekében az ügynöknek minimális verziója van. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Támogatott operációs rendszer

A Windows vendég ügynök több operációs rendszeren fut, de a kiterjesztések keretrendszere korláttal rendelkezik a bővítmények számára. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Egyes bővítmények nem támogatottak az összes operációs rendszeren, és a *51-es hibakódot (nem támogatott operációs rendszert*) bocsátják ki. Az egyes bővítmények dokumentációjában tájékozódhat a támogatásról.

#### <a name="network-access"></a>Hálózati hozzáférés

A bővítmények letöltése az Azure Storage bővítmény-tárházból történik, és a bővítmény állapotának feltöltése az Azure Storage-ba kerül. Ha az ügynökök [támogatott](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzióját használja, nem kell engedélyeznie az Azure Storage-hoz való hozzáférést a virtuálisgép-régióban, mivel az ügynökkel átirányíthatja a kommunikációt az Azure Fabric-vezérlővel az ügynök-kommunikációhoz (HostGAPlugin funkció a privát IP- [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)található privilegizált csatornán keresztül). Ha az ügynök nem támogatott verzióját használ, engedélyeznie kell a kimenő hozzáférést az adott régióban lévő Azure Storage-hoz a virtuális gépről.

> [!IMPORTANT]
> Ha letiltotta a hozzáférést a *168.63.129.16* a vendég tűzfal vagy egy proxy használatával, a bővítmények a fentiektől függetlenül meghiúsulnak. A 80, 443 és 32526 portok megadása kötelező.

Az ügynököket csak a bővítmény-csomagok és a jelentéskészítési állapotok letöltésére lehet használni. Ha például egy bővítmény telepítéséhez le kell töltenie egy parancsfájlt a GitHubról (egyéni parancsfájlból), vagy hozzá kell férnie az Azure Storage-hoz (Azure Backup), akkor további tűzfal/hálózati biztonsági csoport portjait kell megnyitnia. A különböző kiterjesztések eltérő követelményekkel rendelkeznek, mivel ezek az alkalmazások a saját jogukban vannak. Az Azure Storage-hoz vagy Azure Active Directoryhoz hozzáférést igénylő bővítmények esetében engedélyezheti a hozzáférést az [Azure NSG Service-címkék](../../virtual-network/network-security-groups-overview.md#service-tags) használatával a tárolóhoz vagy a AzureActiveDirectory.

A Windows Guest Agent ügynök nem támogatja a proxykiszolgálót az ügynök forgalmi kéréseinek átirányításához, ami azt jelenti, hogy a Windows vendég ügynöke az egyéni proxyra fog támaszkodni (ha rendelkezik ilyennel) az interneten vagy a gazdagépen lévő erőforrások IP-168.63.129.16 való eléréséhez.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. A teljes lista megtekintéséhez használja a [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). Az alábbi példa felsorolja az összes elérhető bővítményt a *WestUS* helyen:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Virtuálisgép-bővítmények futtatása

Az Azure-beli virtuálisgép-bővítmények már meglévő virtuális gépeken futnak, ami akkor hasznos, ha a konfigurációt módosítani kell, vagy egy már telepített virtuális gépen kell helyreállítani a kapcsolatot. A virtuálisgép-bővítmények Azure Resource Manager sablonok üzembe helyezésével is elhelyezhetők. A bővítmények Resource Manager-sablonokkal való használatával az Azure-beli virtuális gépek üzembe helyezése és konfigurálása a telepítés utáni beavatkozás nélkül végezhető el.

A következő módszerek használhatók a bővítmények egy meglévő virtuális gépen való futtatásához.

### <a name="powershell"></a>PowerShell

Több PowerShell-parancs létezik az egyes bővítmények futtatásához. A lista megtekintéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command) és a Filter on *bővítményt*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Ez a következőhöz hasonló kimenetet nyújt:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Az alábbi példa az egyéni szkriptek bővítmény használatával letölt egy-parancsfájlt egy GitHub-adattárból a cél virtuális gépre, majd futtatja a parancsfájlt. Az egyéni szkriptek bővítményével kapcsolatos további információkért lásd: [Egyéni szkriptek bővítményének áttekintése](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

A következő példában a virtuálisgép-hozzáférési bővítmény egy Windows rendszerű virtuális gép rendszergazdai jelszavának alaphelyzetbe állítására szolgál ideiglenes jelszóként. További információ a virtuális gépek hozzáférési bővítménnyel kapcsolatban: [Távoli asztal szolgáltatás alaphelyzetbe állítása Windows rendszerű virtuális gépen](../troubleshooting/reset-rdp.md). Ha már futtatta a parancsot, állítsa alaphelyzetbe a jelszót az első bejelentkezéskor:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

A `Set-AzVMExtension` parancs használatával bármely virtuálisgép-bővítmény elindítható. További információkért lásd a [set-AzVMExtension referenciát](/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

A virtuálisgép-bővítmények a Azure Portal használatával alkalmazhatók egy meglévő virtuális gépre. Válassza ki a virtuális gépet a portálon, válassza a **bővítmények**, majd a **Hozzáadás** lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait.

Az alábbi példa a Microsoft antimalware bővítmény telepítését mutatja be a Azure Portalról:

![Antimalware-bővítmény telepítése](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezésével hajthatók végre. Ha sablonnal telepít egy bővítményt, létrehozhat teljesen konfigurált Azure-telepítéseket. Például a következő JSON egy Resource Manager-sablonból van üzembe helyezve egy elosztott terhelésű virtuális gép és egy Azure SQL Database, majd telepíti a .NET Core-alkalmazást minden egyes virtuális gépre. A virtuálisgép-bővítmény gondoskodik a szoftver telepítéséről.

További információ: [teljes Resource Manager-sablon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

A Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager sablonok készítése Windowsos VM-bővítményekkel](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmények védelme

Virtuálisgép-bővítmény futtatásakor szükség lehet bizalmas adatok, például a hitelesítő adatok, a Storage-fiókok és a Storage-fiók hozzáférési kulcsainak megadására. Számos virtuálisgép-bővítmény tartalmaz egy védett konfigurációt, amely titkosítást végez, és csak a célként megadott virtuális gépen fejti vissza. Mindegyik bővítmény egy adott védett konfigurációs sémával rendelkezik, és mindegyik a bővítményekre vonatkozó dokumentációban van részletezve.

Az alábbi példa a Windowshoz készült egyéni parancsfájl-bővítmény egy példányát mutatja be. A végrehajtandó parancs a hitelesítő adatok egy készletét tartalmazza. Ebben a példában a végrehajtandó parancs nem titkosított:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Ha a **parancs áthelyezésével végrehajtja** a tulajdonságot a **védett** konfigurációra, a a következő példában látható módon védi a végrehajtási karakterláncot:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

A bővítményeket használó Azure IaaS-alapú virtuális gépeken a tanúsítványok konzolon láthatja, hogy a tulajdonos **_Windows Azure CRP-tanúsítványt létrehozó_** tanúsítványokat tartalmaz. A klasszikus RDFE virtuális gépeken ezek a tanúsítványok a **_Windows Azure Service Management_** szolgáltatással rendelkeznek a kiterjesztésekhez.

Ezek a tanúsítványok garantálják a virtuális gép és annak gazdagépe közötti biztonságos kommunikációt a bővítmények által használt védett beállítások (jelszó, egyéb hitelesítő adatok) átvitele során. A tanúsítványokat az Azure-hálóvezérlő hozza létre, és a virtuálisgép-ügynök számára lesz átadva. Ha naponta leállítja és elindítja a virtuális gépet, előfordulhat, hogy a hálóvezérlő minden nap új tanúsítványt hoz létre. A tanúsítvány tárolása a számítógép Személyes tanúsítványok tárolójában történik. Ezek a tanúsítványok törölhetők. A virtuálisgép-ügynök szükség esetén újra létrehozza a tanúsítványokat.

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan frissülnek az ügynökök és a bővítmények?

Az ügynökök és a bővítmények ugyanazt a frissítési mechanizmust használják. Egyes frissítések esetében nincs szükség további tűzfalszabályok megírására.

Ha egy frissítés elérhető, akkor csak a virtuális gépre van telepítve, ha a bővítmények módosulnak, és más virtuálisgép-modell is változik, például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tárolója
- Vendég operációs rendszer titkai
- Virtuális gép mérete
- Hálózati profil

A kiadók különböző időpontokban teszik elérhetővé a frissítéseket a régiók számára, így lehetséges, hogy különböző régiókban lévő virtuális gépeket különböző verziókban lehet.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Virtuális géphez üzembe helyezett bővítmények listázása

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Ügynök frissítései

A Windows vendég ügynök csak a *bővítmények kezelési kódját* tartalmazza, a *Windows-kiépítési kód* külön. Eltávolíthatja a Windows vendég ügynököt. Az ablakos vendég ügynök automatikus frissítése nem tiltható le.

A *bővítmények kezelésére szolgáló kód* felelős az Azure-hálóval való kommunikációért, valamint a virtuálisgép-bővítmények műveleteinek, például a telepítés, a jelentéskészítési állapot, az egyes bővítmények frissítésének és eltávolításának kezeléséért. A frissítések biztonsági javításokat, hibajavításokat és továbbfejlesztett funkciókat tartalmaznak a *bővítmények kezelési kódjához*.

A futtatott verziót a [telepített Windows-ügynök észlelése](agent-windows.md#detect-the-vm-agent)című témakörben tekintheti meg.

#### <a name="extension-updates"></a>Bővítmények frissítései

Ha elérhető egy bővítmény frissítése, a Windows vendég ügynök letölti és frissíti a bővítményt. Az automatikus bővítmény frissítései lehetnek *kisebbek* vagy *gyorsjavítások*. A bővítmények kiépítésekor engedélyezheti vagy letilthatja a bővítmények *másodlagos* frissítéseit. Az alábbi példa bemutatja, hogyan lehet automatikusan frissíteni a alverziókat egy Resource Manager-sablonban a *autoUpgradeMinorVersion ": true"*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Ha a legújabb kiadási hibákat szeretné lekérni, javasoljuk, hogy mindig válassza az automatikus frissítés lehetőséget a bővítmények üzembe helyezésében. A biztonsági vagy a kulcs hibajavításait nem lehet kijelölni.

### <a name="how-to-identify-extension-updates"></a>Bővítmények frissítéseinek azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Annak azonosítása, hogy a bővítmény a autoUpgradeMinorVersion-mel van-e beállítva egy virtuális gépen

A virtuálisgép-modellből megtekintheti, hogy a bővítmény a "autoUpgradeMinorVersion" használatával lett kiépítve. Az kereséshez használja a [Get-AzVm](/powershell/module/az.compute/get-azvm) , és adja meg az erőforráscsoportot és a virtuális gép nevét a következőképpen:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

A következő példa kimenete azt mutatja, hogy a *autoUpgradeMinorVersion* értéke *true (igaz*):

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Azonosítás a autoUpgradeMinorVersion előfordulásakor

Ha meg szeretné tekinteni, hogy mikor történt frissítés a bővítményre, tekintse át az ügynök naplóit a virtuális gépen a következő címen: *C:\WindowsAzure\Logs\WaAppAgent.log*

A következő példában a virtuális gépnek *Microsoft. számítási. CustomScriptExtension 1,8* telepítve van. A *1,9*-es verzióhoz elérhető gyorsjavítás:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Ügynök engedélyei

A feladatok végrehajtásához az ügynöknek *helyi rendszerként* kell futnia.

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibáinak megoldása

Az egyes virtuálisgép-bővítmények a bővítményre vonatkozó hibaelhárítási lépésekkel rendelkezhetnek. Ha például az egyéni szkriptek bővítményét használja, a parancsfájl-végrehajtás részletei helyileg találhatók azon a virtuális gépen, ahol a bővítményt futtatták. A bővítményekre vonatkozó hibaelhárítási lépések részletesen szerepelnek a bővítményekre vonatkozó dokumentációban.

Az alábbi hibaelhárítási lépések minden virtuálisgép-bővítményre érvényesek.

1. A Windows vendég ügynök naplójának vizsgálatához tekintse meg a tevékenységet, amikor a bővítményt kiépítte a *C:\WindowsAzure\Logs\WaAppAgent.log* -ben

2. A *C:\WindowsAzure\Logs\Plugins \\ <extensionName>* kapcsolatos további részletekért olvassa el a tényleges kiterjesztési naplókat

3. Tekintse meg a bővítmények specifikus dokumentációjának hibaelhárítási szakaszt a hibakódok, ismert problémák stb. esetében.

4. Tekintse meg a rendszernaplókat. Keresse meg azokat a műveleteket, amelyek esetleg zavart okozhattak a bővítménnyel, például egy olyan alkalmazás hosszú futású telepítését, amely kizárólagos csomagkezelő-hozzáférést igényelt.

### <a name="common-reasons-for-extension-failures"></a>A bővítmények meghibásodásának gyakori okai

1. A bővítmények 20 percen keresztül futnak (kivételek a CustomScript Extensions, Chef és DSC, amelyek 90 perc). Ha az üzemelő példány túllépi az időkorlátot, időtúllépésként van megjelölve. Ennek oka az lehet, hogy az alacsony erőforrás-alapú virtuális gépek, más virtuálisgép-konfigurációk/indítási feladatok nagy mennyiségű erőforrást igényelnek, miközben a bővítmény megpróbál kiépíteni.

2. A minimális előfeltételek nem teljesülnek. Egyes bővítmények függőségekkel rendelkeznek a virtuális gépekhez, például a HPC-lemezképekhez. A bővítmények bizonyos hálózati hozzáférési követelményeket igényelhetnek, például az Azure Storage-hoz vagy a nyilvános szolgáltatásokhoz való kommunikációhoz. További példák lehetnek a csomagok tárházához való hozzáférés, a lemezterületek kifogyása vagy a biztonsági korlátozások.

3. Exkluzív csomagkezelő-hozzáférés. Bizonyos esetekben előfordulhat, hogy hosszú ideig futó virtuálisgép-konfigurációt és bővítmények telepítésének ütközését tapasztalja, ahol mindkettőnek kizárólagos hozzáféréssel kell rendelkeznie a csomagkezelő eszközhöz.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

A virtuálisgép-bővítmény virtuális gépen való futtatása után a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával adja vissza a bővítmény állapotát. Az *alállapotok [0]* érték azt jelzi, hogy a bővítmény kiépítése sikeres volt, ami azt jelenti, hogy a virtuális gép üzembe helyezése sikeresen megtörtént, de a virtuális gépen belüli bővítmény végrehajtása nem sikerült, *alállapotok [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A kimenet a következő példában látható kimenethez hasonló:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

A bővítmény végrehajtási állapota a Azure Portalban is megtalálható. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális gépet, válassza a **bővítmények** lehetőséget, majd válassza ki a kívánt kiterjesztést.

### <a name="rerun-vm-extensions"></a>VM-bővítmények újrafuttatása

Előfordulhatnak olyan esetek, amikor egy virtuálisgép-bővítményt újra kell futtatni. A bővítmény újrafuttatásához távolítsa el, majd futtassa újra a bővítményt egy tetszőleges végrehajtási módszer használatával. A bővítmények eltávolításához használja a [Remove-AzVMExtension parancsot](/powershell/module/az.compute/remove-azvmextension) a következő módon:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

A bővítményeket a következőképpen is eltávolíthatja a Azure Portalban:

1. Válasszon ki egy virtuális gépet.
2. Válassza a **bővítmények** lehetőséget.
3. Válassza ki a kívánt kiterjesztést.
4. Válassza az **Eltávolítás** lehetőséget.

## <a name="common-vm-extensions-reference"></a>Gyakori virtuálisgép-bővítmények ismertetése
| Kiterjesztés neve | Leírás | További információ |
| --- | --- | --- |
| Egyéniszkript-bővítmény Windows rendszerre |Parancsfájlok futtatása Azure-beli virtuális gépeken |[Egyéniszkript-bővítmény Windows rendszerre](custom-script-windows.md) |
| DSC-bővítmény a Windowshoz |PowerShell DSC (kívánt állapot konfiguráció) bővítmény |[DSC-bővítmény a Windowshoz](dsc-overview.md) |
| Azure Diagnostics bővítmény |Azure Diagnostics kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM-hozzáférési bővítmény |Felhasználók és hitelesítő adatok kezelése |[VM-hozzáférési bővítmény Linux rendszerhez](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Következő lépések

További információ a virtuálisgép-bővítményekről: [Azure-beli virtuális gépek bővítményei és funkcióinak áttekintése](overview.md).
