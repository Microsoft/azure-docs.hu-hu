---
title: Erőforrások az Azure Sentinel egyéni összekötők létrehozásához | Microsoft Docs
description: Ismerje meg az Azure Sentinelhez készült egyéni összekötők létrehozásához rendelkezésre álló erőforrásokat. A módszerek közé tartozik a Log Analytics ügynök és az API, a Logstash, a Logic Apps, a PowerShell és a Azure Functions.
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
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724353"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Erőforrások az Azure Sentinel egyéni összekötők létrehozásához

Az Azure Sentinel számos [beépített összekötőt biztosít az Azure-szolgáltatásokhoz és a külső megoldásokhoz](connect-data-sources.md), valamint az adatok egyes forrásokból való betöltését is támogatja dedikált összekötő nélkül.

Ha nem tudja csatlakoztatni az adatforrást az Azure Sentinelhez az elérhető meglévő megoldások bármelyikével, érdemes lehet saját adatforrás-összekötőt létrehoznia.

A támogatott összekötők teljes listájáért tekintse meg az [Azure Sentinel: az összekötők Grand (CEF, syslog, Direct, Agent, Custom és more)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) blogbejegyzését.

## <a name="compare-custom-connector-methods"></a>Egyéni összekötő módszereinek összehasonlítása

A következő táblázat összehasonlítja a jelen cikkben ismertetett egyéni összekötők létrehozási módszereinek alapvető részleteit. Az egyes módszerekkel kapcsolatos további információkért kattintson a táblázatban található hivatkozásokra.

|Metódus leírása  |Képesség | Kiszolgáló nélküli    |Összetettség  |
|---------|---------|---------|---------|
|**[Log Analytics ügynök](#connect-with-the-log-analytics-agent)** <br>A legjobb megoldás a helyi és a IaaS-forrásokból származó fájlok gyűjtésére   | Csak a fájl gyűjtése  |   No      |Alacsony         |
|**[LogStash](#connect-with-logstash)** <br>A legjobb a helyszíni és a IaaS-forrásokhoz, az olyan forrásokhoz, amelyekhez elérhető egy beépülő modul, valamint a Logstash-t már ismerő szervezetek  | Az elérhető beépülő modulok és az egyéni bővítmények jelentős rugalmasságot biztosítanak.   |   Nem a virtuális gép vagy a virtuálisgép-fürt futtatására van szükség           |   Alacsony számos forgatókönyvet támogat a beépülő modulok esetében      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Magas költséghatékonyság; Kerülje a nagy mennyiségű adatforgalom elkerülését <br>A legjobb kis mennyiségű felhőalapú forrásokhoz  | A kód nélküli programozás lehetővé teszi a korlátozott rugalmasságot, és nem támogatja az algoritmusok megvalósítását.<br><br> Ha egy elérhető művelet már nem támogatja a követelményeit, az egyéni műveletek létrehozása bonyolultságot adhat.    |    Yes         |   Alacsony egyszerű, kódolású fejlesztés      |
|**[PowerShell](#connect-with-powershell)** <br>A legjobb a prototípusokhoz és a rendszeres fájlfeltöltésekhez | A fájlok gyűjtésének közvetlen támogatása. <br><br>A PowerShell segítségével további források gyűjthetők össze, de a kódolást és a parancsfájlt szolgáltatásként kell konfigurálni.      |No               |  Alacsony       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>A legjobb az integrációt megvalósító ISV-ket és az egyedi gyűjtési követelményekhez   | A kód összes funkcióját támogatja.  | A megvalósítástól függ           |     Magas    |
|**[Azure functions](#connect-with-azure-functions)** A legjobb a nagy mennyiségű felhőalapú forrásokhoz és az egyedi gyűjtési követelményekhez  | A kód összes funkcióját támogatja.  |  Yes             |     Magas programozási ismeretek szükségesek    |
|     |         |                |

> [!TIP]
> A Logic Apps és az Azure Functions azonos összekötőhöz való használatának összehasonlításához lásd:
> 
> - [A webalkalmazási tűzfal gyors betöltésének beolvasása az Azure Sentinel szolgáltatásba](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub-Közösség): [logikai alkalmazás-összekötő](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Function Connector](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>A Log Analytics-ügynökkel való kapcsolat

Ha az adatforrás fájlokat biztosít, javasoljuk, hogy az egyéni összekötő létrehozásához használja a Azure Monitor Log Analytics-ügynököt.

- További információ: [egyéni naplók gyűjtése Azure monitorban](../azure-monitor/agents/data-sources-custom-logs.md).

- Példa erre a módszerre: [Egyéni JSON-adatforrások gyűjtése a Linux rendszerhez készült log Analytics-ügynökkel Azure monitorban](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Kapcsolat a Logstash

Ha már ismeri a [Logstash](https://www.elastic.co/logstash)-t, érdemes lehet a Logstash-t használni az [Azure Sentinel Logstash kimeneti beépülő](connect-logstash.md) moduljával az egyéni összekötő létrehozásához.

Az Azure Sentinel Logstash kimeneti beépülő modullal bármilyen Logstash bemeneti és szűrési beépülő modult használhat, és konfigurálhatja az Azure sentinelt egy Logstash-folyamat kimenete. A Logstash nagyméretű beépülő modulokkal rendelkezik, amelyek különböző forrásokból, például Event Hubsokból, Apache Kafkaokból, fájlokból, adatbázisokból és felhőalapú szolgáltatásokból származó bevitelt tesznek lehetővé. Szűrési beépülő modulok használatával elemezheti az eseményeket, szűrheti a szükségtelen eseményeket, és eltorzíthatja az értékeket, és így tovább.

A Logstash egyéni összekötőként való használatára vonatkozó Példákért lásd:

- [Az AWS-naplók TTPs az Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog) használatával
- [Radware az Azure Sentinel megvalósítási útmutatója](https://support.radware.com/ci/okcsFattach/get/1025459_3)

A hasznos Logstash beépülő modulok példáit lásd:

- [Cloudwatch bemeneti beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage – bemeneti beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub bemeneti beépülő modul](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> A Logstash lehetővé teszi a méretezhető adatgyűjtést is egy fürt használatával. További információ: [elosztott terhelésű Logstash virtuális gép használata nagy méretekben](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Kapcsolat Logic Apps

Hozzon létre egy [Azure Logic app](../logic-apps/index.yml) -kiszolgálót, amely létrehoz egy kiszolgáló nélküli, egyéni összekötőt az Azure Sentinel számára.

> [!NOTE]
> A kiszolgáló nélküli összekötők a Logic Apps használatával való létrehozásakor kényelmesek lehetnek, ha az összekötők Logic Apps használatával nagy mennyiségű adattal is költséges lehet.
>
> Azt javasoljuk, hogy ezt a módszert csak kis mennyiségű adatforrások esetén használja, vagy bővítse az adatfeltöltő adatait.
>

1. A **Logic apps elindításához használja a következő eseményindítók egyikét**:

    |Eseményindító  |Leírás  |
    |---------|---------|
    |**Ismétlődő feladat**     |   Például úgy ütemezze a logikai alkalmazást, hogy az adatok rendszeres lekérdezését meghatározott fájlokból, adatbázisokból vagy külső API-kból olvassa be. <br>További információ: [ismétlődő feladatok és munkafolyamatok létrehozása, beosztása és futtatása Azure Logic Appsban](../connectors/connectors-native-recurrence.md).      |
    |**Igény szerinti indítás**     | Igény szerint futtathatja a logikai alkalmazást manuális adatgyűjtés és tesztelés céljából. <br>További információkért lásd:  [logikai alkalmazások hívása, elindítása vagy beágyazása HTTPS-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md).        |
    |**HTTP/S végpont**     |  A folyamatos átvitelhez ajánlott, és ha a forrásrendszer el tudja indítani az adatátvitelt. <br>További információ: szolgáltatás- [végpontok hívása http vagy https](../connectors/connectors-native-http.md)protokollon keresztül.       |
    |     |         |

1. Az **események beolvasásához használja az összes logikai alkalmazás-összekötőt**. Például:

    - [Kapcsolódás REST APIhoz](/connectors/custom-connectors/)
    - [Kapcsolódás SQL Serverhoz](/connectors/sql/)
    - [Kapcsolódás fájlrendszerhez](/connectors/filesystem/)

    > [!TIP]
    > A REST API-k, az SQL-kiszolgálók és a fájlrendszerek egyéni összekötői szintén támogatják az adatok helyszíni adatforrásokból való lekérését. További információ: a helyszíni [adatátjáró telepítésének](/connectors/filesystem/) dokumentációja.
    >

1. **Készítse elő a lekérdezni kívánt adatokat**.

    Például a [JSON elemzése művelettel](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) elérheti a JSON-tartalmak tulajdonságait, így kiválaszthatja ezeket a tulajdonságokat a dinamikus tartalmak listájából, ha a logikai alkalmazás bemeneteit adja meg.

    További információ: [adatműveletek végrehajtása a Azure Logic Appsban](../logic-apps/logic-apps-perform-data-operations.md).

1. **Az log Analyticsba írja az** adatbevitelt.

    További információt az [Azure log Analytics adatgyűjtő](/connectors/azureloganalyticsdatacollector/) dokumentációjában talál.

Példa arra, hogyan hozhat létre egyéni összekötőt az Azure Sentinelhez Logic Apps használatával:

- [Adatfolyamat létrehozása az adatgyűjtő API-val](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic app-összekötő webhook használatával](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub-Közösség)
- [A Microsoft Teams-hívások biztonságossá tétele ütemezett aktiválással](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [ALIENVAULT OTX-veszélyforrások indikátorának beolvasása az Azure sentinelbe](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Proofpoint-koppintó naplók küldése az Azure sentinelnek](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Kapcsolódás a PowerShell-lel

A [upload-AzMonitorLog PowerShell-parancsfájl](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) lehetővé teszi, hogy a PowerShell használatával adatfolyamokat továbbítson az események és a környezeti információk számára az Azure Sentinel számára a parancssorból. Ez a streaming hatékonyan létrehoz egy egyéni összekötőt az adatforrás és az Azure Sentinel között.

Az alábbi szkript például egy CSV-fájlt tölt fel az Azure Sentinelbe:

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

A [upload-AzMonitorLog PowerShell parancsfájl-](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) parancsfájl a következő paramétereket használja:

|Paraméter  |Leírás  |
|---------|---------|
|**Munkaterület azonosítója**     |   Az Azure Sentinel-munkaterület azonosítója, ahol az adatait tárolja.  [Keresse meg a munkaterület azonosítóját és kulcsát](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Az Azure Sentinel munkaterület elsődleges vagy másodlagos kulcsa, ahol az adatait tárolni fogja. [Keresse meg a munkaterület azonosítóját és kulcsát](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Annak az egyéni naplózási táblának a neve, ahol az adattárolást el szeretné tárolni. A rendszer automatikusan hozzáadja a **_CL** utótagját a tábla nevének végéhez.  |
|**AddComputerName**     |   Ha ez a paraméter létezik, a parancsfájl hozzáadja a számítógép aktuális nevét minden naplófájlhoz a **számítógép** nevű mezőben.      |
|**TaggedAzureResourceId**     | Ha ez a paraméter létezik, a parancsfájl társítja az összes feltöltött napló-rekordot a megadott Azure-erőforrással. <br><br>Ez a társítás lehetővé teszi az erőforrás-kontextusú lekérdezések feltöltött naplófájljainak használatát, és betartja az erőforrás-központú, szerepköralapú hozzáférés-vezérlést.       |
|**AdditionalDataTaggingName**     |      Ha ez a paraméter létezik, a parancsfájl egy másik mezőt vesz fel minden naplófájlba, a konfigurált névvel és a **AdditionalDataTaggingValue** paraméterhez konfigurált értékkel. <br><br>Ebben az esetben a **AdditionalDataTaggingValue** nem lehet üres. |
|**AdditionalDataTaggingValue**     |   Ha ez a paraméter létezik, a parancsfájl egy másik mezőt vesz fel minden naplófájlba, a beállított értékkel és a **AdditionalDataTaggingName** paraméterhez konfigurált mezőnevet. <br><br>Ha a **AdditionalDataTaggingName** paraméter üres, de egy érték van konfigurálva, akkor az alapértelmezett mezőnév a **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>A munkaterület AZONOSÍTÓjának és kulcsának megkeresése

A **munkaterület azonosítója** és a **WorkspaceKey** paraméterek részleteinek megkeresése az Azure sentinelben:

1. Az Azure Sentinelben válassza a bal oldali **Beállítások** lehetőséget, majd válassza a **munkaterület-beállítások** lapot.

1. Az első **lépések az log Analytics**  >  **1 adatforrással való összekapcsolásához válassza a** **Windows-és Linux-ügynökök kezelése** lehetőséget.

1. Keresse meg a munkaterület-azonosítót, az elsődleges kulcsot és a másodlagos kulcsot a **Windows Servers** lapokon.
## <a name="connect-with-the-log-analytics-api"></a>A Log Analytics API-val való kapcsolat

Az Azure Sentinel Log Analytics adatgyűjtő API használatával közvetítheti az eseményeket, ha a REST-végpontot közvetlenül hívja meg.

A REST-végpontok közvetlen meghívása során még több programozásra van szükség, ugyanakkor nagyobb rugalmasságot biztosít.

További információkért tekintse meg a [log Analytics adatgyűjtő API](../azure-monitor/logs/data-collector-api.md)-t, különösen az alábbi példákat:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Kapcsolat Azure Functions

A kiszolgáló nélküli egyéni összekötők létrehozásához használja a Azure Functions és a REST API-t, valamint a különböző kódolási nyelveket, például a [PowerShellt](../azure-functions/functions-reference-powershell.md).

Példa erre a módszerre:

- [A VMware Carbon Black Cloud Endpoint standard összekötése az Azure Sentinel és az Azure Function használatával](connect-vmware-carbon-black.md)
- [Az okta egyetlen Sign-On összekötése az Azure Sentinel szolgáltatással az Azure Function használatával](connect-okta-single-sign-on.md)
- [A Proofpoint KOPPINTson az Azure Sentinelre az Azure-függvény használatával](connect-proofpoint-tap.md)
- [A Qualys virtuális gép összekötése az Azure Sentinel szolgáltatással az Azure Function használatával](connect-qualys-vm.md)
- [XML-, CSV-vagy egyéb adatformátumok beolvasása](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Nagyítás figyelése az Azure sentinelrel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Függvényalkalmazás üzembe helyezése az Office 365 Management API-beli adatgyűjtéshez az Azure sentinelben](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel GitHub-Közösség)

## <a name="parse-your-custom-connector-data"></a>Egyéni összekötő-adatelemzés

Az egyéni összekötő beépített elemzési technikájának használatával kinyerheti a kapcsolódó információkat, és feltöltheti a megfelelő mezőket az Azure Sentinelben.

Például:

- **Ha már használta a Logstash**-t, az [grokkolom](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) szűrő beépülő modullal elemezheti az adatait.
- **Ha már használt egy Azure-függvényt**, a kóddal elemezheti az adatait. További információ: [elemzők](normalization.md#parsers).

Az Azure Sentinel a lekérdezési időpontnál támogatja az elemzést. A lekérdezési időpontban történő elemzés lehetővé teszi az adatküldést az eredeti formátumban, majd igény szerint elemezni, ha szükséges.

A lekérdezési idő elemzése azt is jelenti, hogy az egyéni összekötő létrehozásakor, vagy akár a kinyerni kívánt információnál nem kell megismernie az adatok pontos szerkezetét. Ehelyett bármikor elemezheti az adatait, még a vizsgálat során is.

> [!NOTE]
> Az elemző frissítése az Azure Sentinel szolgáltatásban már betöltött adataira is érvényes.
> 
## <a name="next-steps"></a>Következő lépések

Az Azure Sentinelbe betöltött adataival biztosíthatja környezetét az alábbi folyamatok bármelyikével:

- [Áttekinthető riasztások](quickstart-get-visibility.md)
- [Az adataik megjelenítése és figyelése](tutorial-monitor-your-data.md)
- [Incidensek vizsgálata](tutorial-investigate-cases.md)
- [Fenyegetésészlelés](tutorial-detect-threats-built-in.md)
- [A fenyegetéselhárítás automatizálása](tutorial-respond-threats-playbook.md)
- [Fenyegetések keresése](hunting.md)