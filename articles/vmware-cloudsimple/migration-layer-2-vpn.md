---
title: Azure VMware-megoldás CloudSimple – 2. rétegbeli hálózat kinyújtása a saját felhőbe
description: Útmutató a 2. rétegbeli VPN beállításához a NSX-T egy CloudSimple privát felhőben és egy helyszíni önálló NSX Edge-ügyfélen
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06446b6c36e36466fe891d7327d8151603cdecd2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899371"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Számítási feladatok migrálása a 2. rétegbeli kiterjesztett hálózatok használatával

Ebből az útmutatóból megtudhatja, hogyan használható a 2. rétegbeli VPN (L2VPN) a 2. rétegbeli hálózat kiépítéséhez a helyszíni környezetből a CloudSimple saját felhőbe. Ez a megoldás lehetővé teszi a helyszíni VMware-környezetben futó számítási feladatok áttelepítését az Azure-beli privát felhőbe ugyanazon az alhálózaton belül, anélkül, hogy újra kellene telepíteni a munkaterheléseket.

A 2. rétegbeli hálózatok L2VPN-alapú nyújtásával a helyszíni VMware-környezet NSX-alapú hálózatokkal vagy anélkül is működhet. Ha nem rendelkezik NSX-alapú hálózatokkal a helyszíni számítási feladatokhoz, használhat egy önálló NSX Edge Services-átjárót.

> [!NOTE]
> Ez az útmutató azt a forgatókönyvet ismerteti, amelyben a helyszíni és a privát felhőalapú adatközpontok helyek közötti VPN-kapcsolaton keresztül csatlakoznak.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

A helyszíni hálózat L2VPN használatával történő kiépítéséhez konfigurálnia kell egy L2VPN-kiszolgálót (cél NSX-T Tier0 útválasztó) és egy L2VPN-ügyfelet (forrás önálló ügyfél).  

Ebben az üzembe helyezési forgatókönyvben a privát felhő egy helyek közötti VPN-alagúton keresztül csatlakozik a helyszíni környezethez, amely lehetővé teszi a helyszíni felügyeleti és vMotion alhálózatok számára, hogy kommunikáljanak a saját felhőalapú felügyeleti és vMotion alhálózatokkal. Ez a megoldás a vCenter vMotion (xVC-vMotion) esetében szükséges. A NSX-T Tier0 útválasztó L2VPN-kiszolgálóként van telepítve a privát felhőben.

Az önálló NSX Edge üzembe helyezése a helyszíni környezetben L2VPN-ügyfélként történik, és ezt követően a L2VPN-kiszolgálóval párosítva. A GRE-alagút végpontja mindkét oldalon létrejön, és úgy van konfigurálva, hogy "stretch" értékűre állítsa a helyszíni 2. rétegbeli hálózatot a saját felhőbe. Ezt a konfigurációt az alábbi ábra mutatja be.

![Központi telepítési forgatókönyv](media/l2vpn-deployment-scenario.png)

Az L2 VPN használatával történő áttelepítéssel kapcsolatos további tudnivalókért tekintse meg a VMware dokumentációjában található [virtuális](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) magánhálózatok című témakört.

## <a name="prerequisites-for-deploying-the-solution"></a>A megoldás üzembe helyezésének előfeltételei

A megoldás telepítése és konfigurálása előtt győződjön meg arról, hogy a következők vannak érvényben:

* A helyszíni vSphere verziója 6,7 U1 + vagy 6.5 P03 +.
* A helyszíni vSphere-licenc az Enterprise Plus (vSphere Distributed switch) szintjén érhető el.
* Azonosítsa a munkaterhelési réteg 2 hálózatát, amelyet a saját felhőbe kíván nyújtani.
* Azonosítson egy 2. rétegbeli hálózatot a helyszíni környezetben a L2VPN-ügyfél üzembe helyezéséhez.
* [Már létrejött egy privát felhő](create-private-cloud.md).
* Az önálló NSX-T Edge készülék verziója kompatibilis a saját felhőalapú környezetében használt NSX-T Manager-verzióval (NSX-T 2.3.0).
* A rendszer létrehozta a helyszíni vCenter a hamis továbbítások engedélyezése után.
* Egy nyilvános IP-cím van fenntartva a NSX-T önálló ügyfél kimenő IP-címéhez, és 1:1 NAT van érvényben a két cím közötti fordításhoz.
* A DNS-továbbítás beállítása a az.cloudsimple.io tartományhoz tartozó helyszíni DNS-kiszolgálókon a privát Felhőbeli DNS-kiszolgálókra mutat.
* A RTT késése 150 MS-nál kisebb vagy azzal egyenlő, ahogy az a két helyen való működéshez szükséges vMotion.

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

A következő táblázat a támogatott vSphere-verziókat és hálózati adapterek típusait sorolja fel.  

| vSphere verziója | Forrás vSwitch típusa | Virtuális hálózati adapter illesztőprogramja | Cél vSwitch típusa | Támogatott? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Mind | DVS | Mind | DVS | Yes |
| vSphere 6.7 UI vagy magasabb, 6.5 P03 vagy újabb | DVS | VMXNET3 | N-VDS | Yes |
| vSphere 6.7 UI vagy magasabb, 6.5 P03 vagy újabb | DVS | E1000 | N-VDS | [VWware esetében nem támogatott](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI vagy 6.5 P03, NSX-V vagy Versions, NSX-T 2.2, 6.5 P03 vagy újabb verziók | Mind | Mind | N-VDS | [VWware esetében nem támogatott](https://kb.vmware.com/s/article/56991) |

A VMware NSX-T 2,3 kiadástól kezdve:

* A saját Felhőbeli oldalon a helyszíni L2VPN-re mutató logikai kapcsoló nem irányítható egyszerre. A kifeszített logikai kapcsoló nem csatlakoztatható logikai útválasztóhoz.
* A L2VPN és az Útválasztás-alapú IPSEC VPN-ek csak API-hívásokkal konfigurálhatók.

További információ: [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) in the VMware dokumentáció.

### <a name="sample-l2-vpn-deployment-addressing"></a>Példa L2 VPN üzembe helyezési címzésre

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Helyszíni hálózat, amelyben az önálló ESG TANULMÁNYA (L2 VPN-ügyfél) telepítve van

| **Elem** | **Érték** |
|------------|-----------------|
| Hálózati név | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Önálló peremhálózati berendezés IP-címe | 10.250.0.111 |
| Önálló peremhálózati berendezés NAT IP-címe | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>A helyszíni hálózat kifeszíthető

| **Elem** | **Érték** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Saját felhőalapú IP-séma a NSX-T Tier0-útválasztóhoz (L2 VPN Serve)

| **Elem** | **Érték** |
|------------|-----------------|
| Visszacsatolási felület | 192.168.254.254/32 |
| Bújtatási interfész | 5.5.5.1/29 |
| Logikai kapcsoló (kifeszített) | Stretch_LS |
| Visszacsatolási felület (NAT IP-címe) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>A kifeszített hálózathoz hozzárendelni kívánt privát felhőalapú hálózat

| **Elem** | **Érték** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>A L2VPN számára szükséges logikai útválasztó AZONOSÍTÓjának beolvasása

A következő lépések bemutatják, hogyan lehet beolvasni a Tier0 DR logikai útválasztó példányának logikai útválasztó-AZONOSÍTÓját az IPsec és a L2VPN szolgáltatáshoz. A logikai útválasztó AZONOSÍTÓját később kell végrehajtani a L2VPN megvalósításakor.

1. Jelentkezzen be a NSX-T Managerbe `https://*nsx-t-manager-ip-address*` , és válassza a **hálózati**  >  **útválasztók**  >  **szolgáltatója – LR**  >  **Áttekintés** elemet. **Magas rendelkezésre állású mód** esetén válassza az **aktív-készenlét** lehetőséget. Ezzel a művelettel megnyílik egy előugró ablak, amely megjeleníti azt a peremhálózati virtuális gépet, amelyen a Tier0-útválasztó éppen aktív.

    ![Aktív – készenléti állapot kiválasztása](media/l2vpn-fetch01.png)

2. Válassza ki a **háló**  >  **csomópontjainak**  >  **szegélyét**. Jegyezze fel az előző lépésben azonosított aktív peremhálózati virtuális gép (Edge-VM1) felügyeleti IP-címét.

    ![Megjegyzés: felügyeleti IP-cím](media/l2vpn-fetch02.png)

3. Nyisson meg egy SSH-munkamenetet a peremhálózati virtuális gép felügyeleti IP-címével. Futtassa a ```get logical-router``` parancsot a Felhasználónév- **rendszergazda** és a jelszó **CloudSimple 123!**.

    ![A megnyitott SSH-munkamenetet bemutató képernyőkép.](media/l2vpn-fetch03.png)

4. Ha nem látja a "DR-Provider-LR" bejegyzést, hajtsa végre az alábbi lépéseket.

5. Hozzon létre két átfedésben lévő logikai kapcsolót. A rendszer egy logikai kapcsolót helyez el a helyszíni helyre, ahol az áttelepített munkaterhelések találhatók. Egy másik logikai kapcsoló egy dummy kapcsoló. Útmutatásért lásd: [logikai kapcsoló létrehozása](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) a VMware-dokumentációban.

    ![Logikai kapcsoló létrehozása](media/l2vpn-fetch04.png)

6. Csatlakoztassa a próbabábu kapcsolót a Tier1-útválasztóhoz egy hivatkozás helyi IP-címével vagy a helyszíni vagy a saját felhőben nem átfedő alhálózattal. Lásd: a [Antennalevezetés-Port hozzáadása egy 1. rétegbeli logikai útválasztón](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) a VMware-dokumentációban.

    ![Dummy kapcsoló csatolása](media/l2vpn-fetch05.png)

7. Futtassa `get logical-router` újra a parancsot a peremhálózati virtuális gép SSH-munkamenetén. Megjelenik a "DR-Provider-LR" logikai útválasztó UUID azonosítója. Jegyezze fel az UUID-t, amely a L2VPN konfigurálásakor szükséges.

    ![A logikai útválasztó UUID-azonosítóját bemutató képernyőkép.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>A L2VPN számára szükséges logikai kapcsoló AZONOSÍTÓjának beolvasása

1. Jelentkezzen be a NSX-T Manager () alkalmazásba `https://nsx-t-manager-ip-address` .
2. Válassza a **hálózatkezelés**  >  **kapcsolói**  >  **kapcsolók**  >  **< \> \Logical kapcsoló**  >  **áttekintése** lehetőséget.
3. Jegyezze fel a stretch logikai kapcsoló UUID-azonosítóját, amely a L2VPN konfigurálásakor szükséges.

    ![logikai útválasztó kimenetének beolvasása](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>A L2VPN útválasztási és biztonsági szempontjai

Az NSX-T Tier0-útválasztó és az önálló NSX Edge-ügyfél közötti IPsec-útválasztási VPN-kapcsolat létrehozásához a NSX-T Tier0 útválasztó visszacsatolási felületének képesnek kell lennie kommunikálni a helyszíni NSX önálló ügyfelének nyilvános IP-címével a 500/4500-as UDP-n keresztül.

### <a name="allow-udp-5004500-for-ipsec"></a>Az UDP 500/4500 engedélyezése az IPsec számára

1. [Hozzon létre egy nyilvános IP-címet](public-ips.md) az NSX-T Tier0 visszacsatolási interfészhez a CloudSimple-portálon.

2. [Hozzon létre egy](firewall.md) , az UDP 500/4500 bejövő forgalmat engedélyező állapot-nyilvántartó szabályokkal rendelkező tűzfal táblát, és csatolja a tűzfal TÁBLÁT a NSX-T HostTransport alhálózathoz.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>A visszacsatolási felület IP-címének reklámozása az alátét-hálózaton

1. Hozzon létre egy NULL útvonalat a visszacsatolási csatoló hálózata számára. Jelentkezzen be a NSX-T kezelőjébe, és válassza a **hálózatkezelés**  >  **útválasztási**  >  **útválasztó**  >  **szolgáltató – LR**  >  **útválasztási**  >  **statikus útvonalak** lehetőséget. Kattintson a **Hozzáadás** parancsra. A **hálózat** mezőben adja meg a visszacsatolási kapcsolat IP-címét. A **következő ugrások** esetében kattintson a **Hozzáadás** gombra, adja meg a "NULL" értéket a következő ugráshoz, és tartsa meg az alapértelmezett 1 értéket a rendszergazdai távolság számára.

    ![Statikus útvonal hozzáadása](media/l2vpn-routing-security01.png)

2. Hozzon létre egy IP-előtagot tartalmazó listát. Jelentkezzen be a NSX-T kezelőjébe, és válassza a **hálózatkezelés**  >  **útválasztási**  >  **útválasztók**  >  **szolgáltatója – LR**  >  **útválasztási**  >  **IP-előtagok listáját**. Kattintson a **Hozzáadás** parancsra. Adjon meg egy nevet a lista azonosításához. Az **előtagok** esetében kattintson kétszer a **Hozzáadás** gombra. Az első sorban adja meg a "0.0.0.0/0" értéket a **hálózat** és a "megtagadás" **művelethez**. A második sorban válassza a **bármely** a **hálózat** számára lehetőséget, és **engedélyezze** a **műveletet**.
3. Csatolja az IP-előtag listáját a BGP-szomszédokhoz (TOR). Ha az IP-előtag listáját a BGP-szomszédhoz csatolja, azzal meggátolja, hogy az alapértelmezett útvonal a BGP-ben meghirdessen a TOR-kapcsolókra. Azonban minden más útvonal, amely tartalmazza a null útvonalat, meghirdeti a visszacsatolási felület IP-címét a TOR-kapcsolók számára.

    ![IP-előtag-lista létrehozása](media/l2vpn-routing-security02.png)

4. Jelentkezzen be a NSX-T Managerbe, és válassza a **hálózatkezelés**  >  **útválasztási**  >  **útválasztók**  >  **szolgáltató-LR**  >  **Routing**  >  **BGP**-  >  **szomszédok** lehetőséget. Válassza ki az első szomszédot. Kattintson a címtartomány **szerkesztése** elemre  >  . Az IPv4-család esetében szerkessze a **kimeneti szűrő** oszlopot, és válassza ki a létrehozott IP-előtagot. Kattintson a **Mentés** gombra. Ismételje meg ezt a lépést a második szomszédnál.

    ![IP-előtagi lista csatolása 1. ](media/l2vpn-routing-security03.png) ![ IP-előtag csatolása 2. lista](media/l2vpn-routing-security04.png)

5. Terjessze újra a null értékű statikus útvonalat a BGP-be. Ha a visszacsatolási felületet szeretné reklámozni az alátéthez, újra kell terjesztenie a null statikus útvonalat a BGP-be. Jelentkezzen be a NSX-T Managerbe, és válassza a **hálózatkezelés**  >  **útválasztási**  >  **útválasztó**  >  **szolgáltató – LR**  >  **útválasztási**  >  **útvonal újraelosztási**  >  **szomszédok** lehetőséget. Válassza a **Provider-LR-Route_Redistribution** elemet, majd kattintson a **Szerkesztés** gombra. Jelölje be a **statikus** jelölőnégyzetet, majd kattintson a **Mentés** gombra.

    ![NULL Statikus útvonal újraterjesztése a BGP-be](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Route-alapú VPN konfigurálása a NSX-T Tier0-útválasztón

A következő sablonnal megtudhatja, hogyan konfigurálhat egy Route-alapú VPN-T az NSX-T Tier0-útválasztón. Az egyes POST-hívásokhoz tartozó UUID-ket a következő POST-hívásokhoz kell megadni. A L2VPN tartozó visszacsatolási és bújtatási adapterek IP-címeinek egyedinek kell lenniük, és nem lehetnek átfedésben a helyszíni vagy a saját felhőalapú hálózatokkal.

A L2VPN használt visszacsatolási és bújtatási interfészek IP-címeinek egyedinek kell lenniük, és nem lehetnek átfedésben a helyszíni vagy a saját felhőalapú hálózatokkal. A visszacsatolási csatoló hálózatának mindig/32-nek kell lennie.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

A következő API-hívások esetén cserélje le az IP-címet a NSX-T Manager IP-címére. Az összes ilyen API-hívást futtathatja a POSTer-ügyfélen vagy `curl` parancsok használatával.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Az IPSec VPN szolgáltatás engedélyezése a logikai útválasztón

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Profilok létrehozása: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Profilok létrehozása: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Profilok létrehozása: alagút

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Helyi végpont létrehozása

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Társ-végpont létrehozása

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Route-alapú VPN-munkamenet létrehozása

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>L2VPN konfigurálása a NSX-T Tier0 útválasztón

Minden POST hívás után adja meg a következő információkat. Az azonosítók a későbbi POST-hívásokhoz szükségesek.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>A L2VPN szolgáltatás létrehozása

A következő GET parancs kimenete üres lesz, mert a konfiguráció még nem fejeződött be.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

A következő POST parancs esetében a logikai útválasztó azonosítója a korábban beszerzett Tier0 DR logikai útválasztó UUID azonosítója.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>A L2VPN-munkamenet létrehozása

A következő POST parancs esetében a L2VPN szolgáltatás azonosítója az imént beszerzett azonosító, az IPsec VPN-munkamenet azonosítója pedig az előző szakaszban beszerzett azonosító.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Ezek a hívások GRE-alagút végpontot hoznak létre. Az állapot ellenõrzéséhez futtassa a következő parancsot.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Logikai port létrehozása a megadott bújtatási AZONOSÍTÓval

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Szerezze be a L2VPN tartozó társ-kódot a NSX-T oldalon

Szerezze be a NSX-T végpont egyenrangú kódját. A társ kód megadása a távoli végpont konfigurálásakor szükséges. A L2VPN <munkamenet-azonosító> az előző szakaszból szerezhető be. További információ: [NSX-T 2,3 API-útmutató](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>A NSX-T önálló ügyfél (helyszíni) üzembe helyezése

A telepítés előtt ellenőrizze, hogy a helyszíni tűzfalszabályok engedélyezik-e a bejövő és kimenő UDP 500/4500-forgalmat a NSX-T T0 útválasztó visszacsatolási felületéhez korábban lefoglalt CloudSimple nyilvános IP-címről. 

1. [Az önálló NSX Edge-ügyfél letöltése](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF és csomagolja ki a fájlokat a letöltött kötegből egy mappába.

    ![Önálló NSX Edge-ügyfél letöltése](media/l2vpn-deploy-client01.png)

2. Nyissa meg a mappát az összes kibontott fájllal. Válassza ki az összes VMDK (NSX-l2t-Client-Large. MF és NSX-l2t-client-large. OVF nagyméretű berendezés méretéhez vagy NSX-l2t-Client-XLarge. MF és NSX-l2t-client-Xlarge. OVF a nagy méretű készülék méretéhez). Kattintson a **Tovább** gombra.

    ![Válassza ki ](media/l2vpn-deploy-client02.png) ![ a sablon képernyőképét, amely a kiválasztott VMDK-fájlokat jeleníti meg.](media/l2vpn-deploy-client03.png)

3. Adja meg a NSX-T önálló ügyfél nevét, és kattintson a **tovább** gombra.

    ![Adja meg a sablon nevét](media/l2vpn-deploy-client04.png)

4. Az adattár beállításainak eléréséhez kattintson a **tovább** gombra. Válassza ki a megfelelő adattárat a NSX-T önálló ügyfél számára, és kattintson a **tovább** gombra.

    ![Adattár kiválasztása](media/l2vpn-deploy-client06.png)

5. Válassza ki a megfelelő portokat a Trunk (Trunk PG), a Public (kimenő PG) és a HA Interface (kimenő PG) számára a NSX-T különálló ügyfélhez. Kattintson a **Tovább** gombra.

    ![Porttartomány kiválasztása](media/l2vpn-deploy-client07.png)

6. Adja meg a következő adatokat a **sablon testreszabása** képernyőn, majd kattintson a **tovább** gombra:

    L2T kibontása:

    * **Társ címe**. Adja meg az Azure CloudSimple-portálon az NSX-T Tier0 visszacsatolási felület számára fenntartott IP-címet.
    * **Társ-kód**. Illessze be a L2VPN-kiszolgáló telepítésének utolsó lépése alapján beszerzett társ-kódot.
    * **Alinterfészek VLAN (alagút azonosítója)**. Adja meg a feszíteni kívánt VLAN-azonosítót. A zárójelek () mezőben adja meg a korábban konfigurált bújtatási azonosítót.

    Kimenő kapcsolat kibontása:

    * **DNS IP-címe**. Adja meg a helyszíni DNS IP-címét.
    * **Alapértelmezett átjáró**.  Adja meg a virtuális helyi hálózat alapértelmezett átjáróját, amely alapértelmezett átjáróként fog működni ehhez az ügyfélhez.
    * **IP-cím**. Adja meg az önálló ügyfél kimenő IP-címét.
    * **Előtag hossza** Adja meg a kimenő VLAN/alhálózat előtagjának hosszát.
    * **CLI-rendszergazda/engedélyezés/gyökér felhasználói jelszó**. Állítsa be a rendszergazdai/Enable/root-fiók jelszavát.

      ![Sablon testreszabása sablon testreszabása ](media/l2vpn-deploy-client08.png)
       ![ – További](media/l2vpn-deploy-client09.png)

7. Tekintse át a beállításokat, majd kattintson a **Befejezés** gombra.

    ![Konfigurálás befejezése](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Helyszíni fogadó port konfigurálása

Ha az egyik VPN-hely nem rendelkezik telepített NSX, beállíthatja az L2 VPN-t úgy, hogy egy önálló NSX-szegélyt helyez üzembe az adott helyen. Az önálló NSX Edge egy OVF-fájl használatával van telepítve egy olyan gazdagépen, amelyet nem a NSX felügyel. Ez üzembe helyez egy NSX Edge Services Gateway-berendezést, amely L2 VPN-ügyfélként működik.

Ha egy különálló peremhálózati törzs vNIC egy vSphere elosztott kapcsolóhoz csatlakozik, akkor az L2 VPN-függvényhez nem kell kevert módot vagy fogadó portot megadni. A kevert mód használatával duplikált pingelések és ismétlődő válaszok is megadhatók. Ezért használja a fogadó port módot az L2 VPN önálló NSX Edge-konfigurációjában. Tekintse meg a fogadó [port konfigurálása](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) a VMware dokumentációban című témakört.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN-és L2VPN-ellenőrzés

A következő parancsokkal ellenőrizheti az IPsec-és a L2VPN-munkameneteket az önálló NSX-T Edge-ből.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

A következő parancsokkal ellenőrizheti az IPsec-és a L2VPN-munkameneteket a NSX-T Tier0-útválasztóról.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

A következő parancsokkal ellenőrizheti a fogadó portot az ESXi-gazdagépen, ahol a NSX-T különálló ügyfél virtuális gép a helyszíni környezetben található.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
