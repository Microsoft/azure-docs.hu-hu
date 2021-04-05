---
title: 'Oktatóanyag: Azure Active Directory integráció az Amazon Businessrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Amazon Business között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 1424669af9fe28257e66c8c5438f6e1d171954e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672899"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Oktatóanyag: az Amazon Business integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Amazon businesst Azure Active Directory (Azure AD) használatával. Ha integrálja az [Amazon businesst](https://www.amazon.com/b2b/info/amazon-business?layout=landing) az Azure ad-vel, a következőket teheti:

* Az Amazon Businesshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Amazon businessbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Amazon Business egyszeri bejelentkezés (SSO) engedélyezett előfizetése. Nyissa meg az [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) oldalt egy Amazon Business-fiók létrehozásához.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy meglévő Amazon Business-fiókban konfigurálja és teszteli az Azure AD SSO-t.

* Az Amazon Business **az SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja
* Az Amazon Business **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-amazon-business-from-the-gallery"></a>Az Amazon Business hozzáadása a katalógusból

Az Amazon Business az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Amazon businesst a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **Amazon Business** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Business** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Amazon Business használatával egy **B. Simon** nevű teszt felhasználó segítségével.

Az Azure AD SSO az Amazon Businessrel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. Az **[Amazon Business SSO konfigurálása](#configure-amazon-business-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy Amazon Business test User](#create-amazon-business-test-user)** -t, amely az Amazon Businessben található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **Amazon Business** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha az **alapszintű SAML-konfigurációt** **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **azonosító (Entity ID)** szövegmezőben írja be az URL-címet a következő minták egyikének használatával:

       | URL-cím | Region |
       |-|-|
       | `https://www.amazon.com`| Észak-Amerika |
       | `https://www.amazon.co.jp`| Kelet-Ázsia |
       | `https://www.amazon.de`| Európa |

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

       | URL-cím | Region |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Észak-Amerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Kelet-Ázsia |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Európa |

       > [!NOTE]
       > A válasz URL-cím értéke nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címével. Megjelenik az `<idpid>` Amazon Business SSO konfigurációs szakaszának értéke, amelyet az oktatóanyag későbbi részében talál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, akkor a **további URL-címek beállítása** szakaszban fel kell vennie az Amazon üzleti konfigurációjában megadott teljes URL-címet a **bejelentkezési URL** -címre.

1. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Az attribútumok szerkesztéséhez kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok & jogcímek** szakaszban.

    ![Képernyőfelvétel: felhasználói attribútumok & alapértelmezett értékekkel rendelkező jogcímek, például givenName user. givenName és EmailAddress user. mail.](media/amazon-business-tutorial/map-attribute3.png)

1. Szerkessze az attribútumokat, és másolja a **névtér** értékét a Jegyzettömbbe.

    ![Képernyőfelvétel: felhasználói attribútumok & jogcímek a jogcím neve és értéke oszlopokkal.](media/amazon-business-tutorial/map-attribute4.png)

1. A fentieken kívül az Amazon Business Application néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **csoport jogcímek** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

    1. Kattintson a **kérelemben visszaadott csoportok** melletti **tollra** .

        ![A képernyőképen a felhasználói attribútumok & jogcímek szerepelnek a jogcímek beállításban visszaadott csoportok ikonjával.](./media/amazon-business-tutorial/config04.png)

        ![A képernyőfelvétel az ebben az eljárásban leírt értékekkel rendelkező csoportos jogcímeket jeleníti meg.](./media/amazon-business-tutorial/config05.png)

    1. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

    1. Válassza ki a **csoport azonosítóját** **forrás attribútumként**.

    1. Jelölje be a csoportjogcím **nevének testreszabása** jelölőnégyzetet, és adja meg a csoport nevét a szervezet követelményeinek megfelelően.

    1. Kattintson a **Mentés** gombra.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Amazon Business beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Az Amazon Business SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Amazon Business Company webhelyére rendszergazdaként.

1. Kattintson a **felhasználói profilra** , és válassza az **üzleti beállítások** lehetőséget.

    ![Felhasználói profil](media/amazon-business-tutorial/user-profile.png)

1. A **rendszerintegrációk** varázslóban válassza az **egyszeri Sign-On (SSO)** lehetőséget.

    ![Egyszeri bejelentkezés (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Az **egyszeri bejelentkezés beállítása** varázslóban válassza ki a szolgáltatót a szervezeti követelményeknek megfelelően, és kattintson a **tovább** gombra.

    ![A képernyőképen az S S O beállítása látható, Microsoft Azure A D és a következő kijelölése után.](media/amazon-business-tutorial/default-group1.png)

    > [!NOTE]
    > Bár a Microsoft ADFS egy felsorolt lehetőség, nem fog működni az Azure AD SSO-val.

1. Az **új felhasználói fiók alapértelmezései** varázslóban válassza ki az **alapértelmezett csoportot** , majd válassza ki az **alapértelmezett felvásárlási szerepkört** a szervezet felhasználói szerepköre szerint, és kattintson a **tovább** gombra.

    ![A képernyőképen az új felhasználói fiók alapértelmezései láthatók a Microsoft S O-vel, az igényléssel és a következő kijelöléssel.](media/amazon-business-tutorial/dafault-group2.png)

1. A **metaadat-fájl feltöltése** varázslóban kattintson a **Tallózás** gombra a **metaadatok XML-** fájljának feltöltéséhez, amelyet a Azure Portal letöltött, majd kattintson a **feltöltés** gombra.

    ![A képernyőfelvételen a metaadat-fájl feltöltése látható, amely lehetővé teszi, hogy megkeresse az x m l-fájlt, és töltse fel.](media/amazon-business-tutorial/connection-data1.png)

1. A letöltött metaadat-fájl feltöltése után a rendszer automatikusan feltölti a **kapcsolatok adatai** szakaszban található mezőket. Ezután kattintson a **tovább** gombra.

    ![A képernyőfelvételen A kapcsolatok adatai láthatók, ahol megadhatja az Azure D azonosítóját, A login U R L és az SAML aláíró tanúsítványát.](media/amazon-business-tutorial/connection-data2.png)

1. A **Attribute utasítás feltöltése** varázslóban kattintson a **kihagyás** gombra.

    ![A képernyőfelvételen a Attribute utasítás feltöltése látható, amely lehetővé teszi, hogy megkeresse az attribútumok utasításait, de ebben az esetben válassza a Kihagyás lehetőséget.](media/amazon-business-tutorial/map-attribute1.png)

1. Az **attribútum-hozzárendelési** varázslóban adja hozzá a követelmény mezőket a **+ mező hozzáadása** lehetőségre kattintva. Adja hozzá az attribútumok értékeit, beleértve a névteret is, amelyet a Azure Portal a **SAML-AttributeName** mezőbe másolt **felhasználói attribútumok & a jogcímek** szakasza alapján, majd kattintson a **tovább** gombra.

    ![A képernyőképen az attribútumok leképezése látható, ahol szerkesztheti az Amazon-adatsaml-attribútumok nevét.](media/amazon-business-tutorial/map-attribute2.png)

1. Az **Amazon-adatkapcsolatok** varázslóban kattintson a **tovább** gombra.

    ![Képernyőfelvétel: az Amazon-kapcsolódási adatforrások, ahol a Tovább gombra kattintva folytathatja a folytatást.](media/amazon-business-tutorial/amazon-connect.png)

1. Ellenőrizze a konfigurált lépések **állapotát** , és kattintson a **tesztelés megkezdése** elemre.

    ![A képernyőképen az S S O kapcsolat részletei láthatók a tesztelés megkezdésének lehetőségével.](media/amazon-business-tutorial/sso-connection1.png)

1. Az **SSO-kapcsolatok tesztelése** varázslóban kattintson a **teszt** elemre.

    ![A képernyőképen az S S O-kapcsolatok tesztelése gomb látható.](media/amazon-business-tutorial/sso-connection2.png)

1. A **identitásszolgáltató által kezdeményezett URL-cím** varázslóban az **aktiválás** elemre kattintva másolja ki a **idpid** hozzárendelt értéket, és illessze be a **IDPID** paramétert a **Válasz URL-címére** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    ![Képernyőfelvétel: I D P indított U R L, ahol a teszteléshez szükség van egy U R L-re, majd az aktiválás elemre.](media/amazon-business-tutorial/sso-connection3.png)

1. A **készen áll az aktív SSO-ra való váltásra?** varázsló, ellenőrizze, hogy **teljes körűen tesztelt SSO-t használok-e, és készen áll-e az élő** indításra jelölőnégyzet, és kattintson a **váltás aktív**

    ![Képernyőfelvétel: a készen áll arra, hogy váltson az aktív S O-megerősítésre, ahol kiválaszthatja a váltás aktívra lehetőséget.](media/amazon-business-tutorial/sso-connection4.png)

1. Végül az **SSO-kapcsolat részletei** szakaszban az **állapot** **aktívként** jelenik meg.

    ![A képernyőfelvétel aktív állapottal rendelkező S S O-kapcsolati adatait jeleníti meg.](media/amazon-business-tutorial/sso-connection5.png)

    > [!NOTE]
    > Ha az alkalmazást **SP** -kezdeményezésű módban szeretné konfigurálni, hajtsa végre a következő lépést, illessze be a bejelentkezési URL-címet a fenti képernyőképen a **további URL-címek beállítása** szakasz **bejelentkezési URL-címe** szövegmezőbe a Azure Portal. Használja az alábbi formátumot:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

> [!NOTE]
> Ha szükséges, a rendszergazdáknak létre kell hozniuk a saját bérlőn belüli tesztelési felhasználókat. A következő lépések bemutatják, hogyan hozhat létre egy tesztelési felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure AD biztonsági csoport létrehozása a Azure Portalban

1. Kattintson **Azure Active Directory > minden csoport** elemre.

    ![Képernyőfelvétel: a Azure Portal menü, Azure Active Directory kiválasztva és a csoportok ablaktáblán kijelölt összes csoport.](./media/amazon-business-tutorial/all-groups-tab.png)

1. Kattintson az **új csoport** elemre:

    ![A képernyőképen az új csoport gomb látható.](./media/amazon-business-tutorial/new-group-tab.png)

1. Adja meg a **csoport típusát**, a **csoport nevét**, a **csoport leírását**, a **tagság típusát**. Kattintson a nyílra a tagok kiválasztásához, majd keressen rá, vagy kattintson arra a tagra, amelyet hozzá szeretne adni a csoporthoz. Kattintson a **kiválasztás** elemre a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás** gombra.

    ![Képernyőfelvétel: a csoportok ablaktábla, ahol lehetőség van, beleértve a tagok kijelölését és a külső felhasználók meghívását.](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Amazon Business elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Amazon Business** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Képernyőfelvétel: felhasználó hozzáadása gomb.](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

    >[!NOTE]
    > Ha nem rendeli hozzá a felhasználókat az Azure AD-ben, a következő hibaüzenet jelenik meg.

    ![A képernyőképen egy hibaüzenet jelenik meg, amelyet nem lehet bejelentkezni.](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Rendelje hozzá az Azure AD biztonsági csoportot a Azure Portal

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **Amazon Business** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza az **Amazon Business** lehetőséget.

    ![Az Amazon Business hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** elemre.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Keresse meg a használni kívánt biztonsági csoportot, majd kattintson a csoportra, és adja hozzá a tagok kiválasztása szakaszhoz. Kattintson a **kiválasztás**, majd a **hozzárendelés** elemre.

    ![Keresés a biztonsági csoportban](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Győződjön meg arról, hogy a menüsorban található értesítések azt jelzik, hogy a csoport sikeresen hozzá lett-e rendelve a Azure Portal vállalati alkalmazásához.

### <a name="create-amazon-business-test-user"></a>Amazon Business test-felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoztak létre az Amazon Businessben. Az Amazon Business az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Amazon Business-ben, akkor a hitelesítés után létrejön egy új.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Amazon Business csempére kattint, automatikusan be kell jelentkeznie arra az Amazon Business-szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)