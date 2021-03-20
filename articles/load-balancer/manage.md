---
title: Az Azure Load Balancer portálbeállításai
description: Ismerkedés a Azure Load Balancer portál beállításainak megismerésével
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89596280"
---
# <a name="azure-load-balancer-portal-settings"></a>Az Azure Load Balancer portálbeállításai

Azure Load Balancer létrehozásakor az ebben a cikkben található információk segítenek többet megtudni az egyes beállításokról és a megfelelő konfigurációról.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

A Azure Load Balancer egy hálózati terheléselosztó, amely a háttér-készletben lévő virtuálisgép-példányok között osztja el a forgalmat.

### <a name="basics"></a>Alapvető beállítások

A Load Balancer-portál létrehozása lap **alapok** lapján a következő információk láthatók:



| Beállítás |  Részletek |
| ---------- | ---------- |
| Előfizetés  | Válassza ki előfizetését. Ez a beállítás azt az előfizetést adja meg, amelyet a terheléselosztó üzembe helyezéséhez szeretne telepíteni. |
| Erőforráscsoport | Válassza az **új létrehozása** elemet, és írja be az erőforráscsoport nevét a szövegmezőbe. Ha létrehoz egy meglévő erőforráscsoportot, válassza ki azt. |
| Name | Ez a beállítás a Azure Load Balancer neve. |
| Region | Válassza ki azt az Azure-régiót, amelyet a terheléselosztó üzembe helyezéséhez szeretne telepíteni. |
| Típus | A Load balancernek két típusa van: </br> **Belső (privát)** </br> **Nyilvános (külső)**.</br> A belső terheléselosztó (ILB) egy magánhálózati IP-címen keresztül irányítja át a adatforgalmat a háttérbeli készlet tagjainak.</br> A nyilvános Load Balancer az ügyfelektől érkező kéréseket az interneten keresztül a háttér-készletre irányítja.</br> További információ a terheléselosztó [típusairól](components.md#frontend-ip-configuration-).|
| Termékváltozat  | Válassza a **standard** lehetőséget. </br> A terheléselosztó két SKU-t tartalmaz: **Alapszintű** és **standard**. </br> Az alapszintű funkció korlátozott funkcionalitással rendelkezik. </br> A **standard** használata éles számítási feladatokhoz ajánlott. </br> További információ az [SKU](skus.md)-ról. |

Ha a típusként a **nyilvános** lehetőséget választja, a következő információk láthatók:

| Beállítás |  Részletek |
| ---------- | ---------- |
| Nyilvános IP-cím | Válassza az **új létrehozása** lehetőséget a nyilvános terheléselosztó nyilvános IP-címének létrehozásához. </br> Ha meglévő nyilvános IP-címmel rendelkezik, válassza a **meglévő használata** lehetőséget.  |
| Nyilvános IP-cím | A nyilvános IP-cím neve.|
| Nyilvános IP-cím SKU | A nyilvános IP-címek két SKU-val rendelkeznek: **Alapszintű** és **standard**. </br> Az alapszintű nem támogatja a zóna-rugalmasságot és a zónákhoz tartozó attribútumokat. </br> A **standard** használata éles számítási feladatokhoz ajánlott. </br> A terheléselosztó és a nyilvános IP-cím SKU- **nak egyeznie kell**. |
| Hozzárendelés | A **statikus** beállítás automatikus a standard beállításhoz. </br> Az alapszintű nyilvános IP-címek két típussal rendelkezhetnek: **dinamikus** és **statikus**. </br> A dinamikus nyilvános IP-címek hozzárendelése a létrehozásig nem történik meg. </br> Ha törli az erőforrást, az IP-címek elvesznek. </br> Statikus IP-címek használata javasolt. |
| A rendelkezésre állási zóna | Válassza ki a **felesleges zónát** a rugalmas Load Balancer létrehozásához. </br> A Zona Load Balancer létrehozásához jelöljön ki egy **1**, **2** vagy **3** egy adott zónát. </br> A standard Load Balancer és a nyilvános IP-címek támogatásának zónái. </br> További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md). </br> Az alapszintű zónában nem jelenik meg a zóna kiválasztása. Az alapszintű Load Balancer nem támogatja a zónákat. |
| A nyilvános IPv6-cím hozzáadása | A Load Balancer támogatja a frontend **IPv6-** címeit. </br> További információ a [Load balancerről és az IPv6-](load-balancer-ipv6-overview.md) ról
| Útválasztási beállítás | Válassza a **Microsoft Network** lehetőséget. </br> A Microsoft Network azt jelenti, hogy a forgalmat a Microsoft globális hálózatán keresztül irányítja a rendszer. </br> Az Internet azt jelenti, hogy a forgalmat az internetszolgáltató hálózata irányítja át. </br> További információ az [útválasztási beállításokról](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="A terheléselosztó nyilvános létrehozása." border="true":::

Ha a **belső** típust választja, a következő információk jelennek meg:

| Beállítás |  Részletek |
| ---------- | ---------- |
| Virtuális hálózat | Az a virtuális hálózat, amelynek a belső terheléselosztó részét kell képeznie. </br> A belső terheléselosztó számára kiválasztott privát előtérbeli IP-cím ettől a virtuális hálózattól fog származni. |
| IP-cím hozzárendelése | A lehetőségek **statikusak** vagy **dinamikusak**. </br> A statikus biztosítja, hogy az IP-cím ne változzon. A dinamikus IP-cím megváltozhat. |
| A rendelkezésre állási zóna | A következő lehetőségek közül választhat: </br> **Felesleges zóna** </br> **1\. zóna** </br> **2\. zóna** </br> **3\. zóna** </br> Egy olyan terheléselosztó létrehozásához, amely a rendelkezésre állási zónák meghibásodása esetén is nagyon elérhető és rugalmas, válassza ki a **zóna redundáns** IP-címét. |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="A terheléselosztó belső létrehozása." border="true":::

## <a name="frontend-ip-configuration"></a>Előtér-IP-konfiguráció

A Azure Load Balancer IP-címe. Ez a kapcsolódási pont az ügyfelek számára. 

Egy vagy több előtér-IP-konfigurációval is rendelkezhet. Ha elvégezte a fenti alapismeretek szakaszt, már létrehozott egy előtér-felületet a terheléselosztó számára. 

Ha egy előtérbeli IP-konfigurációt szeretne hozzáadni a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portal, válassza a előtéri **IP-konfiguráció** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.

| Beállítás |  Részletek |
| ---------- | ---------- |
| Name | Az előtérbeli IP-konfiguráció neve. |
| IP-cím verziója | Annak az IP-címnek a verziószáma, amelyhez a felületének szüksége van. </br> A Load Balancer az IPv4-és IPv6-előtérbeli IP-konfigurációkat is támogatja. |
| IP-cím típusa | Az IP-típus határozza meg, hogy egyetlen IP-cím van-e társítva a előtérhöz vagy egy IP-címtartományt egy IP-előtag használatával. </br> A [nyilvános IP-előtag](../virtual-network/public-ip-address-prefix.md) segítséget nyújt, ha ismételten csatlakoznia kell ugyanahhoz a végponthoz. Az előtag gondoskodik arról, hogy elegendő port legyen megadva a SNAT-portok problémáinak támogatásához. |
| Nyilvános IP-cím (vagy előtag, ha a fenti előtagot választotta) | Válasszon ki vagy hozzon létre egy új nyilvános IP-címet (vagy előtagot) a terheléselosztó előtérbeli felületéhez. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Előtér-IP-konfiguráció létrehozása lap" border="true":::

## <a name="backend-pools"></a>Háttér-készletek

A háttér-címkészlet a háttér-készletben található virtuális hálózati adapterek IP-címeit tartalmazza. 

Ha hozzá szeretne adni egy háttér-készletet a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portal, válassza a **háttérbeli készletek**, majd a **+ Hozzáadás** lehetőséget.

| Beállítás | Részletek |
| ---------- |  ---------- |
| Name | A háttér-készlet neve. |
| Virtuális hálózat | A háttérbeli példányok virtuális hálózata. |
| IP-cím verziója | A lehetőségek **IPv4** -vagy **IPv6-alapúak**. |

Virtuális gépeket vagy virtuálisgép-méretezési csoportokat adhat hozzá a Azure Load Balancer háttér-készletéhez. Először hozza létre a virtuális gépeket vagy a virtuálisgép-méretezési csoportokat. Ezután adja hozzá őket a terheléselosztáshoz a portálon.

:::image type="content" source="./media/manage/backend.png" alt-text="Háttér-készlet létrehozása lap" border="true":::

## <a name="health-probes"></a>Állapotminták

A háttérbeli virtuális gépek vagy példányok állapotának figyelésére a rendszer állapot-mintavételt használ. Az állapot mintavételi állapota határozza meg, hogy a rendszer mikor küldjön új kapcsolatokat egy példánynak állapot-ellenőrzések alapján. 

Ha állapot-mintavételt szeretne hozzáadni a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portalon, válassza az **állapot**-mintavétel, majd a **+ Hozzáadás** lehetőséget.

| Beállítás | Részletek |
| ---------- | ---------- |
| Name | Az állapot-mintavétel neve. |
| Protokoll | A kiválasztott protokoll határozza meg, hogy a rendszer milyen típusú ellenőrzési eszközt használ annak megállapítására, hogy a háttérbeli példány (ok) kifogástalan állapotú-e. </br> A következő lehetőségek közül választhat: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Győződjön meg arról, hogy a megfelelő protokollt használja. Ez a választás az alkalmazás természetétől függ. </br> Az állapot-mintavétel és a mintavételi válaszok konfigurációja meghatározza, hogy mely háttérbeli készlet-példányok kapják meg az új folyamatokat. </br> Az állapot-mintavételek segítségével észlelhető egy alkalmazás meghibásodása egy háttér-végponton. </br> További információ az [állapot](load-balancer-custom-probe-overview.md)-mintavételről. |
| Port | Az állapot mintavételének célport. </br> Ez a beállítás a háttér-példány portja, amelyet a rendszer a példány állapotának meghatározására használ. |
| Időköz | A mintavételi kísérletek között eltelt másodpercek száma. </br> Az intervallum meghatározza, hogy az állapot-mintavétel milyen gyakran próbálja elérni a háttér-példányt. </br> Ha az 5 értéket választja, a második mintavételi kísérlet 5 másodperc és így tovább lesz elvégezve. |
| Nem kifogástalan állapot küszöbértéke | Az egymást követő mintavételi hibák száma, amelyeknek meg kell történnie, mielőtt a virtuális gép nem kifogástalannak minősül.</br> Ha a 2 lehetőséget választja, a két egymást követő hiba után nem lesz új folyamat erre a backend-példányra. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Állapot mintavételének hozzáadása." border="true":::

## <a name="load-balancing-rules"></a>Terheléselosztási szabályok

Meghatározza, hogy a rendszer hogyan ossza el a bejövő forgalmat a háttér-készleten belüli összes példányra. Egy terheléselosztási szabály egy adott előtérbeli IP-konfigurációt és portot képez le több háttérbeli IP-cím és port számára.

Ha terheléselosztó-szabályt kíván hozzáadni a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portal, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.
    
| Beállítás | Részletek |
| ---------- | ---------- |
| Name | A terheléselosztó szabályának neve. |
| IP-verzió | A lehetőségek **IPv4** -vagy **IPv6-alapúak**.  |
| Előtérbeli IP-cím | Válassza ki a frontend IP-címét. </br> A terheléselosztó előtérbeli IP-címe, amelyhez a terheléselosztó szabály hozzá van rendelve.|
| Protokoll | Azure Load Balancer egy 4. rétegbeli hálózati terheléselosztó. </br> A lehetőségek a következők: **TCP** vagy **UDP**. |
| Port | Ez a beállítás az előtér-IP-címhez társított port, amelyet ezen terheléselosztási szabály alapján kíván terjeszteni a forgalom elosztására. |
| Háttérport | Ez a beállítás a háttér-készletben lévő példányok portja, amelyhez a terheléselosztó küldi a forgalmat. Ez a beállítás lehet azonos a előtér-porttal, vagy eltérő, ha az alkalmazás rugalmassága szükséges. |
| A háttérkészlet | Az a háttérbeli készlet, amelyhez a terheléselosztó-szabályt alkalmazni szeretné. |
| Állapotadat-mintavétel | A háttér-készletben lévő példányok állapotának vizsgálatához létrehozott állapot-mintavétel. </br> Csak a kifogástalan állapotú példányok kapnak új forgalmat. |
| Munkamenet-állandóság |  A következő lehetőségek közül választhat: </br> **Nincs** </br> **Ügyfél IP-címe** </br> **Ügyfél IP-címe és protokollja**</br> </br> Az ügyfélről a háttér-készletben lévő virtuális gépre irányuló adatforgalom fenntartása. A rendszer a munkamenet időtartama alatt megőrzi a forgalmat. </br> A **none** érték azt jelenti, hogy az azonos ügyféltől érkező egymást követő kérelmeket bármely virtuális gép kezelni lehet. </br> Az **ügyfél IP-** címe határozza meg, hogy ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni. </br> Az **ügyfél IP-címe és protokollja** biztosítja, hogy ugyanazon ügyfél IP-címéről és protokollról érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni. </br> További információ a [terjesztési módokról](load-balancer-distribution-mode.md). |
| Üresjárati időkorlát (perc) | A **TCP** -vagy **http** -kapcsolatok nyitva tartása anélkül, hogy az ügyfelek megtartják az életben tartási üzeneteket |  
| TCP alaphelyzetbe állítása | A Load Balancer **TCP-visszaállítók** küldésével kiszámítható alkalmazás-viselkedést hozhat létre, amikor a kapcsolódás üresjáratban van. </br> További információ a [TCP-visszaállításról](load-balancer-tcp-reset.md)|
| Nem fix IP-cím | A lebegőpontos IP-cím az Azure terminológiai része, amely a **Direct Server Return (DSR)** néven ismert részét képezi. </br> A DSR két részből áll: <br> 1. folyamat topológiája </br> 2. egy IP-cím-leképezési séma platform szintjén. </br></br> Azure Load Balancer mindig egy DSR-folyamat topológiájában működik, hogy a lebegő IP engedélyezve van-e vagy sem. </br> Ez a művelet azt jelenti, hogy a folyamat kimenő részét mindig közvetlenül a forráshoz kell visszaírni a folyamatba. </br> A lebegőpontos IP-cím nélkül az Azure egy hagyományos terheléselosztási IP-címzési sémát, a virtuálisgép-példányok IP-címét teszi elérhetővé. </br> A lebegőpontos IP-cím engedélyezése a terheléselosztó előtérbeli IP-címére való leképezést a további rugalmasság érdekében módosítja. </br> További információ: [Azure Load Balancer több előtérbeli felülete](load-balancer-multivip-overview.md).|
| Implicit kimenő szabályok létrehozása | Válassza a **Nem** lehetőséget. </br> Alapértelmezett: **új választható disableoutboundsnat = false**  </br> Ebben az esetben a kimenő művelet ugyanazon előtér-IP-címen keresztül történik. </br></br> **Új választható disableoutboundsnat = True** </br>Ebben az esetben kimenő szabályokra van szükség a kimenő forgalomhoz. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Terheléselosztási szabály hozzáadása." border="true":::

## <a name="inbound-nat-rules"></a>Bejövő NAT-szabályok

A bejövő NAT-szabály továbbítja a bejövő forgalmat az IP-cím és a port kombinációja számára. 

A rendszer elküldje a forgalmat egy adott virtuális gépre vagy példányra a háttér-készletben. A portok továbbítása a terheléselosztással megegyező kivonatoló alapú eloszlással történik.

Ha a forgatókönyvben RDP protokoll (RDP) vagy Secure Shell (SSH) munkamenetek szükségesek egy háttérbeli készletben lévő virtuálisgép-példányok elkülönítéséhez. Több belső végpont is rendelhető ugyanahhoz a előtér-IP-címhez tartozó portokhoz. 

Az előtérbeli IP-címek használatával távolról felügyelheti a virtuális gépeket egy további Jump Box nélkül.

Ha be szeretne állítani egy bejövő NAT-szabályt a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portal, válassza a **bejövő NAT-szabályok** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.

| Beállítás | Részletek |
| ---------- | ---------- |
| Name | A bejövő NAT-szabály neve |
| Előtérbeli IP-cím | Válassza ki a frontend IP-címét. </br> A terheléselosztó előtér-IP-címe, amelyhez a bejövő NAT-szabály hozzá van rendelve. |
| IP-verzió | A lehetőségek az IPv4 és az IPv6. |
| Szolgáltatás | A Azure Load Balancer futtatott szolgáltatás típusa. </br> Az itt megadott kijelöléssel a port adatai megfelelően frissülnek. |
| Protokoll | Azure Load Balancer egy 4. rétegbeli hálózati terheléselosztó. </br> A lehetőségek a következők: TCP vagy UDP. |
| Üresjárati időkorlát (perc) | Tartsa meg a TCP-vagy HTTP-kapcsolatok megnyitását anélkül, hogy az ügyfelek számára a Keep-Alive üzenetek küldésére kellene támaszkodnia. |
| TCP alaphelyzetbe állítása | A Load Balancer képes TCP-visszaállítások küldésére, hogy egy előre jelezhető alkalmazás-viselkedést hozzon létre, amikor a kapcsolódás üresjáratban van. </br> További információ a [TCP-visszaállításról](load-balancer-tcp-reset.md) |
| Port | Ez a beállítás az előtér-IP-címhez társított port, amelyet a forgalom a bejövő NAT-szabály alapján kíván terjeszteni. |
| Cél virtuális gép | Annak a háttér-készletnek a virtuális géphez tartozó része, amelyhez ez a szabály hozzá lesz rendelve. |
| Port leképezése | Ez a beállítás az alkalmazás beállításai alapján lehet alapértelmezett vagy egyéni. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Bejövő NAT-szabály hozzáadása." border="true":::

## <a name="outbound-rules"></a>Kimenő szabályok

A terheléselosztó kimenő szabályai a háttér-készletben lévő virtuális gépek kimenő SNAT konfigurálása.

Ha szeretne hozzáadni egy kimenő szabályt a terheléselosztó számára, lépjen a terheléselosztó elemre a Azure Portal, válassza a **Kimenő szabályok**, majd a **+ Hozzáadás** lehetőséget.

| Beállítás | Részletek |
| ------- | ------ |
| Name | A Kimenő szabály neve. |
| Előtérbeli IP-cím | Válassza ki a frontend IP-címét. </br> A terheléselosztó előtér-IP-címe, amelyhez a kimenő szabályt társítani szeretné. |
| Protokoll | Azure Load Balancer egy 4. rétegbeli hálózati terheléselosztó. </br> A lehetőségek a következők: **mind**, **TCP** vagy **UDP**. |
| Üresjárati időkorlát (perc) | Tartsa meg a **TCP** -vagy **http** -kapcsolatok megnyitását anélkül, hogy az ügyfelek számára a Keep-Alive üzenetek küldésére kellene támaszkodnia. |
| TCP alaphelyzetbe állítása | A Load Balancer **TCP-visszaállítók** küldésével kiszámítható alkalmazás-viselkedést hozhat létre, amikor a kapcsolódás üresjáratban van. </br> További információ a [TCP-visszaállításról](load-balancer-tcp-reset.md) |
| A háttérkészlet | Az a háttérbeli készlet, amelyhez ezt a kimenő szabályt alkalmazni kívánja. |

### <a name="port-allocation"></a>Port kiosztása

| Beállítás | Részletek |
| ------- | ------ |
| Port kiosztása | Azt javasoljuk **, hogy válassza a kimenő portok számának manuális kiválasztása** lehetőséget.|

### <a name="outbound-ports"></a>Kimenő portok

| Beállítás | Részletek |
| ------- | ------ |
| Választás | Válassza ki a **portok száma példányt** |
| Portok száma példányban | Adja meg a **10 000** értéket. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Bejövő Kimenő szabály hozzáadása." border="true":::

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, milyen feltételekkel és beállításokkal Azure Load Balancer a Azure Portal.

* [További információ](./load-balancer-overview.md) a Azure Load Balancerról.
* Azure Load Balancer kapcsolatos [Gyakori kérdések](./load-balancer-faqs.md) .