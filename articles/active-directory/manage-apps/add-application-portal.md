---
title: 'Rövid útmutató: Alkalmazás hozzáadása a Azure Active Directory (Azure AD-) bérlőhöz'
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: d7827d6419e4d820f8aca482df2cfae83eaf319d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378890"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: Alkalmazás hozzáadása a Azure Active Directory (Azure AD)-bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások nagy része valószínűleg már a katalógusban van.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Konfigurálja az alkalmazás tulajdonságait.
- Az alkalmazás felhasználói hozzáférésének kezelése feltételes hozzáférési szabályzat használatával.
- Konfigurálja az egyszeri bejelentkezést, hogy a felhasználók Azure AD-beli hitelesítő adataik használatával jelentkezzenek be az alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

Ha alkalmazást szeretne hozzáadni az Azure AD-bérlőhöz, a következőre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A következő szerepkörök egyike: globális rendszergazda, felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda vagy a szolgáltatásnév tulajdonosa.
- (Nem kötelező: Az alkalmazások [megtekintésének befejezése).](view-applications-portal.md)

>[!IMPORTANT]
>Javasoljuk, hogy ne éles környezetben tesztelje a rövid útmutató lépéseit.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. A bal [Azure Portal](https://portal.azure.com)panelen válassza a Azure Active Directory **lehetőséget.**
2. A **Azure Active Directory** válassza a Vállalati **alkalmazások lehetőséget.** Megnyílik **a Minden alkalmazás** panel, és egy véletlenszerű mintát jelenít meg az Azure AD-bérlőben.
3. A Vállalati **alkalmazások panelen** válassza az Új **alkalmazás lehetőséget.** 
    ![Katalógusalkalmazás bérlőhöz való hozzáadásához válassza az Új alkalmazás lehetőséget](media/add-application-portal/new-application.png)
4. Váltson az új előzetes verziójú katalógusra: Az Alkalmazás hozzáadása oldal tetején található szalagcímen válassza a Kattintson ide az új és továbbfejlesztett alkalmazáskatatár **kipróbálása feliratot.**
5. Megnyílik **az Azure AD-katalógus tallózása (előzetes verzió)** panel, és megjelenik a felhőplatformok, a helyszíni alkalmazások és a kiemelt alkalmazások csempéi. A Kiemelt alkalmazások **szakaszban** felsorolt alkalmazások ikonokkal jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és a kiépítést. 
    ![Alkalmazás keresése név vagy kategória alapján](media/add-application-portal/browse-gallery.png)
6. Tallózással megkeresheti a katalógusban a hozzáadni kívánt alkalmazást, vagy a keresőmezőbe beírásával megkeresheti az alkalmazást. Ezután válassza ki az alkalmazást az eredmények közül. 
7. A következő lépés attól függ, hogy az alkalmazás fejlesztője hogyan valósított meg egyszeri bejelentkezést (SSO). Az egyszeri bejelentkezést az alkalmazásfejlesztők négy módon valósítják meg. A négy módszer az SAML, OpenID Connect, a Jelszó és a Csatolt. Alkalmazás hozzáadásakor szűrhet, és csak az adott SSO-implementációt használó alkalmazásokat láthatja, ahogy az a képernyőképen is látható. Az SSO implementálja például a népszerű szabványt Security Assertion Markup Language (SAML). Egy másik népszerű szabvány az OpenId Connect (OIDC). Az SSO ezen szabványokkal való konfigurálása eltérő, ezért jegyezze fel a hozzáadott alkalmazás által megvalósított SSO-típust.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Képernyőkép az SSO-típusok választóról." lightbox="media/add-application-portal/sso-types.png":::

    - Ha az alkalmazás fejlesztője az **OIDC** szabványt használta az SSO-hez, válassza a **Regisztráció lehetőséget.** Megjelenik egy beállítási lap. Ezután ugrás az OIDC-alapú egyszeri bejelentkezés beállításának rövid útmutatójára.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Képernyőkép egy OIDC-alapú SSO-alkalmazás hozzáadásáról.":::

    - Ha az alkalmazás fejlesztője az **SAML** szabványt használta az SSO-hoz, válassza a **Létrehozás lehetőséget.** Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára. Az űrlapon szerkesztheti az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek. Ezután ugrás az SAML-alapú egyszeri bejelentkezés beállításának rövid útmutatójára.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="SamL-alapú SSO-alkalmazás hozzáadását bemutató képernyőkép.":::


> [!IMPORTANT]
> Van néhány fontos különbség az SAML-alapú és az OIDC-alapú SSO-implementációk között. Az SAML-alapú alkalmazásokkal ugyanannak az alkalmazásnak több példányát is hozzáadhatja. Például: GitHub1, GitHub2 stb. OIDC-alapú alkalmazások esetén csak egy alkalmazáspéldányt adhat hozzá. Ha már hozzáadott egy OIDC-alapú alkalmazást, és megpróbálja ismét hozzáadni ugyanazt az alkalmazást, és kétszer adja meg a hozzájárulást, akkor az nem lesz újra hozzáadva a bérlőhöz.

Ha a keresett alkalmazás nem található a katalógusban, válassza a Create your own application (Saját alkalmazás létrehozása) hivatkozást, majd a What are you looking to do with your **application?** (Mit keres az **alkalmazással?)** alatt válassza a Integrate **any other** application you don't find in the gallery (Más alkalmazás integrálása, amely nem található a katalógusban) lehetőséget. A Microsoft már számos alkalmazásfejlesztővel dolgozott együtt, hogy előre konfigurálja őket az Azure AD-val való használatra. Az előre konfigurált alkalmazások a katalógusban stb. Ha azonban a hozzáadni kívánt alkalmazás nem szerepel a listán, akkor létrehozhat egy új, általános alkalmazást, majd konfigurálhatja saját maga vagy az azt létrehozó fejlesztő útmutatásával.

Befejezte egy alkalmazás hozzáadását. A következő rövid útmutató bemutatja, hogyan módosíthatja az embléma és szerkesztheti az alkalmazás egyéb tulajdonságait.

> [!TIP]
> Az alkalmazáskezelést a következő Graph API automatizálhatja: Az alkalmazáskezelés [automatizálása a Microsoft Graph API-val.](/graph/application-saml-sso-configure-api)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a gyorsindítási sorozatot, érdemes törölnie az alkalmazást a tesztbérlő törléséhez. Az alkalmazás törlésével a sorozat utolsó rövid útmutatója az Alkalmazás törlése( ) [cikkből áll.](delete-application-portal.md)

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan konfigurálható egy alkalmazás.
> [!div class="nextstepaction"]
> [Alkalmazás konfigurálása](add-application-portal-configure.md)