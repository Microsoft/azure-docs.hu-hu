---
title: Kifejezés és függvények a Azure Data Factoryban
description: Ez a cikk a adat-előállító entitások létrehozásához használható kifejezésekkel és függvényekkel kapcsolatos információkat tartalmazza.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9870b239ca0501e63df3d800b8e4847cb0f390ac
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860936"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Kifejezések és függvények az Azure Data Factoryben

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-functions-variables.md)
> * [Aktuális verzió](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory által támogatott kifejezések és függvények részleteit ismerteti. 

## <a name="expressions"></a>Kifejezések

A definícióban található JSON-értékek lehetnek olyan literálok vagy kifejezések, amelyek kiértékelése futásidőben történik. Például:  
  
```json
"name": "value"
```

 vagy  
  
```json
"name": "@pipeline().parameters.password"
```

A kifejezések egy JSON-karakterlánc értékében bárhol megjelenhetnek, és mindig egy másik JSON-értéket eredményeznek. Ha egy JSON-érték egy kifejezés, a rendszer kinyeri a kifejezés törzsét az at-Sign ( \@ ) eltávolításával. Ha olyan literális karakterláncra van szükség, amely a-vel kezdődik \@ , akkor azt a használatával kell megmenekülnie \@ \@ . Az alábbi példák bemutatják a kifejezések kiértékelésének módját.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|paraméterek|A rendszer a "parameters" karaktert adja vissza.|  
|"paraméterek [1]"|A rendszer a "Parameters [1]" karaktert adja vissza.|  
|"\@\@"|Egy "" karaktert tartalmazó 1 karakterből álló karakterláncot \@ ad vissza.|  
|" \@"|A rendszer 2 karakterből álló karakterláncot ad vissza, amely tartalmazza a következőt: " \@ ".|  
  
 A kifejezések a karakterláncok belsejében is megjelenhetnek, amely egy *karakterlánc-interpolációs* funkciót használ, ahol a kifejezések beburkoltak `@{ ... }` . Például:`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 A karakterlánc-interpoláció használatakor az eredmény mindig karakterlánc. Tegyük fel `myNumber` , hogy a következőképpen definiáltam `42` `myString` `foo` :  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|" \@ folyamat (). Parameters. sajatString"| `foo`Karakterláncként tér vissza.|  
|" \@ {folyamat (). Parameters. sajatString}"| `foo`Karakterláncként tér vissza.|  
|" \@ folyamat (). Parameters. myNumber"| `42` *Számként*adja vissza.|  
|" \@ {folyamat (). Parameters. myNumber}"| `42` *Karakterláncként*tér vissza.|  
|"Válasz: @ {pipeline (). Parameters. myNumber}"| A karakterláncot adja vissza `Answer is: 42` .|  
|" \@ concat (' válasz: ', string (folyamat (). Parameters. myNumber))"| A karakterláncot adja vissza.`Answer is: 42`|  
|"Válasz: \@ \@ {pipeline (). Parameters. myNumber}"| A karakterláncot adja vissza `Answer is: @{pipeline().parameters.myNumber}` .|  
  
## <a name="examples"></a>Példák

### <a name="complex-expression-example"></a>Összetett kifejezés példája
Az alábbi példa egy összetett példát mutat be, amely a tevékenység kimenetének mély alterületére hivatkozik. Ha olyan folyamat-paraméterre szeretne hivatkozni, amely kiértékel egy almezőt, használja a [] szintaxist a dot (.) operátor helyett (subfield1 és subfield2 esetén).

@activity({activityName}). output. {subfield1}. {subfield2} [folyamat (). Parameters. subfield3]. {subfield4}

### <a name="a-dataset-with-a-parameter"></a>Egy paraméterrel rendelkező adatkészlet
A következő példában a BlobDataset egy **path**nevű paramétert vesz fel. Az érték a **folderPath** tulajdonság értékének megadására szolgál a következő kifejezés használatával: `dataset().path` . 

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

### <a name="a-pipeline-with-a-parameter"></a>Egy paraméterrel rendelkező folyamat
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
### <a name="tutorial"></a>Oktatóanyag
Ez az [oktatóanyag](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) bemutatja, hogyan adhat át paramétereket egy folyamat és tevékenység között, valamint a tevékenységek között.

  
## <a name="functions"></a>Functions

A függvényeket a kifejezéseken belül hívhatja. A következő szakaszokban információt talál a kifejezésekben használható függvényekről.  

## <a name="string-functions"></a>Sztringfüggvények  

A sztringek használatához használhatja ezeket a karakterlánc-függvényeket és néhány [gyűjteményi funkciót](#collection-functions)is.
A karakterlánc-függvények csak karakterláncokon működnek.

| Karakterlánc-függvény | Tevékenység |
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

## <a name="collection-functions"></a>Gyűjtési függvények

A gyűjtemények, általában tömbök, karakterláncok és esetenként a szótárak használatával a következő gyűjtemény-függvények használhatók.

| Gyűjtési függvény | Tevékenység |
| ------------------- | ---- |
| [tartalmaz](control-flow-expression-language-functions.md#contains) | Győződjön meg arról, hogy egy gyűjteménynek van-e konkrét eleme. |
| [üres](control-flow-expression-language-functions.md#empty) | Győződjön meg arról, hogy a gyűjtemény üres. |
| [első](control-flow-expression-language-functions.md#first) | Egy gyűjtemény első elemének visszaadása. |
| [kereszteződés](control-flow-expression-language-functions.md#intersection) | Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjtemények közös elemeit tartalmazta. |
| [csatlakozás](control-flow-expression-language-functions.md#join) | Egy olyan sztringet ad vissza, amely egy tömb *összes* elemét a megadott karakterrel elválasztva. |
| [utolsó](control-flow-expression-language-functions.md#last) | Egy gyűjtemény utolsó elemének visszaadása. |
| [hossza](control-flow-expression-language-functions.md#length) | Egy sztringben vagy tömbben lévő elemek számának visszaadása. |
| [kihagyása](control-flow-expression-language-functions.md#skip) | Elemek eltávolítása egy gyűjtemény elejéről, és *az összes többi* elem visszaadása. |
| [eltarthat](control-flow-expression-language-functions.md#take) | Elemek visszaküldése egy gyűjtemény elejéről. |
| [Union](control-flow-expression-language-functions.md#union) | Olyan gyűjteményt ad vissza, amely a megadott gyűjtemények *összes* elemét tartalmazta. | 

## <a name="logical-functions"></a>Logikai függvények  

Ezek a függvények a feltételeken belül hasznosak lehetnek, és bármilyen típusú logikát kiértékelnek.  
  
| Logikai összehasonlító függvény | Tevékenység |
| --------------------------- | ---- |
| [és](control-flow-expression-language-functions.md#and) | Győződjön meg arról, hogy az összes kifejezés igaz-e. |
| [egyenlő](control-flow-expression-language-functions.md#equals) | Győződjön meg arról, hogy mindkét érték egyenértékű-e. |
| [greater](control-flow-expression-language-functions.md#greater) | Győződjön meg arról, hogy az első érték nagyobb, mint a második érték. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Győződjön meg arról, hogy az első érték nagyobb vagy egyenlő, mint a második érték. |
| [Ha](control-flow-expression-language-functions.md#if) | Győződjön meg arról, hogy a kifejezés igaz vagy hamis. Az eredmény alapján adja vissza a megadott értéket. |
| [less](control-flow-expression-language-functions.md#less) | Győződjön meg arról, hogy az első érték kisebb a második értéknél. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Győződjön meg arról, hogy az első érték kisebb vagy egyenlő, mint a második érték. |
| [nem](control-flow-expression-language-functions.md#not) | Győződjön meg arról, hogy egy kifejezés hamis-e. |
| [vagy](control-flow-expression-language-functions.md#or) | Győződjön meg arról, hogy legalább egy kifejezés igaz értékű-e. |
  
## <a name="conversion-functions"></a>Átalakítási függvények  

 Ezek a függvények a nyelvben lévő natív típusok közötti átalakításra szolgálnak:  
-   sztring
-   egész szám
-   lebegőpontos
-   logikai
-   tömbök
-   szótárak

| Átalakítási függvény | Tevékenység |
| ------------------- | ---- |
| [tömb](control-flow-expression-language-functions.md#array) | Tömb visszaadása egyetlen megadott bemenetből. Több bemenet esetén lásd: [createArray](control-flow-expression-language-functions.md#createArray). |
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
| [karakterlánc](control-flow-expression-language-functions.md#string) | Egy bemeneti érték karakterlánc-verziójának visszaadása. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Az URI-kódolású verziót adja vissza egy bemeneti értékhez az URL-nem biztonságos karakterek a Escape-karakterekkel való lecserélésével. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Egy URI-kódolású karakterlánc bináris verziójának visszaadása. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | URI-kódolású karakterlánc karakterlánc-verziójának visszaadása. |
| [XML](control-flow-expression-language-functions.md#xml) | Egy sztring XML-verziójának visszaadása. |
| [XPath](control-flow-expression-language-functions.md#xpath) | Az XML-t az XPath (XML-elérésiút) kifejezésnek megfelelő csomópontok vagy értékek esetében adja meg, és adja vissza a megfelelő csomópontokat vagy értékeket. |

## <a name="math-functions"></a>Matematikai függvények  
 Ezek a függvények bármelyik típusú számhoz használhatók: **egész** számok és **lebegőpontok**.  

| Matematikai függvény | Tevékenység |
| ------------- | ---- |
| [hozzáadása](control-flow-expression-language-functions.md#add) | Az eredmény visszaadása két szám hozzáadásával. |
| [div](control-flow-expression-language-functions.md#div) | Az eredmény visszaadása két szám elosztásával. |
| [Max](control-flow-expression-language-functions.md#max) | A legmagasabb értéket adja vissza számokból vagy tömbből. |
| [min](control-flow-expression-language-functions.md#min) | A legkisebb értéket adja vissza számokból vagy tömbből. |
| [mod](control-flow-expression-language-functions.md#mod) | A maradékot a két szám felosztásával állítsa vissza. |
| [mul](control-flow-expression-language-functions.md#mul) | A termék visszaküldése két szám szorzatával. |
| [Rand](control-flow-expression-language-functions.md#rand) | Egy véletlenszerű egész számot ad vissza egy megadott tartományból. |
| [tartomány](control-flow-expression-language-functions.md#range) | Egy egész tömböt ad vissza, amely egy megadott egész számból indul. |
| [Sub](control-flow-expression-language-functions.md#sub) | Az eredmény visszaadása a második számnak az első számból való kivonásával. |
  
## <a name="date-functions"></a>Dátumfüggvények  

| Dátum vagy idő függvény | Tevékenység |
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

## <a name="function-reference"></a>Függvények leírása

Ez a szakasz felsorolja az összes rendelkezésre álló függvényt betűrendbe rendezve.

<a name="add"></a>

### <a name="add"></a>add

Az eredmény visszaadása két szám hozzáadásával.

```
add(<summand_1>, <summand_2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Igen | Egész szám, lebegőpontos vagy vegyes | A hozzáadandó számok |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| <*eredmény – összeg*> | Egész vagy lebegőpontos | A megadott számok hozzáadásának eredménye |
||||

*Például*

Ez a példa hozzáadja a megadott számokat:

```
add(1, 1.5)
```

És visszaadja ezt az eredményt:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adja hozzá a napok számát egy időbélyeghez.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*nap*> | Igen | Egész szám | A hozzáadni kívánt napok pozitív vagy negatív száma |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú nap  |
||||

*1\. példa*

Ez a példa 10 napot ad hozzá a megadott időbélyeghez:

```
addDays('2018-03-15T13:00:00Z', 10)
```

És visszaadja ezt az eredményt:`"2018-03-25T00:00:0000000Z"`

*2\. példa*

Ez a példa öt napot kivon a megadott időbélyegből:

```
addDays('2018-03-15T00:00:00Z', -5)
```

És visszaadja ezt az eredményt:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adja meg az időbélyegzőhöz tartozó órák számát.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*óra*> | Igen | Egész szám | A hozzáadni kívánt órák pozitív vagy negatív száma |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú óra  |
||||

*1\. példa*

Ez a példa 10 órát ad hozzá a megadott időbélyeghez:

```
addHours('2018-03-15T00:00:00Z', 10)
```

És visszaadja ezt az eredményt:`"2018-03-15T10:00:0000000Z"`

*2\. példa*

Ez a példa öt órát kivon a megadott időbélyegből:

```
addHours('2018-03-15T15:00:00Z', -5)
```

És visszaadja ezt az eredményt:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adjon hozzá néhány percet egy időbélyeghez.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*perc*> | Igen | Egész szám | A hozzáadni kívánt percek pozitív vagy negatív száma |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú perc |
||||

*1\. példa*

Ez a példa 10 percet ad hozzá a megadott időbélyeghez:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

És visszaadja ezt az eredményt:`"2018-03-15T00:20:00.0000000Z"`

*2\. példa*

Ez a példa öt percet kivon a megadott időbélyegből:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

És visszaadja ezt az eredményt:`"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adjon meg néhány másodpercet egy időbélyeghez.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*másodperc*> | Igen | Egész szám | A hozzáadni kívánt másodpercek pozitív vagy negatív száma |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú másodperc  |
||||

*1\. példa*

Ez a példa 10 másodpercet ad hozzá a megadott időbélyeghez:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

És visszaadja ezt az eredményt:`"2018-03-15T00:00:10.0000000Z"`

*2\. példa*

Ez a példa öt másodpercet kivon a megadott időbélyegre:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

És visszaadja ezt az eredményt:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Több időegység hozzáadása egy időbélyeghez.
Lásd még: [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*időköz*> | Igen | Egész szám | A hozzáadandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg és a megadott számú időegység  |
||||

*1\. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

És visszaadja ezt az eredményt:`"2018-01-02T00:00:00.0000000Z"`

*2\. példa*

Ez a példa egy napot ad hozzá a megadott időbélyeghez:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

És visszaadja az eredményt a nem kötelező "D" formátum használatával:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>és

Győződjön meg arról, hogy az összes kifejezés igaz-e.
Igaz értéket ad vissza, ha az összes kifejezés igaz, vagy hamis értéket ad vissza, ha legalább egy kifejezés hamis.

```
and(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*Kifejezés2*>,... | Igen | Logikai érték | Az ellenőrzési kifejezések |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | -----| ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha az összes kifejezés igaz. Hamis értéket ad vissza, ha legalább egy kifejezés hamis. |
||||

*1\. példa*

Ezek a példák azt jelzik, hogy a megadott logikai értékek mind igazak-e:

```
and(true, true)
and(false, true)
and(false, false)
```

És a következő eredményeket adja vissza:

* Első példa: mindkét kifejezés igaz, így visszatér `true` .
* Második példa: az egyik kifejezés hamis, így visszatér `false` .
* Harmadik példa: mindkét kifejezés hamis, így visszatér `false` .

*2\. példa*

Ezek a példák azt jelzik, hogy a megadott kifejezések mind igazak-e:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

És a következő eredményeket adja vissza:

* Első példa: mindkét kifejezés igaz, így visszatér `true` .
* Második példa: az egyik kifejezés hamis, így visszatér `false` .
* Harmadik példa: mindkét kifejezés hamis, így visszatér `false` .

<a name="array"></a>

### <a name="array"></a>tömb

Tömb visszaadása egyetlen megadott bemenetből.
Több bemenet esetén lásd: [createArray ()](#createArray).

```
array('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Tömb létrehozásához használt karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*érték*>] | Tömb | Egyetlen megadott bemenetet tartalmazó tömb |
||||

*Például*

Ez a példa egy tömböt hoz létre a "Hello" sztringből:

```
array('hello')
```

És visszaadja ezt az eredményt:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Egy sztring Base64 kódolású verziójának visszaadása.

```
base64('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A bemeneti sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Base64-karakterlánc*> | Sztring | A bemeneti karakterlánc Base64 kódolású verziója |
||||

*Például*

Ez a példa a "Hello" karakterláncot Base64 kódolású karakterlánccá alakítja:

```
base64('hello')
```

És visszaadja ezt az eredményt:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Egy Base64 kódolású karakterlánc bináris verziójának visszaadása.

```
base64ToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-for-Base64-string*> | Sztring | A Base64 kódolású karakterlánc bináris verziója |
||||

*Például*

Ez a példa a "aGVsbG8 =" Base64 kódolású karakterláncot bináris karakterlánccá alakítja:

```
base64ToBinary('aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása, amely hatékonyan dekódolja a Base64-karakterláncot.
Használja ezt a függvényt a [decodeBase64 ()](#decodeBase64)helyett.
Bár mindkét függvény ugyanúgy működik, `base64ToString()` előnyben részesített.

```
base64ToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolás – Base64-string*> | Sztring | Base64 kódolású karakterlánc karakterlánc-verziója |
||||

*Például*

Ez a példa a "aGVsbG8 =" Base64 kódolású karakterláncot csak karakterlánccá alakítja át:

```
base64ToString('aGVsbG8=')
```

És visszaadja ezt az eredményt:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Egy karakterlánc bináris verziójának visszaadása.

```
binary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris a bemenethez – érték*> | Sztring | A megadott karakterlánc bináris verziója |
||||

*Például*

Ez a példa a "Hello" karakterláncot bináris karakterlánccá alakítja át:

```
binary('hello')
```

És visszaadja ezt az eredményt:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>logikai

Egy érték logikai verziójának visszaadása.

```
bool(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Bármelyik | Az átalakítandó érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | A megadott érték logikai verziója |
||||

*Például*

Ezek a példák logikai értékekre alakítják át a megadott értékeket:

```
bool(1)
bool(0)
```

És a következő eredményeket adja vissza:

* Első példa:`true`
* Második példa:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>összefonódik

Az első nem null értéket ad vissza egy vagy több paraméterből.
Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

```
coalesce(<object_1>, <object_2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Igen | Bármely, többféle típust is tartalmazhat | Egy vagy több, a NULL értékre való keresésre szolgáló elem |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*első – nem null értékű – tétel*> | Bármelyik | Az első olyan érték, amely nem null. Ha az összes paraméter null értékű, a függvény Null értéket ad vissza. |
||||

*Például*

Ezek a példák az első nem null értéket adják vissza a megadott értékekről, vagy Null értéket, ha az összes érték null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

És a következő eredményeket adja vissza:

* Első példa:`true`
* Második példa:`"hello"`
* Harmadik példa:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Egyesítse kettő vagy több karakterláncot, és állítsa vissza az egyesített karakterláncot.

```
concat('<text1>', '<text2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Szöveg2*>,... | Igen | Sztring | Legalább két karakterláncot kell egyesíteni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sztring | A kombinált bemeneti sztringből létrehozott sztring |
||||

*Például*

Ez a példa a "Hello" és a "World" karakterláncokat ötvözi:

```
concat('Hello', 'World')
```

És visszaadja ezt az eredményt:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>tartalmazza a következőt:

Győződjön meg arról, hogy egy gyűjteménynek van-e konkrét eleme.
Igaz értéket ad vissza, ha az elem található, vagy ha nem található, hamis értéket ad vissza.
Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Pontosabban, ez a függvény a következő típusú gyűjteményeken működik:

* Egy *karakterlánc* , amely egy *alsztringet* keres
* Egy *tömb* , amely egy *értéket* keres
* A *kulcs* megtalálásához használandó *szótár*

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc, tömb vagy szótár | Az ellenőrzési gyűjtemény |
| <*érték*> | Igen | Karakterlánc, tömb vagy szótár | A keresendő tétel |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha az objektum megtalálható. Hamis értéket ad vissza, ha nem található. |
||||

*1\. példa*

Ez a példa a "Helló világ" karakterláncot ellenőrzi a "World" karakterláncban, és igaz értéket ad vissza:

```
contains('hello world', 'world')
```

*2\. példa*

Ez a példa ellenőrzi a "Hello World" karakterláncot a "Universe" alsztringnél, és hamis értéket ad vissza:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Az időbélyeget az egyezményes világidő (UTC) alapján alakítsa át a cél időzónára.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*destinationTimeZone*> | Igen | Sztring | A cél időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*átalakított időbélyeg*> | Sztring | A cél időzónára konvertált időbélyeg |
||||

*1\. példa*

Ez a példa egy időbélyeget konvertál a megadott időzónába:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt:`"2018-01-01T00:00:00Z"`

*2\. példa*

Ez a példa egy időbélyeget konvertál a megadott időzónába és formátumba:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

A forrás időzóna időbélyegének konvertálása a cél időzónára.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*destinationTimeZone*> | Igen | Sztring | A cél időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*átalakított időbélyeg*> | Sztring | A cél időzónára konvertált időbélyeg |
||||

*1\. példa*

Ez a példa a forrás időzónát a cél időzónára konvertálja:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt:`"2018-01-01T00:00:00.0000000"`

*2\. példa*

Ez a példa egy időzónát konvertál a megadott időzónába és formátumba:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

A forrás időzóna időbélyegének konvertálása az egyezményes világidő (UTC) szerinti időpontra

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*sourceTimeZone*> | Igen | Sztring | A forrás időzóna neve. Az időzóna neveivel kapcsolatban lásd: a [Microsoft időzóna-index értékei](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), de előfordulhat, hogy el kell távolítania az összes írásjelet az időzóna nevéből. |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*átalakított időbélyeg*> | Sztring | Az UTC-re konvertált időbélyeg |
||||

*1\. példa*

Ez a példa a következő időbélyeget konvertálja UTC-re:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

És visszaadja ezt az eredményt:`"2018-01-01T08:00:00.0000000Z"`

*2\. példa*

Ez a példa a következő időbélyeget konvertálja UTC-re:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

És visszaadja ezt az eredményt:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Tömb visszaadása több bemenetből.
Egyetlen bemeneti tömbhöz lásd: [array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Igen | Bármilyen, de nem vegyes | Legalább két elem a tömb létrehozásához |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Tömb | Az összes bemeneti elemből létrehozott tömb |
||||

*Például*

Ez a példa egy tömböt hoz létre ezekből a bemenetekről:

```
createArray('h', 'e', 'l', 'l', 'o')
```

És visszaadja ezt az eredményt:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Egy karakterlánchoz tartozó adat egységes erőforrás-azonosítójának (URI) visszaadása.

```
dataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*adaturi*> | Sztring | A bemeneti karakterlánchoz tartozó adat URI-ja |
||||

*Például*

Ez a példa létrehoz egy adaturi-t a "Hello" karakterlánchoz:

```
dataUri('hello')
```

És visszaadja ezt az eredményt:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Az adategységes erőforrás-azonosító (URI) bináris verziójának visszaadása.
Használja ezt a függvényt a [decodeDataUri ()](#decodeDataUri)helyett.
Bár mindkét függvény ugyanúgy működik, `dataUriBinary()` előnyben részesített.

```
dataUriToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó adaturi |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-for-az-adaturi*> | Sztring | Az adaturi bináris verziója |
||||

*Például*

Ez a példa bináris verziót hoz létre ehhez az adaturi-hoz:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Az adategységes erőforrás-azonosító (URI) karakterlánc-verziójának visszaadása.

```
dataUriToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó adaturi |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-for-az-adaturi*> | Sztring | Az adaturi-azonosító karakterlánc-verziója |
||||

*Például*

Ez a példa karakterláncot hoz létre ehhez az adaturi-hoz:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

A hónap napjának visszaadása egy időbélyegből.

```
dayOfMonth('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hónap napja*> | Egész szám | A hónap napja a megadott időbélyegből |
||||

*Például*

Ez a példa a hónap napjának számát adja vissza ebből az időbélyegből:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

A hét napjának visszaadása egy időbélyegből.

```
dayOfWeek('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hét napja*> | Egész szám | A hét napja a megadott időbélyegtől, ahol a vasárnap 0, hétfő 1, és így tovább |
||||

*Például*

Ez a példa a hét napjának számát adja vissza ebből az időbélyegből:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Az év napjának visszaadása egy időbélyegből.

```
dayOfYear('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*év napja*> | Egész szám | Az év napja a megadott időbélyegből |
||||

*Például*

Ez a példa az év napjának számát adja vissza ebből az időbélyegből:

```
dayOfYear('2018-03-15T13:27:36Z')
```

És visszaadja ezt az eredményt:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Egy Base64 kódolású karakterlánc karakterlánc-verziójának visszaadása, amely hatékonyan dekódolja a Base64-karakterláncot.
A helyett érdemes a [base64ToString ()](#base64ToString) használatát használni `decodeBase64()` .
Bár mindkét függvény ugyanúgy működik, `base64ToString()` előnyben részesített.

```
decodeBase64('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Base64 kódolású karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolás – Base64-string*> | Sztring | Base64 kódolású karakterlánc karakterlánc-verziója |
||||

*Például*

Ez a példa karakterláncot hoz létre egy Base64 kódolású karakterlánchoz:

```
decodeBase64('aGVsbG8=')
```

És visszaadja ezt az eredményt:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Az adategységes erőforrás-azonosító (URI) bináris verziójának visszaadása.
Vegye fontolóra a [dataUriToBinary ()](#dataUriToBinary)használatát a helyett `decodeDataUri()` .
Bár mindkét függvény ugyanúgy működik, `dataUriToBinary()` előnyben részesített.

```
decodeDataUri('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolásra szolgáló adaturi-karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris-for-az-adaturi*> | Sztring | Az adaturi-karakterlánc bináris verziója |
||||

*Például*

Ez a példa a bináris verziót adja vissza ehhez az adaturi-hoz:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

És visszaadja ezt az eredményt:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Olyan karakterláncot ad vissza, amely lecseréli az Escape-karaktereket a dekódolású verziókra.

```
decodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt Escape-karaktereket tartalmazó sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolású – URI*> | Sztring | A dekódolású Escape-karaktereket tartalmazó frissített sztring |
||||

*Például*

Ez a példa dekódolású verziókkal helyettesíti a karakterlánc Escape-karaktereit:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

A két szám közötti egész szám eredményének visszaadása.
A hátralévő eredmény eléréséhez lásd: [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*osztalék*> | Igen | Egész vagy lebegőpontos | Az *osztó* által elosztani kívánt szám |
| <*osztó*> | Igen | Egész vagy lebegőpontos | Az *osztalékot*osztó szám, de nem lehet 0 |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hányados – eredmény*> | Egész szám | Az egész szám, amely a második szám alapján osztja el az első számot |
||||

*Például*

Mindkét példa a második szám alapján osztja el az első számot:

```
div(10, 5)
div(11, 5)
```

És az eredmény visszaadása:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Egy egységes erőforrás-azonosító (URI) kódolású verziót ad vissza egy karakterlánchoz az URL-nem biztonságos karakterek lecserélése Escape-karakterekkel.
Vegye fontolóra a [uriComponent ()](#uriComponent)használatát a helyett `encodeUriComponent()` .
Bár mindkét függvény ugyanúgy működik, `uriComponent()` előnyben részesített.

```
encodeUriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az URI-kódolású formátumba konvertálandó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt – URI*> | Sztring | Az URI-kódolású karakterlánc Escape-karakterekkel |
||||

*Például*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
encodeUriComponent('https://contoso.com')
```

És visszaadja ezt az eredményt:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>üres

Győződjön meg arról, hogy a gyűjtemény üres.
Igaz értéket ad vissza, ha a gyűjtemény üres, vagy ha nem üres, hamis értéket ad vissza.

```
empty('<collection>')
empty([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc, tömb vagy objektum | Az ellenőrzési gyűjtemény |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha a gyűjtemény üres. Hamis értéket ad vissza, ha nem üres. |
||||

*Például*

Ezek a példák azt mutatják be, hogy a megadott gyűjtemények üresek-e:

```
empty('')
empty('abc')
```

És a következő eredményeket adja vissza:

* Első példa: üres karakterláncot ad vissza, így a függvény visszaadja `true` .
* Második példa: az "ABC" karakterláncot adja vissza, így a függvény visszaadja `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Győződjön meg arról, hogy egy karakterlánc egy adott alkarakterlánccal végződik-e.
Igaz értéket ad vissza, ha az alsztring található, vagy hamis értéket ad vissza, ha nem található.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket.

```
endsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az ellenőrzési sztring |
| <*Keresettszöveg*> | Igen | Sztring | A keresett karakterlánc vége |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai érték | Igaz értéket ad vissza, ha a záró alsztring található. Hamis értéket ad vissza, ha nem található. |
||||

*1\. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "World" karakterlánccal végződik-e:

```
endsWith('hello world', 'world')
```

És visszaadja ezt az eredményt:`true`

*2\. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "Universe" karakterlánccal végződik-e:

```
endsWith('hello world', 'universe')
```

És visszaadja ezt az eredményt:`false`

<a name="equals"></a>

### <a name="equals"></a>egyenlő

Győződjön meg arról, hogy az értékek, kifejezések vagy objektumok egyenértékűek-e.
Igaz értéket ad vissza, ha mindkettő egyenértékű, vagy ha nem egyenértékű, hamis értéket ad vissza.

```
equals('<object1>', '<object2>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Igen | Különböző | Az összehasonlítandó értékek, kifejezések vagy objektumok |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha mindkettő egyenértékű. Hamis értéket ad vissza, ha nem egyenértékű. |
||||

*Például*

Ezek a példák azt mutatják be, hogy a megadott bemenetek egyenértékűek-e.

```
equals(true, 1)
equals('abc', 'abcd')
```

És a következő eredményeket adja vissza:

* Első példa: mindkét érték egyenértékű, így a függvény visszaadja `true` .
* Második példa: mindkét érték nem egyenértékű, így a függvény visszaadja `false` .

<a name="first"></a>

### <a name="first"></a>első

Egy sztringből vagy tömbből származó első elem visszaadása.

```
first('<collection>')
first([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az első elemek találhatók |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*első gyűjtemény – tétel*> | Bármelyik | A gyűjtemény első eleme |
||||

*Például*

Ezek a példák a gyűjtemények első tételét keresik:

```
first('hello')
first(createArray(0, 1, 2))
```

És az eredmények visszaadása:

* Első példa:`"h"`
* Második példa:`0`

<a name="float"></a>

### <a name="float"></a>lebegőpontos

Karakterlánc-verzió konvertálása lebegőpontos számra tényleges lebegőpontos számra.

```
float('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó érvényes lebegőpontos számmal rendelkező sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*lebegőpontos érték*> | Float | A megadott karakterlánc lebegőpontos száma |
||||

*Például*

Ez a példa egy karakterlánc-verziót hoz létre ehhez a lebegőpontos számhoz:

```
float('10.333')
```

És visszaadja ezt az eredményt:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Egy időbélyeget ad vissza a megadott formátumban.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*újraformázva – időbélyeg*> | Sztring | A frissített időbélyeg a megadott formátumban |
||||

*Például*

Ez a példa egy időbélyeget konvertál a megadott formátumba:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

És visszaadja ezt az eredményt:`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Az aktuális időbélyeg és a megadott időegységek visszaadása.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időköz*> | Igen | Egész szám | A hozzáadandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az aktuális időbélyeg és a megadott számú időegység |
||||

*1\. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000 Z".
Ez a példa öt napot tesz a következő időbélyeghez:

```
getFutureTime(5, 'Day')
```

És visszaadja ezt az eredményt:`"2018-03-06T00:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-03-01T00:00:00.0000000 Z".
Ez a példa öt napot ad hozzá, és az eredményt "D" formátumra alakítja át:

```
getFutureTime(5, 'Day', 'D')
```

És visszaadja ezt az eredményt:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Az aktuális időbélyeg visszaadása a megadott időegységek mínusz.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időköz*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az aktuális időbélyeg mínusz a megadott számú időegység |
||||

*1\. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000 Z".
Ez a példa öt napot kivon az időbélyegből:

```
getPastTime(5, 'Day')
```

És visszaadja ezt az eredményt:`"2018-01-27T00:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy az aktuális időbélyeg "2018-02-01T00:00:00.0000000 Z".
Ebben a példában öt napot kell kivonnia, és az eredményt "D" formátumra konvertáljuk:

```
getPastTime(5, 'Day', 'D')
```

És visszaadja ezt az eredményt:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Győződjön meg arról, hogy az első érték nagyobb, mint a második érték.
Igaz értéket ad vissza, ha az első érték nagyobb, vagy ha kevesebbet ad vissza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amellyel ellenőrizhető, hogy a második érték nagyobb-e |
| <*Compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha az első érték nagyobb, mint a második érték. Hamis értéket ad vissza, ha az első érték egyenlő vagy kisebb a második értéknél. |
||||

*Például*

Ezek a példák azt mutatják be, hogy az első érték nagyobb-e, mint a második érték:

```
greater(10, 5)
greater('apple', 'banana')
```

És az eredmények visszaadása:

* Első példa:`true`
* Második példa:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Győződjön meg arról, hogy az első érték nagyobb vagy egyenlő, mint a második érték.
Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték kisebb.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amellyel ellenőrizhető, hogy a második érték nagyobb vagy egyenlő-e |
| <*Compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték. Hamis értéket ad vissza, ha az első érték kisebb a második értéknél. |
||||

*Például*

Ezek a példák azt mutatják be, hogy az első érték nagyobb vagy egyenlő, mint a második érték:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

És az eredmények visszaadása:

* Első példa:`true`
* Második példa:`false`

<a name="guid"></a>

### <a name="guid"></a>guid

Globálisan egyedi azonosító (GUID) létrehozása karakterláncként (például "c2ecc88d-88c8-4096-912c-d6f2e2b138ce"):

```
guid()
```

Emellett eltérő formátumot is megadhat az alapértelmezett "D" formátumtól eltérő GUID azonosítóhoz, amely 32 számjegyből áll, kötőjelekkel elválasztva.

```
guid('<format>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*formátumban*> | Nem | Sztring | Egyetlen [Formátum](https://msdn.microsoft.com/library/97af8hh4) megadása a VISSZAADOTT GUID azonosítóhoz. Alapértelmezés szerint a formátum a "D", de az "N", "D", "B", "P" vagy "X" is használható. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*GUID – érték*> | Sztring | Véletlenszerűen generált GUID |
||||

*Például*

Ez a példa ugyanazt a GUID azonosítót hozza létre, de 32 számjegyből áll, kötőjelekkel elválasztva, zárójelek közé zárva:

```
guid('P')
```

És visszaadja ezt az eredményt:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Győződjön meg arról, hogy a kifejezés igaz vagy hamis.
Az eredmény alapján adja vissza a megadott értéket.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés*> | Igen | Logikai érték | Az ellenőrzési kifejezés |
| <*valueIfTrue*> | Igen | Bármelyik | A kifejezés igaz értéke esetén visszaadott érték |
| <*valueIfFalse*> | Igen | Bármelyik | A kifejezés hamis értéke esetén visszaadott érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*megadott – visszatérési érték*> | Bármelyik | A megadott érték, amely attól függ, hogy a kifejezés igaz vagy hamis. |
||||

*Például*

Ez a példa azt eredményezi, `"yes"` hogy a megadott kifejezés igaz értéket ad vissza.
Ellenkező esetben a példa a `"no"` következőket adja vissza:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

A kezdő pozíció vagy az index értékének visszaadása egy alsztringnél.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
indexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A keresendő alsztringet tartalmazó karakterlánc |
| <*Keresettszöveg*> | Igen | Sztring | A keresendő alsztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*index – érték*>| Egész szám | A megadott alsztring kezdő pozíciójának vagy indexének értéke. <p>Ha a sztring nem található, az-1 számot kell visszaadnia. |
||||

*Például*

Ez a példa megkeresi a "világ" alsztring kezdő indexének értékét a "Hello World" karakterláncban:

```
indexOf('hello world', 'world')
```

És visszaadja ezt az eredményt:`6`

<a name="int"></a>

### <a name="int"></a>int

Egy sztring egész változatának visszaadása.

```
int('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*egész szám – eredmény*> | Egész szám | A megadott karakterlánc egész számú verziója |
||||

*Például*

Ez a példa egy egész verziót hoz létre a (z) "10" karakterlánchoz:

```
int('10')
```

És visszaadja ezt az eredményt:`10`

<a name="json"></a>

### <a name="json"></a>json

A JavaScript Object Notation (JSON) típusú értéket vagy objektumot adja vissza egy karakterlánc vagy XML számára.

```
json('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Karakterlánc vagy XML | Az átalakítandó karakterlánc vagy XML |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*JSON – eredmény*> | JSON natív típusa vagy objektum | A JSON natív típusú érték vagy objektum a megadott karakterlánchoz vagy XML-hez. Ha a karakterlánc null értékű, a függvény üres objektumot ad vissza. |
||||

*1\. példa*

Ez a példa átalakítja ezt a karakterláncot a JSON-értékre:

```
json('[1, 2, 3]')
```

És visszaadja ezt az eredményt:`[1, 2, 3]`

*2\. példa*

Ez a példa a következő sztringet konvertálja JSON-ra:

```
json('{"fullName": "Sophia Owen"}')
```

És visszaadja ezt az eredményt:

```
{
  "fullName": "Sophia Owen"
}
```

*3\. példa*

Ez a példa átalakítja ezt az XML-t a JSON-ra:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

És visszaadja ezt az eredményt:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>kereszteződés

Olyan gyűjteményt ad vissza, amely *csak* a megadott gyűjtemények közös elemeit tartalmazta.
Az eredmény megjelenítéséhez egy elemnek szerepelnie kell a függvénynek átadott összes gyűjteményben.
Ha egy vagy több elem neve azonos, az eredményben megjelenik az utolsó elem az adott névvel.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>,... | Igen | Tömb vagy objektum, de nem mindkettő | Azok a gyűjtemények, amelyekről *csak* az általános elemeket szeretné használni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*gyakori elemek*> | Tömb vagy objektum | Gyűjtemény, amely csak a megadott gyűjtemények közös elemeit tartalmazta |
||||

*Például*

Ez a példa a következő tömbökben lévő gyakori elemeket keresi:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

És egy olyan tömböt ad vissza, amely *csak* az alábbi elemekből áll:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>csatlakozás

Olyan karakterláncot ad vissza, *amely egy tömb*összes elemét tartalmazhatja, és mindegyik karakter elválasztó karaktert tartalmaz.

```
join([<collection>], '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Tömb | Az a tömb, amelybe az elemek csatlakoznak |
| <*elválasztó*> | Igen | Sztring | Az eredményül kapott karakterlánc egyes karakterei között megjelenő elválasztó |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*char1* >< elválasztó *karakter* >< *char2* >< elválasztó *>.* .. | Sztring | Az eredményül kapott karakterlánc a megadott tömb összes eleme alapján létrehozva |
||||

*Például*

Ez a példa egy karakterláncot hoz létre a tömb összes eleméről a megadott karakterrel elválasztó karakterrel:

```
join(createArray('a', 'b', 'c'), '.')
```

És visszaadja ezt az eredményt:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>utolsó

Egy gyűjtemény utolsó elemének visszaadása.

```
last('<collection>')
last([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, ahol az utolsó tételt keresi |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*utolsó gyűjtemény – tétel*> | Karakterlánc vagy tömb, illetve | A gyűjtemény utolsó eleme |
||||

*Például*

Ezek a példák a gyűjtemények utolsó tételét keresik:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

És a következő eredményeket adja vissza:

* Első példa:`"d"`
* Második példa:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

A kezdő pozíció vagy az index értékének visszaadása egy alsztring utolsó előfordulásakor.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket, és az indexek a 0 számmal kezdődnek.

```
lastIndexOf('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A keresendő alsztringet tartalmazó karakterlánc |
| <*Keresettszöveg*> | Igen | Sztring | A keresendő alsztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*záró index – érték*> | Egész szám | A megadott alsztring utolsó előfordulásának kezdő pozíciója vagy index értéke. <p>Ha a sztring nem található, az-1 számot kell visszaadnia. |
||||

*Például*

Ez a példa megkeresi a "világ" alsztring utolsó előfordulásának kezdő index értékét a "Hello World" karakterláncban:

```
lastIndexOf('hello world', 'world')
```

És visszaadja ezt az eredményt:`6`

<a name="length"></a>

### <a name="length"></a>hossz

Egy gyűjteményben lévő elemek számának visszaadása.

```
length('<collection>')
length([<collection>])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény és a megszámlálni kívánt elemek |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*hossz vagy darabszám*> | Egész szám | A gyűjtemény elemeinek száma |
||||

*Például*

Ezek a példák a gyűjtemények elemeinek számát számolják el:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

És az eredmény visszaadása:`4`

<a name="less"></a>

### <a name="less"></a>less

Győződjön meg arról, hogy az első érték kisebb a második értéknél.
Igaz értéket ad vissza, ha az első érték kisebb, vagy hamis értéket ad vissza, ha az első érték nagyobb.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amely azt vizsgálja, hogy a második érték kisebb-e |
| <*Compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító tétel |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha az első érték kisebb a második értéknél. Hamis értéket ad vissza, ha az első érték egyenlő vagy nagyobb, mint a második érték. |
||||

*Például*

Ezek a példák azt mutatják be, hogy az első érték kisebb-e a második értéknél.

```
less(5, 10)
less('banana', 'apple')
```

És az eredmények visszaadása:

* Első példa:`true`
* Második példa:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Győződjön meg arról, hogy az első érték kisebb vagy egyenlő, mint a második érték.
Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, vagy hamis értéket ad vissza, ha az első érték nagyobb.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Egész szám, lebegőpontos vagy sztring | Az első érték, amely azt vizsgálja, hogy a második érték kisebb vagy egyenlő-e |
| <*Compareto metódus végrehajtása*> | Igen | Egész szám, lebegőpontos vagy sztring | Az összehasonlító tétel |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai érték | Igaz értéket ad vissza, ha az első érték kisebb vagy egyenlő, mint a második érték. Hamis értéket ad vissza, ha az első érték nagyobb, mint a második érték. |
||||

*Például*

Ezek a példák azt mutatják be, hogy az első érték kisebb vagy egyenlő, mint a második érték.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

És az eredmények visszaadása:

* Első példa:`true`
* Második példa:`false`

<a name="max"></a>

### <a name="max"></a>Max

Egy listából vagy tömbből származó legmagasabb értéket ad vissza, amelynek a száma mindkét végén szerepel.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | Azon számok halmaza, amelyekről a legmagasabb értéket kívánja megadni |
| [<*szám1*>, <*szám2*>,...] | Igen | Tömb – egész szám, lebegőpontos vagy mindkettő | Azon számok tömbje, amelyekről a legmagasabb értéket kívánja használni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*maximális érték*> | Egész vagy lebegőpontos | A megadott tömb vagy számok legmagasabb értéke |
||||

*Például*

Ezek a példák a legmagasabb értéket kapják meg a számok és a tömb közül:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

És az eredmény visszaadása:`3`

<a name="min"></a>

### <a name="min"></a>p

A legkisebb értéket adja vissza számokból vagy tömbből.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szám1*>, <*szám2*>,... | Igen | Egész szám, lebegőpontos vagy mindkettő | Azon számok halmaza, amelyekről a legalacsonyabb értéket szeretné használni |
| [<*szám1*>, <*szám2*>,...] | Igen | Tömb – egész szám, lebegőpontos vagy mindkettő | Azon számok tömbje, amelyekről a legalacsonyabb értéket szeretné használni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*minimális érték*> | Egész vagy lebegőpontos | A megadott számú vagy megadott tömb legalacsonyabb értéke |
||||

*Például*

Ezek a példák a legalacsonyabb értéket kapják meg a számok és a tömbben:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

És az eredmény visszaadása:`1`

<a name="mod"></a>

### <a name="mod"></a>mod

A maradékot a két szám felosztásával állítsa vissza.
Az egész szám eredményének lekéréséhez lásd: [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*osztalék*> | Igen | Egész vagy lebegőpontos | Az *osztó* által elosztani kívánt szám |
| <*osztó*> | Igen | Egész vagy lebegőpontos | Az *osztalékot*osztó szám, de nem lehet 0. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*adatsorozat – eredmény*> | Egész vagy lebegőpontos | A maradék az első számnak a második számmal való felosztásával |
||||

*Például*

Ez a példa a második szám szerint osztja el az első számot:

```
mod(3, 2)
```

És az eredmény visszaadása:`1`

<a name="mul"></a>

### <a name="mul"></a>mul

A termék visszaküldése két szám szorzatával.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Igen | Egész vagy lebegőpontos | A *multiplicand2* által szorozható szám |
| <*multiplicand2*> | Igen | Egész vagy lebegőpontos | A többszörös *multiplicand1* száma |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*termék – eredmény*> | Egész vagy lebegőpontos | A termék, amely az első számot a második számmal szorozza meg |
||||

*Például*

Ezek a példák többek között az első szám a második szám szerint:

```
mul(1, 2)
mul(1.5, 2)
```

És az eredmények visszaadása:

* Első példa:`2`
* Második példa`3`

<a name="not"></a>

### <a name="not"></a>not

Győződjön meg arról, hogy egy kifejezés hamis-e.
Igaz értéket ad vissza, ha a kifejezés hamis, vagy igaz értéket ad vissza.

```json
not(<expression>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kifejezés*> | Igen | Logikai érték | Az ellenőrzési kifejezés |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha a kifejezés hamis. Hamis értéket ad vissza, ha a kifejezés igaz. |
||||

*1\. példa*

Ezek a példák azt mutatják be, hogy a megadott kifejezések hamisak-e:

```json
not(false)
not(true)
```

És az eredmények visszaadása:

* Első példa: a kifejezés hamis, így a függvény visszaadja `true` .
* Második példa: a kifejezés igaz, így a függvény visszaadja `false` .

*2\. példa*

Ezek a példák azt mutatják be, hogy a megadott kifejezések hamisak-e:

```json
not(equals(1, 2))
not(equals(1, 1))
```

És az eredmények visszaadása:

* Első példa: a kifejezés hamis, így a függvény visszaadja `true` .
* Második példa: a kifejezés igaz, így a függvény visszaadja `false` .

<a name="or"></a>

### <a name="or"></a>vagy

Győződjön meg arról, hogy legalább egy kifejezés igaz értékű-e.
Igaz értéket ad vissza, ha legalább egy kifejezés igaz értékű, vagy hamis értéket ad vissza, ha az összes hamis.

```
or(<expression1>, <expression2>, ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*Kifejezés2*>,... | Igen | Logikai érték | Az ellenőrzési kifejezések |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis) | Logikai érték | Igaz értéket ad vissza, ha legalább egy kifejezés igaz. Hamis értéket ad vissza, ha az összes kifejezés hamis. |
||||

*1\. példa*

Ezek a példák azt mutatják be, hogy legalább egy kifejezés igaz-e:

```json
or(true, false)
or(false, false)
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés igaz, így a függvény visszaadja `true` .
* Második példa: mindkét kifejezés hamis, így a függvény visszaadja `false` .

*2\. példa*

Ezek a példák azt mutatják be, hogy legalább egy kifejezés igaz-e:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

És az eredmények visszaadása:

* Első példa: legalább egy kifejezés igaz, így a függvény visszaadja `true` .
* Második példa: mindkét kifejezés hamis, így a függvény visszaadja `false` .

<a name="rand"></a>

### <a name="rand"></a>Rand

Egy, a megadott tartományból származó véletlenszerű egész számot ad vissza, amely csak a kezdési végponton van bezárólag.

```
rand(<minValue>, <maxValue>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Igen | Egész szám | A tartomány legalacsonyabb egésze |
| <*maxValue*> | Igen | Egész szám | Az egész szám, amely a függvény által visszaadott tartomány legmagasabb egészét követi |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*véletlenszerű eredmény*> | Egész szám | A megadott tartományból visszaadott véletlenszerű egész szám |
||||

*Például*

Ez a példa egy véletlenszerű egész számot kap a megadott tartományból, kivéve a maximális értéket:

```
rand(1, 5)
```

És visszaadja az alábbi számok egyikét a következő eredményként:,,, `1` `2` `3` vagy`4`

<a name="range"></a>

### <a name="range"></a>tartomány

Egy egész tömböt ad vissza, amely egy megadott egész számból indul.

```
range(<startIndex>, <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Igen | Egész szám | Egész érték, amely az első elemként elindítja a tömböt. |
| <*száma*> | Igen | Egész szám | A tömbben lévő egész számok száma |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*tartomány – eredmény*>] | Tömb | A tömb a megadott indextől kezdődő egész számokkal |
||||

*Például*

Ez a példa egy egész tömböt hoz létre, amely a megadott indextől kezdődik, és a megadott számú egész számot tartalmaz:

```
range(1, 4)
```

És visszaadja ezt az eredményt:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>csere

Cserélje le az alsztringet a megadott sztringre, és adja vissza az eredmény-karakterláncot. Ez a függvény megkülönbözteti a kis-és nagybetűket.

```
replace('<text>', '<oldText>', '<newText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A lecserélni kívánt alsztringet tartalmazó karakterlánc |
| <*oldText*> | Igen | Sztring | A lecserélni kívánt alsztring |
| <*newText*> | Igen | Sztring | A helyettesítő sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – szöveg*> | Sztring | Az alsztring cseréje után a frissített sztring <p>Ha az alkarakterlánc nem található, az eredeti karakterláncot küldje vissza. |
||||

*Például*

Ez a példa megkeresi a "régi" karakterláncot a "régi" karakterláncban, és a "régi" kifejezést az "új" értékre cseréli:

```
replace('the old string', 'old', 'new')
```

És visszaadja ezt az eredményt:`"the new string"`

<a name="skip"></a>

### <a name="skip"></a>kihagyása

Elemek eltávolítása egy gyűjtemény elejéről, és *az összes többi* elem visszaadása.

```
skip([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Tömb | A gyűjtemény, amelynek elemeit el szeretné távolítani |
| <*száma*> | Igen | Egész szám | Az előtérben eltávolítandó elemek számának pozitív egésze |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*frissítve – gyűjtemény*>] | Tömb | A frissített gyűjtemény a megadott elemek eltávolítása után |
||||

*Például*

Ez a példa egy elemet távolít el, a 0 számot a megadott tömb elejéről:

```
skip(createArray(0, 1, 2, 3), 1)
```

Ezt a tömböt a többi elemmel együtt adja vissza:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>felosztás

Egy olyan tömböt ad vissza, amely vesszővel elválasztott alsztringeket tartalmaz, az eredeti karakterláncban megadott elválasztói karakter alapján.

```
split('<text>', '<delimiter>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A karakterlánc, amely az eredeti sztringben megadott határolójel alapján választja el az alsztringeket |
| <*elválasztó*> | Igen | Sztring | A határolójelként használandó eredeti karakterláncban szereplő karakter |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| [<*substring1*>, <*substring2*>,...] | Tömb | Egy tömb, amely az eredeti sztringből származó alsztringeket tartalmaz, vesszővel elválasztva |
||||

*Például*

Ez a példa egy olyan tömböt hoz létre, amely alsztringekkel rendelkezik a megadott karakterlánc alapján, az elválasztó karaktertől függően:

```
split('a_b_c', '_')
```

És ezt a tömböt adja vissza eredményként:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Egy időbélyeghez tartozó nap kezdetének visszaadása.

```
startOfDay('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | A megadott időbélyeg, de a nap nulla órás megjelölése után |
||||

*Például*

Ez a példa megkeresi a nap kezdetét ehhez az időbélyeghez:

```
startOfDay('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>Óra kezdete

Az óra kezdetének visszaadása egy időbélyeghez.

```
startOfHour('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | A megadott időbélyeg, de az óra nulla perces jelölése után |
||||

*Például*

Ez a példa megkeresi az óra kezdetét ehhez az időbélyeghez:

```
startOfHour('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

A hónap kezdetének visszaadása egy időbélyeghez.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | A megadott időbélyeg, de a hónap első napján kezdődik a nulla órás jelölés |
||||

*Például*

Ez a példa az időbélyeg hónapjának kezdetét adja vissza:

```
startOfMonth('2018-03-15T13:30:30Z')
```

És visszaadja ezt az eredményt:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Győződjön meg róla, hogy egy sztring egy adott alkarakterlánccal kezdődik-e.
Igaz értéket ad vissza, ha az alsztring található, vagy hamis értéket ad vissza, ha nem található.
Ez a függvény nem megkülönbözteti a kis-és nagybetűket.

```
startsWith('<text>', '<searchText>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az ellenőrzési sztring |
| <*Keresettszöveg*> | Igen | Sztring | A keresett kezdő sztring |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| true (igaz) vagy false (hamis)  | Logikai érték | Igaz értéket ad vissza, ha a kezdő alsztring található. Hamis értéket ad vissza, ha nem található. |
||||

*1\. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "Hello" alkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'hello')
```

És visszaadja ezt az eredményt:`true`

*2\. példa*

Ez a példa ellenőrzi, hogy a "Hello World" karakterlánc a "köszöntések" alkarakterlánccal kezdődik-e:

```
startsWith('hello world', 'greetings')
```

És visszaadja ezt az eredményt:`false`

<a name="string"></a>

### <a name="string"></a>sztring

Egy érték karakterlánc-verziójának visszaadása.

```
string(<value>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Bármelyik | Az átalakítandó érték |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*karakterlánc-érték*> | Sztring | A megadott érték karakterlánc-verziója |
||||

*1\. példa*

Ez a példa a következő karakterlánc-verziót hozza létre ehhez a számhoz:

```
string(10)
```

És visszaadja ezt az eredményt:`"10"`

*2\. példa*

Ez a példa egy karakterláncot hoz létre a megadott JSON-objektumhoz, és a fordított perjel karaktert ( \\ ) használja Escape-karakterként a dupla idézőjel (") számára.

```
string( { "name": "Sophie Owen" } )
```

És visszaadja ezt az eredményt:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Az eredmény visszaadása a második számnak az első számból való kivonásával.

```
sub(<minuend>, <subtrahend>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*kisebbítendőt*> | Igen | Egész vagy lebegőpontos | Az a szám, amelyből ki kell vonni a *kivonandó* |
| <*kivonandó*> | Igen | Egész vagy lebegőpontos | A *kisebbítendőt* kivonni kívánt szám |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*találat*> | Egész vagy lebegőpontos | A második számnak az első számból való kivonásának eredménye |
||||

*Például*

Ez a példa kivonja a második számot az első számból:

```
sub(10.3, .3)
```

És visszaadja ezt az eredményt:`10`

<a name="substring"></a>

### <a name="substring"></a>substring

Karaktereket ad vissza egy karakterláncból, a megadott pozíciótól vagy indextől kezdődően.
Az index értékei a 0 számmal kezdődnek.

```
substring('<text>', <startIndex>, <length>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az a karakterlánc, amelynek a karaktereit szeretné |
| <*startIndex*> | Igen | Egész szám | A kiindulási pozícióként vagy indexként használni kívánt nullánál kisebb vagy nagyobb pozitív érték |
| <*hossza*> | Igen | Egész szám | Pozitív számú karakter, amelyet az alkarakterláncban szeretne használni. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*alkarakterlánc – eredmény*> | Sztring | A megadott számú karakterből álló alsztring, amely a forrás sztringben megadott index pozíciótól kezdődik. |
||||

*Például*

Ez a példa egy öt karakterből álló alkarakterláncot hoz létre a megadott karakterláncból, a 6. index értéktől kezdődően:

```
substring('hello world', 6, 5)
```

És visszaadja ezt az eredményt:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Több időegység kivonása egy időbélyegből.
Lásd még: [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeget tartalmazó sztring |
| <*időköz*> | Igen | Egész szám | A kivonandó megadott időegységek száma |
| <*timeUnit*> | Igen | Sztring | Az időegység, amelyet a következő *intervallummal*kell használni: "Second", "minute", "Hour", "Day", "Week", "hónap", "Year" |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*Frissítve – timestamp*> | Sztring | Az időbélyeg mínusz a megadott számú időegység |
||||

*1\. példa*

Ez a példa egy napot kivonja ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

És visszaadja ezt az eredményt:`"2018-01-01T00:00:00:0000000Z"`

*2\. példa*

Ez a példa egy napot kivonja ebből az időbélyegből:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

És visszaadja ezt az eredményt a nem kötelező "D" formátum használatával:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>eltarthat

Elemek visszaküldése egy gyűjtemény elejéről.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*gyűjtemény*> | Igen | Karakterlánc vagy tömb | A gyűjtemény, amelynek elemeit szeretné |
| <*száma*> | Igen | Egész szám | Az elejétől kapott elemek számának pozitív egésze |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*részhalmaz*> vagy [<*részhalmaz*>] | Karakterlánc vagy tömb, illetve | Olyan karakterlánc vagy tömb, amely az eredeti gyűjtemény elejétől kapott megadott számú elemet tartalmaz. |
||||

*Például*

Ezek a példák a gyűjtemények elejéről kapják meg a megadott számú elemet:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

És az eredmények visszaadása:

* Első példa:`"abc"`
* Második példa:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

A `ticks` tulajdonság értékének visszaadása egy megadott időbélyeghez.
A *Tick* egy 100-ns intervallum.

```
ticks('<timestamp>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*időbélyeg*> | Igen | Sztring | Az időbélyeg karakterlánca |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*osztásjelek – szám*> | Egész szám | A megadott időbélyeg óta a kullancsok száma |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Karakterláncot ad vissza kisbetűs formátumban. Ha a karakterlánc egyik karaktere nem rendelkezik kisbetűs változattal, a karakter változatlan marad a visszaadott karakterláncban.

```
toLower('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A kisbetűs formátumban visszaadni kívánt karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kisbetűs – szöveg*> | Sztring | Az eredeti sztring kisbetűs formátumban |
||||

*Például*

Ez a példa kisbetűsre alakítja át ezt a karakterláncot:

```
toLower('Hello World')
```

És visszaadja ezt az eredményt:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Karakterláncot ad vissza nagybetűs formátumban. Ha a karakterlánc egyik karaktere nem rendelkezik nagybetűs változattal, akkor ez a karakter változatlan marad a visszaadott karakterláncban.

```
toUpper('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | A nagybetűs formátumban visszaadni kívánt karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*nagybetűs szöveg*> | Sztring | Az eredeti sztring nagybetűs formátumban |
||||

*Például*

Ez a példa nagybetűvé alakítja át ezt a karakterláncot:

```
toUpper('Hello World')
```

És visszaadja ezt az eredményt:`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Trim

Távolítsa el a kezdő és záró szóközt egy karakterláncból, és küldje vissza a frissített karakterláncot.

```
trim('<text>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*szöveg*> | Igen | Sztring | Az eltávolítandó kezdő és záró szóközt tartalmazó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updatedText*> | Sztring | Frissített verzió az eredeti karakterlánchoz kezdő vagy záró szóközök nélkül |
||||

*Például*

Ez a példa eltávolítja a kezdő és záró szóközt a (z) ""Helló világ!"alkalmazás" karakterláncból:

```
trim(' Hello World  ')
```

És visszaadja ezt az eredményt:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>Union

Olyan gyűjteményt ad vissza, amely a megadott gyűjtemények *összes* elemét tartalmazta.
Az eredmény megjelenítéséhez egy elem megjelenhet a függvénynek átadott gyűjteményben. Ha egy vagy több elem neve azonos, az eredményben megjelenik az utolsó elem az adott névvel.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>,...  | Igen | Tömb vagy objektum, de nem mindkettő | Azok a gyűjtemények, amelyekről az *összes* elemet meg szeretné jeleníteni |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Tömb vagy objektum | Gyűjtemény a megadott gyűjtemények összes elemével – nincsenek duplikálva |
||||

*Például*

Ez a példa a gyűjtemények *összes* elemét lekéri:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

És visszaadja ezt az eredményt:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Egy egységes erőforrás-azonosító (URI) kódolású verziót ad vissza egy karakterlánchoz az URL-nem biztonságos karakterek lecserélése Escape-karakterekkel.
Használja ezt a függvényt a [encodeUriComponent ()](#encodeUriComponent)helyett.
Bár mindkét függvény ugyanúgy működik, `uriComponent()` előnyben részesített.

```
uriComponent('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az URI-kódolású formátumba konvertálandó karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*kódolt – URI*> | Sztring | Az URI-kódolású karakterlánc Escape-karakterekkel |
||||

*Például*

Ez a példa egy URI-kódolású verziót hoz létre ehhez a karakterlánchoz:

```
uriComponent('https://contoso.com')
```

És visszaadja ezt az eredményt:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Egy egységes erőforrás-azonosító (URI) összetevő bináris verziójának visszaadása.

```
uriComponentToBinary('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó URI-kódolású karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*bináris kódolású URI*> | Sztring | Az URI-kódolású karakterlánc bináris verziója. A bináris tartalom Base64 kódolású és a által reprezentált `$content` . |
||||

*Például*

Ez a példa az URI-kódolású karakterlánc bináris verzióját hozza létre:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Egy egységes erőforrás-azonosító (URI) kódolású sztring karakterlánc-verzióját ad vissza, amely hatékonyan dekódolja az URI-kódolású karakterláncot.

```
uriComponentToString('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | A dekódolni kívánt URI-kódolású karakterlánc |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*dekódolású – URI*> | Sztring | Az URI-kódolású karakterlánc dekódolt verziója |
||||

*Például*

Ez a példa az URI-kódolású karakterlánc dekódolt karakterlánc-verzióját hozza létre:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

És visszaadja ezt az eredményt:`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Az aktuális időbélyeg visszaadása.

```
utcNow('<format>')
```

A <*format*> paraméterrel eltérő formátumot is megadhat.

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*formátumban*> | Nem | Sztring | [Egyetlen formátum](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) megadása vagy [egyéni formázási minta](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Az időbélyeg alapértelmezett formátuma az ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (éééé-hh-NNTóó: PP: ss: fffffffk formátumban), amely megfelel az [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -nek, és megőrzi az időzóna-információkat. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*aktuális – timestamp*> | Sztring | Az aktuális dátum és idő |
||||

*1\. példa*

Tegyük fel, hogy ma április 15., 2018-kor, 1:00:00 órakor.
Ez a példa az aktuális időbélyeget kapja:

```
utcNow()
```

És visszaadja ezt az eredményt:`"2018-04-15T13:00:00.0000000Z"`

*2\. példa*

Tegyük fel, hogy ma április 15., 2018-kor, 1:00:00 órakor.
Ez a példa az aktuális időbélyeget az opcionális "D" formátum használatával olvassa be:

```
utcNow('D')
```

És visszaadja ezt az eredményt:`"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Egy JSON-objektumot tartalmazó sztring XML-verziójának visszaadása.

```
xml('<value>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*érték*> | Igen | Sztring | Az átalakítandó JSON-objektumot tartalmazó karakterlánc <p>A JSON-objektumnak csak egy gyökérszintű tulajdonsággal kell rendelkeznie, amely nem lehet tömb. <br>Használja a fordított perjel karaktert ( \\ ) Escape-karakterként az idézőjelek (") számára. |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*XML-verzió*> | Objektum | A megadott karakterlánc-vagy JSON-objektumhoz tartozó kódolt XML |
||||

*1\. példa*

Ez a példa a karakterlánchoz tartozó XML-verziót hozza létre, amely egy JSON-objektumot tartalmaz:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

És az eredmény XML-fájlját adja vissza:

```xml
<name>Sophia Owen</name>
```

*2\. példa*

Tegyük fel, hogy rendelkezik a következő JSON-objektummal:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Ez a példa egy XML-fájlt hoz létre egy olyan karakterlánchoz, amely tartalmazza ezt a JSON-objektumot:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

És az eredmény XML-fájlját adja vissza:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Az XML-t az XPath (XML-elérésiút) kifejezésnek megfelelő csomópontok vagy értékek esetében adja meg, és adja vissza a megfelelő csomópontokat vagy értékeket. Egy XPath-kifejezés, vagy csak "XPath", segít navigálni egy XML-dokumentum struktúrájában, hogy kiválassza a csomópontokat vagy számítási értékeket az XML-tartalomban.

```
xpath('<xml>', '<xpath>')
```

| Paraméter | Kötelező | Típus | Leírás |
| --------- | -------- | ---- | ----------- |
| <*XML*> | Igen | Bármelyik | Az XPath-kifejezés értékének megfelelő csomópontok vagy értékek keresésére szolgáló XML-karakterlánc |
| <*XPath*> | Igen | Bármelyik | Az egyező XML-csomópontok vagy-értékek kereséséhez használt XPath-kifejezés |
|||||

| Visszatérési érték | Típus | Leírás |
| ------------ | ---- | ----------- |
| <*XML – csomópont*> | XML | XML-csomópont, ha csak egyetlen csomópont felel meg a megadott XPath-kifejezésnek |
| <*érték*> | Bármelyik | Egy XML-csomópont értéke, ha csak egyetlen érték egyezik a megadott XPath-kifejezéssel |
| [<*XML-csomópont1*>, <*xml-Csomópont2*>,...] </br>-vagy- </br>[<*érték1*>, <*érték2*>,...] | Tömb | A megadott XPath-kifejezésnek megfelelő XML-csomópontokkal vagy-értékekkel rendelkező tömb |
||||

*1\. példa*

Az 1. példán alapuló példa megkeresi a csomópontnak megfelelő csomópontokat, `<count></count>` és hozzáadja ezeket a csomópont-értékeket a következő `sum()` függvényhez:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

És visszaadja ezt az eredményt:`30`

*2\. példa*

Ebben a példában mindkét kifejezés olyan csomópontokat talál, amelyek megfelelnek a `<location></location>` csomópontnak, a megadott argumentumokban, amelyek tartalmazzák az XML-névteret. A kifejezések a perjel karaktert ( \\ ) használják Escape-karakterként az idézőjelek (") számára.

* *1. kifejezés*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *2. kifejezés*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Az argumentumok a következők:

* Ez az XML, amely tartalmazza az XML-dokumentum névterét `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* A következő XPath-kifejezés:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Itt látható az eredmény csomópont, amely megfelel a `<location></location>` csomópontnak:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*3\. példa*

A 3. példában ez a példa a csomópontban található értéket keresi `<location></location>` :

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

És visszaadja ezt az eredményt:`"Paris"`

## <a name="next-steps"></a>További lépések
A kifejezésekben használható rendszerváltozók listáját a [rendszerváltozók](control-flow-system-variables.md)című részben tekintheti meg.
