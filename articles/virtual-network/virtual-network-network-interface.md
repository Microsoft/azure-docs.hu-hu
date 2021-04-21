---
title: Azure-beli hálózati adapter létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, mi az a hálózati adapter, és hogyan hozhat létre, módosíthat és törölhet egyet.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 8003bf14bcade08f36a7877fdb3a53998aff9e63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773067"
---
# <a name="create-change-or-delete-a-network-interface"></a>Hálózati adapter létrehozása, módosítása vagy törlése

Megtudhatja, hogyan hozhat létre, módosíthat és törölhet hálózati adaptereket. A hálózati adapter lehetővé teszi, hogy az Azure-beli virtuális gépek kommunikáljanak az internettel, az Azure-ral és a helyszíni erőforrásokkal. Amikor virtuális gépet hoz létre a Azure Portal, a portál létrehoz egy hálózati adaptert az alapértelmezett beállításokkal. Ehelyett egyéni beállításokkal is létrehozhat hálózati adaptereket, és a létrehozásakor hozzáadhat egy vagy több hálózati adaptert egy virtuális géphez. Egy meglévő hálózati adapter alapértelmezett hálózati adapter-beállításait is módosíthatja. Ez a cikk azt ismerteti, hogyan hozhat létre egyéni beállításokkal hálózati adaptert, hogyan módosíthatja a meglévő beállításokat, például a hálózatszűrő (hálózati biztonsági csoport) hozzárendelését, az alhálózat-hozzárendelést, a DNS-kiszolgáló beállításait és az IP-továbbítást, valamint hogyan törölhet hálózati adaptert.

Ha egy hálózati adapterHEZ IP-címeket kell hozzáadnia, módosítania vagy eltávolítania, lásd: [IP-címek kezelése.](virtual-network-network-interface-addresses.md) Ha hálózati adaptereket kell hozzáadnia a virtuális gépekhez, vagy hálózati adaptereket kell eltávolítania a virtuális gépekről, lásd: Hálózati adapterek hozzáadása vagy [eltávolítása.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mielőtt végrehajtja a cikk bármely szakaszának lépéseit, a következő feladatokat kell elvégeznie:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy ingyenes [próbafiókra.](https://azure.microsoft.com/free)
- Ha a portált használja, nyissa meg a https://portal.azure.com webhelyet, és jelentkezzen be Azure-fiókjával.
- Ha PowerShell-parancsokat használ a cikkben található feladatok végrehajtásához, futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell 1.0.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha Azure parancssori felületi (CLI)-parancsokat használ a cikkben található feladatok [](https://shell.azure.com/bash)végrehajtásához, futtassa a parancsokat a Azure Cloud Shell-ban, vagy futtassa a parancssori felületet a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI-t helyileg futtatja, akkor a futtatásával kapcsolatot kell `az login` létesítenie az Azure-ral.

A fióknak, amelybe bejelentkezik, vagy amelyről [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) csatlakozik az Azure-hoz, hozzá kell [rendelnie](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely a Permissions (Engedélyek) listában felsorolt megfelelő műveleteket [rendeli hozzá.](#permissions)

## <a name="create-a-network-interface"></a>Hálózati adapter létrehozása

Amikor virtuális gépet hoz létre a Azure Portal, a portál létrehoz egy hálózati adaptert az alapértelmezett beállításokkal. Ha inkább a hálózati adapter összes beállítását meg kellene adnia, létrehozhat egy egyéni beállításokkal bíró hálózati adaptert, és csatolhatja a hálózati adaptert egy virtuális géphez a virtuális gép létrehozásakor (a PowerShell vagy az Azure CLI használatával). Létrehozhat egy hálózati adaptert is, és hozzáadhatja egy meglévő virtuális géphez (a PowerShell vagy az Azure CLI használatával). Ha szeretné megtudni, hogyan hozhat létre virtuális gépet egy meglévő hálózati adapter használatával, illetve hogyan adhat hozzá hálózati adaptereket a meglévő virtuális gépekhez, illetve hogyan távolíthat el hálózati adaptereket onnan, tekintse meg a Hálózati adapterek hozzáadása vagy [eltávolítása cikkeket.](virtual-network-network-interface-vm.md) Hálózati adapter létrehozása előtt egy meglévő [](manage-virtual-network.md) virtuális hálózatnak kell lennie ugyanazon a helyen és előfizetésen, ahol létrehoz egy hálózati adaptert.

1. A lap tetején található  Erőforrások keresése szöveget tartalmazó mezőbe írja Azure Portal hálózati *adapterek szöveget.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. A **Hálózati adapterek** alatt válassza a + **Hozzáadás lehetőséget.**
3. Adja meg vagy válassza ki a következő beállítások értékeit, majd válassza a **Létrehozás lehetőséget:**

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name|Yes|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül. Idővel valószínűleg több hálózati adapter is lesz az Azure-előfizetésében. A több hálózati adapter felügyeletét megkönnyítő elnevezési konvenciók létrehozásával kapcsolatos javaslatokért lásd: [Elnevezési konvenciók.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) A hálózati adapter létrehozása után a név nem módosítható.|
    |Virtuális hálózat|Yes|Válassza ki a hálózati adapter virtuális hálózatát. Csak olyan virtuális hálózathoz rendelhet hálózati adaptert, amely ugyanabban az előfizetésben és helyen található, mint a hálózati adapter. A hálózati adapter létrehozása után nem módosíthatja azt a virtuális hálózatot, amelyhez hozzá van rendelve. A virtuális gépnek, amelyhez hozzáadja a hálózati adaptert, ugyanazon a helyen és előfizetésben kell lennie, mint a hálózati adapternek.|
    |Alhálózat|Yes|Válasszon ki egy alhálózatot a kiválasztott virtuális hálózaton belül. A létrehozás után módosíthatja azt az alhálózatot, amelyhez a hálózati adapter hozzá van rendelve.|
    |Magánhálózati IP-cím hozzárendelése|Yes| Ebben a beállításban az IPv4-cím hozzárendelési módszerét választja. Válasszon a következő hozzárendelési módszerek közül: **Dinamikus:** Ha ezt a beállítást választja, az Azure automatikusan hozzárendeli a következő elérhető címet a kiválasztott alhálózat címtartományához. **Statikus:** Ha ezt a lehetőséget választja, manuálisan kell hozzárendelnie egy elérhető IP-címet a kiválasztott alhálózat címterületében. A statikus és dinamikus címek nem változnak, amíg nem módosítja őket, vagy a hálózati adaptert nem törlik. A hozzárendelési módszer a hálózati adapter létrehozása után is megváltoztatható. Az Azure DHCP-kiszolgáló ezt a címet rendeli hozzá a hálózati adapterhez a virtuális gép operációs rendszerében.|
    |Hálózati biztonsági csoporttal|No| Hagyja meg a **Nincs** lehetőséget, válasszon ki egy meglévő hálózati biztonsági [csoportot,](./network-security-groups-overview.md)vagy hozzon [létre egy hálózati biztonsági csoportot.](tutorial-filter-network-traffic.md) A hálózati biztonsági csoportok lehetővé teszik a hálózati adapterek be- és kifelé forgalmának szűrését. Nulla vagy egy hálózati biztonsági csoportot alkalmazhat a hálózati adapterre. Nulla vagy egy hálózati biztonsági csoport is alkalmazható arra az alhálózatra, amelyhez a hálózati adapter hozzá van rendelve. Amikor hálózati biztonsági csoportot alkalmaznak egy hálózati adapterre, és az alhálózathoz a hálózati adapter hozzá van rendelve, néha váratlan eredmények következnek be. A hálózati adapterek és alhálózatok hálózati biztonsági csoportjainak hibaelhárításához lásd: [Hálózati biztonsági csoportok hibaelhárítása.](diagnose-network-traffic-filter-problem.md)|
    |Előfizetés|Yes|Válassza ki az egyik [Azure-előfizetését.](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) A virtuális gépnek, amelyhez hálózati adaptert csatol, valamint annak a virtuális hálózatnak, amelyhez csatlakoztatja, ugyanabban az előfizetésben kell lennie.|
    |Magánhálózati IP-cím (IPv6)|No| Ha bejelinti ezt a jelölőnégyzetet, a hálózati adapterhez rendelt IPv4-cím mellett egy IPv6-cím is hozzá lesz rendelve a hálózati adapterhez. Az IPv6 hálózati adapterekkel való használatával kapcsolatos fontos információkért tekintse meg a cikk IPv6-című szakaszát. Az IPv6-címhez nem választhat hozzárendelési módszert. Ha IPv6-cím hozzárendelése mellett dönt, azt a rendszer a dinamikus metódussal rendeli hozzá.
    |IPv6-név (csak akkor jelenik meg, ha a **Magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **Magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ez a név egy másodlagos IP-konfigurációhoz van hozzárendelve a hálózati adapterhez. További információ az IP-konfigurációkról: [Hálózati adapter beállításainak megtekintése.](#view-network-interface-settings)|
    |Erőforráscsoport|Yes|Válasszon ki egy meglévő [erőforráscsoportot,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vagy hozzon létre egyet. A hálózati adapter létezhet ugyanabban vagy eltérő erőforráscsoportban, mint a virtuális gép, amelyhez csatlakoztatja, vagy a virtuális hálózatban, amelyhez csatlakoztatja.|
    |Hely|Igen|A virtuális gépnek, amelyhez hálózati adaptert csatol, és a [](https://azure.microsoft.com/regions)virtuális hálózatnak, amelyhez csatlakoztatja, ugyanazon a helyen kell lennie, más néven régión belül.|

A portál nem biztosít lehetőséget arra, hogy nyilvános IP-címet rendeljen a hálózati adapterhez annak létrehozásakor, bár a portál létrehoz egy nyilvános IP-címet, és hozzárendeli azt egy hálózati adapterhez, amikor virtuális gépet hoz létre a portálon. Ha a létrehozás után szeretne nyilvános IP-címet hozzáadni a hálózati adapterhez, tekintse meg az [IP-címek kezelésével kapcsolatos cikkeket.](virtual-network-network-interface-addresses.md) Ha nyilvános IP-címmel szeretne hálózati adaptert létrehozni, a hálózati adapter létrehozásához a parancssori felületet vagy a PowerShellt kell használnia.

A portál nem biztosít lehetőséget a hálózati adapter alkalmazásbiztonsági csoportokhoz való hozzárendelésére a hálózati adapter létrehozásakor, az Azure CLI és a PowerShell viszont igen. A portálon azonban hozzárendelhet egy meglévő hálózati adaptert egy alkalmazásbiztonsági csoporthoz, ha a hálózati adapter csatolva van egy virtuális géphez. Ha szeretné megtudni, hogyan rendelhet hálózati adaptert egy alkalmazásbiztonsági csoporthoz, olvassa el a Hozzáadás alkalmazásbiztonsági csoportokhoz vagy eltávolítás alkalmazásbiztonsági [csoportokból cikkeket.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Az Azure csak azután rendel hozzá MAC-címet a hálózati adapterhez, hogy a hálózati adaptert csatlakoztatta egy virtuális géphez, és a virtuális gép első alkalommal indult el. Az Azure által a hálózati adapterhez hozzárendelt MAC-cím nem adható meg. A MAC-cím mindaddig a hálózati adapterhez lesz hozzárendelve, amíg a hálózati adaptert nem törlik, vagy meg nem változtatják az elsődleges hálózati adapter elsődleges IP-konfigurációjának magánhálózati IP-címét. További információ az IP-címekről és az IP-konfigurációkról: [IP-címek kezelése](virtual-network-network-interface-addresses.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Hálózati adapter beállításainak megtekintése

A hálózati adapterek legtöbb beállítását a létrehozásuk után megtekintheti és módosíthatja. A portál nem jeleníti meg a hálózati adapter DNS-utótagját vagy alkalmazásbiztonsági csoporttagságát. A DNS-utótag és [](#view-settings-commands) az alkalmazásbiztonsági csoport tagságának megtekintéséhez használhatja a PowerShell- vagy az Azure CLI-parancsokat.

1. A lap tetején található *Search resources* (Erőforrások keresése) szövegmezőbe írja be a Azure Portal: hálózati *adapterek.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a listából azt a hálózati adaptert, amely beállításait meg szeretné tekinteni vagy módosítani szeretné.
3. A kiválasztott hálózati adapterhez a következő elemek vannak felsorolva:
   - **Áttekintés:** Információkat nyújt a hálózati adapterről, például a hozzárendelt IP-címekről, arról a virtuális hálózatról/alhálózatról, amelyhez a hálózati adapter hozzá van rendelve, valamint a virtuális gépről, amelyhez a hálózati adapter csatlakozik (ha csatolva van egyhez). Az alábbi képen a **mywebserver256** nevű hálózati adapter áttekintő beállításai láthatóak: ![ Hálózati adapter áttekintése](./media/virtual-network-network-interface/nic-overview.png)

     A hálózati adaptert áthelyezheti egy másik erőforráscsoportba vagy előfizetésbe az Erőforráscsoport vagy az Előfizetés neve melletti **(** **módosítás) kiválasztásával.**  Ha a hálózati adaptert egy új előfizetésbe áthelyezi, akkor a hálózati adapterhez kapcsolódó összes erőforrást át kell helyezze. Ha például a hálózati adapter egy virtuális géphez van csatlakoztatva, akkor a virtuális gépet és a virtuális géphez kapcsolódó egyéb erőforrásokat is át kell helyezze. Hálózati adapter áthelyezéséhez lásd: Erőforrás áthelyezése új [erőforráscsoportba vagy előfizetésbe.](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal) A cikk felsorolja az előfeltételeket, valamint az erőforrások áthelyezését a Azure Portal, a PowerShell és az Azure CLI használatával.
   - **IP-konfigurációk:** Az IP-konfigurációkhoz rendelt nyilvános és magánhálózati IPv4- és IPv6-címek listája itt található. Ha IPv6-cím van hozzárendelve egy IP-konfigurációhoz, a cím nem jelenik meg. További információ az IP-konfigurációkról, valamint az IP-címek hozzáadásáról és eltávolításáról: IP-címek konfigurálása Azure hálózati [adapterhez.](virtual-network-network-interface-addresses.md) Ebben a szakaszban az IP-továbbítás és az alhálózat-hozzárendelés is konfigurálva van. További információ ezekről a beállításokról: [IP-továbbítás](#enable-or-disable-ip-forwarding) engedélyezése vagy letiltása és [Alhálózat-hozzárendelés módosítása.](#change-subnet-assignment)
   - **DNS-kiszolgálók:** Megadhatja, hogy az Azure DHCP-kiszolgálók melyik DNS-kiszolgálóhoz rendelnek hálózati adaptert. A hálózati adapter örökölheti a beállítást attól a virtuális hálózattól, amelyhez a hálózati adapter hozzá van rendelve, vagy olyan egyéni beállítással is rendelkezik, amely felülbírálja annak a virtuális hálózatnak a beállítását, amelyhez hozzá van rendelve. A megjelenített adatok módosításához lásd: [DNS-kiszolgálók módosítása.](#change-dns-servers)
   - **Hálózati biztonsági csoport (NSG):** Megjeleníti a hálózati adapterhez társított NSG-t (ha van). Egy NSG bejövő és kimenő szabályokat tartalmaz a hálózati adapter hálózati forgalmának szűréséhez. Ha egy NSG van a hálózati adapterhez társítva, megjelenik a társított NSG neve. A megjelenített adatok módosításához lásd: Hálózati biztonsági csoport társítása vagy [társításának társítása.](#associate-or-dissociate-a-network-security-group)
   - **Tulajdonságok:** Megjeleníti a hálózati adapter legfontosabb beállításait, beleértve annak MAC-címét (üres, ha a hálózati adapter nincs virtuális géphez csatlakoztatva), valamint az előfizetést, amelyben létezik.
   - **Hatályos biztonsági szabályok:**  A biztonsági szabályok akkor vannak felsorolva, ha a hálózati adapter egy futó virtuális géphez van csatolva, és egy NSG van társítva a hálózati adapterhez, az alhálózathoz, vagy mindkettőhöz. További információ a megjelenített adatokról: Hatályos biztonsági [szabályok megtekintése.](#view-effective-security-rules) További információ az NSG-kről: [Hálózati biztonsági csoportok.](./network-security-groups-overview.md)
   - **Hatályos útvonalak:** Az útvonalak akkor vannak felsorolva, ha a hálózati adapter egy futó virtuális géphez van csatlakoztatva. Az útvonalak az Azure alapértelmezett útvonalai, a felhasználó által megadott útvonalak és a hálózati adapterhez rendelt alhálózathoz esetlegesen létező BGP-útvonalak kombinációja. További információ a megjelenített adatokról: [Hatályos útvonalak megtekintése.](#view-effective-routes) Az Alapértelmezett Azure-útvonalakkal és a felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [Útválasztás áttekintése.](virtual-networks-udr-overview.md)
Gyakori Azure Resource Manager beállítások: [A](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)gyakori Azure Resource Manager további információért lásd: Tevékenységnapló, [](../azure-monitor/essentials/platform-logs-overview.md)Hozzáférés-vezérlés [(IAM)](../role-based-access-control/overview.md), [Címkék,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Zárolások és [Automation-szkript.](../azure-resource-manager/templates/export-template-portal.md)

<a name="view-settings-commands"></a>**Parancsok**

Ha egy IPv6-cím egy hálózati adapterhez van rendelve, a PowerShell-kimenet azt a tényt adja vissza, hogy a cím hozzá van rendelve, de a hozzárendelt címet nem. A CLI hasonlóképpen azt a tényt adja vissza, hogy a cím hozzá van rendelve, de null értéket ad vissza a kimenetében a címhez. 

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic list](/cli/azure/network/nic) az előfizetésben lévő hálózati adapterek megtekintéséhez; [az network nic show](/cli/azure/network/nic) a hálózati adapter beállításainak megtekintéséhez|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) a hálózati adapterek megtekintéséhez az előfizetésben vagy a hálózati adapter beállításainak megtekintéséhez|

## <a name="change-dns-servers"></a>DNS-kiszolgálók módosítása

A DNS-kiszolgálót az Azure DHCP-kiszolgáló rendeli hozzá a hálózati adapterhez a virtuális gép operációs rendszerében. A hálózati adapterHEZ rendelt DNS-kiszolgáló a DNS-kiszolgáló beállítástól függetlenül van hozzárendelve. A hálózati adapterek névfeloldási beállításaival kapcsolatos további információkért lásd: [Névfeloldás virtuális gépekhez.](virtual-networks-name-resolution-for-vms-and-role-instances.md) A hálózati adapter örökölheti a beállításokat a virtuális hálózattól, vagy használhatja a saját egyedi beállításait, amelyek felülbírálják a virtuális hálózat beállításait.

1. A lap tetején található  Erőforrások keresése szöveget tartalmazó mezőbe írja Azure Portal hálózati *adapterek szöveget.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a listából azt a hálózati adaptert, amely dns-kiszolgálóját módosítani szeretné.
3. A **BEÁLLÍTÁSOK alatt válassza a DNS-kiszolgálók** **lehetőséget.**
4. Válassza a következő két lehetőséget:
   - **Öröklődik a virtuális hálózatról:** Ezzel a beállítással örökölheti a dns-kiszolgálónak azt a beállítását, amely ahhoz a virtuális hálózathoz van meghatározva, amelyhez a hálózati adapter hozzá van rendelve. A virtuális hálózat szintjén egy egyéni DNS-kiszolgáló vagy az Azure által biztosított DNS-kiszolgáló van meghatározva. Az Azure által biztosított DNS-kiszolgáló képes feloldani az azonos virtuális hálózathoz rendelt erőforrások gazdagépnevét. A különböző virtuális hálózatokhoz rendelt erőforrások feloldásához FQDN-t kell használni.
   - **Egyéni:** Konfigurálhatja a saját DNS-kiszolgálóját úgy, hogy több virtuális hálózaton oldja fel a neveket. Adja meg a DNS-kiszolgálóként használni kívánt kiszolgáló IP-címét. A megadott DNS-kiszolgálócím csak ehhez a hálózati adapterhez van hozzárendelve, és felülbírálja azon virtuális hálózat DNS-beállítását, amelyhez a hálózati adapter hozzá van rendelve.
     >[!Note]
     >Ha a virtuális gép olyan hálózati adaptert használ, amely egy rendelkezésre állási csoport része, akkor a rendelkezésre állási csoportban lévő összes hálózati adapter minden egyes virtuális gépéhez megadott DNS-kiszolgáló öröklődik.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>IP-továbbítás engedélyezése vagy letiltása

Az IP-továbbítás lehetővé teszi a virtuális gép számára, hogy hálózati adaptert csatol a következőhöz:
- Olyan hálózati forgalmat fogad, amely nem a hálózati adapterhez rendelt IP-konfigurációk egyikének ip-címére sem lesz kiosztva.
- A hálózati adapter IP-konfigurációihoz rendelt ip-címtől eltérő forrás IP-címmel küldjön hálózati forgalmat.

A beállítást minden olyan hálózati adapterhez engedélyezni kell, amely csatolva van a virtuális géphez, és fogadja a virtuális gép által továbbítható forgalmat. A virtuális gépek továbbítják a forgalmat, függetlenül attól, hogy több hálózati adaptert vagy egyetlen hálózati adaptert csatlakoztatnak hozzá. Bár az IP-továbbítás egy Azure-beállítás, a virtuális gépnek a forgalmat továbbító alkalmazást is futtatnia kell, például tűzfalat, WAN-optimalizálást és terheléselosztási alkalmazásokat. Ha egy virtuális gép hálózati alkalmazásokat futtat, a virtuális gépet gyakran hálózati virtuális berendezésnek is nevezik. Az üzembe helyezésre kész hálózati virtuális berendezések listáját a következő [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) Az IP-továbbítást általában felhasználó által megadott útvonalakkal használják. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [Felhasználó által megadott útvonalak.](virtual-networks-udr-overview.md)

1. A lap tetején található *Search resources* (Erőforrások keresése) szövegmezőbe írja be a Azure Portal: hálózati *adapterek.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amely számára engedélyezni vagy letiltani szeretné az IP-továbbítást.
3. A **BEÁLLÍTÁSOK szakaszban** válassza az **IP-konfigurációk** lehetőséget.
4. A **beállítás módosításhoz** válassza az Engedélyezve vagy a **Letiltva** (alapértelmezett beállítás) lehetőséget.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alhálózat-hozzárendelés módosítása

Módosíthatja az alhálózatot, de azt a virtuális hálózatot, amelyhez a hálózati adapter hozzá van rendelve.

1. A lap tetején található *Search resources* (Erőforrások keresése) szövegmezőbe írja be a Azure Portal: hálózati *adapterek.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amely alhálózat-hozzárendelését módosítani szeretné.
3. A **BEÁLLÍTÁSOK alatt válassza az IP-konfigurációk** **lehetőséget.** Ha a felsorolt IP-konfigurációk bármelyik magánhálózati IP-címe **mellett (Statikus)** szerepel, az alábbi lépések elvégzésével dinamikusra kell módosítania az IP-cím-hozzárendelési módszert. A hálózati adapter alhálózat-hozzárendelésének módosítása érdekében minden magánhálózati IP-címet a dinamikus hozzárendelési módszerrel kell hozzárendelni. Ha a címek a dinamikus metódussal vannak hozzárendelve, folytassa az 5. lépéssel. Ha bármely IPv4-cím statikus hozzárendelési módszerrel van hozzárendelve, az alábbi lépésekkel dinamikusra módosíthatja a hozzárendelési módszert:
   - Válassza ki azt az IP-konfigurációt, amelynél módosítani szeretné az IPv4-cím-hozzárendelési módszert az IP-konfigurációk listájából.
   - Válassza **a Dinamikus** lehetőséget a magánhálózati IP-cím **hozzárendelési módszereként.** Statikus hozzárendelési módszerrel nem rendelhet IPv6-címet.
   - Kattintson a **Mentés** gombra.
4. Az Alhálózat legördülő listából válassza ki  azt az alhálózatot, amelybe át szeretné áthelyezni a hálózati adaptert.
5. Kattintson a **Mentés** gombra. Az új dinamikus címek az alhálózat címtartományból vannak hozzárendelve az új alhálózathoz. Miután hozzárendeli a hálózati adaptert egy új alhálózathoz, statikus IPv4-címet rendelhet hozzá az új alhálózat címtartományból, ha úgy dönt. Az IP-címek hálózati adapterhez való hozzáadásával, módosításával és eltávolításával kapcsolatos további információkért lásd: [IP-címek kezelése.](virtual-network-network-interface-addresses.md)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Hozzáadás alkalmazásbiztonsági csoportokhoz vagy eltávolítás az alkalmazásbiztonsági csoportokból

Hálózati adaptert csak akkor adhat hozzá vagy távolíthat el egy alkalmazásbiztonsági csoportból a portál használatával, ha a hálózati adapter csatolva van egy virtuális géphez. A PowerShell vagy az Azure CLI használatával hálózati adaptert adhat hozzá vagy távolíthat el egy hálózati adaptert egy alkalmazásbiztonsági csoportból, függetlenül attól, hogy a hálózati adapter virtuális géphez van-e csatlakoztatva. További információ az [alkalmazásbiztonsági csoportokról](./network-security-groups-overview.md#application-security-groups) és [az alkalmazásbiztonsági csoportok létrehozásáról.](manage-network-security-group.md)

1. A portál tetején található *Erőforrások,* szolgáltatások és dokumentumok keresése mezőben kezdje el beírni egy olyan virtuális gép nevét, amely egy alkalmazásbiztonsági csoporthoz hozzáadni vagy abból eltávolítani kívánt hálózati adaptert használ. Amikor a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
2. A **BEÁLLÍTÁSOK** területen válassza a **Hálózatkezelés** elemet.  Válassza **az Alkalmazásbiztonsági csoportok,** majd az Alkalmazásbiztonsági csoportok konfigurálása lehetőséget, válassza ki azokat az alkalmazásbiztonsági csoportokat, amelyekhez hozzá szeretné adni a hálózati adaptert, vagy törölje az alkalmazásbiztonsági csoportok kijelölését, amelyekből el szeretné távolítani a hálózati adaptert, majd kattintson a Mentés gombra.  Csak az azonos virtuális hálózaton található hálózati adapterek hozzáadhatóak ugyanannak az alkalmazásbiztonsági csoportnak. Az alkalmazásbiztonsági csoportnak ugyanazon a helyen kell lennie, mint a hálózati adapternek.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Hálózati biztonsági csoport társítása vagy társításának mellőzása

1. A portál tetején található keresőmezőbe írja be a *hálózati adaptereket* a keresőmezőbe. Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert a listában, amelyhez hálózati biztonsági csoportot szeretne társítani, vagy válassza le a hálózati biztonsági csoportot.
3. A **BEÁLLÍTÁSOK alatt válassza a** Hálózati biztonsági csoport **lehetőséget.**
4. Válassza a **Szerkesztés** elemet.
5. Válassza **a Hálózati biztonsági csoport** lehetőséget, majd válassza ki a hálózati adapterhez társítani kívánt hálózati biztonsági csoportot, vagy válassza a **Nincs** lehetőséget a hálózati biztonsági csoport társításának kijelöléséhez.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az_network_nic_update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Hálózati adapter törlése

A hálózati adaptereket törölheti, ha az nincs virtuális géphez csatlakoztatva. Ha egy hálózati adapter csatlakozik egy virtuális géphez, először leállított (felszabadított) állapotba kell állítania a virtuális gépet, majd le kell leválasztani a hálózati adaptert a virtuális gépről. A hálózati adapter virtuális gépről való leválasztásához kövesse a Hálózati adapter leválasztása [virtuális gépről lépéseit.](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) A hálózati adaptert nem lehet leválasztani a virtuális gépről, ha azonban ez a virtuális géphez csatolt egyetlen hálózati adapter. A virtuális gépeknek mindig legalább egy hálózati adaptert kell csatlakoztatva lennie. Egy virtuális gép törlése leválasztja az összes csatlakoztatott hálózati adaptert, de nem törli a hálózati adaptereket.

1. A lap tetején található  Erőforrások keresése szöveget tartalmazó mezőbe írja Azure Portal hálózati *adapterek szöveget.* Amikor **a hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a törölni kívánt hálózati adaptert a listában.
3. Az Áttekintés **alatt válassza** a **Törlés lehetőséget.**
4. Válassza **az Igen** lehetőséget a hálózati adapter törlésének megerősítéséhez.

Hálózati adapter törlésekor a hozzá rendelt MAC- vagy IP-címek felszabadulnak.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Csatlakozási problémák megoldása

Ha nem tud kommunikálni egy virtuális gépről vagy gépről, a hálózati adapterre vonatkozó hálózati biztonsági szabályok vagy útvonalak okozhatják a problémát. A probléma megoldásához a következő lehetőségek állnak rendelkezésre:

### <a name="view-effective-security-rules"></a>Az hatályos biztonsági szabályok megtekintése

A virtuális gépekhez csatolt egyes hálózati adapterek hatályos biztonsági szabályai a hálózati biztonsági csoportban létrehozott szabályok és az alapértelmezett biztonsági szabályok [kombinációja.](./network-security-groups-overview.md#default-security-rules) A hálózati adapterek hatályos biztonsági szabályainak megértése segíthet meghatározni, hogy miért nem tud kommunikálni a virtuális géppel vagy a virtuális gépről. Megtekintheti a futó virtuális gépekhez csatolt hálózati adapterek hatályos szabályait.

1. A portál tetején található keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynél meg szeretné tekinteni az hatályos biztonsági szabályokat. Ha nem ismeri a virtuális gép nevét, írja be a *keresőmezőbe* a virtuális gépeket. Amikor **a Virtuális gépek** megjelenik a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. A **BEÁLLÍTÁSOK alatt válassza a** Hálózat **lehetőséget.**
3. Válassza ki a hálózati adapter nevét.
4. A TÁMOGATÁS + HIBAELHÁRÍTÁS **alatt válassza** az Hatályos biztonsági szabályok **lehetőséget.**
5. Tekintse át az hatályos biztonsági szabályok listáját, és állapítsa meg, hogy a megfelelő szabályok vonatkoznak-e a szükséges bejövő és kimenő kommunikációra. További információ a Hálózati biztonsági csoportok áttekintése [listában látható információkról.](./network-security-groups-overview.md)

Az Azure Network Watcher IP-forgalom-ellenőrző funkciója azt is segít megállapítani, hogy a biztonsági szabályok megakadályozzák-e a virtuális gépek és a végpontok közötti kommunikációt. További információ: [IP-forgalom ellenőrzése.](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Parancsok**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Az hatályos útvonalak megtekintése

A virtuális géphez csatolt hálózati adapterek érvényes útvonalai az alapértelmezett útvonalak, az Ön által létrehozott útvonalak és a BGP-n keresztül egy Azure virtuális hálózati átjárón keresztül a helyszíni hálózatokról propagált útvonalak kombinációja. A hálózati adapterek hatályos útvonalának megértése segíthet meghatározni, hogy miért nem tud kommunikálni egy virtuális géppel vagy gépről. Megtekintheti a futó virtuális gépekhez csatolt hálózati adapterek hatályos útvonalait.

1. A portál tetején található keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynél meg szeretné tekinteni az hatályos biztonsági szabályokat. Ha nem ismeri a virtuális gép nevét, írja be a *keresőmezőbe* a virtuális gépeket. Amikor **a Virtuális gépek** megjelenik a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. A **BEÁLLÍTÁSOK alatt válassza a** Hálózat **lehetőséget.**
3. Válassza ki a hálózati adapter nevét.
4. Válassza **az Effective routes (Hatályos útvonalak)** lehetőséget a SUPPORT + **TROUBLESHOOTING (TÁMOGATÁS + HIBAELHÁRÍTÁS) alatt.**
5. Tekintse át az hatályos útvonalak listáját, és állapítsa meg, hogy a megfelelő útvonalak létezik-e a szükséges bejövő és kimenő kommunikációhoz. További információ az Útválasztás áttekintése [listában látható adatokról.](virtual-networks-udr-overview.md)

Az Azure Network Watcher következő ugrás funkciója azt is segít megállapítani, hogy az útvonalak megakadályozzák-e a virtuális gép és a végpont közötti kommunikációt. További információ: Következő [ugrás.](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Parancsok**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Engedélyek

A hálózati adapterek feladatainak végrehajtásához [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) fiókját hozzá kell rendelni a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely a következő táblázatban felsorolt megfelelő engedélyekkel rendelkezik:

| Művelet                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hálózati adapter lekért                                     |
| Microsoft.Network/networkInterfaces/write                                  | Hálózati adapter létrehozása vagy frissítése                        |
| Microsoft.Network/networkInterfaces/join/action                            | Hálózati adapter csatolása virtuális géphez           |
| Microsoft.Network/networkInterfaces/delete                                 | Hálózati adapter törlése                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Erőforrás hálózati adapterhez való csatlakozása egy szolgáltatáson keresztül...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | A hálózati adapter hatályos útválasztási táblázatának lekérte               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | A hálózati adapter hatályos biztonsági csoportjainak lekért beállítása           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hálózati adapterek terheléselosztásának lekért része                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Szolgáltatás társításának lekérte                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Szolgáltatás-társítás létrehozása vagy frissítése                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Szolgáltatás társításának törlése                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Szolgáltatás társításának ellenőrzése                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hálózati adapter IP-konfigurációjának lekért beállítása                    |

## <a name="next-steps"></a>Következő lépések

- Több hálózati etitokkal rendelkező virtuális gép létrehozása az [Azure CLI vagy](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [PowerShell használatával](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Egyetlen hálózati adapterrel rendelkező virtuális gép létrehozása több IPv4-címmel az [Azure CLI vagy](virtual-network-multiple-ip-addresses-cli.md) a [PowerShell használatával](virtual-network-multiple-ip-addresses-powershell.md)
- Hozzon létre egy privát IPv6-címmel (egy virtuális gép mögötti) hálózati adapterrel (Azure Load Balancer) az [Azure CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [a PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy a [Azure Resource Manager használatával](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Hálózati adapter létrehozása [PowerShell- vagy](powershell-samples.md) Azure CLI-mintaszkprogramokkal vagy Azure [Resource Manager sablon használatával](template-samples.md) [](cli-samples.md)
- Virtuális hálózatok [Azure Policy és hozzárendelése](./policy-reference.md)