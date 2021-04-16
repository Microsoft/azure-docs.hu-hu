---
title: Referencia-útmutató kifejezések függvényeihez
description: Referencia-útmutató a függvények kifejezésekben való Azure Logic Apps és Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: d2ea08551299d66edd919a828877c134c84ef938
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477774"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referencia-útmutató a függvények kifejezésekben való Azure Logic Apps és Power Automate

A Azure Logic Apps és [](../logic-apps/logic-apps-overview.md) a Power Automate [egyes](/flow/getting-started)kifejezései olyan [](../logic-apps/logic-apps-workflow-definition-language.md#expressions) futtatókörnyezeti műveletekből kapják meg az értékeiket, amelyek esetleg még nem léteznek, amikor a munkafolyamat elindul. Ezekre az értékekre való hivatkozáshoz vagy a  kifejezésekben található értékek feldolgozásához használhatja a munkafolyamat-definíciós [nyelv által biztosított függvényeket.](../logic-apps/logic-apps-workflow-definition-language.md)

> [!NOTE]
> Ez a referenciaoldal a Azure Logic Apps és Power Automate is vonatkozik, de a dokumentációban Azure Logic Apps jelenik meg. Bár ez az oldal kifejezetten a logikai alkalmazásokra hivatkozik, ezek a függvények folyamatokhoz és logikai alkalmazásokhoz is működnek. További információ a függvényekkel és kifejezésekkel kapcsolatban a Power Automate [kifejezések feltételekben való használatát.](/flow/use-expressions-in-conditions)

Kiszámíthat például értékeket matematikai függvények használatával, például az [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) függvény használatával, ha az összeget egész számból vagy lebegő értékből szeretné kiszámítani. Az alábbi példa a függvényekkel elvégezheti a feladatokat:

| Feladat | Függvényszintaxis | Eredmény |
| ---- | --------------- | ------ |
| Kisbetűs sztringet ad vissza. | toLower('<*text*>') <p>Például: toLower('Hello') | "hello" |
| Globálisan egyedi azonosítót (GUID) ad vissza. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Ha az általános [rendeltetésük alapján keres függvényeket,](#ordered-by-purpose)tekintse át az alábbi táblázatokat. Az egyes függvények részletes információit az ábécésorrendben [is láthatja.](#alphabetical-list)

## <a name="functions-in-expressions"></a>Függvények kifejezésekben

A függvény kifejezésben való használatának megmutatása érdekében ez a példa bemutatja, hogyan kaphatja meg az értéket a paraméterből, és hogyan rendelheti hozzá ezt az értéket a tulajdonsághoz a parameters() függvény használatával `customerName` `accountName` egy kifejezésben: [](#parameters)

```json
"accountName": "@parameters('customerName')"
```

Néhány további általános módszer a függvények kifejezésekben való használatára:

| Feladat | Függvényszintaxis egy kifejezésben |
| ---- | -------------------------------- |
| Egy elemet úgy végezhet el egy elemet, hogy az adott elemet egy függvénynek ad át. | " \@ < *functionName*>(<*item*>)" |
| 1. Szerezze be *a parameterName* értékét a beágyazott függvény `parameters()` használatával. </br>2. Az eredményekkel úgy dolgozhat, hogy ezt az értéket a *functionName függvénynek továbbküldi.* | " \@ < *functionName*>(parameters('<*parameterName*>'))" |
| 1. Szerezze be az eredményt a beágyazott belső függvény *functionName függvényéből.* </br>2. Adja át az eredményt a *functionName2 külső függvénynek.* | " \@ < *functionName2*>(<*functionName*>(<*item*>))" |
| 1. Az eredményt a *functionName függvényből szerezze be.* </br>2. Mivel az eredmény egy olyan objektum, amely a *propertyName* tulajdonsággal van megadva, le kell kapnia a tulajdonság értékét. | " \@ < *functionName*>(<*item*>).<*propertyName*>" |
|||

A függvény például két vagy több sztringértéket `concat()` is vehet paraméterként. Ez a függvény egyetlen sztringben egyesíti ezeket a sztringeket. Sztring-literálokat (például "Merte" és "Merte" ) is átadhat, így egy "Mertowen" sztringet kap:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vagy lekért sztringértékeket is lekért paraméterekből. Ez a példa a `parameters()` függvényt használja minden `concat()` paraméterben, valamint a `firstName` és a `lastName` paramétert. Ezután át kell adni az eredményül kapott sztringeket a függvénynek, hogy egy kombinált sztringet kapjon( például `concat()` "en":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Mindkét példa mindkét esetben hozzárendeli az eredményt a `customerName` tulajdonsághoz.

Néhány további megjegyzés a kifejezések függvényeiről:

* A függvényparaméterek kiértékelése balról jobbra történik.

* A paraméterdefiníciók szintaxisában egy paraméter után megjelenő kérdőjel (?) azt jelenti, hogy a paraméter nem kötelező. Lásd például: [getFutureTime()](#getFutureTime).

Az alábbi szakaszok a függvényeket az általános rendeltetésüknek megfelelő rendszerezés alapján rendezik, vagy betűrendben [tallózhatja őket.](#alphabetical-list)

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Sztringfüggvények

A sztringekkel való munkához használhatja ezeket a sztringfunkciókat és néhány [gyűjteményfunkciót is.](#collection-functions) A sztringek függvényei csak sztringekon működnek.

| String függvény | Feladat |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Két vagy több sztring kombinálható, és az egyesített sztringet adja vissza. |
| [endsWith (vége)](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Ellenőrizze, hogy egy sztring a megadott sztringre végződik-e. |
| [formatNumber (formátumszám)](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Számot ad vissza sztringként a megadott formátum alapján |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Hozzon létre egy globálisan egyedi azonosítót (GUID) sztringként. |
| [indexOf (indexof)](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Egy karakterlánc alsztring kezdőpozícióját adja vissza. |
| [lastIndexOf (lastIndexOf)](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Egy karakterláncsztring utolsó előfordulásának kezdőpozícióját adja vissza. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) (hossz) | Egy sztringben vagy tömbben lévő elemek számát adja vissza. |
| [Helyettesít](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Cserélje le a sztring alsztringet a megadott sztringre, és adja vissza a frissített sztringet. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Olyan tömböt ad vissza, amely vesszővel elválasztott részsztringeket tartalmaz egy nagyobb, az eredeti sztringben megadott elválasztó karakteren alapuló sztringből. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Ellenőrizze, hogy egy sztring egy adott sztringsztring-karakterláncmal kezdődik-e. |
| [Substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Karaktereket ad vissza egy sztringből a megadott pozíciótól kezdve. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Kisbetűs sztringet ad vissza. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Sztringet ad vissza nagybetűs formátumban. |
| [Berendezés](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Távolítsa el a kezdő és záró szóközt egy sztringből, és adja vissza a frissített sztringet. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Gyűjteményfüggvények

A gyűjteményekkel, általában tömbökvel, sztringekkel és néha szótárakkal való munkához használhatja ezeket a gyűjteményi függvényeket.

| Collection függvény | Feladat |
| ------------------- | ---- |
| [Tartalmaz](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Ellenőrizze, hogy egy gyűjtemény rendelkezik-e egy adott elemet. |
| [Üres](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Ellenőrizze, hogy a gyűjtemény üres-e. |
| [Első](../logic-apps/workflow-definition-language-functions-reference.md#first) | Egy gyűjtemény első elemének visszaadása. |
| [Kereszteződés](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Olyan gyűjteményt ad vissza, *amely csak a* megadott gyűjtemények közös elemeit tartalmazza. |
| [Cikk](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ha egy ismétlődő műveletben egy tömböt ismétel, a művelet aktuális iterációja során adja vissza a tömb aktuális elemét. |
| [Csatlakozzon](../logic-apps/workflow-definition-language-functions-reference.md#join) | Olyan sztringet ad vissza, *amely* egy tömb összes elemét tartalmazza, a megadott karakterrel elválasztva. |
| [Utolsó](../logic-apps/workflow-definition-language-functions-reference.md#last) | Egy gyűjtemény utolsó elemének visszaadása. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) (hossz) | Egy sztringben vagy tömbben lévő elemek számát adja vissza. |
| [Ugrál](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Eltávolíthat elemeket egy gyűjtemény elejéről, és visszaadhatja *az összes többi elemet.* |
| [take (take)](../logic-apps/workflow-definition-language-functions-reference.md#take) | Elemeket ad vissza egy gyűjtemény elejéről. |
| [Unió](../logic-apps/workflow-definition-language-functions-reference.md#union) | Olyan gyűjteményt ad vissza, *amely a* megadott gyűjtemények összes elemét tartalmazza. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logikai összehasonlító függvények

A feltételekkel való munkához, az értékek és a kifejezések eredményeinek összehasonlításához, vagy különböző típusú logika kiértékeléséhez használhatja ezeket a logikai összehasonlító függvényeket. Az egyes függvényekkel kapcsolatos teljes referencia a betűrendes [listában található.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

> [!NOTE]
> Ha logikai függvényeket vagy feltételeket használ az értékek összehasonlítására, a null értékek üres sztring ( `""` ) értékekké alakulnak át. A feltételek viselkedése eltérő, ha null érték helyett üres sztringet hasonlít össze. További információ: [string() függvény.](#string) 

| Logikai összehasonlító függvény | Feladat |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Ellenőrizze, hogy az összes kifejezés igaz-e. |
| [Egyenlő](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Ellenőrizze, hogy mindkét érték egyenértékű-e. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Ellenőrizze, hogy az első érték nagyobb-e a második értéknél. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Ellenőrizze, hogy az első érték nagyobb-e a második értéknél, vagy egyenlő-e vele. |
| [Ha](../logic-apps/workflow-definition-language-functions-reference.md#if) | Ellenőrizze, hogy egy kifejezés igaz-e vagy hamis. Az eredmény alapján adja vissza a megadott értéket. |
| [Kevesebb](../logic-apps/workflow-definition-language-functions-reference.md#less) | Ellenőrizze, hogy az első érték kisebb-e a második értéknél. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Ellenőrizze, hogy az első érték kisebb-e a második értéknél, vagy egyenlő-e vele. |
| [Nem](../logic-apps/workflow-definition-language-functions-reference.md#not) | Ellenőrizze, hogy a kifejezés hamis-e. |
| [Vagy](../logic-apps/workflow-definition-language-functions-reference.md#or) | Ellenőrizze, hogy legalább egy kifejezés igaz-e. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverziós függvények

Egy érték típusának vagy formátumának a módosításhoz használhatja ezeket a konverziós függvényeket. Az értékeket például módosíthatja logikai értékről egész számra. További információ arról, hogy a Logic Apps hogyan kezeli a tartalomtípusokat az átalakítás [során: Tartalomtípusok kezelés.](../logic-apps/logic-apps-content-type.md) Az egyes függvényekkel kapcsolatos teljes hivatkozásért tekintse meg a [betűrendes listát.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64-kódolást és -dekódolást végez, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban ezeket a függvényeket a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Implicit adattípus-konverzió.](#implicit-data-conversions)

| Konverziós függvény | Feladat |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Tömböt ad vissza egyetlen megadott bemenetből. Több bemenetért lásd: [createArray.](../logic-apps/workflow-definition-language-functions-reference.md#createArray) |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Sztring base64 kódolású verzióját adja vissza. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Egy base64 kódolású sztring bináris verzióját adja vissza. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Egy base64 kódolású sztring sztringverzióját adja vissza. |
| [Bináris](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Egy bemeneti érték bináris verzióját adja vissza. |
| [logikai](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Egy bemeneti érték logikai verzióját adja vissza. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Tömb visszaadva több bemenetből. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Egy bemeneti érték adat URI-ját adja vissza. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Adat URI bináris verziójának visszaadása. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Egy adat URI sztringverzióját adja vissza. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Egy base64 kódolású sztring sztringverzióját adja vissza. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Adat URI bináris verziójának visszaadása. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Olyan sztringet ad vissza, amely a escape-karaktereket dekódolt verziókra cseréli. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Olyan sztringet ad vissza, amely az URL-címben nem biztonságos karaktereket escape-karakterekre cseréli. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Lebegőpontos számot ad vissza egy bemeneti értékhez. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Sztring egész verzióját adja vissza. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Visszaadja JavaScript Object Notation (JSON) típusú értéket vagy objektumot egy sztringhez vagy XML-hez. |
| [sztring](../logic-apps/workflow-definition-language-functions-reference.md#string) | Egy bemeneti érték sztringverzióját adja vissza. |
| [uriComponent (URI-kiegészítés)](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Adja vissza egy bemeneti érték URI-kódolású verzióját úgy, hogy az URL-nem biztonságos karaktereket escape-karakterekre cseréli. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Egy URI-kódolású sztring bináris verzióját adja vissza. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Egy URI-kódolású sztring sztringverzióját adja vissza. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Egy sztring XML-verziójának visszaadásakor. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Implicit adattípus-átalakítások

Azure Logic Apps egyes adattípusok között automatikusan vagy implicit módon konvertál, így önnek nem kell manuálisan végrehajtania ezeket az átalakításokat. Ha például nem sztring értékeket használ, ahol a sztringek bemenetként várhatók, a Logic Apps automatikusan sztringekká konvertálja a nem sztring értékeket.

Tegyük fel például, hogy egy eseményindító egy numerikus értéket ad vissza kimenetként:

`triggerBody()?['123']`

Ha ezt a numerikus kimenetet használja, ahol sztringbemenetre , például URL-címre van szükség, a Logic Apps automatikusan sztringgé konvertálja az értéket a kapcsos zárójelek ( `{}` ) írásával:

`@{triggerBody()?['123']}`

<a name="base64-encoding-decoding"></a>

### <a name="base64-encoding-and-decoding"></a>Base64-kódolás és -dekódolás

Logic Apps vagy implicit módon végzi el a base64-kódolást vagy -dekódolást, így ezeket a konverziókat nem kell manuálisan végrehajtania a megfelelő függvényekkel:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Ha ezen függvények bármelyikét manuálisan adja hozzá a munkafolyamathoz a Logikaialkalmazás-tervezőn keresztül, például a kifejezésszerkesztővel, kilép a tervezőből, és visszatér a tervezőnek, a függvény eltűnik a tervezőből, és csak a paraméterértékeket hagyja hátra. Ez a viselkedés akkor is előfordul, ha olyan eseményindítót vagy műveletet választ ki, amely ezt a függvényt használja a függvény paraméterértékének szerkesztése nélkül. Ez az eredmény csak a függvény láthatóságát befolyásolja, a hatást nem. A kódnézetben a függvényt ez nem befolyásolja. Ha azonban szerkeszti a függvény paraméterértékét, a függvény és a hatása is el lesz távolítva a kódnézetből, és csak a függvény paraméterértékét hagyja meg.

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematikai függvények

Az egész számokkal és lebegő értékekkel való munkához használhatja ezeket a matematikai függvényeket.
Az egyes függvényekkel kapcsolatos teljes referencia a betűrendes [listában található.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Matematikai függvény | Feladat |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Két szám összeadásának eredményét adja vissza. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Két szám osztásból visszaadott eredmény. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | A legmagasabb értéket adja vissza egy számkészletből vagy egy tömbből. |
| [p](../logic-apps/workflow-definition-language-functions-reference.md#min) | A legalacsonyabb értéket adja vissza egy számkészletből vagy egy tömbből. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | A maradék visszaadása két szám osztásból. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Két szám szorzásának szorzása a szorzásból. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Véletlenszerű egész számot ad vissza egy megadott tartományból. |
| [Tartomány](../logic-apps/workflow-definition-language-functions-reference.md#range) | Olyan egész tömböt ad vissza, amely egy megadott egész számból indul. |
| [Al](../logic-apps/workflow-definition-language-functions-reference.md#sub) | A második szám első számból való kivonásának eredményét adja vissza. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Dátum és időpont függvényei

A dátumok és időpontok használatával ezeket a dátum- és idő függvényeket használhatja.
Az egyes függvényekkel kapcsolatos teljes hivatkozásért tekintse meg a [betűrendes listát.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Dátum vagy idő függvény | Feladat |
| --------------------- | ---- |
| [addDays (nap hozzáadása)](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adja hozzá a napok számát az időbélyeghez. |
| [addHours (Hozzáadási óra)](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adjon hozzá néhány órát egy időbélyeghez. |
| [addMinutes (hozzáadási percek)](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adjon hozzá néhány percet az időbélyeghez. |
| [addSeconds (másodpercek hozzáadása)](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adjon hozzá néhány másodpercet egy időbélyeghez. |
| [addToTime (Hozzáadás ideje)](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adjon hozzá egy időegységet egy időbélyeghez. Lásd [még: getFutureTime.](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Alakítsa át az egyezményes világidő (UTC) időbélyegzőt a cél időzónára. |
| [convertTimeZone (időzóna konvertálása)](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Alakítsa át az időbélyeget a forrás időzónából a cél időzónává. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Alakítsa át az időbélyeget a forrás időzónából az Egyezményes világidő (UTC) szerint egyezményes világidőre. |
| [dayOfMonth (hónap dayOfMonth)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Adja vissza a hónap napját összetevőt egy időbélyegből. |
| [dayOfWeek (hét napja)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | A hét napját adja vissza egy időbélyegből. |
| [dayOfYear (év napja)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Az év napját adja vissza egy időbélyegből. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Visszaadja a dátumot egy időbélyegből. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Visszaadja az aktuális időbélyeget és a megadott időegységeket. Lásd még: [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Az aktuális időbélyeget adja vissza a megadott időegységekből levonva. Lásd [még: subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay (nap kezdete)](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Egy időbélyegző napának kezdőnapját adja vissza. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Egy időbélyeghez adja vissza az óra kezdetét. |
| [startOfMonth (hónap kezdete)](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Egy időbélyegző hónap kezdetének visszaadva. |
| [subtractFromTime (kivonás ideje)](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Időegységek kivonása időbélyegből. Lásd még: [getPastTime.](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) |
| [Kullancsok](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Visszaadja `ticks` egy megadott időbélyeg tulajdonságértékét. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Az aktuális időbélyeget adja vissza sztringként. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Munkafolyamat-függvények

Ezek a munkafolyamat-függvények a következőben segíthetnek:

* A munkafolyamat-példányok futási időben való részleteinek lekérte.
* A logikai alkalmazások vagy folyamatok példányosulatának bemenetei.
* Hivatkozhat az eseményindítók és műveletek kimenetére.

Hivatkozhat például egy művelet kimenetére, és felhasználhatja ezeket az adatokat egy későbbi műveletben.
Az egyes függvényekkel kapcsolatos teljes hivatkozásért tekintse meg a [betűrendes listát.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Munkafolyamat-függvény | Feladat |
| ----------------- | ---- |
| [Akció](../logic-apps/workflow-definition-language-functions-reference.md#action) | Az aktuális művelet futásidejű kimenetét, vagy más JSON-név-érték párok értékeit adja vissza. Lásd még a [műveleteket.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Egy művelet kimenetét adja `body` vissza futásidőben. Lásd még a [törzset.](../logic-apps/workflow-definition-language-functions-reference.md#body) |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Egy művelet kimenetét adja vissza futásidőben. Lásd: [kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) és [műveletek.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [Műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Egy művelet futásidejű kimenetét, vagy más JSON-név-érték párok értékeit adja vissza. Lásd még a [műveletet.](../logic-apps/workflow-definition-language-functions-reference.md#action)  |
| [Szervezet](#body) | Egy művelet kimenetét adja `body` vissza futásidőben. Lásd még: [actionBody.](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Hozzon létre egy tömböt a kulcs nevével egyező értékekkel az *űrlapadatok* vagy *űrlapkódolt* műveletkimenetek között. |
| [formDataValue (űrlapadatok érték)](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Egyetlen értéket ad vissza, amely megegyezik  a művelet űrlapadat- vagy űrlapkódolt kimenetében megadott *kulcsnévvel.* |
| [Cikk](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ha egy tömbre vonatkozó ismétlődő műveleten belül adja vissza a tömb aktuális elemét a művelet aktuális iterációja során. |
| [Elemek](../logic-apps/workflow-definition-language-functions-reference.md#items) | A Foreach vagy a Until cikluson belül a megadott hurokból adja vissza az aktuális elemet.|
| [iterációsindexek](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | A Until-cikluson belül az aktuális iteráció indexértékét adja vissza. Ezt a függvényt beágyazott Until ciklusok között használhatja. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | A "visszahívási URL-címet" adja vissza, amely egy eseményindítót vagy műveletet hív meg. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Egy művelet kimenetének egy több részből áll kimenetének egy adott részének törzsét adja vissza. |
| [Kimenetek](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Egy művelet kimenetét adja vissza futásidőben. |
| [Paraméterek](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Egy olyan paraméter értékét adja vissza, amely a munkafolyamat-definícióban van leírva. |
| [Eredmény](../logic-apps/workflow-definition-language-functions-reference.md#result) | Visszaadja a megadott hatókörű műveleten belüli legfelső szintű műveletek bemenetét és kimenetét, például `For_each` , `Until` és `Scope` . |
| [Ravaszt](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Az eseményindító kimenetét adja vissza futásidőben vagy más JSON-név-érték párból. Lásd még: [triggerOutputs és](#triggerOutputs) [triggerBody.](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Eseményindító kimenetének `body` visszaadása futásidőben. Lásd: [trigger.](../logic-apps/workflow-definition-language-functions-reference.md#trigger) |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Egyetlen értéket ad vissza, amely megfelel egy kulcsnévnek az *űrlapadatokban* vagy az *űrlapkódolt* eseményindító kimenetében. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Egy eseményindító többrészes kimenetének egy adott részének törzsét adja vissza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Hozzon létre egy tömböt, amelynek értékei megegyeznek egy kulcsnévvel az *űrlapadatokban* vagy az *űrlapkódolt* eseményindító kimenetében. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Az eseményindító kimenetét adja vissza futásidőben, vagy más JSON-név-érték párok értékeit. Lásd: [eseményindító.](../logic-apps/workflow-definition-language-functions-reference.md#trigger) |
| [Változók](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Visszaadja egy adott változó értékét. |
| [Munkafolyamat](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | A munkafolyamat minden adatának visszaadása a futási idő során. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-elemzési függvények

Ha egységes erőforrás-azonosítókkal (URI-kkal) dolgozik, és különböző tulajdonságértékeket kap ezekhez az URI-khoz, használja ezeket az URI-elemzési függvényeket.
Az egyes függvényekkel kapcsolatos teljes referencia a betűrendes [listában található.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| URI-elemzési függvény | Feladat |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Egy egységes `host` erőforrás-azonosító (URI) értékét adja vissza. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Egy egységes `path` erőforrás-azonosító (URI) értékét adja vissza. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Egy egységes `path` `query` erőforrás-azonosító (URI) és értékét adja vissza. |
| [URIPort (URIPort)](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Egy egységes `port` erőforrás-azonosító (URI) értékét adja vissza. |
| [URIQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Egy egységes `query` erőforrás-azonosító (URI) értékét adja vissza. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Egy egységes `scheme` erőforrás-azonosító (URI) értékét adja vissza. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulációs függvények: JSON & XML

A JSON-objektumokkal és XML-csomópontokkal való munkához használhatja ezeket a manipulációs függvényeket.
Az egyes függvényekkel kapcsolatos teljes hivatkozásért tekintse meg a [betűrendes listát.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Manipulation függvény | Feladat |
| --------------------- | ---- |
| [addProperty (hozzáadástulajdonság)](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adjon hozzá egy tulajdonságot és annak értékét vagy név-érték párját egy JSON-objektumhoz, és adja vissza a frissített objektumot. |
| [coalesce (coalesce)](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Az első nem null értéket adja vissza egy vagy több paraméterből. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Eltávolít egy tulajdonságot egy JSON-objektumból, és visszaadja a frissített objektumot. |
| [setProperty (Beállítástulajdonság beállítása)](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Állítsa be egy JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Ellenőrizze az XML-ben, hogy vannak-e olyan csomópontok vagy értékek, amelyek megfelelnek egy XPath- (XML Path Language-) kifejezésnek, és visszaadja az egyező csomópontokat vagy értékeket. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Minden függvény – betűrendes lista

Ez a szakasz betűrendben sorolja fel az összes elérhető függvényt.

<a name="action"></a>

### <a name="action"></a>action

Az aktuális *művelet futásidejű* kimenetét, vagy más JSON-név-érték párok értékeit adja vissza, amelyeket kifejezéshez rendelhet.
Alapértelmezés szerint ez a függvény a teljes műveletobjektumra hivatkozik, de opcionálisan megadhat egy tulajdonságot, amelynek a kívánt értéke.
Lásd [még: actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

A függvényt `action()` csak az alábbi helyeken használhatja:

* A `unsubscribe` webhook-művelet tulajdonsága, hogy hozzáfér az eredeti kérés `subscribe` eredményhez
* Egy `trackedProperties` művelet tulajdonsága
* Egy `do-until` művelet ciklus feltétele

```
action()
action().outputs.body.<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Tulajdonság*> | Nem | Sztring | Annak a műveletobjektumnak a neve, amelynek a kívánt értéke: **name**, **startTime,** **endTime,** **inputs, outputs,** **status,** **code,** **trackingId**, és **clientTrackingId**.  A Azure Portal ezeket a tulajdonságokat az adott futtatáselőzmények részleteinek áttekintésével találhatja meg. További információ: REST API [munkafolyamat-futtatás műveletek.](/rest/api/logic/workflowrunactions/get) |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*action-output*> | Sztring | Az aktuális művelet vagy tulajdonság kimenete |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Egy művelet kimenetét adja `body` vissza futásidőben.
Röviden : `actions('<actionName>').outputs.body` .
Lásd: [body()](#body) és [actions()](#actions).

```
actionBody('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | A művelet kívánt `body` kimenetének neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sztring | A `body` megadott művelet kimenete |
||||

*Példa*

Ez a példa a `body` Twitter-művelet kimenetét `Get user` kapja:

```
actionBody('Get_user')
```

A és a következő eredményt adja vissza:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Egy művelet kimenetét adja vissza futásidőben.  A és a röviden `actions('<actionName>').outputs` . Lásd: [actions()](#actions). A függvény a függvényt oldja fel a Logikaialkalmazás-tervezőben, ezért fontolja meg az `actionOutputs()` `outputs()` [outputs()](#outputs)függvényt a `actionOutputs()` helyett. Bár mindkét függvény ugyanúgy működik, `outputs()` ajánlott.

```
actionOutputs('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*Kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet kimenetét `Get user` kapja:

```
actionOutputs('Get_user')
```

A és a következő eredményt adja vissza:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>műveletek

Egy művelet futásidejű kimenetét vagy más JSON-név-érték párok értékeit adja vissza, amelyeket egy kifejezéshez rendelhet. Alapértelmezés szerint a függvény a teljes műveletobjektumra hivatkozik, de megadhat egy olyan tulajdonságot is, amelynek a kívánt értéke.
A rövid verziókért lásd: [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)és [body()](#body).
Az aktuális műveletről lásd: [action()](#action).

> [!TIP]
> A `actions()` függvény sztringként adja vissza a kimenetet. Ha JSON-objektumként kell dolgoznia egy visszaadott értékkel, először konvertálja a sztringértéket. A sztringértéket JSON-objektumként is átalakíthatja a [JSON-művelet elemzési műveletével.](logic-apps-perform-data-operations.md#parse-json-action)

> [!NOTE]
> Korábban a függvénnyel vagy a elemmel megadhatja, hogy egy művelet egy másik művelet kimenete alapján `actions()` `conditions` futott-e. Ha azonban explicit módon meg kell határoznia a függőségeket a műveletek között, most a függő művelet tulajdonságát kell `runAfter` használnia.
> A tulajdonsággal kapcsolatos további információkért lásd a hibák a runAfter tulajdonsággal való kezelését és a `runAfter` [et.](../logic-apps/logic-apps-workflow-definition-language.md)

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | Annak a műveletobjektumnak a neve, amelynek a kimenetét szeretné  |
| <*Tulajdonság*> | Nem | Sztring | Annak a műveletobjektumnak a neve, amelynek a kívánt értéke: **name**, **startTime,** **endTime,** **inputs, outputs,** **status,** **code,** **trackingId**, és **clientTrackingId**.  A Azure Portal ezeket a tulajdonságokat az adott futtatáselőzmények részleteinek áttekintésével találhatja meg. További információ: REST API [munkafolyamat-futtatás műveletek.](/rest/api/logic/workflowrunactions/get) |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*action-output*> | Sztring | A megadott művelet vagy tulajdonság kimenete |
||||

*Példa*

Ez a példa lekérte `status` a tulajdonságértéket a Twitter-műveletből `Get user` futásidőben:

```
actions('Get_user').outputs.body.status
```

A és a következő eredményt adja vissza: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Két szám összeadásának eredményét adja vissza.

```
add(<summand_1>, <summand_2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Igen | Egész szám, lebegő vagy vegyes | A hozzáadni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*eredményösszeg*> | Egész szám vagy lebegő érték | A megadott számok összeadásának eredménye |
||||

*Példa*

Ez a példa hozzáadja a megadott számokat:

```
add(1, 1.5)
```

A és a következő eredményt adja vissza: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays (nap hozzáadása)

Adja hozzá a napok számát egy időbélyegzőhez.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Nap*> | Igen | Egész szám | A hozzáadt napok pozitív vagy negatív száma |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú nap  |
||||

*1\. példa*

Ez a példa 10 napot ad hozzá a megadott időbélyeghez:

```
addDays('2018-03-15T00:00:00Z', 10)
```

A és a következő eredményt adja vissza: `"2018-03-25T00:00:00.0000000Z"`

*2\. példa*

Ez a példa öt napot von ki a megadott időbélyegből:

```
addDays('2018-03-15T00:00:00Z', -5)
```

A és a következő eredményt adja vissza: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours (Hozzáadási óra)

Adja hozzá az órák számát egy időbélyeghez.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Óra*> | Igen | Egész szám | A hozzáadnia kell a pozitív vagy negatív óraszámot |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú óra  |
||||

*1\. példa*

Ez a példa 10 órát ad hozzá a megadott időbélyeghez:

```
addHours('2018-03-15T00:00:00Z', 10)
```

És a következő eredményt adja vissza: "2018-03-15T10:00:00.0000000Z"

*2\. példa*

Ez a példa öt órát von ki a megadott időbélyegből:

```
addHours('2018-03-15T15:00:00Z', -5)
```

A és a következő eredményt adja vissza: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes (hozzáadási percek)

Adjon hozzá néhány percet egy időbélyeghez.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Perc*> | Igen | Egész szám | A hozzáadnia kell a percek pozitív vagy negatív számát |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú perc |
||||

*1\. példa*

Ez a példa 10 percet ad hozzá a megadott időbélyeghez:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

A és a következő eredményt adja vissza: `"2018-03-15T00:20:00.0000000Z"`

*2\. példa*

Ez a példa öt percet von ki a megadott időbélyegből:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

A és a következő eredményt adja vissza: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty (hozzáadástulajdonság)

Adjon hozzá egy tulajdonságot és annak értékét vagy név-érték párját egy JSON-objektumhoz, és adja vissza a frissített objektumot. Ha a tulajdonság már létezik futásidőben, a függvény meghiúsul, és hibát jelez.

```
addProperty(<object>, '<property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelybe hozzá szeretne adni egy tulajdonságot |
| <*Tulajdonság*> | Igen | Sztring | A hozzáadni következő tulajdonság neve: |
| <*Érték*> | Igen | Bármelyik | A tulajdonság értéke |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objektum | A megadott tulajdonsággal frissített JSON-objektum |
||||

Szülőtulajdonság meglévő tulajdonsághoz való hozzáadásához használja a `setProperty()` függvényt, ne a `addProperty()` függvényt. Ellenkező esetben a függvény csak a gyermekobjektumot adja vissza kimenetként.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelyben hozzá szeretne adni egy tulajdonságot |
| <*parent-property (szülőtulajdonság)*> | Igen | Sztring | Annak a szülőtulajdonságnak a neve, ahol hozzá szeretné adni a gyermektulajdonságokat |
| <*gyermektulajdonság*> | Igen | Sztring | A hozzáadni következő gyermektulajdonság neve: |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objektum | A frissített JSON-objektum, amelynek a tulajdonságát Ön beállította |
||||

*1\. példa*

Ez a példa hozzáadja a tulajdonságot egy JSON-objektumhoz, amely a JSON() függvény használatával sztringről `middleName` [JSON-objektumra lesz konvertálva.](#json) Az objektum már tartalmazza a `firstName` és a `surName` tulajdonságot. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Az aktuális JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

A frissített JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*2\. példa*

Ez a példa hozzáadja a gyermektulajdonságokat egy JSON-objektum meglévő tulajdonságához, amelyet a rendszer sztringből JSON-fájlba konvertál a `middleName` `customerName` [JSON() függvény](#json) használatával. A függvény hozzárendeli a megadott értéket az új tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne'))
```

Az aktuális JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

A frissített JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds (másodpercek hozzáadása)

Adjon hozzá néhány másodpercet egy időbélyeghez.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Másodperc*> | Igen | Egész szám | A hozzáadnia kell a másodpercek pozitív vagy negatív számát |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú másodperc  |
||||

*1\. példa*

Ez a példa 10 másodpercet ad hozzá a megadott időbélyeghez:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

A és a következő eredményt adja vissza: `"2018-03-15T00:00:10.0000000Z"`

*2\. példa*

Ez a példa öt másodpercet von ki a megadott időbélyegből:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

A és a következő eredményt adja vissza: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime (Hozzáadás ideje)

Adjon hozzá egy időegységet egy időbélyeghez.
Lásd [még: getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Intervallum*> | Igen | Egész szám | A hozzáadni megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az intervallummal használt *időegység:*"Másodperc", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg és a megadott számú időegység  |
||||

*1\. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

A és a következő eredményt adja vissza: `"2018-01-02T00:00:00.0000000Z"`

*2\. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Az eredményt pedig a nem kötelező "D" formátumban adja vissza: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>és

Ellenőrizze, hogy az összes kifejezés igaz-e.
Igaz értéket ad vissza, ha minden kifejezés igaz, vagy hamis értéket, ha legalább egy kifejezés hamis.

```
and(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Igen | Logikai | Az ellenőrizni következő kifejezések |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) érték visszaadva, ha minden kifejezés igaz. False (hamis) értéket ad vissza, ha legalább egy kifejezés hamis. |
||||

*1\. példa*

Ezek a példák ellenőrzik, hogy a megadott logikai értékek mind igazak-e:

```
and(true, true)
and(false, true)
and(false, false)
```

A és az alábbi eredményeket adja vissza:

* Első példa: Mindkét kifejezés igaz, ezért a értéket adja `true` vissza.
* Második példa: Az egyik kifejezés hamis, ezért a értéket adja `false` vissza.
* Harmadik példa: Mindkét kifejezés hamis, ezért a értéket adja `false` vissza.

*2\. példa*

Ezek a példák ellenőrzik, hogy a megadott kifejezések mind igazak-e:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

A és az alábbi eredményeket adja vissza:

* Első példa: Mindkét kifejezés igaz, ezért a értéket adja `true` vissza.
* Második példa: Az egyik kifejezés hamis, ezért a értéket adja `false` vissza.
* Harmadik példa: Mindkét kifejezés hamis, ezért a értéket adja `false` vissza.

<a name="array"></a>

### <a name="array"></a>array

Tömböt ad vissza egyetlen megadott bemenetből.
Több bemenetért lásd: [createArray()](#createArray).

```
array('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A tömb létrehozására vonatkozó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<érték *>]* | Tömb | Egyetlen megadott bemenetet tartalmazó tömb |
||||

*Példa*

Ez a példa egy tömböt hoz létre a "hello" sztringből:

```
array('hello')
```

A és a következő eredményt adja vissza: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Sztring base64 kódolású verzióját adja vissza.

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64-kódolást és -dekódolást végez, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban mégis ezeket a függvényeket használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
base64('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A bemeneti sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*base64-string*> | Sztring | A bemeneti sztring base64 kódolású verziója |
||||

*Példa*

Ez a példa base64 kódolású sztringgé alakítja a "hello" sztringet:

```
base64('hello')
```

A és a következő eredményt adja vissza: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Egy base64 kódolású sztring bináris verzióját adja vissza.

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64 kódolást és dekódolást végez, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban ezeket a függvényeket a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
base64ToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható base64 kódolású sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Sztring | A base64 kódolású sztring bináris verziója |
||||

*Példa*

Ez a példa bináris sztringgé alakítja az "aGVsbG8=" base64 kódolású sztringet:

```
base64ToBinary('aGVsbG8=')
```

A és a következő eredményt adja vissza:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Egy base64 kódolású sztring sztringverzióját adja vissza, gyakorlatilag dekódolva a base64-sztringet. Ezt a függvényt használja a [decodeBase64() helyett,](#decodeBase64)amely elavult.

> [!NOTE]
> Azure Logic Apps vagy implicit módon végzi el a base64-kódolást és -dekódolást, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban ezeket a függvényeket a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
base64ToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolni használt base64 kódolású sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Sztring | A base64 kódolású sztringek sztringverziója |
||||

*Példa*

Ez a példa az "aGVsbG8=" base64 kódolású sztringet csupán sztringgé alakítja:

```
base64ToString('aGVsbG8=')
```

A és a következő eredményt adja vissza: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Egy sztring bináris verzióját adja vissza.

```
binary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Sztring | A megadott sztring bináris verziója |
||||

*Példa*

Ez a példa bináris sztringgé alakítja a "hello" sztringet:

```
binary('hello')
```

A és a következő eredményt adja vissza:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body (Törzs)

Egy művelet kimenetét adja `body` vissza futásidőben. Röviden : `actions('<actionName>').outputs.body` . Lásd: [actionBody()](#actionBody) és [actions()](#actions).

```
body('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | A művelet kívánt `body` kimenetének neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sztring | A `body` megadott művelet kimenete |
||||

*Példa*

Ez a példa a `body` `Get user` Twitter-művelet kimenetét kapja meg:

```
body('Get_user')
```

A és a következő eredményt adja vissza:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>logikai

Egy érték logikai verzióját adja vissza.

```
bool(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Bármelyik | A logikai értékké alakítható érték. |
|||||

Ha a objektumot használja, az objektum értékének logikai értékké konvertálható sztringnek vagy egész számnak `bool()` kell lennie.

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| `true` vagy `false` | Logikai | A megadott érték logikai verziója. |
||||

*Kimenetek*

Ezek a példák a különböző támogatott bemeneti típusait mutatják `bool()` be:

| Bemeneti érték | Típus | Visszatérési érték |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Egész szám | `true` |
| `bool(0)` | Egész szám    | `false` |
| `bool(-1)` | Egész szám | `true` |
| `bool('true')` | Sztring | `true` |
| `bool('false')` | Sztring | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Az első nem null értéket adja vissza egy vagy több paraméterből.
Az üres sztringek, üres tömbök és üres objektumok nem null értékűek.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>... | Igen | Bármelyik, vegyes típusokat is képes | Egy vagy több elem nullértékének ellenőrzése |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Bármelyik | Az első elem vagy érték, amely nem null. Ha minden paraméter null értékű, ez a függvény null értéket ad vissza. |
||||

*Példa*

Ezek a példák a megadott értékek közül az első nem null értéket, vagy null értéket adnak vissza, ha az összes érték null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

A és az alábbi eredményeket adja vissza:

* Első példa: `true`
* Második példa: `"hello"`
* Harmadik példa: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Két vagy több sztring kombinálható, és az egyesített sztringet adja vissza.

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64 kódolást és dekódolást végez, így nem kell manuálisan végrehajtania ezeket az átalakításokat, amikor a függvényt kódolást vagy dekódolást végző adatokkal `concat()` használja:
> 
> * `concat('data:;base64,',<value>)`
> * `concat('data:,',encodeUriComponent(<value>))`
> 
> Ha azonban ezt a függvényt a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvény láthatóságára vannak hatással, a hatásra nem, hacsak nem szerkeszti a függvény paraméterértékét, amely eltávolítja a függvényt és annak hatását a kódból. 
> További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
concat('<text1>', '<text2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Igen | Sztring | Legalább két sztring kombinálható |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sztring | Az egyesített bemeneti sztringekból létrehozott sztring |
||||

*Példa*

Ez a példa a "Hello" és a "World" sztringeket kombinálja:

```
concat('Hello', 'World')
```

A és a következő eredményt adja vissza: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Ellenőrizze, hogy egy gyűjtemény rendelkezik-e egy adott elemet. True (igaz) értéket ad vissza, ha az elem megtalálható, vagy false (hamis) értéket, ha nem található. Ez a függvény megkülönbözteti a kis- és nagybetűket.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Ez a függvény a következő gyűjteménytípusokon működik:

* *Sztring* a sztring *részsztring megkeresésében*
* Egy *érték* keresését meglökő *tömb*
* Kulcs *megkeresését* meglánt szótár 

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring, tömb vagy szótár | Az ellenőrizni a gyűjteményt |
| <*Érték*> | Igen | Sztring, tömb vagy szótár | A keresni kívánt elem |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) érték visszaadása az elem megtalálása után. Ha nem található, a false (hamis) értéket adja vissza. |
||||

*1\. példa*

Ez a példa ellenőrzi a "hello world" sztringet a "world" sztringben, és igaz értéket ad vissza:

```
contains('hello world', 'world')
```

*2\. példa*

Ez a példa ellenőrzi a "hello world" sztringet a "universe" sztringben, és false (hamis) értéket ad vissza:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Alakítsa át az egyezményes világidő (UTC) időbélyegzőt a cél időzónára.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*destinationTimeZone (cél/időzóna)*> | Igen | Sztring | A célidőzóna neve. Időzónanevek: [Microsoft Windows alapértelmezett](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)időzónák , de előfordulhat, hogy el kell távolítania minden írásjelet az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Sztring | A célidőzónára konvertált időbélyeg |
||||

*1\. példa*

Ez a példa egy időbélyeget alakít át a megadott időzónára:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

A és a következő eredményt adja vissza: `"2018-01-01T00:00:00.0000000"`

*2\. példa*

Ez a példa a megadott időzónára és formátumra konvertál egy időbélyeget:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

A és a következő eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone (időzóna konvertálása)

Alakítsa át az időbélyeget a forrás időzónából a cél időzónává.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone (forrás/időzóna)*> | Igen | Sztring | A forrás időzóna neve. Időzónanevekért lásd: [Microsoft Windows alapértelmezett időzónák](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones), de előfordulhat, hogy el kell távolítania minden írásjelet az időzóna nevéből. |
| <*destinationTimeZone (cél/időzóna)*> | Igen | Sztring | A cél időzóna neve. Időzónanevekért lásd: [Microsoft Windows alapértelmezett időzónák](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones), de előfordulhat, hogy el kell távolítania minden írásjelet az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Sztring | A cél időzónára konvertált időbélyegző |
||||

*1\. példa*

Ez a példa a forrás-időzónát a cél időzónára konvertálja:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

A és a következő eredményt adja vissza: `"2018-01-01T00:00:00.0000000"`

*2\. példa*

Ez a példa a megadott időzónára és formátumra konvertál egy időzónát:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A és a következő eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Alakítsa át az időbélyeget a forrás időzónából az egyezményes világidő (UTC) szerinti világidőre.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone (forrás/időzóna)*> | Igen | Sztring | A forrás időzóna neve. Időzónanevek: [Microsoft Windows alapértelmezett](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)időzónák , de előfordulhat, hogy el kell távolítania minden írásjelet az időzóna nevéből. |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Sztring | Az UTC-hez konvertált időbélyegző |
||||

*1\. példa*

Ez a példa utc időbélyeget alakít át:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

A és a következő eredményt adja vissza: `"2018-01-01T08:00:00.0000000Z"`

*2\. példa*

Ez a példa utc időbélyeget alakít át:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

A és a következő eredményt adja vissza: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Tömb visszaadva több bemenetből.
Az egyetlen bemeneti tömbökről lásd: [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2>,* ... | Igen | Bármelyik, de nem vegyes | Legalább két elem a tömb létrehozásához |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2>,* ...] | Tömb | Az összes bemeneti elemből létrehozott tömb |
||||

*Példa*

Ez a példa egy tömböt hoz létre az alábbi bemenetek alapján:

```
createArray('h', 'e', 'l', 'l', 'o')
```

A és a következő eredményt adja vissza: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Egy sztring egységes erőforrás-azonosítóját (URI) adja vissza.

```
dataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*data-uri*> | Sztring | A bemeneti sztring adat URI-ja |
||||

*Példa*

Ez a példa egy adat URI-t hoz létre a "hello" sztringhez:

```
dataUri('hello')
```

A és a következő eredményt adja vissza: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Egy egységes adaterőforrás-azonosító (URI) bináris verzióját adja vissza.
Ezt a függvényt használja a [decodeDataUri() helyett.](#decodeDataUri)
Bár mindkét függvény ugyanúgy működik, `dataUriBinary()` ajánlott.

```
dataUriToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A konvertálni szükséges adat URI |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Sztring | Az adat URI bináris verziója |
||||

*Példa*

Ez a példa bináris verziót hoz létre ehhez az adat URI-hoz:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A és a következő eredményt adja vissza:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Egy egységes adaterőforrás-azonosító (URI) sztringverziójának visszaadása.

```
dataUriToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható adat URI |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Sztring | Az adat URI sztringverziója |
||||

*Példa*

Ez a példa létrehoz egy sztringet ehhez az adat URI-hoz:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A és a következő eredményt adja vissza: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth (hónap dayOfMonth)

A hónap napját adja vissza egy időbélyegzőből.

```
dayOfMonth('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hónap napja*> | Egész szám | A hónap napja a megadott időbélyegzőből |
||||

*Példa*

Ez a példa a hónap napszámát adja vissza ebből az időbélyegből:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

A és a következő eredményt adja vissza: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek (hét napja)

A hét napját adja vissza egy időbélyegből.

```
dayOfWeek('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hét napja*> | Egész szám | A hétnek a megadott időbélyegzőből származó napja, ahol a vasárnap 0, a hétfő 1 és így tovább |
||||

*Példa*

Ez a példa a hét napszámát adja vissza ebből az időbélyegből:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

A és a következő eredményt adja vissza: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear (év napja)

Az év napját adja vissza egy időbélyegből.

```
dayOfYear('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*az év napja*> | Egész szám | Az év napja a megadott időbélyegzőből |
||||

*Példa*

Ez a példa az év napszámát adja vissza ebből az időbélyegből:

```
dayOfYear('2018-03-15T13:27:36Z')
```

A és a következő eredményt adja vissza: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (elavult)

Ez a függvény elavult, ezért használja helyette a [base64ToString()](#base64ToString) függvényt.

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Egy egységes adaterőforrás-azonosító (URI) bináris verzióját adja vissza. A helyett fontolja meg a [dataUriToBinary()](#dataUriToBinary)használatának a `decodeDataUri()` használatát. Bár mindkét függvény ugyanúgy működik, `dataUriToBinary()` ajánlott.

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64 kódolást és dekódolást végez, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban ezeket a függvényeket a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
decodeDataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolni szükséges adat URI-sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Sztring | Az adat URI-sztring bináris verziója |
||||

*Példa*

Ez a példa az adat URI bináris verzióját adja vissza:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A és a következő eredményt adja vissza:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Olyan sztringet ad vissza, amely a escape-karaktereket dekódolt verziókra cseréli.

```
decodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolni próbált escape-karakterekkel |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Sztring | A dekódolt escape-karakterekkel frissített sztring |
||||

*Példa*

Ez a példa a sztringben szereplő escape-karaktereket dekódolt verziókra cseréli:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

A és a következő eredményt adja vissza: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Két szám osztása eredményének visszaadása. A maradék eredményért lásd: [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Osztalék*> | Igen | Egész szám vagy lebegő érték | Az osztóval *elosztani* |
| <*Osztó*> | Igen | Egész szám vagy lebegőszós érték | Az osztandót elosztó *szám,* de nem lehet 0 |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hányados eredménye*> | Egész szám vagy lebegőszós érték | Az eredmény az első szám második számmal való osztása. Ha az osztandó vagy az osztó Lebegő típussal rendelkezik, az eredmény lebegőszós típussal rendelkezik. <p><p>**Megjegyzés:** A lebegő érték egész számra konvertáláshoz próbáljon meg függvényt létrehozni és hívni [az Azure-ban a](../logic-apps/logic-apps-azure-functions.md) logikai alkalmazásból. |
||||

*1\. példa*

Mindkét példa egész szám típusú értéket ad vissza: `2`

```
div(10,5)
div(11,5)
```

*2\. példa*

Mindkét példa ezt az értéket adja vissza lebegőszós típussal: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Sztring egységes erőforrás-azonosítóval (URI) kódolt verzióját adja vissza úgy, hogy az URL-címben nem biztonságos karaktereket escape-karakterekre cseréli. Fontolja meg [az uriComponent() helyett az uriComponent()](#uriComponent)használatának a használatát. `encodeUriComponent()` Bár mindkét függvény ugyanúgy működik, `uriComponent()` ajánlott.

> [!NOTE]
> Azure Logic Apps vagy implicit módon base64 kódolást és dekódolást végez, így ezeket a konverziókat nem kell manuálisan elvégeznie a kódolási és dekódolási függvények használatával. Ha azonban ezeket a függvényeket a tervezőben is használja, váratlan renderelési viselkedést tapasztalhat a tervezőben. Ezek a viselkedések csak a függvények láthatóságára vannak hatással, azok hatására nem, hacsak nem szerkeszti a függvények paraméterértékét, amely eltávolítja a függvényeket és azok hatásait a kódból. További információ: [Base64-kódolás és -dekódolás.](#base64-encoding-decoding)

```
encodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az URI-kódolású formátumra konvertálni kívánt sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Sztring | Az URI-kódolású sztring escape-karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a sztringhez:

```
encodeUriComponent('https://contoso.com')
```

A és a következő eredményt adja vissza: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>üres

Ellenőrizze, hogy a gyűjtemény üres-e.
True (igaz) értéket ad vissza, ha a gyűjtemény üres, vagy false (hamis) értéket, ha nem üres.

```
empty('<collection>')
empty([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring, tömb vagy objektum | Az ellenőrizni következő gyűjtemény: |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) érték visszaadva, ha a gyűjtemény üres. Hamis értéket ad vissza, ha nem üres. |
||||

*Példa*

Ezek a példák ellenőrzik, hogy a megadott gyűjtemények üresek-e:

```
empty('')
empty('abc')
```

A és az alábbi eredményeket adja vissza:

* Első példa: Egy üres sztringet ad át, így a függvény a értéket adja `true` vissza.
* Második példa: Átadja az "abc" sztringet, így a függvény a értéket adja `false` vissza.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Ellenőrizze, hogy egy sztring egy adott sztringre végződik-e.
True (igaz) értéket ad vissza, ha a karakterlánc alsztring található, vagy false (hamis) értéket, ha nem található.
Ez a függvény nem megkülönbözteti a kis- és nagybetűket.

```
endsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az ellenőrizni következő sztring: |
| <*searchText (Szöveg keresése)*> | Igen | Sztring | A megtalálni fogja a záró alsztringet |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | True (igaz) érték visszaadása, ha a záró karakterlánc karakterláncot talál. Ha nem található, a false (hamis) értéket adja vissza. |
||||

*1\. példa*

Ez a példa ellenőrzi, hogy a "hello world" sztring a "world" sztringre végződik-e:

```
endsWith('hello world', 'world')
```

A és a következő eredményt adja vissza: `true`

*2\. példa*

Ez a példa ellenőrzi, hogy a "hello world" sztring a "universe" sztringre végződik-e:

```
endsWith('hello world', 'universe')
```

A és a következő eredményt adja vissza: `false`

<a name="equals"></a>

### <a name="equals"></a>egyenlő

Ellenőrizze, hogy a két érték, kifejezés vagy objektum egyenértékű-e.
True (igaz) értéket ad vissza, ha mindkettő egyenértékű, vagy false (hamis) értéket, ha azok nem egyenértékűek.

```
equals('<object1>', '<object2>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Igen | Különböző | Az összehasonlítható értékek, kifejezések vagy objektumok |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) érték visszaadva, ha mindkettő egyenértékű. Hamis értéket ad vissza, ha nem egyenértékű. |
||||

*Példa*

Ezek a példák ellenőrzik, hogy a megadott bemenetek egyenértékűek-e.

```
equals(true, 1)
equals('abc', 'abcd')
```

A és az alábbi eredményeket adja vissza:

* Első példa: Mindkét érték egyenértékű, ezért a függvény a értéket adja `true` vissza.
* Második példa: Mindkét érték nem egyenértékű, ezért a függvény a értéket adja `false` vissza.

<a name="first"></a>

### <a name="first"></a>Első

Visszaadja az első elemet egy sztringből vagy tömbből.

```
first('<collection>')
first([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring vagy tömb | A gyűjtemény, amelyben az első elem található |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Bármelyik | A gyűjtemény első elemét |
||||

*Példa*

Ezek a példák a gyűjtemények első elemét találják meg:

```
first('hello')
first(createArray(0, 1, 2))
```

És adja vissza az alábbi eredményeket:

* Első példa: `"h"`
* Második példa: `0`

<a name="float"></a>

### <a name="float"></a>float

A lebegőpontos szám sztringverzióját átalakíthatja tényleges lebegőpontos számká.
Ezt a függvényt csak akkor használhatja, ha egyéni paramétereket ad át egy alkalmazásnak, például egy logikai alkalmazásnak vagy folyamatnak.

```
float('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az a sztring, amely érvényes konvertálható lebegőpontos számmal rendelkezik |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | A megadott sztring lebegőpontos száma |
||||

*Példa*

Ez a példa egy sztringverziót hoz létre ehhez a lebegőpontos számhoz:

```
float('10.333')
```

A és a következő eredményt adja vissza: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Visszaad egy időbélyeget a megadott formátumban.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*újraformált-időbélyeg*> | Sztring | A frissített időbélyeg a megadott formátumban |
||||

*Példa*

Ez a példa a megadott formátumra konvertálja az időbélyeget:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

A és a következő eredményt adja vissza: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Olyan értékeket tartalmazó tömböt ad vissza,  amelyek megegyeznek egy művelet űrlapadat- vagy *űrlapkódolt kimenetében* megadott kulcsnévvel.

```
formDataMultiValues('<actionName>', '<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | Az a művelet, amelynek kimenete a kívánt kulcsértékkel rendelkezik |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek az értékét ön szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Tömb | Egy tömb, amely a megadott kulccsal egyező összes értéket tartalmaz |
||||

*Példa*

Ebben a példában tömböt hozunk létre a "Tárgy" kulcs értékével a megadott művelet űrlapadat- vagy űrlapkódolt kimenetében:

```
formDataMultiValues('Send_an_email', 'Subject')
```

A és a egy tömbben visszaadja a tárgy szövegét, például: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue (űrlapadatok érték)

Egyetlen értéket ad vissza, amely megegyezik  a művelet űrlapadat- vagy űrlapkódolt kimenetében megadott *kulcsnévvel.*
Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
formDataValue('<actionName>', '<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | Az a művelet, amelynek kimenete a kívánt kulcsértékkel rendelkezik |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcsban található érték  |
||||

*Példa*

Ez a példa egy sztringet hoz létre a "Tárgy" kulcs értékében a megadott művelet űrlapadat- vagy űrlapkódolt kimenetében:

```
formDataValue('Send_an_email', 'Subject')
```

A és a sztringként adja vissza a tárgy szövegét, például: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber (formátumszám)

Számot ad vissza sztringként a megadott formátum alapján.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szám*> | Igen | Egész szám vagy dupla | A formázni kívánt érték. |
| <*Formátum*> | Igen | Sztring | Összetett formátumsring, amely meghatározza a használni kívánt formátumot. A támogatott numerikus formátumsringekért lásd: [A által](/dotnet/standard/base-types/standard-numeric-format-strings)támogatott szabványos numerikusformátum-sztringek. `number.ToString(<format>, <locale>)` |
| <*Locale*> | Nem | Sztring | A által támogatott területi `number.ToString(<format>, <locale>)` hely. Ha nincs megadva, az alapértelmezett érték `en-us` . |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*formázott szám*> | Sztring | A megadott szám sztringként a megadott formátumban. Ezt a visszaadott értéket a vagy a értékre is át `int` lehet castálni. `float` |
||||

*1\. példa*

Tegyük fel, hogy a számot szeretné `1234567890` formázni. Ez a példa az "1,234,567,890.00" sztringként formázza a számot.

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*2. példa"

Tegyük fel, hogy a számot szeretné `1234567890` formázni. Ez a példa az "1.234.567.890,00" sztringre formázza a számot.

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*3\. példa*

Tegyük fel, hogy a számot szeretné `17.35` formázni. Ez a példa a számot a "$17.35" sztringre formázza.

```
formatNumber(17.35, 'C2')
```

*4\. példa*

Tegyük fel, hogy a számot szeretné `17.35` formázni. Ez a példa a számot a "17,35 kr" sztringre formázza.

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Visszaadja az aktuális időbélyeget és a megadott időegységeket.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Intervallum*> | Igen | Egész szám | A hozzáadni megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az intervallummal használt *időegység:*"Másodperc", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzónára vonatkozó információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az aktuális időbélyeg és a megadott számú időegység |
||||

*1\. példa*

Tegyük fel, hogy az aktuális időbélyeg a következő: "2018-03-01T00:00:00.0000000Z".
Ez a példa öt napot ad hozzá az időbélyeghez:

```
getFutureTime(5, 'Day')
```

A és a következő eredményt adja vissza: `"2018-03-06T00:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy az aktuális időbélyegző a következő: "2018-03-01T00:00:00.0000000Z".
Ez a példa öt napot ad hozzá, és "D" formátumra konvertálja az eredményt:

```
getFutureTime(5, 'Day', 'D')
```

A és a következő eredményt adja vissza: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime (getPastTime)

Visszaadja az aktuális időbélyeget a megadott időegységekkel csökkentve.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Intervallum*> | Igen | Egész szám | A kivonni megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | A következő időközzel használható időegység: "Másodperc", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzónára vonatkozó információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az aktuális időbélyeg a megadott számú időegységből levonva |
||||

*1\. példa*

Tegyük fel, hogy az aktuális időbélyegző a következő: "2018-02-01T00:00:00.0000000Z".
Ez a példa öt napot von ki az időbélyegből:

```
getPastTime(5, 'Day')
```

A és a következő eredményt adja vissza: `"2018-01-27T00:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy az aktuális időbélyegző a következő: "2018-02-01T00:00:00.0000000Z".
Ez a példa kivon öt napot, és "D" formátumba konvertálja az eredményt:

```
getPastTime(5, 'Day', 'D')
```

A és a következő eredményt adja vissza: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Ellenőrizze, hogy az első érték nagyobb-e a második értéknél.
True (igaz) értéket ad vissza, ha az első érték nagyobb, vagy false (hamis) értéket, ha kisebb.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész szám, lebegőszós érték vagy sztring | Az első érték, amely azt ellenőrzi, hogy nagyobb-e a másodiknál |
| <*compareTo (összehasonlítás)*> | Igen | Egész szám, lebegőszós érték vagy sztring | Az összehasonlítási érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | Igaz értéket ad vissza, ha az első érték nagyobb, mint a második érték. False (hamis) értéket ad vissza, ha az első érték egyenlő vagy kisebb, mint a második érték. |
||||

*Példa*

Ezek a példák ellenőrzik, hogy az első érték nagyobb-e a másodiknál:

```
greater(10, 5)
greater('apple', 'banana')
```

És adja vissza az alábbi eredményeket:

* Első példa: `true`
* Második példa: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Ellenőrizze, hogy az első érték nagyobb-e a második értéknél, vagy egyenlő-e vele.
True (igaz) értéket ad vissza, ha az első érték nagyobb vagy egyenlő, vagy false (hamis) értéket, ha az első érték kisebb.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész szám, lebegő érték vagy sztring | Az első érték, amely azt ellenőrzi, hogy a második értéknél nagyobb vagy egyenlő-e |
| <*compareTo*> | Igen | Egész szám, lebegő lebegőszós érték vagy sztring | Az összehasonlítási érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték. Hamis értéket ad vissza, ha az első érték kisebb, mint a második érték. |
||||

*Példa*

Ezek a példák ellenőrzik, hogy az első érték nagyobb vagy egyenlő-e a második értékkel:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

És adja vissza az alábbi eredményeket:

* Első példa: `true`
* Második példa: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Hozzon létre egy globálisan egyedi azonosítót (GUID) sztringként, például: "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Emellett az alapértelmezett "D" formátumtól eltérő formátumot is megadhat a GUID azonosítóhoz, amely 32 számjegyből áll, kötőjellel elválasztva.

```
guid('<format>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Formátum*> | Nem | Sztring | A [visszaadott GUID](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) egyetlen formátum- határozza meg. Alapértelmezés szerint a formátum "D", de használhatja az "N", "D", "B", "P" vagy "X" formátumot. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*GUID-érték*> | Sztring | Egy véletlenszerűen generált GUID |
||||

*Példa*

Ez a példa ugyanazt a GUID azonosítót hozza létre, de 32 számjegyből, kötőjellel elválasztva, zárójelek között:

```
guid('P')
```

A és a következő eredményt adja vissza: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Ellenőrizze, hogy egy kifejezés igaz-e vagy hamis. Az eredmény alapján adja vissza a megadott értéket. A paraméterek kiértékelése balról jobbra történik.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kifejezés*> | Igen | Logikai | Az ellenőrizni következő kifejezés: |
| <*valueIfTrue*> | Igen | Bármelyik | A visszaadott érték, ha a kifejezés igaz |
| <*valueIfFalse*> | Igen | Bármelyik | A kifejezés hamis értékkel való visszaadott értéke |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*megadott-visszatérési érték*> | Bármelyik | A megadott érték, amely annak alapján ad vissza értéket, hogy a kifejezés igaz vagy hamis |
||||

*Példa*

Ez a példa azért `"yes"` ad vissza értéket, mert a megadott kifejezés true (igaz) értéket ad vissza.
Ellenkező esetben a példa a értéket adja `"no"` vissza:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf (indexof)

Egy karakterlánc alsztring kezdőpozícióját vagy indexértékét adja vissza.
Ez a függvény nem megkülönbözteti a kis- és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
indexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A sztring, amely a megtalált sztring részsztringet rendelkezik |
| <*searchText (Szöveg keresése)*> | Igen | Sztring | A megtalált karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*index-érték*>| Egész szám | A megadott karakterláncsztring kezdőpozíciója vagy indexértéke. <p>Ha a sztring nem található, adja vissza a -1 számot. |
||||

*Példa*

Ez a példa megkeresi a "hello world" sztring "world" részsztring kezdő indexértékét:

```
indexOf('hello world', 'world')
```

A és a következő eredményt adja vissza: `6`

<a name="int"></a>

### <a name="int"></a>int

Egy sztring egész számra vonatkozó verzióját adja vissza.

```
int('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*egész szám eredménye*> | Egész szám | A megadott sztring egész verziója |
||||

*Példa*

Ez a példa egy egész számos verziót hoz létre a "10" sztringhez:

```
int('10')
```

A és a következő eredményt adja vissza: `10`

<a name="item"></a>

### <a name="item"></a>item

Ha egy tömb ismétlődő műveletében használja, a művelet aktuális iterációja során adja vissza a tömb aktuális elemét.
Az értékek az elem tulajdonságaiból is lekértek.

```
item()
```

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Bármelyik | A tömb aktuális elemét a művelet aktuális iterációja számára |
||||

*Példa*

Ez a példa a "Send_an_email" művelet aktuális üzenetének elemét kapja meg `body` az egyes for-each ciklus aktuális iterációiban:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Elemek

Az egyes ciklusok aktuális elemének visszaadása egy for-each ciklusban.
Ezt a függvényt a for-each ciklusban használja.

```
items('<loopName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Igen | Sztring | A for-each ciklus neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Cikk*> | Bármelyik | A megadott for-each ciklus aktuális ciklusának elemét |
||||

*Példa*

Ez a példa lekérte az aktuális elemet a megadott for-each ciklusból:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterációsindexek

Az aktuális iteráció indexértékét adja vissza egy Until hurokban. Ezt a függvényt beágyazott Until ciklusok között használhatja. 

```
iterationIndexes('<loopName>')
```

| Paraméter | Kötelező | Típus | Leírás | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Igen | Sztring | A Until-ciklus neve | 
||||| 

| Visszatérési érték | Típus | Leírás | 
| ------------ | ---- | ----------- | 
| <*Index*> | Egész szám | Az aktuális iteráció indexértéke a megadott Until cikluson belül | 
|||| 

*Példa* 

Ebben a példában egy számlálóváltozót hozunk létre, és a Until ciklus minden iterációja során eggyel növeli a változó értékét, amíg a számláló értéke el nem éri az ötöt. A példa egy változót is létrehoz, amely nyomon követi az egyes iterációk aktuális indexét. Az Until ciklusban a példa minden iteráció során megnöveli a számláló értékét, majd hozzárendeli a számláló értékét az aktuális indexértékhez, majd a számlálót. A hurokban ez a példa az aktuális iterációs indexre hivatkozik a függvény `iterationIndexes` használatával:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Visszaadja JavaScript Object Notation (JSON) típusú értéket, objektumot vagy objektumtömböt egy sztringhez vagy XML-hez.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> A kimenet struktúráját meghatározó XML-séma nélkül a függvény olyan eredményeket ad vissza, amelyekben a struktúra nagy mértékben eltér a várt formátumtól, a bemenettől függően.
>  
> Ez a viselkedés miatt ez a függvény nem használható olyan forgatókönyvekhez, ahol a kimenetnek egy jól meghatározott szerződésnek kell megfelelnie, például kritikus fontosságú üzleti rendszerekben vagy megoldásokban.

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring vagy XML | A konvertálni szükséges sztring vagy XML |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*JSON-eredmény*> | Natív JSON-típus, objektum vagy tömb | A JSON natív típusának értéke, objektuma vagy objektumtömbje a bemeneti sztringből vagy XML-ből. <p><p>– Ha olyan XML-t ad meg, amely a gyökérelem egyetlen gyermekelemével rendelkezik, a függvény egyetlen JSON-objektumot ad vissza a gyermekelemhez. <p> – Ha olyan XML-t ad meg, amely több gyermekelemet tartalmaz a gyökérelemben, a függvény egy olyan tömböt ad vissza, amely JSON-objektumokat tartalmaz ezekhez a gyermekelemekhez. <p>– Ha a sztring null értékű, a függvény egy üres objektumot ad vissza. |
||||

*1\. példa*

Ez a példa ezt a sztringet JSON-értékké alakítja:

```
json('[1, 2, 3]')
```

A és a következő eredményt adja vissza: `[1, 2, 3]`

*2\. példa*

Ez a példa ezt a sztringet JSON formátumba konvertálja:

```
json('{"fullName": "Sophia Owen"}')
```

A és a következő eredményt adja vissza:

```json
{
  "fullName": "Sophia Owen"
}
```

*3\. példa*

Ebben a példában a és a függvény használatával konvertáljuk a gyökérelem egyetlen gyermekelemét a gyermekelemhez elnevezett `json()` `xml()` JSON-objektumká: `person`

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

A és a következő eredményt adja vissza:

```json
{
   "?xml": { 
      "@version": "1.0" 
   },
   "root": {
      "person": {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      }
   }
}
```

*4\. példa*

Ez a példa a és a függvényt használja a gyökérelem több gyermekelemét tartalmazó XML átalakítására egy nevű tömbjére, amely ezen gyermekelemek `json()` `xml()` `person` JSON-objektumát tartalmazza:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

A és a következő eredményt adja vissza:

```json
{
   "?xml": {
      "@version": "1.0"
   },
   "root": {
      "person": [
         {
            "@id": "1",
            "name": "Sophia Owen",
            "occupation": "Engineer"
         },
         {
            "@id": "2",
            "name": "John Doe",
            "occupation": "Engineer"
         }
      ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Kereszteződés

Olyan gyűjteményt ad *vissza, amely csak a* megadott gyűjtemények közös elemeit tartalmazza.
Ahhoz, hogy megjelenjen az eredményben, egy elemnek meg kell jelennie a függvénynek átadott összes gyűjteményben.
Ha egy vagy több elemnek ugyanaz a neve, az eredményben megjelenik az utolsó ilyen nevű elem.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Igen | Tömb vagy objektum, de nem mindkettő | A gyűjtemények, amelyekből csak a *gyakori* elemeket szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*common-items (gyakori elemek)*> | Tömb vagy objektum | Olyan gyűjtemény, amely csak a megadott gyűjtemények közös elemeivel rendelkezik |
||||

*Példa*

Ez a példa megkeresi a tömbök gyakori elemeit:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

A és egy tömböt ad *vissza, amely csak a következő* elemeket tartalmazza: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>csatlakozás

Olyan sztringet ad vissza, amely egy tömb összes elemét tartalmazza, és az egyes karaktereket *elválasztó karakter választja el egymástól.*

```
join([<collection>], '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Tömb | Az a tömb, amely az illesztniandó elemeket tartalmazza |
| <*Határoló*> | Igen | Sztring | Az eredményül kapott sztring egyes karakterei között megjelenő elválasztó |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*char1 (1. karakter)* >< *elválasztójel* >< *char2* >< *elválasztó*>... | Sztring | A megadott tömb összes elemének eredményül kapott sztringje |
||||

*Példa*

Ez a példa egy sztringet hoz létre a tömb összes eleméről, és a megadott karaktert adja meg elválasztó karakterként:

```
join(createArray('a', 'b', 'c'), '.')
```

A és a következő eredményt adja vissza: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Utolsó

Egy gyűjtemény utolsó elemének visszaadása.

```
last('<collection>')
last([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring vagy tömb | A gyűjtemény, amelyben az utolsó elem található |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Sztring vagy tömb | A gyűjtemény utolsó elemét |
||||

*Példa*

Az alábbi példák a gyűjtemények utolsó elemét találják meg:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

A és az alábbi eredményeket adja vissza:

* Első példa: `"d"`
* Második példa: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf (lastIndexOf)

Egy karakterláncsztring utolsó előfordulásának kezdőpozícióját vagy indexértékét adja vissza. Ez a függvény nem megkülönbözteti a kis- és nagybetűket, és az indexek a 0 számmal kezdődnek.

```json
lastIndexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A sztring, amely a megtalált sztring részsztringet rendelkezik |
| <*searchText (Szöveg keresése)*> | Igen | Sztring | A megtalált karakterlánc |
|||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*záróindex-érték*> | Egész szám | A megadott karakterlánc utolsó előfordulásának kezdőpozíciója vagy indexértéke. |
|||

Ha a sztring vagy a sztring részsztring értéke üres, a következő viselkedés történik:

* Ha csak a sztringérték üres, a függvény a értéket adja `-1` vissza.

* Ha a sztring és a sztring részsztring értékei is üresek, a függvény a értéket adja `0` vissza.

* Ha csak a sztring részsztring értéke üres, a függvény a sztring 1-et nem érő hosszát adja vissza.

*Példák*

Ez a példa megkeresi a sztring sztring részsztring utolsó előfordulásának kezdő `world` `hello world hello world` indexértékét. A visszaadott `18` eredmény:

```json
lastIndexOf('hello world hello world', 'world')
```

Ebben a példában hiányzik a sztring részsztring paramétere, és a értéket adja vissza, mert a bemeneti sztring ( ) 1-es negatív értéke nagyobb, mint `22` `23` 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>hossz

Egy gyűjtemény elemeinek számát adja vissza.

```
length('<collection>')
length([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring vagy tömb | A megszámolni és megszámolni a gyűjteményt |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*length-or-count (hossz vagy darabszám)*> | Egész szám | A gyűjtemény elemeinek száma |
||||

*Példa*

Ezek a példák megszámolják a gyűjtemények elemeinek számát:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

És adja vissza a következő eredményt: `4`

<a name="less"></a>

### <a name="less"></a>less

Ellenőrizze, hogy az első érték kisebb-e a második értéknél.
True (igaz) értéket ad vissza, ha az első érték kisebb, vagy false (hamis) értéket, ha az első érték nagyobb.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész szám, lebegőszós érték vagy sztring | Az első érték, amely azt ellenőrzi, hogy kisebb-e a másodiknál |
| <*compareTo*> | Igen | Egész szám, lebegő lebegőszós érték vagy sztring | Az összehasonlító elem |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) értéket ad vissza, ha az első érték kisebb, mint a második érték. Hamis értéket ad vissza, ha az első érték egyenlő vagy nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák ellenőrzik, hogy az első érték kisebb-e a második értéknél.

```
less(5, 10)
less('banana', 'apple')
```

És adja vissza az alábbi eredményeket:

* Első példa: `true`
* Második példa: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Ellenőrizze, hogy az első érték kisebb vagy egyenlő-e a második értékkel.
Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték nagyobb.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Egész szám, lebegő vagy sztring | Az első érték, amely azt ellenőrzi, hogy a második értéknél kisebb vagy egyenlő-e |
| <*compareTo*> | Igen | Egész szám, lebegő lebegőszós érték vagy sztring | Az összehasonlítási elem |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | True (igaz) értéket ad vissza, ha az első érték kisebb vagy egyenlő a második értékkel. Hamis értéket ad vissza, ha az első érték nagyobb, mint a második érték. |
||||

*Példa*

Ezek a példák azt ellenőrzik, hogy az első érték kisebb vagy egyenlő-e a második értékkel.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

És adja vissza az alábbi eredményeket:

* Első példa: `true`
* Második példa: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Adja vissza a "visszahívási URL-címet", amely egy eseményindítót vagy műveletet hív meg.
Ez a függvény csak a **HttpWebhook** és **az ApiConnectionWebhook** összekötőtípusok eseményindítókkal és műveletekkel működik, a **Manuális,** Ismétlődés,  **HTTP** és **APIConnection** típusokkal nem.

```
listCallbackUrl()
```

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*visszahívási URL-cím*> | Sztring | Eseményindító vagy művelet visszahívási URL-címe |
||||

*Példa*

Ez a példa egy minta visszahívási URL-címet mutat be, amely a függvény által visszaadható:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max.

A lista vagy tömb legmagasabb értékét adja vissza olyan számokkal, amelyek mindkét végén befoglalóak.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Igen | Egész szám, lebegőszós érték vagy mindkettő | Az a számkészlet, amelyből a legmagasabb értéket szeretné kihozni |
| [<*number1*>, <*number2*>, ...] | Igen | Tömb – Egész szám, lebegő érték vagy mindkettő | A számok tömbje, amelyből a legmagasabb értéket szeretné kihozni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*max-value (maximális érték)*> | Egész szám vagy lebegőszós érték | A legmagasabb érték a megadott tömbben vagy számkészletben |
||||

*Példa*

Ezek a példák a számokból és a tömbből a legmagasabb értéket kapják:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

És adja vissza ezt az eredményt: `3`

<a name="min"></a>

### <a name="min"></a>p

A legkisebb értéket adja vissza egy számkészletből vagy egy tömbből.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Igen | Egész szám, lebegő vagy mindkettő | Az a számkészlet, amelyből a legalacsonyabb értéket szeretné kihozni |
| [<*number1*>, <*number2*>, ...] | Igen | Tömb – Egész szám, lebegő érték vagy mindkettő | Az a számtömb, amelyből a legalacsonyabb értéket szeretné kihozni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*min-value*> | Egész szám vagy lebegő érték | A megadott számok vagy tömb legalacsonyabb értéke |
||||

*Példa*

Ezek a példák a számok és a tömb legalacsonyabb értékét kapják meg:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

És adja vissza ezt az eredményt: `1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Két szám osztásból való maradékát adja vissza.
Az egész számra kapott eredményért lásd: [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Osztalék*> | Igen | Egész szám vagy lebegő érték | Az osztóval *elosztani* |
| <*Osztó*> | Igen | Egész szám vagy lebegőszós érték | Az osztandót elosztó *szám,* de nem lehet 0. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*modulo-eredmény*> | Egész szám vagy lebegőszós érték | Az első szám második számmal való osztásának maradéka |
||||

*Példa*

Ez a példa az első számot a második számmal osztja el:

```
mod(3, 2)
```

És adja vissza a következő eredményt: `1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Két szám szorzásának szorzása a szorzásból.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Igen | Egész szám vagy lebegőszós érték | A *multiplicand2 többszörös számmal megszorzva* |
| <*multiplicand2*> | Igen | Egész szám vagy lebegőszós érték | A többszörös *többplicands1 szám* |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*termék eredménye*> | Egész szám vagy lebegőszós érték | Az első szám második számmal való szorzásának szorzása |
||||

*Példa*

Ezek a példák az első számot a második számmal többszörösék:

```
mul(1, 2)
mul(1.5, 2)
```

És adja vissza az alábbi eredményeket:

* Első példa: `2`
* Második példa `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Egy művelet kimenetének egy több részből áll kimenetének egy adott részének törzsét adja vissza.

```
multipartBody('<actionName>', <index>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | Annak a műveletnek a neve, amely több részből áll kimenettel |
| <*Index*> | Igen | Egész szám | A kívánt rész indexértéke |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Szervezet*> | Sztring | A megadott rész törzse |
||||

<a name="not"></a>

### <a name="not"></a>not

Ellenőrizze, hogy egy kifejezés hamis-e.
True (igaz) értéket ad vissza, ha a kifejezés hamis, vagy false (hamis) értéket, ha igaz.

```json
not(<expression>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kifejezés*> | Igen | Logikai | Az ellenőrizni következő kifejezés: |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) értéket ad vissza, ha a kifejezés hamis. False (hamis) értéket ad vissza, ha a kifejezés igaz. |
||||

*1\. példa*

Ezek a példák ellenőrzik, hogy a megadott kifejezések hamisak-e:

```json
not(false)
not(true)
```

És adja vissza az alábbi eredményeket:

* Első példa: A kifejezés hamis, ezért a függvény a értéket adja `true` vissza.
* Második példa: A kifejezés igaz, ezért a függvény a értéket adja `false` vissza.

*2\. példa*

Ezek a példák ellenőrzik, hogy a megadott kifejezések hamisak-e:

```json
not(equals(1, 2))
not(equals(1, 1))
```

És adja vissza az alábbi eredményeket:

* Első példa: A kifejezés hamis, ezért a függvény a értéket adja `true` vissza.
* Második példa: A kifejezés igaz, ezért a függvény a értéket adja `false` vissza.

<a name="or"></a>

### <a name="or"></a>vagy

Ellenőrizze, hogy legalább egy kifejezés igaz-e.
True (igaz) értéket ad vissza, ha legalább egy kifejezés igaz, vagy false (hamis) értéket, ha az összes hamis.

```
or(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Igen | Logikai | Az ellenőrizni következő kifejezések |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai | True (igaz) érték visszaadva, ha legalább egy kifejezés igaz. Hamis értéket ad vissza, ha minden kifejezés hamis. |
||||

*1\. példa*

Ezek a példák ellenőrzik, hogy legalább egy kifejezés igaz-e:

```json
or(true, false)
or(false, false)
```

És adja vissza az alábbi eredményeket:

* Első példa: Legalább egy kifejezés igaz, ezért a függvény a értéket adja `true` vissza.
* Második példa: Mindkét kifejezés hamis, ezért a függvény a értéket adja `false` vissza.

*2\. példa*

Ezek a példák ellenőrzik, hogy legalább egy kifejezés igaz-e:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

És adja vissza az alábbi eredményeket:

* Első példa: Legalább egy kifejezés igaz, ezért a függvény a értéket adja `true` vissza.
* Második példa: Mindkét kifejezés hamis, ezért a függvény a értéket adja `false` vissza.

<a name="outputs"></a>

### <a name="outputs"></a>Kimenetek

A művelet kimenetét adja vissza futásidőben. Ezt a függvényt használja a helyett, amely a függvényt oldja fel a `actionOutputs()` `outputs()` Logikaialkalmazás-tervezőben. Bár mindkét függvény ugyanúgy működik, `outputs()` ajánlott.

```
outputs('<actionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*actionName (művelet neve)*> | Igen | Sztring | A művelet kívánt kimenetének neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*Kimeneti*> | Sztring | A megadott művelet kimenete |
||||

*Példa*

Ez a példa a Twitter-művelet kimenetét `Get user` kapja:

```
outputs('Get_user')
```

A és a következő eredményt adja vissza:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Egy olyan paraméter értékét adja vissza, amely a munkafolyamat-definícióban van leírva.

```
parameters('<parameterName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*parameterName (paraméternév)*> | Igen | Sztring | Annak a paraméternek a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*paraméter-érték*> | Bármelyik | A megadott paraméter értéke |
||||

*Példa*

Tegyük fel, hogy ezzel a JSON-értékkel bír:

```json
{
  "fullName": "Sophia Owen"
}
```

Ez a példa a megadott paraméter értékét adja meg:

```
parameters('fullName')
```

A és a következő eredményt adja vissza: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Véletlenszerű egész számot ad vissza egy megadott tartományból, amely csak a kezdőpontnál tér vissza.

```
rand(<minValue>, <maxValue>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*minValue (minimális érték)*> | Igen | Egész szám | A tartomány legalacsonyabb egész száma |
| <*maxValue (maximális érték)*> | Igen | Egész szám | A függvény által visszaadható tartomány legnagyobb egészét követő egész szám |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*véletlenszerű eredmény*> | Egész szám | A megadott tartományból visszaadott véletlenszerű egész szám |
||||

*Példa*

Ez a példa egy véletlenszerű egész számot kap a megadott tartományból, kivéve a maximális értéket:

```
rand(1, 5)
```

A és a következő számok valamelyikét adja vissza eredményként: `1` `2` , , , `3` vagy `4`

<a name="range"></a>

### <a name="range"></a>Tartomány

Olyan egész tömböt ad vissza, amely egy megadott egész számból indul.

```
range(<startIndex>, <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Igen | Egész szám | Egész szám, amely első elemként elindítja a tömböt |
| <*Számít*> | Igen | Egész szám | A tömbben az egész számok száma |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| *[<-eredménytartomány>]* | Tömb | A megadott indextől kezdődő egész számokkal megadott tömb |
||||

*Példa*

Ez a példa egy egész tömböt hoz létre, amely a megadott indexből indul, és a megadott számú egész számmal rendelkezik:

```
range(1, 4)
```

A és a következő eredményt adja vissza: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Helyettesít

Cserélje le a sztring alsztringet a megadott sztringre, és adja vissza az eredménysztringet. Ez a függvény megkülönbözteti a kis- és nagybetűket.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A sztring, amely a lecserélni következő sztringet rendelkezik: |
| <*oldText (régi szöveg)*> | Igen | Sztring | A lecserélni következő karakterlánc |
| <*newText (új szöveg)*> | Igen | Sztring | A helyettesítő sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített szöveg*> | Sztring | A sztring részsztring cseréje után frissített sztring <p>Ha a sztring alsztring nem található, adja vissza az eredeti sztringet. |
||||

*Példa*

Ez a példa megkeresi a "régi" sztringet a "régi sztringben", és az "old" sztringet "new" (új) szövegre cseréli:

```
replace('the old string', 'old', 'new')
```

A és a következő eredményt adja vissza: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Eltávolít egy tulajdonságot egy objektumból, és visszaadja a frissített objektumot. Ha az eltávolítani próbáló tulajdonság nem létezik, a függvény az eredeti objektumot adja vissza.

```
removeProperty(<object>, '<property>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelyről el szeretné távolítani a tulajdonságot |
| <*Tulajdonság*> | Igen | Sztring | Az eltávolítható tulajdonság neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objektum | A JSON-objektum frissítése a megadott tulajdonság nélkül |
||||

Gyermektulajdonság meglévő tulajdonságból való eltávolításához használja a következő szintaxist:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | Az a JSON-objektum, amelynek tulajdonságát el szeretné távolítani |
| <*parent-property (szülőtulajdonság)*> | Igen | Sztring | Az eltávolítani kívánt gyermektulajdonság szülőtulajdonságának neve |
| <*gyermektulajdonság*> | Igen | Sztring | Az eltávolítható gyermektulajdonság neve |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objektum | A frissített JSON-objektum, amelynek gyermektulajdonságát eltávolította |
||||

*1\. példa*

Ez a példa eltávolítja a tulajdonságot egy JSON-objektumból, amelyet egy sztringből JSON-fájlba konvertál a `middleName` rendszer a [JSON()](#json) függvény használatával, és visszaadja a frissített objektumot:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Az aktuális JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

A frissített JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*2\. példa*

Ez a példa eltávolítja a gyermektulajdonságokat egy JSON-objektum szülőtulajdonságaiból, amelyet a rendszer egy sztringből JSON-fájlba konvertál `middleName` `customerName` a [JSON()](#json) függvény használatával, és visszaadja a frissített objektumot:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Az aktuális JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

A frissített JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Visszaadja a megadott hatókörrel megjelölt művelet legfelső szintű műveletének eredményeit, például egy `For_each` `Until` , vagy `Scope` műveletből. A függvény egyetlen paramétert fogad el, amely a hatókör neve, és egy tömböt ad vissza, amely a hatókör első szintű műveleteiből származó információkat `result()` tartalmazza. Ezek a műveletobjektumok tartalmazzák a függvény által visszaadott attribútumokat, például a művelet kezdési idejét, záró idejét, állapotát, bemenetét, korrelációs értékét és `actions()` kimenetét.

> [!NOTE]
> Ez a függvény csak *a* hatókörrel kapcsolatos művelet első szintű műveleteiből ad vissza információt, mélyebb beágyazott műveletekből, például kapcsoló- vagy feltételműveletekből nem.

Ezzel a függvényrel például lekérte a sikertelen műveletek eredményeit, hogy diagnosztizálni és kezelni tudja a kivételeket. További információkért lásd: Környezet és [a hibák eredményeinek lekértése.](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)

```
result('<scopedActionName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Igen | Sztring | Annak a hatókörrel alá tartozó műveletnek a neve, ahol a hatókör legfelső szintű műveleteiből származó bemeneteket és kimeneteket szeretné |
||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*array-object*> | Tömbobjektum | Egy tömb, amely bemenetek és kimenetek tömbit tartalmazza a megadott hatókörben lévő összes felső szintű műveletből |
||||

*Példa*

Ez a példa egy hurokban lévő HTTP-művelet minden iterációja bemenetét és kimenetét adja vissza a műveletben a `For_each` `result()` függvény `Compose` használatával:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

A példa által visszaadott tömb így néz ki, ahol a külső objektum a művelet egyes iterációiból származó bemeneteket `outputs` és `For_each` kimeneteket tartalmazza.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty (Beállítástulajdonság beállítása)

Állítsa be a JSON-objektum tulajdonságának értékét, és adja vissza a frissített objektumot. Ha a beállítani próbáló tulajdonság nem létezik, a tulajdonság hozzá lesz adva az objektumhoz. Új tulajdonság hozzáadásához használja az [addProperty() függvényt.](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelynek a tulajdonságát be szeretné állítani |
| <*Tulajdonság*> | Igen | Sztring | A meglévő vagy új tulajdonság neve, amely be lesz állítva |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

A gyermekobjektum gyermektulajdonságának beállításához használjon inkább beágyazott `setProperty()` hívást. Ellenkező esetben a függvény csak a gyermekobjektumot adja vissza kimenetként.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Objektum*> | Igen | Objektum | A JSON-objektum, amelynek a tulajdonságát be szeretné állítani |
| <*parent-property (szülőtulajdonság)*> | Igen | Sztring | A szülőtulajdonság neve a beállítani kívánt gyermektulajdonságokkal |
| <*gyermektulajdonság*> | Igen | Sztring | A gyermektulajdonság neve, amelynél be kell állítani |
| <*Érték*> | Igen | Bármelyik | A megadott tulajdonsághoz beállított érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objektum | A frissített JSON-objektum, amelynek a tulajdonságát Ön beállította |
||||

*1\. példa*

Ez a példa egy JSON-objektum tulajdonságát állítja be, amelyet a rendszer a JSON() függvény használatával konvertál egy sztringről `surName` [JSON-fájlra.](#json) A függvény hozzárendeli a megadott értéket a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Az aktuális JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

A frissített JSON-objektum a következő:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*2\. példa*

Ez a példa egy JSON-objektum szülőtulajdonságának gyermektulajdonságát állítja be, amelyet a rendszer a JSON() függvény használatával konvertál sztringről `surName` `customerName` [JSON-fájlra.](#json) A függvény hozzárendeli a megadott értéket a tulajdonsághoz, és visszaadja a frissített objektumot:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Az aktuális JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

A frissített JSON-objektum a következő:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Ugrál

Távolítson el elemeket egy gyűjtemény elejéről, és adja vissza *az összes többi elemet.*

```
skip([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Tömb | Az a gyűjtemény, amelynek elemeit el szeretné távolítani |
| <*Számít*> | Igen | Egész szám | Pozitív egész szám az elülső eltávolítható elemek számáról |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<frissített *gyűjtemény>]* | Tömb | A megadott elemek eltávolítása után frissített gyűjtemény |
||||

*Példa*

Ez a példa eltávolít egy elemet, a 0 számot a megadott tömb elejéről:

```
skip(createArray(0, 1, 2, 3), 1)
```

Ezt a tömböt adja vissza a fennmaradó elemekkel együtt: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>felosztás

Olyan tömböt ad vissza, amely vesszővel elválasztott részsztringeket tartalmaz az eredeti sztringben megadott elválasztó karakter alapján.

```
split('<text>', '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A sztring, amely az eredeti sztringben megadott elválasztó karakter alapján alsztringekbe lesz tagolt |
| <*Határoló*> | Igen | Sztring | Az eredeti sztring elválasztó karaktere |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| *[<1. részsztring1*>,<*substring2*>,...] | Tömb | Egy tömb, amely az eredeti sztringből származó részsztringeket tartalmaz, vesszővel elválasztva |
||||

*Példa*

Ez a példa egy tömböt hoz létre a megadott sztring alsztringjével a megadott elválasztó karakter alapján:

```
split('a_b_c', '_')
```

A és a ezt a tömböt adja vissza eredményként: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay (Nap kezdete)

Egy időbélyegző nap elejének visszaadva.

```
startOfDay('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a nap nulla órás jellel kezdődik |
||||

*Példa*

Ez a példa megkeresi a nap első napját ehhez az időbélyeghez:

```
startOfDay('2018-03-15T13:30:30Z')
```

A és a következő eredményt adja vissza: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Egy időbélyeghez adja vissza az óra kezdetét.

```
startOfHour('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de az óra nulla perces jellel kezdődik |
||||

*Példa*

Ez a példa megkeresi a következő időbélyegző óra kezdetét:

```
startOfHour('2018-03-15T13:30:30Z')
```

A és a következő eredményt adja vissza: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth (hónap kezdete)

Egy időbélyegző hónap elejének visszaadva.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszabadoló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | A megadott időbélyeg, de a hónap első napján, nulla órás jellel kezdődik |
||||

*1\. példa*

Ez a példa a hónap első hónapját adja vissza ehhez az időbélyeghez:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A és a következő eredményt adja vissza: `"2018-03-01T00:00:00.0000000Z"`

*2\. példa*

Ez a példa a hónap kezdetét adja vissza a megadott formátumban ehhez az időbélyeghez:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

A és a következő eredményt adja vissza: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Ellenőrizze, hogy egy sztring egy adott sztringsztringből indul-e ki.
True (igaz) értéket ad vissza, ha a karakterlánc alsztring található, vagy false (hamis) értéket, ha nem található.
Ez a függvény nem megkülönbözteti a kis- és nagybetűket.

```
startsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | Az ellenőrizni következő sztring: |
| <*searchText (Szöveg keresése)*> | Igen | Sztring | A megtalált kezdő sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai | True (igaz) érték visszaadása, ha a kezdő alsztring megtalálható. Ha nem található, a false (hamis) értéket adja vissza. |
||||

*1\. példa*

Ez a példa azt ellenőrzi, hogy a "hello world" sztring a "hello" karakterláncmal kezdődik-e:

```
startsWith('hello world', 'hello')
```

A és a következő eredményt adja vissza: `true`

*2\. példa*

Ez a példa ellenőrzi, hogy a "hello world" sztring a "greetings" sztringtel kezdődik-e:

```
startsWith('hello world', 'greetings')
```

A és a következő eredményt adja vissza: `false`

<a name="string"></a>

### <a name="string"></a>sztring

Egy érték sztringverzióját adja vissza.

```
string(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Bármelyik | Az átalakítható érték. Ha ez az érték null vagy null értéket ad vissza, a rendszer üres sztring ( ) értékké `""` konvertálja az értéket. <p><p>Ha például sztringváltozót rendel egy nem létező tulajdonsághoz, amelyet a operátorral ér el, a null érték üres `?` sztringgé lesz konvertálva. A null értékek összehasonlítása azonban nem ugyanaz, mint egy üres sztring összehasonlítása. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*sztring-érték*> | Sztring | A megadott érték sztringverziója. Ha az *értékparaméter* null vagy null értéket ad vissza, a függvény üres sztring ( ) értékként ad `""` vissza értéket. |
||||





*1\. példa*

Ez a példa a sztringverziót hozza létre ehhez a számhoz:

```
string(10)
```

A és a következő eredményt adja vissza: `"10"`

*2\. példa*

Ebben a példában egy sztringet hozunk létre a megadott JSON-objektumhoz, és a vissza perjel karaktert ( ) használja a dupla idézőjel \\ () escape-karaktereként.

```
string( { "name": "Sophie Owen" } )
```

A és a következő eredményt adja vissza: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Al

A második szám első számból való kivonásának eredményét adja vissza.

```
sub(<minuend>, <subtrahend>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kisebbítendő*> | Igen | Egész szám vagy lebegő érték | Az a szám, amelyből a *részfokot ki kell vonni* |
| <*Kivonandó*> | Igen | Egész szám vagy lebegő érték | A *minuendből* kivonható szám |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Eredmény*> | Egész szám vagy lebegő érték | A második szám az első számból való kivonásának eredménye |
||||

*Példa*

Ez a példa kivonja a második számot az első számból:

```
sub(10.3, .3)
```

A és a következő eredményt adja vissza: `10`

<a name="substring"></a>

### <a name="substring"></a>Substring

Karaktereket ad vissza egy sztringből a megadott pozíciótól vagy indextől kezdve. Az indexértékek a 0 számmal kezdődnek.

```
substring('<text>', <startIndex>, <length>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A sztring, amelynek a karaktereit szeretné |
| <*startIndex (kezdőindex)*> | Igen | Egész szám | Egy 0-s vagy annál nagyobb pozitív szám, amely kezdő pozícióként vagy indexértékként használható |
| <*Hossza*> | Nem | Egész szám | A karakterlánc alsztringben kívánt pozitív számú karakter |
|||||

> [!NOTE]
> Győződjön meg arról, hogy a *startIndex* és a *length* paraméterértékek összeadása után megadott összeg kisebb, mint a szöveges paraméterhez megadott *sztring* hossza.
> Ellenkező esetben hibaüzenetet kap, ellentétben más nyelvek hasonló függvényekkel, ahol az eredmény a *startIndex* sztring alsztringe a sztring végéig. A *length* paraméter megadása nem kötelező, és ha nincs megadva, a **substring()** függvény a *startIndextől* a sztring végéig minden karaktert átvesz.

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*alsztring-eredmény*> | Sztring | Megadott számú karakterből áll egy karakterlánc, amely a forrássztringben megadott indexpozíciótól kezdődik |
||||

*Példa*

Ez a példa a megadott sztringből egy öt karakterből származó alsztringet hoz létre a 6. indexértéktől kezdve:

```
substring('hello world', 6, 5)
```

A és a következő eredményt adja vissza: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime (kivonás ideje)

Időegységek kivonása időbélyegből.
Lásd [még: getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*Intervallum*> | Igen | Egész szám | A kivonni megadott időegységek száma |
| <*timeUnit (időegység)*> | Igen | Sztring | Az intervallummal használt *időegység:*"Másodperc", "Perc", "Óra", "Nap", "Hét", "Hónap", "Év" |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*frissített időbélyeg*> | Sztring | Az időbélyeg a megadott számú időegységből levonva |
||||

*1\. példa*

Ez a példa kivon egy napot ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

A és a következő eredményt adja vissza: `"2018-01-01T00:00:00.0000000Z"`

*2\. példa*

Ez a példa kivon egy napot ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Ezt az eredményt a nem kötelező "D" formátumban adja vissza: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Elemeket ad vissza egy gyűjtemény elejéről.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Gyűjtemény*> | Igen | Sztring vagy tömb | A gyűjtemény, amelynek elemeit szeretné |
| <*Számít*> | Igen | Egész szám | Pozitív egész szám az előlapról kívánt elemek számára |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*részkészlet>* vagy [<*részkészlet>]* | Sztring vagy tömb | Egy sztring vagy tömb, amely a megadott számú elemet tartalmazza az eredeti gyűjtemény elejéről |
||||

*Példa*

Ezek a példák a megadott számú elemet kapják meg a gyűjtemények elejéről:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

És adja vissza az alábbi eredményeket:

* Első példa: `"abc"`
* Második példa: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Kullancsok

Az órajelek számát adja vissza, amelyek 100 nanoszekundumos intervallumok, és a január 1., 0001 12:00:00 éjfél (vagy DateTime.Ticks in C#) időponttól a megadott időbélyegig. További információért tekintse meg ezt a témakört: [DateTime.Ticks tulajdonság (Rendszer)](/dotnet/api/system.datetime.ticks).

```
ticks('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Időbélyeg*> | Igen | Sztring | Az időbélyeg sztringe |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*órajel-szám*> | Egész szám | Az órajelek száma a megadott időbélyeg óta |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Kisbetűs sztringet ad vissza. Ha a sztring egyik karakterének nincs kisbetűs verziója, az a visszaadott sztringben változatlan marad.

```
toLower('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A kisbetűs formátumban visszaadni kívánt sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kisbetűs szöveg*> | Sztring | Az eredeti sztring kisbetűs formátumban |
||||

*Példa*

Ez a példa kisbetűssé alakítja ezt a sztringet:

```
toLower('Hello World')
```

A és a következő eredményt adja vissza: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Sztringet ad vissza nagybetűs formátumban. Ha a sztring egyik karakterének nincs nagybetűs verziója, az a visszaadott sztringben változatlan marad.

```
toUpper('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A nagybetűs formátumban visszaadni kívánt sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*nagybetűs szöveg*> | Sztring | Az eredeti sztring nagybetűs formátumban |
||||

*Példa*

Ez a példa nagybetűssé alakítja ezt a sztringet:

```
toUpper('Hello World')
```

A és a következő eredményt adja vissza: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Ravaszt

Az eseményindító futásidejű kimenetét vagy más JSON-név-érték párok értékeit adja vissza, amelyeket egy kifejezéshez rendelhet.

* Az eseményindító bemenetei között ez a függvény az előző végrehajtás kimenetét adja vissza.

* Egy eseményindító feltételében ez a függvény az aktuális végrehajtás kimenetét adja vissza.

Alapértelmezés szerint a függvény a teljes eseményindító-objektumra hivatkozik, de megadhat egy tulajdonságot is, amelynek a kívánt értéke.
Emellett ez a függvény rövid verziókat is elérhetővé válik. Lásd: [triggerOutputs()](#triggerOutputs) és [triggerBody()](#triggerBody).

```
trigger()
```

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Sztring | Az eseményindító kimenete futásidőben |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Eseményindító kimenetének `body` visszaadása futásidőben.
Röviden : `trigger().outputs.body` .
Lásd: [trigger()](#trigger).

```
triggerBody()
```

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Sztring | Az `body` eseményindító kimenete |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Olyan értékeket tartalmazó tömböt ad vissza,  amelyek megegyeznek az eseményindító űrlapadat- vagy *űrlapkódolt kimenetében* megadott kulcsnévvel.

```
triggerFormDataMultiValues('<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek az értékét ön szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Tömb | Egy tömb, amely a megadott kulccsal egyező összes értéket tartalmaz |
||||

*Példa*

Ez a példa egy tömböt hoz létre a "feedUrl" kulcsértékből egy RSS-eseményindító űrlapadat- vagy űrlapkódolt kimenetében:

```
triggerFormDataMultiValues('feedUrl')
```

A és a ezt a tömböt adja vissza példaként: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Egyetlen értékkel megadott sztringet ad vissza,  amely megegyezik az eseményindító űrlapadat- vagy *űrlapkódolt kimenetében* megadott kulcsnévvel.
Ha a függvény egynél több egyezést talál, a függvény hibát jelez.

```
triggerFormDataValue('<key>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Kulcs*> | Igen | Sztring | Annak a kulcsnak a neve, amelynek az értékét ön szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kulcs-érték*> | Sztring | A megadott kulcsban található érték |
||||

*Példa*

Ez a példa egy sztringet hoz létre a "feedUrl" kulcsértékből egy RSS-eseményindító űrlapadat- vagy űrlapkódolt kimenetében:

```
triggerFormDataValue('feedUrl')
```

A és a példaként visszaadja ezt a sztringet: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Egy több részből áll eseményindító kimenetének egy adott részének törzsét adja vissza.

```
triggerMultipartBody(<index>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Igen | Egész szám | A kívánt rész indexértéke |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Szervezet*> | Sztring | Az eseményindító többrészes kimenetében megadott rész törzse |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Az eseményindító futásidejű kimenetét, vagy más JSON-név-érték párok értékeit adja vissza.
Röviden : `trigger().outputs` .
Lásd: [trigger()](#trigger).

```
triggerOutputs()
```

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Sztring | Az eseményindító kimenete futásidőben  |
||||

<a name="trim"></a>

### <a name="trim"></a>Berendezés

Távolítsa el a kezdő és záró szóközt egy sztringből, és adja vissza a frissített sztringet.

```
trim('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Szöveg*> | Igen | Sztring | A kezdő és záró szóközt eltávolító sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updatedText (szöveg frissítése)*> | Sztring | Az eredeti sztring frissített verziója kezdő vagy záró szóköz nélkül |
||||

*Példa*

Ez a példa eltávolítja a kezdő és záró szóközt a " Hello World ":

```
trim(' Hello World  ')
```

A és a következő eredményt adja vissza: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unió

Olyan gyűjteményt ad vissza, *amely a* megadott gyűjtemények összes elemét tartalmazza.
Ahhoz, hogy megjelenjen az eredményben, egy elem a függvénynek átadott bármely gyűjteményben megjelenhet. Ha egy vagy több elem neve megegyezik, az eredményben megjelenik az utolsó ilyen nevű elem.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Igen | Tömb vagy objektum, de nem mindkettő | A gyűjtemények, amelyekből az *összes elemet* el szeretné látni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Tömb vagy objektum | Egy gyűjtemény, amely a megadott gyűjtemények összes elemét tartalmazza – nincsenek ismétlődések |
||||

*Példa*

Ez a példa a *következő* gyűjtemények összes elemét lekérte:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

A és a következő eredményt adja vissza: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>URIComponent (URI-kiegészítés)

Sztring egységes erőforrás-azonosítóval (URI) kódolt verzióját adja vissza úgy, hogy az URL-címben nem biztonságos karaktereket escape-karakterekre cseréli.
Ezt a függvényt használja az [encodeUriComponent() helyett.](#encodeUriComponent)
Bár mindkét függvény ugyanúgy működik, `uriComponent()` ajánlott.

```
uriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az URI-kódolású formátumra konvertálni kívánt sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Sztring | Az URI-kódolású sztring escape-karakterekkel |
||||

*Példa*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a sztringhez:

```
uriComponent('https://contoso.com')
```

A és a következő eredményt adja vissza: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Egy egységes erőforrás-azonosító (URI) összetevő bináris verzióját adja vissza.

```
uriComponentToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható URI-kódolású sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Sztring | Az URI-kódolású sztring bináris verziója. A bináris tartalom base64 kódolású, és a következővel van `$content` ábrázolva: . |
||||

*Példa*

Ez a példa létrehozza a bináris verziót ehhez az URI-kódolású sztringhez:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

A és a következő eredményt adja vissza:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Egy egységes erőforrás-azonosítóval (URI) kódolt sztring sztringverzióját adja vissza, hatékonyan dekódolva az URI-kódolású sztringet.

```
uriComponentToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | A dekódolni használt URI-kódolású sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Sztring | Az URI-kódolású sztring dekódolt verziója |
||||

*Példa*

Ez a példa a dekódolt sztringverziót hozza létre ehhez az URI-kódolású sztringhez:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

A és a következő eredményt adja vissza: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Egy egységes `host` erőforrás-azonosító (URI) értékét adja vissza.

```
uriHost('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak `host` a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*gazdagép-érték*> | Sztring | A `host` megadott URI értéke |
||||

*Példa*

Ez a példa `host` megkeresi ennek az URI-nak az értékét:

```
uriHost('https://www.localhost.com:8080')
```

A és a következő eredményt adja vissza: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Egy egységes `path` erőforrás-azonosító (URI) értékét adja vissza.

```
uriPath('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak `path` a neve, amelynek az értékét meg szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*path-value*> | Sztring | A `path` megadott URI értéke. Ha `path` nem ad meg értéket, a "/" karaktert adja vissza. |
||||

*Példa*

Ez a példa `path` megkeresi ennek az URI-nak az értékét:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

A és a következő eredményt adja vissza: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Egy egységes `path` `query` erőforrás-azonosító (URI) és értékét adja vissza.

```
uriPathAndQuery('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak a `path` `query` neve, amelynek és értékeit szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Sztring | A `path` megadott `query` URI és értékei. Ha `path` nem ad meg értéket, a "/" karaktert adja vissza. |
||||

*Példa*

Ez a példa megkeresi `path` `query` az URI és értékét:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

A és a következő eredményt adja vissza: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>URIPort (URIPort)

Egy egységes `port` erőforrás-azonosító (URI) értékét adja vissza.

```
uriPort('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak `port` a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*port-érték*> | Egész szám | A `port` megadott URI értéke. Ha `port` nem ad meg értéket, adja vissza a protokoll alapértelmezett portját. |
||||

*Példa*

Ez a példa ennek `port` az URI-nak az értékét adja vissza:

```
uriPort('https://www.localhost:8080')
```

A és a következő eredményt adja vissza: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>URIQuery

Egy egységes `query` erőforrás-azonosító (URI) értékét adja vissza.

```
uriQuery('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak `query` a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*query-value*> | Sztring | A `query` megadott URI értéke |
||||

*Példa*

Ez a példa ennek `query` az URI-nak az értékét adja vissza:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

A és a következő eredményt adja vissza: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Egy egységes `scheme` erőforrás-azonosító (URI) értékét adja vissza.

```
uriScheme('<uri>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Igen | Sztring | Annak az URI-nak `scheme` a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*séma-érték*> | Sztring | A `scheme` megadott URI értéke |
||||

*Példa*

Ez a példa ennek `scheme` az URI-nak az értékét adja vissza:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

A és a következő eredményt adja vissza: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow (utcNow)

Adja vissza az aktuális időbélyeget.

```
utcNow('<format>')
```

Másik formátumot is megadhat a paraméter <*>* formátummal.


| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Formátum*> | Nem | Sztring | Egyetlen [formátumot megszanáló vagy](/dotnet/standard/base-types/standard-date-and-time-format-strings) [egyéni formátumminta.](/dotnet/standard/base-types/custom-date-and-time-format-strings) Az időbélyeg alapértelmezett formátuma az ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (éyyy-MM-ddTHH:mm:ss.fffffffK), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) szabványnak, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*aktuális időbélyeg*> | Sztring | Az aktuális dátum és idő |
||||

*1\. példa*

Tegyük fel, hogy ma 2018. április 15. 13:00..
Ez a példa az aktuális időbélyeget kapja meg:

```
utcNow()
```

A és a következő eredményt adja vissza: `"2018-04-15T13:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy ma 2018. április 15. 13:00..
Ez a példa az aktuális időbélyeget a nem kötelező "D" formátum használatával kapja meg:

```
utcNow('D')
```

A és a következő eredményt adja vissza: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Változók

Visszaadja egy adott változó értékét.

```
variables('<variableName>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*variableName (változó neve)*> | Igen | Sztring | Annak a változónak a neve, amelynek az értékét szeretné |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*változó-érték*> | Bármelyik | A megadott változó értéke |
||||

*Példa*

Tegyük fel, hogy egy "numItems" változó aktuális értéke 20.
Ez a példa a változó egész értékét kapja meg:

```
variables('numItems')
```

A és a következő eredményt adja vissza: `20`

<a name="workflow"></a>

### <a name="workflow"></a>munkafolyamat

A munkafolyamat minden adatának visszaadása a futási idő során.

```
workflow().<property>
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Tulajdonság*> | Nem | Sztring | Annak a munkafolyamat-tulajdonságnak a neve, amelynek az értékét ön szeretné <p><p>Alapértelmezés szerint a munkafolyamat-objektumok a következő tulajdonságokkal rendelkeznek: `name` , , , , és `type` `id` `location` `run` `tags` . <p><p>– A `run` tulajdonság értéke egy JSON-objektum, amely a következő tulajdonságokat tartalmazza: `name` , és `type` `id` . <p><p>– A tulajdonság egy JSON-objektum, amely olyan címkéket tartalmaz, amelyek a logikai alkalmazáshoz vannak társítva a Azure Logic Apps vagy folyamatában a Power Automate és ezen címkék `tags` értékeit. [](../azure-resource-manager/management/tag-resources.md) További információ az Azure-erőforrások címkéiről: Erőforrások, erőforráscsoportok és előfizetések címkézése logikai szervezetek számára [az Azure-ban.](../azure-resource-manager/management/tag-resources.md) <p><p>**Megjegyzés:** Alapértelmezés szerint a logikai alkalmazások nem rendelkezik címkékkel, de Power Automate folyamat rendelkezik a és a `flowDisplayName` `environmentName` címkével. |
|||||

*1\. példa*

Ez a példa egy munkafolyamat aktuális futtatásának nevét adja vissza:

`workflow().run.name`

*2\. példa*

Ha a Power Automate, létrehozhat egy kifejezést, amely az output tulajdonság használatával le tudja szerezni a folyamat vagy tulajdonságának `@workflow()` `tags` `flowDisplayName` `environmentName` értékeit.

Küldhet például olyan egyéni e-mail-értesítéseket a folyamatból, amelyek a folyamatra hivatkoznak. Ezek az értesítések tartalmazhatnak egy HTML-hivatkozást, amely a folyamat megjelenítendő nevét tartalmazza az e-mail címében, és az alábbi szintaxist követi:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

Egy JSON-objektumot tartalmazó sztring XML-verziójának visszaadása.

```
xml('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Érték*> | Igen | Sztring | Az átalakítható JSON-objektummal való sztring <p>A JSON-objektumnak csak egy gyökértulajdonságával kell lennie, amely nem lehet tömb. <br>A dupla idézőjel () escape-karaktereként használja a perjelet \\ (). |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*xml-verzió*> | Objektum | A megadott sztring vagy JSON-objektum kódolt XML-fájlja |
||||

*1\. példa*

Ez a példa létrehozza a sztring XML-verzióját, amely egy JSON-objektumot tartalmaz:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Az eredmény XML-fájlja pedig a következő lesz:

```xml
<name>Sophia Owen</name>
```

*2\. példa*

Tegyük fel, hogy ez a JSON-objektummal van:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ez a példa egy XML-fájlt hoz létre egy sztringhez, amely a következő JSON-objektumot tartalmazza:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Az eredmény XML-fájlja pedig a következő lesz:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Ellenőrizze az XML-ben, hogy vannak-e olyan csomópontok vagy értékek, amelyek megfelelnek egy XPath- (XML Elérésiút-nyelv) kifejezésnek, és visszaadja az egyező csomópontokat vagy értékeket. Az XPath-kifejezések vagy csak az "XPath" segítségével navigálhat az XML-dokumentumszerkezetben, így csomópontokat vagy számítási értékeket választhat ki az XML-tartalomban.

```
xpath('<xml>', '<xpath>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Igen | Bármelyik | Az XPath-kifejezésértéknek megfelelő csomópontok vagy értékek keresésére használható XML-sztring |
| <*Xpath*> | Igen | Bármelyik | Az egyező XML-csomópontok vagy -értékek keresésére használt XPath-kifejezés |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | XML-csomópont, ha csak egyetlen csomópont egyezik a megadott XPath-kifejezéssel |
| <*Érték*> | Bármelyik | Egy XML-csomópontból származó érték, ha csak egyetlen érték egyezik meg a megadott XPath-kifejezéssel |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-vagy- </br>[<*value1*>, <*value2*>, ...] | Tömb | A megadott XPath-kifejezésnek megfelelő XML-csomópontokkal vagy -értékekkel |
||||

*1\. példa*

Tegyük fel, hogy ez az `'items'` XML-sztring van meg: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ez a példa átadja az XPath-kifejezést () az XML-sztringben található csomópontnak megfelelő csomópontok megkeresését, és visszaad egy tömböt a következő `'/produce/item/name'` `<name></name>` `'items'` csomópontértékekkel:

`xpath(xml(parameters('items')), '/produce/item/name')`

A példa a [parameters() függvényt](#parameters) is használja az XML-sztring lekért formában, és konvertálja a sztringet `'items'` XML [formátumba az xml() függvény](#xml) használatával.

Itt található az eredménytömb a következőnek megfelelő `<name></name` csomópontokkal:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*2\. példa*

Az 1. példa után ez a példa az XPath-kifejezést () továbbítva megkeresi az elem `'/produce/item/name[1]'` `name` gyermekelemének első `item` elemét.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Az eredmény a következő: `Gala`

*3\. példa*

Az 1. példa után ez a példa az XPath-kifejezést adja át, hogy megkeresse az elem gyermekelemének `'/produce/item/name[last()]'` `name` utolsó `item` elemét.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Az eredmény a következő: `Honeycrisp`

*4\. példa*

Ebben a példában tegyük fel, hogy az `items` XML-sztring a és a attribútumot is `expired='true'` `expired='false'` tartalmazza:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ez a példa az XPath-kifejezést () továbbítva megkeresi az összes olyan elemet, amely `'//name[@expired]'` `name` az `expired` attribútummal van:

`xpath(xml(parameters('items')), '//name[@expired]')`

Az eredmény a következő: `[ Gala, Honeycrisp ]`

*5\. példa*

Ebben a példában tegyük fel, hogy az `items` XML-sztring csak ezt az attribútumot tartalmazza: `expired = 'true'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ez a példa az XPath-kifejezést () továbbítva megkeresi az összes olyan elemet, amely a `'//name[@expired = 'true']'` `name` attribútummal `expired = 'true'` () van:

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Az eredmény a következő: `[ Gala ]`

*6\. példa*

Ebben a példában tegyük fel, hogy az `items` XML-sztring az alábbi attribútumokat is tartalmazza: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ez a példa az XPath-kifejezést () továbbítja, hogy megkeresse az összes olyan elemet, `'//name[price>35]'` `name` amely tartalmazza a `price > 35` következőt:

`xpath(xml(parameters('items')), '//name[price>35]')`

Az eredmény a következő: `Honeycrisp`

*7. példa*

Ebben a példában tegyük fel, hogy az `items` XML-sztring ugyanaz, mint az 1. példában:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ez a példa megkeresi a csomópontnak megfelelő csomópontokat, és hozzáadja ezeket `<count></count>` a csomópontértékeket a `sum()` függvényhez:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Az eredmény a következő: `30`

*8. példa*

Ebben a példában tegyük fel, hogy ez az XML-sztring, amely az XML-dokumentumnévteret `xmlns="https://contoso.com"` tartalmazza:

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Ezek a kifejezések az XPath-kifejezés vagy a használatával megkeresik a `/*[name()="file"]/*[name()="location"]` `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` csomópontnak megfelelő `<location></location>` csomópontokat. Ezek a példák a Logikaialkalmazás-tervezőben vagy a kifejezésszerkesztőben használt szintaxist mutatják be:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Itt található a csomópontnak megfelelő `<location></location>` eredménycsomópont: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Ha kódnézetben dolgozik, a dupla idézőjelet (") a ( ) karakterrel ússza \\ meg. 
> Például escape-karaktereket kell használnia, amikor JSON-sztringként szerializál egy kifejezést. 
> Ha azonban a Logic App Designerben vagy a kifejezésszerkesztőben dolgozik, nem kell a dupla idézőjelet kivédnie, mert a rendszer automatikusan hozzáadja a backslash karaktert az alapul szolgáló definícióhoz, például:
> 
> * Kódnézet: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Kifejezésszerkesztő: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*9. példa*

A 8. példában található példában a XPath-kifejezéssel találják meg az értéket `'string(/*[name()="file"]/*[name()="location"])'` a `<location></location>` csomópontban:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Az eredmény a következő: `Paris`

## <a name="next-steps"></a>Következő lépések

A [munkafolyamat-definíciós nyelv megismerása](../logic-apps/logic-apps-workflow-definition-language.md)
