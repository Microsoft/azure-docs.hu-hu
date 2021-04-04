---
title: Az eredmények kivágására szolgáló biztonsági szűrők
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan valósíthat meg biztonsági jogosultságokat az Azure Cognitive Search keresési eredményeinek dokumentum szintjén, biztonsági szűrők és felhasználói identitások használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631803"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Az Azure Cognitive Search az eredmények kivágására szolgáló biztonsági szűrők

Biztonsági szűrőket alkalmazhat a keresési eredmények az Azure-Cognitive Search a felhasználói identitás alapján történő körülvágásához. Ez a keresési élmény általában ahhoz szükséges, hogy össze lehessen hasonlítani azt az identitást, aki egy olyan mezőre kéri a keresést, amely tartalmazza a dokumentum engedélyeivel rendelkező alapelveket. Ha egyezést talál, a felhasználó vagy a rendszerbiztonsági tag (például egy csoport vagy szerepkör) hozzáfér ehhez a dokumentumhoz.

A biztonsági szűrés elérésének egyik módja az egyenlőségi kifejezések összetett kiválasztásán keresztül történik: például, `Id eq 'id1' or Id eq 'id2'` és így tovább. Ez a megközelítés a hibákra hajlamos, nehezen karbantartható, és olyan esetekben, amikor a lista több száz vagy több ezer értéket tartalmaz, lelassítja a lekérdezés válaszideje több másodpercen belül. 

Az egyszerűbb és gyorsabb megközelítés a `search.in` függvényen keresztül történik. Ha `search.in(Id, 'id1, id2, ...')` Egyenlőségi kifejezés helyett használja, akkor várható, hogy a rendszer a második másodpercben válaszol.

Ez a cikk bemutatja, hogyan hajthatja végre a biztonsági szűrést a következő lépések végrehajtásával:
> [!div class="checklist"]
> * A résztvevő azonosítóit tartalmazó mező létrehozása 
> * Meglévő dokumentumok leküldése vagy frissítése a vonatkozó elsődleges azonosítókkal
> * Keresési kérelem `search.in` kiadása `filter`

>[!NOTE]
> A résztvevő azonosítók lekérésének folyamata nem szerepel ebben a dokumentumban. Szerezze be az azonosítót a személyazonossági szolgáltatótól.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik [Azure-előfizetéssel](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), egy[Azure Cognitive Search szolgáltatással](search-create-service-portal.md)és egy [indexszel](search-what-is-an-index.md).  

## <a name="create-security-field"></a>Biztonsági mező létrehozása

A dokumentumoknak tartalmazniuk kell egy mezőt, amely meghatározza, hogy mely csoportok férhetnek hozzá. Ezek az információk azokra a szűrési feltételekre vonatkoznak, amelyek alapján a rendszer kijelöli vagy elutasítja a kiállítói eredményhalmaz által visszaadott dokumentumokat.
Tegyük fel, hogy a védett fájlok indexét használjuk, és minden fájlhoz egy másik felhasználó férhet hozzá.

1. Mező hozzáadása `group_ids` (itt választhat nevet) `Collection(Edm.String)` . Győződjön meg arról, hogy a mezőhöz egy `filterable` attribútum van beállítva, `true` hogy a keresési eredmények szűrve legyenek a felhasználó hozzáférése alapján. Ha például a (z) `group_ids` `["group_id1, group_id2"]` "secured_file_b" nevű dokumentumra állítja be a mezőt `file_name` , csak a "group_id1" vagy "group_id2" csoport-azonosítóhoz tartozó felhasználók rendelkeznek olvasási hozzáféréssel a fájlhoz.
   
   Győződjön meg arról, hogy a mező `retrievable` attribútuma úgy van beállítva, `false` hogy a rendszer ne adja vissza a keresési kérelem részeként.

2. `file_id` `file_name` A példa kedvéért adja hozzá a és a mezőket is.  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Az adatküldés az indexbe az REST API használatával
  
Adjon ki egy HTTP POST-kérelmet az index URL-címének végpontjának. A HTTP-kérelem törzse egy JSON-objektum, amely tartalmazza a hozzáadandó dokumentumokat:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

A kérelem törzsében adja meg a dokumentumok tartalmát:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Ha egy meglévő dokumentumot kell frissítenie a csoportok listájával, a `merge` vagy a `mergeOrUpload` műveletet használhatja:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

A dokumentumok hozzáadásával vagy frissítésével kapcsolatos részletes információkért olvassa el a [dokumentumok szerkesztése című dokumentumot](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="apply-the-security-filter"></a>A biztonsági szűrő alkalmazása

A dokumentumok hozzáférés alapján történő kivágásához egy `group_ids` szűrővel rendelkező keresési lekérdezést kell kiadnia `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` , ahol "group_id1 group_id2,..." azok a csoportok, amelyekhez a keresési kérelem kiállítója tartozik.

Ez a szűrő minden olyan dokumentumra illeszkedik, amelynek a `group_ids` mezője tartalmazza a megadott azonosítók egyikét.
A dokumentumok Azure Cognitive Search használatával történő keresésével kapcsolatos részletes információkért olvassa el a [keresési dokumentumokat](/rest/api/searchservice/search-documents).
Vegye figyelembe, hogy ez a minta bemutatja, hogyan kereshet dokumentumokat a POST-kérések használatával.

A HTTP POST-kérelem kiadása:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

Határozza meg a szűrőt a kérelem törzsében:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

A dokumentumokat vissza kell olvasnia, ahol a `group_ids` "group_id1" vagy a "group_id2" szerepel. Más szóval azokat a dokumentumokat kapja meg, amelyekre a kérelem kiállítójának olvasási hozzáférése van.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk az eredmények felhasználói identitás és a függvény alapján történő szűrésének mintáját ismerteti `search.in()` . Ezzel a függvénnyel az egyes dokumentumokhoz társított elsődleges azonosítókkal egyező azonosítókat adhat meg a kérelmező felhasználó számára. Keresési kérelem kezelésekor a `search.in` függvény kiszűri azokat a keresési eredményeket, amelyekhez a felhasználó egyetlen résztvevője sem rendelkezik olvasási hozzáféréssel. A résztvevő azonosítói a biztonsági csoportok, szerepkörök vagy akár a felhasználó saját identitása is lehetnek.

A Active Directoryon alapuló alternatív minta esetén vagy más biztonsági funkciók újrakereséséhez tekintse meg a következő hivatkozásokat.

* [Az eredmények kivágására szolgáló biztonsági szűrők Active Directory identitások használatával](search-security-trimming-for-azure-search-with-aad.md)
* [Biztonság az Azure Cognitive Search](search-security-overview.md)