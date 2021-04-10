---
title: Oktatóanyag – hálózati tervezési ellenőrzőlista
description: Ismerje meg a hálózati kapcsolat és a hálózati portok hálózati követelményeit az Azure VMware megoldásban.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 8cee5fa24aab8bd7fe6a9527f9c8e7cdff997511
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462065"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Hálózatkezelési tervezési ellenőrzőlista az Azure VMware-megoldáshoz 

Az Azure VMware-megoldás egy VMware Private Cloud Environment-környezetet kínál a felhasználók és alkalmazások számára a helyszíni és az Azure-alapú környezetekben vagy erőforrásokban. A kapcsolatot hálózati szolgáltatásokon keresztül, például az Azure ExpressRoute és a VPN-kapcsolatokon keresztül továbbítjuk. A szolgáltatások engedélyezéséhez meghatározott hálózati címtartományok és tűzfal-portok szükségesek. Ez a cikk ismerteti a hálózatkezelés konfigurálásához szükséges információkat, hogy megfelelően működjön az Azure VMware-megoldással.

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * A Virtual Network és a ExpressRoute Circuit szempontjai
> * Útválasztási és alhálózati követelmények
> * A szolgáltatásokkal való kommunikációhoz szükséges hálózati portok
> * DHCP-és DNS-megfontolások az Azure VMware-megoldásban

## <a name="prerequisite"></a>Előfeltétel
Győződjön meg arról, hogy minden átjáró, beleértve a ExpressRoute-szolgáltató szolgáltatását, támogatja a 4 bájtos autonóm rendszer számát (ASN). Az Azure VMware-megoldás 4 bájtos nyilvános ASN használ a hirdetési útvonalakhoz.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>A Virtual Network és a ExpressRoute Circuit szempontjai
Amikor létrehoz egy virtuális hálózati kapcsolatot az előfizetésében, a rendszer a ExpressRoute áramkört a társításon keresztül hozza létre, egy engedélyezési kulcsot használ, és egy, a Azure Portal által igényelt társ-azonosítót. A társítás egy privát, egy-az-egyhez kapcsolat a privát felhő és a virtuális hálózat között.

> [!NOTE] 
> A ExpressRoute áramkör nem része a felhőalapú telepítésnek. A helyszíni ExpressRoute áramkör a jelen dokumentum hatókörén kívül esik. Ha helyszíni kapcsolatra van szüksége a saját felhőhöz, használhat egy meglévő ExpressRoute-áramkört, vagy vásárolhat egyet a Azure Portal.

Privát felhő telepítésekor a vCenter és a NSX-T kezelő IP-címeit kapja meg. Ezen felügyeleti felületek eléréséhez további erőforrásokat kell létrehoznia az előfizetés virtuális hálózatában. Ezen erőforrások létrehozásához és [ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) létrehozásához szükséges eljárásokat az oktatóanyagokban találja.

A privát felhőalapú logikai hálózat előre kiépített NSX-T tartalmaz. Az Ön számára előre kiépített 0. rétegbeli átjáró és 1. rétegbeli átjáró. Létrehozhat egy szegmenst, és csatolhatja azt a meglévő 1. rétegbeli átjáróhoz, vagy csatolhatja azt egy Ön által definiált új 1. rétegbeli átjáróhoz. A NSX-T logikai hálózati összetevők East-West kapcsolatot biztosítanak a munkaterhelések között, és North-South kapcsolatot biztosítanak az internettel és az Azure-szolgáltatásokkal.

## <a name="routing-and-subnet-considerations"></a>Útválasztás és alhálózat szempontjai
Az Azure VMware-megoldás privát felhője Azure ExpressRoute-kapcsolaton keresztül csatlakozik az Azure-beli virtuális hálózathoz. Ez a nagy sávszélességű, kis késleltetésű kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó szolgáltatásokhoz a saját felhőalapú környezetében. Az Útválasztás Border Gateway Protocol (BGP) alapul, automatikusan kiépítve, és alapértelmezés szerint engedélyezve van minden egyes privát Felhőbeli telepítéshez. 

Az Azure VMware-megoldás privát felhőkhöz legalább egy `/22` CIDR hálózati címfordító szükséges a lent látható alhálózatokhoz. Ez a hálózat kiegészíti a helyszíni hálózatokat. A Címterület nem fedi át az előfizetésben és a helyszíni hálózatokban lévő más virtuális hálózatokban használt címeket. Ezen a címen a blokkon belül a felügyelet, a kiépítés és a vMotion hálózat automatikusan kiépítve lesz.

>[!NOTE]
>A Címterület engedélyezett tartományai az RFC 1918 Private címterület (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), kivéve a 172.17.0.0/16.

Példa a `/22` CIDR hálózati címterület:  `10.10.0.0/22`

Az alhálózatok:

| Hálózati forgalom             | Alhálózat | Példa          |
| ------------------------- | ------ | ---------------- |
| Magánfelhő-felügyelet  | `/26`  | `10.10.0.0/26`   |
| HCX mgmt-Migrálás       | `/26`  | `10.10.0.64/26`  |
| Global Reach fenntartva     | `/26`  | `10.10.0.128/26` |
| ExpressRoute fenntartva     | `/27`  | `10.10.0.192/27` |
| ExpressRoute-társítás      | `/27`  | `10.10.0.224/27` |
| ESXi-kezelés           | `/25`  | `10.10.1.0/25`   |
| vMotion hálózat           | `/25`  | `10.10.1.128/25` |
| Replikációs hálózat       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX kimenő                | `/26`  | `10.10.3.0/26`   |
| Fenntartva                  | `/26`  | `10.10.3.64/26`  |
| Fenntartva                  | `/26`  | `10.10.3.128/26` |
| Fenntartva                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Szükséges hálózati portok

| Forrás | Cél | Protokoll | Port | Leírás  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Saját Felhőbeli DNS-kiszolgáló | Helyszíni DNS-kiszolgáló | UDP | 53 | DNS-ügyfél – a számítógépek vCenter érkező, a helyszíni DNS-lekérdezésekre vonatkozó kérések (lásd az alábbi DNS-szakaszt) |  
| Helyszíni DNS-kiszolgáló   | Saját Felhőbeli DNS-kiszolgáló | UDP | 53 | DNS-ügyfelek – helyszíni szolgáltatásokból származó, saját Felhőbeli DNS-kiszolgálókra irányuló kérések (a DNS-t lásd alább) |  
| Helyszíni hálózat  | Saját felhőalapú vCenter-kiszolgáló  | TCP (HTTP)  | 80 | vCenter Server a közvetlen HTTP-kapcsolatokhoz a 80-es portot igényli. A 80-es port átirányítja a kérelmeket a 443-es HTTPS-portra. Ez az átirányítás segít, ha `http://server` a helyett a-t használja `https://server` .  <br><br>WS-Management (az 443-es portot is meg kell nyitni) <br><br>Ha egyéni Microsoft SQL Database-adatbázist használ, és nem a csomagban lévő SQL Server 2008-adatbázist a vCenter Server, akkor a SQL Reporting-szolgáltatások a 80-es portot használják. A vCenter Server telepítésekor a telepítő felszólítja, hogy módosítsa a vCenter Server HTTP-portját. A sikeres telepítés érdekében módosítsa a vCenter Server HTTP-portot egy egyéni értékre. A Microsoft Internet Information Services (IIS) a 80-es portot is használja. Lásd: az vCenter Server és az IIS közötti ütközés az 80-as porton. |  
| Saját felhőalapú felügyeleti hálózat | Helyszíni Active Directory  | TCP  | 389 | Ennek a portnak nyitva kell lennie a helyi és a vCenter Server összes távoli példányán. Ez a port az vCenter Server-csoport címtárszolgáltatás-szolgáltatásainak LDAP-portszáma. A vCenter Server rendszernek az 389-es porthoz kell kötnie, még akkor is, ha nem csatlakozik ehhez a vCenter Server-példányhoz egy csatolt módú csoporthoz. Ha egy másik szolgáltatás fut ezen a porton, érdemes lehet eltávolítani, vagy egy másik portra módosítani a portot. Az LDAP szolgáltatást a 1025 és 65535 közötti bármely porton futtathatja.  Ha ez a példány Microsoft Windows Active Directoryként szolgál, módosítsa az 389-es portszámot a 1025 és 65535 közötti rendelkezésre álló portra. Ez a port nem kötelező – a helyszíni AD identitás-forrásként való konfigurálásához a privát felhő vCenter. |  
| Helyszíni hálózat  | Saját felhőalapú vCenter-kiszolgáló  | TCP (HTTPS)  | 443 | Ez a port lehetővé teszi a vCenter elérését egy helyszíni hálózatról. Az a vCenter Server rendszer által használt alapértelmezett port, amellyel figyeli a vSphere-ügyfél kapcsolatait. Ha engedélyezni szeretné a vCenter Server rendszer számára a vSphere-ügyféltől érkező adatok fogadását, nyissa meg a tűzfal 443-as portját. A vCenter Server rendszer az 443-es portot is használja az SDK-ügyfelekről érkező adatátvitel figyelésére. Ez a port a következő szolgáltatásokhoz is használatos: WS-Management (az 80-es portot is meg kell nyitni). vSphere-ügyfél hozzáférése a vSphere Update Managerhez. Harmadik féltől származó hálózatkezelési ügyfélkapcsolatok vCenter Serverhoz. A harmadik féltől származó hálózatkezelési ügyfelek hozzáférést biztosítanak a gazdagépekhez. |  
| Webböngésző  | Hibrid Cloud Manager  | TCP (HTTPS) | 9443 | Hibrid Cloud Manager virtuálisgép-kezelési felület a hibrid Cloud Manager rendszer-konfigurációhoz. |
| Felügyeleti hálózat  | Hibrid Cloud Manager | SSH | 22 | Rendszergazdai SSH-hozzáférés a hibrid Cloud Managerhez. |
| HCM | Felhőbeli átjáró | TCP (HTTPS) | 8123 | Gazdagép-alapú replikációs szolgáltatásra vonatkozó utasítások küldése a hibrid Felhőbeli átjárónak. |
| HCM | Felhőbeli átjáró | HTTP TCP (HTTPS) | 9443 | Felügyeleti utasítások küldése a helyi hibrid Felhőbeli átjárónak a REST API használatával. |
| Felhőbeli átjáró | L2C | TCP (HTTPS) | 443 | Felügyeleti utasítások küldése a Cloud Gatewaytől a L2C, ha a L2C ugyanazt az útvonalat használja, mint a hibrid felhőalapú átjáró. |
| Felhőbeli átjáró | ESXi-gazdagépek | TCP | 80 902 | Felügyelet és OVF üzembe helyezése. |
| Felhőalapú átjáró (helyi)| Cloud Gateway (távoli) | UDP | 4500 | IPSEC-hez szükséges<br>   Internet Key Exchange (IKEv2) a kétirányú alagút számítási feladatainak beágyazásához. A hálózati Translation-Traversal (NAT-T) is támogatott. |
| Felhőalapú átjáró (helyi) | Cloud Gateway (távoli)  | UDP | 500 | IPSEC-hez szükséges<br> Az Internet Key Exchange (ISAKMP) a kétirányú alagúthoz. |
| Helyszíni vCenter-hálózat | Saját felhőalapú felügyeleti hálózat | TCP | 8000 |  helyszíni vCenter származó virtuális gépek vMotion saját Felhőbeli vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP-és DNS-feloldási megfontolások
A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresési és IP-címek hozzárendeléseihez. Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges. A virtuális gépeket konfigurálhatja úgy, hogy biztosítsa ezeket a szolgáltatásokat a saját felhőalapú környezetében.  

A DHCP-szolgáltatás beépített használatával NSX vagy használhat helyi DHCP-kiszolgálót a privát felhőben ahelyett, hogy útválasztást továbbít a DHCP-forgalomnak a WAN-on keresztül a helyszíni környezetbe.

További részletekért tekintse meg a [DHCP-szolgáltatások NSX-T hálózati szegmensre való ellátását](deploy-azure-vmware-solution.md#optional-provide-dhcp-services-to-nsx-t-network-segment) ismertető cikket.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte az Azure VMware-megoldás saját felhő üzembe helyezésének szempontjait és követelményeit. Ha a megfelelő hálózatkezelés van érvényben, folytassa a következő oktatóanyaggal, amely az Azure VMware-megoldás saját felhőjét hozza létre.

> [!div class="nextstepaction"]
> [Azure VMware-megoldás saját felhő létrehozása](tutorial-create-private-cloud.md)
