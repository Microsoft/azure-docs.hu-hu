---
title: SAML-alapú egyszeri bejelentkezés hibakeresése Azure Active Directory
description: Az SAML-alapú egyszeri bejelentkezés hibakeresése Azure Active Directory-alkalmazásokban.
services: active-directory
ms.author: kenwith
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: 4a5fbef3380db8c9b357c83b4cb8240a41a0012c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259287"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Az Azure Active Directoryban található alkalmazásokba történő SAML-alapú egyszeri bejelentkezés hibaelhárítása

Ismerje meg, hogyan találhatja meg és javíthatja az [egyszeri bejelentkezéssel](what-is-single-sign-on.md) kapcsolatos problémákat a Azure Active Directory (Azure ad) alkalmazásokban, amelyek SAML-alapú egyszeri bejelentkezést használnak. 

## <a name="before-you-begin"></a>Előkészületek

Javasoljuk, hogy telepítse a [saját alkalmazások biztonságos bejelentkezési bővítményét](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Ez a böngésző-bővítmény megkönnyíti az SAML-kérelmek és az SAML-válasz adatainak összegyűjtését, amelyekre szüksége van az egyszeri bejelentkezéssel kapcsolatos problémák megoldásához. Ha nem tudja telepíteni a bővítményt, ez a cikk bemutatja, Hogyan oldhatók fel a bővítmények és azok nélkül a hibák.

A saját alkalmazások biztonságos bejelentkezési bővítményének letöltéséhez és telepítéséhez használja az alábbi hivatkozások egyikét.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés tesztelése

Az SAML-alapú egyszeri bejelentkezés tesztelése az Azure AD és egy célalkalmazás között:

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com) globális rendszergazdaként vagy más rendszergazdaként, amely jogosultsággal rendelkezik az alkalmazások kezeléséhez.
1. A bal oldali panelen válassza a **Azure Active Directory**, majd a **vállalati alkalmazások** elemet. 
1. A vállalati alkalmazások listájából válassza ki azt az alkalmazást, amelynek ki szeretné próbálni az egyszeri bejelentkezést, majd válassza a bal oldali Select **egyszeri bejelentkezés** lehetőséget.
1. Az SAML-alapú egyszeri bejelentkezés tesztelési élményének megnyitásához nyissa meg az **egyszeri bejelentkezés tesztelését** ismertető témakört (5. lépés). Ha a **teszt** gomb szürkén jelenik meg, először ki kell töltenie és mentenie kell a szükséges attribútumokat az **SAML alapkonfiguráció** szakaszában.
1. A **teszt egyszeri bejelentkezés** panelen a vállalati hitelesítő adataival jelentkezhet be a célalkalmazás alkalmazásba. Az aktuális felhasználóként vagy más felhasználóként is bejelentkezhet. Ha másik felhasználóként jelentkezik be, a rendszer kérni fogja a hitelesítést.

    ![Az SAML SSO-tesztet megjelenítő oldal képernyőképe](./media/debug-saml-sso-issues/test-single-sign-on.png)

Ha sikeresen bejelentkezett, a teszt sikeres volt. Ebben az esetben az Azure AD SAML-válasz jogkivonatot adott ki az alkalmazásnak. Az alkalmazás az SAML-tokent használta a sikeres bejelentkezéshez.

Ha a vállalati bejelentkezési oldalon vagy az alkalmazás oldalán hibát észlel, használja a következő részeket a hiba elhárításához.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Bejelentkezési hiba elhárítása a céges bejelentkezési oldalon

Amikor megpróbál bejelentkezni, előfordulhat, hogy az alábbi példához hasonló hibaüzenet jelenik meg a vállalati bejelentkezési oldalon.

![Példa a céges bejelentkezési oldal hibájának megjelenítésére](./media/debug-saml-sso-issues/error.png)

A hiba hibakereséséhez szüksége lesz a hibaüzenetre és az SAML-kérelemre. A saját alkalmazások biztonságos bejelentkezési bővítménye automatikusan összegyűjti ezeket az információkat, és az Azure AD-ben a megoldási útmutatást jeleníti meg.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>A bejelentkezési hiba elhárítása a saját alkalmazások biztonságos bejelentkezési bővítménnyel telepítve

1. Hiba esetén a bővítmény átirányítja az Azure AD **-teszt egyszeri bejelentkezési** paneljére.
1. A **teszt egyszeri bejelentkezés** panelen válassza **az SAML-kérelem letöltése** lehetőséget.
1. A hiba és az SAML-kérelem értékei alapján egyedi megoldási útmutatást kell látnia.
1. A probléma megoldásához a **javítás** gomb megnyomásával automatikusan frissítheti az Azure ad konfigurációját. Ha nem látja ezt a gombot, a bejelentkezési probléma az Azure AD-beli helytelen konfiguráció miatt nem fog megjelenni.

Ha a bejelentkezési hiba nem tartalmaz megoldást, javasoljuk, hogy a visszajelzés szövegmezővel tájékoztasson minket.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>A hiba elhárítása a saját alkalmazások biztonságos bejelentkezési bővítményének telepítése nélkül

1. Másolja a hibaüzenetet a lap jobb alsó sarkában. A hibaüzenet a következőket tartalmazza:
    - Egy CorrelationID és egy időbélyeg. Ezek az értékek akkor fontosak, ha támogatási esetet hoz létre a Microsofttal, mert segítenek a mérnököknek azonosítani a problémát, és pontos megoldást biztosítanak a problémára.
    - A probléma kiváltó okát azonosító utasítás.
1. Lépjen vissza az Azure AD-be, és keresse meg az **egyszeri bejelentkezés tesztelési** paneljét.
1. Illessze be a hibaüzenetet a fenti szövegmezőbe a **megoldás beolvasása útmutatással**.
1. A probléma megoldásához szükséges lépések megjelenítéséhez kattintson a **megoldási útmutató beolvasása** elemre. Az útmutatáshoz szükség lehet az SAML-kérelem vagy az SAML-válasz információinak megkövetelésére. Ha nem a saját alkalmazások biztonságos bejelentkezési bővítményét használja, szükség lehet egy eszközre, például a [hegedűsre](https://www.telerik.com/fiddler) az SAML-kérelem és válasz beolvasásához.
1. Ellenőrizze, hogy az SAML-kérelemben szereplő cél megfelel-e az Azure AD-től kapott SAML-Sign-On szolgáltatás URL-címének.
1. Ellenőrizze, hogy az SAML-kérelemben szereplő kiállító megegyezik-e az Azure AD-alkalmazáshoz konfigurált azonosítóval. Az Azure AD a kiállító használatával keres egy alkalmazást a címtárban.
1. Ellenőrizze, hogy az alkalmazás AssertionConsumerServiceURL-e az SAML-tokent az Azure AD-ból. Ezt az értéket beállíthatja az Azure AD-ben, de nem kötelező, ha az SAML-kérelem része.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Bejelentkezési hiba elhárítása az alkalmazás oldalán

Előfordulhat, hogy sikeresen bejelentkezik, majd az alkalmazás oldalán hibaüzenet jelenik meg. Ez akkor fordul elő, ha az Azure AD tokent ad ki az alkalmazásnak, de az alkalmazás nem fogadja el a választ.

A hiba elhárításához kövesse az alábbi lépéseket, vagy tekintse meg ezt a [rövid videót arról, hogyan használhatja az Azure ad-t az SAML SSO hibaelhárításához](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8):

1. Ha az alkalmazás az Azure AD-katalógusban található, ellenőrizze, hogy végrehajtotta-e az alkalmazás Azure AD-vel való integrálásának lépéseit. Az alkalmazáshoz kapcsolódó integrációs utasításokért tekintse meg az [SaaS Application Integration oktatóanyagok listáját](../saas-apps/tutorial-list.md).
1. Az SAML-válasz beolvasása.
    - Ha telepítve van a saját alkalmazások biztonságos bejelentkezési bővítménye, az **egyszeri bejelentkezés tesztelési** paneljén kattintson **az SAML-válasz letöltése** elemre.
    - Ha a bővítmény nincs telepítve, használjon egy eszközt, például a [hegedűst](https://www.telerik.com/fiddler) az SAML-válasz lekéréséhez.
1. Figyelje meg ezeket az elemeket az SAML-válasz tokenben:
   - A NameID értékének és formátumának felhasználói egyedi azonosítója
   - A jogkivonatban kiadott jogcímek
   - A jogkivonat aláírásához használt tanúsítvány.

     További információ az SAML-válaszról: [egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Most, hogy áttekintette az SAML-választ, tekintse meg a [hiba az alkalmazás oldalán, miután bejelentkezett](application-sign-in-problem-application-error.md) a probléma megoldásához szükséges útmutatásra. 
1. Ha még nem tud sikeresen bejelentkezni, megkérheti az alkalmazás gyártójától, hogy mi hiányzik az SAML-válaszból.

## <a name="next-steps"></a>Következő lépések

Most, hogy az egyszeri bejelentkezés az alkalmazáson dolgozik, [automatizálhatja a felhasználók üzembe](../app-provisioning/user-provisioning.md) helyezését és megszüntetését az SaaS-alkalmazásokhoz vagy [a feltételes hozzáférés használatának megkezdéséhez](../conditional-access/app-based-conditional-access.md).
