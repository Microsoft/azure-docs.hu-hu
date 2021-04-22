---
title: Az Azure VMware Solution virtuális gépek védelme Azure Security Center integrációval
description: A virtuális Azure VMware Solution az Azure natív biztonsági eszközeivel védheti a virtuális gépeket Azure Security Center irányítópultról.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877515"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Az Azure VMware Solution virtuális gépek védelme Azure Security Center integrációval

Az Azure natív biztonsági eszközei védelmet biztosítanak az Azure, a Azure VMware Solution és a helyszíni virtuális gépek hibrid környezete számára. Ez a cikk bemutatja, hogyan állíthat be Azure-eszközöket a hibrid környezetek biztonságának érdekében. Ezeket az eszközöket a különböző fenyegetések azonosítására és kezelésére fogja használni.

## <a name="azure-native-services"></a>Natív Azure-szolgáltatások

Az Azure natív szolgáltatásainak rövid összefoglalása:

- **Log Analytics-munkaterület:** A Log Analytics-munkaterület egy egyedi környezet a naplóadatok tárolására. Mindegyik munkaterület saját adattárral és konfigurációval rendelkezik. Az adatforrások és megoldások úgy vannak konfigurálva, hogy az adataikat egy adott munkaterületen tárolják.
- **Azure Security Center:** Azure Security Center egy egységes infrastruktúrabiztonsági felügyeleti rendszer. Megerősíti az adatközpontok biztonságát, és fejlett fenyegetésvédelmet biztosít a hibrid számítási feladatok számára a felhőben vagy a helyszínen.
- **Azure Sentinel:** Azure Sentinel egy natív felhőbeli biztonságiesemény-kezelési (SIEM-) megoldás. Biztonsági elemzéseket, riasztások észlelését és automatizált fenyegetésekre adott választ biztosít a környezetben.

## <a name="topology"></a>Topológia

![Az Azure integrált biztonsági architektúráját bemutató diagram.](media/azure-security-integration/azure-integrated-security-architecture.png)

A Log Analytics-ügynök lehetővé teszi a naplóadatok gyűjtését az Azure-ból, Azure VMware Solution és helyszíni virtuális gépekről. A naplóadatokat a rendszer Azure Monitor naplókba küldi, és egy Log Analytics-munkaterületen tárolja őket. A Log Analytics-ügynököt Az Arc-kompatibilis kiszolgálók virtuálisgép-bővítményei [támogatják](../azure-arc/servers/manage-vm-extensions.md) az új és meglévő virtuális gépeken. 

Miután a Log Analytics-munkaterület összegyűjtötte a naplókat, konfigurálhatja a Log Analytics-munkaterületet a Azure Security Center. Azure Security Center felméri a virtuális gépek biztonsági Azure VMware Solution állapotát, és riasztást küld minden kritikus biztonsági résről. Felméri például a hiányzó operációsrendszer-javításokat, a biztonsági hibás konfigurációkat és a [végpontvédelmet.](../security-center/security-center-services.md)

A Log Analytics-munkaterületet úgy konfigurálhatja, hogy Azure Sentinel riasztások észlelésére, a fenyegetések láthatóságára, a veszélyforrás-felderítésre és a fenyegetésekre adott válaszokra. Az előző ábrán látható, Azure Security Center összekötő Azure Sentinel csatlakozik Azure Security Center összekötőhöz. Azure Security Center továbbítja a környezet biztonsági rését a Azure Sentinel, hogy létrehoz egy incidenst, és leképezse a többi fenyegetésre. Az ütemezett szabályok lekérdezésével a nemkívánatos tevékenységeket is észlelheti, és átalakíthatja incidensekké.

## <a name="benefits"></a>Előnyök

- A natív Azure-szolgáltatások hibrid környezetek biztonságára használhatók az Azure-ban, Azure VMware Solution helyszíni szolgáltatásokban.
- A Log Analytics-munkaterület használatával egyetlen pontra gyűjtheti az adatokat vagy a naplókat, és ugyanezeket az adatokat különböző natív Azure-szolgáltatásoknak is bemutathatja.
- Azure Security Center funkciókat kínál, többek között a következőket:
    - Fájlintegritás monitorozása
    - Fájl nélküli támadásészlelés
    - Operációs rendszer javításfelmérése 
    - Biztonsági hibás konfigurációk felmérése
    - Végpontvédelem értékelése
- Azure Sentinel a következőt teszi lehetővé:
    - Felhőméretű adatgyűjtés minden felhasználóra, eszközre, alkalmazásra és infrastruktúrára, a helyszínen és több felhőben egyaránt.
    - Korábban nem észlelt fenyegetések észlelése.
    - Fenyegetések kivizsgálása mesterséges intelligenciával és gyanús tevékenységek nagy léptékű felderítése.
    - Gyorsan reagálhat az incidensekre a gyakori feladatok beépített vezénylése és automatizálása révén.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

A különböző forrásokból származó adatok gyűjtéséhez Log Analytics-munkaterületre van szükség. További információ: [Log Analytics-munkaterület létrehozása a Azure Portal.](../azure-monitor/logs/quick-create-workspace.md) 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Virtuális Security Center üzembe helyezése és Azure VMware Solution konfigurálása

Azure Security Center egy előre konfigurált eszköz, amely nem igényel üzembe helyezést. A Azure Portal keressen rá a **Security Center,** és válassza ki.

### <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése

Azure Defender kiterjeszti Azure Security Center komplex veszélyforrások elleni védelmet a hibrid számítási feladatokra a helyszínen és a felhőben egyaránt. A virtuális gépek Azure VMware Solution védelméhez engedélyeznie kell a Azure Defender. 

1. A Security Center válassza az **Első lépések lehetőséget.**

2. Válassza a **Frissítés lapot,** majd válassza ki az előfizetését vagy munkaterületét. 

3. Válassza **a Frissítés lehetőséget** a Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Virtuális Azure VMware Solution hozzáadása a Security Center

1. A Azure Portal keressen rá **a** Azure Arc, és válassza ki.

2. Az Erőforrások alatt válassza a **Kiszolgálók,** majd a **+Hozzáadás lehetőséget.**

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Képernyőkép a Azure Arc a virtuális gép Azure-hoz Azure VMware Solution hozzáadásáról.":::

3. Válassza **a Szkript létrehozása lehetőséget.**
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Képernyőkép a Azure Arc oldalról, amely egy kiszolgáló interaktív szkript használatával való hozzáadásának beállítását mutatja be."::: 
 
4. Az **Előfeltételek lapon** válassza a Tovább **lehetőséget.**

5. Az Erőforrás **részletei lapon** adja meg a következő adatokat: 
    - Előfizetés
    - Erőforráscsoport
    - Region 
    - Operációs rendszer
    - Proxykiszolgáló részletei
    
    Ezután válassza **a Tovább: Címkék lehetőséget.**

6. A Címkék **lapon** válassza a Tovább **lehetőséget.**

7. A Download **and run script (Szkript letöltése és futtatása) lapon** válassza a Download (Letöltés) **lehetőséget.**

8. Adja meg az operációs rendszert, és futtassa a szkriptet a Azure VMware Solution virtuális gépen.

## <a name="view-recommendations-and-passed-assessments"></a>Javaslatok megtekintése és az átadott értékelések

1. A Azure Security Center bal **oldali panelen válassza** az Inventory lehetőséget.

2. Az Erőforrás típusa mezőben válassza **a Kiszolgálók – Azure Arc.**
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Képernyőkép a Azure Security Center lapról, amely a Kiszolgálók – Azure Arc az Erőforrástípus alatt látható.":::

3. Válassza ki az erőforrás nevét. Megnyílik egy oldal, amely az erőforrás biztonsági állapotának részleteit mutatja.

4. A **Javaslatok lista** alatt válassza a **Javaslatok,** Az átadott **értékelések** és a Nem érhető **el értékelések** lapokat ezeknek a részleteknek a megtekintéséhez.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Biztonsági javaslatokat Azure Security Center és értékeléseket megjelenítő képernyőkép.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Munkaterület Azure Sentinel üzembe helyezése

Azure Sentinel Log Analytics-munkaterületre épül. Az első lépés a Azure Sentinel, hogy ki kell választania az erre a célra használni kívánt Log Analytics-munkaterületet.

1. A Azure Portal keresse meg a **Azure Sentinel,** és jelölje ki.

2. A Munkaterületek Azure Sentinel válassza a **+Hozzáadás lehetőséget.**

3. Válassza ki a Log Analytics-munkaterületet, és válassza a **Hozzáadás lehetőséget.**

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Adatgyűjtő engedélyezése biztonsági eseményekhez Azure VMware Solution virtuális gépeken

Most már készen áll arra, hogy Azure Sentinel adatforrásokkal, ebben az esetben a biztonsági eseményekkel.

1. Az Azure Sentinel munkaterületek lapon válassza ki a konfigurált munkaterületet.

2. A Konfiguráció alatt válassza az **Adat-összekötők lehetőséget.**

3. Az Összekötő neve oszlopban válassza a **biztonsági események lehetőséget** a listából, majd válassza az Összekötő **oldalának megnyitása lehetőséget.**

4. Az összekötő lapon válassza ki a streamelni kívánt eseményeket, majd válassza a **Módosítások alkalmazása lehetőséget.**

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Képernyőkép a biztonsági események lapról a Azure Sentinel amelyen kiválaszthatja a streamelni kívánt eseményeket.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Csatlakozás Azure Sentinel Azure Security Center  

1. A munkaterület Azure Sentinel válassza ki a konfigurált munkaterületet.

2. A Konfiguráció alatt válassza az **Adat-összekötők lehetőséget.**

3. Válassza **Azure Security Center** listában, majd válassza az **Összekötő oldalának megnyitása lehetőséget.**

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Képernyőkép az Adat-összekötők lapról a Azure Sentinel, amely a Azure Security Center összekötőkhöz való Azure Sentinel.":::

4. Válassza **a Csatlakozás** lehetőséget a Azure Security Center Azure Sentinel.

5. Engedélyezze **az Incidens létrehozása** gombra, hogy incidenst hozzon létre a Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Szabályok létrehozása a biztonsági fenyegetések azonosításához

Miután csatlakoztatta az adatforrásokat a Azure Sentinel, szabályokat hozhat létre az észlelt fenyegetésekre vonatkozó riasztások létrehozásához. A következő példában egy szabályt hozunk létre, amely a nem megfelelő jelszóval próbál bejelentkezni a Windows-kiszolgálóra.

1. A Azure Sentinel lapján, a Konfigurációk alatt válassza az **Elemzés lehetőséget.**

2. A Konfigurációk alatt válassza az **Elemzés lehetőséget.**

3. Válassza **a +Létrehozás** lehetőséget, majd a legördülő menüben válassza az **Ütemezett lekérdezési szabály lehetőséget.**

4. Az Általános **lapon** adja meg a szükséges adatokat.

    - Név
    - Leírás
    - Taktikák
    - Súlyosság
    - Állapot

    Válassza **a Tovább: Szabálylogika beállítása lehetőséget >.**

5. A **Szabálylogika beállítása lapon** adja meg a szükséges adatokat.

    - Szabálylekérdezés (itt látható a példalekérdezés)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entitások leképezése
    - Lekérdezésütemezés
    - Riasztás küszöbértéke
    - Események csoportosítása
    - Mellőzés

    Kattintson a **Tovább** gombra.

6. Az **Incidensbeállítások lapon** engedélyezze az **Incidensek** létrehozása az elemzési szabály által aktivált riasztások alapján beállítást, majd válassza a Tovább: Automatikus válaszlépések **>.**
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Képernyőkép az Analytic rule varázslóról új szabály létrehozásához a Azure Sentinel. A szabály által aktivált riasztások incidensek létrehozása lehetőségét jeleníti meg engedélyezettként.":::

7. Válassza **a Tovább: Áttekintés >** lehetőséget.

8. Az Áttekintés **és létrehozás lapon** tekintse át az adatokat, és válassza a Létrehozás **lehetőséget.**

Miután a harmadik sikertelen bejelentkezési kísérlet történt a Windows-kiszolgálóra, a létrehozott szabály minden sikertelen kísérlet esetén kivált egy incidenst.

## <a name="view-alerts"></a>Riasztások megtekintése

A generált incidenseket a Azure Sentinel. Az incidenseket a megoldásuk után is hozzárendelheti és bezárhatja, mindezt a Azure Sentinel.

1. Ugrás a Azure Sentinel áttekintési lapjára.

2. A Fenyegetéskezelés alatt válassza az **Incidensek lehetőséget.**

3. Válasszon ki egy incidenst. Ezt követően az incidenst hozzárendelheti egy csapathoz a megoldásukhoz.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Képernyőkép az Azure Sentinel lapról, az incidens kijelölve, és lehetőség az incidens hozzárendelésre a megoldáshoz.":::

    A probléma megoldása után bezárhatja.

## <a name="hunt-security-threats-with-queries"></a>Biztonsági fenyegetések veszélyének kikeresése lekérdezésekkel

A környezetben a fenyegetések azonosításához létrehozhat lekérdezéseket, vagy használhatja a Azure Sentinel elérhető, előre definiált lekérdezést. Az alábbi lépések egy előre definiált lekérdezést futtatnak.

1. Ugrás a Azure Sentinel áttekintési lapjára.

2. A Fenyegetéskezelés alatt válassza a **Veszélyforrás-veszélyforrás-keresés lehetőséget.** Megjelenik az előre definiált lekérdezések listája.

3. Válasszon ki egy lekérdezést, majd válassza a **Lekérdezés futtatása lehetőséget.**

4. Az **eredmények ellenőrzéshez** válassza az Eredmények megtekintése lehetőséget.

### <a name="create-a-new-query"></a>Új lekérdezés létrehozása

1.  A Fenyegetéskezelés alatt válassza a **Veszélyforrás-keresés,** majd az **+Új lekérdezés lehetőséget.**

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Képernyőkép a Azure Sentinel keresési oldalról, az + Új lekérdezés kiemeléssel.":::

2. Egyéni lekérdezés létrehozásához adja meg az alábbi adatokat.

    - Név
    - Leírás
    - Egyéni lekérdezés
    - Leképezés beírva
    - Taktikák
    
3. Válassza a **Létrehozás** lehetőséget. Ezután kiválaszthatja a létrehozott lekérdezést, a **Lekérdezés futtatása és** az Eredmények megtekintése **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan védheti meg Azure VMware Solution virtuális gépeket, a következővel ismerkedhet meg:

- Az irányítópult [Azure Defender használata](../security-center/azure-defender-dashboard.md)
- [Speciális többlépcsős támadásészlelés a Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Virtuális gépek életciklus-Azure VMware Solution kezelése](lifecycle-management-of-azure-vmware-solution-vms.md)
