---
title: Paraméterek és kifejezések használata a Azure Data Factoryban
description: Ez a cikk a adat-előállító entitások létrehozásakor használható kifejezésekkel és függvényekkel kapcsolatos információkat tartalmaz.
author: ssabat
ms.author: susabat
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 8f22645eafa0969eac3d6c4c0645909f8c650cad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199817"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Paraméterek, kifejezések és függvények használata a Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-functions-variables.md)
> * [Aktuális verzió](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben a dokumentumban elsősorban a különböző példákkal foglalkozó alapfogalmakat mutatjuk be, hogy felderítse, hogyan hozhat létre paraméteres adatfolyamatokat Azure Data Factoryon belül. A paraméterezés és a dinamikus kifejezések az ADF-hez hasonló jelentős kiegészítések, mivel rengeteg időt takarítanak meg, és lehetővé teszik egy sokkal rugalmasabb kinyerési, átalakítási, betöltési (ETL) vagy kinyerési, betöltési és átalakítási (ELT) megoldás használatát, ami jelentősen csökkenti a megoldás karbantartásának költségeit, és felgyorsítja az új funkciók megvalósítását a meglévő folyamatokban Ezek a nyereségek azért vannak, mert a paraméterezés minimálisra csökkentheti a merevlemezek mennyiségét, és növeli a megoldás újrafelhasználható objektumainak és folyamatainak számát.

## <a name="azure-data-factory-ui-and-parameters"></a>Azure-beli adatok gyári felhasználói felülete és paraméterei

Ha még nem ismeri az Azure-beli adat-előállító paramétert az ADF felhasználói felületén, tekintse át a [adat-előállító felhasználói felületét](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui)  a társított szolgáltatások paramétereit és a [adat-előállító felhasználói felületét a metaadatok vezérelt folyamatához](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) , a vizualizációs magyarázattal.

## <a name="parameter-and-expression-concepts"></a>Paraméterek és kifejezések fogalmak 

A paraméterekkel külső értékeket is átadhat a folyamatoknak, az adatkészleteknek, a társított szolgáltatásoknak és az adatfolyamatoknak. Miután a paramétert átadta az erőforrásnak, nem módosítható. Az erőforrások parameterizing az egyes időpontokban különböző értékekkel újra felhasználhatja őket. A paraméterek egyénileg vagy kifejezések részeként is használhatók. A definícióban található JSON-értékek lehetnek olyan literálok vagy kifejezések, amelyek kiértékelése futásidőben történik.

Például:  
  
```json
"name": "value"
```

 vagy  
  
```json
"name": "@pipeline().parameters.password"
```

A kifejezések egy JSON-karakterlánc értékében bárhol megjelenhetnek, és mindig egy másik JSON-értéket eredményeznek. Itt a *jelszó* egy folyamat paraméter a kifejezésben. Ha egy JSON-érték egy kifejezés, a rendszer kinyeri a kifejezés törzsét az at-Sign ( \@ ) eltávolításával. Ha olyan literális karakterláncra van szükség, amely a-vel kezdődik \@ , akkor azt a használatával kell megmenekülnie \@ \@ . Az alábbi példák bemutatják a kifejezések kiértékelésének módját.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|paraméterek|A rendszer a "parameters" karaktert adja vissza.|  
|"paraméterek [1]"|A rendszer a "Parameters [1]" karaktert adja vissza.|  
|"\@\@"|Egy "" karaktert tartalmazó 1 karakterből álló karakterláncot \@ ad vissza.|  
|" \@"|A rendszer 2 karakterből álló karakterláncot ad vissza, amely tartalmazza a következőt: " \@ ".|  
  
 A kifejezések a karakterláncok belsejében is megjelenhetnek, amely egy *karakterlánc-interpolációs* funkciót használ, ahol a kifejezések beburkoltak `@{ ... }` . Például: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 A karakterlánc-interpoláció használatakor az eredmény mindig karakterlánc. Tegyük fel `myNumber` , hogy a következőképpen definiáltam `42`  `myString`  `foo` :  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|" \@ folyamat (). Parameters. sajatString"| `foo`Karakterláncként tér vissza.|  
|" \@ {folyamat (). Parameters. sajatString}"| `foo`Karakterláncként tér vissza.|  
|" \@ folyamat (). Parameters. myNumber"| `42` *Számként* adja vissza.|  
|" \@ {folyamat (). Parameters. myNumber}"| `42` *Karakterláncként* tér vissza.|  
|"Válasz: @ {pipeline (). Parameters. myNumber}"| A karakterláncot adja vissza `Answer is: 42` .|  
|" \@ concat (' válasz: ', string (folyamat (). Parameters. myNumber))"| A karakterláncot adja vissza. `Answer is: 42`|  
|"Válasz: \@ \@ {pipeline (). Parameters. myNumber}"| A karakterláncot adja vissza `Answer is: @{pipeline().parameters.myNumber}` .|  

## <a name="examples-of-using-parameters-in-expressions"></a>Példák paraméterek használatára kifejezésekben 

### <a name="complex-expression-example"></a>Összetett kifejezés példája
Az alábbi példa egy összetett példát mutat be, amely a tevékenység kimenetének mély alterületére hivatkozik. Ha olyan folyamat-paraméterre szeretne hivatkozni, amely kiértékel egy almezőt, használja a [] szintaxist a dot (.) operátor helyett (subfield1 és subfield2 esetén).

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Dinamikus tartalomkezelés

A dinamikus tartalomkezelés a Szerkesztés befejeződése után automatikusan elvégzi a tartalomban lévő karakterek elmenekülését. A Content Editor következő tartalma például egy karakterlánc-interpoláció két Expression függvénysel. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

A dinamikus tartalomkezelési szerkesztő átalakítja a fenti tartalmat a kifejezésre `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . A kifejezés eredménye egy JSON formátumú karakterlánc, amely a következőt mutatja be.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Paraméterekkel rendelkező adatkészlet

A következő példában a BlobDataset egy **path** nevű paramétert vesz fel. Az érték a **folderPath** tulajdonság értékének megadására szolgál a következő kifejezés használatával: `dataset().path` . 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Paraméterekkel rendelkező folyamat

A következő példában a folyamat **inputPath** és **outputPath** paramétereket használ. A paraméteres blob-adatkészlet **elérési útja** a paraméterek értékeinek használatával van beállítva. Az itt használt szintaxis a következő: `pipeline().parameters.parametername` . 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Függvények hívása kifejezéseken belül 

A függvényeket a kifejezéseken belül hívhatja. A következő szakaszokban információt talál a kifejezésekben használható függvényekről.  

### <a name="string-functions"></a>Sztringfüggvények  

A sztringek használatához használhatja ezeket a karakterlánc-függvényeket és néhány [gyűjteményi funkciót](#collection-functions)is.
A karakterlánc-függvények csak karakterláncokon működnek.

| Karakterlánc-függvény | Feladat |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Egyesítse kettő vagy több karakterláncot, és állítsa vissza az egyesített karakterláncot. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Győződjön meg arról, hogy a karakterlánc a megadott alkarakterlánccal végződik-e. |
| [guid](control-flow-expression-language-functions.md#guid) | Globálisan egyedi azonosító (GUID) létrehozása karakterláncként. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Egy alsztring kezdő pozíciójának visszaadása. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Egy alsztring utolsó előfordulásának kezdő pozíciójának visszaadása. |
| [csere](control-flow-expression-language-functions.md#replace) | Cserélje le az alsztringet a megadott sztringre, és adja vissza a frissített karakterláncot. |
| [felosztása](control-flow-expression-language-functions.md#split) | Egy olyan tömböt ad vissza, amely vesszővel elválasztott alsztringeket tartalmaz, egy nagyobb karakterláncból, amely az eredeti karakterlánc megadott elválasztói karaktere alapján van megadva. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Győződjön meg róla, hogy egy sztring egy adott alkarakterlánccal kezdődik-e. |
| [substring](control-flow-expression-language-functions.md#substring) | Karakterek visszaadása egy karakterláncból a megadott pozíciótól kezdődően. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Karakterláncot ad vissza kisbetűs formátumban. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Karakterláncot ad vissza nagybetűs formátumban. |
| [Trim](control-flow-expression-language-functions.md#trim) | Távolítsa el a kezdő és záró szóközt egy karakterláncból, és küldje vissza a frissített karakterláncot. |

### <a name="collection-functions"></a>Gyűjteményfüggvények

A gyűjtemények, általában tömbök, karakterláncok és esetenként a szótárak használatával a következő gyűjtemény-függvények használhatók.

| Gyűjtési függvény | Feladat |
| ------------------- | ---- |
| [tartalmaz](control-flow-expression-language-functions.md#contains) | Győződjön meg arról, hogy egy gyűjteménynek van-e konkrét eleme. |
| [üres](control-flow-expression-language-functions.md#empty) | Győződjön meg arról, hogy a gyűjtemény üres. |
| [első](control-flow-expression-language-functions.md#first) | Egy gyűjtemény első elemének visszaadása. |
| [kereszteződés](control-flow-expression-language-functions.md#intersection) | Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjtemények közös elemeit tartalmazta. |
| [csatlakozás](control-flow-expression-language-functions.md#join) | Egy olyan sztringet ad vissza, amely egy tömb *összes* elemét a megadott karakterrel elválasztva. |
| [utolsó](control-flow-expression-language-functions.md#last) | Egy gyűjtemény utolsó elemének visszaadása. |
| [length](control-flow-expression-language-functions.md#length) (hossz) | Egy sztringben vagy tömbben lévő elemek számának visszaadása. |
| [kihagyása](control-flow-expression-language-functions.md#skip) | Elemek eltávolítása egy gyűjtemény elejéről, és *az összes többi* elem visszaadása. |
| [eltarthat](control-flow-expression-language-functions.md#take) | Elemek visszaküldése egy gyűjtemény elejéről. |
| [Union](control-flow-expression-language-functions.md#union) | Olyan gyűjteményt ad vissza, amely a megadott gyűjtemények *összes* elemét tartalmazta. | 

### <a name="logical-functions"></a>Logikai függvények  

Ezek a függvények a feltételeken belül hasznosak lehetnek, és bármilyen típusú logikát kiértékelnek.  
  
| Logikai összehasonlító függvény | Feladat |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Győződjön meg arról, hogy az összes kifejezés igaz-e. |
| [egyenlő](control-flow-expression-language-functions.md#equals) | Győződjön meg arról, hogy mindkét érték egyenértékű-e. |
| [greater](control-flow-expression-language-functions.md#greater) | Győződjön meg arról, hogy az első érték nagyobb, mint a második érték. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Győződjön meg arról, hogy az első érték nagyobb vagy egyenlő, mint a második érték. |
| [Ha](control-flow-expression-language-functions.md#if) | Győződjön meg arról, hogy a kifejezés igaz vagy hamis. Az eredmény alapján adja vissza a megadott értéket. |
| [kisebb](control-flow-expression-language-functions.md#less) | Győződjön meg arról, hogy az első érték kisebb a második értéknél. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Győződjön meg arról, hogy az első érték kisebb vagy egyenlő, mint a második érték. |
| [nem](control-flow-expression-language-functions.md#not) | Győződjön meg arról, hogy egy kifejezés hamis-e. |
| [vagy](control-flow-expression-language-functions.md#or) | Győződjön meg arról, hogy legalább egy kifejezés igaz értékű-e. |
  
### <a name="conversion-functions"></a>Konverziós függvények  

 Ezek a függvények a nyelvben lévő natív típusok közötti átalakításra szolgálnak:  
-   sztring
-   egész szám
-   float
-   boolean
-   tömbök
-   szótárak

| Átalakítási függvény | Feladat |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Tömb visszaadása egyetlen megadott bemenetből. Több bemenet esetén lásd: [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Egy sztring Base64 kódolású verziójának visszaadása. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Egy Base64 kódolású karakterlánc bináris verziójának visszaadása. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [bináris](control-flow-expression-language-functions.md#binary) | Egy bemeneti érték bináris verziójának visszaadása. |
| [logikai](control-flow-expression-language-functions.md#bool) | Egy bemeneti érték logikai verziójának visszaadása. |
| [összefonódik](control-flow-expression-language-functions.md#coalesce) | Az első nem null értéket ad vissza egy vagy több paraméterből. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Tömb visszaadása több bemenetből. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Egy bemeneti értékhez tartozó adat URI-azonosítójának visszaadása. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Egy adaturi-fájl bináris verziójának visszaadása. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Egy adaturi-azonosító karakterlánc-verziójának visszaadása. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Egy adaturi-fájl bináris verziójának visszaadása. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Olyan karakterláncot ad vissza, amely lecseréli az Escape-karaktereket a dekódolású verziókra. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Egy olyan karakterláncot ad vissza, amely lecseréli az URL-nem biztonságos karaktereket Escape-karakterekkel. |
| [float](control-flow-expression-language-functions.md#float) | Lebegőpontos szám visszaadása egy bemeneti értékhez. |
| [int](control-flow-expression-language-functions.md#int) | Egy sztring egész változatának visszaadása. |
| [JSON](control-flow-expression-language-functions.md#json) | A JavaScript Object Notation (JSON) típusú értéket vagy objektumot adja vissza egy karakterlánc vagy XML számára. |
| [sztring](control-flow-expression-language-functions.md#string) | Egy bemeneti érték karakterlánc-verziójának visszaadása. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Az URI-kódolású verziót adja vissza egy bemeneti értékhez az URL-nem biztonságos karakterek a Escape-karakterekkel való lecserélésével. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Egy URI-kódolású karakterlánc bináris verziójának visszaadása. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | URI-kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [XML](control-flow-expression-language-functions.md#xml) | Egy sztring XML-verziójának visszaadása. |
| [XPath](control-flow-expression-language-functions.md#xpath) | Az XML-t az XPath (XML-elérésiút) kifejezésnek megfelelő csomópontok vagy értékek esetében adja meg, és adja vissza a megfelelő csomópontokat vagy értékeket. |

### <a name="math-functions"></a>Matematikai függvények  
 Ezek a függvények bármelyik típusú számhoz használhatók: **egész** számok és **lebegőpontok**.  

| Matematikai függvény | Feladat |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Az eredmény visszaadása két szám hozzáadásával. |
| [div](control-flow-expression-language-functions.md#div) | Az eredmény visszaadása két szám elosztásával. |
| [Max](control-flow-expression-language-functions.md#max) | A legmagasabb értéket adja vissza számokból vagy tömbből. |
| [p](control-flow-expression-language-functions.md#min) | A legkisebb értéket adja vissza számokból vagy tömbből. |
| [mod](control-flow-expression-language-functions.md#mod) | A maradékot a két szám felosztásával állítsa vissza. |
| [mul](control-flow-expression-language-functions.md#mul) | A termék visszaküldése két szám szorzatával. |
| [Rand](control-flow-expression-language-functions.md#rand) | Egy véletlenszerű egész számot ad vissza egy megadott tartományból. |
| [tartomány](control-flow-expression-language-functions.md#range) | Egy egész tömböt ad vissza, amely egy megadott egész számból indul. |
| [Sub](control-flow-expression-language-functions.md#sub) | Az eredmény visszaadása a második számnak az első számból való kivonásával. |
  
### <a name="date-functions"></a>Dátumfüggvények  

| Dátum vagy idő függvény | Feladat |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Adja hozzá a napok számát egy időbélyeghez. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Adja meg az időbélyegzőhöz tartozó órák számát. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Adjon hozzá néhány percet egy időbélyeghez. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Adjon meg néhány másodpercet egy időbélyeghez. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Több időegység hozzáadása egy időbélyeghez. Lásd még: [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Az időbélyeget az egyezményes világidő (UTC) alapján alakítsa át a cél időzónára. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | A forrás időzóna időbélyegének konvertálása a cél időzónára. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | A forrás időzóna időbélyegének konvertálása az egyezményes világidő (UTC) szerinti időpontra |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | A hónap napjának visszaadása időbélyeg alapján. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Egy időbélyegből a hét napjának visszaadása. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Az év napjának visszaadása egy időbélyegből. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Az időbélyeget karakterláncként, opcionális formátumban kell visszaadnia. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Az aktuális időbélyeg és a megadott időegységek visszaadása. Lásd még: [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Az aktuális időbélyeg visszaadása a megadott időegységek mínusz. Lásd még: [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Egy időbélyeghez tartozó nap kezdetének visszaadása. |
| [Óra kezdete](control-flow-expression-language-functions.md#startOfHour) | Az óra kezdetének visszaadása egy időbélyeghez. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | A hónap kezdetének visszaadása egy időbélyeghez. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Több időegység kivonása egy időbélyegből. Lásd még: [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | A `ticks` tulajdonság értékének visszaadása egy megadott időbélyeghez. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Az aktuális időbélyeg visszaadása karakterláncként. |

## <a name="detailed-examples-for-practice"></a>Részletes példák a gyakorlatra

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Részletes Azure-beli adatfeldolgozó másolási folyamat paraméterekkel 

Ez az [Azure-beli adatfeldolgozó másolási folyamata az oktatóanyag](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) elvégzésével végigvezeti Önt egy folyamat és tevékenység, valamint a tevékenységek közötti paraméterek átadásának lépésein.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Részletes leképezési adatfolyam-folyamat paraméterekkel 

A paraméterek az adatfolyamban való használatának módjával kapcsolatban kövesse az [adatforgalom leképezése](https://docs.microsoft.com/azure/data-factory/parameters-data-flow) az átfogó példa paraméterekkel című szakaszát.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Részletes metaadatok vezérelt folyamat paraméterekkel

Adja meg a [metaadatok vezérelt folyamatát paraméterekkel](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization) , és tudjon meg többet arról, hogyan használhatók paraméterek a metaadatokat használó folyamatok kialakításához. Ez egy népszerű használati eset a paraméterekhez.


## <a name="next-steps"></a>Következő lépések
A kifejezésekben használható rendszerváltozók listáját a [rendszerváltozók](control-flow-system-variables.md)című részben tekintheti meg.
