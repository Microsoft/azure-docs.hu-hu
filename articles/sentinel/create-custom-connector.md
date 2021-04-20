---
title: Erőforrások egyéni Azure Sentinel összekötők létrehozásához | Microsoft Docs
description: Ismerje meg az egyéni összekötők létrehozásához rendelkezésre álló erőforrásokat a Azure Sentinel. A módszerek közé tartozik a Log Analytics-ügynök és az API, a Logstash, Logic Apps, a PowerShell és a Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: a1aaf89624f8d0ab48692629d859f3c1bdb4ba67
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738899"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Erőforrások egyéni Azure Sentinel létrehozásához

Azure Sentinel azure-szolgáltatásokhoz és külső megoldásokhoz számos beépített összekötőt [biztosít,](connect-data-sources.md)és támogatja az adatok bizonyos forrásokból, dedikált összekötő nélküli becslését is.

Ha nem sikerül csatlakoztatnia az adatforrást a Azure Sentinel meglévő megoldások bármelyikének használatával, fontolja meg egy saját adatforrás-összekötő létrehozását.

A támogatott összekötők teljes listájáért tekintse meg a [Azure Sentinel: The connectors grand (CEF, Syslog, Direct, Agent, Custom](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) stb.) blogbejegyzést.

## <a name="compare-custom-connector-methods"></a>Egyéni összekötők módszereinek összehasonlítása

Az alábbi táblázat az ebben a cikkben ismertetett egyéni összekötők létrehozásának minden módszerével kapcsolatos alapvető részleteket hasonlítja össze. Az egyes metódusokkal kapcsolatos további részletekért kattintson a táblázatban található hivatkozásokra.

|Metódus leírása  |Képesség | Kiszolgáló nélküli    |Összetettség  |
|---------|---------|---------|---------|
|**[Log Analytics-ügynök](#connect-with-the-log-analytics-agent)** <br>A legjobb választás fájlok helyszíni és IaaS-forrásokból való gyűjtéséhez   | Csak fájlgyűjtemény  |   No      |Alacsony         |
|**[LogStash](#connect-with-logstash)** <br>A legjobb választás helyszíni és IaaS-forrásokhoz, bármilyen forráshoz, amelyhez elérhető beépülő modul, és a Logstasht már jól ható szervezeteknek  | Az elérhető beépülő modulok, valamint az egyéni beépülő modulok jelentős rugalmasságot biztosítanak.   |   Nem; virtuális gép vagy virtuálisgép-fürt futtatását igényli           |   Alacsony; számos beépülő modult támogató forgatókönyvet támogat      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Magas költségek; kerülje a nagy mennyiségű adatot <br>Kis mennyiségű felhőbeli forrásokhoz ajánlott  | A kód nélküli programozás korlátozott rugalmasságot tesz lehetővé algoritmusok megvalósítása nélkül.<br><br> Ha még nem áll rendelkezésre művelet, az egyéni művelet létrehozása összetettebbé teszi a követelményeket.    |    Yes         |   Alacsony; egyszerű, kód nélküli fejlesztés      |
|**[PowerShell](#connect-with-powershell)** <br>Prototípus-hoz és rendszeres fájlfeltöltéshez ajánlott | A fájlgyűjtés közvetlen támogatása. <br><br>A PowerShell használatával több forrást is gyűjthet, de ehhez kódolásra és a szkript szolgáltatásként való konfigurálására van szükség.      |No               |  Alacsony       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>Integrációt végrehajtó ISV-khez és egyedi gyűjtési követelményekhez ajánlott   | A kóddal elérhető összes képességet támogatja.  | A megvalósítástól függ           |     Magas    |
|**[Azure Functions](#connect-with-azure-functions)** Nagy mennyiségű felhőbeli forráshoz és egyedi gyűjtési követelményekhez a legjobb  | A kóddal elérhető összes képességet támogatja.  |  Yes             |     Magas; programozási ismereteket igényel    |
|     |         |                |

> [!TIP]
> Az egy összekötőhöz Logic Apps Azure Functions összekötők használatának összehasonlításához lásd:
> 
> - [Gyorsan be kell Web Application Firewall naplókat a Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub-közösség): [Logic Alkalmazás-összekötő](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function-összekötő](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Kapcsolódás a Log Analytics-ügynökkel

Ha az adatforrás fájlokban kézbesít eseményeket, javasoljuk, hogy az Azure Monitor Log Analytics-ügynök használatával hozza létre az egyéni összekötőt.

- További információ: [Egyéni naplók gyűjtése a Azure Monitor.](../azure-monitor/agents/data-sources-custom-logs.md)

- Erre a módszerre az egyéni JSON-adatforrások gyűjtése [a Linuxhoz](../azure-monitor/agents/data-sources-json.md)készült Log Analytics-ügynökkel a következő Azure Monitor.

## <a name="connect-with-logstash"></a>Kapcsolódás Logstash-sel

Ha már ismeri a [Logstasht,](https://www.elastic.co/logstash)az egyéni összekötő létrehozásához használhatja a Logstasht a [Logstash](connect-logstash.md) kimeneti beépülő modulját Azure Sentinel összekötő létrehozásához.

A Azure Sentinel Logstash Output beépülő modullal bármilyen Logstash bemeneti és szűrő beépülő modult használhat, és konfigurálhatja a Azure Sentinel-t a Logstash-folyamat kimeneteként. A Logstash számos beépülő modult tartalmaz, amelyek különböző forrásokból, például a Event Hubs, Apache Kafka, fájlokból, adatbázisokból és felhőszolgáltatásokból származó bemenetet teszik lehetővé. Szűrési beépülő modulok használatával elemezheti az eseményeket, szűrheti a szükségtelen eseményeket, eltűrheti az értékeket stb.

Példák a Logstash egyéni összekötőként való használatára:

- [A Capital One Breach TTPs-t használó AWS-naplókban](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) való Azure Sentinel (blog)
- [A Radware Azure Sentinel implementáció útmutatója](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Példák hasznos Logstash beépülő modulokra:

- [Cloudwatch bemeneti beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage bemeneti beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> A Logstash fürt használatával is lehetővé teszi a skálázható adatgyűjtést. További információ: Elosztott terhelésű [Logstash virtuális gép használata nagy méretekben.](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)
>

## <a name="connect-with-logic-apps"></a>Csatlakozás Logic Apps

Az [Azure Logic App használatával](../logic-apps/index.yml) kiszolgáló nélküli, egyéni összekötőt hozhat létre a Azure Sentinel.

> [!NOTE]
> Bár a kiszolgáló nélküli összekötők Logic Apps kényelmes lehet, a Logic Apps használata az összekötőkhöz költséges lehet nagy mennyiségű adat esetén.
>
> Javasoljuk, hogy ezt a módszert csak kis mennyiségű adatforráshoz vagy adatfeltöltések gazdagítására használja.
>

1. **A következő eseményindítók egyikével indítsa el a Logic Apps:**

    |Eseményindító  |Description  |
    |---------|---------|
    |**Ismétlődő feladat**     |   Ütemezheti például úgy a logikai alkalmazást, hogy rendszeresen lekérje az adatokat adott fájlokból, adatbázisokból vagy külső API-kból. <br>További információ: Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása a [Azure Logic Apps.](../connectors/connectors-native-recurrence.md)      |
    |**Igény szerinti triggerek**     | A logikai alkalmazást igény szerint futtatva manuális adatgyűjtést és -tesztelést is futtathat. <br>További információ: Logikai alkalmazások hívása, aktiválása vagy [beágyazása HTTPS-végpontok használatával.](../logic-apps/logic-apps-http-endpoint.md)        |
    |**HTTP/S-végpont**     |  Ajánlott a streameléshez, és ha a forrásrendszer el tudja kezdeni az adatátvitelt. <br>További információ: [Szolgáltatásvégpont hívása HTTP- vagy HTTP-protokollon keresztül.](../connectors/connectors-native-http.md)       |
    |     |         |

1. **Az események lekért információk beolvassa a logikaialkalmazás-összekötők bármelyikét.** Például:

    - [Csatlakozás REST API](/connectors/custom-connectors/)
    - [Csatlakozás SQL Server](/connectors/sql/)
    - [Csatlakozás fájlrendszerhez](/connectors/filesystem/)

    > [!TIP]
    > A REST API-k, SQL-kiszolgálók és fájlrendszerek egyéni összekötői szintén támogatják az adatok helyszíni adatforrásból való leolvasását. További információ: [Helyszíni](/connectors/filesystem/) adatátjáró telepítése – dokumentáció.
    >

1. **Készítse elő a lekérni kívánt információkat.**

    A JSON-elemzési művelettel például hozzáférhet a [JSON-tartalom](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) tulajdonságaihoz, és kiválaszthatja ezeket a tulajdonságokat a dinamikus tartalmak listájából, amikor bemeneteket ad meg a logikai alkalmazáshoz.

    További információ: [Adatműveletek végrehajtása a Azure Logic Apps.](../logic-apps/logic-apps-perform-data-operations.md)

1. **Írja az adatokat a Log Analyticsbe.**

    További információt az [Azure Log Analytics Data Collector dokumentációjában](/connectors/azureloganalyticsdatacollector/) talál.

Példák arra, hogyan hozhat létre egyéni összekötőt a Azure Sentinel a Logic Apps használatával:

- [Folyamat létrehozása a Data Collector API-val](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic Alkalmazás-összekötő webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) használatával (Azure Sentinel GitHub-közösség)
- [Microsoft Teams-hívások biztonságossá biztosítása ütemezett aktiválással](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [A](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) Azure Sentinel(blog)

## <a name="connect-with-powershell"></a>Kapcsolódás a PowerShell-lel

Az [Upload-AzMonitorLog PowerShell-szkript](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) lehetővé teszi, hogy a PowerShell használatával eseményeket vagy környezeti Azure Sentinel a parancssorból. Ez a streamelés hatékonyan létrehoz egy egyéni összekötőt az adatforrás és a Azure Sentinel.

Az alábbi szkript például feltölt egy CSV-fájlt a Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Az [Upload-AzMonitorLog PowerShell-szkript](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) a következő paramétereket használja:

|Paraméter  |Leírás  |
|---------|---------|
|**WorkspaceId (Munkaterület-azonosító)**     |   A Azure Sentinel azonosítója, ahol az adatokat fogja tárolni.  [Keresse meg a munkaterület azonosítóját és kulcsát.](#find-your-workspace-id-and-key)  |
|**WorkspaceKey (Munkaterületkulcs)**     |   A munkaterület elsődleges vagy másodlagos Azure Sentinel, ahol az adatokat tárolni fogja. [Keresse meg a munkaterület azonosítóját és kulcsát.](#find-your-workspace-id-and-key)  |
|**LogTypeName (LogTypeName)**     |    Annak az egyéni naplótáblának a neve, ahol az adatokat tárolni szeretné. A _CL **utótagja** automatikusan hozzá lesz adva a tábla nevének véghez.  |
|**AddComputerName (Számítógépnév hozzáadása)**     |   Ha ez a paraméter létezik, a parancsfájl hozzáadja az aktuális számítógépnevet minden naplórekordhoz a **Computer (Számítógép) nevű mezőben.**      |
|**TaggedAzureResourceId**     | Ha ez a paraméter létezik, a szkript az összes feltöltött naplórekordot a megadott Azure-erőforráshoz társítja. <br><br>Ez a társítás lehetővé teszi a feltöltött naplórekordok számára az erőforrás-környezet lekérdezéseit, és megfelel az erőforrás-központú, szerepköralapú hozzáférés-vezérlésnek.       |
|**AdditionalDataTaggingName (További adatcímkézés neve)**     |      Ha ez a paraméter létezik, a szkript egy újabb mezőt ad hozzá minden naplórekordhoz a konfigurált névvel és az **AdditionalDataTaggingValue** paraméterhez konfigurált értékkel. <br><br>Ebben az esetben az **AdditionalDataTaggingValue nem** lehet üres. |
|**AdditionalDataTaggingValue (További adatok címkézése érték)**     |   Ha ez a paraméter létezik, a szkript egy újabb mezőt ad hozzá minden naplórekordhoz a konfigurált értékkel és az **AdditionalDataTaggingName** paraméterhez konfigurált mezőnévvel. <br><br>Ha az **AdditionalDataTaggingName** paraméter üres, de egy érték van konfigurálva, az alapértelmezett mezőnév **a DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>A munkaterület azonosítójának és kulcsának megkeresése

A **WorkspaceID** és **a WorkspaceKey** paraméterek részleteit a következő Azure Sentinel:

1. A Azure Sentinel bal **oldalon válassza** a Beállítások lehetőséget, majd a Munkaterület **beállításai** lapot.

1. Az **Első lépések a Log Analytics**  >  **1-ben Adatforrás csatlakoztatása** alatt válassza a **Windows- és Linux-ügynökök kezelése lehetőséget.**

1. Keresse meg a munkaterület azonosítóját, az elsődleges kulcsot és a másodlagos kulcsot a **Windows-kiszolgálók lapokon.**
## <a name="connect-with-the-log-analytics-api"></a>Kapcsolódás a Log Analytics API-val

A ReSTful-végpontok Azure Sentinel Log Analytics Data Collector API-val streamelheti az eseményeket a restful-végpontokra.

Bár a RESTful-végpontok közvetlen hívása több programozást igényel, nagyobb rugalmasságot is biztosít.

További információkért lásd a [Log Analytics Data Collector API-t,](../azure-monitor/logs/data-collector-api.md)különösen az alábbi példákat:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Csatlakozás Azure Functions

A Azure Functions EGY RESTful API-val és különböző kódolási nyelvekkel, például a [PowerShell-sel](../azure-functions/functions-reference-powershell.md)együtt használva hozhat létre kiszolgáló nélküli egyéni összekötőt.

Példák erre a módszerre:

- [A VMware Carbon Black Cloud Endpoint Standard csatlakoztatása Azure Sentinel Azure-függvényhez](connect-vmware-carbon-black.md)
- [Az Egyetlen Okta-Sign-On csatlakoztatása Azure Sentinel Azure-függvényhez](connect-okta-single-sign-on.md)
- [Proofpoint TAP csatlakoztatása Azure Sentinel Azure-függvény használatával](connect-proofpoint-tap.md)
- [A Qualys virtuális gép csatlakoztatása Azure Sentinel Azure-függvény használatával](connect-qualys-vm.md)
- [XML-, CSV- vagy egyéb adatformátumok importálása](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Nagyítás monitorozása Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Függvényalkalmazás üzembe helyezése az Office 365-Alkalmazáskezelés API](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) adatok Azure Sentinel (Azure Sentinel GitHub-közösség) számára

## <a name="parse-your-custom-connector-data"></a>Az egyéni összekötő adatainak elemezve

Az egyéni összekötő beépített elemzési technikáját használva kinyerheti a releváns információkat, és feltöltheti a megfelelő mezőket a Azure Sentinel.

Például:

- **Ha már használta a Logstasht,** a [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) filter beépülő modullal elemezheti az adatokat.
- **Ha már használt Azure-függvényt,** elemezze az adatokat kóddal. További információ: [Elemzők.](normalization.md#parsers)

Azure Sentinel támogatja a lekérdezéskor való elemezéseket. A lekérdezéskor való elemezés lehetővé teszi az adatok eredeti formátumban való leküldését, majd igény szerinti, igény szerinti elemezését.

A lekérdezési időpontban való elemezés azt is jelenti, hogy nem kell előre tudni az adatok pontos szerkezetét, az egyéni összekötő létrehozásakor, vagy akár a kinyerni szükséges információkat. Ehelyett bármikor elemezze az adatokat, akár vizsgálat közben is.

> [!NOTE]
> Az elemző frissítése azokra az adatokra is vonatkozik, amelyek már be vannak helve a Azure Sentinel.
> 
## <a name="next-steps"></a>Következő lépések

A Azure Sentinel adatok használatával az alábbi folyamatok bármelyikével biztosíthatja a környezet védelmét:

- [Áttekinthető riasztások](quickstart-get-visibility.md)
- [Adatok vizualizációja és monitorezése](tutorial-monitor-your-data.md)
- [Incidensek vizsgálata](tutorial-investigate-cases.md)
- [Fenyegetésészlelés](tutorial-detect-threats-built-in.md)
- [A fenyegetéselhárítás automatizálása](tutorial-respond-threats-playbook.md)
- [Fenyegetések keresése](hunting.md)
