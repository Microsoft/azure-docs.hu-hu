---
title: Az Azure Signaler szolgáltatás hibaelhárítási gyakorlata
description: Ismerje meg, hogy miként lehet elhárítani a kapcsolódási és üzenetek kézbesítési problémáit
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97707656"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>A kapcsolat és az üzenetek kézbesítésével kapcsolatos problémák elhárítása

Ez az útmutató számos módszert mutat be az öndiagnosztizálásra, hogy a kiváltó okot közvetlenül vagy Szűkítse le a probléma megoldásához. Az öndiagnózisi eredmény akkor is hasznos, ha további vizsgálat céljából jelentést küld nekünk.

Először ellenőriznie kell az Azure Portal, hogy a [ServiceMode](./concept-service-mode.md) melyik Azure signaler szolgáltatás (más néven **ASRS**).

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* A `Default` mód esetében tekintse meg az [alapértelmezett mód hibaelhárítása című témakört](#default_mode_tsg) .

* A `Serverless` mód esetében tekintse meg a [kiszolgáló nélküli mód hibaelhárítását ismertető témakört](#serverless_mode_tsg) .

* A `Classic` mód esetében tekintse meg a [klasszikus mód hibaelhárítását ismertető témakört](#classic_mode_tsg) .

<a name="default_mode_tsg"></a>

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>Alapértelmezett mód hibaelhárítása

Ha a **ASRS** *alapértelmezett* módban van, **három** szerepkör létezik: *ügyfél*, *kiszolgáló* és *szolgáltatás*:

* *Ügyfél*: az *ügyfél* a **ASRS**-hez csatlakozó ügyfelek számára áll. Ebben az útmutatóban az ügyfél-és **ASRS** összekötő állandó kapcsolatok neve *ügyfélkapcsolatok* .

* *Kiszolgáló*: *a kiszolgáló az* ügyfél egyeztetését és a jelző logikáját üzemeltető kiszolgáló számára áll `Hub` . És a *kiszolgáló* és a **ASRS** közötti állandó kapcsolatok neve *kiszolgálói kapcsolatnak* nevezzük ebben az útmutatóban.

* *Szolgáltatás*: a *szolgáltatás* a **ASRS** rövid neve az útmutatóban.

A teljes architektúra és munkafolyamat részletes bevezetéséhez tekintse meg az [Azure signaler szolgáltatás belső részeit](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

A probléma több módon is leszűkíthető. 

* Ha a probléma a megfelelő módon történik, vagy Reprodukálási van, az egyszerű továbbítási mód a folyamatban lévő forgalom megtekintése. 

* Ha a probléma nehezen Reprodukálási, a Nyomkövetések és a naplók segíthetnek.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>A forgalom megtekintése és a probléma szűkítése

A folyamatban lévő forgalom rögzítése a legközvetlenebb megoldás a probléma szűkítéséhez. A [Hálózati nyomkövetést](/aspnet/core/signalr/diagnostics#network-traces) az alább ismertetett beállítások használatával rögzítheti:

* [Hálózati nyomkövetés összegyűjtése a Hegedűsrel](/aspnet/core/signalr/diagnostics#network-traces)

* [Hálózati nyomkövetés összegyűjtése a tcpdump](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Hálózati nyomkövetés összegyűjtése a böngészőben](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Ügyfelek kérései

A szignáló állandó kapcsolata esetén először az `/negotiate` üzemeltetett app Serverre, majd átirányítjuk az Azure signaler szolgáltatásra, majd az Azure signaler szolgáltatáshoz való valódi állandó kapcsolatot létesít. A részletes lépésekért tekintse meg az [Azure signaler szolgáltatás belső részeit](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Az ügyféloldali hálózati nyomkövetéssel ellenőrizze, hogy melyik kérelem meghiúsul, milyen állapotkódot és milyen válaszokat keres, és keressen megoldásokat a [hibaelhárítási útmutatóban](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Kiszolgálói kérelmek

A jelző *kiszolgáló* a *kiszolgáló és a* *szolgáltatás* közötti *kiszolgálói kapcsolatot* tartja fenn. Az App Server indításakor elindul a **WebSocket** -kapcsolat az Azure signaler szolgáltatással. A rendszer az összes ügyfél-forgalmat az Azure Signaler szolgáltatáson keresztül irányítja át a *kiszolgálói kapcsolódási* pontokra, majd elküldi őket a következőnek: `Hub` . Ha a *Kiszolgálói kapcsolatok* elvesznek, a rendszer hatással lesz az erre a *kiszolgálói kapcsolódásra* átirányított ügyfelekre. Az Azure Signaler SDK-ban logikai "mindig újrapróbálkozás" van, hogy a *kiszolgáló-kapcsolat* legfeljebb 1 perces késleltetéssel kapcsolódjon újra a hatás csökkentése érdekében.

A *Kiszolgálói kapcsolatok* a hálózat instabilitása vagy az Azure signaler szolgáltatás rendszeres karbantartása, illetve az üzemeltetett app Server-frissítések/karbantartás miatt csökkenhetnek. Ha az ügyféloldali kapcsolat bontási/újracsatlakozási mechanizmussal rendelkezik, a hatás minimális, mint bármely ügyféloldali kapcsolat bontása – újracsatlakozás.

Tekintse meg a kiszolgálóoldali hálózati nyomkövetést az állapotkód megállapításához, és a hiba részletes leírását, a *kiszolgáló kapcsolatának* a *szolgáltatás* által elvetett vagy visszautasított állapotát, valamint a [hibaelhárítási útmutatóban](./signalr-howto-troubleshoot-guide.md)megjelenő kiváltó okot.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Naplók hozzáadása

A naplók hasznosak lehetnek a problémák diagnosztizálásához és a futó állapot figyeléséhez.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Az ügyféloldali napló engedélyezése

Az ügyféloldali naplózási élmény pontosan ugyanaz, mint a saját üzemeltetésű jelző használata esetén.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Ügyféloldali naplózás engedélyezése a következőhöz: `ASP.NET Core SignalR`

* [JavaScript-ügyfél naplózása](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET-ügyfél naplózása](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Ügyféloldali naplózás engedélyezése a következőhöz: `ASP.NET SignalR`

* [.NET-ügyfél](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Nyomkövetés engedélyezése Windows Phone-telefon 8 ügyfélen](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Nyomkövetés engedélyezése a JavaScript-ügyfélben](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Kiszolgálóoldali napló engedélyezése

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Kiszolgálóoldali naplózás engedélyezése a következőhöz: `ASP.NET Core SignalR`

A kiszolgálóoldali naplózási funkció `ASP.NET Core SignalR` integrálható a `ILogger` keretrendszerben megadott alapú [naplózással](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) `ASP.NET Core` . A kiszolgálóoldali naplózást a következő módon engedélyezheti `ConfigureLogging` a használatával:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Az Azure szignáló naplózó kategóriái mindig a következővel kezdődnek: `Microsoft.Azure.SignalR` . Az Azure-jelző részletes naplófájljainak engedélyezéséhez konfigurálja az előző előtagokat a `Information` **appsettings.js** az alábbihoz hasonló fájlban:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Ellenőrizze, hogy vannak-e rendellenes figyelmeztető/hibanapló rögzített hibák. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Kiszolgálóoldali nyomkövetés engedélyezése a következőhöz: `ASP.NET SignalR`

A (z) >= SDK-verzió használatakor `1.0.0` a következők hozzáadásával engedélyezheti a nyomkövetést `web.config` : ([részletek](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

Ellenőrizze, hogy vannak-e rendellenes figyelmeztető/hibanapló rögzített hibák. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Naplók engedélyezése az Azure Signaler szolgáltatáson belül

A [diagnosztikai naplókat](./signalr-howto-diagnostic-logs.md) az Azure signaler szolgáltatáshoz is engedélyezheti, ezek a naplók részletes információkat biztosítanak az Azure signaler szolgáltatáshoz csatlakozó összes kapcsolatról.

<a name="serverless_mode_tsg"></a>

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Kiszolgáló nélküli mód – hibaelhárítás

Ha a **ASRS** *kiszolgáló* nélküli módban van, csak **ASP.net Core-jelző** támogatja a `Serverless` módot, és a **ASP.net-jelző** **nem** támogatja ezt a módot.

A kapcsolati problémák megoldásához `Serverless` a legközvetlenebb továbbítási mód az [ügyféloldali forgalom megtekintésére](#view_traffic_client)szolgál. Az [ügyféloldali naplók](#add_logs_client) és a [szolgáltatási oldali naplók](#add_logs_server) engedélyezése is hasznos lehet.

<a name="classic_mode_tsg"></a>

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Klasszikus mód hibaelhárítása

`Classic` a mód elavult, és nem javasolt használni. Ebben a módban az Azure Signaler szolgáltatás a csatlakoztatott *kiszolgáló kapcsolatai* alapján határozza meg, hogy az aktuális szolgáltatás `default` mód vagy mód-e `serverless` . Ez bizonyos köztes ügyfélkapcsolati problémákhoz vezethet, mert ha az összes csatlakoztatott *kiszolgáló kapcsolata* hirtelen csökken, például a hálózati instabilitás miatt, az Azure-jelző úgy véli, hogy most már `serverless` üzemmódra vált, és az ebben az időszakban csatlakoztatott ügyfelek soha nem lesznek átirányítva az üzemeltetett alkalmazás-kiszolgálóra. Engedélyezze a [szolgáltatás-oldalsó naplókat](#add_logs_server) , és ellenőrizze, hogy vannak-e olyan ügyfelek, amelyek úgy vannak rögzítve, mintha futtatta `ServerlessModeEntered` az App Servert, azonban néhány ügyfél soha nem éri el az alkalmazáskiszolgáló oldalát Ha van ilyen, [szakítsa meg ezeket az ügyfélkapcsolatokat](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) , és hagyja, hogy az ügyfelek újraindítása segítséget nyújtson.

A hibaelhárítási `classic` mód kapcsolata és az üzenetek kézbesítési problémái hasonlóak az [alapértelmezett módú problémák elhárításához](#default_mode_tsg).

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Szolgáltatás állapota

A szolgáltatás állapotának állapot API-ját is megtekintheti.

* Kérelem: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Válasz állapotkód:
  * 200: kifogástalan.
  * 503: a szolgáltatás állapota nem kifogástalan. A következőket teheti:
    * Várjon néhány percet az automatikus helyreállításhoz.
    * Győződjön meg arról, hogy az IP-cím megegyezik az IP-címmel a portálon.
    * Vagy indítsa újra a példányt.
    * Ha az összes fenti lehetőség nem működik, vegye fel velünk a kapcsolatot új támogatási kérelem hozzáadásával Azure Portal.

További információ a vész- [helyreállítási](./signalr-concept-disaster-recovery.md)szolgáltatásról.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan lehet elhárítani a kapcsolódási és üzenetek kézbesítési problémáit. Azt is megtudhatja, hogyan kezelheti a gyakori problémákat. 

> [!div class="nextstepaction"]
> [Hibaelhárítási útmutató](./signalr-howto-troubleshoot-guide.md)