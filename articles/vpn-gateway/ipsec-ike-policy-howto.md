---
title: 'IPsec/IKE-szabályzat a S2S VPN & VNet – VNet kapcsolatok: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Konfigurálja az IPsec/IKE-házirendet S2S vagy VNet-VNet kapcsolatokhoz az Azure VPN Gateway Azure Resource Manager és Azure Portal használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878544"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>IPsec/IKE-házirend konfigurálása a S2S VPN-hez vagy a VNet-VNet kapcsolatokhoz: Azure Portal

Ez a cikk végigvezeti az IPsec/IKE-házirend konfigurálásának lépésein a VPN Gateway helyek közötti VPN-vagy VNet-VNet kapcsolatokhoz a Azure Portal használatával. A következő részben az IPsec/IKE-szabályzatok létrehozásához és konfigurálásához, valamint a szabályzat új vagy meglévő kapcsolódáshoz való alkalmazásához lehet segítséget nyújtani.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Az IPsec és az IKE házirend paramétereinek ismertetése

Az IPsec és az IKE protokoll standard verziója számos titkosítási algoritmust támogat különböző kombinációkban. Tekintse át a [titkosítási követelményeket és az Azure VPN Gateways](vpn-gateway-about-compliance-crypto.md) -t, amelyből megtudhatja, hogy ez hogyan segítheti a létesítmények közötti és VNet kapcsolatok biztosítását a megfelelőségi vagy biztonsági követelmények kielégítése érdekében.

Ez a cikk útmutatást nyújt az IPsec/IKE-szabályzatok létrehozásához és konfigurálásához, és alkalmazza azt egy új vagy meglévő VPN Gateway-kapcsolatban.

### <a name="considerations"></a>Megfontolandó szempontok

* Az IPsec/IKE-házirend csak a következő átjárók esetében működik:
  * ***VpnGw1 ~ 5 és VpnGw1AZ ~ 5AZ***
  * ***Standard** _ és _ *_HighPerformance_**
* Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.
* Meg kell adnia az összes algoritmust és paramétert mind az IKE (Main Mode), mind az IPsec (gyors mód) esetében. A részleges házirend-megadás nem engedélyezett.
* A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a helyi VPN-eszközökön támogatott-e a házirend. A S2S-vagy VNet-VNet kapcsolatok nem tudják létrehozni, ha a házirendek nem kompatibilisek.

## <a name="workflow"></a><a name ="workflow"></a>Munkafolyamat

Ez a szakasz az IPsec/IKE-házirend létrehozásához és frissítéséhez szükséges munkafolyamatot ismerteti egy S2S VPN-vagy VNet-VNet kapcsolaton:

1. Hozzon létre egy virtuális hálózatot és egy VPN-átjárót.
2. Hozzon létre egy helyi hálózati átjárót a létesítmények közötti kapcsolatok számára, vagy egy másik virtuális hálózatot és egy átjárót a VNet-VNet kapcsolatban.
3. Hozzon létre egy kapcsolatokat (IPsec vagy VNet2VNet).
4. Konfigurálja/frissítse/távolítsa el az IPsec/IKE-szabályzatot a kapcsolatok erőforrásain.

Az ebben a cikkben szereplő utasítások segítséget nyújtanak az IPsec/IKE-szabályzatok beállításához és konfigurálásához a következő ábrán látható módon:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-házirend diagramja" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>A támogatott titkosítási algoritmusok & a kulcs erősségeit

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmusok és kulcsok

A következő táblázat felsorolja az ügyfelek által konfigurálható támogatott titkosítási algoritmusokat és főbb erősségeket:

| **IPsec/IKE**    | **Beállítások**    |
| ---              | ---            |
| IKE-titkosítás   | AES256, AES192, AES128, DES3, DES                  |
| IKE-integritás    | SHA384, MD5, SHA1, SHA256                          |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, nincs |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs   |
| Gyorsmódú biztonsági társítás élettartama   | (Nem **kötelező**: Ha nincs megadva, az alapértelmezett értékek szerepelnek)<br>Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)    |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors * * ($True/$False; Nem **kötelező**, alapértelmezett $false, ha nincs megadva)    |
| DPD időtúllépés      | Másodperc (egész szám: min. 9/max. 3600; alapértelmezett 45 másodperc) |
|  |  |

#### <a name="important-requirements"></a>Fontos követelmények

* A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:
  * IKE titkosítási algoritmus (fő mód/1. fázis)
  * IKE integritási algoritmus (fő mód/1. fázis)
  * DH-csoport (fő mód/1. fázis)
  * IPsec titkosítási algoritmus (gyors mód/2. fázis)
  * IPsec-integritási algoritmus (gyors mód/2. fázis)
  * PFS-csoport (gyors mód/2. fázis) > * forgalmi választó (ha UsePolicyBasedTrafficSelectors van használatban)
  * AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.

* Ha a GCMAES-t IPsec titkosítási algoritmusként használja, ki kell választania ugyanazt a GCMAES algoritmust és a kulcs hosszát az IPsec-integritáshoz; például a GCMAES128 használata mindkettőhöz.

* A fenti [algoritmusok és kulcsok táblában](#table1) :
  * Az IKE a Main Mode vagy az 1. fázisnak felel meg
  * Az IPsec megfelel a gyors vagy a 2. fázisnak
  * A DH-csoport a főmódban vagy az 1. fázisban használt Diffie-Hellmen csoportot határozza meg.
  * A PFS-csoport a Diffie-Hellmen a gyors módban vagy a 2. fázisban használt csoportot adta meg

* Az IKE alapmódú SA élettartama 28 800 másodpercen belül megoldódott az Azure-beli VPN-átjárók esetében.

* Ha úgy állítja be a **UsePolicyBasedTrafficSelectors** , hogy $True egy kapcsolaton, akkor az Azure VPN-átjárót úgy konfigurálja, hogy a helyi házirend-alapú VPN-tűzfalhoz kapcsolódjon. Ha engedélyezi a PolicyBasedTrafficSelectors-t, gondoskodnia kell arról, hogy a VPN-eszköz megfelelő forgalmi választókkal rendelkezik, amelyek a helyszíni hálózat (helyi hálózati átjáró) előtagjainak az Azure Virtual Network előtagjaihoz tartozó összes kombinációját megadják, nem pedig bármely-a-any érték helyett. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   A házirend-alapú forgalmi választókkal kapcsolatos további információkért lásd: [több helyszíni házirend-alapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD időtúllépés – az alapértelmezett érték 45 másodperc az Azure VPN Gateway átjárón. Ha az időtúllépést rövidebb időszakokra állítja be, az IKE sokkal agresszívebb lesz, ami azt eredményezi, hogy a kapcsolat bizonyos példányokban le lesz választva. Előfordulhat, hogy ez nem kívánatos, ha a helyszíni helyei távolabb vannak attól az Azure-régiótól, ahol a VPN-átjáró található, vagy a fizikai hivatkozás feltétele a csomagok elvesztését vonhatja maga után. Az általános javaslat a **30 – 45** másodperc közötti időkorlát beállítása.

### <a name="diffie-hellman-groups"></a>Diffie-Hellman csoportok

A következő táblázat felsorolja az egyéni házirend által támogatott megfelelő Diffie-Hellman csoportokat:

| **Diffie-Hellman Group**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP384       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |

További részletekért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>S2S VPN IPsec/IKE-házirenddel

Ez a szakasz végigvezeti a helyek közötti VPN-kapcsolat IPsec/IKE-házirenddel való létrehozásának lépésein. Az alábbi ábrán látható módon hozza létre a-kapcsolatokat:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Helyek közötti házirend" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

Hozza létre a következő erőforrásokat, ahogy az alábbi képernyőképen is látható. A lépéseket lásd: [helyek közötti VPN-kapcsolat létrehozása](./tutorial-site-to-site-portal.md).

* **Virtuális hálózat:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN-átjáró:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Átjáró":::

* **Helyi hálózati átjáró:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Hely":::

* **Kapcsolatok:** VNet1 – Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Kapcsolat":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>2. lépés – IPsec/IKE-házirend konfigurálása a S2S VPN-kapcsolaton

Ebben a szakaszban egy IPsec/IKE-házirendet konfigurál a következő algoritmusokkal és paraméterekkel:

* IKE: AES256, SHA384, DHGroup24, DPD időkorlát 45 másodperc
* IPsec: AES256, SHA256, PFS nincs, SA élettartama 30000 másodperc és 102400000KB

1. Navigáljon a Azure Portal **VNet1toSite6** található kapcsolódási erőforráshoz. Válassza a **konfiguráció** lapot, és válassza az **Egyéni** IPSec/IKE-szabályzat lehetőséget az összes konfigurációs beállítás megjelenítéséhez. Az alábbi képernyőkép a lista alapján mutatja be a konfigurációt:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="6. hely":::

1. Ha IPsec-GCMAES használ, ugyanazt a GCMAES algoritmust és a kulcs hosszát kell használnia az IPsec-titkosítás és az integritás esetében is. Az alábbi képernyőkép például az IPsec-titkosítás és az IPsec-integritás GCMAES128 határozza meg:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES az IPsec számára":::

1. Ha az **Engedélyezés** lehetőséget választja, a **házirend-alapú forgalom kiválasztása** lehetőséget választva engedélyezheti az Azure VPN Gateway számára, hogy a fentiekben leírtak szerint kapcsolódjon a házirend alapú VPN-eszközökhöz a helyszínen.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Házirend-alapú forgalmi választó":::

1. Ha az összes beállítás ki van választva, válassza a **Mentés** lehetőséget, hogy véglegesítse a módosításokat a kapcsolódási erőforrásban. A szabályzat egy percen belül érvénybe lép.

> [!IMPORTANT]
>
> * Ha egy kapcsolathoz IPsec/IKE-házirend van megadva, az Azure VPN Gateway csak az IPsec/IKE-javaslatot küldi el vagy fogadja el az adott kapcsolaton megadott titkosítási algoritmusokkal és fő erősségekkel. Győződjön meg arról, hogy a kapcsolat a helyszíni VPN-eszközt használja, vagy fogadja el a pontos szabályzat-kombinációt, ellenkező esetben a S2S VPN-alagút nem jön létre.
>
> * A **házirend-alapú forgalom választója** és a **DPD időtúllépési** beállításai az **alapértelmezett** szabályzattal megadhatók az egyéni IPSec/IKE-házirend nélkül, ahogy az a fenti képernyőképen is látható.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet – VNet IPsec/IKE-házirenddel

Az IPsec/IKE-szabályzattal VNet és VNet közötti kapcsolat létrehozásának lépései hasonlóak egy S2S VPN-kapcsolathoz.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet-VNet szabályzat diagramja" border="false":::

1. A VNet és a VNet közötti kapcsolatok létrehozásához kövesse a [VNet – VNet-kapcsolatok létrehozása](vpn-gateway-vnet-vnet-rm-ps.md) című cikk lépéseit.

2. A lépések elvégzése után két VNet-VNet kapcsolat jelenik meg, ahogy az alábbi képernyőképen látható a VNet2GW erőforrásban:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Virtuális hálózatok közötti kapcsolatok":::

3. Navigáljon a kapcsolódási erőforráshoz, és lépjen a **konfiguráció** lapra a portálon. Az egyéni házirend-beállítások megjelenítéséhez válassza az **Egyéni** lehetőséget az **IPSec/IKE-házirendben** . Válassza ki a titkosítási algoritmusokat a megfelelő kulcs hosszával.

   A képernyőkép egy másik IPsec/IKE-házirendet mutat be a következő algoritmusokkal és paraméterekkel:
   * IKE: AES128, SHA1, DHGroup14, DPD időkorlát 45 másodperc
   * IPsec: GCMAES128, GCMAES128, PFS14, SA-élettartam 14400 másodperc & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Kapcsolatok házirendje":::

4. A **Mentés** gombra kattintva alkalmazhatja a házirend módosításait a kapcsolódási erőforráson.

5. Alkalmazza ugyanazt a házirendet a másik VNet2toVNet1-erőforrásra. Ha nem, akkor az IPsec/IKE VPN-alagút nem fog kapcsolódni a házirendek eltérése miatt.

   > [!IMPORTANT]
   > Ha egy kapcsolathoz IPsec/IKE-házirend van megadva, az Azure VPN Gateway csak az IPsec/IKE-javaslatot küldi el vagy fogadja el az adott kapcsolaton megadott titkosítási algoritmusokkal és fő erősségekkel. Győződjön meg arról, hogy mindkét kapcsolat IPsec-házirendjei azonosak, ellenkező esetben a VNet – VNet kapcsolat nem jön létre.

6. A fenti lépések elvégzése után a rendszer néhány percen belül kialakítja a kapcsolatokat, és a következő hálózati topológiával fog rendelkezni:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-házirend diagramja" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Egyéni IPsec/IKE-házirend eltávolítása egy kapcsolatban

1. Ha egyéni házirendet szeretne eltávolítani egy kapcsolatban, lépjen a kapcsolódási erőforráshoz, és lépjen a **konfiguráció** lapra, és tekintse meg az aktuális szabályzatot.

2. Válassza az **alapértelmezett** lehetőséget az **IPSec/IKE-házirend** beállításnál. Ezzel a művelettel eltávolítja a korábban megadott összes egyéni házirendet a kapcsolatban, és visszaállítja az alapértelmezett IPsec/IKE-beállításokat a következő kapcsolatban:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Szabályzat törlése":::

3. A **Mentés** gombra kattintva távolítsa el az egyéni házirendet, és állítsa vissza az alapértelmezett IPSec/IKE-beállításokat a kapcsolatban.

## <a name="next-steps"></a>Következő lépések

További részletekért lásd: [több helyszíni házirend-alapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) a házirend-alapú forgalmi választókkal kapcsolatban.