---
title: Azure nyilvános IP-cím előtagjának létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: 'Ismerje meg a nyilvános IP-címek előtagjait, valamint a létrehozásával, módosításával vagy törlésével kapcsolatos tudnivalókat. További információ: hol találhatók.'
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
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938653"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Nyilvános IP-címelőtag létrehozása, módosítása vagy törlése

Ismerje meg a nyilvános IP-cím előtagját, valamint azt, hogyan hozhat létre, módosíthat és törölhet egyet. A nyilvános IP-cím előtagja a megadott nyilvános IP-címek száma alapján a címek folytonos tartománya. A címek hozzá vannak rendelve az előfizetéshez. Amikor létrehoz egy nyilvános IP-cím erőforrást, hozzárendelhet egy statikus nyilvános IP-címet az előtagból, és hozzárendelheti a címet a virtuális gépekhez, a terheléselosztóhoz vagy más erőforrásokhoz az internetkapcsolat engedélyezéséhez. Ha még nem ismeri a nyilvános IP-címek előtagjait, tekintse meg a [nyilvános IP-cím előtagjának áttekintése](public-ip-address-prefix.md) című témakört.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com , majd jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.41 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

A nyilvános IP-címek előtagjai díjkötelesek. Részletekért tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Nyilvános IP-cím előtagjának létrehozása

1. A portál bal felső sarkában válassza az **+ erőforrás létrehozása** lehetőséget.
2. A *Keresés a piactéren* mezőbe írja be a *nyilvános IP-előtagot* . Ha a **nyilvános IP-cím előtag** megjelenik a keresési eredmények között, válassza ki.
3. A **nyilvános IP-cím előtagja** területen válassza a **Létrehozás** lehetőséget.
4. Adja meg vagy válassza ki a következő beállítások értékeit a **nyilvános IP-cím létrehozása** lehetőség alatt, majd válassza a **Létrehozás** lehetőséget:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Előfizetés|Yes|Ugyanabban az [előfizetésben](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) kell lennie, mint azt az erőforrást, amelyhez hozzá szeretné rendelni a nyilvános IP-címet.|
   |Erőforráscsoport|Yes|Ugyanabban a vagy más [erőforrás-csoportban](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) létezhet, mint azt az erőforrást, amelyhez hozzá szeretné rendelni a nyilvános IP-címet.|
   |Name|Yes|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporthoz.|
   |Region|Yes|Ugyanabban a [régióban](https://azure.microsoft.com/regions)kell lennie, mint a nyilvános IP-címek, a tartományból kell címeket rendelni.|
   |Előtag mérete|Yes| A szükséges előtag mérete. Az alapértelmezett érték a/28 vagy 16 IP-cím.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network Public-IP előtag létrehozása](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Új – AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>A rendelkezésre állási zónákkal rendelkező régiókban a PowerShell vagy a CLI parancsok használatával létrehozhat egy nyilvános IP-cím előtagot: nem zóna nélküli, adott zónához társított, vagy a zóna-redundancia használata.  A 2020-08-01-es vagy újabb API-verzió esetén, ha nincs megadva zóna paraméter, a rendszer létrehozza a nem zónákhoz tartozó nyilvános IP-cím előtagját. Az 2020-08-01-nál régebbi API-verziók esetében létrejön egy zóna – redundáns nyilvános IP-cím előtagja. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Statikus nyilvános IP-cím létrehozása előtagból
Az előtag létrehozása után statikus IP-címeket kell létrehoznia az előtagból. Ennek elvégzéséhez kövesse az alábbi lépéseket.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőben írja be a *nyilvános IP-cím előtagját*. Ha a **nyilvános IP-címek előtagjai** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt az előtagot, amelyhez nyilvános IP-címeket kíván létrehozni.
3. Ha megjelenik a keresési eredmények között, válassza ki azt, és kattintson az **IP-cím hozzáadása** lehetőségre az Áttekintés szakaszban.
4. Adja meg vagy válassza ki az alábbi beállítások értékeit a **nyilvános IP-cím létrehozása** területen. Mivel a standard SKU-hoz, az IPv4-hez és a statikushoz tartozik egy előtag, csak a következő információkat kell megadnia:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Name|Yes|A nyilvános IP-cím nevének egyedinek kell lennie a kiválasztott erőforráscsoporthoz.|
   |Üresjárati időkorlát (perc)|No|Hány percet tart a TCP-vagy HTTP-kapcsolatok megnyitása anélkül, hogy az ügyfelek a Keep-Alive üzenetek küldésére támaszkodnak. |
   |DNS-névcímke|No|Egyedinek kell lennie az Azure-régióban, amelyben a nevet létrehozza (az összes előfizetésben és az összes ügyfélen). Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, hogy csatlakozni tudjanak a névvel rendelkező erőforráshoz. Az Azure a teljes DNS-név létrehozásához hozzáfűz egy alapértelmezett alhálózatot, például a *Location.cloudapp.Azure.com* (ahol a hely az Ön által kiválasztott hely). További információ: [Azure DNS használata Azure nyilvános IP-címmel](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Másik lehetőségként használhatja a CLI és a PS parancsokat is a--Public-IP-előtag (CLI) és a-PublicIpPrefix (PS) paraméterekkel a nyilvános IP-cím erőforrás létrehozásához. 

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Előtag megtekintése vagy törlése

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőben írja be a *nyilvános IP-cím előtagját*. Ha a **nyilvános IP-címek előtagjai** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a megtekinteni kívánt nyilvános IP-cím előtagjának nevét, a beállítások módosítását vagy a listából való törlését.
3. Hajtsa végre a következő lehetőségek egyikét attól függően, hogy meg szeretné-e tekinteni, törölni vagy módosítani a nyilvános IP-cím előtagját.
   - **Megtekintés**: az **Áttekintés** szakasz a nyilvános IP-cím előtagjának legfontosabb beállításait, például az előtagot tartalmazza.
   - **Törlés**: a nyilvános IP-cím előtag törléséhez válassza a **Törlés** lehetőséget az **Áttekintés** szakaszban. Ha az előtagon belüli címek a nyilvános IP-cím erőforrásaihoz vannak társítva, először törölnie kell a nyilvános IP-címek erőforrásait. Lásd: [nyilvános IP-cím törlése](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network Public-IP előtag-lista](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) a nyilvános IP-címek listázásához [az az Network Public-IP előtag megjelenítése](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) a beállítások megjelenítéséhez; [az Network Public-IP előtag frissítése](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) a frissítéshez; [az Network Public-IP előtag törlés](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) törölni|
|PowerShell|A [Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) egy nyilvános IP-cím objektumot kér le, és megtekinti a beállításait, a [set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) a beállítások frissítéséhez; A DELETE [-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) törlése|

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címek előtagjaival kapcsolatos feladatok elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Művelet                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft. Network/publicIPPrefixes/READ                           | Nyilvános IP-cím előtagjának beolvasása                                |
| Microsoft. Network/publicIPPrefixes/Write                          | Nyilvános IP-cím előtagjának létrehozása vagy frissítése                    |
| Microsoft. Network/publicIPPrefixes/delete                         | Nyilvános IP-cím előtagjának törlése                              |
|Microsoft. Network/publicIPPrefixes/csatlakozás/művelet                     | Nyilvános IP-cím létrehozása előtagból |

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [nyilvános IP-előtag](public-ip-address-prefix.md) használatának forgatókönyveit és előnyeit
