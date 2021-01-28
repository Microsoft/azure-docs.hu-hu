---
title: 'Azure-ExpressRoute: NPM konfigurálása áramkörökhöz'
description: Felhőalapú hálózati figyelés (NPM) konfigurálása az Azure ExpressRoute-áramkörökhöz. Ez magában foglalja a ExpressRoute privát és Microsoft-partnerek általi figyelését.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 837d1f78a9fde72137383e6c22adb8870997dc9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986039"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Network Performance Monitor for ExpressRoute konfigurálása

Ez a cikk segítséget nyújt Network Performance Monitor bővítmény konfigurálásához a ExpressRoute figyeléséhez. A Network Performance Monitor (NPM) egy felhőalapú hálózatmonitorozási megoldás, amely az Azure felhőkörnyezetek és a helyszíni környezetek (fiókirodák stb.) közötti kapcsolatot monitorozza. Az NPM az Azure Monitor-naplók részét képezi. Az NPM által biztosított ExpressRoute-bővítménnyel monitorozhatja a privát vagy Microsoft-társviszony használatára konfigurált ExpressRoute-kapcsolatcsoportok hálózati teljesítményét. Az NPM ExpressRoute használatára való konfigurálásával azonosíthatja és elkerülheti a hálózati problémákat. Ez a szolgáltatás az Azure Government felhő esetében is elérhető.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A következőket teheti:

* A különböző virtuális hálózatok és a riasztások beállításával figyelheti a veszteséget és a késést

* A hálózaton lévő összes elérési út (beleértve a redundáns útvonalakat is) figyelése

* Nehezen replikálható átmeneti és időponthoz tartozó hálózati problémák elhárítása

* Segít meghatározni a hálózat egy adott szegmensét, amely a teljesítményért felelős.

* Átviteli sebesség virtuális hálózatonként (ha vannak ügynökök telepítve az egyes VNet)

* A ExpressRoute rendszerállapotának megjelenítése egy korábbi időpontból

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

A monitorozási ügynökök több kiszolgálóra is települnek, mind a helyszínen, mind az Azure-ban. Az ügynökök kommunikálnak egymással, de nem küldenek adatküldést, TCP-kézfogási csomagokat küldenek. Az ügynökök közötti kommunikáció lehetővé teszi az Azure számára a hálózati topológia hozzárendelését és a forgalom elérési útját.

1. Hozzon létre egy NPM-munkaterületet. Ez ugyanaz, mint egy Log Analytics munkaterületen.
2. A szoftveres ügynökök telepítése és konfigurálása. (Ha csak a Microsoft társközi felügyeletét szeretné figyelni, nem kell telepítenie és konfigurálnia a szoftver-ügynököket.): 
    * Telepítse a figyelési ügynököket a helyszíni kiszolgálókon és az Azure-beli virtuális gépeken (privát társításhoz).
    * Konfigurálja a figyelési ügynök kiszolgálóinak beállításait, hogy engedélyezze a figyelési ügynökök számára a kommunikációt. (Tűzfal portjainak megnyitása stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályait, hogy engedélyezze az Azure-beli virtuális gépeken telepített figyelési ügynöknek a helyszíni figyelő ügynökökkel való kommunikációt.
4. Figyelés beállítása: automatikusan felderítheti és felügyelheti, hogy mely hálózatok láthatók a NPM.

Ha már használja a Network Performance Monitort más objektumok vagy szolgáltatások figyelésére, és már rendelkezik munkaterülettel az egyik támogatott régióban, kihagyhatja az 1. lépést és a 2. lépést, és megkezdheti a konfigurálást a 3. lépéssel.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>1. lépés: munkaterület létrehozása

Hozzon létre egy olyan munkaterületet az előfizetésben, amely a virtuális hálózatok hivatkozást tartalmaz a ExpressRoute-áramkör (ek) ra.

1. A [Azure Portal](https://portal.azure.com)válassza ki azt az előfizetést, amely az ExpressRoute-áramkörhöz tartozó virtuális hálózatok rendelkezik. Ezután keresse meg a szolgáltatások listáját a **piactéren** a következőhöz: "Network Performance monitor". A visszatérésnél kattintson ide a **Network Performance monitor** lap megnyitásához.

   >[!NOTE]
   >Létrehozhat egy új munkaterületet, vagy használhat egy meglévő munkaterületet is. Ha meglévő munkaterületet szeretne használni, meg kell győződnie arról, hogy a munkaterület át lett telepítve az új lekérdezési nyelvre. [További információ...](../azure-monitor/log-query/log-query-overview.md)
   >

   ![portál](./media/how-to-npm/3.png)<br><br>
2. A fő **Network Performance monitor** lap alján kattintson a **Létrehozás** elemre a **Network Performance monitor új megoldás létrehozása** lap megnyitásához. Kattintson **log Analytics munkaterületre – válasszon ki egy munkaterületet** a munkaterületek lap megnyitásához. Kattintson az **+ Új munkaterület létrehozása** lehetőségre a munkaterület lap megnyitásához.
3. A **log Analytics munkaterület** lapon válassza az **új létrehozása** lehetőséget, majd konfigurálja a következő beállításokat:

   * Log Analytics munkaterület – írja be a munkaterület nevét.
   * Előfizetés – ha több előfizetéssel rendelkezik, válassza ki azt, amelyet hozzá szeretne rendelni az új munkaterülethez.
   * Erőforráscsoport – hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt.
   * Hely – ez a hely az ügynök kapcsolódási naplóihoz használt Storage-fiók helyének megadására szolgál.
   * Díjszabási réteg – válassza ki az árképzési szintet.
  
     >[!NOTE]
     >A ExpressRoute áramkör bárhol lehet a világon. Nem kell ugyanabban a régióban lennie, mint a munkaterületet.
     >
  
     ![munkaterület](./media/how-to-npm/4.png)<br><br>
4. Kattintson az **OK** gombra a beállítások sablon mentéséhez és telepítéséhez. A sablon érvényesítése után a **Létrehozás** gombra kattintva telepítheti a munkaterületet.
5. Miután telepítette a munkaterületet, navigáljon a létrehozott **NetworkMonitoring (Name)** erőforráshoz. Érvényesítse a beállításokat, majd kattintson a **megoldás további konfigurálást igényel**.

   ![További konfiguráció](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>2. lépés: ügynökök telepítése és konfigurálása

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: az ügynök telepítési fájljának letöltése

1. Lépjen az erőforrás **Network Performance monitor konfiguráció** lapjának **Common Settings (általános beállítások** ) lapjára. Kattintson arra az ügynökre, amely megfelel a kiszolgáló processzorának a **log Analytics-ügynökök telepítése** szakaszban, majd töltse le a telepítőfájlt.
2. Ezután másolja a **munkaterület-azonosítót** és az **elsődleges kulcsot** a Jegyzettömbbe.
3. A **log Analytics-ügynökök konfigurálása a TCP protokoll használatával történő figyeléshez** szakaszban töltse le a PowerShell-szkriptet. A PowerShell-parancsfájl segítségével megnyithatja a megfelelő tűzfal-portot a TCP-tranzakciókhoz.

   ![PowerShell-parancsprogram](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: figyelő ügynök telepítése minden figyelési kiszolgálón (minden figyelni kívánt VNET)

Javasoljuk, hogy legalább két ügynököt telepítsen a redundancia (például helyszíni, Azure virtuális hálózatok) ExpressRoute-kapcsolatok mindkét oldalán. Az ügynököt Windows Serverre kell telepíteni (2008 SP1 vagy újabb). A ExpressRoute-áramkörök figyelése a Windows asztali operációs rendszer és a Linux operációs rendszer használatával nem támogatott. Az ügynökök telepítéséhez kövesse az alábbi lépéseket:
   
  >[!NOTE]
  >A SCOM által leküldett ügynökök (beleértve az [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))-t is) nem tudják folyamatosan felderíteni a helyüket, ha az Azure-ban futnak. Javasoljuk, hogy ezeket az ügynököket ne használja az Azure virtuális hálózatok a ExpressRoute figyeléséhez.
  >

1. A **telepítő** futtatásával telepítse az ügynököt minden olyan kiszolgálóra, amelyet a ExpressRoute figyelésére kíván használni. A figyeléshez használt kiszolgáló lehet virtuális gép vagy helyszíni, és rendelkeznie kell internet-hozzáféréssel. Telepítenie kell legalább egy ügynököt a helyszínen, és egy ügynököt minden egyes, az Azure-ban figyelni kívánt hálózati szegmensen.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **licencfeltételek** oldalon olvassa el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **célmappa** lapon módosítsa vagy tartsa meg az alapértelmezett telepítési mappát, majd kattintson a **tovább** gombra.
5. Az **ügynök telepítésének beállításai** lapon összekapcsolhatja az ügynököt Azure monitor naplókhoz vagy Operations Managerhoz. Ha később szeretné konfigurálni az ügynököt, akkor hagyja üresen a beállításokat. A kiválasztás (ok) elkészítése után kattintson a **tovább** gombra.

   * Ha úgy döntött, hogy csatlakozik az **Azure log Analyticshoz**, illessze be az előző szakaszban a Jegyzettömbbe másolt **munkaterület-azonosítót** és a **munkaterület kulcsát** (elsődleges kulcs). Ezután kattintson a **tovább** gombra.

     ![AZONOSÍTÓ és kulcs](./media/how-to-npm/8.png)
   * Ha a **Operations Managerhoz** való csatlakozást választotta, a **felügyeleti csoport konfigurációja** lapon írja be a **felügyeleti csoport nevét**, a **felügyeleti kiszolgálót** és a **felügyeleti kiszolgáló portját**. Ezután kattintson a **tovább** gombra.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Az **ügynök műveleti fiókja** lapon válassza ki a **helyi** rendszerfiókot, vagy a  **tartomány vagy a helyi számítógép fiókot**. Ezután kattintson a **tovább** gombra.

     ![Fiók](./media/how-to-npm/10.png)
6. A **telepítésre kész** oldalon tekintse át a beállításokat, majd kattintson a **telepítés** gombra.
7. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.
8. Ha elkészült, a Microsoft monitoring Agent megjelenik a Vezérlőpulton. Itt áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök csatlakoztatva van-e Azure Monitor naplókhoz. Ha csatlakozik, az ügynök üzenet jelenik meg: **a Microsoft monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz**.

9. Ismételje meg ezt az eljárást minden olyan VNET esetében, amelyet figyelni kell.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: proxybeállítások konfigurálása (nem kötelező)

Ha webproxyt használ az Internet eléréséhez, a következő lépésekkel konfigurálja a proxybeállításokat a Microsoft monitoring Agenthez. Hajtsa végre ezeket a lépéseket az egyes kiszolgálókon. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, tekintse [meg a proxy beállításainak konfigurálása a Microsoft monitoring agenthez parancsfájl használatával](../azure-monitor/platform/agent-windows.md)című témakört.

Proxybeállítások konfigurálása a Microsoft monitoring Agent számára a Vezérlőpult használatával:

1. Nyissa meg a **Vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.
4. Jelölje be **a proxykiszolgáló használata** jelölőnégyzetet, és írja be az URL-címet és a portszámot, ha van ilyen. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: az ügynök kapcsolatának ellenőrzése

Könnyedén ellenőrizheti, hogy az ügynökök kommunikálnak-e.

1. A figyelési ügynököt tartalmazó kiszolgálón nyissa meg a **Vezérlőpultot**.
2. Nyissa meg a **Microsoft monitoring Agent ügynököt**.
3. Kattintson az **Azure log Analytics** fülre.
4. Az **állapot** oszlopban látnia kell, hogy az ügynök sikeresen csatlakozott a naplók Azure monitor.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: Nyissa meg a tűzfal portjait a figyelési ügynök kiszolgálóin

A TCP protokoll használatához meg kell nyitnia a tűzfal portjait, hogy biztosítsa, hogy a figyelő ügynökök kommunikálhassanak.

PowerShell-parancsfájl futtatásával hozhatja létre a Network Performance Monitor által igényelt beállításkulcsokat. Ez a parancsfájl létrehozza a Windows tűzfal szabályait is, amelyek lehetővé teszik, hogy a figyelő ügynökök TCP-kapcsolatokat hozzanak létre egymással. A parancsfájl által létrehozott beállításkulcsok határozzák meg, hogy naplózzák-e a hibakeresési naplókat, valamint a naplófájlok elérési útját. Meghatározza továbbá a kommunikációhoz használt ügynök TCP-portját is. A kulcsok értékeit a parancsfájl automatikusan beállítja. Ezeket a kulcsokat nem kell manuálisan módosítania.

Alapértelmezés szerint a 8084-es port van megnyitva. Egyéni portot is használhat a "portszám" paraméter megadásával a parancsfájlhoz. Ha azonban így tesz, ugyanazt a portot kell megadnia az összes olyan kiszolgálóhoz, amelyen futtatja a parancsfájlt.

>[!NOTE]
>A "EnableRules" PowerShell-parancsfájl csak a parancsfájlt futtató kiszolgálón konfigurálja a Windows tűzfal szabályait. Ha hálózati tűzfallal rendelkezik, győződjön meg arról, hogy az Network Performance Monitor által használt TCP-portra irányuló forgalmat engedélyezi.
>
>

Az ügynök-kiszolgálókon nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal. Futtassa a korábban letöltött [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-szkriptet. Ne használjon paramétereket.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>3. lépés: a hálózati biztonsági csoport szabályainak konfigurálása

Az Azure-ban található ügynök-kiszolgálók figyeléséhez konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályait, hogy engedélyezzék a TCP-forgalmat a NPM által a szintetikus tranzakciókhoz használt portokon. Az alapértelmezett port a 8084. Ez lehetővé teszi, hogy az Azure-beli virtuális gépen telepített figyelő ügynök kommunikáljon egy helyszíni figyelő ügynökkel.

További információ a NSG: [hálózati biztonsági csoportok](../virtual-network/tutorial-filter-network-traffic.md).

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynököket (a helyszíni kiszolgáló ügynökét és az Azure Server Agent ügynököt is), és futtassa a PowerShell-parancsfájlt, mielőtt folytatná a lépést.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>4. lépés: egyenrangú kapcsolatok felderítése

1. A **minden erőforrás** lapon lépjen a Network Performance monitor áttekintő csempére, majd kattintson a allowlisted NPM munkaterületére.

   ![NPM-munkaterület](./media/how-to-npm/npm.png)
2. Az irányítópult megjelenítéséhez kattintson a **Network Performance monitor** áttekintés csempére. Az irányítópult egy ExpressRoute-oldalt tartalmaz, amely azt mutatja, hogy a ExpressRoute "nem konfigurált állapotban" van. Kattintson a **szolgáltatás telepítése** elemre a Network Performance monitor konfiguráció lap megnyitásához.

   ![szolgáltatás beállítása](./media/how-to-npm/npm2.png)
3. A konfiguráció lapon navigáljon a bal oldali panelen található "ExpressRoute-partnerek" lapra. Ezután kattintson a **felderítés most** lehetőségre.

   ![felderítése](./media/how-to-npm/13.png)
4. A felderítés befejezésekor megjelenik egy lista, amely a következő elemeket tartalmazza:
   * Az előfizetéshez társított ExpressRoute-áramkör (ek) ben található összes Microsoft-társi kapcsolat.
   * Az előfizetéshez társított virtuális hálózatok csatlakozó összes privát társ-kapcsolat.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>5. lépés: figyelők konfigurálása

Ebben a szakaszban a figyelőket konfigurálja. Kövesse a figyelni kívánt típusú társítások lépéseit: **privát társ**-vagy **Microsoft-társak**.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

A privát kapcsolatok esetében a felderítés befejezésekor a szabályok az egyedi **áramkör neve** és a **VNet neve** szerint jelennek meg. Kezdetben ezek a szabályok le vannak tiltva.

![szabályok](./media/how-to-npm/14.png)

1. Jelölje be a következő jelölőnégyzetet: **figyelő** .
2. Jelölje be az **állapot figyelésének engedélyezése ehhez** a társításhoz jelölőnégyzetet.
3. Válassza ki a figyelési feltételeket. A küszöbértékek beírásával egyéni küszöbértékeket állíthat be az állapotadatok létrehozásához. Ha a feltétel értéke a kiválasztott hálózati/alhálózati pár esetén a kiválasztott küszöbérték fölé esik, a rendszer egy állapot-eseményt generál.
4. Az ügynökök **hozzáadása** gombra kattintva adja hozzá azokat a helyszíni kiszolgálókat, amelyekről figyelni szeretné a magánhálózati kapcsolatot. Győződjön meg arról, hogy csak azokat az ügynököket választja, amelyeken a 2. lépésben a szakaszban megadott Microsoft-szolgáltatás végpontja kapcsolódik. A helyszíni ügynököknek képesnek kell lenniük a végpont elérésére a ExpressRoute-kapcsolat használatával.
5. Mentse a beállításokat.
6. Miután engedélyezte a szabályokat, és kiválasztja a figyelni kívánt értékeket és ügynököket, várjon körülbelül 30-60 percet, amíg az értékek megkezdik a feltöltést, és a **ExpressRoute-figyelési** csempék elérhetővé válnak.

### <a name="microsoft-peering"></a>Microsoftos társhálózat-létesítés

Microsoft-társak esetén kattintson a figyelni kívánt Microsoft-társi kapcsolatok elemre, majd konfigurálja a beállításokat.

1. Jelölje be a következő jelölőnégyzetet: **figyelő** . 
2. Választható Megváltoztathatja a cél Microsoft szolgáltatás végpontját. Alapértelmezés szerint a NPM kiválasztja a Microsoft szolgáltatási végpontját célként. A NPM figyeli a helyszíni kiszolgálók kapcsolatait a ExpressRoute keresztül a cél végpontra. 
    * A cél végpont módosításához kattintson a **(Szerkesztés)** hivatkozásra a **cél:** területen, és válasszon másik Microsoft szolgáltatási cél végpontot az URL-címek listájából.
      ![cél szerkesztése](./media/how-to-npm/edit_target.png)<br>

    * Használhat egyéni URL-címet vagy IP-címet. Ez a beállítás különösen akkor fontos, ha Microsoft-partneri kapcsolatot létesít az Azure Pásti-szolgáltatásokkal, például az Azure Storage-val, az SQL-adatbázisokkal és a nyilvános IP-címeken elérhető webhelyekkel létesített kapcsolat létesítéséhez. Ehhez kattintson a hivatkozásra **(az egyéni URL-cím vagy IP-cím használata helyett)** az URL-címek listájának alján, majd adja meg az Azure-beli ExpressRoute által a Microsoft-partneri kapcsolaton keresztül csatlakozó nyilvános végpontot.
    ![Egyéni URL-cím](./media/how-to-npm/custom_url.png)<br>

    * Ha ezeket a választható beállításokat használja, győződjön meg arról, hogy csak a Microsoft szolgáltatási végpontja van kiválasztva. A végpontnak csatlakoznia kell a ExpressRoute, és elérhetőnek kell lennie a helyszíni ügynökök számára.
3. Jelölje be az **állapot figyelésének engedélyezése ehhez** a társításhoz jelölőnégyzetet.
4. Válassza ki a figyelési feltételeket. A küszöbértékek beírásával egyéni küszöbértékeket állíthat be az állapotadatok létrehozásához. Ha a feltétel értéke a kiválasztott hálózati/alhálózati pár esetén a kiválasztott küszöbérték fölé esik, a rendszer egy állapot-eseményt generál.
5. Az ügynökök **hozzáadása** gombra kattintva adja hozzá azokat a helyszíni kiszolgálókat, amelyekről figyelni szeretné a Microsoft-társi kapcsolatot. Ügyeljen arra, hogy csak azokat az ügynököket válassza, amelyek a 2. lépésben a szakaszban megadott Microsoft-szolgáltatási végpontokhoz kapcsolódnak. A helyszíni ügynököknek képesnek kell lenniük a végpont elérésére a ExpressRoute-kapcsolat használatával.
6. Mentse a beállításokat.
7. Miután engedélyezte a szabályokat, és kiválasztja a figyelni kívánt értékeket és ügynököket, várjon körülbelül 30-60 percet, amíg az értékek megkezdik a feltöltést, és a **ExpressRoute-figyelési** csempék elérhetővé válnak.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>6. lépés: figyelési csempék megtekintése

Amint megtekinti a figyelési csempéket, a ExpressRoute-áramköröket és a kapcsolatok erőforrásait a NPM figyeli. A Microsoft-partneri csempére kattintva megtekintheti a Microsoft-partneri kapcsolatok állapotát.

![csempék figyelése](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Network Performance Monitor lap

A NPM oldal tartalmaz egy oldalt a ExpressRoute, amely áttekintést nyújt a ExpressRoute-áramkörök és-társítások állapotáról.

![A képernyőképen egy irányítópult látható, amely áttekintést nyújt a ExpressRoute-áramkörök és-társítások állapotáról.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Áramkörök listája

Az összes figyelt ExpressRoute-áramkör listájának megtekintéséhez kattintson a **ExpressRoute-áramkörök** csempére. Kiválaszthat egy áramkört, megtekintheti az állapotot, a csomagok elvesztésével, a sávszélesség kihasználtságával és a késéssel kapcsolatos trend-diagramokat. A diagramok interaktívak. Kiválaszthat egy egyéni időablakot a diagramok ábrázolásához. Az egérmutatót a diagram egyik területére húzva a nagyításhoz és a részletes adatpontok megjelenítéséhez.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>A veszteség, a késés és az átviteli sebesség trendje

A sávszélesség, a késés és a veszteség diagram interaktív. A diagramok bármelyik szakaszára kinagyíthatja az egér vezérlőelemeit. A bal felső sarokban található műveletek gomb alatt a **dátum/idő** lehetőségre kattintva megtekintheti a sávszélességet, a késést és a veszteséget, valamint más intervallumokat is.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Társítások listája

Ha szeretné megtekinteni a virtuális hálózatokkal létesített kapcsolatok listáját a privát kapcsolaton keresztül, kattintson az irányítópulton található **privát társak** csempére. Itt választhatja ki a virtuális hálózati kapcsolatokat, és megtekintheti az állapotát, a csomagok elvesztésének, a sávszélesség kihasználtságának és a késésnek a trend-diagramokat.

![áramköri lista](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Csomópontok nézet

A helyi csomópontok és az Azure-beli virtuális gépek/Microsoft szolgáltatási végpontok közötti, a kiválasztott ExpressRoute-társítási kapcsolathoz tartozó kapcsolatok listájának megtekintéséhez kattintson a **csomópont-hivatkozások megtekintése** elemre. Megtekintheti az egyes hivatkozások állapotát, valamint a hozzájuk tartozó veszteségek és késések alakulását is.

![csomópontok nézet](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Áramköri topológia

Az áramköri topológia megtekintéséhez kattintson a **topológia** csempére. Ekkor megjelenik a kiválasztott kör topológiájának nézete. A topológiai diagram a hálózat minden szegmensének késését biztosítja. Minden 3. rétegbeli ugrást a diagram egy csomópontja képvisel. Egy ugrásra kattintva további részleteket talál a hop-ról.

Megnövelheti a láthatóság szintjét a helyszíni ugrások hozzáadásával, ha a csúszkát a **szűrők** között helyezi el. A csúszka balra vagy jobbra mozgatásával növelheti vagy csökkentheti a topológiai gráfban lévő ugrások számát. Az egyes szegmensek késése látható, ami lehetővé teszi a nagy késésű szegmensek gyorsabb elkülönítését a hálózaton.

![szűrők](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Áramkör részletes Topológiás nézete

Ebben a nézetben láthatók a VNet-kapcsolatok.
![részletes topológia](./media/how-to-npm/17.png)