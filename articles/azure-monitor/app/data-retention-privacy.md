---
title: Adatmegőrzés és tárolás az Azure Application Insightsban | Microsoft Docs
description: Adatmegőrzési és adatvédelmi szabályzati nyilatkozat
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mbullwin
ms.openlocfilehash: df441a55ef4a9a40fe4defcabca5f667eeddbf29
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207289"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Adatgyűjtés, megőrzés és tárolás az Application Insights szolgáltatásban

Ha telepíti az [Azure Application Insights][start] SDK-t az alkalmazásban, az telemetria küld az alkalmazásról a felhőre. Természetesen a felelős fejlesztők pontosan tudni szeretnék, hogy milyen adat érkezik, mi történik az adatmennyiséggel, és hogyan tarthatják kézben az irányítást. Különösen a bizalmas adatok küldhetők, hol tárolják, és mennyire biztonságosak? 

Először is a rövid választ:

* A "kívülről" futó szabványos telemetria-modulok nem valószínű, hogy bizalmas adatokat küldjenek a szolgáltatásnak. A telemetria a terheléssel, a teljesítménnyel és a használattal kapcsolatos metrikákkal, a kivételekkel és egyéb diagnosztikai adatokkal foglalkozik. A diagnosztikai jelentésekben látható fő felhasználói adatértékek URL-címek; az alkalmazás azonban nem tartalmazhat bizalmas adatokat egyszerű szövegként egy URL-címben.
* Írhat olyan kódot, amely további egyéni telemetria küld a diagnosztika és a figyelés használatának elősegítése érdekében. (Ez a bővíthetőség a Application Insights nagyszerű funkciója.) A kód megírása lehetséges, ha a személyes és más bizalmas adatokat is tartalmaz. Ha az alkalmazás együttműködik ilyen adattal, alaposan tekintse át az összes írni kívánt kódot.
* Az alkalmazás fejlesztése és tesztelése során könnyen megvizsgálhatja, hogy mi történik az SDK-ban. Az adatokat az IDE és böngésző kimeneti ablakában jeleníti meg. 
* Az adattárolást az Egyesült Államokban vagy Európában [Microsoft Azure](https://azure.com) -kiszolgálók tárolják. (De az alkalmazás bárhol futhat.) Az Azure [erős biztonsági folyamatokkal rendelkezik, és megfelel a megfelelőségi szabványok széles skálájának](https://azure.microsoft.com/support/trust-center/). Csak Ön és a kijelölt csapat férhet hozzá az adataihoz. A Microsoft munkatársai csak bizonyos korlátozott körülmények között érhetik el a hozzáférést az Ön számára. Az átvitel és a nyugalmi állapotban is titkosítva van.

A cikk további részében részletesen ismertetjük a válaszokat. Úgy tervezték, hogy önálló legyen, így megjelenítheti azokat a munkatársakat, akik nem részei a közvetlen csapatának.

## <a name="what-is-application-insights"></a>Mi az Application Insights?
Az [Azure Application Insights][start] a Microsoft által biztosított szolgáltatás, amely segít az élő alkalmazások teljesítményének és használhatóságának javításában. Az alkalmazást folyamatosan figyeli az alkalmazás futása során, a tesztelés során, illetve a közzététel vagy a telepítés után. A Application Insights diagramokat és táblákat hoz létre, amelyek megmutatják, hogy a legtöbb felhasználó mikor kapja meg a legtöbb felhasználót, hogyan reagál az alkalmazásra, és milyen jól szolgálja ki azokat a külső szolgáltatások, amelyektől függenek. Ha összeomlik, hiba vagy teljesítménnyel kapcsolatos probléma merül fel, részletesen megkeresheti a telemetria adatokat az OK diagnosztizálásához. A szolgáltatás pedig e-maileket küld az alkalmazás rendelkezésre állásának és teljesítményének változásairól.

Ennek a funkciónak a beszerzéséhez telepítenie kell egy Application Insights SDK-t az alkalmazásba, amely a kód részévé válik. Ha az alkalmazás fut, az SDK figyeli a műveletét, és elküldi a telemetria a Application Insights szolgáltatásnak. Ez egy [Microsoft Azure](https://azure.com)által üzemeltetett felhőalapú szolgáltatás. (A Application Insights azonban bármilyen alkalmazás esetében működik, nem csak az Azure-ban üzemeltetett alkalmazásokat.)

A Application Insights szolgáltatás a telemetria tárolja és elemzi. Ha meg szeretné tekinteni az elemzést, vagy keresést végez a tárolt telemetria, jelentkezzen be az Azure-fiókjába, és nyissa meg az alkalmazás Application Insights erőforrását. Megoszthatja az adataihoz való hozzáférést is a csapat más tagjaival vagy a megadott Azure-előfizetőkkel.

A Application Insights szolgáltatásból exportálhatja az adatait, például egy adatbázisba vagy egy külső eszközre. Minden eszközt a szolgáltatástól kapott speciális kulccsal kell megadnia. Ha szükséges, a kulcs visszavonható. 

Application Insights SDK-k számos különböző típusú alkalmazáshoz érhetők el: a saját Java EE-vagy ASP.NET-kiszolgálókon üzemeltetett webszolgáltatások vagy az Azure-ban. webes ügyfelek – vagyis a weblapokon futó kód asztali alkalmazások és szolgáltatások; eszköz-alkalmazások, például Windows Phone-telefon, iOS és Android. Minden telemetria ugyanarra a szolgáltatásra küldenek.

## <a name="what-data-does-it-collect"></a>Milyen adatokat gyűjt?
### <a name="how-is-the-data-is-collected"></a>Hogyan történik az adatok gyűjtése?
Három adatforrás található:

* Az SDK, amelyet [fejlesztési](../../azure-monitor/app/asp-net.md) vagy [futtatási időben](../../azure-monitor/app/monitor-performance-live-website-now.md)integrálhat az alkalmazásba. Különböző SDK-k léteznek különböző típusú alkalmazásokhoz. Emellett van egy [SDK a](../../azure-monitor/app/javascript.md)weblapokhoz is, amelyek betöltődik a végfelhasználó böngészőjébe az oldallal együtt.
  
  * Minden SDK számos modult tartalmaz [](../../azure-monitor/app/configuration-with-applicationinsights-config.md), amelyek különböző módszereket használnak különböző telemetria gyűjtéséhez.
  * Ha a fejlesztés során telepíti az SDK-t, az API-val saját telemetria is elküldheti a standard modulok mellett. Ez az egyéni telemetria tartalmazhat az elküldeni kívánt összes adathalmazt.
* Egyes webkiszolgálókon vannak olyan ügynökök is, amelyek az alkalmazással együtt futnak, és telemetria küldenek a CPU, a memória és a hálózat kihasználtságáról. Például az Azure-beli virtuális gépek, a Docker-gazdagépek és a [Java EE-kiszolgálók](../../azure-monitor/app/java-agent.md) rendelkezhetnek ilyen ügynökökkel.
* A [rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) a Microsoft által futtatott folyamatok, amelyek rendszeres időközönként küldenek kéréseket a webalkalmazásnak. Az eredményeket a rendszer elküldi a Application Insights szolgáltatásnak.

### <a name="what-kinds-of-data-are-collected"></a>Milyen típusú adatokat gyűjt a rendszer?
A fő kategóriák a következők:

* [Webkiszolgáló telemetria](../../azure-monitor/app/asp-net.md) – HTTP-kérelmek.  URI, a kérelem feldolgozásához szükséges idő, válasz kódja, ügyfél IP-címe. Munkamenet-azonosító.
* [Weblapok](../../azure-monitor/app/javascript.md) – oldal, felhasználói és munkamenetek száma. Oldal betöltési ideje Kivételek. Ajax-hívások.
* Teljesítményszámlálók – memória, CPU, IO, hálózati kihasználtság.
* Ügyfél-és kiszolgálói környezet – operációs rendszer, területi beállítás, eszköz típusa, böngésző, képernyőfelbontás.
* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md) és összeomlások – **verem**-memóriaképek, LÉTREHOZÁSi azonosító, CPU-típus. 
* [Függőségek](../../azure-monitor/app/asp-net-dependencies.md) – külső szolgáltatásokra irányuló hívások, például REST, SQL, Ajax. URI vagy a kapcsolatok karakterlánca, időtartam, sikeres, parancs.
* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) – a tesztelés és a lépések időtartama, válaszok.
* A [nyomkövetési naplók](../../azure-monitor/app/asp-net-trace-logs.md) és az [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md) - **minden kódot a naplókba vagy telemetria**.

[További részletek](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hogyan tudom ellenőrizni, hogy milyen adatokat gyűjt a rendszer?
Ha a Visual Studióval fejleszti az alkalmazást, futtassa az alkalmazást hibakeresési módban (F5). A telemetria megjelenik a kimeneti ablakban. Innen átmásolhatja és formázhatja JSON-ként az egyszerű ellenőrzés érdekében. 

![](./media/data-retention-privacy/06-vs.png)

A diagnosztika ablakban is olvasható a nézet.

Weblapok esetében nyissa meg a böngésző hibakeresési ablakát.

![Nyomja meg az F12 billentyűt, és nyissa meg a hálózat lapot.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Írhatok kódot a telemetria szűréséhez az elküldése előtt?
Ez egy [telemetria-feldolgozó beépülő modul](../../azure-monitor/app/api-filtering-sampling.md)megírásával lehetséges.

## <a name="how-long-is-the-data-kept"></a>Mennyi ideig tart az adat?
A nyers adatpontok (azaz az elemzésekben lekérdezhető és a keresés során megvizsgálandó elemek) akár 730 napig is megmaradnak. [A megőrzési időtartam](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 vagy 730 nap lehet. Ha 730 napnál hosszabb ideig kell megőriznie az adatmennyiséget, a [folyamatos exportálás](../../azure-monitor/app/export-telemetry.md) használatával átmásolhatja azt egy Storage-fiókba az adatfeldolgozás során. 

Az 90 napnál hosszabb ideig tartott adatok mellett további díjak is felmerülhetnek. További információ a [Azure monitor díjszabási oldalának](https://azure.microsoft.com/pricing/details/monitor/)Application Insights díjszabásáról.

Az összesített adatokat (azaz a számításokat, az átlagokat és az egyéb statisztikai adatokat, amelyek a metrika Explorerben láthatók) a 90 napos gabona 1 percen belül őrzi meg.

[](../../azure-monitor/app/snapshot-debugger.md) A hibakeresési Pillanatképek tárolása tizenöt napig történik. A megőrzési házirend van beállítva a alkalmazásonként. Ha ez az érték növelése van szüksége, kérheti növelését az Azure Portalon nyissa meg egy támogatási esetet.

## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
Az adatai láthatók az Ön számára, és ha van szervezeti fiókja, a csapattagok. 

Ezt Ön és a csapattagok is exportálhatjuk, és más helyekre is másolhatók, és más személyeknek is átadhatók.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Mit tesz a Microsoft az alkalmazás által küldött adatokkal Application Insights?
A Microsoft csak a szolgáltatás nyújtásához használja fel az adatgyűjtést.

## <a name="where-is-the-data-held"></a>Hol tárolják az adattárolást?
* Ha új Application Insights erőforrást hoz létre, akkor kiválaszthatja a helyet. További információ a Application Insights rendelkezésre állásról [](https://azure.microsoft.com/global-infrastructure/services/?products=all)régiónként.

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Ez azt jelenti, hogy az alkalmazást az USA-ban, Európában vagy Délkelet-Ázsiában kell üzemeltetni?
* Nem. Az alkalmazás bárhol futhat, akár a saját helyszíni gazdagépeken, akár a felhőben.

## <a name="how-secure-is-my-data"></a>Mennyire biztonságos az adataim?
Application Insights egy Azure-szolgáltatás. A biztonsági szabályzatokat az [Azure biztonsági, adatvédelmi és megfelelőségi tanulmányai](https://go.microsoft.com/fwlink/?linkid=392408)tárgyalják.

Az adattárolás Microsoft Azure-kiszolgálókon történik. Az Azure Portalon lévő fiókok esetében a fiókok korlátozásait az [Azure biztonsági, adatvédelmi és megfelelőségi dokumentuma](https://go.microsoft.com/fwlink/?linkid=392408)tárgyalja.

A Microsoft munkatársai korlátozott mértékben férhetnek hozzá adataihoz. Csak az Ön engedélyével férhet hozzá az adataihoz, és a Application Insights használatának támogatásához szükséges. 

Az összes ügyfelünk alkalmazásaiban (például az adatdíjakban és a Nyomkövetések átlagos méretében) összesített adatokat a Application Insights javítására használjuk.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Tud valaki másnak telemetria beavatkozni a Application Insights adataival?
További telemetria is küldhetnek a fiókjához a kialakítási kulccsal, amely a weblapok kódjában található. A mérőszámok elegendő mennyiségű adattal nem felelnek meg az alkalmazás teljesítményének és használatának.

Ha más projektekkel oszt meg kódot, ne felejtse el eltávolítani a kialakítási kulcsot.

## <a name="is-the-data-encrypted"></a>Titkosítva van-e az adatátvitel?
Az összes adatok titkosítva maradnak, és az adatközpontok között mozognak.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>A rendszer az alkalmazásból a Application Insights kiszolgálókra titkosítja az adatok átvitelét?
Igen, a https használatával az adatoknak a portálra való küldését szinte minden SDK-ból, beleértve a webkiszolgálókat, az eszközöket és a HTTPS-weblapokat is. Az egyetlen kivétel az egyszerű HTTP-weblapokról érkező adatok.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Az SDK ideiglenes helyi tárolót hoz létre?

Igen, bizonyos telemetria csatornák helyileg is megőrzik az adattárolást, ha egy végpont nem érhető el. Tekintse át alább, hogy mely keretrendszerek és telemetria-csatornák érintettek.

A helyi tárolót használó telemetria-csatornák ideiglenes fájlokat hoznak létre a TEMP vagy az APPDATA címtárakban, amelyek az alkalmazást futtató adott fiókra korlátozódnak. Ez akkor fordulhat elő, ha egy végpont átmenetileg nem érhető el, vagy elérte a szabályozási korlátot. A probléma megoldása után a telemetria-csatorna folytatja az új és megőrzött adatok küldését.

Ez a megőrzött adatbázis nem titkosított helyileg. Ha ez aggodalomra ad okot, tekintse át az adatgyűjtést, és korlátozza a személyes adataik gyűjteményét. (További információért lásd [a magánjellegű adatok exportálását és törlését](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) ismertető témakört.)

Ha az ügyfélnek meghatározott biztonsági követelményekkel kell konfigurálnia ezt a könyvtárat, akkor az egy keretrendszer szerint konfigurálható. Győződjön meg arról, hogy az alkalmazás futtatására szolgáló folyamat rendelkezik írási hozzáféréssel ehhez a címtárhoz, de győződjön meg arról, hogy a könyvtár védett, hogy elkerülje a nem kívánt felhasználók által beolvasott telemetria.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`az adatmegőrzéshez használatos. Ez a hely nem konfigurálható a konfigurációs könyvtárból, és a mappa elérésére vonatkozó engedélyek az adott felhasználóra korlátozódnak a szükséges hitelesítő adatokkal. (Lásd [](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) a megvalósítást itt.)

###  <a name="net"></a>.Net

Alapértelmezés `ServerTelemetryChannel` szerint az aktuális felhasználó helyi alkalmazás-adatmappáját `%localAppData%\Microsoft\ApplicationInsights` vagy ideiglenes mappáját `%TMP%`használja. (Lásd [](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) a megvalósítást itt.)


Konfigurációs fájlon keresztül:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Kód használatával:

- ServerTelemetryChannel eltávolítása a konfigurációs fájlból
- Adja hozzá ezt a kódrészletet a konfigurációhoz:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Alapértelmezés `ServerTelemetryChannel` szerint az aktuális felhasználó helyi alkalmazás-adatmappáját `%localAppData%\Microsoft\ApplicationInsights` vagy ideiglenes mappáját `%TMP%`használja. (Lásd [](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) a megvalósítást itt.) Linux-környezetben a helyi tárterület le lesz tiltva, kivéve, ha meg van adva egy tárolási mappa.

A következő kódrészlet bemutatja, hogyan állítható `ServerTelemetryChannel.StorageFolder` be az `ConfigureServices()` `Startup.cs` osztály metódusában:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(További információért lásd a [AspNetCore egyéni konfigurációját](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) ismertető témakört. )

### <a name="nodejs"></a>Node.js

Alapértelmezés `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` szerint az adatmegőrzésre szolgál. A mappához való hozzáféréshez szükséges engedélyek az aktuális felhasználóra és rendszergazdákra korlátozódnak. (Lásd [](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) a megvalósítást itt.)

A mappa előtagja `appInsights-node` felülbírálható a [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)fájlban található statikus változó `Sender.TEMPDIR_PREFIX` futásidejű értékének módosításával.



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hogyan az adatküldés Application Insights a TLS 1,2 használatával?

Az Application Insights-végpontok felé irányuló adatátvitel biztonságának biztosításához határozottan javasoljuk, hogy az alkalmazásait legalább Transport Layer Security (TLS) 1,2 használatára konfigurálja. Sebezhetők régebbi verziói a TLS/Secure Sockets Layer (SSL) találhatók, és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**, és az iparág gyorsan változó kénytelen volt megszakítani ezt támogatása ezen régebbi protokollok. 

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) be van állítva egy [2018. június 30. a határidő](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) régebbi verziói a TLS/SSL és a frissítés biztonságosabb protokoll letiltásához. Ha az Azure elveszíti az örökölt támogatást, ha az alkalmazás vagy az ügyfelek nem tudnak kommunikálni legalább TLS 1,2-ben, akkor nem fog tudni adatküldeni a Application Insightsba. Az alkalmazás TLS-támogatásának teszteléséhez és ellenőrzéséhez szükséges módszer az operációs rendszertől/platformtól, valamint az alkalmazás által használt nyelvtől/keretrendszertől függően változhat.

Nem ajánlott explicit módon beállítani az alkalmazást úgy, hogy csak a TLS 1,2-et használja, kivéve, ha ez nem feltétlenül szükséges, mert ez a platform szintű biztonsági funkciókat is lehetővé teszi, amelyekkel automatikusan észlelhetők és kihasználhatják az újabb biztonságosabb protokollokat. elérhető, például TLS 1,3. Javasoljuk, hogy az alkalmazás kódjának alapos vizsgálatával ellenőrizze az adott TLS/SSL-verziók rögzítjük.

### <a name="platformlanguage-specific-guidance"></a>Platform/nyelvspecifikus útmutató

|Platformon és nyelven | Támogatás | További információ |
| --- | --- | --- |
| Azure App Services  | Támogatott, szükség lehet a konfigurációra. | A támogatást 2018 áprilisában jelentették be. Olvassa el a [konfigurációs adatokról](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)szóló közleményt.  |
| Azure Function-alkalmazások | Támogatott, szükség lehet a konfigurációra. | A támogatást 2018 áprilisában jelentették be. Olvassa el a [konfigurációs adatokról](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)szóló közleményt. |
|.NET | Támogatott, a konfiguráció a verziótól függően változik. | A .NET 4,7-es és korábbi verziók részletes konfigurációs adatai a [következő útmutatókban találhatók](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Állapotmonitor | Támogatott, konfigurálás szükséges | A Állapotmonitor az [operációs rendszer konfigurációja](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.net](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) -konfigurációra támaszkodik a TLS 1,2 támogatásához.
|Node.js |  A v 10.5.0-ben támogatott konfigurációra lehet szükség. | Bármely alkalmazásspecifikus konfigurációhoz használja a [hivatalos Node. js TLS/SSL-dokumentációt](https://nodejs.org/api/tls.html) . |
|Java | Támogatott, a TLS 1,2-es JDK-támogatása a [jdk 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) és a [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html)verzióban lett hozzáadva. | A JDK 8 [alapértelmezés szerint a TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)-et használja.  |
|Linux | Linux-disztribúciók általában támaszkodhat [OpenSSL](https://www.openssl.org) a TLS 1.2 támogatása.  | Ellenőrizze a [OpenSSL változásnaplójában](https://www.openssl.org/news/changelog.html) annak ellenőrzéséhez, hogy az OpenSSL-verziót támogatja.|
| Windows 8.0-s és 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállítások](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| A Windows Server 2012-2016-ban | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállításai](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és a Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) lap engedélyezése részleteiért.  |
| Windows Server 2008 SP2 | A TLS 1.2 támogatásához szükséges frissítést. | Lásd: [frissítést a TLS 1.2 támogatása](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) a Windows Server 2008 SP2. |
|Windows Vista | Nem támogatott. | –

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Győződjön meg arról, hogy az OpenSSL milyen verziója fut a Linux-disztribúcióban

A telepített OpenSSL-verziójának megtekintéséhez nyissa meg a terminált, és futtassa a következőt:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Teszt TLS 1,2-tranzakció futtatása Linux rendszeren

Egy alapszintű előzetes teszt futtatásával ellenőrizheti, hogy a Linux rendszer képes-e kommunikálni a TLS 1,2-en keresztül. Nyissa meg a terminált, és futtassa a következőket:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>A Application Insightsban tárolt személyes adattárolás

A [Application Insights személyes adatkezelési cikk](../../azure-monitor/platform/personal-data-mgmt.md) részletesen ismerteti ezt a problémát.

#### <a name="can-my-users-turn-off-application-insights"></a>Kikapcsolhatom a felhasználók a Application Insights?
Közvetlenül nem. Nem biztosítunk olyan kapcsolót, amelyet a felhasználók a Application Insights kikapcsolására használhatnak.

Egy ilyen szolgáltatást azonban az alkalmazásban is megvalósíthat. Az SDK-k mindegyike tartalmaz egy API-beállítást, amely kikapcsolja a telemetria-gyűjteményt. 

## <a name="data-sent-by-application-insights"></a>A Application Insights által elküldett adatszolgáltatások
Az SDK-k különböző platformok között változnak, és több összetevő is telepíthető. (Lásd: [Application Insights – áttekintés][start].) Az egyes összetevők különböző adatokat küldenek.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Különböző forgatókönyvekben elküldett adatok osztályai

| A művelet | Összegyűjtött adatosztályok (lásd a következő táblázatot) |
| --- | --- |
| [Application Insights SDK hozzáadása .NET webes projekthez][greenbrown] |ServerContext<br/>Következtetni<br/>Teljesítményszámlálók<br/>Kérelmek<br/>**Kivételek**<br/>Munkamenet<br/>Felhasználók |
| [Állapotmonitor telepítése IIS-kiszolgálón][redfield] |Függőségek<br/>ServerContext<br/>Következtetni<br/>Teljesítményszámlálók |
| [Application Insights SDK hozzáadása Java-webalkalmazáshoz][java] |ServerContext<br/>Következtetni<br/>Kérés<br/>Munkamenet<br/>Felhasználók |
| [JavaScript SDK hozzáadása a weboldalhoz][client] |ClientContext <br/>Következtetni<br/>Oldal<br/>ClientPerf<br/>Ajax |
| [Alapértelmezett tulajdonságok megadása][apiproperties] |A standard és az egyéni események **tulajdonságai** |
| [TrackMetric hívása][api] |Numerikus értékek<br/>**Tulajdonságok** |
| [Hívási sáv *][api] |Esemény neve<br/>**Tulajdonságok** |
| [TrackException hívása][api] |**Kivételek**<br/>Verem kiírása<br/>**Tulajdonságok** |
| Az SDK nem tud adatokat gyűjteni. Példa: <br/> -nem érhető el a teljesítményszámláló-számlálók<br/> – kivétel a telemetria inicializálásakor |SDK-diagnosztika |

[Más platformokhoz készült SDK][platforms]-k esetében tekintse meg a dokumentumokat.

#### <a name="the-classes-of-collected-data"></a>Az összegyűjtött adatok osztályai

| Összegyűjtött adatosztály | Tartalmazza (nem kimerítő lista) |
| --- | --- |
| **Tulajdonságok** |**A kód által meghatározott összes adattal** |
| DeviceContext |Azonosító, IP, területi beállítás, eszköz modell, hálózat, hálózat típusa, SZÁMÍTÓGÉPGYÁRTÓ neve, képernyőfelbontás, szerepkör-példány, szerepkör neve, eszköz típusa |
| ClientContext |Operációs rendszer, területi beállítás, nyelv, hálózat, ablak felbontása |
| Munkamenet |munkamenet-azonosító |
| ServerContext |Számítógépnév, területi beállítás, operációs rendszer, eszköz, felhasználói munkamenet, felhasználói környezet, művelet |
| Következtetni |földrajzi hely az IP-címről, időbélyeg, operációs rendszer, böngésző |
| Mérőszámok |Metrika neve és értéke |
| Events |Esemény neve és értéke |
| Oldalmegtekintések |URL-cím és az oldal neve vagy a képernyő neve |
| Ügyfél-Teljesítményfigyelő |URL-cím/oldal neve, böngésző betöltési ideje |
| Ajax |HTTP-hívások a weblapokról a kiszolgálóra |
| Kérelmek |URL-cím, időtartam, válasz kódja |
| Függőségek |Type (SQL, HTTP,...), kapcsolatok karakterlánca vagy URI, szinkronizálás/aszinkron, időtartam, sikeres, SQL-utasítás (Állapotmonitor) |
| **Kivételek** |Típus, **üzenet**, hívási verem, forrásfájl és sor száma, szál azonosítója |
| Összeomlik |Folyamat azonosítója, szülő folyamat azonosítója, összeomlási szál azonosítója; alkalmazás-javítás, azonosító, Build;  Kivétel típusa, címe, ok; eltorzított szimbólumok és regiszterek, bináris kezdő és záró címek, bináris név és elérési út, processzor típusa |
| Híváslánc |**Üzenet** és súlyossági szint |
| Teljesítményszámlálók |Processzoridő, rendelkezésre álló memória, kérelmek gyakorisága, kivételek száma, a folyamat saját bájtjai, i/o-sebesség, kérelem időtartama, kérelmek várólistájának hossza |
| Rendelkezésre állás |Webteszt-válasz kódja, az egyes tesztelési lépések időtartama, teszt neve, időbélyeg, sikeres, válaszidő, tesztelési hely |
| SDK-diagnosztika |Nyomkövetési üzenet vagy kivétel |

[Az ApplicationInsights. config fájl szerkesztésével kikapcsolhatja az egyes][config] adatmennyiségeket

> [!NOTE]
> Az ügyfél IP-címe a földrajzi hely kiértékelésére szolgál, de alapértelmezés szerint az IP-adatmennyiség már nem tárolódik, és az összes nulla a társított mezőbe íródik. Ha többet szeretne megtudni a személyes adatkezelésről, javasoljuk ezt a [cikket](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Ha IP-cím adatait kell tárolnia, az [IP-cím gyűjtésével kapcsolatos cikk](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) végigvezeti Önt a lehetőségein.

## <a name="credits"></a>Stáblisták
Ez a termék a [https://www.maxmind.com](https://www.maxmind.com)Maxmind által létrehozott GeoLite2-adatokból áll.



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
