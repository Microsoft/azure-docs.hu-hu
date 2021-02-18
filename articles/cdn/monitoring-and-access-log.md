---
title: A Azure CDN figyelése, metrikái és nyers naplói
description: Ez a cikk azt ismerteti, hogyan lehet beállítani és használni Azure CDN monitorozást, metrikákat és nyers naplókat.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 8a4b48586c564ee5d14a0768156b0477e2935ccf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575453"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>A Azure CDN valós idejű figyelése, metrikái és hozzáférési naplói
A Microsoft Azure CDNával a következő módokon figyelheti az erőforrásokat a hibák elhárítása, nyomon követése és hibakeresése érdekében. 

* A nyers naplók részletes információkat biztosítanak a CDN által fogadott összes kérelemről. A nyers naplók eltérnek a tevékenység naplóitól. A Tevékenységnaplók biztosítják az Azure-erőforrásokon végzett műveletek láthatóságát.
* Metrikák, amelyek négy fő mérőszámot jelenítenek meg a CDN-ben, beleértve a bájtos találatok arányát, a kérelmek számát, a válasz méretét és az összes késést. Emellett különböző dimenziókat biztosít a metrikák lebontásához.
* Riasztás, amely lehetővé teszi, hogy az ügyfél riasztást állítson be a fő mérőszámokhoz
* További mérőszámok, amelyek lehetővé teszik, hogy az ügyfelek az Azure Log Analytics használatával engedélyezzék az értékek további mérőszámait. Lekérdezési mintákat is biztosítunk néhány, az Azure Log Analytics alatti mérőszámhoz.

> [!IMPORTANT]
> A HTTP nyers naplók szolgáltatás a Microsoft Azure CDN számára érhető el.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="configuration---azure-portal"></a>Konfiguráció – Azure Portal

Nyers naplók konfigurálása a Azure CDNhoz a Microsoft profilból: 

1. A Azure Portal menüben válassza a **minden erőforrás** elemet  >>  **\<your-CDN-profile>** .

2. Válassza a **Diagnosztikai beállítások** lehetőséget a **Monitorozás** szakaszban.

3. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Adja hozzá a CDN-profil diagnosztikai beállításait." border="true":::
    
    > [!IMPORTANT]
    > A nyers naplók csak a profil szintjén érhetők el, míg az összesített HTTP-állapotkódok naplói a végpont szintjén érhetők el.

4. A **diagnosztikai beállítások** területen adja meg a diagnosztikai **beállítások neve** alatt a diagnosztikai beállítás nevét.

5. Válassza ki a **AzureCdnAccessLog** , és adja meg a megőrzési napokat.

6. Válassza ki a **célhely részleteit**. A cél beállításai a következők:
    * **Küldés a Log Analyticsnek**
        * Válassza ki az **előfizetést** és a **log Analytics munkaterületet**.
    * **Archiválás egy Storage-fiókba**
        * Válassza ki az **előfizetést** és a **Storage-fiókot**.
    * **Streamelés eseményközpontba**
        * Válassza ki az **előfizetést**, az **Event hub-névteret**, az **Event hub nevét (nem kötelező)** és az **Event hub-szabályzat nevét**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Adja meg a naplózási beállításokat a célhelyen." border="true":::

7. Kattintson a **Mentés** gombra.

## <a name="configuration---azure-powershell"></a>Konfiguráció – Azure PowerShell

A [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) használatával konfigurálja a nyers naplók diagnosztikai beállításait.

Az adatmegőrzési adatok meghatározása a parancsban a **-RetentionInDays** beállítással történik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnosztikai naplók engedélyezése a Storage-fiókokban

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Ha egy Storage-fiókban szeretné engedélyezni a diagnosztikai naplókat, adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnosztikai naplók engedélyezése Log Analytics munkaterülethez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics munkaterülethez tartozó diagnosztikai naplók engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnosztikai naplók engedélyezése az Event hub-névtérhez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Az Event hub-névtér diagnosztikai naplóinak engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Nyers naplók tulajdonságai

A Microsoft-szolgáltatásból Azure CDN jelenleg nyers naplókat biztosít. A nyers naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával: 

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| BackendHostname | Ha a kérést egy háttérbe továbbítják, ez a mező a háttér állomásnevét jelöli. Ez a mező üresen marad, ha a kérés átirányítása vagy továbbítása regionális gyorsítótárba történik (ha a gyorsítótárazás engedélyezve van az útválasztási szabályhoz). |
| CacheStatus | Gyorsítótárazási forgatókönyvek esetén ez a mező a legördülő menüben definiált gyorsítótár-találatok és-kihagyás |
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe azonos. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| HttpMethod | A kérelem által használt HTTP-metódus. |
| HttpStatusCode | A proxy által visszaadott HTTP-állapotkód. |
| HttpStatusDetails | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található. |
| HttpVersion | A kérelem vagy a kapcsolatok típusa. |
| POP | A kérelem által kirakodott szegély rövid neve. |
| RequestBytes | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét. |
| RequestUri | A fogadott kérelem URI-ja. |
| ResponseBytes | A háttér-kiszolgáló által válaszként küldött bájtok.  |
| RoutingRuleName | Annak az útválasztási szabálynak a neve, amelyhez a kérelem illeszkedik. |
| RulesEngineMatchNames | A kérelemnek megfelelő szabályok nevei. |
| SecurityProtocol | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás. |
| SentToOriginShield </br> (elavult) * **tekintse meg a következő szakaszban szereplő, az elavult megjegyzések című szakaszt.**| Ha az értéke igaz, az azt jelenti, hogy a rendszer a lekérést a peremhálózati pop helyett a forrás pajzs gyorsítótárából választta. A Origin Shield egy szülő gyorsítótár, amely a gyorsítótár találati arányának növelésére szolgál. |
| isReceivedFromClient | Ha az értéke igaz, az azt jelenti, hogy a kérelem az ügyféltől érkezett. Ha false (hamis) értékű, a kérelem a szélén (gyermek POP) található, és a forrás pajzstól (szülő POP) válaszol. |
| Eltelt idő | A kérelem első bájtjában eltelt idő (másodpercben) a válasz utolsó bájtjában. |
| TrackingReference | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| UserAgent | Az ügyfél által használt böngésző típusa. |
| ErrorInfo | Ez a mező a hibaelhárítási terület leszűkített hibájának adott típusát tartalmazza. </br> A lehetséges értékek a következők: </br> Nincs **hiba**: azt jelzi, hogy nem található hiba. </br> **CertificateError**: általános SSL-tanúsítvány hiba.</br> **CertificateNameCheckFailed**: az SSL-tanúsítványban található állomásnév érvénytelen vagy nem egyezik. </br> **ClientDisconnected**: az ügyfél hálózati kapcsolatai miatt sikertelen volt a kérelem. </br> **UnspecifiedClientError**: általános ügyfél-hiba. </br> **InvalidRequest**: érvénytelen kérelem. Előfordulhat, hogy helytelen formátumú fejléc, törzs és URL-cím miatt fordul elő. </br> **DNSFailure**: DNS-hiba. </br> **DNSNameNotResolved**: a kiszolgáló neve vagy címe nem oldható fel. </br> **OriginConnectionAborted**: a forrással létesített kapcsolatok váratlanul leálltak. </br> **OriginConnectionError**: általános forrással kapcsolatos hiba történt. </br> **OriginConnectionRefused**: a forrással létesített kapcsolódás nem sikerült. </br> **OriginError**: általános eredetű hiba. </br> **OriginInvalidResponse**: a forrás érvénytelen vagy ismeretlen választ adott vissza. </br> **OriginTimeout**: a forrás kérelemre vonatkozó időtúllépési időszak lejárt. </br> **ResponseHeaderTooBig**: a forrás túl nagy értéket adott vissza a válasz fejlécében. </br> **RestrictedIP**: a kérést a rendszer a korlátozott IP-cím miatt blokkolta. </br> **SSLHandshakeError**: nem sikerült kapcsolatot létesíteni a forrással az SSL-Shake-hiba miatt. </br> **UnspecifiedError**: hiba történt, amely nem felelt meg a tábla egyik hibájának sem. |
| TimeToFirstByte | Az az időtartam ezredmásodpercben, ameddig a Microsoft CDN megkapja a kérést az ügyfélnek az első bájt elküldésekor. Az idő mérése csak a Microsoft oldalán történik. Nincs mért ügyféloldali adat. |
> [!NOTE]
> A naplók a Log Analytics profil alatt tekinthetők meg egy lekérdezés futtatásával. A minta lekérdezés A következőképpen fog kinézni:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Elküldve a Origin Shield elavulttá
A RAW log tulajdonság **isSentToOriginShield** elavult, és egy új mező **isReceivedFromClient** vált. Ha már használja az elavult mezőt, használja az új mezőt. 

A nyers naplók közé tartoznak a CDN Edge (Child POP) és a Origin Shield által létrehozott naplók. A Origin Shield olyan szülő csomópontokra utal, amelyek stratégiailag a világ minden részén találhatók. Ezek a csomópontok a forrás-kiszolgálókkal kommunikálnak, és csökkentik a forgalom terhelését a forrásnál. 

A forrásként szolgáló védelemre kerülő minden kérelem esetében 2 naplós bejegyzés található:

* Egy az Edge-csomópontok számára
* Az egyik a Origin Shield. 

Az Edge-csomópontok és a forrás pajzs alapján a kilépések és válaszok megkülönböztetése érdekében a megfelelő adatok beolvasásához használja a **isReceivedFromClient** mezőt. 

Ha az érték hamis, akkor az azt jelenti, hogy a kérést a rendszer a forrás pajzsról az Edge-csomópontokra válaszol. Ez a megközelítés hatékonyan hasonlítható össze a számlázási adattal rendelkező nyers naplók összehasonlításával. Nem számítunk fel díjat a forrásként szolgáló pajzs és a peremhálózati csomópont közötti kimenő forgalomért. A peremhálózati csomópontok és az ügyfelek közötti kimenő forgalomért számítunk fel díjat. 

**Kusto lekérdezési minta a forrásként szolgáló Log Analyticsban generált naplók kizárásához.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> A HTTP nyers naplók funkció automatikusan elérhető minden, a **2020. február 25**. után létrehozott vagy frissített profilhoz. A korábban létrehozott CDN-profilok esetében az egyiknek frissítenie kell a CDN-végpontot a naplózás beállítása után. Például megnyithatja a Geo-szűrést a CDN-végpontok területen, és blokkolhatja az országokat és régiókat, amelyek nem relevánsak a számítási feladatokhoz és a mentéshez.


## <a name="metrics"></a>Mérőszámok
A Microsoft Azure CDN integrálva van Azure Monitor és négy CDN-metrikát tesz közzé a problémák nyomon követése, hibaelhárítása és hibakeresése érdekében. 

A metrikák a diagramokban jelennek meg, és a PowerShell, a CLI és az API használatával érhetők el. A CDN-metrikák díjmentesek.

Azure CDN a Microsoft mértékeit, és 60 másodperces időközönként elküldi a metrikákat. A metrikák akár 3 percet is igénybe vehetnek, hogy megjelenjenek a portálon. 

További információ: [Azure monitor mérőszámok](../azure-monitor/essentials/data-platform-metrics.md).

**A Microsoft által Azure CDN által támogatott metrikák**

| Mérőszámok  | Description | Dimenziók |
| ------------- | ------------- | ------------- |
| Találatok aránya * | A CDN-gyorsítótárból kiérkező kimenő forgalom százalékos aránya, a teljes kimenő forgalomra kiszámítva. | Végpont |
| RequestCount | A CDN által kiszolgált ügyfél-kérelmek száma. | Végpont </br> Ügyfél országa. </br> Ügyfél-régió. </br> A HTTP-állapot. </br> HTTP-állapot csoport. |
| ResponseSize | A CDN Edge által az ügyfeleknek küldött válaszként küldött bájtok száma. |Végpont </br> Ügyfél országa. </br> Ügyfél-régió. </br> A HTTP-állapot. </br> HTTP-állapot csoport. |
| TotalLatency | A CDN által az ügyfélnek **küldött, a CDN által az utolsó válasz bájtig** fogadott kérelmek teljes ideje. |Végpont </br> Ügyfél országa. </br> Ügyfél-régió. </br> A HTTP-állapot. </br> HTTP-állapot csoport. |

***Találatok száma (a forrástól a kilépéstől a szélétől kimenő forgalomból)/egress**

A bájtok találati arányának kiszámítása során kizárt forgatókönyvek:

* Explicit módon nem konfigurálhat gyorsítótárat a szabályok motorja vagy a lekérdezési karakterlánc gyorsítótárazási viselkedése alapján.
* A Cache-Control direktívát explicit módon, tároló nélküli vagy privát gyorsítótárral konfigurálja.

### <a name="metrics-configuration"></a>Metrikák konfigurálása

1. A Azure Portal menüben válassza a **minden erőforrás** elemet  >>  **\<your-CDN-profile>** .

2. A **figyelés** területen válassza a **metrikák** elemet:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="A CDN-profil metrikái." border="true":::

3. Válassza a **metrika hozzáadása** lehetőséget, majd válassza ki a hozzáadni kívánt metrikát:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Adja hozzá a metrikát a CDN-profilhoz, és válassza ki." border="true":::

4. Szűrő hozzáadásához válassza a **szűrő hozzáadása** elemet:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Szűrő alkalmazása a metrikára" border="true":::

5. Válassza a felosztás **alkalmazása** lehetőséget, hogy a trendeket különböző dimenziók szerint lássuk:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="A metrikára való felosztás alkalmazása." border="true":::

6. Új diagram hozzáadásához válassza az **új diagram** lehetőséget:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Új diagram hozzáadása a metrika nézethez." border="true":::

### <a name="alerts"></a>Riasztások

A riasztásokat a Microsoft CDN-ben beállíthatja a **figyelési**  >>  **riasztások** lehetőség kiválasztásával.

Válassza az **új riasztási szabály** a metrikák szakaszban felsorolt mérőszámok esetén:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="A CDN-végpontra vonatkozó riasztások konfigurálása." border="true":::

A riasztások Azure Monitor alapján lesznek felszámítva. További információ a riasztásokról: [Azure monitor riasztások](../azure-monitor/alerts/alerts-overview.md).

### <a name="additional-metrics"></a>További mérőszámok
A további mérőszámokat az Azure Log Analytics és a nyers naplók használatával is engedélyezheti további díjakért.

1. A diagnosztika engedélyezéséhez kövesse az alábbi lépéseket a nyers napló küldéséhez a log Analytics szolgáltatásban.

2. Válassza ki a létrehozott Log Analytics munkaterületet:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Log Analytics-munkaterület kiválasztása" border="true":::   

3. A log Analytics-munkaterület **általános** területén válassza a **naplók** lehetőséget.  Ezután válassza az első **lépések** lehetőséget:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log Analytics-erőforrás munkaterület." border="true":::   
 
4. Válassza a **CDN-profilok** lehetőséget.  Válasszon egy példát a lekérdezés FUTTATÁSához vagy a példa képernyő bezárásához, és adja meg az egyéni lekérdezést:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Példa a lekérdezési képernyőre." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Lekérdezés végrehajtása." border="true":::   

4. Ha diagramon szeretné megtekinteni az adatmegjelenítést, válassza a **diagram** lehetőséget.  Válassza a **rögzítés az irányítópulton** lehetőséget a diagram Azure-irányítópultra való rögzítéséhez:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Diagram rögzítése az irányítópulton." border="true"::: 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben engedélyezte a HTTP nyers naplókat a Microsoft CDN szolgáltatáshoz.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Elemzés](cdn-log-analysis.md) Azure CDN használati minták.

* További információ a [Azure monitorról](../azure-monitor/overview.md).

* [Log Analytics konfigurálása Azure monitorban](../azure-monitor/logs/log-analytics-tutorial.md).