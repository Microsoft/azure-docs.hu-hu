---
title: Azure VMware-megoldás CloudSimple – magas rendelkezésre állás konfigurálása a helyszínről a CloudSimple VPN-átjáróra
description: Ismerteti, hogyan konfigurálható magas rendelkezésre állású kapcsolat a helyszíni környezetből a magas rendelkezésre állású CloudSimple VPN-átjáróra
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895699"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Magas rendelkezésre állású kapcsolat konfigurálása a helyszínről a VPN Gateway CloudSimple

A hálózati rendszergazdák magas rendelkezésre állású IPsec helyek közötti VPN-kapcsolatokat konfigurálhatják a helyszíni környezetből egy CloudSimple VPN-átjáróra.

Ez az útmutató ismerteti a helyszíni tűzfal konfigurálásának lépéseit az IPsec-helyek közötti VPN magas rendelkezésre állási kapcsolatához. A részletes lépések a helyszíni tűzfal típusától függenek. Példaként ez az útmutató a következő két típusú tűzfalat ismerteti: Cisco ASA és Palo Alto Networks.

## <a name="before-you-begin"></a>Előkészületek

A helyszíni tűzfal konfigurálása előtt végezze el a következő feladatokat.

1. Ellenőrizze, hogy a szervezet [kiosztotta-e a szükséges](create-nodes.md) csomópontokat, és létrehozott-e legalább egy CloudSimple privát felhőt.
2. [Konfiguráljon egy helyek közötti VPN-átjárót](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni hálózat és a CloudSimple privát felhője között.

Lásd: a [VPN-átjárók áttekintése](cloudsimple-vpn-gateways.md) az 1. és a 2. fázisra vonatkozó támogatottak.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Helyszíni Cisco ASA-tűzfal konfigurálása

Az ebben a szakaszban szereplő utasítások a Cisco ASA 8,4-es vagy újabb verziójára vonatkoznak. A konfigurációs példában a Cisco adaptív biztonsági berendezés szoftverének 9,10-es verziója IKEv1 módban van telepítve és konfigurálva.

Ahhoz, hogy a helyek közötti VPN működjön, engedélyeznie kell az UDP 500/4500 és az ESP (IP protokoll 50) szolgáltatást a CloudSimple elsődleges és másodlagos nyilvános IP-címéről (társ IP) a helyszíni Cisco ASA VPN-átjáró külső felületén.

### <a name="1-configure-phase-1-ikev1"></a>1. az 1. fázis konfigurálása (IKEv1)

Ha engedélyezni szeretné az 1. fázist (IKEv1) a külső felületen, adja meg a következő CLI-parancsot a Cisco ASA-tűzfalon.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. IKEv1 szabályzat létrehozása

Hozzon létre egy IKEv1 szabályzatot, amely meghatározza a kivonatoláshoz, hitelesítéshez, Diffie-Hellman csoporthoz, élettartamhoz és titkosításhoz használandó algoritmusokat és metódusokat.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. bújtatási csoport létrehozása

Hozzon létre egy bújtatási csoportot az IPsec-attribútumok alatt. Konfigurálja a társ IP-címet és a bújtatás előtti előmegosztott kulcsot, amelyet [a helyek közötti VPN-átjáró konfigurálásakor](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)állított be.

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. a 2. fázis (IPsec) konfigurálása

A 2. fázis (IPsec) konfigurálásához hozzon létre egy hozzáférés-vezérlési listát (ACL), amely meghatározza a titkosítani kívánt forgalmat és a bújtatást. A következő példában az adatforgalom a helyszíni helyi alhálózatból (10.16.1.0/24) származó, a saját Felhőbeli távoli alhálózatra (192.168.0.0/24) forrású alagútból származik. Az ACL több bejegyzést is tartalmazhat, ha több alhálózat található a helyek között.

A Cisco ASA 8,4-es és újabb verzióiban létrehozhatók olyan objektumok vagy objektumtípusok, amelyek tárolóként szolgálnak a hálózatok, alhálózatok, gazdagépek IP-címei vagy több objektum számára. Hozzon létre egy objektumot a helyi és egy objektum számára a távoli alhálózatok számára, és használja őket a kriptográfiai ACL-hez és a NAT-utasításokhoz.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Helyszíni helyi alhálózat definiálása objektumként

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>A CloudSimple távoli alhálózat definiálása objektumként

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Hozzáférési lista konfigurálása az érdeklődési forgalomhoz

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Állítsa be az átalakító készletet

Konfigurálja az átalakítási készletet (TS), amelynek tartalmaznia kell a kulcsszót ```ikev1``` . A TS-ben megadott titkosítási és kivonatoló attribútumoknak meg kell egyezniük a [CloudSimple VPN-átjárók alapértelmezett konfigurációjában](cloudsimple-vpn-gateways.md)felsorolt paraméterekkel.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. a kriptográfiai Térkép konfigurálása

Konfigurálja a titkosítási térképet, amely a következő összetevőket tartalmazza:

* Társ IP-címe
* Az érdeklődési forgalmat tartalmazó megadott ACL
* Átalakító készlet

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. a titkosítási Térkép alkalmazása

A kriptográfiai Térkép alkalmazása a külső felületen:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. a vonatkozó NAT-szabályok megerősítése

Az alábbiakban a használt NAT-szabály szerepel. Győződjön meg arról, hogy a VPN-forgalom nem tartozik más NAT-szabályhoz.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Példa IPsec-helyek közötti VPN-kimenetre a Cisco ASA-ből

1. fázis kimenete:

![1. fázis kimenet a Cisco ASA-tűzfalhoz](media/ha-vpn-connection-cisco-phase1.png)

2. fázis kimenete:

![2. fázis kimenet a Cisco ASA-tűzfalhoz](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Helyszíni Palo Alto hálózati tűzfal konfigurálása

Az ebben a szakaszban szereplő utasítások a Palo Alto Networks 7,1-es és újabb verzióira vonatkoznak. Ebben a konfigurációs példában a Palo Alto Networks VM-Series 8.1.0 IKEv1 módban van telepítve és konfigurálva.

Ahhoz, hogy a helyek közötti VPN működjön, engedélyeznie kell az UDP 500/4500 és az ESP (IP protokoll 50) szolgáltatást a CloudSimple elsődleges és másodlagos nyilvános IP-címéről (társ IP) a helyszíni Palo Alto Networks Gateway külső felületén.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. elsődleges és másodlagos bújtatási felületek létrehozása

Jelentkezzen be a Palo Alto-tűzfalba, válassza a **hálózati**  >  **adapterek**  >  **alagút**  >  **hozzáadása** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

* A csatoló neve. Az első mező automatikusan fel van töltve a "Tunnel" kulcsszóval. A szomszédos mezőbe írjon be egy 1 és 9999 közötti számot. Ez az interfész elsődleges bújtatási felületként használható a helyszíni adatközpont és a privát felhő közötti helyek közötti adatforgalom továbbítására.
* Megjegyzés. Adja meg a megjegyzéseket az alagút céljának egyszerű azonosításához
* Netflow-profil. Hagyja meg az alapértelmezett értéket.
* Config. Kapcsolat társítása a következőhöz: Virtual router: válassza az **alapértelmezett** lehetőséget. 
        Biztonsági zóna: válassza ki a zónát a megbízható helyi hálózati forgalomhoz. Ebben a példában a LAN-forgalomhoz tartozó zóna neve "Trust".
* IPv4. Kattintson a **Hozzáadás** gombra, és adja hozzá a nem egymást átfedő fel nem használt/32 IP-címet a környezetben, amely az elsődleges alagút felületéhez lesz rendelve, és a rendszer az alagutak figyelésére fogja használni (ezt később ismertetjük).

Mivel ez a konfiguráció magas rendelkezésre állású VPN-hez készült, két bújtatási csatolóra van szükség: egy elsődleges és egy másodlagos. A másodlagos alagút felületének létrehozásához ismételje meg az előző lépéseket. Válasszon másik bújtatási azonosítót és egy másik használaton kívüli/32 IP-címet.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. statikus útvonalak beállítása a saját Felhőbeli alhálózatok számára a helyek közötti VPN eléréséhez

Útvonalak szükségesek ahhoz, hogy a helyszíni alhálózatok CloudSimple a saját felhőalapú alhálózatok eléréséhez.

Válassza a **hálózati**  >  **virtuális útválasztók**  >  *alapértelmezett*  >  **statikus útvonalak**  >  **hozzáadása** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

* név. Adja meg az útvonal céljának egyszerű azonosítására szolgáló nevet.
* Cél. Adja meg az CloudSimple saját Felhőbeli alhálózatait, hogy elérhetők legyenek a helyszíni S2S-alagutakon keresztül
* Felület. Válassza ki az 1. lépésben (2. szakasz) a legördülő listából létrehozott elsődleges bújtatási felületet. Ebben a példában ez az alagút. 20.
* Következő ugrás. Válassza a **Nincs** lehetőséget.
* Rendszergazdai távolság. Hagyja meg az alapértelmezett értéket.
* Metrika. 1 és 65535 közötti értéket adjon meg. A kulcs az elsődleges alagút felületének megfelelő útvonal alsó metrikájának beírása, amely a korábbi útvonal előnyben részesített elérési útjához hasonlít. Ha az alagút. 20 metrikai értéke 20, a Tunnel. 30 esetében pedig 30, a Tunnel. 20 értéke javasolt.
* Útválasztási táblázat. Hagyja meg az alapértelmezett értéket.
* BFD-profil. Hagyja meg az alapértelmezett értéket.
* Elérési út figyelése Hagyja üresen.

Az előző lépések megismétlésével hozzon létre egy másik útvonalat a privát Felhőbeli alhálózatok számára másodlagos/biztonsági mentési útvonalként a másodlagos alagút felületén keresztül. Ezúttal válasszon másik bújtatási azonosítót és egy magasabb metrikát, mint az elsődleges útvonalhoz.

### <a name="3-define-the-cryptographic-profile"></a>3. a titkosítási profil megadása

Definiáljon egy titkosítási profilt, amely meghatározza a VPN-alagutak beállításához használt azonosítási, hitelesítési és titkosítási protokollokat és algoritmusokat az 1. IKEv1 fázisban.

Válassza a **hálózat**  >  **kibontása hálózati profilok**  >  **IKE titkosítási**  >  **Hozzáadás** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

* név. Adja meg az IKE titkosítási profil nevét.
* DH-csoport. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő DH-csoportot.
* Titkosítás. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő titkosítási módszert.
* Hitelesítés. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő hitelesítési módszert.
* Kulcs élettartama. Hagyja meg az alapértelmezett értéket.
* Több IKEv2-hitelesítés. Hagyja meg az alapértelmezett értéket.

### <a name="4-define-ike-gateways"></a>4. IKE-átjárók definiálása

Adja meg az IKE-átjárókat a partnereknek a VPN-alagút egyes végpontján belüli kommunikációjának létrehozásához.

Válassza a **hálózat**  >  **kibontása hálózati profilok**  >  **IKE-átjárók**  >  **Hozzáadás** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

Általános lap:

* név. Adja meg az elsődleges CloudSimple VPN-társával egyenrangú IKE-átjáró nevét.
* Verzió felirat mellett megjelenő számot. Válassza a **csak IKEv1 módot**.
* A címek típusa Válassza az **IPv4** lehetőséget.
* Felület. Válassza ki a nyilvános vagy kívüli felületet.
* Helyi IP-cím. Hagyja meg az alapértelmezett értéket.
* Társ IP-címének típusa Válassza az **IP** lehetőséget.
* Társ címe. Adja meg az elsődleges CloudSimple VPN-társ IP-címét.
* Hitelesítés. Válassza az **előmegosztott kulcs** lehetőséget.
* Előmegosztott kulcs/előmegosztott kulcs megerősítése. Adja meg az előmegosztott kulcsot, hogy egyezzen a CloudSimple VPN-átjáró kulcsával.
* Helyi azonosító. Adja meg a helyszíni Palo Alto-tűzfal nyilvános IP-címét.
* Társ-azonosítás. Adja meg az elsődleges CloudSimple VPN-társ IP-címét.

Speciális beállítások lap:

* A passzív üzemmód engedélyezése. Hagyja üresen.
* NAT-bejárás engedélyezése. Ha a helyszíni Palo Alto tűzfal nem a NAT-eszköz mögött van, hagyja üresen a jelölést. Ellenkező esetben jelölje be a jelölőnégyzetet.

IKEv1

* Exchange mód. Válassza a **Main (fő**) lehetőséget.
* IKE titkosítási profil. Válassza ki a korábban létrehozott IKE titkosítási profilt. Ne jelölje be a darabolás engedélyezése jelölőnégyzetet.
* Elhalt társ-észlelés. Ne jelölje be a jelölőnégyzetet.

Az előző lépések megismétlésével hozza létre a másodlagos IKE-átjárót.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IPSEC titkosítási profilok definiálása

Válassza a **hálózat**  >  **kibontása hálózati profilok**  >  **IPSec-titkosítási**  >  **Hozzáadás** elemet, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

* név. Adja meg az IPsec titkosítási profil nevét.
* IPsec protokoll. Válassza az **ESP** lehetőséget.
* Titkosítás. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő titkosítási módszert.
* Hitelesítés. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő hitelesítési módszert.
* DH-csoport. Válassza a **nem-PFS** lehetőséget.
* Élettartam. Állítsa 30 percre.
* Engedélyezése. Ne jelölje be a jelölőnégyzetet.

Az előző lépések megismétlésével hozzon létre egy másik IPsec titkosítási profilt, amelyet a rendszer a másodlagos CloudSimple VPN-társként fog használni. Ugyanez az IPSEC-titkosítási profil is használható mind az elsődleges, mind a másodlagos IPsec-alagúthoz (lásd a következő eljárást).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. a figyelési profilok definiálása az alagút figyeléséhez

Válassza a **hálózat**  >  **kibontása hálózati profilok**  >  **figyelő**  >  **Hozzáadás** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

* név. Adja meg annak a figyelő profilnak a nevét, amelyet a rendszer a hiba esetén a bújtatás figyeléséhez használ.
* Művelet. Válassza a **feladatátvétel** lehetőséget.
* Időköz. Adja meg a **3** értéket.
* Küszöb. Adja meg a **7-es** értéket.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Állítsa be az elsődleges és a másodlagos IPsec-alagutat.

Válassza a **hálózati**  >  **IPSec-alagutak**  >  **Hozzáadás** lehetőséget, konfigurálja a következő mezőket, majd kattintson **az OK** gombra.

Általános lap:

* név. Adja meg az elsődleges IPSEC-alagút nevét az elsődleges CloudSimple VPN-társsal való társításhoz.
* Bújtatási interfész. Válassza ki az elsődleges bújtatási felületet.
* Típusa. Hagyja meg az alapértelmezett értéket.
* A címek típusa Válassza az **IPv4** lehetőséget.
* IKE-átjáró. Válassza ki az elsődleges IKE-átjárót.
* IPsec titkosítási profil. Válassza ki az elsődleges IPsec-profilt. Válassza a **Speciális beállítások megjelenítése** lehetőséget.
* Visszajátszás elleni védelem engedélyezése. Hagyja meg az alapértelmezett értéket.
* A TOS-fejléc másolása. Ne jelölje be a jelölőnégyzetet.
* Bújtatási figyelő. Jelölje be a jelölőnégyzetet.
* Cél IP-címe. Adja meg a CloudSimple saját felhőalapú alhálózatához tartozó bármely IP-címet, amely a helyek közötti kapcsolaton keresztül engedélyezett. Győződjön meg arról, hogy az alagút interfészei (például a Tunnel. 20-10.64.5.2/32 és az Tunnel. 30-10.64.6.2/32) a Palo Alto-on keresztül elérhetők a CloudSimple privát felhő IP-címére a helyek közötti VPN-en keresztül. A proxy-azonosítókat a következő konfigurációban tekintheti meg.
* Profil. Válassza ki a figyelő profilt.

Proxy-azonosítók lap: kattintson az **IPv4**  >  **Hozzáadás** elemre, és konfigurálja a következőket:

* Proxy azonosítója. Adja meg az érdekes forgalom nevét. Több proxy azonosító is szerepelhet egy IPsec-alagúton belül.
* Helyi. Adja meg azokat a helyszíni helyi alhálózatokat, amelyek számára engedélyezett a magánhálózati Felhőbeli alhálózatokkal való kommunikáció a helyek közötti VPN-en keresztül.
* Távoli. Itt adhatja meg a helyi alhálózatokkal való kommunikációra jogosult privát Felhőbeli távoli alhálózatokat.
* Protokoll. Válassza **a bármelyik** lehetőséget.

Az előző lépések megismétlésével hozzon létre egy másik IPsec-alagutat, amelyet a másodlagos CloudSimple VPN-társához kíván használni.

## <a name="references"></a>Hivatkozások

NAT konfigurálása a Cisco ASA-on:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">A Cisco ASA 5500 sorozatának konfigurációs útmutatója</a>

A Cisco ASA támogatott IKEv1-és IKEv2-attribútumai:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">A Cisco ASA Series parancssori felületének konfigurációs útmutatója</a>

Az IPsec helyek közötti VPN konfigurálása a Cisco ASA 8,4-es és újabb verzióival:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">IKEv1 IPsec-helyek közötti alagutak konfigurálása az ASA-ASDM vagy CLI-vel</a>

A Cisco adaptív biztonsági berendezés virtuális (ASAv) konfigurálása az Azure-on:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">A Cisco adaptív biztonsági virtuális berendezés (ASAv) rövid útmutatója</a>

Helyek közötti VPN konfigurálása a Palo Alto proxy-azonosítókkal:

[Helyek közötti VPN beállítása](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Bújtatási figyelő beállítása:

[Alagút figyelésének beállítása](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE-átjáró vagy IPsec-alagút műveletei:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IKE-átjáró vagy IPsec-alagút engedélyezése/letiltása, frissítése vagy újraindítása</a>
