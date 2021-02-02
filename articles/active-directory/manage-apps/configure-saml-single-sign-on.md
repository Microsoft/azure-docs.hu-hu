---
title: Az SAML-alapú egyszeri bejelentkezés (SSO) megismerése a Azure Active Directoryban lévő alkalmazásokhoz
description: Az SAML-alapú egyszeri bejelentkezés (SSO) megismerése a Azure Active Directoryban lévő alkalmazásokhoz
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257692"
---
# <a name="understand-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés ismertetése

Az alkalmazások felügyeletének rövid útmutató [sorozatában](view-applications-portal.md) megtudhatta, hogyan használhatja az Azure ad-t az alkalmazás identitás-szolgáltatója (identitásszolgáltató) használatával. Ez a cikk részletesebben ismerteti az egyszeri bejelentkezéshez használható SAML-alapú beállítást. 


## <a name="before-you-begin"></a>Előkészületek

Az Azure AD használata az identitás-szolgáltatóként (identitásszolgáltató) és az egyszeri bejelentkezés (SSO) konfigurálása a használt alkalmazástól függően egyszerű vagy összetett lehet. Néhány alkalmazás csak néhány művelettel konfigurálható. Másoknak részletes konfigurációra van szükségük. Az ismeretek gyors bejárásához járjon végig az alkalmazás-felügyeleti útmutató [sorozatán](view-applications-portal.md) . Ha a hozzáadott alkalmazás egyszerű, akkor valószínűleg nem kell elolvasnia ezt a cikket. Ha a hozzáadott alkalmazáshoz egyéni konfiguráció szükséges az SAML-alapú egyszeri bejelentkezéshez, akkor ez a cikk Önnek szól.

A gyors üzembe helyezési [sorozatban](add-application-portal-setup-sso.md)van egy cikk, amely az egyszeri bejelentkezés konfigurálását ismerteti. Itt megismerheti, hogyan érheti el az alkalmazás SAML-konfigurációs lapját. Az SAML-konfiguráció oldal öt szakaszt tartalmaz. Ezeket a szakaszokat részletesen ismertetjük ebben a cikkben.

> [!IMPORTANT] 
> Vannak olyan helyzetek, amikor az **egyszeri bejelentkezési** lehetőség nem fog megjelenni a navigációban a **vállalati alkalmazásokban** lévő alkalmazásokhoz. 
>
> Ha az alkalmazás a **Alkalmazásregisztrációk** használatával lett regisztrálva, akkor az egyszeri bejelentkezés funkció alapértelmezés szerint a OIDC OAuth használatára van konfigurálva. Ebben az esetben az **egyszeri bejelentkezési** lehetőség nem jelenik meg a **vállalati alkalmazások** navigációs sávján. Ha a **Alkalmazásregisztrációk** használatával adja hozzá az egyéni alkalmazást, a jegyzékfájlban konfigurálhatja a beállításokat. További információ a jegyzékfájlról: [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Az SSO-szabványokkal kapcsolatos további tudnivalókért tekintse meg a [hitelesítés és engedélyezés a Microsoft Identity platform használatával](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)című témakört. 
>
> Egyéb forgatókönyvek, amelyekben az **egyszeri bejelentkezés** hiányzik a navigálásból, ha egy alkalmazás egy másik bérlőn fut, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás-rendszergazda vagy az egyszerű szolgáltatásnév tulajdonosa). Az engedélyek olyan eseteket is okozhatnak, ahol megnyithatja az **egyszeri bejelentkezést** , de nem fogja tudni menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Alapszintű SAML-konfiguráció

Az alkalmazás gyártójától kell beolvasnia az értékeket. Manuálisan is megadhatja az értékeket, vagy feltöltheti a metaadatokat a mezők értékének kinyeréséhez.

> [!TIP]
> Számos alkalmazás már előre konfigurálva van az Azure AD-vel való együttműködésre. Ezek az alkalmazások az alkalmazások katalógusában jelennek meg, amelyekkel megkereshet egy alkalmazást az Azure AD-bérlőhöz való hozzáadásakor. A gyors üzembe helyezési [sorozat](add-application-portal-setup-sso.md) végigvezeti a folyamaton. A katalógusban található alkalmazásokhoz részletes, lépésenkénti útmutatót talál. A lépések eléréséhez kattintson az alkalmazás SAML-konfiguráció lapján található hivatkozásra, amelyet a rövid útmutatóban leírtak szerint, vagy a [SaaS app Configuration oktatóanyagokban](../saas-apps/tutorial-list.md)böngészhet az összes alkalmazás-konfigurációs oktatóanyag listájában.

| Alapszintű SAML konfigurációs beállítás | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
|:--|:--|:--|:--|
| **Azonosító (entitásazonosító)** | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedileg azonosítja az alkalmazást. Az Azure AD elküldi az azonosítót az alkalmazásnak az SAML-jogkivonat célközönségi paramétereként. Az alkalmazásnak el kell érvényesíteni. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik. Adjon meg egy URL-címet, amely a következő mintát használja: "https:// <subdomain> . contoso.com". *Ez az érték az alkalmazás által elküldhető **AuthnRequest** (SAML-kérelem) **kiállító** elemeként is megkereshető* . |
| **Válasz URL-címe** | Kötelező | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. A további válasz URL-címek mezővel több válasz URL-címet is megadhat. Előfordulhat például, hogy több altartományhoz is szüksége van további válasz URL-címekre. Vagy tesztelési célból egyszerre több válasz URL-címet (helyi gazdagépet és nyilvános URL-címeket) is megadhat. |
| **Bejelentkezési URL-cím** | Kötelező | Nincs megadva | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-cím használatával indítja el az alkalmazást Microsoft 365 vagy az Azure AD saját alkalmazásaiból. Ha üres, az Azure AD identitásszolgáltató-bejelentkezést végez, ha a felhasználó Microsoft 365, Azure AD saját alkalmazások vagy az Azure AD SSO URL-címéről indítja el az alkalmazást.|
| **Továbbítási állapot** | Választható | Választható | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe. Néhány alkalmazás azonban eltérő módon használja ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.
| **Kijelentkezési URL-cím** | Választható | Választható | Az SAML-kijelentkezési válaszok visszaküldésére szolgál az alkalmazásnak.

## <a name="user-attributes-and-claims"></a>Felhasználói attribútumok és jogcímek 

Amikor egy felhasználó hitelesíti magát az alkalmazásban, az Azure AD egy SAML-jogkivonatot bocsát ki a felhasználótól, amely egyedileg azonosítja azokat. Alapértelmezés szerint ez az információ tartalmazza a felhasználó felhasználónevét, e-mail-címét, utónevét és vezetéknevét. Előfordulhat, hogy testre kell szabnia ezeket a jogcímeket, ha például az alkalmazásnak konkrét jogcím-értékeket vagy a felhasználónévtől eltérő **nevet** kell megadni. 

> [!IMPORTANT]
> Számos alkalmazás már előre konfigurálva van, és az alkalmazás-katalógusban nem kell aggódnia a felhasználói és csoportos jogcímek beállításával kapcsolatban. A gyors üzembe helyezési [sorozat](add-application-portal.md) végigvezeti az alkalmazások hozzáadásának és konfigurálásának lépésein.


Az **egyedi felhasználói azonosító (Name ID)** azonosító értéke kötelező jogcím, és fontos. Az alapértelmezett érték a *User. userPrincipalName*. A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

Ha többet szeretne megtudni az SAML-jogcímek testreszabásáról, tekintse meg [az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz](../develop/active-directory-saml-claims-customization.md)című témakört.

Új jogcímeket adhat hozzá, további részletekért lásd: [alkalmazás-specifikus jogcímek hozzáadása](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) vagy csoport jogcímek hozzáadása. lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Az SAML-token Azure AD-ből az alkalmazásba való testreszabásának további módjaiért lásd az alábbi forrásokat.
>- Ha egyéni szerepköröket szeretne létrehozni a Azure Portal keresztül, tekintse meg a [szerepkör-jogcímek konfigurálása](../develop/active-directory-enterprise-app-role-management.md)című részt.
>- A jogcímek PowerShell használatával történő testreszabásával kapcsolatban lásd: [jogcímek testreszabása – PowerShell](../develop/active-directory-claims-mapping.md).
>- Ha módosítani szeretné az alkalmazás jegyzékfájlját az alkalmazás választható jogcímeinek konfigurálásához, tekintse meg a [választható jogcímek konfigurálása](../develop/active-directory-optional-claims.md)című témakört.
>- A jogkivonat élettartamára vonatkozó szabályzatok frissítési jogkivonatok, hozzáférési tokenek, munkamenet-tokenek és azonosító tokenek beállításával kapcsolatban lásd: [jogkivonat-élettartamok konfigurálása](../develop/active-directory-configurable-token-lifetimes.md). Ha az Azure AD feltételes hozzáférés használatával szeretné korlátozni a hitelesítési munkameneteket, tekintse meg a [hitelesítési munkamenetek kezelési képességeit](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>SAML-aláíró tanúsítvány

Az Azure AD egy tanúsítványt használ az alkalmazásnak küldött SAML-tokenek aláírásához. Erre a tanúsítványra van szüksége az Azure AD és az alkalmazás közötti megbízhatósági kapcsolat konfigurálásához. A tanúsítvány formátumával kapcsolatos részletekért tekintse meg az alkalmazás SAML-dokumentációját. További információ: [tanúsítványok kezelése összevont egyszeri bejelentkezéshez](manage-certificates-for-federated-single-sign-on.md) és [speciális tanúsítvány-aláírási beállítások az SAML-jogkivonatban](certificate-signing-options.md).

> [!IMPORTANT]
> Számos alkalmazás már előre konfigurálva van, és az alkalmazás-katalógusban nem kell belefoglalni a tanúsítványokra. A gyors üzembe helyezési [sorozat](add-application-portal.md) végigvezeti az alkalmazások hozzáadásának és konfigurálásának lépésein.

Az Azure AD-ből az aktív tanúsítvány Base64 vagy RAW formátumban tölthető le közvetlenül a fő **beállítás egyetlen Sign-On az SAML** -oldalról. Azt is megteheti, hogy az aktív tanúsítványt letölti az alkalmazás metaadatainak XML-fájljának használatával vagy az alkalmazás-összevonás metaadat-URL-címével. Ha szeretné megtekinteni, létrehozni vagy letölteni a tanúsítványokat (aktív vagy inaktív), kövesse az alábbi lépéseket.

Néhány gyakori dolog a tanúsítvány ellenőrzéséhez: 
   - *A helyes lejárati dátum.* A lejárati dátumot akár három évig is beállíthatja a jövőbe.
   - *A megfelelő tanúsítvány aktív állapota.* Ha az állapot **inaktív**, módosítsa az állapotot **aktív** értékre. Az állapot módosításához kattintson a jobb gombbal a tanúsítvány sorára, majd válassza a **tanúsítvány aktívvá tétele** lehetőséget.
   - *A helyes aláírási beállítás és algoritmus.*
   - *A helyes értesítő e-mail-cím (ek).* Ha az aktív tanúsítvány közel van a lejárati dátumhoz, az Azure AD értesítést küld az ebben a mezőben konfigurált e-mail-címre.

Esetenként előfordulhat, hogy le kell töltenie a tanúsítványt. Ügyeljen arra, hogy a mentést mégis megőrizze. A tanúsítvány letöltéséhez válassza ki a Base64 formátum, a nyers formátum vagy az összevonási metaadatok XML-fájljának egyik lehetőségét. Az Azure AD az alkalmazás- **összevonási metaadatok URL-címét** is tartalmazza, ahol az alkalmazáshoz tartozó metaadatokat a következő formátumban érheti el: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Az alkalmazásnak képesnek kell lennie a használatakor megjelenített XML-ben lévő bájtok sorrendjének kezelésére https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . A bájtok sorrendje nem nyomtatható ASCII-karakterként jelenik meg.» ¿és hexadecimális formátumban a rendszer az XML-adattartalom áttekintésekor az EF BB BF néven jelenik meg.

A tanúsítvány módosításához kattintson a Szerkesztés gombra. Az **SAML aláíró tanúsítvány** lapján többféleképpen is elvégezhető:
   - Új tanúsítvány létrehozása: válassza az **új tanúsítvány** lehetőséget, válassza ki a **lejárati dátumot**, majd kattintson a **Mentés** gombra. A tanúsítvány aktiválásához válassza a helyi menüt (**...**), majd válassza a **tanúsítvány aktívvá tétele** lehetőséget.
   - Titkos kulccsal és pfx hitelesítő adatokkal rendelkező tanúsítvány feltöltése: válassza a **tanúsítvány importálása** lehetőséget, és keresse meg a tanúsítványt. Adja meg a **pfx-jelszót**, majd kattintson a **Hozzáadás** gombra.  
   - Speciális tanúsítvány-aláírás konfigurálása. További információ ezekről a lehetőségekről: [speciális tanúsítvány-aláírási beállítások](certificate-signing-options.md).
   - További személyek értesítése, amikor az aktív tanúsítvány közel van a lejárati dátumhoz: adja meg az e-mail címeket az **értesítési e-mail címek** mezőiben.

## <a name="set-up-the-application-to-use-azure-ad"></a>Az alkalmazás beállítása az Azure AD használatára

A **beállítás \<applicationName>** szakasz felsorolja azokat az értékeket, amelyeket konfigurálni kell az alkalmazásban, hogy az Azure ad-t SAML-identitás-szolgáltatóként fogja használni. Az értékeket az alkalmazások webhely konfiguráció lapján állíthatja be. Ha például a GitHubot konfigurálja, akkor a github.com-webhelyre lép, és beállítja az értékeket. Ha az alkalmazás már előre konfigurálva van, és az Azure AD-galériában megjelenik egy hivatkozás, amely **részletes útmutatást** nyújt. Ellenkező esetben meg kell keresnie a konfigurálni kívánt alkalmazás dokumentációját. 

A **bejelentkezési URL-cím** és a **KIJELENTKEZÉSI URL-cím** mindkét esetben ugyanazt a végpontot oldja fel, amely az Azure ad-bérlő SAML-kérelem-kezelési végpontja. 

Az **Azure ad-azonosító** az alkalmazás számára kiállított SAML-token **kiállítójának** értéke.

## <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ha úgy konfigurálta az alkalmazást, hogy az Azure AD-t SAML-alapú identitás-szolgáltatóként használja, a beállítások tesztelésével ellenőrizheti, hogy az egyszeri bejelentkezés működik-e a fiókjával. 

Válassza a **teszt** lehetőséget, majd válassza ki az aktuálisan bejelentkezett felhasználóval vagy másnak való tesztelést. 

Ha a bejelentkezés sikeres, készen áll a felhasználók és csoportok hozzárendelésére az SAML-alkalmazáshoz. Gratulálunk!

Ha hibaüzenet jelenik meg, hajtsa végre a következő lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Válassza a **megoldás lekérése útmutatást**. Megjelenik az alapvető ok és a megoldási útmutató.  Ebben a példában a felhasználó nem lett hozzárendelve az alkalmazáshoz.

3. Olvassa el a megoldási útmutatót, és próbálja meg elhárítani a problémát.

4. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

További információ: [SAML-alapú egyszeri bejelentkezés hibakeresése Azure Active Directory-alkalmazásokban](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Következő lépések

- [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
- [Felhasználók vagy csoportok társítása az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Egyszeri bejelentkezéses SAML-protokoll](../develop/single-sign-on-saml-protocol.md)
