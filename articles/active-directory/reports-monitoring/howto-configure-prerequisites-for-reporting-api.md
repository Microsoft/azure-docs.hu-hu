---
title: A jelentéskészítési API Azure Active Directory használatára vonatkozó előfeltételek | Microsoft Docs
description: Tudnivalók az Azure AD Reporting API eléréséhez szükséges előfeltételekről
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
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532543"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>A Azure Active Directory Reporting API elérésének előfeltételei

Az [Azure Active Directory (Azure AD) Reporting API-k](./concept-reporting-api.md) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Ezeket az API-kat programozási nyelvekből és eszközökből hívhatja meg.

A jelentéskészítő API [OAuth-hitelesítéssel](../../api-management/api-management-howto-protect-backend-with-aad.md) engedélyezi a webes API-khoz való hozzáférést.

A jelentéskészítési API-hoz való hozzáférés előkészítéséhez a következőt kell használnia:

1. [Szerepkörök hozzárendelése](#assign-roles)
2. [Licenckövetelmények](#license-requirements)
3. [Egy alkalmazás regisztrálása](#register-an-application)
4. [Engedélyek megadása](#grant-permissions)
5. [Konfigurációs beállítások gyűjtése](#gather-configuration-settings)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Ahhoz, hogy az API-n keresztül hozzáférjen a jelentéskészítési adatokhoz, a következő szerepkörök egyikével kell rendelkezik:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda

## <a name="license-requirements"></a>Licenckövetelmények

Egy bérlő bejelentkezési jelentésének eléréséhez egy Azure AD-bérlőhöz társítva kell prémium szintű Azure AD licenccel. prémium szintű Azure AD P1 (vagy magasabb) licencre van szükség bármely Azure AD-bérlő bejelentkezési jelentéséhez való hozzáféréshez. Ha a címtár típusa Azure AD B2C, a bejelentkezési jelentések további licenckövetelmények nélkül érhetők el az API-n keresztül. 


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Regisztrációra akkor is szükség van, ha szkript használatával fér hozzá a jelentéskészítő API-hoz. A regisztráció egy **alkalmazásazonosítót** ad meg, amely az engedélyezési hívásokhoz szükséges, és lehetővé teszi a kód számára a jogkivonatok fogadását.

Ahhoz, hogy címtárát az Azure AD Reporting API-hoz való hozzáférésre konfigurálja, be kell  jelentkeznie az [Azure Portal-be](https://portal.azure.com) egy Olyan Azure-rendszergazdai fiókkal, amely szintén tagja a globális rendszergazdai címtár-szerepkörnek az Azure AD-bérlőben.

> [!IMPORTANT]
> A rendszergazdai jogosultságokkal rendelkező hitelesítő adatokkal futó alkalmazások rendkívül hatékonyak, ezért ne feledje az alkalmazás azonosítóját és titkos hitelesítő adatait egy biztonságos helyen.
> 

**Azure AD-alkalmazás regisztrálása:**

1. A bal [Azure Portal](https://portal.azure.com)panelen válassza **Azure Active Directory** gombra.
   
    ![A Azure Active Directory menüből kiválasztott Azure Portal képernyőkép.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** válassza a **Alkalmazásregisztrációk** lehetőséget.

    ![Képernyőkép a Alkalmazásregisztrációk menüből kiválasztott elemekről.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. A **Alkalmazásregisztrációk** válassza az Új **regisztráció lehetőséget.**

    ![A kiválasztott Új regisztráció képernyőképe.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Az **Alkalmazás regisztrálása** lap:

    ![Képernyőkép az Alkalmazás regisztrálása lapról, ahol megadhatja az ebben a lépésben megadott értékeket.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. A Név **szövegmezőbe** írja be a következőt: `Reporting API application` .

    b. A **Támogatott fiókok mezőbe írja be a következőt:**, válassza a Csak ebben a szervezeti **fiókban lehetőséget.**

    c. Az **Átirányítási URL-cím**  mezőben válassza **a Web szövegmezőt,** és írja be a következőt: `https://localhost` .

    d. Válassza a **Regisztráció** lehetőséget. 


## <a name="grant-permissions"></a>Engedélyek megadása 

Az elérni kívánt API-któl függően a következő engedélyeket kell az alkalmazásnak kiadni:  

| API | Engedély |
| --- | --- |
| Microsoft Azure Active Directory | Címtáradatok olvasása |
| Microsoft Graph | Az auditnaplók adatainak olvasása |

![Az A P I-engedélyek panelen az Engedély hozzáadása lehetőséget megjelenítő képernyőkép.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A következő szakasz mindkét API lépéseit sorolja fel. Ha valamelyik API-t nem szeretné elérni, kihagyhatja a kapcsolódó lépéseket.

**Engedélyek megadása az alkalmazás számára az API-k használatára:**


1. Válassza **az API-engedélyek,** majd **az Engedély hozzáadása lehetőséget.** 

    ![Képernyőkép az A P I Permissions (A P I-engedélyek) lapról, ahol kiválaszthatja az Engedély hozzáadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. A Request **API permissions (API-engedélyek kérése) lapon** keresse meg **a Support legacy API** Azure Active Directory Graph (Örökölt API-k **támogatása) gráfot.** 

    ![Képernyőkép a Request A P I permissions (A P I-engedélyek kérése) lapról, ahol kiválaszthatja a Azure Active Directory Graph lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. A Szükséges **engedélyek lapon** válassza az Alkalmazásengedélyek lehetőséget, **bontsa** ki **a Címtár** **jelölőnégyzetet.ReadAll**.  Válassza **az Engedélyek hozzáadása lehetőséget.**

    ![Képernyőkép a Request A P I permissions (A P I-engedélyek kérése) lapról, ahol kiválaszthatja az Alkalmazásengedélyek lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. A **Reporting API Application - API Permissions (Jelentéskészítő API-alkalmazás – API-engedélyek) lapon** válassza a **Grant admin consent (Rendszergazdai jóváhagyás megadása) lehetőséget.** 

    ![Képernyőkép a JELENTÉSKÉSZÍTÉSI ALKALMAZÁS A P I-engedélyek lapról, ahol kiválaszthatja a Rendszergazdai hozzájárulás megadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Megjegyzés: **Microsoft Graph** a rendszer alapértelmezés szerint hozzáadja az adatokat az API-regisztráció során.

    ![Képernyőkép az A P I-engedélyek oldalról, ahol kiválaszthatja az Engedély hozzáadása lehetőséget.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Konfigurációs beállítások gyűjtése 

Ez a szakasz bemutatja, hogyan lehet lekért beállításokat a címtárból:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkulcs

Ezekre az értékekre szüksége lesz a jelentéskészítő API hívásának konfigurálásakor. 

### <a name="get-your-domain-name"></a>A tartománynév lekért neve

**A tartománynév lekért neve:**

1. A bal [Azure Portal](https://portal.azure.com)navigációs panelen válassza a Azure Active Directory **lehetőséget.**
   
    ![A Azure Active Directory menüből kiválasztott Azure Portal képernyőkép.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** válassza az **Egyéni tartománynevek lehetőséget.**

    ![A képernyőképen az egyéni tartománynevek vannak kiválasztva Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Másolja ki a tartománynevet a tartományok listájából.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-azonosítójának lekérte

**Az alkalmazás ügyfél-azonosítójának lekért száma:**

1. A bal [Azure Portal](https://portal.azure.com)navigációs panelen kattintson a **Azure Active Directory.**
   
    ![A Azure Active Directory menüből kiválasztott Azure Portal képernyőkép.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Válassza ki az alkalmazást az **Alkalmazásregisztrációk** lapon.

3. Az alkalmazás oldalán lépjen az **Alkalmazásazonosító elemre,** és válassza **a Kattintson a másoláshoz lehetőséget.**

    ![Képernyőkép a Jelentéskészítési A P I-alkalmazás oldalról, ahol az alkalmazás-ID-t másolhatja.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Az alkalmazás titkos ügyfél-titkos útjára vonatkozó lekért kérelem
 Kerülje az auditnaplók elérésére vagy az API-val való bejelentkezésre vonatkozó hibákat.

**Az alkalmazás titkos ügyfél-titkos útjára vonatkozó lekért kérelem:**

1. A bal [Azure Portal](https://portal.azure.com)navigációs panelen kattintson a **Azure Active Directory.**
   
    ![A Azure Active Directory menüből kiválasztott Azure Portal képernyőkép.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Válassza ki az alkalmazást az **Alkalmazásregisztrációk** lapon.

3.  Válassza **a Tanúsítványok és titkos** kulcsok lehetőséget az API-alkalmazás **lapon,** az Ügyfél titkos kulcsok **szakaszában** kattintson az + Új titkos ügyfél titkos **kód elemre.** 

    ![Képernyőkép a Tanúsítványok & titkos kulcsok lapról, ahol titkos ügyféltitkot adhat hozzá.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Az **Add a client secret (Titkos ügyfél titkos útjára hozzáadása) lapon** adja hozzá a következőt:

    a. A Leírás **szövegmezőbe** írja be a következőt: `Reporting API` .

    b. Az **Expires (Lejárat) mezőben** válassza a **In 2 years (2 év múlva) lehetőséget.**

    c. Kattintson a **Mentés** gombra.

    d. Másolja a kulcs értékét.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Hibák elhárítása a jelentéskészítő API-ban

Ez a szakasz azokat a gyakori hibaüzeneteket sorolja fel, amelyek a tevékenységjelentések a Microsoft Graph API-val való elérésekor, valamint a megoldásuk lépéseit sorolja fel.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Hiba: Nem sikerült lekért felhasználói szerepkörök a Microsoft Graph

 Jelentkezzen be a fiókjába mindkét bejelentkezési gombbal a Graph Explorer felhasználói felületén, hogy elkerülje a hibaüzenetet, amikor a Graph Explorerrel próbál bejelentkezni. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Hiba: Nem sikerült a prémium szintű licenc ellenőrzése a Microsoft Graph 

Ha ez a hibaüzenet jelenik meg, amikor a Graph Explorerrel próbál hozzáférni a bejelentkezésekhez, válassza a bal oldali navigációs sávon a fiók alatti Engedélyek módosítása lehetőséget, majd válassza a **Tasks.ReadWrite** és **a Directory.Read.All** lehetőséget.  

![Engedélyek módosítása felhasználói felület](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: A bérlő nem B2C, vagy a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez prémium Azure Active Directory 1-es (P1) licenc szükséges. Ha ezt a hibaüzenetet látja a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Hiba: Az engedélyezett szerepkörök nem tartalmazzák a Felhasználót. 

 Kerülje az auditnaplók elérésével vagy az API-val való bejelentkezéssel kapcsolatos hibákat. Győződjön meg arról, hogy  a  fiókja a biztonsági olvasó vagy a jelentésolvasó szerepkör tagja a Azure Active Directory bérlőben.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik az AAD "Címtáradatok olvasása" engedélye 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazásból hiányzik Microsoft Graph API "Az összes auditnapló-adat olvasása" engedély

Kövesse az Előfeltételek dokumentumban található lépéseket a [Azure Active Directory Reporting API](howto-configure-prerequisites-for-reporting-api.md) eléréséhez annak biztosításához, hogy az alkalmazás a megfelelő engedélyekkel fut. 

## <a name="next-steps"></a>Következő lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Api-referencia naplózása](/graph/api/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin)