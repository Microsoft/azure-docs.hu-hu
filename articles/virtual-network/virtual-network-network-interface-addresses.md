---
title: IP-címek konfigurálása Azure hálózati adapterhez | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá, módosíthat és távolíthat el privát és nyilvános IP-címeket egy hálózati adapterhez.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 1df132e558421d2ec6e26c3883c89457716dfc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419014"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure hálózati adapter IP-címének hozzáadása, módosítása vagy eltávolítása

Megtudhatja, hogyan adhat hozzá, módosíthat és távolíthat el nyilvános és magánhálózati IP-címeket egy hálózati adapterhez. A hálózati adapterhez rendelt magánhálózati IP-címek lehetővé teszik a virtuális gépek számára az Azure-beli virtuális hálózatok és a csatlakoztatott hálózatok más erőforrásaival való kommunikációt. A magánhálózati IP-címek lehetővé teszik az internet felé irányuló kimenő kommunikációt előre nem látható IP-címek használatával. Egy hálózati adapterhez hozzárendelt [nyilvános IP-cím](virtual-network-public-ip-address.md) lehetővé teszi a bejövő kommunikációt az internetről a virtuális gépekre. A cím azt is lehetővé teszi, hogy a kimenő kommunikáció a virtuális gépről az internetre kiszámítható IP-cím használatával történjen. Részletekért lásd: [a kimenő kapcsolatok ismertetése az Azure-ban](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ha egy hálózati adaptert kell létrehoznia, módosítania vagy törölnie, olvassa el a [hálózati adapter kezelése](virtual-network-network-interface.md) című cikket. Ha hálózati adaptereket kell hozzáadnia a virtuális gépről, vagy el kell távolítani a hálózati adaptereket, olvassa el a [hálózati adapterek hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md) című cikket.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com , majd jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely a [hálózati adapter engedélyeiben](virtual-network-network-interface.md#permissions)felsorolt megfelelő műveletekhez van rendelve.

## <a name="add-ip-addresses"></a>IP-címek hozzáadása

A hálózati adapterekhez szükség szerint annyi [magán](#private) -és [nyilvános](#public) [IPv4](#ipv4) -címet adhat hozzá, az [Azure Limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikkben felsorolt korlátokon belül. Privát IPv6-címet adhat hozzá egy [másodlagos IP-konfigurációhoz](#secondary) (ha nincsenek meglévő másodlagos IP-konfigurációk) egy meglévő hálózati adapterhez. Az egyes hálózati adapterek legfeljebb egy IPv6 magánhálózati címen rendelkezhetnek. Opcionálisan hozzáadhat egy nyilvános IPv6-cím IPv6-hálózati adapter konfigurációhoz is. Az IPv6-címek használatával kapcsolatos részletekért lásd az [IPv6-ot](#ipv6) .

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyhez IPv4-címeket szeretne hozzáadni a listából.
3. A **Beállítások** területen válassza az **IP-konfigurációk** elemet.
4. Az **IP-konfigurációk** területen válassza a **+ Hozzáadás** lehetőséget.
5. Adja meg a következőt, majd kattintson **az OK gombra**:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Name|Yes|Egyedinek kell lennie a hálózati adapterhez|
   |Típus|Yes|Mivel IP-konfigurációt ad hozzá egy meglévő hálózati adapterhez, és az egyes hálózati adaptereknek [elsődleges](#primary) IP-konfigurációval kell rendelkezniük, az egyetlen lehetőség a **másodlagos**.|
   |Magánhálózati IP-cím hozzárendelési módszere|Yes|[**Dinamikus**](#dynamic): az Azure a következő elérhető címeket rendeli hozzá a hálózati adapterhez tartozó alhálózat-címtartomány számára. [**Statikus**](#static): Ha a hálózati adaptert a (z) rendszerhez tartozó alhálózat-címtartomány számára nem használt címeket rendel hozzá.|
   |Nyilvános IP-cím|No|**Letiltva:** A nyilvános IP-cím erőforrás jelenleg nincs társítva az IP-konfigurációhoz. **Engedélyezve:** Válasszon egy meglévő IPv4 nyilvános IP-címet, vagy hozzon létre egy újat. Ha meg szeretné tudni, hogyan hozható létre nyilvános IP-cím, olvassa el a [nyilvános IP-címekkel](virtual-network-public-ip-address.md#create-a-public-ip-address) foglalkozó cikket.|
6. Manuálisan adja hozzá a másodlagos magánhálózati IP-címeket a virtuális gép operációs rendszeréhez a [több IP-cím társítása a virtuális gép operációs](virtual-network-multiple-ip-addresses-portal.md#os-config) rendszeréhez című cikk utasításait követve. Az IP-címek virtuális gépi operációs rendszerhez való manuális hozzáadása előtt tekintse meg a [magánhálózati](#private) IP-címek című témakört. Ne adjon meg nyilvános IP-címeket a virtuális gép operációs rendszeréhez.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP-cím beállításainak módosítása

Előfordulhat, hogy módosítania kell egy IPv4-cím hozzárendelési módszerét, módosítania kell a statikus IPv4-címet, vagy módosítania kell egy hálózati adapterhez rendelt nyilvános IP-címet. Ha módosítja egy virtuális gép másodlagos hálózati adapteréhez társított másodlagos IP-konfiguráció magánhálózati IPv4-címét (További információ az [elsődleges és a másodlagos hálózati adapterekről](virtual-network-network-interface-vm.md)), helyezze a virtuális gépet a leállított (felszabadított) állapotba az alábbi lépések elvégzése előtt:

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyen meg szeretné tekinteni vagy módosítani kívánja az IP-címek beállításait a listából.
3. A **Beállítások** területen válassza az **IP-konfigurációk** elemet.
4. Válassza ki a listából a módosítani kívánt IP-konfigurációt.
5. Szükség szerint módosítsa a beállításokat az [IP-konfiguráció hozzáadása](#add-ip-addresses)az 5. lépésben megadott beállításokkal.
6. Kattintson a **Mentés** gombra.

>[!NOTE]
>Ha az elsődleges hálózati adapter több IP-konfigurációval rendelkezik, és megváltoztatja az elsődleges IP-konfiguráció magánhálózati IP-címét, akkor manuálisan kell hozzárendelni az elsődleges és másodlagos IP-címeket a Windowson belüli hálózati adapterhez (Linux esetén nem szükséges). Ha az IP-címeket manuálisan szeretné hozzárendelni egy operációs rendszeren belüli hálózati adapterhez, tekintse meg a [több IP-cím társítása virtuális gépekhez](virtual-network-multiple-ip-addresses-portal.md#os-config)című témakört. Az IP-címek virtuális gépi operációs rendszerhez való manuális hozzáadását megelőző szempontokat lásd: [magánhálózati](#private) IP-címek. Ne adjon meg nyilvános IP-címeket a virtuális gép operációs rendszeréhez.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP-címek eltávolítása

A [magánhálózati](#private) és a [nyilvános](#public) IP-címeket eltávolíthatja egy hálózati adapterről, de a hálózati adapternek mindig rendelkeznie kell legalább egy magánhálózati IPv4-címmel.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyről el szeretné távolítani az IP-címeket a listából.
3. A **Beállítások** területen válassza az **IP-konfigurációk** elemet.
4. Válasszon ki egy [másodlagos](#secondary) IP-konfigurációt (nem törölheti a törölni kívánt [elsődleges](#primary) konfigurációt), válassza a **Törlés** lehetőséget, majd válassza az **Igen** lehetőséget a törlés megerősítéséhez. Ha a konfigurációhoz hozzá van rendelve egy nyilvános IP-cím erőforrás, az erőforrás le van választva az IP-konfigurációból, de az erőforrás nem törlődik.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC IP-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurációk

A [privát](#private) és (opcionális) [nyilvános](#public) IP-címek a hálózati adapterhez rendelt egy vagy több IP-konfigurációhoz vannak rendelve. Két típusú IP-konfiguráció létezik:

### <a name="primary"></a>Elsődleges

Minden hálózati adapterhez egy elsődleges IP-konfiguráció van rendelve. Elsődleges IP-konfiguráció:

- Hozzá van rendelve egy [magánhálózati](#private) [IPv4](#ipv4) -címe. Magánhálózati [IPv6](#ipv6) -cím nem rendelhető hozzá elsődleges IP-konfigurációhoz.
- Az is lehet, hogy hozzá van rendelve egy [nyilvános](#public) IPv4-címe. Nyilvános IPv6-cím nem rendelhető hozzá elsődleges (IPv4) IP-konfigurációhoz. 

### <a name="secondary"></a>Másodlagos

Az elsődleges IP-konfiguráción kívül a hálózati adapterek nulla vagy több másodlagos IP-konfigurációval rendelkezhetnek hozzájuk rendelve. Másodlagos IP-konfiguráció:

- Hozzá kell rendelnie egy magánhálózati IPv4-vagy IPv6-cím. Ha a cím IPv6, akkor a hálózati adapter csak egy másodlagos IP-konfigurációval rendelkezhet. Ha a cím IPv4, akkor előfordulhat, hogy a hálózati adapterhez több másodlagos IP-konfiguráció van rendelve. Ha többet szeretne megtudni arról, hogy hány magán-és nyilvános IPv4-cím rendelhető hozzá egy hálózati adapterhez, tekintse meg az [Azure korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ismertető cikket.
- Az is lehet, hogy egy nyilvános IPv4-vagy IPv6-cím is hozzá van rendelve. Több IPv4-cím egy hálózati adapterhez való hozzárendeléséhez a következő helyzetekben lehet hasznos:
  - Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és TLS/SSL-tanúsítványokkal egyetlen kiszolgálón.
  - Hálózati virtuális berendezésként szolgáló virtuális gép, például tűzfal vagy terheléselosztó.
  - Bármely hálózati adapter magánhálózati IPv4-címeinek hozzáadása egy Azure Load Balancer háttér-készlethez. A múltban csak az elsődleges hálózati adapter elsődleges IPv4-címe adható hozzá a háttér-készlethez. Ha többet szeretne megtudni a több IPv4-konfiguráció elosztásáról, tekintse meg a [több IP-konfiguráció](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) terheléselosztásáról szóló cikket. 
  - A hálózati adapterhez rendelt egyik IPv6-cím elosztásának lehetősége. Ha többet szeretne megtudni arról, hogyan kell terheléselosztást alkalmazni egy privát IPv6-címre, tekintse meg az [IPv6-címek terheléselosztása](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.

## <a name="address-types"></a>Címek típusai

A következő IP-címeket rendelheti hozzá egy [IP-konfigurációhoz](#ip-configurations):

### <a name="private"></a>Személyes

A magánhálózati [IPv4](#ipv4) -vagy IPv6-címek lehetővé teszik, hogy a virtuális gépek más erőforrásokkal kommunikáljanak a virtuális hálózatokban vagy más csatlakoztatott hálózatokban. 

Alapértelmezés szerint az Azure DHCP-kiszolgálók az Azure hálózati adapter [elsődleges IP-konfigurációjához](#primary) tartozó magánhálózati IPv4-címet a virtuális gép operációs rendszerén belüli hálózati adapterhez rendelik. Ha szükséges, soha ne állítsa be manuálisan a hálózati adapter IP-címét a virtuális gép operációs rendszerén belül.

> [!WARNING]
> Ha a virtuális gép operációs rendszerén belüli hálózati adapter elsődleges IP-címeként beállított IPv4-cím már nem egyezik meg az Azure-ban található virtuális géphez csatlakoztatott elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt magánhálózati IPv4-címmel, akkor elveszti a kapcsolatot a virtuális géppel.

Vannak olyan forgatókönyvek, amelyekkel manuálisan kell beállítani egy hálózati adapter IP-címét a virtuális gép operációs rendszerén belül. Például manuálisan kell beállítania egy Windows operációs rendszer elsődleges és másodlagos IP-címét, ha több IP-címet ad hozzá egy Azure-beli virtuális géphez. Linux rendszerű virtuális gépek esetében előfordulhat, hogy csak manuálisan kell beállítania a másodlagos IP-címeket. További részletekért lásd: [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](virtual-network-multiple-ip-addresses-portal.md#os-config) . Ha bármikor módosítania kell az IP-konfigurációhoz rendelt címet, javasoljuk, hogy:

1. Győződjön meg arról, hogy a virtuális gép fogad-e címeket az Azure DHCP-kiszolgálókról. Ha már megtörtént, módosítsa az IP-cím hozzárendelését a DHCP-re az operációs rendszeren belül, majd indítsa újra a virtuális gépet.
2. A virtuális gép leállítása (felszabadítása).
3. Módosítsa az IP-címet az Azure-ban az IP-konfigurációhoz.
4. Indítsa el a virtuális gépet.
5. A másodlagos IP-címeket [manuálisan konfigurálja](virtual-network-multiple-ip-addresses-portal.md#os-config) az operációs rendszeren belül (és a Windowson belüli elsődleges IP-címen is) az Azure-ban beállított értékeknek megfelelően.

Az előző lépések követésével az Azure-on belüli hálózati adapterhez rendelt magánhálózati IP-cím és a virtuális gép operációs rendszerén belül maradnak. Ha nyomon szeretné követni, hogy az előfizetésben lévő virtuális gépeket manuálisan állította be az operációs rendszeren belüli IP-címekre, érdemes lehet hozzáadni egy Azure- [címkét](../azure-resource-manager/management/tag-resources.md) a virtuális gépekhez. Használhatja például az "IP-cím hozzárendelése: static" kifejezést. Így könnyedén megtalálhatja az előfizetésében lévő virtuális gépeket, hogy az operációs rendszeren belül manuálisan beállította az IP-címet.

Amellett, hogy a virtuális gép más erőforrásokkal is kommunikálni fog ugyanazon vagy csatlakoztatott virtuális hálózatokon, egy magánhálózati IP-cím is lehetővé teszi, hogy a virtuális gép továbbítsa a kimenő forgalmat az internethez. A kimenő kapcsolatok az Azure által a nem kiszámítható nyilvános IP-címekhez fordított forrásoldali hálózati címek. Ha többet szeretne megtudni az Azure kimenő internetkapcsolatával kapcsolatban, olvassa el az [Azure kimenő internetkapcsolatát](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ismertető cikket. Az internetről nem tud kommunikálni a virtuális gép magánhálózati IP-címével. Ha a kimenő kapcsolatok kiszámítható nyilvános IP-címet igényelnek, rendeljen hozzá egy nyilvános IP-cím típusú erőforrást egy hálózati adapterhez.

### <a name="public"></a>Nyilvános

Nyilvános IP-cím erőforráson keresztül hozzárendelt nyilvános IP-címek lehetővé teszik a virtuális géphez való bejövő kapcsolódást az internetről. Az internet felé irányuló kimenő kapcsolatok kiszámítható IP-címet használnak. További részleteket [Az Azure kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakörben talál. Nyilvános IP-címet rendelhet egy IP-konfigurációhoz, de nem szükséges. Ha nem rendel nyilvános IP-címet egy virtuális géphez egy nyilvános IP-cím erőforrás társításával, akkor a virtuális gép továbbra is kommunikálhat a kimenő állapottal az internettel. Ebben az esetben a magánhálózati IP-cím az Azure által az előre nem látható nyilvános IP-címhez fordított forrás-hálózati cím. További információ a nyilvános IP-címek erőforrásairól: [nyilvános IP-cím erőforrás](virtual-network-public-ip-address.md).

A hálózati adapterhez hozzárendelhető magán-és nyilvános IP-címek száma korlátozott. Részletekért olvassa el az [Azure korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ismertető cikket.

> [!NOTE]
> Az Azure lefordítja a virtuális gép magánhálózati IP-címét egy nyilvános IP-címhez. Ennek eredményeképpen a virtuális gép operációs rendszere nem ismeri a hozzá rendelt nyilvános IP-címeket, így nem kell manuálisan hozzárendelni egy nyilvános IP-címet az operációs rendszeren belül.

## <a name="assignment-methods"></a>Hozzárendelési módszerek

A nyilvános és magánhálózati IP-címeket a következő hozzárendelési módszerek egyikével rendeli hozzá a rendszer:

### <a name="dynamic"></a>Dinamikus

A dinamikus magánhálózati IPv4-és IPv6-(opcionális) címek alapértelmezés szerint vannak hozzárendelve.

- **Csak nyilvános**: az Azure az egyes Azure-régiókban egyedi tartományhoz rendeli a címeket. Az egyes régiókhoz rendelt tartományok megismeréséhez tekintse meg [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A címek megváltozhatnak a virtuális gép leállításakor (fel van foglalva), majd újra elindítva. Nyilvános IPv6-cím nem rendelhető hozzá IP-konfigurációhoz vagy hozzárendelési módszer használatával.
- **Csak privát**: az Azure fenntartja az első négy címet az egyes alhálózati címtartományok között, és nem rendeli hozzá a címeket. Az Azure az alhálózat címtartományának egyik erőforrásához rendeli hozzá a következő elérhető címet. Ha például az alhálózat címtartománye 10.0.0.0/16, és a 10.0.0.4-10.0.0.14 már hozzá vannak rendelve (. 0-.3 van fenntartva), az Azure 10.0.0.15 rendel hozzá az erőforráshoz. Az alapértelmezett lefoglalási módszer a dinamikus. Kiosztás után a dinamikus IP-címek csak a hálózati adapter törlésekor, a virtuális hálózaton belüli másik alhálózatra történő kiosztáskor vagy a kiosztási módszer statikusra váltása és másik IP-cím megadása esetén szabadulnak fel. Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet. 

### <a name="static"></a>Statikus

Létrehozhat egy nyilvános vagy privát statikus IPv4-vagy IPv6-címet az IP-konfigurációhoz. Ha többet szeretne megtudni arról, hogy az Azure hogyan rendel statikus nyilvános IPv4-címeket, tekintse meg a [nyilvános IP-címek](virtual-network-public-ip-address.md)című témakört.

- **Csak nyilvános**: az Azure az egyes Azure-régiókban egyedi tartományhoz rendeli a címeket. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében. A cím csak akkor változik, ha a hozzárendelt nyilvános IP-cím erőforrás törölve lett, vagy a hozzárendelési módszer dinamikusra módosul. Ha a nyilvános IP-cím erőforrás IP-konfigurációhoz van társítva, a hozzárendelési módszer módosítása előtt le kell vonni az IP-konfigurációból.
- **Csak privát**: válasszon ki és rendeljen hozzá egy címeket az alhálózat címtartományból. A hozzárendelt cím az alhálózat címtartományán belül bármilyen cím lehet, amely nem tartozik az alhálózat címtartományának első négy címébe, és nincs hozzárendelve más erőforráshoz az alhálózatban. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. Ha a kiosztási módszert statikusra módosítja, az Azure dinamikusan rendeli hozzá a korábban hozzárendelt dinamikus IP-címet statikus címnek, még akkor is, ha a cím nem az alhálózat címtartomány következő elérhető címe. A cím akkor is megváltozik, ha a hálózati adapter ugyanazon a virtuális hálózaton belül egy másik alhálózathoz lesz kiosztva, de ahhoz, hogy a hálózati adaptert egy másik alhálózathoz ossza ki, a kiosztási módszert először statikusról dinamikusra kell váltani. Miután hozzárendelte a hálózati adaptert egy másik alhálózathoz, a kiosztási módszer visszaváltható statikusra, és hozzárendelhet egy IP-címet az új alhálózat címtartományából.

## <a name="ip-address-versions"></a>IP-címek verziói

A következő verziókat adhatja meg a címek hozzárendeléséhez:

### <a name="ipv4"></a>IPv4

Minden hálózati adapternek rendelkeznie kell egy, a hozzárendelt [magánhálózati](#private) [IPv4](#ipv4) -címmel rendelkező [elsődleges](#primary) IP-konfigurációval. Hozzáadhat egy vagy több [másodlagos](#secondary) IP-konfigurációt, amelyek mindegyike IPv4-magánhálózati és (opcionálisan) IPv4 [nyilvános](#public) IP-címet tartalmaz.

### <a name="ipv6"></a>IPv6

A hálózati adapterek egy másodlagos IP-konfigurációjához nulla vagy egy magánhálózati [IPv6](#ipv6) -cím is hozzárendelhető. A hálózati adapterhez nem tartozhat meglévő másodlagos IP-konfiguráció. Az egyes hálózati adapterek legfeljebb egy IPv6 magánhálózati címen rendelkezhetnek. Opcionálisan hozzáadhat egy nyilvános IPv6-cím IPv6-hálózati adapter konfigurációhoz is.

> [!NOTE]
> Bár a portál használatával létrehozhat egy IPv6-cím hálózati adaptert, nem adhat hozzá meglévő hálózati adaptert új vagy meglévő virtuális géphez a portál használatával. A PowerShell vagy az Azure CLI használatával hozzon létre egy magánhálózati IPv6-címekkel rendelkező hálózati adaptert, majd csatlakoztassa a hálózati adaptert a virtuális gép létrehozásakor. Nem csatolhat olyan hálózati adaptert, amelyhez hozzá van rendelve egy magánhálózati IPv6-cím egy meglévő virtuális géphez. Nem adhat hozzá magánhálózati IPv6-címet a virtuális géphez csatlakoztatott bármely hálózati adapter IP-konfigurációjához bármilyen eszköz (portál, CLI vagy PowerShell) használatával.

## <a name="skus"></a>Termékváltozatok

A rendszer létrehoz egy nyilvános IP-címet az alapszintű vagy a standard SKU-val. A SKU-különbözetekről további információt a [nyilvános IP-címek kezelése](virtual-network-public-ip-address.md)című témakörben talál.

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](./network-security-groups-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="next-steps"></a>Következő lépések
Ha eltérő IP-konfigurációval rendelkező virtuális gépet szeretne létrehozni, olvassa el a következő cikkeket:

|Feladat|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása több IPv4-címmel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása magánhálózati IPv6-cím (Azure Load Balancer mögött)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|