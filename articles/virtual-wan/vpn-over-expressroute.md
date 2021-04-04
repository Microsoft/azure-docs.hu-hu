---
title: 'ExpressRoute-titkosítás konfigurálása: IPsec a ExpressRoute-en keresztül az Azure Virtual WAN-hoz'
description: Ismerje meg, hogyan hozhat létre helyek közötti VPN-kapcsolatot az Azure Virtual WAN használatával a ExpressRoute privát kapcsolatain keresztül.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487507"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-titkosítás: IPsec a ExpressRoute-en keresztül a virtuális WAN-hoz

Ez a cikk bemutatja, hogyan hozhat létre IPsec/IKE VPN-kapcsolatot a helyszíni hálózatról az Azure-hoz az Azure Virtual WAN használatával az Azure ExpressRoute-áramkör privát összevonásával. Ezzel a technikával a helyszíni hálózatok és az Azure-beli virtuális hálózatok között titkosított átvitelt biztosíthat a ExpressRoute-en keresztül anélkül, hogy nyilvános vagy nyilvános IP-címeket kellene használnia.

## <a name="topology-and-routing"></a>Topológia és Útválasztás

Az alábbi ábrán egy példa látható a VPN-kapcsolatra a ExpressRoute privát társításával:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN over ExpressRoute":::

Az ábrán az Azure hub VPN Gateway-hez csatlakoztatott helyszíni hálózaton belüli hálózat látható a ExpressRoute-alapú privát kapcsolaton keresztül. A kapcsolat létrehozása egyszerű:

1. Hozzon létre ExpressRoute kapcsolatot egy ExpressRoute-áramkörrel és egy privát partneri kapcsolattal.
2. Hozza létre a VPN-kapcsolatot a jelen cikkben leírtak szerint.

Ennek a konfigurációnak a fontos aspektusa a helyszíni hálózatok és az Azure közötti útválasztás a ExpressRoute és a VPN elérési útjain keresztül.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Helyszíni hálózatokból az Azure-ba irányuló forgalom

A helyszíni hálózatokból az Azure-ba irányuló forgalom esetében az Azure-előtagok (beleértve a virtuális hubot és az elosztóhoz csatlakozó összes küllős virtuális hálózatot) a ExpressRoute privát és a VPN BGP használatával is meghirdetik. Ennek eredményeképpen két hálózati útvonal (elérési út) van az Azure felé a helyszíni hálózatokból:

- Egy az IPsec által védett elérési úton
- Egy közvetlenül az IPSec-védelem *nélküli* ExpressRoute 

Ha titkosítást szeretne alkalmazni a kommunikációra, meg kell győződnie arról, hogy a diagramon a VPN-kapcsolattal rendelkező hálózat számára a helyszíni VPN-átjárón keresztüli Azure-útvonalak előnyben részesítettek a közvetlen ExpressRoute útvonalon.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Az Azure-ból a helyszíni hálózatokra irányuló forgalom

Ugyanez a követelmény vonatkozik az Azure-ból a helyszíni hálózatokra érkező forgalomra is. Ha biztosítani szeretné, hogy az IPsec elérési útja előnyben legyen a közvetlen ExpressRoute útvonalon (IPsec nélkül), két lehetőség közül választhat:

- A VPN-kapcsolattal rendelkező hálózathoz tartozó VPN BGP-munkamenetben konkrétabb előtagokat Hirdessen. Meghirdetheti a VPN-kapcsolattal rendelkező hálózatot a ExpressRoute privát kapcsolaton keresztül, majd a VPN BGP-munkamenetben meghatározott tartományokat is. Például Hirdessen 10.0.0.0/16 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

- A VPN-és ExpressRoute különálló előtagjainak reklámozása. Ha a VPN-kapcsolattal rendelkező hálózati tartományok más ExpressRoute csatlakoztatott hálózatokból vannak kiválasztva, akkor a VPN-és ExpressRoute BGP-munkamenetekben meghirdetheti az előtagokat. Például Hirdessen 10.0.0.0/24 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

Mindkét példában az Azure a VPN-kapcsolaton keresztül küldi el a forgalmat a 10.0.1.0/24-re, nem pedig közvetlenül a VPN-védelem nélküli ExpressRoute.

> [!WARNING]
> Ha *ugyanazokat* az előtagokat hirdeti meg mind a ExpressRoute, mind a VPN-kapcsolaton keresztül, az Azure közvetlenül a VPN-védelem nélkül fogja használni a ExpressRoute útvonalat.
>

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. virtuális WAN és hub létrehozása átjárókkal

A folytatás előtt a következő Azure-erőforrásokat és a megfelelő helyszíni konfigurációkat kell megadnia:

- Azure-beli virtuális WAN
- Egy [ExpressRoute-átjáróval](virtual-wan-expressroute-portal.md) és egy [VPN-átjáróval](virtual-wan-site-to-site-portal.md) rendelkező virtuális WAN-központ

Az Azure-beli virtuális WAN és egy ExpressRoute társítással rendelkező központ létrehozásához szükséges lépésekért lásd: [ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md). A VPN Gateway virtuális WAN-beli létrehozásához szükséges lépéseket lásd: [helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. hely létrehozása a helyszíni hálózathoz

A hely erőforrása megegyezik a virtuális WAN-hoz nem ExpressRoute VPN-helyekkel. A helyszíni VPN-eszköz IP-címe mostantól lehet privát IP-cím, vagy a helyszíni hálózaton elérhető nyilvános IP-cím az 1. lépésben létrehozott ExpressRoute-alapú privát társon keresztül érhető el.

> [!NOTE]
> A helyszíni VPN *-eszköz IP* -címének részét kell képeznie a virtuális WAN-hubhoz az Azure ExpressRoute privát kapcsolaton keresztül meghirdetett cím előtagjainak.
>

1. Nyissa meg a Azure Portalt a böngészőben. 
1. Válassza ki a létrehozott hubot. A virtuális WAN-központ lap **kapcsolat** területén válassza a **VPN-helyek** lehetőséget.
1. A **VPN-helyek** lapon válassza a **+ hely létrehozása** lehetőséget.
1. A **Hely létrehozása** lapon töltse ki a következő mezőket:
   * **Előfizetés**: Ellenőrizze az előfizetést.
   * **Erőforráscsoport**: válassza ki vagy hozza létre a használni kívánt erőforráscsoportot.
   * **Régió**: adja meg a VPN-hely erőforrásához tartozó Azure-régiót.
   * **Név**: adja meg azt a nevet, amellyel a helyszíni helyre hivatkozni kíván.
   * **Eszköz szállítója**: adja meg a helyszíni VPN-eszköz szállítóját.
   * **Border Gateway Protocol**: válassza az "engedélyezés" lehetőséget, ha a helyszíni hálózat BGP-t használ.
   * **Magánhálózati címterület**: adja meg a helyszíni helyen található IP-címtartományt. A Címterület felé irányuló forgalom a VPN-átjárón keresztül lesz átirányítva a helyszíni hálózatra.
   * **Hubok**: válasszon ki egy vagy több hubokat a VPN-hely összekapcsolásához. A kiválasztott huboknak már létre kell hozniuk a VPN-átjárókat.
1. Válassza a **Tovább: hivatkozások >** a VPN-kapcsolat beállításaihoz:
   * **Hivatkozás neve**: az a név, amellyel kapcsolatban hivatkozni kíván erre a kapcsolatra.
   * **Szolgáltató neve**: a webhely internetszolgáltatójának neve. ExpressRoute helyszíni hálózat esetén ez a ExpressRoute-szolgáltató neve.
   * **Speed (sebesség**): az Internet Service-hivatkozás vagy a ExpressRoute áramkör sebessége.
   * **IP-cím**: a helyszíni helyen található VPN-eszköz nyilvános IP-címe. A helyszíni ExpressRoute pedig a VPN-eszköz magánhálózati IP-címe a ExpressRoute használatával.

   Ha a BGP engedélyezve van, akkor az az Azure-ban a helyhez létrehozott összes kapcsolatra érvényes lesz. A BGP virtuális WAN-on való konfigurálása egyenértékű a BGP Azure-beli VPN-átjárón való konfigurálásával. 
   
   A helyszíni BGP-társ címe *nem* egyezhet meg a VPN-hely IP-címével vagy a VPN-hely virtuális hálózati címével. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Azonban *nem* lehet APIPA (169,254).*x*. *x*) címe. Adja meg ezt a címeket a helyet jelölő megfelelő VPN-helyen. A BGP-előfeltételeket lásd: [a BGP és az Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Válassza a **Next (tovább): Áttekintés + >létrehozása** elemet a beállítási értékek ellenőrzéséhez és a VPN-hely létrehozásához. Ha a csatlakozáshoz a **hubok** lehetőséget választotta, a kapcsolat létrejön a helyszíni hálózat és a hub VPN-átjáró között.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. frissítse a VPN-kapcsolat beállítását a ExpressRoute használatára

Miután létrehozta a VPN-helyet, és csatlakozik a központhoz, a következő lépésekkel konfigurálhatja a kapcsolatot a ExpressRoute privát társítás használatára:

1. Lépjen vissza a virtuális WAN-erőforrás lapra, és válassza ki a hub-erőforrást. Vagy navigáljon a VPN-helyről a csatlakoztatott hubhoz.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Válasszon hubot":::
1. A **kapcsolat** területen válassza a **VPN (helyek közötti)** lehetőséget.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="Válassza a VPN (helyek közötti) lehetőséget.":::
1. A VPN-helyen válassza a három pontot (**..**.) a ExpressRoute-on keresztül, majd válassza a **VPN-kapcsolat szerkesztése ehhez a hubhoz** lehetőséget.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Konfigurációs menü megadása":::
1. Az **Azure Private IP-cím használatához** válassza az **Igen** lehetőséget. A beállítás úgy konfigurálja a hub VPN-átjárót, hogy a nyilvános IP-címek helyett magánhálózati IP-címeket használjon a hub-címtartomány ezen a kapcsolaton belül. Ezzel biztosíthatja, hogy a helyszíni hálózatról érkező forgalom a nyilvános Internet helyett a VPN-kapcsolaton keresztül áthaladjon a ExpressRoute privát elágazási útjain. A következő képernyőképen a beállítás látható:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Beállítás a VPN-kapcsolat magánhálózati IP-címének használatára" border="false":::
1. Kattintson a **Mentés** gombra.

A módosítások mentése után a hub VPN-átjáró a VPN-átjáró magánhálózati IP-címeivel hozza létre az IPsec/IKE-kapcsolatokat a helyszíni VPN-eszközzel a ExpressRoute-en keresztül.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. a hub VPN-átjáró magánhálózati IP-címeinek lekérése

Töltse le a VPN-eszköz konfigurációját a hub VPN-átjáró magánhálózati IP-címeinek lekéréséhez. Ezekre a címekre a helyszíni VPN-eszköz konfigurálásához van szükség.

1. A hub lapján válassza a **VPN (helyek közötti)** lehetőséget a **kapcsolat** alatt.
1. Az **Áttekintés** lap tetején válassza a **VPN-konfiguráció letöltése** lehetőséget. 

   Az Azure létrehoz egy Storage-fiókot a "Microsoft-Network-[Location]" erőforráscsoporthoz, ahol a *hely* a WAN helye. Miután alkalmazza a konfigurációt a VPN-eszközökre, törölheti ezt a Storage-fiókot.
1. A fájl létrehozása után kattintson a hivatkozásra a letöltéshez.
1. Alkalmazza a konfigurációt a VPN-eszközre.

### <a name="vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájlja

Az eszköz konfigurációs fájlja a helyszíni VPN-eszköz konfigurálásakor használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration**: Ez a szakasz azt jelzi, hogy az eszköz adatai a virtuális WAN-hoz csatlakozó helyként vannak beállítva. Magában foglalja a fiókirodai eszköz nevét és nyilvános IP-címét.
* **vpnSiteConnections**: Ez a szakasz a következő beállításokkal kapcsolatos információkat tartalmazza:

    * A virtuális központ virtuális hálózatának címterület.<br/>Példa:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Az elosztóhoz csatlakozó virtuális hálózatok címterület.<br>Példa:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * A virtuális központ VPN-átjárójának IP-címei. Mivel a VPN-átjáró minden kapcsolata két alagútból áll az aktív-aktív konfigurációban, a fájlban felsorolt IP-címeket is látni fogja. Ebben a példában az `Instance0` `Instance1` egyes helyekhez és a nyilvános IP-címek helyett magánhálózati IP-címekre van szükség.<br>Példa:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * A VPN Gateway-kapcsolat konfigurációs adatai, például a BGP és az előmegosztott kulcs. Az előmegosztott kulcs automatikusan létrejön. Bármikor szerkesztheti a kapcsolatokat az **Áttekintés** oldalon egy egyéni előmegosztott kulcshoz.
  
### <a name="example-device-configuration-file"></a>Eszközkonfigurációs példafájl

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>VPN-eszköz konfigurálása

Amennyiben útmutatásra van szüksége az eszköz konfigurálásához, használhatja a [VPN-eszközkonfigurációs szkriptek lapon](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) található utasításokat az alábbi kikötésekkel:

* A VPN-eszköz lapon lévő utasítások nem íródnak virtuális WAN-hoz. A VPN-eszköz manuális konfigurálásához azonban a konfigurációs fájl virtuális WAN-értékeit is használhatja. 
* A VPN-átjáróhoz tartozó letölthető eszköz konfigurációs parancsfájljai nem működnek a virtuális WAN esetében, mert a konfiguráció eltérő.
* Az új virtuális WAN A IKEv1 és a IKEv2 is támogatja.
* A virtuális WAN csak Route-alapú VPN-eszközöket és eszköz-utasításokat használhat.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. a virtuális WAN megtekintése

1. Nyissa meg a virtuális WAN-t.
1. Az **Áttekintés** oldalon a térképen lévő minden pont egy hubot jelöl.
1. A **hubok és kapcsolatok** szakaszban megtekintheti a hub-, hely-, régió-és VPN-kapcsolat állapotát. A bájtok be-és kifelé is megtekinthetők.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. a kapcsolatok figyelése

Hozzon létre egy kapcsolatot az Azure-beli virtuális gép (VM) és egy távoli hely közötti kommunikáció figyeléséhez. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrás mező a virtuális gép IP-címe az Azure-ban, a cél IP pedig a hely IP-címe.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. erőforrások tisztítása

Ha már nincs szüksége ezekre az erőforrásokra, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a benne található összes erőforrást. Futtassa a következő PowerShell-parancsot, és cserélje le az `myResourceGroup` erőforráscsoport nevére:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatja, hogyan hozhat létre VPN-kapcsolatot a ExpressRoute magánhálózati kapcsolaton keresztül a virtuális WAN használatával. Ha többet szeretne megtudni a virtuális WAN-ról és a kapcsolódó szolgáltatásokról, tekintse meg a [virtuális WAN áttekintése](virtual-wan-about.md)című témakört.
