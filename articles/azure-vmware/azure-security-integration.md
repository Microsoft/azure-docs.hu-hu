---
title: Az Azure VMware-megoldás virtuális gépei a Azure Security Center integrációval védhetők
description: Az Azure VMware-megoldás virtuális gépei az Azure natív biztonsági eszközeivel védhetők az Azure Security Center irányítópulton.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578249"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Az Azure VMware-megoldás virtuális gépei a Azure Security Center integrációval védhetők

Az Azure natív biztonsági eszközei biztosítják az Azure, az Azure VMware megoldás és a helyszíni virtuális gépek (VM-EK) hibrid környezetének védelmét. Ez a cikk bemutatja, hogyan állíthatja be az Azure-eszközöket a hibrid környezetek biztonságához. Ezeket az eszközöket a különböző fenyegetések azonosítására és kezelésére fogja használni.

## <a name="azure-native-services"></a>Azure Native Services

Az Azure Native Services gyors összefoglalása:

- **Log Analytics munkaterület:** Log Analytics munkaterület a naplófájlok tárolására szolgáló egyedi környezet. Mindegyik munkaterület saját adattárral és konfigurációval rendelkezik. Az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adatforrásokat.
- **Azure Security Center:** Azure Security Center egy egységes infrastruktúra-biztonsági felügyeleti rendszer. Erősíti az adatközpontok biztonságát, és komplex veszélyforrások elleni védelmet biztosít a felhőben vagy a helyszínen lévő hibrid számítási feladatokhoz.
- **Azure Sentinel:** Az Azure Sentinel egy felhőalapú, biztonsági Information Event Management-(SIEM-) megoldás. Biztonsági elemzéseket, riasztások észlelését és automatizált veszélyforrásokra adott válaszokat biztosít a környezetek között.

## <a name="topology"></a>Topológia

![Az Azure-beli integrált biztonság architektúráját bemutató ábra.](media/azure-security-integration/azure-integrated-security-architecture.png)

A Log Analytics ügynök lehetővé teszi a naplózási adatok gyűjtését az Azure-ból, az Azure VMware-megoldásból és a helyszíni virtuális gépekről. A rendszer elküldi a naplófájlokat Azure Monitor naplókba, és egy Log Analytics munkaterületen tárolja. Az Log Analytics-ügynököt az arc-kompatibilis kiszolgálók virtuálisgép- [bővítmények](../azure-arc/servers/manage-vm-extensions.md) segítségével telepítheti az új és a meglévő virtuális gépekhez. 

Ha a naplók gyűjtése a Log Analytics munkaterületen történik, a Log Analytics munkaterületet Azure Security Center használatával konfigurálhatja. Azure Security Center felméri az Azure VMware-megoldás virtuális gépei sebezhetőségi állapotát, és riasztást küld a kritikus biztonsági rések ellen. Például az operációs rendszer hiányzó javításait, a biztonsági beállításokat és az [Endpoint Protectiont](../security-center/security-center-services.md)vizsgálja.

A Log Analytics munkaterület a riasztások észlelése, a fenyegetések láthatósága, a vadászat és a fenyegetés megválaszolásához konfigurálható az Azure Sentinel használatával. Az előző ábrán a Azure Security Center Azure Security Center Connector használatával csatlakozik az Azure Sentinelhez. A Azure Security Center továbbítja a környezeti sebezhetőséget az Azure Sentinelnek, hogy eseményt hozzon létre, és más fenyegetésekkel képezze le azokat. Az ütemezett szabályok lekérdezést is létrehozhatja a nemkívánatos tevékenységek észleléséhez és az incidensekre való átalakításához.

## <a name="benefits"></a>Előnyök

- Az Azure natív szolgáltatásai az Azure-ban, az Azure VMware megoldásban és a helyszíni szolgáltatásokban is használhatók a hibrid környezetek biztonsága érdekében.
- Log Analytics munkaterület használatával egyetlen pontba gyűjtheti az adatokat vagy a naplókat, és ugyanazokat az adatokat különböző Azure-beli natív szolgáltatásokhoz is bemutathatja.
- Azure Security Center számos funkciót kínál, többek között:
    - Fájlintegritás monitorozása
    - Fájlok közötti támadás észlelése
    - Operációs rendszer javításának felmérése 
    - Biztonsági konfigurációs beállítások értékelése
    - Endpoint Protection-Értékelés
- Az Azure Sentinel a következőket teszi lehetővé:
    - Minden felhasználó, eszköz, alkalmazás és infrastruktúra esetében, a helyszínen és több felhőben is gyűjthet adatokat a felhőben.
    - A korábban nem észlelt fenyegetések észlelése.
    - Kivizsgálhatja a fenyegetéseket a mesterséges intelligenciával, és a gyanús tevékenységekre való vadászatot.
    - Az incidensekre való gyors reagálás a közös feladatok beépített összehangolása és automatizálása révén.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

A különböző forrásokból származó adatok gyűjtéséhez Log Analytics munkaterületre van szükség. További információ: [log Analytics munkaterület létrehozása a Azure Portal](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Az Azure VMware megoldás virtuális gépei Security Center üzembe helyezése és konfigurálása

A Azure Security Center egy olyan előre konfigurált eszköz, amely nem igényel telepítést. A Azure Portal keresse meg **Security Center** és jelölje ki.

### <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése

Az Azure Defender a helyszíni és a felhőben egyaránt kiterjeszti Azure Security Center komplex veszélyforrások elleni védelmét. Az Azure-beli VMware-megoldás virtuális gépei elleni védelem érdekében engedélyeznie kell az Azure Defendert. 

1. Security Center kattintson az **első lépések** elemre.

2. Válassza a **frissítés** lapot, majd válassza ki az előfizetést vagy a munkaterületet. 

3. Válassza a **frissítés** lehetőséget az Azure Defender engedélyezéséhez.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Azure VMware-megoldás virtuális gépek hozzáadása Security Centerhoz

1. A Azure Portal keresse meg az **Azure-ívet** , és válassza ki.

2. Az erőforrások területen válassza a **kiszolgálók** , majd a **+ Hozzáadás** lehetőséget.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Egy Azure-beli Azure-beli virtuális gép Azure-ba való felvételét bemutató képernyőkép az Azure arc-kiszolgálók lapon.":::

3. Válassza a **parancsfájl létrehozása** lehetőséget.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Az Azure arc oldalának képernyőképe, amely a kiszolgáló interaktív parancsfájllal történő hozzáadásának lehetőségét mutatja be."::: 
 
4. Az **Előfeltételek** lapon válassza a **tovább** lehetőséget.

5. Az **erőforrás részletei** lapon adja meg a következő adatokat: 
    - Előfizetés
    - Erőforráscsoport
    - Region 
    - Operációs rendszer
    - Proxykiszolgáló részletei
    
    Ezután válassza a **következő: címkék** lehetőséget.

6. A **címkék** lapon válassza a **tovább** lehetőséget.

7. A **letöltési és futtatási parancsfájl** lapon válassza a **Letöltés** lehetőséget.

8. Adja meg az operációs rendszert, és futtassa a szkriptet az Azure VMware megoldás virtuális gépén.

## <a name="view-recommendations-and-passed-assessments"></a>Javaslatok megtekintése és a felmérések átadása

1. Azure Security Center a bal oldali ablaktáblán válassza a **leltár** lehetőséget.

2. Az erőforrástípus mezőben válassza a **kiszolgálók – Azure arc** lehetőséget.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="A Azure Security Center leltár oldalának képernyőképe, amelyen a kiszolgálók láthatók – az Azure arc van kiválasztva az erőforrás típusa területen.":::

3. Válassza ki az erőforrás nevét. Megnyílik egy oldal, amely az erőforrás biztonsági állapotával kapcsolatos adatokat jeleníti meg.

4. Az **ajánlás listában** válassza ki a **javaslatokat**, az **átadott értékeléseket** és a nem **elérhető értékelések** lapokat a részletek megtekintéséhez.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="A biztonsági ajánlásokat és értékeléseket bemutató Azure Security Center képernyőképe.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure Sentinel-munkaterület üzembe helyezése

Az Azure Sentinel egy Log Analytics munkaterületre épül. Az Azure Sentinel bevezetésének első lépéseként válassza ki az adott célra használni kívánt Log Analytics munkaterületet.

1. A Azure Portal keressen rá az **Azure Sentinel** kifejezésre, és válassza ki.

2. Az Azure Sentinel-munkaterületek lapon válassza a **+ Hozzáadás** lehetőséget.

3. Válassza ki a Log Analytics munkaterületet, és válassza a **Hozzáadás** lehetőséget.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Adatgyűjtő engedélyezése a biztonsági eseményekhez az Azure VMware-megoldás virtuális gépeken

Most már készen áll az Azure Sentinel és az adatforrások (ebben az esetben a biztonsági események) összekapcsolására.

1. Az Azure Sentinel-munkaterületek lapon válassza ki a konfigurált munkaterületet.

2. A konfiguráció területen válassza **az adatösszekötők** lehetőséget.

3. Az összekötő neve oszlopban válassza a **biztonsági események** elemet a listából, majd válassza az **összekötő lap megnyitása** lehetőséget.

4. Az összekötő lapon válassza ki az adatfolyamként használni kívánt eseményeket, majd válassza a **módosítások alkalmazása** lehetőséget.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Képernyőkép a Security Events oldaláról az Azure Sentinel-ben, ahol kiválaszthatja, hogy mely eseményeket szeretné továbbítani.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Az Azure Sentinel és a Azure Security Center összekötése  

1. Az Azure Sentinel-munkaterület lapon válassza ki a konfigurált munkaterületet.

2. A konfiguráció területen válassza **az adatösszekötők** lehetőséget.

3. Válassza ki **Azure Security Center** a listából, majd válassza az **összekötő lap megnyitása** lehetőséget.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Képernyőkép az Azure Sentinel adatösszekötők oldaláról, amely a Azure Security Center és az Azure Sentinel összekapcsolását mutatja be.":::

4. Válassza a **Kapcsolódás** lehetőséget a Azure Security Center az Azure sentineltel való összekapcsolásához.

5. A **létrehozási incidens** engedélyezése Azure Security Center incidens előállításához.

## <a name="create-rules-to-identify-security-threats"></a>Szabályok létrehozása a biztonsági fenyegetések azonosításához

Az adatforrások Azure Sentinelhez való csatlakoztatása után létrehozhat olyan szabályokat, amelyek riasztásokat hoznak létre az észlelt fenyegetésekkel kapcsolatban. A következő példában létrehozunk egy szabályt, amely nem megfelelő jelszóval kísérli meg a Windows Server rendszerbe való bejelentkezést.

1. Az Azure Sentinel – Áttekintés lap konfigurációk területén válassza az **elemzés** lehetőséget.

2. A konfigurációk területen válassza az **elemzés** lehetőséget.

3. Válassza a **+ Létrehozás** lehetőséget, majd a legördülő menüből válassza az **ütemezett lekérdezési szabály** lehetőséget.

4. Az **általános** lapon adja meg a szükséges adatokat.

    - Név
    - Leírás
    - Taktikák
    - Súlyosság
    - Állapot

    Válassza a **Tovább: szabály logikai >beállítása** lehetőséget.

5. A **szabály logikájának beállítása** lapon adja meg a szükséges adatokat.

    - Szabály lekérdezése (itt látható a példában szereplő lekérdezés)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entitások leképezése
    - Lekérdezés ütemezése
    - Riasztás küszöbértéke
    - Események csoportosítása
    - Mellőzés

    Kattintson a **Tovább** gombra.

6. Az **incidens beállításai** lapon engedélyezze a **létrehozási incidensek létrehozását az elemzési szabály által aktivált riasztások közül** , és válassza a **Tovább: automatikus válasz >** lehetőséget.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Az elemzési szabály varázsló képernyőképe új szabály létrehozásához az Azure Sentinelben. Azt mutatja, hogy a szabály által aktivált riasztások létrehozási incidensei engedélyezve vannak.":::

7. Válassza a Next (tovább) lehetőséget **: >áttekintése**.

8. Az **Áttekintés és létrehozás** lapon tekintse át az adatokat, és válassza a **Létrehozás** lehetőséget.

Miután a harmadik sikertelen kísérletet tett a Windows Serverbe való bejelentkezésre, a létrehozott szabály minden sikertelen kísérlet esetén eseményt indít el.

## <a name="view-alerts"></a>Riasztások megtekintése

A generált incidenseket az Azure Sentinel használatával tekintheti meg. Az Azure Sentinel szolgáltatásból is hozzárendelhet incidenseket, és a megoldásuk után lezárhatja őket.

1. Nyissa meg az Azure Sentinel áttekintés lapját.

2. A veszélyforrások kezelése területen válassza az **incidensek** lehetőséget.

3. Válasszon ki egy incidenst. Ezután hozzárendelheti az incidenst egy csapathoz a feloldáshoz.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Képernyőkép az Azure Sentinel incidensekről oldalról, az incidens kiválasztásával és az incidens megoldáshoz való hozzárendelésének lehetőségével.":::

    A probléma megoldása után lezárhatja azt.

## <a name="hunt-security-threats-with-queries"></a>Biztonsági fenyegetések vadászata lekérdezésekkel

Létrehozhat lekérdezéseket, vagy használhatja a rendelkezésre álló előre definiált lekérdezést az Azure Sentinelben, hogy azonosítsa a fenyegetéseket a környezetben. A következő lépések egy előre definiált lekérdezést futtatnak.

1. Nyissa meg az Azure Sentinel áttekintés lapját.

2. A veszélyforrások kezelése területen válassza a **vadászat** lehetőséget. Megjelenik az előre definiált lekérdezések listája.

3. Válasszon ki egy lekérdezést, majd válassza a **lekérdezés futtatása** lehetőséget.

4. Válassza az **eredmények megtekintése** lehetőséget az eredmények megtekintéséhez.

### <a name="create-a-new-query"></a>Új lekérdezés létrehozása

1.  A veszélyforrások kezelése területen válassza a **vadászat** , majd az **+ Új lekérdezés** elemet.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Képernyőfelvétel az Azure Sentinel Hunting oldaláról + új lekérdezés kiemelve.":::

2. Egyéni lekérdezés létrehozásához adja meg a következő információkat.

    - Név
    - Leírás
    - Egyéni lekérdezés
    - Leképezés megadása
    - Taktikák
    
3. Válassza a **Létrehozás** lehetőséget. Ezután kiválaszthatja a létrehozott lekérdezést, **futtathatja a lekérdezést**, és **megtekintheti az eredményeket**.

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte az Azure VMware-megoldás virtuális gépei elleni védelem biztosítását, érdemes megismernie az alábbiakat:

- Az [Azure Defender irányítópultjának](../security-center/azure-defender-dashboard.md)használata.
- [Fejlett többlépcsős támadások észlelése az Azure sentinelben](../azure-monitor/logs/quick-create-workspace.md).
- [Azure VMware-megoldás virtuális gépek életciklus-kezelése](lifecycle-management-of-azure-vmware-solution-vms.md).
