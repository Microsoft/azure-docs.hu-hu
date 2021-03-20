---
title: SQL-áttekintések hibaelhárítása (előzetes verzió)
description: SQL-áttekintések hibaelhárítása Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609375"
---
# <a name="troubleshooting-sql-insights-preview"></a>SQL-áttekintések hibaelhárítása (előzetes verzió)
Az SQL-elemzések adatgyűjtési problémáinak elhárításához a figyelési gép állapota a **profil kezelése** lapon ellenőrizhető. Ennek a következő állapotok egyike lesz:

- Gyűjtése 
- Nem gyűjt 
- Gyűjtés hibákkal 
 
Kattintson az **állapotra** a részletezéshez a naplók és további részletek megtekintéséhez, ami segíthet a probléma megoldásában. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Számítógép figyelésének állapota":::

## <a name="not-collecting-state"></a>Nem gyűjti az állapotot 
A figyelő gép *nem gyűjti* az állapotot, ha az elmúlt 10 percben nem találhatók adatok az SQL- *InsightsMetrics* . 

Az SQL-információk az alábbi lekérdezést használják az adatok lekéréséhez:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Ellenőrizze, hogy van-e olyan napló a-grafből, amely segít azonosítani a problémát a probléma gyökerében. Naplóbejegyzések esetén kattintson a *nem gyűjtés* gombra, és ellenőrizze a gyakori problémákkal kapcsolatos naplókat és hibaelhárítási információkat. 


Ha nincsenek naplók, akkor a figyelés virtuális gépen található naplókat a következő, két virtuálisgép-bővítmény által telepített szolgáltatásokhoz kell ellenőriznie:

- Microsoft. Azure. monitor. AzureMonitorLinuxAgent 
  - Szolgáltatás: MDSD 
- Microsoft. Azure. monitor. számítási feladatok. munkaterhelés. WLILinuxExtension 
  - Szolgáltatás: WLI 
  - Szolgáltatás: MS-a Graf 
  - Szolgáltatás: TD-Agent-bit-WLI 
  - Bővítményi napló a telepítési hibák kereséséhez:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>WLI-szolgáltatási naplók 

Szolgáltatási naplók: `/var/log/wli.log`

A legutóbbi naplók megtekintéséhez: `tail -n 100 -f /var/log/wli.log`
 

Ha a következő hibanapló jelenik meg, az a **MDSD** szolgáltatással kapcsolatos problémát jelez.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>A Service-naplók 

Szolgáltatási naplók: `/var/log/ms-telegraf/telegraf.log`

A legutóbbi naplók megjelenítéséhez: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` a legutóbbi hiba-és figyelmeztetési naplók megtekintéséhez: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 A WLI szolgáltatás létrehozta a (z) által használt konfigurációt, és a következőket helyezi el: `/etc/ms-telegraf/telegraf.d/wli`
 
Ha egy helytelen konfigurációt generál az MS-a-Graf szolgáltatás, akkor a következő paranccsal ellenőrizze, hogy fut-e az MS-Service Graf szolgáltatás: `service ms-telegraf status`

A következő parancs futtatásával tekintheti meg a (z) a (z) Service-ből származó hibaüzeneteket manuálisan: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>MDSD-szolgáltatási naplók 

A Azure Monitor ügynök [aktuális korlátozásait](../agents/azure-monitor-agent-overview.md#current-limitations) vizsgálja meg. 


Szolgáltatási naplók:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

A legutóbbi hibák megtekintéséhez: `tail -n 100 -f /var/log/mdsd.err`

 Ha kapcsolatba kell lépnie az ügyfélszolgálattal, a következő információkat kell összegyűjtenie: 

- Naplók a `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- bejelentkezés `/var/log/waagent.log` 
- Naplók a `/var/log/mdsd*`
- Fájlok itt: `/etc/mdsd.d/`
- Fájl `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>A virtuális gép figyelésének beállítása érvénytelen

A *nem gyűjtő* állapot egyik oka, hogy a figyelési virtuálisgép-konfiguráció érvénytelen.  Az alapértelmezett konfiguráció a következő:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Ez a konfiguráció határozza meg a figyelési virtuális gépen a profil konfigurációjában használandó helyettesítő jogkivonatokat. Azt is lehetővé teszi, hogy Azure Key Vaulti titkokat hivatkozzon, így nem kell semmilyen konfigurációban megőriznie a titkos értékeket, ami erősen ajánlott.

#### <a name="secrets"></a>Titkos kulcsok
A titkok olyan tokenek, amelyek értékeit a rendszer a Azure Key Vault futási idején kéri le. A titkos kulcsot egy Key Vault hivatkozás és egy titkos név párja határozza meg. Ez lehetővé teszi Azure Monitor számára a titkos kulcs dinamikus értékének lekérését, és azt, hogy az alsóbb rétegbeli konfigurációs referenciákat használja.

A konfigurációban a szükséges számú titkot meghatározhatja, beleértve a különálló Kulcstartókban tárolt titkos kódokat is.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

A Key Vault eléréséhez szükséges engedélyek a figyelési virtuális gépen lévő Managed Service Identityhoz vannak biztosítva. Azure Monitor elvárja, hogy a Key Vault legalább titkos kulcsot kapjon a virtuális géphez. A Azure Portal, a PowerShell, a CLI vagy a Resource Manager-sablon használatával engedélyezheti.

#### <a name="parameters"></a>Paraméterek
A paraméterek olyan tokenek, amelyek a profil konfigurációjában JSON-sablon használatával hivatkozhatók. A paraméterek névvel és értékkel rendelkeznek. Az értékek tetszőleges JSON-típusok lehetnek, beleértve az objektumokat és a tömböket is. A profil konfigurációjában egy paramétert a rendszer az ebben az egyezményben szereplő névvel hivatkozik `.Parameters.<name>` .

A paraméterek hivatkozhatnak a Key Vault titkos kódokra ugyanezen egyezmény használatával. Például a `sqlAzureConnections` titokra hivatkozik az `telegrafPassword` egyezmény használatával `$telegrafPassword` .

Futásidőben az összes paraméter és titok fel lesz oldva, és a rendszer egyesíti a profil konfigurációját a számítógépen használni kívánt tényleges konfiguráció létrehozásához.

> [!NOTE]
> A `sqlAzureConnections` `sqlVmConnections` (z) és a (z) paraméterek nevei `sqlManagedInstanceConnections` mind a konfigurációban szükségesek, még akkor is, ha nem rendelkezik a hozzájuk tartozó kapcsolatok karakterláncokkal.


## <a name="collecting-with-errors-state"></a>Gyűjtés a hibák állapotával
Ha legalább egy *InsightsMetrics* -napló van, de a *művelet* táblában is hibák léptek fel, a figyelési gép állapota *hibákba* kerül.

Az SQL-információk az alábbi lekérdezésekkel kérik le ezeket az információkat:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

Gyakori esetekben hibaelhárítási ismereteket biztosítunk a naplók nézetben: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Hibaelhárítási naplók nézet.":::



## <a name="next-steps"></a>Következő lépések

- További információ az [SQL-információk engedélyezéséről](sql-insights-enable.md).
