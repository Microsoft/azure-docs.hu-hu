---
title: Azure-beli hálózati biztonsági csoport létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hol találhat információt a biztonsági szabályokról, valamint arról, hogyan hozhat létre, módosíthat vagy törölhet egy hálózati biztonsági csoportot.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783502"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása, módosítása vagy törlése

A hálózati biztonsági csoportok biztonsági szabályai lehetővé teszik a virtuális hálózati alhálózatok és hálózati adapterek be- és kifelé áramló hálózati forgalmának szűrését. A hálózati biztonsági csoportokkal kapcsolatos további információkért [lásd: Hálózati biztonsági csoportok áttekintése.](./network-security-groups-overview.md) Következő lépésként töltse ki [a Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyagot, hogy némi tapasztalatot szerezzen a hálózati biztonsági csoportokkal.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik előfizetéssel, állítson be egy aktív előfizetéssel egy Azure-fiókot. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) A cikk hátralévő részében az alábbi feladatok valamelyikét kell elvégeznie:

- **Portálfelhasználók:** Jelentkezzen [](https://portal.azure.com) be Azure Portal azure-fiókjával.

- **PowerShell-felhasználók:** Futtassa a parancsokat a [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell lapon keresse meg a  Környezet kiválasztása legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja Azure PowerShell modul 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI)** felhasználói: Futtassa [](https://shell.azure.com/bash)a parancssori felületen Azure Cloud Shell parancsokat, vagy futtassa a parancssori felületet a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI 2.0.28-as vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

A fióknak, amelybe bejelentkezik, vagy amelyről csatlakozik az Azure-hoz, [hozzá](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kell rendelnie a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz, amely a Permissions (Engedélyek) listában felsorolt megfelelő műveleteket [rendeli hozzá.](#permissions) [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Létrehozhatja, megtekintheti [az összeset,](#view-all-network-security-groups) [megtekintheti](#view-details-of-a-network-security-group) [a](#delete-a-network-security-group) [részleteit,](#change-a-network-security-group)módosíthatja a és a hálózati biztonsági csoportokat, és törölheti őket. Hálózati biztonsági [csoportot hálózati adapterhez](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) vagy alhálózathoz is társíthat, illetve az alhálózatról is társíthat.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az egyes Azure-beli helyhez és előfizetéshez létrehozható hálózati biztonsági csoportok maximálisan korlátozva vannak. További információ: [Azure-előfizetések és -szolgáltatások korlátai, kvótái és megkötései.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Válassza **a Hálózat,** majd a **Hálózati biztonsági csoport lehetőséget.**

3. A **Hálózati biztonsági csoport létrehozása lap** Alapvető **beállítások** lapján állítsa be a következő beállítások értékeit:

    | Beállítás | Művelet |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **Új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. |
    | **Név** | Adjon meg egy egyedi szöveges sztringet egy erőforráscsoporton belül. |
    | **Régió** | Válassza ki a kívánt helyet. |

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. Miután megjelenik az **Ellenőrzésen áteső üzenet,** válassza a **Létrehozás lehetőséget.**

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Az összes megtekintése biztonsági csoportok létrehozása

A hálózati [Azure Portal](https://portal.azure.com) megtekintéséhez tekintse meg a hálózati biztonsági csoportokat. Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.** Megjelenik az előfizetés hálózati biztonsági csoportjainak listája.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Hálózati biztonsági csoport részleteinek megtekintése

1. A hálózati [Azure Portal](https://portal.azure.com) megtekintéséhez tekintse meg a hálózati biztonsági csoportokat. Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki a hálózati biztonsági csoport nevét.

A hálózati biztonsági csoport menüsávjában, a Beállítások alatt megtekintheti a Bejövő biztonsági  **szabályokat,** a  Kimenő biztonsági **szabályokat,** a Hálózati adaptereket és az Alhálózatokat, amelyekhez a hálózati biztonsági csoport társítva van. 

A **Figyelés** alatt engedélyezheti vagy letilthatja a **Diagnosztikai beállításokat.** A **Támogatás és hibaelhárítás alatt** megtekintheti az Hatályos biztonsági **szabályokat.** További információ: Hálózati biztonsági csoport diagnosztikai [naplózása](virtual-network-nsg-manage-log.md) és Virtuális gép hálózati forgalomszűrő [problémájának diagnosztizálása.](diagnose-network-traffic-filter-problem.md)

A felsorolt gyakori Azure-beállításokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Tevékenységnapló](../azure-monitor/essentials/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Hálózati biztonsági csoport módosítása

1. A hálózati [Azure Portal](https://portal.azure.com) megtekintéséhez tekintse meg a hálózati biztonsági csoportokat. Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki a módosítani kívánt hálózati biztonsági csoport nevét.

A leggyakoribb módosítás egy biztonsági szabály [hozzáadása,](#create-a-security-rule) [egy](#delete-a-security-rule)szabály eltávolítása, valamint egy hálózati biztonsági csoport társítása vagy társításának eltávolítása egy alhálózathoz vagy hálózati [adapterhez.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Hálózati biztonsági csoport társítása vagy társításának társítása alhálózathoz vagy hálózati adapterhez

Hálózati biztonsági csoport hálózati adapterhez való társításával vagy a csoport hálózati adapterről való társításával kapcsolatban lásd: Hálózati biztonsági csoport társítása hálózati biztonsági csoporthoz, vagy a hálózati adapter társításának társítása a hálózati [adapterről.](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) Hálózati biztonsági csoport alhálózathoz való társításához vagy a hálózati biztonsági csoport alhálózatról való társításának társításához lásd: [Alhálózati beállítások módosítása.](virtual-network-manage-subnet.md#change-subnet-settings)

### <a name="delete-a-network-security-group"></a>Hálózati biztonsági csoport törlése

Ha egy hálózati biztonsági csoport bármely alhálózathoz vagy hálózati adapterhez van társítva, nem törölhető. Egy hálózati biztonsági csoport társításának törlése az összes alhálózatról és hálózati adapterről a törlés megkísérlését megelőzően.

1. A hálózati [Azure Portal](https://portal.azure.com) megtekintéséhez tekintse meg a hálózati biztonsági csoportokat. Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki a törölni kívánt hálózati biztonsági csoport nevét.

3. A hálózati biztonsági csoport eszköztárán válassza a **Törlés lehetőséget.** Ezután válassza **az Igen** lehetőséget a megerősítő párbeszédpanelen.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Biztonsági szabályok munkához

A hálózati biztonsági csoportok nulla vagy több biztonsági szabályt tartalmaznak. Létrehozhatja, [megtekintheti az összeset,](#view-all-security-rules) [megtekintheti](#view-details-of-a-security-rule)a biztonsági szabályok részleteit, [módosíthatja](#change-a-security-rule) [és](#delete-a-security-rule) törölheti őket.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

Az egyes Azure-beli helyhez és előfizetéshez létrehozható hálózati biztonsági csoportok száma korlátozva van. További információ: [Azure-előfizetések és -szolgáltatások korlátai, kvótái és megkötései.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. A hálózati [Azure Portal](https://portal.azure.com) megtekintéséhez tekintse meg a hálózati biztonsági csoportokat. Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelybe biztonsági szabályt szeretne hozzáadni.

3. A hálózati biztonsági csoport menüsávjában válassza a **Bejövő** biztonsági szabályok vagy a Kimenő biztonsági **szabályok lehetőséget.**

    Számos meglévő szabály szerepel a listában, köztük olyan is, amely még nem lett hozzáadva. Amikor létrehoz egy hálózati biztonsági csoportot, számos alapértelmezett biztonsági szabály jön létre benne. További tudnivalókért lásd az alapértelmezett [biztonsági szabályokat.](./network-security-groups-overview.md#default-security-rules)  Az alapértelmezett biztonsági szabályok nem törölhetők, de magasabb prioritású szabályokkal felülbírálhatóak.

4. <a name="security-rule-settings"></a>Válassza a **Hozzáadás lehetőséget.** Válassza ki vagy adja hozzá az értékeket a következő beállításokhoz, majd kattintson az **OK gombra:**

    | Beállítás | Érték | Részletek |
    | ------- | ----- | ------- |
    | **Forrás** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatáscímke** (bejövő biztonsági szabály) vagy **VirtualNetwork** (kimenő biztonsági szabály)</li><li>**Alkalmazásbiztonsági &nbsp; &nbsp; csoport**</li></ul> | <p>Ha az **IP-címeket választja,** forrás **IP-címeket/CIDR-tartományokat** is meg kell adnia.</p><p>Ha a **Szolgáltatáscímke** lehetőséget választja, választhatja a **Forrás szolgáltatáscímkét is.**</p><p>Ha az **Alkalmazásbiztonsági csoport** lehetőséget választja, egy meglévő alkalmazásbiztonsági csoportot is ki kell választania. Ha az Alkalmazásbiztonsági csoport  **lehetőséget** választja a Forrás és a Cél számára **is,** akkor a mindkét alkalmazásbiztonsági csoportban lévő hálózati adapternek ugyanabban a virtuális hálózatban kell lennie.</p> |
    | **Forrás IP-címek/CIDR-tartományok** | AZ IP-címek és a Classless Interdomain Routing - CIDR-tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha a **Forrás beállítást** **IP-címekre módosítja.** Egyetlen értéket vagy több érték vesszővel elválasztott listáját kell megadnia. Több értékre példa a `10.0.0.0/16, 192.188.1.1` . A megadható értékek száma korlátozásokat tartalmaz. További részletekért lásd: [Az Azure korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Ha a megadott IP-cím egy Azure-beli virtuális géphez van rendelve, akkor a magánhálózati IP-címét adja meg, ne a nyilvános IP-címét. Az Azure akkor dolgozza fel a biztonsági szabályokat, ha a nyilvános IP-címet magánhálózati IP-címre fordítja le a bejövő biztonsági szabályokhoz, de mielőtt nyilvános IP-címre fordítja le a kimenő szabályokat. Az Azure-beli nyilvános és magánhálózati IP-címekkel kapcsolatos további információkért lásd: [IP-címtípusok.](./public-ip-addresses.md)</p> |
    | **Forrás szolgáltatáscímke** | Szolgáltatáscímke a legördülő listából | Ez a választható beállítás akkor jelenik meg, ha a **Forrás** beállításnál a **Szolgáltatáscímke** beállítást adja meg egy bejövő biztonsági szabályhoz. A szolgáltatáscímke az IP-címek egy kategóriájának előre definiált azonosítója. Az elérhető szolgáltatáscímkékről és az egyes címkék jelentésükről további információt a [Szolgáltatáscímkékcímke- (Service tags) (Szolgáltatáscímkék) lapon olvashat.](./network-security-groups-overview.md#service-tags) |
    | **Forrásalkalmazás biztonsági csoportja** | Meglévő alkalmazásbiztonsági csoport | Ez a beállítás akkor jelenik meg, ha a **Forrás** beállításnál az **Alkalmazásbiztonsági csoport beállítást adja meg.** Válasszon ki egy olyan alkalmazásbiztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Ismerje meg, [hogyan hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group) |
    | **Forrásporttartományok** | Az egyik:<ul><li>Egyetlen port, például `80`</li><li>Porttartomány, például `1024-65535`</li><li>Egy portok és/vagy porttartományok vesszővel elválasztott listája, például: `80, 1024-65535`</li><li>Egy csillag ( `*` ) a forgalom bármely porton való engedélyezése érdekében</li></ul> | Ez a beállítás határozza meg azokat a portokat, amelyeken a szabály engedélyezi vagy letiltja a forgalmat. A megadható portok száma korlátozásokkal rendelkezik. További részletekért lásd: [Az Azure korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Cél** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatáscímke** (kimenő biztonsági szabály) vagy **VirtualNetwork** (bejövő biztonsági szabály)</li><li>**Alkalmazásbiztonsági &nbsp; &nbsp; csoport**</li></ul> | <p>Ha az **IP-címeket választja,** adja meg a **cél IP-címeit/CIDR-tartományokat is.**</p><p>Ha a **VirtualNetwork** lehetőséget választja, a forgalom a virtuális hálózat címterületének minden IP-címére engedélyezett. **A VirtualNetwork** egy szolgáltatáscímke.</p><p>Ha az **Alkalmazásbiztonsági csoport lehetőséget választja,** ki kell választania egy meglévő alkalmazásbiztonsági csoportot. Ismerje meg, [hogyan hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group)</p> |
    | **Cél IP-címek/CIDR-tartományok** | IP-címek és CIDR-tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha a **Cél** beállítást **IP-címekre módosítja.** A Forrás **és** **Forrás IP-címekhez/CIDR-tartományokhoz** hasonlóan egy vagy több címet vagy tartományt is megadhat. A megadható számra korlátozások vonatkoznak. További részletekért lásd: [Az Azure korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Ha a megadott IP-cím egy Azure-beli virtuális géphez van rendelve, ügyeljen arra, hogy a magánhálózati IP-címét adja meg, ne a nyilvános IP-címét. Az Azure akkor dolgozza fel a biztonsági szabályokat, ha a nyilvános IP-címet magánhálózati IP-címre fordítja le a bejövő biztonsági szabályokhoz, de mielőtt az Azure nyilvános IP-címre fordítja le a kimenő szabályokat. Az Azure-beli nyilvános és magánhálózati IP-címekkel kapcsolatos további információkért lásd: [IP-címtípusok.](./public-ip-addresses.md)</p> |
    | **Cél szolgáltatáscímkéje** | Szolgáltatáscímke a legördülő listából | Ez a választható beállítás  akkor jelenik meg, ha egy kimenő biztonsági szabály **célhelyét** Szolgáltatáscímke beállításra módosítja. A szolgáltatáscímke egy előre definiált azonosító az IP-címek egy kategóriájához. További információ az elérhető szolgáltatáscímkékről és az egyes címkékről: [Szolgáltatáscímkék.](./network-security-groups-overview.md#service-tags) |
    | **Célalkalmazás biztonsági csoportja** | Meglévő alkalmazásbiztonsági csoport | Ez a beállítás akkor jelenik meg, ha a **Cél** beállításnál az **Alkalmazásbiztonsági csoport beállítást adja meg.** Válasszon ki egy olyan alkalmazásbiztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Ismerje meg, [hogyan hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group) |
    | **Célporttartományok** | Az egyik:<ul><li>Egyetlen port, például `80`</li><li>Porttartomány, például `1024-65535`</li><li>Egy portok és/vagy porttartományok vesszővel elválasztott listája, például: `80, 1024-65535`</li><li>Egy csillag ( `*` ) a forgalom bármely porton való engedélyezése érdekében</li></ul> | Ahogy a **Forrásporttartományok** esetén, itt is megadhat egy vagy több portot és tartományt. A megadható számra korlátozások vonatkoznak. További részletekért lásd: [Az Azure korlátai.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protokoll** | **Bármely,** **TCP,** **UDP**, vagy **ICMP** | A szabályt korlátozhatja az Transmission Control Protocol (TCP), a User Datagram Protocol (UDP) vagy a Internet Control Message Protocol (ICMP) protokollra. Az alapértelmezett beállítás az, hogy a szabály minden protokollra vonatkozik. |
    | **Művelet** | **Engedélyezése vagy** **megtagadása** | Ez a beállítás határozza meg, hogy ez a szabály engedélyezi vagy megtagadja-e a hozzáférést a megadott forrás- és célkonfigurációhoz. |
    | **Prioritás** | 100 és 4096 közötti érték, amely a hálózati biztonsági csoportban található összes biztonsági szabályra egyedi | Az Azure prioritási sorrendben dolgozza fel a biztonsági szabályokat. Minél alacsonyabb a szám, annál magasabb a prioritás. Javasoljuk, hogy a szabályok létrehozásakor hagyjon eltérést a prioritási számok között, például: 100, 200 és 300. A hézagok meghagyása megkönnyíti a szabályok hozzáadását a jövőben, így magasabb vagy alacsonyabb prioritást adhat nekik, mint a meglévő szabályok. |
    | **Név** | A szabály egyedi neve a hálózati biztonsági csoportban | A név legfeljebb 80 karakterből állhat. Betűvel vagy számmal kell kezdődnie, és betűvel, számmal vagy aláhúzásjellel kell végződnie. A név csak betűket, számokat, aláhúzásjeleket, pontokat vagy kötőjeleket tartalmazhat. |
    | **Leírás** | Egy szöveges leírás | Igény szerint megadhat egy szöveges leírást a biztonsági szabályhoz. A leírás nem lehet hosszabb 140 karakternél. |

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Az összes megtekintése biztonsági szabályok

A hálózati biztonsági csoportok nulla vagy több szabályt tartalmaznak. További információ a szabályok megtekintésekor felsorolt információkról: [Hálózati biztonsági csoportok áttekintése.](./network-security-groups-overview.md)

1. A hálózati [Azure Portal](https://portal.azure.com) csoport szabályainak megtekintéséhez kattintson a következőre: . Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amely számára meg szeretné tekinteni a szabályokat.

3. A hálózati biztonsági csoport menüsávjában válassza a Bejövő **biztonsági** szabályok vagy a **Kimenő biztonsági szabályok lehetőséget.**

A lista tartalmazza a létrehozott szabályokat és a hálózati biztonsági csoport alapértelmezett [biztonsági szabályait.](./network-security-groups-overview.md#default-security-rules)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

1. A hálózati [Azure Portal](https://portal.azure.com) csoport szabályainak megtekintéséhez kattintson a következőre: . Keresse meg és válassza a **Hálózati biztonsági csoportok lehetőséget.**

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amely számára meg szeretné tekinteni a szabály részleteit.

3. A hálózati biztonsági csoport menüsávjában válassza a **Bejövő** biztonsági szabályok vagy a Kimenő biztonsági **szabályok lehetőséget.**

4. Válassza ki azt a szabályt, amely adatait meg szeretné tekinteni. Az összes beállítás magyarázatát lásd: [Biztonsági szabályok beállításai.](#security-rule-settings)

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Nem működik, ha alapértelmezett biztonsági szabályt választ.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Kövesse a [Biztonsági szabály részleteinek megtekintése lépéseit.](#view-details-of-a-security-rule)

2. Szükség szerint módosítsa a beállításokat, majd válassza a **Mentés lehetőséget.** Az összes beállítás magyarázatát lásd: [Biztonsági szabályok beállításai.](#security-rule-settings)

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Az alapértelmezett biztonsági szabályokat nem módosíthatja.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Kövesse a [Biztonsági szabály részleteinek megtekintése lépéseit.](#view-details-of-a-security-rule)

2. Válassza a **Törlés**, majd az **Igen** lehetőséget.

    > [!NOTE]
    > Ez az eljárás csak az egyéni biztonsági szabályokra vonatkozik. Alapértelmezett biztonsági szabály törlése nem engedélyezett.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Alkalmazásbiztonsági csoportok használata

Az alkalmazásbiztonsági csoportok nulla vagy több hálózati adaptert tartalmaznak. További tudnivalókért lásd az [alkalmazásbiztonsági csoportokat.](./network-security-groups-overview.md#application-security-groups) Az alkalmazásbiztonsági csoportban lévő összes hálózati adapternek ugyanabban a virtuális hálózatban kell lennie. A hálózati adapterek alkalmazásbiztonsági csoporthoz való hozzáadásáról lásd: Hálózati adapter hozzáadása [alkalmazásbiztonsági csoporthoz.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Alkalmazásbiztonsági csoport létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. A keresőmezőbe írja be a következőt: *Alkalmazásbiztonsági csoport.*

3. Az **Alkalmazásbiztonsági csoport lapon** válassza a Létrehozás **lehetőséget.**

4. Az **Alkalmazásbiztonsági csoport** létrehozása lap Alapvető **beállítások** lapján állítsa be a következő beállítások értékeit:

    | Beállítás | Művelet |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **Új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához. |
    | **Név** | Adjon meg egy egyedi szöveges sztringet egy erőforráscsoporton belül. |
    | **Régió** | Válassza ki a kívánt helyet. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Az Ellenőrzés **+ létrehozás lapon,** miután megjelenik az **Érvényesítés** üzenet, válassza a **Létrehozás lehetőséget.**

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Az összes megtekintése biztonsági csoportok létrehozása

Az alkalmazásbiztonsági [Azure Portal](https://portal.azure.com) az alkalmazásbiztonsági csoportok megtekintéséhez ugrás a következőre: . Keresse meg és válassza az **Alkalmazásbiztonsági csoportok lehetőséget.** A Azure Portal megjeleníti az alkalmazásbiztonsági csoportok listáját.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Adott alkalmazásbiztonsági csoport részleteinek megtekintése

1. Az alkalmazásbiztonsági [csoport](https://portal.azure.com) Azure Portal az alkalmazásbiztonsági csoport megtekintéséhez. Keresse meg és válassza az **Alkalmazásbiztonsági csoportok lehetőséget.**

2. Válassza ki annak az alkalmazásbiztonsági csoportnak a nevét, amely adatait meg szeretné tekinteni.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alkalmazásbiztonsági csoport módosítása

1. Az alkalmazásbiztonsági [csoport](https://portal.azure.com) Azure Portal az alkalmazásbiztonsági csoport megtekintéséhez. Keresse meg és válassza az **Alkalmazásbiztonsági csoportok lehetőséget.**

2. Válassza ki a módosítani kívánt alkalmazásbiztonsági csoport nevét.

3. Válassza **a** módosítás lehetőséget a módosítani kívánt beállítás mellett. Hozzáadhat vagy eltávolíthat például címkéket, vagy módosíthatja az **erőforráscsoportot** vagy az **előfizetést.**

    > [!NOTE]
    > A helyet nem módosíthatja.

    A menüsávon kiválaszthatja a Hozzáférés-vezérlés **(IAM) lehetőséget is.** A **Hozzáférés-vezérlés (IAM)** lapon engedélyeket rendelhet hozzá vagy távolíthat el az alkalmazásbiztonsági csoporthoz.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Nincs PowerShell-parancsmag |

### <a name="delete-an-application-security-group"></a>Alkalmazásbiztonsági csoport törlése

Az alkalmazásbiztonsági csoport nem törölhető, ha az hálózati adaptereket tartalmaz. Ha el szeretné távolítani az összes hálózati adaptert az alkalmazásbiztonsági csoportból, módosítsa a hálózati adapter beállításait, vagy törölje a hálózati adaptereket. További információ: Hozzáadás alkalmazásbiztonsági csoportokhoz vagy eltávolítás az alkalmazásbiztonsági [csoportokból vagy](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) [Hálózati adapter törlése.](virtual-network-network-interface.md#delete-a-network-interface)

1. Az alkalmazásbiztonsági [Azure Portal](https://portal.azure.com) kezeléséhez ugrás a webhelyre. Keresse meg és válassza az **Alkalmazásbiztonsági csoportok lehetőséget.**

2. Válassza ki a törölni kívánt alkalmazásbiztonsági csoport nevét.

3. Válassza **a Törlés** lehetőséget, majd válassza az **Igen** lehetőséget az alkalmazásbiztonsági csoport törléséhez.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportokkal, biztonsági szabályokkal és alkalmazásbiztonsági csoportokkal [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) kapcsolatos feladatok végrehajtásához [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) fiókját a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz kell hozzárendelni, amely a következő táblázatokban felsorolt megfelelő engedélyekkel rendelkezik:

### <a name="network-security-group"></a>Hálózati biztonsági csoporttal

| Művelet                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hálózati biztonsági csoport lekérte                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Hálózati biztonsági csoport létrehozása vagy frissítése                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Hálózati biztonsági csoport törlése                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Hálózati biztonsági csoport társítása alhálózathoz vagy hálózati adapterhez 


>[!NOTE]
> Egy hálózati biztonsági csoport műveleteinek végrehajtásához az előfizetési fióknak legalább az erőforráscsoportra vonatkozó engedélyekkel és `write` `read` engedélyekkel kell `Microsoft.Network/networkSecurityGroups/write` rendelkeznie.


### <a name="network-security-group-rule"></a>Hálózati biztonsági csoport szabálya

| Művelet                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Szabály lekérte                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Szabály létrehozása vagy frissítése                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Szabály törlése                                                         |

### <a name="application-security-group"></a>Alkalmazásbiztonsági csoport

| Művelet                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP-konfiguráció csatlakozása alkalmazásbiztonsági csoporthoz|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Biztonsági szabály alkalmazásbiztonsági csoporthoz való csatlakozása    |
| Microsoft.Network/applicationSecurityGroups/read                           | Alkalmazásbiztonsági csoport lekérte                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Alkalmazásbiztonsági csoport létrehozása vagy frissítése           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Alkalmazásbiztonsági csoport törlése                     |

## <a name="next-steps"></a>Következő lépések

- Hálózat- vagy alkalmazásbiztonsági csoport létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaszkprogramokkal vagy Azure [Resource Manager sablonokkal](template-samples.md) [](cli-samples.md)
- Virtuális hálózatok [Azure Policy és hozzárendelése](./policy-reference.md)
