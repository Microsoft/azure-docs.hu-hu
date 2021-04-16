---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az AWS-Single-Account Access | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az AWS Single-Account Access között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537731"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja az AWS Single-Account Accessvel

Ez az oktatóanyag bemutatja, hogyan integrálhatja az AWS Single-Account Accesst Azure Active Directory (Azure AD) használatával. Ha az AWS-t Single-Account Accesst az Azure AD-val integrálja, a következőt tudja:

* Vezérlés az Azure AD-ban, aki hozzáféréssel rendelkezik az AWS-hez Single-Account Hozzáférés.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az AWS-be Single-Account Hozzáférés az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Az Azure AD alkalmazáskatatárban található különböző AWS-alkalmazások ismertetése
Az alábbi információk alapján döntsen az AWS Single Sign-On és az AWS Single-Account Access alkalmazások azure AD-alkalmazáskatatárban való használata között.

**AWS egyszeri bejelentkezés**

[Az AWS egyszeri bejelentkezés](./aws-single-sign-on-tutorial.md) 2021 februárjában lett hozzáadva az Azure AD alkalmazáskatatárhoz. Megkönnyíti a több AWS-fiókhoz és AWS-alkalmazáshoz való hozzáférés központi kezelését a bejelentkezéssel a Microsoft Azure AD. Az Microsoft Azure AD az AWS SSO-val, és az AWS SSO-val egyetlen helyről kezelheti az összes AWS-fiók engedélyeinek kezelését. Az AWS SSO automatikusan kijavalja az engedélyeket, és a szabályzatok és a hozzáférés-hozzárendelések frissítése során megőrzi az engedélyeket. A végfelhasználók Azure AD hitelesítő adataik használatával hitelesítve férhetnek hozzá az AWS-konzolhoz, a parancssori felülethez és az AWS SSO-val integrált alkalmazásokhoz.

**AWS Single-Account Access**

[Az AWS Single-Account Accesst]() az ügyfelek az elmúlt néhány évben használták, és lehetővé teszi, hogy az Azure AD-t egyetlen AWS-fiókkal egyesítődve kezelje az AWS IAM-szerepkörökhöz való hozzáférést. Az AWS IAM-rendszergazdák szerepköröket és szabályzatokat határoznak meg az egyes AWS-fiókokban. Minden AWS-fiók esetében az Azure AD-rendszergazdák összeadnak az AWS IAM-hez, felhasználókat vagy csoportokat rendelnek a fiókhoz, és konfigurálják az Azure AD-t, hogy a szerepkör-hozzáférést engedélyező helyességi érvényeket küldjenek.  

| Szolgáltatás | AWS Single Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|Feltételes hozzáférés| Egyetlen feltételes hozzáférési szabályzatot támogat az összes AWS-fiókhoz. | Egyetlen feltételes hozzáférési szabályzatot támogat minden fiókhoz vagy fiókonként egyéni szabályzatokat|
| CLI-hozzáférés | Támogatott | Támogatott|
| Privileged Identity Management | Még nem támogatott | Még nem támogatott |
| Fiókkezelés központosította | Központosíthatja a fiókkezelést az AWS-ban. | Központosíthatja a fiókkezelést az Azure AD-ban (valószínűleg fiókonként egy Vállalati Azure AD-alkalmazásra lesz szükség). |
| SAML-tanúsítvány| Egyetlen tanúsítvány| Tanúsítványokat különít el alkalmazásonként/fiókonként | 

## <a name="aws-single-account-access-architecture"></a>Az AWS Single-Account Access architektúrája
![Az Azure AD és az AWS közötti kapcsolat ábrája](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Több azonosítót is konfigurálhat több példányhoz. Például:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel az Azure AD eltávolítja a értékét, és a megfelelő értéket küldi el célközönségi URL-címként az **#** `https://signin.aws.amazon.com/saml` SAML-jogkivonatban.

Ezt a megközelítést a következő okokból javasoljuk:

- Minden alkalmazás egyedi X509-tanúsítványt biztosít. Az AWS-alkalmazáspéldányok minden példánya más-más tanúsítvány lejárati dátummal is rendelkezik, amely egyéni AWS-fiók alapján kezelhető. Ebben az esetben a tanúsítvány teljes átváltása egyszerűbb.

- Engedélyezheti a felhasználóátépítést egy AWS-alkalmazással az Azure AD-ban, majd a szolgáltatás lekéri az összes szerepkört az adott AWS-fiókból. Az AWS-szerepköröket nem kell manuálisan hozzáadnia vagy frissítenie az alkalmazásban.

- Az alkalmazás tulajdonosát egyenként is hozzárendelheti az alkalmazáshoz. Ez a személy közvetlenül az Azure AD-ban kezelheti az alkalmazást.

> [!Note]
> Ügyeljen arra, hogy csak katalógusalkalmazást használjon.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Egyszeri bejelentkezést (SSO) engedélyező AWS-előfizetés.

> [!Note]
> A szerepköröket nem lehet manuálisan szerkeszteni az Azure AD-ban szerepkör-importáláskor.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* Az AWS Single-Account Access támogatja az **SP és az idP által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>AWS Single-Account hozzáférés a katalógusból

Az AWS Single-Account Access az Azure AD-be való integrálásának konfigurálához hozzá kell adni az AWS Single-Account Accesst a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi fiókkal, iskolai fiókkal vagy személyes Microsoft-fiók.
1. A Azure Portal keresse meg és válassza a **Azure Active Directory.**
1. Az áttekintési Azure Active Directory válassza a **Vállalati alkalmazások Minden alkalmazás**  >  **lehetőséget.**
1. Válassza **az Új alkalmazás** lehetőséget egy alkalmazás hozzáadásához.
1. A Hozzáadás **a katalógusból szakaszban** írja be az **AWS Single-Account a keresőmezőbe.**
1. Válassza **az AWS Single-Account Hozzáférés az** eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Az Azure AD SSO konfigurálása és tesztelése az AWS Single-Account Accesshez

Konfigurálja és tesztelje az Azure AD SSO-t az AWS Single-Account Access használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és az AWS hozzáféréssel kapcsolatos Single-Account között.

Ha az Azure AD SSO-t az AWS Single-Account Access használatával konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az AWS Single-Account SSO](#configure-aws-single-account-access-sso)** konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Hozzon létre egy AWS Single-Account](#create-aws-single-account-access-test-user)** Access-tesztfelhasználót – hogy a B.Simon megfelelője legyen az AWS Single-Account Accessben, amely a felhasználó Azure AD-ábrázolásával van összekapcsolva.
    1. **[Szerepkör-kiépítés konfigurálása az AWS-Single-Account Accessben](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **az AWS Single-Account Access** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer kiválasztása lapon** válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az **SamL-konfiguráció** egyszerű beállításainak szerkesztéséhez/toll ikonjára a beállítások szerkesztéséhez.

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció** szakaszban frissítse az **Azonosító (Entitásazonosító)** és a Válasz **URL-cím** értékét is ugyanazokkal az alapértelmezett értékkel: `https://signin.aws.amazon.com/saml` . A konfigurációs módosítások **mentéshez** a Mentés lehetőséget kell választania.

1. Ha több példányt konfigurál, adjon meg egy azonosító értéket. A második példánytól kezdve használja a következő formátumot, beleértve egy előjelet egy egyedi **#** SPN-érték megadásához.

    `https://signin.aws.amazon.com/saml#2`

1. Az AWS-alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az AWS-alkalmazás arra számít, hogy néhány további attribútumot kell visszaadódni SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.
    
    | Name  | Forrásattribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName (Szerepkörnév) | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Szerepkör | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration (Munkamenet-kezelés) | "adjon meg egy 900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket." |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > Az AWS az alkalmazáshoz rendelt felhasználók szerepköreit várja. Ezeket a szerepköröket állítsa be az Azure AD-ban, hogy a felhasználókhoz hozzá tudja rendelni a megfelelő szerepköröket. A szerepkörök Azure AD-beli konfigurálásának mikéntét itt [láthatja](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon, az **SAML** aláíró tanúsítvány (3. lépés) párbeszédpanelen válassza a Tanúsítvány **hozzáadása lehetőséget.**

    ![Új SAML-tanúsítvány létrehozása](common/add-saml-certificate.png)

1. Hozzon létre egy új SAML aláíró tanúsítványt, majd válassza az **Új tanúsítvány lehetőséget.** Adjon meg egy e-mail-címet a tanúsítványértesítések számára.
   
    ![Új SAML-tanúsítvány](common/new-saml-certificate.png) 

1. Az **SAML aláíró tanúsítvány szakaszban** keresse meg  az összevonási metaadatok XML-fájlját, és válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez. 

    ![A Tanúsítvány letöltése hivatkozás](./media/amazon-web-service-tutorial/certificate.png)

1. Az **AWS-hozzáférés Single-Account szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A Azure Portal keresse meg és válassza **a** Azure Active Directory.
1. A Azure Active Directory menüben válassza a **Felhasználók Minden** felhasználó  >  **lehetőséget.**
1. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja fel a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát úgy, hogy hozzáférést ad az AWS-hez Single-Account hozzáféréshez.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az AWS és **Single-Account lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása legördülő** menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-aws-single-account-access-sso"></a>Az AWS Single-Account SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AWS vállalati webhelyre rendszergazdaként.

2. Válassza **az AWS Kezdőlap lehetőséget.**

    ![Képernyőkép az AWS vállalati webhelyről, kiemelt AWS Kezdőlap ikonnal][11]

3. Válassza **az Identitás- és hozzáférés-kezelés lehetőséget.**

    ![Képernyőkép az AWS-szolgáltatások oldalról, kiemelt IAM-sel][12]

4. Válassza **az Identitásszolgáltatók**  >  **Szolgáltató létrehozása lehetőséget.**

    ![Képernyőkép az IAM lapról, az Identitásszolgáltatók és a Szolgáltató létrehozása kiemeléssel][13]

5. A Szolgáltató **konfigurálása lapon** hajtsa végre a következő lépéseket:

    ![Képernyőkép a szolgáltató konfigurálásról][14]

    a. A **Szolgáltató típusa mezőben** válassza az **SAML lehetőséget.**

    b. A **Szolgáltató neve mezőbe** írjon be egy szolgáltatónevet (például: *WAAD).*

    c. A letöltött metaadatfájl **feltöltéséhez** a Azure Portal válassza a **Fájl kiválasztása lehetőséget.**

    d. Válassza **a Következő lépés lehetőséget.**

6. A **Szolgáltatói adatok ellenőrzése lapon** válassza a Létrehozás **lehetőséget.**

    ![A Szolgáltatói adatok ellenőrzése képernyőképe a Létrehozás kiemeléssel][15]

7. Válassza **a Szerepkörök Szerepkör** létrehozása  >  **lehetőséget.**

    ![Képernyőkép a Szerepkörök lapról][16]

8. A Szerepkör **létrehozása lapon** hajtsa végre a következő lépéseket:  

    ![Képernyőkép a Szerepkör létrehozása lapról][19]

    a. A **Megbízható entitás típusának kiválasztása alatt válassza** az **SAML 2.0 összevonás lehetőséget.**

    b. Az **SAML 2.0-szolgáltató kiválasztása** alatt válassza ki a korábban létrehozott **SAML-szolgáltatót** (például: *WAAD).*

    c. Válassza **a Programozott és az AWS felügyeleti konzol hozzáférésének engedélyezése lehetőséget.**
  
    d. Válassza a **Next: Permissions** (Tovább: Engedélyek) lehetőséget.

9. Az **Engedély-szabályzatok csatolása** párbeszédpanelen csatolja a szervezetnek megfelelő szabályzatot. Ezután válassza **a Tovább: Áttekintés lehetőséget.**  

    ![Képernyőkép az Engedélyek csatolása szabályzat párbeszédpanelről][33]

10. Az Áttekintés **párbeszédpanelen** hajtsa végre a következő lépéseket:

    ![Az Áttekintés párbeszédpanel képernyőképe][34]

    a. A **Szerepkör neve alatt** adja meg a szerepkör nevét.

    b. A **Szerepkör leírása mezőben** adja meg a leírást.

    c. Válassza **a Szerepkör létrehozása lehetőséget.**

    d. Hozzon létre annyi szerepkört, amennyit csak szükséges, és leképezi őket az identitásszolgáltatóra.

11. Az AWS-szolgáltatásfiók hitelesítő adataival lekéri a szerepköröket az AWS-fiókból az Azure AD-felhasználóátépítésben. Ehhez nyissa meg az AWS-konzol kezdőlapját.

12. Válassza a **Szolgáltatások lehetőséget.** A **Biztonság, Identitás és & alatt** válassza az **IAM lehetőséget.**

    ![Képernyőkép az AWS-konzol kezdőlapról, kiemelt Szolgáltatások és IAM kiemelésével](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Az IAM szakaszban válassza a **Házirendek lehetőséget.**

    ![Képernyőkép az IAM szakaszról, kiemelt Szabályzatokkal](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Hozzon létre egy új szabályzatot a **Create policy** (Szabályzat létrehozása) lehetőség kiválasztásával, hogy be tudja kérni a szerepköröket az AWS-fiókból az Azure AD-felhasználóátépítésben.

    ![Képernyőkép a Szerepkör létrehozása lapról, kiemelt Szabályzat létrehozása gombra](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Hozzon létre egy saját szabályzatot, amely lekéri az összes szerepkört az AWS-fiókokból.

    ![Képernyőkép a Szabályzat létrehozása lapról, kiemelt JSON-nal](./media/amazon-web-service-tutorial/policy1.png)

    a. A **Szabályzat létrehozása lapon** válassza a **JSON** lapot.

    b. A szabályzatdokumentumban adja hozzá a következő JSON-t:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. A **szabályzat érvényesítéséhez válassza** a Szabályzat áttekintése lehetőséget.

    ![Képernyőkép a Szabályzat létrehozása lapról](./media/amazon-web-service-tutorial/policy5.png)

16. Határozza meg az új szabályzatot.

    ![Képernyőkép a Szabályzat létrehozása lapról, kiemelt Név és Leírás mezővel](./media/amazon-web-service-tutorial/policy2.png)

    a. A **Név alatt** adja meg a **AzureAD_SSOUserRole_Policy.**

    b. A **Leírás mezőben** adja meg a következőt: Ez a szabályzat lehetővé teszi a szerepkörök **lekérését az AWS-fiókokból.**

    c. Válassza a **Create policy** (Szabályzat létrehozása) lehetőséget.

17. Hozzon létre egy új felhasználói fiókot az AWS IAM szolgáltatásban.

    a. Az AWS IAM-konzolon válassza a **Felhasználók lehetőséget.**

    ![Képernyőkép az AWS IAM-konzolról, kiemelt Users (Felhasználók) gombra](./media/amazon-web-service-tutorial/policy3.png)

    b. Új felhasználó létrehozásához válassza a **Felhasználó hozzáadása lehetőséget.**

    ![Képernyőkép a Felhasználó hozzáadása gombról](./media/amazon-web-service-tutorial/policy4.png)

    c. A Felhasználó **hozzáadása szakaszban:**

    ![Képernyőkép a Felhasználó hozzáadása lapról, a Felhasználónév és a Hozzáférés típusa kiemeléssel](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet **AzureADRoleManager néven.**

    * A hozzáférés típusaként válassza a **Programozott hozzáférés lehetőséget.** Így a felhasználó meghívhatja az API-kat, és lekérheti a szerepköröket az AWS-fiókból.

    * Válassza **a Következő engedélyek lehetőséget.**

18. Hozzon létre egy új szabályzatot ehhez a felhasználóhoz.

    ![Képernyőkép a Felhasználó hozzáadása oldalról, ahol létrehozhat egy szabályzatot a felhasználó számára.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Válassza a **Meglévő szabályzatok közvetlen csatolása lehetőséget.**

    b. Keresse meg az újonnan létrehozott szabályzatot a szűrési **szakaszban a AzureAD_SSOUserRole_Policy.**

    c. Válassza ki a szabályzatot, majd válassza **a Tovább: Áttekintés lehetőséget.**

19. Tekintse át a csatolt felhasználóra vonatkozó szabályzatot.

    ![Képernyőkép a Felhasználó hozzáadása lapról, kiemelt Felhasználó létrehozása gombra](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználóhoz hozzárendelt felhasználónevet, hozzáférési típust és szabályzatot.

    b. Válassza **a Felhasználó létrehozása lehetőséget.**

20. Töltse le a felhasználó hitelesítő adatait.

    ![Képernyőkép a Felhasználó hozzáadása lapról a Felhasználói hitelesítő adatok lekért c s v gombbal.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja ki a felhasználó **hozzáférési kulcsának azonosítóját** **és titkos hozzáférési kulcsát.**

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználóátépítés szakaszban a szerepkörök az AWS-konzolról való lekéréséhez.

    c. Válassza a **Bezárás** gombot.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Szerepkör-kiépítés konfigurálása az AWS-Single-Account Accessben

1. Az Azure AD felügyeleti portálon, az AWS-alkalmazásban, a Kiépítés **oldalon.**

    ![Képernyőkép az AWS-alkalmazásról a kiépítés kiemelésével](./media/amazon-web-service-tutorial/provisioning.png)

2. Adja meg a hozzáférési kulcsot és a titkos adatokat az **clientsecret** és **a Secret Token (Titkos** jogkivonat) mezőkben.

    ![A Rendszergazdai hitelesítő adatok párbeszédpanel képernyőképe](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot az **clientsecret mezőben.**

    b. A Secret Token (Titkos jogkivonat) mezőbe írja be az AWS felhasználói titkos secret **(Titkos jogkivonat)** mezőjét.

    c. Válassza a **Kapcsolat tesztelése** lehetőséget.

    d. Mentse a beállítást a Mentés **lehetőség kiválasztásával.**

3. A Beállítások **szakaszban** a **Kiépítési állapot beállításnál** válassza a **Be lehetőséget.** Kattintson a **Mentés** gombra.

    ![Képernyőkép a Beállítások szakaszról, kiemelt Be beállítással](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> A kiépítési szolgáltatás csak az AWS-ről importál szerepköröket az Azure AD-be. A szolgáltatás nem biztosít felhasználókat és csoportokat az Azure AD-ről az AWS-be.

> [!NOTE]
> A kiépítési hitelesítő adatok mentése után meg kell várnia a kezdeti szinkronizálási ciklus futását. A szinkronizálás általában körülbelül 40 percet vesz igénybe. Az állapot a Kiépítés lap  alján, az Aktuális **állapot alatt látható.**

### <a name="create-aws-single-account-access-test-user"></a>AWS Single-Account tesztfelhasználó létrehozása

Ennek a szakasznak a célja egy B.Simon nevű felhasználó létrehozása az AWS-ben Single-Account Hozzáférés. Az AWS Single-Account Accessnek nincs szüksége arra, hogy a rendszerben létre legyen hozva egy felhasználó az SSO-hoz, így itt nem kell semmilyen műveletet végrehajtania.

## <a name="test-sso"></a>Az SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja az AWS-Single-Account bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen az AWS Single-Account a bejelentkezési URL-cím közvetlen eléréséhez, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve az AWS Single-Account Accessbe, amelyhez beállította az SSO-t 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha az Saját alkalmazások-ban az AWS Single-Account Access csempére kattint, a rendszer sp módban átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie az AWS Single-Account Accessbe, amelyhez az SSO-t beállította. További információ a Saját alkalmazások: [Bevezetés a](../user-help/my-apps-portal-end-user-access.md)Saját alkalmazások.


## <a name="known-issues"></a>Ismert problémák

* Az AWS Single-Account access provisioning integration csak az AWS nyilvános felhőbeli végpontjaihoz való csatlakozáshoz használható. Az AWS Single-Account access provisioning integration nem használható az AWS Government-környezetek eléréséhez.
 
* A **Kiépítés szakaszban** a **Leképezések** alszakaszban a "Betöltés..." üzenetet, és soha nem jeleníti meg az attribútumleképezéseket. Jelenleg az egyetlen támogatott kiépítési munkafolyamat az AWS-szerepkörök importálása az Azure AD-be a felhasználók vagy csoportok hozzárendelése során történő kiválasztáshoz. Ennek attribútumleképezései előre meghatározva vannak, és nem konfigurálhatók.

* A **Kiépítés szakasz** egyszerre csak egy AWS-bérlőhöz támogatja a hitelesítő adatok egy készletének megadását. Minden importált szerepkör az `appRoles` AWS-bérlő [ `servicePrincipal` ](/graph/api/resources/serviceprincipal) Azure AD-objektumának tulajdonságára lesz írva.

  A katalógusból több AWS-bérlő is hozzáadható az `servicePrincipals` Azure AD-hez kiépítésre. Van azonban egy ismert probléma, amely miatt nem tudja automatikusan megírni az összes importált szerepkört az egyszeri bejelentkezéshez használt több AWS-ről, amelyeket a kiépítéshez `servicePrincipals` `servicePrincipal` használnak.

  Áthidaló megoldásként a [Microsoft Graph API-val](/graph/api/resources/serviceprincipal) kinyerheti az összes importált adatokat minden olyan AWS-be, ahol a `appRoles` `servicePrincipal` kiépítés konfigurálva van. Ezt követően hozzáadhatja ezeket a szerepköri sztringeket ahhoz az AWS-hez, ahol `servicePrincipal` az SSO konfigurálva van.

* Ahhoz, hogy az AWS-ről importálni tudja az Azure AD-be, a szerepköröknek meg kell felelnie a következő követelményeknek:

  * A szerepköröknek pontosan egy, az AWS-ban meghatározott saml-szolgáltatóval kell
  * A szerepkör ARN-jának (Amazon-erőforrás neve) és a társított saml-szolgáltató ARN-ének együttes hossza nem lehet hosszabb 240 karakternél.

## <a name="change-log"></a>Változási napló

* 2020.01.12. – A szerepkör hosszkorlátja 119 karakterről 239 karakterre nőtt. 

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az AWS Single-Account Accesst, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Megtudhatja, hogyan kényszeríthető ki a munkamenet-vezérlés Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png