---
title: 'Oktatóanyag: Azure Active Directory integráció Oracle Cloud Infrastructure-konzollal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az Oracle felhőalapú infrastruktúra konzolja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: 914858471432979e5516a1f075ed4fcbb8061a5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181850"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Oktatóanyag: az Oracle felhőalapú infrastruktúra-konzol integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Oracle Cloud Infrastructure-konzolt Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az Oracle Cloud Infrastructure-konzolt, a következőket teheti:

* Az Oracle Cloud Infrastructure-konzolhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Oracle Cloud Infrastructure-konzolba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az Oracle felhőalapú infrastruktúra konzoljának egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Oracle felhőalapú infrastruktúra konzolja támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Oracle Cloud Infrastructure-konzol hozzáadása a katalógusból

Az Oracle Cloud Infrastructure-konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Oracle Cloud Infrastructure-konzolt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **Oracle Cloud Infrastructure Console** kifejezést a keresőmezőbe.
1. Válassza ki az **Oracle Cloud Infrastructure-konzolt** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Oracle felhőalapú infrastruktúra-konzollal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Oracle Cloud Infrastructure-konzolon.

Az Azure AD SSO és az Oracle felhőalapú infrastruktúra konzoljának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Oracle felhőalapú infrastruktúra konzoljának konfigurálása](#configure-oracle-cloud-infrastructure-console)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy Oracle felhőalapú infrastruktúra-konzolt a felhasználónak](#create-oracle-cloud-infrastructure-console-test-user)** , hogy az a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezzen.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Oracle Cloud Infrastructure Console** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

   > [!NOTE]
   > A szolgáltatói metaadatokat az oktatóanyag **Oracle felhő-infrastruktúra konfigurálása konzoljának egyszeri bejelentkezés** szakaszában találja.
    
   1. Kattintson a **metaadat-fájl feltöltése** elemre.

   1. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

   1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan fel lesznek töltve az **SAML alapszintű konfigurációs** szakasz szövegmezőben.
    
      > [!NOTE]
      > Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

      A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot az [Oracle Cloud Infrastructure-konzol ügyfél-támogatási csapatával](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az Oracle Cloud Infrastructure Console alkalmazás megadott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

   ![image1](common/edit-attribute.png)

1. A fentieken kívül az Oracle Cloud Infrastructure Console alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **csoport jogcímek (előzetes verzió)** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

   1. Kattintson a **név azonosító érték** melletti **tollra** .

   1. Válassza az **állandó** lehetőséget a **név azonosító formátumának** kiválasztása lehetőségnél.
 
   1. Kattintson a **Mentés** gombra.

      ![image2](./media/oracle-cloud-tutorial/config07.png)
    
      ![image3](./media/oracle-cloud-tutorial/config11.png)

   1. Kattintson a **kérelemben visszaadott csoportok** melletti **tollra** .

   1. Válassza a **biztonsági csoportok** lehetőséget a választógombok listából.

   1. Válassza ki a **csoport azonosítója** **forrás attribútumát** .

   1. Győződjön meg arról **, hogy testreszabja a csoportjogcím nevét**.

   1. A **név** szövegmezőbe írja be a következőt: **Csoportnév**.

   1. A **névtér (nem kötelező)** szövegmezőbe írja be a következőt: `https://auth.oraclecloud.com/saml/claims` .

   1. Kattintson a **Mentés** gombra.

      ![image4](./media/oracle-cloud-tutorial/config08.png)

1. Az **Oracle Cloud Infrastructure konzol beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B. Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Oracle Cloud Infrastructure-konzolhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **Oracle Cloud Infrastructure Console** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-oracle-cloud-infrastructure-console"></a>Az Oracle felhőalapú infrastruktúra konzoljának konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az Oracle Cloud Infrastructure-konzolra.

1. Kattintson a menü bal oldalára, majd kattintson az **identitás** elemre, majd navigáljon az **összevonáshoz**.

   ![Configuration1](./media/oracle-cloud-tutorial/config01.png)

1. Mentse a **szolgáltatói metaadat-fájlt** a **dokumentum letöltése** hivatkozásra kattintva, és töltse fel a Azure Portal **alapszintű SAML-konfiguráció** szakaszára, majd kattintson az **Identity Provider hozzáadása** elemre.

   ![Configuration2](./media/oracle-cloud-tutorial/config02.png)

1. Az **identitás-szolgáltató hozzáadása** előugró ablakban végezze el a következő lépéseket:

   ![Configuration3](./media/oracle-cloud-tutorial/config03.png)

   1. A **név** szövegmezőbe írja be a nevét.

   1. A **Leírás** szövegmezőbe írja be a leírást.

   1. Válassza a **Microsoft Active Directory ÖSSZEVONÁSI szolgáltatás (ADFS) vagy az SAML 2,0-kompatibilis identitás-szolgáltató** **típusként** lehetőséget.

   1. Kattintson a **Tallózás** gombra, és töltse fel az összevonási metaadatok XML-fájlját, amelyet a Azure Portalról töltött le.

   1. Kattintson a **Folytatás** gombra, és az **Identitáskezelő szerkesztése** szakaszban hajtsa végre a következő lépéseket:

      ![Configuration4](./media/oracle-cloud-tutorial/configure-09.png)

   1. Az **identitás-szolgáltató csoportot** az Azure ad Group objektum-azonosítóként kell kiválasztani. A csoport AZONOSÍTÓjának a csoport GUID azonosítójának kell lennie Azure Active Directoryból. A csoportot hozzá kell rendelni a megfelelő csoporthoz a **OCI csoport** mezőben.

   1. A Azure Portal és a szervezet igényeinek megfelelően több csoportot is leképezheti a telepítéshez. Kattintson a **+ leképezés hozzáadása** lehetőségre, hogy minél több csoportot adjon hozzá, mint amennyire szüksége van.

   1. Kattintson a **Submit (Küldés**) gombra.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle felhőalapú infrastruktúra-konzol tesztelési felhasználójának létrehozása

 Az Oracle felhő-infrastruktúra konzolja az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint megtörténik. Ez a szakasz nem tartalmaz műveleti elemeket. Egy új felhasználó nem jön létre az elérési kísérlet során, és nem kell létrehoznia a felhasználót.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen kiválasztja az Oracle Cloud Infrastructure Console csempét, a rendszer átirányítja az Oracle Cloud Infrastructure Console bejelentkezési oldalára. Válassza ki az **Identitáskezelő** elemet a legördülő menüből, majd kattintson a **Folytatás** lehetőségre, amint az alább látható a bejelentkezéshez. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

![Konfiguráció](./media/oracle-cloud-tutorial/config10.png)

## <a name="next-steps"></a>Következő lépések

Az Oracle felhőalapú infrastruktúra-konzol konfigurálása után kényszerítheti a munkamenet-vezérlők betartatását, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)