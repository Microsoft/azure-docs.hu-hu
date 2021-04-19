---
title: Ügynökalapú függőségelemzés beállítása a Azure Migrate
description: Ez a cikk az ügynökalapú függőségelemzés beállítását ismerteti a Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714775"
---
# <a name="set-up-dependency-visualization"></a>Függőségvizualizáció beállítása

Ez a cikk azt ismerteti, hogyan állíthatja be az ügynökalapú függőségelemzést a Azure Migrate: Felderítés és értékelés modulban. [A függőségelemzés](concepts-dependency-visualization.md) segítségével azonosíthatja és megértheti a kiszolgálók közötti függőségeket, amelyek kiértékelését és az Azure-ba való áttelepítését szeretné.

## <a name="before-you-start"></a>Előkészületek

- Tekintse át az ügynökalapú függőségelemzés támogatási és üzembe helyezési követelményeit a következőhöz:
    - [Kiszolgálók VMware-környezetben](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Kiszolgálók Hyper-V környezetben](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Győződjön meg arról, hogy:
    - Legyen egy Azure Migrate projektje. Ha nem, [hozzon létre](./create-manage-projects.md) egyet.
    - Ellenőrizze, hogy [hozzáadta-e](how-to-assess.md) a Azure Migrate: Felderítés és értékelés eszközt a projekthez.
    - Állítson be [egy Azure Migrate berendezést a](migrate-appliance.md) helyszíni kiszolgálók felderítéséhez. A berendezés felderíti a helyszíni kiszolgálókat, és metaadatokat és teljesítményadatokat küld Azure Migrate felderítési és felmérési szolgáltatásnak. Berendezés beállítása a következőre:
        - [Kiszolgálók VMware-környezetben](how-to-set-up-appliance-vmware.md)
        - [Kiszolgálók Hyper-V környezetben](how-to-set-up-appliance-hyper-v.md)
        - [Fizikai kiszolgálók](how-to-set-up-appliance-physical.md)
- A függőségek vizualizációjának használata érdekében egy [Log Analytics-munkaterületet](../azure-monitor/logs/manage-access.md) társít egy Azure Migrate projekthez:
    - Munkaterületet csak a hálózati berendezés beállítása és a Azure Migrate felderítése után csatolhat a Azure Migrate projektben.
    - Győződjön meg arról, hogy az előfizetésben van egy munkaterület, amely tartalmazza a Azure Migrate projektet.
    - A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-Európában kell lennie. Más régiókban lévő munkaterületek nem társíthatóak projekthez.
    - A munkaterületnek olyan régióban kell lennie, amelyben a Service Map [támogatott.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
    - Új vagy meglévő Log Analytics-munkaterületet társíthat egy Azure Migrate projekthez.
    - A munkaterületet a kiszolgáló függőségi vizualizációjának első beállításakor csatolja. A Azure Migrate projekt munkaterülete a hozzáadása után nem módosítható.
    - A Log Analyticsben a projekthez társított Azure Migrate meg van címkézve a Migration Project (Migrálási projekt) kulcs és a projekt neve.

## <a name="associate-a-workspace"></a>Munkaterület társítása

1. Miután felderített kiszolgálókat az értékeléshez, a **Kiszolgálók** és  >  **Azure Migrate: Felderítés és** értékelés menüben kattintson az Áttekintés **elemre.**  
2. A **Azure Migrate: Felderítés és értékelés menüben** kattintson az **Alapvető szolgáltatások elemre.**
3. Az **OMS-munkaterületen kattintson a** Konfiguráció szükséges **elemre.**

     ![A Log Analytics-munkaterület konfigurálása](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Az **OMS-munkaterület konfigurálása** beállításnál adja meg, hogy új munkaterületet szeretne létrehozni, vagy egy meglévőt szeretne használni.
    - Kiválaszthat egy meglévő munkaterületet a projekt-előfizetésben lévő összes munkaterület közül.
    - A társításhoz Olvasó hozzáféréssel kell hozzáférnie a munkaterülethez.
5. Ha új munkaterületet hoz létre, válasszon ki egy helyet.

    ![Új munkaterület hozzáadása](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Megtudhatja,](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) hogyan konfigurálhatja az OMS-munkaterületet a privát végpontok csatlakoztatása érdekében.  

## <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

Telepítse az ügynököket minden elemezni kívánt kiszolgálón.

> [!NOTE]
> A 2012 R2 System Center Operations Manager által figyelt kiszolgálók esetén nem kell telepítenie az MMA-ügynököt. Service Map integrálható a Operations Manager. [Kövesse az](../azure-monitor/vm/service-map-scom.md#prerequisites) integrációs útmutatót.

1. A **Azure Migrate: Felderítés és értékelés menüben** kattintson a **Felderített kiszolgálók elemre.**
2. Minden olyan kiszolgálóhoz, amely függőségek vizualizációját elemezni szeretné, a **Függőségek** oszlopban kattintson az Ügynöktelepítés **szükséges elemre.**
3. A **Függőségek lapon** töltse le a Windows vagy Linux MMA és Dependency Agent ügynököt.
4. Az **MMA-ügynök konfigurálása alatt** másolja ki a munkaterület azonosítóját és kulcsát. Ezekre szüksége lesz az MMA-ügynök telepítésekor.

    ![Az ügynökök telepítése](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Az MMA telepítése

Telepítse az MMA-t minden elemezni kívánt Windows- vagy Linux-kiszolgálón.

### <a name="install-mma-on-a-windows-server"></a>Az MMA telepítése Windows-kiszolgálón

Az ügynök telepítése Windows Serverre:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencszerződések lapon** kattintson az **Elfogadom gombra a** licenc elfogadásához.
3. A **Célmappa mappában** tartsa meg vagy módosítsa az alapértelmezett telepítési mappát a > **gombra.**
4. Az **Ügynökbeállítási beállítások között válassza** az Azure Log Analytics **Next**  >  **lehetőséget.**
5. Új **Log** Analytics-munkaterület hozzáadásához kattintson a Hozzáadás gombra. Illessze be a portálról kimáselt munkaterület-azonosítót és -kulcsot. Kattintson a **Tovább** gombra.

Az ügynököt telepítheti a parancssorból, vagy egy automatizált módszerrel, például a Konfigurációkezelő vagy az [Intigua használatával.](https://www.intigua.com/intigua-for-azure-migration)
- [További információ az](../azure-monitor/agents/log-analytics-agent.md#installation-options) MMA-ügynök ezen módszerek használatával való telepítésével kapcsolatban.
- Az MMA-ügynök ezzel a [szkripttel](https://github.com/brianbar-MSFT/Install-MMA) is telepíthető.
- [További információ](../azure-monitor/agents/agents-overview.md#supported-operating-systems) az MMA által támogatott Windows operációs rendszerekről.

### <a name="install-mma-on-a-linux-server"></a>Az MMA telepítése Linux-kiszolgálón

Az MMA telepítése Linux-kiszolgálóra:

1. A megfelelő csomagot (x86 vagy x64) az scp/sftp használatával továbbítja a Linux-számítógépre.
2. Telepítse a csomagot az --install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További információ](../azure-monitor/agents/agents-overview.md#supported-operating-systems) az MMA által támogatott Linux operációs rendszerek listájáról. 

## <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows-kiszolgálóra való telepítéséhez kattintson duplán a telepítőfájlra, és kövesse a varázsló lépéseit.
2. A függőségi ügynök Linux-kiszolgálóra való telepítéséhez telepítse gyökérként a következő paranccsal:

    ```sh InstallDependencyAgent-Linux64.bin```

- [További információ](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) a függőségi ügynök szkriptekkel való telepítésének mikéntjéhez.
- [További információ](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) a függőségi ügynök által támogatott operációs rendszerekről.


## <a name="create-a-group-using-dependency-visualization"></a>Csoport létrehozása függőségvizualizációval

Most hozzon létre egy csoportot az értékeléshez. 


> [!NOTE]
> Azok a csoportok, amelyek függőségeit szeretné vizualizálni, nem tartalmazhatnak 10-esnél több kiszolgálót. Ha 10-esnél több kiszolgálója van, ossza fel őket kisebb csoportokra.

1. A **Azure Migrate: Felderítés és értékelés menüben** kattintson a **Felderített kiszolgálók elemre.**
2. A **Dependencies (Függőségek) oszlopban** kattintson a **View dependencies** for each server you want to review (Az áttekintni kívánt kiszolgálók függőségeinek megtekintése) elemre.
3. A függőségi térképen a következőket láthatja:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a kiszolgálóra és a kiszolgálóról.
    - A függőségi ügynökökkel nem telepített függő kiszolgálók portszámok szerint vannak csoportosítva.
    - A függőségi ügynökökkel telepített függő kiszolgálók külön mezőkként jelennek meg.
    - A kiszolgálón futó folyamatok. Bontsa ki az egyes kiszolgálómezőket a folyamatok megtekintéséhez.
    - Kiszolgáló tulajdonságai (beleértve a teljes tartomány nevét, az operációs rendszert és a MAC-címet). Kattintson az egyes kiszolgálómezőkre a részletek megtekintéséhez.

4. A különböző időtartamok függőségeit az időtartomány címkéjén az időtartamra kattintva tekintheti meg.
    - Alapértelmezés szerint a tartomány egy óra. 
    - Módosíthatja az időtartományt, vagy megadhat kezdő és záró dátumokat és időtartamot is.
    - Az időtartomány legfeljebb egy óra lehet. Ha hosszabb tartományra van szüksége, a Azure Monitor használatával hosszabb ideig lekérdezheti a függő adatokat.

5. Miután azonosította a csoportosítni kívánt függő kiszolgálókat, ctrl+kattintással jelöljön ki több kiszolgálót a térképen, majd kattintson a **Gépek csoportosítása elemre.**
6. Adja meg a csoport nevét.
7. Ellenőrizze, hogy a függő kiszolgálókat a Azure Migrate.

    - Ha egy függő kiszolgálót nem fedez fel a Azure Migrate: Felderítés és értékelés, nem használhatja azt a csoporthoz.
    - Kiszolgáló hozzáadásához futtassa újra a felderítést, és ellenőrizze, hogy a kiszolgáló felderítve van-e.

8. Ha ehhez a csoporthoz szeretne értékelést létrehozni, jelölje be a jelölőnégyzetet egy új értékelés létrehozásához a csoporthoz.
8. A **csoport mentéshez** kattintson az OK gombra.

A csoport létrehozása után javasoljuk, hogy telepítsen ügynököket a csoportban található összes kiszolgálóra, majd vizualizálja a teljes csoport függőségeit.

## <a name="query-dependency-data-in-azure-monitor"></a>Függőségi adatok lekérdezése a Azure Monitor

A projekthez társított Log Analytics-munkaterület Service Map által rögzített függőségi adatokat Azure Migrate le. A Log Analytics a naplólekérdezések írását Azure Monitor futtatja.

- [Megtudhatja, hogyan keresheti](../azure-monitor/vm/service-map.md#log-analytics-records) meg Service Map adatokat a Log Analyticsben.
- [A naplólekérdezések](../azure-monitor/logs/get-started-queries.md) Log Analyticsben való [írásának áttekintése.](../azure-monitor/logs/log-analytics-tutorial.md)

Futtatassa a függőségi adatok lekérdezését a következőképpen:

1. Az ügynökök telepítése után a portálon kattintson az Áttekintés **elemre.**
2. A **Azure Migrate: Felderítés és értékelés menüben** kattintson az **Áttekintés elemre.** Kattintson a lefelé mutató nyílra az **Alapvető szolgáltatások kibontásához.**
3. Az **OMS-munkaterületen kattintson** a munkaterület nevére.
3. A Log Analytics-munkaterület általános lapján kattintson > **Naplók** **elemre.**
4. Írja meg a lekérdezést, és kattintson a **Futtatás parancsra.**

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány példa a függőségi adatok kinyeréséhez használható lekérdezésekre.

- A lekérdezések módosításával kinyerheti a kívánt adatpontokat.
- [Tekintse](../azure-monitor/vm/service-map.md#log-analytics-records) át a függőségi adatrekordok teljes listáját.
- [Tekintse át a](../azure-monitor/vm/service-map.md#sample-log-searches) további mintalekérdezések áttekintését.

#### <a name="sample-review-inbound-connections"></a>Minta: Bejövő kapcsolatok áttekintése

Tekintse át a kiszolgálók egy készletének bejövő kapcsolatait.

- A kapcsolati metrikák (VMConnection) táblában található rekordok nem az egyes fizikai hálózati kapcsolatokat képviselik.
- Több fizikai hálózati kapcsolat van logikai kapcsolatba csoportosítva.
- [További információ](../azure-monitor/vm/service-map.md#connections) a fizikai hálózati kapcsolat adatainak a VMConnectionben való összesítésével kapcsolatban.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Minta: Elküldött és fogadott adatok összegzése

Ez a példa a kiszolgálók közötti bejövő kapcsolatokon küldött és fogadott adatok mennyiségét foglalja össze.

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

[Értékelés létrehozása](how-to-create-assessment.md) egy csoporthoz.
