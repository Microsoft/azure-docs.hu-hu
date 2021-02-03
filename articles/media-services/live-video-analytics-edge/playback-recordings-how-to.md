---
title: Felvételek lejátszása – Azure
description: Élő videó-elemzést használhat IoT Edge a folyamatos videofelvételek rögzítéséhez, amelyekkel hetek vagy hónapok szerint rögzíthet videókat a felhőbe. A rögzítést a fontos klipekre is korlátozhatja, az Event-alapú rögzítéssel. Ez a cikk a felvételek lejátszását mutatja be.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490895"
---
# <a name="playback-of-recordings"></a>Felvételek lejátszása 

## <a name="pre-read"></a>Előzetes olvasás  

* [Videó lejátszása](video-playback-concept.md)
* [Folyamatos videófelvétel](continuous-video-recording-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)

## <a name="background"></a>Háttér  

A [folyamatos videofelvételek](continuous-video-recording-concept.md) (CVR) esetében IoT Edge élő videó-elemzést is használhat, amelynek segítségével hetekig vagy hónapokig rögzíthet videót a felhőbe. Azt is megteheti, hogy a rögzítést olyan klipekre korlátozza, amelyek érdekesek, az [Event-based video Recording](event-based-video-recording-concept.md) (EVR) használatával. 

A Media Service-fiók egy Azure Storage-fiókhoz van csatolva, és amikor videót rögzít a felhőbe, a tartalmat egy [Media Service-eszközbe](terminology.md#asset)írja a rendszer. Media Services lehetővé teszi a [tartalom adatfolyamként való továbbítását](terminology.md#streaming)a rögzítés befejeződése után, vagy ha a rögzítés folyamatban van. Media Services biztosítja a szükséges képességeket a streamek HLS vagy MPEG-DASH protokollon keresztüli továbbításához az eszközök (ügyfelek) lejátszásához. További részletekért tekintse meg a [videó lejátszásáról](video-playback-concept.md) szóló cikket. A Media Service API-k segítségével előkészíti a szükséges folyamatos átviteli URL-címeket, amelyeket az ügyfelek a videó & hangjának lejátszására használnak. Az adatforráshoz tartozó streaming URL-cím létrehozásával kapcsolatban lásd: [streaming-lokátor létrehozása és URL-címek összeállítása](../latest/create-streaming-locator-build-url.md). Miután létrehozta az adatátviteli URL-címet egy eszközhöz, az URL-cím társítását a tartalomkezelő rendszerének videó forrásával (azaz a kamerával) kell tárolnia.

Ha például a HLS-on keresztül folyó adatfolyamot, a folyamatos átviteli URL-cím hasonlít `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Az MPEG-DASH esetében a következőképpen fog kinézni: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Ez egy jegyzékfájlt ad vissza, amely többek között leírja a továbbított adatfolyam teljes időtartamát, valamint azt, hogy az előre rögzített tartalmat vagy a folyamatban lévő "élő" hírcsatornát jelöli-e.

### <a name="live-vs-vod"></a>Élő és VoD  

Az olyan adatfolyam-protokollok, mint a HLS és az MPEG-DASH olyan forgatókönyvek kezelésére lettek létrehozva, mint az élő videók folyamatos átvitele, valamint az igény szerinti/előre rögzített tartalmak, például a TÉVÉMŰSORok és a filmek folyamatos továbbítása. Az élő videók, a HLS és az MPEG-DASH ügyfelek a "legutóbbi" időponttól kezdve kezdhetik a játékot. A filmek esetében azonban a nézők elvárják, hogy elindulnak az elejétől, és megkezdődhetnek, ha úgy döntenek, hogy. A HLS és az MPEG-DASH jegyzékfájlok olyan jelzőket mutatnak be, amelyek jelzik az ügyfeleknek, hogy a videó élő streamet képvisel-e, vagy előre rögzített tartalom-e.
Ez a fogalom a HLS és az MPEG-DASH streamek esetében is érvényes, amelyek az élő videó-elemzéssel rögzített videókat tartalmaznak IoT Edgeon.

## <a name="browsing-and-selective-playback-of-recordings"></a>A felvételek tallózása és szelektív lejátszása  

Tekintse át azt a forgatókönyvet, ahol a IoT Edge élő videó-elemzést használt, hogy a videó csak 08:00 és 10:00 közötti napokon legyen felhasználva, amikor egy iskola nyitva volt a teljes tanév során. Vagy lehet, hogy a videót csak 07:00 és 19:00 között készíti el országos ünnepnapokon. A videó rögzítésének megkeresése és megtekintése során a következő két forgatókönyvben a következőkre lesz szüksége:

* Egy olyan módszer, amellyel meghatározhatja, hogy milyen dátum/óra videó szerepel a rögzítésben.
* Egy adott rész kiválasztásának módja (például az új év napján 09:00 és 11:00 között) az adott felvétel a lejátszáshoz.

A Media Services egy lekérdezési API-t (availableMedia) biztosít az első probléma megoldásához, valamint az időtartományos szűrők (kezdő időpont, a Befejezés időpontja) számára a második megoldáshoz.

## <a name="query-api"></a>Lekérdezési API 

A CVR használatakor a lejátszási eszközök (ügyfelek) nem igényelhetnek olyan jegyzékfájlt, amely a teljes felvétel lejátszását fedi le.  A többéves felvétel olyan jegyzékfájlt hoz létre, amely túl nagy volt a lejátszáshoz, és nem lenne hasznos, ha az ügyfél oldalán felhasználható részekre kellene elemezni.  Az ügyfélnek tudnia kell, hogy milyen datetime-tartományokban vannak a rögzítésben lévő adatmennyiségek, így az érvényes kérelmeket nem sokkal kitalált módon hajthatja végre. Itt jön az új lekérdezési API – az ügyfelek mostantól használhatják ezt a kiszolgálóoldali API-t a tartalmak felderítésére.

Ahol a pontosság értéke a következők egyike lehet: év, hónap, nap vagy teljes (az alább látható módon). 

|Pontosság|év|hónap|nap|teljes|
|---|---|---|---|---|
|Lekérdezés|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Reagálás|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Teljes hűséggel kapcsolatos válasz. Ha egyáltalán nem voltak hézagok, a kezdés kezdő időpont lesz, és a Befejezés a Befejezés lenne.|
|Korlátozások|&#x2022;kezdő időpont <= befejezési időpont<br/>&#x2022;mindkettőnek éééé formátumúnak kell lennie, ellenkező esetben hiba történt.<br/>&#x2022;érték tetszőleges számú lehet.<br/>&#x2022;értékek a következők: inclusive.|&#x2022;kezdő időpont <= befejezési időpont<br/>&#x2022;mindkettőnek éééé-hh formátumúnak kell lennie, ellenkező esetben hibaüzenetet ad vissza.<br/>&#x2022;érték legfeljebb 12 hónap lehet egymástól.<br/>&#x2022;értékek a következők: inclusive.|&#x2022;kezdő időpont <= befejezési időpont<br/>&#x2022;mindkettőnek éééé-hh-nn formátumúnak kell lennie, ellenkező esetben hibaüzenetet kell visszaadnia.<br/>&#x2022;érték legfeljebb 31 napja lehet egymástól.<br/>Az értékek a következők: inclusive.|&#x2022;kezdő időpont < befejezési időközben<br/>&#x2022;érték legfeljebb 25 óra lehet.<br/>&#x2022;értékek a következők: inclusive.|

#### <a name="additional-request-format-considerations"></a>A kérelem formátumának további szempontjai

* Minden alkalommal UTC
* A pontosság lekérdezési karakterlánc paraméterének megadása kötelező.  
* Az időpontok és a befejezési lekérdezési karakterlánc paraméterei szükségesek a hónap, nap és teljes pontosság értékéhez.  
* Az időpontok és a befejezési lekérdezési karakterlánc paraméterei nem kötelezőek az év pontossági értékeként (nincs, sem vagy mindkettő támogatott).  

    * Ha a kezdési időpont ki van hagyva, a rendszer feltételezi, hogy az első év a rögzítésben.
    * Ha a rendszer kihagyja a befejezési időpontot, azt feltételezzük, hogy az utolsó év a rögzítésben.
    * Ha például a rögzítés 2011-ben kezdődött, és 2020-ig folytatódott, akkor:

        * /availableMedia? Precision = év ugyanaz, mint a/availableMedia? precíziós = év&kezdő időpont = 2011&befejezési = 2020
        * /availableMedia? precíziós = év&kezdő időpont = 2015 ugyanaz, mint a/availableMedia? precíziós = év&kezdő időpont = 2015&Befejezés = 2020
        * /availableMedia? Precision = év&befejezési időpont = 2018 ugyanaz, mint a/availableMedia? precíziós = év&kezdő időpont = 2011&befejezési időpont = 2018

Ha az időtartományokhoz nem érhető el adathordozó-érték, a rendszer üres listát ad vissza.

Ha az eszköz nem tartalmaz egy adathordozó-gráfból származó felvételt, akkor a rendszer egy HTTP 400-választ ad vissza, amely arról tájékoztatja, hogy ez a funkció csak a Media Graph használatával rögzített tartalomhoz érhető el.

Ha a paraméterekben megadott idő nagyobb, mint az adott lekérdezés típusának maximális időtartománya, akkor a rendszer egy HTTP 400 hibaüzenetet küld vissza, amely a kért lekérdezési típus maximálisan engedélyezett időtartományát ismerteti.

Feltételezve, hogy az időtartomány érvényes a megadott paramétereknél, a rögzítésben lévő adatok időablakán kívüli lekérdezések esetében nem kerül sor hibákra.  Ami azt jelenti, hogy a rögzítés 7 órával ezelőtt kezdődött, de az ügyfél a (UtcNow) {UtcNow – 24 óra} verzióról kéri le a rendelkezésre álló adathordozókat, ezért csak a {UtcNow – 7} órányi adatmennyiséget adja vissza.

### <a name="response-format"></a>Válasz formátuma  

A availableMedia hívása időértékek halmazát adja vissza.

A válasz egy JSON-törzs lesz, a timeRanges értékek pedig a kért pontosságtól függően ISO 8601 UTC dátumok (éééé formátumban), hónap (éééé-hh formátumban) vagy Day (éééé-hh-nn) tömbje.  A teljes timeRanges a kezdő és a záró értéket is tartalmazza ISO 8601 UTC dátum (éééé-hh-NNTóó: PP: SS. sssZ Format) formátumban.

A availableMedia-lekérdezés esetében az API kikapcsolja a videó idővonalát. A válaszban az idősoron megjelenő folytonossági különbségek jelennek meg.

#### <a name="response-example-for-availablemedia"></a>Válasz példa a availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>1. példa: hézag nélküli élő rögzítés

Íme egy válasz, amely az összes rendelkezésre álló adathordozót mutatja 2019 december 21-én, ahol a rögzítés 100%-ban fejeződött be. A válasz csak egy kezdő/záró párral rendelkezik.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>2. példa: élő felvétel 2 másodperces hézagtal

Tegyük fel, hogy a kamera valamilyen okból nem tudott érvényes videót küldeni egy nap 2 másodperces intervallumára. Íme egy válasz, amely az összes rendelkezésre álló adathordozót mutatja 2019 december 21-én:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>3. példa: élő felvétel 8 órás hézagtal

Tegyük fel, hogy a kamera és/vagy a helyszíni létesítmény elvesztette a teljesítményt egy 8 órás időszakra a nap folyamán, 4 órától UTC és Dél között, és nem volt biztonsági mentési áramforrás. Itt látható egy válasz, amely az adott nap összes elérhető adathordozóját megjeleníti

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>4. példa: élő rögzítés, amely nem rögzíti a videót a nyári szünet során

Tegyük fel, hogy csak akkor rögzített videót, ha az iskola munkamenetben volt, és a rögzítést június 17-én, szeptember 6-ától leállították. A rendelkezésre álló hónapok lekérdezése a következőképpen néz ki:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Ha ezt követően megkérdezi a rendelkezésre álló napokat júniusban, a következőt fogja látni:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>5. példa: élő rögzítés, amely nem rögzíti a videót hétvégén vagy ünnepnapon

Tegyük fel, hogy a videó csak a munkaidőn belül van rögzítve. A rendelkezésre álló napok lekérdezése a következőképpen néz ki:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Időtartomány-szűrők

A fentiekben leírtaknak megfelelően ezek a szűrők segítenek kijelölni a rögzítés egyes részeit (például: 9 és 11:00 között, az új év napján) a lejátszáshoz. A streaming URL-cím a HLS-on keresztül fog kinézni `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . A rögzítés egy részének kiválasztásához adjon hozzá egy kezdő időpontot és egy befejezési paramétert, például: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Így az időtartomány-szűrők olyan URL-módosítók, amelyek a rögzítési jegyzékben szereplő idővonalának leírására szolgálnak:

* `starttime` egy ISO 8601 DateTime bélyegző, amely leírja a visszaadott jegyzékfájlban a videó idővonalának kívánt kezdési időpontját.
* `endtime` egy ISO 8601 DateTime bélyegző, amely leírja a jegyzékfájlban visszaadott videó idővonalának kívánt befejezési időpontját.

Az ilyen jegyzékfájlok maximális hossza (időben) nem lehet hosszabb 24 óra.

A szűrők megkötései.

|startTime| endTime |Eredmény|
|---|---|---|
|Hiányzik |Hiányzik |A videó utolsó részét adja vissza az objektumban, legfeljebb 4 órával.<br/><br/>Ha az eszköz nem lett beírva (nem érkezik új videó-adatforgalom), a rendszer egy igény szerinti (VoD) jegyzékfájlt ad vissza. Más esetben a rendszer élő jegyzékfájlt ad vissza.|
|Jelen|Hiányzik|    Olyan jegyzékfájlt ad vissza, amely bármilyen videóval elérhető, amely újabb, mint a kezdő időpont, ha egy ilyen jegyzékfájl rövidebb, mint 24 óra.<br/>Ha a jegyzékfájl hossza meghaladja a 24 órát, hibaüzenetet ad vissza.<br/>Ha az eszköz nem lett beírva (nem érkezik új videó-adatforgalom), a rendszer egy igény szerinti (VoD) jegyzékfájlt ad vissza. Más esetben a rendszer élő jegyzékfájlt ad vissza.
|Hiányzik|Jelen |Hiba – ha a kezdő időpont szerepel, a Befejezés kötelező.|
|Jelen|Jelen|Egy VoD-jegyzékfájl visszaadása bármilyen videóval a kezdő és a záró időpont között.<br/>A span (kezdő időpont, Befejezés) nem haladhatja meg a 24 órát.|

### <a name="response-examples"></a>Példák a válaszokra  

#### <a name="example-1-live-recording-with-no-gaps"></a>1. példa: hézag nélküli élő rögzítés

Íme egy válasz, amely az összes rendelkezésre álló adathordozót mutatja 2019 december 21-én, ahol a rögzítés 100%-ban fejeződött be. A válasz csak egy kezdő/záró párral rendelkezik.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Ha a rögzítés folyamatosan történik, akkor a kezdő/záró párokban bármikor kérhet HLS-vagy DASH-jegyzékfájlokat, feltéve, hogy a span 24 óra vagy kevesebb. A fenti 4 órás HLS-jegyzékre vonatkozó kérelem például a következő lehet:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>2. példa: élő felvétel 2 másodperces hézagtal

Tegyük fel, hogy a kamera valamilyen okból nem tudott érvényes videót küldeni egy nap 2 másodperces intervallumára. Itt látható egy válasz, amely a 2019-es december 21-én elérhető adathordozót mutatja:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Ha például a fentieket rögzíti, a HLS és a DASH-jegyzékeket bármely kezdő/záró párral kérheti, feltéve, hogy a span 24 óra alatt volt. Ha ezek az értékek a (z) 04:01:08AM UTC értéknél átnyúltak a hézaghoz, akkor a visszaadott jegyzékfájl jelzi az adathordozó idővonalának folytonosságát, az ezekre a protokollokra vonatkozó specifikációk alapján.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>3. példa: élő felvétel 8 órás hézagtal

Tegyük fel, hogy a kamera és/vagy a helyszíni létesítmény elvesztette a teljesítményt egy 8 órás időszakra a nap folyamán, 4 órától UTC és Dél között, és nem volt biztonsági mentési áramforrás. Itt látható egy válasz, amely az adott nap összes elérhető adathordozóját megjeleníti.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Ilyen rögzítéssel:

* Ha olyan jegyzékfájlt kér, amelyben mind az időpontok, mind a befejezési idő tartománya az idősor "elérhető" részén található, azaz éjféltől 04:00-ig, vagy délben éjfélig, akkor a szolgáltatás egy olyan jegyzékfájlt ad vissza, amely a kezdő időpont és a Befejezés időpontjáig lefedi az időt, például:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Ha olyan jegyzékfájlt kér, amelyben a kezdő időpont és a befejezési idő a középső részen található "Hole" értéken belül van – mondjuk 08:00 és 10:00 között, akkor a szolgáltatás ugyanúgy viselkedik, mintha egy adott eszköz szűrője üres eredményt eredményezne.

    [Ez egy olyan kérelem, amely üres választ kap] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Ha olyan jegyzékfájlt kér, amelyben csak az egyik kezdő időpont vagy a befejezési időpont van, akkor a visszaadott jegyzékfájl csak a TimeSpan egy részét fogja tartalmazni. A kezdő időpont vagy a végső Befejezés értékét a legközelebbi érvényes határhoz igazítja. Ha például a 3 órás adatfolyamot 10 – 01:00-re kérték, a válasz 1-HR értékű adathordozót tartalmaz 12 délig és 01:00 között.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    A visszaadott jegyzékfájl a 2019-12-21T12:00:00.000 Z időpontban indul el, még akkor is, ha a kérést a rendszer 10:00-kor megkezdésére kéri. A videó-kezelő rendszernek a Player beépülő modullal való létrehozásakor ügyelni kell arra, hogy jelezze ezt a megjelenítőnek. Egy nem megfelelő megjelenítő nem tévesztendő össze azzal, hogy miért a lejátszási ütemterv délben, és nem a kért 10 órakor kezdődik

## <a name="recording-and-playback-latencies"></a>Rögzítés és lejátszás késése

Ha IoT Edge élő videó-elemzést használ egy eszközre való rögzítéshez, meg kell adnia egy segmentLength-tulajdonságot, amely megadja, hogy a modul a felhőbe való rögzítés előtt minimális időtartamú videót (másodpercben) összesítse. Ha például a segmentLength 300 értékre van állítva, akkor a modul 5 perces videót fog gyűjteni, mielőtt feltölti az 1 5-es percet a "darab" értékre, majd a következő 5 percben a felhalmozási módba lép, majd feltölti újra. A segmentLength növelésének előnye az Azure Storage-tranzakciók költségeinek csökkentése, mivel az olvasási és írási műveletek száma nem lesz gyakoribb, mint minden segmentLength másodpercben.

Ennek következtében a videó Media Servicesról való folyamatos átvitele legalább ennyi idő alatt késleltetve lesz. 

Egy másik tényező, amely meghatározza a lejátszási késleltetést (a kamera előtti esemény időpontját, a lejátszási eszközön megtekinthető idő közötti késleltetést) a csoport-a-Pictures [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) időtartama. Az [élő streamek 3 egyszerű technikával való késleltetésének csökkentése](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) többek között azt mutatja be, hogy hosszabb a GOP időtartama, és hosszabb a késés. Gyakori, hogy az IP-kamerákat olyan megfigyelési és biztonsági forgatókönyvekben használják, amelyek 30 másodpercnél hosszabb Pallagi Péter használatára vannak konfigurálva. Ez nagy hatással van a teljes késésre.

## <a name="next-steps"></a>Következő lépések

[Folyamatos videofelvételi oktatóanyag](continuous-video-recording-tutorial.md)
