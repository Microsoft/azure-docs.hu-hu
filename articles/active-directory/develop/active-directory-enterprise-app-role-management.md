---
title: Szerepkör-jogcím konfigurálása a vállalati Azure AD-alkalmazásokhoz | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan konfigurálhatja a vállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcímet Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: aab1f99984ed5286692cbf9dae39fb4f7d28599c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652462"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: az SAML-jogkivonatban kiadott szerepkör-jogcím konfigurálása nagyvállalati alkalmazásokhoz

A Azure Active Directory (Azure AD) használatával testreszabhatja a szerepkör-jogcím típusát az alkalmazás engedélyezése után kapott válasz jogkivonatban.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD-előfizetés címtár-telepítővel.
- Egyszeri bejelentkezést (SSO) engedélyező előfizetés. Az egyszeri bejelentkezést az alkalmazással kell konfigurálnia.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan lehet alkalmazás-szerepköröket létrehozni/frissíteni/törölni az egyszerű szolgáltatásban az Azure AD-ben API-k használatával. Ha az új felhasználói felületet szeretné használni az alkalmazás szerepköreihez, tekintse meg a részleteket [itt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

## <a name="when-to-use-this-feature"></a>Mikor kell használni ezt a funkciót?

Akkor használja ezt a szolgáltatást, ha az alkalmazás az Azure AD által visszaadott SAML-válaszban egyéni szerepköröket vár. Annyi szerepkört hozhat létre, amennyire csak szüksége van.

## <a name="create-roles-for-an-application"></a>Szerepkörök létrehozása egy alkalmazáshoz

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>bal oldali ablaktábláján válassza a **Azure Active Directory** ikont.

    ![Azure Active Directory ikon][1]

2. Válassza a **vállalati alkalmazások** lehetőséget. Ezután válassza **a minden alkalmazás** lehetőséget.

    ![Vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    !["Új alkalmazás" gomb][3]

4. A keresőmezőbe írja be az alkalmazás nevét, majd válassza ki az alkalmazást az eredmény panelen. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Alkalmazás az eredmények listájában](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Az alkalmazás hozzáadása után nyissa meg a **Tulajdonságok** lapot, és másolja az objektumazonosítót.

    ![Tulajdonságok lap](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Nyissa meg [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban, és hajtsa végre a következő lépéseket:

    1. Jelentkezzen be a Graph Explorer-webhelyre a bérlőhöz tartozó globális rendszergazdai vagy rendszergazdai hitelesítő adatok használatával.

    1. A szerepkörök létrehozásához megfelelő engedélyekkel kell rendelkeznie. Az engedélyek beszerzéséhez válassza az **engedélyek módosítása** lehetőséget.

        ![Az "engedélyek módosítása" gomb](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > Ebben a forgatókönyvben a Cloud app Administrator és az App Administrator szerepkör nem fog működni, mivel a címtár olvasására és írására vonatkozó globális rendszergazdai engedélyekre van szükségünk.

    1. Válassza ki az alábbi engedélyeket a listából (ha még nem rendelkezik ezekkel), és válassza az **engedélyek módosítása** lehetőséget.

        ![Engedélyek listája és "engedélyek módosítása" gomb](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Fogadja el az engedélyt. Újra bejelentkezett a rendszerbe.

    1. Módosítsa a verziót a **Beta** értékre, és az alábbi lekérdezéssel olvassa be az egyszerű szolgáltatásnév listáját a bérlőből:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Ha több könyvtárat használ, kövesse az alábbi mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Graph Explorer párbeszédpanel az egyszerű szolgáltatások beolvasására szolgáló lekérdezéssel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. A beolvasott egyszerű szolgáltatások listájából szerezze be azt, amelyet módosítania kell. A CTRL + F billentyűkombinációval is megkeresheti az alkalmazást az összes felsorolt egyszerű szolgáltatásból. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezés használatával szerezze be a szolgáltatásnevet:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![A módosítani kívánt szolgáltatásnév lekérésének lekérdezése](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Bontsa ki a **appRoles** tulajdonságot az egyszerű szolgáltatásnév objektumból.

        ![A appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Ha az egyéni alkalmazást (nem az Azure Marketplace alkalmazást) használja, akkor két alapértelmezett szerepkör jelenik meg: felhasználói és msiam_access. A Marketplace-alkalmazás esetében a msiam_access az egyetlen alapértelmezett szerepkör. Semmilyen módosítást nem kell végeznie az alapértelmezett szerepkörökben.

    1. Új szerepkörök előállítása az alkalmazáshoz.

        A következő JSON egy példa a **appRoles** objektumra. Hozzon létre egy hasonló objektumot az alkalmazáshoz használni kívánt szerepkörök hozzáadásához.

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        A javítási művelet msiam_access után csak új szerepköröket adhat hozzá. Emellett a szervezet igényeinek megfelelő számú szerepkört is hozzáadhat. Az Azure AD ezeket a szerepköröket az SAML-válaszban szereplő jogcím értékként fogja elküldeni. Az új szerepkörök AZONOSÍTÓjának GUID-értékeinek létrehozásához használja [a következőhöz hasonló webes](https://www.guidgenerator.com/) eszközöket:

    1. Lépjen vissza a Graph Explorerben, és változtassa meg a **Get** to **patch** metódust. Az előző példában látható módon frissítse a **appRoles** tulajdonságot úgy, hogy az egyszerű szolgáltatásnév objektummal rendelkezzen a kívánt szerepkörökkel. Válassza a **lekérdezés futtatása** lehetőséget a javítási művelet végrehajtásához. A sikeres üzenet megerősíti a szerepkör létrehozását.

        ![Javítási művelet sikeres üzenettel](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. Ha a szolgáltatásnév több szerepkörrel is rendelkezik, a felhasználókat hozzárendelheti a megfelelő szerepkörökhöz. A felhasználók hozzárendeléséhez nyissa meg a portált, és tallózással keresse meg az alkalmazást. Válassza a **felhasználók és csoportok** lapot. Ez a lap felsorolja az alkalmazáshoz már hozzárendelt összes felhasználót és csoportot. Új felhasználókat adhat hozzá az új szerepkörökhöz. Kiválaszthat egy meglévő felhasználót is, és a **Szerkesztés** lehetőségre kattintva módosíthatja a szerepkört.

    !["Felhasználók és csoportok" lap](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    A szerepkör bármely felhasználóhoz való hozzárendeléséhez válassza ki az új szerepkört, és kattintson a lap alján található **hozzárendelés** gombra.

    !["Hozzárendelés szerkesztése" ablaktábla és "szerepkör kiválasztása" panel](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Frissítse a munkamenetet a Azure Portalban az új szerepkörök megtekintéséhez.

1. Frissítse az **attribútumok** táblát a szerepkör-jogcím testreszabott leképezésének megadásához.

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Attribútum neve | Attribútum értéke |
    | -------------- | ----------------|
    | Szerepkörnév  | User. assignedroles |

    Ha a szerepkör-jogcím értéke null, akkor az Azure AD nem küldi el ezt az értéket a jogkivonatban, és ez az alapértelmezett érték a terv szerint.

    1. Az ikon **szerkesztése** gombra kattintva megnyithatja a **felhasználói attribútumok & jogcímek** párbeszédpanelt.

        ![Képernyőfelvétel: a felhasználói attribútumok & jogcímek párbeszédpanel megnyitásához használt szerkesztési ikon kiemelése.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. A **felhasználói jogcímek kezelése** párbeszédpanelen adja hozzá az SAML-jogkivonat attribútumot az **új jogcím hozzáadása** lehetőségre kattintva.

        !["Attribútum hozzáadása" gomb](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        !["Attribútum hozzáadása" panel](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. A **név** mezőbe írja be az attribútum nevét igény szerint. Ez a példa a **szerepkör nevét** használja jogcím neveként.

    1. Hagyja üresen a **névtér** mezőt.

    1. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson a **Mentés** gombra.

10. Ha az alkalmazást egy identitás-szolgáltató által kezdeményezett egyszeri bejelentkezéssel szeretné tesztelni, jelentkezzen be a [hozzáférési panelre](https://myapps.microsoft.com) , és válassza ki az alkalmazás csempéjét. Az SAML-jogkivonatban a felhasználóhoz tartozó összes hozzárendelt szerepkört látnia kell a megadott jogcím nevével.

## <a name="update-an-existing-role"></a>Meglévő szerepkör frissítése

Meglévő szerepkör frissítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer).

1. Jelentkezzen be a Graph Explorer-webhelyre a bérlőhöz tartozó globális rendszergazdai vagy rendszergazdai hitelesítő adatok használatával.

1. Módosítsa a verziót a **Beta** értékre, és az alábbi lekérdezéssel olvassa be az egyszerű szolgáltatásnév listáját a bérlőből:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, kövesse az alábbi mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanel az egyszerű szolgáltatások beolvasására szolgáló lekérdezéssel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. A beolvasott egyszerű szolgáltatások listájából szerezze be azt, amelyet módosítania kell. A CTRL + F billentyűkombinációval is megkeresheti az alkalmazást az összes felsorolt egyszerű szolgáltatásból. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezés használatával szerezze be a szolgáltatásnevet:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![A módosítani kívánt szolgáltatásnév lekérésének lekérdezése](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Bontsa ki a **appRoles** tulajdonságot az egyszerű szolgáltatásnév objektumból.

    ![A appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. A meglévő szerepkör frissítéséhez kövesse az alábbi lépéseket.

    ![A "javítás", a "Description" és a "DisplayName" kifejezéssel jelölt kérelem törzse](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Módosítsa a metódust a **Get** és a **patch** között.

    1. Másolja a meglévő szerepköröket, és illessze be őket a **kérelem törzsében**.

    1. Frissítse a szerepkör értékét úgy, hogy szükség szerint frissíti a szerepkör leírását, a szerepkör értékét vagy a szerepkör megjelenítendő nevét.

    1. Az összes szükséges szerepkör frissítése után válassza a **lekérdezés futtatása** lehetőséget.

## <a name="delete-an-existing-role"></a>Meglévő szerepkör törlése

Meglévő szerepkör törléséhez hajtsa végre a következő lépéseket:

1. Nyissa meg [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

1. Jelentkezzen be a Graph Explorer-webhelyre a bérlőhöz tartozó globális rendszergazdai vagy rendszergazdai hitelesítő adatok használatával.

1. Módosítsa a verziót a **Beta** értékre, és az alábbi lekérdezéssel olvassa be az egyszerű szolgáltatásnév listáját a bérlőből:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, kövesse az alábbi mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanel, az egyszerű szolgáltatások listájának beolvasására szolgáló lekérdezéssel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. A beolvasott egyszerű szolgáltatások listájából szerezze be azt, amelyet módosítania kell. A CTRL + F billentyűkombinációval is megkeresheti az alkalmazást az összes felsorolt egyszerű szolgáltatásból. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezés használatával szerezze be a szolgáltatásnevet:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![A módosítani kívánt szolgáltatásnév lekérésének lekérdezése](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Bontsa ki a **appRoles** tulajdonságot az egyszerű szolgáltatásnév objektumból.

    ![A appRoles tulajdonság részletei az egyszerű szolgáltatásnév objektumból](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. A meglévő szerepkör törléséhez kövesse az alábbi lépéseket.

    ![A "javítás" kérés törzse, a IsEnabled hamis értékre állítva](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Módosítsa a metódust a **Get** és a **patch** között.

    1. Másolja a meglévő szerepköröket az alkalmazásból, és illessze be őket a **kérelem törzsében**.

    1. Állítsa a **IsEnabled** értéket **hamis** értékre a törölni kívánt szerepkörhöz.

    1. Válassza a **Lekérdezés futtatása** lehetőséget.

    Győződjön meg arról, hogy rendelkezik a msiam_access szerepkörrel, és hogy az azonosító megegyezik a generált szerepkörben.

1. Miután a szerepkör le lett tiltva, törölje a szerepkör-blokkot a **appRoles** szakaszból. Tartsa a metódust **javításként**, és válassza a **lekérdezés futtatása** lehetőséget.

1. A lekérdezés futtatása után a rendszer törli a szerepkört.

    A szerepkört le kell tiltani ahhoz, hogy el lehessen távolítani.

## <a name="next-steps"></a>Következő lépések

További lépésekért tekintse meg az [alkalmazás dokumentációját](../saas-apps/tutorial-list.md).

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
