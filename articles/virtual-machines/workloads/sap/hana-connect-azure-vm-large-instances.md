---
title: Kapcsolat beállítása virtuális gépekről az Azure-ba SAP HANA (nagyméretű példányok) | Microsoft Docs
description: Kapcsolat beállítása virtuális gépekről az Azure-beli SAP HANA használatára (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9caf0a69d2f6caf23edbc35864e1aa560aaf63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673002"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure-beli virtuális gépek csatlakozása nagy méretű HANA-példányokhoz

Mi a [SAP HANA az Azure-ban (nagyméretű példányok)?](./hana-overview-architecture.md) megemlíti, hogy a HANA nagyméretű példányainak az Azure-beli SAP-alkalmazási réteggel való minimális üzembe helyezése a következőhöz hasonlít:

![Azure-VNet az Azure-ban (nagyméretű példányok) és a helyszíni SAP HANAhoz csatlakoztatva](./media/hana-overview-architecture/image1-architecture.png)

Az Azure Virtual Network oldalának alaposabban megkeresve a következőkre van szükség:

- Annak az Azure-beli virtuális hálózatnak a definíciója, amelyre telepíteni fogja az SAP-alkalmazás rétegében lévő virtuális gépeket.
- Egy alapértelmezett alhálózat definíciója az Azure Virtual Networkben, amely valójában a virtuális gépek üzembe helyezésének egyike.
- A létrehozott Azure-beli virtuális hálózatnak rendelkeznie kell legalább egy virtuálisgép-alhálózattal és egy Azure ExpressRoute virtuális hálózati átjáró-alhálózattal. Ezeket az alhálózatokat az IP-címtartományok a következő szakaszokban meghatározott módon kell kiosztani.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Azure-beli virtuális hálózat létrehozása a HANA nagyméretű példányaihoz

>[!Note]
>A HANA nagyméretű példányok Azure-beli virtuális hálózatát a Azure Resource Manager üzemi modell használatával kell létrehozni. A HANA nagyméretű példány megoldás nem támogatja a régebbi Azure-alapú üzemi modellt, amelyet gyakran a klasszikus üzemi modellnek is nevezünk.

A virtuális hálózat létrehozásához használhatja a Azure Portal, a PowerShell, az Azure-sablon vagy az Azure CLI-t. (További információ: [virtuális hálózat létrehozása a Azure Portal használatával](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)). Az alábbi példában a Azure Portal használatával létrehozott virtuális hálózatot tekintjük át.

Ha a jelen dokumentációban található **címterület** hivatkozik arra, hogy az Azure-beli virtuális hálózat által használt címtartomány engedélyezve legyen. Ez a Címterület azt a címtartományt is használja, amelyet a virtuális hálózat a BGP-útvonal propagálásához használ. Ez a **címterület** itt látható:

![A Azure Portalban megjelenített Azure-beli virtuális hálózat címterület](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Az előző példában a 10.16.0.0/16 esetében az Azure-beli virtuális hálózat a használathoz meglehetősen nagy és széles körű IP-címtartományt kapott. Ezért az ezen a virtuális hálózaton belüli további alhálózatok összes IP-címének tartománya az adott címtartomány tartományán belül lehet. Az Azure-beli virtuális hálózatok esetében általában nem ajánlott egy ilyen nagy címtartomány használata. Nézzük meg az Azure Virtual Networkben definiált alhálózatokat:

![Azure-beli virtuális hálózati alhálózatok és azok IP-címtartományok](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Megvizsgálunk egy virtuális hálózatot az első virtuálisgép-alhálózattal (itt az "alapértelmezett") és egy "GatewaySubnet" nevű alhálózatot.

A két előző ábrán a **virtuális hálózati címtartomány** az Azure virtuális gép és a virtuális hálózati átjáró **alhálózati IP-címének tartományát** is tartalmazza.

A **virtuális hálózati címtartomány** az egyes alhálózatok által használt meghatározott tartományokra korlátozható. A virtuális hálózatok virtuális hálózati **címterület** több megadott tartományként is definiálható, ahogy az itt látható:

![Azure-beli virtuális hálózati címtartomány két szóközzel](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Ebben az esetben a **virtuális hálózati címtartomány** két szóközzel van definiálva. Ugyanazok, mint az Azure-beli virtuális gép és a virtuális hálózati átjáró alhálózat IP-címének tartományában definiált IP-címtartományok. 

Bármely, a bérlői alhálózathoz (VM-alhálózatokhoz) hasonló elnevezési szabványt használhat. Az Azure-beli (nagyméretű példányok) ExpressRoute áramkörön lévő SAP HANAhoz **tartozó virtuális hálózatok esetében azonban mindig csak egy, és csak egy átjáró-alhálózat tartozhat** . Az **átjáró-alhálózat neve "GatewaySubnet" kell legyen** , hogy a ExpressRoute-átjáró megfelelően legyen elhelyezve.

> [!WARNING] 
> Fontos, hogy az átjáró alhálózatának neve mindig "GatewaySubnet" legyen.

Több virtuálisgép-alhálózatot és nem folytonos címtartományt is használhat. Ezeket a címtartományt a virtuális hálózat **virtuális hálózati címterület** fedi le. Lehetnek aggregált formában. Emellett a virtuálisgép-alhálózatok és az átjáró-alhálózatok pontos tartományának listáját is megtekinthetik.

Az alábbiakban összefoglaljuk a HANA nagyméretű példányokhoz csatlakozó Azure-beli virtuális hálózat fontos tényeit:

- Ha a HANA nagyméretű példányainak kezdeti üzembe helyezését végzi, a **virtuális hálózati címtartomány** elküldését a Microsoftnak kell elküldenie. 
- A **virtuális hálózati címtartomány** egy nagyobb tartomány, amely az Azure virtuális gép és a virtuális hálózati átjáró ALHÁLÓZATI IP-címeinek tartományait fedi le.
- Vagy több olyan tartományt is elküldhet, amely a virtuálisgép-alhálózatok IP-címeinek és a virtuális hálózati átjáró IP-címének különböző IP-címtartományok körét fedi le.
- A **virtuális hálózati címtartomány** definiálása a BGP-útválasztás propagálásához használatos.
- Az átjáró-alhálózat nevének a következőnek kell lennie: **"GatewaySubnet"**.
- A címtartomány a HANA nagyméretű példányának szűrője, amely engedélyezi vagy letiltja az Azure-ból származó HANA nagyméretű példány-egységek forgalmát. Az Azure-beli virtuális hálózat BGP-útválasztási információi, valamint a HANA nagyméretű példányának szűrésére konfigurált IP-címtartományok egyezniük kell egymással. Ellenkező esetben csatlakozási problémák léphetnek fel.
- A későbbiekben tárgyalt átjáró-alhálózatról a **virtuális hálózat a HANA nagyméretű példány ExpressRoute való csatlakoztatásáról** szóló szakaszban olvashat bővebben.



## <a name="different-ip-address-ranges-to-be-defined"></a>Különböző IP-címtartományok definiálása 

A HANA nagyméretű példányok üzembe helyezéséhez szükséges IP-címtartományok némelyike már be lett vezetve. Azonban további IP-címtartományok is fontosak. A következő IP-címtartományok egyikét sem kell elküldenie a Microsoftnak. Azonban a kezdeti központi telepítésre vonatkozó kérelem elküldése előtt meg kell határoznia őket:

- **Virtuális hálózati címtartomány**: a **virtuális hálózati címtartomány** az Azure-beli virtuális hálózatok címterület-paraméteréhez hozzárendelt IP-címtartományok. Ezek a hálózatok a SAP HANA nagyméretű példány-környezethez csatlakoznak. Javasoljuk, hogy ez a Címterület-paraméter többsoros érték legyen. Tartalmaznia kell az Azure-beli virtuális gép alhálózati tartományát és az Azure-átjáró alhálózatának tartományát. Ez az alhálózat-tartomány az előző ábrán látható. NEM lehet átfedésben a helyszíni vagy a kiszolgáló IP-készletével vagy az ER-P2P címtartományok. Hogyan érheti el ezeket az IP-címtartományt (ka) t? A vállalati hálózati csapatának vagy szolgáltatójának egy vagy több olyan IP-címtartományt kell megadnia, amely nem használatos a hálózaton belül. Az Azure-beli virtuális gép alhálózata például 10.0.1.0/24, az Azure Gateway-alhálózat alhálózata pedig 10.0.2.0/28.  Javasoljuk, hogy az Azure-beli virtuális hálózati címtartomány a következőképpen legyen definiálva: 10.0.1.0/24 és 10.0.2.0/28. Bár a címtartomány értéke összesíthető, javasoljuk, hogy az alhálózati tartományokhoz egyeztesse őket. Így véletlenül elkerülhető a nem használt IP-címtartományok újrafelhasználása a hálózatban máshol lévő nagyobb címeken belül. **A virtuális hálózati címtartomány egy IP-címtartomány. A kezdeti telepítés megkezdése előtt** el kell küldeni a Microsoftnak.
- **Azure virtuális gép alhálózatának IP-címtartomány:** Ezt az IP-címtartományt az Azure Virtual Network alhálózati paraméterhez rendeli. Ez a paraméter az Azure Virtual Networkben található, és a SAP HANA nagyméretű példány-környezethez csatlakozik. Ez az IP-címtartomány az IP-címek Azure-beli virtuális gépekhez való hozzárendelésére szolgál. A tartományon kívüli IP-címek csatlakozhatnak a SAP HANA nagyméretű példány-kiszolgáló (k) hoz. Ha szükséges, több Azure VM-alhálózatot is használhat. Az egyes Azure-beli virtuálisgép-alhálózatokhoz egy/24 CIDR-blokkot ajánlunk. Ez a címtartomány az Azure-beli virtuális hálózati címtartomány által használt értékek részét képezi. Hogyan érheti el ezt az IP-címtartományt? A vállalati hálózati csapatának vagy szolgáltatójának olyan IP-címtartományt kell megadnia, amely nincs használatban a hálózaton belül.
- **Virtuális hálózati átjáró alhálózati IP-címtartomány:** A használni kívánt funkcióktól függően az ajánlott méret a következő:
   - Ultra-Performance ExpressRoute Gateway:/26 címterület – az SKU-i típushoz szükséges.
   - A VPN és a ExpressRoute együttes használata nagy teljesítményű ExpressRoute virtuális hálózati átjáró (vagy kisebb) használatával:/27 címterület.
   - Minden más helyzet:/28 címterület. Ez a címtartomány a "VNet címtartomány" értékeiben használt értékek részét képezi. Ez a címtartomány a Microsoft számára elküldött Azure virtuális hálózati címtartomány-értékekben használt értékek részét képezi. Hogyan érheti el ezt az IP-címtartományt? A vállalati hálózati csapatának vagy szolgáltatójának olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül. 
- **Címtartomány az er-P2P kapcsolathoz:** Ez a tartomány a SAP HANA nagyméretű példányú ExpressRoute (ER) P2P-kapcsolatok IP-tartománya. Az IP-címtartományok ezen tartományának egy/29 CIDR IP-címtartomány kell lennie. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címtartományok. Ez az IP-címtartomány a ExpressRoute virtuális átjáróról a SAP HANA nagyméretű példány-kiszolgálókhoz való kapcsolat beállítására szolgál. Hogyan érheti el ezt az IP-címtartományt? A vállalati hálózati csapatának vagy szolgáltatójának olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül. **Ez a tartomány egy IP-címtartomány. A kezdeti telepítés megkezdése előtt** el kell küldeni a Microsoftnak.  
- **Kiszolgálói IP-címkészlet címtartomány:** Ez az IP-címtartomány az egyedi IP-cím és a HANA nagyméretű példány-kiszolgálók hozzárendelésére szolgál. Az ajánlott alhálózat mérete egy/24 CIDR blokk. Ha szükséges, kisebb lehet, mindössze 64 IP-címmel. Ebből a tartományból az első 30 IP-cím a Microsoft számára van fenntartva. A tartomány méretének kiválasztásakor győződjön meg arról, hogy ezt a tényt veszi figyelembe. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címekkel. Hogyan érheti el ezt az IP-címtartományt? A vállalati hálózati csapatának vagy szolgáltatójának olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül.  **Ez a tartomány egy IP-címtartomány, amelyet a Microsoftnak kell elküldenie, amikor egy kezdeti telepítést kér**.

Választható IP-címtartományok, amelyeket végül el kell küldeni a Microsoftnak:

- Ha úgy dönt, hogy a [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md) használatával engedélyezi a közvetlen útválasztást a helyszínről a nagyméretű példányok számára, egy másik/29 IP-címtartományt kell fenntartania. Ez a tartomány nem fedi át a korábban definiált többi IP-címtartományt.
- Ha úgy dönt, hogy a [ExpressRoute Global REACH](../../../expressroute/expressroute-global-reach.md) használatával engedélyezi az egyik Azure-régióban található Hana nagyméretű példány-bérlőtől egy másik Azure-régióban lévő másik Hana nagyméretű példány bérlőhöz való közvetlen útválasztást, egy másik/29 IP-címtartományt kell fenntartania. Ez a tartomány nem fedi át a korábban definiált többi IP-címtartományt.

A ExpressRoute Global Reach és a HANA nagyméretű példányok használatáról a következő dokumentumokban talál további információt:

- [SAP HANA (nagyméretű példányok) hálózati architektúrája](./hana-network-architecture.md)
- [Virtuális hálózat összekapcsolása a HANA nagyméretű példányaival](./hana-connect-vnet-express-route.md)
 
Meg kell határoznia és meg kell terveznie a korábban ismertetett IP-címtartományt. Azonban nem kell továbbítania őket a Microsoftnak. A Microsoft számára a következő IP-címtartományok szükségesek:

- Azure-beli virtuális hálózati címtartomány (ok)
- Címtartomány az ER-P2P kapcsolathoz
- Kiszolgálói IP-címkészlet címtartomány

Ha további virtuális hálózatokat ad hozzá, amelyeknek a HANA nagyméretű példányaihoz kell csatlakozniuk, el kell küldenie a Microsoft számára hozzáadott új Azure-beli virtuális hálózati címtartományt. 

A következőkben egy példa látható a különböző tartományokra és néhány olyan tartományra, amelyet a Microsoft számára kell konfigurálnia és végül nyújtani. Az Azure-beli virtuális hálózati címtartomány értéke nincs összesítve az első példában. Ez azonban az első Azure virtuálisgép-alhálózat IP-címtartomány és a virtuális hálózati átjáró alhálózati IP-címtartomány tartományában van meghatározva. 

Az Azure virtuális hálózatban több virtuálisgép-alhálózatot is használhat, ha a további virtuálisgép-alhálózatok további IP-címeinek konfigurálását és elküldését az Azure virtuális hálózati címtartomány részeként konfigurálja és elküldi.

![Az Azure-ban SAP HANA szükséges IP-címtartományok (nagyméretű példányok) minimális üzembe helyezés](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

A kép nem jeleníti meg azokat a további IP-címtartományt, amelyek szükségesek a ExpressRoute Global Reach opcionális használatához.

Összesítheti a Microsoftnak elküldött adatokat is. Ebben az esetben az Azure-beli virtuális hálózat címterület csak egy szóközt tartalmaz. A korábbi példában szereplő IP-címtartományok használatával az összesített virtuális hálózati címtartomány a következő képhez hasonlóan néz ki:

![A SAP HANA az Azure-ban (nagyméretű példányok) a minimális üzembe helyezéshez szükséges IP-címtartományok második lehetősége](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

A példában két kisebb tartomány helyett, amely az Azure-beli virtuális hálózat címterületet definiálta, egy nagyobb tartományunk van, amely 4096 IP-címet foglal magában. A Címterület ilyen nagy meghatározása a nem használt, hanem a nagy tartományokat is elhagyja. Mivel a virtuális hálózati címtartomány értéke (i) a BGP-útvonal propagálásához használatos, a használaton kívüli tartományok vagy a hálózat más részei útválasztási problémákhoz vezethetnek. A kép nem jeleníti meg azokat a további IP-címtartományt, amelyek szükségesek a ExpressRoute Global Reach opcionális használatához.

Javasoljuk, hogy a címtartomány szorosan illeszkedjen az Ön által használt alhálózati címtartomány-területhez. Ha szükséges, anélkül, hogy állásidőt kellene felvennie a virtuális hálózaton, később bármikor hozzáadhat új címterület-értékeket.
 
> [!IMPORTANT] 
> Az ER-P2P, a kiszolgálói IP-címkészlet és az Azure virtuális hálózati címtartomány minden IP-címtartomány **nem** lehet átfedésben egymással, sem a hálózatban használt többi tartománnyal. Mindegyiknek különállónak kell lennie. Ahogy a két előző ábrán is látható, nem lehet más tartomány alhálózata is. Ha átfedések történnek a tartományok között, előfordulhat, hogy az Azure-beli virtuális hálózat nem csatlakozik a ExpressRoute áramkörhöz.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>A címtartomány meghatározása utáni következő lépések
Az IP-címtartományok meghatározása után a következő lépések szükségesek:

1. Küldje el az IP-címtartományt az Azure-beli virtuális hálózati címtartomány, az ER-P2P kapcsolat és a kiszolgáló IP-címkészlet címtartomány, valamint a dokumentum elején felsorolt egyéb adatmennyiségek között. Ezen a ponton a virtuális hálózat és a virtuálisgép-alhálózatok létrehozását is megkezdheti. 
2. A Microsoft létrehoz egy ExpressRoute áramkört az Azure-előfizetés és a HANA nagyméretű példány bélyegzője között.
3. A Microsoft a nagy példányszámú bélyegzőn hozza létre a bérlői hálózatot.
4. A Microsoft az Azure-beli (nagyméretű példányok) infrastruktúra SAP HANA konfigurálja a hálózatkezelést, hogy fogadja az Azure-beli virtuális hálózati címtartomány IP-címeit, amelyek a HANA nagyméretű példányaival kommunikálnak.
5. A vásárolt Azure-beli (nagyméretű példányok) SKU-ra vonatkozó SAP HANA függően a Microsoft egy számítási egységet rendel a bérlői hálózathoz. Emellett lefoglalja és csatlakoztatja a tárolót, és telepíti az operációs rendszert (SUSE vagy Red Hat Linux). Az ezen egységek IP-címei kikerülnek a Microsoft számára elküldött kiszolgáló IP-címkészlet címtartományból.

A telepítési folyamat végén a Microsoft a következő adatforrásokat biztosítja Önnek:
- Az Azure-beli virtuális hálózatoknak a ExpressRoute-áramkörhöz való csatlakoztatásához szükséges információk, amelyek az Azure-beli virtuális hálózatokat a HANA nagyméretű példányokhoz csatlakoztatják:
     - Engedélyezési kulcs (ok)
     - ExpressRoute PeerID
- A HANA nagyméretű példányainak a ExpressRoute-áramkör és az Azure-beli virtuális hálózat létrehozása után történő elérésére vonatkozó adatok.

Megtalálhatja a HANA nagyméretű példányok összekapcsolásának sorozatát is az Azure-beli [(nagyméretű példányok) telepítő SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Az alábbi lépések többsége az adott dokumentumban található központi telepítésben látható. 

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a virtuális hálózat csatlakoztatása a HANA nagyméretű példány ExpressRoute](hana-connect-vnet-express-route.md)című témakört.
