---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az IBM Digital Business Automation szolgáltatással a felhőben | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az IBM digitális üzleti automatizálása között a felhőben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: 91a50ed85b5802657a27c5a545de0013937e9da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az IBM Digital Business Automation szolgáltatással a felhőben

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az IBM Digital Business Automationt a felhőbe Azure Active Directory (Azure AD) használatával. Ha az IBM Digital Business Automationt az Azure AD-vel integrálja a felhőbe, a következőket teheti:

* A felhőben elérhető IBM digitális üzleti automatizáláshoz hozzáférő Azure AD-vezérlés.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezzenek az IBM digitális üzleti automatizálásba a felhőben az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* IBM Digital Business Automation felhőalapú egyszeri bejelentkezésre (SSO) engedélyezett előfizetésre.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az IBM Digital Business Automation a felhőben támogatja az **SP-t és a identitásszolgáltató** kezdeményezett SSO-t
* Miután konfigurálta az IBM Digital Business Automationt a felhőben, kikényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását is biztosítja. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Az IBM Digital Business Automation hozzáadása a felhőben a katalógusból

Az IBM Digital Business Automation felhőből az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az IBM Digital Business Automation szolgáltatást a felhőben a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **IBM Digital Business Automation on Cloud** kifejezést.
1. Válassza az **IBM Digital Business Automation lehetőséget a felhőben** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az IBM digitális üzleti automatizáláshoz a felhőben

Konfigurálja és tesztelje az Azure AD SSO-t az IBM Digital Business Automation használatával a felhőben egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az IBM Digital Business Automation szolgáltatásban a felhőben.

Az Azure AD SSO konfigurálásához és teszteléséhez a felhőben az IBM Digital Business Automation használatával végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[IBM Digital Business Automation konfigurálása a Felhőbeli egyszeri](#configure-ibm-digital-business-automation-on-cloud-sso)** bejelentkezéshez – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálása.
    1. Az **[IBM Digital Business Automation létrehozása a Cloud test User](#create-ibm-digital-business-automation-on-cloud-test-user)** szolgáltatásban – ha az IBM Digital Business automationben a felhőben található, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **IBM Digital Business Automation on Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:
    
    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    c. A metaadat-fájl feltöltése után a rendszer az IBM Digital Business Automation szolgáltatásban automatikusan kitölti az **azonosítót** és a **Válasz URL-** értékeket a Cloud szakasz szövegmezőben:

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus kitöltést, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

    > [!Note]
    > Az ügyfelek a felhőalapú előfizetésük metaadat-fájlját az IBM Digital Business Automation szolgáltatásban szerezhetik be a [Cloud Client ügyfélszolgálati csapatában](mailto:supportbpmoncloud@us.ibm.com).

1. Ha nem rendelkezik **szolgáltatói metaadat-fájllal**, az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot az [IBM Digital Business Automation szolgáltatással a Cloud Client támogatási csapatával](mailto:supportbpmoncloud@us.ibm.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **IBM digitális üzleti automatizálás beállítása a felhőben** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az IBM digitális üzleti automatizáláshoz a felhőben.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **IBM Digital Business Automation a felhőben** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Az IBM Digital Business Automation konfigurálása felhőalapú egyszeri bejelentkezéshez

Az **IBM Digital Business Automation felhőalapú** oldalon történő egyszeri bejelentkezésének konfigurálásához el kell küldenie a letöltött **összevonás-metaadatokat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalról az [IBM Digital Business Automation szolgáltatásba a felhőalapú támogatási csapatban](mailto:supportbpmoncloud@us.ibm.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Az IBM Digital Business Automation létrehozása a Cloud test User szolgáltatásban

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az IBM Digital Business Automationben a felhőben. Az [IBM Digital Business Automation használata a felhőalapú támogatási csapatban](mailto:supportbpmoncloud@us.ibm.com) a felhasználók hozzáadásához az IBM Digital Business Automation szolgáltatásban a Cloud platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az IBM Digital Business Automation on Cloud csempére kattint, automatikusan be kell jelentkeznie az IBM Digital Business Automation szolgáltatásba a felhőben, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az IBM Digital Business Automation kipróbálása a felhőben az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [Az IBM digitális üzleti automatizálásának biztosítása a felhőben fejlett láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)