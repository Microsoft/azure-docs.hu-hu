---
title: VMware Monitoring megoldás a Azure Monitorban | Microsoft Docs
description: Ismerje meg, hogyan segíthet a VMware Monitoring-megoldás a naplók kezelésében és az ESXi-gazdagépek figyelésében.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9ade5a51e2251669daee6fbaca9aa4c50f7e9bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704361"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring (elavult) megoldás a Azure Monitor

![VMware-szimbólum](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A VMware Monitoring megoldás elavult.  Azok a felhasználók, akik már telepítették a megoldást, továbbra is használhatják, de VMware Monitoring nem vehetők fel új munkaterületekre.

A Azure Monitor VMware Monitoring megoldása olyan megoldás, amellyel központosított naplózási és figyelési módszert hozhat létre a nagyméretű VMware-naplók számára. Ez a cikk azt ismerteti, hogyan lehet az ESXi-gazdagépeket a megoldás használatával egyetlen helyen elhárítani, rögzíteni és felügyelni. A megoldással egyetlen helyen láthatja az ESXi-gazdagépek részletes adatait. Az ESXi-gazdagép naplófájljain keresztül megtekintheti a virtuálisgép-és ESXi-gazdagépek leggyakoribb események számát, állapotát és trendjét. A hibakeresést a központosított ESXi-gazdagép naplófájljainak megtekintésével és keresésével végezheti el. Emellett a naplóbeli keresési lekérdezések alapján is létrehozhat riasztásokat.

A megoldás az ESXi-gazdagép natív syslog funkcióját használja az adat leküldésére egy célként megadott virtuális gépre, amely rendelkezik a Log Analytics ügynökkel. A megoldás azonban nem ír fájlokat a syslog-be a cél virtuális gépen belül. A Log Analytics ügynök megnyitja a 1514-es portot, és ezt figyeli. Amint megkapja az adatkérést, a Log Analytics ügynök leküldi az adatAzure Monitorba.

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

* Adja hozzá a VMware Monitoring megoldást az előfizetéséhez a [figyelési megoldás telepítése](./solutions.md#install-a-monitoring-solution)című témakörben ismertetett eljárás használatával.

#### <a name="supported-vmware-esxi-hosts"></a>Támogatott VMware ESXi gazdagépek
vSphere ESXi-gazdagép 5,5, 6,0 és 6,5

#### <a name="prepare-a-linux-server"></a>Linux-kiszolgáló előkészítése
Hozzon létre egy Linux operációs rendszer virtuális gépet az ESXi-gazdagépekről származó összes syslog-adatok fogadásához. A [log Analytics Linux-ügynök](../vm/quick-collect-linux-computer.md) az ESXi-gazdagép syslog-adatgyűjtési pontja. Több ESXi-gazdagép használatával továbbíthatja a naplókat egyetlen Linux-kiszolgálóra, ahogy az alábbi példában is látható.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog-folyamat](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog-gyűjtemény konfigurálása
1. Állítsa be a syslog-továbbítást a VSphere. A syslog-továbbítás beállításával kapcsolatos részletes információkért lásd: [a syslog konfigurálása ESXi 5,0 és újabb rendszereken (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Ugrás az **ESXi-gazdagép konfigurációs**  >  **szoftver**  >  **Speciális beállítások**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. A *syslog. Global. logHost* mezőben adja hozzá a Linux-kiszolgálót és a *1514*-as portszámot. Példa: `tcp://hostname:1514` vagy `tcp://123.456.789.101:1514`
1. Nyissa meg az ESXi-gazdagép tűzfalát a syslog számára. **ESXi-gazdagép konfigurációja**  >  **Szoftver**  >  **Biztonsági profil**  >  **Tűzfal** és nyitott **Tulajdonságok**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. A vSphere-konzolon ellenőrizze, hogy a syslog megfelelően van-e beállítva. Erősítse meg az ESXI-gazdagépen, hogy a **1514** -es port konfigurálva van.
1. Töltse le és telepítse a Linux rendszerhez készült Log Analytics-ügynököt a Linux-kiszolgálón. További információkért tekintse meg a [Linux rendszerhez készült log Analytics-ügynök dokumentációját](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. A Linux rendszerhez készült Log Analytics-ügynök telepítése után nyissa meg a/etc/opt/Microsoft/omsagent/sysconf/omsagent.d könyvtárat, és másolja a vmware_esxi. conf fájlt a/etc/opt/Microsoft/omsagent/conf/omsagent.d könyvtárba, és módosítsa a fájl tulajdonosát, csoportját és engedélyeit. Például:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Indítsa újra a Log Analytics Linux-ügynököt a futtatásával `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Tesztelje a Linux-kiszolgáló és az ESXi-gazdagép közötti kapcsolatot az `nc` ESXi-gazdagépen található parancs használatával. Például:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. A Azure Portal hajtson végre egy log-lekérdezést a következőhöz: `VMware_CL` . Amikor Azure Monitor gyűjti a syslog-adatokat, megőrzi a syslog formátumát. A portálon bizonyos mezők rögzítése történik, például az *állomásnév* és a *processname*.  

    ![A képernyőképen a Type = VMware_CL egy időbélyeggel rendelkező napló-lekérdezés látható.](./media/vmware/type.png)  

    Ha a nézet naplójának keresési eredményei hasonlóak a fenti képen láthatóhoz, akkor a VMware Monitoring megoldás irányítópultjának használatára van beállítva.  

## <a name="vmware-data-collection-details"></a>A VMware-adatok gyűjtésének részletei
A VMware Monitoring megoldás különféle teljesítménymutatókat és adatokat gyűjt az ESXi-gazdagépekről az Ön által engedélyezett linuxos Log Analytics-ügynökök használatával.

Az alábbi táblázat az adatgyűjtés módszereit és az adatok gyűjtésének egyéb részleteit mutatja be.

| platform | Linux-Log Analytics ügynök | System Center Operations Manager ügynök | Azure Storage | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |3 percenként |

A következő táblázat példákat mutat be a VMware Monitoring megoldás által összegyűjtött adatmezőkre:

| mező neve | leírás |
| --- | --- |
| Device_s |VMware Storage-eszközök |
| ESXIFailure_s |hibák típusai |
| EventTime_t |az esemény bekövetkezésekor bekövetkezett idő |
| HostName_s |ESXi-gazdagép neve |
| Operation_s |virtuális gép létrehozása vagy virtuális gép törlése |
| ProcessName_s |esemény neve |
| ResourceId_s |a VMware-gazdagép neve |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI-állapot |
| SyslogMessage_s |Syslog-adatbázis |
| UserName_s |a virtuális gépet létrehozó vagy törölt felhasználó |
| VMName_s |a virtuális gép neve |
| Computer |gazdaszámítógép |
| TimeGenerated |az adatgyűjtés ideje |
| DataCenter_s |VMware Datacenter |
| StorageLatency_s |tárolási késés (MS) |

## <a name="vmware-monitoring-solution-overview"></a>VMware Monitoring megoldás áttekintése
A VMware csempe megjelenik a Log Analytics munkaterületen. Magas szintű áttekintést nyújt a hibákról. Amikor rákattint a csempére, egy irányítópult-nézetet nyithat meg.

![A képernyőképen látható a VMware csempe, amely kilenc hibát jelenít meg.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigáljon az irányítópult nézetbe
A **VMware** irányítópult nézetében a pengék a következők szerint vannak rendezve:

* Sikertelen állapotok száma
* Legfelső szintű gazdagép események száma szerint
* Leggyakoribb események száma
* Virtuális gépekkel kapcsolatos tevékenységek
* ESXi-gazdagép eseményei

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kattintson bármelyik panelre a Log Analytics keresési ablaktáblájának megnyitásához, amely a panel részletes információit jeleníti meg.

Itt szerkesztheti a napló lekérdezését, hogy az adott dologra vonatkozóan módosítható legyen. A naplók létrehozásával kapcsolatos további információkért lásd: [adatok keresése a Azure monitor található naplók használatával](../logs/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>ESXi-gazdagép eseményeinek keresése
Egyetlen ESXi-gazdagép több naplót hoz létre a folyamataik alapján. A VMware Monitoring megoldás központosítja őket, és összegzi az események számát. Ez a központosított nézet segít megérteni, hogy mely ESXi-állomás nagy mennyiségű eseményt tartalmaz, és milyen események történnek a leggyakrabban a környezetben.

![esemény](./media/vmware/events.png)

További részletezéshez kattintson egy ESXi-gazdagépre vagy egy esemény típusára.

Az ESXi-állomásnévre kattintva megtekintheti az adott ESXi-gazdagép adatait. Ha az esemény típusával szeretné szűkíteni az eredményeket, vegye fel a `“ProcessName_s=EVENT TYPE”` kifejezést a keresési lekérdezésbe. A keresési szűrőben a **processname** is kiválaszthatja. Ez leszűkíti az adatokat.

![Képernyőfelvétel az ESXi-állomásról események száma és az esemény-típusok részletezése alapján a VMware Monitoring irányítópult nézetében.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Magas virtuális gépekkel kapcsolatos tevékenységek keresése
A virtuális gépek bármelyik ESXi-gazdagépen létrehozhatók és törölhetők. A rendszergazdák számára hasznos lehet azonosítani, hogy hány virtuális gépet hoz létre ESXi-gazdagép. Ez pedig segít megérteni a teljesítményt és a kapacitás megtervezését. A virtuális gépek tevékenységi eseményeinek nyomon követése létfontosságú a környezet kezelésekor.

![Képernyőfelvétel a VMware Monitoring irányítópulton található virtuálisgép-tevékenységek panelről, amely az ESXi-gazdagép által a virtuális gépek létrehozásának és törlésének gráfját mutatja.](./media/vmware/vmactivities1.png)

Ha további ESXi-gazdagép virtuális gépek létrehozási információit szeretné látni, kattintson az ESXi-gazdagép nevére.

![Képernyőkép a VMware Monitoring-irányítópulton lévő panelről, amely egy ESXi-gazdagép által létrehozott összes virtuális gép adatait tartalmazó táblázatot mutat.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Gyakori naplók lekérdezései
A megoldás más hasznos lekérdezéseket is tartalmaz, amelyek segíthetnek az ESXi-gazdagépek, például a nagy tárterület, a tárolási késés és az elérési út meghibásodásának kezelésében.

![A képernyőképen az ajánlott KERESÉSek láthatók, amelyek hasznos tárolt lekérdezések.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Lekérdezések mentése
A naplófájlok mentése a Azure Monitor egy szabványos funkciója, amely segítséget nyújt a hasznosnak ítélt lekérdezések megtartásában. Ha olyan lekérdezést hoz létre, amelyet hasznosnak talál, mentse azt a **Kedvencek** elemre kattintva. A mentett lekérdezésekkel könnyedén újra felhasználhatja később a saját [irányítópult](../visualize/tutorial-logs-dashboards.md) oldaláról, ahol létrehozhat saját egyéni irányítópultokat is.

![A képernyőképen egy egyéni irányítópulton, a visszavonás, az exportálás, a riasztás, a mentés, a kedvencek és az előzmények ikonjai láthatók.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Riasztások létrehozása lekérdezésekből
A lekérdezések létrehozása után érdemes lehet a lekérdezésekkel riasztást küldeni, ha adott események történnek. Riasztások létrehozásával kapcsolatos információkért tekintse [meg a log Analytics riasztásait](../alerts/alerts-overview.md) . A lekérdezések és az egyéb lekérdezési példák esetében tekintse meg a [VMware Monitor log Analytics blogbejegyzés használatával](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) című témakört.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Mit kell tennem az ESXi-gazdagép beállításain? Milyen hatással lesz a jelenlegi környezetre?
A megoldás a natív ESXi-gazdagép syslog-továbbító mechanizmusát használja. A naplók rögzítéséhez nincs szükség további Microsoft-szoftverekre az ESXi-gazdagépen. Kis hatással kell lennie a meglévő környezetre. Azonban be kell állítania a syslog-továbbítást, amely ESXI-funkció.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Újra kell indítani az ESXi-gazdagépet?
Nem. Ez a folyamat nem igényel újraindítást. Időnként a vSphere nem frissíti megfelelően a syslog-t. Ilyen esetben jelentkezzen be az ESXi-gazdagépre, és töltse be újra a syslog-t. Megint nem kell újraindítani a gazdagépet, így ez a folyamat nem zavarja meg a környezetét.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Megnövelhető vagy csökkenthető a Log Analytics elküldhető naplófájlok mennyisége?
Igen, lehet. Az ESXi-gazdagép naplózási szintjének beállításai a vSphere-ben használhatók. A naplók gyűjtése az *információs* szint alapján történik. Ha tehát a virtuális gép létrehozását vagy törlését szeretné naplózni, akkor meg kell őriznie az *információs* szintet a gazdagépen. További információ: [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Miért van a gazdagépen nem biztosítva az adatLog Analytics? A napló beállítása az info (adatok) értékre van állítva.
ESXi-gazdagép hibája volt a syslog timestamp számára. További információ: [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). A megkerülő megoldás alkalmazása után a gazdagépnek rendesen működnie kell.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Használhatok több ESXi-gazdagépet arra, hogy egyetlen virtuális gépre továbbítsák a syslog-omsagent?
Igen. Több ESXi-gazdagép is továbbítható egyetlen virtuális géphez a omsagent-mel.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Miért nem látom a Log Analyticsbe áramló adatforgalmat?
Több oka is lehet:

* Az ESXi-gazdagép nem megfelelően küldi el az adattovábbítást a omsagent-t futtató virtuális gépre. A teszteléshez hajtsa végre a következő lépéseket:

  1. A megerősítéshez jelentkezzen be az ESXi-gazdagépre SSH használatával, és futtassa a következő parancsot: `nc -z ipaddressofVM 1514`

      Ha ez nem sikerül, a Speciális konfiguráció vSphere beállításai valószínűleg nem megfelelőek. A syslog-továbbítás beállításával kapcsolatos információkért lásd: a [syslog-gyűjtemény konfigurálása](#configure-syslog-collection) .
  1. Ha a syslog-port kapcsolata sikeres, de még nem lát semmilyen adatmennyiséget, akkor az ESXi-gazdagépen töltse be a syslog-t az alábbi parancs futtatásához: `esxcli system syslog reload`
* A Log Analytics ügynökkel rendelkező virtuális gép helytelenül van beállítva. Ennek teszteléséhez hajtsa végre a következő lépéseket:

  1. Log Analytics figyeli a 1514-es portot. A megnyitásának ellenőrzéséhez futtassa a következő parancsot: `netstat -a | grep 1514`
  1. Ekkor meg kell jelennie a port `1514/tcp` megnyitásának. Ha nem, ellenőrizze, hogy a omsagent megfelelően van-e telepítve. Ha nem látja a port információit, akkor a syslog-port nincs megnyitva a virtuális gépen.

    a. Ellenőrizze, hogy a Log Analytics-ügynök fut-e a használatával `ps -ef | grep oms` . Ha nem fut, indítsa el a folyamatot a parancs futtatásával. `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Nyissa meg az `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fájlt.

     c. Győződjön meg arról, hogy a megfelelő felhasználó és Csoport beállítás érvényes, a következőhöz hasonlóan: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Ha a fájl nem létezik, vagy a felhasználó és a csoport beállításai nem megfelelőek, végezze el a megfelelő műveletet a [Linux-kiszolgáló előkészítésével](#prepare-a-linux-server).

## <a name="next-steps"></a>Következő lépések
* A részletes VMware-gazdagépek részleteinek megtekintéséhez használjon Log Analytics [naplózási lekérdezéseket](../logs/log-query-overview.md) .
* [Hozzon létre saját irányítópultokat a](../visualize/tutorial-logs-dashboards.md) VMware-gazdagép adataival.
* [Riasztások létrehozása](../alerts/alerts-overview.md) , ha adott VMware-gazdagép eseményei történnek.

