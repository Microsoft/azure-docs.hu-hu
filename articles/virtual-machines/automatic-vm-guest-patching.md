---
title: Automatikus virtuálisgép-vendégjavítás Azure-beli virtuális gépekhez
description: Ismerje meg, hogyan javíthatók automatikusan a virtuális gépek az Azure-ban.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762586"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Előzetes verzió: Automatikus virtuálisgép-vendégjavítás Azure-beli virtuális gépekhez

Az Azure-beli virtuális gépek automatikus vendégjavításának engedélyezése megkönnyíti a frissítések kezelését a virtuális gépek biztonságos és automatikus javításával a biztonsági megfelelőség fenntartása érdekében.

Az automatikus virtuálisgép-vendégjavítások a következő jellemzőkkel rendelkeznek:
- A kritikus vagy *biztonsági* besorolású javítások *automatikusan* letöltve és alkalmazva vannak a virtuális gépen.
- A javítások a virtuális gép időzónájában, csúcsidőn kívül vannak alkalmazva.
- A patch vezénylést az Azure kezeli, és a javítások a rendelkezésre [állásra vonatkozó alapelvek alapján vannak alkalmazva.](#availability-first-patching)
- A rendszer a platform állapotjelzői alapján meghatározott virtuális gépek állapotát figyeli a javítási hibák észlelése érdekében.
- Minden virtuálisgép-mérethez használható.

> [!IMPORTANT]
> Az automatikus virtuálisgép-vendégjavítás jelenleg nyilvános előzetes verzióban érhető el. Az alább ismertetett nyilvános előzetes verziójú funkciók használatához egy lemondási eljárásra van szükség.
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hogyan működik a virtuális gépek automatikus vendégjavítása?

Ha egy virtuális gépen engedélyezve van az automatikus  virtuálisgép-vendégjavítás, akkor a rendszer automatikusan letölti és alkalmazza az elérhető kritikus és biztonsági javításokat a virtuális gépen.  Ez a folyamat minden hónapban automatikusan elindul, amikor új javítások megjelenik. A javítások felmérése és telepítése automatikus, és a folyamat szükség szerint magában foglalja a virtuális gép újraindítását.

A rendszer néhány naponta és 30 napon belül többször ellenőrzi a virtuális gépet, hogy megállapítsa a virtuális gép megfelelő javításokat. A javítások bármely nap telepíthetők a virtuális gépre a virtuális gép csúcsidőn kívüli időszakában. Ez az automatikus értékelés biztosítja, hogy a hiányzó javítások a lehető leghamarabb felderítve legyenek.

A javítások telepítése a havi javítások kiadását követő 30 napon belül történik, az alábbiakban ismertetett, rendelkezésre állásra vonatkozó első vezénylést követően. A javítások telepítése a virtuális gép időzónája alapján csak a virtuális gép csúcsidőn kívüli időszakában lesz telepítve. A javítások automatikus telepítésének a csúcsidőn kívül kell futnia. Ha egy virtuális gép rendszeres értékelés során ki van kapcsolva, a rendszer automatikusan értékeli a virtuális gépet, és a megfelelő javításokat a rendszer automatikusan telepíti a következő rendszeres értékelés során (általában néhány napon belül), amikor a virtuális gép be van kapcsolva.

A definíciófrissítések és a  nem kritikus vagy biztonsági besorolású egyéb javítások *nem* lesznek telepítve a virtuális gépek vendégének automatikus javításával. Ha más javításbesorolásokkal vagy a saját egyéni karbantartási időszakán belül ütemezi a javítástelepítést, a következőt [használhatja: Update Management.](./windows/tutorial-config-management.md#manage-windows-updates)

### <a name="availability-first-patching"></a>Első rendelkezésre állási javítás

A javítástelepítési folyamatot az Azure globálisan vezényelte minden olyan virtuális géphez, amelyeken engedélyezve van az automatikus virtuálisgép-vendégjavítás. Ez a vezénylés a rendelkezésre állásra vonatkozó alapelveket követi az Azure által biztosított különböző rendelkezésre állási szinteken.

A frissítésen áteső virtuális gépek egy csoportja számára az Azure platform fogja összehangolni a frissítéseket:

**Régiók között:**
- A havi frissítéseket globálisan, több fázisban vezényeljük az Azure-ban, hogy megelőzzük a globális üzembe helyezési hibákat.
- Egy fázis egy vagy több régióval is lehet, és a frissítés csak akkor lép át a következő fázisba, ha egy fázis frissítésének jogosult virtuális gépei sikeresen frissülnek.
- A földrajzilag párosított régiók nem frissülnek egyidejűleg, és nem lehetek ugyanabban a regionális fázisban.
- A frissítés sikerességének méréséhez nyomon kell követni a virtuális gép frissítés utáni állapotát. A virtuális gép állapotát a virtuális gép platform-állapotjelzői követik nyomon.

**Egy régión belül:**
- A különböző Availability Zones virtuális gépek nem frissülnek egyidejűleg.
- Azok a virtuális gépek, amelyek nem részei egy rendelkezésre állási készletnek, a rendszer az elérhető lehetőségek alapján kötegbe van állítva, hogy elkerülje az előfizetésben futó összes virtuális gép egyidejű frissítését.

**Rendelkezésre állási készleten belül:**
- Egy közös rendelkezésre állási készletben nem minden virtuális gép frissül egyidejűleg.
-   Az egy közös rendelkezésre állási csoportban lévő virtuális gépek a frissítési tartományhatáron belül frissülnek, a több frissítési tartományban lévő virtuális gépek pedig nem frissülnek egyidejűleg.

Egy adott virtuális gép javítási telepítési dátuma hónapról hónapra változhat, mivel egy adott virtuális gép a havi javítási ciklusok között egy másik kötegben is átvehető.

### <a name="which-patches-are-installed"></a>Mely javítások vannak telepítve?
A telepített javítások a virtuális gép bevezetési szakaszától függnek. Minden hónapban elindul egy új globális bevezetés, ahol az adott virtuális géphez telepített összes biztonsági és kritikus javítás telepítve van az adott virtuális géphez. A bevezetés kötegekbe van szervezve az összes Azure-régióban (a fenti, rendelkezésre állási-első javítás című szakaszban leírtak szerint).

A telepítve szükséges javítások pontos készlete a virtuális gép konfigurációja alapján változik, beleértve az operációs rendszer típusát és az értékelés időzítését. A különböző régiókban található két azonos virtuális gép különböző javítások telepítésére is lehetőség van, ha több vagy kevesebb javítás érhető el, ha a javítás vezénylése különböző időpontokban éri el a különböző régiókat. Hasonlóképpen, de ritkábban, az azonos régióban található, de különböző időpontokban értékelt virtuális gépek (eltérő rendelkezésre állási zóna vagy rendelkezésre állási készlet kötegei miatt) eltérő javításokat kapnak.

Mivel az automatikus virtuális gép vendégjavítása nem konfigurálja a javítás forrását, a különböző javítási forrásokhoz konfigurált két hasonló virtuális gép, például a nyilvános adattár és a privát adattár, szintén különbséget tapasztalhat a telepített javítások pontos készletében.

Az olyan operációsrendszer-típusok esetében, amelyek rögzített ütemben oldják fel a javításokat, az operációs rendszer nyilvános adattárában konfigurált virtuális gépek várhatóan egy hónapon belül ugyanazt a javításokat kapják meg a különböző bevezetési fázisok során. Ilyen például a nyilvános tárházhoz Windows Update Windows rendszerű virtuális gépek.

Mivel minden hónapban új bevezetés történik, a virtuális gép minden hónapban legalább egy javítást kap, ha a virtuális gép csúcsidőn kívül van bekapcsolva. Ez biztosítja, hogy a virtuális gép havonta a legújabb elérhető biztonsági és kritikus javításokkal van javítva. A telepített javítások konzisztenciájának biztosítása érdekében úgy konfigurálhatja a virtuális gépeket, hogy a saját privát adattáraiból mérjék fel és töltsék le a javításokat.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Az előzetes verzióban jelenleg csak bizonyos operációsrendszer-platformok rendszerképeiből létrehozott virtuális gépek támogatottak. Az egyéni rendszerképek jelenleg nem támogatottak az előzetes verzióban.

Jelenleg a következő platform-SKUS-k támogatottak (és rendszeresen bővül újabbak):

| Publisher               | Operációsrendszer-ajánlat      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Javítási vezénylési módok
Az Azure-beli virtuális gépek mostantól támogatják a következő javítási vezénylési módokat:

**AutomaticByPlatform:**
- Ez a mód Linux és Windows rendszerű virtuális gépeken is támogatott.
- Ez a mód lehetővé teszi a virtuális gép vendégjavításának automatikus frissítését a virtuális gépen, az ezt követő javítástelepítést pedig az Azure vezényli.
- Ez a mód az első rendelkezésre állási javításhoz szükséges.
- Ez a mód csak a fenti támogatott operációsrendszer-rendszerképekkel létrehozott virtuális gépeken támogatott.
- Windows rendszerű virtuális gépek esetén ennek a módnak a beállítása letiltja a windowsos virtuális gépen a natív automatikus frissítéseket is a duplikáció elkerülése érdekében.
- Ha Ezt a módot Linux rendszerű virtuális gépeken is használnia kell, állítsa be a tulajdonságot `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` a virtuálisgép-sablonban.
- Ha Ezt a módot Windows rendszerű virtuális gépeken is használni kívánta, állítsa be a tulajdonságot, és állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=true`  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` a virtuálisgép-sablonban.

**AutomaticByOS:**
- Ez a mód csak Windows rendszerű virtuális gépek esetén támogatott.
- Ez a mód engedélyezi az automatikus frissítéseket a Windows rendszerű virtuális gépen, és az Automatikus frissítések funkcióval telepíti a javításokat a virtuális gépre.
- Ez a mód nem támogatja a rendelkezésre állásra vonatkozó első javításokat.
- Ez a mód alapértelmezés szerint be van állítva, ha egy Windows rendszerű virtuális géphez nincs más javítási mód megadva.
- Ha Ezt a módot Windows rendszerű virtuális gépeken is használni kívánta, állítsa be a tulajdonságot, és állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=true`  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` a virtuálisgép-sablonban.

**Kézi:**
- Ez a mód csak Windows rendszerű virtuális gépek esetén támogatott.
- Ez a mód letiltja az automatikus frissítéseket a Windows rendszerű virtuális gépen.
- Ez a mód nem támogatja a rendelkezésre állásra vonatkozó első javításokat.
- Ezt a módot egyéni javítási megoldások használata esetén kell beállítani.
- Ha Ezt a módot Windows rendszerű virtuális gépeken is használni kívánta, állítsa be a tulajdonságot, és állítsa be a tulajdonságot `osProfile.windowsConfiguration.enableAutomaticUpdates=false`  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` a virtuálisgép-sablonban.

**ImageDefault:**
- Ez a mód csak Linux rendszerű virtuális gépek esetén támogatott.
- Ez a mód nem támogatja a rendelkezésre állásra vonatkozó első javításokat.
- Ez a mód a virtuális gép létrehozásához használt rendszerkép alapértelmezett javítási konfigurációját használja.
- Ez a mód alapértelmezés szerint be van állítva, ha linuxos virtuális géphez nincs más javítási mód megadva.
- Ennek a módnak a Linux rendszerű virtuális gépeken való használathoz állítsa be a tulajdonságot `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` a virtuálisgép-sablonban.

> [!NOTE]
>Windows rendszerű virtuális gépek esetén a tulajdonság jelenleg csak a virtuális gép első létrehozásakor `osProfile.windowsConfiguration.enableAutomaticUpdates` beállítható. A Manuális módról automatikus módra vagy az Automatikus módról a Manuális módra váltás jelenleg nem támogatott. Az AutomaticByOS módról AutomaticByPlatfom módra váltás támogatott.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>A virtuális gépek automatikus vendégjavításának engedélyezésére vonatkozó követelmények

- A virtuális gépen telepítve kell lennie a [Windows](./extensions/agent-windows.md) vagy Linux rendszerű Azure-beli [virtuálisgép-ügynöknek.](./extensions/agent-linux.md)
- Linux rendszerű virtuális gépeken az Azure Linux-ügynöknek 2.2.53.1-es vagy újabb verziójúnak kell lennie. [Frissítse a Linux-ügynököt,](./extensions/update-linux-agent.md) ha az aktuális verzió alacsonyabb a szükséges verziónál.
- Windows rendszerű virtuális gépek esetén a Windows Update szolgáltatásnak futnia kell a virtuális gépen.
- A virtuális gépnek hozzá kell férnie a konfigurált frissítési végponthoz. Ha a virtuális gép privát adattárak használatára van konfigurálva Linux vagy Windows Server Update Services (WSUS) Windows rendszerű virtuális gépekhez, a megfelelő frissítési végpontnak elérhetőnek kell lennie.
- Használja a Compute API 2020-12-01-es vagy újabb verzióját. A Compute API 2020-06-01-es verziója korlátozott funkcionalitású Windows rendszerű virtuális gépekhez használható.

Az előzetes verziójú funkciók engedélyezéséhez az **InGuestAutoPatchVMPreview** és **az InGuestPatchVMPreview** előfizetésenként egyszer kell engedélyezni a funkciókat, az alábbi szakaszban részletezettek szerint.

### <a name="rest-api"></a>REST API
Az alábbi példa bemutatja, hogyan engedélyezheti az előzetes verziójú előfizetését:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Miután regisztrálta a funkciót az előfizetésben, a módosításnak a Számítási erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmag használatával engedélyezheti az előzetes verziójú előfizetését.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással történő befejezésével befejezi a feliratkozási folyamatot.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az [az feature register használatával](/cli/azure/feature#az_feature_register) engedélyezheti az előzetes verzió használatát az előfizetéséhez.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással történő befejezésével befejezi a feliratkozási folyamatot.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Automatikus virtuálisgép-vendég-javítás engedélyezése
Ha engedélyezni szeretné az automatikus virtuálisgép-vendégjavítást egy Windows rendszerű virtuális gépen, győződjön meg arról,  hogy az *osProfile.windowsConfiguration.enableAutomaticUpdates* tulajdonság true (igaz) értékre van állítva a virtuálisgép-sablon definíciójában. Ezt a tulajdonságot csak a virtuális gép létrehozásakor lehet beállítani. Ez a további tulajdonság Linux rendszerű virtuális gépeken nem alkalmazható.

### <a name="rest-api-for-linux-vms"></a>REST API linuxos virtuális gépekhez
A következő példa bemutatja, hogyan engedélyezheti a virtuális gépek vendégjavítását:

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
A következő példa bemutatja, hogyan engedélyezheti a virtuális gépek vendégjavítását:

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
A [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) parancsmag használatával engedélyezheti a virtuális gépek automatikus vendégjavítását a virtuális gépek létrehozásakor vagy frissítésekkor.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI Windows rendszerű virtuális gépekhez
Új virtuális gép létrehozásakor az [az vm create](/cli/azure/vm#az_vm_create) parancs használatával engedélyezheti az automatikus virtuálisgép-vendégjavítást. A következő példa a *myResourceGroup* nevű erőforráscsoportban található *myVM* nevű virtuális gép automatikus virtuálisgép-vendégjavítását konfigurálja:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Meglévő virtuális gép módosításához használja az [az vm update-et](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Engedélyezés és értékelés

> [!NOTE]
>A virtuális gép vendégfrissítésének engedélyezése több mint három órát is igénybe vehet a virtuális gépen, mivel az engedélyezés a virtuális gép csúcsidőn kívüli időszakában fejeződik be. Mivel az értékelés és a javítás telepítése csak csúcsidőn kívül történik, a javítások alkalmazáshoz a virtuális gépnek csúcsidőn kívül is futnia kell.

Ha egy virtuális gépen engedélyezve van az automatikus virtuálisgép-vendégjavítás, a rendszer telepít egy típusú virtuálisgép-bővítményt egy Linux rendszerű virtuális gépre, vagy egy típusú virtuálisgép-bővítményt egy `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` Windows rendszerű virtuális gépre. Ezt a bővítményt nem kell manuálisan telepíteni vagy frissíteni, mivel ezt a bővítményt az Azure platform kezeli a virtuális gépek vendégjavítási folyamatának részeként.

A virtuális gép vendégfrissítésének engedélyezése több mint három órát is igénybe vehet a virtuális gépen, mivel az engedélyezés a virtuális gép csúcsidőn kívüli időszakában fejeződik be. A bővítmény a virtuális gép csúcsidőn kívüli időszakában is telepítve és frissítve lesz. Ha a virtuális gép csúcsidőn kívüli ideje befejeződik az engedélyezés befejezése előtt, az engedélyezés folyamata a következő elérhető csúcsidőn kívül folytatódik.

Az automatikus frissítések a legtöbb esetben le vannak tiltva, és a javítás telepítése a bővítményen keresztül történik. Az alábbi feltételek érvényesek.
- Ha egy Windows rendszerű virtuális gépen korábban az Automatikus Windows Update az AutomaticByOS javítási módon keresztül kapcsolták be, akkor a bővítmény telepítésekor az Automatic Windows Update ki van kapcsolva a virtuális gépen.
- Ubuntu virtuális gépek esetén az alapértelmezett automatikus frissítések automatikusan le vannak tiltva, amikor az automatikus virtuálisgép-vendégjavítás befejezi az engedélyezést.
- RHEL esetén az automatikus frissítéseket manuálisan kell letiltani (ez egy előzetes verziójú korlátozás). Végre:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Annak ellenőrzéséhez, hogy befejeződött-e az automatikus virtuálisgép-vendégjavítás, és telepítve van-e a javító bővítmény a virtuális gépen, tekintse át a virtuális gép példánynézetét. Ha az engedélyezés befejeződött, a bővítmény telepítve lesz, és a virtuális gép értékelési eredményei a alatt lesznek `patchStatus` elérhetők. A virtuális gép példánynézete az alábbiakban leírtak szerint több módon is elérhető.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Használja a [Get-AzVM](/powershell/module/az.compute/get-azvm) parancsmagot a paraméterrel a virtuális gép `-Status` példánynézetének eléréséhez.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

A PowerShell jelenleg csak a javításbővítményről nyújt információt. Az információk `patchStatus` hamarosan a PowerShellen keresztül is elérhetők lesznek.

### <a name="azure-cli"></a>Azure CLI
A virtuális gép példánynézetének eléréséhez használja az [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) használhatja.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>A virtuális gép javítási állapotának ismertetése

A példánynézetre adott válasz szakasza részletesen tartalmazza a legutóbbi értékelést és a virtuális gép legutóbbi `patchStatus` javítástelepítését.

A virtuális gép felmérési eredményeit a szakaszban lehet `availablePatchSummary` áttekintni. A rendszer rendszeres időközönként értékelést végez egy olyan virtuális gépről, amelyen engedélyezve van a virtuális gépek vendégjavítása. Az értékelés után elérhető javítások száma a és a `criticalAndSecurityPatchCount` `otherPatchCount` eredmények között. Az automatikus virtuálisgép-vendégjavítások a  Kritikus és biztonsági javítások besorolása alatt értékelt *összes* javítást telepítik. A rendszer kihagy minden más értékelt javítást.

A virtuális gép javítástelepítési eredményeit a szakaszban lehet `lastPatchInstallationSummary` áttekintni. Ez a szakasz a virtuális gép legutóbbi javítási telepítési kísérletének részleteit tartalmazza, beleértve a telepített, függő, sikertelen vagy kihagyott javítások számát. A javításokat csak a virtuális gép csúcsidőn kívüli karbantartási időszakában telepíti a rendszer. A függőben lévő és a sikertelen javítások a következő csúcsidőn kívüli karbantartási időszakban automatikusan újra megjelennek.

## <a name="on-demand-patch-assessment"></a>Igény szerinti javításfelmérés
Ha a virtuális gép vendégjavítása már engedélyezve van a virtuális gépen, a rendszer rendszeres javításértékelést végez a virtuális gépen a virtuális gép csúcsidőn kívüli időszakában. Ez a folyamat automatikus, és a legutóbbi értékelés eredményeit a virtuális gép példánynézetében lehet áttekintni a dokumentumban korábban leírtak szerint. Igény szerinti javításfelmérést bármikor aktiválhat a virtuális géphez. A javítás felmérése eltarthat néhány percig, és a legújabb értékelés állapota frissül a virtuális gép példánynézetében.

Az előzetes verziójú funkció engedélyezéséhez előfizetésenként egyszer kell engedélyezni az **InGuestPatchVMPreview** funkciót. Ez a funkcióelőnézet eltér az **inGuestAutoPatchVMElőnézetben** korábban végzett automatikus virtuálisgép-vendégjavítási szolgáltatás regisztrációtól. A további funkcióelőnézet engedélyezése egy külön és további követelmény. Az igény szerinti javításértékelés funkcióelőnézete [](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) a virtuális gépek vendégének automatikus javításához korábban ismertetett előzetes verziójú frissítési folyamattal engedélyezhető.

> [!NOTE]
>Az igény szerinti javításfelmérés nem aktiválja automatikusan a javítás telepítését. Ha engedélyezte az automatikus virtuálisgép-vendégjavításokat, akkor a virtuális gép értékelt és alkalmazható javításokat a virtuális gép csúcsidőn kívüli időszakában telepíti a rendszer a jelen dokumentumban korábban ismertetett, rendelkezésre állási szintű javítási folyamatot követően.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Az [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) parancsmag használatával felmérheti a virtuális géphez elérhető javításokat.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Az [az vm assess-patches használatával](/cli/azure/vm#az_vm_assess_patches) felmérheti a virtuális gép rendelkezésre álló javításokat.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [További információ a Windows rendszerű virtuális gépek létrehozásáról és kezeléséről](./windows/tutorial-manage-vm.md)
