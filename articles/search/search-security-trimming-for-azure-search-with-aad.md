---
title: Az eredmények a Active Directory használatával történő levágására szolgáló biztonsági szűrők
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan valósíthat meg biztonsági jogosultságokat az Azure Cognitive Search keresési eredményeinek dokumentum szintjén, biztonsági szűrők és Azure Active Directory (AD) identitások használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629510"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Az Azure Cognitive Search eredményeinek Active Directory identitások használatával történő kivágására szolgáló biztonsági szűrők

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (AD) biztonsági identitások az Azure Cognitive Search szűrőinek használatával a keresési eredmények felhasználói csoporttagság alapján történő levágásához.

Ez a cikk a következő feladatokat mutatja be:
> [!div class="checklist"]
> - Azure AD-csoportok és-felhasználók létrehozása
> - A felhasználó hozzárendelése a létrehozott csoporthoz
> - Az új csoportok gyorsítótárazása
> - Dokumentumok indexelése társított csoportokkal
> - Keresési kérelem kibocsátása csoportazonosító szűrővel
> 
> [!NOTE]
> A cikkben szereplő kódrészletek a C# nyelven íródnak. A teljes forráskódot a [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started) webhelyén találja. 

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Searchban lévő indexnek rendelkeznie kell egy olyan [biztonsági mezővel](search-security-trimming-for-azure-search.md) , amely a dokumentum olvasási hozzáféréssel rendelkező csoport-identitások listáját tárolja. Ez a használati eset feltételezi, hogy egy-az-egyhez a biztonságos elemek (például az egyéni főiskolai alkalmazások) és egy biztonsági mező, amely meghatározza, hogy ki férhet hozzá az adott tételhez (a felvételi személyzethez).

A felhasználók, csoportok és társítások létrehozásához az útmutatóban szükséges Azure AD-rendszergazdai jogosultságokkal kell rendelkeznie. 

Az alkalmazást az Azure AD-ben is regisztrálni kell több-bérlős alkalmazásként az alábbi eljárásban leírtak szerint.

### <a name="register-your-application-with-azure-active-directory"></a>Az alkalmazás regisztrálása a Azure Active Directory

Ez a lépés integrálja az alkalmazást az Azure AD-vel a felhasználói és csoportfiókok bejelentkezésének elfogadásához. Ha Ön nem bérlői rendszergazda a szervezetben, előfordulhat, hogy [létre kell hoznia egy új bérlőt](../active-directory/develop/quickstart-create-new-tenant.md) az alábbi lépések végrehajtásához.

1. A [Azure Portalban](https://portal.azure.com)keresse meg az előfizetéséhez Azure Active Directory erőforrást.

1. A bal oldalon a **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.

1. Adja meg a regisztráció nevét, például egy nevet, amely hasonló a keresési alkalmazás nevéhez. Válassza a **Regisztráció** lehetőséget.

1. Az alkalmazás regisztrációjának létrehozása után másolja ki az alkalmazás AZONOSÍTÓját. Ezt a karakterláncot meg kell adnia az alkalmazás számára.

   Ha a [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)halad át, illessze be ezt az értéket a **app.config** fájlba.

   Ismételje meg a bérlő AZONOSÍTÓját.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Az alkalmazás azonosítója az Essentials szakaszban":::

1. A bal oldalon válassza az **API-engedélyek** lehetőséget, majd kattintson az **engedély hozzáadása** lehetőségre. 

1. Válassza a **Microsoft Graph** lehetőséget, majd válassza a **delegált engedélyek** lehetőséget.

1. Keresse meg, majd adja hozzá a következő delegált engedélyeket:

   - **Directory. ReadWrite. All**
   - **Group.ReadWrite.All**
   - **User. ReadWrite. All**

A Microsoft Graph olyan API-t biztosít, amely lehetővé teszi a programozott hozzáférést az Azure AD-hez egy REST APIon keresztül. Az útmutatóhoz tartozó mintakód a Microsoft Graph API-nak a csoportok, a felhasználók és a társítások létrehozásához szükséges engedélyeit használja. Az API-k a csoportok azonosítóinak gyorsítótárazására is használhatók a gyorsabb szűrés érdekében.

## <a name="create-users-and-groups"></a>Felhasználók és csoportok létrehozása

Ha a keresést egy létrehozott alkalmazáshoz adja hozzá, lehetséges, hogy már rendelkezik felhasználói és csoportazonosító az Azure AD-ben. Ebben az esetben kihagyhatja a következő három lépést. 

Ha azonban nem rendelkezik meglévő felhasználókkal, a rendszerbiztonsági tag létrehozásához Microsoft Graph API-kat használhat. A következő kódrészletek bemutatják, hogyan hozhatja ki az azonosítókat, amelyek az Azure Cognitive Search index biztonsági mezőjének adatértékei lesznek. A feltételezett főiskolai felvételi alkalmazásban ez a bevezetések személyzetének biztonsági azonosítója lenne.

A felhasználók és a csoportok tagsága nagyon sok folyadék lehet, különösen a nagyméretű szervezeteknél. A felhasználók és csoportok identitásait felépítő programkódnak elég gyakran kell futnia ahhoz, hogy felvegye a változtatásokat a szervezeti tagságban. Hasonlóképpen, az Azure Cognitive Search indexéhez hasonló frissítési ütemtervre van szükség, hogy tükrözze az engedélyezett felhasználók és erőforrások aktuális állapotát.

### <a name="step-1-create-group"></a>1. lépés: [csoport létrehozása](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>2. lépés: [felhasználó létrehozása](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>3. lépés: a felhasználó és a csoport hozzárendelése

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>4. lépés: a csoportok azonosítóinak gyorsítótárazása

Ha szeretné csökkenteni a hálózati késést, gyorsítótárba helyezheti a felhasználói csoport társításait, így a keresési kérelem kiadása után a rendszer visszaadja a csoportokat a gyorsítótárból, és egy oda-vissza menti az Azure AD-ba. Az [Azure ad batch API](/graph/json-batching) használatával egyetlen HTTP-kérést küldhet több felhasználóval, és felépítheti a gyorsítótárat.

A Microsoft Graph nagy mennyiségű kérelem kezelésére szolgál. Ha a rendszer több kérést is felvesz, Microsoft Graph a 429-as HTTP-állapotkód alapján meghiúsul a kérelem. További információ: [Microsoft Graph szabályozás](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Dokumentum indexelése az engedélyezett csoportokkal

Az Azure Cognitive Searchban végrehajtott lekérdezési műveletek Azure Cognitive Search-indexen keresztül hajthatók végre. Ebben a lépésben az indexelési művelet a kereshető adatmennyiséget egy indexbe importálja, beleértve a biztonsági szűrőként használt azonosítókat. 

Az Azure Cognitive Search nem hitelesíti a felhasználói identitásokat, illetve nem biztosít logikát annak meghatározásához, hogy egy felhasználó milyen tartalmat tekinthet meg. A biztonsági kivágás használati esete feltételezi, hogy Ön biztosítja a bizalmas dokumentum és a dokumentumhoz hozzáféréssel rendelkező csoportazonosító közötti társítást, és a rendszer érintetlenül importál egy keresési indexbe. 

A feltételezett példában egy Azure Cognitive Search indexbe helyezett PUT-kérelem törzse magában foglalja a pályázó főiskolai tanulmányát vagy átiratát, valamint a csoport azonosítóját, amely jogosult a tartalom megtekintésére. 

A jelen útmutatóban a kódban használt általános példában az index művelet a következőképpen nézhet ki:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Keresési kérelem kiadása

A biztonsági kivágási célokra az index biztonsági mezőjében szereplő értékek a keresési eredményekben található dokumentumok belefoglalásához vagy kizárásához használt statikus értékek. Ha például a (z) "A11B22C33D44-E55F66G77-H88I99JKK" típusú felvételekhez tartozó csoportazonosító, az Azure Cognitive Search indexben lévő minden olyan dokumentum szerepel (vagy kizárva), amely a kérelmezőnek visszaadott keresési eredmények között szerepel.

A kérést kiállító felhasználó csoportjai alapján a keresési eredményekben visszaadott dokumentumok szűréséhez tekintse át a következő lépéseket.

### <a name="step-1-retrieve-users-group-identifiers"></a>1. lépés: a felhasználó csoport-azonosítóinak beolvasása

Ha a felhasználó csoportjai még nem lettek gyorsítótárazva, vagy a gyorsítótár lejárt, adja ki a [csoportok](/graph/api/directoryobject-getmembergroups) kérését.

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>2. lépés: a keresési kérelem összeállítása

Feltéve, hogy a felhasználó csoportjának tagsága van, a keresési kérést a megfelelő szűrési értékekkel adhatja ki.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>3. lépés: az eredmények kezelése

A válasz magában foglalja a dokumentumok szűrt listáját, amely azokat a felhasználókat tartalmazza, amelyeknek a megtekintésére jogosult. A keresési eredmények oldalának létrehozási módjától függően előfordulhat, hogy a szűrt eredményhalmaz megjelenítéséhez vizuális kifejezéseket szeretne használni.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan lehet az Azure AD-bejelentkezéseket használni a dokumentumok Azure-beli Cognitive Search eredményeinek szűrésére, a kérésben megadott szűrőnek nem megfelelő dokumentumok eredményeinek a kivágásával. Egy olyan alternatív minta esetén, amely egyszerűbb lehet, vagy ha más biztonsági funkciókat szeretne áttekinteni, tekintse meg a következő hivatkozásokat.

- [Az eredmények kivágására szolgáló biztonsági szűrők](search-security-trimming-for-azure-search.md)
- [Biztonság az Azure Cognitive Search](search-security-overview.md)