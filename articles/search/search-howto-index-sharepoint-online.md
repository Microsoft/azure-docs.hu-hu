---
title: SharePoint Online indexelő konfigurálása (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Állítson be egy SharePoint Online indexelő, amely automatizálja a dokumentumtár tartalmának indexelését az Azure Cognitive Searchban.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 22adccfc4adbb7f8b1c72d8b5705ec8fcdb9a375
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441091"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>A SharePoint Online indexelés konfigurálása a Cognitive Searchban (előzetes verzió)

> [!IMPORTANT] 
> A SharePoint Online-támogatás jelenleg egy **kezdeményezett nyilvános előzetes** verzióban érhető el. Az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével hozzáférést kérhet a kihelyezett előzetes verzióhoz.
>
> Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> A [REST API 2020-06-30-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál vagy SDK-támogatás.

> [!NOTE]
> A SharePoint Online olyan szemcsés engedélyezési modellt támogat, amely a dokumentum szintjén határozza meg a felhasználónkénti hozzáférést. A SharePoint Online indexelő nem kéri le ezeket az engedélyeket a keresési indexbe, és Cognitive Search nem támogatja a dokumentum szintű engedélyezést. Ha egy dokumentum indexelve van a SharePoint Online-ból egy keresési szolgáltatásba, a tartalom mindenki számára elérhető lesz, aki olvasási hozzáféréssel rendelkezik az indexhez. Ha a dokumentum szintű engedélyekre van szüksége, meg kell vizsgálnia a biztonsági szűrőket a jogosulatlan tartalom eredményeinek kivágásához. További információ: [biztonsági kivágás Active Directory identitások használatával](search-security-trimming-for-azure-search-with-aad.md).

Ez a cikk azt ismerteti, hogyan használható az Azure Cognitive Search a SharePoint Online-dokumentumtárakban tárolt dokumentumok (például PDF-fájlok, Microsoft Office dokumentumok és számos más gyakori formátum) Azure Cognitive Search indexbe való indexeléséhez. Először is ismerteti az indexelő beállításának és konfigurálásának alapjait. Ezt követően mélyebben megismerheti a viselkedéseket és a valószínűleg felmerülő forgatókönyveket.

## <a name="functionality"></a>Funkció
Az Azure Cognitive Search indexelő egy olyan webbejáró, amely egy adatforrásból Kinyeri a kereshető adatokat és metaadatokat. A SharePoint Online-indexelő a SharePoint Online-webhelyhez fog csatlakozni, és egy vagy több dokumentumtárból indexeli a dokumentumokat. Az indexelő a következő funkciókat biztosítja:
+ Tartalom indexelése egy vagy több SharePoint Online-dokumentumtárból.
+ Indexelheti a tartalmat a SharePoint Online-dokumentumtárakban, amelyek ugyanabban a bérlőben találhatók, mint az Azure Cognitive Search szolgáltatás. Az indexelő nem fog működni olyan SharePoint-webhelyekkel, amelyek egy másik bérlőn találhatók, mint az Azure Cognitive Search szolgáltatás. 
+ Az indexelő támogatja a növekményes indexelést, ami azt jelenti, hogy a dokumentumtárban megváltoztak a tartalom, és a rendszer csak a frissített tartalmat indexeli a jövőbeli indexelési futtatások során. Ha például 5 PDF-et eredetileg az indexelő indexel, az 1 frissül, majd az indexelő újra fut, az indexelő csak a frissített 1 PDF-fájlt fogja indexelni.
+ A rendszer alapértelmezés szerint Kinyeri a szöveget és a normalizált képeket az indexelt dokumentumokból. Opcionálisan készségkészlet is hozzáadhat a folyamathoz a tartalom további bővítése érdekében. A szakértelmével kapcsolatos további információkért tekintse meg a [készségkészlet-fogalmakat az Azure Cognitive Search](cognitive-search-working-with-skillsets.md)cikkben.

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok

Az Azure Cognitive Search SharePoint Online indexelő a következő dokumentum-formátumokból tud szöveget kinyerni:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelés és törlés észlelése
Alapértelmezés szerint a SharePoint Online indexelő támogatja a növekményes indexelést, ami azt jelenti, hogy a dokumentumtárban megváltoztak a tartalom, és a rendszer csak a frissített tartalmat indexeli a jövőbeli indexelési futtatásokban. Ha például 5 Word-dokumentumot eredetileg az indexelő indexel, az 1 frissül, majd az indexelő újra fut, az indexelő csak a frissített 1 Word-dokumentumot fogja újra indexelni.

Alapértelmezés szerint a törlés észlelése is támogatott. Ez azt jelenti, hogy ha töröl egy dokumentumot egy SharePoint Online-dokumentumtárból, az indexelő felismeri a törlést a jövőbeli indexelő futtatása során, és eltávolítja a dokumentumot az indexből.

## <a name="setting-up-sharepoint-online-indexing"></a>A SharePoint Online indexelésének beállítása
A SharePoint Online indexelő beállításához végre kell hajtania néhány műveletet a Azure Portal és néhány műveletet a Preview REST API használatával. Az SDK nem támogatja ezt az előzetes verziót.

### <a name="step-1-enable-system-assigned-managed-identity"></a>1. lépés: a rendszerhez rendelt felügyelt identitás engedélyezése
Ha egy rendszerhez rendelt felügyelt identitás engedélyezve van, az Azure létrehoz egy identitást a keresési szolgáltatás számára, amelyet az indexelő használhat.

![Rendszerhez rendelt felügyelt identitás engedélyezése](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Rendszerhez rendelt felügyelt identitás engedélyezése")

A **Mentés** gombra kattintva megjelenik egy objektumazonosító, amely hozzá van rendelve a keresési szolgáltatáshoz.

![Rendszerhez rendelt felügyelt identitás](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Rendszerhez rendelt felügyelt identitás")

### <a name="step-2-create-an-aad-application"></a>2. lépés: HRE-alkalmazás létrehozása
A SharePoint Online indexelő ezt a HRE alkalmazást fogja használni a hitelesítéshez. 

1.  Navigáljon a [Azure Portal](https://portal.azure.com/).

1.  Nyissa meg a Főoldal bal oldalán található menüt, majd válassza a **Azure Active Directory** **Alkalmazásregisztrációk** lehetőséget. Válassza az **+ új regisztráció** lehetőséget.
    1.  Adja meg az alkalmazás nevét.
    2.  Válasszon **egy bérlőt**.
    3.  Nincs szükség átirányítási URI-ra.
    4.  **Regisztráció** kiválasztása

1.  Válassza az **API-engedélyek** lehetőséget a bal oldali menüben, majd **adjon hozzá egy engedélyt**, majd **Microsoft Graph** majd **delegált engedélyeket**. Adja hozzá a következő API-engedélyeket: 
    1.  **Delegált fájlok. Read. All** 
    2.  **Delegált-sites. Read. All** 
    3.  **Delegált felhasználó. Read**

    ![Delegált API-engedélyek](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Delegált API-engedélyek")

    A delegált engedélyek használata azt jelenti, hogy az indexelő hozzáfér a SharePoint-webhelyhez a felhasználói környezetben. Tehát az indexelő futtatásakor csak azok a tartalmak férhetnek hozzá, amelyekhez a bejelentkezett felhasználó hozzáfér. A felhasználói bejelentkezés az indexelő létrehozásakor vagy a dátum forrásának frissítésekor történik. A bejelentkezési lépésről a cikk későbbi részében olvashat.

1.  Válassza a **hitelesítés** lapot. Állítsa a **nyilvános ügyfelek forgalmának engedélyezése** **Igen értéket** , majd válassza a **Mentés** lehetőséget.

1.  Válassza **a + platform hozzáadása**, majd a **mobil-és asztali alkalmazások**, majd a `https://login.microsoftonline.com/common/oauth2/nativeclient` **Konfigurálás** lehetőséget.

    ![HRE-alkalmazás hitelesítésének konfigurációja](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "HRE-alkalmazás hitelesítésének konfigurációja")

1.  Rendszergazdai beleegyezés megadása (csak bizonyos bérlők esetében szükséges).

    Bizonyos bérlők úgy vannak zárolva, hogy rendszergazdai jogosultsággal kell rendelkezniük a delegált API-engedélyekhez. Ebben az esetben rendszergazdai jogosultsággal kell rendelkeznie ehhez a HRE-alkalmazáshoz az indexelő létrehozása előtt. 

    Mivel nem minden bérlő rendelkezik ezzel a követelménnyel, javasoljuk, hogy először ugorja át ezt a lépést, és folytassa az utasításokat. Ha az indexelő létrehozásakor rendszergazdai jóváhagyásra van szüksége, a hitelesítés sikertelen lesz, és a hitelesítés jóváhagyásához rendszergazdai jogosultságra van szükség. Ebben az esetben a bérlői rendszergazda az alábbi gomb használatával adja meg a jóváhagyást.

    ![HRE-alkalmazás engedélyezése rendszergazdai jóváhagyással](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "HRE-alkalmazás engedélyezése rendszergazdai jóváhagyással")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>3. lépés: adatforrás létrehozása
> [!IMPORTANT] 
> Ennek a szakasznak a megkezdése előtt az előzetes verzió REST API kell használnia a hátralévő lépésekhez. Ha még nem ismeri az Azure Cognitive Search REST API, javasoljuk, hogy tekintse át [ezt a rövid útmutatót.](search-get-started-rest.md)

Az adatforrás meghatározza az adatokhoz való hozzáféréshez szükséges hitelesítő adatokat, valamint a szabályzatokat az adatok változásainak hatékony azonosításához (új, módosított vagy törölt sorok). Egy adatforrást több indexelő is használhat ugyanabban a keresési szolgáltatásban.

A SharePoint-indexeléshez az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:
+ a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
+ a típusnak "SharePoint" **típusúnak** kell lennie. Ez megkülönbözteti a kis-és nagybetűket.
+ a **hitelesítő adatok** biztosítják a SharePoint Online-végpontot és a HRE alkalmazás (ügyfél) azonosítóját. Egy példa a SharePoint Online-végpontra `https://microsoft.sharepoint.com/teams/MySharePointSite` . A SharePoint Online-végpontot úgy érheti el, hogy a SharePoint-webhely kezdőlapján navigál, és az URL-címet a böngészőből másolja.
+ a **Container** meghatározza, hogy melyik dokumentumtárat kell indexelni. A tároló létrehozásával kapcsolatos további információkért tekintse meg a jelen dokumentum a [dokumentumok indexelt](#controlling-which-documents-are-indexed) részeinek ellenőrzése című szakaszát.

Adatforrás létrehozása:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>4. lépés: index létrehozása
Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy kereshető tartalommal rendelkező indexet a dokumentumokból kinyert szöveg tárolására a dokumentumtárban:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

További információ: [create index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>5. lépés: indexelő létrehozása
Az indexelő egy adatforrást hoz létre a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít. Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására.

Ebben a szakaszban a rendszer megkéri, hogy jelentkezzen be a vállalati hitelesítő adataival, amelyek hozzáférnek a SharePoint-webhelyhez. Ha lehetséges, javasoljuk, hogy hozzon létre egy új szervezeti felhasználói fiókot, és adja meg az új felhasználónak azokat a pontos engedélyeket, amelyeknek az indexelő számára szüksége van.

Az indexelő létrehozása néhány lépésből áll:

1.  Küldjön egy kérelmet az indexelő létrehozásához.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  Az indexelő első létrehozásakor a művelet sikertelen lesz, és a következő hibaüzenet jelenik meg. Lépjen a hibaüzenetben található hivatkozásra. Ha 10 percen belül nem lép a hivatkozásra, a kód lejár, és újra létre kell hoznia az [adatforrást](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Adja meg a hibaüzenetben megadott kódot.

    ![Adja meg az eszköz kódját](media/search-howto-index-sharepoint-online/enter-device-code.png "Adja meg az eszköz kódját")

1.  A SharePoint indexelő a bejelentkezett felhasználóként fogja elérni a SharePoint-tartalmat. Az ebben a lépésben bejelentkező felhasználó a bejelentkezett felhasználó lesz. Ha tehát olyan felhasználói fiókkal jelentkezik be, amely nem rendelkezik hozzáféréssel a dokumentumtárban található dokumentumhoz, akkor az indexelő nem fér hozzá a dokumentumhoz.

    Ha lehetséges, javasoljuk, hogy hozzon létre egy új felhasználói fiókot, és adja meg az új felhasználónak azokat a pontos engedélyeket, amelyeknek az indexelő számára szüksége van.

1.  Hagyja jóvá a kért engedélyeket.

    ![API-engedélyek jóváhagyása](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API-engedélyek jóváhagyása")

1.  Küldje el újra az indexelő létrehozására vonatkozó kérést. Ebben az esetben a kérésnek sikeresnek kell lennie. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>6. lépés: az indexelő állapotának keresése
Az indexelő létrehozása után a következő kéréssel tekintheti meg az indexelő állapotát.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Az indexelő állapotáról további információt itt talál: [Indexelő állapotának beolvasása](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Az adatforrás frissítése
Ha nincsenek frissítések az adatforrás-objektumhoz, az indexelő az ütemezett felhasználói beavatkozás nélkül is futtatható. Az Azure Cognitive Search adatforrás-objektum frissítésekor azonban minden alkalommal újra be kell jelentkeznie, hogy az indexelő fusson. Ha például megváltoztatja az adatforrás-lekérdezést, akkor újra be kell jelentkeznie a `https://microsoft.com/devicelogin` és egy új kód használatával.

Az adatforrás frissítése után kövesse az alábbi lépéseket:

1.  Az indexelő futtatásának manuális elindításához.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Az indexelő futtatására vonatkozó kéréssel kapcsolatos további információkért tekintse meg a következőt: [Run indexelő](https://docs.microsoft.com/rest/api/searchservice/run-indexer).

1.  Az indexelő állapotának megtekintése. Ha az utolsó indexelő futtatása során hiba történt `https://microsoft.com/devicelogin` , lépjen a lapra, és adja meg az új kódot. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Az indexelő állapotáról további információt itt talál: [Indexelő állapotának beolvasása](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

1.  Bejelentkezés

1.  Indítsa el manuálisan az indexelő futtatását, és győződjön meg róla, hogy az indexelő állapota. Ez alkalommal, amikor az indexelő futtatása sikeresen elindul.

## <a name="indexing-document-metadata"></a>Dokumentum metaadatainak indexelése
Ha úgy állította be az indexelő, hogy indexelje a dokumentum metaadatait, a következő metaadatok lesznek elérhetők az index számára.

> [!NOTE]
> Az előzetes verzió aktuális verziója nem tartalmazza az egyéni metaadatokat.

| Azonosító | Típus | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | A hely AZONOSÍTÓjának, a kódtár AZONOSÍTÓjának és az elem AZONOSÍTÓjának kombinációs kulcsa, amely egyedileg azonosít egy adott helyet a dokumentumtárban egy adott helyhez. |
| metadata_spo_site_id | Edm.String | A SharePoint Online-webhely azonosítója. |
| metadata_spo_library_id | Edm.String | A dokumentumtár azonosítója. |
| metadata_spo_item_id | Edm.String | A könyvtárban lévő (dokumentum) elemek azonosítója. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Az adott tétel utolsó módosításának dátuma/időpontja (UTC). |
| metadata_spo_item_name | Edm.String | Az elemek neve. |
| metadata_spo_item_size | Edm.Int64 | Az elemek mérete (bájtban). | 
| metadata_spo_item_content_type | Edm.String | Az tétel tartalomtípusa. | 
| metadata_spo_item_extension | Edm.String | Az tétel kiterjesztése. |
| metadata_spo_item_weburi | Edm.String | Az elemek URI-ja. |
| metadata_spo_item_path | Edm.String | A szülő elérési útjának és az elem nevének a kombinációja. | 

A SharePoint Online indexelő az egyes dokumentumtípusok által meghatározott metaadatokat is támogatja. További információ az [Azure Cognitive Searchban használt tartalom metaadatainak tulajdonságaiban](search-blob-metadata-properties.md)található.

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>A dokumentumok indexelésének szabályozása
Egyetlen SharePoint Online indexelő indexelheti a tartalmakat egy vagy több dokumentumtárakban. Az adatforrás létrehozásakor használja a *Container* paramétert, hogy jelezze az indexelni kívánt dokumentumtárakat. Az adatforrás *tárolójának* két tulajdonsága van: *név* és *lekérdezés*. 

### <a name="name"></a>Name
A *Name* tulajdonság megadása kötelező, és a három érték egyikének kell lennie:
+ *defaultSiteLibrary*
    + A helyek alapértelmezett dokumentumtárában lévő összes tartalom indexelése.
+   *allSiteLibraries*
    + Egy adott hely összes dokumentumtárában lévő összes tartalom indexelése. Ez nem indexeli a dokumentumtárakat egy alhelyről. Ezek a *lekérdezésekben* is megadhatók.
+   *useQuery*
    + Csak a *lekérdezésben* definiált tartalom indexelése.

### <a name="query"></a>Lekérdezés
A *lekérdezési* tulajdonság kulcsszó/érték párokból tevődik fel. Az alábbi kulcsszavakat használhatja. Az értékek vagy a webhely URL-címei vagy a dokumentumtár URL-címei.

> [!NOTE]
> Egy adott kulcsszó értékének lekéréséhez javasoljuk, hogy nyissa meg a SharePoint Online-t egy böngészőben, navigáljon ahhoz a dokumentumtárhoz, amelybe be kívánja vonni az URI-t, majd másolja azt a böngészőből. Ez a legegyszerűbb módszer arra, hogy az értéket a lekérdezésben szereplő kulcsszóval lehessen használni.

| Kulcsszó | Lekérdezés leírása | Példa |
| ------------- | -------------- | ----------- |
| null | Null vagy üres érték esetén az index az alapértelmezett dokumentumtár vagy az összes dokumentumtár, a tároló nevétől függően. | Az alapértelmezett hely könyvtárából származó összes tartalom indexelése: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | A kapcsolati karakterláncban a definiált hely összes könyvtárának tartalmát indexeli. Ezek a webhelye alhelyeire korlátozódnak <br><br> A kulcsszó *lekérdezési* értéke csak a hely vagy az alwebhely URI-ja lehet. | A mysite összes dokumentumtárában lévő összes tartalom indexelése. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | A könyvtár tartalmának indexelése. <br><br> A kulcsszó *lekérdezési* értékének a következő formátumok egyikében kell lennie: <br><br> 1. példa: <br><br> *includeLibrary = [webhely vagy alwebhely]/[dokumentum könyvtára]* <br><br> 2. példa <br><br> A böngészőből másolt URI. | A MyDocumentLibrary összes tartalomának indexelése: <br><br> 1. példa: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 2. példa <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Ne indexelje a tartalmat ebből a könyvtárból. <br><br> A kulcsszó *lekérdezési* értékének a következő formátumok egyikében kell lennie: <br><br> 1. példa: <br><br> *excludeLibrary = [webhely vagy Alhely URI-ja]/[dokumentum könyvtára]* <br><br> 2. példa <br><br> A böngészőből másolt URI. | Az összes könyvtár tartalmának indexelése a MyDocumentLibrary kivételével: <br><br> 1. példa: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 2. példa <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Index fájltípus szerint
Beállíthatja, hogy mely dokumentumok indexelve legyenek, és melyeket a rendszer kihagyja.

### <a name="include-documents-having-specific-file-extensions"></a>Adott fájlkiterjesztések tartalmazó dokumentumok belefoglalása
Az indexelő konfigurációs paraméterrel csak a megadott fájlnévkiterjesztéssel rendelkező dokumentumokat indexelheti `indexedFileNameExtensions` . Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például csak a érték indexeléséhez. PDF és. DOCX-dokumentumok:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Megadott fájlkiterjesztés-kiterjesztésű dokumentumok kizárása
A konfigurációs paraméter használatával kizárhat bizonyos fájlnévkiterjesztés-kiterjesztésű dokumentumokat az indexelésből `excludedFileNameExtensions` . Az érték egy olyan karakterlánc, amely a fájlkiterjesztés vesszővel tagolt listáját tartalmazza (vezető ponttal). Például az összes tartalom indexeléséhez, kivéve a következővel:. PNG és. JPEG-bővítmények:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Ha mindkettő `indexedFileNameExtensions` és `excludedFileNameExtensions` paraméter szerepel, az Azure Cognitive Search először a következőt tekinti meg: `indexedFileNameExtensions` `excludedFileNameExtensions` . Ez azt jelenti, hogy ha ugyanaz a fájlkiterjesztés szerepel mindkét listán, az indexelésből ki lesz zárva.

## <a name="handling-errors"></a>Hibák kezelése
Alapértelmezés szerint a SharePoint Online indexelő leáll, amint egy nem támogatott tartalomtípusú dokumentumot (például egy képet) észlel. Természetesen használhatja a `excludedFileNameExtensions` paramétert bizonyos tartalomtípusok kihagyása érdekében. Előfordulhat azonban, hogy a dokumentumokat a lehetséges tartalomtípusok előzetes ismerete nélkül kell indexelni. Ha nem támogatott tartalomtípust észlel, az indexelés folytatásához állítsa a `failOnUnsupportedContentType` konfigurációs paramétert hamis értékre:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Egyes dokumentumok esetében az Azure Cognitive Search nem tudja meghatározni a tartalomtípust, vagy nem tud feldolgozni egy egyébként támogatott tartalomtípusú dokumentumot. A hiba módjának figyelmen kívül hagyásához állítsa hamis értékre a `failOnUnprocessableDocument` konfigurációs paramétert:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Az Azure Cognitive Search korlátozza az indexelt dokumentumok méretét. Ezek a korlátok az [Azure Cognitive Search szolgáltatási korlátaiban](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)vannak dokumentálva. A túlméretezett dokumentumokat alapértelmezés szerint hibaként kezeli a rendszer. Ha azonban `indexStorageMetadataOnlyForOversizedDocuments` a konfigurációs paraméter igaz értékre van állítva, továbbra is indexelheti a túlméretezett dokumentumok tárolási metaadatait:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Folytathatja az indexelést is, ha a hibák bármilyen feldolgozási ponton történnek, vagy a dokumentumok elemzésekor vagy a dokumentumok indexbe való felvételekor. Ha egy adott számú hibát szeretne figyelmen kívül hagyni, állítsa a `maxFailedItems` és a `maxFailedItemsPerBatch` konfigurációs paramétereket a kívánt értékekre. Például:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Lásd még
+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Az Azure Cognitive Searchban használt tartalmi metaadatok tulajdonságai](search-blob-metadata-properties.md)
