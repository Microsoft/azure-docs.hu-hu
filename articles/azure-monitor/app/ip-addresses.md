---
title: Az Azure Monitor által használt IP-címek
description: A Application Insights által igényelt kiszolgálói tűzfal-kivételek
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 0e7028c59b4588ac23424a63f4e3ce240391dfb8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220802"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Az Azure Monitor által használt IP-címek
[Azure monitor](../overview.md) számos IP-címet használ. A Azure Monitor a Log Analytics és a Application Insights mellett a fő platform metrikái és a bejelentkezések is elérhetők. Előfordulhat, hogy ismernie kell ezeket a címeket, ha a figyelt alkalmazás vagy infrastruktúra tűzfal mögött található.

> [!NOTE]
> Habár ezek a címek statikusak, lehetséges, hogy időről időre módosítaniuk kell őket. Az összes Application Insights forgalom a kimenő forgalmat a rendelkezésre állás monitorozása és a beérkező tűzfalszabályok használatát igénylő webhookok kivételével jelenti.

> [!TIP]
> Ha Azure hálózati biztonsági csoportokat használ, használhatja az Azure [hálózati szolgáltatás címkéit](../../virtual-network/service-tags-overview.md) a hozzáférés kezelésére. Ha hibrid/helyszíni erőforrásokhoz való hozzáférést kezel, letöltheti az egyenértékű IP-címlistát [JSON-fájlként](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , amely minden héten frissül:. A cikkben szereplő összes kivétel lefedéséhez a következő szolgáltatást kell használnia: "ActionGroup", "ApplicationInsightsAvailability", "AzureMonitor".

Azt is megteheti, hogy RSS-hírcsatornáként előfizethet erre a lapra, ha hozzáadja a https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom kedvenc RSS/Atom-olvasóját, hogy értesítést kapjon a legújabb változásokról.


## <a name="outgoing-ports"></a>Kimenő portok
Meg kell nyitnia néhány kimenő portot a kiszolgálója tűzfalán, hogy a Application Insights SDK és/vagy Állapotmonitor számára lehetővé váljon az adatküldés a portálra:

| Rendeltetés | URL-cím | IP | Portok |
| --- | --- | --- | --- |
| Telemetria |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| Élő metrikastream | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Állapotmonitor
Állapotmonitor konfiguráció – csak a módosítások végrehajtásakor szükséges.

| Rendeltetés | URL-cím | IP | Portok |
| --- | --- | --- | --- |
| Konfiguráció |`management.core.windows.net` | |`443` |
| Konfiguráció |`management.azure.com` | |`443` |
| Konfiguráció |`login.windows.net` | |`443` |
| Konfiguráció |`login.microsoftonline.com` | |`443` |
| Konfiguráció |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfiguráció |`auth.gfx.ms` | |`443` |
| Konfiguráció |`login.live.com` | |`443` |
| Telepítés | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Rendelkezésre állási tesztek
Azon címek listája, amelyekről a [rendelkezésre állási webes tesztek](./monitor-web-app-availability.md) futnak. Ha webteszteket szeretne futtatni az alkalmazáson, de a webkiszolgálója meghatározott ügyfelek kiszolgálására korlátozódik, akkor engedélyeznie kell a rendelkezésre állási tesztelési kiszolgálókról érkező bejövő forgalmat.


> [!NOTE]
> A privát virtuális hálózatokon belül található, a nyilvános Azure-beli rendelkezésre állási tesztekkel nem rendelkező közvetlen bejövő kommunikációt nem engedélyező erőforrások esetében az egyetlen lehetőség a [saját, egyéni rendelkezésre állási tesztek létrehozása és üzemeltetése](availability-azure-functions.md).

### <a name="service-tag"></a>Szolgáltatáscímke

Ha Azure hálózati biztonsági csoportokat használ, egyszerűen adjon hozzá egy **bejövő portszabály-szabályt** , amely lehetővé teszi a Application Insights rendelkezésre állási tesztek adatforgalmát, ha a **szolgáltatás címkét** a **forrás-és** **ApplicationInsightsAvailability** adja meg a **forrásoldali szolgáltatás címkéje**.

>[!div class="mx-imgBorder"]
>![A beállítások területen válassza a bejövő biztonsági szabályok elemet, majd a lap tetején kattintson a Hozzáadás elemre. ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Bejövő biztonsági szabály hozzáadása lap](./media/ip-addresses/add-inbound-security-rule2.png)

Nyissa meg a 80 (http) és a 443 (https) portot a bejövő forgalomhoz a következő címekről (az IP-címek helye szerint vannak csoportosítva):

### <a name="addresses-grouped-by-location"></a>Címek helye szerint csoportosítva

> [!NOTE]
> Ezek a címek az osztály nélküli Inter-Domain Routing (CIDR) jelöléssel jelennek meg. Ez azt jelenti, hogy egy, a (z) értékkel `51.144.56.112/28` megegyező, 16 IP-címmel rendelkező bejegyzés a `51.144.56.112` következő időpontban kezdődik: `51.144.56.127` .

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

#### <a name="azure-government"></a>Azure Government

Nem szükséges, ha Ön Azure-beli nyilvános Felhőbeli ügyfél.

```
USGov Virginia
52.227.229.80/31


USGov Arizona
52.244.35.112/31


USGov Texas
52.243.157.80/31


USDoD Central
52.182.23.96/31


USDoD East
52.181.33.96/31

```

## <a name="application-insights--log-analytics-apis"></a>Application Insights & Log Analytics API-k

| Cél | URI |  IP | Portok |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80 443 |
| Azure pipeline-jegyzetek bővítmény |aigs1.aisvc.visualstudio.com |dinamikus|443 | 

## <a name="application-insights-analytics"></a>Application Insights Analitika

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Elemzési portál | analytics.applicationinsights.io | dinamikus | 80 443 |
| Tartalomkézbesítési hálózat (CDN) | applicationanalytics.azureedge.net | dinamikus | 80 443 |
| Media CDN | applicationanalyticsmedia.azureedge.net | dinamikus | 80 443 |

Megjegyzés: a *. applicationinsights.io tartomány Application Insights csapat tulajdonában van.

## <a name="log-analytics-portal"></a>Log Analytics portál

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Portál | portal.loganalytics.io | dinamikus | 80 443 |
| Tartalomkézbesítési hálózat (CDN) | applicationanalytics.azureedge.net | dinamikus | 80 443 |

Megjegyzés: a *. loganalytics.io tartomány tulajdonosa a Log Analytics csapata.

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure Portal bővítmény

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Application Insights bővítmény | stamp2.app.insightsportal.visualstudio.com | dinamikus | 80 443 |
| Application Insights-bővítmény CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dinamikus | 80 443 |

## <a name="application-insights-sdks"></a>SDK-k Application Insights

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | dinamikus | 80 443 |

## <a name="action-group-webhooks"></a>A műveleti csoport webhookai

A műveleti csoportok által használt IP-címek listáját a [Get-AzNetworkServiceTag PowerShell-paranccsal](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)kérdezheti le.

### <a name="action-groups-service-tag"></a>Műveleti csoportok szolgáltatás címkéje
A forrás IP-címek változásainak kezelése meglehetősen sok időt vehet igénybe. A **szolgáltatás-címkék** használatával nem kell frissítenie a konfigurációt. A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli az IP-címeket, és automatikusan frissíti a szolgáltatás címkéjét a címek változásával, így nincs szükség a műveleti csoport hálózati biztonsági szabályainak frissítésére.

1. A Azure Portal az Azure-szolgáltatások alatt keresse meg a *hálózati biztonsági csoportot*.
2. Kattintson a **Hozzáadás** gombra, és hozzon létre egy hálózati biztonsági csoportot.

   1. Adja hozzá az erőforráscsoport nevét, majd adja meg a *példány részleteit*.
   1. Kattintson a **felülvizsgálat + létrehozás** elemre, majd a *Létrehozás* gombra.
   
   :::image type="content" source="../platform/media/action-groups/action-group-create-security-group.png" alt-text="Példa hálózati biztonsági csoport létrehozására."border="true":::

3. Nyissa meg az erőforráscsoportot, majd kattintson a létrehozott *hálózati biztonsági csoportra* .

    1. Válassza a *bejövő biztonsági szabályok* lehetőséget.
    1. Kattintson a **Hozzáadás** gombra.
    
    :::image type="content" source="../platform/media/action-groups/action-group-add-service-tag.png" alt-text="Példa szolgáltatási címke hozzáadására."border="true":::

4. Ekkor megnyílik egy új ablak a jobb oldali ablaktáblán.
    1.  Forrás kiválasztása: **szolgáltatás címkéje**
    1.  Forrásoldali szolgáltatás címkéje: **ActionGroup**
    1.  Kattintson a **Hozzáadás** parancsra.
    
    :::image type="content" source="../platform/media/action-groups/action-group-service-tag.png" alt-text="Példa a szolgáltatási címke hozzáadására."border="true":::


## <a name="profiler"></a>Profilkészítő

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Ügynök | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | gateway.azureserviceprofiler.net | dinamikus | 443
| Tárolás | *.core.windows.net | dinamikus | 443

## <a name="snapshot-debugger"></a>Pillanatkép-hibakereső

> [!NOTE]
> A Profiler és a Snapshot Debugger ugyanazokat az IP-címeket használják.

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Ügynök | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | gateway.azureserviceprofiler.net | dinamikus | 443
| Tárolás | *.core.windows.net | dinamikus | 443

