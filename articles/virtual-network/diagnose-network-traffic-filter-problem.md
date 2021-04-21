---
title: Virtuális gép hálózati forgalmának szűrési problémájának diagnosztizálása a | Microsoft Docs
description: Megtudhatja, hogyan diagnosztizálhatja a virtuális gépek hálózati forgalomszűrési problémáját a virtuális gépekre vonatkozó hatályos biztonsági szabályok megtekintésével.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: d6835d06015923a70301c95370c76efbd0c2163e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776734"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Virtuális gép hálózati forgalmi szűrővel való problémájának diagnosztizálása

Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a hálózati forgalomszűrési problémákat a virtuális gépekre (VM)vonatkozó hálózati biztonsági csoportok (NSG-k) biztonsági szabályainak megtekintésével.

Az NSG-k lehetővé teszik a virtuális gépek be- és kifelé áramló forgalmának szabályozását. Az NSG-ket hozzárendelheti egy Azure-beli virtuális hálózat alhálózatához, egy virtuális géphez csatolt hálózati adapterhez, vagy mindkettőhöz. A hálózati adapterre alkalmazott érvényes biztonsági szabályok a hálózati adapterhez társított NSG-ben és a hálózati adapter alhálózatán található szabályok összesítését jelentik. A különböző NSG-k szabályai olykor ütköznek egymással, és hatással lehetnek a virtuális gépek hálózati kapcsolatra. A virtuális gép hálózati adaptereire alkalmazott összes érvényes biztonsági szabályt megtekintheti az NSG-kből. Ha nem ismeri a virtuális hálózattal, a hálózati adaptersel vagy az [](virtual-network-network-interface.md)NSG-vel kapcsolatos fogalmakat, tekintse meg a virtuális hálózatok áttekintését, [](virtual-networks-overview.md)a hálózati adaptert és a hálózati biztonsági csoportokat [áttekintő témakört.](./network-security-groups-overview.md)

## <a name="scenario"></a>Eset

Megpróbál csatlakozni egy virtuális géphez az internetről a 80-as porton keresztül, de a kapcsolat meghiúsul. Annak megállapításához, hogy miért nem tudja elérni a 80-as portot az internetről, megtekintheti a hálózati adapterre vonatkozó hatályos biztonsági szabályokat az [Azure](#diagnose-using-azure-portal)Portal, a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI használatával.](#diagnose-using-azure-cli)

A következő lépések feltételezik, hogy már van egy virtuális gépe az hatályos biztonsági szabályainak megtekintéséhez. Ha még nincs virtuális gépe, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális gépet a cikkben található feladatok elvégzéséhez. A cikkben található példák egy *myVM* nevű virtuális gépre és egy *myVMVMNic* nevű hálózati adapterre mutatnak. A virtuális gép és a hálózati adapter egy *myResourceGroup* nevű erőforráscsoportban található, és az USA keleti *régiójában* található. Szükség szerint módosítsa a lépésekben szereplő értékeket arra a virtuális gépre, amelyről diagnosztizálja a problémát.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás a Azure Portal

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy olyan Azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)
2. A virtuális gép Azure Portal írja be a virtuális gép nevét a keresőmezőbe. Amikor a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
3. A **BEÁLLÍTÁSOK alatt** válassza a Hálózat **lehetőséget** az alábbi képen látható módon:

   ![Képernyőkép az Azure Portal V M M Nic hálózati beállításairól.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Az előző képen látható szabályok a **myVMVMNic** nevű hálózati adapterre vonatkoznak. Láthatja, hogy a hálózati adapterhez két különböző hálózati biztonsági csoport **bejövőPORT-szabályai** vannak:
   
   - **mySubnetNSG:** Ahhoz az alhálózathoz van társítva, amely a hálózati adaptert használja.
   - **myVMNSG:** A **myVMVMNic** nevű virtuális gép hálózati adapterével van társítva.

   A **DenyAllInBound** nevű szabály akadályozza meg a virtuális gépre irányuló bejövő kommunikációt a 80-as porton keresztül az internetről a forgatókönyvben leírtak [szerint.](#scenario) A szabály a *0.0.0.0/0 0-s* listában szerepel a **SOURCE**(FORRÁS) alatt, amely tartalmazza az internetet is. Más magasabb prioritású (alacsonyabb sorszámú) szabály nem engedélyezi a 80-as bejövő portot. Ha engedélyeznie kell a 80-as porton az internetről a virtuális gépre irányuló bejövő forgalmat, tekintse meg [a probléma megoldását.](#resolve-a-problem) A biztonsági szabályokkal és azok Azure-beli alkalmazásával kapcsolatos további információkért lásd: [Hálózati biztonsági csoportok.](./network-security-groups-overview.md)

   A kép alján a KIMENŐ **PORTSZABÁLYOK is láthatóak.** A alatt a hálózati adapter kimenő portszabálya található. Bár a képen csak négy bejövő szabály látható minden NSG-hez, az NSG-kben négynél több szabály is lehet. A képen a **VirtualNetwork** a **SOURCE** (FORRÁS) és a DESTINATION (CÉL) **alatt,** az **AzureLoadBalancer** pedig a SOURCE (FORRÁS) alatt **látható.** **A VirtualNetwork** és **az AzureLoadBalancer** [szolgáltatáscímkék.](./network-security-groups-overview.md#service-tags) A szolgáltatáscímkék IP-címelőtagok egy csoportját képviselik, így minimálisra csökkenthető a biztonsági szabályok létrehozásának összetettsége.

4. Győződjön meg arról, hogy a virtuális gép fut, majd válassza az Hatályos biztonsági szabályok lehetőséget az előző képen látható módon az alábbi ábrán látható hatályos biztonsági szabályokhoz:

   ![Képernyőkép az Hatályos biztonsági szabályok panelről, a Letöltés és az AllowAzureLoadBalancerInbound Bejövő szabály kijelölve.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   A felsorolt szabályok ugyanazok, mint a 3. lépésben, bár a hálózati adapterhez és az alhálózathoz társított NSG különböző lapokból áll. Ahogy a képen is látható, csak az első 50 szabály jelenik meg. Az összes szabályt tartalmazó .csv-fájl letöltéséhez válassza a **Letöltés lehetőséget.**

   Az egyes szolgáltatáscímkék által képviselt előtagokért válasszon ki egy szabályt, például az **AllowAzureLoadBalancerInbound nevű szabályt.** Az alábbi képen az **AzureLoadBalancer** szolgáltatáscímke előtagja látható:

   ![Képernyőkép a megadott AllowAzureLoadBalancerInbound címelőtagról.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Bár az **AzureLoadBalancer** szolgáltatáscímkéje csak egy előtagot jelöl, más szolgáltatáscímkék több előtagot jelölnek.

5. Az előző lépések a **myVMVMNic** nevű hálózati adapter biztonsági szabályait mutattuk be, de néhány korábbi képen egy **myVMVMNic2** nevű hálózati adaptert is láthatott. A példában található virtuális géphez két hálózati adapter van csatlakoztatva. Az egyes hálózati adapterek hatályos biztonsági szabályai eltérőek is lehet.

   Válassza ki a **myVMVMNic2** hálózati adapterre vonatkozó szabályokat. Ahogy a következő képen is látható, a hálózati adapter alhálózatához ugyanazok a szabályok vannak társítva, mint a **myVMVMNic** hálózati adapter, mert mindkét hálózati adapter ugyanabban az alhálózatban található. Amikor egy NSG-t társít egy alhálózathoz, annak szabályai az alhálózat összes hálózati adapterére érvényesek.

   ![Képernyőkép az Azure Portal V M M Nic 2 hálózati beállításairól.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   A **myVMVMNic** hálózati adaptertől eltérően a **myVMVMNic2** hálózati adapterhez nincs hálózati biztonsági csoport társítva. Minden hálózati adapterhez és alhálózathoz nulla vagy egy NSG társítható. Az egyes hálózati adapterek vagy alhálózatok NSG-i azonosak vagy eltérőek is lehetnek. Ugyanazt a hálózati biztonsági csoportot annyi hálózati adapterhez és alhálózathoz társíthatja, amennyit csak akar.

Bár az hatályos biztonsági szabályokat a virtuális gépen keresztül tekinteni meg, az egyes felhasználókon keresztül is megtekintheti az hatályos biztonsági szabályokat:
- **Hálózati adapter:** Ismerje meg, hogyan [lehet megtekinteni egy hálózati adaptert.](virtual-network-network-interface.md#view-network-interface-settings)
- **NSG:** Megtudhatja, hogyan [lehet megtekinteni egy NSG-t.](manage-network-security-group.md#view-details-of-a-network-security-group)

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathatja a Azure Cloud Shell [vagy](https://shell.azure.com/powershell)a PowerShell futtatásával a számítógépről. A Azure Cloud Shell egy ingyenes interaktív felület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell 1.0.0-s vagy újabb verziójára. A telepített verzió megkereséhez futtassa a következőt `Get-Module -ListAvailable Az` a számítógépen: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a futtatásával jelentkezzen be az Azure-ba egy olyan fiókkal, amely rendelkezik `Connect-AzAccount` a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions)].

Lekérte a hálózati adapterre vonatkozó hatályos biztonsági szabályokat a [Get-AzEffectiveNetworkSecurityGroup használatával.](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) Az alábbi példa egy *myVMVMNic* nevű hálózati adapterre vonatkozó hatályos biztonsági szabályokat tartalmazza, amely egy *myResourceGroup nevű erőforráscsoportban van:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

A kimenet json formátumban lesz visszaadva. A kimenet értelmezéséhez tekintse meg a [parancskimenet értelmezését.](#interpret-command-output)
A kimenet csak akkor lesz visszaadva, ha egy NSG van társítva a hálózati adapterhez, az alhálózathoz, amely a hálózati adapter, vagy mindkettő. A virtuális gépnek futó állapotban kell lennie. Egy virtuális gépen több hálózati adapter is lehet, amelyekre különböző NSG-k vonatkoznak. Hibaelhárításkor futtassa az parancsot minden egyes hálózati adapterhez.

Ha továbbra is csatlakozási problémát lát, tekintse meg a további [diagnosztika és](#additional-diagnosis) [szempontokat.](#considerations)

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatolva van, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati adapterhez visszaadják az adatokat:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

A következő példához hasonló kimenetet kap:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimenetben a hálózati adapter neve *myVMVMNic.*

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

Ha Azure parancssori felületi (CLI)-parancsokat használ a cikkben található feladatok [](https://shell.azure.com/bash)végrehajtásához, futtassa a parancsokat a Azure Cloud Shell-ban, vagy futtassa a parancssori felületet a számítógépről. Ehhez a cikkhez az Azure CLI 2.0.32-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor futtatnia kell az Azure-t, és be kell jelentkeznie az Azure-ba egy olyan fiókkal, amely rendelkezik a szükséges `az login` [engedélyekkel.](virtual-network-network-interface.md#permissions)

Az az [network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)használatával lekért hálózati adapterre vonatkozó hatályos biztonsági szabályok. Az alábbi példa egy *myVMVMNic* nevű hálózati adapter hatályos biztonsági szabályait kapja meg, amely egy *myResourceGroup nevű erőforráscsoportban van:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A kimenet json formátumban lesz visszaadva. A kimenet értelmezéséhez tekintse meg a [parancskimenet értelmezését.](#interpret-command-output)
A kimenet csak akkor lesz visszaadva, ha egy NSG van társítva a hálózati adapterhez, az alhálózathoz, a hálózati adapterhez, vagy mindkettőhöz. A virtuális gépnek futó állapotban kell lennie. Egy virtuális gépen több hálózati adapter is lehet, amelyekre különböző NSG-k vonatkoznak. Hibaelhárításkor futtassa az parancsot minden egyes hálózati adapterhez.

Ha továbbra is csatlakozási problémát lát, tekintse meg a további [diagnosztika és](#additional-diagnosis) [szempontokat.](#considerations)

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatolva van, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati adapterhez visszaadják az adatokat:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

A visszaadott kimenetben az alábbi példához hasonló információk adatokat láthat:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Az előző kimenetben a hálózati adapter neve *myVMVMNic adapter.*

## <a name="interpret-command-output"></a>Parancskimenet értelmezése

Függetlenül attól, hogy a [PowerShellt](#diagnose-using-powershell)vagy az [Azure CLI-t](#diagnose-using-azure-cli) használta a probléma diagnosztizálásához, a következő információkat tartalmazó kimenetet kap:

- **NetworkSecurityGroup:** A hálózati biztonsági csoport azonosítója.
- **Társítás:** Azt határozza meg, hogy a hálózati biztonsági csoport egy *NetworkInterface* vagy *alhálózathoz legyen-e társítva.* Ha mindkettőhöz NSG van társítva, a kimenet a **NetworkSecurityGroup,** **Association** és **EffectiveSecurityRules** értékekkel lesz visszaadva minden NSG-hez. Ha az NSG-t közvetlenül a parancs futtatása előtt társította vagy társította az hatályos biztonsági szabályok megtekintéséhez, előfordulhat, hogy néhány másodpercet várnia kell, hogy a módosítás tükrözze a parancs kimenetét.
- **EffectiveSecurityRules:** Az egyes tulajdonságokkal kapcsolatos magyarázatokat a Biztonsági szabály [létrehozásacímű cikk részletezi.](manage-network-security-group.md#create-a-security-rule) A *defaultSecurityRules/* előtaggal előtagú szabálynevek olyan alapértelmezett biztonsági szabályok, amelyek minden NSG-ben léteznek. A *securityRules/ előtaggal előtaggal* szereplő szabálynevek ön által létrehozott szabályok. A szolgáltatáscímkét (például **Internet,** **VirtualNetwork** és **AzureLoadBalancer)** [a](./network-security-groups-overview.md#service-tags) **destinationAddressPrefix** vagy **sourceAddressPrefix** tulajdonságokhoz megszaporító szabályok szintén rendelkeznek értékekkel az **expandedDestinationAddressPrefix** tulajdonsághoz. Az **expandedDestinationAddressPrefix** tulajdonság felsorolja a szolgáltatáscímke által képviselt összes címelőtagot.

Ha ismétlődő szabályokat lát a kimenetben, annak az az oka, hogy egy NSG van társítva a hálózati adapterhez és az alhálózathoz is. Mindkét NSG ugyanazokkal az alapértelmezett szabályokkal és további duplikált szabályokkal is lehet, ha saját szabályokat hozott létre, amelyek azonosak mindkét NSG-ben.

A **defaultSecurityRules/DenyAllInBound** nevű szabály megakadályozza a virtuális gépre irányuló bejövő kommunikációt az internetről a 80-as porton keresztül, a [forgatókönyvben](#scenario)leírtak szerint. Más magasabb prioritású (alacsonyabb sorszámú) szabály nem engedélyezi a 80-as porton keresztüli bejövő forgalmat az internetről.

## <a name="resolve-a-problem"></a>Probléma megoldása

Akár az Azure [Portalt,](#diagnose-using-azure-portal)a [PowerShellt](#diagnose-using-powershell)vagy az Azure [](#scenario) [CLI-t](#diagnose-using-azure-cli) használja a cikkben bemutatott probléma diagnosztizálásához, a megoldás egy hálózati biztonsági szabály létrehozása a következő tulajdonságokkal:

| Tulajdonság                | Érték                                                                              |
|---------                |---------                                                                           |
| Forrás                  | Bármely                                                                                |
| Forrásporttartományok      | Bármelyik                                                                                |
| Cél             | A virtuális gép IP-címe, egy IP-címtartomány vagy az alhálózat összes címe. |
| Célporttartományok | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Művelet                  | Engedélyezés                                                                              |
| Prioritás                | 100                                                                                |
| Név                    | Allow-HTTP-All                                                                     |

A szabály létrehozása után a 80-as port bejövő forgalma engedélyezett az internetről, mert a szabály prioritása magasabb, mint a *DenyAllInBound* nevű alapértelmezett biztonsági szabály, amely megtagadja a forgalmat. Ismerje meg, [hogyan hozhat létre biztonsági szabályt.](manage-network-security-group.md#create-a-security-rule) Ha a hálózati adapterhez és az alhálózathoz is különböző NSG-k vannak társítva, mindkét NSG-ben létre kell hoznia ugyanazt a szabályt.

Amikor az Azure feldolgozza a bejövő forgalmat, feldolgozza az alhálózathoz társított NSG-ben található szabályokat (ha van társított NSG), majd feldolgozza a hálózati adapterhez társított NSG szabályait. Ha a hálózati adapterhez és az alhálózathoz NSG van társítva, a portnak mindkét NSG-ben nyitva kell lennie ahhoz, hogy a forgalom elérje a virtuális gépet. Az adminisztrációs és kommunikációs problémák megoldása érdekében azt javasoljuk, hogy az egyes hálózati adapterek helyett egy alhálózathoz társítsa az NSG-t. Ha egy alhálózaton belüli virtuális gépeknek eltérő biztonsági szabályokra van szükségük, a hálózati adaptereket egy alkalmazásbiztonsági csoport (ASG) tagjaivá is lehet tenni, és megadhat egy ASG-t egy biztonsági szabály forrásaként és céljaként. További információ az [alkalmazásbiztonsági csoportokról:](./network-security-groups-overview.md#application-security-groups).

Ha továbbra is kommunikációs problémákat tapasztal, tekintse meg a [megfontolandó](#considerations) szempontokat és további diagnosztikát.

## <a name="considerations"></a>Megfontolandó szempontok

A kapcsolati problémák elhárításakor vegye figyelembe a következő pontokat:

* Az alapértelmezett biztonsági szabályok blokkolják az internetről bejövő hozzáférést, és csak a virtuális hálózatról bejövő forgalmat engedélyezik. Az internetről bejövő forgalom engedélyezése érdekében az alapértelmezett szabályoknál magasabb prioritású biztonsági szabályokat adjon hozzá. További információ az [alapértelmezett biztonsági szabályokról](./network-security-groups-overview.md#default-security-rules)és [a biztonsági szabályok hozzáadásáról.](manage-network-security-group.md#create-a-security-rule)
* Ha virtuális társhálózatokkal is létesített, alapértelmezés szerint a **VIRTUAL_NETWORK** szolgáltatáscímkéje automatikusan kibővül, és előtagokat tartalmaz a virtuális társhálózatok számára. A virtuális hálózatok közötti társviszony-létesítés problémáinak elhárításához tekintse meg az előtagokat az **ExpandedAddressPrefix** listában. További információ a [virtuális hálózatok közötti társviszony-létesítésről](virtual-network-peering-overview.md) [és a szolgáltatáscímkékről.](./network-security-groups-overview.md#service-tags)
* Az hatályos biztonsági szabályok csak akkor jelennek meg a hálózati adapterek esetén, ha a virtuális gép hálózati adapterével és alhálózatával társított NSG van, illetve ha a virtuális gép fut.
* Ha nincsenek hálózati adapterhez vagy alhálózathoz társított NSG-k, és rendelkezik egy virtuális géphez rendelt nyilvános [IP-címmel,](virtual-network-public-ip-address.md) minden porton meg van nyitva a bejövő és kimenő hozzáférés bárhonnan. Ha a virtuális gép nyilvános IP-címmel rendelkezik, javasoljuk, hogy alkalmazzon egy NSG-t a hálózati adapter alhálózatán.

## <a name="additional-diagnosis"></a>További diagnosztika

* Egy gyors teszt futtatásához annak megállapításához, hogy a [](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) virtuális gépre vagy a virtuális gépről engedélyezett-e a forgalom, használja az Azure-beli virtuális gépek IP-forgalom-Network Watcher. Az IP-forgalom ellenőrzése jelzi, hogy a forgalom engedélyezett vagy tiltott. Ha a rendszer megtagadja, az IP-forgalom ellenőrzése megmutatja, melyik biztonsági szabály tagadja meg a forgalmat.
* Ha nincsenek olyan biztonsági szabályok, amelyek a virtuális gép hálózati kapcsolatának meghiúsulása miatt meghiúsulnak, a problémát a következő okozhatja:
  * A virtuális gép operációs rendszerében futó tűzfalszoftverek
  * Virtuális berendezésekhez vagy helyszíni forgalomhoz konfigurált útvonalak. Az internetes forgalom átirányítható a helyszíni hálózatra kényszerített [bújtatással.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha kényszeríti az internetes forgalom bújtatását egy virtuális berendezésre vagy helyszíni berendezésre, előfordulhat, hogy nem tud csatlakozni a virtuális géphez az internetről. A virtuális gépről kimenő forgalom áramlását akadályozó problémák diagnosztizálását lásd: Virtuális gép hálózati forgalmának [útválasztási problémáinak diagnosztizálása.](diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a hálózati biztonsági csoportokhoz és [biztonsági](manage-network-security-group.md#work-with-network-security-groups) szabályokhoz szükséges összes feladatot, tulajdonságot és [beállítást.](manage-network-security-group.md#work-with-security-rules)
- Ismerje meg [az alapértelmezett biztonsági szabályokat,](./network-security-groups-overview.md#default-security-rules) [szolgáltatáscímkéket](./network-security-groups-overview.md#service-tags)és azt, hogyan dolgozza fel az [Azure](./network-security-groups-overview.md#network-security-groups) a virtuális gépek bejövő és kimenő forgalmára vonatkozó biztonsági szabályokat.