---
title: Virtuális gép hálózati forgalmának szűrésével kapcsolatos probléma diagnosztizálása | Microsoft Docs
description: Megtudhatja, hogyan diagnosztizálhatja a virtuális gép hálózati forgalmának szűrési problémáit a virtuális gépek érvényes biztonsági szabályainak megtekintésével.
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
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223669"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Virtuális gép hálózati forgalmának szűrésével kapcsolatos probléma diagnosztizálása

Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a hálózati forgalom szűrésével kapcsolatos problémát úgy, hogy megtekinti a virtuális gép (VM) számára érvényes hálózati biztonsági csoport (NSG) biztonsági szabályait.

A NSG lehetővé teszi a virtuális gépeken belüli és kimenő forgalom típusának szabályozását. NSG társíthat egy Azure-beli virtuális hálózatban lévő alhálózathoz, egy virtuális géphez csatolt hálózati adapterhez vagy mindkettőhöz. A hálózati adapterre alkalmazott érvényes biztonsági szabályok a hálózati adapterhez társított NSG található szabályok összesítései, valamint a hálózati adaptert tartalmazó alhálózat. A különböző NSG található szabályok időnként ütközhetnek egymással, és befolyásolhatják a virtuális gép hálózati kapcsolatát. Megtekintheti a virtuális gép hálózati adapterén alkalmazott NSG érvényes biztonsági szabályokat. Ha nem ismeri a virtuális hálózatot, a hálózati adaptert vagy a NSG kapcsolatos fogalmakat, tekintse meg a [virtuális hálózat áttekintése](virtual-networks-overview.md), a [hálózati adapter](virtual-network-network-interface.md)és a [hálózati biztonsági csoportok áttekintése](./network-security-groups-overview.md)című témakört.

## <a name="scenario"></a>Eset

Megpróbál csatlakozni egy virtuális géphez az internetről érkező 80-as porton keresztül, de a kapcsolat meghiúsul. Annak megállapításához, hogy miért nem fér hozzá az internetről a 80-es porthoz, az Azure [Portal](#diagnose-using-azure-portal), a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli)használatával megtekintheti a hálózati adapter érvényes biztonsági szabályait.

Az alábbi lépések azt feltételezik, hogy rendelkezik egy meglévő virtuális géppel a hatályos biztonsági szabályok megtekintéséhez. Ha nem rendelkezik meglévő virtuális géppel, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows rendszerű](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépet a cikkben ismertetett feladatok végrehajtásához. A jelen cikkben szereplő példák egy *myVM* nevű virtuális gépre vonatkoznak egy *myVMVMNic* nevű hálózati adapterrel. A virtuális gép és a hálózati adapter egy *myResourceGroup* nevű erőforráscsoport, és az *USA keleti* régiójában található. Szükség szerint módosítsa a lépésekben szereplő értékeket arra a virtuális gépre, amelyre a problémát diagnosztizálja.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás a Azure Portal használatával

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy olyan Azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).
2. A Azure Portal tetején adja meg a virtuális gép nevét a keresőmezőbe. Ha a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
3. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget, ahogy az a következő képen látható:

   ![A képernyőképen az V M V M hálózati adapter hálózati beállításaival Azure Portal látható.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Az előző képen látható szabályok a **myVMVMNic** nevű hálózati adapterhez tartoznak. Láthatja, hogy a hálózati adapterhez **bejövő portszabályok** tartoznak két különböző hálózati biztonsági csoportból:
   
   - **mySubnetNSG**: ahhoz az alhálózathoz van társítva, amelyhez a hálózati adapter tartozik.
   - **myVMNSG**: a **myVMVMNic** nevű virtuális gép hálózati adapteréhez van társítva.

   A **DenyAllInBound** nevű szabály az internetről az 80-as porton keresztül megakadályozza a virtuális gép felé irányuló bejövő kommunikációt, a [forgatókönyvben](#scenario)leírtak szerint. A szabály a **forráshoz** tartozó *0.0.0.0/0* listát sorolja fel, amely magában foglalja az internetet is. Nincs más olyan szabály, amely magasabb prioritással rendelkezik (alacsonyabb szám), amely engedélyezi a 80-as portot. Ha engedélyezni szeretné a 80-as portot a virtuális géphez az internetről, tekintse meg a [probléma elhárítása](#resolve-a-problem)című témakört. Ha többet szeretne megtudni a biztonsági szabályokról és arról, hogy az Azure hogyan alkalmazza őket, lásd: [hálózati biztonsági csoportok](./network-security-groups-overview.md).

   A kép alján megtekintheti a **kimenő portok szabályait** is. Alatta a kimenő portszabályok a hálózati adapterhez. Bár a képen csak négy Bejövő szabály látható az egyes NSG, a NSG több mint négy szabálya lehet. A képen a **forrás és a** **cél** és a **AzureLoadBalancer** alatti **VirtualNetwork** láthatók a **forrás** területen. A **VirtualNetwork** és a **AzureLoadBalancer** [szolgáltatás-címkék](./network-security-groups-overview.md#service-tags). A szolgáltatás címkéi az IP-címek egy csoportját jelölik, így könnyebben csökkenthetők a biztonsági szabályok létrehozásának bonyolultsága.

4. Győződjön meg arról, hogy a virtuális gép futó állapotban van, majd válassza a **hatályos biztonsági szabályok** elemet az előző képen látható módon, hogy megtekintse az érvényes biztonsági szabályokat, amelyek az alábbi képen láthatók:

   ![Képernyőfelvétel: az érvényben lévő biztonsági szabályok ablaktábla, amelyen a kiválasztott letöltés és a AllowAzureLoadBalancerInbound Bejövő szabály van kiválasztva.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   A felsorolt szabályok ugyanazok, mint a 3. lépésben, de a hálózati adapterhez és az alhálózathoz társított NSG különböző lapok találhatók. Amint a képen látható, csak az első 50-szabály jelenik meg. Az összes szabályt tartalmazó. csv-fájl letöltéséhez válassza a **Letöltés** lehetőséget.

   Ha szeretné megtekinteni, hogy az egyes szolgáltatási címkék mely előtagokat képviselik, jelöljön ki egy szabályt, például a **AllowAzureLoadBalancerInbound** nevű szabályt. A következő képen a **AzureLoadBalancer** szolgáltatás címkéi előtagjai láthatók:

   ![A képernyőfelvételen a beírt AllowAzureLoadBalancerInbound tartozó címek előtagjai láthatók.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Bár a **AzureLoadBalancer** szolgáltatás címkéje csak egy előtagot jelöl, az egyéb szolgáltatási címkék több előtagokat is képviselnek.

5. Az előző lépések a **myVMVMNic** nevű hálózati adapter biztonsági szabályait mutatták be, de az előző képek némelyikében egy **myVMVMNic2** nevű hálózati adaptert is látott. A példában szereplő virtuális gépnek két hálózati adaptere van csatlakoztatva. Az érvényes biztonsági szabályok az egyes hálózati adapterek esetében eltérőek lehetnek.

   A **myVMVMNic2** hálózati adapter szabályainak megtekintéséhez válassza ki azt. Ahogy az a következő képen látható, a hálózati adapter ugyanazokkal a szabályokkal van társítva az alhálózathoz, mint a **myVMVMNic** hálózati adapter, mivel mindkét hálózati adapter ugyanahhoz az alhálózathoz tartozik. Ha egy alhálózathoz rendel hozzá egy NSG, annak szabályai az alhálózat összes hálózati adapterére érvényesek lesznek.

   ![Képernyőfelvétel: az V M V M NIC 2 hálózati beállításait tartalmazó Azure Portal.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   A **myVMVMNic** hálózati adaptertől eltérően a **myVMVMNic2** hálózati adapterhez nincs hozzárendelve hálózati biztonsági csoport. Minden hálózati adapter és alhálózat nulla vagy egy NSG társítható. Az egyes hálózati adapterekhez vagy alhálózatokhoz társított NSG azonos vagy eltérő lehet. Ugyanazt a hálózati biztonsági csoportot a kiválasztott számú hálózati adapterhez és alhálózathoz is hozzárendelheti.

Bár az érvényes biztonsági szabályok a virtuális gépen keresztül tekinthetők meg, a hatályos biztonsági szabályok egy személyen keresztül is megtekinthetők:
- **Hálózati adapter**: megtudhatja, hogyan [tekintheti meg a hálózati adaptereket](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: megtudhatja, hogyan [TEKINTHET meg egy NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0 vagy újabb verzióra. Futtassa a parancsot a `Get-Module -ListAvailable Az` számítógépen, és keresse meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor is futtatnia kell az `Connect-AzAccount` Azure-ba való bejelentkezést egy olyan fiókkal, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).

Szerezze be a [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)hálózati adapter érvényes biztonsági szabályait. A következő példa egy *myVMVMNic* nevű hálózati adapter érvényes biztonsági szabályait olvassa be, amely egy *myResourceGroup* nevű erőforráscsoport:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

A rendszer JSON formátumban adja vissza a kimenetet. A kimenet megismeréséhez tekintse meg a [parancs kimenetének értelmezése](#interpret-command-output)című témakört.
A rendszer csak akkor adja vissza a kimenetet, ha egy NSG van társítva a hálózati adapterhez, a hálózati adapterhez tartozó alhálózat vagy mindkettő. A virtuális gépnek futó állapotban kell lennie. Egy virtuális gépnek több hálózati adaptere is lehet, különböző NSG alkalmazva. Hibaelhárításkor futtassa a parancsot az egyes hálózati adapterekhez.

Ha továbbra is kapcsolódási problémája van, tekintse meg a [további diagnosztikai](#additional-diagnosis) és [szempontokat](#considerations).

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatolt összes hálózati adapter azonosítóit adják vissza:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet kap:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimenetben a hálózati adapter neve *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez a cikkhez az Azure CLI 2.0.32 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, a `az login` [szükséges engedélyekkel](virtual-network-network-interface.md#permissions)rendelkező fiókkal is futtatnia kell az Azure-ba, és be kell jelentkeznie.

A hálózati adapter érvényes biztonsági szabályainak lekérése az [az Network NIC List-effektív-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg). A következő példa egy *myVMVMNic* nevű hálózati adapter érvényes biztonsági szabályait olvassa be, amely egy *myResourceGroup* nevű erőforráscsoport:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A rendszer JSON formátumban adja vissza a kimenetet. A kimenet megismeréséhez tekintse meg a [parancs kimenetének értelmezése](#interpret-command-output)című témakört.
A rendszer csak akkor adja vissza a kimenetet, ha egy NSG van társítva a hálózati adapterhez, a hálózati adapterhez tartozó alhálózat vagy mindkettő. A virtuális gépnek futó állapotban kell lennie. Egy virtuális gépnek több hálózati adaptere is lehet, különböző NSG alkalmazva. Hibaelhárításkor futtassa a parancsot az egyes hálózati adapterekhez.

Ha továbbra is kapcsolódási problémája van, tekintse meg a [további diagnosztikai](#additional-diagnosis) és [szempontokat](#considerations).

Ha nem ismeri a hálózati adapter nevét, de ismeri annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatolt összes hálózati adapter azonosítóit adják vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

A visszaadott kimeneten a következő példához hasonló információk jelennek meg:

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

Az előző kimenetben a hálózati adapter neve *myVMVMNic Interface*.

## <a name="interpret-command-output"></a>A parancs kimenetének értelmezése

Függetlenül attól, hogy használta-e a [PowerShellt](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli) -t a probléma diagnosztizálásához, a következő adatokat tartalmazó kimenetet kapja:

- **NetworkSecurityGroup**: a hálózati biztonsági csoport azonosítója.
- **Társítás**: azt határozza meg, hogy a hálózati biztonsági csoport egy *hálózati adaptere* vagy *alhálózathoz* van-e társítva. Ha egy NSG mindkettőhöz van társítva, a rendszer az egyes NSG esetében a kimenetet **NetworkSecurityGroup**, **társítással** és **EffectiveSecurityRules** együtt adja vissza. Ha a NSG társítva van, vagy közvetlenül társítva van a parancs futtatása előtt, hogy megtekintse az érvényes biztonsági szabályokat, előfordulhat, hogy néhány másodpercig várnia kell, hogy a változás tükrözze a parancs kimenetében.
- **EffectiveSecurityRules**: az egyes tulajdonságok magyarázatát részletesen a [biztonsági szabály létrehozása](manage-network-security-group.md#create-a-security-rule)című rész ismerteti. A *defaultSecurityRules/* alapértelmezett biztonsági szabályok az összes NSG találhatók. A *securityRules vagy* az Ön által létrehozott szabályok nevei. A [szolgáltatás címkéjét](./network-security-groups-overview.md#service-tags)(például **Internet**, **VirtualNetwork** és **AzureLoadBalancer** ) megadó szabályok a **destinationAddressPrefix** vagy a **sourceAddressPrefix** tulajdonsághoz is rendelkeznek értékekkel a **expandedDestinationAddressPrefix** tulajdonsághoz. A **expandedDestinationAddressPrefix** tulajdonság a szolgáltatás címkéje által képviselt összes előtagot listázza.

Ha a kimenetben a duplikált szabályok láthatók, annak oka az, hogy egy NSG a hálózati adapterhez és az alhálózathoz is társítva van. Mindkét NSG ugyanazokkal az alapértelmezett szabályokkal rendelkezik, és további duplikált szabályok is lehetnek, ha saját szabályokat hozott létre mindkét NSG.

A **defaultSecurityRules/DenyAllInBound** nevű szabály a [forgatókönyvben](#scenario)leírtak szerint megakadályozza a virtuális gép felé irányuló bejövő kommunikációt a 80-as porton keresztül az internetről. Nincs más olyan szabály, amely magasabb prioritással rendelkezik (alacsonyabb szám) az internetről bejövő 80-as port.

## <a name="resolve-a-problem"></a>Probléma megoldása

Függetlenül attól, hogy az Azure [Portal](#diagnose-using-azure-portal), a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli) használatával diagnosztizálja a jelen cikkben szereplő [forgatókönyvben](#scenario) bemutatott problémát, a megoldás egy hálózati biztonsági szabály létrehozása a következő tulajdonságokkal:

| Tulajdonság                | Érték                                                                              |
|---------                |---------                                                                           |
| Forrás                  | Bármely                                                                                |
| Forrásporttartományok      | Bármelyik                                                                                |
| Cél             | A virtuális gép IP-címe, az IP-címek tartománya vagy az alhálózat összes címe. |
| Célporttartományok | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Művelet                  | Engedélyezés                                                                              |
| Prioritás                | 100                                                                                |
| Név                    | Engedélyezés – HTTP-mind                                                                     |

A szabály létrehozása után a 80-es port engedélyezi az internetről érkező bejövő adatokat, mert a szabály prioritása nagyobb, mint az *DenyAllInBound* nevű alapértelmezett biztonsági szabály, amely megtagadja a forgalmat. Útmutató [biztonsági szabályok létrehozásához](manage-network-security-group.md#create-a-security-rule). Ha a hálózati adapterhez és az alhálózathoz is különböző NSG vannak társítva, akkor mindkét NSG ugyanazt a szabályt kell létrehoznia.

Amikor az Azure feldolgozza a bejövő forgalmat, az az alhálózathoz társított NSG (ha van társított NSG) a szabályokat dolgozza fel, majd feldolgozza a hálózati adapterhez társított NSG szabályait. Ha a hálózati adapterhez és az alhálózathoz NSG van társítva, akkor a portnak mindkét NSG nyitva kell lennie ahhoz, hogy a forgalom elérje a virtuális gépet. Az adminisztrációs és kommunikációs problémák enyhítése érdekében javasoljuk, hogy az egyes hálózati adapterek helyett egy NSG rendeljen hozzá egy alhálózathoz. Ha az alhálózaton belüli virtuális gépeknek eltérő biztonsági szabályokra van szükségük, a hálózati adapterek tagjait egy alkalmazás biztonsági csoportjának (ASG) kell megadnia, és meg kell adnia egy ASG a biztonsági szabály forrásaként és céljaként. További információ az [alkalmazás biztonsági csoportjairól](./network-security-groups-overview.md#application-security-groups).

Ha továbbra is kommunikációs problémák léptek fel, tekintse meg a [szempontokat](#considerations) és a további diagnózist.

## <a name="considerations"></a>Megfontolandó szempontok

A kapcsolódási problémák elhárításakor vegye figyelembe a következő szempontokat:

* Az alapértelmezett biztonsági szabályok letiltják a bejövő hozzáférést az internetről, és csak a virtuális hálózatról érkező bejövő forgalmat engedélyezik. Az internetről érkező bejövő adatforgalom engedélyezéséhez adjon hozzá magasabb prioritású biztonsági szabályokat az alapértelmezett szabályoknál. További információ az [alapértelmezett biztonsági szabályokról](./network-security-groups-overview.md#default-security-rules), illetve [egy biztonsági szabály hozzáadásáról](manage-network-security-group.md#create-a-security-rule).
* Ha a társ virtuális hálózatokkal rendelkezik, alapértelmezés szerint a **VIRTUAL_NETWORK** szolgáltatás címkéje automatikusan kibontja, hogy tartalmazza az előtagokat a társ virtuális hálózatokhoz. A virtuális hálózati társítással kapcsolatos problémák elhárításához tekintse meg az előtagokat a **ExpandedAddressPrefix** listán. További információ a [virtuális hálózatok](virtual-network-peering-overview.md) társításáról és a [szolgáltatással kapcsolatos címkékről](./network-security-groups-overview.md#service-tags).
* Az érvényes biztonsági szabályok csak akkor jelennek meg egy hálózati adapterhez, ha a virtuális gép hálózati adapteréhez, illetve a, az alhálózathoz és a virtuális géphez tartozó NSG van társítva.
* Ha nincs NSG társítva a hálózati adapterhez vagy az alhálózathoz, és van egy virtuális géphez hozzárendelt [nyilvános IP-cím](virtual-network-public-ip-address.md) , minden port nyitva van a bejövő hozzáféréshez és a kimenő hozzáféréshez bárhonnan. Ha a virtuális gépnek nyilvános IP-címe van, javasoljuk, hogy alkalmazzon egy NSG a hálózati adaptert tartalmazó alhálózatra.

## <a name="additional-diagnosis"></a>További diagnosztika

* Ha egy gyors tesztet szeretne futtatni annak megállapításához, hogy a forgalom engedélyezett-e a virtuális gép számára, vagy egy virtuális gépről, használja az [IP-folyamat ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) az Azure Network Watcher funkcióját. Az IP-forgalom ellenőrzésekor megtudhatja, hogy a forgalom engedélyezett vagy tiltott. Ha nem engedélyezett, az IP-flow ellenőrzi, hogy melyik biztonsági szabály utasítja el a forgalmat.
* Ha nincsenek olyan biztonsági szabályok, amelyek miatt a virtuális gép hálózati kapcsolata meghiúsul, a probléma oka a következő lehet:
  * A virtuális gép operációs rendszerén belül futó tűzfal szoftver
  * Virtuális készülékekhez vagy helyszíni forgalomhoz konfigurált útvonalak. Az internetes forgalom [kényszerített bújtatáson](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)keresztül átirányítható a helyszíni hálózatra. Ha kényszeríti az internetes forgalmat a virtuális készülékre vagy a helyszíni hálózatra, előfordulhat, hogy nem tud kapcsolódni a virtuális GÉPHEZ az internetről. Ha meg szeretné tudni, hogyan diagnosztizálhatja a virtuális gép forgalmának áramlását akadályozó útválasztási problémákat, tekintse meg a [virtuálisgép-hálózati forgalom útválasztási problémáinak diagnosztizálása](diagnose-network-routing-problem.md)című témakört.

## <a name="next-steps"></a>Következő lépések

- A [hálózati biztonsági csoport](manage-network-security-group.md#work-with-network-security-groups) és a [biztonsági szabályok](manage-network-security-group.md#work-with-security-rules)összes feladatának, tulajdonságának és beállításának megismerése.
- Ismerje meg az [alapértelmezett biztonsági szabályokat](./network-security-groups-overview.md#default-security-rules), a [szolgáltatási címkéket](./network-security-groups-overview.md#service-tags), valamint azt, hogy az Azure hogyan dolgozza fel a virtuális gépek [bejövő és kimenő forgalmának biztonsági szabályait](./network-security-groups-overview.md#network-security-groups) .