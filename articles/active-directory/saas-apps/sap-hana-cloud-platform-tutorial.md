---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az SAP Cloud platform között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964048"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Oktatóanyag: Azure Active Directory integráció az SAP Cloud platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud platformot Azure Active Directory (Azure AD) használatával. Az SAP Cloud platform Azure AD-val való integrálásával a következőket teheti:

* Az SAP Cloud platformhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP Cloud platformba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SAP Cloud platformmal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAP Cloud platform egyszeri bejelentkezésre alkalmas előfizetés

Az oktatóanyag elvégzése után az SAP Cloud platformhoz rendelt Azure AD-felhasználók egyszeri bejelentkezést végezhetnek az alkalmazásba a [hozzáférési panel bevezetésének](../user-help/my-apps-portal-end-user-access.md)használatával.

>[!IMPORTANT]
>Az egyszeri bejelentkezés teszteléséhez telepítenie kell egy alkalmazást, vagy elő kell fizetnie egy alkalmazásra az SAP Cloud platform-fiókjában. Ebben az oktatóanyagban egy alkalmazást helyezünk üzembe a fiókban.
> 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SAP Cloud platform támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>SAP Cloud platform hozzáadása a katalógusból

Az SAP Cloud platform Azure AD-integrációjának konfigurálásához hozzá kell adnia az SAP Cloud platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **SAP Cloud platform** kifejezést.
1. Válassza az **SAP Cloud platform** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Az Azure AD SSO konfigurálása és tesztelése az SAP Cloud platformhoz

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Cloud platformmal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud platformon.

Az Azure AD SSO SAP Cloud platformmal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. Az **[SAP Cloud platform SSO konfigurálása](#configure-sap-cloud-platform-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[SAP Cloud platform tesztelési felhasználó létrehozása](#create-sap-cloud-platform-test-user)** – a Britta Simon-nek az SAP Cloud platformon, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **SAP Cloud platform** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    ![SAP Cloud platform tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a **SAP Cloud platform** alkalmazásba való bejelentkezéshez használt URL-címet. Ez a védett erőforrás fiókra vonatkozó URL-címe az SAP Cloud platform alkalmazásban. Az URL-cím a következő mintán alapul: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Ez az az SAP Cloud platform-alkalmazás URL-címe, amelyhez a felhasználónak hitelesítenie kell magát.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Az **azonosító** szövegmezőben adja meg az SAP Cloud platformhoz tartozó URL-címet az alábbi minták egyikének használatával: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot az [SAP Cloud platform ügyfél-támogatási csapatával](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) Sign-On URL-cím és azonosító beszerzéséhez. A válasz URL-címe a megbízhatósági kezelés szakaszból szerezhető be, amelyet az oktatóanyag későbbi részében ismertetünk.
    > 
4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SAP Cloud platform elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SAP Cloud platform** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-cloud-platform-sso"></a>Az SAP Cloud platform SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az SAP Cloud platform pilótafülkére `https://account.<landscape host>.ondemand.com/cockpit` (például: https://account.hanatrial.ondemand.com/cockpit) ).

2. Kattintson a **megbízhatóság** fülre.
   
    ![Bizalom](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Bizalom")

3. A megbízhatósági kezelés szakaszban a **helyi szolgáltató** területen hajtsa végre a következő lépéseket:

    ![A "megbízhatósági kezelés" szakaszt a "helyi szolgáltató" lapon és az összes kijelölt szövegmezőben ábrázoló képernyőkép.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Megbízhatóság kezelése")
   
    a. Kattintson a **Szerkesztés** gombra.

    b. A **konfiguráció típusa** mezőben válassza az **Egyéni** lehetőséget.

    c. A **helyi szolgáltató neve** beállításnál hagyja meg az alapértelmezett értéket. Másolja ezt az értéket, és illessze be az SAP Cloud platformhoz készült Azure AD-konfiguráció **azonosító** mezőjébe.

    d. Az **aláíró kulcs** és az **aláíró tanúsítvány** kulcspár létrehozásához kattintson a kulcspár **létrehozása** lehetőségre.

    e. **Elsődleges propagálásként** válassza a **Letiltva** lehetőséget.

    f. **Kényszerített hitelesítésként** válassza a **Letiltva** lehetőséget.

    : Kattintson a **Mentés** gombra.

4. A **helyi szolgáltató** beállításainak mentése után hajtsa végre a következőt a válasz URL-címének beszerzéséhez:
   
    ![Metaadatok beolvasása](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metaadatok beolvasása")

    a. Töltse le az SAP Cloud platform metaadat-fájlját a **metaadatok lekérése** elemre kattintva.

    b. Nyissa meg a letöltött SAP Cloud platform metadata XML-fájlt, és keresse meg a **ns3: AssertionConsumerService** címkét.
 
    c. Másolja a **Location** attribútum értékét, majd illessze be az SAP Cloud platformhoz készült Azure ad **-konfiguráció válasz URL** mezőjébe.

5. Kattintson a **megbízható identitás-szolgáltató** fülre, majd a **megbízható identitás-szolgáltató hozzáadása** lehetőségre.
   
    ![Képernyőkép, amely a "megbízható identitás-szolgáltató" lapon bejelölt "megbízhatósági kezelés" lapot mutatja.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Megbízhatóság kezelése")
   
    >[!NOTE]
    >A megbízható identitás-szolgáltatók listájának kezeléséhez a helyi szolgáltató szakaszban kell megadnia az egyéni konfiguráció típusát. Az alapértelmezett konfigurációs típus esetében az SAP ID szolgáltatás nem szerkeszthető és implicit megbízhatósági kapcsolattal rendelkezik. Nincs, nem rendelkezik megbízhatósági beállításokkal.
    > 
    > 

6. Kattintson az **általános** fülre, majd a **Tallózás** gombra a letöltött metaadat-fájl feltöltéséhez.
    
    ![Megbízhatóság kezelése](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Megbízhatóság kezelése")
    
    >[!NOTE]
    >A metaadat-fájl feltöltése után a rendszer automatikusan kitölti az **egyszeri bejelentkezési URL-cím**, az **egyszeri KIJELENTKEZÉSI URL-cím** és az **aláíró tanúsítvány** értékét.
    > 
     
7. Kattintson az **Attribútumok** fülre.

8. Az **attribútumok** lapon hajtsa végre a következő lépést:
    
    ![Attribútumok](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attribútumok") 

    a. Kattintson a **Assertion-Based attribútum hozzáadása** lehetőségre, majd adja hozzá a következő kijelentési attribútumokat:
       
    | Érvényesítési attribútum | Elsődleges attribútum |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |LastName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Az attribútumok konfigurációja attól függ, hogy az adott alkalmazás (ok) hogyan lett kifejlesztve, azaz hogy mely attribútum (ok) vár az SAML-válaszban, és milyen névvel (a résztvevő attribútummal) férhetnek hozzá ehhez az attribútumhoz a kódban.
     > 
    
    b. A képernyőkép **alapértelmezett attribútuma** csak illusztrációs célokat szolgál. A forgatókönyv működéséhez nem szükséges.  
 
    c. Az alkalmazás fejlesztésének módjától függ, hogy a **fő attribútum** neve és értékei milyen módon jelennek meg a képernyőképen. Lehetséges, hogy az alkalmazásnak eltérő leképezéseket kell használnia.

### <a name="assertion-based-groups"></a>Kijelentési alapú csoportok

Választható lépésként konfigurálhatja az Azure Active Directory identitás-szolgáltatóhoz tartozó érvényesítő csoportokat is.

A csoportok használata az SAP Cloud platformon lehetővé teszi, hogy dinamikusan rendeljen hozzá egy vagy több felhasználót egy vagy több szerepkörhöz az SAP Cloud platform-alkalmazásokban, az SAML 2,0-utasítás attribútumainak értékei alapján. 

Ha például a "*Szerződés = ideiglenes*" attribútumot tartalmazza, az összes érintett felhasználót fel kell venni az "*ideiglenes*" csoportba. Az "*ideiglenes*" csoport egy vagy több olyan szerepkört tartalmazhat, amelyek egy vagy több, az SAP Cloud platform-fiókban üzembe helyezett alkalmazásból származnak.
 
Ha egyszerre több felhasználót szeretne hozzárendelni egy vagy több szerepkörhöz az SAP Cloud platform-fiókban, használja az állítási alapú csoportokat. Ha csak egy vagy több felhasználót szeretne hozzárendelni bizonyos szerepkörökhöz, azt javasoljuk, hogy közvetlenül az SAP Cloud platform cockpit "**engedélyek**" lapján Rendeljen hozzájuk.

### <a name="create-sap-cloud-platform-test-user"></a>SAP Cloud platform tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az SAP Cloud platformba, hozzá kell rendelnie a szerepköröket az SAP Cloud platformon.

**Ha szerepkört szeretne hozzárendelni egy felhasználóhoz, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **SAP Cloud platform** cockpitbe.

2. Hajtsa végre a következőket:
   
    ![Engedélyek](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Engedélyek")
   
    a. Kattintson az **Engedélyezés** elemre.

    b. Kattintson a **felhasználók** fülre.

    c. A **felhasználó** szövegmezőbe írja be a felhasználó e-mail-címét.

    d. Kattintson a **hozzárendelés** elemre a felhasználó szerepkörhöz rendeléséhez.

    e. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az SAP Cloud platform bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az SAP Cloud platform bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások SAP Cloud platform csempére kattint, automatikusan be kell jelentkeznie az SAP Cloud platformra, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az SAP Cloud platform konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).