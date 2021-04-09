---
title: Az Azure Signaler szolgáltatás erőforrás-naplói
description: Ismerje meg, hogyan állíthatja be az Azure Signaler szolgáltatáshoz tartozó erőforrás-naplókat, és hogyan használhatja azt az önálló hibakereséshez.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572248"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Az Azure Signaler szolgáltatás erőforrás-naplói

Ez az oktatóanyag azt ismerteti, hogy milyen erőforrás-naplók érhetők el az Azure Signaler szolgáltatáshoz, hogyan állíthatók be, és hogyan lehet azokat elhárítani. 

## <a name="prerequisites"></a>Előfeltételek
Az erőforrás-naplók engedélyezéséhez a naplófájlok tárolásához valahol szüksége lesz. Ez az oktatóanyag az Azure Storage és a Log Analytics használatát ismerteti.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) – erőforrás-naplókat tart fenn a házirendek naplózása, a statikus elemzés vagy a biztonsági mentés számára.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) – egy rugalmas napló keresési és elemzési eszköze, amely lehetővé teszi az Azure-erőforrások által generált nyers naplók elemzését.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Erőforrás-naplók beállítása Azure Signaler szolgáltatáshoz

Az Azure Signaler szolgáltatáshoz tartozó erőforrás-naplók megtekinthetők. Ezek a naplók szélesebb körű áttekintést nyújtanak az Azure Signaler szolgáltatás példányával létesített kapcsolatról. Az erőforrás-naplók részletes információkat biztosítanak minden egyes kapcsolatban. Ilyenek például az alapinformációk (felhasználói azonosító, a kapcsolat azonosítója és a szállítási típus stb.) és az eseményekre vonatkozó információk (a csatlakozás, az esemény leválasztása és megszakítása stb.). az erőforrás-naplók a probléma azonosításához, a kapcsolatok nyomon követéséhez és az elemzéshez használhatók.

### <a name="enable-resource-logs"></a>Erőforrásnaplók engedélyezése

Az erőforrás-naplók alapértelmezés szerint le vannak tiltva. Az erőforrás-naplók engedélyezéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)a **figyelés** területen kattintson a **diagnosztikai beállítások** elemre.

    ![Ablaktáblában navigáljon a diagnosztikai beállításokhoz](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Ezután kattintson a **diagnosztikai beállítás hozzáadása** elemre.

    ![Erőforrás-naplók hozzáadása](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Állítsa be a kívánt archiválási célt. Jelenleg támogatjuk az **archiválást egy Storage-fiókba** , és **elküldeni a log Analyticsba**.

1. Válassza ki az archiválni kívánt naplókat.

    ![Diagnosztikai beállítások ablaktábla](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Mentse az új diagnosztikai beállításokat.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célhelyen a **diagnosztikai naplók** panelen.

A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure-erőforrások naplóinak áttekintése](../azure-monitor/essentials/platform-logs-overview.md)című témakört.

### <a name="resource-logs-categories"></a>Erőforrás-naplók kategóriái

Az Azure Signaler szolgáltatás egyetlen kategóriában rögzíti az erőforrás-naplókat:

* **Minden napló**: az Azure Signaler szolgáltatáshoz kapcsolódó kapcsolatok nyomon követése. A naplók a csatlakoztatási/leválasztási, hitelesítési és szabályozási adatokat tartalmazzák. További információt a következő szakaszban talál.

### <a name="archive-to-a-storage-account"></a>Archiválás tárfiókba

A naplókat a rendszer a **diagnosztikai naplók** ablaktáblán konfigurált Storage-fiókban tárolja. A rendszer automatikusan létrehoz egy nevű tárolót `insights-logs-alllogs` az erőforrás-naplók tárolásához. A tárolóban a naplók a fájlban tárolódnak `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . Az elérési út alapvetően a és a kombinációja `resource ID` `Date Time` . A naplófájlokat a rendszer feldarabolja `hour` . Ezért a percek mindig a következő: `m=00` .

Az összes napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzés tartalmaz egy karakterlánc-mezőt, amely a következő szakaszokban ismertetett formátumot használja.

Az Archive log JSON-karakterláncok az alábbi táblázatokban felsorolt elemeket tartalmazzák:

**Formátum**

Név | Leírás
------- | -------
time | Napló eseményének időpontja
szint | Naplózási esemény szintje
resourceId | Az Azure Signaler szolgáltatás erőforrás-azonosítója
location | Az Azure Signaler szolgáltatás helye
category | A naplózási Esemény kategóriája
operationName | Az esemény műveleti neve
callerIpAddress | A kiszolgáló/ügyfél IP-címe
properties | A naplózási eseményhez kapcsolódó részletes tulajdonságok. További részletekért tekintse meg az alábbi tulajdonságok táblázatot

**Tulajdonságok tábla**

Név | Leírás
------- | -------
típus | A naplózási esemény típusa Jelenleg az Azure Signaler szolgáltatással való kapcsolattal kapcsolatos információkat biztosítunk. Csak `ConnectivityLogs` a típus érhető el
gyűjtemény | A naplózási esemény gyűjteménye. Az engedélyezett értékek a következők: `Connection` , `Authorization` és `Throttling`
connectionId | A kapcsolatok identitása
transportType | A kapcsolatok átvitelének típusa. Az engedélyezett értékek a következők: `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | A hálózat típusa. Az engedélyezett értékek a következők: `Server` \| `Client` . `Server`: a kiszolgáló oldaláról történő kapcsolatfelvétel; `Client`: Kapcsolatfelvétel az ügyfél oldaláról
userId | A felhasználó identitása
message | A naplózási esemény részletes üzenete

A következő kód egy példa egy archivált log JSON-karakterláncra:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Archiválási naplók sémája Log Analytics

Az erőforrás-naplók megtekintéséhez kövesse az alábbi lépéseket:

1. Kattintson a `Logs` cél log Analytics.

    ![Log Analytics menüelem](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Adja meg `SignalRServiceDiagnosticLogs` és válassza ki az időtartományt az erőforrás-naplók lekérdezéséhez. A speciális lekérdezéssel kapcsolatban lásd: a [log Analytics első lépései Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md)

    ![Lekérdezési napló Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Az archiválási napló oszlopai a következő táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | ------- 
TimeGenerated | Napló eseményének időpontja
Gyűjtemény | A naplózási esemény gyűjteménye. Az engedélyezett értékek a következők: `Connection` , `Authorization` és `Throttling`
OperationName | Az esemény műveleti neve
Hely | Az Azure Signaler szolgáltatás helye
Level | Naplózási esemény szintje
CallerIpAddress | A kiszolgáló/ügyfél IP-címe
Üzenet | A naplózási esemény részletes üzenete
UserId (Felhasználóazonosító) | A felhasználó identitása
ConnectionId | A kapcsolatok identitása
ConnectionType | A hálózat típusa. Az engedélyezett értékek a következők: `Server` \| `Client` . `Server`: a kiszolgáló oldaláról történő kapcsolatfelvétel; `Client`: Kapcsolatfelvétel az ügyfél oldaláról
TransportType | A kapcsolatok átvitelének típusa. Az engedélyezett értékek a következők: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Hibaelhárítás erőforrás-naplókkal

Az Azure Signaler szolgáltatással kapcsolatos hibák elhárításához engedélyezheti a kiszolgálói és ügyféloldali naplók számára a hibák rögzítését. Az Azure Signaler szolgáltatás jelenleg elérhetővé teszi az erőforrás-naplókat, továbbá engedélyezheti a naplókat a szolgáltatás oldalain.

A kapcsolódás váratlan növekedése vagy eldobása esetén az erőforrás-naplók előnyeit a hibák megoldására használhatja.

A gyakori problémák gyakran a kapcsolatok váratlan mennyiségének változásait, a kapcsolatok elérési korlátait és az engedélyezési hibát jelentik. A következő részekben talál útmutatást a hibák megoldásához.

#### <a name="unexpected-connection-number-changes"></a>Váratlan kapcsolatok száma megváltozik

##### <a name="unexpected-connection-dropping"></a>Váratlan kapcsolatok eldobása

Ha váratlan kapcsolatokkal találkozik, először engedélyezze a naplózást a szolgáltatásban, a kiszolgálón és az ügyfél oldalain.

Ha a kapcsolat megszakad, az erőforrás-naplók rögzítik ezt a leválasztási eseményt, és megtekintheti a következőt: `ConnectionAborted` `ConnectionEnded` `operationName` .

A és a közötti különbség a `ConnectionAborted` `ConnectionEnded` `ConnectionEnded` várt leválasztás, amelyet az ügyfél vagy a kiszolgáló oldal aktivál. Míg a `ConnectionAborted` általában váratlan kapcsolat-megszakítási esemény, és a rendszer megszakítja az okot a következőben: `message` .

A megszakítás okai a következő táblázatban láthatók:

Ok | Leírás
------- | ------- 
A kapcsolatok száma eléri a korlátot | A kapcsolatok száma eléri a jelenlegi árszint korlátját. A szolgáltatási egység vertikális felskálázása
Az alkalmazáskiszolgáló lezárta a kapcsolatokat | Az App Server elindítja az abortuszt. A várt abortusznak számít
Kapcsolat pingelésének időtúllépése | Általában hálózati probléma okozta. Érdemes megfontolni az App Server internetről való rendelkezésre állásának ellenőrzését.
Szolgáltatás újratöltése, újraösszekapcsolás | Az Azure Signaler szolgáltatás újratöltődik. Az Azure Signaler támogatja az automatikus újrakapcsolódást, megvárhatja az újrakapcsolódást, vagy manuálisan újra csatlakozhat az Azure Signaler szolgáltatáshoz
Belső kiszolgáló átmeneti hibája | Átmeneti hiba történik az Azure Signaler szolgáltatásban, automatikusan helyreállítva kell lennie
Kiszolgáló-kapcsolatok elvetve | A kiszolgálói kapcsolatok ismeretlen hibával csökkennek, először a szolgáltatás/kiszolgáló/ügyféloldali napló önhibáját kell figyelembe venni. Próbálja meg kizárni az alapvető problémákat (pl. hálózati probléma, az App Server oldali probléma stb.). Ha a probléma nem oldódik meg, további segítségért forduljon hozzánk. További információt a [Súgó beszerzése](#get-help) című szakaszban talál. 

##### <a name="unexpected-connection-growing"></a>Váratlan kapcsolatok növekvő száma

A váratlan kapcsolat növekedésével kapcsolatos hibák megoldásához először a további kapcsolatok kiszűrése szükséges. Egyedi tesztelési felhasználói azonosítót is hozzáadhat a tesztelési ügyfélkapcsolathoz. Ezt követően ellenőrizze, hogy az erőforrás-naplókban van-e, ha egynél több ügyfélkapcsolat ugyanazzal a teszt felhasználói AZONOSÍTÓval vagy IP-címmel rendelkezik, akkor valószínű, hogy az ügyféloldali kapcsolat a vártnál több kapcsolatot hoz létre. Keresse meg az ügyféloldali oldalt.

#### <a name="authorization-failure"></a>Engedélyezési hiba

Ha a 401 jogosulatlanul visszaadott ügyfél-kérelmeket kap, ellenőrizze az erőforrás-naplókat. Ha találkozik, az azt `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` jelenti, hogy a hozzáférési jogkivonatban szereplő összes célközönség érvénytelen. Próbálja meg a naplóban javasolt érvényes célközönségeket használni.


#### <a name="throttling"></a>Throttling

Ha azt tapasztalja, hogy nem tudja létrehozni a szignáló ügyfélkapcsolatait az Azure Signaler szolgáltatáshoz, ellenőrizze az erőforrás-naplókat. Ha `Connection count reaches limit` az erőforrás-naplóban találkozik, túl sok kapcsolatot hoz létre a signaler szolgáltatással, amely eléri a kapcsolati szám korlátot. Vegye fontolóra a Signaler szolgáltatás méretezését. Ha `Message count reaches limit` az erőforrás-naplóban találkozik, az azt jelenti, hogy az ingyenes szintet használja, és az üzenetek kvótáját használja. Ha további üzeneteket szeretne küldeni, érdemes lehet a Signal Service standard csomagra módosítania a további üzenetek küldését. További információ: az [Azure signaler szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Segítség kérése

Javasoljuk, hogy először is hárítsa el a hibakeresést. A legtöbb problémát az App Server vagy a hálózati problémák okozzák. Kövesse a [hibaelhárítási útmutatót az erőforrás-naplóval](#troubleshooting-with-resource-logs) és az [alapvető hibaelhárítási útmutatóval](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) a kiváltó ok megtalálásához.
Ha a probléma továbbra sem oldható fel, érdemes lehet megnyitnia egy problémát a GitHubon, vagy jegyet létrehozni az Azure Portalon.
Nyújt
1. A probléma bekövetkezésekor 30 percen belül eltelt idő
2. Az Azure Signaler szolgáltatás erőforrás-azonosítója
3. Adja meg a részleteket a lehető legpontosabban: például a appserver nem küld üzeneteket, az ügyfélkapcsolati adatvesztést és így tovább.
4. A kiszolgáló/ügyfél oldaláról gyűjtött naplók és az esetleg hasznos egyéb anyagok
5. Választható Reprodukálási-kód

> [!NOTE]
> Ha a GitHubon megnyitja a problémát, a bizalmas adatok (például az erőforrás-azonosító, a kiszolgáló/ügyfél-naplók) magánjellegűek maradnak, csak a Microsoft szervezet tagjainak küldenek privát adatokat.