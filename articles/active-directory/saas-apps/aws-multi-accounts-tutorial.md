---
title: 'Oktatóanyag: Azure Active Directory integráció a Amazon Web Services használatával több fiók összekapcsolásához | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure AD és a Amazon Web Services (örökölt oktatóanyag) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "100384112"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Oktatóanyag: Azure Active Directory integráció a Amazon Web Services

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directoryt (Azure AD) a Amazon Web Services (AWS) szolgáltatással (örökölt oktatóanyag).

Ez az integráció a következő előnyöket biztosítja:

- Megadhatja az AWS-hez hozzáférő Azure AD-t.
- Lehetővé teheti, hogy a felhasználók az egyszeri bejelentkezés (SSO) használatával automatikusan bejelentkezzenek az AWS-be az Azure AD-fiókjával.
- A fiókokat egy központi helyen, a Azure Portal is kezelheti.

![Az AWS-vel való Azure AD-integráció ábrája.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Javasoljuk, hogy *ne* csatlakoztasson egyetlen AWS-alkalmazást az összes AWS-fiókhoz. Ehelyett azt javasoljuk, hogy használja az [Azure ad SSO-integrációt az AWS](./amazon-web-service-tutorial.md) használatával az AWS-fiók több példányának az Azure ad-ben több példányára való konfigurálásához. 

Javasoljuk, hogy a következő okokból *ne* csatlakoztasson egyetlen AWS-alkalmazást az összes AWS-fiókhoz:

* Ezt a módszert csak akkor használja, ha kis számú AWS-fiókkal és-szerepkörrel rendelkezik, mert ez a modell nem méretezhető, mert az AWS-fiókok és az azokban található szerepkörök száma nő. A módszer nem használja az AWS szerepkör-importálás funkciót az Azure AD-beli felhasználók kiosztásával, így manuálisan kell hozzáadnia, frissítenie vagy törölnie a szerepköröket. 

* Az alkalmazás összes szerepkörének javításához a Microsoft Graph Explorer megközelítését kell használnia. A manifest file megközelítést nem ajánlott használni.

* Az ügyfelek jelentést készítenek arról, hogy egy AWS-alkalmazáshoz hozzáadott ~ 1 200 alkalmazási szerepköröket, az alkalmazás minden további művelete elkezdi a mérettel kapcsolatos hibák dobását. Az Application objektumhoz rögzített méret van korlátozva.

* Manuálisan kell frissítenie a szerepköröket, ahogy azok a fiókok bármelyikében hozzáadódnak. Ez sajnos egy *Csere* megközelítés, nem pedig *hozzáfűzési* megközelítés. Továbbá, ha a számlaszámok egyre növekednek, ez egy *n* &times; *n* kapcsolat lesz a fiókokkal és szerepkörökkel.

* Az AWS-fiókok ugyanazt az összevonási metaadatokat tartalmazó XML-fájlt használják. A tanúsítvány átváltásának időpontjában a tanúsítvány frissítése az összes AWS-fiókon egy időben jelentős feladat lehet.

## <a name="prerequisites"></a>Előfeltételek

Az AWS-sel való Azure AD-integráció konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-előfizetéssel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is kaphat.
* AWS SSO-kompatibilis előfizetés.

> [!NOTE]
> Az oktatóanyag lépéseit nem ajánlott éles környezetben tesztelni, kivéve, ha szükséges.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

Az AWS támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

## <a name="add-aws-from-the-gallery"></a>AWS hozzáadása a katalógusból

Az AWS Azure AD-be való integrálásának konfigurálásához az AWS-t hozzáadja a katalógusból a felügyelt szolgáltatott szoftverek (SaaS) alkalmazásai listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali panelen válassza ki azt az Azure AD-szolgáltatást, amellyel dolgozni szeretne.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Amazon Web Services** kifejezést a keresőmezőbe.
1. Az eredmények listájában válassza a **Amazon Web Services** lehetőséget, majd adja hozzá az alkalmazást. Néhány másodpercen belül az alkalmazás bekerül a bérlőbe.

1. Nyissa meg a **Tulajdonságok** ablaktáblát, és másolja ki az **objektum azonosítója** mezőben megjelenő értéket.

    ![Képernyőfelvétel a Tulajdonságok ablaktáblán található objektumazonosító mezőről.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az AWS-sel konfigurálja és teszteli a "Britta Simon" nevű teszt felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek tudnia kell, hogy az AWS-ben lévő felhasználó hogyan működik az Azure AD-felhasználó számára. Más szóval az Azure AD-felhasználó és az AWS-felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

Az AWS-ben rendelje hozzá a **Felhasználónév** értékét az Azure ad-ben az AWS- **Felhasználónév** értékeként a kapcsolati kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés AWS használatával történő konfigurálásához és teszteléséhez tegye a következőket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
1. **[Konfigurálja az AWS SSO](#configure-aws-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD SSO használatát a Azure Portalban, és konfigurálja az egyszeri bejelentkezést az AWS-alkalmazásban a következő módon:

1. A Azure Portal az **Amazon Web Services (AWS)** alkalmazás-integráció lap bal oldali paneljén válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Képernyőkép az "egyszeri bejelentkezés" parancsról.](common/select-sso.png)

1. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Képernyőkép az "egyszeri bejelentkezési módszer kiválasztása" panelről.](common/select-saml-option.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen kattintson a  **Szerkesztés** gombra (ceruza ikon).

    ![Képernyőkép az "egyszeres Sign-On beállítása az SAML-vel" panel Szerkesztés gombjára.](common/edit-urls.png)

1. Megnyílik az **alapszintű SAML-konfiguráció** ablaktábla. Ugorja át ezt a szakaszt, mert az alkalmazás előre integrálva van az Azure-ban. Kattintson a **Mentés** gombra.

   Az AWS-alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Az attribútumok értékeit a **felhasználói attribútumok & jogcímek** szakaszban találja az **alkalmazás-integráció** lapon. 
   
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** gombot.

    ![Képernyőkép a "felhasználói attribútumok" panel szerkesztés gombjáról.](common/edit-attribute.png)

1. A **felhasználói attribútumok** ablaktábla felhasználói **jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumát az alábbi táblázatban szereplő értékek használatával:

    | Name  | Forrás attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Szerepkör | User. assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket adjon meg" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Válassza az **új jogcím hozzáadása** lehetőséget, majd a **felhasználói jogcímek kezelése** panelen tegye a következőket:

   ![Képernyőkép: "új jogcím hozzáadása" és "Mentés" gomb a "felhasználói jogcímek" ablaktáblán.](common/new-save-attribute.png)

   ![Képernyőkép a "felhasználói jogcímek kezelése" panelről.](common/new-attribute-details.png)

   b. A **név** mezőbe írja be az attribútum nevét.  

   c. A **névtér** mezőbe írja be a névtér értékét.

   d. A **forrás** mezőben válassza az **attribútum** lehetőséget.

   e. A **forrás attribútum** legördülő listában válassza ki az attribútumot.

   f. Válassza **az OK**, majd a **Mentés** lehetőséget.

   >[!NOTE]
   >Az Azure AD szerepköreivel kapcsolatos további információkért lásd: [alkalmazás-Szerepkörök hozzáadása az alkalmazáshoz és fogadása a jogkivonatban](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget az összevonási metaadatok XML-fájljának letöltéséhez, majd mentse azt a számítógépre.

   ![Képernyőkép az "összevonási metaadatok XML" letöltési hivatkozásról az "SAML aláíró tanúsítvány" panelen.](common/metadataxml.png)

### <a name="configure-aws-sso"></a>AWS SSO konfigurálása

1. Egy új böngészőablakban jelentkezzen be az AWS vállalati webhelyre rendszergazdaként.

1. Válassza ki az **AWS Kezdőlap** ikonját.

   ![Képernyőkép az "AWS Home" ikonról.][11]

1. Az **AWS szolgáltatások** ablaktáblán, a **biztonság, identitás & megfelelőség** területen válassza a **iam (identitás & hozzáférés-kezelés)** lehetőséget.

    ![Képernyőkép az "identitás-és hozzáférés-kezelés" hivatkozásról az "AWS szolgáltatások" panelen.][12]

1. A bal oldali ablaktáblán válassza az **identitás-szolgáltatók**, majd a **szolgáltató létrehozása** lehetőséget.

    ![Képernyőkép a "Provider létrehozása" gombról.][13]

1. A **szolgáltató konfigurálása** panelen tegye a következőket:

    ![Képernyőkép a "szolgáltató konfigurálása" panelről.][14]

    a. A **szolgáltató típusa** legördülő listában válassza az **SAML** elemet.

    b. A **szolgáltató neve** mezőbe írja be a szolgáltató nevét (például:. *WAAD*).

    c. A metaadat- **dokumentum** mező mellett válassza a **fájl kiválasztása** lehetőséget a letöltött összevonási metaadatok XML-fájljának a Azure Portal való feltöltéséhez.

    d. Válassza a **következő lépés** lehetőséget.

1. A **szolgáltató adatainak ellenőrzése** panelen válassza a **Létrehozás** lehetőséget.

    ![A "szolgáltatói információk ellenőrzése" panel képernyőképe.][15]

1. A bal oldali ablaktáblán válassza a **szerepkörök**, majd a **szerepkör létrehozása** lehetőséget.

    ![Képernyőkép a "szerepkör létrehozása" gombról a szerepkörök ablaktáblán.][16]

    > [!NOTE]
    > Az importált szerepkörhöz tartozó Amazon-erőforrás neve (ARN) és az SAML-szolgáltató ARN együttes hossza legfeljebb 240 karakter hosszú lehet.

1. A **szerepkör létrehozása** lapon tegye a következőket:  

    ![Képernyőkép a "SAML 2,0-összevonás" megbízható entitás gombról a "szerepkör létrehozása" lapon.][19]

    a. A **megbízható entitás típusának kiválasztása** területen válassza az **SAML 2,0-összevonás** lehetőséget.

    b. Az **saml 2,0-szolgáltató kiválasztása** területen válassza ki a korábban létrehozott SAML-szolgáltatót (például *WAAD*).

    c. Válassza **a programozott és AWS felügyeleti konzol hozzáférésének engedélyezése** lehetőséget.

    d. Válassza a **Next: Permissions** (Tovább: Engedélyek) lehetőséget.

1. A keresőmezőbe írja be a **rendszergazdai hozzáférés** kifejezést, jelölje be a **AdministratorAccess** jelölőnégyzetet, majd kattintson a **Tovább gombra: címkék**.

    ![Képernyőkép a "szabályzat neve" listáról a kiválasztott AdministratorAccess szabályzattal.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. A **Címkék hozzáadása (nem kötelező)** panelen tegye a következőket:

    ![Képernyőkép a "Címkék hozzáadása (nem kötelező)" panelről.](./media/aws-multi-accounts-tutorial/config2.png)

    a. A **kulcs** mezőbe írja be a kulcs nevét (például *Azureadtest*).

    b. Az **érték (nem kötelező)** mezőben adja meg a kulcs értékét a következő formátumban: `<accountname-aws-admin>` . A fiók neve csak kisbetűket tartalmazhat.

    c. Válassza a **Next: Review** (Tovább: Áttekintés) lehetőséget.

1. Az **Áttekintés** panelen tegye a következőket:

    ![Képernyőkép az Áttekintés panelről, a "szerepkör neve" és a "szerepkör leírása" mezők kiemelve.][34]

    a. A **szerepkör neve** mezőbe írja be az értéket a következő formátumban: `<accountname-aws-admin>` .

    b. A **szerepkör leírása** mezőbe írja be a szerepkör neveként használt értéket.

    c. Válassza a **szerepkör létrehozása** lehetőséget.

    d. Hozzon létre annyi szerepkört, amennyire szüksége van, és rendelje hozzá őket az identitás-szolgáltatóhoz.

    > [!NOTE]
    > Hasonlóképpen létrehozhat más szerepköröket is, például a *accountname-Finance-admin*, a *accountname-írásvédett-User*, a *accountname-devops-User* vagy a *accountname-TPM-User*, amelyek mindegyike más házirenddel van csatolva. Ezeket a szerepkör-házirendeket később módosíthatja az egyes AWS-fiókok követelményeinek megfelelően. Az AWS-fiókok minden egyes szerepköre esetében érdemes megtartani a szabályzatokat.

1. Ügyeljen arra, hogy az AWS-fiókhoz tartozó fiókot az Amazon rugalmas számítási felhő (Amazon EC2) tulajdonságok paneljén vagy a IAM irányítópulton jegyezze fel, ahogy az alábbi képernyőképen is látható:

    ![Képernyőfelvétel: a fiók AZONOSÍTÓjának helye az "identitás-és hozzáférés-kezelés" ablaktáblán.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Jelentkezzen be a Azure Portalba, majd lépjen a **csoportok** elemre.

1. Hozzon létre új csoportokat ugyanazzal a névvel, mint a korábban létrehozott IAM-szerepkörökkel, majd jegyezze fel az egyes új csoportok **objektumazonosító** mezőjében szereplő értéket.

    ![Képernyőkép az új csoport fiókjának részleteiről.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Jelentkezzen ki az aktuális AWS-fiókból, majd jelentkezzen be egy másik fiókba, ahol az SSO-t az Azure AD-vel szeretné konfigurálni.

1. Miután létrehozta az összes szerepkört a fiókokban, azok megjelennek a **szerepkörök** listájában ezekhez a fiókokhoz.

    ![Képernyőkép a szerepkörök listájáról, amely megjeleníti az egyes szerepkörök nevét, leírását és megbízható entitásait.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

A következő lépés az összes szerepkör ARNs és megbízható entitásának rögzítése az összes fiókban. Ezeket manuálisan kell leképezni az Azure AD-alkalmazással. Ehhez tegye a következőket:

1. Válassza ki az egyes szerepköröket az ARN és a megbízható entitások értékének másolásához. Az Azure AD-ben létrehozott összes szerepkörhöz szüksége lesz rájuk.

    ![Képernyőkép a szerepkör-ARNs és a megbízható entitások összefoglaló paneljéről.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Ismételje meg az előző lépést az összes fiókban található összes szerepkörnél, majd a következő formátumban tárolja a fájlokat egy szövegfájlban: `<Role ARN>,<Trusted entities>` .

1. Nyissa meg [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer), majd tegye a következőket:

    a. Jelentkezzen be a Microsoft Graph Explorer webhelyre a bérlő globális rendszergazdájának vagy társ-rendszergazdai hitelesítő adataival.

    b. A szerepkörök létrehozásához megfelelő engedélyekkel kell rendelkeznie. Válassza az **engedélyek módosítása** lehetőséget.

      ![Képernyőkép az "engedélyek módosítása" hivatkozásra az Microsoft Graph Explorer hitelesítési paneljén.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Ha még nem rendelkezik a következő képernyőképen látható engedélyekkel, válassza ki a megfelelő engedélyeket az engedélyek listában, majd válassza az **engedélyek módosítása** lehetőséget. 

      ![Képernyőkép a Microsoft Graph Explorer engedélyek listájáról, a megfelelő engedélyek kijelölve.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Jelentkezzen be újra a Graph Explorerben, és fogadja el a hely használati feltételeit. 

    e. A panel felső részén válassza a **beolvasás** a következőhöz lehetőséget, válassza a verzióhoz a **Beta** lehetőséget, majd a lekérdezés mezőben adja meg a következők egyikét: 
    
    * Az összes egyszerű szolgáltatásnév beolvasásához használja a következőt: `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Ha több könyvtárat használ, használja `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` az elsődleges tartományt.

    ![Képernyőkép a Microsoft Graph Explorer lekérdezési Törzséről.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Az egyszerű szolgáltatások listájából szerezze be azt, amelyet módosítania kell. 
    
    A CTRL + F billentyűkombinációval is kereshet az alkalmazásban az összes felsorolt szolgáltatáshoz. Egy adott egyszerű szolgáltatásnév beszerzéséhez vegye fel a következőt az Azure AD tulajdonságok paneljéről korábban átmásolt egyszerű szolgáltatásnév-objektumazonosító lekérdezésében:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Az objektumazonosító részét képező egyszerű szolgáltatás lekérdezését bemutató képernyőkép.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    : Bontsa ki a appRoles tulajdonságot az egyszerű szolgáltatásnév objektumból.

    ![Képernyőkép a appRoles tulajdonság kibontásához a szolgáltatás egyszerű objektumáról.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Most új szerepköröket kell létrehoznia az alkalmazáshoz. 

    i. A következő JSON-kód egy példa egy appRoles objektumra. Hozzon létre egy hasonló objektumot az alkalmazáshoz használni kívánt szerepkörök hozzáadásához.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Új szerepköröket csak akkor adhat hozzá, ha *msiam_accesst* adott hozzá a javítási művelethez. A szervezet igényeitől függően tetszőleges számú szerepkört is hozzáadhat. Az Azure AD ezeket a szerepköröket az SAML-válaszban szereplő jogcím értékként küldi *el.*

    j. A Microsoft Graph Explorerben változtassa meg a **Get** to **patch** metódust. A appRoles tulajdonság frissítésével, például az előző példában láthatóval, az egyszerű szolgáltatásnév objektumának javításához frissítse a kívánt szerepköröket. Válassza a **lekérdezés futtatása** lehetőséget a javítási művelet végrehajtásához. A sikeres üzenet megerősíti az AWS-alkalmazás szerepkörének létrehozását.

      ![Képernyőkép a Microsoft Graph Explorer panelről, és a metódus JAVÍTÁSra módosult.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Ha a szolgáltatásnév több szerepkörrel is rendelkezik, a felhasználók és csoportok a megfelelő szerepkörökhöz rendelhetők. Ezt a Azure Portal az AWS alkalmazásra, majd a felül található **felhasználók és csoportok** lapra kattintva hajthatja végre.

1. Javasoljuk, hogy minden AWS-szerepkörhöz hozzon létre egy új csoportot, hogy az adott szerepkört hozzá lehessen rendelni a csoporthoz. Ez az egy-az-egyhez típusú hozzárendelés azt jelenti, hogy az egyik csoport hozzá van rendelve egy szerepkörhöz. Ezután hozzáadhatja a csoporthoz tartozó tagokat.

1. A csoportok létrehozása után válassza ki a csoportot, és rendelje hozzá az alkalmazáshoz.

    ![Képernyőkép a "felhasználók és csoportok" panelről.](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > A beágyazott csoportok nem támogatottak a csoportok hozzárendelésével.

1. Ha a szerepkört hozzá szeretné rendelni a csoporthoz, válassza ki a szerepkört, majd válassza a **hozzárendelés** lehetőséget.

    ![Képernyőkép a "hozzárendelés hozzáadása" panelről.](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > A szerepkörök hozzárendelése után megtekintheti őket a Azure Portal-munkamenet frissítésével.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a Microsoft My apps szolgáltatással.

Amikor kiválasztja a saját alkalmazások **AWS** csempéjét, megnyílik az AWS-alkalmazás lap, amelyen kiválaszthatja a szerepkört.

![Képernyőfelvétel az AWS-ről az AWS-tesztről.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Azt is ellenőrizheti, hogy az SAML-válasz a jogcímek szerint átadott szerepköröket tekinti-e meg.

![Képernyőkép az SAML-válaszról.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

További információ az alkalmazásokról: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

Az AWS konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
