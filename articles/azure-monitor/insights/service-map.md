---
title: Service Map megoldás használata az Azure-ban | Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen központi telepítése a Service Map a környezetben, és a számos célra használja.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: magoedte
ms.openlocfilehash: 98bf38a6c293f6d339413b5395bb32d74bcb30c0
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905721"
---
# <a name="using-service-map-solution-in-azure"></a>Service Map megoldás használata az Azure-ban

A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. A Service Map használatával a kiszolgálókat úgy tekintheti meg, ahogyan azt el szoktuk képzelni: egymással összekapcsolt rendszereket, amelyek kritikus fontosságú szolgáltatásokat tesznek elérhetővé. A Service Map megmutatja a kiszolgálók közötti kapcsolatokat, a folyamatokat, a bejövő és a kimenő kapcsolatok késéseit, valamint minden TCP-vel csatlakoztatott architektúra portjait, és ehhez konfigurációra sincs szükség, csupán telepíteni kell az ügynököt.

Ez a cikk a Service Map bevezetésének és használatának részleteit ismerteti. További információ a megoldás előfeltételeinek konfigurálásáról: [a Azure monitor for VMS áttekintésének engedélyezése](vminsights-enable-overview.md#prerequisites). Az összegzéshez a következőkre lesz szüksége:

* Log Analytics munkaterület a megoldás engedélyezéséhez.

* A Windows rendszerű számítógépre vagy Linux-kiszolgálóra telepített Log Analytics ügynök, amely ugyanazt a munkaterületet jelenti, amelyhez a megoldást engedélyezte.

* A Windows rendszerű számítógépre vagy Linux-kiszolgálóra telepített függőségi ügynök.

>[!NOTE]
>Ha már telepítette Service Map, mostantól megtekintheti a térképeit Azure Monitor for VMsban is, amely a virtuális gépek állapotának és teljesítményének figyelésére szolgáló további funkciókat is tartalmaz. További információ: [Azure monitor for VMS Overview (áttekintés](../../azure-monitor/insights/vminsights-overview.md)). Ha többet szeretne megtudni a Service Map megoldás és a Azure Monitor for VMs Térkép funkció közötti különbségekről, tekintse meg az alábbi [gyakori kérdéseket](vminsights-faq.md#how-is-azure-monitor-for-vms-map-feature-different-from-service-map).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="enable-service-map"></a>Service Map engedélyezése

1. Engedélyezze a Service Map megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) -en, vagy használja a figyelési [megoldások hozzáadása a Solutions Galleryból](solutions.md)című témakörben ismertetett eljárást.
1. [Telepítse a függőségi ügynököt a Windows rendszerre](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) , vagy [telepítse a függőségi ügynököt Linux](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) rendszeren minden olyan számítógépen, amelyen le szeretné kérni az adatgyűjtést. A függőségi ügynök képesek a közvetlen szomszédaikkal való kapcsolatok monitorozására, így lehetséges, hogy nem kell minden egyes számítógépre ügynököt telepíteni.

Service Map a Log Analytics munkaterületen található Azure Portal, és a bal oldali panelen válassza a **megoldások** lehetőséget.<br><br> ![Válassza a megoldások lehetőséget a](./media/service-map/select-solution-from-workspace.png)munkaterületen.<br> A megoldások listájában válassza a **ServiceMap (workspaceName)** lehetőséget, majd a Service Map megoldás áttekintése lapon kattintson a Service Map összefoglaló csempére.<br><br> ![Service Map összefoglaló csempe](./media/service-map/service-map-summary-tile.png)

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Használati esetek: Az informatikai folyamatok függőségének elkészítése

### <a name="discovery"></a>Felderítés

A Service Map a kiszolgálókon, folyamatokon és harmadik féltől származó szolgáltatások függőségeinek általános hivatkozási térképét hozza létre. Feltérképezi és leképezi az összes TCP-függőséget, azonosíthatja a meglepetésekkel létesített kapcsolatokat, a külső gyártótól származó rendszereket, és a hálózat hagyományos sötét területeire (például Active Directory) való függőségeket. Service Map felderíti a felügyelt rendszerek által megkísérelt hálózati kapcsolatokat, így azonosíthatja a lehetséges kiszolgálók helytelen konfigurálását, a szolgáltatás leállását és a hálózati problémákat.

### <a name="incident-management"></a>incidenskezelés

Service Map segít megelőzni a probléma elkülönítésének találgatását azáltal, hogy megmutatja, hogy a rendszerek hogyan kapcsolódnak egymáshoz, és hogyan hatnak egymásra. A sikertelen kapcsolatok azonosításán kívül segít azonosítani a helytelenül konfigurált terheléselosztó, a meglepő vagy túlzott terhelést a kritikus fontosságú szolgáltatásokban, valamint a rosszindulatú ügyfeleket, például a fejlesztői gépeket, amelyeken az éles rendszerek is beszélnek. Change Tracking használatával integrált munkafolyamatok segítségével azt is megtudhatja, hogy egy háttérrendszer vagy szolgáltatás változási eseménye az incidens kiváltó okát magyarázza-e.

### <a name="migration-assurance"></a>Áttelepítési garancia

A Service Map használatával hatékonyan megtervezheti, felgyorsíthatja és érvényesítheti az Azure-áttelepítéseket, így biztosítva, hogy semmi ne maradjon le, és ne legyen meglepő kimaradás. Felderítheti az összes olyan egymástól függő rendszert, amelyeknek együtt kell lenniük, értékelniük kell a rendszerkonfigurációt és a kapacitást, és meg kell határozniuk, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt Az áthelyezés befejezése után ellenőrizheti az ügyfelek terhelését és identitását, és ellenőrizheti, hogy a tesztelési rendszerek és az ügyfelek csatlakoznak-e. Ha az alhálózat megtervezése és a tűzfal definíciója problémákba ütközik, a Service Map Maps-ben lévő kapcsolatok sikertelenek arra a rendszerre, amelyhez kapcsolat szükséges.

### <a name="business-continuity"></a>Az üzletmenet folytonossága

Ha Azure Site Recovery használ, és segítségre van szüksége az alkalmazási környezet helyreállítási sorrendjének meghatározásához, Service Map automatikusan megmutathatja, hogy a rendszerek hogyan támaszkodnak egymásra a helyreállítási terv megbízhatóságának biztosítása érdekében. Kritikus kiszolgáló vagy csoport kiválasztásával és az ügyfelek megtekintésével azonosíthatja, hogy mely előtér-rendszerek legyenek helyreállítva a kiszolgáló visszaállítása és rendelkezésre állása után. Ezzel szemben a kritikus fontosságú kiszolgálók háttérbeli függőségeinek megtekintésével meghatározhatja, hogy mely rendszerek legyenek helyreállítva a fókuszrendszer visszaállítása előtt.

### <a name="patch-management"></a>Javítási felügyelet

Service Map fokozza a rendszerfrissítés-értékelés használatát azáltal, hogy megmutatja, hogy mely más csapatok és kiszolgálók függenek a szolgáltatástól, így előzetesen értesítheti őket, mielőtt lekéri a rendszereit a javítások elvégzésére. A Service Map javítja a javítások kezelését is, ha azt mutatja, hogy a szolgáltatások elérhetők és megfelelően csatlakoznak-e a javításuk és újraindításaik után.

## <a name="mapping-overview"></a>Leképezés áttekintése

Service Map ügynökök információt gyűjtenek az összes TCP-csatlakozású folyamatról azon a kiszolgálón, amelyen telepítve vannak, valamint az egyes folyamatok bejövő és kimenő kapcsolatainak részleteit.

A bal oldali ablaktábla listájában kiválaszthatja azokat a gépeket vagy csoportokat, amelyeken Service Map ügynökök láthatók a függőségek megjelenítéséhez egy adott időtartományon belül. A számítógép-függőségi térképek egy adott gépre összpontosítanak, és megjelenítik az összes olyan gépet, amely közvetlen TCP-ügyfél vagy-kiszolgáló.  A számítógép-csoport a kiszolgálók és függőségeik készleteit jeleníti meg.

![Service Map áttekintése](media/service-map/service-map-overview.png)

A gépek kiterjeszthetők a térképen, hogy megjelenjenek a futó folyamatok csoportjai és folyamatai az aktív hálózati kapcsolatokkal a kiválasztott időtartományban. Ha egy Service Map ügynökkel rendelkező távoli gép ki van bontva a folyamat részleteinek megjelenítésére, akkor csak azok a folyamatok jelennek meg, amelyek a fókusszal rendelkező géppel kommunikálnak. A fókusszal rendelkező géphez csatlakozó ügynök nélküli előtér-gépek száma a folyamatok azon folyamatainak bal oldalán van megadva, amelyhez csatlakoznak. Ha a fókuszban lévő gép olyan háttér-számítógéphez csatlakozik, amely nem rendelkezik ügynökkel, a háttér-kiszolgáló beletartozik egy kiszolgálói portba, és más, azonos portszámú kapcsolatokkal is rendelkezik.

Alapértelmezés szerint a Service Map Maps a függőségi adatok utolsó 30 percét jeleníti meg. A bal felső sarokban lévő idő vezérlőelemekkel lekérdezheti a térképeket a korábbi időtartományokra akár egy óráig is, hogy megmutassa, hogyan nézett ki a függőségek a múltban (például egy incidens vagy egy változás előtt). A Service Map az adattárolási munkaterületeken 30 napig, az ingyenes munkaterületeken pedig 7 napig tárolja a rendszer.

## <a name="status-badges-and-border-coloring"></a>Állapot-jelvények és szegélyek színe

A Térkép minden egyes kiszolgálójának alján az állapot-jelvények listája látható, amely a-kiszolgálóval kapcsolatos állapotinformációkat közvetít. A jelvények azt jelzik, hogy a-kiszolgáló a megoldás-integrációk egyikével kapcsolatos lényeges információkkal szolgál. A jelvényre kattintva közvetlenül a jobb oldali ablaktáblában található állapot adataihoz juthat. A jelenleg elérhető status jelvények közé tartoznak a riasztások, az ügyfélszolgálat, a módosítások, a biztonság és a frissítések.

Az állapot-jelvények súlyossága függvényében a számítógép-csomópontok szegélyei piros (kritikus), sárga (figyelmeztetés) vagy kék (tájékoztató) színűek lehetnek. A szín az állapot-jelvények legsúlyosabb állapotát jelöli. A szürke szegély olyan csomópontot jelöl, amely nem tartalmaz állapotjelzőket.

![Állapot jelvényei](media/service-map/status-badges.png)

## <a name="process-groups"></a>Feldolgozható csoportok

A feldolgozási csoportok egy közös termékhez vagy szolgáltatáshoz társított folyamatokat egyesítenek egy folyamat csoportba.  A számítógép-csomópontok kibontásakor a rendszer önálló folyamatokat jelenít meg a folyamat csoportjaival együtt.  Ha egy folyamaton belül egy folyamathoz tartozó bejövő és kimenő kapcsolat meghiúsult, akkor a kapcsolat a teljes feldolgozási csoport esetében sikertelenként jelenik meg.

## <a name="machine-groups"></a>Számítógépcsoportok

A számítógép-csoportok lehetővé teszik, hogy a térképeket a kiszolgálók körére központilag megtekintse, nem csak egy, így egyetlen térképen láthatja egy többrétegű alkalmazás vagy kiszolgálófürt összes tagját.

A felhasználók kiválaszthatják, hogy mely kiszolgálók tartoznak egy csoporthoz, és kiválasztják a csoport nevét.  Ezután megtekintheti a csoportot az összes folyamatával és kapcsolatával, vagy megtekintheti a csoport többi tagjához közvetlenül kapcsolódó folyamatokkal és kapcsolatokkal.

![Számítógépcsoport](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Számítógép-csoport létrehozása

Csoport létrehozásához válassza ki a kívánt gépet vagy gépeket a gépek listájában, és kattintson a **Hozzáadás a csoporthoz**lehetőségre.

![Csoport létrehozása](media/service-map/machine-groups-create.png)

Itt választhatja ki az **új létrehozása** lehetőséget, és megadhatja a csoport nevét.

![Csoport neve](media/service-map/machine-groups-name.png)

>[!NOTE]
>A gépi csoportok legfeljebb 10 kiszolgálóval rendelkeznek.

### <a name="viewing-a-group"></a>Csoport megtekintése

Miután létrehozott néhány csoportot, megtekintheti őket a csoportok lapon.

![Csoportok lap](media/service-map/machine-groups-tab.png)

Ezután válassza ki a csoport nevét az adott számítógépcsoport térképének megtekintéséhez.
![](media/service-map/machine-group.png) Számítógépcsoport a csoporthoz tartozó gépek a térképen fehéren jelennek meg.

A csoport kibontásakor a rendszer felsorolja a számítógépcsoport alkotó gépeket.

![Machine Group-gépek](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Szűrés folyamat szerint

A Térkép nézetet a csoport összes folyamatának és kapcsolatának, valamint a számítógép csoportra közvetlenül kapcsolódók megjelenítésének a bekapcsolásával állíthatja be.  Az alapértelmezett nézet az összes folyamat megjelenítése.  A nézet módosításához kattintson a Térkép feletti szűrő ikonra.

![Szűrő csoport](media/service-map/machine-groups-filter.png)

Ha az **összes folyamat** ki van választva, a Térkép a csoport összes számítógépén a folyamatokat és a kapcsolatokat is tartalmazza.

![Számítógép csoport minden folyamat](media/service-map/machine-groups-all.png)

Ha úgy módosítja a nézetet, hogy csak a **csoporthoz csatlakoztatott folyamatokat**jelenítse meg, a Térkép csak azokra a folyamatokra és kapcsolatokra lesz szűkítve, amelyek közvetlenül kapcsolódnak a csoport többi számítógépéhez, így egyszerűbb nézet jön létre.

![Gépi csoport szűrt folyamatai](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Gépek felvétele egy csoportba

Ha gépeket szeretne hozzáadni egy meglévő csoporthoz, jelölje be a kívánt gépek melletti jelölőnégyzeteket, majd kattintson a **Hozzáadás a csoporthoz**lehetőségre.  Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni a gépeket.
 
### <a name="removing-machines-from-a-group"></a>Gépek eltávolítása egy csoportból

A csoportok listában bontsa ki a csoport nevét a Machines csoportban lévő gépek listázásához.  Ezután kattintson az eltávolítani kívánt gép melletti három pont menüre, és válassza az **Eltávolítás**lehetőséget.

![Számítógép eltávolítása a csoportból](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Csoport eltávolítása vagy átnevezése

Kattintson a csoport neve melletti három pont menüre a csoport listában.

![Számítógép-csoport menü](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Szerepkör ikonjai

Bizonyos folyamatok adott szerepköröket szolgálnak ki a gépeken: webkiszolgálók, alkalmazáskiszolgáló, adatbázis stb. A Service Map megjegyzésekkel láthatja el a folyamatokat és a számítógépeket a szerepkörök ikonjaival, így könnyebben azonosíthatja a folyamat vagy a kiszolgáló szerepét.

| Szerepkör ikonja | Leírás |
|:--|:--|
| ![Webkiszolgáló](media/service-map/role-web-server.png) | Webkiszolgáló |
| ![App Server](media/service-map/role-application-server.png) | Alkalmazáskiszolgáló |
| ![Adatbázis-kiszolgáló](media/service-map/role-database.png) | Adatbázis-kiszolgáló |
| ![LDAP-kiszolgáló](media/service-map/role-ldap.png) | LDAP-kiszolgáló |
| ![SMB-kiszolgáló](media/service-map/role-smb.png) | SMB-kiszolgáló |

![Szerepkör ikonjai](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Sikertelen kapcsolatok

A sikertelen kapcsolatok Service Map Maps for folyamatok és számítógépek esetében jelennek meg, és szaggatott piros vonallal jelzi, hogy az ügyfélrendszer nem tud elérni egy folyamatot vagy portot. A sikertelen kapcsolatok bármely rendszerből származnak, egy telepített Service Map ügynökkel, ha ez a rendszer a sikertelen csatlakozást kísérli meg. Service Map méri ezt a folyamatot azáltal, hogy megfigyeli a kapcsolat létesítését sikertelen TCP-szoftvercsatornát. Ez a hiba a tűzfal, az ügyfél vagy a kiszolgáló helytelen konfigurációja, vagy a távoli szolgáltatás nem érhető el.

![Sikertelen kapcsolatok](media/service-map/failed-connections.png)

A sikertelen kapcsolatok ismertetése segíthet a hibaelhárításban, az áttelepítési ellenőrzésben, a biztonsági elemzésben és a teljes építészeti ismeretekben. A sikertelen kapcsolatok néha ártalmatlanok, de gyakran közvetlenül a problémára mutatnak, például egy feladatátvételi környezet, amely hirtelen elérhetetlenné válik, vagy két alkalmazási réteg nem tud kommunikálni a felhő áttelepítése után.

## <a name="client-groups"></a>Ügyfélcsoportok

Az ügyféltanúsítványok olyan mezők a térképen, amelyek nem rendelkeznek függőségi ügynökökkel. Egyetlen ügyféloldali csoport az egyes folyamatokhoz vagy gépekhez tartozó ügyfeleket jelöli.

![Ügyfélcsoportok](media/service-map/client-groups.png)

Ha meg szeretné tekinteni az ügyféloldali kiszolgálók IP-címeit, válassza ki a csoportot. A csoport tartalma megjelenik az **ügyféloldali tulajdonságok** ablaktáblán.

![Ügyfél csoportjának tulajdonságai](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Kiszolgálói portok csoportjai

A kiszolgálói portok a függőségi ügynökkel nem rendelkező kiszolgálókon lévő kiszolgálói portokat jelképező mezők. A mező tartalmazza a kiszolgáló portszámát és az adott porttal létesített kapcsolattal rendelkező kiszolgálók számát. Bontsa ki a mezőt az egyes kiszolgálók és kapcsolatok megtekintéséhez. Ha a mezőben csak egy kiszolgáló található, a rendszer a nevet vagy az IP-címet listázza.

![Kiszolgálói portok csoportjai](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Helyi menü

A kiszolgálók jobb felső sarkában található három pontra (...) kattintva megjelenik az adott kiszolgáló helyi menüje.

![Sikertelen kapcsolatok](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Kiszolgálói Térkép betöltése

A **betöltési kiszolgáló leképezése** lehetőségre kattintva új térképre juthat a kiválasztott kiszolgálóval az új fókuszként szolgáló számítógépként.

### <a name="show-self-links"></a>Önálló hivatkozások megjelenítése

Az **önálló hivatkozások megjelenítése** lehetőségre kattintva újrarajzolja a kiszolgáló csomópontját, beleértve az olyan önálló hivatkozásokat is, amelyek TCP-kapcsolatok, amelyek a kiszolgálón belüli folyamatokkal kezdődnek és végződik. Ha az önálló hivatkozások jelennek meg, a menüparancsok az **önálló hivatkozások elrejtésére**is módosulnak, így kikapcsolhatja őket.

## <a name="computer-summary"></a>Számítógép összegzése

A **számítógép összegzése** panel a kiszolgáló operációs rendszerének, függőségi számának és más megoldásokból származó adatoknak a áttekintését tartalmazza. Ilyen adatok például a teljesítmény-mérőszámok, a ügyfélszolgálati jegyek, a változások nyomon követése, a biztonság és a frissítések.

![Számítógép összegzése panel](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Számítógép és folyamat tulajdonságai

Service Map térképen való navigálás esetén kiválaszthatja a gépeket és a folyamatokat, hogy további kontextust szerezzenek a tulajdonságaik számára. A gépek információt nyújtanak a DNS-nevekről, az IPv4-címekről, a PROCESSZORról és a memóriáról, a virtuálisgép-típusról, az operációs rendszerről és a verzióról, a legutóbbi újraindításról, valamint a OMS és a Service Map ügynökök

![Számítógép tulajdonságai panel](media/service-map/machine-properties.png)

Az operációs rendszer metaadataiból adatokat gyűjthet a futó folyamatokról, beleértve a folyamat nevét, a folyamat leírását, a felhasználónevet és a tartományt (Windows rendszeren), a vállalat nevét, a termék nevét, a termék verziószámát, a munkakönyvtárat, a parancssort és a folyamatot. Kezdési idő.

![Folyamat tulajdonságai ablaktábla](media/service-map/process-properties.png)

A **folyamat összegzése** panel további információkat biztosít a folyamat kapcsolatáról, beleértve a hozzá tartozó portokat, a bejövő és kimenő kapcsolatokat, valamint a sikertelen kapcsolatokat.

![Folyamat összegzése panel](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Riasztások integrációja

A Service Map integrálható az Azure-riasztásokkal, hogy megjelenjenek a kijelölt kiszolgáló kilőtt riasztásai a kiválasztott időtartományban. A kiszolgáló egy ikont jelenít meg, ha vannak aktuális riasztások, és a **számítógép riasztások** ablaktáblája felsorolja a riasztásokat.

![Számítógép-riasztások panel](media/service-map/machine-alerts.png)

Ha engedélyezni szeretné a Service Map a releváns riasztások megjelenítéséhez, hozzon létre egy riasztási szabályt, amely egy adott számítógép számára aktiválódik. Megfelelő riasztások létrehozása:
- Adjon meg egy záradékot a számítógép szerinti csoportosításhoz (például **1 perc számítógép**-intervallummal).
- Válassza ki a riasztást a metrika mértékének alapján.

## <a name="log-events-integration"></a>Naplózási események integrációja

A Service Map a naplóbeli kereséssel integrálva megjeleníti a kijelölt kiszolgáló összes elérhető naplózási eseményének számát a kijelölt időtartományban. Az események listájának bármelyik sorára kattintva megtekintheti a naplóbeli keresést, és megtekintheti az egyes naplózási eseményeket.

![A számítógép naplójának eseményei panel](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Az ügyfélszolgálat integrációja

Ha mindkét megoldás engedélyezve van és konfigurálva van a Log Analytics munkaterületen, Service Map az IT-szolgáltatásmenedzsmenti csatoló integrációja automatikusan megtörténik. A Service Map integrációja a "Service Desk" címkével van ellátva. További információ: központilag felügyelheti a [ITSM munkaelemeit it-szolgáltatásmenedzsmenti csatoló használatával](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

A **számítógép-szolgáltatási** tábla ablaktábla a kiválasztott időtartományban lévő összes IT Service Management-eseményt listázza. A kiszolgáló egy ikont jelenít meg, ha vannak aktuális elemek, és a Machine Service Desk ablaktábla felsorolja őket.

![Machine Service Desk-ablaktábla](media/service-map/service-desk.png)

Az elemnek a csatlakoztatott ITSM-megoldásban való megnyitásához kattintson a **munkaelem megtekintése**elemre.

Ha meg szeretné tekinteni az elem részleteit a naplóbeli keresésben, kattintson a **Megjelenítés a naplóban keresés**lehetőségre.
A kapcsolódási metrikákat két új táblázatba írja a rendszer Log Analytics 

## <a name="change-tracking-integration"></a>Change Tracking integráció

Ha mindkét megoldás engedélyezve van és be van állítva a Log Analytics munkaterületen, akkor a Change Tracking Service Map integrációja automatikusan megtörténik.

A **gép Change Tracking** panel felsorolja az összes módosítást, a legutóbbi elsővel együtt, valamint egy hivatkozást, amely részletesen ismerteti a naplóbeli keresés részleteit.

![Számítógép Change Tracking panel](media/service-map/change-tracking.png)

Az alábbi képen egy olyan konfigurációváltozás-esemény részletes nézete látható, amelyet a **log Analytics megjelenítésének**kiválasztása után láthat.

![Konfigurációváltozás esemény](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Teljesítmény-integráció

A **gép teljesítménye** ablaktábla a kiválasztott kiszolgálóhoz tartozó normál teljesítménymutatókat jeleníti meg. A metrikák közé tartoznak a processzor kihasználtsága, a memóriahasználat, a küldött és fogadott hálózati bájtok, valamint a legfontosabb folyamatok listája a küldött és fogadott hálózati bájtok alapján.

![Gépi teljesítmény panel](media/service-map/machine-performance.png)

A teljesítményadatok megjelenítéséhez szükség lehet [a megfelelő log Analytics teljesítményszámlálók engedélyezésére](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Az engedélyezni kívánt számlálók:

Windows:
- Processzor (*)\\processzoridő (%)
- Memória\\%-ban előjegyzett bájtok használatban
- Hálózati adapter (*)\\elküldési sebesség (bájt/s)
- Hálózati adapter (*)\\másodpercenként fogadott bájtok száma

Linux:
- Processzor (*)\\processzoridő (%)
- Memória (*)\\%-ban használt memória
- Hálózati adapter (*)\\elküldési sebesség (bájt/s)
- Hálózati adapter (*)\\másodpercenként fogadott bájtok száma

A hálózati teljesítményadatok beszerzéséhez az Wire Data 2.0 megoldást is engedélyeznie kell a munkaterületen.
 
## <a name="security-integration"></a>Biztonsági integráció

Ha mindkét megoldás engedélyezve van és be van állítva a Log Analytics munkaterületen, akkor a Security and Audit Service Map integrációja automatikusan megtörténik.

A **számítógép biztonsági** paneljén a kiválasztott kiszolgálóhoz tartozó Security and Audit megoldás adatait jeleníti meg. A panel felsorolja a kiszolgáló összes, a kijelölt időtartományban fennálló biztonsági problémájának összegzését. A biztonsági problémák bármelyikére kattintva részletesen megtalálhatja a naplóbeli keresés részleteit.

![Számítógép biztonsági panelje](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Frissítések integrációja

Ha mindkét megoldás engedélyezve van és be van állítva a Log Analytics munkaterületen, akkor a Update Management Service Map integrációja automatikusan megtörténik.

A **számítógép frissítései** ablaktábla a kiválasztott kiszolgálóhoz tartozó Update Management megoldás adatait jeleníti meg. A panel felsorolja a kiszolgáló hiányzó frissítéseinek összegzését a kijelölt időtartományban.

![Számítógép Change Tracking panel](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Service Map számítógép-és feldolgozási leltári adatként [kereshetők](../../azure-monitor/log-query/log-query-overview.md) a log Analytics. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

A rendszer óránként létrehoz egy rekordot minden egyedi számítógéphez és folyamathoz, továbbá a folyamat vagy számítógép indításakor vagy a Service Mapba való bevezetéskor generált rekordokon kívül. Ezek a rekordok a következő táblákban található tulajdonságokkal rendelkeznek. A ServiceMapComputer_CL-események mezői és értékei a ServiceMap Azure Resource Manager API-ban lévő számítógép-erőforrás mezőire képezhetők le. A ServiceMapProcess_CL-események mezői és értékei a ServiceMap Azure Resource Manager API-ban a folyamat erőforrás mezőire mutatnak. A ResourceName_s mező megegyezik a megfelelő Resource Manager-erőforrásban található Name mezővel. 

>[!NOTE]
>A Service Map-funkciók növekedésével ezek a mezők változhatnak.

Az egyedi folyamatok és számítógépek azonosításához belsőleg generált tulajdonságok tartoznak:

- Számítógép A *ResourceId* vagy a *ResourceName_s* használatával egyedileg azonosíthatja a számítógépeket egy log Analytics munkaterületen belül.
- Folyamat: A *ResourceId* használatával egyedileg azonosíthatja a folyamatokat egy log Analytics munkaterületen belül. A *ResourceName_s* egyedi azon számítógép kontextusában, amelyen a folyamat fut (MachineResourceName_s) 

Mivel a megadott időtartományban több rekord is létezhet egy adott folyamat és számítógép esetében, a lekérdezések több rekordot is visszaadhatnak ugyanahhoz a számítógéphez vagy folyamathoz. Ha csak a legújabb rekordot szeretné felvenni, adja hozzá a következőt: "| deduplikáció ResourceId "a lekérdezéshez.

### <a name="connections"></a>Kapcsolatok

A kapcsolódási metrikák a Log Analytics-VMConnection új táblájába íródnak. Ez a táblázat a gépek kapcsolatairól nyújt információkat (bejövő és kimenő). A kapcsolódási metrikák olyan API-kkal is elérhetők, amelyek biztosítják egy adott metrika egy adott időszakra vonatkozó beszerzését.  Az "*elfogadás*egy figyelő szoftvercsatornán" típusú TCP-kapcsolatok bejövőek, míg a *Kapcsolódás*egy adott IP-címhez és porthoz a kimenő. A kapcsolatok irányát az Direction tulajdonság jelképezi, amely beállítható **bejövő** vagy **kimenő**értékre. 

A táblázatokban szereplő rekordok a függőségi ügynök által jelentett adatokból jönnek létre. Minden rekord egy egyperces időszakra vonatkozó megfigyelést jelöl. A TimeGenerated tulajdonság az időintervallum kezdetét jelzi. Minden rekord tartalmaz információt a megfelelő entitás, azaz a csatlakozás vagy a port, valamint az entitáshoz társított metrikák azonosítására. Jelenleg csak a TCP protokollt használó hálózati tevékenységek jelentik a jelentést.

A költségeket és a bonyolultságot a kapcsolati rekordok nem jelölik az egyes fizikai hálózati kapcsolatokat. A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva, amely az adott táblázatban látható.  Ez azt jelenti, hogy a *VMConnection* táblában lévő rekordok logikai csoportosítást jelölnek, nem pedig a megfigyelt egyedi fizikai kapcsolatokat. A fizikai hálózati kapcsolatok, amelyek ugyanazt az értéket használják a következő attribútumok esetében egy adott perc intervallumban, egyetlen logikai rekordba vannak összesítve a *VMConnection*-ben. 

| Tulajdonság | Leírás |
|:--|:--|
| `Direction` |A kapcsolatok iránya, az érték *bejövő* vagy *kimenő* . |
| `Machine` |A számítógép teljes tartományneve |
| `Process` |Folyamatok vagy csoportok identitása, a kapcsolatok kezdeményezése/elfogadása |
| `SourceIp` |A forrás IP-címe |
| `DestinationIp` |A cél IP-címe |
| `DestinationPort` |A célhely portszáma |
| `Protocol` |A kapcsolathoz használt protokoll.  Az értékek a *TCP*. |

A csoportosítás hatásának kiszámításához a csoportosított fizikai kapcsolatok számával kapcsolatos információk a rekord következő tulajdonságaiban vannak megadva:

| Tulajdonság | Leírás |
|:--|:--|
| `LinksEstablished` |A jelentéskészítési idő ablakában létrehozott fizikai hálózati kapcsolatok száma |
| `LinksTerminated` |A jelentéskészítési idő ablakában leállított fizikai hálózati kapcsolatok száma |
| `LinksFailed` |Azon fizikai hálózati kapcsolatok száma, amelyek sikertelenek voltak a jelentéskészítési idő ablakában. Ez az információ jelenleg csak a kimenő kapcsolatok esetében érhető el. |
| `LinksLive` |A jelentéskészítési idő ablakának végén megnyitott fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

A kapcsolatok számának mérőszámai mellett az adott logikai kapcsolatban vagy hálózati porton küldött és fogadott adatok mennyiségére vonatkozó információk is szerepelni fog a rekord alábbi tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
| `BytesSent` |A jelentéskészítési idő ablakában elküldhető bájtok teljes száma |
| `BytesReceived` |A jelentéskészítési idő ablakában fogadott bájtok teljes száma |
| `Responses` |A jelentéskészítési idő ablakában megfigyelt válaszok száma. 
| `ResponseTimeMax` |A jelentéskészítési idő ablakában megfigyelt legnagyobb válaszidő (ezredmásodpercben).  Ha nincs érték, a tulajdonság üres.|
| `ResponseTimeMin` |A jelentéskészítési idő ablakában megfigyelt legkisebb válaszidő (ezredmásodpercben).  Ha nincs érték, a tulajdonság üres.|
| `ResponseTimeSum` |A jelentéskészítési idő ablakában megfigyelt összes válaszidő (ezredmásodperc) összege.  Ha nincs érték, a tulajdonság üres|

A jelentett harmadik adattípus a válaszidő – mennyi időt vesz igénybe a hívó a kapcsolaton keresztül küldött kérések feldolgozására, és a távoli végpont által válaszol. A jelentett válaszidő az alapul szolgáló alkalmazás protokolljának valós válaszideje. A rendszer a heurisztikus használatával számítja ki a fizikai hálózati kapcsolat forrás-és cél vége közötti adatáramlás megfigyelése alapján. Elméletileg az a különbség, hogy a kérelem utolsó bájtja mikor hagyja el a küldőt, valamint azt az időpontot, amikor a válasz utolsó bájtja vissza fog érkezni. Ez a két időbélyeg a kérések és válaszok eseményeinek egy adott fizikai kapcsolatban való kiválasztására szolgál. A kettő közötti különbség az egyetlen kérelem válaszideje. 

A szolgáltatás ezen első kiadásában az algoritmus egy olyan közelítés, amely az adott hálózati kapcsolathoz használt tényleges alkalmazási protokolltól függően különböző sikereket eredményezhet. Például az aktuális megközelítés jól működik a kérelem-válasz alapú protokollok, például a HTTP (S) esetében, de nem működik egyirányú vagy üzenetsor-alapú protokollokkal.

Néhány fontos szempontot figyelembe kell venni:

1. Ha egy folyamat ugyanazon az IP-címen, de több hálózati adapteren keresztül fogad kapcsolatokat, a rendszer minden egyes csatolóhoz külön rekordot fog jelenteni. 
2. A helyettesítő karakteres IP-címmel rendelkező rekordok nem tartalmaznak tevékenységet. A szolgáltatás tartalmazza azt a tényt, hogy a gépen lévő port nyitva van a bejövő forgalom számára.
3. A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő karakteres IP-címekkel rendelkező rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord van (ugyanahhoz a folyamathoz, porthoz és protokollhoz). Ha nincs megadva helyettesítő IP-rekord, a IsWildcardBind Record tulajdonsága az adott IP-címmel "true" (igaz) értékre lesz állítva, amely jelzi, hogy a port a jelentéskészítő gép összes felületén elérhetővé válik.
4. Azok a portok, amelyek csak egy adott csatolón vannak kötve, IsWildcardBind "false" értékre vannak állítva.

#### <a name="naming-and-classification"></a>Elnevezés és besorolás

A kényelem érdekében a kapcsolatok távoli végének IP-címét a RemoteIp tulajdonság tartalmazza. A bejövő kapcsolatok esetében a RemoteIp ugyanaz, mint a SourceIp, míg a kimenő kapcsolatok esetében ugyanaz, mint a DestinationIp. A RemoteDnsCanonicalNames tulajdonság a gép által a RemoteIp számára jelentett DNS-kanonikus neveket jelöli. A RemoteDnsQuestions és a RemoteClassification tulajdonságok későbbi használatra vannak fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely

A *VMConnection* az egyes kapcsolatok távoli végére vonatkozó térinformatikai információkat is tartalmaz a rekord alábbi tulajdonságaiban: 

| Tulajdonság | Leírás |
|:--|:--|
| `RemoteCountry` |A RemoteIp üzemeltető ország/régió neve.  Például *Egyesült Államok* |
| `RemoteLatitude` |A térinformatikai szélesség.  Például *47,68* |
| `RemoteLongitude` |A térinformatikai hosszúság.  Például: *-122,12* |

#### <a name="malicious-ip"></a>Kártékony IP-cím

A *VMConnection* táblában lévő összes RemoteIp-tulajdonságot a rendszer az ismert kártékony tevékenységgel rendelkező IP-címek készletében ellenőrzi. Ha a RemoteIp rosszindulatúként van azonosítva, a következő tulajdonságok lesznek feltöltve (ezek üresek, ha az IP nem minősül rosszindulatúnak) a rekord következő tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
| `MaliciousIp` |A RemoteIp címe |
| `IndicatorThreadType` |Az észlelt veszélyforrás az alábbi értékek egyike, a *botnet*, a *C2*, a *CryptoMining*, a *Darknet*, a *DDos*, a *MaliciousUrl*, a *malware*, az adathalászat, a *proxy*, a *PUA*, a  *List*.   |
| `Description` |A megfigyelt fenyegetés leírása. |
| `TLPLevel` |A forgalmi lámpa protokoll (TLP) szintje az egyik definiált érték, a *fehér*, a *zöld*, a *sárga*és a *vörös*. |
| `Confidence` |Az értékek *0 – 100*. |
| `Severity` |Az értékek *0 – 5*, ahol az *5* a legsúlyosabb, a *0* pedig egyáltalán nem súlyos. Az alapértelmezett érték *3*.  |
| `FirstReportedDateTime` |Az első alkalommal, amikor a szolgáltató jelentette a kijelzőt. |
| `LastReportedDateTime` |A mutató a folyamat utolsó időpontjában volt látható. |
| `IsActive` |Azt jelzi, hogy a kijelzők inaktiválva vannak-e *igaz* vagy *hamis* értékkel. |
| `ReportReferenceLink` |Egy adott megfigyelhető jelentéshez kapcsolódó jelentések hivatkozásai. |
| `AdditionalInformation` |További információkat biztosít a megfigyelt fenyegetésről. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL records

A *ServiceMapComputer_CL* rendelkező rekordok a Service Map-ügynökökkel rendelkező kiszolgálók leltározási adataival rendelkeznek. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | A munkaterületen belüli gép egyedi azonosítója |
| `ResourceName_s` | A munkaterületen belüli gép egyedi azonosítója |
| `ComputerName_s` | A számítógép teljes tartományneve |
| `Ipv4Addresses_s` | A kiszolgáló IPv4-címeinek listája |
| `Ipv6Addresses_s` | A kiszolgáló IPv6-címeinek listája |
| `DnsNames_s` | DNS-nevek tömbje |
| `OperatingSystemFamily_s` | Windows vagy Linux |
| `OperatingSystemFullName_s` | Az operációs rendszer teljes neve  |
| `Bitness_s` | A gép bitszáma (32 bites vagy 64 bites)  |
| `PhysicalMemory_d` | A fizikai memória (MB) |
| `Cpus_d` | A processzorok száma |
| `CpuSpeed_d` | A processzor sebessége (MHz)|
| `VirtualizationState_s` | *ismeretlen*, *fizikai*, *virtuális*, *hypervisor* |
| `VirtualMachineType_s` | *HyperV*, *VMware*stb. |
| `VirtualMachineNativeMachineId_g` | A virtuális gép azonosítója, amelyet a hypervisor rendel hozzá |
| `VirtualMachineName_s` | A virtuális gép neve |
| `BootTime_t` | A rendszerindítási idő |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL Type records

A *ServiceMapProcess_CL* rendelkező rekordok a TCP-kapcsolattal rendelkező, Service Map ügynökökkel rendelkező kiszolgálókon tárolt folyamatokra vonatkozó leltározási adatokkal rendelkeznek. Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | A munkaterületen belüli folyamat egyedi azonosítója |
| `ResourceName_s` | Egy folyamat egyedi azonosítója azon a gépen belül, amelyen fut|
| `MachineResourceName_s` | A gép erőforrásának neve |
| `ExecutableName_s` | A folyamat végrehajtható fájljának neve |
| `StartTime_t` | A folyamat készletének kezdési ideje |
| `FirstPid_d` | A folyamat első PID-je |
| `Description_s` | A folyamat leírása |
| `CompanyName_s` | A vállalat neve |
| `InternalName_s` | A belső név |
| `ProductName_s` | A termék neve |
| `ProductVersion_s` | A termék verziószáma |
| `FileVersion_s` | A fájl verziója |
| `CommandLine_s` | A parancssor |
| `ExecutablePath _s` | A végrehajtható fájl elérési útja |
| `WorkingDirectory_s` | A munkakönyvtár |
| `UserName` | Az a fiók, amelyben a folyamat végre van hajtva |
| `UserDomain` | Az a tartomány, amelyben a folyamat végrehajtás alatt áll |

## <a name="sample-log-searches"></a>Naplókeresési minták

### <a name="list-all-known-machines"></a>Az összes ismert gép listázása

ServiceMapComputer_CL | arg_max (TimeGenerated, *) összegzése ResourceId szerint

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Az összes felügyelt számítógép fizikai memória-kapacitásának listázása.

ServiceMapComputer_CL | arg_max összegzése (TimeGenerated, *) a ResourceId alapján | projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>A számítógép nevének, DNS-címének, IP-címének és operációs rendszerének listázása.

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Az összes folyamat megkeresése az "SQL" paranccsal a parancssorban

ServiceMapProcess_CL | where CommandLine_s contains_cs "SQL" | arg_max (TimeGenerated, *) összegzése ResourceId szerint

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Számítógép (legfrissebb rekord) keresése erőforrás neve alapján

Keresés a következőben: (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | arg_max (TimeGenerated, *) összegzése ResourceId szerint

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Számítógép keresése (legfrissebb rekord) IP-cím szerint

Keresés a következőben: (ServiceMapComputer_CL) "10.229.243.232" | arg_max (TimeGenerated, *) összegzése ResourceId szerint

### <a name="list-all-known-processes-on-a-specified-machine"></a>A megadott gépen lévő összes ismert folyamat listázása

ServiceMapProcess_CL | where MachineResourceName_s = = "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | arg_max (TimeGenerated, *) összegzése ResourceId szerint

### <a name="list-all-computers-running-sql"></a>Az összes SQL-t futtató számítógép listázása

ServiceMapComputer_CL | ahol a ResourceName_s ((Keresés itt (ServiceMapProcess_CL) "\*SQL\*" | DISTINCT MachineResourceName_s)) | eltérő ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A curl összes egyedi termék-verziójának listázása az adatközpontban

ServiceMapProcess_CL | where ExecutableName_s = = "curl" | különböző ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>A CentOS-t futtató összes számítógép számítógépcsoport létrehozása

ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>A kimenő kapcsolatok összefoglalása a számítógépek csoportjából

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API

Service Map összes kiszolgáló-, folyamat-és függőségi értéke a [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/)keresztül érhető el.

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok

A Microsoft automatikusan gyűjt használatának és teljesítményének adatokat a Szolgáltatástérkép szolgáltatás használata. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a Szolgáltatástérkép szolgáltatás javítására használja. A pontos és hatékony hibaelhárítási lehetőségek biztosításához az adatok tartalmazzák a szoftver konfigurációjával kapcsolatos információkat, például az operációs rendszert és a verziót, az IP-címet, a DNS-nevet és a munkaállomás nevét. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>További lépések

További információ a [](../../azure-monitor/log-query/log-query-overview.md) log Analytics naplóbeli keresésekről Service Map által összegyűjtött adatok lekéréséhez.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik telepítését és futtatását a Service Map, ez a szakasz segítségével. Ha még mindig nem tudja megoldani a problémát, forduljon a Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Függőségi ügynök telepítési problémák

#### <a name="installer-prompts-for-a-reboot"></a>Telepítő kérni fogja a számítógép újraindítása
A függőségi ügynök *általában* nem igényel újraindítást a telepítés vagy az eltávolítás után. Egyes ritka esetekben azonban a Windows Server-telepítés folytatásához újraindítás szükséges. Ez akkor fordulhat elő, ha egy függőség, általában C++ a Microsoft vizualizáció újraterjeszthető könyvtára egy zárolt fájl miatt újraindítást igényel.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Üzenet: "nem sikerült telepíteni a függőségi ügynököt: A Visual Studio futásidejű kódtárai nem telepíthetők (code = [code_number])

A Microsoft Dependency agent a Microsoft Visual Studio modul kódtárak épül. Egy üzenetet fog kapni, ha probléma van a kódtárak telepítése során. 

A futásidejű kódtár telepítőcsomagokat %LOCALAPPDATA%\temp mappában hozzon létre naplókat. A `dd_vcredist_arch_yyyymmddhhmmss.log`fájl az, ahol az *Arch* `x86` vagy `amd64` a, a *yyyymmddhhmmss* pedig az a dátum és idő (24 órás óra), amikor a napló létrejött. A napló a probléma, amely blokkolja a telepítési részletesen ismerteti.

Hasznos lehet először telepíteni a [legújabb futásidejű kódtárakat](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) .

A következő táblázat felsorolja a kód számokat és a javasolt megoldások.

| Kód | Leírás | Megoldás: |
|:--|:--|:--|
| 0x17 | A könyvtár telepítő szükséges a Windows update, amely nem lett telepítve. | Tekintse meg a legutóbbi könyvtár installer naplójának.<br><br>Ha egy hivatkozást `Windows8.1-KB2999226-x64.msu` egy olyan vonal `Error 0x80240017: Failed to execute MSU package,` követ, amely nem rendelkezik a KB2999226 telepítéséhez szükséges előfeltételekkel. Kövesse az [univerzális C futtatókörnyezet](https://support.microsoft.com/kb/2999226) előfeltételek szakaszának útmutatásait a Windows-cikkben. Szükség lehet, futtassa a Windows Update, és indítsa újra a többször annak érdekében, hogy telepítse az előfeltételeket.<br><br>Futtassa újra a Microsoft Dependency agent telepítő. |

### <a name="post-installation-issues"></a>Telepítés utáni kapcsolatos problémák

#### <a name="server-doesnt-appear-in-service-map"></a>Kiszolgáló nem jelenik meg a Service Map

Ha a függőségi ügynök telepítése sikeres volt, de nem látja a gépet a Service Map megoldásban:
* A függőségi ügynök sikeres telepítését? Ellenőrzi, hogy ha a szolgáltatás telepítve van és fut ellenőrizheti.<br><br>
**Windows**: Keresse meg a **Microsoft függőségi ügynök**nevű szolgáltatást.
**Linux**: Keresse meg a futó folyamatot a **Microsoft-függőség-ügynöknek**.

* A [log Analytics ingyenes szintet](https://azure.microsoft.com/pricing/details/monitor/)választja? Az ingyenes csomag legfeljebb öt egyedi Service Map gépet tesz lehetővé. Minden további gép nem jelenik meg Service Mapban, még akkor sem, ha az előző öt már nem küld adatokat.

* A kiszolgáló napló-és teljesítményadatok-adatokat küld Azure Monitor naplókba? Nyissa meg az Azure Monitor\Logs, és futtassa a következő lekérdezést a számítógépen: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Jutott el a különféle eseményekre az eredmények között? Az adatok legutóbbi? Ha igen, a Log Analytics ügynök megfelelően működik, és kommunikál a munkaterülettel. Ha nem, ellenőrizze az ügynököt a gépen: [Log Analytics ügynök a Windows hibaelhárításához](../platform/agent-windows-troubleshoot.md) vagy [log Analytics ügynök a Linux rendszerhez – hibaelhárítás](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Kiszolgáló megjelenik a Szolgáltatástérkép, de nincs folyamatokat

Ha a gép Service Mapban jelenik meg, de nem rendelkezik folyamat-vagy adatforrással, az azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernel-illesztőprogram nem töltődött be. 

Győződjön meg `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` a (Windows) `/var/opt/microsoft/dependency-agent/log/service.log file` vagy (Linux) rendszerről. Az utolsó sort jeleznie kell, miért nem töltődtek be a kernelbe. Például a kernel előfordulhat, hogy nem támogatja a linuxon futó Ha frissítette a kernel.

## <a name="feedback"></a>Visszajelzés

Visszajelzést szeretne kapni a Service Mapről vagy a dokumentációról?  Látogasson el a felhasználói hangvételi [oldalra](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol javaslatot tehet a funkciókra, vagy megszavazhatja a meglévő javaslatokat.
