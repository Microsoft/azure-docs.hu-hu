---
title: Nyilvános Azure IP-címelőtag létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: 'Ismerje meg a nyilvános IP-címelőtagokat, valamint azok létrehozási, módosítási és törlési útmutatóját. További információkért lásd: hol található.'
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783430"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Nyilvános IP-címelőtag létrehozása, módosítása vagy törlése

Megismerhet egy nyilvános IP-címelőtagot, és megtudhatja, hogyan hozhat létre, módosíthat és törölhet egyet. A nyilvános IP-címelőtag címek összefüggő tartománya a megadott nyilvános IP-címek száma alapján. A címek az előfizetéshez vannak rendelve. Nyilvános IP-cím erőforrás létrehozásakor statikus nyilvános IP-címet rendelhet az előtagból, és hozzárendelheti a címet virtuális gépekhez, terheléselosztáshoz vagy más erőforrásokhoz az internetkapcsolat engedélyezéséhez. Ha még nem ismeri a nyilvános IP-címelőtagokat, tekintse meg a nyilvános [IP-címelőtagok áttekintésével kapcsolatos témakört.](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mielőtt végrehajtja a cikk bármely szakaszának lépéseit, a következő feladatokat kell elvégeznie:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy ingyenes [próbafiókra.](https://azure.microsoft.com/free)
- Ha a portált használja, nyissa meg a https://portal.azure.com webhelyet, és jelentkezzen be Azure-fiókjával.
- Ha PowerShell-parancsokat használ a cikkben található feladatok elvégzéséhez, futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsokat, vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz a Azure PowerShell 1.0.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha Azure parancssori felületi (CLI)-parancsokat használ a cikkben található feladatok [](https://shell.azure.com/bash)végrehajtásához, futtassa a parancsokat a Azure Cloud Shell-ban, vagy futtassa a parancssori felületet a számítógépről. Az oktatóanyaghoz az Azure CLI 2.0.41-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI-t helyileg futtatja, akkor a futtatásával kapcsolatot kell `az login` létesítenie az Azure-ral.

A fióknak, amelybe bejelentkezik, vagy amelyről [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) csatlakozik az Azure-hoz, hozzá kell [rendelnie](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely a Permissions (Engedélyek) listában felsorolt megfelelő műveleteket [rendeli hozzá.](#permissions)

A nyilvános IP-címelőtagok díjat számolnak fel. Részletekért lásd a [díjszabást.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address-prefix"></a>Nyilvános IP-címelőtag létrehozása

1. A portál bal felső sarkában válassza a **+ Erőforrás létrehozása lehetőséget.**
2. Írja *be a nyilvános IP-előtagot* a Keresés a *Marketplace-en mezőbe.* Amikor **a nyilvános IP-címelőtag** megjelenik a keresési eredmények között, válassza ki.
3. A **Nyilvános IP-címelőtag alatt válassza** a Létrehozás **lehetőséget.**
4. Adja meg vagy válassza ki a következő beállítások értékeit a **Nyilvános IP-címelőtag** létrehozása alatt, majd válassza a **Létrehozás lehetőséget:**

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Előfizetés|Yes|Ugyanabban az előfizetésben kell lennie, mint annak az erőforrásnak, amelyhez társítani szeretné a nyilvános IP-címet. [](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)|
   |Erőforráscsoport|Yes|Ugyanabban vagy eltérő erőforráscsoportban [](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) is létezhet, mint az az erőforrás, amelyhez társítani szeretné a nyilvános IP-címet.|
   |Name|Yes|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |Region|Yes|Ugyanabban a régióban kell [lennie,](https://azure.microsoft.com/regions)mint a tartományból hozzárendelni fogja a címeket a nyilvános IP-címeknek.|
   |Előtag mérete|Yes| A szükséges előtag mérete. Az alapértelmezett érték a /28 vagy 16 IP-cím.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>A rendelkezésre állási zónákkal társított régiókban PowerShell- vagy CLI-parancsokkal hozhat létre nyilvános IP-címelőtagot a következő ként: nem zóna szintű, egy adott zónához társítva, vagy zónaredundania használatával.  A 2020-08-01-es vagy újabb API-verzió esetén, ha nincs zónaparaméter megadva, létrejön egy nem zónaális nyilvános IP-címelőtag. A 2020-08-01-esnél régebbi API-verziókhoz zónaredundáns nyilvános IP-címelőtag jön létre. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Statikus nyilvános IP-cím létrehozása előtagból
Az előtag létrehozása után statikus IP-címeket kell létrehoznia az előtagból. Ehhez kövesse az alábbi lépéseket.

1. A lap tetején található Search *resources* (Erőforrások keresése) szövegmezőbe írja Azure Portal *nyilvános IP-címelőtagot.* Amikor **nyilvános IP-címelőtagok** jelennek meg a keresési eredmények között, válassza ki.
2. Válassza ki azt az előtagot, amelyből nyilvános IP-eket szeretne létrehozni.
3. Amikor megjelenik a keresési eredmények között, jelölje ki, és kattintson az Áttekintés szakasz **+IP-cím** hozzáadása gombjára.
4. A Nyilvános IP-cím létrehozása alatt adja meg vagy válassza ki az alábbi **beállítások értékeit.** Mivel az előtag standard termékváltozathoz, IPv4-hez és statikushoz való, csak a következő információkat kell meg adnia:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Name|Yes|A nyilvános IP-cím nevének egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |Üresjárati időkorlát (perc)|No|Hány percig lehet nyitva tartani egy TCP- vagy HTTP-kapcsolatot anélkül, hogy az ügyfeleknek életben tartás üzeneteket kellene küldeniük. |
   |DNS-névcímke|No|Egyedinek kell lennie abban az Azure-régióban, ahol a nevet létrehozza (az összes előfizetésben és az összes ügyfélben). Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, így Ön egy nevű erőforráshoz csatlakozhat. Az Azure hozzáfűz egy alapértelmezett alhálózatot, például a *location.cloudapp.azure.com* (ahol a hely a kiválasztott hely) a névhez, a teljes DNS-név létrehozásához. További információkért lásd a [Azure DNS Nyilvános Azure IP-címmel való használatát.](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)|

Az alábbi CLI- és PS-parancsokat a --public-ip-prefix (CLI) és a -PublicIpPrefix (PS) paraméterekkel is használhatja nyilvános IP-cím erőforrás létrehozásához. 

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Előtag megtekintése vagy törlése

1. A lap tetején található Search *resources* (Erőforrások keresése) szövegmezőbe írja Azure Portal *nyilvános IP-címelőtagot.* Amikor **nyilvános IP-címelőtagok** jelennek meg a keresési eredmények között, válassza ki.
2. Válassza ki a listában megtekinteni, módosítani vagy törölni kívánt nyilvános IP-címelőtag nevét.
3. Attól függően, hogy meg szeretné tekinteni, törölni vagy módosítani szeretné a nyilvános IP-címelőtagot, az alábbi lehetőségek egyikét kell végrehajtania.
   - **Nézet:** Az **Áttekintés szakasz** a nyilvános IP-címelőtag, például az előtag kulcsbeállításait jeleníti meg.
   - **Törlés:** A nyilvános IP-címelőtag törléséhez válassza a **Törlés** lehetőséget az **Áttekintés szakaszban.** Ha az előtagon belüli címek nyilvános IP-cím-erőforrásokhoz vannak társítva, először törölnie kell a nyilvános IP-cím erőforrásait. Lásd: [Nyilvános IP-cím törlése.](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) to list public IP addresses, [az az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) to show settings; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) to update; [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) to delete|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) nyilvános IP-címobjektum lekéréséhez és beállításainak megtekintéséhez: [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) a beállítások frissítéséhez; [Törölni kívánt Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)|

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címelőtagok feladatainak végrehajtásához a [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) fiókját hozzá [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kell rendelni a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely a következő táblázatban felsorolt megfelelő műveleteket rendeli hozzá:

| Művelet                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Nyilvános IP-címelőtag olvasása                                |
| Microsoft.Network/publicIPPrefixes/write                          | Nyilvános IP-címelőtag létrehozása vagy frissítése                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Nyilvános IP-címelőtag törlése                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Nyilvános IP-cím létrehozása előtagból |

## <a name="next-steps"></a>Következő lépések

- A nyilvános IP-előtag használatának forgatókönyvei és [előnyei](public-ip-address-prefix.md)
