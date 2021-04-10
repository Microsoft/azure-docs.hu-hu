---
title: Telemetria csatornák Az Azure Application Insightsban | Microsoft Docs
description: Telemetria-csatornák testreszabása az Azure Application Insights SDK-ban a .NET-hez és a .NET Core-hoz.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937348"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetria csatornák Application Insights

A telemetria csatornák az [Azure Application Insights SDK](./app-insights-overview.md)-k szerves részét képezik. Kezelik a telemetria pufferelését és a Application Insights szolgáltatásba való átvitelét. Az SDK-k .NET-és .NET Core-verziói két beépített telemetria-csatornával rendelkeznek: `InMemoryChannel` és `ServerTelemetryChannel` . Ez a cikk részletesen ismerteti az egyes csatornákat, beleértve a csatornák viselkedésének testreszabását is.

## <a name="what-are-telemetry-channels"></a>Mik azok a telemetria-csatornák?

A telemetria csatornák felelősek a telemetria-elemek puffereléséhez és a Application Insights szolgáltatásba való küldéséhez, ahol lekérdezési és elemzési célból vannak tárolva. A telemetria-csatorna bármely olyan osztály, amely megvalósítja az [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) illesztőfelületet.

A `Send(ITelemetry item)` telemetria-csatorna metódusát a rendszer az összes telemetria inicializáló és telemetria processzor hívása után hívja meg. Tehát a telemetria-feldolgozó által eldobott elemek nem érik el a csatornát. `Send()` nem általában azonnal küldi el az elemeket a háttérbe. A szolgáltatás általában a memóriában, és kötegekben küldi el őket a hatékony átvitel érdekében.

A [élő metrikastream](live-stream.md) egy egyéni csatornát is tartalmaz, amely a telemetria élő közvetítését is felruházza. Ez a csatorna független a normál telemetria-csatornától, és ez a dokumentum nem vonatkozik rá.

## <a name="built-in-telemetry-channels"></a>Beépített telemetria csatornák

A Application Insights .NET és .NET Core SDK-k két beépített csatornával rendelkeznek:

* `InMemoryChannel`: Egy könnyűsúlyú csatorna, amely a memóriában lévő elemeket a továbbításig kihasználja. Az elemek a memóriában vannak puffereltek, és 30 másodpercenként kiürítve, vagy ha 500 elem van pufferelt. Ez a csatorna minimális megbízhatósági garanciákat biztosít, mivel a hiba után nem próbálkozik újra a telemetria küldésével. Ez a csatorna nem tartja meg a lemezen lévő elemeket is, így az összes el nem küldött elem véglegesen elvész az alkalmazás leállításakor (kecses vagy nem). Ez a csatorna olyan metódust valósít meg, amely lehetővé teszi a `Flush()` memóriában tárolt telemetria-elemek egyidejű kényszerítését. Ez a csatorna kiválóan alkalmas a rövid ideig futó alkalmazások esetében, amelyeknél a szinkron ürítés ideális.

    Ez a csatorna a Microsoft. ApplicationInsights NuGet csomag részét képezi, és az az alapértelmezett csatorna, amelyet az SDK használ, ha nincs más konfigurálva.

* `ServerTelemetryChannel`: Egy fejlettebb csatorna, amely újrapróbálkozási házirendekkel rendelkezik, és képes az adattárolásra helyi lemezen. Ez a csatorna újrapróbálkozik a telemetria küldésével, ha átmeneti hibák történnek. Ez a csatorna a helyi lemezes tárolást is használja a hálózati kimaradások vagy a nagy telemetria-kötetek esetében a lemezen lévő elemek megtartására. Az újrapróbálkozási mechanizmusok és a helyi lemezes tárolás miatt ez a csatorna megbízhatóbbnak minősül, és minden éles környezetben ajánlott. Ez a csatorna az alapértelmezett a [ASP.net](./asp-net.md) és a [ASP.net Core](./asp-net-core.md) a hivatalos dokumentációnak megfelelően konfigurált alkalmazások esetében. Ez a csatorna kiszolgálói forgatókönyvekhez van optimalizálva hosszan futó folyamatokkal. A [`Flush()`](#which-channel-should-i-use) csatorna által megvalósított metódus nem szinkronban van.

    Ez a csatorna a Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel NuGet csomagként érhető el, és a Microsoft. ApplicationInsights. web vagy a Microsoft. ApplicationInsights. AspNetCore NuGet csomag használatakor automatikusan megtörténik.

## <a name="configure-a-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Telemetria-csatornát úgy konfigurálhat, hogy az aktív telemetria-konfigurációra állítja be. A ASP.NET alkalmazások esetében a konfiguráció magában foglalja a telemetria-csatorna példányának `TelemetryConfiguration.Active` vagy módosításának beállítását `ApplicationInsights.config` . ASP.NET Core alkalmazások esetében a konfiguráció magában foglalja a csatorna hozzáadását a függőségi injektálási tárolóhoz.

A következő részekben példákat talál a `StorageFolder` csatorna beállításának konfigurálására különböző típusú alkalmazásokban. `StorageFolder` csak a konfigurálható beállítások egyike. A konfigurációs beállítások teljes listájáért tekintse meg a cikk későbbi, [a beállítások című szakaszát](#configurable-settings-in-channels) .

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurálás ApplicationInsights.config használatával ASP.NET-alkalmazásokhoz

A [ApplicationInsights.config](configuration-with-applicationinsights-config.md) következő szakasza a beállított `ServerTelemetryChannel` csatornát `StorageFolder` egy egyéni helyre állítja be:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguráció a kódban a ASP.NET-alkalmazásokhoz

A következő kód egy "ServerTelemetryChannel" példányt állít be, amely `StorageFolder` Egyéni helyre van beállítva. Adja hozzá ezt a kódot az alkalmazás elején, jellemzően a `Application_Start()` Global. aspx. cs metódusban.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguráció a kódban ASP.NET Core alkalmazások számára

Módosítsa a `ConfigureServices` osztály metódusát az `Startup.cs` itt látható módon:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> `TelemetryConfiguration.Active`ASP.net Core alkalmazások esetében nem ajánlott a csatornát a használatával konfigurálni.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguráció a .NET/.NET Core Console-alkalmazások kódjában

A konzolos alkalmazások esetében a kód ugyanaz, mint a .NET és a .NET Core esetében:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>A ServerTelemetryChannel működési részletei

`ServerTelemetryChannel` egy memóriában lévő pufferben tárolja a beérkező elemeket. A rendszer 30 másodpercenként egyszer szerializálja, tömöríti és tárolja a `Transmission` példányokat, vagy ha az 500-es elemek pufferelése megtörtént. Egy `Transmission` példány legfeljebb 500 elemet tartalmaz, és a Application Insights szolgáltatásnak egyetlen HTTPS-híváson keresztül küldött telemetria-köteget jelöl.

Alapértelmezés szerint legfeljebb 10 `Transmission` példányt lehet elküldeni párhuzamosan. Ha a telemetria gyorsabban érkezik, vagy ha a hálózat vagy a Application Insights háttérrendszer lassú, a `Transmission` példányok tárolása a memóriában történik. A memóriában lévő puffer alapértelmezett kapacitása `Transmission` 5 MB. Ha túllépte a memórián belüli kapacitást, a `Transmission` példányok tárolása a helyi lemezen legfeljebb 50 MB-ra történik. `Transmission` a példányok helyi lemezen vannak tárolva, ha hálózati problémák vannak. Csak a helyi lemezen tárolt elemek maradnak meg az alkalmazás összeomlása miatt. A rendszer elküldi őket, amikor az alkalmazás újra elindul.

## <a name="configurable-settings-in-channels"></a>Konfigurálható beállítások a csatornákon

Az egyes csatornák konfigurálható beállításainak teljes listájáért lásd:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

A leggyakrabban használt beállítások a következők `ServerTelemetryChannel` :

1. `MaxTransmissionBufferCapacity`: A memória maximális mérete (bájt), amelyet a csatorna használ a memóriában lévő pufferek továbbítására. Ha eléri ezt a kapacitást, a rendszer az új elemeket közvetlenül a helyi lemezen tárolja. Az alapértelmezett érték 5 MB. Ha nagyobb értéket ad meg, a lemez kevesebb használatot eredményez, de ne feledje, hogy a memóriában lévő elemek elvesznek, ha az alkalmazás összeomlik.

1. `MaxTransmissionSenderCapacity`: Az `Transmission` Application Insights számára egyszerre küldendő példányok maximális száma. Az alapértelmezett érték 10. Ez a beállítás nagyobb számra állítható be, ami akkor ajánlott, ha nagy mennyiségű telemetria generál. A nagy mennyiség általában a terheléses tesztelés során vagy a mintavétel kikapcsolásakor fordul elő.

1. `StorageFolder`: A csatorna által az elemek lemezre való tárolásához használt mappa szükség szerint. A Windows rendszerben a rendszer% LOCALAPPDATA% vagy% TEMP% értéket használ, ha nincs más elérési út megadva explicit módon. A Windowson kívüli környezetekben meg kell adnia egy érvényes helyet vagy telemetria, amely nem tárolódik a helyi lemezen.

## <a name="which-channel-should-i-use"></a>Melyik csatornát érdemes használni?

`ServerTelemetryChannel` a hosszú ideig futó alkalmazásokat érintő legtöbb éles környezetben ajánlott. A `Flush()` által megvalósított metódus `ServerTelemetryChannel` nem szinkron, és nem garantálja az összes függőben lévő elem küldését a memóriából vagy a lemezből. Ha ezt a csatornát olyan helyzetekben használja, ahol az alkalmazás leáll, javasoljuk, hogy a hívás után némi késleltetést vezessen be `Flush()` . A szükséges késleltetési időtartam nem kiszámítható. Olyan tényezőktől függ, mint a `Transmission` memóriában lévő elemek vagy példányok száma, a lemez mennyisége, a háttérben továbbított mennyiség, valamint azt, hogy a csatorna exponenciális visszatartási forgatókönyvek közepén van-e.

Ha szinkron flöss szükséges, javasoljuk, hogy használja a következőt: `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>A Application Insights Channel garantálja a telemetria kézbesítését? Ha nem, milyen helyzetekben lehet elveszíteni a telemetria?

A rövid válasz az, hogy a beépített csatornák egyike sem nyújt tranzakció típusú garanciát a telemetria történő kézbesítéshez. `ServerTelemetryChannel` a `InMemoryChannel` megbízható kézbesítéshez képest fejlettebb, de csak a legjobb erőfeszítést tesz a telemetria küldéséhez. A telemetria több helyzetben is elvész, beleértve a gyakori forgatókönyveket:

1. Az alkalmazás összeomlása esetén a memóriában lévő elemek elvesznek.

1. A telemetria megszakadt a hálózati problémák hosszabb időtartama alatt. A telemetria helyi lemezre van tárolva a hálózati kimaradások során, vagy ha problémák merülnek fel a Application Insights háttérrel. Az 48 óránál régebbi elemek azonban el lesznek vetve.

1. A Windows telemetria tárolásának alapértelmezett helye a következő:% LOCALAPPDATA% vagy% TEMP%. Ezek a helyek általában helyiek a gépen. Ha az alkalmazás fizikailag áttelepíti az egyik helyről a másikra, az eredeti helyen tárolt összes telemetria elvész.

1. A Windows Web Apps esetében az alapértelmezett lemez-tárolási hely a D:\local\LocalAppData. Ezt a helyet nem őrzi meg a program. Az alkalmazás újraindításakor, kibővítve és más hasonló műveletekben törölhető, ami az ott tárolt telemetria elvesztését eredményezi. Felülbírálhatja az alapértelmezett értéket, és megtarthatja a tárterületet megőrzött helyre, például D:\home. Az ilyen megőrzött telephelyeket azonban a távoli tárterület szolgáltatja, így lassú lehet.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Működik a ServerTelemetryChannel a Windowson kívül más rendszereken?

Bár a csomagjának és névterének neve tartalmazza a "WindowsServer" nevet, ez a csatorna a Windows rendszeren kívül más rendszerek esetében is támogatott, a következő kivétellel. A Windowstól eltérő rendszereken a csatorna alapértelmezés szerint nem hoz létre helyi tároló mappát. Létre kell hoznia egy helyi tárolási mappát, és konfigurálnia kell a csatornát a használatára. A helyi tárterület konfigurálása után a csatorna ugyanúgy működik az összes rendszeren.

> [!NOTE]
> A kiadás 2.15.0-beta3 és a nagyobb helyi tárterület mostantól automatikusan létrejön a Linux, Mac és Windows rendszerekhez. Nem Windows rendszerű rendszerek esetén az SDK a következő logika alapján automatikusan létrehoz egy helyi tárolási mappát:
> - `${TMPDIR}` -Ha a `${TMPDIR}` környezeti változó be van állítva, a rendszer ezt a helyet használja.
> - `/var/tmp` – Ha az előző hely nem létezik, próbálkozzon `/var/tmp` .
> - `/tmp` – Ha az előző helyszínek közül mindkettő nem létezik, próbálkozzon `tmp` . 
> - Ha a helyek egyike sem létezik, a helyi tárterület nem jön létre, és a manuális konfigurálásra továbbra is szükség van. [A teljes megvalósítás részleteit](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Az SDK ideiglenes helyi tárolót hoz létre? Titkosítottak-e az adattárolóban tárolt adatforgalom?

Az SDK hálózati problémák vagy a szabályozás során tárolja a helyi tárolóban lévő telemetria-elemeket. Ezek az adatforgalom nem helyileg titkosított.

Windows rendszerek esetében az SDK automatikusan létrehoz egy ideiglenes helyi mappát a (z)% TEMP% vagy% LOCALAPPDATA% könyvtárban, és korlátozza a hozzáférést a rendszergazdákhoz és az aktuális felhasználóhoz.

A Windows rendszertől eltérő rendszerek esetén az SDK nem hozza létre automatikusan a helyi tárterületet, így alapértelmezés szerint nem tárolja az adattárolást.

> [!NOTE]
> A kiadás 2.15.0-beta3 és a nagyobb helyi tárterület mostantól automatikusan létrejön a Linux, Mac és Windows rendszerekhez. 

 Saját maga is létrehozhat egy tárolási könyvtárat, és beállíthatja a csatornát a használatára. Ebben az esetben Ön a felelős a címtár biztonságának biztosításáért.
További információ az adatvédelem [és az adatvédelem terén](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK
A Application Insights összes SDK-hoz hasonlóan a csatornák is nyílt forráskódúak. Olvassa el és járuljon hozzá a kód vagy a jelentéssel kapcsolatos problémákhoz [a hivatalos GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)-tárházban.

## <a name="next-steps"></a>Következő lépések

* [Mintavételezés](./sampling.md)
* [SDK – hibaelhárítás](./asp-net-troubleshoot-no-data.md)

