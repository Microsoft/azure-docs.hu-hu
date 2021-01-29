---
title: Oktatóanyag – SCIM-végpont fejlesztése az Azure AD-ből származó alkalmazások felhasználói kiépítéséhez
description: A rendszer a tartományok közötti Identitáskezelés (SCIM) esetében szabványosítja a felhasználók automatikus kiépítési folyamatát. Ebből az oktatóanyagból megtudhatja, hogyan fejleszthet egy SCIM-végpontot, hogyan integrálhatja a SCIM API-t a Azure Active Directoryval, és megkezdheti a felhasználók és csoportok üzembe helyezését a felhőalapú alkalmazásokba.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6250aef5ec69c65dcdfe457a56d4e077b31af4d1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051668"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Oktatóanyag: SCIM-végpont létesítésének fejlesztése és tervezése

Alkalmazás-fejlesztőként használhatja a tartományok közötti Identitáskezelés (SCIM) felhasználói felügyeleti API-t, hogy lehetővé váljon a felhasználók és csoportok automatikus kiépítés az alkalmazás és az Azure AD között. Ez a cikk bemutatja, hogyan hozhat létre egy SCIM-végpontot, és hogyan integrálható az Azure AD kiépítési szolgáltatásával. A SCIM-specifikáció általános felhasználói sémát biztosít az üzembe helyezéshez. Az olyan összevonási szabványokkal együtt, mint az SAML vagy az OpenID Connect, a SCIM teljes körű, szabványokon alapuló megoldást biztosít a rendszergazdáknak a hozzáférés-kezeléshez.

A SCIM a két végpont szabványosított definíciója: egy `/Users` végpont és egy `/Groups` végpont. Általános REST-műveleteket használ az objektumok létrehozásához, frissítéséhez és törléséhez, valamint egy előre definiált sémát az általános attribútumok, például a csoportnév, a Felhasználónév, az utónév, a vezetéknév és az e-mailek számára. A SCIM 2,0 REST API-t kínáló alkalmazások csökkenthetik vagy megszüntethetik a saját felhasználói felügyeleti API-k használatával végzett munkát. A megfelelő SCIM-ügyfelek például megtudhatják, hogyan hozhat létre egy JSON-objektum HTTP-BEJEGYZÉSét a `/Users` végpontra új felhasználói bejegyzés létrehozásához. Ahelyett, hogy némileg eltérő API-ra lenne szüksége ugyanahhoz az alapszintű műveletekhez, a SCIM szabványnak megfelelő alkalmazások azonnal kihasználhatják a meglévő ügyfeleket, eszközöket és kódokat. 

![Kiépítés az Azure AD-ből egy SCIM-val rendelkező alkalmazásba](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

A SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) által meghatározott általános felhasználói objektum sémája és REST API-k lehetővé teszik az identitás-szolgáltatók és az alkalmazások könnyebb integrálását. Az SCIM-végpontot felépítő alkalmazás-fejlesztők az egyéni munka nélkül is integrálhatók bármely SCIM-kompatibilis ügyféllel.

Az alkalmazások kiépítésének automatizálásához SCIM-végpontokat kell létrehoznia és integrálnia az Azure AD SCIM-ügyféllel. A felhasználók és csoportok alkalmazásba való kiépítés elindításához hajtsa végre az alábbi lépéseket. 
    
  * **[1. lépés: a felhasználói és a csoport sémájának megtervezése.](#step-1-design-your-user-and-group-schema)** Azonosítsa az alkalmazás igényeinek megfelelő objektumokat és attribútumokat, és határozza meg, hogyan képezik le az Azure AD SCIM implementációja által támogatott felhasználói és csoportosítási sémákat.

  * **[2. lépés: az Azure AD SCIM implementációjának ismertetése.](#step-2-understand-the-azure-ad-scim-implementation)** Ismerje meg, hogyan valósítja meg az Azure AD SCIM-ügyfelet, és modellezheti a SCIM protokoll kérelmek kezelését és válaszait.

  * **[3. lépés: hozzon létre egy SCIM-végpontot.](#step-3-build-a-scim-endpoint)** A végpontnak 2,0-kompatibilisnek kell lennie az Azure AD kiépítési szolgáltatással való SCIM. Lehetőség van arra, hogy a Microsoft Common Language Infrastructure (CLI) kódtárait és a kód mintáit használja a végpont létrehozásához. Ezek a minták kizárólag referenciára és tesztelésre szolgálnak; azt javasoljuk, hogy az éles alkalmazással való függőséget ne használja.

  * **[4. lépés: a SCIM-végpont integrálása az Azure AD SCIM-ügyféllel.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Ha a szervezet olyan külső alkalmazást használ, amely megvalósítja az Azure AD által támogatott SCIM 2,0-profilt, akkor azonnal elkezdheti a felhasználók és csoportok kiépítésének és megszüntetésének automatizálását.

  * **[5. lépés: az alkalmazás közzététele az Azure AD Application Galleryben.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Megkönnyíti az ügyfelek számára az alkalmazás felderítését és a kiépítés egyszerű konfigurálását. 

![A SCIM-végpont Azure AD-vel való integrálásának lépései](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1. lépés: a felhasználó és a csoport sémájának megtervezése

Minden alkalmazáshoz különböző attribútumok szükségesek egy felhasználó vagy csoport létrehozásához. Az integráció megkezdéséhez azonosítsa az alkalmazás által igényelt objektumokat (felhasználókat, csoportokat) és attribútumokat (név, felettes, beosztás stb.). A SCIM standard definiál egy sémát a felhasználók és csoportok kezeléséhez. Az alapvető felhasználói sémához csak három attribútum szükséges: **azonosító** (szolgáltató által definiált azonosító), **externalId** (ügyfél által definiált azonosító) és **meta** (csak olvasható metaadatok a szolgáltató által karbantartva). Az összes többi attribútum nem kötelező. Az alapszintű felhasználói sémán kívül a SCIM standard definiál egy vállalati felhasználói bővítményt és egy modellt a felhasználói séma kibővítéséhez, hogy megfeleljen az alkalmazás igényeinek. Ha például az alkalmazás egy felhasználó felettesét igényli, a vállalati felhasználói sémával összegyűjtheti a felhasználó felettesét és a központi sémát a felhasználó e-mail-címének összegyűjtéséhez. A séma kialakításához kövesse az alábbi lépéseket:
  1. Az alkalmazás által igényelt attribútumok listázása. Hasznos lehet a követelményeknek a hitelesítéshez (pl. loginName és e-mailek) való bontása, a felhasználó életciklusának kezeléséhez szükséges attribútumok (pl. állapot/aktív) és az adott alkalmazás működéséhez szükséges egyéb attribútumok (például a kezelő, a címke).
  2. Győződjön meg arról, hogy ezek az attribútumok már definiálva vannak-e az alapszintű felhasználói sémában vagy a vállalati felhasználói sémában. Ha az alapszintű vagy a vállalati felhasználói sémák nem fedik le az összes szükséges attribútumot, akkor meg kell adnia egy bővítményt a felhasználói sémához, amely tartalmazza a szükséges attribútumokat. Az alábbi példában egy bővítményt adtunk hozzá a felhasználóhoz, amely lehetővé teszi a felhasználó számára a "címke" kiépítési lehetőséget. A legjobb, ha csak az alapszintű és a vállalati felhasználói sémákkal indul, és később további egyéni sémákat szeretne kibővíteni.  
  3. Képezze le a SCIM attribútumokat az Azure AD felhasználói attribútumaira. Ha a SCIM-végpontban definiált attribútumok egyike nem rendelkezik egyértelmű jogosultsággal az Azure AD felhasználói sémájában, akkor az adatai nem a legtöbb bérlőn található felhasználói objektumon vannak tárolva. Vegye figyelembe, hogy ez az attribútum nem kötelező-e a felhasználók létrehozásához. Ha az attribútum kritikus fontosságú az alkalmazás működéséhez, a bérlői rendszergazdát a séma kiterjesztéséhez, vagy a "címkék" tulajdonsághoz az alábbi ábrán látható módon használja.

### <a name="table-1-outline-the-attributes-that-you-need"></a>1. táblázat: a szükséges attribútumok körvonalazása 
| 1. lépés: az alkalmazás által igényelt attribútumok meghatározása| 2. lépés: az alkalmazásra vonatkozó követelmények leképezése a SCIM standard értékre| 3. lépés: SCIM-attribútumok leképezése az Azure AD-attribútumokra|
|--|--|--|
|loginName|userName (Felhasználónév)|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Vezetéknév|
|workMail|e-mailek [type EQ "work"]. Value|Mail|
|manager|manager|manager|
|címke|urn: IETF: params: scim: sémák: bővítmény: 2.0: CustomExtension: címke|extensionAttribute1|
|status|active|isSoftDeleted (nem a felhasználó által tárolt számított érték)|

A fent definiált séma az alábbi JSON-adattartalommal lesz ábrázolva. Vegye figyelembe, hogy az alkalmazáshoz szükséges attribútumok mellett a JSON-ábrázolás magában foglalja a szükséges `id` , `externalId` és `meta` attribútumokat is.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```

### <a name="table-2-default-user-attribute-mapping"></a>2. táblázat: alapértelmezett felhasználói attribútum leképezése
Ezután az alábbi táblázat segítségével megismerheti, hogy az alkalmazás által igényelt attribútumok hogyan képezhetők le az Azure AD-ben és a SCIM RFC-ben található attribútumokra. [Testreszabhatja](customize-application-attributes.md) az attribútumok leképezését az Azure ad és a scim végpontja között. Vegye figyelembe, hogy nem kell támogatnia a felhasználókat és a csoportokat, sem az összes alább látható attribútumot. Ezek arra utalnak, hogy az Azure AD-beli attribútumok gyakran a SCIM protokoll tulajdonságaira vannak leképezve. 

| Azure Active Directory felhasználó | "urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: user" |
| --- | --- |
| IsSoftDeleted |active |
|Részleg|urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|
| displayName |displayName |
|Alkalmazottkód|urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [type EQ "fax"]. Value |
| givenName |name.givenName |
| jobTitle |cím |
| Levelezés |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |címek [type EQ "work"]. irányítószám |
| proxy – címek |e-mailek [type EQ "other"]. Érték |
| fizikai kézbesítés – OfficeName |címek [type EQ "other"]. Formázott |
| streetAddress |címek [type EQ "work"]. streetAddress |
| surname |name.familyName |
| telefonszám |phoneNumbers[type eq "work"].value |
| felhasználó – egyszerű név |userName (Felhasználónév) |


### <a name="table-3-default-group-attribute-mapping"></a>3. táblázat: az alapértelmezett Group attribútum leképezése

| Azure Active Directory csoport | urn: IETF: params: scim: sémák: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| Levelezés |emails[type eq "work"].value |
| mailNickname |displayName |
| tagok |tagok |
| objectId |externalId |
| proxyAddresses |e-mailek [type EQ "other"]. Érték |

A SCIM RFC-ben több végpont is definiálva van. Megkezdheti a/User-végpont megkezdését, majd onnan kiterjesztheti. Az/schemas-végpont hasznos lehet egyéni attribútumok használatakor, vagy ha a séma gyakran változik. Lehetővé teszi, hogy az ügyfél automatikusan lekérje a legfrissebb sémát. A/bulk végpont különösen hasznos a csoportok támogatásakor. Az alábbi táblázat a SCIM standardban definiált különböző végpontokat ismerteti.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>4. táblázat: a fejleszteni kívánt végpontok meghatározása
|VÉGPONT|LEÍRÁS|
|--|--|
|/User|SZIFILISZ-műveletek végrehajtása felhasználói objektumon.|
|/Group|SZIFILISZ-műveletek végrehajtása egy csoport objektumon.|
|/ServiceProviderConfig|A SCIM szabvány által támogatott funkciók részleteit tartalmazza, például a támogatott erőforrásokat és a hitelesítési módszert.|
|/ResourceTypes|Az egyes erőforrásokra vonatkozó metaadatok megadása|
|/Schemas|Az egyes ügyfelek és szolgáltatók által támogatott attribútumok különbözőek lehetnek. Az egyik szolgáltató például, `name` `title` , és `emails` , míg egy másik szolgáltató a, a és a szolgáltatást használja `name` `title` `phoneNumbers` . A sémák végpont lehetővé teszi a támogatott attribútumok felderítését.|
|/Bulk|A tömeges műveletek lehetővé teszik, hogy az erőforrás-objektumok nagy gyűjteményében műveleteket hajtson végre egyetlen műveletben (például egy nagy csoport frissítési tagsága).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2. lépés: az Azure AD SCIM implementációjának ismertetése
> [!IMPORTANT]
> Az Azure AD-SCIM implementációjának viselkedését legutóbb 2018. december 18-án frissítették. További információ a változásokról: [SCIM 2,0 protokoll megfelelősége az Azure ad felhasználói kiépítési szolgáltatáshoz](application-provisioning-config-problem-scim-compatibility.md).

Ha olyan alkalmazást hoz létre, amely támogatja a SCIM 2,0 felhasználói Management API-t, ez a szakasz részletesen ismerteti az Azure AD SCIM-ügyfél megvalósításának módját. Azt is bemutatja, hogyan modellezheti a SCIM-kérelmek kezelését és válaszait. A SCIM-végpont implementálása után tesztelheti azt az előző szakaszban leírt eljárással.

Az [SCIM 2,0 protokoll specifikációja](http://www.simplecloud.info/#Specification)keretében az alkalmazásnak meg kell felelnie a következő követelményeknek:

* Támogatja a felhasználók létrehozását és opcionálisan csoportokat is, [az scim protokoll 3,3](https://tools.ietf.org/html/rfc7644#section-3.3). szakaszának megfelelően.  
* Támogatja a javítási kérelmekkel rendelkező felhasználók vagy csoportok módosítását [a scim protokoll 3.5.2. szakasza](https://tools.ietf.org/html/rfc7644#section-3.5.2)szerint. A támogatása biztosítja, hogy a csoportok és a felhasználók a megfelelő módon legyenek kiépítve. 
* A támogatja a korábban létrehozott felhasználók vagy csoportok ismert erőforrásának beolvasását, [a scim protokoll 3.4.1-es szakaszának](https://tools.ietf.org/html/rfc7644#section-3.4.1)megfelelően.  
* Támogatja a felhasználók vagy csoportok lekérdezését [a scim protokoll 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2). szakaszának megfelelően.  Alapértelmezés szerint a felhasználók lekérik a és a által `id` lekérdezett `username` felhasználókat `externalId` , és a csoportokat a által kérdezik le `displayName` .  
* Támogatja a felhasználó azonosító és kezelő általi lekérdezését az SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Támogatja a csoportok lekérdezését azonosító és tag szerint, a SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Támogatja a [excludedAttributes = tagok](#get-group) szűrőt a csoport erőforrásának LEKÉRDEZÉSEKOR a scim protokoll 3.4.2.5.
* Elfogad egyetlen tulajdonosi jogkivonatot az Azure AD hitelesítéséhez és engedélyezéséhez az alkalmazáshoz.
* Támogatja a felhasználó törlését `active=false` és a felhasználó visszaállítását `active=true` (a felhasználói objektumot egy kérelemben kell visszaadnia, függetlenül attól, hogy a felhasználó aktív-e). Az egyetlen alkalommal, amikor a felhasználót nem lehet visszaadni, ha az alkalmazásból nehezen törlődik. 

Az Azure AD-vel való kompatibilitás érdekében kövesse az alábbi általános irányelveket a SCIM-végpontok megvalósításához:

* `id` az összes erőforráshoz kötelező tulajdonság. Minden erőforrást visszaadó válasznak biztosítania kell, hogy minden erőforrás rendelkezik ezzel a tulajdonsággal, kivéve a `ListResponse` nulla taggal.
* A lekérdezési/szűrési kérelemre adott válasznak mindig a következőnek kell lennie: `ListResponse` .
* A csoportok nem kötelezőek, de csak akkor támogatottak, ha a SCIM implementációja támogatja a javítási kérelmeket.
* A javítás válaszában nem szükséges a teljes erőforrás belefoglalása.
* Microsoft Azure AD csak a következő operátorokat használja:  
    - `eq`
    - `and`
* Nincs szükség a kis-és nagybetűk megkülönböztetésére a SCIM szerkezeti elemein, különösen a javítás `op` műveleti értékein, ahogy az a-ben van meghatározva https://tools.ietf.org/html/rfc7644#section-3.5.2 . Az Azure AD az "op" értékeket bocsátja ki, `Add` `Replace` és `Remove` .
* Microsoft Azure AD egy véletlenszerűen kiválasztott felhasználó és csoport beolvasását kéri a végpont és a hitelesítő adatok érvényességének biztosításához. Emellett a [Azure Portal](https://portal.azure.com) **tesztelési kapcsolati** folyamatának részeként is végrehajtja. 
* Azt az attribútumot, amely alapján az erőforrásokat le lehet kérdezni, megfelelő attribútumként kell beállítani az alkalmazásban a [Azure Portalban](https://portal.azure.com). További információ: a [felhasználói kiépítési attribútumok társításának testreszabása](customize-application-attributes.md)
* HTTPS-támogatás a SCIM-végponton

### <a name="user-provisioning-and-deprovisioning"></a>Felhasználók kiépítése és megszüntetése

A következő ábra azokat az üzeneteket mutatja be, amelyeket Azure Active Directory küld egy SCIM szolgáltatásnak az alkalmazás identitás-tárolójában lévő felhasználó életciklusának kezeléséhez.  

![A felhasználó kiépítési és megszüntetési sorrendjének megjelenítése](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*A felhasználó kiépítése és megszüntetése*

### <a name="group-provisioning-and-deprovisioning"></a>Csoport kiépítése és megszüntetése

A csoport kiépítése és megszüntetése nem kötelező. A implementált és a engedélyezést követően az alábbi ábra azokat az üzeneteket mutatja be, amelyeket az Azure AD a SCIM szolgáltatásnak küld egy csoport életciklusának kezeléséhez az alkalmazás identitás-tárolójában.  Ezek az üzenetek két módon különböznek a felhasználók üzeneteitől:

* A csoportok beolvasására irányuló kérések azt határozzák meg, hogy a tagok attribútumot ki kell zárni a kérelemre válaszként megadott összes erőforrásból.  
* Azon kérések, amelyek alapján megállapítható, hogy egy hivatkozási attribútumnak van-e bizonyos értéke a Members attribútummal kapcsolatos kérelmek.  

![Megjeleníti a csoport kiépítésének és megszüntetésének folyamatát.](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Csoport kiépítése és megszüntetési folyamata*

### <a name="scim-protocol-requests-and-responses"></a>SCIM és válaszok
Ez a szakasz példákat tartalmaz az Azure AD SCIM-ügyfél által kibocsátott SCIM-kérelmekre, és példa erre a várt válaszokra. A legjobb eredmény érdekében az alkalmazásnak az ilyen formátumú kérelmek kezelésére és a várt válaszokat kell kibocsátania.

> [!IMPORTANT]
> Ha meg szeretné tudni, hogyan és mikor bocsátja ki az Azure AD-beli felhasználó-kiépítési szolgáltatást az alább ismertetett műveleteket, tekintse meg a [kiépítési](how-provisioning-works.md) [ciklusok: kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) kiépítés című szakaszt.

[Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user) ([kérelemre](#request)  /  [adott válasz](#response))
  - [Felhasználó beolvasása](#get-user) ([kérelem](#request-1)  /  [válasza](#response-1))
  - [Felhasználó lekérdezésének beolvasása](#get-user-by-query) (válasz[kérése](#request-2)  /  [](#response-2))
  - [Felhasználó lekérése lekérdezéssel – nulla eredmények](#get-user-by-query---zero-results) ([kérelem](#request-3)  /  [válasza](#response-3))
  - [Felhasználó frissítése [többértékű tulajdonságok]](#update-user-multi-valued-properties) ([kérelem](#request-4)  /  [válasza](#response-4))
  - [Felhasználó frissítése [egyértékű tulajdonságok]](#update-user-single-valued-properties) ([kérelem](#request-5)  /  [válasza](#response-5)) 
  - [Felhasználó letiltása](#disable-user) ([Válasz kérése](#request-14)  /  [](#response-14))
  - [Felhasználó törlése](#delete-user) ([kérelem](#request-6)  /  [válasza](#response-6))


[Csoportosítási műveletek](#group-operations)
  - [Csoport létrehozása](#create-group) ([Válasz kérése](#request-7)  /  [](#response-7))
  - [Csoport beolvasása](#get-group) (válasz[kérése](#request-8)  /  [](#response-8))
  - [Csoport beolvasása DisplayName alapján](#get-group-by-displayname) ([kérelem](#request-9)  /  [válasza](#response-9))
  - [Csoport frissítése [nem tag attribútumok]](#update-group-non-member-attributes) (válasz[kérése](#request-10)  /  [](#response-10))
  - [Frissítési csoport [Tagok hozzáadása]](#update-group-add-members) ([kérelem](#request-11)  /  [válasza](#response-11))
  - [Csoport frissítése [tagok eltávolítása]](#update-group-remove-members) ([kérelem](#request-12)  /  [válasza](#response-12))
  - [Csoport törlése](#delete-group) ([Válasz kérése](#request-13)  /  [](#response-13))

### <a name="user-operations"></a>Felhasználói műveletek

* A felhasználókat lekérdezéssel `userName` vagy attribútumokkal lehet lekérdezni `email[type eq "work"]` .  

#### <a name="create-user"></a>Felhasználó létrehozása

###### <a name="request"></a>Kérés

*/Users közzététele*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Reagálás

*HTTP/1.1 201 létrehozva*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Felhasználó beolvasása

###### <a name="request"></a><a name="request-1"></a>Kérés
*/Users/5d48a0a8e9f04aa38008 beolvasása* 

###### <a name="response-user-found"></a><a name="response-1"></a>Válasz (felhasználó található)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Kérés
*/Users/5171a35d82074e068ce2 beolvasása* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Válasz (a felhasználó nem található. Vegye figyelembe, hogy a részletek nem szükségesek, csak az állapot.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Felhasználó lekérése lekérdezés alapján

##### <a name="request"></a><a name="request-2"></a>Kérés

*/Users beolvasása? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Reagálás

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Felhasználó lekérése lekérdezéssel – nulla eredmények

##### <a name="request"></a><a name="request-3"></a>Kérés

*/Users beolvasása? Filter = userName EQ "nem létező felhasználó"*

##### <a name="response"></a><a name="response-3"></a>Reagálás

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Felhasználó frissítése [többszörös értékű tulajdonságok]

##### <a name="request"></a><a name="request-4"></a>Kérés

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Reagálás

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Felhasználó frissítése [egyértékű tulajdonságok]

##### <a name="request"></a><a name="request-5"></a>Kérés

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Reagálás

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Felhasználó letiltása

##### <a name="request"></a><a name="request-14"></a>Kérés

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Reagálás

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Felhasználó törlése

##### <a name="request"></a><a name="request-6"></a>Kérés

*/Users/5171a35d82074e068ce2 törlése HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Reagálás

*HTTP/1.1 204 nincs tartalom*

### <a name="group-operations"></a>Csoportosítási műveletek

* A csoportokat mindig üres tagok listájával kell létrehozni.
* A csoportokat az attribútum kérdezheti le `displayName` .
* Ha frissíteni szeretne a csoport-javítási kérelemre, egy *HTTP 204* -es verzióra van szükség a válaszban. Ha egy törzset ad vissza, az összes tag listája nem ajánlott.
* Nem szükséges a csoport összes tagjának visszaküldését támogatni.

#### <a name="create-group"></a>Csoport létrehozása

##### <a name="request"></a><a name="request-7"></a>Kérés

*/Groups közzététele HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Reagálás

*HTTP/1.1 201 létrehozva*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Csoport beolvasása

##### <a name="request"></a><a name="request-8"></a>Kérés

*/Groups/40734ae655284ad3abcc beolvasása? excludedAttributes = tagok HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Reagálás
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Csoport beolvasása displayName alapján

##### <a name="request"></a><a name="request-9"></a>Kérés
*/Groups beolvasása? excludedAttributes = tagok&Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Reagálás

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Csoport frissítése [nem tag attribútumok]

##### <a name="request"></a><a name="request-10"></a>Kérés

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Reagálás

*HTTP/1.1 204 nincs tartalom*

### <a name="update-group-add-members"></a>Csoport frissítése [Tagok hozzáadása]

##### <a name="request"></a><a name="request-11"></a>Kérés

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Reagálás

*HTTP/1.1 204 nincs tartalom*

#### <a name="update-group-remove-members"></a>Csoport frissítése [tagok eltávolítása]

##### <a name="request"></a><a name="request-12"></a>Kérés

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Reagálás

*HTTP/1.1 204 nincs tartalom*

#### <a name="delete-group"></a>Csoport törlése

##### <a name="request"></a><a name="request-13"></a>Kérés

*/Groups/cdb1ce18f65944079d37 törlése HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Reagálás

*HTTP/1.1 204 nincs tartalom*

### <a name="security-requirements"></a>Biztonsági követelmények
**TLS protokoll verziói**

Az egyetlen elfogadható TLS protokoll-verzió a TLS 1,2 és a TLS 1,3. A TLS más verziói nem engedélyezettek. Nem engedélyezett az SSL verziója. 
- Az RSA-kulcsoknak legalább 2 048 bitenek kell lenniük.
- Az ECC-kulcsoknak legalább 256 bitenek kell lenniük, jóváhagyott elliptikus görbe használatával létrehozva


**Kulcs hossza**

Az összes szolgáltatásnak a megfelelő hosszúságú titkosítási kulcsok használatával generált X. 509 tanúsítványokat kell használnia, ami azt jelenti, hogy:

**Titkosítási csomagok**

Minden szolgáltatást úgy kell konfigurálni, hogy a következő titkosítási csomagokat használja az alább megadott sorrendben. Vegye figyelembe, hogy ha csak RSA-tanúsítvánnyal rendelkezik, akkor a ECDSA titkosítási csomagok nem lépnek érvénybe. </br>

TLS 1,2 titkosítási csomagok minimális sávja:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-címtartományok
Az Azure AD kiépítési szolgáltatás jelenleg az [itt](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)felsorolt AzureActiveDirectory IP-címtartományok alatt működik. A AzureActiveDirectory címke alatt felsorolt IP-címtartományok hozzáadásával engedélyezheti az Azure AD kiépítési szolgáltatásból érkező adatforgalmat az alkalmazásba. Vegye figyelembe, hogy a számított címek esetében alaposan át kell tekintenie az IP-címtartomány listáját. A "40.126.25.32" címet az IP-címtartomány listában "40.126.0.0/18"-ként lehet megjeleníteni. Az IP-címtartomány listáját programozott módon is lekérheti az alábbi [API](/rest/api/virtualnetwork/servicetags/list)használatával.

## <a name="step-3-build-a-scim-endpoint"></a>3. lépés: SCIM-végpont létrehozása

Most, hogy megtervezte a sémát, és megértette az Azure AD SCIM megvalósítását, megkezdheti az SCIM-végpont fejlesztését. Ahelyett, hogy teljesen elkezdené, és a megvalósítást teljes mértékben saját maga is kiépítheti, a SCIM-Közösség által közzétett nyílt forráskódú SCIM-könyvtárakra támaszkodhat.

A példákat tartalmazó SCIM-végpontok létrehozásával kapcsolatos útmutatásért lásd: [Sample scim-végpont fejlesztése](use-scim-to-build-users-and-groups-endpoints.md).

Az Azure AD-kiépítési csapat által közzétett nyílt forráskódú .NET Core [hivatkozási kód példa](https://aka.ms/SCIMReferenceCode) egy ilyen erőforrás, amely elkezdheti a fejlesztést. Miután létrehozta a SCIM-végpontot, tesztelni kell. Használhatja a hivatkozási kód részeként megadott [Poster-tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gyűjteményét, vagy futtathatja a [fentebb](#user-operations)ismertetett minta kérelmeket/válaszokat.  

   > [!Note]
   > A hivatkozási kód célja, hogy segítséget nyújtson a SCIM-végpont létrehozásában, és "ahogy van". A közösségi hozzájárulások szívesen segítenek felépíteni és karbantartani a kódot.

A megoldás két projektből áll: a _Microsoft. scim_ és a _Microsoft. scim. WebHostSample_.

A _Microsoft. scim_ projekt az a könyvtár, amely meghatározza a webszolgáltatás azon összetevőit, amelyek megfelelnek a scim specifikációjának. Deklarálja a _Microsoft. scim. IProvider_ felületet, a kérelmeket a szolgáltató módszereire fordítja le, amelyek az Identity Store-ban való működésre lesznek programozva.

![Részletezés: A szolgáltató módszereire irányuló kérések lefordítása](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

A _Microsoft. scim. WebHostSample_ projekt egy Visual Studio ASP.net Core webalkalmazás, amely az _üres_ sablon alapján történik. Ez lehetővé teszi, hogy a mintakód önálló, tárolókban vagy Internet Information Services belül legyen üzembe helyezhető. Emellett a _Microsoft. scim. IProvider_ felületet is alkalmazza a memóriában lévő osztályok tárolására a minta identitás-tárolóként.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Egyéni SCIM-végpont létrehozása

A SCIM szolgáltatásnak rendelkeznie kell egy HTTP-címen és egy kiszolgálói hitelesítési tanúsítvánnyal, amelynek a legfelső szintű hitelesítésszolgáltatója a következő nevek egyike:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ X3

A .NET Core SDK tartalmaz egy HTTPS-fejlesztési tanúsítványt, amelyet a fejlesztés során használhat, a tanúsítványt a rendszer az első futtatási élmény részeként telepíti. Attól függően, hogy hogyan futtatja a ASP.NET Core webalkalmazást, egy másik portot fog hallgatni:

* Microsoft. SCIM. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

A HTTPS-vel kapcsolatos további információkért ASP.NET Core használja a következő hivatkozást: [https betartatása ASP.net Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Végpont hitelesítésének feldolgozása

A Azure Active Directorytól érkező kérések közé tartozik egy OAuth 2,0 tulajdonosi jogkivonat. A kérelmet fogadó bármely szolgáltatásnak hitelesítenie kell a kiállítót úgy, hogy Azure Active Directory a várt Azure Active Directory bérlőnek.

A jogkivonatban a kibocsátót egy ISS jogcím azonosítja, például: `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . Ebben a példában a jogcím értékének alapszintű címe, `https://sts.windows.net` amely a kiállítóként azonosítja Azure Active Directory, míg a relatív cím szegmens, a _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, annak a Azure Active Directory bérlőnek az egyedi azonosítója, amelyhez a tokent kiállították.

A jogkivonat célközönsége lesz az alkalmazás sablon-azonosítója a katalógusban, és az egyetlen bérlőben regisztrált összes alkalmazás ugyanazt a `iss` jogcímet fogadhatja a scim-kérelmekkel. Az összes egyéni alkalmazáshoz tartozó _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_-azonosító. Az Azure AD-létesítési szolgáltatás által generált jogkivonat csak tesztelésre használható. Éles környezetben nem használható.

A mintakód a kérelmeket a Microsoft. AspNetCore. Authentication. JwtBearer csomag használatával hitelesíti. A következő kód azt kényszeríti, hogy a szolgáltatás bármelyik végpontjának küldött kérések hitelesítése egy adott bérlő Azure Active Directory által kiállított tulajdonosi jogkivonatának használatával történik:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Tulajdonosi jogkivonatra is szükség van a megadott [Poster-tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) használatához, és helyi hibakeresést végezni a localhost használatával. A mintakód ASP.NET Core környezetekkel módosítja a hitelesítési beállításokat a fejlesztési fázisban, és engedélyezi a saját aláírású jogkivonat használatát.

ASP.NET Core több környezetéről a következő hivatkozás használható: több [környezet használata a ASP.net Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

A következő kód azt kényszeríti, hogy a szolgáltatás bármelyik végpontján érkező kérések hitelesítése egy egyéni kulccsal aláírt tulajdonosi jogkivonat használatával történik:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Küldjön egy GET kérelmet a jogkivonat-vezérlőnek, hogy érvényes tulajdonosi jogkivonatot kapjon, a _GenerateJSONWebToken_ metódus feladata a fejlesztéshez konfigurált paramétereknek megfelelő jogkivonat létrehozása:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>A felhasználók kiépítésének és megszüntetésének kezelési felépítése

***1. példa. A szolgáltatás lekérdezése a megfelelő felhasználóhoz** _

Azure Active Directory lekérdezi a szolgáltatást az `externalId` Azure ad-beli felhasználó mailNickname attribútumának megfelelő attribútumérték értékkel rendelkező felhasználó számára. A lekérdezés Hypertext Transfer Protocol (HTTP) kérelemként van kifejezve, mint például az a példa, amelyben a jyoung egy, a Azure Active Directory felhasználójának egy mailNickname-mintája.

>[!NOTE]
> Ez csak példa. Nem minden felhasználó rendelkezik mailNickname attribútummal, és a felhasználó értéke nem lehet egyedi a címtárban. Emellett a megfeleltetéshez használt attribútum (amely ebben az esetben a `externalId` ) az [Azure ad attribútum-hozzárendelésekben](customize-application-attributes.md)konfigurálható.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

A mintakód a kérést a szolgáltatás szolgáltatójának QueryAsync metódusára hívja le. Itt látható a metódus aláírása: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

A mintául szolgáló lekérdezésben az attribútumhoz megadott értékkel rendelkező felhasználó számára a `externalId` QueryAsync metódusnak átadott argumentumok értékei a következők:

paraméterek. AlternateFilters. Count: 1
* paraméterek. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* paraméterek. AlternateFilters. ElementAt (0). ÖsszehasonlítóOperátor: ÖsszehasonlítóOperátor. Equals
* paraméterek. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***2. példa. Felhasználó kiépítése** _

Ha egy olyan felhasználó számára, aki egy olyan attribútum értékkel rendelkezik `externalId` , amely megfelel a felhasználó mailNickname attribútumának, nem ad vissza felhasználót, Azure Active Directory kéri, hogy a szolgáltatás olyan felhasználót helyezzen üzembe, amely megfelel a Azure Active Directory.  Íme egy példa erre a kérelemre: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

A mintakód a kérést a szolgáltatás szolgáltatójának CreateAsync metódusára hívja le. Itt látható a metódus aláírása: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

A felhasználó kiépítésére vonatkozó kérelemben az erőforrás argumentum értéke a Microsoft. SCIM. Core2EnterpriseUser osztály egy példánya, amely a Microsoft. SCIM. schemas könyvtárban van definiálva.  Ha a felhasználó kiépítésére vonatkozó kérelem sikeres, akkor a metódus megvalósításának várhatóan a Microsoft. SCIM. Core2EnterpriseUser osztály egy példányát kell visszaadnia, az azonosító tulajdonság értéke pedig az újonnan kiosztott felhasználó egyedi azonosítójára van beállítva.  

_*_3. példa. Felhasználó aktuális állapotának lekérdezése_*_ 

Egy olyan felhasználó frissítéséhez, amely egy SCIM által ellátott identitás-tárolóban létezik, Azure Active Directory folytatja, ha a felhasználó aktuális állapotát kéri a szolgáltatástól a következő kéréssel: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

A mintakód a kérést a szolgáltatás szolgáltatójának RetrieveAsync metódusára hívja le. Itt látható a metódus aláírása: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

A felhasználó aktuális állapotának lekérésére irányuló kérelem példájában a paraméterek argumentum értékeként megadott objektum tulajdonságainak értékei a következők: 
  
_ Azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: paraméterek: scim: sémák: bővítmény: Enterprise: 2.0: user"

***4. példa. A frissítendő Reference attribútum értékének lekérdezése** _ 

Ha frissíteni kell egy hivatkozási attribútumot, akkor Azure Active Directory lekérdezi a szolgáltatást annak meghatározására, hogy a szolgáltatás által elindított, az identitás-tárolóban lévő Reference attribútum aktuális értéke már megegyezik-e az adott attribútum értékével Azure Active Directoryban. A felhasználók számára az egyetlen olyan attribútum, amelynek az aktuális értéke a felettes attribútum, így a jelenlegi érték lekérdezhető. Íme egy példa arra a kérelemre, amely meghatározza, hogy egy felhasználói objektum Manager-attribútuma jelenleg egy bizonyos értékkel rendelkezik-e: a mintakód a kérést a szolgáltatás szolgáltatójának QueryAsync metódusára hívja le. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következő: 
  
paraméterek. AlternateFilters. Count: 2
* paraméterek. AlternateFilters. ElementAt (x). AttributePath: "azonosító"
* paraméterek. AlternateFilters. ElementAt (x). ÖsszehasonlítóOperátor: ÖsszehasonlítóOperátor. Equals
* paraméterek. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* paraméterek. AlternateFilters. ElementAt (y). AttributePath: "Manager"
* paraméterek. AlternateFilters. ElementAt (y). ÖsszehasonlítóOperátor: ÖsszehasonlítóOperátor. Equals
* paraméterek. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* paraméterek. RequestedAttributePaths. ElementAt (0): "azonosító"
* paraméterek. SchemaIdentifier: "urn: IETF: paraméterek: scim: sémák: bővítmény: Enterprise: 2.0: user"

Itt az x index értéke 0 is lehet, az y index értéke pedig 1, vagy az x értéke 1, az y értéke pedig 0, a Filter lekérdezési paraméter kifejezésének sorrendjétől függően.   

***5. példa. Kérelem az Azure AD-ről egy SCIM-szolgáltatásra egy felhasználó frissítéséhez** 

Íme egy példa arra, hogy Azure Active Directory egy SCIM szolgáltatásra irányuló kérést egy felhasználó frissítéséhez: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

A mintakód a kérést a szolgáltatás szolgáltatójának UpdateAsync metódusára hívja le. Itt látható a metódus aláírása: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

A felhasználó frissítésére irányuló kérelem példájában a patch argumentum értékeként megadott objektum a következő tulajdonságértékeket tartalmazhatja: 
  
_ ResourceIdentifier. azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: user"
* (PatchRequest as PatchRequest2). Műveletek. darabszám: 1
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). OperationName: OperationName. Add
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Path. AttributePath: "Manager"
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Érték. darabszám: 1
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Hivatkozás: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Érték: 2819c223-7f76-453a-919d-413861904646

***6. példa. Felhasználó kiépítése** _

Az Azure AD egy olyan kérelmet küld, amely egy SCIM-szolgáltatás által elküldött identitás-tárolóból kiépíti a felhasználót, például a következőt:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

A mintakód a kérést a szolgáltatás szolgáltatójának DeleteAsync metódusára hívja le. Itt látható a metódus aláírása: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

A resourceIdentifier argumentum értékeként megadott objektum a következő tulajdonság értékeit írja be a felhasználó megszüntetésére irányuló kérelem példájában: 

_ ResourceIdentifier. azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: user"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4. lépés: a SCIM-végpont integrálása az Azure AD SCIM-ügyféllel

Az Azure AD beállítható úgy, hogy automatikusan kiépítse a hozzárendelt felhasználókat és csoportokat olyan alkalmazásokhoz, amelyek a [SCIM 2,0 protokoll](https://tools.ietf.org/html/rfc7644)egy adott profilját implementálják. A profil sajátosságait a [2. lépés: az Azure ad scim implementációjának ismertetése című témakör](#step-2-understand-the-azure-ad-scim-implementation)ismerteti.

Forduljon az alkalmazás-szolgáltatóhoz, vagy az alkalmazás szolgáltatójának dokumentációjában, hogy kompatibilisek legyenek ezekkel a követelményekkel.

> [!IMPORTANT]
> Az Azure AD SCIM implementációja az Azure AD-beli felhasználói kiépítési szolgáltatásra épül, amelynek célja, hogy folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és a szabványos műveletek nagyon specifikus készletét implementálja. Fontos megérteni ezeket a viselkedéseket az Azure AD SCIM-ügyfél működésének megismerése érdekében. További információ: a [kiépítés működésének](how-provisioning-works.md) [kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) szakasza.

### <a name="getting-started"></a>Első lépések

Az ebben a cikkben ismertetett SCIM-profilt támogató alkalmazások az Azure AD Application Gallery "nem katalógus alkalmazás" funkciójával csatlakozhatnak Azure Active Directoryhoz. A csatlakozást követően az Azure AD 40 percenként futtat szinkronizálási folyamatot, ahol lekérdezi az alkalmazás SCIM-végpontját a hozzárendelt felhasználókhoz és csoportokhoz, és a hozzárendelés részletei szerint létrehozza vagy módosítja azokat.

**SCIM támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). Vegye figyelembe, hogy a [fejlesztői programra](https://developer.microsoft.com/office/dev-program) való feliratkozással a P2-licenccel rendelkező Azure Active Directory ingyenes próbaverzióját érheti el
2. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
3. Válassza az **+ új alkalmazás**  >  **+ saját alkalmazás létrehozása** lehetőséget.
4. Adja meg az alkalmazás nevét, válassza a "*bármely más alkalmazás integrálása a* katalógusban" lehetőséget, majd válassza a **Hozzáadás** elemet az alkalmazás-objektum létrehozásához. A rendszer hozzáadja az új alkalmazást a vállalati alkalmazások listájához, és megnyílik az alkalmazás-felügyeleti képernyőjén.

   ![A képernyőképen az Azure AD Application Gallery ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure ad-alkalmazás* katalógusa látható

   > [!NOTE]
   > Ha a régi alkalmazás-katalógust használja, kövesse az alábbi képernyőn látható útmutatót.
   
   ![A képernyőképen az Azure AD Old App Gallery az ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Azure ad régi alkalmazás* -katalógusának élményét jeleníti meg

5. Az App Management képernyőn válassza a **kiépítés** lehetőséget a bal oldali panelen.
6. A **létesítési mód** menüben válassza az **automatikus** lehetőséget.

   ![Példa: az alkalmazás kiépítési lapja a Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *A kiépítés konfigurálása a Azure Portalban*

7. A **bérlői URL-cím** mezőben adja meg az alkalmazás scim-végpontjának URL-címét. Például: `https://api.contoso.com/scim/`
8. Ha az SCIM-végpont OAuth-tulajdonosi jogkivonatot igényel az Azure AD-től eltérő kibocsátótól, akkor másolja a szükséges OAuth-tulajdonosi tokent a nem kötelező **titkos jogkivonat** mezőbe. Ha ez a mező üresen marad, az Azure AD az Azure AD-ből kiállított OAuth-tulajdonosi jogkivonatot tartalmaz minden kéréssel. Az Azure AD-t identitás-szolgáltatóként használó alkalmazások ellenőrizhetik ezt az Azure AD-kiállított jogkivonatot. 
   > [!NOTE]
   > Ez **_nem_* ajánlott üresen hagyni ezt a mezőt, és az Azure ad által generált tokenre támaszkodhat. Ez a lehetőség elsősorban tesztelési célokra használható.
9. Válassza a _ *test kapcsolat* lehetőséget, hogy Azure Active Directory próbáljon csatlakozni az scim-végponthoz. Ha a kísérlet sikertelen, a rendszer hibaüzenetet jelenít meg.  

    > [!NOTE]
    > A SCIM a nem létező felhasználóhoz tartozó végpontot **kérdezi** le, véletlenszerű GUID azonosítóval, amely az Azure ad-konfigurációban kiválasztott egyező tulajdonság. A várt helyes válasz a HTTP 200 OK, amely üres SCIM ListResponse-üzenettel rendelkezik.

10. Ha az alkalmazáshoz való kapcsolódási kísérlet sikeres, kattintson a **Save (Mentés** ) gombra a rendszergazdai hitelesítő adatok mentéséhez.
11. A **leképezések** szakaszban két választható [attribútum-hozzárendelés](customize-application-attributes.md)közül választhat: egyet a felhasználói objektumokhoz és egyet a csoport objektumaihoz. Jelölje ki mindegyiket, hogy áttekintse a Azure Active Directoryról az alkalmazásba szinkronizált attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az alkalmazás felhasználóinak és csoportjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    > [!NOTE]
    > A csoportok leképezésének letiltásával letilthatja a csoport objektumainak szinkronizálását.

12. A **Beállítások** területen a **hatókör** mező határozza meg, hogy mely felhasználók és csoportok legyenek szinkronizálva. Jelölje be a **csak a hozzárendelt felhasználók** és csoportok szinkronizálása (javasolt) lehetőséget, hogy csak a **felhasználók és csoportok** lapon hozzárendelt felhasználókat és csoportokat szinkronizálja.
13. A konfiguráció befejezése után állítsa be a **kiépítési állapotot** **a** következőre:.
14. Válassza a **Mentés** lehetőséget az Azure ad-kiépítési szolgáltatás elindításához.
15. Ha csak a hozzárendelt felhasználókat és csoportokat szinkronizálja (ajánlott), akkor ügyeljen arra, hogy a **felhasználók és csoportok** lapot jelölje ki, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus elindítása után a bal oldali panelen kiválaszthatja a **kiépítési naplókat** a folyamat figyeléséhez, amely megjeleníti az alkalmazáson belüli kiépítési szolgáltatás által végrehajtott összes műveletet. Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](check-status-user-account-provisioning.md)helyezéséhez.

> [!NOTE]
> A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 40 percenként történik, amíg a szolgáltatás fut.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5. lépés: az alkalmazás közzététele az Azure AD Application Galleryben

Ha egynél több bérlő által használt alkalmazást készít, azt az Azure AD Application Galleryben teheti elérhetővé. Ez megkönnyíti a szervezetek számára az alkalmazás felderítését és a kiépítés konfigurálását. Az alkalmazás közzététele az Azure AD-katalógusban és a mások számára elérhető kiépítés egyszerűvé tétele. Tekintse meg a lépéseket [itt](../develop/v2-howto-app-gallery-listing.md). A Microsoft együttműködik Önnel, hogy integrálja az alkalmazást a katalógusba, tesztelje a végpontot, és bocsásson ki a használati [dokumentációt](../saas-apps/tutorial-list.md) az ügyfelek számára.

### <a name="gallery-onboarding-checklist"></a>Katalógus-előkészítési ellenőrzőlista
Kövesse az alábbi feladatlistát, és győződjön meg arról, hogy az alkalmazás gyors előkészítés alatt áll, és az ügyfelek zökkenőmentes üzembe helyezési tapasztalattal rendelkeznek. A katalógusba való bevezetéskor a rendszer összegyűjti az adatokat. 
> [!div class="checklist"]
> * [SCIM 2,0](#step-2-understand-the-azure-ad-scim-implementation) felhasználói és csoportos végpont támogatása (csak egy szükséges, de mindkettő ajánlott)
> * A bérlők által másodpercenként legalább 25 kérelem támogatása, hogy a felhasználók és csoportok kiosztása és megszüntetése késedelem nélkül történjen (kötelező)
> * Mérnöki és támogatási kapcsolattartók létrehozása útmutató az ügyfeleknek a katalógus bevezetéséhez (kötelező)
> * 3 nem lejáró teszt hitelesítő adatai az alkalmazáshoz (kötelező)
> * Támogassa a OAuth engedélyezési kód támogatását vagy az alább leírtak szerint a hosszú élettartamú tokent (kötelező)
> * Hozzon létre egy mérnöki és támogatási pontot az ügyfelek támogatásához a katalógus bevezetése után (kötelező)
> * Több csoporttagság frissítésének támogatása egyetlen JAVÍTÁSsal (ajánlott) 
> * A SCIM-végpont nyilvánosan történő dokumentálása (ajánlott) 
> * [Támogatási séma felderítése](https://tools.ietf.org/html/rfc7643#section-6) (ajánlott)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Engedélyezés a kiépítési összekötők számára az alkalmazás-katalógusban
A SCIM spec nem határoz meg SCIM-specifikus sémát a hitelesítéshez és az engedélyezéshez. A meglévő iparági szabványok használatára támaszkodik. Az Azure AD-kiépítési ügyfél két engedélyezési módszert támogat a katalógusban lévő alkalmazásokhoz. 

|Engedélyezési módszer|Előnyök|Hátrányok|Támogatás|
|--|--|--|--|
|Felhasználónév és jelszó (az Azure AD nem javasolja vagy támogatja)|Könnyen megvalósítható|Nem biztonságos – [a PA $ $Word nem számít](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|A Gallery-alkalmazások eseti alapon támogatottak. Nem Gallery-alkalmazások esetében nem támogatott.|
|Hosszú élettartamú tulajdonosi jogkivonat|A hosszú élettartamú tokenek nem igénylik a felhasználó jelenlétét. A rendszergazdák egyszerűen használhatók a kiépítés beállításakor.|A hosszú élettartamú tokenek nehezen oszthatók meg rendszergazdaként anélkül, hogy nem biztonságos módszereket, például e-maileket kellene használnia. |A katalógus és a nem Gallery-alkalmazások esetében támogatott. |
|OAuth engedélyezési kód engedélyezése|A hozzáférési tokenek sokkal rövidebbek, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amelyet a hosszú élettartamú tulajdonosi jogkivonatok nem rendelkeznek.  Egy valós felhasználónak jelen kell lennie a kezdeti engedélyezés során, és hozzá kell adnia egy szintű elszámoltathatóságot. |A felhasználónak jelen kell lennie. Ha a felhasználó elhagyja a szervezetet, a jogkivonat érvénytelen, és az engedélyezést újra el kell végezni.|A Gallery-alkalmazások esetében támogatott, de nem Gallery-alkalmazások. A felhasználói felületen azonban egy hozzáférési tokent is megadhat titkos jogkivonatként rövid távú tesztelési célokra. A nem katalógusban lévő OAuth-kód támogatásának támogatása a várakozó hitelesítési/jogkivonat-URL-címek támogatásán felül a Gallery alkalmazásban.|
|OAuth-ügyfél hitelesítő adatainak megadása|A hozzáférési tokenek sokkal rövidebbek, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amelyet a hosszú élettartamú tulajdonosi jogkivonatok nem rendelkeznek. Az engedélyezési kód és az ügyfél hitelesítő adatai is azonos típusú hozzáférési tokent hoznak létre, így a módszerek közötti váltás az API-ra is átlátható.  A kiépítés teljesen automatizált lehet, és az új tokenek felhasználói beavatkozás nélkül is csendesen kérhetők. ||Katalógus-és nem katalógus-alkalmazások esetén nem támogatott. A támogatás a várakozó fájlok között található.|

> [!NOTE]
> Nem ajánlott üresen hagyni a jogkivonat mezőt az Azure AD kiépítési konfiguráció egyéni alkalmazás felhasználói felületén. A generált jogkivonat elsődlegesen tesztelési célokra használható.

**OAuth engedélyezési kód engedélyezése:** A kiépítési szolgáltatás támogatja az [engedélyezési kód engedélyezését](https://tools.ietf.org/html/rfc6749#page-24). Miután elküldte az alkalmazást a katalógusban való közzétételre vonatkozó kérelmét, a csapat együttműködik Önnel a következő információk összegyűjtéséhez:
*  Engedélyezési URL-cím: az ügyfél URL-címe, amely a felhasználói ügynök átirányításával szerzi be az erőforrás-tulajdonostól az engedélyt. A rendszer átirányítja a felhasználót erre az URL-címre, hogy engedélyezze a hozzáférést. Vegye figyelembe, hogy ez az URL-cím jelenleg nem konfigurálható bérlőn.
*  Jogkivonat-Exchange URL-cím: az ügyfél URL-címe, amely egy hozzáférési jogkivonat engedélyezési engedélyének kiváltására használható, jellemzően az ügyfél-hitelesítéssel. Vegye figyelembe, hogy ez az URL-cím jelenleg nem konfigurálható bérlőn.
*  Ügyfél-azonosító: az engedélyezési kiszolgáló kiállítja a regisztrált ügyfelet az ügyfél-azonosítóval, amely egy egyedi karakterlánc, amely az ügyfél által megadott regisztrációs adatokat jelképezi.  Az ügyfél-azonosító nem titok; az erőforrás tulajdonosának van kitéve, és **nem** használható egyedül az ügyfél-hitelesítéshez.  
*  Ügyfél titka: az ügyfél titkos kulcsa az engedélyezési kiszolgáló által generált titok. Egyedi értéknek kell lennie, amely csak az engedélyezési kiszolgáló számára ismert. 

Vegye figyelembe, hogy az OAuth v1 nem támogatott az ügyfél titkos kulcsa miatt. A OAuth v2 támogatott.  

Ajánlott eljárások (javasolt, de nem kötelező):
* Több átirányítási URL-cím támogatása. A rendszergazdák a "portal.azure.com" és a "aad.portal.azure.com" típusból is konfigurálhatják az üzembe helyezést. Több átirányítási URL-cím támogatása biztosítja, hogy a felhasználók bármelyik portálról engedélyezzék a hozzáférést.
* Több titkot is támogat, hogy az állásidő nélkül zökkenőmentes legyen a titkos kulcs megújítása. 

A OAuth-kód engedélyezési folyamatának lépései:
1. A felhasználó bejelentkezik a Azure Portal > vállalati alkalmazások > válassza az alkalmazás > kiépítés > kattintson az engedélyezés elemre.
2. Azure Portal átirányítja a felhasználót az engedélyezési URL-címre (bejelentkezési oldal a harmadik féltől származó alkalmazáshoz).
3. A rendszergazda hitelesítő adatokat biztosít a harmadik féltől származó alkalmazás számára. 
4. A harmadik féltől származó alkalmazás visszairányítja a felhasználót a Azure Portalre, és megadja a kódot 
5. Az Azure AD-kiépítési szolgáltatások meghívja a jogkivonat URL-címét, és megadja a kód megadását. A harmadik féltől származó alkalmazás a hozzáférési jogkivonattal, a frissítési jogkivonattal és a lejárati dátummal válaszol.
6. A kiépítési ciklus megkezdésekor a szolgáltatás ellenőrzi, hogy az aktuális hozzáférési jogkivonat érvényes-e, és szükség esetén egy új jogkivonatra cseréli-e. A hozzáférési jogkivonatot az alkalmazásra vonatkozó minden kérelemben meg kell adni, és a kérelem érvényessége minden kérelem előtt be van jelölve.

> [!NOTE]
> Habár jelenleg nem lehet beállítani a OAuth a katalóguson kívüli alkalmazásban, manuálisan is létrehozhat egy hozzáférési jogkivonatot az engedélyezési kiszolgálóról, és beírhatja azt a nem katalógus alkalmazás titkos token mezőjébe. Ez lehetővé teszi a SCIM-kiszolgáló és az Azure AD SCIM-ügyfél kompatibilitásának ellenőrzését az alkalmazás-katalógusba való beléptetés előtt, amely támogatja a OAuth-kód engedélyezését.  

**Hosszú élettartamú OAuth tulajdonosi jogkivonatok:** Ha az alkalmazás nem támogatja a OAuth-engedélyezési kód engedélyezésének folyamatát, hosszú élettartamú OAuth tulajdonosi jogkivonatot is létrehozhat, mint amennyit a rendszergazda a kiépítési integráció beállításához használhat. A tokennek véglegesnek kell lennie, különben a kiépítési feladatot a jogkivonat lejárta után [karanténba](application-provisioning-quarantine-status.md) helyezi a rendszer.

További hitelesítési és engedélyezési módszerek esetén tudassa velünk a [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>A katalógus piacról indított indítási ellenőrzési listája
Ha segítségre van szüksége a közös integrációval kapcsolatos ismeretek és igények kielégítéséhez, javasoljuk, hogy frissítse a meglévő dokumentációját, és bővítse az integrációt a marketing-csatornákon.  Az alábbi ellenőrzőlista-tevékenységeket ajánljuk az indítás támogatásához.

* **Értékesítési és ügyféltámogatási készültség.** Gondoskodjon arról, hogy az értékesítési és támogatási csapatok tisztában legyenek, és beszéljenek az integrációs képességekről. Küldje el az értékesítési és támogatási csapatát, és adja meg a gyakori kérdéseket, és foglalja bele az értékesítési anyagokba való integrációt. 
* **Blogbejegyzés és/vagy sajtóközlemény.** Készítsen egy blogbejegyzést, vagy nyomja meg a kiadást, amely leírja a közös integrációt, az előnyöket és az első lépéseket. [Példa: inprivata és Azure Active Directory Press sajtóközlemény](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Közösségi média.** A közösségi média, például a Twitter, a Facebook vagy a LinkedIn segítségével népszerűsítheti ügyfelei integrációját. Ügyeljen arra, hogy @AzureAD a közzététel után is meg lehessen adni a bejegyzést. [Példa: inprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing webhely.** Hozza létre vagy frissítse a marketing-oldalakat (például integrációs oldal, partner oldal, díjszabási oldal stb.), hogy tartalmazza a közös integráció rendelkezésre állását. [Példa: Pingboard Integration Page](https://pingboard.com/org-chart-for), [Smartsheet Integration Page](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com díjszabása oldal](https://monday.com/pricing/) 
* **Technikai dokumentáció.** Hozzon létre egy Help Center-cikket vagy technikai dokumentációt arról, hogy az ügyfelek hogyan kezdhetik meg az első lépéseket. [Példa: megbízottat + Microsoft Azure Active Directory Integration.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Ügyfél-kommunikáció.** Az ügyfél-kommunikáció (havi hírlevél, e-mail-kampányok, termék-kibocsátási megjegyzések) révén riasztást készíthet az ügyfelektől az új integrációról. 

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Minta SCIM-végpont fejlesztése](use-scim-to-build-users-and-groups-endpoints.md)
* [A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások megszüntetése](user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
* [Kifejezések írása attribútum-hozzárendelésekhez](functions-for-customizing-application-data.md)
* [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
* [Fiók kiépítési értesítései](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)

