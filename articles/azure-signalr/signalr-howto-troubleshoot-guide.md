---
title: Hibaelhárítási útmutató az Azure SignalR Service-hez
description: Tudnivalók a gyakori problémák elhárításáról
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: e26def56fbd03626c3efc660db57012ee1b767ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048204"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Hibaelhárítási útmutató az Azure Signaler szolgáltatás gyakori problémáira

Ez az útmutató hasznos hibaelhárítási útmutatót nyújt az ügyfelek által az elmúlt években teljesített és feloldott gyakori problémák alapján.

## <a name="access-token-too-long"></a>A hozzáférési token túl hosszú

### <a name="possible-errors"></a>Lehetséges hibák

* Ügyféloldali `ERR_CONNECTION_`
* 414 URI túl hosszú
* 413 túl nagy a hasznos adat
* A hozzáférési jogkivonat nem lehet hosszabb, mint 4K. 413 kérelem entitása túl nagy

### <a name="root-cause"></a>Gyökérok

A HTTP/2 esetében az egyetlen fejléc maximális hossza **4 K**, így ha a böngészővel fér hozzá az Azure-szolgáltatáshoz, a korlátozás hibaüzenetet kap `ERR_CONNECTION_` .

A HTTP/1.1 vagy C# ügyfelek esetében a maximális URI-érték **12 k**, a fejléc maximális hossza **16 k**.

Az SDK **1.0.6** vagy újabb verziója `/negotiate` akkor fog kiindulni, `413 Payload Too Large` Ha a generált hozzáférési jogkivonat nagyobb, mint **4 K**.

### <a name="solution"></a>Megoldás

Alapértelmezés szerint a rendszer a jogcímeket a `context.User.Claims` JWT hozzáférési token **ASRS**(Zure **S** ignal **R** zésének) való létrehozásakor tartalmazza, így a jogcímek megmaradnak, és a **ASRS** -ből továbbítható, `Hub` Ha az ügyfél csatlakozik a szolgáltatáshoz `Hub` .

Bizonyos esetekben az `context.User.Claims` app Server számos információjának tárolására szolgál, amelyek többségét nem használja az `Hub` s, hanem más összetevők.

A generált hozzáférési jogkivonat átadása a hálózaton keresztül történik, a WebSocket/SSE kapcsolatok esetében pedig a hozzáférési tokeneket a lekérdezési karakterláncok továbbítják. Az ajánlott eljárás az, hogy csak a **szükséges** jogcímeket kell átadnia az ügyféltől a **ASRS** keresztül az alkalmazáskiszolgáló számára, amikor a hub-nak szüksége van rá.

A `ClaimsProvider` hozzáférési jogkivonatban a **ASRS** áthaladó jogcímeket testreszabhatja.

ASP.NET Core esetén:

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

ASP.NET esetén:

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1,2 szükséges

### <a name="possible-errors"></a>Lehetséges hibák

* ASP.NET "nincs elérhető kiszolgáló" hiba [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "a kapcsolódás nem aktív, az adatszolgáltatás nem küldhető el." hiba [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Hiba történt a HTTP-kérelem https://való végrehajtása közben <API endpoint> . Ennek a hibának az lehet az oka, hogy a kiszolgálói tanúsítvány nincs megfelelően konfigurálva a HTTPS-eset HTTP.SYS. Ezt a hibát az ügyfél és a kiszolgáló közötti biztonsági kötés eltérése is okozhatja. "

### <a name="root-cause"></a>Gyökérok

Az Azure-szolgáltatás csak a TLS 1.2-es verzióját támogatja biztonsági okokból. A .NET-keretrendszerrel lehetséges, hogy a TLS 1.2 nem az alapértelmezett protokoll. Ennek eredményeképpen a ASRS-kiszolgáló kapcsolatainak létrehozása nem sikerült.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

1. Ha a hiba helyileg reprodukálható, törölje *saját kód* , és dobja el az összes CLR-kivételt, és az alkalmazáskiszolgáló helyi hibakeresésével tekintse meg a kivételeket.
    * *Saját kód* törlése

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Saját kód törlése":::

    * CLR-kivételek eldobása

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR-kivételek eldobása":::

    * Tekintse meg a kivételeket az alkalmazás kiszolgálóoldali kódjának hibakeresése során:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Kivételek":::

2. A ASP.NET a következő kódot is hozzáadhatja a `Startup.cs` -hoz a részletes nyomkövetés engedélyezéséhez és a naplóban található hibák megtekintéséhez.

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>Megoldás

Adja hozzá a következő kódot az indításhoz:

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400 helytelen kérelem érkezett az ügyfelek kéréseihez

### <a name="root-cause"></a>Gyökérok

Ellenőrizze, hogy az ügyfél kérésére több `hub` lekérdezési karakterlánc van-e. `hub` egy megőrzött lekérdezési paraméter, a 400 pedig kidobja, ha a szolgáltatás egynél többt észlel a `hub` lekérdezésben.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>A rendszer 401-es (Jogosulatlan) hibát ad vissza az ügyfélkérésekre

### <a name="root-cause"></a>Gyökérok

Jelenleg az JWT token élettartamának alapértelmezett értéke 1 óra.

A ASP.NET Core jelző esetében, ha a WebSocket-átvitel típusát használja, az OK.

A ASP.NET Core a jelző más típusú átviteli típusát, az SSE-t és a hosszú lekérdezéseket, ez azt jelenti, hogy alapértelmezés szerint a kapcsolatok legfeljebb 1 órára maradhatnak.

A ASP.NET-jelző esetében az ügyfél időről időre elküld egy életben tartási `/ping` kérelmet a szolgáltatásnak, amikor a `/ping` művelet meghiúsul, az ügyfél **megszakítja** a kapcsolatot, és soha nem csatlakozik újra. Ez azt jelenti, hogy a ASP.NET-jelző esetében az alapértelmezett jogkivonat élettartama **legfeljebb** 1 órát tart a teljes átviteli típushoz.

### <a name="solution"></a>Megoldás

Biztonsági okokból az élettartam meghosszabbítása nem ajánlott. Javasoljuk, hogy az ügyfél újracsatlakozási logikájának hozzáadásával indítsa újra a kapcsolatot, ha az 401 történik. Amikor az ügyfél újraindítja a kapcsolódást, a rendszer egyezteti az App Serverrel, hogy újra lekérje az JWT-tokent, és megújított jogkivonatot kapjon.

Az ügyfélkapcsolatok újraindítását [itt](#restart_connection) tekintheti meg.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>A rendszer 404-es hibát ad vissza az ügyfélkérésekre

A Signaler állandó kapcsolat esetén először `/negotiate` Az Azure signaler szolgáltatáshoz kell csatlakoznia, majd létre kell hoznia a valódi kapcsolatot az Azure signaler szolgáltatással.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

* A következő lépésekkel [tekintheti meg a kimenő kérelmeket](#view_request) , hogy lekérje a kérést az ügyféltől a szolgáltatás felé.
* Győződjön meg arról, hogy a kérelem URL-címe 404 történik. Ha az URL-cím a webalkalmazásra irányul, és a következőhöz hasonló `{your_web_app}/hubs/{hubName}` , ellenőrizze, hogy az ügyfél `SkipNegotiation` `true` . Az Azure-jelző használatakor az ügyfél megkapja az átirányítási URL-címet, amikor először egyeztet az alkalmazás-kiszolgálóval. Az ügyfél nem hagyhatja figyelmen **kívül** az egyeztetést az Azure-jelző használatakor.
* Egy másik 404 akkor fordulhat elő, ha a kapcsolódási kérelem a hívása után **5** másodpercnél több időt vesz igénybe `/negotiate` . Ellenőrizze az ügyfél kérelmének időbélyegét, és nyisson meg egy problémát, ha a szolgáltatásra irányuló kérés lassú választ ad.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 visszaadott ASP.NET-jelző újracsatlakoztatási kérelméhez

A ASP.NET-jelző esetében, amikor az [ügyfél kapcsolata csökken](#client_connection_drop), a kapcsolat leállítása előtt ugyanezt a három alkalommal is újracsatlakoztatja `connectionId` . `/reconnect` segíthet, ha a kapcsolat eldobása olyan hálózati időszakos problémák miatt történik, amelyek `/reconnect` az állandó kapcsolat sikeres újraépítését teszik lehetővé. Más körülmények között például az ügyfélkapcsolatot a rendszer az útválasztásos kiszolgáló kapcsolatainak eldobása miatt eldobta, vagy olyan belső hibákat tartalmaz, mint például a példány újraindítása/feladatátvétel/üzembe helyezés, a kapcsolódás már nem létezik, így `/reconnect` visszatér `404` . Ez a várt viselkedés a `/reconnect` három alkalommal újra próbálkozik a kapcsolatok leállásával. A kapcsolódás leállításakor javasoljuk a [kapcsolatok újraindítási](#restart_connection) logikáját.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (túl sok kérés) lett visszaküldve az ügyfél kéréseihez

Két eset létezik.

### <a name="concurrent-connection-count-exceeds-limit"></a>Az **egyidejű** kapcsolatok száma meghaladja a korlátot

Az **ingyenes** példányok esetében az **egyidejű** kapcsolatok száma legfeljebb 20 a **standard** példányok esetében, az **egységenkénti** **kapcsolati** korlát egységenként 1 K, ami azt jelenti, hogy a Unit100 engedélyezi a 100-K egyidejű kapcsolatait.

A kapcsolatok az ügyfél és a kiszolgáló kapcsolatait is tartalmazzák. [itt](./signalr-concept-messages-and-connections.md#how-connections-are-counted) tekintheti meg a kapcsolatok számításának módját.

### <a name="too-many-negotiate-requests-at-the-same-time"></a>Túl sok egyeztetési kérelem van egyidejűleg

Javasoljuk, hogy az újrakapcsolódás előtt véletlenszerű késleltetést kapjon, [és próbálkozzon](#restart_connection) újra a mintákkal.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 hiba az egyeztetés során: az Azure Signaler szolgáltatás még nincs csatlakoztatva, próbálkozzon újra később

### <a name="root-cause"></a>Gyökérok

Ez a hiba akkor jelenik meg, ha az Azure Signaler szolgáltatáshoz nem kapcsolódik kiszolgálói kapcsolat.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Engedélyezze a kiszolgálóoldali nyomkövetést, hogy megtudja a hiba részleteit, amikor a kiszolgáló megpróbál csatlakozni az Azure Signaler szolgáltatáshoz.

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Kiszolgálóoldali naplózás engedélyezése ASP.NET Core jelzőhöz

A ASP.NET Core-jelző kiszolgálóoldali naplózása integrálható a `ILogger` ASP.net Core-keretrendszerben megadott alapú [naplózással](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true) . A kiszolgálóoldali naplózást a következő módon engedélyezheti `ConfigureLogging` a használatával:

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Az Azure szignáló naplózó kategóriái mindig a következővel kezdődnek: `Microsoft.Azure.SignalR` . Az Azure-jelző részletes naplófájljainak engedélyezéséhez konfigurálja az előző előtagokat a `Debug` **appsettings.js** az alábbihoz hasonló fájlban:

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Kiszolgálóoldali nyomkövetés engedélyezése a ASP.NET-jelzőhöz

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

<a name="client_connection_drop"></a>

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>Ügyfélkapcsolat-vesztés

Ha az ügyfél az Azure-jelzőhöz csatlakozik, az ügyfél és az Azure-jelző közötti állandó kapcsolat esetenként különböző okok miatt csökkenhet. Ez a szakasz számos olyan lehetőséget ismertet, amely az ilyen jellegű kapcsolatok eldobását okozza, és útmutatást nyújt a kiváltó ok azonosításához.

### <a name="possible-errors-seen-from-the-client-side"></a>Az ügyfél oldalán észlelt lehetséges hibák

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Gyökérok

Az ügyfélkapcsolatok különféle körülmények között csökkenhetnek:
* Ha `Hub` kivételeket jelez a bejövő kérelemmel kapcsolatban.
* Ha a kiszolgáló kapcsolata, amelyre az ügyfél átirányított, elveszíti az alábbi szakaszt a [kiszolgáló kapcsolatának elejtésével](#server_connection_drop)kapcsolatos részletekért.
* Ha hálózati kapcsolati hiba történik az ügyfél és a jelző szolgáltatás között.
* Ha a jelző szolgáltatás olyan belső hibákkal rendelkezik, mint például a példány újraindítása, feladatátvétel, üzembe helyezés stb.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

1. Nyissa meg az alkalmazás-kiszolgálóoldali naplót, és ellenőrizze, hogy van-e rendellenes
2. Tekintse meg az App kiszolgálóoldali eseménynaplóját, és ellenőrizze, hogy az alkalmazáskiszolgáló újraindult-e
3. Hozzon létre egy problémát, amely megadja az időkeretet, és küldje el nekünk az erőforrás nevét

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>Az Ügyfélkapcsolat folyamatosan növekszik

Ennek oka lehet az Ügyfélkapcsolat helytelen használata. Ha valaki elfelejti a jelző ügyfél leállítását/eldobását, a kapcsolódás nyitva marad.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>A jelző azon mérőszámai által észlelt lehetséges hibák, amelyek a Azure Portal erőforrás menüjének figyelés szakaszában találhatók

Az ügyfélkapcsolatok hosszú ideje folyamatosan növekednek az Azure-jelző Metrikái között.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Az Ügyfélkapcsolat folyamatosan növekszik":::

### <a name="root-cause"></a>Gyökérok

`DisposeAsync`A jelző-ügyfélkapcsolat soha nem hívható, a kapcsolatok nyitva maradnak.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Ellenőrizze, hogy a jelző ügyfele **soha nem** zárul-e le.

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy be van-e zárva a kapcsolatok. Manuálisan hívja `HubConnection.DisposeAsync()` meg a kapcsolatok leállítását a használat után.

Például:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Az ügyfél-kapcsolatok gyakori helytelen használata

#### <a name="azure-function-example"></a>Példa Azure-függvényre 

Ez a probléma gyakran fordul elő, ha valaki a Signal-ügyfélkapcsolatot az Azure Function metódusban hozza létre ahelyett, hogy statikus tagot kellene létesítenie a Function osztályba. Előfordulhat, hogy csak egy ügyfélkapcsolatot kell létrehoznia, de az ügyfél-kapcsolatok száma folyamatosan növekszik a Azure Portal erőforrás menüjének figyelés szakaszában lévő mérőszámok között, és ezek a kapcsolatok csak az Azure-függvény vagy az Azure-jelző szolgáltatás újraindítása után jelennek meg. Ennek az az oka, hogy **minden** kérelem esetében az Azure Function **egy** ügyfélkapcsolatot hoz létre, ha nem állítja le az ügyfélkapcsolatot a Function metódusban, az ügyfél életben tartja a kapcsolatokat az Azure signaler szolgáltatásban.

#### <a name="solution"></a>Megoldás

* Ne felejtse el lezárva az ügyfélkapcsolatot, ha a Signaler-ügyfeleket az Azure-függvényben használja, vagy ha a Signaler-ügyfelet különállóként
* A Signaler-ügyfelek az Azure-függvényben való használata helyett a Signaler-ügyfelek bárhol létrehozhatók, és az [Azure signaler szolgáltatáshoz Azure functions kötések](https://github.com/Azure/azure-functions-signalrservice-extension) használatával [egyeztetik](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) az ügyfelet az Azure-jelzővel. Emellett a kötést is használhatja az [üzenetek küldéséhez](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Az ügyfél egyeztetésére és az üzenetek küldésére szolgáló mintákat [itt](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)találja. További információt [itt](https://github.com/Azure/azure-functions-signalrservice-extension)találhat.
* Ha a Signaler-ügyfeleket az Azure-függvényben használja, a forgatókönyvnek jobb architektúrája lehet. Ellenőrizze, hogy megfelelő kiszolgáló nélküli architektúrát tervez-e. A [Azure functions-ben a signaler szolgáltatás kötéseivel valós idejű kiszolgáló nélküli alkalmazásokat](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)is megtekintheti.

<a name="server_connection_drop"></a>

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>A kiszolgáló kapcsolatainak adatvesztése

Az App Server indításakor a háttérben az Azure SDK elkezdi kezdeményezni a kiszolgálói kapcsolatokat a távoli Azure-jelzővel. Az Azure signaler [szolgáltatás belső részében](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)leírtaknak megfelelően az Azure signaler a bejövő ügyfelek forgalmát a kiszolgálói kapcsolatokhoz irányítja. A kiszolgáló kapcsolatának eldobása után az összes általa üzemeltetett ügyfélkapcsolat is le lesz zárva.

Mivel az App Server és a Signaler szolgáltatás közötti kapcsolatok állandó kapcsolatok, a hálózati kapcsolattal kapcsolatos problémák merülhetnek fel. A Server SDK-ban **mindig újrakapcsolódott** a stratégia a kiszolgálói kapcsolatokhoz. Az ajánlott eljárás az, hogy a felhasználók folyamatos újracsatlakozási logikát adjanak hozzá az ügyfelekhez véletlenszerű késleltetési idővel, hogy el lehessen kerülni a kiszolgálóval való tömeges egyidejű kérelmeket.

Az Azure Signaler szolgáltatás új verziókkal rendelkezik, és esetenként az Azure-alapú operációsrendszer-javítások vagy-frissítések, illetve a függő szolgáltatások időnkénti megszakítása. Ezek a szolgáltatás rövid idő alatt megszakadnak, de mindaddig, amíg az ügyféloldali a leválasztási/újracsatlakozási mechanizmussal rendelkezik, a hatás minimális, például bármely ügyféloldali kapcsolat bontása – újracsatlakozást okoz.

Ez a szakasz számos olyan lehetőséget ismertet, amely a kiszolgáló kapcsolatainak csökkenését eredményezi, és útmutatást nyújt a kiváltó ok azonosításához.

### <a name="possible-errors-seen-from-the-server-side"></a>A kiszolgáló oldaláról származó lehetséges hibák

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Gyökérok

A kiszolgáló-szolgáltatással létesített kapcsolatokat a **ASRS**(A zure **S** ignal **R** **S** zésének) zárja **be**.

A pingelés időtúllépése miatt előfordulhat, hogy a kiszolgáló oldalán a CPU-használat vagy a szál-készlet éhezése okozta.

A ASP.NET-jelző esetében az SDK-1.6.0 rögzített egy ismert probléma. Frissítse az SDK-t a legújabb verzióra.

## <a name="thread-pool-starvation"></a>Szál készletének éhezése

Ha a kiszolgáló éhezik, ez azt jelenti, hogy egyetlen szál sem működik az üzenetek feldolgozásakor. Az összes szál nem válaszol egy adott metódusban.

Ezt a forgatókönyvet általában a szinkronizálás vagy `Task.Result` / `Task.Wait()` aszinkron metódusok okozzák.

Lásd: [ASP.net Core teljesítményre vonatkozó ajánlott eljárások](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls).

További információ a [Thread Pool éhezésről](https://docs.microsoft.com/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall).

### <a name="how-to-detect-thread-pool-starvation"></a>A szál-készlet éhezésének észlelése

Keresse meg a szálak darabszámát. Ha ebben az időszakban nincsenek tüskék, hajtsa végre a következő lépéseket:
* Ha Azure App Service használ, ellenőrizze a szálak darabszámát a mérőszámokban. Keresse meg az `Max` összesítést:
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Képernyőfelvétel a szálak maximális száma ablaktábláról Azure App Service.":::

* Ha a .NET-keretrendszert használja, a Teljesítményfigyelőben megtalálhatja a kiszolgáló virtuális gépe [mérőszámait](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters) .
* Ha a .NET Core-t használja egy tárolóban, olvassa el a [diagnosztika gyűjtése a tárolókban](https://docs.microsoft.com/dotnet/core/diagnostics/diagnostics-in-containers)című témakört.

Emellett kódot is használhat a szál-készlet éhezésének észleléséhez:

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
Adja hozzá a szolgáltatáshoz:
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

Ezután ellenőrizze a naplót, ha a kiszolgáló kapcsolata le van választva a ping időtúllépéssel.

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>A szál-készlet éhezésének kiváltó okainak megkeresése

A szál-készlet éhezésének kiváltó okainak megkeresése:

* A memória kiírása, majd a hívási verem elemzése. További információ: [memóriaképek gyűjtése és elemzése](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/).
* A [clrmd](https://github.com/microsoft/clrmd) használata a memória kiírásához, ha a szál-készlet éhezés észlelhető. Ezután jelentkezzen be a hívási verembe.

### <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

1. Nyissa meg az App kiszolgálóoldali naplót, és ellenőrizze, hogy nem történt-e rendellenesen.
2. Ellenőrizze az alkalmazás kiszolgálóoldali eseménynaplójában, hogy az alkalmazáskiszolgáló újraindult-e.
3. Hozzon létre egy problémát. Adja meg az időkeretet, és küldje el nekünk az erőforrás nevét.

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>Tippek

<a name="view_request"></a>

* Hogyan lehet megtekinteni a kimenő kérést az ügyféltől?
Vegyen fel ASP.NET Core egyet például (a ASP.NET egy hasonló):
    * Böngészőből:

        A Chrome-ra példaként használhatja az **F12** billentyűt a konzol ablak megnyitásához, és váltson a **hálózat** lapra. Előfordulhat, hogy frissítenie kell a lapot az **F5 billentyűvel** a hálózat legelején történő rögzítéséhez.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome nézet hálózata":::

    * C#-ügyfélről:

        A helyi webes forgalom a [Hegedűs](https://www.telerik.com/fiddler)használatával tekinthető meg. A WebSocket-forgalom a Hegedűs 4,5 óta támogatott.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Hegedűs nézet hálózata" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Az Ügyfélkapcsolat újraindítása
    
    Az alábbi [mintakód](https://github.com/Azure/azure-signalr/tree/dev/samples) a kapcsolatok logikájának újraindítását tartalmazza a *mindig újrapróbálkozási* stratégiával:

    * [C#-ügyfél ASP.NET Core](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core JavaScript-ügyfél](https://github.com/Azure/azure-signalr/blob/release/1.0.0-preview1/samples/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET C#-ügyfél](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript-ügyfél](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[Problémákba ütközik vagy visszajelzést szeretne küldeni a hibaelhárításról? Tudassa velünk.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan kezelheti a gyakori problémákat. További általános hibaelhárítási módszereket is megtudhat. 

> [!div class="nextstepaction"]
> [A kapcsolat és az üzenetek kézbesítésével kapcsolatos problémák elhárítása](./signalr-howto-troubleshoot-method.md)
