---
title: Ügynök-alapú függőség elemzésének beállítása Azure Migrate
description: Ez a cikk azt ismerteti, hogyan állítható be az ügynök-alapú függőségi elemzés a Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 84a672f76de4b11558f2b39bf417a3eda2e31a36
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786532"
---
# <a name="set-up-dependency-visualization"></a>Függőségi vizualizáció beállítása

Ez a cikk azt ismerteti, hogyan állítható be az ügynök-alapú függőségi elemzés a Azure Migrateban: felderítés és Értékelés. A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és áttelepíteni kívánt kiszolgálók függőségeinek azonosításában és értelmezésében.

## <a name="before-you-start"></a>Előkészületek

- Tekintse át az ügynök-alapú függőségek elemzésének támogatási és telepítési követelményeit a következőhöz:
    - [Kiszolgálók a VMware-környezetben](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Kiszolgálók a Hyper-V környezetben](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Győződjön meg róla, hogy:
    - Azure Migrate projekttel rendelkezik. Ha nem, [hozzon létre](./create-manage-projects.md) egyet most.
    - Győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Discovery and Assessment eszközt a projektbe.
    - Hozzon létre egy [Azure Migrate berendezést](migrate-appliance.md) a helyszíni kiszolgálók felderítéséhez. A készülék felderíti a helyszíni kiszolgálókat, és metaadatokat és teljesítményadatokat küld Azure Migratenak: felderítés és Értékelés. Készülék beállítása a következőhöz:
        - [Kiszolgálók a VMware-környezetben](how-to-set-up-appliance-vmware.md)
        - [Kiszolgálók a Hyper-V környezetben](how-to-set-up-appliance-hyper-v.md)
        - [Fizikai kiszolgálók](how-to-set-up-appliance-physical.md)
- A függőségi vizualizáció használatához egy [log Analytics munkaterületet](../azure-monitor/logs/manage-access.md) társít egy Azure Migrate projekthez:
    - Munkaterületet csak a Azure Migrate berendezés beállítása után csatolhat, és a Azure Migrate projektben található kiszolgálók felfedése.
    - Győződjön meg arról, hogy rendelkezik az előfizetésben a Azure Migrate projektet tartalmazó munkaterülettel.
    - A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.
    - A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Új vagy meglévő Log Analytics munkaterületet társíthat egy Azure Migrate projekthez.
    - A munkaterületet a kiszolgáló függőségi vizualizációjának első beállításakor csatolja. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
    - Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.

## <a name="associate-a-workspace"></a>Munkaterület hozzárendelése

1. Miután felderített kiszolgálókat értékelésre, a **kiszolgálók**  >  **Azure Migrate: felderítés és értékelés** területen kattintson az **Áttekintés** elemre.  
2. **Azure Migrate: felderítés és értékelés**, kattintson az **Essentials** elemre.
3. A **OMS munkaterületen** kattintson a **Konfigurálás szükséges** elemre.

     ![A Log Analytics-munkaterület konfigurálása](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Az **OMS-munkaterület konfigurálása** lapon adja meg, hogy új munkaterületet kíván-e létrehozni, vagy egy meglévőt szeretne használni.
    - A projekt-előfizetés összes munkaterületéről kiválaszthat egy meglévő munkaterületet.
    - Az olvasóhoz való hozzáférésre van szüksége a munkaterülethez a hozzárendeléshez.
5. Ha új munkaterületet hoz létre, válassza ki a kívánt helyet.

    ![Új munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

Az összes elemezni kívánt kiszolgálón telepítse az ügynököket.

> [!NOTE]
> System Center Operations Manager 2012 R2 vagy újabb rendszer által figyelt kiszolgálók esetében nem kell telepítenie az MMA-ügynököt. A Service Map a Operations Managersal integrálódik. [Kövesse](../azure-monitor/vm/service-map-scom.md#prerequisites) az integrációs útmutatót.

1. **Azure Migrate: felderítés és értékelés**, kattintson a **felderített kiszolgálók** elemre.
2. Minden olyan kiszolgáló esetében, amelyet elemezni szeretne a függőségi vizualizációval, kattintson a **függőségek** oszlopban a **szükséges ügynök telepítésére**.
3. A **függőségek** lapon töltse le a Windows vagy Linux rendszerhez készült MMA-és függőségi ügynököt.
4. Az **MMA-ügynök konfigurálása** területen másolja a munkaterület azonosítóját és kulcsát. Ezek az MMA-ügynök telepítésekor szükségesek.

    ![Az ügynökök telepítése](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Az MMA telepítése

Telepítse az MMA-t minden elemezni kívánt Windows-vagy Linux-kiszolgálóra.

### <a name="install-mma-on-a-windows-server"></a>Az MMA telepítése Windows Serverre

Az ügynök telepítése Windows Serverre:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. **A licencfeltételek oldalon kattintson** **az Elfogadom gombra a** licenc elfogadásához.
3. A **célmappában** tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > a **Tovább gombra**.
4. Az **ügynök telepítési beállításai** területen válassza az **Azure log Analytics**  >  **tovább** lehetőséget.
5. Új Log Analytics munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be azt a munkaterület-azonosítót és-kulcsot, amelyet a portálról másolt. Kattintson a **Tovább** gombra.

Az ügynököt a parancssorból vagy egy automatizált módszerrel, például Configuration Manager vagy [Intigua](https://www.intigua.com/intigua-for-azure-migration)is telepítheti.
- [További](../azure-monitor/agents/log-analytics-agent.md#installation-options) információ az MMA-ügynök telepítésével kapcsolatban ezen módszerek használatával.
- Az MMA-ügynök ezzel a [szkripttel](https://github.com/brianbar-MSFT/Install-MMA) is telepíthető.
- [További](../azure-monitor/agents/agents-overview.md#supported-operating-systems) információ az MMA által támogatott Windows operációs rendszerekről.

### <a name="install-mma-on-a-linux-server"></a>Az MMA telepítése Linux-kiszolgálóra

Az MMA telepítése Linux-kiszolgálóra:

1. Vigye át a megfelelő köteget (x86 vagy x64) a Linux rendszerű számítógépre SCP/SFTP használatával.
2. Telepítse a csomagot a--install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További](../azure-monitor/agents/agents-overview.md#supported-operating-systems) információ az MMA által támogatott Linux operációs rendszerek listájáról. 

## <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows Serverre való telepítéséhez kattintson duplán a telepítési fájlra, és kövesse a varázslót.
2. Ha a függőségi ügynököt Linux-kiszolgálóra szeretné telepíteni, a következő parancs használatával root-ként kell telepítenie:

    ```sh InstallDependencyAgent-Linux64.bin```

- [További](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) információ arról, hogyan használhatók a parancsfájlok a függőségi ügynök telepítéséhez.
- [További](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) információ a függőségi ügynök által támogatott operációs rendszerekről.


## <a name="create-a-group-using-dependency-visualization"></a>Csoport létrehozása függőségi vizualizáció használatával

Most hozzon létre egy csoportot az értékeléshez. 


> [!NOTE]
> Azok a csoportok, amelyeknek a függőségeit szeretné megjeleníteni, nem tartalmazhatnak 10-nél több kiszolgálót. Ha több mint 10 kiszolgálóval rendelkezik, Ossza szét őket kisebb csoportokba.

1. **Azure Migrate: felderítés és értékelés**, kattintson a **felderített kiszolgálók** elemre.
2. A **függőségek** oszlopban kattintson az összes áttekinteni kívánt kiszolgáló **függőségeinek megtekintése** elemre.
3. A függőségi térképen a következők láthatók:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a-kiszolgáló és a között.
    - Azok a függő kiszolgálók, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A telepített függőségi ügynökökkel rendelkező függő kiszolgálók külön mezőkként jelennek meg.
    - A kiszolgálón belül futó folyamatok. Bontsa ki az egyes kiszolgálók panelt a folyamatok megtekintéséhez.
    - Kiszolgáló tulajdonságai (beleértve a teljes tartománynevet, az operációs rendszert, a MAC-címeket). A részletek megtekintéséhez kattintson az egyes kiszolgálók mezőre.

4. A különböző időtartamokra vonatkozó függőségeket az időtartomány címkében lévő idő időtartamára kattintva tekintheti meg.
    - Alapértelmezés szerint a tartomány egy óra. 
    - Módosíthatja az időtartományt, vagy megadhatja a kezdő és a záró dátumot, valamint az időtartamot.
    - Az időtartomány akár egy óráig is elvégezhető. Ha hosszabb tartományra van szüksége, használja a Azure Monitort a függő adatlekérdezés hosszabb időtartamra való lekéréséhez.

5. Miután azonosította a csoportba felvenni kívánt függő kiszolgálókat, a CTRL billentyűt lenyomva tartva több kiszolgálót is kijelölhet a térképen, majd kattintson a **csoport gépek** elemre.
6. Adja meg a csoport nevét.
7. Győződjön meg arról, hogy a Azure Migrate által felderített függő kiszolgálók találhatók.

    - Ha egy függő kiszolgálót Azure Migrate: felderítés és értékelés nem derít fel, akkor azt nem lehet felvenni a csoportba.
    - Kiszolgáló hozzáadásához futtassa újra a felderítést, és győződjön meg arról, hogy a kiszolgáló fel van derítve.

8. Ha értékelést szeretne készíteni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, és hozzon létre egy új értékelést a csoport számára.
8. A csoport mentéséhez kattintson **az OK** gombra.

A csoport létrehozása után javasoljuk, hogy telepítse az ügynököket a csoport összes kiszolgálójára, majd jelenítse meg a függőségeket a teljes csoport számára.

## <a name="query-dependency-data-in-azure-monitor"></a>Függőségi adatlekérdezések Azure Monitor

A Azure Migrate projekthez társított Log Analytics munkaterületen Service Map által rögzített függőségi adatmennyiséget kérdezheti le. A Log Analytics Azure Monitor naplók írására és futtatására szolgál.

- [Megtudhatja, hogyan](../azure-monitor/vm/service-map.md#log-analytics-records) kereshet Service Map-adatLog Analyticsokban.
- [Tekintse](../azure-monitor/logs/get-started-queries.md)  át a [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)naplójának lekérdezéseit.

Futtasson egy lekérdezést a függőségi értékekhez a következő módon:

1. Az ügynökök telepítése után lépjen a portálra, és kattintson az **Áttekintés** elemre.
2. **Azure Migrate: felderítés és értékelés**, kattintson az **Áttekintés** elemre. Az **Essentials** kibontásához kattintson a lefelé mutató nyílra.
3. A **OMS munkaterületen** kattintson a munkaterület nevére.
3. A Log Analytics munkaterület oldalon > **általános** lapon kattintson a **naplók** elemre.
4. Írja be a lekérdezést, és kattintson a **Futtatás** gombra.

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány példa a függőségi adatok kinyerésére.

- A lekérdezéseket módosíthatja az előnyben részesített adatpontok kinyeréséhez.
- [Tekintse át](../azure-monitor/vm/service-map.md#log-analytics-records) a függőségi adatrekordok teljes listáját.
- [Tekintse át](../azure-monitor/vm/service-map.md#sample-log-searches) a további mintavételi lekérdezéseket.

#### <a name="sample-review-inbound-connections"></a>Minta: bejövő kapcsolatok áttekintése

Tekintse át a kiszolgálók egy csoportjának bejövő kapcsolatait.

- A kapcsolati metrikák (VMConnection) táblában lévő rekordok nem jelölik az egyes fizikai hálózati kapcsolatokat.
- A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva.
- [További](../azure-monitor/vm/service-map.md#connections) információ a fizikai hálózati kapcsolatok adatainak összesítéséről a VMConnection-ben.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Minta: az elküldött és fogadott adatértékek összefoglalása

Ez a minta összefoglalja a kiszolgálók közötti bejövő kapcsolatokon küldött és fogadott adatmennyiséget.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy értékelést](how-to-create-assessment.md) egy csoport számára.
