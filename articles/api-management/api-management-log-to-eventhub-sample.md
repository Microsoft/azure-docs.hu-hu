---
title: API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével
titleSuffix: Azure API Management
description: A eventhub házirendet bemutató példa az Azure API Management, az Azure Event Hubs és a Moesif HTTP-naplózáshoz és-figyeléshez való csatlakoztatásával
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88205511"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Az API-k monitorozása az Azure API Management, a Event Hubs és a Moesif segítségével
A [API Management szolgáltatás](api-management-key-concepts.md) számos lehetőséget kínál a http API-nak küldött HTTP-kérések feldolgozásának javítására. Azonban a kérések és válaszok megléte átmeneti jellegű. A rendszer elvégzi a kérést, és a API Management szolgáltatáson keresztül áramlik a háttérbeli API-ra. Az API feldolgozza a kérést, és a válasz visszaáramlik az API-fogyasztó felé. A API Management szolgáltatás megtart néhány fontos statisztikát az API-król a Azure Portal irányítópulton való megjelenítéshez, de ezen túlmenően a részletek eltűnnek.

Ha a API Management szolgáltatásban a eventhub szabályzatot használja, az adatokat a kérelemből és válaszból is elküldheti egy [Azure Event hub](../event-hubs/event-hubs-about.md)-ra. Számos oka lehet annak, hogy az API-khoz küldött HTTP-üzenetekről szeretne eseményeket előállítani. Néhány példa a frissítések naplózási nyomvonalára, a használati elemzésekre, a kivételek riasztására és a harmadik féltől származó integrációra.

Ez a cikk bemutatja, hogyan rögzítheti a teljes HTTP-kérést és válaszüzenetet, elküldheti azt egy Event hub-nak, majd továbbíthatja az üzenetet egy olyan külső szolgáltatásnak, amely HTTP-naplózási és figyelési szolgáltatásokat biztosít.

## <a name="why-send-from-api-management-service"></a>Miért érdemes elküldeni a API Management szolgáltatást?
Http-alapú middleware-t is írhat, amely a HTTP-kérelmek és válaszok rögzítésére és a naplózási és figyelési rendszerbe való felvételére képes a HTTP API-keretrendszerekbe való csatlakoztatásra. Ennek a megközelítésnek a hátránya a HTTP middleware integrálása a háttér-API-ba, és meg kell egyeznie az API platformjának. Ha több API van, akkor mindegyiknek telepítenie kell a middleware-t. Gyakran előfordul, hogy miért nem lehet frissíteni a háttérbeli API-kat.

Az Azure API Management szolgáltatás a naplózási infrastruktúrával való integrálásához központosított és platform-független megoldást biztosít. A részben az Azure API Management [geo-replikációs](api-management-howto-deploy-multi-region.md) képességei miatt is méretezhető.

## <a name="why-send-to-an-azure-event-hub"></a>Miért érdemes elküldeni egy Azure Event hub-nak?
Érdemes megkérdezni, miért hozzon létre egy, az Azure Event Hubs-ra vonatkozó szabályzatot? A kérések naplózása számos különböző helyen lehetséges. Miért nem csak közvetlenül a végső célra küldje el a kéréseket?  Ez egy lehetőség. Ha azonban egy API Management szolgáltatásból naplózza a kérelmeket, meg kell fontolnia, hogy az üzenetek naplózása milyen hatással van az API teljesítményére. A terhelés fokozatos növekedése a rendszerösszetevők rendelkezésre álló példányainak vagy a földrajzi replikálás kihasználása révén kezelhető. A forgalomban lévő rövid tüskék azonban késleltetik a kérések késleltetését, ha az infrastruktúra naplózására irányuló kérelmek lassan megkezdik a terhelést.

Az Azure Event Hubs a nagy mennyiségű adatforgalom beáramlására szolgál, amelynek kapacitása sokkal nagyobb számú eseményt igényel, mint a legtöbb API-t használó HTTP-kérelmek száma. Az Event hub az API Management szolgáltatás és az üzeneteket tároló és feldolgozó infrastruktúra között kifinomult pufferként működik. Ez biztosítja, hogy az API teljesítménye a naplózási infrastruktúra miatt nem fog szenvedni.

Ha az adott esemény központjának átadása megtörtént, a rendszer megőrzi, és megvárja, amíg az Event hub-felhasználók feldolgozzák azt. Az Event hub nem érdekli a feldolgozás folyamatát, csak arról gondoskodik, hogy az üzenet sikeres kézbesítése megtörténjen.

A Event Hubs képes több fogyasztói csoportba továbbítani az eseményeket. Ez lehetővé teszi az események különböző rendszerek általi feldolgozását. Ez lehetővé teszi számos integrációs forgatókönyv támogatását anélkül, hogy a API Management szolgáltatáson belüli API-kérelem feldolgozására további késleltetést kellene bevezetni, mivel csak egyetlen eseményt kell létrehoznia.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Alkalmazás-/http-üzenetek küldésére szolgáló szabályzat
Az Event hub egyszerű sztringként fogadja el az eseményeket. Ennek a karakterláncnak a tartalma. A HTTP-kérések becsomagolásához és a Event Hubsre való elküldéséhez a karakterláncot a kérelem vagy válasz adataival kell formázni. Ilyen helyzetekben, ha van egy meglévő formátum, amelyet felhasználhatunk, előfordulhat, hogy nem kell saját elemzési kódot írnia. Kezdetben úgy tekintem, hogy a [har](http://www.softwareishard.com/blog/har-12-spec/) -t használja a HTTP-kérések és válaszok küldéséhez. Ez a formátum azonban a HTTP-kérelmek egy sorozatának a JSON-alapú formátumban történő tárolására van optimalizálva. Számos olyan kötelező elemet foglalt magukban, amelyek szükségtelen bonyolultságot adnak a HTTP-üzenet hálózaton keresztüli átadásának forgatókönyvéhez.

Egy másik lehetőség az `application/http` adathordozó típusának használata az [RFC 7230](https://tools.ietf.org/html/rfc7230)http-specifikációban leírtak szerint. Ez az adathordozó-típus ugyanazt a formátumot használja, mint amellyel ténylegesen HTTP-üzeneteket küld a hálózaton keresztül, de a teljes üzenet egy másik HTTP-kérelem törzsében is elhelyezhető. Ebben az esetben a törzset fogjuk használni az üzenetünk Event Hubs való elküldéséhez. Kényelmesen, a [Microsoft ASP.net web API 2,2-es verziójának](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) olyan elemzője létezik, amely elemezni tudja ezt a formátumot, és átalakítja a natív `HttpRequestMessage` és `HttpResponseMessage` objektumokra.

Ahhoz, hogy létre tudja hozni ezt az üzenetet, ki kell használnia a C#-alapú [házirend-kifejezések](./api-management-policy-expressions.md) előnyeit az Azure API Managementban. Itt látható a szabályzat, amely HTTP-kérést küld az Azure Event Hubsnak.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Szabályzat deklarációja
Ebben a házirend-kifejezésben néhány konkrét dolgot érdemes megemlíteni. A eventhub-szabályzat egy Logger-ID nevű attribútummal rendelkezik, amely a API Management szolgáltatáson belül létrehozott naplózó nevére hivatkozik. Az Event hub-naplózó API Management szolgáltatásban való beállításának részletei a következő dokumentumban találhatók: [események naplózása az azure Event Hubsba az azure API Managementban](api-management-howto-log-event-hubs.md). A második attribútum egy opcionális paraméter, amely arra utasítja Event Hubs, hogy melyik partíció tárolja az üzenetet a alkalmazásban. Event Hubs partíciókat használ a méretezhetőség engedélyezéséhez, és legalább kettőt igényel. Az üzenetek rendezett kézbesítése csak egy partíción belül garantált. Ha nem utasítja el az Event hub-t, amelyben az üzenetet helyezi el, egy ciklikusan elválasztó algoritmust használ a terhelés elosztásához. Azonban előfordulhat, hogy az üzenetek bizonyos részét feldolgozzuk a rendelésből.

### <a name="partitions"></a>Partíciók
Annak biztosítása érdekében, hogy az üzenetek a felhasználók számára legyenek elérhetők, és kihasználják a partíciók terheléselosztási funkcióját, úgy döntöttem, hogy HTTP-kérési üzeneteket küldek egy partícióra és HTTP-válaszüzeneteket egy második partíciónak. Ez biztosítja még a terheléselosztást is, és garantáljuk, hogy az összes kérelem használatban van, és a rendszer az összes választ sorrendben felhasználja. A válasz a megfelelő kérelem előtt is felhasználható, de ez nem jelent problémát, mivel a válaszokra vonatkozó kérések egymáshoz való megfeleltetése nem probléma, és tudjuk, hogy a kérések mindig a válaszok előtt jönnek létre.

### <a name="http-payloads"></a>HTTP-adattartalom
A kiépítése után `requestLine` ellenőrizze, hogy a kérelem törzse csonkítva van-e. A kérés törzse csak 1024-ra van csonkítva. Ez megnövelhető, azonban az egyes Event hub-üzenetek 256 KB-ra korlátozódnak, így valószínű, hogy egyes HTTP-üzenetek nem férnek hozzá egyetlen üzenetben sem. A naplózás és az elemzés során jelentős mennyiségű információt lehet a HTTP-kérelmek sorából és fejlécből származtatni. Emellett sok API-kérés csak kisméretű törzseket ad vissza, így az adatértékek elvesztése a nagyméretű szervezetek csonkítása révén meglehetősen minimális az átvitel, a feldolgozás és a tárolási költségek csökkentése érdekében az összes törzs tartalmának megőrzéséhez képest. Egy utolsó megjegyzés a törzs feldolgozásával kapcsolatban, hogy át kell adni `true` a `As<string>()` metódusnak, mivel a szövegtörzs tartalmát olvasjuk, de a háttér-API is azt akarta, hogy a törzs beolvasható legyen. Ennek a módszernek az igaz értékre való átadásával a törzs pufferbe kerül, hogy egy második alkalommal is olvasható legyen. Fontos megjegyezni, hogy ha olyan API-val rendelkezik, amely nagyméretű fájlokat tölt fel, vagy hosszú lekérdezést használ. Ezekben az esetekben érdemes elkerülni a törzs olvasását.

### <a name="http-headers"></a>HTTP-fejlécek
A HTTP-fejlécek átvihetők az üzenet formátumára egyszerű kulcs/érték párok formájában. Úgy döntöttünk, hogy a hitelesítő adatok szükségtelen kiszivárgásának elkerülése érdekében kiszűrjük bizonyos biztonsági szempontból bizalmas mezőket. Nem valószínű, hogy az API-kulcsokat és az egyéb hitelesítő adatokat használja az elemzési célokra. Ha a felhasználóra és az általa használt termékre vonatkozó elemzést szeretnénk végezni, akkor az objektumból lekérhetjük ezt az `context` üzenetet, és hozzáadhatjuk azt az üzenethez.

### <a name="message-metadata"></a>Üzenet metaadatai
Az Event hub számára küldendő teljes üzenet létrehozásakor az első sor valójában nem része az `application/http` üzenetnek. Az első sor további metaadatokat tartalmaz, amelyekből az üzenet egy kérelem vagy válaszüzenet, valamint egy üzenet azonosítója, amely a válaszok összekapcsolására szolgál. Az üzenet azonosítója egy másik, a következőhöz hasonló házirend használatával jön létre:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Létrehozhatjuk a kérelem üzenetét, amely egy változóban volt tárolva, amíg a válasz vissza nem tért, majd elküldte a kérést és a választ egyetlen üzenetként. Ha azonban a kérést és a választ egymástól függetlenül küldi el, és egy üzenet-azonosítót használ a kettő összekapcsolásához, egy kicsit nagyobb rugalmasságot biztosítunk az üzenetek méretében, így több partíció előnyeit is kihasználhatja az üzenetek sorrendjének fenntartása mellett, és a kérés a naplózási irányítópulton is megjelenik. Bizonyos esetekben előfordulhat, hogy az Event hub számára soha nem érkezik érvényes válasz, valószínűleg végzetes hiba történt a API Management szolgáltatásban, de továbbra is van egy rekordja a kérésnek.

A válasz HTTP-üzenet elküldésére vonatkozó házirend a kérelemhez hasonlóan néz ki, így a teljes házirend-konfiguráció a következőképpen néz ki:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

A `set-variable` házirend egy olyan értéket hoz létre, amely `log-to-eventhub` a `<inbound>` szakasz és a szakasz szabályzatával is elérhető `<outbound>` .

## <a name="receiving-events-from-event-hubs"></a>Események fogadása Event Hubs
Az Azure Event hub eseményei a [AMQP protokoll](https://www.amqp.org/)használatával érkeznek. A Microsoft Service Bus csapata elérhetővé tette az ügyfél-kódtárakat, hogy megkönnyítsék a megrendelői eseményeket. Két különböző megközelítés támogatott, az egyik a *közvetlen fogyasztó* , a másik pedig az `EventProcessorHost` osztályt használja. A két megközelítés példái a [Event Hubs programozási útmutatóban](../event-hubs/event-hubs-programming-guide.md)találhatók. A különbségek rövid változata, amely teljes körű irányítást biztosít, és a különböző vízmennyiségeket is végrehajtja, `Direct Consumer` `EventProcessorHost` de bizonyos feltételezéseket tesz az események feldolgozásával kapcsolatban.

### <a name="eventprocessorhost"></a>EventProcessorHost
Ebben a példában a `EventProcessorHost` for egyszerűséget használjuk, de ez nem a legmegfelelőbb választás ehhez az adott forgatókönyvhöz. `EventProcessorHost` gondoskodik arról, hogy nem kell aggódnia az adott esemény-feldolgozó osztályon belül felmerülő problémákkal kapcsolatban. Ebben az esetben azonban egyszerűen konvertáljuk az üzenetet egy másik formátumba, és egy aszinkron módszerrel átadják egy másik szolgáltatásnak. Nincs szükség a megosztott állapot frissítésére, ezért nem áll fenn a szálakkal kapcsolatos problémák kockázata. A legtöbb esetben `EventProcessorHost` valószínűleg a legjobb választás, és ez természetesen a könnyebb megoldás.

### <a name="ieventprocessor"></a>IEventProcessor
A használatakor a központi fogalom a `EventProcessorHost` `IEventProcessor` metódust tartalmazó felület implementációjának létrehozása `ProcessEventAsync` . A metódus lényege itt látható:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

A rendszer átadja a metódusnak a EventData objektumok listáját, és ezt a listát megismétli. Az egyes metódusok bájtjait egy HttpMessage-objektumba elemezzük, és a rendszer átadja az objektumot egy IHttpMessageProcessor-példánynak.

### <a name="httpmessage"></a>HttpMessage
A `HttpMessage` példány három adatot tartalmaz:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

A `HttpMessage` példány egy GUID-azonosítót tartalmaz, `MessageId` amely lehetővé teszi a HTTP-kérések összekapcsolását a megfelelő HTTP-válaszhoz, valamint egy logikai értéket, amely meghatározza, hogy az objektum tartalmaz-e egy HttpRequestMessage és egy HttpResponseMessage-példányt. A-ből származó beépített HTTP-osztályok használatával Kihasználhatom `System.Net.Http` a `application/http` részét képező elemzési kódot `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
A rendszer `HttpMessage` ezután továbbítja a példányt a megvalósításához `IHttpMessageProcessor` , amely az Azure Event hub-beli esemény fogadásának és értelmezésének elválasztására, valamint a tényleges feldolgozására szolgáló felület.

## <a name="forwarding-the-http-message"></a>A HTTP-üzenet továbbítása
Ebben a példában úgy döntöttem, hogy érdekes lenne a HTTP-kérelem továbbítása a [MOESIF API Analytics szolgáltatáshoz](https://www.moesif.com). A Moesif egy felhőalapú szolgáltatás, amely a HTTP-elemzéshez és a hibakereséshez használható. Ingyenes szinttel rendelkeznek, így könnyen kipróbálható, és lehetővé teszi, hogy a HTTP-kérelmeket valós időben lássuk a API Management szolgáltatáson keresztül.

A `IHttpMessageProcessor` megvalósítás így néz ki:

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

A a `MoesifHttpMessageProcessor` [MOESIF C# API-kódtárat](https://www.moesif.com/docs/api?csharp#events) használ, amely megkönnyíti a http-események adatküldését a szolgáltatásba. Ahhoz, hogy HTTP-alapú adatküldést küldhessen a Moesif Collector API-nak, szüksége lesz egy fiókra és egy alkalmazás-azonosítóra. A Moesif-alkalmazás azonosítójának létrehozásához hozzon létre egy fiókot a [Moesif webhelyén](https://www.moesif.com) , és nyissa meg a _jobb felső menü_  ->  _alkalmazás-telepítőt_.

## <a name="complete-sample"></a>Teljes minta
A mintához tartozó [forráskód](https://github.com/dgilling/ApimEventProcessor) és tesztek a githubon találhatók. A minta futtatásához szüksége van egy [API Management szolgáltatásra](get-started-create-service-instance.md), [egy csatlakoztatott esemény-hubhoz](api-management-howto-log-event-hubs.md)és egy [Storage-fiókra](../storage/common/storage-account-create.md) .   

A minta csak egy egyszerű konzolos alkalmazás, amely az Event hub-ból érkező eseményeket figyeli, átalakítja őket egy Moesif `EventRequestModel` és `EventResponseModel` objektummá, majd továbbítja őket a MOESIF Collector API-hoz.

A következő animált ábrán megtekintheti a fejlesztői portálon egy API-ra irányuló kérést, amely megjeleníti az üzenet fogadását, feldolgozását és továbbítását, majd a kérést és választ, amely az esemény-adatfolyamban jelenik meg.

![A kérelem Runscope való továbbításának bemutatása](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás ideális helyet biztosít az API-khoz érkező és onnan érkező HTTP-forgalom rögzítéséhez. Az Azure Event Hubs egy rugalmasan méretezhető, alacsony díjszabású megoldás a forgalom rögzítésére és a másodlagos feldolgozási rendszerekbe való etetésére a naplózás, figyelés és más kifinomult elemzések céljából. A harmadik féltől származó forgalom figyelési rendszereihez (például a Moesif) való csatlakozás néhány tucat sornyi kód.

## <a name="next-steps"></a>Következő lépések
* További információ az Azure Event Hubs
  * [Ismerkedés az Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Üzenetek fogadása az EventProcessorHost szolgáltatással](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md)
* További információ a API Management és a Event Hubs integrációról
  * [Események naplózása az Azure Event Hubsba az Azure-ban API Management](api-management-howto-log-event-hubs.md)
  * [Naplózó entitás referenciája](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [eventhub házirend-hivatkozás](./api-management-advanced-policies.md#log-to-eventhub)
