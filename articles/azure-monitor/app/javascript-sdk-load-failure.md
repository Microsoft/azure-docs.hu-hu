---
title: Az SDK betöltési hibáinak elhárítása JavaScript-webalkalmazások esetén – Azure Application Insights
description: Az SDK betöltési hibáinak hibaelhárítása JavaScript-webalkalmazások esetén
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711416"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Az SDK betöltési hibáinak elhárítása JavaScript-webalkalmazásokhoz

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

Az SDK-Betöltési hiba kivételt a JavaScript-kódrészlet (v3 vagy újabb) hozta létre, és azt észleli, hogy az SDK-parancsfájl letöltése vagy inicializálása nem sikerült. A végfelhasználói ügyfél (böngésző) leegyszerűsítve nem tudta letölteni az Application Insights SDK-t, vagy inicializálni az azonosított üzemeltetési lapról, ezért nem fog telemetria vagy eseményeket jelenteni.

![Azure Portal böngésző meghibásodásának áttekintése](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Ezt a kivételt a fetch () API-t vagy az XMLHttpRequest-t támogató összes főbb böngésző támogatja. Ez kizárja az IE 8 és az alatti verziókat, ezért nem fog ilyen típusú kivételt kapni az adott böngészőktől (kivéve, ha a környezet tartalmazza a beolvasás kitöltését).

![böngésző kivételének részletei](./media/javascript-sdk-load-failure/exception.png)

A verem részletei tartalmazzák a végfelhasználó által használt URL-címekre vonatkozó alapvető információkat.

| Név                      | Leírás                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN- &nbsp; végpont&gt; | Az SDK letöltéséhez használt URL-cím (és sikertelen).                                                      |
| &lt;Súgó &nbsp; hivatkozása&gt;    | Egy URL-cím, amely a hibaelhárítási dokumentációra hivatkozik (ez a lap).                                              |
| &lt;Gazdagép &nbsp; URL-címe&gt;     | A végfelhasználó által használt oldal teljes URL-címe.                                                    |
| &lt;Végpont &nbsp; URL-címe&gt; | A kivétel jelentésére szolgáló URL-cím hasznos lehet annak azonosításához, hogy az üzemeltetési oldal elérhető-e a nyilvános internetről vagy egy privát felhőből.

A kivétel leggyakoribb okai:

- Átmeneti hálózati kapcsolódási hiba történt.
- Application Insights CDN-kimaradás.
- Nem sikerült inicializálni az SDK-t a parancsfájl betöltése után.
- Application Insights JavaScript CDN blokkolva.

Az [átmeneti hálózati kapcsolat hibája](#intermittent-network-connectivity-failure) a kivétel leggyakoribb oka, különösen olyan mobil barangolási helyzetekben, ahol a felhasználók időnként elveszítik a hálózati kapcsolatot.

A következő szakaszok leírják, hogyan lehet elhárítani a hiba lehetséges kiváltó okát.

> [!NOTE]
> A hibaelhárítási lépések többek között azt feltételezik, hogy az alkalmazásnak van közvetlen hozzáférése a kódrészlethez &lt; / &gt; címkéhez és a konfigurációhoz, amelyet a rendszer az üzemeltetési HTML-oldal részeként ad vissza. Ha nem, akkor ezek az azonosított lépések nem lesznek érvényesek a forgatókönyvre.

## <a name="intermittent-network-connectivity-failure"></a>Átmeneti hálózati kapcsolódási hiba

**Ha a felhasználó időszakos hálózati csatlakozási hibákat tapasztal, akkor kevesebb lehetséges megoldás érhető el, és ezek általában rövid idő alatt oldják meg magukat.** Ha például a felhasználó újratölti a webhelyét (frissíti a lapot), a fájlok (végül) letöltése és a gyorsítótárba helyezése csak a frissített verzió kiadása után történik meg.

> [!NOTE]
> Ha ez a kivétel állandó, és számos felhasználó (a jelen kivétel gyors és tartós szintje által diagnosztizált), valamint a normál ügyfél-telemetria csökkenése miatt következik be, akkor az időszakos hálózati kapcsolati hibák _valószínűleg nem valószínűek_ a probléma valódi okának, és a többi ismert problémával folytatják a diagnosztizálást.

Ha az SDK-t saját CDN-re üzemelteti, nem valószínű, hogy megadja vagy csökkenti a kivétel előfordulását, mivel a saját CDN-t ugyanazzal a problémával fogja érinteni.

Ugyanez igaz akkor is, ha az SDK-t NPM-csomagok megoldáson keresztül használja. A végfelhasználók szempontjából azonban, ha ez bekövetkezik, a teljes alkalmazás betöltése/inicializálása sikertelen (nem _csak_ a telemetria SDK-t, amely nem jelenik meg a vizuálisan), így valószínűleg frissítik a webhelyet egészen addig, amíg a szolgáltatás teljesen betöltődik.

[NPM-csomagokat](#use-npm-packages-to-embed-the-application-insight-sdk) is használhat a Application Insights SDK beágyazásához.

Az időszakos hálózati csatlakozási hibák csökkentése érdekében minden CDN-fájlon Cache-Control fejléceket hoztunk létre, így ha a végfelhasználó böngészője letöltötte az SDK aktuális verzióját, akkor nem kell újra letöltenie, és a böngésző újra felhasználja a korábban beszerzett példányt (lásd: a [gyorsítótárazás működése](../../cdn/cdn-how-caching-works.md)). Ha a gyorsítótárazási ellenőrzés meghiúsul, vagy új kiadás van, akkor a végfelhasználó böngészőjében le kell töltenie a frissített verziót. Így előfordulhat, hogy a _"Noise" ("zaj"_ ) hátterét a sikertelenség ellenőrzése vagy egy ideiglenes tüske okozza, amikor új kiadás történik, és általánosan elérhetővé válik (a CDN-ben üzembe helyezett).
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN-kimaradás

Megerősítheti, hogy van-e Application Insights CDN-kimaradás, ha közvetlenül a böngészőből próbál hozzáférni a CDN-végponthoz (például https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js vagy https://js.monitor.azure.com/scripts/b/ai.2.min.js) egy másik helyről, mint a végfelhasználók valószínűleg a saját fejlesztői gépről) (feltéve, hogy a szervezet nem blokkolja ezt a tartományt).

Ha megerősíti, hogy van leállás, [hozzon létre egy új támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) , vagy próbálja meg módosítani az SDK letöltéséhez használt URL-címet.

### <a name="change-the-cdn-endpoint"></a>CDN-végpont módosítása
  
Mivel a kódrészletet és annak konfigurációját az alkalmazás az egyes generált lapok részeként adja vissza, a kódrészletek `src` konfigurációjának módosításával más URL-címet is használhat az SDK-hoz. Ennek a módszernek a használatával megkerülheti a CDN letiltott hibáját, mivel az új URL-cím nem tiltható le.

Aktuális Application Insights JavaScript SDK CDN-végpontok
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> A `https://js.monitor.azure.com/` végpont egy alias, amely lehetővé teszi számunkra, hogy körülbelül 5 percen belül váltani lehessen a CDN-szolgáltatók között anélkül, hogy módosítania kellene a konfigurációt. Ez azt teszi lehetővé, hogy az észlelt CDN-vel kapcsolatos problémákat gyorsabban javítsuk, ha egy CDN-szolgáltató regionális vagy globális problémákkal rendelkezik anélkül, hogy mindenki a beállítások módosítására lenne szükség.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Nem sikerült inicializálni az SDK-t a parancsfájl betöltése után

Ha az SDK inicializálása sikertelen, a &lt; szkript/ &gt; sikeresen le lett töltve a CDN-ről, de az inicializálás során meghiúsul. Ennek oka az lehet, hogy hiányoznak vagy érvénytelenek a függőségek vagy valamilyen JavaScript-kivétel.

Első lépésként ellenőrizze, hogy az SDK letöltése sikeresen megtörtént-e, ha a parancsfájl nem lett letöltve, és ez a forgatókönyv __nem__ az SDK-Betöltési hiba oka.

Gyors ellenőrzés: olyan böngésző használata, amely támogatja a fejlesztői eszközöket (F12), ellenőrizze a hálózat lapon, hogy a kódrészlet konfigurációjában definiált parancsfájl a ```src``` 200 (sikeres) vagy a 304 (nem módosított) hibakódgal lett-e letöltve. A hálózati forgalom áttekintéséhez olyan eszközt is használhat, mint a Hegedűs.

Az alábbi szakaszban a különböző jelentési lehetőségek szerepelnek, és a támogatási jegyet kell létrehoznia, vagy probléma merül fel a GitHubon.

 Alapszintű jelentéskészítési szabályok:

- Ha a probléma csak kis számú felhasználót érint, és a böngésző (k) egy adott vagy egy részhalmazát (a jelentett kivétel részleteit), akkor valószínű, hogy a végfelhasználó vagy a környezet problémát okoz, ami valószínűleg megköveteli, hogy az alkalmazás további `polyfill` megvalósításokat is nyújtson. Ilyen esetben [a githubon egy problémát kell megadnia](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Ha ez a probléma hatással van a teljes alkalmazásra és az összes felhasználóra, akkor valószínűleg a kiadással kapcsolatos probléma merül fel, ezért [új támogatási jegyet kell létrehoznia](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>JavaScript-kivételek

Először is ellenőrizheti a JavaScript-kivételeket, ha olyan böngészőt használ, amely támogatja a fejlesztői eszközöket (F12), betölti a lapot, és áttekinti, hogy történt-e kivétel

Ha a rendszer kivételeket jelez az SDK-szkriptben (például ai.2.min.js), akkor ez arra utalhat, hogy az SDK-ba átadott konfiguráció váratlan vagy hiányzó szükséges konfigurációt tartalmaz, vagy ha egy hibás kiadás lett telepítve a CDN-ben.

A hibás konfiguráció ellenőrzéséhez módosítsa a kódrészletbe átadott konfigurációt (ha még nincs), hogy csak karakterlánc-értékként tartalmazza a kialakítási kulcsot.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Ha ezt a minimális konfigurációt használja, továbbra is JavaScript-kivételt lát az SDK-szkriptben, [hozzon létre egy új támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) , mivel ez a hibás buildek visszaállítását igényli, mivel valószínűleg probléma van egy újonnan telepített verzióval.

Ha a kivétel eltűnik, a problémát valószínűleg egy típus nem egyezik vagy váratlan érték okozza. Először adja meg a konfigurációs beállításokat, és tesztelje, amíg a kivétel újra be nem következik. Ezután jelölje be a problémát okozó tétel dokumentációját. Ha a dokumentáció nem egyértelmű, vagy segítségre van szüksége, [a githubon](https://github.com/Microsoft/ApplicationInsights-JS/issues)teheti fel a problémát.

Ha korábban már üzembe helyezte a konfigurációt, de éppen elkezdte jelenteni ezt a kivételt, akkor az egy újonnan telepített verzióval kapcsolatos probléma lehet, ellenőrizze, hogy csak a felhasználók vagy a böngésző egy kis készletét érinti-e, vagy a [githubon](https://github.com/Microsoft/ApplicationInsights-JS/issues) vagy  [egy új támogatási jegyet hoz létre](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Konzol hibakeresésének engedélyezése

Feltételezve, hogy a következő lépés nem jelent kivételt a konzol hibakeresésének engedélyezéséhez azáltal `loggingLevelConsole` , hogy hozzáadja a beállítást a konfigurációhoz, ezzel az összes inicializálási hibát és figyelmeztetést elküldi a böngészők konzoljának (általában a fejlesztői eszközökön (F12) keresztül érhető el). A jelentett hibáknak magától értetődőnek kell lenniük, és ha további segítségre van szüksége [a githubon](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Az inicializálás során az SDK néhány alapvető ellenőrzést hajt végre az ismert fő függőségek tekintetében. Ha ezeket az aktuális futtatókörnyezet nem biztosítja, akkor a rendszer figyelmeztető üzenetként fogja jelenteni a hibákat a konzolon, de csak akkor, ha a `loggingLevelConsole` nullánál nagyobb.

Ha továbbra sem sikerül az inicializálás, próbálja meg engedélyezni a ```enableDebug``` konfigurációs beállítást. Ez azt eredményezi, hogy az összes belső hiba kivételként fog megjelenni (ami miatt a telemetria elvész). Mivel ez csak fejlesztőként van beállítva, valószínűleg zajosak lesznek az egyes belső ellenőrzések részeként kiváltott kivételek, ezért az egyes kivételeket át kell tekintenie annak megállapításához, hogy az SDK milyen hibát okoz. A parancsfájl nem minified verzióját használja (jegyezze fel az alábbi bővítményt ". js" és nem ".min.js"), ellenkező esetben a kivételek nem olvashatók.

> [!WARNING]
> Ez csak fejlesztő beállítás, és nem szabad engedélyezni a teljes éles környezetben, mivel elveszti a telemetria.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Ha ez még nem nyújt információt, akkor a [githubon](https://github.com/Microsoft/ApplicationInsights-JS/issues) a részleteket és egy példa-webhelyet tartalmazó problémát kell megadnia, ha rendelkezik ilyennel. A probléma azonosításához adja meg a böngésző verziószámát, az operációs rendszer és a JS-keretrendszer részleteit.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>A Application Insights JavaScript CDN blokkolva

A CDN blokkolása akkor lehetséges, amikor egy Application Insights JavaScript SDK CDN-végpontot jelentettek be és/vagy nem biztonságosként azonosítottak. Ha ez bekövetkezik, akkor a végpont nyilvánosan blokkolva lesz, és a listán szereplő felhasználók megkezdik az összes hozzáférés blokkolását.

A megoldáshoz a CDN-végpont tulajdonosának úgy kell működnie, hogy működjön együtt a blokk-tőzsdei entitással, amely nem biztonságosként jelölte meg a végpontot, hogy el lehessen távolítani a megfelelő listából.

Ellenőrizze, hogy a CDN-végpont nem biztonságosként van-e azonosítva.
- [Google átláthatósági jelentés](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Attól függően, hogy az alkalmazás, a tűzfal vagy a környezet frissíti-e a listában lévő helyi másolatokat, jelentős mennyiségű időt vehet igénybe, és/vagy manuális beavatkozást igényelhet a végfelhasználók vagy a vállalati informatikai részlegek számára a frissítés kényszerítéséhez, vagy explicit módon engedélyezheti a CDN-végpontok számára a probléma megoldását.

Ha a CDN-végpont nem biztonságosként van azonosítva, [hozzon létre egy támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) , amely biztosítja, hogy a probléma a lehető leghamarabb megoldódik.

Ha *gyorsabban szeretné megkerülni* ezt a problémát, [módosíthatja az SDK CDN-végpontot](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights a JavaScript CDN blokkolva van (a felhasználó által letiltott böngésző; telepített blokkoló; személyes tűzfal)

Ellenőrizze, hogy a végfelhasználók rendelkeznek-e:

- Telepített egy böngésző beépülő modult (általában ad-, kártevő-vagy felugró ablakos formában).
- Blokkolva (vagy nem engedélyezett) a Application Insights CDN-végpontok a böngészőben vagy a proxyn.
- Olyan tűzfalszabály konfigurálva, amely az SDK CDN tartományának blokkolását okozza (vagy a DNS-bejegyzés nem oldható fel).

Ha ezek bármelyikét konfigurálták, a CDN-végpontok engedélyezéséhez együtt kell működnie (vagy meg kell adnia a dokumentációt).

Előfordulhat, hogy a telepített beépülő modul a nyilvános Blocklist használja. Ha ez nem így van, akkor valószínűleg egy másik manuálisan konfigurált megoldás, vagy egy privát tartományi Blocklist használ.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Kivételek hozzáadása CDN-végpontokhoz

A végfelhasználók és a dokumentációk megadásával tájékoztatják őket arról, hogy az Application Insights CDN-végpontokból származó parancsfájlokat a böngésző beépülő moduljának vagy a tűzfalszabályok kivételének listájáról kell letölteniük (a végfelhasználó környezete alapján változhat).

Íme egy példa arra, hogyan konfigurálhatja a [Chrome-t a webhelyekhez való hozzáférés engedélyezéséhez vagy letiltásához.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN blokkolva van (vállalati tűzfal)

Ha a végfelhasználók vállalati hálózaton vannak, akkor valószínűleg a tűzfal megoldásuk, és hogy az informatikai részleg valamilyen internetes szűrési rendszer megvalósítását valósítja meg. Ebben az esetben dolgoznia kell velük, hogy engedélyezze a szükséges szabályokat a végfelhasználók számára.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Kivételek hozzáadása a vállalatok CDN-végpontjai számára

  Ez hasonló a [végfelhasználók kivételek hozzáadásához](#add-exceptions-for-cdn-endpoints), de a vállalat informatikai részlegével kell dolgoznia, hogy a Application Insights CDN-végpontokat a letöltéshez konfigurálja (vagy eltávolítja) azokat bármely tartományi blokk-tőzsdei vagy engedélyezési-tőzsdei szolgáltatásban.

  > [!WARNING]
  > Ha a vállalati felhasználó [privát felhőt](https://azure.microsoft.com/overview/what-is-a-private-cloud/) használ, és nem engedélyez semmilyen kivételt a belső felhasználók számára a CDN-végpontokhoz való nyilvános hozzáférés biztosításához, akkor a [Application Insights NPM-csomagokat](https://www.npmjs.com/package/applicationinsights) kell használnia, vagy a saját CDN-re kell TELEPÍTENIe a Application Insights SDK-t.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>További hibaelhárítás a blokkolt CDN-hez

> [!NOTE]
> Ha a felhasználók [privát felhőt](https://azure.microsoft.com/overview/what-is-a-private-cloud/) használnak, és nem férnek hozzá a nyilvános internethez, az SDK-t a saját CDN-ben kell üzemeltetni, vagy NPM-csomagokat kell használnia.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Az SDK üzemeltetése saját CDN-ben

 Ahelyett, hogy a végfelhasználó letölti a Application Insights SDK-t a nyilvános CDN-ből, a saját CDN-végpontján futtathatja a Application Insights SDK-t. Javasoljuk, hogy egy adott verziót (AI. 2. #. # .min.js) használjon, hogy könnyebb legyen azonosítani, hogy melyik verziót használja. Frissítse rendszeresen a jelenlegi verzióra (ai.2.min.js), így kihasználhatja az elérhetővé váló hibajavításokat és új funkciókat.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>NPM-csomagok használata az Application Insight SDK beágyazásához

A kódrészlet és a nyilvános CDN-végpontok használata helyett használhatja a [NPM-csomagokat](https://www.npmjs.com/package/applicationinsights) , hogy a saját JavaScript-fájljainak részeként tartalmazza az SDK-t. Az SDK a saját parancsfájljain belül csak egy csomagot fog kiváltani.

> [!NOTE]
> Javasoljuk, hogy a NPM-csomagok használatakor a [JavaScript-köteg](https://www.bing.com/search?q=javascript+bundler) valamilyen formáját is használja a kódok felosztásához és a minification való segítségnyújtáshoz.

A kódrészlethez hasonlóan az is előfordulhat, hogy a saját parancsfájlokat (az SDK-NPM csomagok használatával vagy anélkül) érintheti az itt felsorolt blokkoló problémák, így az alkalmazástól, a felhasználóktól és a keretrendszertől függően érdemes lehet megfontolni a kódrészlet logikájának megvalósítását, hogy észlelje és jelentse ezeket a problémákat.


## <a name="next-steps"></a>Következő lépések 
- [További segítség kérése a GitHubon történt probléma bejelentésével](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Weblap használatának figyelése](javascript.md)
