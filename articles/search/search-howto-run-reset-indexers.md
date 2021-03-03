---
title: Indexelő futtatása vagy visszaállítása
titleSuffix: Azure Cognitive Search
description: Az indexelő, a készségek vagy az egyes dokumentumok alaphelyzetbe állításával frissítheti az összes, illetve az index-vagy a Knowledge-tárolót.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667660"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Indexelő, szakismeretek vagy dokumentumok futtatása vagy visszaállítása

Az indexelő végrehajtása az indexelő első létrehozásakor [, az](search-indexer-overview.md)indexelő igény szerinti futtatásakor, illetve az indexelő ütemezésének beállításakor fordulhat elő. A kezdeti futtatást követően az indexelő nyomon követi, hogy mely keresési dokumentumok indexelve lettek egy belső "magas vízjelek" használatával. A jelölő soha nem jelenik meg az API-ban, de belsőleg az indexelő tudja, hogy az indexelés hol állt le, hogy a következő futtatáskor is lefusson.

A magas vízjeleket törölheti úgy, hogy alaphelyzetbe állítja az indexelő, ha újra fel szeretné dolgozni. Az API-k alaphelyzetbe állítása az objektum-hierarchia csökkenő szintjein érhető el:

+ A teljes keresési Corpus (az [indexek visszaállítása](#reset-indexers))
+ Egy adott dokumentum vagy dokumentum listája (A [dokumentumok alaphelyzetbe állítása – előzetes](#reset-docs)verzió)
+ Adott szakértelem vagy gazdagítás egy dokumentumban (a [képességek alaphelyzetbe állítása – előzetes](#reset-skills)verzió)

Az API-k alaphelyzetbe állításával frissíthetik a gyorsítótárazott tartalmakat (a [mesterséges intelligenciával](cognitive-search-concept-intro.md) kapcsolatos esetekben), vagy törölheti a magas vízjeleket, és újraépítheti az indexet.

Az Alaphelyzetbe állítás, amelyet a Futtatás követ, a képes újra feldolgozni a meglévő dokumentumokat és új dokumentumokat, de nem távolítja el a korábbi futtatások során létrehozott keresési indexben lévő árva keresési dokumentumokat. A törléssel kapcsolatos további információkért lásd: [dokumentumok hozzáadása, frissítése vagy törlése](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Indexelő futtatása

Az indexelő létrehozása és futtatása az indexelő [létrehozásával](/rest/api/searchservice/create-indexer) , ha nem a letiltott állapotban van ("Letiltva": true). Az első futtatás valamivel több időt vesz igénybe, mert az objektum létrehozása is megtörténik.

Az [Indexelő futtatásával](/rest/api/searchservice/run-indexer) a rendszer csak azt fogja felderíteni és feldolgozni, hogy az adatforrással hogyan kell szinkronizálni a keresési indexet. A blob Storage beépített változások észlelésével rendelkezik. Más adatforrásokat, például az Azure SQL-t vagy a Cosmos DB-t úgy kell konfigurálni, hogy az indexelő csak az új és frissített sorokat tudja olvasni.

Az indexelő a következő módszerek bármelyikével futtatható:

+ Azure Portal a **Futtatás** parancs használatával az indexelő lapon
+ [Indexelő futtatása (REST)](/rest/api/searchservice/run-indexer)
+ [RunIndexers metódus](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) az Azure .net SDK-ban (vagy egy másik SDK-ban egyenértékű RunIndexer metódus használatával)

Az indexelő végrehajtásához a következő korlátozások vonatkoznak:

+ Az indexelő feladatok maximális száma 1/replika, egyidejű feladatok nélkül.

  Ha az indexelő végrehajtása már kapacitása van, ezt az értesítést fogja kapni: "nem sikerült futtatni az indexelő" <indexelő-név> ", hiba:" egy másik indexelő hívás jelenleg folyamatban van; az egyidejű meghívások nem engedélyezettek. "

+ A maximális futási idő 2 óra, ha készségkészlet használ, és 24 órát sem. 

  Kiterjesztheti a feldolgozást úgy, hogy az indexelő ütemezettre helyezi. Az ingyenes szinten alacsonyabb a futási idő korlátai. A teljes listát lásd: [Indexelő korlátai](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Indexelő alaphelyzetbe állítása

Az indexelő alaphelyzetbe állítása az összes felöleli. A keresési indexben az indexelő által eredetileg kitöltött keresési dokumentumok teljes feldolgozásra vannak megjelölve. A rendszer az alapul szolgáló forrást keresési dokumentumokként adja hozzá az indexhez. Ha az indexelő úgy van beállítva, hogy készségkészlet és [gyorsítótárazást](search-howto-incremental-index.md)használjon, a készségkészlet újrafuttatása történik, és a gyorsítótár frissül.

Ezen megközelítések bármelyikével alaphelyzetbe állíthatja az indexelő, majd egy indexelő futtathatja a fentiekben ismertetett módszerek valamelyikét.

+ Azure Portal az Indexer lapon az **Alaphelyzetbe állítás** parancs használatával
+ [Indexelő alaphelyzetbe állítása (REST)](/rest/api/searchservice/reset-indexer)
+ [ResetIndexers metódus](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) az Azure .net SDK-ban (vagy egy másik SDK-ban egyenértékű RunIndexer metódus használatával)

A Futtatás befejezése után a rendszer törli az Alaphelyzetbe állítás jelzőt. Az adatforrásra vonatkozó minden normál változás-észlelési logika folytatódik a következő futtatáskor, és minden más új vagy frissített értéket vesz fel a többi adatkészletbe.

> [!NOTE]
> Az alaphelyzetbe állítási kérelem meghatározza, hogy mi az újrafeldolgozás (indexelő, szakértelem vagy dokumentum), de más módon nem befolyásolja az indexelő futásidejű viselkedését. Ha az indexelő futásidejű paramétereket, mező-hozzárendeléseket, gyorsítótárazást, kötegelt beállításokat tartalmaz, és így tovább, akkor ezek a beállítások mind érvényesek, amikor alaphelyzetbe állítja az indexet.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Képességek alaphelyzetbe állítása (előzetes verzió)

> [!IMPORTANT] 
> A [képességek alaphelyzetbe állítása](/rest/api/searchservice/preview-api/reset-skills) nyilvános előzetes verzióban érhető el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők.

A szakértelmével rendelkező indexelő esetében alaphelyzetbe állíthatja az adott képességek feldolgozását, valamint a kimenettől függő alsóbb szintű képességeket. A [gyorsítótárazott dúsítások](search-howto-incremental-index.md) is frissülnek. A képességek alaphelyzetbe állítása érvényteleníti a gyorsítótárazott képességek eredményeit, ami akkor lehet hasznos, ha egy szaktudás új verziója van telepítve, és azt szeretné, hogy az indexelő az összes dokumentumra újrafuttassa ezt a képességet. 

A [képességek visszaállítása](/rest/api/searchservice/preview-api/reset-skills) a REST-en keresztül érhető el **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Egyéni képességeket is megadhat, ahogy az a fenti példában is látható, de ha ezek a képességek a nem listázott szaktudásból (#2 #4) származó kimenetet igényelnek, akkor a fel nem jelentett képességek akkor futnak le, ha a gyorsítótár nem tudja biztosítani a szükséges információkat. Ahhoz, hogy ez igaz legyen, a (z) #4-on keresztül #2 képességek gyorsítótárazott bővítései nem lehetnek függőségek #1 (alaphelyzetbe állítás).

Ha nem ad meg szakértelmet, a rendszer végrehajtja a teljes készségkészlet, és ha engedélyezve van a gyorsítótárazás, a gyorsítótár is frissül.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Dokumentumok alaphelyzetbe állítása (előzetes verzió)

> [!IMPORTANT] 
> A [dokumentumok alaphelyzetbe állítása](/rest/api/searchservice/preview-api/reset-documents) nyilvános előzetes verzióban érhető el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők.

A [dokumentumok alaphelyzetbe állítása API](/rest/api/searchservice/preview-api/reset-documents) fogadja a dokumentumok kulcsainak listáját, hogy az adott dokumentumokat frissíteni lehessen. Ha meg van adva, az alaphelyzetbe állítási paraméterek a feldolgozott adatoktól függetlenül egyetlen tényezővé válnak. Ha például 20 blob lett hozzáadva vagy frissítve az utolsó indexelő futtatása óta, de csak egy dokumentumot állít vissza, csak az egyik dokumentum lesz feldolgozva.

A keresési dokumentumban lévő összes mező az adatforrásból származó értékekkel frissül. Nem választhat, és kiválaszthatja a frissíteni kívánt mezőket. 

Ha a dokumentumot egy készségkészlet gazdagítja, és gyorsítótárazza az adattárat, akkor a rendszer csak a megadott dokumentumokhoz hívja meg a készségkészlet, a gyorsítótárazott fájlokat pedig frissíti a feldolgozatlan dokumentumokhoz.

Ha első alkalommal teszteli az API-t, a következő API-k segítenek a viselkedés érvényesítésében és tesztelésében:

+ [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status) API-verzióval `2020-06-30-Preview` a visszaállítási állapot és a végrehajtás állapotának megtekintéséhez. Az alaphelyzetbe állítási kérelemre vonatkozó információk az állapot válaszának végén találhatók.
+ A dokumentumok API-verzióval való [visszaállítása](/rest/api/searchservice/preview-api/reset-documents) `2020-06-30-Preview` a feldolgozandó dokumentumok megadásához.
+ [Futtassa az indexelő](/rest/api/searchservice/run-indexer) az indexelő futtatásához (bármely API-verzió).
+ [Keressen dokumentumokat](/rest/api/searchservice/search-documents) a frissített értékek kereséséhez, valamint a dokumentumok kulcsainak visszaadásához, ha nem biztos benne, hogy az érték. Akkor használja, `"select": "<field names>"` Ha korlátozni szeretné, hogy mely mezők jelenjenek meg a válaszban.

### <a name="formulate-and-send-the-reset-request"></a>Az alaphelyzetbe állítási kérelem kialakítása és elküldése

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

A kérelemben megadott dokumentum-kulcsok a keresési indexből származó értékek, amelyek az adatforrás megfelelő mezőitől eltérőek lehetnek. Ha nem biztos a kulcs értékében, [küldjön egy lekérdezést](search-query-create.md) az érték visszaküldéséhez. `select` A paranccsal csak a dokumentum kulcsa mező adható vissza.

Több keresési dokumentumba elemzett Blobok esetén (Ha például [jsonLines vagy jsonArrays](search-howto-index-json-blobs.md), vagy [delimitedText](search-howto-index-csv-blobs.md)) elemező módban használta, a dokumentum kulcsát az indexelő hozza létre, és előfordulhat, hogy a rendszer ismeretlen. Ebben az esetben a dokumentum kulcsának lekérdezése a megfelelő érték megadásával fog megjelenni.

Az API többszöri meghívása különböző kulcsokkal: hozzáfűzi az új kulcsokat a dokumentum-kulcsok alaphelyzetbe állításának listájához. Ha az API-t **`overwrite`** úgy hívja meg, hogy az true paramétert adja meg, a rendszer felülírja a dokumentum azon kulcsainak aktuális listáját, amelyeket vissza kíván állítani a kérelem hasznos adataival

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Alaphelyzetbe állítás állapotának ellenõrzése

Az Alaphelyzetbe állítás állapotának vizsgálatához és a feldolgozáshoz várólistára állított dokumentum-kulcsok megtekintéséhez használja az [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status) a következővel: **`api-version=06-30-2020-Preview`** . Az előzetes verziójú API a szakaszt fogja visszaadni **`currentState`** , amely az indexelő állapotának lekérése válasz végén található.

A "Mode" (mód **`indexingAllDocs`** ) az alaphelyzetbe állítási ismeretek (mivel potenciálisan az összes dokumentumra vonatkozik) a mesterséges intelligenciával kitöltött mezők esetében.

A dokumentumok alaphelyzetbe állításához a mód a következőre van beállítva: **`indexingResetDocs`** . Az indexelő mindaddig megőrzi ezt az állapotot, amíg a rendszer a dokumentumok visszaállítása hívásban megadott összes dokumentumot feldolgozza, és a művelet végrehajtása közben más indexelő feladatok sem lesznek végrehajtva. Ha megtalálta az összes dokumentumot a dokumentum kulcsainak listájában, a rendszer minden dokumentumot kihasznál a kulcs megkereséséhez és egyeztetéséhez, és ez hosszabb időt is igénybe vehet, ha az adathalmaz nagy. Ha egy blob-tároló több száz blobot tartalmaz, és az alaphelyzetbe állítani kívánt docs a végén található, akkor az indexelő nem találja a megfelelő blobokat, amíg az összes többi felhasználó be nem jelölte az összes többit.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

A dokumentumok újrafeldolgozása után az indexelő visszatér a **`indexingAllDocs`** módba, és feldolgozza a többi új vagy frissített dokumentumot a következő futtatáskor.

## <a name="next-steps"></a>Következő lépések

Az API-k alaphelyzetbe állításával tájékoztathatja a következő indexelő futtatásának hatókörét. A tényleges feldolgozáshoz szükség van egy igény szerinti indexelő futtatására, vagy az ütemezett feladatok végrehajtásának engedélyezése a munka befejezéséhez. A Futtatás befejezése után az indexelő visszatér a normál feldolgozásra, legyen az ütemezés vagy igény szerinti feldolgozás.

Az indexelő feladatok alaphelyzetbe állítása és újrafuttatása után nyomon követheti a keresési szolgáltatás állapotát, vagy részletes információkat szerezhet be a diagnosztikai naplózás használatával.

+ [Indexelő műveletei (REST)](/rest/api/searchservice/indexer-operations)
+ [Keresési indexelő állapotának figyelése](search-howto-monitor-indexers.md)
+ [Naplózási adatok összegyűjtése és elemzése](search-monitor-logs.md)
+ [Indexelő beosztása](search-howto-schedule-indexers.md)