---
title: Az Azure AD-hitelesítés használata az Azure Media Services API REST-tel való eléréséhez | Microsoft Docs
description: Megtudhatja, hogyan érheti el Azure Media Services API-t Azure Active Directory hitelesítéssel a REST használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a2b4e7bf03ebb1fbc197b78287cb50b3f421d713
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017307"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>A Media Services API és a REST hozzáférése Azure AD-hitelesítéssel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Az Azure AD-hitelesítés Azure Media Services használatával történő használatakor kétféleképpen végezheti el a hitelesítést:

- A **felhasználói hitelesítés** az alkalmazást használó személyt hitelesíti Azure Media Services erőforrásokkal való kommunikációhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Ilyen például az a felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak. 
- A **szolgáltatás egyszerű hitelesítése** hitelesíti a szolgáltatást. Azok az alkalmazások, amelyek gyakran használják ezt a hitelesítési módszert a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok, például a Web Apps, a Function apps, a Logic apps, az API-k vagy a Service-alkalmazások futtatásához.

    Ez az oktatóanyag bemutatja, hogyan használható az Azure AD **szolgáltatás egyszerű** hitelesítése az AMS API Rest-tel való eléréséhez. 

    > [!NOTE]
    > Az **egyszerű szolgáltatásnév** a Azure Media Serviceshoz csatlakozó legtöbb alkalmazás ajánlott gyakorlata. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hitelesítő adatok beolvasása a Azure Portal
> * Hozzáférési jogkivonat beszerzése a Poster használatával
> * Az **assets** API tesztelése a hozzáférési jogkivonat használatával


> [!IMPORTANT]
> A Media Services jelenleg az Azure Access Control Services hitelesítési modelljét támogatja. A Access Control hitelesítés azonban 2018. június 1-től lesz elavult. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Hozzon létre egy Azure Media Services fiókot a Azure Portal használatával](media-services-portal-create-account.md).
- Tekintse át a [hozzáférés Azure Media Services API-t az Azure ad-hitelesítés áttekintésével foglalkozó](media-services-use-aad-auth-to-access-ams-api.md) cikkben.
- Telepítse a [Poster](https://www.getpostman.com/) Rest-ügyfelet, hogy végrehajtsa a cikkben látható REST API-kat. 

    Ebben az oktatóanyagban a **Poster** -t használjuk, de minden Rest-eszköz megfelelő lenne. Más alternatívák: **Visual Studio Code** a REST beépülő modullal vagy a **Telerik Hegedűs**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Hitelesítő adatok beolvasása a Azure Portal

### <a name="overview"></a>Áttekintés

Media Services API eléréséhez a következő adatpontokat kell összegyűjtenie.

|Beállítás|Példa|Leírás|
|---|-------|-----|
|Azure Active Directory bérlői tartomány|microsoft.onmicrosoft.com|Az Azure AD-t biztonságos jogkivonat-szolgáltatás (STS) végpontja hozza létre a következő formátumban: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . Az Azure AD JWT bocsát ki az erőforrásokhoz (hozzáférési token) való hozzáféréshez.|
|REST API-végpont|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Ez az a végpont, amelyen az alkalmazás összes Media Services REST API hívása történik.|
|Ügyfél-azonosító (alkalmazás azonosítója)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-alkalmazás (ügyfél) azonosítója. A hozzáférési jogkivonat beszerzéséhez az ügyfél-azonosító szükséges. |
|Titkos ügyfélkulcs|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Azure AD-alkalmazás kulcsai (ügyfél titka). A hozzáférési jogkivonat beszerzéséhez az ügyfél titkos kulcsa szükséges.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Azure Active Directory hitelesítési adatok beolvasása a Azure Portal

Az információk beszerzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. Navigáljon az AMS-példányhoz.
3. Válassza az **API-hozzáférés** lehetőséget.
4. Kattintson a **kapcsolódás Azure Media Services API-hoz az egyszerű szolgáltatásnév** lehetőségre.

    ![A "Media Services" menüből kiválasztott "A P I hozzáférés", a jobb oldali ablaktáblán pedig a "kapcsolódás Azure Media Services A P I és a szolgáltatáshoz](./media/connect-with-rest/connect-with-rest01.png)

5. Válasszon ki egy meglévő **Azure ad-alkalmazást** , vagy hozzon létre egy újat (alább látható).

    > [!NOTE]
    > Ahhoz, hogy az Azure Media REST-kérelme sikeres legyen, a hívó felhasználónak rendelkeznie kell **közreműködői** vagy **tulajdonosi** szerepkörrel ahhoz a Media Services fiókhoz, amelyhez hozzáférést próbál. Ha olyan kivételt kap, amely azt jelzi, hogy "a távoli kiszolgáló a következő hibát adta vissza: (401) nem engedélyezett", lásd: [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Ha új AD-alkalmazást kell létrehoznia, kövesse az alábbi lépéseket:
    
   1. Kattintson a **Create New (új létrehozása**) gombra.
   2. Adjon meg egy nevet.
   3. Kattintson ismét az **új létrehozása** gombra.
   4. Kattintson a **Mentés** gombra.

      ![A "Create app" (új alkalmazás létrehozása) szövegmezővel és a "Mentés" gomb kiválasztásával megjelenített képernyőkép.](./media/connect-with-rest/new-app.png)

      Az új alkalmazás megjelenik az oldalon.

6. Kérje le az **ügyfél-azonosítót** (az alkalmazás azonosítóját).
    
   1. Válassza ki az alkalmazást.
   2. Szerezze be az **ügyfél-azonosítót** a jobb oldalon található ablakból. 

      ![Képernyőfelvétel: az "Azure A D alkalmazás" és az "alkalmazás kezelése" lehetőség van kiválasztva, és a jobb oldali ablaktáblán a "Client I D" elem van kiemelve.](./media/connect-with-rest/existing-client-id.png)

7. Az alkalmazás **kulcsának** (az ügyfél titkos kulcsa) beolvasása. 

   1. Kattintson az **alkalmazás kezelése** gombra (figyelje meg, hogy az ügyfél-azonosító információ az **alkalmazás azonosítója** alatt található). 
   2. Nyomja meg a **kulcsok** gombot.
    
       ![Képernyőkép: az "alkalmazás kezelése" gomb kiválasztásakor az "Application I D" a középső ablaktáblán látható, a jobb oldali ablaktáblán pedig a "kulcsok" lehetőség van kiválasztva.](./media/connect-with-rest/manage-app.png)
   3. Az alkalmazás kulcsának (az ügyfél titkos kulcsa) előállításához töltse ki a **leírást** , és nyomja **le** a **Mentés gombot**.
    
       A **Mentés** gomb megnyomásakor megjelenik a kulcs értéke. Másolja a kulcs értékét a panel elhagyása előtt.

   ![API-hozzáférés](./media/connect-with-rest/connect-with-rest03.png)

Az AD-kapcsolódási paraméterekhez értékeket adhat hozzá a web.config vagy app.config fájlhoz, hogy később használhassa a kódot.

> [!IMPORTANT]
> Az **ügyfél kulcsa** fontos titok, és megfelelő védelemmel kell rendelkeznie egy kulcstartóban vagy éles környezetben.

## <a name="get-the-access-token-using-postman"></a>Hozzáférési jogkivonat beszerzése a Poster használatával

Ez a szakasz bemutatja, hogyan használható a **Poster** egy olyan REST API végrehajtásához, amely egy JWT tulajdonosi tokent (hozzáférési tokent) ad vissza. Bármely Media Services REST API meghívásához hozzá kell adnia az "engedélyezés" fejlécet a hívásokhoz, és hozzá kell adnia a "tulajdonos *your_access_token*" értéket az egyes hívásokhoz (ahogy az oktatóanyag következő szakaszában is látható). 

1. A **Poster** megnyitása.
2. Válassza a **POST** lehetőséget.
3. Adja meg a bérlő nevét tartalmazó URL-címet a következő formátumban: a bérlő nevének a **. onmicrosoft.com** értékkel kell végződnie, és az URL-címnek **oauth2/tokenrel** kell végződnie: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Válassza a **fejlécek** fület.
5. Adja meg a **fejlécek** adatait a "kulcs/érték" adatrács használatával. 

    ![A "headers" (fejlécek) lapot és a "tömeges szerkesztés" műveletet megjelenítő képernyőkép.](./media/connect-with-rest/headers-data-grid.png)

    Azt is megteheti, hogy a Poster-ablak jobb oldalán található **tömeges szerkesztés** hivatkozásra kattint, és beilleszti a következő kódot.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Nyomja meg a **törzs** fület.
7. Adja meg a törzs adatait a "kulcs/érték" adatrács használatával (cserélje le az ügyfél-azonosítót és a titkos értékeket). 

    ![Adatrács](./media/connect-with-rest/data-grid.png)

    Azt is megteheti, hogy a Poster ablak jobb oldalán a **tömeges szerkesztés** elemre kattint, és beilleszti a következő törzset (az ügyfél-azonosító és a titkos értékek helyett):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Kattintson a **Küldés** gombra.

    ![Képernyőkép, amely megjeleníti a "post" szövegmezőt, a "headers" és a "Body" fület, valamint a "access_token", valamint a "Küldés" gombot.](./media/connect-with-rest/connect-with-rest04.png)

A visszaadott válasz tartalmazza azt a **hozzáférési jogkivonatot** , amelyet az AMS API-k eléréséhez használnia kell.

## <a name="test-the-assets-api-using-the-access-token"></a>Az **assets** API tesztelése a hozzáférési jogkivonat használatával

Ez a szakasz bemutatja, hogyan érheti el az **assets** API-t a **Poster** használatával.

1. A **Poster** megnyitása.
2. Válassza a **GET** lehetőséget.
3. Illessze be a REST API végpontot (például: https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Válassza az **Engedélyezés** lapot. 
5. Jelölje ki a **tulajdonosi jogkivonatot**.
6. Illessze be az előző szakaszban létrehozott jogkivonatot.

    ![jogkivonat beolvasása](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > A Poster UX a Mac és a számítógép között eltérő lehet. Ha a Mac-verzió nem rendelkezik a "tulajdonosi jogkivonat" lehetőséggel a **hitelesítési** szakasz legördülő listájában, akkor manuálisan kell hozzáadnia az **engedélyezési** fejlécet a Mac-ügyfélen.

   ![Hitelesítési fejléc](./media/connect-with-rest/auth-header.png)

7. Válassza a **fejlécek** lehetőséget.
5. A Poster ablak jobb oldalán kattintson a **tömeges szerkesztés** hivatkozásra.
6. Illessze be a következő fejléceket:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Kattintson a **Küldés** gombra.

A visszaadott válasz tartalmazza a fiókjában található eszközöket.

## <a name="next-steps"></a>Következő lépések

* Próbálja ki ezt a mintakód az [Azure ad-hitelesítésben Azure Media Services hozzáféréshez: mindkettőn keresztül REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Fájlok feltöltése a .NET-tel](media-services-dotnet-upload-files.md)
