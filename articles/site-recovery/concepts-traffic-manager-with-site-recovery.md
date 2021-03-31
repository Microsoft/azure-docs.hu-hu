---
title: Azure-Traffic Manager Azure Site Recoverykal | Microsoft Docs
description: Ismerteti, hogyan használható az Azure Traffic Manager a Azure Site Recovery a vész-helyreállítási és-áttelepítéshez
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134951"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager az Azure Site Recovery-vel

Az Azure Traffic Manager segítségével szabályozhatja az alkalmazás-végpontok forgalmának eloszlását. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

Traffic Manager a tartománynévrendszer (DNS) használatával irányítja az ügyfelek kérelmeit a legmegfelelőbb végpontra a forgalom-útválasztási módszer és a végpontok állapota alapján. A Traffic Manager különböző [forgalom-útválasztási módszereket](../traffic-manager/traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](../traffic-manager/traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. Az ügyfelek közvetlenül a kiválasztott végponthoz kapcsolódnak. Traffic Manager nem proxy vagy átjáró, és nem látja a forgalmat az ügyfél és a szolgáltatás között.

Ez a cikk azt ismerteti, hogyan kombinálható az Azure Traffic monitor intelligens útválasztása Azure Site Recovery hatékony vész-helyreállítási és-áttelepítési képességeivel.

## <a name="on-premises-to-azure-failover"></a>Helyszíni – Azure feladatátvétel

Az első forgatókönyv esetében vegye fontolóra az **a vállalatot** , amely a helyszíni környezetben futó összes alkalmazás-infrastruktúrával rendelkezik. Az üzletmenet folytonossága és megfelelősége miatt az **a vállalat** úgy dönt, hogy az alkalmazásait Azure site Recovery használja.

Az **A vállalat** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és szeretné zökkenőmentesen átirányítani az Azure-ba irányuló forgalmat vészhelyzeti eseményre. A [Kiemelt](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom – az Azure Traffic Manager-útválasztási módszer lehetővé teszi a vállalat számára a feladatátvételi minta egyszerű megvalósítását.

A telepítés a következő:
- **Az a vállalat** létrehoz egy [Traffic Manager profilt](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- A **Kiemelt** útválasztási módszer felhasználásával az **a vállalat** két végpontot hoz létre – **elsődleges** a helyszíni és a **feladatátvételhez** az Azure-hoz. Az **elsődleges** hozzárendelés 1. prioritású, a **feladatátvétel** pedig 2. prioritású.
- Mivel az **elsődleges** végpont az Azure-on kívül van tárolva, a végpont [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpontként jön létre.
- Azure Site Recovery az Azure-webhely nem rendelkezik a feladatátvétel előtt futó virtuális gépekkel vagy alkalmazásokkal. Így a **feladatátvételi** végpont is **külső** végpontként jön létre.
- Alapértelmezés szerint a felhasználói forgalom a helyszíni alkalmazásra van irányítva, mert a végponthoz a legmagasabb prioritás tartozik. A rendszer nem irányítja át a forgalmat az Azure-ba, ha az **elsődleges** végpont kifogástalan állapotú.

![Helyszíni – Azure feladatátvétel előtt](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

Katasztrófa esetén az A vállalat elindíthat egy [feladatátvételt](site-recovery-failover.md) az Azure-ba, és helyreállíthatja az alkalmazásait az Azure-ban. Ha az Azure Traffic Manager észleli, hogy az **elsődleges** végpont már nem kifogástalan állapotú, a DNS-válaszban automatikusan a **feladatátvételi** végpontot használja, és a felhasználók az Azure-ban helyreállított alkalmazáshoz csatlakoznak.

![Helyszíni – Azure feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Az üzleti követelményektől függően az **a vállalat** magasabb vagy alacsonyabb szintű [Szondázási gyakoriságot](../traffic-manager/traffic-manager-monitoring.md) választhat a helyszíni és az Azure közötti váltáshoz egy katasztrófa esetén, és minimális állásidőt biztosít a felhasználók számára.

Ha a katasztrófa bekerül, az **a vállalat** az Azure-ból a helyszíni környezetbe ([VMware](vmware-azure-failback.md) vagy [Hyper-V](hyper-v-azure-failback.md)) való feladat-visszavételt hajthat végre Azure site Recovery használatával. Most, amikor Traffic Manager észleli, hogy az **elsődleges** végpont állapota ismét kifogástalan, automatikusan az **elsődleges** VÉGPONTot használja a DNS-válaszokban.

## <a name="on-premises-to-azure-migration"></a>Helyszíniről Azure-ba való Migrálás

A vész-helyreállítás mellett a Azure Site Recovery lehetővé teszi [Az Azure-ba való áttelepítést](migrate-overview.md)is. Az Azure Site Recovery hatékony tesztelési feladatátvételi képességeinek használatával az ügyfelek az Azure-ban is felhasználhatják az alkalmazások teljesítményét anélkül, hogy ez hatással lenne a helyszíni környezetre. Ha pedig az ügyfelek készen állnak az áttelepítésre, dönthetnek úgy, hogy a teljes munkaterheléseket együtt telepítik át, és fokozatosan méretezhetők.

Az Azure Traffic Manager [súlyozott](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) útválasztási módszerével a bejövő forgalom egy részét az Azure-ba irányíthatja, miközben a többséget a helyszíni környezetbe irányítja. Ezzel a megközelítéssel mérhető a méretezési teljesítmény, mivel tovább növelheti az Azure-hoz rendelt súlyozást, miközben egyre több számítási feladatot telepít át az Azure-ba.

Például a **B vállalat** úgy dönt, hogy fázisokban áttelepíti az alkalmazást, és az alkalmazás környezetét áthelyezi, miközben megtartja a helyszíni pihenést. A kezdeti fázisokban, amikor a környezet nagy része a helyszínen található, nagyobb súlyt rendel a helyszíni környezethez. A Traffic Manager egy végpontot ad vissza az elérhető végpontokhoz rendelt súlyok alapján.

![Helyszíni – Azure Migrálás](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Az áttelepítés során mindkét végpont aktív, és a legtöbb forgalom a helyszíni környezetre van irányítva. A Migrálás során nagyobb súlyt rendelhet hozzá a végponthoz az Azure-ban, és végül a helyszíni végpont inaktiválható az áttelepítés után.

## <a name="azure-to-azure-failover"></a>Azure-ról Azure-ra feladatátvétel

Ebben a példában tekintse meg a **C vállalatot** , amely az Azure-t futtató összes alkalmazás-infrastruktúrával rendelkezik. Az üzletmenet folytonossága és megfelelősége érdekében a **C vállalat** úgy dönt, hogy az alkalmazásait Azure site Recovery használja.

A **C vállalat** nyilvános végpontokkal rendelkező alkalmazásokat futtat, és szeretné zökkenőmentesen átirányítani a forgalmat egy másik Azure-régióba vészhelyzeti esemény esetén. A [Kiemelt](../traffic-manager/traffic-manager-configure-priority-routing-method.md) forgalom – az útválasztási módszer lehetővé teszi a **C vállalat** számára a feladatátvételi minta egyszerű megvalósítását.

A telepítés a következő:
- A **C vállalat** létrehoz egy [Traffic Manager profilt](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- A **Kiemelt** útválasztási módszer használatával a **C vállalat** két végpontot hoz létre – **elsődleges** a forrás régió (Azure Kelet-Ázsia) és a **feladatátvétel** a helyreállítási régióban (az Azure Délkelet-Ázsiában). Az **elsődleges** hozzárendelés 1. prioritású, a **feladatátvétel** pedig 2. prioritású.
- Mivel az **elsődleges** végpont az Azure-ban üzemel, a végpont lehet [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) -végpont.
- A Azure Site Recovery használatával a helyreállítási Azure-webhely nem rendelkezik a feladatátvétel előtt futó virtuális gépekkel vagy alkalmazásokkal. Így a **feladatátvételi** végpont [külső](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) végpontként is létrehozható.
- Alapértelmezés szerint a felhasználói forgalom a forrás régió (Kelet-Ázsia) alkalmazásra van irányítva, mivel a végpont a legmagasabb prioritással rendelkezik. Ha az **elsődleges** végpont kifogástalan állapotú, a rendszer nem irányítja át a forgalmat a helyreállítási régióba.

![Feladatátvétel előtt az Azure-ról az Azure-ra](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

Katasztrófa esetén a **C vállalat** elindíthatja a [feladatátvételt](azure-to-azure-tutorial-failover-failback.md) , és helyreállíthatja az alkalmazásait a helyreállítási Azure-régióban. Amikor az Azure Traffic Manager észleli, hogy az elsődleges végpont már nem kifogástalan állapotú, automatikusan a **feladatátvételi** végpontot használja a DNS-válaszban, és a felhasználók a helyreállítási Azure-régióban (Délkelet-Ázsiában) a helyreállított alkalmazáshoz csatlakoznak.

![Azure-to-Azure feladatátvétel után](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Az üzleti követelményektől függően a **C vállalat** a forrás-és a helyreállítási régiók közötti váltáshoz magasabb vagy alacsonyabb szintű [szondázás-gyakoriságot](../traffic-manager/traffic-manager-monitoring.md) is választhat, és minimális állásidőt biztosít a felhasználók számára.

A katasztrófa betartása esetén a **C vállalat** a helyreállítási Azure-régióból a Azure site Recovery használatával visszaállíthatja a forrást az Azure-régióba. Most, amikor Traffic Manager észleli, hogy az **elsődleges** végpont állapota ismét kifogástalan, automatikusan az **elsődleges** VÉGPONTot használja a DNS-válaszokban.

## <a name="protecting-multi-region-enterprise-applications"></a>Több régióból álló vállalati alkalmazások védelme

A globális vállalatok gyakran javítják az ügyfelek élményét azáltal, hogy az alkalmazásaikat a regionális igények kiszolgálására szabják. A honosítás és a késés csökkentése az alkalmazás-infrastruktúra régiók közötti felosztásához vezethet. A vállalatokat a regionális adattörvények is kötik bizonyos területeken, és dönthetnek úgy, hogy az alkalmazás-infrastruktúrájuk részét a regionális határokon belül elkülönítik.  

Vegyünk egy példát arra, hogy a **D vállalat** feldarabolta az alkalmazás-végpontokat, hogy külön szolgáljanak Németország és a világ többi részén. A **D vállalat** az Azure Traffic Manager [földrajzi](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) útválasztási módszerét használja a beállításához. A Németországból származó összes forgalmat az 1. **végpontra** irányítja, és a Németországon kívüli forgalom a **2. végpontra** irányul.

Ennek a beállításnak a problémája, hogy ha az **1. végpont** bármilyen okból leáll, nincs átirányítva a **2-es végpontra** irányuló forgalom. A Németországból származó forgalmat továbbra is az **1. végpontra** irányítja a rendszer, függetlenül a végpont állapottól, így a német felhasználók nem férhetnek hozzá a **vállalat D** alkalmazásához. Hasonlóképpen, ha a **2. végpont** offline állapotba kerül, az **1-es végpontra** irányuló forgalom átirányítása nem történik meg.

![Több régióból álló alkalmazás előtte](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

A probléma elkerüléséhez és az alkalmazások rugalmasságának biztosításához a **D vállalat** [beágyazott Traffic Manager profilokat](../traffic-manager/traffic-manager-nested-profiles.md) használ Azure site Recoveryokkal. Beágyazott profilok telepítésekor a rendszer nem az egyes végpontokra, hanem más Traffic Manager-profilokra irányítja át a forgalmat. A telepítés a következőképpen működik:
- Ahelyett, hogy a földrajzi útválasztást egyéni végpontokkal használja, a **D vállalat** földrajzi útválasztást használ Traffic Manager profilokkal.
- Minden gyermek Traffic Manager profil elsődleges és helyreállítási végponttal rendelkező **prioritási** útválasztást használ, így a **prioritás** -útválasztást a **földrajzi** útválasztáson belül ágyazza be.
- Az alkalmazások rugalmasságának engedélyezéséhez minden egyes munkaterhelés-eloszlás Azure Site Recoveryt használ a helyreállítási régióba való feladatátvételhez vészhelyzeti esemény esetén.
- Ha a szülő Traffic Manager DNS-lekérdezést kap, a rendszer átirányítja a megfelelő alárendelt Traffic Managerra, amely egy elérhető végponttal válaszol a lekérdezésre.

![Több régióból álló alkalmazás](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Ha például a közép-németországi végpont meghibásodik, az alkalmazás gyorsan visszaállítható az Északkelet-németországi régióba. Az új végpont a Németországból származó forgalmat minimális állásidővel kezeli a felhasználók számára. A Nyugat-európai végpontok hasonló módon kezelhetők az alkalmazás számítási feladatainak észak-európai rendszerbe való helyreállításával, az Azure Traffic Manager a DNS-átirányítások az elérhető végpontra való kezelésével.

A fenti beállítás kiterjeszthető úgy, hogy a szükséges számú régiót és végpont-kombinációt is tartalmazza. A Traffic Manager legfeljebb 10 szinten beágyazott profilt tesz lehetővé, és nem engedélyezi a hurkokat a beágyazott konfiguráción belül.

## <a name="recovery-time-objective-rto-considerations"></a>A helyreállítási időre vonatkozó célkitűzés (RTO) szempontjai

A legtöbb szervezetben a DNS-rekordok hozzáadását vagy módosítását egy különálló csapat vagy a szervezeten kívüli személy kezeli. Ez a feladat a DNS-rekordok nagy kihívást jelentő megváltoztatását teszi lehetővé. A DNS-infrastruktúrát kezelő más csapatok vagy szervezetek DNS-rekordjainak frissítéséhez szükséges idő a szervezettől a szervezetig változhat, és hatással van az alkalmazás RTO.

Traffic Manager használatával frontload a DNS-frissítésekhez szükséges munkát. A tényleges feladatátvétel időpontjában nem szükséges manuális vagy parancsfájl nélküli művelet. Ez a megközelítés segít a gyors váltásban (és ezáltal a RTO csökkentésében), valamint a katasztrófák miatti, költségesebb DNS-változási hibák elkerülésében. A Traffic Manager még a feladat-visszavételi lépés is automatizált, amelyet egyébként külön kell felügyelni.

Ha az alapszintű vagy a gyors intervallumú állapot-ellenőrzéseken keresztül állítja be a megfelelő [Szondázási időközt](../traffic-manager/traffic-manager-monitoring.md) , a feladatátvétel során jelentősen lecsökkentheti a RTO, és csökkentheti a felhasználók állásidő

Emellett optimalizálhatja a Traffic Manager profilhoz tartozó DNS-élettartam (TTL) értékét is. Az élettartam az az érték, amelynek a DNS-bejegyzéseit egy ügyfél gyorsítótárazza. Egy rekord esetében a DNS nem kérdezhető le kétszer az TTL-tartományon belül. Minden DNS-rekordhoz hozzá van rendelve egy TTL. Ennek az értéknek a csökkentése nagyobb DNS-lekérdezéseket eredményez Traffic Manager de csökkentheti a RTO a leállások gyorsabb felfedezésével.

Az ügyfél által tapasztalt TTL nem növekszik, ha a DNS-feloldók száma az ügyfél és a mérvadó DNS-kiszolgáló között növekszik. A DNS-feloldók "Count Down" az ÉLETTARTAMot, és csak a rekord gyorsítótárazása óta eltelt időt tükröző TTL-értéket továbbítanak. Ez biztosítja, hogy a DNS-rekord a TTL után frissül az ügyfélen, függetlenül attól, hogy hány DNS-feloldó van a láncban.

## <a name="next-steps"></a>Következő lépések
- További információ a Traffic Manager [útválasztási módszerekről](../traffic-manager/traffic-manager-routing-methods.md).
- További információ a [beágyazott Traffic Manager-profilokról](../traffic-manager/traffic-manager-nested-profiles.md).
- További információ a [végpontok figyeléséről](../traffic-manager/traffic-manager-monitoring.md).
- További információ az alkalmazások feladatátvételének automatizálására szolgáló [helyreállítási tervekről](site-recovery-create-recovery-plans.md) .
