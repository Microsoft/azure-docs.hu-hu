---
title: Hirdetések beszúrása az ügyféloldali oldalon | Microsoft Docs
description: Ez a cikk bemutatja, hogyan szúrhat be hirdetéseket az adathordozóra az ügyféloldali oldalon.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014959"
---
# <a name="inserting-ads-on-the-client-side"></a>Hirdetések beszúrása az ügyféloldali oldalon

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk azt ismerteti, hogyan szúrhat be különféle típusú hirdetéseket az ügyfél oldalán.

A kódolt feliratozással és az ad-támogatással kapcsolatos további információkért lásd: [támogatott kódolt feliratok és ad-beszúrási szabványok](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> A Azure Media Player jelenleg nem támogatja a hirdetéseket.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Hirdetések beszúrása az adathordozóra
Azure Media Services támogatja az ad-beszúrást a Windows Media platformon keresztül: Player frameworks. A Windows 8, a Silverlight, a Windows Phone-telefon 8 és az iOS rendszerű eszközök esetében az ad-támogatással rendelkező Player-keretrendszerek érhetők el. Minden egyes Player Framework tartalmaz egy mintakód, amely bemutatja, hogyan implementálhat egy Player-alkalmazást. Háromféle különböző típusú hirdetés helyezhető be a médiába: List.

* **Lineáris** – teljes keretű hirdetések, amelyek szüneteltetik a fő videót.
* Nem **lineáris** – a fő videóként megjelenő átfedésben lévő hirdetéseket általában egy embléma vagy más, a lejátszón elhelyezett statikus rendszerkép játssza le.
* **Companion** – a lejátszón kívül megjelenített hirdetések.

A hirdetéseket a fő videó idővonalának tetszőleges pontjára lehet helyezni. Meg kell adnia a játékosnak, hogy mikor kell lejátszani az ad-t és a lejátszani kívánt hirdetéseket. Ezt szabványos XML-alapú fájlok használatával végezheti el: a video ad szolgáltatás sablonja (nagy méretű), a digitális videó több ad-lista (VMAP), a média absztrakt előkészítési sablon (MAST) és a digitális videolejátszó ad Interface Definition (VPAID). A nagy fájlok meghatározzák, hogy milyen hirdetéseket kell megjeleníteni. A VMAP-fájlok a különböző hirdetések lejátszásának idejét határozzák meg, és nagy XML-kódot tartalmaznak. Az ÁRBOC-fájlok egy másik módja a nagy XML-t is tartalmazó hirdetések sorozatának. A VPAID-fájlok a videolejátszó és az ad-kiszolgáló közötti felületet határozzák meg.

Minden egyes játékos-keretrendszer másképp működik, és mindegyiket a saját cikke fogja fedezni. Ez a cikk a hirdetések beszúrásához használt alapvető mechanizmusokat ismerteti. A videolejátszó alkalmazásai hirdetéseket kérnek az ad-kiszolgálótól. Az ad-kiszolgáló számos módon képes válaszolni:

* HATALMAS fájl visszaadása
* VMAP-fájl visszaadása (nagy mennyiségű beágyazott)
* MAST-fájl visszaadása (hatalmas beágyazott)
* VPAID-hirdetéseket tartalmazó hatalmas fájl visszaadása

### <a name="using-a-video-ad-service-template-vast-file"></a>Videó ad-szolgáltatás sablonjának (hatalmas) fájljának használata
Egy hatalmas fájl határozza meg, hogy mely ad vagy hirdetéseket szeretné megjeleníteni. Az alábbi XML-példa egy lineáris ad-hez készült, nagy mennyiségű fájlt mutat be:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

A lineáris ad-t a <**lineáris**> elem írja le. Meghatározza az ad időtartamát, a követési eseményeket, a kattintást, a nyomon követést és számos **MediaFile** elemet. A nyomkövetési események a <**TrackingEvents**> elemben vannak megadva, és lehetővé teszik, hogy az ad-kiszolgáló nyomon követhesse az ad megtekintésekor előforduló különböző eseményeket. Ebben az esetben a Start, a középpont, a Complete és a Expand esemény nyomon követhető. A Start esemény a hirdetés megjelenésekor következik be. A középponti esemény akkor következik be, amikor az ad idővonalának legalább 50%-át megtekintették. A teljes esemény akkor következik be, amikor az ad a végére futott. A kibontási esemény akkor következik be, amikor a felhasználó kibontja a videolejátszó teljes képernyős megjelenítését. Az átkattintások egy <**Átkattintási**> elemmel vannak megadva egy <**VideoClicks**> elemen belül, és egy olyan erőforrás URI azonosítóját adja meg, amelyet a felhasználó az ad-ra való kattintáskor megjelenít. A ClickTracking egy <**ClickTracking**> elemben van megadva, a <**VideoClicks**> elemen belül is, és azt a követési erőforrást adja meg, amelyet a lejátszónak a felhasználó az ad-ra való kattintáskor kell kérnie. Az <**MediaFile**> elemek határozzák meg az ad adott kódolásával kapcsolatos információkat. Ha egynél több <**MediaFile**> elemet használ, a videolejátszó kiválaszthatja a platform legjobb kódolását.

A lineáris hirdetések a megadott sorrendben jeleníthetők meg. Ehhez adjon hozzá további `<Ad>` elemeket a hatalmas fájlhoz, és adja meg a sorrendet a sorrend attribútum használatával. A következő példa ezt illusztrálja:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

A nem lineáris hirdetések egy `<Creative>` elemben is meg vannak adva. Az alábbi példa egy nem `<Creative>` lineáris ad-t leíró elemet mutat be.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

A <**NonLinearAds**> elem egy vagy több <nem **lineáris**> elemet tartalmazhat, amelyek mindegyike egy nem lineáris ad-t tud leírni. A <**nemlineáris**> elem a nemlineáris ad erőforrását adja meg. Az erőforrás lehet egy <**StaticResource**>, egy <**IFrameResource**> vagy egy <**HTMLResource**>. \<**StaticResource**> a nem HTML típusú erőforrásokat ismerteti, és definiál egy creativeType attribútumot, amely megadja az erőforrás megjelenését:

Rendszerkép/GIF, rendszerkép/JPEG, rendszerkép/png – az erőforrás egy HTML <**img**> címkében jelenik meg.

Application/x-JavaScript – az erőforrás HTML-<**parancsfájlban**> címkével jelenik meg.

Application/x-shockwave-flash – az erőforrás egy flash-lejátszóban jelenik meg.

A **IFrameResource** leírja az IFRAME-ben megjeleníthető HTML-erőforrásokat. A **HTMLResource** egy HTML-kódot ír le, amely beszúrható egy weblapra. A **TrackingEvents** megadják a követési eseményeket és az esemény bekövetkezésekor KÉRELMEZett URI-t. Ebben a példában a acceptInvitation és az összecsukási eseményeket nyomon követjük. A **NonLinearAds** elemmel és annak gyermekeivel kapcsolatos további információkért lásd: IAB.net/VAST. Vegye figyelembe, hogy a **TrackingEvents** elem nem **lineáris** elem helyett a **NonLinearAds** elemen belül található.

A Companion-hirdetések egy elemen belül vannak definiálva `<CompanionAds>` . Az `<CompanionAds>` elem egy vagy több elemet is tartalmazhat `<Companion>` . Minden `<Companion>` elem egy társ-ad-t ír le, és tartalmazhat egy `<StaticResource>` , vagy egy, `<IFrameResource>` `<HTMLResource>` a nem lineáris ad-ben megegyező módon megadott, vagy a-t. Egy hatalmas fájl több kiegészítő hirdetést is tartalmazhat, és a Player alkalmazás kiválaszthatja a legmegfelelőbb ad-t. További információ a hatalmas használatáról: [hatalmas 3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Több ad Playlist-(VMAP-) fájl használata digitális videó használatával
Egy VMAP-fájl segítségével megadhatja, hogy mikor történjenek az ad-töréspontok, mennyi ideig lehet megjelenni az egyes megszakítások, és milyen típusú hirdetések jelenhetnek meg a megszakítás során. Az alábbi példa egy VMAP-fájlt, amely egyetlen ad-töréspontot definiál:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

A VMAP-fájlok egy `<VMAP>` vagy több elemet tartalmazó elemmel kezdődnek `<AdBreak>` , melyek mindegyike ad-töréspontot definiál. Mindegyik ad-töréspont megadja a Töréspont típusát, a Töréspont AZONOSÍTÓját és az idő eltolását. A breakType attribútum azt a típust adja meg, amely a megszakítás során játszható le: lineáris, nemlineáris vagy Display. Jelenítse meg a hirdetéseket a széles körű hirdetéseket bemutató térképekhez. Több ad-típust is megadhat egy vesszővel (szóközök nélkül) elválasztott listában. A breakID az ad nem kötelező azonosítója. A timeOffset határozza meg, hogy mikor jelenjen meg a hirdetés. A következő módszerek egyikével adható meg:

1. Idő – óó: PP: SS vagy óó: PP: SS. mmm formátumban, ahol. mmm ezredmásodperc. Ennek az attribútumnak az értéke határozza meg a videó idővonalának kezdetétől az ad-töréspont elejétől számított időt.
2. Százalék – n% formátumban, ahol n az ad-lejátszás előtt lejátszandó videó idővonalának százalékos értéke
3. Kezdés/Befejezés – azt adja meg, hogy a rendszer megjelenjen-e a videó előtt vagy után.
4. Pozíció – megadja az ad-töréspontok sorrendjét, ha az ad-töréspontok időzítése ismeretlen, például élő közvetítés esetén. Az egyes ad-töréspontok sorrendje a #n formátumban van megadva, ahol n egész szám 1 vagy nagyobb. 1 azt jelzi, hogy az ad-t az első lehetőségnél kell lejátszani, a 2 azt jelzi, hogy az ad-t a második lehetőségnél kell lejátszani, és így tovább.

A `<AdBreak>` elemen belül egy <**AdSource**> elem lehet. A <**AdSource**> elem a következő attribútumokat tartalmazza:

1. Azonosító – az ad-forrás azonosítóját adja meg.
2. allowMultipleAds – logikai érték, amely meghatározza, hogy az ad-töréspontban több hirdetés is megjeleníthető-e
3. followRedirects – egy nem kötelező logikai érték, amely megadja, hogy a videó játékosának tiszteletben kell-e hagynia egy ad-válaszon belüli átirányítást

A <**AdSource**> elem egy beágyazott ad-választ vagy egy ad-válaszra mutató hivatkozást biztosít a lejátszónak. A következő elemek egyikét tartalmazhatja:

* `<VASTAdData>` azt jelzi, hogy egy hatalmas ad-válasz van beágyazva a VMAP-fájlba
* `<AdTagURI>` egy másik rendszer ad-válaszára hivatkozó URI
* `<CustomAdData>` – tetszőleges karakterlánc, amely nem nagy választ jelöl

Ebben a példában egy beágyazott ad-válasz van megadva egy olyan elemmel, `<VASTAdData>` amely egy hatalmas ad-választ tartalmaz. További információt a többi elemről a következő témakörben talál: [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

A <**AdBreak**> elem egy <**TrackingEvents**> elemet is tartalmazhat. A <**TrackingEvents**> elem lehetővé teszi egy ad-töréspont indításának vagy befejezésének nyomon követését, illetve azt, hogy hiba történt-e az ad-szünet során. A <**TrackingEvents**> elem egy vagy több <**követési**> elemet tartalmaz, amelyek mindegyike nyomon követési eseményt és nyomkövetési URI-t határoz meg. A lehetséges követési események a következők:

1. breakStart – egy ad-töréspont kezdetének nyomon követése
2. breakEnd – ad-töréspont befejezésének nyomon követése
3. hiba – az ad-töréspont során felmerülő hiba nyomon követése

A következő példa egy VMAP-fájlt mutat be, amely meghatározza a követési eseményeket.

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

További információ a <**TrackingEvents**> elemről és annak gyermekeiről: http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Média absztrakt előkészítési sablon (MAST) fájl használata
A MAST-fájlok lehetővé teszik olyan eseményindítók megadását, amelyek meghatározzák, hogy mikor jelenjen meg egy hirdetés. A következő példában egy olyan MAST-fájl látható, amely egy előre elküldhető ad-hoz, egy közép-és egy, a post-roll ad-hoz tartozó eseményindítókat tartalmaz.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Egy MAST-fájl egy olyan **Mast** elemmel kezdődik, amely egy **eseményindító** elemet tartalmaz. Az `<triggers>` elem egy vagy több **trigger** elemet tartalmaz, amelyek meghatározzák, hogy mikor kell lejátszani egy adott hirdetést.

Az **trigger** elem egy **startConditions** elemet tartalmaz, amely megadja, hogy mikor kell elkezdeni a lejátszást. A **startConditions** elem egy vagy több `<condition>` elemet tartalmaz. Ha minden `<condition>` kiértékelés igaz értéket ad meg, akkor a `<condition>` rendszer a **startConditions** vagy **endConditions** elemben található, attól függően kezdeményezi vagy visszavonja az aktiválást. Ha több `<condition>` elem van jelen, a rendszer implicit módon kezeli őket, vagy ha az igaz értékre van kiértékelve, a trigger kezdeményezhető lesz. `<condition>` az elemek egymásba ágyazhatók. Ha `<condition>` a gyermek elemek előre vannak beállítva, a rendszer implicitként kezeli őket, és minden feltételnek igaz értéket kell kiértékelnie, hogy az trigger kezdeményezzen. Az `<condition>` elem a következő attribútumokat tartalmazza, amelyek meghatározzák a feltételt:

1. **Type (típus** ) – a feltétel, az esemény vagy a tulajdonság típusát adja meg.
2. **név** – a kiértékelés során használandó tulajdonság vagy esemény neve
3. **Value (érték** ) – az a tulajdonság, amelyet a rendszer a tulajdonságok alapján kiértékel
4. **operátor** – a kiértékelés során használandó művelet: EQ (EQUAL), NEQ (nem egyenlő), GTR (nagyobb), geq (nagyobb vagy egyenlő), lt (kevesebb, mint), LEQ (kisebb vagy egyenlő), mod (többrésztvevős)

a **endConditions** elemek is tartalmaznak `<condition>` . Ha egy feltétel igaz értéket ad vissza, az trigger alaphelyzetbe áll. Az `<trigger>` elem olyan elemet is tartalmaz `<sources>` , amely egy vagy több `<source>` elemet tartalmaz. Az `<source>` elemek határozzák meg az ad-válaszhoz tartozó URI-t és az ad-válasz típusát. Ebben a példában egy URI-t kap egy hatalmas válasz.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>A video Player-Ad Interface Definition (VPAID) használata
A VPAID egy olyan API, amely lehetővé teszi, hogy a futtatható ad-egységek egy videolejátszó használatával kommunikáljanak. Ez lehetővé teszi az igen interaktív ad-élményt. A felhasználó együttműködhet az ad-vel, és az ad reagálhat a megjelenítőben végrehajtott műveletekre. Előfordulhat például, hogy az ad olyan gombokat jelenít meg, amelyek lehetővé teszik, hogy a felhasználó további információkat vagy az ad egy hosszabb verzióját megtekinthesse. A videolejátszónak támogatnia kell a VPAID API-t, és a végrehajtható ad-nek meg kell valósítania az API-t. Amikor egy játékos egy ad-kiszolgálótól kér egy ad-kiszolgálót, a kiszolgáló egy olyan nagy választal válaszolhat, amely tartalmaz egy VPAID ad-t.

A kód olyan végrehajtható ad-t hoz létre, amelyet egy olyan futtatókörnyezeti környezetben kell végrehajtani, mint amilyen például az Adobe Flash™ vagy a JavaScript, amely egy böngészőben végrehajtható. Ha egy ad-kiszolgáló egy VPAID ad-t tartalmazó hatalmas választ ad vissza, az elem apiFramework attribútumának `<MediaFile>` "VPAID" értékűnek kell lennie. Ez az attribútum azt adja meg, hogy a foglalt ad egy VPAID végrehajtható ad. A Type attribútumot a végrehajtható fájl MIME-típusára kell beállítani, például "application/x-shockwave-flash" vagy "application/x-JavaScript". A következő XML-kódrészlet `<MediaFile>` egy VPAID végrehajtható ad-t tartalmazó hatalmas válasz elemét jeleníti meg.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Egy végrehajtható hirdetés inicializálható a `<AdParameters>` vagy elemen belüli elem használatával `<Linear>` `<NonLinear>` , amely egy hatalmas válasz. A elemmel kapcsolatos további információkért `<AdParameters>` lásd: [hatalmas 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). További információ a VPAID API-ról: [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Windows vagy Windows Phone-telefon 8 rendszerű játékos implementálása ad-támogatással
A Microsoft Media platform: a Windows 8 és Windows Phone-telefon 8 rendszerhez készült Player Framework egy minta-alkalmazásokból álló gyűjteményt tartalmaz, amely bemutatja, hogyan implementálhat egy videolejátszó-alkalmazást a keretrendszer használatával. A Player Framework és a Samples a [Windows 8 és Windows Phone-telefon 8 rendszerhez készült Player Framework](https://playerframework.codeplex.com)használatával tölthető le.

A Microsoft. PlayerFramework. XAML. Samples megoldás megnyitásakor a projekten belül számos mappa jelenik meg. A hirdetési mappa tartalmazza az ad-támogatással rendelkező videolejátszó létrehozásához szükséges mintakód-kódot. A hirdetési mappában számos XAML/cs fájl szerepel, amelyek bemutatják, hogyan szúrhat be hirdetéseket más módon. Az alábbi lista a következőket ismerteti:

* Az AdPodPage. XAML megjeleníti az ad Pod megjelenítésének módját.
* A AdSchedulingPage. XAML bemutatja, hogyan ütemezhet hirdetéseket.
* A FreeWheelPage. XAML bemutatja, hogyan ütemezhet hirdetéseket a Fogaskoszorú beépülő modullal.
* A MastPage. XAML bemutatja, hogyan ütemezhet be hirdetéseket egy MAST-fájllal.
* A ProgrammaticAdPage. XAML azt mutatja be, hogyan lehet programozott módon ütemezni a hirdetéseket egy videóban.
* A ScheduleClipPage. XAML azt mutatja be, hogyan ütemezhet egy ad-t hatalmas fájl nélkül.
* A VastLinearCompanionPage. XAML bemutatja, hogyan szúrhat be egy lineáris és egy kiegészítő ad-t.
* A VastNonLinearPage. XAML azt mutatja be, hogyan szúrhat be egy nem lineáris ad-t.
* A VmapPage. XAML bemutatja, hogyan adhatja meg a hirdetéseket egy VMAP-fájllal.

Mindegyik minta a Player Framework által definiált Media Player osztályt használja. A legtöbb minta olyan beépülő modult használ, amely támogatja a különböző ad-válaszok formátumait. A ProgrammaticAdPage minta programozott módon kommunikál egy Media Player-példánnyal.

### <a name="adpodpage-sample"></a>AdPodPage minta
Ez a példa a AdSchedulerPlugin használatával határozza meg, hogy mikor jelenjen meg egy ad. Ebben a példában egy Mid-roll hirdetményt ütemezünk öt másodperc elteltével. Az ad Pod (a sorrendben megjelenítendő hirdetések csoportja) az ad-kiszolgáló által visszaadott hatalmas fájlban van megadva. A hatalmas fájl URI-ja a `<RemoteAdSource>` elemben van megadva.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

A AdSchedulerPlugin kapcsolatos további információkért lásd: [a Windows 8 és Windows Phone-telefon 8 rendszerű lejátszó-keretrendszer reklámozási](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation) útmutatója

### <a name="adschedulingpage"></a>AdSchedulingPage
Ez a minta a AdSchedulerPlugin is használja. Három hirdetést, egy előre roll ad-t, egy Mid-roll ad-t és egy post-roll ad-t ütemezhet. Az egyes ad-elemek nagy részét képező URI-azonosító egy `<RemoteAdSource>` elemben van megadva.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Ez a példa azt a FreeWheelPlugin használja, amely a forrás attribútumot határozza meg, amely egy SmartXML-fájlra mutató URI-t határoz meg, amely az ad-tartalmat és az ad-ütemezési információkat is megadja.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Ez a példa a MastSchedulerPlugin használja, amely lehetővé teszi egy MAST-fájl használatát. A forrás attribútum meghatározza az ÁRBOC fájljának helyét.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ez a minta programozott módon kommunikál a Media Player. A ProgrammaticAdPage. XAML fájl a Media Player-t hozza létre:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

A ProgrammaticAdPage.xaml.cs-fájl létrehoz egy AdHandlerPlugin, hozzáadja a TimelineMarker, amely meghatározza, hogy mikor jelenjen meg egy ad, majd hozzáadja a kezelőt a MarkerReached eseményhez, amely betölti a RemoteAdSource egy URI-t egy hatalmas fájlba, majd lejátssza az ad-t.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Ez a példa a AdSchedulerPlugin használatával ütemezhet egy Mid-roll ad-t az ad-t tartalmazó. wmv fájl megadásával.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Ez a példa azt szemlélteti, hogyan használható a AdSchedulerPlugin egy Mid-roll lineáris ad a Companion ad-vel való ütemezésekor. Az `<RemoteAdSource>` elem megadja a hatalmas fájl helyét.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Ez a példa a AdSchedulerPlugin használatával lineáris és nem lineáris ad-t ütemezhet. A hatalmas fájl helye van megadva a `<RemoteAdSource>` elemmel.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
Ez a példa a VmapSchedulerPlugin használja a hirdetések VMAP-fájllal való elküldéséhez. A VMAP-fájl URI-ja az elem forrás attribútumában van megadva `<VmapSchedulerPlugin>` .

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>IOS-videolejátszó megvalósítása ad-támogatással
A Microsoft Media platform: az iOS-hez készült Player Framework olyan minta-alkalmazásokból álló gyűjteményt tartalmaz, amelyek bemutatják, hogyan implementálhat egy videolejátszó-alkalmazást a keretrendszer használatával. A Player Framework és a Samples a [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework)webhelyről tölthető le. A GitHub-oldal egy olyan wikire mutató hivatkozást tartalmaz, amely a Player Framework további információit tartalmazza, és bevezeti a Player-minta: [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Hirdetések ütemezése a VMAP
Az alábbi példa bemutatja, hogyan ütemezhet hirdetéseket egy VMAP-fájl használatával.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Hirdetések ütemezése nagy mértékben
A következő minta azt mutatja be, hogyan ütemezhet egy késői kötési hatalmas ad-t.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Az alábbi példa bemutatja, hogyan ütemezhet egy korai kötésű, nagy mennyiségű ad-t.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Az alábbi minta bemutatja, hogyan szúrhat be egy ad-t a durva vágású szerkesztés (RCE) használatával

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Az alábbi példa bemutatja, hogyan ütemezhet egy ad Pod-t.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Az alábbi példa bemutatja, hogyan ütemezhet egy nem ragadós Közép-roll ad-t. A nem Kiemelt ad-t csak egyszer játssza le a rendszer, függetlenül attól, hogy a megjelenítőt keresi-e.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Az alábbi példa bemutatja, hogyan ütemezhet egy Sticky Mid-roll ad-t. A rendszer minden alkalommal megjelenít egy Sticky ad-t, amikor eléri a videó idővonalának megadott pontját.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő minta azt mutatja be, hogyan ütemezhet egy post-roll ad-t.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Az alábbi példa bemutatja, hogyan ütemezhet előre roll ad-t.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Az alábbi minta azt mutatja be, hogyan ütemezhet be egy közép-roll overlay ad-t.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
