---
title: Oktatóanyag – SCIM-végpont fejlesztése felhasználókiépítéshez az Azure AD-ból származó alkalmazásokhoz
description: System for Cross-domain Identity Management (SCIM) szabványosítja az automatikus felhasználóátépítést. Ebből az oktatóanyagból megtudhatja, hogyan fejleszthet SCIM-végpontot, hogyan integrálhatja az SCIM API-t az Azure Active Directory-val, és hogyan automatizálhatja a felhasználók és csoportok felhőalkalmazásba való kiépítését.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3d53c96c4b0306911b0c8a0b8576f35a73419db0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498152"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Oktatóanyag: SCIM-végpont kiépítésének fejlesztése és megtervezése

Alkalmazásfejlesztőként a System for Cross-Domain Identity Management (SCIM) felhasználókezelési API-val engedélyezheti a felhasználók és csoportok automatikus építését az alkalmazás és az Azure AD (AAD) között. Ez a cikk az SCIM-végpontok felépítését és az AAD kiépítési szolgáltatásba való integrálását ismerteti. Az SCIM-specifikáció egy közös felhasználói sémát biztosít a kiépítéshez. Az OLYAN összevonási szabványokkal együtt használva, mint az SAML vagy OpenID Connect, az SCIM végpontok között szabványalapú megoldást biztosít a rendszergazdáknak a hozzáférés-kezeléshez.

![Kiépítés Az Azure AD-ból egy alkalmazásba SCIM használatával](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Az SCIM két végpont szabványosított definíciója: egy `/Users` végpont és egy `/Groups` végpont. Általános REST-műveleteket használ az objektumok létrehozásához, frissítéséhez és törléséhez, valamint egy előre definiált sémát az olyan általános attribútumok számára, mint a csoport neve, a felhasználónév, a vezetéknév, a vezetéknév és az e-mail-cím. Az SCIM 2.0-t REST API alkalmazások csökkenthetik vagy kiküszöbölik a saját fejlesztésű felhasználókezelési API-val való munka nehezét. Például bármely megfelelő SCIM-ügyfél tudja, hogyan hozhat létre egy JSON-objektum HTTP POST-üzenetét a végponton egy új `/Users` felhasználói bejegyzés létrehozásához. Ahelyett, hogy némileg eltérő API-t kellene használni ugyanazon alapszintű műveletekhez, az SCIM szabványnak megfelelő alkalmazások azonnal kihasználhatják a már meglévő ügyfelek, eszközök és kódok előnyeit. 

Az SCIM 2.0-ban (RFC [7642, 7643](https://tools.ietf.org/html/rfc7642), [7644)](https://tools.ietf.org/html/rfc7644)meghatározott felügyelethez használt szabványos felhasználói objektumsémák és REST API-k lehetővé teszik az identitásszolgáltatók és az alkalmazások közötti integrációt. [](https://tools.ietf.org/html/rfc7643) Az SCIM-végpontot buildelő alkalmazásfejlesztők egyéni munka nélkül integrálhatók bármilyen SCIM-kompatibilis ügyféllel.

Az alkalmazáshoz való kiépítés automatizálásához SCIM-végpontot kell kiépítenünk és integrálni az Azure AD SCIM-ügyféllel. A következő lépésekkel kezdheti meg a felhasználók és csoportok üzembe lépéseit az alkalmazásban. 
    
1. A felhasználói és csoportséma megtervezése

   Az alkalmazás objektumainak és attribútumainak azonosítása annak meghatározásához, hogy azok hogyan vannak leképezve az AAD SCIM-implementáció által támogatott felhasználói és csoportsémára.

1. Az AAD SCIM implementációja

   Az SCIM-protokollkérések kezelésének és válaszait modellező AAD SCIM-ügyfél megvalósítása.

1. SCIM-végpont összeállítása

   A végpontnak SCIM 2.0-kompatibilisnek kell lennie az AAD kiépítési szolgáltatással való integrációhoz. A végpontot a Microsoft Közös nyelvi infrastruktúra (CLI) kódtárai és kódminái segítségével is létrehozhatja. Ezek a minták csak referenciaként és tesztelésre szolgálnak; Javasoljuk, hogy ne használja őket függőségként az éles alkalmazásban.

1. Az SCIM-végpont integrálása az AAD SCIM-ügyféllel 

   Ha a szervezet egy külső alkalmazással valósítja meg az AAD által támogatott SCIM 2.0-profilt, gyorsan automatizálhatja a felhasználók és csoportok kiépítését és megszüntetést.

1. Az alkalmazás közzététele az AAD-alkalmazásgyűjteményben 

   Az ügyfelek könnyedén felfedezhetik az alkalmazást, és könnyedén konfigurálják a kiépítést. 

![SCIM-végpont Azure AD-val való integrálásának lépései](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>A felhasználói és csoportséma megtervezése

Minden alkalmazás különböző attribútumokat igényel egy felhasználó vagy csoport létrehozásához. Kezdje az integrációt az alkalmazás számára szükséges objektumok (felhasználók, csoportok) és attribútumok (név, vezető, beosztás stb.) azonosításával. 

Az SCIM-szabvány meghatároz egy sémát a felhasználók és csoportok kezeléséhez. 

Az **alapvető felhasználói** séma csak három attribútumot igényel (az összes többi attribútum megadása nem kötelező):

- `id`, szolgáltató által definiált azonosító
- `userName`, a felhasználó egyedi azonosítója (általában az Azure AD egyszerű felhasználónevére van leképezve)
- `meta`, *a szolgáltató által* karbantartott írási metaadatok

Az alapvető felhasználói **séma** mellett az SCIM-szabvány egy vállalati felhasználói bővítményt is definiál, amely modellt tartalmaz a felhasználói séma az alkalmazás igényeinek megfelelő kibővítésére.  

Ha például az alkalmazás egy felhasználó e-mail-címét és a  felhasználó felettesét is igényli,  az alapsémával gyűjtse össze a felhasználó e-mail-címét és a vállalati felhasználói sémát a felhasználó felettesének összegyűjtéséhez.

A séma kialakításához kövesse az alábbi lépéseket:

1. Sorolja fel az alkalmazás által megkövetelt attribútumokat, majd kategorizálja a hitelesítéshez szükséges attribútumokat (például loginName és e-mail), a felhasználói életciklus kezeléséhez szükséges attribútumokat (például állapot/aktív), és az alkalmazáshoz szükséges összes egyéb attribútumot (például kezelő, címke).

1. Ellenőrizze, hogy az attribútumok már meg vannak-e határozva az **alapvető** felhasználói sémában vagy a **vállalati** felhasználói sémában. Ha nem, meg kell határoznia egy bővítményt a felhasználói sémához, amely lefedi a hiányzó attribútumokat. Az alábbi példában láthat egy bővítményt a felhasználó számára, amely lehetővé teszi a felhasználó üzembe való `tag` építését.

1. Az SCIM-attribútumokat leképezi az Azure AD felhasználói attribútumaira. Ha az SCIM-végpontban meghatározott egyik attribútumnak nincs egyértelmű megfelelője az Azure AD felhasználói sémában, akkor a bérlői rendszergazdának az alább látható módon kell kiterjesztenie a sémáját, vagy bővítményattribútumot kell használnia a `tags` tulajdonsághoz.

|Kötelező alkalmazásattribútum|Leképezett SCIM-attribútum|Leképezett Azure AD-attribútum|
|--|--|--|
|loginName (bejelentkezési név)|userName (Felhasználónév)|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Vezetéknév|
|munkahelyi e-mail-cím|emails[type eq "work"].value|Mail|
|manager|manager|manager|
|címke|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|active|isSoftDeleted (a számított érték nem a felhasználón van tárolva)|

**Példa a szükséges attribútumok listájára**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
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
**JSON-adatforgalom által definiált példaséma**

> [!NOTE]
> Az alkalmazáshoz szükséges attribútumok mellett a JSON-ábrázolás a szükséges `id` , `externalId` és `meta` attribútumokat is tartalmazza.

Segít az Azure AD-ben található alapértelmezett felhasználói attribútumok SCIM RFC-hez való leképezésében és kategorizálásában. Tekintse meg, hogyan vannak leképezve az attribútumok az Azure AD és az `/User` `/Group` [SCIM-végpont között.](customize-application-attributes.md)


| Azure Active Directory felhasználó | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted (Törölve) |active |
|Részleg|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId (alkalmazottazonosító)|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |cím |
| Levelezés |emails[type eq "work"].value |
| mailNickname (mailNickname) |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxycímek |emails[type eq "other"]. Érték |
| physical-Delivery-OfficeName |addresses[type eq "other"]. Formázott |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telefonszám |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName (Felhasználónév) |

**Példa a felhasználói és csoportattribútumok listájára**

| Azure Active Directory csoport | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| Levelezés |emails[type eq "work"].value |
| mailNickname |displayName |
| tagok |tagok |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"]. Érték |

**Példa csoportattribútumok listájára**

> [!NOTE]
> Nem kell támogatnia a felhasználókat és a csoportokat, illetve az itt látható összes attribútumot sem. Ez csupán hivatkozás arra, hogy az Azure AD attribútumai gyakran hogyan vannak leképezve az SCIM protokoll tulajdonságaira. 

Az SCIM RFC több végpontot is meghatároz. Kezdje a `/User` végponttal, majd bontsa ki onnan. 

|Végpont|Leírás|
|--|--|
|/User|CRUD-műveletek végrehajtása egy felhasználói objektumon.|
|/Group (Csoport)|CRUD-műveletek végrehajtása csoportobjektumon.|
|/Schemas|Az egyes ügyfelek és szolgáltatók által támogatott attribútumok eltérőek lehetnek. Az egyik szolgáltató a , a és a , míg egy másik `name` `title` szolgáltató , és `emails` `name` `title` `phoneNumbers` . A sémavégpont lehetővé teszi a támogatott attribútumok felderítését.|
|/Bulk|A tömeges műveletek lehetővé teszik, hogy egyetlen műveletben hajtsa végre az erőforrás-objektumok nagy gyűjteményének műveleteit (például egy nagy csoport frissítési tagságát).|
|/ServiceProviderConfig|Részletes információkat nyújt az SCIM-szabvány támogatott szolgáltatásairól, például a támogatott erőforrásokról és a hitelesítési módszerről.|
|/ResourceTypes|Az egyes erőforrások metaadatait határozza meg.|

**Példa végpontok listájára**

> [!NOTE]
> Használja a végpontot az egyéni attribútumok támogatásához, vagy ha a séma gyakran változik, mivel lehetővé teszi az ügyfél számára a legfrissebb séma automatikus `/Schemas` lekérését. Csoportok `/Bulk` támogatásához használja a végpontot.

## <a name="understand-the-aad-scim-implementation"></a>Az AAD SCIM implementációja

Az SCIM 2.0 felhasználókezelési API-k támogatásához ez a szakasz az AAD SCIM-ügyfél használatát ismerteti, és bemutatja, hogyan modellzhető az SCIM protokoll által nyújtott kérések kezelése és válaszai.

> [!IMPORTANT]
> Az Azure AD SCIM-implementáció viselkedése utoljára 2018. december 18-án frissült. A változásokkal kapcsolatos információkért lásd: Az Azure AD User [Provisioning Service SCIM 2.0](application-provisioning-config-problem-scim-compatibility.md)protokollnak való megfelelősége.

Az [SCIM 2.0 protokoll specifikáción](http://www.simplecloud.info/#Specification)belül az alkalmazásnak támogatnia kell a következő követelményeket:

|Követelmény|Referencia megjegyzések (SCIM protokoll)|
|-|-|
|Felhasználók és opcionális csoportok létrehozása|[3.3-as szakasz](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Felhasználók vagy csoportok módosítása PATCH-kérésekkel|[3.5.2. szakasz.](https://tools.ietf.org/html/rfc7644#section-3.5.2) A támogatás biztosítja, hogy a csoportok és a felhasználók megfelelő módon legyen kiépítve.|
|Korábban létrehozott felhasználó vagy csoport ismert erőforrásának lekérése|[3.4.1 szakasz](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Felhasználók vagy csoportok lekérdezése|[3.4.2. szakasz.](https://tools.ietf.org/html/rfc7644#section-3.4.2)  Alapértelmezés szerint a és a lekéri a felhasználókat, és lekérdezi őket, a csoportokat pedig `id` `username` a `externalId` `displayName` lekérdezése.|
|Felhasználó lekérdezése azonosító és felettes szerint|3.4.2-es szakasz|
|Csoportok lekérdezése azonosító és tag szerint|3.4.2-es szakasz|
|A [excludedAttributes=members szűrő](#get-group) a csoporterőforrás lekérdezésekor|3.4.2.5 szakasz|
|Az AAD hitelesítéséhez és az alkalmazáshoz való hitelesítéséhez fogadja el a megfelelő jogkivonatot.||
|Felhasználó helyreállított törlése és `active=false` a felhasználó visszaállítása `active=true`|A felhasználói objektumot egy kérésben kell visszaadni, függetlenül attól, hogy a felhasználó aktív-e. A felhasználót csak akkor ne szabad visszaadni, ha az alkalmazásból nehéz törölni.|
|A /Schemas végpont támogatása|[7. szakasz](https://tools.ietf.org/html/rfc7643#page-30) A rendszer a sémafelderítési végpont használatával további attribútumokat derít fel.|

SciM-végpontok megvalósításakor használja az általános irányelveket az AAD-kompatibilitás biztosításához:

* `id` A minden erőforráshoz kötelező tulajdonság. Minden erőforrást visszaküldő válasznak biztosítania kell, hogy minden erőforrás rendelkezik ezzel a tulajdonsággal, kivéve `ListResponse` a nulla taggal.
* A lekérdezési/szűrőkérésre adott válasznak mindig egynek kell `ListResponse` lennie.
* A csoportok nem kötelezőek, de csak  akkor támogatottak, ha az SCIM-implementáció támogatja a PATCH-kéréseket.
* A PATCH-válaszban nem szükséges a teljes **erőforrást is** szerepeltetni.
* A Microsoft AAD csak a következő operátorokat használja: `eq` , `and`
* Ne igényelj megkülönbözteti a kis- és nagybetűket az SCIM szerkezeti elemein, különösen a **PATCH** műveleti értékeken `op` a [3.5.2 szakaszban meghatározottak szerint.](https://tools.ietf.org/html/rfc7644#section-3.5.2) Az AAD a következő értékeket bocsátja `op` ki: Add ( **Hozzáadás)**, **Replace (Csere)** és **Remove (Eltávolítás).**
* A Microsoft AAD egy véletlenszerű felhasználó és csoport lekérését kéri le, hogy a végpont és a hitelesítő adatok érvényesek-e. Ez a Kapcsolat tesztelése folyamat  részeként is meg [Azure Portal.](https://portal.azure.com) 
* Az erőforrások lekérdezhető attribútumát megfelelő attribútumként kell beállítani a Azure Portal alkalmazásban, lásd: A felhasználóátépítési [attribútumleképezések](https://portal.azure.com) [testreszabása.](customize-application-attributes.md)
* A jogosultságok attribútum nem támogatott.
* HTTPS támogatása az SCIM-végponton.
* [Sémafelderítés](#schema-discovery)
  * Az egyéni alkalmazás jelenleg nem támogatja a sémafelderítést, de bizonyos katalógusalkalmazások használják. A 2012-hez kapcsolódó további attribútumok összekötőkhöz való hozzáadásának elsődleges módszere a sémafelderítés lesz. 
  * Ha nincs érték, ne küldjön null értékeket.
  * A tulajdonságértékeket kis- és kis-, illetve kis- és nagy karakterekkel kell összeírni (például readWrite).
  * Listaválaszt kell visszaadni.
  
### <a name="user-provisioning-and-deprovisioning"></a>Felhasználóátépítés és -megszüntetés

Az alábbi ábra azokat az üzeneteket mutatja be, amelyek az AAD-nek az SCIM-szolgáltatásnak küldve kezelik a felhasználók életciklusát az alkalmazás identitástárolójában.  

![Megjeleníti a felhasználóátépítési és -megszüntetés sorrendjét](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Felhasználóátépítési és -megszüntetés sorrendje*

### <a name="group-provisioning-and-deprovisioning"></a>Csoport kiépítése és megszüntetése

A csoport kiépítése és megszüntetése nem kötelező. Az alábbi ábrán az AAD által az SCIM-szolgáltatásnak küldött üzenetek láthatóak az alkalmazás identitástárolójában lévő csoportok életciklusának kezeléséhez. Ezek az üzenetek kétféleképpen különböznek a felhasználókkal kapcsolatos üzenetektől:

* A csoportok lekérésének kérése határozza meg, hogy a members attribútumot ki kell zárni a kérésre adott bármely erőforrásból.  
* A tagok attribútummal kapcsolatos kérések, amelyek azt határozzák meg, hogy egy referenciaattribútum rendelkezik-e bizonyos értékkel.  

![A csoportátépítési és -megszüntetés sorrendjének mutatása](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Csoport kiépítési és megszüntetés sorozata*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-protokollkérések és -válaszok
Ez a szakasz példa az AAD SCIM-ügyfél által kibocsátott SCIM-kérésekre, valamint a várt válaszokra. A legjobb eredmény érdekében úgy kódolja az alkalmazást, hogy ilyen formátumban kezelje ezeket a kéréseket, és a várt válaszokat bocsátja ki.

> [!IMPORTANT]
> Annak végrehajtásához, hogy az AAD-felhasználóátépítési szolgáltatás hogyan és mikor bocsátja ki az alább leírt műveleteket, tekintse meg a Kiépítési [ciklusok:](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) Kezdeti és növekményes szakaszt a [Kiépítés működése](how-provisioning-works.md)című szakaszban.

[Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user) ([Kérésre adott](#request)  /  [válasz](#response))
  - [Felhasználó lekérése](#get-user) ([](#request-1)  /  [Kérésre adott válasz](#response-1))
  - [Felhasználó lekérése lekérdezés alapján](#get-user-by-query) ([Kérésre adott](#request-2)  /  [válasz](#response-2))
  - [Felhasználó lekérése lekérdezés alapján – Nulla eredmény](#get-user-by-query---zero-results) ([Kérésre adott](#request-3)  /  [válasz](#response-3))
  - [Felhasználó frissítése [Többértékű tulajdonságok]](#update-user-multi-valued-properties) ([Kérésre adott](#request-4)  /  [válasz](#response-4))
  - [Felhasználó frissítése [Egyértékes tulajdonságok]](#update-user-single-valued-properties) ([Kérésre adott](#request-5)  /  [válasz](#response-5)) 
  - [Felhasználó letiltása](#disable-user) ([](#request-14)  /  [Kérésre adott válasz](#response-14))
  - [Felhasználó törlése](#delete-user) ([](#request-6)  /  [Kérésre adott válasz](#response-6))

[Csoportműveletek](#group-operations)
  - [Csoport létrehozása](#create-group) ([Kérésre adott](#request-7)  /  [válasz](#response-7))
  - [Csoport lekérése](#get-group) ([](#request-8)  /  [Kérésre adott válasz](#response-8))
  - [Csoport lekérése displayName](#get-group-by-displayname) [(kérésre adott](#request-9)  /  [válasz)](#response-9)alapján
  - [Csoport frissítése [Nem tag attribútumok]](#update-group-non-member-attributes) ([Kérésre adott](#request-10)  /  [válasz](#response-10))
  - [Csoport frissítése [Tagok hozzáadása]](#update-group-add-members) ([Kérésre adott](#request-11)  /  [válasz](#response-11))
  - [Csoport frissítése [Tagok eltávolítása]](#update-group-remove-members) ([Kérésre adott](#request-12)  /  [válasz](#response-12))
  - [Csoport törlése](#delete-group) ([Kérésre adott](#request-13)  /  [válasz](#response-13))

[Sémafelderítés](#schema-discovery)
  - [Séma felderítése](#discover-schema) ([](#request-15)  /  [Kérésre adott válasz](#response-15))

### <a name="user-operations"></a>Felhasználói műveletek

* A felhasználók lekérdezhetőek vagy `userName` `email[type eq "work"]` attribútumokkal.  

#### <a name="create-user"></a>Felhasználó létrehozása

##### <a name="request"></a>Kérés

*POST /Users*
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
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Válasz (a felhasználó megtalálta)
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
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Válasz (a felhasználó nem található. Vegye figyelembe, hogy a részletekre nincs szükség, csak az állapotra.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Felhasználó lekérdezve lekérdezés alapján

##### <a name="request"></a><a name="request-2"></a>Kérés

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

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

#### <a name="get-user-by-query---zero-results"></a>Felhasználó lekérdezése lekérdezés alapján – Nulla eredmény

##### <a name="request"></a><a name="request-3"></a>Kérés

*GET /Users?filter=userName eq "non-existent user"*

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

#### <a name="update-user-multi-valued-properties"></a>Felhasználó frissítése [Többértékű tulajdonságok]

##### <a name="request"></a><a name="request-4"></a>Kérés

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
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

#### <a name="update-user-single-valued-properties"></a>Felhasználó frissítése [Egyértékes tulajdonságok]

##### <a name="request"></a><a name="request-5"></a>Kérés

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Reagálás

*HTTP/1.1 204 Nincs tartalom*

### <a name="group-operations"></a>Csoportműveletek

* A csoportokat mindig üres tagok listájával kell létrehozni.
* A csoportok a attribútum alapján `displayName` kérdezhetőek le.
* A csoportos PATCH-kérés frissítése http *204 No Content (HTTP 204 Nincs tartalom)* választ eredményez. A törzs visszaadása az összes tag listájával nem ajánlott.
* Nem szükséges támogatni a csoport összes tagjának visszaküldését.

#### <a name="create-group"></a>Csoport létrehozása

##### <a name="request"></a><a name="request-7"></a>Kérés

*POST /Groups HTTP/1.1*
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

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

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

#### <a name="get-group-by-displayname"></a>Csoport lekérte a displayName alapján

##### <a name="request"></a><a name="request-9"></a>Kérés
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

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

#### <a name="update-group-non-member-attributes"></a>Csoport frissítése [Nem tag attribútumok]

##### <a name="request"></a><a name="request-10"></a>Kérés

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

*HTTP/1.1 204 Nincs tartalom*

### <a name="update-group-add-members"></a>Csoport frissítése [Tagok hozzáadása]

##### <a name="request"></a><a name="request-11"></a>Kérés

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Nincs tartalom*

#### <a name="update-group-remove-members"></a>Csoport frissítése [Tagok eltávolítása]

##### <a name="request"></a><a name="request-12"></a>Kérés

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Nincs tartalom*

#### <a name="delete-group"></a>Csoport törlése

##### <a name="request"></a><a name="request-13"></a>Kérés

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Reagálás

*HTTP/1.1 204 Nincs tartalom*

### <a name="schema-discovery"></a>Sémafelderítés
#### <a name="discover-schema"></a>Séma felderítése

##### <a name="request"></a><a name="request-15"></a>Kérés
*GET /Schemas* 
##### <a name="response"></a><a name="response-15"></a>Reagálás
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Biztonsági követelmények
**TLS protokollverziók**

Az egyetlen elfogadható TLS-protokollverzió a TLS 1.2 és a TLS 1.3. A TLS más verziói nem engedélyezettek. Az SSL egyik verziója sem engedélyezett. 
- Az RSA-kulcsoknak legalább 2048 bitesnek kell lennie.
- Az ECC-kulcsoknak legalább 256 bitesnek kell lennie, és egy jóváhagyott három ponttal kell generálni

**Kulcshosszok**

Minden szolgáltatásnak megfelelő hosszúságú titkosítási kulcsokkal létrehozott X.509-tanúsítványokat kell használnia, ami a következőt jelenti:

**Titkosítási csomagok**

Minden szolgáltatást úgy kell konfigurálni, hogy az alábbi titkosítási csomagokat használja az alább megadott sorrendben. Vegye figyelembe, hogy ha csak RSA-tanúsítvánnyal rendelkezik, a telepített ECDSA-titkosítási csomagoknak nincs hatása. </br>

TLS 1.2 titkosítási csomagok minimális sávja:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-tartományok
Az Azure AD kiépítési szolgáltatás jelenleg az AzureActiveDirectory IP-tartományai alatt működik az itt [felsoroltak szerint.](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) Az AzureActiveDirectory címke alatt felsorolt IP-címtartományok felvételével engedélyezheti az Azure AD kiépítési szolgáltatásból származó forgalmat az alkalmazásba. Vegye figyelembe, hogy a kiszámított címekhez gondosan át kell vizsgálnia az IP-címtartományok listáját. A "40.126.25.32" cím az IP-címtartományok listájában a "40.126.0.0/18" értékként ábrázolható. Az IP-címtartományok listáját programozott módon is lekérheti a következő [API-val.](/rest/api/virtualnetwork/servicetags/list)

## <a name="build-a-scim-endpoint"></a>SCIM-végpont összeállítása

Most, hogy megtervelte a sémát, és megértette az Azure AD SCIM implementációját, elkezdheti az SCIM-végpont fejlesztését. Ahelyett, hogy teljesen újat kezd, és teljesen egyedül építi fel a megvalósítást, az SCIM-közösség által közzétett számos nyílt forráskódú SCIM-kódtárra támaszkodhat.

Példákat tartalmazó SCIM-végpontok fejlesztésére vonatkozó útmutatásért [lásd: SciM-mintavégpont fejlesztése.](use-scim-to-build-users-and-groups-endpoints.md)

Az Azure AD kiépítési csapata által közzétett nyílt forráskódú .NET [Core](https://aka.ms/SCIMReferenceCode) referenciakód-példa egy olyan erőforrás, amely segíthet a fejlesztésben. Miután felépítette az SCIM-végpontot, tesztelni kell. Használhatja a referenciakód részeként megadott [postman-tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gyűjteményét, vagy futtathatja a fent megadott mintakéréseket/válaszokat. [](#user-operations)  

   > [!Note]
   > A referenciakód célja, hogy segítséget nyújtson az SCIM-végpontok építésének elkezdésében, és az "AS IS" (ADOTT KÉNT) rendelkezésre áll. A közösség közreműködése segíthet a kód felépítésében és karbantartásában.

A megoldás két projektből áll: _Microsoft.SCIM_ és _Microsoft.SCIM.WebHostSample._

A _Microsoft.SCIM-projekt_ az a kódtár, amely meghatározza a webszolgáltatás az SCIM-specifikációnak megfelelő összetevőit. Deklarálja a _Microsoft.SCIM.IProvider_ felületet, a kérelmeket a rendszer a szolgáltató metódusaira való hívásokra fordítja le, amelyek az identitástáron való működésre lesznek programozva.

![Lebontás: A szolgáltató metódusaira vonatkozó hívásokra lefordított kérés](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

A _Microsoft.SCIM.WebHostSample_ projekt egy Visual Studio ASP.NET Core-webalkalmazás, amely az _Üres sablonon_ alapul. Ez lehetővé teszi, hogy a mintakód önálló, tárolókban vagy tárolókon belül üzemelő Internet Information Services. Emellett megvalósítja a _Microsoft.SCIM.IProvider_ felületet is, amely mintaidentitás-tárolóként tartja meg az osztályokat a memóriában.

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

Az SCIM-szolgáltatásnak olyan HTTP-címmel és kiszolgálóhitelesítési tanúsítvánnyal kell lennie, amelynek legfelső szintű hitelesítésszolgáltatója a következő nevek egyike:

* Cnnic
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* Globalsign
* Go Foga
* Verisign
* WoSign (Aláírás)
* DST legfelső szintű hitelesítésszolgáltató X3

A .NET Core SDK tartalmaz egy HTTPS fejlesztési tanúsítványt, amely a fejlesztés során használható, és a tanúsítvány az első futtatás részeként van telepítve. Attól függően, hogy hogyan futtatja ASP.NET Core-webalkalmazást, az egy másik portot fog figyelni:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

A HTTPS-ről a ASP.NET a következő hivatkozáson keresztül érhető el: HTTPS kényszerítés [a ASP.NET Core-ban](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Végponthitelesítés kezelése

A Azure Active Directory OAuth 2.0-ás bearer jogkivonatot tartalmaznak. A kérést fogadó összes szolgáltatásnak hitelesítenie kell a kiállítót Azure Active Directory a várt Azure Active Directory bérlőhöz.

A jogkivonatban a kiállítót egy iss jogcím azonosítja, például `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . Ebben a példában a jogcímérték alapcíme kiállítóként azonosítja az Azure Active Directory-t, míg a relatív címszegmens, a `https://sts.windows.net` _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ a jogkivonatot kibocsátó Azure Active Directory-bérlő egyedi azonosítója.

A jogkivonat célközönsége a katalógusban lévő alkalmazás alkalmazássablon-azonosítója lesz, az egyetlen bérlőben regisztrált alkalmazások pedig SCIM-kérésekkel kaphatják meg ugyanazt a `iss` jogcímet. Az összes egyéni alkalmazás alkalmazássablon-azonosítója: _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. Az Azure AD kiépítési szolgáltatás által létrehozott jogkivonatot csak tesztelésre szabad használni. Éles környezetben nem használható.

A mintakódban a kérelmek hitelesítése a Microsoft.AspNetCore.Authentication.JwtBearer csomaggal történik. A következő kód kényszeríti, hogy a szolgáltatás végpontjaira vonatkozó kérések hitelesítése a Azure Active Directory által a megadott bérlőhöz kiadott bearer token használatával történik:

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

A megadott [postman-tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) végrehajtásához és a localhost használatával végzett helyi hibakereséshez egy bearer tokenre is szükség van. A mintakód ASP.NET alapkörnyezetek használatával módosítja a hitelesítési beállításokat a fejlesztési fázisban, és engedélyezi az önaírt jogkivonat használatát.

A Core több környezetének további ASP.NET lásd: Több környezet használata a [ASP.NET Core-ban.](/aspnet/core/fundamentals/environments)

A következő kód kikényszerítenie kell, hogy a szolgáltatás bármely végpontjára vonatkozó kérések hitelesítése egy egyéni kulccsal aláírt, bearer token használatával történik:

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

Küldjön GET kérést a Jogkivonat-vezérlőnek egy érvényes jogkivonat lekéréséhez. A _GenerateJSONWebToken_ metódus felelős a fejlesztéshez konfigurált paramétereknek megfelelő jogkivonat létrehozásáért:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Felhasználók kiépítésének és megszüntetésének kezelése

***1. példa. Egyező felhasználó lekérdezése a szolgáltatásban***

Azure Active Directory (AAD) lekérdezi a szolgáltatást egy olyan felhasználótól, aki attribútumértéke megegyezik az AAD-hez tartozó felhasználó `externalId` mailNickname attribútumértékével. A lekérdezés egy Hypertext Transfer Protocol (HTTP) kérelemként van kifejezve, például ebben a példában, amelyben a jyoung egy felhasználó mailNickname nevű mintája a Azure Active Directory.

>[!NOTE]
> Ez csak egy példa. Nem minden felhasználó rendelkezik mailNickname attribútummal, és előfordulhat, hogy a felhasználó értéke nem egyedi a címtárban. Emellett az egyeztetéshez használt attribútum (ebben az esetben ) konfigurálható az `externalId` [AAD attribútumleképezésében.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

A mintakódban a kérés le lesz fordítva a szolgáltatás szolgáltatójának QueryAsync metódusának hívására. A metódus aláírása: 

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

A mintalekérdezésben a queryAsync metódusnak átadott argumentumok értéke egy adott attribútummal megadott felhasználó esetében `externalId` a következő:

* Paraméterek. AlternateFilters.Count: 1
* Paraméterek. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* Paraméterek. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"

***2. példa. Felhasználó kiépítése***

Ha a felhasználó mailNickname attribútumértékével egy olyan felhasználó webszolgáltatásra vonatkozó lekérdezésére adott válasz, amely megegyezik a felhasználó mailNickname attribútumértékével, akkor az AAD azt kéri, hogy a szolgáltatás biztosítsa az AAD-hez tartozó `externalId` felhasználót.  Példa egy ilyen kérésre: 

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
   "userName":"jyoung@testuser.com",
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

A mintakódban a kérés le lesz fordítva a szolgáltatás szolgáltatójának CreateAsync metódusának hívására. A metódus aláírása a következő: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

Felhasználó igénylése esetén az erőforrás-argumentum értéke a Microsoft.SCIM.Schemas kódtárban definiált Microsoft.SCIM.Core2EnterpriseUser osztály egy példánya.  Ha a felhasználó igénylése sikeres, akkor a metódus implementációja várhatóan a Microsoft.SCIM.Core2EnterpriseUser osztály egy példányát adja vissza, és az Identifier tulajdonság értéke az újonnan kiépített felhasználó egyedi azonosítója lesz.  

***3. példa. Felhasználó aktuális állapotának lekérdezése*** 

Ha frissítenie kell egy sciM által előzhető identitástárban ismert felhasználót, a Azure Active Directory a felhasználó aktuális állapotát kéri le a szolgáltatástól a következő kéréssel: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

A mintakódban a kérés a szolgáltatás szolgáltatójának RetrieveAsync metódusának hívására lesz lefordítva. A metódus aláírása a következő: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

A felhasználó aktuális állapotának lekérésére vonatkozó kérés példában a paraméter argumentum értékeként megadott objektum tulajdonságainak értékei a következők: 
  
* Azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***4. példa. Egy frissíthető referenciaattribútum értékének lekérdezése*** 

Ha frissíteni kell egy referenciaattribútumot, a Azure Active Directory lekérdezi a szolgáltatást annak meghatározására, hogy a szolgáltatás által előtereként megadott identitástárban lévő referenciaattribútum aktuális értéke megegyezik-e az adott attribútum Azure Active Directory. Felhasználók esetén az egyetlen attribútum, amelynek az aktuális értéke így lekérdezve van, a manager attribútum. Az alábbi példa egy olyan kérésre mutat példát, amely meghatározza, hogy egy felhasználói objektum kezelői attribútuma jelenleg rendelkezik-e egy bizonyos értékkel: A mintakódban a kérés a szolgáltató QueryAsync metódusának hívására lesz lefordítva. A parameters argumentum értékeként megadott objektum tulajdonságainak értéke a következő: 
  
* Paraméterek. AlternateFilters.Count: 2
* Paraméterek. AlternateFilters.ElementAt(x). AttributePath: "ID"
* Paraméterek. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
* Paraméterek. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* Paraméterek. RequestedAttributePaths.ElementAt(0): "ID"
* Paraméterek. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Itt az x index értéke lehet 0, az y index értéke pedig 1, az x értéke pedig 1, az y értéke pedig 0, a szűrőlekérdezés paraméter kifejezésének sorrendjétől függően.   

***5. példa. Kérés az Azure AD-től egy SCIM-szolgáltatáshoz egy felhasználó frissítésére*** 

Az alábbi példa egy felhasználó frissítésére Azure Active Directory scim-szolgáltatásnak adott kérésre: 

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

A mintakódban a kérés le lesz fordítva a szolgáltatás szolgáltatójának UpdateAsync metódusának hívására. A metódus aláírása: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

A felhasználó frissítésére vonatkozó kérés példában a patch argumentum értékeként megadott objektum a következő tulajdonságértékekkel rendelkezik: 

|Argumentum|Érték|
|-|-|
|ResourceIdentifier.Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"|
|(PatchRequest mint PatchRequest2). Operations.Count|1|
|(PatchRequest mint PatchRequest2). Operations.ElementAt(0). OperationName (Művelet neve)|OperationName.Add|
|(PatchRequest mint PatchRequest2). Operations.ElementAt(0). Path.AttributePath|"vezető"|
|(PatchRequest mint PatchRequest2). Operations.ElementAt(0). Value.Count|1|
|(PatchRequest mint PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Hivatkozás|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest mint PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Érték| 2819c223-7f76-453a-919d-413861904646|

***6. példa. Felhasználó megszüntetése***

Ha egy felhasználót meg kell megszüntetni egy SCIM-szolgáltatás előtt található identitástárolóból, az AAD a következő kéréseket küldi el:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

A mintakódban a kérést a szolgáltató DeleteAsync metódusának hívására fordítja le a program. A metódus aláírása a következő: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

A resourceIdentifier argumentum értékeként megadott objektum a következő tulajdonságértékekkel rendelkezik a felhasználó megszüntetésére vonatkozó kérés példáján: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Az SCIM-végpont integrálása az AAD SCIM-ügyféllel

Az Azure AD konfigurálható úgy, hogy automatikusan kiépítsen hozzárendelt felhasználókat és csoportokat olyan alkalmazások számára, amelyek az [SCIM 2.0](https://tools.ietf.org/html/rfc7644)protokoll egy adott profilját valósítják meg. A profil részletes dokumentációja az Azure AD SCIM-implementációval foglalkozó [dokumentumban van.](#understand-the-aad-scim-implementation)

Az ezekkel a követelményekkel való kompatibilitásra vonatkozó nyilatkozatokat az alkalmazásszolgáltatójánál vagy az alkalmazásszolgáltató dokumentációjában ellenőrizheti.

> [!IMPORTANT]
> Az Azure AD SCIM-implementációja az Azure AD felhasználóátépítési szolgáltatására épül, amely úgy lett kialakítva, hogy folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és a szabványos műveletek nagyon meghatározott készletét valósítja meg. Fontos megérteni ezeket a viselkedésmódokat az Azure AD SCIM-ügyfél viselkedésének a jobb működése miatt. További információkért lásd a Kiépítési [ciklusok: Kezdeti](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) és növekményes [szakaszt a Kiépítés működése című részben.](how-provisioning-works.md)

### <a name="getting-started"></a>Első lépések

Az ebben a cikkben leírt SCIM-profilt támogató alkalmazások az Azure AD-alkalmazásgyűjtemény "katalóguson kívüli alkalmazás" funkciójának használatával Azure Active Directory csatlakoztathatóak az alkalmazáshoz. A csatlakozás után az Azure AD 40 percenként futtat egy szinkronizálási folyamatot, amelyben lekérdezi az alkalmazás SCIM-végpontját a hozzárendelt felhasználók és csoportok számára, és a hozzárendelés részleteinek megfelelően hozza létre vagy módosítja azokat.

**SciM-et támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be az [AAD portálra.](https://aad.portal.azure.com) Vegye figyelembe, hogy a P2 licenccel rendelkező felhasználók ingyenes Azure Active Directory a fejlesztői programra való [feliratkozáskor](https://developer.microsoft.com/office/dev-program)
1. A **bal oldali panelen** válassza a Vállalati alkalmazások lehetőséget. Megjelenik az összes konfigurált alkalmazás listája, beleértve a katalógusból hozzáadott alkalmazásokat is.
1. Válassza **az + Új alkalmazás**+ Saját alkalmazás létrehozása  >  **lehetőséget.**
1. Adja meg az alkalmazás nevét, válassza az "*integrálja* a katalógusban nem  található más alkalmazást" lehetőséget, majd válassza a Hozzáadás lehetőséget egy alkalmazásobjektum létrehozásához. Az új alkalmazás megjelenik a vállalati alkalmazások listájában, és megnyílik az alkalmazáskezelési képernyője.

   ![Képernyőkép az Azure AD ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *alkalmazásgyűjteményről Az Azure AD alkalmazásgyűjteménye*

   > [!NOTE]
   > Ha a régi alkalmazás-katalógust használja, kövesse az alábbi képernyő-útmutatót.
   
   ![Képernyőkép az Azure AD régi alkalmazás-katalógusbeli élményről ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *az Azure AD régi alkalmazás-katalógusában*

1. Az alkalmazáskezelési képernyőn válassza a kiépítés **lehetőséget** a bal oldali panelen.
1. A **Kiépítési mód menüben** válassza az Automatikus **lehetőséget.**

   ![Példa: Az alkalmazás Provisioning (Kiépítés) lapja a Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *A kiépítés konfigurálása a Azure Portal*

1. A **Bérlői URL-cím** mezőbe írja be az alkalmazás SCIM-végpontjának URL-címét. Például: `https://api.contoso.com/scim/`
1. Ha az SCIM-végponthoz az Azure AD-től nem származó kiállító OAuth-jogkivonata szükséges, másolja a kötelező OAuth-alapú bearer tokent a választható **Titkos** jogkivonat mezőbe. Ha ezt a mezőt üresen hagyja, az Azure AD minden kéréshez tartalmaz egy OAuth-jogkivonatot, amely az Azure AD-ból van kibocsátva. Az Azure AD-t identitásszolgáltatóként felhasználó alkalmazások ellenőrizhetik ezt az Azure AD által kiadott jogkivonatot. 
   > [!NOTE]
   > Nem ajánlott ***üresen*** hagyni ezt a mezőt, és az Azure AD által létrehozott jogkivonatra támaszkodni. Ez a lehetőség elsősorban tesztelési célokra érhető el.
1. Válassza **a Kapcsolat tesztelése lehetőséget,** Azure Active Directory megpróbál csatlakozni az SCIM-végponthoz. Ha a kísérlet sikertelen, hibaüzenet jelenik meg.  

    > [!NOTE]
    > **A Kapcsolat tesztelése** szolgáltatás lekérdezi az SCIM-végpontot egy nem létező felhasználótól, és véletlenszerű GUID-azonosítót használ az Azure AD-konfigurációban kiválasztott egyező tulajdonságként. A várt helyes válasz HTTP 200 OK, üres SCIM ListResponse üzenettel.

1. Ha az alkalmazáshoz való csatlakozásra tett kísérlet sikeres, válassza a **Mentés lehetőséget** a rendszergazdai hitelesítő adatok mentéséhez.
1. A **Leképezések szakaszban** két választható attribútumleképezés-készlet [található:](customize-application-attributes.md)egy a felhasználói objektumokhoz, egy pedig a csoportobjektumok számára. Válassza ki mindegyiket az alkalmazásból szinkronizált Azure Active Directory áttekintéséhez. Az Egyező tulajdonságokként **kiválasztott** attribútumok az alkalmazás felhasználóinak és csoportjainak megfeleltetésére használhatók a frissítési műveletekhez. A **módosítások véglegesítéshez** válassza a Mentés lehetőséget.

    > [!NOTE]
    > Ha szeretné, letilthatja a csoportobjektumok szinkronizálását a "csoportok" leképezés letiltásával.

1. A **Beállítások területen** a Hatókör **mező** határozza meg, hogy mely felhasználók és csoportok lesznek szinkronizálva. Válassza **a Csak a hozzárendelt felhasználók** és csoportok szinkronizálása (ajánlott) lehetőséget, ha csak a Felhasználók és csoportok lapon hozzárendelt felhasználókat és **csoportokat szinkronizálja.**
1. A konfigurálás befejezése után állítsa a **Kiépítési állapot beállítását** Be **állapotúra.**
1. Az  Azure AD kiépítési szolgáltatásának indításhoz válassza a Mentés lehetőséget.
1. Ha csak a hozzárendelt felhasználókat és csoportokat  szinkronizálja (ajánlott), válassza a Felhasználók és csoportok lapot, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus elindulása után  a bal oldali panelen a Kiépítési naplók lehetőséget választva figyelheti a folyamat előrehaladását, amely az alkalmazáson a kiépítési szolgáltatás által végzett összes műveletet megjeleníti. Az Azure AD kiépítési naplók olvasására vonatkozó további információkért lásd: Reporting on automatic user account provisioning (Jelentéskészítés a felhasználói fiókok automatikus [építésével kapcsolatban).](check-status-user-account-provisioning.md)

> [!NOTE]
> A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 40 percenként történik, amíg a szolgáltatás fut.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Az alkalmazás közzététele az AAD-alkalmazásgyűjteményben

Ha egynél több bérlő által használt alkalmazást hozunk majd üzembe, azt az Azure AD alkalmazásgyűjteményében is elérhetővé teheti. Ez megkönnyíti a szervezetek számára az alkalmazás felderítését és a kiépítés konfigurálását. Az alkalmazás közzététele az Azure AD-katalógusban és a kiépítés mások számára való elérhetővé tevése egyszerű. A lépéseket itt [ellenőrizheti.](../develop/v2-howto-app-gallery-listing.md) A Microsoft segít Önnek az alkalmazás katalógusba integrálásában, a végpont [](../saas-apps/tutorial-list.md) tesztelésében és az ügyfelek által használható dokumentáció kiadásában.

### <a name="gallery-onboarding-checklist"></a>Katalógus-beirehozás ellenőrzőlistája
Használja az ellenőrzőlistát az alkalmazás gyors üzembe helyezéséhez, és az ügyfelek zökkenőmentes üzembe helyezést tapasztalnak. A katalógusba való berakozáskor a rendszer az információkat öntől gyűjti össze. 
> [!div class="checklist"]
> * SCIM [2.0](#understand-the-aad-scim-implementation) felhasználói és csoportvégpont támogatása (Csak egy szükséges, de mindkettő ajánlott)
> * Bérlőnként legalább 25 kérés támogatása annak érdekében, hogy a felhasználók és csoportok igénylése és megszüntetése késve legyen (kötelező)
> * Mérnöki és támogatási kapcsolattartók létrehozása az ügyfelek katalógusba való bevezető utáni támogatásához (kötelező)
> * 3 Az alkalmazás nem lejáró teszt hitelesítő adatai (kötelező)
> * Támogatja az OAuth engedélyezési kód megadását vagy egy hosszú életű tokent az alábbiakban leírtak szerint (kötelező)
> * Mérnöki és támogatási pont létrehozása az ügyfelek támogatásához katalógus-beépítés után (kötelező)
> * [Sémafelderítés támogatása (kötelező)](https://tools.ietf.org/html/rfc7643#section-6)
> * Több csoporttagság frissítésének támogatása egyetlen PATCH-sel
> * Az SCIM-végpont nyilvános dokumentálása

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Összekötők építésének engedélyezése az alkalmazáskatatárban
Az SCIM specifikációja nem határoz meg SCIM-specifikus hitelesítési és engedélyezési sémát, és a meglévő iparági szabványok használatára támaszkodik.

|Engedélyezési módszer|Előnyök|Hátrányok|Támogatás|
|--|--|--|--|
|Felhasználónév és jelszó (az Azure AD nem ajánlott vagy támogatott)|Könnyen megvalósítható|Nem biztonságos – [A Pa$$word nem számít](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|A katalógusbeli alkalmazások esetében eseti alapon támogatott. A nem katalógusbeli alkalmazások esetében nem támogatott.|
|Hosszú életű bearer token|A hosszú ideig tartó jogkivonatok nem követelik meg, hogy a felhasználó jelen legyen. A rendszergazdák könnyen használhatók a kiépítés beállításakor.|A hosszú életű jogkivonatokat nehéz lehet megosztani egy rendszergazdával nem biztonságos módszerek, például e-mail használata nélkül. |Katalógusbeli és nem katalógusbeli alkalmazások esetén támogatott. |
|OAuth engedélyezési kód megadása|A hozzáférési jogkivonatok sokkal rövidebb életűek, mint a jelszavak, és automatikus frissítési mechanizmussal is bírnak, amely a hosszú ideig tartó jogkivonatokkal nem rendelkezik.  A valódi felhasználónak jelen kell lennie a kezdeti engedélyezés során, és hozzá kell adnunk egy elszámoltathatósági szintet. |Egy felhasználónak jelen kell lennie. Ha a felhasználó elhagyja a szervezetet, a jogkivonat érvénytelen, és az engedélyezést újra el kell végrehajtani.|Katalógusbeli alkalmazásokhoz támogatott, nem katalógusbeli alkalmazásokhoz. Rövid távú tesztelési célokra azonban meg lehet adni egy hozzáférési jogkivonatot a felhasználói felületen titkos jogkivonatként. A nem katalógusbeli OAuth-kódok megadásának támogatása a hátralékban található, valamint a konfigurálható hitelesítési/jogkivonat-URL-címek támogatása a katalógusalkalmazásban.|
|OAuth-ügyfél hitelesítő adatainak megadása|A hozzáférési jogkivonatok sokkal rövidebb életűek, mint a jelszavak, és automatikus frissítési mechanizmussal is bírnak, amely a hosszú ideig tartó jogkivonatokkal nem rendelkezik. Az engedélyezési kód megadása és az ügyfél-hitelesítő adatok is azonos típusú hozzáférési jogkivonatot hoznak létre, ezért a metódusok közötti áthelyezés átlátható az API számára.  A kiépítés teljesen automatizált is lehet, és az új jogkivonatok felhasználói beavatkozás nélkül, csendesen kérhetőek. ||Katalógusbeli és nem katalógusbeli alkalmazások esetén nem támogatott. A támogatás a hátralékunkban van.|

> [!NOTE]
> Nem ajánlott üresen hagyni a jogkivonat mezőt az AAD-kiépítési konfiguráció egyéni alkalmazás felhasználói felületén. A létrehozott jogkivonat elsősorban tesztelési célokra érhető el.

### <a name="oauth-code-grant-flow"></a>OAuth-kód megadó folyamat

A kiépítési [](https://tools.ietf.org/html/rfc6749#page-24) szolgáltatás támogatja az engedélyezési kód megadását, és miután beküldi az alkalmazás katalógusban való közzétételére vonatkozó kérelmét, csapatunk az alábbi információk gyűjtésével fog dolgozni:

- **Engedélyezési** URL-cím, egy URL-cím, amelyet az ügyfél a felhasználó-ügynök átirányításával szerez be az erőforrás tulajdonosától. A rendszer erre az URL-címre irányítja át a felhasználót a hozzáféréshez. 

- **Jogkivonat-csere URL-címe,** egy URL-cím, amelyet az ügyfél egy hozzáférési jogkivonat engedélyezési engedélyének cseréjére használ, általában ügyfél-hitelesítéssel.

- **Az ügyfél-azonosító**, az engedélyezési kiszolgáló egy ügyfélazonosítót ad ki a regisztrált ügyfélnek, amely egy egyedi sztring, amely az ügyfél által megadott regisztrációs adatokat képviseli.  Az ügyfél-azonosító nem titkos. az erőforrás tulajdonosa számára elérhető, és **nem** használható önmagában az ügyfél-hitelesítéshez.  

- **Titkos ügyféloldali**, az engedélyezési kiszolgáló által létrehozott titkos adatokat, amelyek csak az engedélyezési kiszolgáló által ismert egyedi értékek. 

> [!NOTE]
> Az **engedélyezési URL-cím** és a **jogkivonatcsere URL-címe** jelenleg nem konfigurálható bérlőnként.

> [!NOTE]
> Az OAuth v1 nem támogatott az ügyfél titkos kódának miatt. Az OAuth v2 támogatott.  

Ajánlott eljárások (ajánlott, de nem kötelező):
* Több átirányítási URL-cím támogatása. A rendszergazdák a kiépítést a "portal.azure.com" és a "aad.portal.azure.com" aad.portal.azure.com konfigurálhatják. Ha több átirányítási URL-címet támogat, a felhasználók mindkét portálról engedélyezhetik a hozzáférést.
* Több titkos adat támogatása az egyszerű megújítás érdekében, leállás nélkül. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>OAuth-kód megadó folyamatának beállítása

1. Jelentkezzen be a Azure Portal, válassza a **Vállalati** alkalmazások Alkalmazás kiépítése, majd az Authorize  >    >   **(Engedély) lehetőséget.**

   1. Azure Portal átirányítja a felhasználót az engedélyezési URL-címre (a harmadik féltől származó alkalmazás bejelentkezési oldalára).

   1. A rendszergazda hitelesítő adatokat ad meg a harmadik féltől származó alkalmazásnak. 

   1. A harmadik féltől származó alkalmazás visszairányítsa a felhasználót Azure Portal, és megadja a megadókódot 

   1. Az Azure AD kiépítési szolgáltatások a jogkivonat URL-címét hívja meg, és megadja a hozzáférési kódot. A harmadik féltől származó alkalmazás a hozzáférési jogkivonattal, a frissítési jogkivonattal és a lejárati dátummal válaszol

1. A kiépítési ciklus kezdetekor a szolgáltatás ellenőrzi, hogy az aktuális hozzáférési jogkivonat érvényes-e, és szükség esetén új jogkivonatot hoz létre. A hozzáférési jogkivonat az alkalmazáshoz minden kérésben meg van jelölve, és a kérés érvényességét az egyes kérések előtt ellenőrzi a rendszer.

> [!NOTE]
> Bár az OAuth nem állítható be a katalóguson kívüli alkalmazásokon, manuálisan létrehozhat egy hozzáférési jogkivonatot az engedélyezési kiszolgálóról, és meg is használhatja azt titkos jogkivonatként egy nem katalógusban található alkalmazáshoz. Ez lehetővé teszi, hogy ellenőrizze az SCIM-kiszolgáló és az AAD SCIM-ügyfél kompatibilitását, mielőtt az OAuth-kód megadását támogató alkalmazás-katalógusba való be- vagy felveszné.  

**Hosszú ideig tartó OAuth-jogkivonatok:** Ha az alkalmazás nem támogatja az OAuth engedélyezési kódengedélyezési folyamatot, hozzon létre egy hosszú ideig tartó, OAuth-jogkivonatot, amely segítségével a rendszergazda beállíthatja a kiépítési integrációt. A jogkivonatnak állandónak kell lennie, különben [](application-provisioning-quarantine-status.md) a kiépítési feladat karanténba kerül a jogkivonat lejártakor.

További hitelesítési és engedélyezési módszerekért tudassa velünk a [UserVoice-on.](https://aka.ms/appprovisioningfeaturerequest)

### <a name="gallery-go-to-market-launch-check-list"></a>Katalógus piacra dobáskor – ellenőrzőlista
A közös integráció tudatosságának és igényének felkeltése érdekében javasoljuk, hogy frissítse meglévő dokumentációját, és erősíti az integrációt a marketingcsatornákban.  Az alábbi ellenőrzőlista-tevékenységeket javasoljuk, hogy teljes körűen támogassa az indítást

> [!div class="checklist"]
> * Győződjön meg arról, hogy az értékesítési és ügyfélszolgálati csapatok tisztában vannak az integrációs képességekkel, készen állnak és tudnak beszélni az integrációs képességekkel. Tájékoztassa a csapatokat, adja meg nekik a gyakori kérdésekre vonatkozó információkat, és foglalja bele az értékesítési anyagokba való integrációt. 
> * Blogbejegyzés vagy kiadás létrehozása, amely leírja a közös integrációt, az előnyöket és az első lépések útmutatóját. [Példa: Imprivata és Azure Active Directory Press Release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * A közösségi médiával, például a Twitter, a Facebook vagy a LinkedIn segítségével népszerűsítheti az integrációt az ügyfelek felé. Mindenképpen foglalja bele @AzureAD a bejegyzést, hogy újratweetkedjünk a bejegyzésben. [Példa: Imprivata Twitter-bejegyzés](https://twitter.com/azuread/status/1123964502909779968)
> * Hozza létre vagy frissítse a marketingoldalakat/webhelyeket (például az integrációs oldalt, a partneroldalt, a díjszabási oldalt stb.), hogy tartalmazza a közös integráció elérhetőségét. [Példa: Pingboard integrációs oldal,](https://pingboard.com/org-chart-for) [Smartsheet integrációs oldal,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com díjszabási oldal](https://monday.com/pricing/) 
> * Hozzon létre egy súgóközpont-cikket vagy műszaki dokumentációt arról, hogyan kezdik meg az ügyfelek az első lépésekben. [Például: Envoy + Microsoft Azure Active Directory integráció.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Értesítse az ügyfeleket az új integrációról az ügyfelek kommunikációján keresztül (havi hírlevelek, e-mail-kampányok, termék kibocsátási megjegyzései). 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [SCIM-mintavégpont fejlesztése](use-scim-to-build-users-and-groups-endpoints.md) 
>  Felhasználókiépítés és -megszüntetés [automatizálása SaaS-alkalmazásokban](user-provisioning.md) 
>  [Attribútumleképezések testreszabása felhasználókiépítéshez](customize-application-attributes.md) 
>  [Kifejezések írása attribútumleképezéshez](functions-for-customizing-application-data.md) 
>  [Felhasználóátépítés hatókörszűrői](define-conditional-rules-for-provisioning-user-accounts.md) 
>  [Fiók létesítésével kapcsolatos értesítések](user-provisioning.md) 
>  [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
