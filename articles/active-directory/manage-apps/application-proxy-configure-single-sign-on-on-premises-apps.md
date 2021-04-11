---
title: SAML egyszeri bejelentkezés a helyi alkalmazásokhoz Azure AD alkalmazás proxyval
description: Ismerje meg, hogyan biztosíthat egyszeri bejelentkezést az SAML-hitelesítéssel védett helyszíni alkalmazásokhoz. Távoli hozzáférés biztosítása a helyszíni alkalmazásokhoz alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c688952f37bf9fc91e9dd25e09d9c31cd980d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257068"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML-alapú egyszeri bejelentkezés helyszíni alkalmazásokhoz alkalmazásproxyval

Az egyszeri bejelentkezést (SSO) az SAML-hitelesítéssel védett helyszíni alkalmazások számára is biztosíthatja, és az alkalmazás-proxyn keresztül távoli hozzáférést biztosíthat ezekhez az alkalmazásokhoz. Az SAML egyszeri bejelentkezéssel az Azure Active Directory (Azure AD) a felhasználó Azure AD-fiókjának használatával hitelesíti az alkalmazást. Az Azure AD a bejelentkezési adatokat kapcsolati protokollon keresztül továbbítja az alkalmazásnak. A felhasználókat az SAML-jogcímek által meghatározott szabályok alapján adott alkalmazási szerepkörökre is leképezheti. A SAML SSO mellett az alkalmazásproxy engedélyezésével a felhasználók külső hozzáféréssel fognak rendelkezni az alkalmazáshoz és a zökkenőmentes SSO-élményhez.

Az alkalmazásoknak képesnek kell lenniük a **Azure Active Directory** által kiadott SAML-tokenek felhasználására. Ez a konfiguráció nem vonatkozik a helyszíni identitás-szolgáltatót használó alkalmazásokra. Ezen forgatókönyvek esetében javasoljuk, hogy tekintse [át az alkalmazások Azure ad-ba való áttelepítésének erőforrásait](migration-resources.md).

Az SAML SSO és az Application proxy is együttműködik az SAML-jogkivonat titkosítási funkciójával. További információ: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](howto-saml-token-encryption.md).

Az alábbi protokollok a szolgáltató által kezdeményezett (SP által kezdeményezett) folyamat és az identitás-szolgáltató által kezdeményezett (identitásszolgáltató által kezdeményezett) folyamat egyszeri bejelentkezési folyamatát írják le. Az alkalmazásproxy az SAML SSO-nal működik, ha gyorsítótárazza az SAML-kérelmet, és választ küld a helyi alkalmazásnak és a szolgáltatásnak.

  ![A diagram az alkalmazás, az alkalmazásproxy, az ügyfél és az Azure A D műveleteit mutatja a P által kezdeményezett egyszeri bejelentkezéshez.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![A diagramon az alkalmazás, az alkalmazásproxy, az ügyfél és az Azure A D interakciói láthatók az I d P által kezdeményezett egyszeri bejelentkezéshez.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Alkalmazás létrehozása és SAML SSO beállítása

1. A Azure Portal válassza a **Azure Active Directory > vállalati alkalmazások** lehetőséget, és válassza az **új alkalmazás** lehetőséget.

2. Adja meg az új alkalmazás megjelenítendő nevét, válassza a katalógusban **nem található bármely más alkalmazás integrálása** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

3. Az alkalmazás **Áttekintés** lapján válassza az **egyszeri bejelentkezés** lehetőséget.

4. Válassza az **SAML** lehetőséget az egyszeri bejelentkezési módszerként.

5. Először állítsa be az SAML SSO-t a vállalati hálózaton való működésre, és tekintse meg az SAML- [alapú egyszeri bejelentkezés konfigurálását](configure-saml-single-sign-on.md) ismertető alapszintű SAML-konfiguráció szakaszt az SAML-alapú hitelesítés konfigurálásához az alkalmazáshoz.

6. Adjon hozzá legalább egy felhasználót az alkalmazáshoz, és győződjön meg arról, hogy a teszt fiók hozzáfér az alkalmazáshoz. Ha csatlakozik a vállalati hálózathoz, a tesztelési fiók használatával ellenőrizheti, hogy van-e egyszeri bejelentkezés az alkalmazásba. 

   > [!NOTE]
   > Az alkalmazásproxy beállítása után visszatérhet és frissítheti az SAML **-Válasz URL-címét**.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>A helyszíni alkalmazás közzététele az Application proxyval

A helyszíni alkalmazásokhoz való egyszeri bejelentkezés biztosítása előtt engedélyeznie kell az alkalmazásproxy használatát, és telepítenie kell egy összekötőt. Tekintse meg az [Azure ad-beli Application proxyn keresztüli távelérés helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md) a helyszíni környezet előkészítését, az összekötők telepítését és regisztrálását, valamint az összekötő tesztelését ismertető oktatóanyagot. Ezután kövesse az alábbi lépéseket az új alkalmazás alkalmazás-proxyval való közzétételéhez. Az alább nem említett egyéb beállításokért tekintse [meg a helyszíni alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) hez című szakaszt az oktatóanyagban.

1. Ha az alkalmazás továbbra is meg van nyitva a Azure Portalban, válassza az **alkalmazásproxy** lehetőséget. Adja meg az alkalmazás **belső URL-címét** . Ha egyéni tartományt használ, fel kell töltenie az alkalmazás TLS/SSL-tanúsítványát is. 
   > [!NOTE]
   > Ajánlott eljárásként használjon egyéni tartományokat, amikor lehetséges az optimalizált felhasználói élmény. További információ az [Egyéni tartományok Azure ad Application proxy-beli használatáról](application-proxy-configure-custom-domain.md).

2. Válassza a **Azure Active Directory** lehetőséget az alkalmazás **előhitelesítési** módszere.

3. Másolja az alkalmazás **külső URL-címét** . Az SAML-konfiguráció befejezéséhez szüksége lesz erre az URL-címre.

4. A teszt fiók használatával próbálja meg megnyitni az alkalmazást a **külső URL-címmel** annak ellenőrzéséhez, hogy az alkalmazásproxy megfelelően van-e beállítva. Ha problémák merülnek fel, tekintse meg az [alkalmazásproxy-problémák és a hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)című témakört.

## <a name="update-the-saml-configuration"></a>Az SAML-konfiguráció frissítése

1. Ha az alkalmazás továbbra is meg van nyitva a Azure Portalban, válassza az **egyszeri bejelentkezés** lehetőséget. 

2. Az **egyszeres Sign-On beállítása SAML** használatával lapon lépjen az **alapszintű SAML-konfiguráció** fejlécére, és válassza ki annak **szerkesztési** ikonját (egy ceruza). Győződjön meg arról, hogy az alkalmazásproxy-ban konfigurált **külső URL** -cím fel van töltve az **azonosító**, a **Válasz URL-cím** és a **kijelentkezési URL-cím** mezőkbe. Ezek az URL-címek szükségesek ahhoz, hogy az alkalmazásproxy megfelelően működjön. 

3. Szerkessze a korábban konfigurált **Válasz URL-címet** , hogy a tartomány elérhető legyen az interneten az alkalmazásproxy használatával. Ha például a **külső URL** -cím `https://contosotravel-f128.msappproxy.net` , és az eredeti **Válasz URL-címe** `https://contosotravel.com/acs` , akkor frissítenie kell az eredeti **Válasz URL-címét** a következőre: `https://contosotravel-f128.msappproxy.net/acs` .

    ![Alapszintű SAML-konfigurációs adatok megadása](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Jelölje be a frissített **Válasz URL-címe** melletti jelölőnégyzetet az alapértelmezettként való megjelöléséhez.

   * A kötelező **Válasz URL-címének** alapértelmezettként való megjelölése után törölheti a korábban konfigurált **Válasz URL-címet** is, amely a belső URL-címet használta.

   * Egy SP által kezdeményezett folyamat esetén győződjön meg arról, hogy a háttérbeli alkalmazás megadja a megfelelő **Válasz URL-címet** vagy az állítási fogyasztói szolgáltatás URL-címét a hitelesítési jogkivonat fogadásához.

    > [!NOTE]
    > Ha a háttérbeli alkalmazás a belső URL-címnek a **Válasz URL-címét** fogja használni, [Egyéni tartományokat](application-proxy-configure-custom-domain.md) kell használnia a belső és külső URL-címek egyeztetéséhez, vagy a saját alkalmazások biztonságos bejelentkezési bővítmény telepítését a felhasználói eszközökön. Ezt a bővítményt a rendszer automatikusan átirányítja a megfelelő alkalmazásproxy-szolgáltatásba. A bővítmény telepítéséhez tekintse meg az [alkalmazások biztonságos bejelentkezési bővítményét](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Az alkalmazás tesztelése

Az összes lépés elvégzése után az alkalmazásnak futnia kell. Az alkalmazás tesztelése:

1. Nyisson meg egy böngészőt, és navigáljon arra a **külső URL-címre** , amelyet az alkalmazás közzétételekor hozott létre. 
1. Jelentkezzen be az alkalmazáshoz hozzárendelt tesztelési fiókkal. Képesnek kell lennie az alkalmazás betöltésére és az egyszeri bejelentkezésre az alkalmazásba.

## <a name="next-steps"></a>Következő lépések

- [Hogyan biztosítja az Azure AD Application Proxy az egyszeri bejelentkezést?](./what-is-single-sign-on.md)
- [Alkalmazásproxyval kapcsolatos hibaelhárítás](application-proxy-troubleshoot.md)