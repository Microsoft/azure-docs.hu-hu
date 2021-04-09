---
title: OpenID/OAuth-alkalmazás konfigurálása az Azure AD App Galleryből | Microsoft Docs
description: Az OpenID/OAuth alkalmazás Azure AD-katalógusból történő konfigurálásának lépései.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 32f79f24df6fe705146b39750c710450ef8f1f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735937"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>OpenID/OAuth-alkalmazás konfigurálása az Azure AD App Galleryből

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>OpenID-alkalmazás felvételének folyamata a katalógusból

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Nyissa meg a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az alkalmazás nevét. Válassza ki a kívánt alkalmazást az eredmény panelen, és regisztráljon az alkalmazásra.

    ![OpenID az eredmények listájában](common/search-new-app.png)


1. Az alkalmazás neve lapon kattintson a **regisztráció** gombra.

    ![Hozzáadás gomb](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > Itt a bérlői rendszergazdának ki kell választania a regisztrációs gombot, és meg kell adnia az alkalmazáshoz való hozzájárulásukat. Az alkalmazás ezután bekerül az ügyfél bérlőbe, ahol elvégezheti a konfigurációkat. Nem kell explicit módon felvennie az alkalmazást.

5. A rendszer átirányítja az alkalmazás bejelentkezési lapjára vagy Azure Active Directory (Azure AD) lapra a bejelentkezési hitelesítő adatokhoz.

6. A sikeres hitelesítés után elfogadja a beleegyezett a beleegyező oldalról. Ezután megjelenik az alkalmazás kezdőlapja.

    > [!NOTE]
    > Csak egy példányt adhat hozzá az alkalmazáshoz. Ha már hozzáadott egyet, és ismét próbálta megadnia a beleegyezik, akkor a bérlő nem adja hozzá újra. Így logikailag csak egy alkalmazás-példányt használhat a bérlőben.

1. Az alábbi videó használatával adjon hozzá egy OpenID-alkalmazást a katalógusból.
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat OpenID Connect használatával

A legalapvetőbb bejelentkezési folyamat a következő lépéseket tartalmazza:

![Hitelesítési folyamat OpenID Connect használatával](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Több-bérlős alkalmazás
A több-bérlős alkalmazások számos szervezet számára készültek, nem csupán egyetlen szervezet számára. Ezek jellemzően egy független szoftvergyártó (ISV) által írt, szolgáltatott szoftveres (SaaS) alkalmazások.

Több-bérlős alkalmazásokat kell kiépíteni minden olyan könyvtárban, ahol használni fogják őket. A regisztrációhoz felhasználói vagy rendszergazdai jogosultság szükséges. Ez a engedélyezési folyamat akkor indul el, amikor egy alkalmazás regisztrálva van a címtárban, és hozzáférést kap a Graph APIhoz vagy esetleg egy másik webes API-hoz. Ha egy másik szervezet felhasználói vagy rendszergazdái feliratkozik az alkalmazás használatára, megjelenik egy párbeszédpanel, amely az alkalmazás által igényelt engedélyeket jeleníti meg.

A felhasználó vagy a rendszergazda megadhatja az alkalmazásnak. A belefoglalt engedély lehetővé teszi az alkalmazás számára a megadott információhoz való hozzáférést, és végül regisztrálja az alkalmazást a címtárban.

> [!NOTE]
> Ha az alkalmazást több címtárban is elérhetővé teszi a felhasználók számára, szüksége lesz egy olyan mechanizmusra, amely meghatározza, hogy melyik bérlőn belül van. Az egybérlős alkalmazásoknak csak a saját címtárában kell megkeresniük a felhasználókat. Egy több-bérlős alkalmazásnak azonosítania kell egy adott felhasználót az Azure AD összes könyvtárában.
>
> A feladat elvégzéséhez az Azure AD egy közös hitelesítési végpontot biztosít, amelyben bármely több-bérlős alkalmazás képes a bejelentkezési kérelmeket a bérlő-specifikus végpont helyett. Ez a végpont az `https://login.microsoftonline.com/common` Azure ad összes könyvtára számára elérhető. A bérlő-specifikus végpont lehet `https://login.microsoftonline.com/contoso.onmicrosoft.com` .
>
> A közös végpontot fontos figyelembe venni az alkalmazás fejlesztésekor. A bejelentkezés, a kijelentkezés és a jogkivonat-érvényesítés során több bérlő kezeléséhez szükséges logikára lesz szüksége.

Az Azure AD alapértelmezés szerint támogatja a több-bérlős alkalmazásokat. A felhasználók könnyedén hozzáférhetnek a szervezetekhez, és a beleegyezikés elfogadását követően könnyen használhatók.

## <a name="consent-framework"></a>Jóváhagyási keretrendszer

A több-bérlős webes és natív ügyfélalkalmazások fejlesztéséhez használhatja az Azure AD-beli engedélyezési keretrendszert. Ezek az alkalmazások lehetővé teszik a felhasználói fiókok általi bejelentkezést egy Azure AD-bérlőtől, amely eltér az alkalmazás regisztrálásának helyétől. Előfordulhat, hogy a következő webes API-kat is el kell érnie:
- Az Microsoft Graph API, amely az Azure AD, az Intune és a szolgáltatások elérésére Microsoft 365.
- Más Microsoft-szolgáltatások API-jai.
- Saját webes API-k.

A keretrendszer egy felhasználó vagy egy rendszergazda azon alapul, amely beleegyezik egy olyan alkalmazásba, amely a címtárban való regisztrálást kéri. Előfordulhat, hogy a regisztráció a címtáradatok elérését is magában foglalja. A beleegyező engedély megadása után az ügyfélalkalmazás meghívja a Microsoft Graph API-t a felhasználó nevében, és igény szerint használhatja az információt.

A [Microsoft Graph API](https://developer.microsoft.com/graph/) Microsoft 365hoz biztosít hozzáférést, például:

- Naptárak és üzenetek az Exchange-ből.
- Helyek és listázza a SharePointból.
- Dokumentumok a OneDrive.
- Jegyzetfüzetek a OneNote-ból.
- A Planner feladatai.
- Excel-munkafüzetek.

A Graph API hozzáférést biztosít a felhasználók és csoportok számára az Azure AD-ből és más, a Microsoft Cloud servicesből származó adatobjektumokból is.

Az alábbi lépések bemutatják, hogyan működik az alkalmazás fejlesztője és felhasználója a beleegyezett felhasználói élményben:

1. Tegyük fel, hogy van egy webes ügyfélalkalmazás, amelynek konkrét engedélyeket kell kérnie egy erőforrás vagy API eléréséhez. A Azure Portal a rendszer a konfigurációs időpontban az engedélyezési kérelmek bejelentésére szolgál. Más konfigurációs beállításokhoz hasonlóan az alkalmazás Azure AD-regisztrációi is részévé válnak. Az engedély kérésének elérési útjához az alábbi lépéseket kell követnie:

    a. Kattintson a menü bal oldalán lévő **Alkalmazásregisztrációkra** , és nyissa meg az alkalmazást a keresőmezőbe írja be az alkalmazás nevét.

    ![A bal oldali menüből kiválasztott "Alkalmazásregisztrációk" és az "Application I D" keresőmező kijelölve.](./media/openidoauth-tutorial/application.png)

    b. Kattintson az **API-engedélyek megtekintése** elemre.

    ![Képernyőfelvétel: a "P i: P i/o-engedélyek megtekintése" gomb megjelenítése.](./media/openidoauth-tutorial/api-permission.png)

    c. Kattintson az **engedély hozzáadása** lehetőségre.

    ![Képernyőfelvétel: az "a P I engedélyek" szakasz, amelyen be van jelölve az "engedély hozzáadása" gomb.](./media/openidoauth-tutorial/add-permission.png)

    d. Kattintson **Microsoft Graph**.

    ![Képernyőfelvétel: "a P I engedélyek kérése" oldal a "Microsoft A P I" lapon és a "Microsoft Graph" csempe kijelölve.](./media/openidoauth-tutorial/microsoft-graph.png)

    e. A **delegált engedélyek** és az **alkalmazás engedélyei** közül válassza a szükséges beállítások lehetőséget.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vegye figyelembe, hogy az alkalmazás engedélyei frissültek. Az alkalmazás fut, és a felhasználó első alkalommal hamarosan használatba kerül. Először az alkalmazásnak be kell szereznie egy engedélyezési kódot az Azure AD/Authorize-végpontból. Az engedélyezési kód ezután új hozzáférési és frissítési jogkivonat beszerzésére használható.

3. Ha a felhasználó még nincs hitelesítve, az Azure AD/Authorize-végpont bekéri a bejelentkezést.

    ![A fiók bejelentkezési kérésének képernyőképe](./media/openidoauth-tutorial/authentication.png)

4. Miután a felhasználó bejelentkezett, az Azure AD megállapítja, hogy a felhasználónak meg kell-e jelenítenie egy beleegyező lapot. Ez a meghatározás azon alapul, hogy a felhasználó (vagy a szervezet rendszergazdája) már megadta-e az alkalmazáshoz való hozzájárulásukat.

   Ha nem adta meg a hozzájárulást, az Azure AD belekéri a felhasználót a jóváhagyásra, és megjeleníti a szükséges engedélyeket a működéséhez. A beleegyezés párbeszédpanelen megjelenő engedélyek megegyeznek a Azure Portal delegált engedélyeiben kiválasztott engedélyekkel.

    ![Beleegyezett oldal](./media/openidoauth-tutorial/consentpage.png)

A normál felhasználók bizonyos engedélyekkel rendelkezhetnek. A többi engedélyhez bérlői rendszergazda beleegyeznie kell.

## <a name="difference-between-admin-consent-and-user-consent"></a>A rendszergazdai beleegyezés és a felhasználói beleegyezés közötti különbség

Rendszergazdaként a bérlő összes felhasználója nevében jóváhagyhatja az alkalmazás delegált engedélyeit is. A rendszergazdai jogosultság meggátolja, hogy a megjelenő párbeszédpanel minden felhasználó számára megjelenjen a bérlőben. A rendszergazdai szerepkörrel rendelkező felhasználók hozzájárulnak a Azure Portal. Az alkalmazás **Beállítások** lapján válassza a rendszergazdai jóváhagyás **szükséges engedélyek**  >  **megadása** lehetőséget.

![Engedélyek megadása gomb](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A kifejezett beleegyezés megadása a **rendszergazdai jóváhagyás megadása** gomb megadásával megtörténik a ADAL.jst használó egylapos alkalmazások (SPAs) használata. Ellenkező esetben az alkalmazás a hozzáférési jogkivonat kérése esetén meghiúsul.

Csak az alkalmazásra vonatkozó engedélyek szükségesek a bérlői rendszergazda beleegyezni. Ha az alkalmazás csak alkalmazásra vonatkozó engedélyt kér, és egy felhasználó megpróbál bejelentkezni az alkalmazásba, hibaüzenet jelenik meg. Az üzenet azt mondja, hogy a felhasználó nem tud beleegyezni.

Ha az alkalmazás rendszergazdai jogosultságot igénylő engedélyeket használ, olyan kézmozdulattal kell rendelkeznie, mint például egy gomb vagy egy hivatkozás, amelyben a rendszergazda el tudja indítani a műveletet. Az alkalmazás által a művelethez küldött kérelem a szokásos OAuth2/OpenID Connect engedélyezési kérelem. Ez a kérelem tartalmazza a *prompt = admin_consent* lekérdezési karakterlánc paramétert.

Miután a rendszergazda beleegyezett, és az ügyfél bérlője létrehozta a szolgáltatásnevet, a későbbi bejelentkezési kérésekhez nem szükséges a *prompt = admin_consent* paraméter. Mivel a rendszergazda úgy döntött, hogy a kért engedélyek elfogadhatók, a bérlőn kívül más felhasználókat sem kell megadnia az adott időponthoz.

A bérlői rendszergazdák letilthatják, hogy a felhasználók miként tudnak hozzájárulni az alkalmazásokhoz. Ha ez a funkció le van tiltva, a rendszergazda beleegyezik, hogy az alkalmazás a bérlőben legyen használatban. Ha tesztelni szeretné az alkalmazást a végfelhasználói beleegyezikés letiltásával, a [Azure Portalban](https://portal.azure.com/)találhatja meg a konfigurációs kapcsolót. Ez a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) szakaszban, a **vállalati alkalmazások** területen található.

A *prompt = admin_consent* paramétert olyan alkalmazások is felhasználhatják, amelyek nem igénylik a rendszergazdai jóváhagyást. Ilyen például egy olyan alkalmazás, amely egy olyan felhasználói élményt igényel, amelyben a bérlői rendszergazda "feliratkozik", és a rendszer nem kér más felhasználókat az adott ponttól.

Képzelje el, hogy egy alkalmazásnak rendszergazdai jóváhagyásra van szüksége, és egy rendszergazda bejelentkezik a *prompt = admin_consent* paraméter küldése nélkül. Ha a rendszergazda sikeresen beleegyezett az alkalmazásba, csak a felhasználói fiókjára vonatkozik. A normál felhasználók továbbra sem tudnak bejelentkezni vagy beleegyezni az alkalmazásba. Ez a funkció akkor hasznos, ha azt szeretné, hogy a bérlői rendszergazda meg tudja vizsgálni az alkalmazást, mielőtt engedélyezné más felhasználók hozzáférését.

## <a name="next-steps"></a>Következő lépések

[OIDC-alapú egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben](../manage-apps/add-application-portal-setup-oidc-sso.md)