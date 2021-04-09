---
title: 'Oktatóanyag: Azure Active Directory integráció a Costpoint-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Costpoint között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652929"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Oktatóanyag: a Costpoint és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Costpoint a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Costpoint-t az Azure AD-vel, a következőket teheti:

* A Costpoint-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Costpoint az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Costpoint egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli környezetekben végezheti el. 

* A Costpoint **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="generate-costpoint-metadata"></a>Costpoint-metaadatok előállítása

A Costpoint SAML SSO-konfiguráció ismertetése a **DeltekCostpoint711Security.pdf** útmutatóban található. Töltse le ezt az útmutatót a Deltek Costpoint támogatási webhelyéről, és tekintse meg az **SAML egyszeri bejelentkezés** beállítása  >  **SAML egyszeri bejelentkezést a Costpoint és a Microsoft Azure szakasz között** . Kövesse az utasításokat, és készítsen **COSTPOINT SP összevonási metaadatokat tartalmazó XML-** fájlt. 

![Képernyőfelvétel: a "termék-konfigurációs segédprogram" a "Weblogic-Security" lapon van kiválasztva.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint hozzáadása a gyűjteményből

A Costpoint Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Costpoint a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Costpoint** kifejezést a keresőmezőbe.
1. Válassza ki a **Costpoint** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Azure AD SSO konfigurálása és tesztelése a Costpoint-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Costpoint a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Costpoint-ben.

Az Azure AD SSO és a Costpoint konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[COSTPOINT SSO konfigurálása](#configure-costpoint-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Costpoint-teszt felhasználót](#create-costpoint-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Costpoint rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A **Costpoint** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha rendelkezik a szolgáltatói **metaadatok fájljával**, hajtsa végre a következő lépéseket:

   > [!NOTE]
   > A szolgáltatói metaadatokat a Costpoint- [metaadatok létrehozása](#generate-costpoint-metadata)című fájlban szerezheti be. A fájl használatát az oktatóanyag későbbi részében ismertetjük.
 
   1. Kattintson a **metaadatok feltöltése** gombra, majd válassza ki a **Costpoint SP összevonási METAADATOKAT tartalmazó XML-** fájlt, amelyet korábban a Costpoint hozott létre, majd kattintson a **Hozzáadás** gombra a fájl feltöltéséhez.

      ![A metaadat-fájl feltöltése](./media/costpoint-tutorial/upload-metadata.png)
    
   1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan ki lesznek töltve a Costpoint szakaszban.

      > [!NOTE]
      > Ha az **azonosító** és a **Válasz URL-címe** nem automatikus polulated, a követelménynek megfelelően adja meg manuálisan az értékeket. Győződjön meg arról, hogy az **azonosító (Entity ID)** és a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** megfelelően van beállítva, és az **ACS URL-** cím egy érvényes Costpoint URL-cím, amely **/LoginServlet.CPS** végződik.

   1. Válassza a **további URL-címek beállítása** lehetőséget. A **továbbítási állapot** mezőben adja meg az értéket a következő minta használatával: `system=[your system]` (például **System = DELTEKCP**).

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** ikont az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a Jegyzettömbbe mentéséhez.

   ![SAML-aláíró tanúsítvány](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Costpoint.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Costpoint** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-costpoint-sso"></a>Costpoint SSO konfigurálása

1. Térjen vissza a Costpoint konfigurációs segédprogramhoz. Az **identitásszolgáltató összevonási metaadatok XML-** szövege szövegmezőbe illessze be az *alkalmazás-összevonási metaadatok URL-* fájljának tartalmát. 

   ![Costpoint-konfigurációs segédprogram](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Folytassa a **DeltekCostpoint711Security.pdf** útmutató utasításait a Costpoint SAML-beállítás befejezéséhez.

### <a name="create-costpoint-test-user"></a>Costpoint-tesztelési felhasználó létrehozása

Ebben a szakaszban egy felhasználót hoz létre a Costpoint-ben. Tegyük fel, hogy a felhasználói azonosító **b. Simon** , a felhasználó neve pedig **b. Simon**. A [Costpoint ügyfél-támogatási csapattal](https://www.deltek.com/about/contact-us) együttműködve vegye fel a felhasználót a Costpoint-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználót.

A felhasználó létrehozása után a felhasználó **hitelesítési módszerének** **Active Directory** kell lennie, az **SAML egyszeri bejelentkezés** jelölőnégyzetet ki kell jelölni, és a felhasználónévnek Azure Active Directory kell LENNIe **Active Directory vagy tanúsítvány-azonosítónak** (az alábbi képernyőképen látható).

![Costpoint-felhasználó](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Costpoint bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Costpoint bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Costpoint, amelyhez be szeretné állítani az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Costpoint csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a Costpoint, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Costpoint konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).