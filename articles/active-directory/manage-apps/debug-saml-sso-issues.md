---
title: SAML-alapú egyszeri bejelentkezés hibakeresése – Azure Active Directory
description: SAML-alapú egyszeri bejelentkezés hibakeresése a Azure Active Directory.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374318"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Az Azure Active Directoryban található alkalmazásokba történő SAML-alapú egyszeri bejelentkezés hibaelhárítása

Megtudhatja, hogyan találhatja meg és javíthatja ki az [SAML-alapú](what-is-single-sign-on.md) egyszeri bejelentkezést Azure Active Directory (Azure AD) szolgáltatásban található alkalmazások egyszeri bejelentkezési problémáit. 

## <a name="before-you-begin"></a>Előkészületek

Javasoljuk, hogy telepítse [Saját alkalmazások biztonságos bejelentkezési bővítményt.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) Ez a böngészőbővítmény megkönnyíti az EGYSZERI bejelentkezéssel kapcsolatos problémák megoldásához szükséges SAML-kérések és SAML-válaszinformációk összegyűjtését. Ha nem tudja telepíteni a bővítményt, ez a cikk bemutatja, hogyan oldhatja meg a bővítménysel és anélkül kapcsolatos problémákat.

A biztonságos bejelentkezési bővítmény Saját alkalmazások és telepítéséhez használja az alábbi hivatkozások egyikét.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés tesztelése

SAML-alapú egyszeri bejelentkezés tesztelése az Azure AD és egy célalkalmazás között:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként vagy más rendszergazdaként, aki jogosult az alkalmazások kezelésére.
1. A bal oldali panelen válassza **a** Azure Active Directory, majd a Vállalati **alkalmazások lehetőséget.** 
1. A vállalati alkalmazások listájából válassza ki azt az alkalmazást, amelynek egyszeri bejelentkezését tesztelni szeretné, majd a bal oldali lehetőségek közül válassza az Egyszeri bejelentkezés **lehetőséget.**
1. Az SAML-alapú egyszeri bejelentkezés tesztelési folyamatának megnyitásához lépjen az Egyszeri **bejelentkezés** tesztelése (5. lépés) pontra. Ha **a Teszt** gomb szürkével van kiszürkülve, először ki kell töltenie és mentenie kell a szükséges attribútumokat az **SAML-alapkonfiguráció** szakaszban.
1. Az **Egyszeri bejelentkezés tesztelése panelen** a vállalati hitelesítő adatokkal jelentkezzen be a célalkalmazásba. Bejelentkezhet aktuális felhasználóként vagy másik felhasználóként. Ha másik felhasználóként jelentkezik be, a rendszer kérni fogja a hitelesítést.

    ![Képernyőkép az SAML SSO tesztoldalról](./media/debug-saml-sso-issues/test-single-sign-on.png)

Ha sikeresen bejelentkezett, a teszt sikeres volt. Ebben az esetben az Azure AD egy SAML-választokent adott ki az alkalmazásnak. Az alkalmazás az SAML-jogkivonattal sikeresen bejelentkeztet.

Ha a vállalati bejelentkezési oldalon vagy az alkalmazás oldalán hiba lép fel, a következő szakaszok egyikének használatával hárítsa el a hibát.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Bejelentkezési hiba megoldása a vállalati bejelentkezési oldalon

Amikor megpróbál bejelentkezni, a következő példához hasonló hibaüzenet jelenhet meg a vállalati bejelentkezési oldalon.

![A vállalati bejelentkezési oldalon megjelenő hibát bemutató példa](./media/debug-saml-sso-issues/error.png)

A hibakereséshez szüksége lesz a hibaüzenetre és az SAML-kérelemre. A Saját alkalmazások biztonságos bejelentkezési bővítmény automatikusan összegyűjti ezt az információt, és megoldási útmutatást jelenít meg az Azure AD-ről.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>A bejelentkezési hiba megoldása a telepített Saját alkalmazások biztonságos bejelentkezési bővítménysel

1. Hiba esetén a bővítmény visszairányít az Azure **AD-teszt egyszeri bejelentkezési** paneljére.
1. Az Egyszeri **bejelentkezés tesztelése panelen** válassza az **SAML-kérelem letöltése lehetőséget.**
1. A hiba és az SAML-kérelemben megadott értékek alapján konkrét megoldási útmutatást kell látnia.
1. Megjelenik a **Javítás gomb,** amely automatikusan frissíti a konfigurációt az Azure AD-ban a probléma megoldása érdekében. Ha nem látja ezt a gombot, akkor a bejelentkezési problémát nem az Azure AD helytelen konfigurációja miatt jelentkezik.

Ha nincs megoldás a bejelentkezési hibára, javasoljuk, hogy a visszajelzési szövegmezőben értesítsen minket.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>A hiba megoldása a Saját alkalmazások biztonságos bejelentkezési bővítmény telepítése nélkül

1. Másolja ki a lap jobb alsó sarkában található hibaüzenetet. A hibaüzenet a következőket tartalmazza:
    - Korrelációsazonosító és időbélyeg. Ezek az értékek azért fontosak, ha támogatási esetet hoz létre a Microsofttal, mert segítenek a mérnököknek azonosítani a problémát, és pontos megoldást nyújtani a problémára.
    - A probléma gyökerét azonosító utasítás.
1. Vissza Azure AD-be, és keresse meg az Egyszeri **bejelentkezés tesztelése panelt.**
1. Illessze be a hibaüzenetet a **megoldási útmutató feletti** szövegmezőbe.
1. Kattintson **a Megoldási útmutató lekért** elemre a probléma megoldásának lépései megjelenítéséhez. Az útmutatáshoz szükség lehet az SAML-kérelem vagy az SAML-válasz információira. Ha nem használja a Saját alkalmazások biztonságos bejelentkezési bővítményt, szüksége lehet egy olyan eszközre, mint a [Fiddler](https://www.telerik.com/fiddler) az SAML-kérés és -válasz lekéréshez.
1. Ellenőrizze, hogy az SAML-kérelemben megadott célhely megfelel-e az Azure AD-től Sign-On SAML single Sign-On service URL-címének.
1. Ellenőrizze, hogy az SAML-kérelem kiállítója ugyanaz-e az azonosító, mint amit az Azure AD-beli alkalmazáshoz konfigurált. Az Azure AD a kiállító használatával keres egy alkalmazást a címtárban.
1. Ellenőrizze, hogy az assertionConsumerServiceURL az a hely, ahol az alkalmazás az SAML-jogkivonatot várja az Azure AD-től. Ezt az értéket konfigurálhatja az Azure AD-ben, de nem kötelező, ha az SAML-kérelem része.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Bejelentkezési hiba elhárítása az alkalmazás oldalán

Előfordulhat, hogy sikeresen bejelentkezik, és hibaüzenet jelenik meg az alkalmazás oldalán. Ez akkor fordul elő, ha az Azure AD jogkivonatot adott ki az alkalmazásnak, de az alkalmazás nem fogadja el a választ.

A hiba elhárításához kövesse az alábbi lépéseket, vagy tekintse meg ezt a rövid videót arról, hogyan használható az Azure AD az [SAML SSO hibaelhárításához:](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)

1. Ha az alkalmazás az Azure AD-katalógusban található, ellenőrizze, hogy követte-e az alkalmazás Azure AD-val való integrálásának összes lépését. Az alkalmazás integrációs utasításait az SaaS-alkalmazásintegrációs oktatóanyagok listájában [találja.](../saas-apps/tutorial-list.md)
1. Az SAML-válasz lekérése.
    - Ha a Saját alkalmazások biztonságos bejelentkezés bővítmény telepítve van, az Egyszeri bejelentkezés tesztelése panelen kattintson az **SAML-válasz letöltésére.** 
    - Ha a bővítmény nincs telepítve, egy olyan eszközzel, mint a [Fiddler,](https://www.telerik.com/fiddler) lekéri az SAML-választ.
1. Figyelje meg az SAML-válasz jogkivonatának alábbi elemeit:
   - A NameID érték és formátum egyedi felhasználóazonosítója
   - A jogkivonatban kiadott jogcímek
   - A jogkivonat aláíráshoz használt tanúsítvány.

     További információ az SAML-válaszról: Egyszeri bejelentkezéses [SAML protokoll.](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

1. Most, hogy áttekintotta az SAML-választ, tekintse meg [a](application-sign-in-problem-application-error.md) Hiba egy alkalmazás oldalán a bejelentkezés után oldalt, amely útmutatást nyújt a probléma megoldásához. 
1. Ha továbbra sem tud sikeresen bejelentkezni, megkérdezheti az alkalmazás gyártójától, hogy mi hiányzik az SAML-válaszból.

## <a name="next-steps"></a>Következő lépések

Most, hogy az egyszeri bejelentkezés működik az alkalmazással, automatizálhatja a felhasználókiépítést és -kiépítést [a SaaS-alkalmazásokban,](../app-provisioning/user-provisioning.md) vagy elkezdhet a feltételes [hozzáféréssel.](../conditional-access/app-based-conditional-access.md)
