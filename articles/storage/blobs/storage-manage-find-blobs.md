---
title: Azure Blob-adatkezelés és-keresés a blob-index címkékkel (előzetes verzió)
description: Megtudhatja, hogyan használhat blob-index címkéket a blob-objektumok kategorizálásához, kezeléséhez és lekérdezéséhez.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 2188aaea0cf5a4616291d3fdad839aefb2dbc413
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280700"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Azure Blob-adatkezelés és-keresés a blob-index címkékkel (előzetes verzió)

Mivel az adatkészletek nagyobb méretűek, nehéz lehet egy adott objektum megkeresése egy adattengeren. A blob index címkéi az adatkezelési és felderítési képességeket a kulcs-érték index címke attribútumaival biztosítják. Az objektumokat kategorizálhatja és megkeresheti egyetlen tárolóban vagy a Storage-fiók összes tárolóján belül. Az adatkövetelmények változása esetén az objektumok dinamikusan kategorizálható az index-címkék frissítésével. Az objektumok az aktuális Container-szervezettel maradhatnak.

A blob index címkéi a következőket teszik lehetővé:

- Blobok dinamikusan kategorizálása a kulcs-érték index-címkék használatával
- Adott címkézett Blobok gyors keresése egy teljes Storage-fiókban
- Feltételes viselkedés megadása a blob API-k számára az index címkék kiértékelése alapján
- Az indexelési címkék használata a speciális vezérlőkhöz, például a [blob életciklus-kezelési](storage-lifecycle-management-concepts.md) funkciói

Vegyünk például egy olyan forgatókönyvet, ahol több millió blob található a Storage-fiókban, és számos különböző alkalmazás érhető el. Egyetlen projekt összes kapcsolódó adatát szeretné megkeresni. Nem biztos benne, hogy mi a hatókör, mert az adathalmaz több tárolón is átterjedhet különböző elnevezési konvenciókkal. Az alkalmazások azonban az összes, a projekten alapuló címkével feltöltik az összes adathalmazt. Ahelyett, hogy több millió blobot keres, és nem hasonlítja össze a neveket és a tulajdonságokat, használhatja `Project = Contoso` felderítési feltételként. A blob index a teljes Storage-fiókban lévő összes tárolót szűri, így gyorsan megkeresheti és visszaküldheti a 50-es Blobok készletét `Project = Contoso` .

> [!IMPORTANT]
> A blob index címkék jelenleg **előzetes** verzióban érhetők el, és az összes nyilvános régióban elérhetők. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

A blob index használatára vonatkozó példákkal kapcsolatban lásd: [blob-index címkék használata az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>BLOB index címkék és adatkezelés

A Container és a blob Name előtag egydimenziós kategorizálás. A blob index címkéi lehetővé teszik a többdimenziós kategorizálást a [blob-adattípusok esetében (blokk, Hozzáfűzés vagy lap)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). A többdimenziós kategorizálást natív módon indexeli az Azure Blob Storage, így gyorsan megtalálhatja adatait.

Vegye figyelembe a következő öt blobot a Storage-fiókban:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *fényképek/bannerphoto.png*
- *Archívum/befejezett/2019review.pdf*
- *naplók/2020/01/01/logfile.txt*

Ezek a Blobok a *tároló/virtuális mappa/blob neve* előtaggal vannak elválasztva. Az alábbi öt blobon beállíthatja az index címke attribútumát `Project = Contoso` , hogy azok együtt legyenek kategorizálva az aktuális előtag-szervezetük megtartása mellett. Az indexelési címkék hozzáadásával nem kell áthelyeznie az adatok áthelyezését azáltal, hogy az index használatával szűrni és keresni kívánja az adatkeresési lehetőséget.

## <a name="setting-blob-index-tags"></a>BLOB-index címkék beállítása

A blob index címkéi olyan kulcs-érték attribútumok, amelyek alkalmazhatók a Storage-fiókjában lévő új vagy meglévő objektumokra is. A feltöltési folyamat során megadhat index címkéket a [put blob](/rest/api/storageservices/put-blob), a [tiltási lista](/rest/api/storageservices/put-block-list)vagy a [blob másolása](/rest/api/storageservices/copy-blob) és a nem kötelező `x-ms-tags` fejléc használatával. Ha már rendelkezik Blobokkal a Storage-fiókban, hívja [meg a blob-címkék beállítása](/rest/api/storageservices/set-blob-tags) a formázott XML-dokumentumot a kérelem törzsében szereplő index címkékkel.

> [!IMPORTANT]
> A blob-indexek címkéit a [Storage blob-Adattulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) , valamint a blob-címkék ( `t` sas-engedély) elérésére jogosult közös hozzáférési aláírással rendelkező felhasználók is elvégezheti.
>
> Emellett az engedéllyel rendelkező felhasználók RBAC `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` is elvégezhetik ezt a műveletet.

A blobon egyetlen címkét alkalmazhat, amely leírja, hogy mikor végzett az adatai feldolgozásával.

> "processedDate" = "2020-01-01"

A blobon több címkét is alkalmazhat, hogy az adatai jobban meglegyenek.

> "Projekt" = "contoso"  
> "Besorolt" = "true"  
> "Status" = "feldolgozatlan"  
> "Priority" = "01"

A meglévő index címke attribútumainak módosításához kérje le a meglévő címke attribútumait, módosítsa a címke attribútumait, és cserélje le a [blob-címkék beállítása](/rest/api/storageservices/set-blob-tags) műveletre. Ha az összes index címkét el szeretné távolítani a blobból, hívja meg a `Set Blob Tags` műveletet, és ne adja meg a címke attribútumait. Mivel a blob-index címkéi a blob-adattartalomhoz tartozó alerőforrások, `Set Blob Tags` nem módosítják a mögöttes tartalmakat, és nem változtatják meg a blob utolsó módosításának időpontját vagy eTag. Az összes jelenlegi alapblob és a korábbi verziók esetében létrehozhat vagy módosíthat index-címkéket. A pillanatképek vagy a törölt Blobok címkéi azonban nem módosíthatók.

A következő korlátozások vonatkoznak a blob index címkékre:

- Minden blob legfeljebb 10 blob-index címkével rendelkezhet
- A címkék kulcsának egy és 128 karakter közöttinek kell lennie
- A címke értékének nulla és 256 karakter közöttinek kell lennie
- A kulcsok és értékek címkézése megkülönbözteti a kis-és nagybetűket
- A kulcsok és az értékek címkézése csak a karakterlánc-adattípusokat támogatja. A rendszer a számokat, dátumokat, időpontokat vagy speciális karaktereket karakterláncként menti.
- A kulcsok és értékek címkézésének meg kell felelnie a következő elnevezési szabályoknak:
  - Alfanumerikus karakterek:
    - **a** – **z** (kisbetűs)
    - **A** – **Z** (nagybetűs)
    - **0** – **9** (szám)
  - Érvényes speciális karakterek: szóköz, plusz, mínusz, időtartam, kettőspont, egyenlő, aláhúzás, továbbítási perjel ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>BLOB-index címkék beolvasása és listázása

A blob index címkéit alerőforrásként tárolja a rendszer a blob-adatok mellett, és az alapul szolgáló blob-adatoktól függetlenül is lekérhető. A blob-indexek címkéi egyetlen blobhoz a blob- [címkék beolvasása](/rest/api/storageservices/get-blob-tags) művelettel kérhetők le. A (z) paraméterrel rendelkező Blobok [listája](/rest/api/storageservices/list-blobs) a `include:tags` tárolón belüli összes blobot is visszaküldi a blob-index címkével együtt.

> [!IMPORTANT]
> A blob-indexek lekérése és listázása a [Storage blob-adatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) és bárki számára olyan közös hozzáférésű aláírással végezhető el, amely jogosult a blob-címkék ( `t` sas-engedély) elérésére.
>
> Emellett az engedéllyel rendelkező felhasználók RBAC `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` is elvégezhetik ezt a műveletet.

Minden olyan blobnál, amely legalább egy blob index címkével rendelkezik, a a Blobok `x-ms-tag-count` [listázása](/rest/api/storageservices/list-blobs), a [blob lekérése](/rest/api/storageservices/get-blob)és a blob- [Tulajdonságok beolvasása](/rest/api/storageservices/get-blob-properties) művelet jelzi a blobban lévő indexek számát.

## <a name="finding-data-using-blob-index-tags"></a>Az adatkeresés a blob-index címkékkel

Az indexelési motor a kulcs-érték attribútumokat több dimenziós indexbe helyezi. Az indexelési címkék beállítása után azok a blobon találhatók, és azonnal lekérhető. A blob-index frissítései előtt eltarthat egy ideig. A blob-index frissítései után a Blob Storage által kínált natív lekérdezési és felderítési képességeket használhatja.

A [Blobok keresése címkék szerint](/rest/api/storageservices/find-blobs-by-tags) művelet lehetővé teszi olyan Blobok szűrt készletének lekérését, amelyek indexelési címkéi egy adott lekérdezési kifejezésnek felelnek meg. `Find Blobs by Tags` a a Storage-fiókban lévő összes tárolóban támogatja a szűrést, vagy a szűrést csak egyetlen tárolóra szűkítheti. Mivel az összes indexelő címke kulcsa és értéke karakterlánc, a lexikográfiai rendezést használ a viszonyítási operátorok.

> [!IMPORTANT]
> Az adatkeresés a blob-indexek használatával a [Storage blob-adattulajdonos](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) és a közös hozzáférési aláírással rendelkező felhasználók számára is végrehajtható, amely jogosult a Blobok címkék szerinti keresésére ( `f` sas-engedély).
>
> Emellett az engedéllyel rendelkező felhasználók RBAC `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` is elvégezhetik ezt a műveletet.

A blob index szűrésére a következő feltételek érvényesek:

- A címke kulcsai idézőjelek közé kell, hogy legyenek (")
- A címkézési értékeket és a tárolók nevét szimpla idézőjelek közé kell foglalni (')
- A @ karakter csak egy adott tároló neve esetében engedélyezett (például: `@container = 'ContainerName'` ).
- A szűrők lexikográfiai-rendezéssel lesznek alkalmazva a sztringeken
- Ugyanazon a kulcson azonos egyoldalas tartománybeli műveletek is érvénytelenek (például: `"Rank" > '10' AND "Rank" >= '15'` ).
- Ha a REST használatával hoz létre egy szűrési kifejezést, a karaktereknek URI-kódolással kell rendelkezniük.

Az alábbi táblázatban az összes érvényes operátor látható `Find Blobs by Tags` :

|  Operátor  |  Leírás  | Példa |
|------------|---------------|---------|
|     =      |     Egyenlő     | `"Status" = 'In Progress'` |
|     >      |  Nagyobb, mint | `"Date" > '2018-06-18'` |
|     >=     |  Nagyobb vagy egyenlő mint | `"Priority" >= '5'` |
|     <      |  Kisebb mint   | `"Age" < '32'` |
|     <=     |  Kisebb vagy egyenlő mint  | `"Company" <= 'Contoso'` |
|    ÉS     |  Logikai és  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Hatókör egy adott tárolóra | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> A címkék beállításakor és lekérdezése során Ismerje meg a lexicographical megrendelését.
>
> - A számok a betűk előtt vannak rendezve. A számok az első számjegy alapján vannak rendezve.
> - A nagybetűket a kisbetűs karakterek előtt rendezi a rendszer.
> - A szimbólumok nem szabványosak. Egyes szimbólumok a numerikus értékek előtt vannak rendezve. A többi szimbólum a levelek előtt vagy után van rendezve.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Feltételes blob-műveletek a blob-index címkékkel

Az 2019-10-10-as és újabb REST-verziók esetében a legtöbb [blob Service API](/rest/api/storageservices/operations-on-blobs) mostantól támogatja a feltételes fejlécet, `x-ms-if-tags` így a művelet csak akkor lesz sikeres, ha a megadott blob-index feltétele teljesül. Ha a feltétel nem teljesül, megkapja a következőket: `error 412: The condition specified using HTTP conditional header(s) is not met` .

Előfordulhat, hogy a `x-ms-if-tags` fejléc a többi meglévő http feltételes fejléctel együtt használható (IF-Match, If-None-Match stb.). Ha egy kérelemben több feltételes fejléc van megadva, akkor a művelet sikerességéhez minden esetben igaz értéket kell kiértékelni. A feltételes fejlécek hatékonyan kombinálhatók a logikai és a szolgáltatással.

Az alábbi táblázatban a feltételes műveletek érvényes operátorai láthatók:

|  Operátor  |  Leírás  | Példa |
|------------|---------------|---------|
|     =      |     Egyenlő     | `"Status" = 'In Progress'` |
|     <>     |   Nem egyenlő   | `"Status" <> 'Done'` |
|     >      |  Nagyobb, mint | `"Date" > '2018-06-18'` |
|     >=     |  Nagyobb vagy egyenlő mint | `"Priority" >= '5'` |
|     <      |  Kisebb mint   | `"Age" < '32'` |
|     <=     |  Kisebb vagy egyenlő mint  | `"Company" <= 'Contoso'` |
|    ÉS     |  Logikai és  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | Logikai vagy   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Két további operátor létezik, nem egyenlő és logikai, vagy pedig a `x-ms-if-tags` blob-műveletek feltételes fejlécében engedélyezett, de nem léteznek a `Find Blobs by Tags` műveletben.

## <a name="platform-integrations-with-blob-index-tags"></a>Platform-integrációk a blob index címkékkel

A blob-index címkéi nem csak a blob-adataik kategorizálását, kezelését és keresését segítik, hanem más Blob Storage szolgáltatásokkal való integrációt is biztosítanak, például az [életciklus-felügyeletet](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Életciklus-kezelés

Az `blobIndexMatch` as a as a Rule (szabály) szűrő használata az életciklus-kezelésben az adatátvitelt hűvösebb rétegekre helyezheti át, vagy a blobokra alkalmazott indexelési címkék alapján törölheti az adatait. Részletesebben is megtekintheti a szabályokat, és csak akkor helyezheti át vagy törölheti a blobokat, ha azok megfelelnek a megadott címkék feltételeinek.

A blob-indexeket beállíthatja úgy, hogy egy életciklus-szabályban beállított önálló szűrőként alkalmazza a címkézett adatműveleteket. Vagy kombinálhatja az előtagot és a blob-indexet is, hogy az megfeleljen az egyes adatkészleteknek. Ha több szűrőt ad meg egy életciklus-szabályban, a logikai és a műveletet alkalmazza. A művelet csak akkor lesz érvényes, ha az *összes* szűrési feltétel egyezik.

A következő minta életciklus-kezelési szabály a *videofiles* nevű tárolóban lévő Blobok blokkolására vonatkozik. A szabály-rétegek csak akkor archiválják az archiválási tárolót, ha az adatok megfelelnek a blob-index címkéi feltételeinek `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portál](#tab/azure-portal)

![A blob-index egyezési szabálya a Azure Portal életciklus-felügyeletére vonatkozó példa](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Engedélyek és engedélyezés

A blob indexekhez való hozzáférést a következő módszerek egyikével engedélyezheti:

- Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata egy Azure Active Directory (Azure AD) rendszerbiztonsági tag engedélyeinek megadásához. Az Azure AD használata a kiváló biztonság és a könnyű használat érdekében. További információ az Azure AD és a blob-műveletek használatáról: a Blobok [és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../common/storage-auth-aad.md).
- Közös hozzáférésű aláírás (SAS) használata a blob-indexhez való hozzáférés delegálására. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md).
- A fiók-hozzáférési kulcsok használatával engedélyezze a megosztott kulccsal rendelkező műveleteket. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key).

A blob index címkéi a blob-adatforráshoz tartozó alerőforrások. Előfordulhat, hogy a Blobok olvasásához vagy írásához szükséges engedélyekkel rendelkező felhasználó vagy SAS-token nem fér hozzá a blob-index címkéhez.

### <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

Az [Azure ad-identitást](../common/storage-auth-aad.md) használó hívók a következő engedélyeket kaphatják meg a blob index-címkéken való működéshez.

| BLOB-index címkézési műveletei                                          | Azure RBAC művelet                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [BLOB-címkék beállítása](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/címkék/írás    |
| [BLOB-címkék beolvasása](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/címkék/olvasás     |
| [Blobok keresése címkék alapján](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/Filter/művelet |

A mögöttes blob-adatoktól elkülönítve további engedélyek szükségesek az index címke műveleteihez. A [Storage blob-adatok tulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepköre engedélyeket kap mind a három blob-index címkézési művelethez. A [Storage blob-Adatolvasó](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) csak a és a `Find Blobs by Tags` `Get Blob Tags` műveletekhez biztosít engedélyeket.

### <a name="sas-permissions"></a>SAS-engedélyek

A [közös hozzáférésű aláírást (SAS)](../common/storage-sas-overview.md) használó hívók hatókörön belüli engedélyekkel rendelkezhetnek a blob index címkén való működéshez.

#### <a name="blob-sas"></a>BLOB SAS

A következő engedélyek adhatók meg egy blob SAS-ben a blob-indexekhez való hozzáférés engedélyezéséhez. A blob olvasási és írási engedélyei önmagában nem elegendőek ahhoz, hogy lehetővé tegyék az indexelési címkék olvasását vagy írását.

| Engedély | URI-szimbólum | Engedélyezett műveletek                |
|------------|------------|-----------------------------------|
| Címkék indexelése |     t      | Blobok indexelési címkéinak beolvasása és beállítása |

#### <a name="container-sas"></a>Tároló SAS

A következő engedélyek adhatók meg egy tároló SAS-ben a blob-címkék szűrésének engedélyezéséhez. Az `Blob List` engedély nem elegendő ahhoz, hogy a Blobok indexelési címkékkel engedélyezzék a szűrést.

| Engedély | URI-szimbólum | Engedélyezett műveletek         |
|------------|------------|----------------------------|
| Címkék indexelése |     f      | Blobok keresése az index címkékkel |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>A metaadatok és a blob-index címkék közötti választás

A blob-index címkéi és metaadatai egyaránt lehetővé teszik a felhasználó által definiált kulcs-érték tulajdonságok tárolására a blob-erőforrások mellett. Mindkettő lekérhető és beállítható közvetlenül a blob tartalmának visszaküldése vagy módosítása nélkül. A metaadatok és az indexelési címkék egyaránt használhatók.

Csak indexelő címkék automatikusan indexelve lesznek, és a natív Blob Storage szolgáltatás által kereshetővé tehetők. A metaadatok nem lehetnek natív módon indexelve vagy kereshetők. Külön szolgáltatást kell használnia, például [Azure Search](../../search/search-blob-ai-integration.md). A blob index címkéi további engedélyekkel rendelkeznek a mögöttes blob-adatoktól elkülönítve olvasáshoz, szűréshez és íráshoz. A metaadatok ugyanazokat az engedélyeket használják, mint a blob, és a rendszer a blob [lekérése](/rest/api/storageservices/get-blob) és a [blob tulajdonságainak beolvasása](/rest/api/storageservices/get-blob-properties) művelettel http-fejlécként adja vissza. A blob indexek címkéi a [Microsoft által felügyelt kulcs](../common/storage-service-encryption.md)használatával titkosítva vannak. A metaadatok titkosítva maradnak a blob-adatokhoz megadott titkosítási kulcs használatával.

Az alábbi táblázat a metaadatok és a blob-index címkék közötti különbségeket foglalja össze:

|              |   Metaadatok   |   BLOB-index Címkék  |
|--------------|--------------|--------------------|
| **Korlátok**      | Nincs numerikus korlát, 8 KB összeg, kis-és nagybetűk megkülönböztetése | 10 címke/blob max., 768 bájt/címke, kis-és nagybetűk megkülönböztetése |
| **Frissítések**    | Archiválási szinten nem engedélyezett, `Set Blob Metadata` az összes meglévő metaadatot lecseréli, és `Set Blob Metadata` megváltoztatja a blob utolsó módosításának időpontját. | Minden hozzáférési réteg esetében engedélyezett, és az `Set Blob Tags` összes meglévő címkét lecseréli, `Set Blob Tags` nem változtatja meg a blob utolsó módosításának időpontját. |
| **Storage**     | A blob-adattal együtt tárolva | A blob-adatforrások alerőforrása |
| **Indexelés & lekérdezés** | Külön szolgáltatást kell használnia, például Azure Search | A Blob Storagebe épített funkciók indexelése és lekérdezése |
| **Titkosítás** | Titkosítva, a blob-adatokhoz használt titkosítási kulccsal | Microsoft által felügyelt titkosítási kulccsal inaktív állapotban titkosított |
| **Díjszabás** | A metaadatok mérete a blob tárolási költségei közé tartozik. | Fix Cost index címkével |
| **Fejléc válasza** | A és a fejlécében visszaadott metaadatok `Get Blob``Get Blob Properties` | A vagy a által visszaadott `Get Blob` `Get Blob Properties` címkék száma, és a csak a és a címkét adja vissza. `Get Blob Tags``List Blobs` |
| **Engedélyek**  | A blob-adatokra vonatkozó olvasási vagy írási engedélyek kiterjeszthetők a metaadatokra | További engedélyek szükségesek a tárgymutató-címkék olvasásához, szűréséhez vagy írásához |
| **Elnevezés** | A metaadatok neveinek meg kell felelniük a C# azonosítók elnevezési szabályainak | A blob index címkéi több alfanumerikus karaktert támogatnak |

## <a name="pricing"></a>Díjszabás

A blob index díjszabása nyilvános előzetes verzióban érhető el, és az általános elérhetőséget is megváltoztathatja. A Storage-fiókban lévő index-címkék havi átlagos számának díját számítjuk fel. Az indexelési motornak nincs díja. A és a rendszerre irányuló kérelmeket a `Set Blob Tags` `Get Blob Tags` `Find Blobs by Tags` megfelelő működési típusok szerint számítjuk fel. [További információért lásd a blob díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakört.

## <a name="regional-availability-and-storage-account-support"></a>Regionális rendelkezésre állási és Storage-fiókok támogatása

A blob index címkék csak általános célú v2 (GPv2) fiókokban érhetők el, amelyeken a hierarchikus névtér (HNS) le van tiltva. A általános célú-(GPV1-) fiókok nem támogatottak, de bármely GPv1-fiókot GPv2-fiókra frissíthet.

A Premium Storage-fiókok nem támogatják az indexelési címkéket. A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../common/storage-account-overview.md).

A blob index-címkék jelenleg az összes nyilvános régióban elérhetők.

Első lépésként tekintse meg a [blob-index címkék használata az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md)című témakört.

> [!IMPORTANT]
> Regisztrálnia kell az előfizetését, mielőtt használni tudja a blob index előzetes verzióját a Storage-fiókokban. Tekintse meg a jelen cikk [feltételek és ismert problémák](#conditions-and-known-issues) című szakaszát.

### <a name="register-your-subscription-preview"></a>Előfizetés regisztrálása (előzetes verzió)

Mivel a blob-index címkéi csak nyilvános előzetes verzióban érhetők el, regisztrálnia kell az előfizetést, mielőtt használni tudja a funkciót. Kérelem elküldéséhez futtassa a következő PowerShell-vagy CLI-parancsokat.

#### <a name="register-by-using-powershell"></a>Regisztrálás a PowerShell használatával

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Regisztrálás az Azure CLI használatával

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Feltételek és ismert problémák

Ez a szakasz a blob-indexek címkék nyilvános előzetes verziójának ismert problémáit és feltételeit ismerteti. Ez a szolgáltatás nem használható éles számítási feladatokhoz, amíg az általánosan elérhetővé nem válik (GA), mivel a működés változhat.

- Előzetes verzióként regisztrálnia kell az előfizetését, mielőtt a Storage-fiókhoz az előzetes verziójú régiókban lévő blob-indexet használhassa.
- Előzetes verzióban csak a GPv2-fiókok támogatottak. A blob, a BlockBlobStorage és a HNS engedélyezve DataLake Gen2-fiókok nem támogatottak. A GPv1-fiókok nem támogatottak.
- Az oldal-Blobok indexelési címkékkel való feltöltése nem őrzi meg a címkéket. Adja meg a címkéket az oldal blobjának feltöltése után.
- Ha a szűrés egyetlen tárolóra van korlátozva, a `@container` csak akkor adható át, ha a szűrő kifejezésben szereplő összes indexelő címke egyenlőségi ellenőrzés (kulcs = érték).
- Ha a tartomány operátort használja a `AND` feltétellel, akkor csak ugyanazt az index címke kulcsának nevét () kell megadnia `"Age" > '013' AND "Age" < '100'` .
- A verziószámozás és a blob-index nem támogatott. A blob-index címkéi megmaradnak a verzióknál, de nem jutnak el a blob index motorhoz.
- Nincs olyan API, amely meghatározza, hogy az indexelési címkék indexelve vannak-e.
- A fiók feladatátvétele nem támogatott. Előfordulhat, hogy a blob-index nem frissül megfelelően a feladatátvétel után.
- Az életciklus-kezelés csak a blob-index egyeztetését támogató esélyegyenlőségi ellenőrzéseket támogatja.
- `Copy Blob` nem másolja a blob-index címkéit a forrás blobból az új cél blobba. A másolási művelet során megadhatja a cél blobra alkalmazni kívánt címkéket.
- `Copy Blob` (Aszinkron másolás) egy másik Storage-fiók és a célként megadott blobon alkalmazott címkék használatával a blob index motorja nem adja vissza a blobot és annak címkéit a szűrő készletében. Használat `Copy Blob` URL-címről (szinkronizálási másolat).
- A címkék a pillanatképek létrehozásán is megmaradnak. A pillanatkép előléptetése azonban nem támogatott, és egy üres kódelemet eredményezhet.

## <a name="faq"></a>GYIK

**A blob-index segíthet a saját blobokban lévő tartalmak szűrésében és lekérdezésében?**

Nem, ha a blob-adatain belül kell keresnie, használja a lekérdezési gyorsítás vagy az Azure Search funkciót.

**Vannak követelmények az index címkézési értékeire vonatkozóan?**

A blob index címkéi csak a karakterlánc-adattípusokat támogatják, a lekérdezés pedig az eredményeket a lexicographical rendeléssel adja vissza. A Numbers értéke nulla. Dátumok és időpontok esetén az áruház ISO 8601-kompatibilis formátumú.

**A blob-indexek címkéi és a hozzájuk kapcsolódó Azure Resource Manager Címkék?**

Nem, a Resource Manager-címkék segítenek megszervezni a vezérlési sík erőforrásait, például az előfizetéseket, az erőforráscsoportokat és a Storage-fiókokat. Az index címkéi a Blobok kezelését és felderítését biztosítják az adatsíkon.

## <a name="next-steps"></a>Következő lépések

A blob-indexek használatáról a [blob index használata az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md)című témakörben talál példát.

Ismerje meg az [életciklus-kezelést](storage-lifecycle-management-concepts.md) , és állítson be egy olyan szabályt, amely a blob-indexnek megfelelő.
