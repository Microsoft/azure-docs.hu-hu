---
title: Felhasználó által definiált JavaScript-függvények Azure Stream Analytics
description: Ez a cikk a JavaScript felhasználó által definiált függvények bevezetését ismerteti Stream Analyticsban.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020383"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Felhasználó által definiált JavaScript-függvények Azure Stream Analytics
 
Az Azure Stream Analytics támogatja a JavaScript nyelven írt felhasználói függvényeket. A JavaScript által biztosított **karakterlánc**-, **regexp**-, **matematikai**, **Array** és **Date** metódusok gazdag készletével könnyebben hozhatók létre összetett adatátalakítások stream Analytics feladatokkal.

## <a name="overview"></a>Áttekintés

A felhasználó által definiált JavaScript-függvények olyan állapot nélküli, csak számítási skaláris függvényeket támogatnak, amelyek nem igényelnek külső kapcsolatot. Egy függvény visszaadott értéke csak skaláris (egyetlen) érték lehet. Miután hozzáadott egy felhasználói JavaScript-függvényt egy feladathoz, bárhol használhatja a függvényt a lekérdezésben, egy beépített skaláris függvényhez hasonlóan.

Az alábbiakban bemutatunk néhány forgatókönyvet, amelyekben hasznosnak találhatja a felhasználói JavaScript-függvényeket:
* Reguláriskifejezés-függvényeket (például: **Regexp_Replace()** és **Regexp_Extract()**) tartalmazó sztringek elemzése és módosítása.
* Adatok dekódolása és kódolása, például bináris adatok átalakítása hexadecimális adatokká.
* Matematikai-számítások végrehajtása JavaScript **matematikai** függvényekkel
* Tömbbeli műveletek, például a rendezés, a csatlakozás, a keresés és a kitöltés

Íme néhány dolog, amit nem lehet a felhasználó által definiált JavaScript-függvénnyel Stream Analytics:
* Külső REST-végpontok meghívása, például fordított IP-címkeresés végrehajtása vagy hivatkozás adatainak külső forrásból való kihúzása
* Egyéni eseményformátum szerializálása vagy deszerializálása bemenetekben/kimenetekben.
* Egyéni összesítések létrehozása.

Bár a függvények, például a **Date. GetDate ()** vagy a **Math. Random ()** függvény nem blokkolja a függvények definícióját, ne használja őket. Ezek a függvények **nem** adják vissza ugyanazt az eredményt minden alkalommal, amikor meghívja őket, és a Azure stream Analytics szolgáltatás nem tartja meg a függvény-meghívások naplóját, és eredményül adja vissza az eredményeket. Ha egy függvény eltérő eredményt ad vissza ugyanazon eseményeken, az ismételhetőség nem garantált, ha Ön vagy a Stream Analytics szolgáltatás újraindította a feladatot.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>JavaScript felhasználó által definiált függvény hozzáadása a feladathoz

> [!NOTE]
> Ezek a lépések a felhőben való futtatásra konfigurált Stream Analytics-feladatokon működnek. Ha a Stream Analytics-feladat úgy van konfigurálva, hogy Azure IoT Edgeon fusson, Ehelyett használja a Visual studiót, és [írja be a felhasználó által definiált függvényt a C# használatával](stream-analytics-edge-csharp-udf.md).

JavaScript felhasználó által definiált függvény létrehozásához a Stream Analytics feladatban válassza a **feladatok** a **feladat topológiája** alatt lehetőséget. Ezután válassza a **JavaScript UDF** elemet a **+** legördülő menüből. 

![JavaScript UDF hozzáadása](./media/javascript/stream-analytics-jsudf-add.png)

Ezután meg kell adnia a következő tulajdonságokat, majd a **Mentés** lehetőséget kell választania.

|Tulajdonság|Leírás|
|--------|-----------|
|Függvény aliasa|Adjon meg egy nevet a függvény meghívásához a lekérdezésben.|
|Kimenet típusa|A JavaScript felhasználó által definiált függvény által visszaadott típust a Stream Analytics lekérdezéshez adja vissza a rendszer.|
|Függvény definíciója|JavaScript-függvény implementálása, amely minden alkalommal végrehajtva lesz, amikor az UDF meghívja a lekérdezést.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF tesztelése és hibakeresése 

A JavaScript UDF-logikát bármely böngészőben tesztelheti és hibakeresést végezhet. A felhasználó által definiált függvények logikájának hibakeresése és tesztelése jelenleg nem támogatott a Stream Analytics portálon. Ha a függvény a várt módon működik, a fentiekben leírtak szerint adhatja hozzá a Stream Analytics feladathoz, majd közvetlenül a lekérdezésből hívhatja meg. A lekérdezési logikát a JavaScript UDF használatával tesztelheti a [Visual studióhoz készült stream Analytics eszközökkel](./stream-analytics-tools-for-visual-studio-install.md).

A JavaScript futásidejű hibái végzetesnek minősülnek, és a tevékenységnaplóban tekinthetők meg. A napló lekéréséhez lépjen a feladatra az Azure Portalon, és válassza a **Tevékenységnapló** elemet.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Felhasználói JavaScript-függvény meghívása lekérdezésben

A JavaScript-függvényt a lekérdezésben egyszerűen meghívhatja az **UDF** előtaggal megadott függvény alias használatával. Íme egy példa egy JavaScript UDF-re, amely a hexadecimális értékeket egy Stream Analytics lekérdezésben meghívott egész számra konvertálja.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Támogatott JavaScript-objektumok

Az Azure Stream Analytics felhasználói JavaScript-függvényei támogatják a szabványos, beépített JavaScript-objektumokat. Az objektumok listáját a [globális objektumokkal](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) foglalkozó cikkben tekintheti meg.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Típusátalakítás a Stream Analytics és a JavaScript között

A Stream Analytics lekérdezési nyelv és a JavaScript által támogatott típusok között különbségek vannak. Az alábbi táblázat a kettő közötti átalakítás megfeleléseit tartalmazza:

Stream Analytics | JavaScript
--- | ---
bigint | Szám (a JavaScript legfeljebb pontosan a 2^53-ig tudja a számokat kezelni)
DateTime | Dátum (a JavaScript csak az ezredmásodperceket támogatja)
double | Szám
nvarchar(MAX) | Sztring
Rekord | Objektum
Tömb | Tömb
NULL | Null

A JavaScriptről a Stream Analyticsre történő átalakítások:

JavaScript | Stream Analytics
--- | ---
Szám | Bigint (ha a szám kerek és a long.MinValue és a long.MaxValue közé esik, máskülönben double)
Date | DateTime
Sztring | nvarchar(MAX)
Objektum | Rekord
Tömb | Tömb
Null, nem definiált | NULL
Bármely más típus (például függvény vagy hiba) | Nem támogatott (futásidejű hibát eredményez)

A JavaScript nyelv megkülönbözteti a kis-és nagybetűket, és a JavaScript-kódban található objektumok mezőinek meg kell egyeznie a bejövő adatokban lévő mezők házával. A 1,0 kompatibilitási szinttel rendelkező feladatok az SQL SELECT utasítás mezőinek kisbetűs értékre lesznek konvertálva. A 1,1-es és magasabb kompatibilitási szint alatt a SELECT utasítás mezőinek az SQL-lekérdezésben megadottal azonos burkolattal kell rendelkezniük.

## <a name="other-javascript-user-defined-function-patterns"></a>Egyéb felhasználói JavaScript-függvényminták

### <a name="write-nested-json-to-output"></a>Beágyazott JSON írása a kimenetbe

Ha van még egy további feldolgozási lépés, amely a Stream Analytics-feladat kimenetét használja a bemeneteként, és JSON-formátumot igényel, a kimenetbe írhat JSON-sztringet. A következő példa a **JSON.stringify()** függvény meghívásával becsomagolja a bemenetben lévő összes név/érték párt, majd egyetlen sztringértékként írja őket a kimenetbe.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Példa lekérdezésre:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>A feldolgozható karakterlánc a JSON-objektumba

Ha olyan karakterlánc-mezővel rendelkezik, amely JSON formátumú, és egy JSON-objektumba szeretné átalakítani egy JavaScript UDF-ben történő feldolgozásra, a **JSON. Parse ()** függvénnyel LÉTREHOZHAT egy JSON-objektumot, amelyet aztán használhat.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Példa lekérdezésre:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>A hibák kezelésére szolgáló try/catch szolgáltatás használata

A try/catch blokkokkal azonosíthatja a JavaScript UDF-ben átadott helytelenül formázott bemeneti adatokkal kapcsolatos problémákat.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Minta lekérdezés: a teljes rekord első paraméterként való átadásával visszaadható, ha hiba történt.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
A JavaScript **toLocaleString** metódusa használható olyan nyelvre érzékeny karakterlánc visszaadására, amely a metódus hívásának dátumát és időpontját jelöli.
Annak ellenére, hogy az Azure stream-Analytics csak az UTC időpontot fogadja el a rendszer időbélyegzője, ezzel a módszerrel a rendszer időbélyegét más területi beállításokra és időzónára is használhatja.
Ez a módszer ugyanazt a megvalósítási viselkedést követi, mint az Internet Explorerben.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Minta lekérdezés: dátum és idő beírása bemeneti értékként**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

A lekérdezés kimenete a megadott beállításokkal megjelenő, **de-de** dátum és idő közötti dátumot fogja tartalmazni.
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>Felhasználói naplózás
A naplózási mechanizmus lehetővé teszi, hogy egyéni adatokat rögzítsen a feladatok futtatása közben. A naplózási adatok segítségével valós időben végezhet hibakeresést vagy felmérni az egyéni kód helyességét. Ez a mechanizmus a Console. log () metóduson keresztül érhető el.

```javascript
console.log('my error message');
```

A naplózási üzeneteket a [diagnosztikai naplókon](data-errors.md)keresztül érheti el.
## <a name="next-steps"></a>Következő lépések

* [UDF Machine Learning](./machine-learning-udf.md)
* [C# UDF](./stream-analytics-edge-csharp-udf-methods.md)
