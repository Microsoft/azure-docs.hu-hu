---
title: Oktatóanyag – Azure Active Directory attribútum-hozzárendelések testreszabása
description: Ismerje meg, hogy az SaaS-alkalmazásokhoz milyen attribútum-hozzárendelések Azure Active Directory az üzleti igények kielégítése érdekében.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 738b89ee9347a25e2d24369a48e966f0bec6daf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579467"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Oktatóanyag – a felhasználó-kiépítési attribútum testreszabása – SaaS-alkalmazások leképezése Azure Active Directory

A Microsoft Azure AD támogatja a külső SaaS-alkalmazások, például a Salesforce, a G Suite és más felhasználók számára történő üzembe helyezést. Ha engedélyezi a felhasználók kiosztását egy külső SaaS-alkalmazás számára, a Azure Portal attribútum-hozzárendelések segítségével vezérli az attribútum értékeit.

Mielőtt elkezdené, győződjön meg arról, hogy ismeri az App managementet és az **egyszeri Sign-On (SSO)** fogalmakat. Tekintse meg az alábbi hivatkozásokat:
- [A gyors üzembe helyezési sorozat az alkalmazások kezelésében az Azure AD-ben](../manage-apps/view-applications-portal.md)
- [Mi az az egyszeri Sign-On (SSO)?](../manage-apps/what-is-single-sign-on.md)

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között van egy előre konfigurált attribútum és attribútum-hozzárendelés. Egyes alkalmazások más típusú objektumokat is kezelhetnek a felhasználók, például a csoportok mellett.

Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is.

## <a name="editing-user-attribute-mappings"></a>Felhasználói attribútum szerkesztése – leképezések

Kövesse az alábbi lépéseket a felhasználók kiosztásának **leképezési** funkciójának eléréséhez:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com).
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza ki bármelyik alkalmazást az alkalmazás-felügyeleti panel betöltéséhez, ahol megtekintheti a jelentéseket, és kezelheti az alkalmazás beállításait.
1. A **kiépítés** lehetőség kiválasztásával kezelheti a kiválasztott alkalmazás felhasználói fiókjának kiépítési beállításait.
1. A **leképezések** kibontásával megtekintheti és szerkesztheti a felhasználói attribútumokat, amelyek az Azure ad és a célalkalmazás között áramlanak. Ha a célalkalmazás támogatja azt, ez a szakasz lehetővé teszi a csoportok és felhasználói fiókok üzembe helyezésének igény szerinti konfigurálását.

   ![Leképezések használata felhasználói attribútumok megtekintéséhez és szerkesztéséhez](./media/customize-application-attributes/21.png)

1. Válassza ki a **leképezések** konfigurációját a kapcsolódó **attribútum-leképezési** képernyő megnyitásához. Az SaaS-alkalmazások megfelelő működéséhez bizonyos attribútum-hozzárendelések szükségesek. A kötelező attribútumok esetében a **törlési** funkció nem érhető el.

   ![Attribútum-hozzárendelés használata az alkalmazások attribútumainak konfigurálásához](./media/customize-application-attributes/22.png)

   Ebben a képernyőfelvételben láthatja, hogy a Salesforce található felügyelt objektum **username** attribútuma a csatolt Azure Active Directory objektum **userPrincipalName** értékével van feltöltve.

1. Válasszon ki egy meglévő **attribútum-hozzárendelést** az **attribútum szerkesztése** képernyő megnyitásához. Itt szerkesztheti az Azure AD és a célalkalmazás közötti adatforgalom felhasználói attribútumait.

   ![Felhasználói attribútumok szerkesztése a szerkesztési attribútum használatával](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Az attribútum-leképezési típusok ismertetése

Az attribútum-hozzárendelésekkel szabályozhatja, hogy az attribútumok hogyan legyenek feltöltve egy külső gyártótól származó SaaS-alkalmazásban.
Négy különböző leképezési típus támogatott:

- **Direct (közvetlen** ) – a TARGET attribútum az Azure ad-ben csatolt objektum attribútumának értékével van feltöltve.
- **Konstans** – a TARGET attribútum egy megadott karakterlánccal van feltöltve.
- **Kifejezés** – a cél attribútum a parancsfájl-szerű kifejezés eredménye alapján van feltöltve.
  További információ: [kifejezések írása Attribute-Mappingshoz Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Nincs** – a cél attribútum változatlan marad. Ha azonban a cél attribútum még mindig üres, akkor a rendszer a megadott alapértelmezett értékkel tölti fel.

A négy alaptípussal együtt az egyéni attribútum-hozzárendelések az opcionális **alapértelmezett** érték-hozzárendelés fogalmát támogatják. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a TARGET attribútum értéke akkor legyen feltöltve, ha nincs érték az Azure AD-ben vagy a TARGET objektumon. A leggyakoribb beállítás az, ha üresen hagyja ezt a beállítást.

### <a name="understanding-attribute-mapping-properties"></a>Attribútumok – megfeleltetési tulajdonságok ismertetése

Az előző szakaszban már be lett vezetve az attribútum-leképezési típus tulajdonságra.
A tulajdonsággal együtt az attribútum-hozzárendelések a következő attribútumokat is támogatják:

- **Forrásoldali attribútum** – a felhasználói attribútum a forrásoldali rendszerből (példa: Azure Active Directory).
- **Target attribútum** – a felhasználói attribútum a célként megadott rendszeren (például: ServiceNow).
- **Alapértelmezett érték, ha Null (nem kötelező)** – a célként megadott rendszernek átadandó érték, ha a forrás attribútum értéke null. Ez az érték csak a felhasználó létrehozásakor lesz kiépítve. Egy meglévő felhasználó frissítésekor az "alapértelmezett érték, ha null" nem lesz kiépítve. Ha például a célrendszer összes meglévő felhasználóját egy adott feladathoz tartozó címmel szeretné kiépíteni (ha a forrásrendszer null értékű, akkor a következő [kifejezést](../app-provisioning/functions-for-customizing-application-data.md)használhatja: Switch (IsPresent ([beosztás]), "DefaultValue", "true", [beosztás]). Ügyeljen arra, hogy a "default Value" értéket cserélje le a forrásrendszer null értékének megadásához. 
- **Objektumok egyeztetése ezzel az attribútummal** – azt határozza meg, hogy ez a leképezés használható-e a felhasználók egyedi azonosítására a forrás-és a célként megadott rendszerek között. Általában az Azure AD userPrincipalName vagy mail attribútumára van beállítva, amely általában egy célalkalmazás username mezőjére van leképezve.
- **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, azok kiértékelése a mező által meghatározott sorrendben történik. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok. Habár beállíthatja, hogy minél több egyező attribútumot szeretne használni, vegye figyelembe, hogy az egyező attribútumokként használt attribútumok valóban egyediek-e, és meg kell-e adni a megfelelő attribútumokat. Az ügyfelek általában 1 vagy 2 egyező attribútumokkal rendelkeznek a konfigurációban. 
- **A leképezés alkalmazása**
  - **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is.
  - **Csak a létrehozás során** – alkalmazza ezt a leképezést csak a felhasználói létrehozási műveletekre.

## <a name="matching-users-in-the-source-and-target--systems"></a>A forrás-és a megcélzott rendszerek felhasználóinak egyeztetése
Az Azure AD-kiépítési szolgáltatás a "zöld mező" forgatókönyvekben is üzembe helyezhető (ahol a felhasználók nem lépnek ki a célként megadott rendszerből) és a "rozsdaövezetek rehabilitálása" forgatókönyvek (ahol a felhasználók már léteznek a célszámítógépen). Mindkét forgatókönyv támogatásához a kiépítési szolgáltatás a megfelelő attribútumok koncepcióját használja. Az egyeztetési attribútumok lehetővé teszik annak meghatározását, hogy a rendszer egyedi módon azonosítsa a felhasználókat a forrásban, és megegyezzen a célként megadott felhasználóval. Az üzembe helyezés megtervezésének részeként azonosítsa azt az attribútumot, amellyel egyedileg azonosíthatók a felhasználók a forrás-és a célként szolgáló rendszerekben. Tudnivaló:

- **Az egyező attribútumok egyedinek kell lenniük:** Az ügyfelek gyakran használják az attribútumokat, például a userPrincipalName, az e-maileket vagy az objektumazonosítók használatát a megfelelő attribútumként.
- **Több attribútum is használható egyező attribútumként:** Több attribútumot is megadhat a kiértékeléshez, ha a felhasználók és a kiértékelésük sorrendje (a felhasználói felületen egyező prioritásként van meghatározva). Ha például három attribútumot határoz meg egyező attribútumokként, és a felhasználó egyedi módon illeszkedik az első két attribútum kiértékelése után, akkor a szolgáltatás nem értékeli ki a harmadik attribútumot. A szolgáltatás a megadott sorrendben értékeli ki a megfelelő attribútumokat, és leállítja az értékelést, ha egyezés található.  
- A **forrás és a cél értékének nem kell pontosan megegyeznie:** A cél értéke lehet a forrás értékének néhány egyszerű funkciója. Tehát a forrásban és a userPrincipalName lévő emailAddress attribútummal rendelkezhet, és az emailAddress attribútum egy függvényével egyezik meg, amely bizonyos karaktereket egy konstans értékkel helyettesít.  
- Az **attribútumok kombinációja alapján történő megfeleltetés nem támogatott:** A legtöbb alkalmazás nem támogatja a lekérdezések két tulajdonság alapján történő lekérdezését. Ezért az attribútumok kombinációja alapján nem lehet egyeztetni. Lehetséges, hogy egy másik után kiértékeli az egyes tulajdonságokat.
- **Minden felhasználónak rendelkeznie kell legalább egy megfelelő attribútum értékével:** Ha egy egyező attribútumot határoz meg, az összes felhasználónak rendelkeznie kell egy értékkel az adott attribútumhoz a forrás rendszerében. Ha például a userPrincipalName-t a megfelelő attribútumként definiálja, az összes felhasználónak rendelkeznie kell userPrincipalName. Ha több egyező attribútumot (például extensionAttribute1 és e-mailt) definiál, nem minden felhasználónak ugyanazzal a megfelelő attribútummal kell rendelkeznie. Egy felhasználó rendelkezhet extensionAttribute1, de nem küldheti el az e-mailt, míg egy másik felhasználónak nem lehet extensionAttribute1. 
- **A célként megadott alkalmazásnak támogatnia kell a szűrést a megfelelő attribútumon:** Az alkalmazások fejlesztői lehetővé teszik a felhasználók vagy csoportok API-k attribútumainak egy részhalmazának szűrését. A katalógusban található alkalmazások esetében biztosítjuk, hogy az alapértelmezett attribútum-hozzárendelés egy olyan attribútumhoz legyen hozzárendelve, amelyet a célalkalmazás API-je támogat a szűrést. Ha módosítja a célalkalmazás alapértelmezett egyező attribútumát, tekintse meg a harmadik féltől származó API-dokumentációt, és győződjön meg arról, hogy az attribútum szűrhető.  

## <a name="editing-group-attribute-mappings"></a>Csoport attribútumának szerkesztése – leképezések

A kiválasztott számú alkalmazás, például a ServiceNow, a Box és a G Suite lehetővé teszi a csoport objektumainak és felhasználói objektumainak kiépítését. A csoport objektumai tartalmazhatnak olyan csoport-tulajdonságokat, mint a megjelenítendő nevek és az e-mail-aliasok, a csoporttagokkal együtt.

![Példa a kiépített csoporttal és felhasználói objektumokkal rendelkező ServiceNow](./media/customize-application-attributes/24.png)

A csoportok kiosztása opcionálisan engedélyezhető vagy letiltható úgy, hogy kijelöli a **hozzárendelések** csoport leképezése elemét, és **engedélyezi** az **attribútum-leképezési** képernyőn a kívánt beállítást.

A csoport objektumainak részeként kiépített attribútumok ugyanúgy testreszabhatók, mint a korábban leírt felhasználói objektumok. 

> [!TIP]
> A csoport objektumainak (tulajdonságai és tagjai) [kiosztása a csoportok](../manage-apps/assign-user-or-group-access-portal.md) alkalmazáshoz való hozzárendelésének különböző fogalma. Egy csoportot hozzárendelhet egy alkalmazáshoz, de csak a csoportban lévő felhasználói objektumokat kell kiépíteni. A teljes csoport objektumainak kiosztása nem szükséges a hozzárendelésekhez tartozó csoportok használatához.

## <a name="editing-the-list-of-supported-attributes"></a>A támogatott attribútumok listájának szerkesztése

Az adott alkalmazás által támogatott felhasználói attribútumok előre konfigurálva vannak. A legtöbb alkalmazás felhasználói felügyeleti API-jai nem támogatják a séma-felderítést. Így az Azure AD-kiépítési szolgáltatás nem képes dinamikusan létrehozni a támogatott attribútumok listáját az alkalmazás hívásával.

Egyes alkalmazások azonban támogatják az egyéni attribútumokat, az Azure AD-kiépítési szolgáltatás pedig képes az egyéni attribútumok olvasására és írására. Ha meg szeretné adni a definíciókat a Azure Portalban, jelölje be a **Speciális beállítások megjelenítése** jelölőnégyzetet az **attribútum-leképezési** képernyő alján, majd válassza az **attribútumok szerkesztése** elemet az alkalmazáshoz.

Az attribútumok listájának testreszabását támogató alkalmazások és rendszerek a következők:

- Salesforce
- ServiceNow
- Munkanapokat Active Directory/munkanapokat Azure Active Directory
- SuccessFactors Active Directory/SuccessFactors-re Azure Active Directory
- Azure Active Directory (az[Azure ad-Graph API az alapértelmezett attribútumok](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) és az egyéni címtárszolgáltatás-bővítmények támogatottak). További információ a [bővítmények](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning-sync-attributes-for-mapping#create-an-extension-attribute-on-a-cloud-only-user) és az [ismert korlátozások](https://docs.microsoft.com/azure/active-directory/app-provisioning/known-issues)létrehozásáról. 
- A [SCIM 2,0](https://tools.ietf.org/html/rfc7643) -et támogató alkalmazások
- Ha Azure Active Directory visszaírási a munkanap vagy a SuccessFactors számára, a rendszer támogatja a támogatott attribútumok (XPATH és JSONPath) releváns metaadatainak frissítését, de nem támogatott az alapértelmezett sémán kívüli új munkanapokat vagy SuccessFactors-attribútumokat hozzáadni.


> [!NOTE]
> A támogatott attribútumok listájának szerkesztése csak olyan rendszergazdák számára ajánlott, akik testre szabták az alkalmazásaikat és a rendszerüket, és hogy az egyéni attribútumok hogyan lettek definiálva, vagy ha a forrás attribútum nem jelenik meg automatikusan az Azure Portal felhasználói felületén. Ez esetenként az alkalmazás vagy a rendszer által biztosított API-k és fejlesztői eszközök ismeretét igényli. A támogatott attribútumok listájának szerkesztésének lehetősége alapértelmezés szerint le van tiltva, de az ügyfelek a következő URL-címhez való navigálással engedélyezhetik a képességet: https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true . Ezután navigáljon az alkalmazáshoz, és tekintse meg az attribútumok listáját a [fent](#editing-the-list-of-supported-attributes)leírtak szerint. 

A támogatott attribútumok listájának szerkesztésekor a következő tulajdonságokat kell megadnia:

- **Név** – a cél objektum sémájában definiált attribútum rendszerneve.
- **Type (típus** ) – az attribútum által a célobjektum sémájában definiált adattípus, amely a következő típusok egyike lehet:
  - *Bináris* – az attribútum bináris adatértékeket tartalmaz.
  - *Logikai* – az attribútum igaz vagy hamis értéket tartalmaz.
  - *Dátum és idő* – az attribútum egy Date karakterláncot tartalmaz.
  - Az *Integer* -attribútum egész számot tartalmaz.
  - A *Reference* -ATTRIBÚTUM olyan azonosítót tartalmaz, amely a célalkalmazás egy másik táblájában tárolt értékre hivatkozik.
  - *Karakterlánc*  – az attribútum szöveges karakterláncot tartalmaz.
- **Elsődleges kulcs?** – Azt határozza meg, hogy az attribútum a célobjektum sémájában elsődleges kulcs mezőként van-e definiálva.
- **Kötelező?** – Azt határozza meg, hogy az attribútumot kötelező-e kitölteni a célalkalmazás vagy a rendszer számára.
- **Több érték?** – Azt határozza meg, hogy az attribútum több értéket is támogat-e.
- **Pontos eset?** – Azt határozza meg, hogy az attribútumok értékei kis-és nagybetűk megkülönböztetésével legyenek kiértékelve.
- **API-kifejezés** – ne használja, kivéve, ha erre a célra egy adott kiépítési összekötő (például a munkanap) dokumentációja kéri.
- **Hivatkozott Object attribútum** – ha ez egy hivatkozástípus attribútum, akkor ezzel a menüvel kiválaszthatja a célalkalmazás azon tábláját és attribútumát, amely az attribútumhoz társított értéket tartalmazza. Ha például egy "részleg" nevű attribútummal rendelkezik, amelynek tárolt értéke egy különálló "részleg" táblában található objektumra hivatkozik, akkor válassza a "Departments.Name" elemet. Az adott alkalmazáshoz támogatott hivatkozási táblák és az elsődleges azonosító mezők előre konfigurálva vannak, és jelenleg nem szerkeszthetők a Azure Portal használatával, de a [Microsoft Graph API](/graph/api/resources/synchronization-configure-with-custom-target-attributes)használatával szerkeszthető.

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Egyéni kiterjesztési attribútum kiépítés egy SCIM-kompatibilis alkalmazásba
A SCIM RFC egy alapszintű felhasználót és csoportot határoz meg, és lehetővé teszi, hogy a bővítmények megfeleljenek a sémának az alkalmazás igényeinek megfelelően. Egyéni attribútum hozzáadása SCIM-alkalmazáshoz:
   1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com), válassza a **vállalati alkalmazások** lehetőséget, válassza ki az alkalmazást, majd válassza a **kiépítés** lehetőséget.
   2. A **leképezések** területen válassza ki azt az objektumot (felhasználót vagy csoportot), amelyhez egyéni attribútumot szeretne hozzáadni.
   3. A lap alján válassza a **Speciális beállítások megjelenítése** lehetőséget.
   4. Válassza **a AppName szerkesztése** elemet.
   5. Az attribútum lista alján adja meg az egyéni attribútum adatait a megadott mezőkben. Ezután válassza az **attribútum hozzáadása** elemet.

A SCIM alkalmazások esetében az attribútum nevének az alábbi példában látható mintázatot kell követnie. A "CustomExtensionName" és a "CustomAttribute" testreszabható az alkalmazás követelményei szerint, például: urn: IETF: params: scim: schemas: Extension: CustomExtensionName: 2.0: User: CustomAttribute 

Ezek az utasítások csak a SCIM-kompatibilis alkalmazásokra vonatkoznak. Az olyan alkalmazások, mint a ServiceNow és a Salesforce, nem integráltak az Azure AD-vel a SCIM használatával, ezért nem igénylik ezt az adott névteret egyéni attribútumok hozzáadásakor.

Az egyéni attribútumok nem lehetnek hivatkozási attribútumok, többértékű vagy összetett típusú attribútumok. Az egyéni többértékű és összetett típusú kiterjesztési attribútumok jelenleg csak a katalógusban lévő alkalmazásokhoz támogatottak.  
 
**Példa kiterjesztési attribútummal rendelkező felhasználó ábrázolására:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
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


## <a name="provisioning-a-role-to-a-scim-app"></a>Szerepkör üzembe helyezése egy SCIM-alkalmazásban
Az alábbi lépésekkel szerepköröket hozhat létre az alkalmazáshoz. Vegye figyelembe, hogy az alábbi leírás az egyéni SCIM-alkalmazásokra vonatkozik. A Gallery-alkalmazások, például a Salesforce és a ServiceNow esetében használja az előre meghatározott szerepkör-leképezéseket. Az alábbi felsorolás leírja, hogyan alakíthatja át az AppRoleAssignments attribútumot az alkalmazás által várt formátumra.

- Az Azure AD-beli appRoleAssignment az alkalmazás egyik szerepköréhez való leképezéséhez az attribútumot [kifejezéssel](../app-provisioning/functions-for-customizing-application-data.md)kell átalakítani. A appRoleAssignment attribútum **nem képezhető le közvetlenül** egy szerepkör-attribútumra anélkül, hogy kifejezést kellene használnia a szerepkör részleteinek elemzéséhez. 

- **SingleAppRoleAssignment** 
  - **Mikor kell használni:** A SingleAppRoleAssignment kifejezés használatával egyetlen szerepkört helyezhet üzembe egy felhasználó számára, és meghatározhatja az elsődleges szerepkört. 
  - **Konfigurálás:** A fent ismertetett lépések végrehajtásával navigáljon az attribútum-hozzárendelések lapra, és használja a SingleAppRoleAssignment kifejezést a roles attribútum leképezéséhez. Három szerepkör-attribútum közül választhat: (szerepkörök [elsődleges EQ "igaz"]. megjelenítés, szerepkörök [elsődleges EQ "igaz]. típus és szerepkörök [elsődleges EQ" true "]. Value). Dönthet úgy, hogy a hozzárendelések bármelyikét vagy az összes szerepkör-attribútumot tartalmazza. Ha egynél többre szeretne belefoglalni, csak adjon hozzá egy új leképezést, és adja meg a célként megadott attribútumként.  
  
  ![SingleAppRoleAssignment hozzáadása](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Megfontolandó dolgok**
    - Győződjön meg arról, hogy a rendszer nem rendel hozzá több szerepkört a felhasználóhoz. Nem garantáljuk, hogy melyik szerepkört kell kiépíteni.
    
  - **Példa kérésre (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Példa kimenetre (javítás)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
A javítás és a POST kérelem formátuma eltérő. Annak biztosítása érdekében, hogy a POST és a PATCH ugyanolyan formátumban legyen elküldve, használhatja az [itt](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior)ismertetett funkció jelzőjét. 

- **AppRoleAssignmentsComplex** 
  - **Mikor kell használni:** A AppRoleAssignmentsComplex kifejezés használatával több szerepkört is kiépítheti egy felhasználó számára. 
  - **Konfigurálás:** Szerkessze a fentiekben ismertetett támogatott attribútumok listáját, és adjon hozzá egy új attribútumot a szerepkörökhöz: 
  
    ![Szerepkörök hozzáadása](./media/customize-application-attributes/add-roles.png)<br>

    Ezután a AppRoleAssignmentsComplex kifejezés használatával képezhető le az egyéni szerepkör attribútumra az alábbi képen látható módon:

    ![AppRoleAssignmentsComplex hozzáadása](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Megfontolandó dolgok**
    - Az összes szerepkör elsődleges = hamis értékként lesz kiépítve.
    - A bejegyzés tartalmazza a szerepkör típusát. A javítási kérelem nem tartalmaz típust. Dolgozunk a típus küldésében a POST-és a PATCH-kérésekben.
    
  - **Példa kimenetre** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Többértékű attribútum kiépítés
Bizonyos attribútumok, például a phoneNumbers és az e-mailek olyan többértékű attribútumok, amelyekben különböző típusú telefonszámokat vagy e-maileket kell megadnia. Használja az alábbi kifejezést a többértékű attribútumok esetében. Lehetővé teszi az attribútum típusának és leképezésének megadását az értékhez tartozó Azure AD felhasználói attribútumhoz. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers [type EQ "fax"]. Value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Az alapértelmezett attribútumok és attribútumok leképezésének visszaállítása

Ha újra kell indítania a meglévő leképezéseket, és vissza kell állítania az alapértelmezett állapotukat, akkor jelölje be az **alapértelmezett leképezések visszaállítása** jelölőnégyzetet, és mentse a konfigurációt. Ezzel beállítja az összes leképezést és hatókör-szűrőt úgy, mintha az alkalmazás csak az alkalmazás-katalógusból lett hozzáadva az Azure AD-bérlőhöz.

Ha ezt a beállítást választja, a kiépítési szolgáltatás futása közben minden felhasználó újraszinkronizálását fogja kényszeríteni.

> [!IMPORTANT]
> Javasoljuk, hogy a beállítás meghívása előtt állítsa **ki** a **kiépítési állapotot** .

## <a name="what-you-should-know"></a>Alapismeretek

- Microsoft Azure AD a szinkronizálási folyamat hatékony megvalósítását teszi lehetővé. Egy inicializált környezetben csak a frissítéseket igénylő objektumok lesznek feldolgozva szinkronizálási ciklusban.
- Az attribútumok frissítése-a leképezések hatással vannak a szinkronizálási ciklus teljesítményére. Az attribútum-hozzárendelési konfiguráció frissítéséhez az összes felügyelt objektum újraértékelése szükséges.
- Az ajánlott eljárás az, ha a minimálisan megőrzi az attribútum-hozzárendelések egymást követő módosításait.
- Ha olyan fénykép-attribútumot ad hozzá, amelyet egy alkalmazáshoz szeretne kiépíteni, ma nem lehet megadnia a fénykép szinkronizálásának formátumát. A funkciót [felhasználói hangon](https://feedback.azure.com/forums/169401-azure-active-directory) kérheti le
- A IsSoftDeleted attribútum gyakran része az alkalmazás alapértelmezett leképezésének. A IsSoftdeleted az alkalmazás négy forgatókönyvének egyikében igaz lehet (a felhasználó hatókörén kívül esik, mert nincs hozzárendelve a hatókörhöz, mert a felhasználó nem felel meg egy hatóköri szűrőnek, mert a felhasználó az Azure AD-ben nem lett törölve, vagy a AccountEnabled tulajdonság hamis értékre van állítva a felhasználónál). Nem ajánlott eltávolítani a IsSoftDeleted attribútumot az attribútumok hozzárendeléseiből.
- Az Azure ad-kiépítési szolgáltatás nem támogatja a null értékek kiépítés.
- Az elsődleges kulcs (általában az "ID") nem szerepelhet célként megadott attribútumként az attribútum-hozzárendelésekben. 
- A szerepkör attribútumot általában egy kifejezéssel kell leképezni, nem közvetlen hozzárendelést. A szerepkör-hozzárendeléssel kapcsolatos további részletekért lásd a fenti szakaszt. 
- Noha letilthatja a csoportokat a leképezésekről, a felhasználók letiltása nem támogatott. 

## <a name="next-steps"></a>Következő lépések

- [A felhasználók üzembe helyezésének és megszüntetésének automatizálása az SaaS-alkalmazásokban](user-provisioning.md)
- [Kifejezések írása attribútum-leképezésekhez](functions-for-customizing-application-data.md)
- [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
- [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
