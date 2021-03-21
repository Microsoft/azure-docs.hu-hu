---
title: Azure Application Gateway infrastruktúra-konfiguráció
description: Ez a cikk az Azure Application Gateway-infrastruktúra konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397620"
---
# <a name="application-gateway-infrastructure-configuration"></a>Infrastruktúra-konfiguráció Application Gateway

Az Application Gateway-infrastruktúra magában foglalja a virtuális hálózatot, az alhálózatokat, a hálózati biztonsági csoportokat és a felhasználó által megadott útvonalakat.

## <a name="virtual-network-and-dedicated-subnet"></a>Virtuális hálózat és dedikált alhálózat

Az Application Gateway egy dedikált üzembe helyezés a virtuális hálózaton. A virtuális hálózaton belül egy dedikált alhálózat szükséges az Application Gateway számára. Egy adott Application Gateway-telepítés több példánya is lehet egy alhálózaton. Az alhálózaton más Application Gateway-átjárókat is üzembe helyezhet. Más erőforrás azonban nem helyezhető üzembe az Application Gateway alhálózatban. Standard_v2 és standard Azure-Application Gateway nem keverhető ugyanazon az alhálózaton.

> [!NOTE]
> A [virtuális hálózati szolgáltatás végponti házirendjei](../virtual-network/virtual-network-service-endpoint-policies-overview.md) jelenleg nem támogatottak Application Gateway alhálózatban.

### <a name="size-of-the-subnet"></a>Az alhálózat mérete

A Application Gateway egy magánhálózati IP-címet és egy másik magánhálózati IP-címet használ, ha a magánhálózati előtéri IP-cím konfigurálva van.

Az Azure az egyes alhálózatokban öt IP-címet is fenntart belső használatra: az első négyet és az utolsó IP-címet. Vegyünk például 15 Application Gateway-példányt, amelyek nem rendelkeznek privát előtéri IP-címmel. Ehhez az alhálózathoz legalább 20 IP-címnek kell lennie: öt belső használatra és 15 az Application Gateway-példányokhoz.

Vegyünk egy olyan alhálózatot, amelyben 27 Application Gateway-példány és egy magánhálózati előtér-IP-cím IP-címe található. Ebben az esetben 33 IP-címre van szükség az Application Gateway példányaihoz, egyet a privát kezelőfelülethez, és öt belső használatra.

A Application Gateway (standard vagy WAF) SKU legfeljebb 32 példányt tud támogatni (32 példány IP-címe + 1 privát előtér-IP + 5 Azure számára fenntartott) – Ezért az alhálózat minimális mérete/26 ajánlott

Application Gateway (Standard_v2 vagy WAF_v2 SKU) legfeljebb 125 példányt tud támogatni (125 példány IP-címe + 1 privát előtér-IP + 5 Azure számára fenntartott) – Ezért az alhálózat minimális mérete/24 ajánlott

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Application Gateway a hálózati biztonsági csoportok (NSG-EK) támogatottak. Vannak azonban korlátozások:

- Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534 **-as TCP** -portokon a Application Gateway v1 SKU-hoz, valamint a 65200-65535-es TCP-portokat a v2 SKU-hoz a célként megadott alhálózattal a **GatewayManager** szolgáltatás címkéjének megfelelően. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A külső entitások, beleértve az átjárók ügyfeleit, nem tudnak kommunikálni ezeken a végpontokon.

- A kimenő internetkapcsolatot nem lehet blokkolni. A NSG alapértelmezett kimenő szabályai lehetővé teszik az internetkapcsolatot. A következő megoldást javasoljuk:

  - Ne távolítsa el az alapértelmezett kimenő szabályokat.
  - Ne hozzon létre más kimenő szabályokat, amelyek megtagadják a kimenő kapcsolatokat.

- A **AzureLoadBalancer** címke és a célként megadott alhálózat közötti forgalomnak engedélyezettnek **kell** lennie.

### <a name="allow-access-to-a-few-source-ips"></a>Hozzáférés engedélyezése néhány forrás IP-címeihez

Ebben a forgatókönyvben a Application Gateway alhálózat NSG használja. A következő korlátozásokat helyezze az alhálózatra az adott prioritási sorrendben:

1. Engedélyezi a bejövő forgalmat egy forrás IP-címről vagy IP-tartományból a célhelyként a teljes Application Gateway alhálózati címtartomány és célportként a bejövő hozzáférési portként, például a HTTP-hozzáférés 80-es portját.
2. A forrástól érkező, a **GatewayManager** szolgáltatásként használt és a célként megadott 65503-65534 portokként való bejövő kérések engedélyezése a Application Gateway v1 SKU esetében, valamint a **65200-65535-es** port a v2 SKU-hoz a [háttér állapotának kommunikációja](./application-gateway-diagnostics.md)érdekében. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A megfelelő tanúsítványok nélkül a külső entitások nem indíthatnak módosításokat ezeken a végpontokon.
3. A bejövő Azure Load Balancer mintavételek (*AzureLoadBalancer* -címkék) és a bejövő virtuális hálózati forgalom (*VirtualNetwork* címke) engedélyezése a [hálózati biztonsági csoporton](../virtual-network/network-security-groups-overview.md).
4. Az összes többi bejövő forgalom blokkolása megtagadás – minden szabály használatával.
5. Az internetre irányuló kimenő adatforgalom engedélyezése az összes célhelyre.

## <a name="supported-user-defined-routes"></a>Támogatott, felhasználó által definiált útvonalak 

> [!IMPORTANT]
> Ha a UDR-t használja az Application Gateway alhálózaton, a [háttér állapot nézet](./application-gateway-diagnostics.md#back-end-health) állapota **ismeretlen** lehet. Azt is okozhatja, hogy Application Gateway naplók és mérőszámok generálása meghiúsul. Azt javasoljuk, hogy ne használja a UDR a Application Gateway alhálózaton, így megtekintheti a háttér állapotát, a naplókat és a metrikákat.

- **v1**

   A v1 SKU esetében a felhasználó által megadott útvonalak (UDR-EK) a Application Gateway alhálózat esetében támogatottak, feltéve, hogy nem változtatják meg a végpontok közötti kérés/válasz kommunikációt. Beállíthat például egy UDR a Application Gateway alhálózatban úgy, hogy egy tűzfal-berendezésre mutasson a csomagok ellenőrzéséhez. Azonban győződjön meg arról, hogy a csomag a vizsgálat után is elérheti a kívánt célját. Ennek elmulasztása helytelen állapot-mintavételi vagy forgalom-útválasztási viselkedést eredményezhet. Ebbe beletartoznak a megtanult útvonalak vagy az alapértelmezett 0.0.0.0/0 útvonalak, amelyeket a virtuális hálózat Azure ExpressRoute vagy VPN-átjárói továbbítanak. Ha a rendszer a 0.0.0.0/0 helyszíni (kényszerített bújtatást használó) átirányítását végzi, a v1 nem támogatja a helyzetet.

- **v2**

   A v2 SKU esetében támogatott és nem támogatott helyzetek vannak:

   **v2 támogatott forgatókönyvek**
   > [!WARNING]
   > Az útválasztási táblázat nem megfelelő konfigurációja aszimmetrikus útválasztást eredményezhet Application Gateway v2-ben. Győződjön meg arról, hogy az összes felügyeleti/vezérlési sík forgalmát közvetlenül az internethez küldik, és nem a virtuális berendezésen keresztül. A naplózást és a metrikákat is érintheti.


  **1. forgatókönyv**: a BORDER Gateway Protocol (BGP) útvonal-propagálás letiltása az Application Gateway alhálózaton UDR

   Előfordul, hogy az alapértelmezett átjáró útvonala (0.0.0.0/0) a Application Gateway virtuális hálózathoz társított ExpressRoute vagy VPN-átjárón keresztül kerül meghirdetésre. Ez megszakítja a felügyeleti sík forgalmát, ami közvetlen elérési utat igényel az internethez. Ilyen esetekben a BGP-útvonalak propagálásának letiltására UDR használható. 

   A BGP-útvonalak propagálásának letiltásához kövesse az alábbi lépéseket:

   1. Útválasztási tábla erőforrásának létrehozása az Azure-ban.
   2. Tiltsa le a **virtuális hálózati átjáró útvonal-propagálási** paraméterét. 
   3. Rendelje hozzá az útválasztási táblázatot a megfelelő alhálózathoz. 

   Ennek a forgatókönyvnek a UDR engedélyezésével nem kell megszüntetnie a meglévő beállításokat.

  **2. forgatókönyv**: UDR a 0.0.0.0/0 és az internet között

   Létrehozhat egy UDR, amely a 0.0.0.0/0 forgalmat közvetlenül az internetre küldi. 

  **3. forgatókönyv**: az Azure Kubernetes szolgáltatás UDR az kubenet-mel

  Ha a kubenet-t az Azure Kubernetes szolgáltatással (ak) és a Application Gateway beáramló vezérlővel (AGIC) használja, szüksége lesz egy útválasztási táblázatra, amely lehetővé teszi, hogy a Application Gateway hüvelyek számára továbbított forgalom a megfelelő csomópontra legyen irányítva. Ez az Azure CNI használata esetén nem szükséges. 

  Az alábbi lépésekkel engedélyezheti az útválasztási táblázat használatát a kubenet működésének engedélyezéséhez:

  1. Ugrás az AK által létrehozott erőforráscsoporthoz (az erőforráscsoport nevének a "MC_" kifejezéssel kell kezdődnie)
  2. Keresse meg az AK által létrehozott útválasztási táblázatot az adott erőforráscsoporthoz. Az útválasztási táblázatot a következő információk alapján kell kitölteni:
     - A cím előtagjának az AK-ban elérni kívánt hüvelyek IP-tartományának kell lennie. 
     - A következő ugrás típusa csak virtuális készülék lehet. 
     - A következő ugrási címnek a hüvelyt működtető csomópont IP-címének kell lennie.
  3. Az útválasztási táblázat hozzárendelése a Application Gateway alhálózathoz. 
    
  **v2 nem támogatott forgatókönyvek**

  **1. forgatókönyv**: virtuális berendezések UDR

  Minden olyan szituációban, ahol a 0.0.0.0/0 át kell irányítani a virtuális berendezéseken keresztül, a hub/küllős virtuális hálózat vagy a helyszíni (kényszerített bújtatás) nem támogatott a v2-ben.

## <a name="next-steps"></a>Következő lépések

- [Az előtér-IP-cím konfigurációjának megismerése](configuration-front-end-ip.md).