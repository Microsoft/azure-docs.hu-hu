---
title: Azure Active Directory jelentési API előfeltételei | Microsoft Docs
description: Tudnivalók az Azure AD Reporting API elérésének előfeltételeiről
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348738"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>A Azure Active Directory jelentési API elérésének előfeltételei

Az [Azure Active Directory (Azure AD) Reporting API-k](./concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Ezeket az API-kat programozási nyelvekből és eszközökből hívhatja.

A jelentéskészítési API a [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) használatával engedélyezi a hozzáférést a webes API-khoz.

A jelentéskészítési API-hoz való hozzáférés előkészítéséhez a következőket kell tennie:

1. [Szerepkörök hozzárendelése](#assign-roles)
2. [Licencekre vonatkozó követelmények](#license-requirements)
3. [Egy alkalmazás regisztrálása](#register-an-application)
4. [Engedélyek megadása](#grant-permissions)
5. [Konfigurációs beállítások összegyűjtése](#gather-configuration-settings)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Ha a jelentéskészítési adathoz az API-n keresztül szeretne hozzáférni, a következő szerepkörök egyikét kell hozzárendelni:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda

## <a name="license-requirements"></a>Licencekre vonatkozó követelmények

A bérlőhöz tartozó bejelentkezési jelentések eléréséhez az Azure AD-bérlőnek hozzá kell rendelnie prémium szintű Azure AD-licencet. Prémium szintű Azure AD P1 (vagy újabb) licencre van szükség az Azure AD-bérlők bejelentkezési jelentéseinek eléréséhez. Ha a könyvtár típusa Azure AD B2C, a bejelentkezési jelentések az API-n keresztül érhetők el, további licencfeltételek nélkül. 


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

A regisztrációra akkor is szükség van, ha parancsfájl használatával éri el a jelentéskészítési API-t. A regisztráció egy alkalmazás- **azonosítót** ad meg, amely az engedélyezési hívásokhoz szükséges, és lehetővé teszi a kód számára a jogkivonatok fogadását.

Ha úgy szeretné konfigurálni a címtárat, hogy hozzáférhessen az Azure AD Reporting API-hoz, be kell jelentkeznie a [Azure Portalba](https://portal.azure.com) egy olyan Azure-beli rendszergazdai fiókkal, amely az Azure ad-bérlő **globális rendszergazdai** címtár szerepkörének tagja is.

> [!IMPORTANT]
> A rendszergazdai jogosultságokkal rendelkező hitelesítő adatokkal működő alkalmazások nagyon hatékonyak lehetnek, ezért ügyeljen arra, hogy az alkalmazás AZONOSÍTÓját és a titkos kulcs hitelesítő adatait biztonságos helyen tárolja.
> 

**Azure AD-alkalmazás regisztrálása:**

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
   
    ![A képernyőképen a Azure Portal menüből kiválasztott Azure Active Directory látható.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** lapon válassza a **Alkalmazásregisztrációk** lehetőséget.

    ![A képernyőképen látható Alkalmazásregisztrációk a kezelés menüből kiválasztva.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. A **Alkalmazásregisztrációk** lapon válassza az **új regisztráció** lehetőséget.

    ![A képernyőképen a kiválasztott új regisztráció látható.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Az **alkalmazás regisztrációjának** lapja:

    ![Képernyőfelvétel: az alkalmazás regisztrálása oldal, ahol megadhatja az ebben a lépésben szereplő értékeket.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. A **név** szövegmezőbe írja be a következőt: `Reporting API application` .

    b. A **támogatott fiókok típusa** beállításnál válassza a **fiókok csak ebben a szervezetiben** lehetőséget.

    c. Az **átirányítási URL-cím**  kijelölése **webes** szövegmezőbe írja be a következőt: `https://localhost` .

    d. Válassza a **Regisztráció** lehetőséget. 


## <a name="grant-permissions"></a>Engedélyek megadása 

Az elérni kívánt API-tól függően a következő engedélyeket kell megadnia az alkalmazásnak:  

| API | Engedély |
| --- | --- |
| Microsoft Azure Active Directory | Címtáradatok olvasása |
| Microsoft Graph | Az összes naplózási napló adatolvasása |

![Képernyőfelvétel: Itt választhatja ki az engedély hozzáadása elemet az A P I engedélyek ablaktáblán.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A következő szakasz az API-k lépéseit sorolja fel. Ha nem szeretne hozzáférni valamelyik API-hoz, ugorja át a kapcsolódó lépéseket.

**Az API-k használatának engedélyezése az alkalmazás számára:**


1. Válassza az **API-engedélyek** lehetőséget, majd **adjon hozzá egy engedélyt**. 

    ![Képernyőfelvétel: a P I engedélyek lap, amelyen kiválaszthatja az engedély hozzáadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Az **API-engedélyek kérése lapon** keresse meg a **támogatási örökölt API** - **Azure Active Directory gráfot**. 

    ![A képernyőképen a P I engedély kérése oldal jelenik meg, ahol kiválaszthatja Azure Active Directory gráfot.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. A **szükséges engedélyek** lapon válassza az **alkalmazás engedélyei** lehetőséget **, majd a könyvtár** **. ReadAll** elemet.  Válassza az **engedélyek hozzáadása** lehetőséget.

    ![A képernyőképen a P I engedély kérése lap jelenik meg, amelyen kiválaszthatja az alkalmazás engedélyeit.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. A **Reporting API Application-API-engedélyek** lapon válassza a **rendszergazdai jóváhagyás megadása** lehetőséget. 

    ![Képernyőfelvétel: a P I alkalmazást futtató alkalmazás A P I engedélyek lapon válassza a rendszergazdai jóváhagyás megadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Megjegyzés: az API-regisztráció során az **Microsoft Graph** alapértelmezés szerint hozzá lesz adva.

    ![Képernyőfelvétel: a P I engedélyek lap, amelyen kiválaszthatja az engedély hozzáadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 

Ez a szakasz bemutatja, hogyan kérheti le a következő beállításokat a címtárból:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkulcs

Ezekre az értékekre szüksége lesz a jelentéskészítési API-hoz való hívások konfigurálásakor. 

### <a name="get-your-domain-name"></a>Tartománynév beszerzése

**A tartománynév beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs paneljén válassza a **Azure Active Directory** lehetőséget.
   
    ![A képernyőképen a Azure Portal menüből kiválasztott Azure Active Directory látható.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** lapon válassza az **Egyéni tartománynevek** lehetőséget.

    ![A képernyőképen a Azure Active Directory kiválasztott egyéni tartománynevek láthatók.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Másolja a tartománynevet a tartományok listájából.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-AZONOSÍTÓjának beolvasása

**Az alkalmazás ügyfél-AZONOSÍTÓjának beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján kattintson az **Azure Active Directory** elemre.
   
    ![A képernyőképen a Azure Portal menüből kiválasztott Azure Active Directory látható.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Válassza ki az alkalmazást az alkalmazás **regisztrációi** lapján.

3. Az alkalmazás oldalon navigáljon az **alkalmazás-azonosítóhoz** , és válassza a **Másolás gombra**.

    ![A képernyőképen a P I alkalmazásról szóló jelentés látható, amelyen átmásolhatja az I D alkalmazást.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Az alkalmazás ügyfél-titkának beszerzése
 Kerülje a naplókhoz vagy a bejelentkezéshez szükséges hibákat az API használatával.

**Az alkalmazás ügyfél-titkos kódjának beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján kattintson az **Azure Active Directory** elemre.
   
    ![A képernyőképen a Azure Portal menüből kiválasztott Azure Active Directory látható.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Válassza ki az alkalmazást az alkalmazás **regisztrációi** lapján.

3.  Válassza ki a **tanúsítványok és titkos kulcsok** elemet az **API-alkalmazás** oldalon, az **ügyfél titkai** szakaszban kattintson az **+ új ügyfél titka** elemre. 

    ![Képernyőfelvétel: a tanúsítványok & titkok oldal, ahol hozzáadhat egy ügyfél titkot.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Az **ügyfél titkos kulcsának hozzáadása** lapon adja hozzá a következőt:

    a. A **Leírás** szövegmezőbe írja be a következőt: `Reporting API` .

    b. Ahogy **lejár**, válassza **a 2 év** lehetőséget.

    c. Kattintson a **Mentés** gombra.

    d. Másolja a kulcs értékét.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Hibák elhárítása a jelentéskészítési API-ban

Ez a szakasz azokat a gyakori hibaüzeneteket sorolja fel, amelyekkel a Microsoft Graph API-val és a megoldásuk lépéseivel férhet hozzá a tevékenységek jelentéseihez.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Hiba: nem sikerült beolvasni a felhasználói szerepköröket a Microsoft Graph

 Jelentkezzen be a fiókjába a Graph Explorer felhasználói felületén található bejelentkezési gombok használatával, hogy elkerülje a hiba beolvasását a Graph Explorer használatával. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Hiba: nem sikerült a prémium szintű licencek ellenőrzését Microsoft Graph 

Ha ezt a hibaüzenetet akkor futtatja, amikor a Graph Explorer használatával próbál hozzáférni a bejelentkezésekhez, válassza a bal oldali navigációs menüben a fiók **módosítása** lehetőséget, majd válassza a **feladatok. READWRITE** és a **könyvtár. olvasás. összes** lehetőséget. 

![Engedélyek módosítása felhasználói felület](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: a bérlő nem B2C, vagy a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory Premium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Hiba: az engedélyezett szerepkörök nem tartalmazzák a felhasználót. 

 Kerülje a naplókhoz vagy a bejelentkezéshez szükséges hibákat az API használatával. Győződjön meg arról, hogy a fiókja a Azure Active Directory bérlőn a **biztonsági olvasó** vagy a **jelentéskészítő olvasó** szerepkör részét képezi.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: az alkalmazásból hiányzik a "HRE olvasása" engedély. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: az alkalmazás hiányzik Microsoft Graph API "a naplózási napló összes adatszolgáltatásának olvasása" engedély

Az előfeltételekben ismertetett lépéseket követve [férhet hozzá a Azure Active Directory jelentési API](howto-configure-prerequisites-for-reporting-api.md) -hoz annak biztosításához, hogy az alkalmazás a megfelelő engedélyekkel fusson. 

## <a name="next-steps"></a>Következő lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási API-referenciák](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [A bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin?view=graph-rest-beta)