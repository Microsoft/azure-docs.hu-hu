---
title: 'Oktatóanyag: Az Azure Stream Analytics JavaScript felhasználó által definiált függvények |} A Microsoft Docs '
description: A jelen oktatóanyagban összetett lekérdezési műveleteket fog végrehajtani felhasználói JavaScript-függvényekkel.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: ff8e61c53774429087ffe1a9137d40b155eb3f68
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192275"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Oktatóanyag: Az Azure Stream Analytics JavaScript felhasználó által definiált függvények
 
Az Azure Stream Analytics támogatja a JavaScript nyelven írt felhasználói függvényeket. A JavaScript által biztosított **String**, **RegExp**, **Math**, **Array** és **Date** metódusok széles választékának köszönhetően könnyebben hozhatók létre összetett adatátalakítások Stream Analytics-feladatokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felhasználói JavaScript-függvények definiálása
> * A függvény hozzáadása a portálhoz
> * A függvényt futtató lekérdezés definiálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="javascript-user-defined-functions"></a>Felhasználói JavaScript-függvények
A felhasználói JavaScript-függvények támogatják a külső kapcsolatokat nem igénylő, állapot nélküli, csak számítási skaláris függvényeket. Egy függvény visszaadott értéke csak skaláris (egyetlen) érték lehet. Miután hozzáadott egy felhasználói JavaScript-függvényt egy feladathoz, bárhol használhatja a függvényt a lekérdezésben, egy beépített skaláris függvényhez hasonlóan.

Az alábbiakban bemutatunk néhány forgatókönyvet, amelyekben hasznosnak találhatja a felhasználói JavaScript-függvényeket:
* Reguláriskifejezés-függvényeket (például: **Regexp_Replace()** és **Regexp_Extract()**) tartalmazó sztringek elemzése és módosítása.
* Adatok dekódolása és kódolása, például bináris adatok átalakítása hexadecimális adatokká.
* Matematikai számítások végrehajtása JavaScript **Math** függvényekkel.
* Tömbműveletek, például rendezés, összekapcsolás, keresés és kitöltés végrehajtása.

Néhány dolog, amely nem hajtható végre felhasználói JavaScript-függvényekkel a Stream Analytics szolgáltatásban:
* Külső REST-végpontok meghívása, például fordított IP-keresés végrehajtása vagy referenciaadatok lekérése külső forrásból.
* Egyéni eseményformátum szerializálása vagy deszerializálása bemenetekben/kimenetekben.
* Egyéni összesítések létrehozása.

Jóllehet az olyan függvények, mint a **Date.GetDate()** vagy a **Math.random()** nincsenek blokkolva a függvénydefinícióban, kerülje a használatukat. Ezek a függvények **nem** minden alkalommal ugyanazt az eredményt adják vissza a meghívásukkor, az Azure Stream Analytics szolgáltatás pedig nem naplózza a függvények meghívását és a visszaadott eredményeket. Ha egy függvény eltérő eredményeket ad vissza ugyanazon eseményekre vonatkozóan, az ismételhetőség nem garantált, ha Ön vagy a Stream Analytics szolgáltatás újraindítja a feladatot.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Felhasználói JavaScript-függvény hozzáadása az Azure Portalon
Egy egyszerű felhasználói JavaScript-függvény alapján egy meglévő Stream Analytics-feladat létrehozásához kövesse az alábbi lépéseket:

> [!NOTE]
> Ezek a lépések a a Stream Analytics-feladatok a felhőben való futtatásra konfigurálva. Ha a Stream Analytics-feladat futtatása az Azure IoT Edge-ben van konfigurálva, helyette használja a Visual Studio és [írása, a felhasználó által definiált függvény használatával C# ](stream-analytics-edge-csharp-udf.md).

1.  Keresse meg a Stream Analytics-feladatot az Azure Portalon.

2. Alatt a **feladattopológia** szakaszban kattintson **funkciók**. Megjelenik egy üres függvénylista.

3.  Egy új felhasználói függvény létrehozásához válassza **+ Hozzáadás**.

4.  Az **Új függvény** panel **Függvénytípus** részén válassza a **JavaScript** lehetőséget. A szerkesztőben megjelenik egy alapértelmezett függvénysablon.

5.  Az **UDF-alias** mezőben adja meg a **hex2Int** értéket, és módosítsa a függvény implementálását az alábbiak szerint:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Kattintson a **Mentés** gombra. A függvény megjelenik a függvénylistában.
7.  Válassza ki az új **hex2int** függvényt, és ellenőrizze a függvény definícióját. Minden függvény az aliasához hozzáadott **UDF** előtaggal rendelkezik. Ezt az *előtagot meg kell adni*, amikor a Stream Analytics-lekérdezésben meghívja a függvényt. Ebben az esetben az **UDF.hex2Int** függvényt hívjuk meg.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Felhasználói JavaScript-függvény meghívása lekérdezésben

1. A lekérdezés-szerkesztőben alatt a **feladattopológia** szakaszban kattintson **lekérdezés**.
2.  Módosítsa a lekérdezést, majd hívja meg a felhasználói függvényt az alábbiak szerint:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  A mintaadatfájl feltöltéséhez kattintson a jobb gombbal a feladat bemenetére.
4.  A lekérdezés teszteléséhez válassza a **Teszt** elemet.


## <a name="supported-javascript-objects"></a>Támogatott JavaScript-objektumok
Az Azure Stream Analytics felhasználói JavaScript-függvényei támogatják a szabványos, beépített JavaScript-objektumokat. Az objektumok listáját a [globális objektumokkal](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) foglalkozó cikkben tekintheti meg.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Típusátalakítás a Stream Analytics és a JavaScript között

A Stream Analytics lekérdezési nyelv és a JavaScript által támogatott típusok között különbségek vannak. Az alábbi táblázat a kettő közötti átalakítás megfeleléseit tartalmazza:

Stream Analytics | JavaScript
--- | ---
bigint | Szám (a JavaScript legfeljebb pontosan a 2^53-ig tudja a számokat kezelni)
DateTime | Dátum (a JavaScript csak az ezredmásodperceket támogatja)
double | Szám
nvarchar(MAX) | Karakterlánc
Record | Objektum
Tömb | Tömb
NULL | Null


A JavaScriptről a Stream Analyticsre történő átalakítások:


JavaScript | Stream Analytics
--- | ---
Szám | Bigint (ha a szám kerek és a long.MinValue és a long.MaxValue közé esik, máskülönben double)
Dátum | DateTime
Karakterlánc | nvarchar(MAX)
Objektum | Record
Tömb | Tömb
Null, nem definiált | NULL
Bármely más típus (például függvény vagy hiba) | Nem támogatott (futásidejű hibát eredményez)

## <a name="troubleshooting"></a>Hibaelhárítás
A JavaScript futásidejű hibái végzetesnek minősülnek, és a tevékenységnaplóban tekinthetők meg. A napló lekéréséhez lépjen a feladatra az Azure Portalon, és válassza a **Tevékenységnapló** elemet.


## <a name="other-javascript-user-defined-function-patterns"></a>Egyéb felhasználói JavaScript-függvényminták

### <a name="write-nested-json-to-output"></a>Beágyazott JSON írása a kimenetbe
Ha van még egy további feldolgozási lépés, amely a Stream Analytics-feladat kimenetét használja a bemeneteként, és JSON-formátumot igényel, a kimenetbe írhat JSON-sztringet. A következő példa a **JSON.stringify()** függvény meghívásával becsomagolja a bemenetben lévő összes név/érték párt, majd egyetlen sztringértékként írja őket a kimenetbe.

**Felhasználói JavaScript-függvény definíciója:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Mintalekérdezés:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megelőzheti a feladat által használt streamelési egységek kiszámlázását. Ha a jövőben szeretné még használni a feladatot, leállíthatja, és később újraindíthatja, amikor szüksége lesz rá. Ha többé már nem kívánja használni ezt a feladatot, az alábbi lépéseket követve törölheti a gyors útmutató által létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="get-help"></a>Segítségkérés
További segítségért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyszerű felhasználói JavaScript-függvényt futtató Stream Analytics-feladatot. A Stream Analytics szolgáltatással kapcsolatos további információkért folytassa a valós idejű forgatókönyveket ismertető cikkekkel:

> [!div class="nextstepaction"]
> [Valós idejű Twitter-hangulatelemzés az Azure Stream Analytics szolgáltatásban](stream-analytics-twitter-sentiment-analysis-trends.md)
