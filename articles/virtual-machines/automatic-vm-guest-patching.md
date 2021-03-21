---
title: Azure-beli virtuális gépekhez készült automatikus virtuális gép vendégének javítása
description: Ismerje meg, hogyan javíthatja a virtuális gépek automatikus javítását az Azure-ban.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679797"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Előzetes verzió: az Azure-beli virtuális gépekhez készült automatikus virtuális gép vendégének javítása

Az Azure-beli virtuális gépekhez készült automatikus virtuálisgép-javítás engedélyezésével a biztonsági megfelelőség fenntartása érdekében biztonságosan és automatikusan javíthatja a virtuális gépeket.

Az automatikus VM-vendég javításának jellemzői a következők:
- A *kritikus* vagy *biztonsági* besorolású javítások automatikusan letöltődnek és alkalmazhatók a virtuális gépen.
- A javítások a virtuális gép időzónáján kívüli órákon belül lesznek alkalmazva.
- A javítás-előkészítést az Azure felügyeli, és a javítások a [rendelkezésre állási első alapelveket](#availability-first-patching)követik.
- A platform állapotára vonatkozó jelek alapján meghatározott virtuálisgép-állapotot a rendszer figyeli a hibajavítások észlelése érdekében.
- Minden virtuálisgép-mérettel működik.

> [!IMPORTANT]
> Az automatikus VM vendég javítás jelenleg nyilvános előzetes verzióban érhető el. Az alábbiakban ismertetett nyilvános előzetes funkciók használatához egy opt-in eljárás szükséges.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hogyan működik az automatikus VM-vendég-javítás?

Ha az automatikus virtuális gép vendégének javítása engedélyezve van egy virtuális gépen, akkor a rendszer automatikusan letölti és alkalmazza a rendelkezésre álló *kritikus* és *biztonsági* javításokat a virtuális gépen. Ez a folyamat minden hónapban automatikusan indul el, amikor új javításokat bocsátanak ki. A javítások értékelése és telepítése automatikusan történik, és a folyamat a virtuális gép szükség szerinti újraindítását is magában foglalja.

A virtuális gépet az adott virtuális gép megfelelő javításának megállapítása érdekében néhány naponként és többször, a 30 napos időszakon belül többször kell ellenőrizni. A javítások a virtuális gépen a virtuális gép futási ideje alatt akár naponta is telepíthetők. Ez az automatikus értékelés biztosítja, hogy a hiányzó javítások a lehető legkorábbi módon legyenek felderítve.

A javítások a havi javításokat követő 30 napon belül települnek, a rendelkezésre állást követően, az alábbiakban ismertetett első lépéseket követve. A javítások a virtuális gép időzónájától függően csak a virtuális gép időzónája szerint lesznek telepítve. Ahhoz, hogy a javítások automatikusan telepítve legyenek, a virtuális gépnek le kell futnia a leállási idő alatt. Ha egy virtuális gép ki van kapcsolva egy időszakos értékelés során, a rendszer automatikusan értékeli a virtuális gépet, és a rendszer automatikusan telepíti a megfelelő javításokat a következő időszakos értékelés során (általában néhány napon belül), amikor a virtuális gép be van kapcsolva.

A definíciós frissítések és a nem *kritikus* vagy *biztonsági* besorolású javítások nem TELEPÜLNEK automatikusan a virtuális gép vendégének javításával. Ha a javításokat más javítási besorolásokkal szeretné telepíteni, vagy a javítások telepítését a saját egyéni karbantartási időszakán belül szeretné ütemezni, használhatja a [Update Management](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Rendelkezésre állás – első javítás

A javítás telepítési folyamatát az Azure globálisan hangolja össze minden olyan virtuális gép esetében, amelyen engedélyezve van az automatikus VM Guest-javítás. Ez a koordinálás a rendelkezésre állást követi – az első alapelveket az Azure által biztosított különböző szintű rendelkezésre állási szintek között.

A frissítés alatt álló virtuális gépek egy csoportja esetében az Azure platform a frissítéseket fogja összehangolni:

**Régiók között:**
- A globális üzembe helyezési hibák megelőzése érdekében a havi frissítés az Azure-ban globálisan zajlik.
- Egy fázisnak lehet egy vagy több régiója, és a frissítés csak akkor kerül a következő fázisokra, ha a jogosult virtuális gépek sikeresen frissültek.
- A földrajzilag párosított régiók nem frissülnek egyszerre, és nem lehetnek ugyanabban a regionális fázisban.
- A frissítés sikerességét a virtuális gép állapot utáni frissítésének nyomon követésével mérjük. A virtuális gép állapotának nyomon követése a virtuális gép platformjának állapotára vonatkozó mutatókon keresztül történik.

**Régión belül:**
- A különböző Availability Zonesban lévő virtuális gépek egyidejű frissítése nem történik meg.
- A rendelkezésre állási csoport részét nem képező virtuális gépeket a legjobb megoldási szinten kell kiszámítani az előfizetésben lévő összes virtuális gép egyidejű frissítéseinek elkerülése érdekében.

**Egy rendelkezésre állási csoporton belül:**
- A Common rendelkezésre állási csoportokban lévő összes virtuális gép egyidejű frissítése nem történik meg.
-   A Common rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartomány határain belül frissülnek, a több frissítési tartományba tartozó virtuális gépek pedig nem frissülnek egyszerre.

Egy adott virtuális gép javításának telepítési dátuma hónapról hónapra változhat, mivel egy adott virtuális gép a havi javítási ciklusok között egy másik kötegben is megjelenhet.

### <a name="which-patches-are-installed"></a>Mely javítások vannak telepítve?
A telepített javítások a virtuális gép bevezetési szintjétől függenek. A rendszer minden hónapban új globális bevezetést indít el, ahol az egyes virtuális gépekre vonatkozó összes biztonsági és kritikus javítás telepítve van az adott virtuális géphez. A bevezetést a kötegekben lévő összes Azure-régióban összehangoljuk (lásd a fenti rendelkezésre állás – első javítás szakaszban).

A telepítendő javítások pontos készlete a virtuális gép konfigurációja, az operációs rendszer típusa és az értékelés időzítése alapján változhat. Két azonos virtuális gép különböző régiókban is lehetséges a különböző javítások telepítése, ha több vagy kevesebb javítás érhető el, ha a javítási folyamat különböző időpontokban eléri a különböző régiókat. Hasonlóképpen, de ritkábban, ugyanabban a régióban lévő virtuális gépek, de különböző időpontokban (a különböző rendelkezésre állási zónák vagy rendelkezésre állási csoport kötegei miatt) értékelték a különböző javításokat.

Mivel az automatikus virtuális gép vendégének javítása nem konfigurálja a javítási forrást, a különböző javítási forrásokhoz (például a nyilvános adattárhoz és a privát tárházhoz) konfigurált két hasonló virtuális gép is megjelenhet a telepített javítások pontos készletében.

A rögzített lépésszám esetén a javításokat felszabadító operációsrendszer-típusok esetében az operációs rendszer nyilvános tárházához konfigurált virtuális gépek várhatóan ugyanazt a javítást kapják meg a különböző bevezetési fázisok között egy hónapban. Például a Windows rendszerű virtuális gépek a nyilvános Windows Update adattárra vannak konfigurálva.

Mivel az új bevezetést havonta aktiválja, a virtuális gép legalább egy javítást fog kapni havonta, ha a virtuális gép a munkaidőn kívüli idő alatt üzemel. Ez biztosítja, hogy a virtuális gép a legújabb rendelkezésre álló biztonsági és kritikus javításokkal legyen felhasználva havi rendszerességgel. A telepített javítások konzisztenciájának biztosításához beállíthatja a virtuális gépeket, hogy a saját privát tárházai alapján értékeljék és töltse le a javításokat.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációsrendszer-platform lemezképből létrehozott virtuális gépek támogatottak az előzetes verzióban. Az előzetes verzióban jelenleg nem támogatottak az egyéni lemezképek.

A következő platformos SKU-EK jelenleg támogatottak (és a továbbiak rendszeres időközönként bővülnek):

| Publisher               | Operációs rendszer ajánlata      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18,04 – LTS |
| Redhat  | RHEL | 7. x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter – Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Javítások előkészítési módjai
Az Azure-beli virtuális gépek mostantól a következő javítási előkészítési módokat támogatják:

**AutomaticByPlatform:**
- Ez a mód a Linux és a Windows rendszerű virtuális gépek esetében is támogatott.
- Ez a mód lehetővé teszi a virtuális gépek automatikus javítását a virtuális gépen, és az Azure-ban az azt követő javítás telepítését.
- Ez a mód szükséges a rendelkezésre álláshoz – az első javításhoz.
- Ez a mód csak olyan virtuális gépek esetén támogatott, amelyek a fenti támogatott operációsrendszer-platform rendszerképekkel lettek létrehozva.
- Windows rendszerű virtuális gépek esetén ez a mód letiltja a natív automatikus frissítéseket a Windows rendszerű virtuális gépen a Duplikálás elkerülése érdekében.
- Ha ezt a módot Linux rendszerű virtuális gépeken szeretné használni, állítsa be a tulajdonságot `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` a virtuálisgép-sablonban.
- Ha ezt a módot Windows rendszerű virtuális gépeken szeretné használni, állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=true` , és állítsa be a tulajdonságot  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` a virtuálisgép-sablonban.

**AutomaticByOS:**
- Ez a mód csak Windows rendszerű virtuális gépek esetén támogatott.
- Ez a mód lehetővé teszi az automatikus frissítések használatát a Windows rendszerű virtuális gépen, a javítások pedig automatikusan települnek a virtuális gépre.
- Ez a mód nem támogatja a rendelkezésre állást – az első javításokat.
- Ez a mód alapértelmezés szerint be van állítva, ha nincs más javítási mód megadva egy Windows rendszerű virtuális géphez.
- Ha ezt a módot Windows rendszerű virtuális gépeken szeretné használni, állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=true` , és állítsa be a tulajdonságot  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` a virtuálisgép-sablonban.

**Kézi**
- Ez a mód csak Windows rendszerű virtuális gépek esetén támogatott.
- Ez a mód letiltja az automatikus frissítéseket a Windows rendszerű virtuális gépen.
- Ez a mód nem támogatja a rendelkezésre állást – az első javításokat.
- Ezt a módot egyéni javítási megoldások használatakor kell beállítani.
- Ha ezt a módot Windows rendszerű virtuális gépeken szeretné használni, állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=false` , és állítsa be a tulajdonságot  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` a virtuálisgép-sablonban.

**ImageDefault:**
- Ez a mód csak Linux rendszerű virtuális gépek esetén támogatott.
- Ez a mód nem támogatja a rendelkezésre állást – az első javításokat.
- Ez a mód tiszteletben tartja a virtuális gép létrehozásához használt rendszerkép alapértelmezett javítási konfigurációját.
- Ez a mód alapértelmezés szerint be van állítva, ha nincs más javítási mód megadva egy Linux rendszerű virtuális géphez.
- Ha ezt a módot Linux rendszerű virtuális gépeken szeretné használni, állítsa be a tulajdonságot `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` a virtuálisgép-sablonban.

> [!NOTE]
>Windows rendszerű virtuális gépek esetén a tulajdonság `osProfile.windowsConfiguration.enableAutomaticUpdates` jelenleg csak a virtuális gép első létrehozásakor állítható be. A Manuálisról automatikus módra való váltás vagy az automatikus üzemmódok manuális módba való váltása jelenleg nem támogatott. A AutomaticByOS módból a AutomaticByPlatfom módba való váltás támogatott.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>A VM-vendég automatikus javításának engedélyezésére vonatkozó követelmények

- A virtuális gépnek telepítve kell lennie a Windows vagy [Linux](./extensions/agent-linux.md) [rendszerhez](./extensions/agent-windows.md) készült Azure VM-ügynöknek.
- Linux rendszerű virtuális gépek esetében az Azure Linux-ügynöknek 2.2.53.1 vagy újabb verziójúnak kell lennie. [Frissítse a Linux-ügynököt](./extensions/update-linux-agent.md) , ha az aktuális verzió alacsonyabb, mint a szükséges verzió.
- Windows rendszerű virtuális gépek esetén a Windows Update szolgáltatásnak futnia kell a virtuális gépen.
- A virtuális gépnek képesnek kell lennie elérni a konfigurált frissítési végpontokat. Ha a virtuális gép a Windows rendszerű virtuális gépekhez készült linuxos vagy Windows Server Update Services (WSUS) magánhálózati adattárak használatára van konfigurálva, akkor a megfelelő frissítési végpontoknak elérhetőnek kell lenniük.
- A számítási API 2020-12-01-es vagy újabb verzióját használja. A számítási API 2020-06-01-es verziója a korlátozott funkcionalitással rendelkező Windows rendszerű virtuális gépekhez használható.

Az előzetes verzió funkciójának engedélyezéséhez egyszeri bejelentkezés szükséges a szolgáltatások **InGuestAutoPatchVMPreview** és **InGuestPatchVMPreview** , az alábbi szakaszban részletezve.

### <a name="rest-api"></a>REST API
Az alábbi példa azt ismerteti, hogyan engedélyezhető az előfizetés előnézete:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Ha a szolgáltatás regisztrálva van az előfizetéséhez, végezze el a beléptetési folyamatot a számítási erőforrás-szolgáltatóba való propagálásával.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Az előfizetés előnézetének engedélyezéséhez használja a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmagot.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Ha a szolgáltatás regisztrálva van az előfizetéséhez, végezze el a beléptetési folyamatot a számítási erőforrás-szolgáltatóba való propagálásával.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az az [Feature Register](/cli/azure/feature#az-feature-register) paranccsal engedélyezheti az előfizetésének előnézetét.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Ha a szolgáltatás regisztrálva van az előfizetéséhez, végezze el a beléptetési folyamatot a számítási erőforrás-szolgáltatóba való propagálásával.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Automatikus virtuálisgép-vendég-javítás engedélyezése
Ha engedélyezni szeretné az automatikus VM-vendég javítását egy Windows rendszerű virtuális gépen, győződjön meg arról, hogy a virtuálisgép-sablon definíciójában a *osProfile. windowsConfiguration. enableAutomaticUpdates* tulajdonság értéke *true (igaz* ). Ez a tulajdonság csak akkor állítható be, amikor létrehozza a virtuális gépet. Ez a további tulajdonság nem alkalmazható Linux rendszerű virtuális gépek esetén.

### <a name="rest-api-for-linux-vms"></a>REST API Linux rendszerű virtuális gépekhez
Az alábbi példa azt ismerteti, hogyan engedélyezhető az automatikus virtuális gép vendégének javítása:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST API Windows rendszerű virtuális gépekhez
Az alábbi példa azt ismerteti, hogyan engedélyezhető az automatikus virtuális gép vendégének javítása:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell Windows rendszerű virtuális gépekhez
A [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) parancsmag használatával engedélyezheti a virtuális gépek automatikus javítását a virtuális gépek létrehozásakor vagy frissítésekor.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI Windows rendszerű virtuális gépekhez
Az [az VM Create](/cli/azure/vm#az-vm-create) paranccsal engedélyezheti, hogy az új virtuális gép létrehozásakor az automatikus virtuális gép vendége javításokat hozzon létre. A következő példa egy *myVM* nevű virtuális géphez tartozó automatikus virtuális gép vendég-javítását konfigurálja a *myResourceGroup* nevű erőforráscsoporthoz:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Meglévő virtuális gép módosításához használja [az az VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Engedélyezés és Értékelés

> [!NOTE]
>Több mint három órát is igénybe vehet, hogy engedélyezze a virtuális gépek automatikus frissítését egy virtuális gépen, mivel az engedélyezés a virtuális gép távoli nyitvatartási ideje alatt fejeződik be. Mivel az értékelés és a javítás telepítése csak a munkaidőn kívüli órákon belül történik, a virtuális gépnek a javítások alkalmazásához a munkaidőn kívül is futnia kell.

Ha az automatikus virtuális gép vendégének javítása engedélyezve van egy virtuális gépen, akkor a virtuálisgép-bővítmény Linux rendszerű virtuális gépre van telepítve, vagy a virtuálisgép- `Microsoft.CPlat.Core.LinuxPatchExtension` bővítmény egy `Microsoft.CPlat.Core.WindowsPatchExtension` Windows rendszerű virtuális gépre van telepítve. Ezt a bővítményt nem kell manuálisan telepíteni vagy frissíteni, mivel ezt a bővítményt az Azure platform felügyeli az automatikus VM vendég javítási folyamatának részeként.

Több mint három órát is igénybe vehet, hogy engedélyezze a virtuális gépek automatikus frissítését egy virtuális gépen, mivel az engedélyezés a virtuális gép távoli nyitvatartási ideje alatt fejeződik be. A bővítmény telepítése és frissítése is megtörténik a virtuális gép távoli nyitvatartási ideje alatt. Ha a virtuális gép off-Peak órája véget ért az engedélyezés befejezése előtt, akkor az engedélyezési folyamat a következő elérhető időpontnál folytatódik.

Az automatikus frissítések a legtöbb esetben le vannak tiltva, és a javítás telepítése a bővítményen keresztül történik. A következő feltételek érvényesek.
- Ha egy Windows rendszerű virtuális gép korábban automatikus Windows Update bekapcsolta a AutomaticByOS-javító módot, akkor az automatikus Windows Update ki van kapcsolva a virtuális géphez a bővítmény telepítésekor.
- Ubuntu rendszerű virtuális gépek esetén az alapértelmezett automatikus frissítések automatikusan le lesznek tiltva, amikor a virtuális gép vendégének automatikus javítása befejeződött.
- A RHEL automatikus frissítéseit manuálisan le kell tiltani (ez az előzetes verzióra vonatkozó korlátozás). Végrehajtása

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Annak ellenőrzéséhez, hogy befejeződött-e az automatikus virtuális gép vendégének javítása, és a javítási bővítmény telepítve van-e a virtuális gépen, áttekintheti a virtuális gép példányának nézetét. Ha az engedélyezési folyamat befejeződött, a bővítmény telepítve lesz, és a virtuális gép értékelésének eredményei elérhetők lesznek a alatt `patchStatus` . A virtuális gép példányának nézete több módon is elérhető az alább leírtak szerint.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Get-AzVM](/powershell/module/az.compute/get-azvm) parancsmagot a `-Status` PARAMÉTERREL a virtuális gép példány nézetének eléréséhez.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

A PowerShell jelenleg csak a patch bővítménnyel kapcsolatos információkat tartalmazza. A szolgáltatással kapcsolatos információk `patchStatus` hamarosan a powershellen keresztül is elérhetők lesznek.

### <a name="azure-cli"></a>Azure CLI
Az az [VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) paranccsal érheti el a virtuális gép példányának nézetét.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>A virtuális gép javítási állapotának ismertetése

A `patchStatus` példány nézet válaszának szakasza részletesen ismerteti a virtuális gép legújabb értékelését és utolsó javításának telepítését.

A virtuális gép értékelésének eredményeit a szakasz alatt tekintheti át `availablePatchSummary` . A rendszer időszakos vizsgálatot végez egy olyan virtuális gép esetében, amelyen engedélyezve van az automatikus virtuális gép vendégének javítása. A rendelkezésre álló javítások száma az értékelés után `criticalAndSecurityPatchCount` és a `otherPatchCount` találatok között. Az automatikus virtuális gép vendégének javítása a *kritikus* és *biztonsági* javítási besorolások alapján kiértékelésre kerülő összes javítást telepíti. A rendszer kihagyja a többi felmért javítást.

A virtuális gép javításának telepítési eredményeit a szakasz alatt tekintheti át `lastPatchInstallationSummary` . Ez a szakasz részletesen ismerteti a virtuális gépen az utolsó javítás telepítési kísérletét, beleértve a telepített, függőben lévő, sikertelen vagy kihagyott javítások számát is. A javítások telepítése csak a virtuális gép kiinduló órákon kívüli karbantartási időszakában történik. A függőben lévő és a sikertelen javítások automatikusan újrapróbálkoznak a következő off-Peak óra karbantartási időszakában.

## <a name="on-demand-patch-assessment"></a>Igény szerinti javítás értékelése
Ha az automatikus virtuális gép vendégének javítása már engedélyezve van a virtuális gépen, a virtuális gép időszakos javítási értékelését a virtuális gép futási ideje alatt végezheti el. Ez a folyamat automatikusan történik, és a legújabb értékelés eredményei áttekinthetők a virtuális gép példányának nézetében, a jelen dokumentum korábbi részében leírtak szerint. Bármikor elindíthat egy igény szerinti javítási értékelést is a virtuális géphez. A javítás értékelése eltarthat néhány percig, és a legújabb értékelés állapota frissül a virtuális gép példányának nézetében.

Az előzetes verzió funkciójának engedélyezéséhez egyszeri bejelentkezésre van szükség a **InGuestPatchVMPreview** szolgáltatás előfizetéséhez. Ez a szolgáltatás előzetes verziója eltér a virtuális gépek automatikus javítási funkciójának a **InGuestAutoPatchVMPreview**-ben végzett regisztrációjának. A további funkció előzetes verziójának engedélyezése külön és további követelmény. Az igény szerinti javítás értékelése funkció előzetes verziójának engedélyezése a korábban ismertetett [előzetes engedélyezési folyamat](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) után engedélyezhető a virtuális gép vendégének javításához.

> [!NOTE]
>Az igény szerinti javítás kiértékelése nem aktiválja automatikusan a javítás telepítését. Ha engedélyezte az automatikus virtuális gép vendég javítását, akkor a virtuális gép kiértékelése és az arra vonatkozó javítások a virtuális gép leállási ideje alatt lesznek telepítve, a jelen dokumentumban korábban ismertetett rendelkezésre állási – első javítási folyamat után.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Meghívási-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) parancsmag használatával kivizsgálhatja a virtuális gép elérhető javításait.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Használja az [az VM értékelés-Patchs](/cli/azure/vm#az-vm-assess-patches) lehetőséget a virtuális gép elérhető javításának értékeléséhez.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [További információ a Windows rendszerű virtuális gépek létrehozásáról és kezeléséről](./windows/tutorial-manage-vm.md)
