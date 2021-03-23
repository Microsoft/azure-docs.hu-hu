---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az AWS Single-Account-hozzáféréssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AWS Single-Account hozzáférés között.
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
ms.openlocfilehash: 842ab27fe02501efbbc6c06c3d36d2218c3c17b9
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799241"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az AWS Single-Account-hozzáféréssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az AWS Single-Account hozzáférést a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AWS Single-Account az Azure AD-val való hozzáférését, a következőket teheti:

* Az AWS Single-Account hozzáféréshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az AWS Single-Account hozzáférésre az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>A különböző AWS-alkalmazások megismerése az Azure AD Application Galleryben
Az alábbi információk segítségével döntéseket hozhat az AWS Single Sign-On és AWS Single-Account Access Applications az Azure AD Application Galleryben.

**AWS egyszeri bejelentkezés**

Az [AWS egyszeri bejelentkezés](./aws-single-sign-on-tutorial.md) a 2021 februárjában lett hozzáadva az Azure ad Application Gallery-hez. Megkönnyíti a hozzáférést központilag több AWS-fiókhoz és AWS-alkalmazásokhoz, a bejelentkezést Microsoft Azure ADon keresztül. A összevonása az AWS egyszeri bejelentkezéssel Microsoft Azure AD egyszer, az AWS SSO használatával pedig egyetlen helyről kezelheti az összes AWS-fiók engedélyeit. Az AWS egyszeri bejelentkezésre vonatkozó engedélyei automatikusan, a házirendek és a hozzáférési hozzárendelések frissítésekor pedig naprakészek maradnak. A végfelhasználók Azure AD-beli hitelesítő adataikkal hitelesíthetők az AWS konzol, a parancssori felület és az AWS SSO integrált alkalmazások eléréséhez.

**AWS Single-Account hozzáférés**

Az [aws Single-Account a hozzáférést]() az elmúlt néhány évben az ügyfelek használták, és lehetővé teszi, hogy egyetlen AWS-fiókkal összevonása az Azure ad-t, és az Azure ad segítségével kezelje az AWS iam-szerepkörökhöz való hozzáférést. Az AWS IAM-rendszergazdák minden AWS-fiókban határozzák meg a szerepköröket és a szabályzatokat. Minden AWS-fiók esetében az Azure AD-rendszergazdák összevonása az AWS IAM-t, felhasználókat vagy csoportokat rendelnek hozzá a fiókhoz, és az Azure AD-t úgy konfigurálja, hogy a szerepkör-hozzáférést engedélyező jogcímeket küldje el.  

| Szolgáltatás | AWS egyszeri Sign-On | AWS Single-Account hozzáférés |
|:--- |:---:|:---:|
|Feltételes hozzáférés| Egyetlen feltételes hozzáférési szabályzatot támogat az összes AWS-fiókhoz. | Egyetlen feltételes hozzáférési szabályzatot támogat az összes fiókhoz vagy egyéni házirendhez egy fiókban|
| CLI-hozzáférés | Támogatott | Támogatott|
| Privileged Identity Management | Még nem támogatott | Még nem támogatott |
| Fiókkezelés központosított kezelése | Az AWS-ben központosíthatja a fiókok kezelését. | Az Azure AD-fiók kezelésének központosítása (valószínűleg egy Azure AD Enterprise-alkalmazásra lesz szükség). |
| SAML-tanúsítvány| Egyetlen tanúsítvány| Külön tanúsítvány/alkalmazás/fiók | 

## <a name="aws-single-account-access-architecture"></a>AWS Single-Account-hozzáférési architektúra
![Az Azure AD és az AWS kapcsolatának ábrája](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Több példányhoz is konfigurálhat több azonosítót. Például:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel az Azure AD eltávolítja a értékét **#** , és elküldi a megfelelő értéket a `https://signin.aws.amazon.com/saml` célközönség URL-címének az SAML-jogkivonatban.

Ezt a megközelítést a következő okok miatt javasoljuk:

- Az egyes alkalmazások egyedi X509-tanúsítványt biztosítanak. Az AWS-alkalmazások példányainak minden példánya rendelkezhet egy másik tanúsítvány lejárati dátumával, amely külön AWS-fiók alapján kezelhető. Ebben az esetben a teljes tanúsítvány-váltás egyszerűbb.

- Az Azure AD-ben egy AWS-alkalmazással engedélyezheti a felhasználók üzembe helyezését, majd a szolgáltatás az AWS-fiókból beolvassa az összes szerepkört. Nem kell manuálisan hozzáadnia vagy frissítenie az AWS-szerepköröket az alkalmazásban.

- Az alkalmazás tulajdonosát egyenként is hozzárendelheti az alkalmazáshoz. Ez a személy közvetlenül az Azure AD-ben tudja kezelni az alkalmazást.

> [!Note]
> Győződjön meg arról, hogy csak a gyűjtemény alkalmazásait használja.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AWS egyszeri bejelentkezést (SSO) engedélyező előfizetés.

> [!Note]
> A szerepkörök importálásakor a szerepköröket nem szabad manuálisan szerkeszteni az Azure AD-ben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az AWS Single-Account-hozzáférés támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>AWS Single-Account-hozzáférés hozzáadása a katalógusból

Az AWS Single-Account hozzáférésének konfigurálásához az Azure AD-ben hozzá kell adnia az AWS-Single-Account hozzáférést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi fiók, iskolai fiók vagy személyes Microsoft-fiók használatával.
1. A Azure Portal keresse meg és válassza a **Azure Active Directory** lehetőséget.
1. A Azure Active Directory áttekintése menüben válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **AWS Single-Account hozzáférés** elemet a keresőmezőbe.
1. Válassza az **AWS Single-Account hozzáférés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Azure AD SSO konfigurálása és tesztelése az AWS Single-Account hozzáféréshez

Konfigurálja és tesztelje az Azure AD SSO-t az AWS-Single-Account hozzáférését egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az AWS Single-Account hozzáférés szolgáltatásban.

Az Azure AD SSO és az AWS Single-Account hozzáférésének konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[AWS Single-Account Access SSO konfigurálása](#configure-aws-single-account-access-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre aws Single-Account Access test User](#create-aws-single-account-access-test-user)** -t, hogy az AWS-ben B. Simon-beli párja legyen Single-Account a felhasználó Azure ad-beli képviseletéhez kapcsolódó hozzáférés.
    1. **[Szerepkör-kiépítés konfigurálása az AWS-ben Single-Account hozzáférés](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **AWS Single-Account Access** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban frissítse az **azonosítót (Entity ID)** és a **Válasz URL-címet** ugyanazzal az alapértelmezett értékkel: `https://signin.aws.amazon.com/saml` . A konfigurációs módosítások mentéséhez a **Mentés** lehetőséget kell választania.

1. Ha egynél több példányt konfigurál, adjon meg egy azonosító értéket. A második példánytól kezdődően a következő formátumot használja, beleértve egy **#** egyedi SPN érték megadására szolgáló jelet is.

    `https://signin.aws.amazon.com/saml#2`

1. Az AWS-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az AWS-alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name  | Forrás attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Szerepkör | User. assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket adjon meg" |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > Az AWS szerepköröket vár az alkalmazáshoz rendelt felhasználók számára. Állítsa be ezeket a szerepköröket az Azure AD-ben, hogy a felhasználók hozzá lehessen rendelni a megfelelő szerepköröket. A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** (3. lépés) párbeszédpanelen válassza a **tanúsítvány hozzáadása** elemet.

    ![Új SAML-tanúsítvány létrehozása](common/add-saml-certificate.png)

1. Új SAML-aláíró tanúsítvány létrehozása, majd az **új tanúsítvány** kiválasztása. Adjon meg egy e-mail-címet a tanúsítványok értesítéseihez.
   
    ![Új SAML-tanúsítvány](common/new-saml-certificate.png) 

1. Az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](./media/amazon-web-service-tutorial/certificate.png)

1. Az **AWS Single-Account-hozzáférés beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal keresse meg és válassza a **Azure Active Directory** lehetőséget.
1. A Azure Active Directory áttekintése menüben válassza a **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az AWS Single-Account hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **AWS Single-Account hozzáférés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-aws-single-account-access-sso"></a>AWS-Single-Account hozzáférési SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AWS vállalati webhelyre rendszergazdaként.

2. Válassza az **AWS Kezdőlap** lehetőséget.

    ![Képernyőfelvétel az AWS vállalati webhelyről, az AWS Home ikon kiemelve][11]

3. Válassza **az identitás-és hozzáférés-kezelés** lehetőséget.

    ![Képernyőfelvétel az AWS-szolgáltatások oldalról, a IAM kiemelve][12]

4. Válassza az **identitás-szolgáltatók**  >  **Létrehozás szolgáltató** elemet.

    ![A IAM oldal, a személyazonosság-szolgáltatók és a Kiemelt szolgáltató létrehozása Képernyőkép][13]

5. A **szolgáltató konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![A szolgáltató konfigurálásának képernyőképe][14]

    a. A **szolgáltató típusa** beállításnál válassza az **SAML** lehetőséget.

    b. A **szolgáltató neve** mezőbe írja be a szolgáltató nevét (például: *WAAD*).

    c. A letöltött metaadat- **fájl** Azure Portal való feltöltéséhez válassza a **fájl kiválasztása** lehetőséget.

    d. Válassza a **következő lépés** lehetőséget.

6. A **szolgáltatói adatok ellenőrzése** lapon válassza a **Létrehozás** lehetőséget.

    ![Képernyőkép a szolgáltatói információk ellenőrzéséről, a létrehozás kiemelve][15]

7. Válassza a **szerepkörök**  >  **szerepkör létrehozása** lehetőséget.

    ![A szerepkörök lap képernyőképe][16]

8. A **szerepkör létrehozása** oldalon hajtsa végre a következő lépéseket:  

    ![A szerepkör létrehozása lap képernyőképe][19]

    a. A **megbízható entitás típusának kiválasztása** területen válassza az **SAML 2,0-összevonás** lehetőséget.

    b. Az **saml 2,0-szolgáltató kiválasztása** területen válassza ki a korábban létrehozott **SAML-szolgáltatót** (például: *WAAD*).

    c. Válassza **a programozott és AWS felügyeleti konzol hozzáférésének engedélyezése** lehetőséget.
  
    d. Válassza a **Next: Permissions** (Tovább: Engedélyek) lehetőséget.

9. Az **engedélyezési házirendek csatolása** párbeszédpanelen csatolja a megfelelő szabályzatot a szervezeten belül. Ezután válassza a **Tovább: felülvizsgálat** lehetőséget.  

    ![Az engedélyek házirend csatolása párbeszédpanel képernyőképe][33]

10. A **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A felülvizsgálati párbeszédpanel képernyőképe][34]

    a. A **szerepkör neve** mezőben adja meg a szerepkör nevét.

    b. A **szerepkör leírása** mezőben adja meg a leírást.

    c. Válassza a **szerepkör létrehozása** lehetőséget.

    d. Szükség szerint hozzon létre annyi szerepkört, és rendelje őket az identitás-szolgáltatóhoz.

11. Az AWS szolgáltatásfiók hitelesítő adataival beolvashatja a szerepköröket az AWS-fiókból az Azure AD-felhasználók üzembe helyezése során. Ehhez nyissa meg az AWS-konzolt kezdőlapját.

12. Válassza a **szolgáltatások** lehetőséget. A **biztonság, identitás & megfelelőség** területen válassza a **iam** lehetőséget.

    ![Képernyőfelvétel az AWS-konzol kezdőlapján, a szolgáltatások és a IAM kiemelve](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. A IAM szakaszban válassza a **házirendek** elemet.

    ![Az IAM szakasz képernyőképe – Kiemelt szabályzatok](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Hozzon létre egy új szabályzatot a szabályzat **létrehozása** elem kiválasztásával a szerepkörök beolvasásához az AWS-fiókból az Azure ad-felhasználók üzembe helyezése szolgáltatásban.

    ![Képernyőfelvétel a szerepkör létrehozása lapról, a szabályzat létrehozása kiemelve](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Hozzon létre saját szabályzatot az AWS-fiókok összes szerepkörének beolvasásához.

    ![Képernyőkép a szabályzat létrehozása lapról a JSON kijelölve](./media/amazon-web-service-tutorial/policy1.png)

    a. A **házirend létrehozása** lapon válassza a **JSON** fület.

    b. A házirend dokumentumban adja hozzá a következő JSON-t:

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

    c. Válassza **a házirend ellenőrzése** lehetőséget a szabályzat érvényesítéséhez.

    ![A házirend létrehozása lap képernyőképe](./media/amazon-web-service-tutorial/policy5.png)

16. Adja meg az új szabályzatot.

    ![A házirend létrehozása lap képernyőképe, a név és leírás mezők kiemelve](./media/amazon-web-service-tutorial/policy2.png)

    a. A **név** mezőbe írja be a következőt: **AzureAD_SSOUserRole_Policy**.

    b. A **Leírás** mezőbe írja be a szabályzatot, **amely lehetővé teszi, hogy az AWS-fiókokból beolvassa a szerepköröket**.

    c. Válassza a **Create policy** (Szabályzat létrehozása) lehetőséget.

17. Hozzon létre egy új felhasználói fiókot az AWS IAM szolgáltatásban.

    a. Az AWS IAM-konzolon válassza a **felhasználók** lehetőséget.

    ![Képernyőfelvétel az AWS IAM-konzolról, Kiemelt felhasználók](./media/amazon-web-service-tutorial/policy3.png)

    b. Új felhasználó létrehozásához válassza a **felhasználó hozzáadása** elemet.

    ![A felhasználó hozzáadása gomb képernyőképe](./media/amazon-web-service-tutorial/policy4.png)

    c. A **felhasználó hozzáadása** szakaszban:

    ![A felhasználó hozzáadása lap képernyőképe, a Felhasználónév és a hozzáférés típusa kiemelve](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet **AzureADRoleManager**.

    * A hozzáférési típushoz válassza a **programozott hozzáférés** lehetőséget. Így a felhasználó meghívhatja az API-kat, és beolvashatja a szerepköröket az AWS-fiókból.

    * Válassza a **következő engedélyek** lehetőséget.

18. Hozzon létre egy új szabályzatot ehhez a felhasználóhoz.

    ![Képernyőfelvétel: a felhasználó hozzáadása lap, amelyen létrehozhat egy szabályzatot a felhasználó számára.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Válassza a **meglévő házirendek csatolása közvetlenül** lehetőséget.

    b. Keresse meg az újonnan létrehozott szabályzatot a szűrő szakaszban **AzureAD_SSOUserRole_Policy**.

    c. Válassza ki a szabályzatot, majd kattintson a **Tovább gombra: Áttekintés**.

19. Tekintse át a szabályzatot a csatlakoztatott felhasználó számára.

    ![A felhasználó hozzáadása lap képernyőképe, a felhasználó kiemelve](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználóhoz rendelt felhasználónevet, hozzáférési típust és házirendet.

    b. Válassza a **felhasználó létrehozása** lehetőséget.

20. Töltse le a felhasználó felhasználói hitelesítő adatait.

    ![A felhasználói hitelesítő adatok beolvasásához a felhasználó hozzáadása lap Letöltés c s v gombját jeleníti meg.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja a felhasználói **hozzáférési kulcs azonosítóját** és a **titkos elérési kulcsot**.

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó kiépítési szakaszában a szerepkörök az AWS-konzolról való beolvasásához.

    c. Válassza a **Bezárás** gombot.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Szerepkör-kiépítés konfigurálása az AWS-ben Single-Account hozzáférés

1. Az Azure AD felügyeleti portálon, az AWS alkalmazásban lépjen a **kiépítés** elemre.

    ![Képernyőfelvétel az AWS-alkalmazásról a kiépítés kiemelésével](./media/amazon-web-service-tutorial/provisioning.png)

2. Adja meg a hozzáférési kulcsot és a titkos kódot a **clientsecret** és a **titkos jogkivonat** mezőiben.

    ![Képernyőkép a rendszergazdai hitelesítő adatok párbeszédpanelről](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot a **clientsecret** mezőben.

    b. Adja meg az AWS felhasználói titkot a **titkos jogkivonat** mezőben.

    c. Válassza a **Kapcsolat tesztelése** lehetőséget.

    d. Mentse a beállítást a **Mentés** lehetőség kiválasztásával.

3. A **Beállítások** szakaszban a **kiépítési állapotnál** válassza **a** be lehetőséget. Kattintson a **Mentés** gombra.

    ![Képernyőkép a Settings (beállítások) szakaszról a Kiemelt](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> A kiépítési szolgáltatás csak az AWS-ből az Azure AD-be importálja a szerepköröket. A szolgáltatás nem nyújt felhasználókat és csoportokat az Azure AD-ből AWS-re.

> [!NOTE]
> A kiépítési hitelesítő adatok mentése után meg kell várnia, hogy a kezdeti szinkronizálási ciklus fusson. A szinkronizálás általában körülbelül 40 percet vesz igénybe. Az állapotot a **kiépítési** lap alján, az **aktuális állapot** alatt tekintheti meg.

### <a name="create-aws-single-account-access-test-user"></a>AWS-Single-Account hozzáférés-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja, hogy egy B. Simon nevű felhasználót hozzon létre AWS Single-Account hozzáférés. Az AWS Single-Account hozzáféréshez nem szükséges, hogy a felhasználó a rendszerében hozzon létre egyszeri bejelentkezést, így nincs szükség semmilyen művelet elvégzésére.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az AWS Single-Account hozzáférés-bejelentkezés URL-címére, ahol kezdeményezheti a bejelentkezési folyamatot.  

* Lépjen az AWS Single-Account hozzáférés bejelentkezési URL-címére közvetlenül, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra az AWS-Single-Account-hozzáférésre, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások AWS Single-Account hozzáférés csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, akkor automatikusan be kell jelentkeznie az AWS Single-Account-hozzáférésre, amelyhez beállítja az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="known-issues"></a>Ismert problémák

* Az AWS Single-Account hozzáférés-létesítési integráció csak az AWS nyilvános Felhőbeli végpontokhoz való csatlakozáshoz használható. AWS Single-Account hozzáférés-létesítési integráció nem használható AWS kormányzati környezetek eléréséhez.
 
* A **kiépítési** szakaszban a **leképezések** alszakasz a "betöltés..." szakaszt jeleníti meg. üzenet, és soha nem jeleníti meg az attribútumok leképezéseit. Az egyetlen jelenleg támogatott kiépítési munkafolyamat az AWS-ből az Azure AD-be való, a felhasználó vagy a csoport hozzárendelése során történő kiválasztáshoz. Az attribútumhoz tartozó hozzárendelések előre meg vannak határozva, és nem konfigurálhatók.

* A **kiépítési** szakasz egyszerre csak egy AWS-bérlőhöz tartozó hitelesítő adatok megadását támogatja. Az összes importált szerepkör az AWS- `appRoles` bérlőhöz tartozó Azure ad- [ `servicePrincipal` objektum](/graph/api/resources/serviceprincipal?view=graph-rest-beta) tulajdonságára íródik.

  A katalógusból több AWS-bérlőt `servicePrincipals` is hozzáadhat az Azure ad-hoz az üzembe helyezéshez. Van azonban egy ismert probléma, amely nem képes automatikusan írni az összes importált szerepkört a több AWS-ből, amelyet `servicePrincipals` az egyszeri `servicePrincipal` bejelentkezéshez használtak.

  Megkerülő megoldásként a [Microsoft Graph API](/graph/api/resources/serviceprincipal?view=graph-rest-beta) -val kinyerheti az összes `appRoles` importált AWS-t, `servicePrincipal` ahol a kiépítés konfigurálva van. Ezeket a szerepkör-karakterláncokat később is hozzáadhatja az AWS-hez, `servicePrincipal` ahol az SSO konfigurálva van.

* A szerepköröknek a következő követelményeknek kell megfelelniük, hogy az AWS-ből az Azure AD-be való importálásra jogosult legyen:

  * A szerepköröknek pontosan egy SAML-szolgáltatót kell meghatároznia az AWS-ben
  * A szerepkörhöz tartozó ARN (Amazon-erőforrás neve) és a társított SAML-szolgáltatóhoz tartozó ARN együttes hossza nem haladja meg a 240 karaktert.

## <a name="change-log"></a>Változási napló

* 01/12/2020 – nagyobb a szerepkör hossza 119 karakter és 239 karakter között. 

## <a name="next-steps"></a>Következő lépések

Az AWS Single-Account-hozzáférés konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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