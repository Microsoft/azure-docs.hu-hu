---
title: Végfelhasználói hitelesítés – Data Lake Storage Gen1 az Azure AD-vel
description: Ismerje meg, hogyan érheti el a végfelhasználói hitelesítést Azure Data Lake Storage Gen1 használatával Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103781"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Végfelhasználói hitelesítés Azure Data Lake Storage Gen1 használatával Azure Active Directory
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

A Azure Data Lake Storage Gen1 Azure Active Directory használ a hitelesítéshez. Data Lake Storage Gen1 vagy Azure Data Lake Analytics rendszerű alkalmazás létrehozása előtt el kell döntenie, hogyan hitelesítheti az alkalmazást Azure Active Directory (Azure AD) használatával. A két fő lehetőség közül választhat:

* Végfelhasználói hitelesítés (ez a cikk)
* Szolgáltatások közötti hitelesítés (válassza ezt a lehetőséget a fenti legördülő listából)

Mindkét lehetőség eredményeként az alkalmazás egy OAuth 2,0-tokenrel van ellátva, amely az Data Lake Storage Gen1 vagy Azure Data Lake Analyticshoz benyújtott minden kérelemhez csatolva lesz.

Ez a cikk azt ismerteti, hogyan hozhat létre egy **natív Azure ad-alkalmazást a végfelhasználói hitelesítéshez**. A szolgáltatások közötti hitelesítéshez használható Azure AD-alkalmazás konfigurálásával kapcsolatos utasításokért lásd: [szolgáltatások közötti hitelesítés Data Lake Storage Gen1 Azure Active Directory használatával](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Az előfizetés azonosítója. A Azure Portal kérheti le. Például a Data Lake Storage Gen1 fiók panelről érhető el.

    ![Előfizetés-azonosító lekérése](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD-tartományneve. Lekérheti a Azure Portal jobb felső sarkában lévő egér fölé. Az alábbi képernyőképen a tartománynév **contoso.onmicrosoft.com**, a zárójeleken belüli GUID pedig a bérlő azonosítója.

    ![HRE-tartomány beolvasása](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Az Azure-bérlő azonosítója. A bérlői azonosító lekérésével kapcsolatos utasításokért lásd: [a bérlő azonosítójának](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)beolvasása.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
Ez a hitelesítési mechanizmus az ajánlott módszer, ha azt szeretné, hogy a végfelhasználó az Azure AD-n keresztül jelentkezzen be az alkalmazásba. Az alkalmazás ezután elérheti az Azure-erőforrásokat a bejelentkezett végfelhasználóval megegyező hozzáférési szintű hozzáféréssel. A végfelhasználónak rendszeresen meg kell adnia hitelesítő adatait ahhoz, hogy az alkalmazás fenntartsa a hozzáférést.

A végfelhasználói bejelentkezés eredménye az, hogy az alkalmazás hozzáférési jogkivonatot és frissítési jogkivonatot kap. A hozzáférési jogkivonat minden Data Lake Storage Gen1 vagy Data Lake Analyticshoz kapcsolódó kérelemhez csatolva lesz, és alapértelmezés szerint egy órára érvényes. A frissítési token új hozzáférési jogkivonat beszerzésére használható, és alapértelmezés szerint legfeljebb két hétig érvényes. A végfelhasználói bejelentkezéshez két különböző megközelítés használható.

### <a name="using-the-oauth-20-pop-up"></a>A OAuth 2,0 előugró ablak használata
Az alkalmazás elindíthat egy OAuth 2,0-alapú engedélyezési előugró ablakot, amelyben a végfelhasználó megadhatja a hitelesítő adatait. Ez az előugró ablak az Azure AD kétfaktoros hitelesítési (2FA) folyamatával is együttműködik, ha szükséges.

> [!NOTE]
> Ez a módszer még nem támogatott a Python és a Java rendszerhez készült Azure AD Authentication Library (ADAL) szolgáltatásban.
>
>

### <a name="directly-passing-in-user-credentials"></a>Közvetlenül a felhasználói hitelesítő adatok átadása
Az alkalmazás közvetlenül is biztosíthatja a felhasználói hitelesítő adatokat az Azure AD-nek. Ez a metódus csak szervezeti azonosító felhasználói fiókokkal működik; nem kompatibilis a személyes/"élő azonosító" felhasználói fiókokkal, beleértve a vagy a végződésű fiókokat is @outlook.com @live.com . Továbbá ez a módszer nem kompatibilis olyan felhasználói fiókokkal, amelyek megkövetelik az Azure AD kétfaktoros hitelesítését (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Mire van szükségem ehhez a megközelítéshez?
* Azure AD-tartománynév. Ez a követelmény már szerepel a cikk előfeltételeiben.
* Azure AD-bérlő azonosítója. Ez a követelmény már szerepel a cikk előfeltételeiben.
* Azure AD **natív alkalmazás**
* Az Azure AD natív alkalmazáshoz tartozó alkalmazás azonosítója
* Az Azure AD natív alkalmazás átirányítási URI-ja
* Delegált engedélyek beállítása


## <a name="step-1-create-an-active-directory-native-application"></a>1. lépés: Active Directory natív alkalmazás létrehozása

Hozzon létre és konfiguráljon egy natív Azure AD-alkalmazást a végfelhasználói hitelesítéshez Data Lake Storage Gen1 a Azure Active Directory használatával. Útmutatásért lásd: [Azure ad-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md).

A hivatkozás utasításait követve győződjön meg róla, hogy a **natív** lehetőséget választotta az alkalmazás típusaként, ahogyan az a következő képernyőképen látható:

![Webalkalmazás létrehozása](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Natív alkalmazás létrehozása")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2. lépés: az alkalmazás-azonosító és az átirányítási URI lekérése

Lásd: az alkalmazás-azonosító [beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) az alkalmazás azonosítójának lekéréséhez.

Az átirányítási URI beolvasásához hajtsa végre a következő lépéseket.

1. A Azure Portal válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk** lehetőséget, majd keresse meg és kattintson a létrehozott Azure ad natív alkalmazásra.

2. Az alkalmazás **Beállítások** paneljén kattintson az **átirányítási URI**-k elemre.

    ![Átirányítási URI lekérése](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Másolja a megjelenő értéket.


## <a name="step-3-set-permissions"></a>3. lépés: engedélyek beállítása

1. A Azure Portal válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk** lehetőséget, majd keresse meg és kattintson a létrehozott Azure ad natív alkalmazásra.

2. Az alkalmazás **Beállítások** paneljén kattintson a **szükséges engedélyek** elemre, majd a **Hozzáadás** gombra.

    ![Képernyőkép a beállítások panelről, az átirányítási U R I kapcsolóval, valamint az átirányítási U R I panelen a tényleges U R-vel.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Az **API-hozzáférés hozzáadása** panelen kattintson az **API kiválasztása** elemre, majd a **Azure Data Lake** elemre, végül a **kiválasztás** gombra.

    ![Képernyőkép az API-hozzáférés hozzáadása panelről, az API kiválasztása lehetőség kiválasztásával és az API kiválasztása panelen a Azure Data Lake lehetőséggel és a kinevezett Select lehetőséggel.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  Az **API-hozzáférés hozzáadása** panelen kattintson az **engedélyek kiválasztása** elemre, jelölje be a jelölőnégyzetet, hogy **teljes hozzáférést adjon a Data Lake Storehoz**, majd kattintson a **kiválasztás** gombra.

    ![Képernyőkép az API-hozzáférés hozzáadása panelről, a kinevezett engedélyek kiválasztása lehetőséggel, a hozzáférés engedélyezése panelen pedig a teljes hozzáféréssel rendelkezik a Azure Data Lake szolgáltatás lehetőséghez, és a Select (kiválasztás) lehetőséget.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kattintson a **Kész** gombra.

5. Ismételje meg az utolsó két lépést a **Windows Azure Service Management API** engedélyeinek megadásához is.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy natív Azure AD-alkalmazást hozott létre, és összegyűjtötte a szükséges információkat a .NET SDK, Java SDK, REST API stb. használatával létrehozott ügyfélalkalmazások számára. A következő cikkekkel megtudhatja, hogyan használhatja az Azure AD-webalkalmazást, hogy először hitelesítse Data Lake Storage Gen1, majd más műveleteket hajtson végre az áruházban.

* [Végfelhasználói hitelesítés Data Lake Storage Gen1 a Java SDK-val](data-lake-store-end-user-authenticate-java-sdk.md)
* [Végfelhasználói hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-end-user-authenticate-net-sdk.md)
* [Végfelhasználói hitelesítés Data Lake Storage Gen1 a Python használatával](data-lake-store-end-user-authenticate-python.md)
* [Végfelhasználói hitelesítés Data Lake Storage Gen1 használatával REST API](data-lake-store-end-user-authenticate-rest-api.md)