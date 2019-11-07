---
title: A hozzáférési naplók, a Teljesítménynaplók, a háttérbeli állapot és a metrikák figyelése az Azure Application Gateway
description: Ismerje meg, hogyan engedélyezheti és kezelheti az Azure-beli hozzáférési naplókat és Teljesítménynaplók Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: 9e1fe0e5bae462715a8cb2950cca100f0f409325
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718728"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Application Gateway háttérbeli állapot-és diagnosztikai naplói

Az Azure Application Gateway használatával az alábbi módokon figyelheti az erőforrásokat:

* [Háttérbeli állapot](#back-end-health): Application Gateway lehetővé teszi a háttérbeli készletekben lévő kiszolgálók állapotának figyelését a Azure Portal és a PowerShell használatával. A háttér-készletek állapotát a teljesítmény diagnosztikai naplóin keresztül is megtalálhatja.

* [Naplók](#diagnostic-logging): a naplók lehetővé teszik a teljesítmény, hozzáférés és egyéb adatok mentését, illetve az erőforrásokból való felhasználását figyelés céljából.

* [Metrikák](application-gateway-metrics.md): Application Gateway több metrikával rendelkezik, amelyek segítségével ellenőrizheti, hogy a rendszer a várt módon működik-e.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Háttér állapota

A Application Gateway lehetővé teszi a háttér-készletek egyes tagjai állapotának figyelését a portál, a PowerShell és a parancssori felület (CLI) segítségével. A háttér-készletek összesített állapotának összegzését a teljesítmény diagnosztikai naplóin keresztül is megtalálhatja. 

A háttér-állapot jelentés a Application Gateway Health-mintavétel kimenetét tükrözi a háttérbeli példányok számára. Ha a szondázás sikeres volt, és a háttérrendszer fogadja a forgalmat, akkor a rendszer kifogástalannak tekinti. Ellenkező esetben nem megfelelő állapotú.

> [!IMPORTANT]
> Ha van hálózati biztonsági csoport (NSG) egy Application Gateway alhálózaton, nyissa meg a 65503-65534-es portot a bejövő forgalom Application Gateway alhálózatán. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. A portokat Azure-tanúsítványok védik (zárják le). A megfelelő tanúsítványok nélkül a külső entitások, beleértve az átjárók ügyfeleit, nem fognak tudni semmilyen változást kezdeményezni ezeken a végpontokon.


### <a name="view-back-end-health-through-the-portal"></a>Háttérbeli állapot megtekintése a portálon keresztül

A portálon a háttér állapota automatikusan elérhető. Egy meglévő Application Gateway-ben válassza a **figyelés** > **háttér állapota**lehetőséget. 

Ezen az oldalon a háttér-készlet minden tagja megjelenik (legyen az a hálózati adapter, az IP-cím vagy a teljes tartománynév). Megjelenik a háttér-készlet neve, a port, a háttérbeli HTTP-beállítások neve és az állapot állapota. Az állapot állapota **kifogástalan**, **sérült**és **ismeretlen**.

> [!NOTE]
> Ha a háttér állapota **ismeretlen**, akkor győződjön meg arról, hogy a NSG-szabály, a felhasználó által megadott útvonal (UDR) vagy a virtuális hálózatban lévő egyéni DNS nem blokkolja a háttér elérését.

![Háttér állapota][10]

### <a name="view-back-end-health-through-powershell"></a>Háttér állapotának megtekintése a PowerShell-lel

A következő PowerShell-kód bemutatja, hogyan tekintheti meg a háttér-állapotot a `Get-AzApplicationGatewayBackendHealth` parancsmag használatával:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Háttérbeli állapot megtekintése az Azure CLI-n keresztül

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Results (Eredmények)

A következő kódrészlet a válasz példáját mutatja:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Diagnosztikai naplók

Az Azure-ban különféle típusú naplókat használhat az Application Gateway-alkalmazások kezeléséhez és hibakereséséhez. Ezen naplók egy része a portálról érhető el. Az összes napló kinyerhető az Azure Blob Storage-ból, és különböző eszközökön (például [Azure monitor naplók](../azure-monitor/insights/azure-networking-analytics.md), Excel és Power BI) is megtekinthető. További információ a naplók különböző típusairól a következő listából:

* **Műveletnapló**: az Azure- [tevékenység naplófájljai](../monitoring-and-diagnostics/insights-debugging-with-events.md) (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéshez elküldött összes műveletet, valamint azok állapotát. A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.
* **Hozzáférési napló**: a napló használatával megtekintheti Application Gateway hozzáférési mintákat, és elemezheti a fontos információkat. Ide tartozik a hívó IP-címe, a kért URL-cím, a válasz késése, a visszatérési kód és a kimenő bájtok. A rendszer minden 300 másodpercenként gyűjti a hozzáférési naplókat. Ez a napló a Application Gateway példányán egy rekordot tartalmaz. A Application Gateway példányt a instanceId tulajdonság azonosítja.
* **Teljesítménynapló**: ezt a naplót használhatja a Application Gateway-példányok teljesítményének megjelenítéséhez. Ez a napló rögzíti az egyes példányok teljesítményadatait, beleértve a kiszolgált kérelmek teljes számát, az átviteli sebességet, a kiszolgált kérelmek számát, a sikertelen kérelmek számát, valamint az kifogástalan és a nem megfelelő állapotú példányok számát. A rendszer minden 60 másodpercenként gyűjti a teljesítményadatokat. A Teljesítménynapló csak a v1 SKU-hoz érhető el. A v2 SKU esetében használja a teljesítményadatok [mérőszámait](application-gateway-metrics.md) .
* **Tűzfal naplója**: a napló használatával megtekintheti a webalkalmazási tűzfallal konfigurált Application Gateway észlelési vagy megelőzési módjában naplózott kérelmeket.

> [!NOTE]
> A naplók csak az Azure Resource Manager üzemi modellben üzembe helyezett erőforrásokhoz érhetők el. A klasszikus üzemi modell erőforrásaihoz nem használhat naplókat. A két modell jobb megismeréséhez tekintse meg a [Resource Manager-telepítés és a klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.

A naplók tárolásához három lehetőség közül választhat:

* **Storage-fiók**: A Storage-fiókok akkor a legmegfelelőbbek a naplók tárolására, ha a naplókat hosszabb ideig tárolják, és szükség esetén áttekintik őket.
* **Event hub**-EK: az Event hubok nagyszerű lehetőséget biztosítanak a más biztonsági információk és ESEMÉNYKEZELŐ (SIEM) eszközök integrálására, hogy riasztásokat szerezzenek az erőforrásokról.
* **Azure monitor naplók**: a rendszer a Azure monitor naplókat az alkalmazás általános valós idejű figyelésére, vagy trendek keresésére használja.

### <a name="enable-logging-through-powershell"></a>Naplózás engedélyezése a PowerShell-lel

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Az ezen naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez engedélyeznie kell a hozzáférés és a teljesítmény naplózását. A naplózás engedélyezéséhez kövesse az alábbi lépéseket:

1. Jegyezze fel azon Storage-fiók erőforrás-azonosítóját, ahol a naplóadatokat tárolja. Ez az érték a következő:/Subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport neve\>/providers/Microsoft.Storage/storageAccounts/\<Storage-fiók neve\>. Az előfizetés bármilyen tárfiókját használhatja. Ezeket az információkat az Azure Portalon találhatja meg.

    ![Portál: a Storage-fiók erőforrás-azonosítója](./media/application-gateway-diagnostics/diagnostics1.png)

2. Jegyezze fel az Application Gateway azon erőforrás-AZONOSÍTÓját, amelyhez a naplózás engedélyezve van. Ez az érték a következő:/Subscriptions/\<subscriptionId\>/resourceGroups/\<erőforráscsoport neve\>/providers/Microsoft.Network/applicationGateways/\<Application Gateway Name\>. Ezeket az információkat a portálon találhatja meg.

    ![Portál: az Application Gateway erőforrás-azonosítója](./media/application-gateway-diagnostics/diagnostics2.png)

3. Engedélyezze a diagnosztikai naplózást az alábbi PowerShell-parancsmaggal:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>A Tevékenységnaplók nem igényelnek külön Storage-fiókot. A Storage hozzáférés- és teljesítménynaplózásra való használata szolgáltatási díjjal jár.

### <a name="enable-logging-through-the-azure-portal"></a>Naplózás engedélyezése az Azure Portalon

1. A Azure Portal keresse meg az erőforrást, és válassza a **diagnosztikai beállítások**lehetőséget.

   Application Gateway esetén három napló érhető el:

   * Hozzáférési napló
   * Teljesítménynapló
   * Tűzfal naplója

2. Az adatok gyűjtésének megkezdéséhez kattintson **a diagnosztika bekapcsolása**elemre.

   ![Diagnosztika bekapcsolása][1]

3. A **Diagnosztikai beállítások** lap megadja a diagnosztikai naplók beállításait. Ebben a példában a Log Analytics tárolja a naplókat. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![A konfigurációs folyamat elindítása][2]

5. Adja meg a beállítások nevét, erősítse meg a beállításokat, majd kattintson a **Mentés**gombra.

### <a name="activity-log"></a>Tevékenységnapló

Az Azure alapértelmezés szerint a tevékenység naplóját hozza létre. A naplók 90 napig őrződnek meg az Azure Eseménynapló-tárolójában. A naplókról további információt az [események megtekintése és a tevékenység naplója](../monitoring-and-diagnostics/insights-debugging-with-events.md) című cikkben olvashat.

### <a name="access-log"></a>Hozzáférési napló

A hozzáférési napló csak akkor jön létre, ha minden Application Gateway példányon engedélyezte az előző lépésekben részletezett módon. Az adatai a naplózás engedélyezésekor megadott Storage-fiókban tárolódnak. A Application Gateway minden hozzáférése JSON formátumban van naplózva, ahogyan az a V1-hez készült következő példában látható:

|Érték  |Leírás  |
|---------|---------|
|instanceId     | Application Gateway a kérelmet kézbesítő példány.        |
|Ügyfélip     | A kérelemből származó IP-cím.        |
|clientPort     | A kérelemből származó port.       |
|httpMethod     | A kérelem által használt HTTP-metódus.       |
|requestUri     | A fogadott kérelem URI-ja.        |
|RequestQuery     | **Kiszolgáló – átirányítva**: a kérést küldő háttér-készlet példánya.</br>**X-AzureApplicationGateway-log-ID**: a kérelemhez használt korrelációs azonosító. Felhasználható a háttér-kiszolgálók forgalmával kapcsolatos problémák elhárítására. </br>**Server-status**: a háttérből Application Gateway fogadott http-válasz kódja.       |
|UserAgent     | Felhasználói ügynök a HTTP-kérelem fejlécében.        |
|httpStatus     | A HTTP-állapotkódot a rendszer visszaküldi az ügyfélnek a Application Gatewayból.       |
|httpVersion     | A kérelem HTTP-verziója.        |
|– Fogadott bájtok     | A fogadott csomagok mérete bájtban megadva.        |
|– Küldött bájtok| Az elküldött csomagok mérete bájtban kifejezve.|
|Eltelt idő| A kérelem feldolgozására és a hozzá tartozó válasz elküldésekor szükséges időtartam (ezredmásodpercben). Ez a számítás azt az időintervallumot számítja ki, amikor a Application Gateway egy HTTP-kérelem első bájtját kapja meg a válasz küldési műveletének befejezési idejére. Fontos megjegyezni, hogy az idő mező általában magában foglalja azt az időpontot, ameddig a kérelem és a válasz csomagjai a hálózaton keresztül utaznak. |
|sslEnabled| Azt jelzi, hogy a háttér-készletekkel való kommunikáció SSL protokollt használ-e. Az érvényes értékek be-és kikapcsolva.|
|gazdagép| Az az állomásnév, amellyel a rendszer elküldte a kérést a háttér-kiszolgálónak. Ha a háttérbeli állomásnév felülbírálva van, akkor ez a név fog megjelenni.|
|originalHost| Az az állomásnév, amellyel a kérést a Application Gateway fogadta az ügyféltől.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Application Gateway és WAF v2 esetén a naplók valamivel több információt mutatnak be:

|Érték  |Leírás  |
|---------|---------|
|instanceId     | Application Gateway a kérelmet kézbesítő példány.        |
|Ügyfélip     | A kérelemből származó IP-cím.        |
|clientPort     | A kérelemből származó port.       |
|httpMethod     | A kérelem által használt HTTP-metódus.       |
|requestUri     | A fogadott kérelem URI-ja.        |
|UserAgent     | Felhasználói ügynök a HTTP-kérelem fejlécében.        |
|httpStatus     | A HTTP-állapotkódot a rendszer visszaküldi az ügyfélnek a Application Gatewayból.       |
|httpVersion     | A kérelem HTTP-verziója.        |
|– Fogadott bájtok     | A fogadott csomagok mérete bájtban megadva.        |
|– Küldött bájtok| Az elküldött csomagok mérete bájtban kifejezve.|
|Eltelt idő| Az az időtartam ( **másodpercben**), ameddig a rendszer feldolgozza a kérést, és elküldi a válaszát. Ez a számítás azt az időintervallumot számítja ki, amikor a Application Gateway egy HTTP-kérelem első bájtját kapja meg a válasz küldési műveletének befejezési idejére. Fontos megjegyezni, hogy az idő mező általában magában foglalja azt az időpontot, ameddig a kérelem és a válasz csomagjai a hálózaton keresztül utaznak. |
|sslEnabled| Azt jelzi, hogy a háttér-készletekkel való kommunikáció SSL protokollt használ-e. Az érvényes értékek be-és kikapcsolva.|
|sslCipher| Az SSL-kommunikációhoz használt titkosító csomag (ha az SSL engedélyezve van).|
|sslProtocol| Használt SSL/TLS protokoll (ha az SSL engedélyezve van).|
|serverRouted| Az a háttér-kiszolgáló, amelyhez az Application Gateway átirányítja a kérést.|
|serverStatus| A háttér-kiszolgáló HTTP-állapotkódot.|
|serverResponseLatency| A háttér-kiszolgáló válaszának késése.|
|gazdagép| A kérelemben szereplő állomásfejléc.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Teljesítménynapló

A rendszer csak akkor hozza létre a teljesítményadatokat, ha minden Application Gateway példányon engedélyezte azt az előző lépésekben részletezett módon. Az adatai a naplózás engedélyezésekor megadott Storage-fiókban tárolódnak. A Teljesítménynapló adatai 1 percenként jönnek létre. Csak a v1 SKU esetében érhető el. A v2 SKU esetében használja a teljesítményadatok [mérőszámait](application-gateway-metrics.md) . A rendszer naplózza a következő adatnaplókat:


|Érték  |Leírás  |
|---------|---------|
|instanceId     |  Application Gateway példány, amelynél a teljesítményadatokat generálja a rendszer. A többpéldányos Application Gateway esetében a példányok száma egy sor.        |
|healthyHostCount     | A háttér-készletben található kifogástalan állapotú gazdagépek száma.        |
|unHealthyHostCount     | A nem kifogástalan állapotú gazdagépek száma a háttérbeli készletben.        |
|requestCount     | A kézbesített kérelmek száma.        |
|Késleltetés | A példány és a háttérben futó kérelmek átlagos késése (ezredmásodpercben), amely a kérelmeket szolgálja ki. |
|failedRequestCount| Sikertelen kérelmek száma.|
|Átviteli sebesség| Átlagos átviteli sebesség az utolsó napló óta, bájt/másodpercben mérve.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> A késés kiszámítása a http-válasz utolsó bájtjának elküldésekor bekövetkező idő után történik. Ez a Application Gateway feldolgozási idő összege, valamint a háttérbe kerülő hálózati díj, valamint a háttérben a kérelem feldolgozásához szükséges idő.

### <a name="firewall-log"></a>Tűzfal naplója

A rendszer csak akkor hozza létre a tűzfal-naplót, ha engedélyezte az összes Application Gateway számára az előző lépésekben részletezett módon. Ehhez a naplóhoz az is szükséges, hogy a webalkalmazási tűzfal konfigurálva legyen egy Application gatewayen. Az adatai a naplózás engedélyezésekor megadott Storage-fiókban tárolódnak. A rendszer naplózza a következő adatnaplókat:


|Érték  |Leírás  |
|---------|---------|
|instanceId     | Application Gateway a példány, amelyről a rendszer a tűzfalat hozza létre. A többpéldányos Application Gateway esetében a példányok száma egy sor.         |
|Ügyfélip     |   A kérelemből származó IP-cím.      |
|clientPort     |  A kérelemből származó port.       |
|requestUri     | A fogadott kérelem URL-címe.       |
|Szerepkörkészlet     | Szabálykészlet típusa A rendelkezésre álló érték a OWASP.        |
|ruleSetVersion     | A szabálykészlet verziója használatban van. Az elérhető értékek a következők: 2.2.9 és 3,0.     |
|ruleId     | Az eseményindító eseményének szabály-azonosítója.        |
|message     | Felhasználóbarát üzenet az eseményindító eseményhez. További részletek a Részletek szakaszban olvashatók.        |
|action     |  A kérésen végrehajtott művelet. Az elérhető értékek le vannak tiltva és engedélyezettek.      |
|hely     | A hely, amelyhez a napló létrejött. Jelenleg csak a globális érték van felsorolva, mivel a szabályok globálisak.|
|Részletek     | Az eseményindító esemény részletei.        |
|részletek. üzenet     | A szabály leírása.        |
|részletek. adat     | A szabálynak megfelelő kérelemben található konkrét adatértékek.         |
|részletek. fájl     | A szabályt tartalmazó konfigurációs fájl.        |
|részletek. sor     | Az eseményt kiváltó konfigurációs fájlban lévő sorszám.       |
|Hostname   | A Application Gateway állomásneve vagy IP-címe.    |
|Tranzakcióazonosító  | Egy adott tranzakció egyedi azonosítója, amely lehetővé teszi több szabály megsértésének csoportosítását ugyanazon kérelemen belül.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100", 
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnaplók adatainak megtekintéséhez és elemzéséhez használja az alábbi módszerek bármelyikét:

* **Azure-eszközök**: Információkat kérhet le a tevékenységnaplóból az Azure PowerShell-lel, az Azure CLI-vel, az Azure REST API-val vagy az Azure Portallal. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../azure-resource-manager/resource-group-audit.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik [Power BI](https://powerbi.microsoft.com/pricing)-fiókkal, ingyenesen kipróbálhatja. A [Power bi template apps](https://docs.microsoft.com/power-bi/service-template-apps-overview)segítségével elemezheti az adatait.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>A hozzáférési, a teljesítmény-és a tűzfal-naplók megtekintése és elemzése

[Azure monitor naplók](../azure-monitor/insights/azure-networking-analytics.md) a blob Storage-fiókból gyűjthetik be a számláló és az Eseménynapló fájljait. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Hozzáférési naplók elemzése a GoAccess-on keresztül

Közzétettünk egy Resource Manager-sablont, amely a népszerű [GoAccess](https://goaccess.io/) log Analyzert telepíti és futtatja Application Gateway hozzáférési naplókhoz. A GoAccess olyan értékes HTTP-forgalmi statisztikát biztosít, mint például az egyedi látogatók, a kért fájlok, gazdagépek, operációs rendszerek, böngészők, HTTP-állapotkódok és egyebek. További részletekért tekintse meg az [információs fájlt a GitHub Resource Manager-sablon mappájából](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>További lépések

* Számlálók és Eseménynaplók megjelenítése [Azure monitor naplók](../azure-monitor/insights/azure-networking-analytics.md)használatával.
* [Jelenítse meg az Azure-beli tevékenység naplóját Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzésben.
* [Megtekintheti és elemezheti az Azure-beli tevékenységek naplóit Power bi és további](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzésekben.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
