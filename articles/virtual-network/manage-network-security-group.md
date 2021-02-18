---
title: Azure-beli hálózati biztonsági csoport létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogy hol találhatók a biztonsági szabályokkal kapcsolatos információk, valamint a hálózati biztonsági csoportok létrehozásának, módosításának és törlésének módja.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 5070b64c3900aa4ee1cf51519c7b64362ac37413
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596676"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása, módosítása vagy törlése

A hálózati biztonsági csoportokban található biztonsági szabályok lehetővé teszik a virtuális hálózati alhálózatok és hálózati adapterek közötti és onnan kimenő hálózati forgalom típusának szűrését. További információ a hálózati biztonsági csoportokról: [hálózati biztonsági csoport áttekintése](./network-security-groups-overview.md). Ezután hajtsa végre a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyagot, és szerezzen némi élményt a hálózati biztonsági csoportokkal.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik ilyennel, állítson be egy aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). A cikk hátralévő részének megkezdése előtt végezze el az alábbi feladatok egyikét:

- **Portál felhasználói**: Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók**: futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell böngésző lapon keresse meg a **környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell modul 1.0.0 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói**: futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy futtassa a CLI-t a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI-2.0.28 verziójára vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

A fiókba, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelni a [hálózati közreműködő szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Létrehozhatja, [megtekintheti az összeset](#view-all-network-security-groups), [megtekintheti](#view-details-of-a-network-security-group), [módosíthatja](#change-a-network-security-group)és [törölheti](#delete-a-network-security-group) a hálózati biztonsági csoportokat. Hálózati biztonsági csoportot is [hozzárendelhet vagy](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) leválaszthat egy hálózati adapterről vagy alhálózatról.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az egyes Azure-helyekhez és-előfizetésekhez több hálózati biztonsági csoport is létrehozható. További információ: Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Válassza a **hálózatkezelés**, majd a **hálózati biztonsági csoport** elemet.

3. A **hálózati biztonsági csoport létrehozása** lapon az **alapok** lapon adja meg a következő beállítások értékeit:

    | Beállítás | Művelet |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. |
    | **Név** | Adjon meg egy egyedi szöveges karakterláncot egy erőforráscsoport belül. |
    | **Régió** | Válassza ki a kívánt helyet. |

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. Miután megtalálta az **érvényesítési üzenet érvényességét** , válassza a **Létrehozás** lehetőséget.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Az összes hálózati biztonsági csoport megtekintése

A hálózati biztonsági csoportok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet. Megjelenik az előfizetéshez tartozó hálózati biztonsági csoportok listája.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG List](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Hálózati biztonsági csoport részleteinek megtekintése

1. A hálózati biztonsági csoportok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki a hálózati biztonsági csoport nevét.

A hálózati biztonsági csoport menüsorában, a **Beállítások** alatt megtekintheti a **bejövő biztonsági szabályokat**, a **kimenő biztonsági szabályokat**, a hálózati **adaptereket** és az **alhálózatokat** , amelyekhez a hálózati biztonsági csoport társítva van.

A **figyelés** területen engedélyezheti vagy letilthatja a **diagnosztikai beállításokat**. A **támogatás + hibaelhárítás** alatt megtekintheti az **érvényes biztonsági szabályokat**. További információt [a hálózati biztonsági csoport diagnosztikai naplózása](virtual-network-nsg-manage-log.md) és a virtuálisgép- [hálózati forgalom szűrése problémájának diagnosztizálásával](diagnose-network-traffic-filter-problem.md)foglalkozó témakörben talál.

Ha többet szeretne megtudni a felsorolt általános Azure-beállításokról, tekintse meg a következő cikkeket:

- [Tevékenységnapló](../azure-monitor/essentials/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Hálózati biztonsági csoport módosítása

1. A hálózati biztonsági csoportok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki a módosítani kívánt hálózati biztonsági csoport nevét.

A leggyakoribb módosítások [egy biztonsági szabály hozzáadása](#create-a-security-rule), [egy szabály eltávolítása](#delete-a-security-rule), valamint egy [hálózati biztonsági csoport társítása vagy leválasztása egy alhálózathoz vagy egy hálózati adapterhez](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Hálózati biztonsági csoport társítása vagy leválasztása egy alhálózathoz vagy hálózati adapterhez

Ha hálózati biztonsági csoportot szeretne hozzárendelni a vagy a hálózati biztonsági csoport hálózati adapterből való társításához, tekintse meg a hálózati biztonsági csoport társítása a hálózathoz vagy a hálózati [biztonsági csoport társítása hálózati adapterről](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)című témakört. Ha hálózati biztonsági csoportot szeretne hozzárendelni, vagy a hálózati biztonsági csoportot egy alhálózatról szeretné leválasztani, tekintse meg az [alhálózat beállításainak módosítása](virtual-network-manage-subnet.md#change-subnet-settings)című témakört.

### <a name="delete-a-network-security-group"></a>Hálózati biztonsági csoport törlése

Ha egy hálózati biztonsági csoport bármely alhálózathoz vagy hálózati adapterhez van társítva, nem törölhető. A törlés megkísérlése előtt szüntesse meg a hálózati biztonsági csoportot az összes alhálózatból és hálózati adapterből.

1. A hálózati biztonsági csoportok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki a törölni kívánt hálózati biztonsági csoport nevét.

3. A hálózati biztonsági csoport eszköztárán válassza a **Törlés** lehetőséget. Ezután válassza az **Igen** lehetőséget a megerősítő párbeszédpanelen.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Biztonsági szabályok használata

A hálózati biztonsági csoport nulla vagy több biztonsági szabályt tartalmaz. Létrehozhat, [megtekintheti az összeset](#view-all-security-rules) [, megtekintheti](#view-details-of-a-security-rule), [módosíthatja](#change-a-security-rule)és [törölheti](#delete-a-security-rule) a biztonsági szabályokat.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

Az egyes Azure-helyekhez és-előfizetésekhez legfeljebb egy hálózati biztonsági csoportra vonatkozó szabályt lehet létrehozni. További információ: Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A hálózati biztonsági csoportok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelyhez biztonsági szabályt kíván hozzáadni.

3. A hálózati biztonsági csoport menüsávján válassza a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok** elemet.

    Számos meglévő szabály szerepel a felsorolásban, beleértve azt is, hogy nem lett hozzáadva. Hálózati biztonsági csoport létrehozásakor több alapértelmezett biztonsági szabály jön létre. További információ: [alapértelmezett biztonsági szabályok](./network-security-groups-overview.md#default-security-rules).  Az alapértelmezett biztonsági szabályok nem törölhetők, de felülbírálhatja azokat olyan szabályokkal, amelyek magasabb prioritással rendelkeznek.

4. <a name="security-rule-settings"></a>Válassza a **Hozzáadás** lehetőséget. Válassza ki vagy adja meg a következő beállítások értékeit, majd kattintson **az OK gombra**:

    | Beállítás | Érték | Részletek |
    | ------- | ----- | ------- |
    | **Forrás** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatás címkéje** (bejövő biztonsági szabály) vagy **VirtualNetwork** (kimenő biztonsági szabály)</li><li>**Alkalmazás &nbsp; biztonsági &nbsp; csoportja**</li></ul> | <p>Ha **IP-címeket** választ, meg kell adnia a **forrás IP-címek/CIDR tartományokat** is.</p><p>Ha a **szolgáltatás címkét** választja, akkor kiválaszthatja a **forrás szolgáltatás címkéjét** is.</p><p>Ha az **alkalmazás biztonsági csoportja** lehetőséget választja, válasszon ki egy meglévő alkalmazás biztonsági csoportot is. Ha a **forrás** és a **cél** esetében az **alkalmazás biztonsági csoportot** választja, akkor az alkalmazás biztonsági csoportjain belüli hálózati adaptereknek ugyanabban a virtuális hálózatban kell lenniük.</p> |
    | **Forrás IP-címeinek/CIDR tartományai** | Az IP-címek és az osztály nélküli tartomány-útválasztási (CIDR-) tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha a **forrást** **IP-címekre** módosítja. Egyetlen értéket vagy vesszővel tagolt listát kell megadnia több értékről. Több érték például: `10.0.0.0/16, 192.188.1.1` . A megadható értékek száma korlátozott. További részletek: Azure- [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Ha a megadott IP-cím egy Azure-beli virtuális géphez van hozzárendelve, akkor a saját magánhálózati IP-címét, nem pedig a nyilvános IP-címét kell megadnia. Az Azure a biztonsági szabályokat feldolgozza, miután lefordítja a nyilvános IP-címet a bejövő biztonsági szabályok magánhálózati IP-címére, de még mielőtt lefordítja a magánhálózati IP-címet a kimenő szabályokhoz tartozó nyilvános IP-címekre. Ha többet szeretne megtudni az Azure nyilvános és magánhálózati IP-címeiről, tekintse meg az [IP-címek típusait](./public-ip-addresses.md).</p> |
    | **Forrás szolgáltatáscímke** | Egy szolgáltatás címkéje a legördülő listából | Ez a választható beállítás akkor jelenik meg, ha a **forrás** - **szolgáltatás címkét** egy bejövő biztonsági szabályhoz állítja be. A szolgáltatási címke az IP-címek kategóriájának előre meghatározott azonosítója. Ha többet szeretne megtudni az elérhető szolgáltatásokkal kapcsolatos címkékről és az egyes címkékről, tekintse meg a [szolgáltatás címkéi](./network-security-groups-overview.md#service-tags)című témakört. |
    | **Forrásoldali alkalmazás biztonsági csoportja** | Egy meglévő alkalmazás biztonsági csoportja | Ez a beállítás akkor jelenik meg, ha a **forrás** - **alkalmazás biztonsági csoportot** állítja be. Válasszon ki egy olyan alkalmazás biztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Megtudhatja, hogyan [hozhat létre egy alkalmazás biztonsági csoportot](#create-an-application-security-group). |
    | **Forrásporttartományok** | Az egyik:<ul><li>Egyetlen port, például `80`</li><li>Számos port, például `1024-65535`</li><li>Az egyes portok és/vagy porttartomány-tartományok vesszővel tagolt listája, például: `80, 1024-65535`</li><li>Egy csillag ( `*` ), amely bármely porton engedélyezi a forgalmat</li></ul> | Ez a beállítás határozza meg azokat a portokat, amelyeken a szabály engedélyezi vagy megtagadja a forgalmat. A megadható portok száma korlátozott. További részletek: Azure- [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Cél** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatás címkéje** (kimenő biztonsági szabály) vagy **VirtualNetwork** (bejövő biztonsági szabály)</li><li>**Alkalmazás &nbsp; biztonsági &nbsp; csoportja**</li></ul> | <p>Ha az **IP-címek** lehetőséget választja, adja meg a **cél IP-címek/CIDR tartományokat** is.</p><p>Ha a **VirtualNetwork** lehetőséget választja, a forgalom minden IP-cím számára engedélyezett a virtuális hálózat címterület-területén. A **VirtualNetwork** egy szolgáltatás címkéje.</p><p>Ha az **alkalmazás biztonsági csoport** lehetőséget választja, ki kell választania egy meglévő alkalmazás biztonsági csoportot. Megtudhatja, hogyan [hozhat létre egy alkalmazás biztonsági csoportot](#create-an-application-security-group).</p> |
    | **Cél IP-címek/CIDR-tartományok** | Az IP-címek és CIDR tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha a **célhelyet** **IP-címekre** módosítja. A **forrás** -és **forrás IP-CÍMEKhez/CIDR-tartományokhoz** hasonlóan egyetlen vagy több címet vagy tartományt is megadhat. A megadható szám korlátozott. További részletek: Azure- [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Ha a megadott IP-cím egy Azure-beli virtuális géphez van hozzárendelve, akkor győződjön meg arról, hogy a magánhálózati IP-címe nem a nyilvános IP-címe. Az Azure biztonsági szabályokat dolgoz fel, miután lefordítja a nyilvános IP-címet a bejövő biztonsági szabályok magánhálózati IP-címére, de az Azure még azelőtt lefordítja a magánhálózati IP-címet a kimenő szabályokhoz tartozó nyilvános IP-címekre. Ha többet szeretne megtudni az Azure nyilvános és magánhálózati IP-címeiről, tekintse meg az [IP-címek típusait](./public-ip-addresses.md).</p> |
    | **Cél szolgáltatáscímkéje** | Egy szolgáltatás címkéje a legördülő listából | Ez a választható beállítás akkor jelenik meg, ha egy kimenő biztonsági szabály **célját** a **szolgáltatás címkéjére** módosítja. A szolgáltatási címke az IP-címek kategóriájának előre meghatározott azonosítója. Ha többet szeretne megtudni az elérhető szolgáltatásokkal kapcsolatos címkékről és az egyes címkékről, tekintse meg a [szolgáltatás címkéi](./network-security-groups-overview.md#service-tags)című témakört. |
    | **Célalkalmazás biztonsági csoportja** | Egy meglévő alkalmazás biztonsági csoportja | Ez a beállítás akkor jelenik meg, ha a **cél** értéket adja meg az **alkalmazás biztonsági csoportjának**. Válasszon ki egy olyan alkalmazás biztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Megtudhatja, hogyan [hozhat létre egy alkalmazás biztonsági csoportot](#create-an-application-security-group). |
    | **Célporttartományok** | Az egyik:<ul><li>Egyetlen port, például `80`</li><li>Számos port, például `1024-65535`</li><li>Az egyes portok és/vagy porttartomány-tartományok vesszővel tagolt listája, például: `80, 1024-65535`</li><li>Egy csillag ( `*` ), amely bármely porton engedélyezi a forgalmat</li></ul> | A **forrásport-tartományokhoz** hasonlóan egyetlen vagy több portot és tartományt is megadhat. A megadható szám korlátozott. További részletek: Azure- [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokoll** | **Bármely**, **TCP**, **UDP** vagy **ICMP** | A szabályt a Transmission Control Protocol (TCP), a User Datagram Protocol (UDP) vagy a Internet Control Message Protocol (ICMP) számára korlátozhatja. Az alapértelmezett érték az, hogy a szabály az összes protokollra vonatkozzon. |
    | **Művelet** | **Engedélyezés** vagy **Megtagadás** | Ez a beállítás határozza meg, hogy ez a szabály engedélyezi vagy megtagadja a hozzáférést a megadott forrás-és célhely-konfigurációhoz. |
    | **Prioritás** | 100 és 4096 közötti érték, amely a hálózati biztonsági csoporton belüli összes biztonsági szabály esetében egyedi. | Az Azure prioritási sorrendben dolgozza fel a biztonsági szabályokat. Minél kisebb a szám, annál magasabb a prioritás. Azt javasoljuk, hogy a prioritási számok között hagyjon különbséget a szabályok létrehozásakor, például 100, 200 és 300. A hézagok kihagyása megkönnyíti a szabályok hozzáadását a jövőben, így a meglévő szabályoknál magasabb vagy alacsonyabb prioritást biztosíthat. |
    | **Név** | A szabály egyedi neve a hálózati biztonsági csoporton belül | A név legfeljebb 80 karakter hosszú lehet. Betűvel vagy számmal kell kezdődnie, és betűvel, számmal vagy aláhúzással kell végződnie. A név csak betűket, számokat, aláhúzásokat, pontokat és kötőjeleket tartalmazhat. |
    | **Leírás** | Egy szöveg leírása | Opcionálisan megadhatja a biztonsági szabály szövegének leírását is. A leírás nem lehet hosszabb 140 karakternél. |

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Az összes biztonsági szabály megtekintése

A hálózati biztonsági csoport nulla vagy több szabályt tartalmaz. Ha többet szeretne megtudni a szabályok megtekintésekor felsorolt információkkal kapcsolatban, tekintse meg a [hálózati biztonsági csoport áttekintése](./network-security-groups-overview.md)című témakört.

1. A hálózati biztonsági csoport szabályainak megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelyre vonatkozóan meg szeretné tekinteni a szabályokat.

3. A hálózati biztonsági csoport menüsávján válassza a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok** elemet.

A lista tartalmazza a létrehozott szabályokat, valamint a hálózati biztonsági csoport [alapértelmezett biztonsági szabályait](./network-security-groups-overview.md#default-security-rules).

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

1. A hálózati biztonsági csoport szabályainak megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelyre vonatkozóan meg szeretné tekinteni a szabály részleteit.

3. A hálózati biztonsági csoport menüsávján válassza a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok** elemet.

4. Válassza ki azt a szabályt, amelynek adatait meg szeretné tekinteni. Az összes beállítás magyarázatát lásd: [biztonsági szabályok beállításai](#security-rule-settings).

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Az alapértelmezett biztonsági szabály kiválasztásakor nem működik.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule)című témakör lépéseit.

2. Szükség szerint módosítsa a beállításokat, majd válassza a **Mentés** lehetőséget. Az összes beállítás magyarázatát lásd: [biztonsági szabályok beállításai](#security-rule-settings).

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Az alapértelmezett biztonsági szabályt nem lehet módosítani.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule)című témakör lépéseit.

2. Válassza a **Törlés**, majd az **Igen** lehetőséget.

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Nem törölhet alapértelmezett biztonsági szabályt.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network NSG Rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Alkalmazásbiztonsági csoportok használata

Az alkalmazás biztonsági csoportja nulla vagy több hálózati adaptert tartalmaz. További információ: [alkalmazás biztonsági csoportjai](./network-security-groups-overview.md#application-security-groups). Egy alkalmazás biztonsági csoportjában lévő összes hálózati csatolónak ugyanabban a virtuális hálózatban kell lennie. Ha meg szeretné tudni, hogyan adhat hozzá hálózati adaptert egy alkalmazás biztonsági csoportjához, tekintse meg [a hálózati adapter hozzáadása alkalmazás-biztonsági csoporthoz](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)című témakört.

### <a name="create-an-application-security-group"></a>Alkalmazás biztonsági csoportjának létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. A keresőmezőbe írja be az *alkalmazás biztonsági csoportja* mezőt.

3. Az **alkalmazás biztonsági csoport** lapján válassza a **Létrehozás** elemet.

4. Az **alkalmazás biztonsági csoport létrehozása** lapon az **alapok** lapon adja meg a következő beállítások értékeit:

    | Beállítás | Művelet |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. |
    | **Név** | Adjon meg egy egyedi szöveges karakterláncot egy erőforráscsoport belül. |
    | **Régió** | Válassza ki a kívánt helyet. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. A **felülvizsgálat + létrehozás** lapon az **átadott üzenet ellenőrzése** után válassza a **Létrehozás** lehetőséget.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network ASG Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Új – AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Az összes alkalmazás biztonsági csoport megtekintése

Az alkalmazás biztonsági csoportjai megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **alkalmazás biztonsági csoportjait**. A Azure Portal megjeleníti az alkalmazás biztonsági csoportjai listáját.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network ASG List](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Egy adott alkalmazás biztonsági csoportjának részleteinek megtekintése

1. Az alkalmazás biztonsági csoportjának megtekintéséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **alkalmazás biztonsági csoportjait**.

2. Válassza ki annak az alkalmazás-biztonsági csoportnak a nevét, amelynek adatait meg szeretné tekinteni.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network ASG show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alkalmazás biztonsági csoportjának módosítása

1. Az alkalmazás biztonsági csoportjának megtekintéséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **alkalmazás biztonsági csoportjait**.

2. Válassza ki a módosítani kívánt alkalmazás-biztonsági csoport nevét.

3. Válassza **a módosítás lehetőséget** a módosítani kívánt beállítás mellett. Hozzáadhat vagy eltávolíthat például **címkéket**, vagy módosíthatja az **erőforráscsoportot** vagy az **előfizetést**.

    > [!NOTE]
    > A hely nem módosítható.

    A menüsávon a **hozzáférés-vezérlés (iam)** lehetőséget is választhatja. A **hozzáférés-vezérlés (iam)** lapon engedélyeket rendelhet hozzá vagy távolíthat el az alkalmazás biztonsági csoportjához.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network ASG Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Nincs PowerShell-parancsmag |

### <a name="delete-an-application-security-group"></a>Alkalmazás biztonsági csoportjának törlése

Ha bármilyen hálózati adaptert tartalmaz, nem törölheti az alkalmazás biztonsági csoportjait. Ha az összes hálózati adaptert el szeretné távolítani az alkalmazás biztonsági csoportjából, módosítsa a hálózati adapter beállításait, vagy törölje a hálózati adaptereket. További információ: [Hozzáadás vagy eltávolítás az alkalmazás biztonsági csoportjaiból](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) vagy [a hálózati adapter törlése](virtual-network-network-interface.md#delete-a-network-interface).

1. Az alkalmazás biztonsági csoportjainak kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **alkalmazás biztonsági csoportjait**.

2. Válassza ki a törölni kívánt alkalmazás-biztonsági csoport nevét.

3. Válassza a **Törlés** lehetőséget, majd válassza az **Igen** lehetőséget az alkalmazás biztonsági csoportjának törléséhez.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network ASG delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportokkal, a biztonsági szabályokkal és az alkalmazás biztonsági csoportjaival kapcsolatos feladatok elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz, vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az alábbi táblázatokban felsorolt megfelelő engedélyeket kapja:

### <a name="network-security-group"></a>Hálózati biztonsági csoporttal

| Művelet                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/READ                  |   Hálózati biztonsági csoport beolvasása                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Hálózati biztonsági csoport létrehozása vagy frissítése                             |
| Microsoft. Network/networkSecurityGroups/delete                |   Hálózati biztonsági csoport törlése                                       |
| Microsoft. Network/networkSecurityGroups/csatlakozás/művelet           |   Hálózati biztonsági csoport hozzárendelése alhálózathoz vagy hálózati adapterhez 

### <a name="network-security-group-rule"></a>Hálózati biztonsági csoport szabálya

| Művelet                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/securityRules/READ            |   Szabály lekérése                                                            |
| Microsoft. Network/networkSecurityGroups/securityRules/Write           |   Szabály létrehozása vagy frissítése                                               |
| Microsoft. Network/networkSecurityGroups/securityRules/delete          |   Szabály törlése                                                         |

### <a name="application-security-group"></a>Alkalmazásbiztonsági csoport

| Művelet                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action     | IP-konfiguráció csatlakoztatása egy alkalmazás biztonsági csoportjához|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Biztonsági szabály csatlakoztatása egy alkalmazás biztonsági csoportjához    |
| Microsoft. Network/applicationSecurityGroups/READ                           | Alkalmazás biztonsági csoportjának beolvasása                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Alkalmazás biztonsági csoportjának létrehozása vagy frissítése           |
| Microsoft. Network/applicationSecurityGroups/delete                         | Alkalmazás biztonsági csoportjának törlése                     |

## <a name="next-steps"></a>Következő lépések

- Hálózati vagy alkalmazás-biztonsági csoport létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájlokkal vagy Azure [Resource Manager-sablonokkal](template-samples.md)
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz