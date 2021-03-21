---
title: Azure Diagnostics bővítmény hibaelhárítása
description: Az Azure Diagnostics Azure Virtual Machines, Service Fabric vagy Cloud Services használatakor felmerülő problémák elhárítása.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 431b19595fbe2f5bc1f989e712c9c104af8e839b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711518"
---
# <a name="azure-diagnostics-troubleshooting"></a>Az Azure Diagnostics hibaelhárítása
Ez a cikk a Azure Diagnostics használatára vonatkozó hibaelhárítási információkat ismerteti. További információ az Azure Diagnostics szolgáltatásról: [Azure Diagnostics Overview (áttekintés](diagnostics-extension-overview.md)).

## <a name="logical-components"></a>Logikai összetevők
**Diagnosztika beépülő modul indítója (DiagnosticsPluginLauncher.exe)**: elindítja a Azure Diagnostics bővítményt. Belépési pontként szolgál.

**Diagnosztikai beépülő modul (DiagnosticsPlugin.exe)**: a figyelési ügynök élettartamának konfigurálása, elindítása és kezelése. Ez az indító által indított fő folyamat.

**Figyelő ügynök (MonAgent \* . exe folyamatok)**: figyeli, gyűjti és továbbítja a diagnosztikai adatokat.  

## <a name="logartifact-paths"></a>Log/lelet elérési útjai
A következő néhány fontos napló és összetevő elérési útja. Ezt az információt a dokumentum többi részén tekintjük át.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Összetevő | Elérési út |
| --- | --- |
| **Azure Diagnostics konfigurációs fájl** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Naplófájlok** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Diagnosztikai célú helyi tároló** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájlja** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics kiterjesztési csomag** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **A log Collection segédprogram elérési útja** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost naplófájl** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Configuration\MonAgentHost. <seq_num>. log |

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
| Összetevő | Elérési út |
| --- | --- |
| **Azure Diagnostics konfigurációs fájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Naplófájlok** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Diagnosztikai célú helyi tároló** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájlja** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Állapot fájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Azure Diagnostics kiterjesztési csomag** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **A log Collection segédprogram elérési útja** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost naplófájl** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost. <seq_num>. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>A metrikaadatok nem jelennek meg az Azure Portalon
A Azure Diagnostics a Azure Portal megjeleníthető metrikai adatokat biztosít. Ha problémába ütközik a portálon található adatokkal kapcsolatban, tekintse meg a \* Azure Diagnostics Storage-fiók WADMetrics táblázatát, és ellenőrizze, hogy a megfelelő metrikai rekordok léteznek-e, és győződjön meg arról, hogy az [erőforrás-szolgáltató](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft. az elemzések regisztrálva vannak.

Itt a tábla **PartitionKey** az erőforrás-azonosító, a virtuális gép vagy a virtuálisgép-méretezési csoport. A **RowKey** a metrika neve (más néven a teljesítményszámláló neve).

Ha az erőforrás-azonosító helytelen, ellenőrizze  ,   >    >  hogy az erőforrás-azonosító helyesen van-e megadva a diagnosztikai konfigurációs metrikák **ResourceId** .

Ha az adott metrika nem tartalmaz adatokat, a **diagnosztikai konfiguráció**  >  **PerformanceCounter** ellenőrizze, hogy szerepel-e a metrika (teljesítményszámláló). Alapértelmezés szerint a következő számlálókat engedélyezjük:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET alkalmazások (__összesen__) \ kérelmek/másodperc
- \ ASP.NET alkalmazások (__összesen__) \Errors összesen/mp
- \ASP.NET\Requests várólistán
- \ASP.NET\Requests elutasítva
- \Processor (w3wp) \% processzoridő
- \Process (w3wp) \Private bájtok
- \Process (WaIISHost) \% processzoridő
- \Process (WaIISHost) \Private bájtok
- \Process (WaWorkerHost) \% processzoridő
- \Process (WaWorkerHost) \Private bájtok
- \Memory\Page hibák másodpercenként
- \.NET CLR memória (_globális_) \% idő a GC-ben
- \Logikai lemez (C:) \ írási sebesség (bájt/s)
- \Logikai lemez (C:) \ olvasási sebesség (bájt/s)
- \Logikai lemez (D:) \ írási sebesség (bájt/s)
- \Logikai lemez (D:) \ olvasási sebesség (bájt/s)

Ha a konfiguráció helyesen van beállítva, de továbbra sem látja a metrikai adatokat, a következő irányelvek segítséget nyújtanak a hibakereséshez.


## <a name="azure-diagnostics-is-not-starting"></a>Az Azure Diagnostics nem indul el
Arról, hogy miért nem sikerült elindítani a Azure Diagnosticst, tekintse meg a **DiagnosticsPluginLauncher. log** és a **DiagnosticsPlugin. log** fájlt a korábban megadott naplófájlok helyén.

Ha ezek a naplók azt jelzik `Monitoring Agent not reporting success after launch` , hogy hiba történt a MonAgentHost.exe elindítása közben. Tekintse meg az előző szakaszban jelzett helyen található naplókat `MonAgentHost log file` .

A naplófájlok utolsó sora tartalmazza a kilépési kódot.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Ha **negatív** kilépési kódot talál, tekintse meg a [kilépési kód táblát](#azure-diagnostics-plugin-exit-codes) a [hivatkozások szakaszban](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>A diagnosztikai adatok nincsenek naplózva az Azure Storage-ban
Annak megállapítása, hogy egyik adathalmaz sem jelenik-e meg, vagy egy adott adathalmaz.

### <a name="diagnostics-infrastructure-logs"></a>Diagnosztikai infrastruktúra naplói
A diagnosztika az összes hibát naplózza a diagnosztikai infrastruktúra naplóiban. Győződjön meg arról, hogy engedélyezte a [diagnosztikai infrastruktúra naplófájljainak rögzítését a konfigurációban](#how-to-check-diagnostics-extension-configuration). Ezután gyorsan megkeresheti a `DiagnosticInfrastructureLogsTable` konfigurált Storage-fiók táblában megjelenő esetleges hibákat.

### <a name="no-data-is-appearing"></a>Nem jelenik meg az adatai
A leggyakoribb ok, hogy az események adatai nem jelennek meg, a Storage-fiók adatai helytelenül vannak megadva.

Megoldás: javítsa ki a diagnosztikai konfigurációt, és telepítse újra a diagnosztikát.

Ha a Storage-fiók megfelelően van konfigurálva, a távoli elérést a gépre, és ellenőrizze, hogy a *DiagnosticsPlugin.exe* és *MonAgentCore.exe* fut-e. Ha nem futnak, hajtsa végre a következő témakörben ismertetett lépéseket: [Azure Diagnostics nem indul](#azure-diagnostics-is-not-starting)el.

Ha a folyamatok futnak, ugorjon a következőre: az adatrögzítés [helyileg?](#is-data-getting-captured-locally) és kövesse az itt található utasításokat.

Ha ez nem oldja meg a problémát, próbálja meg a következőket:

1. Ügynök eltávolítása
2. Könyvtár C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics eltávolítása
3. Ügynök újbóli telepítése


### <a name="part-of-the-data-is-missing"></a>Az adatmennyiség hiányzik
Ha bizonyos adatok lekérése nem minden, az adatgyűjtési/-átviteli folyamat helyesen van beállítva. A probléma szűkítéséhez kövesse az alábbi alszakaszokat.

#### <a name="is-the-collection-configured"></a>A gyűjtemény konfigurálva van?
A diagnosztikai konfiguráció egy adott típusú adat gyűjtésére vonatkozó utasításokat tartalmaz. [Tekintse át a konfigurációt](#how-to-check-diagnostics-extension-configuration) annak ellenőrzéséhez, hogy csak a gyűjteményhez konfigurált adatait keresi.

#### <a name="is-the-host-generating-data"></a>A gazdagép létrehozza az adathalmazt?
- **Teljesítményszámlálók**: Nyissa meg a perfmon eszközt, és keresse meg a számlálót.

- **Nyomkövetési naplók**: távoli hozzáférés a virtuális géphez, és adjon hozzá egy TextWriterTraceListener az alkalmazás konfigurációs fájljához.  Lásd: https://msdn.microsoft.com/library/sk36c28t.aspx a szöveges figyelő beállítása.  Győződjön meg arról `<trace>` , hogy az elem rendelkezik `<trace autoflush="true">` .<br />
Ha nem látja a létrehozott nyomkövetési naplókat, további információ: hiányzó nyomkövetési naplók.

- **ETW-nyomkövetés**: távoli hozzáférés a virtuális géphez és a perfview eszköz telepítése.  A perfview eszköz-ben futtassa a **fájl**  >  **felhasználói parancsát**,  >  **figyelje a etwprovder1**  >  **etwprovider2**, és így tovább. A **Listen** parancs megkülönbözteti a kis-és nagybetűket, és a ETW-szolgáltatók vesszővel tagolt listája nem tartalmazhat szóközöket. Ha a parancs futtatása sikertelen, a Perfview eszköz eszköz jobb alsó sarkában található **log (napló** ) gombra kattintva megtekintheti, hogy milyen kísérlet történt a futtatására és az eredményre.  Ha a bevitel helyes, egy új ablak jelenik meg. Néhány másodpercen belül megkezdheti a ETW nyomkövetését.

- **Eseménynaplók**: távoli hozzáférés a virtuális géphez. Nyissa meg `Event Viewer` , majd ellenőrizze, hogy az események léteznek-e.

#### <a name="is-data-getting-captured-locally"></a>Helyileg történik az adatrögzítés?
Ezután győződjön meg arról, hogy az adatrögzítés helyileg történik.
Az adatait a rendszer helyileg tárolja a `*.tsf` helyi tárolóban található fájlokban diagnosztikai adatként. A különböző típusú naplók gyűjtése különböző `.tsf` fájlokban történik. A nevek hasonlóak az Azure Storage-ban található táblák neveihez.

Például `Performance Counters` gyűjtsön adatokat a alkalmazásban `PerformanceCountersTable.tsf` . Az eseménynaplók gyűjtése folyamatban van `WindowsEventLogsTable.tsf` . A helyi [naplók kibontása](#local-log-extraction) szakaszban található utasítások segítségével nyissa meg a helyi gyűjtemény fájljait, és ellenőrizze, hogy a rendszer begyűjti-e őket a lemezen.

Ha nem jelenik meg a naplók helyi gyűjtése, és már ellenőrizte, hogy a gazdagép adatokat hoz létre, valószínűleg konfigurációs probléma van. Alaposan tekintse át a konfigurációt.

Tekintse át a MonitoringAgent-MaConfig.xml létrehozott konfigurációt is. Ellenőrizze, hogy van-e olyan szakasz, amely leírja a kapcsolódó napló forrását. Ezután ellenőrizze, hogy a diagnosztika és a figyelési ügynök konfigurációja között nem vész el a fordítás.

#### <a name="is-data-getting-transferred"></a>Az adatátvitel bekerül?
Ha ellenőrizte, hogy az adatai helyileg vannak rögzítve, de még mindig nem látja a Storage-fiókban, hajtsa végre a következő lépéseket:

- Ellenőrizze, hogy megfelelő Storage-fiókot adott-e meg, és hogy nem adott-e át kulcsokat a megadott Storage-fiókhoz. Az Azure Cloud Services esetében időnként azt látjuk, hogy az emberek nem frissítik `useDevelopmentStorage=true` .

- Ellenőrizze, hogy helyes-e a megadott Storage-fiók. Győződjön meg arról, hogy nincs hálózati korlátozás, amely megakadályozza, hogy az összetevők elérjék a nyilvános tárolási végpontokat. Ennek egyik módja a távoli hozzáférés a gépen, majd próbáljon meg valamit ugyanabba a Storage-fiókba írni.

- Végezetül megtekintheti, hogy a figyelési ügynök milyen hibákat jelez. A figyelési ügynök beírja a naplóit `maeventtable.tsf` , amelyek a helyi tárolóban találhatók a diagnosztikai szolgáltatásokhoz. A fájl megnyitásához kövesse a [helyi naplók kibontása](#local-log-extraction) szakasz utasításait. Ezután próbálja meg megállapítani, hogy vannak- `errors` e olyan hibák, amelyek a tárolóba írás helyi fájlokra való beolvasását jelzik.

### <a name="capturing-and-archiving-logs"></a>Naplók rögzítése és archiválása
Ha úgy gondolja, hogy felveszi a kapcsolatot a támogatási szolgálattal, az első dolog, hogy megkérdezzük a naplók begyűjtését a gépről. Időt takaríthat meg. Futtassa a `CollectGuestLogs.exe` segédprogramot a log Collection segédprogram elérési útján. Létrehoz egy. zip fájlt, amely az összes kapcsolódó Azure-naplóval azonos mappában található.

## <a name="diagnostics-data-tables-not-found"></a>Nem találhatóak a diagnosztikai adattáblák
A ETW-eseményeket tároló Azure Storage-táblákat a következő kód alapján nevezi el:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Alább bemutatunk egy példát:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Ez a kód négy táblát hoz létre:

| Esemény | Table name (Táblázat neve) |
| --- | --- |
| Provider = "Prov1" &lt; esemény azonosítója = "1"/&gt; |WADEvent + MD5 ("Prov1") + "1" |
| Provider = "Prov1" &lt; esemény azonosítója = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "Prov1" &lt; DefaultEvents/&gt; |WADDefault + MD5 ("Prov1") |
| Provider = "prov2" &lt; DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Hivatkozások

### <a name="how-to-check-diagnostics-extension-configuration"></a>A diagnosztikai bővítmény konfigurációjának megkeresése
A bővítmény konfigurálásának legegyszerűbb módja, ha a [Azure erőforrás-kezelő](https://resources.azure.com), majd a Azure Diagnostics bővítményt (IaaSDiagnostics/PaaDiagnostics) futtató virtuális gépre vagy Felhőbeli szolgáltatásra lép.

Azt is megteheti, hogy a Távoli asztalt a gépre helyezi, és megtekinti a log-összetevők elérési útja szakaszban leírt Azure Diagnostics konfigurációs fájlt.

Mindkét esetben keressen rá a **Microsoft. Azure. Diagnostics** kifejezésre, majd a **XmlCfg** vagy a **WadCfg** mezőre.

Ha virtuális gépet keres, és a **WadCfg** mező megtalálható, akkor a konfiguráció JSON formátumú. Ha a **xmlCfg** mező megtalálható, az azt jelenti, hogy a konfiguráció XML-ben van, és Base64 kódolású. [Dekódolni](https://www.bing.com/search?q=base64+decoder) kell a diagnosztika által betöltött XML-kód megtekintéséhez.

A Cloud Service szerepkör esetében, ha a konfigurációt a lemezről választja, az adatok Base64 kódolású, ezért [dekódolni](https://www.bing.com/search?q=base64+decoder) kell, hogy megjelenjen a diagnosztika által betöltött XML-kód.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics beépülő modul kilépési kódjai
A beépülő modul a következő kilépési kódokat adja vissza:

| Kilépési kód | Leírás |
| --- | --- |
| 0 |Sikeres művelet. |
| -1 |Általános hiba. |
| -2 |Nem tölthető be a RCF-fájl.<p>Ez a belső hiba csak akkor fordulhat elő, ha a vendég ügynök beépülő modulját manuálisan, helytelenül hívja meg a virtuális gépen. |
| -3 |Nem tölthető be a diagnosztikai konfigurációs fájl.<p><p>Megoldás: egy konfigurációs fájl nem továbbítja a séma-ellenőrzést. A megoldás egy olyan konfigurációs fájl megadása, amely megfelel a sémának. |
| -4 |A figyelési ügynök diagnosztika egy másik példánya már használja a helyi erőforrás-könyvtárat.<p><p>Megoldás: válasszon másik értéket a **LocalResourceDirectory**. |
| -6 |A vendég ügynök plugin-indítója érvénytelen parancssorral próbálta elindítani a diagnosztikát.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendég ügynök beépülő modulját manuálisan, helytelenül hívja meg a virtuális gépen. |
| -10 |A diagnosztikai beépülő modul nem kezelt kivétellel kilépett. |
| -11 |A vendég ügynök nem tudta létrehozni a figyelési ügynök elindításához és figyeléséhez felelős folyamatot.<p><p>Megoldás: Ellenőrizze, hogy rendelkezésre áll-e elegendő rendszererőforrás az új folyamatok indításához.<p> |
| – 101 |Érvénytelen argumentumok a diagnosztikai beépülő modul meghívásakor.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendég ügynök beépülő modulját manuálisan, helytelenül hívja meg a virtuális gépen. |
| – 102 |A beépülő modul folyamata nem tudja inicializálni magát.<p><p>Megoldás: Ellenőrizze, hogy rendelkezésre áll-e elegendő rendszererőforrás az új folyamatok indításához. |
| – 103 |A beépülő modul folyamata nem tudja inicializálni magát. Konkrétan nem tudja létrehozni a naplózó objektumot.<p><p>Megoldás: Ellenőrizze, hogy rendelkezésre áll-e elegendő rendszererőforrás az új folyamatok indításához. |
| – 104 |Nem tölthető be a vendég ügynök által biztosított RCF-fájl.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendég ügynök beépülő modulját manuálisan, helytelenül hívja meg a virtuális gépen. |
| – 105 |A diagnosztikai beépülő modul nem tudja megnyitni a diagnosztikai konfigurációs fájlt.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a diagnosztikai beépülő modult manuálisan, helytelenül hívja meg a virtuális gépen. |
| – 106 |A diagnosztikai konfigurációs fájl nem olvasható.<p><p>Egy konfigurációs fájl okozta, amely nem továbbítja a séma-ellenőrzést. <br><br>Megoldás: adjon meg egy olyan konfigurációs fájlt, amely megfelel a sémának. További információ: [a diagnosztikai bővítmények konfigurációjának megkeresése](#how-to-check-diagnostics-extension-configuration). |
| – 107 |Az erőforrás-könyvtár a figyelési ügynöknek való továbbítása érvénytelen.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a figyelési ügynököt manuálisan, helytelenül hívja meg a virtuális gépen.</p> |
| – 108 |A diagnosztikai konfigurációs fájl nem alakítható át a figyelési ügynök konfigurációs fájljába.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a diagnosztikai beépülő modult manuálisan, érvénytelen konfigurációs fájllal hívja meg. |
| – 110 |Általános diagnosztikai konfigurációs hiba.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a diagnosztikai beépülő modult manuálisan, érvénytelen konfigurációs fájllal hívja meg. |
| – 111 |Nem sikerült elindítani a figyelési ügynököt.<p><p>Megoldás: Ellenőrizze, hogy rendelkezésre áll-e elegendő rendszererőforrás. |
| – 112 |Általános hiba |

### <a name="local-log-extraction"></a>Helyi napló kibontása
A figyelési ügynök naplófájlokat és összetevőket gyűjt a `.tsf` fájlokként. A `.tsf` fájl nem olvasható, de a következőképpen alakítható át `.csv` :

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
A nevű új fájl `<relevantLogFile>.csv` ugyanabban az elérési úton jön létre, mint a megfelelő `.tsf` fájl.

>[!NOTE]
> Ezt a segédprogramot csak a Main. TSF fájl (például PerformanceCountersTable. TSF) esetében kell futtatnia. A rendszer automatikusan feldolgozza a kapcsolódó fájlokat (például PerformanceCountersTables_ \* \* 001. tsf, PerformanceCountersTables_ \* \* 002. TSF stb.).

### <a name="more-about-missing-trace-logs"></a>További információ a hiányzó nyomkövetési naplókról

>[!NOTE]
> Az alábbi információk többnyire az Azure Cloud Services vonatkoznak, hacsak nem konfigurálta a DiagnosticsMonitorTraceListener a IaaS virtuális gépen futó alkalmazáson.

- Győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** konfigurálva van a web.config vagy app.config.  Ez alapértelmezés szerint a Cloud Service-projektekben van konfigurálva. Egyes ügyfelek azonban bemutatják, hogy a nyomkövetési utasítások nem lesznek összegyűjtve a diagnosztika alapján.

- Ha a naplók nem a **OnStart** vagy a **Run** metódusból íródnak, győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** szerepel a app.config.  Alapértelmezés szerint a web.configban van, de ez csak w3wp.exeon belül futó kódra vonatkozik. A WaIISHost.exeban futó Nyomkövetések rögzítéséhez app.config szükséges.

- Győződjön meg róla, hogy **Diagnostics. Trace. TraceXXX** használ a **Diagnostics. debug. WriteXXX helyett.** A hibakeresési utasítások el lesznek távolítva a kiadási buildből.

- Győződjön meg arról, hogy a lefordított kód ténylegesen rendelkezik a **diagnosztika. nyomkövetési sorokkal** (a tükrözés, a ildasm vagy a ILSpy használata az ellenőrzéshez). A **Diagnostics. Trace** parancsok el lesznek távolítva a lefordított bináris fájlból, kivéve, ha a nyomkövetési feltételes fordítási szimbólumot használja. Ez egy gyakori probléma, amely akkor fordul elő, ha az MSBuild-t használja projekt létrehozásához.   

## <a name="known-issues-and-mitigations"></a>Ismert problémák és enyhítések
Az ismert problémákkal kapcsolatos ismert problémák listáját itt találja:

**1. .NET 4,5-függőség**

A Windows Azure Diagnostics-bővítmény futásidejű függőséggel rendelkezik a .NET 4,5-keretrendszerben vagy újabb verzióban. Az írás időpontjában az Azure Cloud Services számára kiépített összes gép, valamint az Azure-beli virtuális gépeken alapuló összes hivatalos rendszerkép telepítve van a .NET 4,5-es vagy újabb verziójával.

Ha olyan gépen próbál Windows Azure Diagnostics-bővítményt futtatni, amely nem rendelkezik .NET 4,5-es vagy újabb verzióval, továbbra is lehetséges. Ez akkor történik meg, amikor egy régi rendszerképből vagy pillanatképből hozza létre a gépet, vagy ha saját egyéni lemezt használ.

Ez általában a **255** -es kilépési kód, amelyDiagnosticsPluginLauncher.exe futtatásakor jelentkezik **.** A hiba a következő kezeletlen kivétel miatt fordul elő:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Enyhítés:** Telepítse a .NET 4,5-es vagy újabb verzióját a gépen.

**2. a teljesítményszámlálók adatai a tárolóban érhetők el, de nem jelennek meg a portálon**

A virtuális gépeken a portál felülete alapértelmezés szerint bizonyos teljesítményszámlálókat jelenít meg. Ha nem látja a teljesítményszámlálókat, és biztos lehet benne, hogy az adat létrejött, mert elérhető a tárolóban, ellenőrizze a következőket:

- Azt határozza meg, hogy a tárolóban lévő összes számláló neve angol nyelven van-e. Ha a számlálók nevei nem angol nyelvűek, a portál metrikai diagramja nem tudja felismerni. Megoldás **: módosítsa** a gép nyelvét angolra a rendszerfiókok számára. Ehhez válassza a **Vezérlőpult**  >  **régió**  >  **felügyeleti**  >  **másolási beállítások** lehetőséget. Ezután törölje az **üdvözlőképernyő és a rendszerfiókok** kijelölését, hogy a rendszer ne alkalmazza az egyéni nyelvet a rendszerfiókra.

- Ha helyettesítő karaktereket () használ a \* teljesítményszámlálók neveiben, a portál nem fogja tudni összekapcsolni a konfigurált és az összegyűjtött számlálót, amikor a teljesítményszámlálók az Azure Storage-tárolóba kerülnek. Megoldás **: Győződjön** meg arról, hogy használhat helyettesítő karaktereket, és a portál kibontásával ( \* ) a teljesítményszámlálók átirányítása a Azure monitor fogadóba.
