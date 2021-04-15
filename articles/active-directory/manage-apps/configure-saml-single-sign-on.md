---
title: Az SAML-alapú egyszeri bejelentkezés (SSO) használata a Azure Active Directory
description: Az SAML-alapú egyszeri bejelentkezés (SSO) használata a Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374488"
---
# <a name="understand-saml-based-single-sign-on"></a>Az SAML-alapú egyszeri bejelentkezés

Az [alkalmazáskezelés](view-applications-portal.md) gyorsútmutató-sorozatában megtanulta, hogyan használhatja az Azure AD-t identitásszolgáltatóként (IdP) egy alkalmazáshoz. Ez a cikk az egyszeri bejelentkezés SAML-alapú beállítását ismerteti részletesebben. 


## <a name="before-you-begin"></a>Előkészületek

Az Azure AD identitásszolgáltatóként (IdP) való használata és az egyszeri bejelentkezés (SSO) konfigurálása a használt alkalmazástól függően egyszerű vagy összetett lehet. Egyes alkalmazások mindössze néhány művelettel konfigurálhatóak. Mások részletes konfigurálást igényelnek. A tudás gyors kigyomlálása érdekében végig kell mennie az [alkalmazáskezelés gyorsútmutató-sorozatán.](view-applications-portal.md) Ha a hozzáadott alkalmazás egyszerű, akkor valószínűleg nem kell elolvasni ezt a cikket. Ha a hozzáadnia kell egy egyéni konfigurációt az SAML-alapú SSO-hez, akkor ez a cikk Önnek készült.

A [gyorsútmutató-sorozatban](add-application-portal-setup-sso.md)az egyszeri bejelentkezés konfigurálását egy cikk írja le. Ebből megtudhatja, hogyan férhet hozzá egy alkalmazás SAML konfigurációs oldalára. Az SAML konfigurációs oldala öt szakaszt tartalmaz. Ezeket a szakaszokat ebben a cikkben tárgyaljuk részletesen.

> [!IMPORTANT] 
> Vannak olyan forgatókönyvek,  amelyekben az Egyszeri bejelentkezés lehetőség nem lesz jelen a vállalati alkalmazásokban az alkalmazások **navigációs sávján.** 
>
> Ha az alkalmazást a **Alkalmazásregisztrációk,** akkor az egyszeri bejelentkezés alapértelmezés szerint az OIDC OAuth használatára van konfigurálva. Ebben az esetben az **Egyszeri bejelentkezés** lehetőség nem fog ni a Vállalati alkalmazások alatti **navigációs sávon.** Ha egyéni alkalmazást **Alkalmazásregisztrációk** a jegyzékfájlban, a beállításokat a jegyzékfájlban kell konfigurálnia. A jegyzékfájllal kapcsolatos további információkért lásd az Azure Active Directory [jegyzékfájlt.](../develop/reference-app-manifest.md) Az SSO-szabványokkal kapcsolatos további információkért lásd: Hitelesítés és engedélyezés [a Microsoft identitásplatformján.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Egyéb forgatókönyvek,  amelyekben az egyszeri bejelentkezés hiányzik a navigációs menüből, például ha egy alkalmazást egy másik bérlő üzemeltet, vagy ha a fiókja nem rendelkezik a szükséges engedélyekkel (globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa). Az engedélyek olyan forgatókönyvet is okozhatnak, amelyben meg lehet nyitni az egyszeri **bejelentkezést,** de nem lehet menteni. További információ az Azure AD rendszergazdai szerepköreiről: ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Alapszintű SAML-konfiguráció

Az értékeket az alkalmazás gyártójától kell lekérte. Manuálisan megadhatja az értékeket, vagy feltölthet egy metaadatfájlt a mezők értékének kinyerése érdekében.

> [!TIP]
> Számos alkalmazás már előre konfigurálva van az Azure AD-val való használatra. Ezek az alkalmazások megjelenik a katalógusban, amelyek között böngészhet, amikor alkalmazást ad hozzá az Azure AD-bérlőhöz. A [gyorsútmutató-sorozat](add-application-portal-setup-sso.md) végigvezeti a folyamaton. A katalógusban található alkalmazások részletes, részletes utasításokat tartalmaznak. A lépések eléréséhez kattintson az alkalmazás SAML-konfigurációs oldalán található hivatkozásra a rövid útmutató sorozatban leírtak szerint, vagy az összes alkalmazáskonfigurációs oktatóanyag listáját az SaaS-alkalmazáskonfigurációs oktatóanyagok között [böngészheti.](../saas-apps/tutorial-list.md)

| Az SAML alapszintű konfigurációs beállítása | SP által kezdeményezve | Identitásszolgáltató által kezdeményezve | Leírás |
|:--|:--|:--|:--|
| **Azonosító (entitásazonosító)** | Néhány alkalmazáshoz szükséges | Néhány alkalmazáshoz szükséges | Egyedileg azonosítja az alkalmazást. Az Azure AD az SAML-jogkivonat Célközönség paramétereként küldi el az azonosítót az alkalmazásnak. Az alkalmazásnak érvényesítenie kell azt. Ez az érték az alkalmazás által megadott SAML-metaadatok entitásazonosítójaként is megjelenik. Adjon meg egy URL-címet, amely a következő mintát használja: "https:// .contoso.com" Ezt az értéket az alkalmazás által küldött <subdomain> ***AuthnRequest** (SAML-kérelem)* Kiállító elemeként találhatja meg. |
| **Válasz URL-cím** | Kötelező | Kötelező | Megadja, hogy az alkalmazás hová várja az SAML-jogkivonatot. A válasz URL-címet más néven a tényfeldolgozó szolgáltatás (Assertion Consumer Service, ACS) URL-címének hívják. A további válasz URL-cím mezőkkel több válasz URL-címet is megadhat. Előfordulhat például, hogy több altartományhoz is szüksége lesz további válasz URL-címekre. Tesztelési célokra egyszerre több válasz-URL-t is megadhat (helyi gazdagép és nyilvános URL-címek). |
| **Bejelentkezési URL-cím** | Kötelező | Ne adja meg | Amikor egy felhasználó megnyitja ezt az URL-címet, a szolgáltató átirányítja az Azure AD-re a felhasználó hitelesítése és beléptetése érdekében. Az Azure AD az URL-cím használatával indítja el az alkalmazást Microsoft 365 Azure AD-Saját alkalmazások. Ha üres, az Azure AD egy idP által kezdeményezett bejelentkezést kezdeményez, amikor egy felhasználó elindítja az alkalmazást az Microsoft 365-ból, az Azure AD Saját alkalmazások-ről vagy az Azure AD SSO URL-címről.|
| **Továbbítási állapot** | Választható | Választható | Megadja az alkalmazásnak, hogy hová irányítsa át a felhasználót a hitelesítés befejezése után. Az érték általában az alkalmazás érvényes URL-címe. Egyes alkalmazások azonban másként használják ezt a mezőt. További információt az alkalmazás forgalmazójától kérhet.
| **Kijelentkezés URL-címe** | Választható | Választható | Az SAML-kijelentkezés válaszait küldi vissza az alkalmazásnak.

## <a name="user-attributes-and-claims"></a>Felhasználói attribútumok és jogcímek 

Amikor egy felhasználó hitelesíti magát az alkalmazásban, az Azure AD SAML-jogkivonatot ad ki az alkalmazásnak a felhasználóval kapcsolatos, egyedi azonosító adatokkal (vagy jogcímekkel). Alapértelmezés szerint ezek az adatok tartalmazzák a felhasználó felhasználónevét, e-mail-címét, vezetéknevét és vezetéknevét. Előfordulhat, hogy testre kell szabni ezeket **a** jogcímeket, ha például az alkalmazásnak konkrét jogcímértékeket vagy a felhasználónévtől különböző Név formátumot kell használnia. 

> [!IMPORTANT]
> Számos alkalmazás már előre konfigurálva van az alkalmazás-katalógusban, és nem kell felhasználói és csoport jogcímeket beállítania. A [gyorsútmutató-sorozat](add-application-portal.md) végigvezeti az alkalmazások hozzáadásán és konfigurálásán.


Az **egyedi felhasználói azonosító (névazonosító)** azonosító értéke kötelező jogcím, és fontos. Az alapértelmezett érték *a user.userprincipalname.* A felhasználói azonosító egyedi módon azonosítja az alkalmazás egyes felhasználóit. Ha például az e-mail-cím a felhasználónév és az egyedi azonosító is egyben, állítsa be a *user.mail* értéket.

Az SAML-jogcímek testreszabásával kapcsolatos további információkért [lásd: How to: customize claims issued in the SAML token for enterprise applications](../develop/active-directory-saml-claims-customization.md)(Az SAML-jogkivonatban kibocsátott jogcímek testreszabása vállalati alkalmazásokhoz).

Hozzáadhat új jogcímeket. További részletekért lásd: [Alkalmazásspecifikus](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) jogcímek hozzáadása vagy csoport jogcímek [hozzáadása: Csoport jogcímek konfigurálása.](../hybrid/how-to-connect-fed-group-claims.md)


> [!NOTE]
> Az SAML-jogkivonat Azure AD-ból az alkalmazásba való testreszabásának további módjaiért tekintse meg a következő erőforrásokat.
>- Egyéni szerepkörök létrehozásához a Azure Portal [lásd: Szerepköri jogcímek konfigurálása.](../develop/active-directory-enterprise-app-role-management.md)
>- A jogcímek PowerShellen keresztüli testreszabásához lásd: [Jogcímek testreszabása – PowerShell.](../develop/active-directory-claims-mapping.md)
>- Ha módosítani szeretné az alkalmazásjegyzéket, hogy konfigurálja az alkalmazás választható jogcímeit, tekintse meg a választható [jogcímek konfigurálásával kapcsolatos részben.](../develop/active-directory-optional-claims.md)
>- A frissítési jogkivonatok, hozzáférési jogkivonatok, munkamenet-jogkivonatok és azonosító jogkivonatok élettartam-szabályzatának beállítását lásd: Jogkivonatok élettartamának [konfigurálása.](../develop/active-directory-configurable-token-lifetimes.md) Vagy az Azure AD feltételes hozzáférésen keresztüli hitelesítési munkamenetek korlátozását lásd: Hitelesítési [munkamenet-kezelési képességek.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="saml-signing-certificate"></a>SAML aláíró tanúsítvány

Az Azure AD tanúsítvánnyal írja alá az alkalmazásnak küldött SAML-jogkivonatokat. Erre a tanúsítványra szüksége lesz az Azure AD és az alkalmazás közötti megbízhatóság konfigurálához. A tanúsítvány formátumával kapcsolatos részletekért tekintse meg az alkalmazás SAML-dokumentációját. További információ: [Tanúsítványok](manage-certificates-for-federated-single-sign-on.md) kezelése összevont egyszeri bejelentkezéshez és Speciális tanúsítvány-aláírási beállítások az [SAML-jogkivonatban.](certificate-signing-options.md)

> [!IMPORTANT]
> Számos alkalmazás már előre konfigurálva van az alkalmazásgyűjteményben, így nem kell a tanúsítványokba belemerülni. A [gyorsútmutató-sorozat](add-application-portal.md) végigvezeti az alkalmazások hozzáadásának és konfigurálásának a menetében.

Az Azure AD-ból letöltheti az aktív tanúsítványt Base64 vagy Raw formátumban közvetlenül a Set up Single Sign-On with SAML (Egyszeri tanúsítvány beállítása **SAML-sel) lapról.** Az aktív tanúsítványt az alkalmazás metaadatainak XML-fájljának letöltésével vagy az alkalmazás összevonási metaadatainak URL-címével is lekérheti. A tanúsítványok megtekintéséhez, létrehozásához vagy letöltéséhez (aktív vagy inaktív) kövesse az alábbi lépéseket.

Néhány gyakori dolog, amit ellenőrizni kell a tanúsítvány ellenőrzéséhez: 
   - *A helyes lejárati dátum.* A lejárati dátumot a jövőben legfeljebb három évre konfigurálhatja.
   - *A megfelelő tanúsítvány aktív állapota.* Ha az állapot **Inaktív,** módosítsa az állapotot Aktív **állapotra.** Az állapot módosításhoz kattintson a jobb gombbal a tanúsítvány sorra, és válassza a **Tanúsítvány aktívvá változtatása lehetőséget.**
   - *A megfelelő aláírási lehetőség és algoritmus.*
   - *A megfelelő értesítési e-mail-cím(e)t.* Ha az aktív tanúsítvány közel van a lejárati dátumhoz, az Azure AD értesítést küld az ebben a mezőben konfigurált e-mail-címre.

Előfordulhat, hogy le kell töltenie a tanúsítványt. Legyen óvatos, hogy hová mentse! A tanúsítvány letöltéséhez válasszon egyet a Base64 formátum, a Nyers formátum vagy az Összevonási metaadatok XML-fájlja lehetőségek közül. Az Azure AD az alkalmazás **összevonási** metaadatainak URL-címét is biztosítja, ahol az alkalmazáshoz tartozó metaadatokat a következő formátumban lehet elérni: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Az alkalmazásnak képesnek kell lennie a használata során megjelenített XML-fájlban lévő bájtrendelési jelölő https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} kezelésére. A bájtok rendelési jelölése nem nyomtatható ASCII-karakterként (» karakterként, Hexályban pedig EFALMA BF-ként) ábrázolható az XML-adatok áttekintésekor.

A tanúsítvány módosításaihoz kattintson a Szerkesztés gombra. Az **SAML-aláíró** tanúsítvány lapon több dolgot is lehet tenni:
   - Hozzon létre egy új tanúsítványt: válassza az **Új tanúsítvány** lehetőséget, válassza a **Lejárati dátum** lehetőséget, majd kattintson a Mentés **gombra.** A tanúsítvány aktiválásához válassza a helyi menüt (**...**), majd a **Make certificate active (Tanúsítvány aktívvá tenni) lehetőséget.**
   - Titkos kulccsal és pfx hitelesítő adatokkal feltöltött tanúsítvány: válassza a Tanúsítvány **importálása** lehetőséget, és keresse meg a tanúsítványt. Adja meg a **PFX-jelszót,** majd válassza a **Hozzáadás lehetőséget.**  
   - Speciális tanúsítvány-aláírás konfigurálása. További információ ezekről a beállításokról: [Speciális tanúsítvány-aláírási beállítások.](certificate-signing-options.md)
   - Értesítsen további személyeket, ha az aktív tanúsítvány a lejárati dátumhoz közeledik: adja meg az e-mail-címeket az **Értesítési e-mail-címek mezőben.**

## <a name="set-up-the-application-to-use-azure-ad"></a>Az alkalmazás beállítása az Azure AD használatára

A **Beállítás \<applicationName> szakasz** azokat az értékeket sorolja fel, amelyek az alkalmazásban konfigurálva lesznek, hogy az Azure AD-t használja SAML-identitásszolgáltatóként. Az értékeket az alkalmazások webhelyének konfigurációs oldalán állíthatja be. Ha például a GitHubot konfigurálja, akkor a webhely github.com meg az értékeket. Ha az alkalmazás már előre konfigurálva van, és az Azure AD-katalógusban található, akkor talál egy hivatkozást, amely a részletes utasítások **megtekintésére szolgál.** Ellenkező esetben meg kell találnia a konfigurált alkalmazás dokumentációját. 

A **bejelentkezési URL-cím** és a **kijelentkezés URL-címe** is ugyanannak a végpontnak a feloldása, amely az Azure AD-bérlő SAML-kérelemkezelési végpontja. 

Az **Azure AD-azonosító** az  alkalmazásnak kiadott SAML-jogkivonat kiállítójának értéke.

## <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Miután konfigurálta az alkalmazást az Azure AD SAML-alapú identitásszolgáltatóként való használatára, tesztelheti a beállításokat, és ellenőrizze, hogy működik-e az egyszeri bejelentkezés a fiókjához. 

Válassza **a Tesztelés** lehetőséget, majd válassza az aktuálisan bejelentkezett felhasználóval vagy valaki másként való tesztelést. 

Ha a bejelentkezés sikeres, készen áll arra, hogy felhasználókat és csoportokat rendeljen az SAML-alkalmazáshoz. Gratulálunk!

Ha hibaüzenet jelenik meg, kövesse az alábbi lépéseket:

1. Másolja ki és illessze be a részleteket a **hiba részleteit ismertető** mezőbe.

    ![Feloldási útmutatás beolvasása](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Válassza **a Megoldási útmutató lekért lehetőséget.** Megjelenik a kiváltó ok és a megoldási útmutató.  Ebben a példában a felhasználó nem lett hozzárendelve az alkalmazáshoz.

3. Olvassa el a megoldási útmutatót, majd próbálja meg elhárítani a problémát.

4. Futtassa ismét a tesztet, amíg sikeresnek nem bizonyul.

További információ: [SAML-alapú](./debug-saml-sso-issues.md)egyszeri bejelentkezés hibakeresése az alkalmazásokban a Azure Active Directory.


## <a name="next-steps"></a>Következő lépések

- [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](./assign-user-or-group-access-portal.md)
- [Felhasználói fiókok automatikus üzembe építésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Egyszeri bejelentkezéses SAML-protokoll](../develop/single-sign-on-saml-protocol.md)
