---
title: Alkalmazások függőségeinek megtekintése Azure Monitor for VMs (előzetes verzió) | Microsoft Docs
description: A Térkép a Azure Monitor for VMs egyik funkciója. A szolgáltatás automatikusan feltérképezi az alkalmazás-összetevőket Windows és Linux rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen ismerteti, hogyan használhatja a Térkép funkciót különböző helyzetekben.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: f886ce4ed1b9396cf90bf98626e0cfd2424b2eac
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553778"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Az alkalmazás-összetevők megismeréséhez használja a Azure Monitor for VMs (előzetes verzió) funkcióját
Azure Monitor for VMs a felderített alkalmazás-összetevőket megtekintheti az Azure-ban vagy a környezetben futó Windows-és Linux-alapú virtuális gépeken (VM-EK). A virtuális gépek két módon figyelhetők meg. A térképeket közvetlenül egy virtuális gépről tekintheti meg, vagy megtekintheti az Azure Monitor térképét, hogy megjelenjenek-e az összetevők a virtuális gépek csoportjai között. Ez a cikk segítséget nyújt a két megtekintési módszer és a Térkép funkció használatának megismerésében. 

További információ a Azure Monitor for VMs konfigurálásáról: [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>A térképes élmény bemutatása
A térképes élmény megismerése előtt meg kell ismernie, hogyan jeleníti meg és jeleníti meg az információkat. Függetlenül attól, hogy a Térkép funkciót közvetlenül egy virtuális gépről vagy Azure Monitorról választja-e ki, a Térkép funkció egységes felhasználói élményt nyújt. Az egyetlen különbség, hogy a Azure Monitortól kezdve egy Térkép megjeleníti a többrétegű alkalmazások vagy fürtök összes tagját.

A Térkép funkció megjeleníti a virtuális gépek függőségeit a következőket tartalmazó futó folyamatok felfedésével: 

- A kiszolgálók közötti aktív hálózati kapcsolatok.
- A bejövő és a kimenő kapcsolatok késése.
- Portok bármely TCP-kapcsolattal rendelkező architektúrán egy adott időtartományon belül.  
 
Bontsa ki a virtuális gépet, és jelenítse meg a folyamat részleteit, és csak a virtuális géppel kommunikáló folyamatokat. Az ügyfél csoport a virtuális géphez csatlakozó előtér-ügyfelek számát jeleníti meg. A kiszolgáló – port csoportok megjelenítik azon háttér-kiszolgálók számát, amelyekhez a virtuális gép csatlakozik. Bontsa ki a kiszolgáló-port csoportot, hogy megtekintse a porton keresztül csatlakozó kiszolgálók részletes listáját.  

Ha kiválasztja a virtuális gépet, a jobb oldali **Tulajdonságok** ablaktábla megjeleníti a virtuális gép tulajdonságait. A tulajdonságok közé tartoznak az operációs rendszer által jelentett rendszerinformációk, az Azure-beli virtuális gép tulajdonságai, valamint a felderített kapcsolatokat összefoglaló perecdiagram. 

![A Tulajdonságok panel](./media/vminsights-maps/properties-pane-01.png)

A panel jobb oldalán válassza a **naplózási események** lehetőséget, hogy megjelenjenek azon adatlista, amelyet a virtuális gép a Azure monitorba küld. Ezek az adatlekérdezések is elérhetők.  Válassza ki bármelyik bejegyzéstípust a **naplók** lap megnyitásához, ahol az adott bejegyzéstípus eredményei láthatók. A virtuális gépre szűrt előre konfigurált lekérdezést is láthat.  

![A naplózási események panel](./media/vminsights-maps/properties-pane-logs-01.png)

Zárjuk be a **naplók** lapot, és térjen vissza a **Tulajdonságok** ablaktáblához. Itt a **riasztások** elemre kattintva megtekintheti a virtuális gépek állapotának feltételeivel kapcsolatos riasztásokat. A Map szolgáltatás integrálva van az Azure-riasztásokkal, hogy megjelenjenek a kiválasztott időtartományban a kiválasztott kiszolgálóhoz tartozó riasztások. A kiszolgáló megjeleníti az aktuális riasztások ikonját, és a **számítógép riasztások** ablaktáblája felsorolja a riasztásokat. 

![A riasztások panel](./media/vminsights-maps/properties-pane-alerts-01.png)

Ahhoz, hogy a Térkép funkció megjelenjen a kapcsolódó riasztások, hozzon létre egy riasztási szabályt, amely egy adott számítógépre vonatkozik:

- Adjon meg egy záradékot a riasztások számítógép szerinti csoportosításához (például **1 perc számítógép-intervallummal**).
- A riasztás alapja egy metrika.

Az Azure-riasztásokkal és a riasztási szabályok létrehozásával kapcsolatos további információkért tekintse [meg a Azure monitor egyesített riasztásait](../../azure-monitor/platform/alerts-overview.md)ismertető témakört.

A jobb felső sarokban a **Jelmagyarázat** lehetőség a térképen lévő szimbólumokat és szerepköröket ismerteti. Ha közelebbről szeretné megtekinteni a térképet, és a körére helyezi, használja a jobb alsó sarokban található Nagyítási vezérlőket. Megadhatja a nagyítási szintet, és a térképet az oldal méretéhez is elvégezheti.  

## <a name="connection-metrics"></a>Kapcsolatok metrikái
A **kapcsolatok** ablaktábla a virtuális gép által a TCP-porton keresztül kiválasztott kapcsolat normál metrikáit jeleníti meg. A metrikák közé tartoznak a válaszidő, a kérelmek percenkénti száma, a forgalom átviteli sebessége és a hivatkozások.  

![Hálózati kapcsolati diagramok a kapcsolatok ablaktáblán](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Sikertelen kapcsolatok
A Térkép megjeleníti a folyamatok és számítógépek sikertelen kapcsolatait. A szaggatott piros vonal azt jelzi, hogy az ügyfélrendszer nem tud elérni egy folyamatot vagy portot. A függőségi ügynököt használó rendszerek esetében az ügynök nem tud csatlakozni a sikertelen csatlakozási kísérletekhez. A Térkép funkció figyeli a folyamatot a kapcsolat létesítését nem teljesítő TCP-szoftvercsatornák megfigyelésével. Ez a hiba a tűzfal, az ügyfél vagy a kiszolgáló helytelen konfigurációja, vagy egy nem elérhető távoli szolgáltatás miatt eredményezhet.

![Sikertelen a csatlakoztatás a térképen](./media/vminsights-maps/map-group-failed-connection-01.png)

A sikertelen kapcsolatok ismertetése segíthet a hibák megoldásában, az áttelepítés ellenőrzésében, a biztonság elemzésében és a szolgáltatás általános architektúrájának megismerésében. A sikertelen kapcsolatok néha ártalmatlanok, de gyakran problémára mutatnak. Előfordulhat, hogy a kapcsolatok sikertelenek lehetnek, például ha egy feladatátvételi környezet hirtelen elérhetetlenné válik, vagy ha két alkalmazási réteg nem tud kommunikálni egymással a felhő áttelepítése után.

### <a name="client-groups"></a>Ügyféloldali csoportok
A térképen az ügyfélalkalmazások a leképezett számítógéphez csatlakozó ügyfélszámítógépeket jelölik. Egyetlen ügyféloldali csoport az egyes folyamatokhoz vagy gépekhez tartozó ügyfeleket jelöli.

![Egy ügyféloldali csoport a térképen](./media/vminsights-maps/map-group-client-groups-01.png)

Ha meg szeretné tekinteni a megfigyelt ügyfeleket és az ügyféloldali rendszerek IP-címeit, válassza ki a csoportot. A csoport tartalma alább látható.  

![Az ügyfél-csoport IP-címeinek listája a térképen](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Ha a csoport figyelt és nem figyelt ügyfeleket tartalmaz, kiválaszthatja a csoport perec-diagramjának megfelelő szakaszát az ügyfelek szűréséhez.

### <a name="server-port-groups"></a>Kiszolgáló – portok csoportjai
A kiszolgálóoldali csoportok a leképezett gépről bejövő kapcsolattal rendelkező kiszolgálók portjait jelölik. A csoport tartalmazza a kiszolgáló portszámát és az adott porttal létesített kapcsolattal rendelkező kiszolgálók számát. Válassza ki a csoportot az egyes kiszolgálók és kapcsolatok megtekintéséhez. 

![Egy kiszolgáló-port csoport a térképen](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Ha a csoport figyelt és nem figyelt kiszolgálókat is tartalmaz, kiválaszthatja a csoport perec-diagramjának megfelelő szakaszát a kiszolgálók szűréséhez.

## <a name="view-a-map-from-a-vm"></a>Térkép megtekintése egy virtuális gépről 

Azure Monitor for VMs elérése közvetlenül egy virtuális gépről:

1. A Azure Portal válassza a **Virtual Machines**lehetőséget. 
2. A listából válassza ki a virtuális gépet. A **figyelés** szakaszban válassza az **áttekintések (előzetes verzió)** lehetőséget.  
3. Válassza a **Térkép** lapot.

A Térkép megjeleníti a virtuális gép függőségeit úgy, hogy felveszi a futó folyamatok azon csoportjait és folyamatait, amelyek egy adott időtartományon keresztül aktív hálózati kapcsolattal rendelkeznek.  

Alapértelmezés szerint a Térkép az utolsó 30 percet mutatja. Ha szeretné megtekinteni, hogy a függőségek hogyan látszanak a múltban, akár egy óráig is lekérdezheti a korábbi időtartományokat. A lekérdezés futtatásához használja a bal felső sarokban található **TimeRange** választót. Futtathat egy lekérdezést, például egy incidens során, vagy megtekintheti az állapotot a változás előtt.  

![A virtuális gép térképének áttekintése](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Térkép megtekintése virtuálisgép-méretezési csoportból

Azure Monitor for VMs elérése közvetlenül egy virtuálisgép-méretezési csoportból:

1. A Azure Portal válassza a **virtuálisgép-méretezési**csoportok lehetőséget.
2. A listából válassza ki a virtuális gépet. Ezután a **figyelés** szakaszban válassza az **áttekintés (előzetes verzió)** lehetőséget.  
3. Válassza a **Térkép** lapot.

A Térkép megjeleníti a méretezési csoport összes példányát a csoport függőségeivel együtt. A kibontott csomópont listázza a méretezési csoport példányait. Ezeket a példányokat egyszerre 10 lapozhatja. 

Egy adott példányhoz tartozó Térkép betöltéséhez először válassza ki ezt a példányt a térképen. Ezután kattintson a **három** pontot ábrázoló gombra (...) a jobb oldalon, majd válassza a **kiszolgálói Térkép betöltése**lehetőséget. A megjelenő térképen megtekintheti azokat a feldolgozási csoportokat és folyamatokat, amelyek aktív hálózati kapcsolattal rendelkeznek egy megadott időtartományon belül. 

Alapértelmezés szerint a Térkép az utolsó 30 percet mutatja. Ha szeretné megtekinteni, hogy a függőségek hogyan látszanak a múltban, akár egy óráig is lekérdezheti a korábbi időtartományokat. A lekérdezés futtatásához használja a **TimeRange** -választót. Futtathat egy lekérdezést, például egy incidens során, vagy megtekintheti az állapotot a változás előtt.

![A virtuális gép térképének áttekintése](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>A virtuálisgép-méretezési csoport **példányok** nézetében egy adott példányhoz is elérheti a leképezést. A beállítások szakaszban válassza a **példányok**  >  a**bepillantások (előzetes verzió)** **lehetőséget** .

## <a name="view-a-map-from-azure-monitor"></a>Térkép megtekintése Azure Monitor

Azure Monitor a Térkép funkció globális áttekintést nyújt a virtuális gépekről és azok függőségeiről. A Térkép funkció eléréséhez Azure Monitor:

1. A Azure Portal válassza a **figyelő**elemet. 
2. Az **Áttekintés** szakaszban válassza a **Virtual Machines (előzetes verzió)** lehetőséget.
3. Válassza a **Térkép** lapot.

   ![Több virtuális gép Azure Monitor áttekintő térképe](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Válasszon ki egy munkaterületet a lap tetején található **munkaterület** -választó használatával. Ha egynél több Log Analytics munkaterülettel rendelkezik, válassza ki azt a munkaterületet, amelyen engedélyezve van a megoldás, és amelyen a virtuális gépek jelentést küldenek. 

A **csoport** választója a kiválasztott munkaterülethez kapcsolódó számítógép-előfizetéseket, erőforráscsoportokat, [számítógépcsoportokat](../../azure-monitor/platform/computer-groups.md)és virtuálisgép-méretezési csoportokat adja vissza. A kijelölés csak a Térkép szolgáltatásra vonatkozik, és nem végzi el a teljesítményt vagy az állapotot.

Alapértelmezés szerint a Térkép az utolsó 30 percet mutatja. Ha szeretné megtekinteni, hogy a függőségek hogyan látszanak a múltban, akár egy óráig is lekérdezheti a korábbi időtartományokat. A lekérdezés futtatásához használja a **TimeRange** -választót. Futtathat egy lekérdezést, például egy incidens során, vagy megtekintheti az állapotot a változás előtt.  

## <a name="next-steps"></a>Következő lépések

A szűk keresztmetszetek azonosításához, a teljesítmény vizsgálatához és a virtuális gépek általános kihasználtságának megismeréséhez tekintse meg a [Azure monitor for VMS teljesítmény állapotának megtekintése](vminsights-performance.md)című témakört. 
