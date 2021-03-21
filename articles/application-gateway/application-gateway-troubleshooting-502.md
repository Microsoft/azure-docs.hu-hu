---
title: Hibás átjárókkal kapcsolatos hibák elhárítása – Azure Application Gateway
description: 'Ismerje meg, hogyan lehet elhárítani a Application Gateway kiszolgálóhiba: 502 – a webkiszolgáló érvénytelen választ kapott az átjáróként vagy proxykiszolgálóként való működés közben.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182937"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Hibás átjárókkal kapcsolatos hibák elhárítása az Application Gatewayben

Megtudhatja, hogyan lehet elhárítani az Azure Application Gateway használatakor kapott hibás átjáró (502) hibáit.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Az Application Gateway konfigurálása után a következő hibaüzenetek egyike a "kiszolgálóhiba: 502 – a webkiszolgáló érvénytelen választ kapott az átjáróként vagy a proxykiszolgálóként való működés közben". Ez a hiba a következő fő okok miatt fordulhat elő:

* A NSG, a UDR vagy az egyéni DNS blokkolja a háttérbeli készlet tagjainak hozzáférését.
* A háttérben futó virtuális gépek vagy virtuálisgép-méretezési csoport példányai nem válaszolnak az alapértelmezett állapot-mintavételre.
* Az egyéni állapotú mintavételek érvénytelenek vagy helytelenek.
* Az Azure Application Gateway [háttér-készlete nincs konfigurálva vagy üres](#empty-backendaddresspool).
* A [virtuálisgép-méretezési csoportba tartozó virtuális](#unhealthy-instances-in-backendaddresspool)gépek és példányok egyike sem kifogástalan állapotú.
* [Időtúllépési vagy csatlakozási problémák kérése](#request-time-out) a felhasználói kérelmekkel kapcsolatban.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Hálózati biztonsági csoport, felhasználó által megadott útvonal vagy egyéni DNS-probléma

### <a name="cause"></a>Ok

Ha a háttér-hozzáférés NSG, UDR vagy egyéni DNS miatt le van tiltva, az Application Gateway példányai nem érhetik el a háttér-készletet. Emiatt a mintavételi hibák miatt 502 hiba történt.

A NSG/UDR lehet jelen az Application Gateway alhálózatban vagy abban az alhálózatban, amelyben az alkalmazás virtuális gépei telepítve vannak.

Hasonlóképpen a VNet lévő egyéni DNS jelenléte is okozhat problémákat. Előfordulhat, hogy a háttérbeli készlet tagjaihoz tartozó teljes tartománynevet nem oldja meg megfelelően a felhasználó által konfigurált DNS-kiszolgáló a VNet.

### <a name="solution"></a>Megoldás

Ellenőrizze az NSG, a UDR és a DNS-konfigurációt az alábbi lépések végrehajtásával:

* Keresse meg az Application Gateway-alhálózathoz társított NSG. Győződjön meg arról, hogy a háttérbeli kommunikáció nincs letiltva.
* Keresse meg az Application Gateway-alhálózathoz társított UDR. Győződjön meg arról, hogy a UDR nem irányítja át a forgalmat a háttér-alhálózatból. Keressen például a hálózati virtuális berendezésekhez vagy a ExpressRoute/VPN-en keresztül az Application Gateway alhálózatra hirdetett alapértelmezett útvonalakhoz tartozó útválasztást.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* A háttérbeli virtuális géppel való hatékony NSG és útvonal ellenõrzése

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Vizsgálja meg az egyéni DNS jelenlétét a VNet. A DNS-t a kimenetben található VNet tulajdonságok részleteivel tekintheti meg.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Ha van ilyen, győződjön meg arról, hogy a DNS-kiszolgáló helyesen tudja feloldani a háttérbeli készlet tagjainak teljes tartománynevét.

## <a name="problems-with-default-health-probe"></a>Problémák az alapértelmezett állapot-mintavételsel

### <a name="cause"></a>Ok

502 a hibák olyan gyakori mutatók is lehetnek, amelyek az alapértelmezett állapot mintavétele nem éri el a háttérbeli virtuális gépeket.

Az Application Gateway-példányok üzembe helyezésekor automatikusan beállítja az alapértelmezett állapot-mintavételt az egyes BackendAddressPool a Backendhttpsetting értékre tulajdonságainak használatával. A mintavétel beállításához nincs szükség felhasználói beavatkozásra. Pontosabban, ha egy terheléselosztási szabályt konfiguráltak, a társítás egy Backendhttpsetting értékre és egy BackendAddressPool között történik. Mindegyik társításhoz alapértelmezett mintavétel van konfigurálva, és az Application Gateway egy rendszeres állapot-ellenőrzési kapcsolódást indít el a BackendAddressPool minden példányához a Backendhttpsetting értékre elemben megadott porton. 

A következő táblázat felsorolja az alapértelmezett állapot-mintavételhez társított értékeket:

| Mintavételi tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Mintavételi URL-cím |`http://127.0.0.1/` |URL-cím elérési útja |
| Időköz |30 |Mintavételi időköz (másodperc) |
| Időtúllépés |30 |Mintavétel időtúllépése másodpercben |
| Nem kifogástalan állapot küszöbértéke |3 |Újrapróbálkozások száma. A háttér-kiszolgáló az egymást követő mintavételi hibák számának elérésekor a nem megfelelő állapotú küszöbértéket éri el. |

### <a name="solution"></a>Megoldás

* Győződjön meg arról, hogy az alapértelmezett hely konfigurálva van, és figyeli a 127.0.0.1-t.
* Ha a Backendhttpsetting értékre a 80-től eltérő portot ad meg, az alapértelmezett helyet úgy kell konfigurálni, hogy figyelje a portot.
* A hívásnak az 200-es `http://127.0.0.1:port` http-eredmény kódját kell visszaadnia. Ezt vissza kell adni a 30 másodperces időkorlát alatt.
* Győződjön meg arról, hogy a konfigurált port meg van nyitva, és nincsenek tűzfalszabályok vagy Azure hálózati biztonsági csoportok, amelyek letiltják a bejövő vagy kimenő forgalmat a konfigurált porton.
* Ha a klasszikus Azure-beli virtuális gépeket vagy a felhőalapú szolgáltatást teljes tartománynévvel vagy nyilvános IP-címmel használja, győződjön meg arról, hogy a megfelelő [végpont](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) meg van nyitva.
* Ha a virtuális gép Azure Resource Manageron keresztül van konfigurálva, és azon a VNet kívül esik, amelyen az Application Gateway telepítve van, úgy kell konfigurálni egy [hálózati biztonsági csoportot](../virtual-network/network-security-groups-overview.md) , hogy engedélyezze a hozzáférést a kívánt porton.

## <a name="problems-with-custom-health-probe"></a>Problémák az egyéni állapotú mintavételsel

### <a name="cause"></a>Ok

Az egyéni állapotú tesztek további rugalmasságot tesznek lehetővé az alapértelmezett szondázás működéséhez. Ha egyéni mintavételt használ, beállíthatja a mintavételi időközt, az URL-címet, a teszt elérési útját, valamint azt, hogy hány sikertelen választ fogadjon el a háttérbeli készlet példányának nem megfelelőként való megjelölése előtt.

A következő további tulajdonságokat adja hozzá a rendszer:

| Mintavételi tulajdonság | Leírás |
| --- | --- |
| Név |A mintavétel neve. Ez a név szolgál a mintavételre a háttérbeli HTTP-beállításokban. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttérbeli HTTP-beállításokban definiált protokollt használja. |
| Gazdagép |A mintavétel elküldésére szolgáló állomásnév. Csak akkor alkalmazható, ha több hely van konfigurálva az Application gatewayben. Ez különbözik a virtuális gép gazdagépének nevétől. |
| Elérési út |A mintavétel relatív elérési útja. Az érvényes elérési út "/" karakterrel kezdődik. A mintavétel a \<protocol\> :// \<host\> :\<port\>\<path\> |
| Időköz |Mintavételi időköz másodpercben. Ez az időtartam két egymást követő mintavétel között. |
| Időtúllépés |Mintavétel időtúllépése másodpercben. Ha nem érkezik érvényes válasz ezen az időkorláton belül, a mintavétel sikertelenként van megjelölve. |
| Nem kifogástalan állapot küszöbértéke |Újrapróbálkozások száma. A háttér-kiszolgáló az egymást követő mintavételi hibák számának elérésekor a nem megfelelő állapotú küszöbértéket éri el. |

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy az egyéni állapot mintavétele megfelelően van-e konfigurálva az előző táblázatban. Az előző hibaelhárítási lépések mellett a következőket is ellenőrizze:

* Győződjön meg arról, hogy a mintavétel megfelelően van megadva az [útmutató](application-gateway-create-probe-ps.md)alapján.
* Ha az Application Gateway egyetlen helyhez van konfigurálva, alapértelmezés szerint az állomásnevet kell megadni `127.0.0.1` , kivéve, ha az egyéni mintavétel másként van konfigurálva.
* Győződjön meg arról, hogy a http://hívása \<host\> : \<port\> \<path\> egy 200-es http-eredmény kódját adja vissza.
* Győződjön meg arról, hogy az intervallum, az időkorlát és a UnhealtyThreshold az elfogadható tartományokon belül vannak.
* HTTPS-mintavétel használata esetén győződjön meg arról, hogy a háttér-kiszolgáló nem igényel SNI egy tartalék tanúsítványnak a háttér-kiszolgálón való konfigurálásával.

## <a name="request-time-out"></a>Kérelem időkorlátja

### <a name="cause"></a>Ok

Felhasználói kérés fogadásakor az Application Gateway alkalmazza a konfigurált szabályokat a kérelemre, és átirányítja azt egy háttérbeli készlet-példányra. Megvárja, amíg a háttérbeli példány válasza konfigurálható időintervallumot vár. Alapértelmezés szerint ez az intervallum **20** másodperc. Ha az Application Gateway ebben az intervallumban nem kap választ a háttérbeli alkalmazástól, a felhasználói kérelem 502-as hibát kap.

### <a name="solution"></a>Megoldás

Application Gateway lehetővé teszi a beállítás konfigurálását a Backendhttpsetting értékre keresztül, amelyet aztán különböző készletekre alkalmazhat. A különböző háttérbeli készletek különböző Backendhttpsetting értékre rendelkezhetnek, és egy másik kérelem időtúllépése is be van állítva.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Üres BackendAddressPool

### <a name="cause"></a>Ok

Ha az Application Gateway nem rendelkezik a háttér-címkészletet konfigurált virtuális gépekkel vagy virtuálisgép-méretezési csoporttal, nem tud átirányítani semmilyen ügyfél-kérelmet, és hibás átjárót küld.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a háttérbeli címkészlet nem üres. Ezt a PowerShell, a CLI vagy a Portal használatával teheti meg.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Az előző parancsmag kimenetének tartalmaznia kell nem üres háttér-címkészletet. Az alábbi példa két, visszaadott készletet mutat be, amelyek a háttérbeli virtuális gépek teljes tartománynevével vagy IP-címével vannak konfigurálva. A BackendAddressPool kiépítési állapotának "sikeres" értékűnek kell lennie.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Nem megfelelő állapotú példányok a BackendAddressPool

### <a name="cause"></a>Ok

Ha a BackendAddressPool összes példánya nem kifogástalan állapotú, akkor az Application Gateway nem rendelkezik a felhasználói kérések továbbítására szolgáló háttér-végponttal. Ez abban az esetben is lehetséges, ha a háttérbeli példányok állapota Kifogástalan, de nincs telepítve a szükséges alkalmazás.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy a példányok állapota Kifogástalan-e, és hogy az alkalmazás megfelelően van-e konfigurálva. Ellenőrizze, hogy a háttérbeli példányok tudnak-e válaszolni a pingelésre egy másik virtuális gépről ugyanabban a VNet. Ha nyilvános végponttal van konfigurálva, győződjön meg arról, hogy a webalkalmazáshoz tartozó böngésző-kérelem szervizelhető.

## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).