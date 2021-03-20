---
title: A részletes olvasó SDK-referenciája
titleSuffix: Azure Cognitive Services
description: A lebilincselő olvasó SDK egy JavaScript-függvénytárat tartalmaz, amely lehetővé teszi a magával ragadó olvasó integrálását az alkalmazásba.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636527"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Magával ragadó olvasó JavaScript SDK-referenciája (v 1.1)

A lebilincselő olvasó SDK egy JavaScript-függvénytárat tartalmaz, amely lehetővé teszi a magával ragadó olvasó integrálását az alkalmazásba.

## <a name="functions"></a>Functions

Az SDK a függvényeket teszi elérhetővé:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Elindítja az olvasót a `iframe` webalkalmazáson belül. Vegye figyelembe, hogy a tartalom mérete legfeljebb 50 MB-ra van korlátozva.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| `token` | sztring | Az Azure AD hitelesítési jogkivonata. További részletekért tekintse meg a részletes [olvasó-erőforrás létrehozását](./how-to-create-immersive-reader.md) ismertető témakört. |
| `subdomain` | sztring | Az Azure-beli magától elolvasó erőforrás egyedi altartománya. További részletekért tekintse meg a részletes [olvasó-erőforrás létrehozását](./how-to-create-immersive-reader.md) ismertető témakört. |
| `content` | [Tartalom](#content) | Egy objektum, amely a magába foglaló olvasóban megjelenítendő tartalmat tartalmazza. |
| `options` | [Beállítások](#options) | Beállítások a magával ragadó olvasó bizonyos viselkedésének konfigurálásához. Választható. |

#### <a name="returns"></a>Válaszok

A egy értéket ad vissza `Promise<LaunchResponse>` , amely feloldja a magával ragadó olvasó betöltését. A `Promise` feloldás egy [`LaunchResponse`](#launchresponse) objektumra.

#### <a name="exceptions"></a>Kivételek

A visszaadott objektum akkor kerül `Promise` elutasításra, [`Error`](#error) Ha a magával ragadó olvasó nem töltődik be. További információ: [hibakódok](#error-codes).

<br>

## <a name="close"></a>bezárás

A magával ragadó olvasó bezárása.

Példa erre a függvényre, ha a kilépési gomb el van rejtve ```hideExitButton: true``` a [Beállítások](#options)beállításban. Ezután egy másik gomb (például a mobil fejléc vissza nyíl) hívhatja ezt a ```close``` függvényt, amikor rákattint.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Olvasó indítása gomb

Az SDK alapértelmezett stílust biztosít a magával ragadó olvasó indítására szolgáló gombhoz. A `immersive-reader-button` Class attribútum használatával engedélyezze ezt a stílust. További részletekért tekintse meg [a részletes olvasó gomb testreszabását](./how-to-customize-launch-button.md) ismertető témakört.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Nem kötelező attribútumok

A gomb megjelenésének és működésének konfigurálásához használja a következő attribútumokat.

| Attribútum | Leírás |
| --------- | ----------- |
| `data-button-style` | Beállítja a gomb stílusát. Lehet `icon` , `text` , vagy `iconAndText` . Alapértelmezett értéke `icon` . |
| `data-locale` | Beállítja a területi beállítást. Például `en-US` vagy `fr-FR`. Az alapértelmezett érték az angol `en` . |
| `data-icon-px-size` | Beállítja az ikon méretét képpontban megadva. Az alapértelmezett érték a 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

A ```renderButtons``` függvény nem szükséges, ha az útmutató segítségével [testreszabja a részletes olvasó gomb](./how-to-customize-launch-button.md) útmutatását.

Ez a függvény stílusokat és frissítéseket frissít a dokumentum az olvasó gombjának elemeivel. Ha ```options.elements``` meg van adni, a gombok a alkalmazásban megadott egyes elemeken belül jelennek meg ```options.elements``` . A paraméter használata akkor ```options.elements``` lehet hasznos, ha több olyan szakaszt használ a dokumentumban, amelyen a magától ellátott olvasót el szeretné indítani, és egyszerűsített módon kívánja megjeleníteni több gomb használatát ugyanazzal a stílussal, vagy egyszerű és konzisztens tervezési mintázattal szeretné megjeleníteni a gombokat. Ha ezt a függvényt a [renderButtons beállítások](#renderbuttons-options) paraméterrel szeretné használni, hívja ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` meg az oldal betöltését az alábbi kódrészletben bemutatott módon. Ellenkező esetben a gombok a dokumentum azon elemein belül jelennek meg, amelyeknek az osztálya látható, ahogyan az az ```immersive-reader-button``` [útmutató a részletes olvasó testreszabása gombra](./how-to-customize-launch-button.md) mutat.

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

További megjelenítési lehetőségekért tekintse meg a fenti [opcionális attribútumokat](#optional-attributes) . Ezeknek a beállításoknak a használatához adja hozzá a beállítási attribútumok bármelyikét az ```HTMLDivElement``` oldal HTML-címéhez.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| `options` | [renderButtons-beállítások](#renderbuttons-options) | A renderButtons függvény bizonyos viselkedésének konfigurálására szolgáló beállítások. Választható. |

### <a name="renderbuttons-options"></a>renderButtons-beállítások

A magával ragadó olvasó gombok megjelenítésének lehetőségei.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons-beállítások paraméterei

| Beállítás | Típus | Description |
| ------- | ---- | ----------- |
| elemek | HTMLDivElement[] | Elemek, amelyekkel a beolvasó gombokat jelenítheti meg. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

A hívásának válaszát tartalmazza `ImmersiveReader.launchAsync` . Vegye figyelembe, hogy a `iframe` magával ragadó olvasót tartalmazó hivatkozás a használatával érhető el `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse paraméterek

| Beállítás | Típus | Description |
| ------- | ---- | ----------- |
| tároló | HTMLDivElement | HTML-elem, amely az olvasó iframe-t tartalmazza. |
| sessionId | Sztring | A munkamenet globálisan egyedi azonosítója, amelyet a rendszer hibakereséshez használ. |
 
## <a name="error"></a>Hiba

A hibával kapcsolatos információkat tartalmaz.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Hiba paraméterei

| Beállítás | Típus | Description |
| ------- | ---- | ----------- |
| code | Sztring | A hibakódok egyike. Lásd: [hibakódok](#error-codes). |
| message | Sztring | A hiba ember által olvasható ábrázolása. |

#### <a name="error-codes"></a>Hibakódok

| Code | Description |
| ---- | ----------- |
| BadArgument | A megadott argumentum érvénytelen: `message` a [hiba](#error)paramétere. |
| Időtúllépés | Nem sikerült betölteni a magával ragadó olvasót a megadott időkorláton belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Szabályozott | Túllépte a hívási sebesség korlátját. |

<br>

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A lebilincselő olvasóban megjelenítendő tartalmat tartalmazza.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Tartalmi paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| cím | Sztring | A lebilincselő olvasó tetején látható cím szövege (nem kötelező) |
| adattömböket | [Adathalmaz []](#chunk) | Adathalmazok tömbje |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Adattömb

Egyetlen adathalmaz, amely a magára az olvasóba kerül át a tartalomba.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Adattömb paramétereinek

| Név | Típus | Description |
| ---- | ---- |------------ |
| tartalom | Sztring | Az a karakterlánc, amely a lebilincselő olvasónak eljuttatott tartalmat tartalmazza. |
| lang | Sztring | A szöveg nyelve, az érték az IETF BCP 47 Language címke formátuma, például en, es-ES. Ha nincs megadva, a rendszer automatikusan észleli a nyelvet. Lásd: [Támogatott nyelvek](#supported-languages). |
| mimeType | sztring | Az egyszerű szöveg, a MathML, a HTML & a Microsoft Word DOCX formátuma támogatott. További részletekért tekintse meg a [támogatott MIME-típusokat](#supported-mime-types) . |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Description |
| --------- | ----------- |
| szöveg/egyszerű | Egyszerű szöveg. |
| szöveg/html | HTML-tartalom. [További információ](#html-support)|
| Application/MathML + XML | Matematikai Markup Language (MathML). [További információ](./how-to/display-math.md).
| alkalmazás/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word. docx formátumú dokumentum.


<br>

## <a name="options"></a>Beállítások

Azokat a tulajdonságokat tartalmazza, amelyek a magába foglaló olvasó bizonyos viselkedéseit konfigurálják.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Beállítások paraméterei

| Név | Típus | Description |
| ---- | ---- |------------ |
| uiLang | Sztring | A felhasználói felület nyelve, az érték az IETF BCP 47 Language címke formátuma, például en, es-ES. Ha nincs megadva, a rendszer alapértelmezés szerint a böngésző nyelvét adja meg. |
| timeout | Szám | Az időtartam (ezredmásodpercben), mielőtt a [launchAsync](#launchasync) időtúllépési hiba miatt meghiúsul (az alapértelmezett érték 15000 MS). Ez az időkorlát csak az olvasó oldal kezdeti indítására vonatkozik, ahol a sikeres megfigyelhető, amikor megnyílik az olvasó lap, és elindul a tárcsa. Az időkorlát módosítása nem szükséges. |
| uiZIndex | Szám | Z – a létrehozandó iframe-index (alapértelmezett érték: 1000). |
| useWebview | Logikai| IFrame helyett Webview címkét használhat a Chrome-alkalmazásokkal való kompatibilitáshoz (az alapértelmezett érték a False). |
| onExit | Függvény | Végrehajtja, amikor a magától megjelenő olvasó kilép. |
| allowFullscreen | Logikai | Teljes képernyős váltás lehetősége (az alapértelmezett érték igaz). |
| hideExitButton | Logikai | Azt jelzi, hogy el kell-e rejteni a megölelő olvasó kilépési gombjának nyilát (az alapértelmezett érték hamis). Ez csak akkor lehet igaz, ha egy olyan alternatív mechanizmus van megadva, amely kizárja az olvasót (például a mobil eszköztár vissza nyilát). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Az olvasó cookie-felhasználásának beállítása (az alapértelmezett érték a *CookiePolicy. disable*). A gazda alkalmazás feladata a szükséges felhasználói jóváhagyások beszerzése az EU cookie-k megfelelőségi szabályzatának megfelelően. Lásd: [cookie-házirend beállításai](#cookiepolicy-options). |
| disableFirstRun | Logikai | Tiltsa le az első futtatási élményt. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Beállítások a beolvasás konfigurálásához. |
| translationOptions | [TranslationOptions](#translationoptions) | A fordítás konfigurálásának beállításai. |
| displayOptions | [DisplayOptions](#displayoptions) | A szövegméret konfigurálására szolgáló beállítások, betűkészletek stb. |
| beállítások | Sztring | Az a onPreferencesChanged, amely a felhasználó beállításait jelképezi a felhasználóbarát olvasóban. További információért lásd a [Beállítások paramétereit](#settings-parameters) és a [felhasználói beállítások tárolását](./how-to-store-user-preferences.md) ismertető témakört. |
| onPreferencesChanged | Függvény | A felhasználó beállításainak megváltozásakor végez végrehajtást. További információkért lásd: [útmutató a felhasználói beállítások tárolásához](./how-to-store-user-preferences.md) . |
| customDomain | Sztring | Belső használatra fenntartva. Az az egyéni tartomány, ahol a magával ragadó olvasó WebApp fut (az alapértelmezett érték null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Fontos** Ne próbálja meg programozott módon megváltoztatni a `-preferences` felhasználóbarát olvasó alkalmazásba érkező és a felé irányuló karakterlánc értékeit, mivel ez váratlan viselkedést eredményezhet, ami az ügyfelek számára csökkentett teljesítményű felhasználói élményt eredményez. A gazdagép alkalmazásai soha nem rendelhetnek egyéni értéket a karakterlánchoz, vagy kezelhetik azokat `-preferences` . A `-preferences` karakterlánc beállítás használatakor csak a `-onPreferencesChanged` visszahívási beállításban visszaadott pontos értéket használja.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| hang | Sztring | Hang, "női" vagy "férfi". Vegye figyelembe, hogy nem minden nyelv támogatja a nemek közötti különbségeket. |
| sebesség | Szám | A lejátszási sebességnek 0,5 és 2,5 közöttinek kell lennie, a határokat is beleértve. |
| Robotpilota | Logikai | A rendszer automatikusan beolvassa a beolvasást, amikor a magával ragadó olvasó betöltődik. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> A böngésző korlátozásai miatt a Safari nem támogatja az Automatikus lejátszást.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| language | Sztring | Beállítja a fordítási nyelvet, az érték az IETF BCP 47 nyelvi címke formátumában van, például: fr-FR, es-MX, zh-Hans-CN. A Word vagy a dokumentum fordításának automatikus engedélyezéséhez szükséges. |
| autoEnableDocumentTranslation | Logikai | A teljes dokumentum automatikus lefordítása. |
| autoEnableWordTranslation | Logikai | A Word fordítás automatikus engedélyezése. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions paraméterek

| Név | Típus | Description |
| ---- | ---- |------------ |
| Textsize értékének | Szám | Beállítja a kiválasztott szövegméret méretét. |
| increaseSpacing | Logikai | Beállítja, hogy be-vagy kikapcsolja-e a szöveg térközét. |
| fontFamily | Sztring | Beállítja a kiválasztott betűkészletet ("Calibri", "ComicSans" vagy "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy-beállítások

```typescript
enum CookiePolicy { Disable, Enable }
```

**Az alább felsorolt beállítások csak tájékoztató jellegűek**. A teljes olvasó tárolja a beállításait, vagy a felhasználói beállításokat a cookie-kon. Ez a *cookiePolicy* beállítás **letiltja** a cookie-k alapértelmezett használatát, hogy megfeleljen az EU cookie-megfelelőségi törvényeinek. Ha újra engedélyezni szeretné a cookie-kat, és vissza kívánja állítani az olvasói felhasználói beállítások alapértelmezett funkcióit, meg kell győződnie arról, hogy a webhely vagy alkalmazás beolvassa a felhasználótól a cookie-k engedélyezéséhez szükséges megfelelő engedélyt. Ezután, ha újra engedélyezni szeretné a cookie-kat a lebilincselő olvasóban, explicit módon be kell állítania a *cookiePolicy* beállítást a *cookiePolicy. Enable* értékre, amikor elindítja a lebilincselő olvasót. Az alábbi táblázat azt ismerteti, hogy milyen beállítások jelennek meg a saját cookie-ban, amikor a *cookiePolicy* beállítás engedélyezve van.

#### <a name="settings-parameters"></a>Beállítások paraméterei

| Beállítás | Típus | Description |
| ------- | ---- | ----------- |
| Textsize értékének | Szám | Beállítja a kiválasztott szövegméret méretét. |
| fontFamily | Sztring | Beállítja a kiválasztott betűkészletet ("Calibri", "ComicSans" vagy "Sitka"). |
| textSpacing | Szám | Beállítja, hogy be-vagy kikapcsolja-e a szöveg térközét. |
| formattingEnabled | Logikai | Beállítja, hogy a HTML-formázás be van-e kapcsolva vagy ki van-e kapcsolva. |
| Téma | Sztring | Beállítja a kiválasztott témát (például "Light", "Dark"...). |
| syllabificationEnabled | Logikai | Beállítja, hogy be-vagy kikapcsolja-e a syllabification. |
| nounHighlightingEnabled | Logikai | azt határozza meg, hogy be van-e kapcsolva a főnév kiemelése. |
| nounHighlightingColor | Sztring | Beállítja a kijelölt főnév kiemelésének színét. |
| verbHighlightingEnabled | Logikai | Beállítja, hogy be van-e kapcsolva a művelet kiemelése. |
| verbHighlightingColor | Sztring | Beállítja a kiválasztott művelet kiemelésének színét. |
| adjectiveHighlightingEnabled | Logikai | Beállítja, hogy be van-e kapcsolva a melléknév kiemelése. |
| adjectiveHighlightingColor | Sztring | Beállítja a kijelölt jelző kiemelésének színét. |
| adverbHighlightingEnabled | Logikai | Beállítja, hogy be van-e kapcsolva a határozószók kiemelése. |
| adverbHighlightingColor | Sztring | Beállítja a kiválasztott határozószók kiemelésének színét. |
| pictureDictionaryEnabled | Logikai | Beállítja, hogy be-vagy kikapcsolja-e a képszótárat. |
| posLabelsEnabled | Logikai | Azt határozza meg, hogy a beszéd minden Kiemelt részének felső felirata be van-e kapcsolva.  |

<br>

## <a name="supported-languages"></a>Támogatott nyelvek

A lebilincselő olvasó fordítási funkciója számos nyelvet támogat. További részletekért lásd a [nyelvi támogatást](./language-support.md) ismertető témakört.

<br>

## <a name="html-support"></a>HTML-támogatás

Ha a formázás engedélyezve van, az alábbi tartalom HTML-ként jelenik meg a magára az olvasóba.

| HTML | Támogatott tartalom |
| --------- | ----------- |
| Betűstílusok | Félkövér, dőlt, aláhúzás, kód, áthúzott, felső, alsó index |
| Rendezetlen listák | Lemez, kör, négyzet |
| Rendezett felsorolások | Decimális, felső-alfa, alsó-alfa, felső-római, Lower-Roman |

A nem támogatott címkék hasonlóan lesznek megjelenítve. A képek és a táblázatok jelenleg nem támogatottak.

<br>

## <a name="browser-support"></a>Böngészőtámogatás

Használja az alábbi böngészők legújabb verzióit a legjobb élmény érdekében a teljes olvasóval.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Következő lépések

* Ismerje [meg az olvasót a githubon](https://github.com/microsoft/immersive-reader-sdk)
* [Rövid útmutató: hozzon létre egy webalkalmazást, amely elindítja a részletes olvasót (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
