---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Maxient magatartás-kezelő szoftverrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Maxient-kezelő szoftver között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85e71b76-cac3-4ce6-a35f-796d2cb7bdb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09399f59e61ded49fef5a2388900b7b478111119
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847191"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Maxient magatartás-kezelő szoftverrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Maxient Manager szoftvereket Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Maxient-kezelő szoftvereket, a következőket teheti:

* Használja az Azure AD-t a felhasználók hitelesítéséhez a Maxient magatartás-kezelő szoftverhez
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával Maxient a vezető szoftvereket.


Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Maxient-kezelő szoftveres egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja az Azure AD-t a Maxient-kezelő szoftverrel való használatra.


* A Maxient-kezelő szoftver támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Maxient-kezelő szoftver hozzáadása a katalógusból

A Maxient-kezelő szoftvernek az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Maxient Manager szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Maxient viselkedés-kezelő szoftver** kifejezést a keresőmezőbe.
1. Válassza a **Maxient-kezelő szoftver** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Maxient-kezelő szoftverhez

Az Azure AD SSO konfigurálása és tesztelése a Maxient-kezelő szoftverrel. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD és a Maxient magatartás-kezelő szoftvere között.

Az Azure AD SSO Maxient-kezelő szoftverrel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára, hogy a Maxient-kezelő szoftverrel való használatra hitelesítést végezzenek.
    1. **[Rendelje hozzá az összes felhasználót a Maxient használatához](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** – hogy mindenki számára lehetővé váljon az intézmény hitelesítése.
1. Az **[Azure ad telepítőjének tesztelése a Maxient-vel](#test-with-maxient)** – annak ellenőrzése, hogy a konfiguráció működik-e, és hogy a megfelelő attribútumok jelennek-e meg

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Maxient magatartás-kezelő szoftver** -integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs**   szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató**által   kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés**   ) gombra kattintva.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez működjön együtt a Maxient implementációval/támogatási képviselővel.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.  Ehhez az URL-címhez meg kell adnia a Maxient implementációját/támogatási képviselőjét.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Az összes felhasználó társítása a Maxient-kezelő szoftver hitelesítéséhez

Ebben a szakaszban minden fiók számára hozzáférést biztosít a Maxient-kezelő szoftverhez készült Azure-rendszer használatával végzett hitelesítéshez.  Fontos megjegyezni, hogy ez a lépés **szükséges** ahhoz, hogy a Maxient megfelelően működjön.  A Maxient a felhasználók *hitelesítésére* használja az Azure ad-t. A felhasználók *engedélyezése* a Maxient rendszeren belül történik a végrehajtani kívánt adott függvény esetében. A Maxient nem használ attribútumokat a címtárból a döntések meghozatalához.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Maxient-kezelő szoftver**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a minden felhasználó (vagy a megfelelő csoportok) lehetőséget, és **rendelje** hozzá őket a Maxient-hitelesítéshez.

## <a name="test-with-maxient"></a>Tesztelés a Maxient 

Ha egy támogatási jegyet még nem nyitott meg egy Maxient-implementációval/támogatási képviselővel, küldjön egy e-mailt [support@maxient.com](mailto:support@maxient.com) a tulajdonos "Campus-alapú hitelesítés/Azure Setup- \< \< School name \> \> " névre. Az e-mail törzsében adja meg az **alkalmazás-összevonási metaadatok URL-címét**. A Maxient-munkatársak egy tesztelési hivatkozással fognak válaszolni a megfelelő attribútumok felszabadítására.  
    
## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Maxient-kezelő szoftvert az Azure AD-vel](https://aad.portal.azure.com/)

