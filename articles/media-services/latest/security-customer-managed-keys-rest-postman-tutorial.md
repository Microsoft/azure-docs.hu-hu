---
title: Ügyfél által felügyelt kulcsok vagy BYOK használata REST API
description: Ebben az oktatóanyagban az ügyfél által felügyelt kulcsokat használja, vagy saját kulcsot (BYOK) használ egy Azure Media Services Storage-fiókkal.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282384"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Oktatóanyag: ügyfél által felügyelt kulcsok vagy BYOK használata Media Services REST API

Az 2020-05-01 API-val az ügyfél által felügyelt RSA-kulcs egy rendszerfelügyelt identitással rendelkező Azure Media Services-fiókkal használható. Ez az oktatóanyag egy Poster-gyűjteményt és-környezetet tartalmaz, amely REST-kérelmeket küld az Azure-szolgáltatásoknak. A használt szolgáltatások a következők:

- Azure Active Directory (Azure AD) alkalmazás regisztrálása a Poster-ban
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

Ebben az oktatóanyagban megtanulhatja, hogyan használhatja a Poster-t:

> [!div class="checklist"]
> - Jogkivonatok beszerzése az Azure-szolgáltatásokkal való használathoz.
> - Hozzon létre egy erőforráscsoportot és egy Storage-fiókot.
> - Rendszer által felügyelt identitással rendelkező Media Services-fiók létrehozása.
> - Hozzon létre egy Key vaultot az ügyfél által felügyelt RSA-kulcs tárolásához.
> - Frissítse a Media Services fiókot az RSA-kulcs és a Storage-fiók használatára.
> - Használjon változókat a Poster-ben.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

1. Egy egyszerű szolgáltatásnév regisztrálása a megfelelő engedélyekkel.
1. A [Poster](https://www.postman.com)telepítése.
1. Töltse le az oktatóanyaghoz tartozó Poster-gyűjteményt az [Azure-minták: Media-Services-Customer-Managed-Keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Egyszerű szolgáltatásnév regisztrálása a szükséges engedélyekkel

1. [Egyszerű szolgáltatásnév létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md).
1. Lépjen a [2. lehetőségre: hozzon létre egy új alkalmazás titkot](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) a szolgáltatás egyszerű titkának beszerzéséhez.

   > [!IMPORTANT]
   >Másolja ki és mentse a titkot későbbi használatra. A titkos oldal a portálon való elhagyása után nem fér hozzá a titokhoz.

1. Rendeljen engedélyeket az egyszerű szolgáltatáshoz az alábbi képernyőképen látható módon:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Képernyőfelvétel: az egyszerű szolgáltatásnév számára szükséges engedélyek.":::
   Az engedélyek a szolgáltatás, az engedély neve, a típus és a leírás szerint jelennek meg. Azure Key Vault: felhasználói megszemélyesítés, delegált, teljes hozzáférés Azure Key Vaulthoz. Azure Service Management: felhasználói megszemélyesítés, delegált, hozzáférés az Azure Service Managementhez szervezeti felhasználóként. Azure Storage: felhasználói megszemélyesítés, delegált, hozzáférés az Azure Storage-hoz. Media Services: felhasználói megszemélyesítés, delegált, hozzáférés a Media Services szolgáltatáshoz. Microsoft Graph: user. Read, delegált, bejelentkezés és felhasználói profil olvasása.
   :::image-end:::

### <a name="install-postman"></a>Poster telepítése

Ha még nem telepítette az Azure-beli Poster-t, a következő címen érheti el: [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>A gyűjtemény letöltése és importálása

Töltse le az oktatóanyaghoz tartozó Poster-gyűjteményt az [Azure-minták: Media-Services-Customer-Managed-Keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>A Poster-gyűjtemény és-környezet telepítése

1. Poster futtatása.
1. Válassza az **Importálás** lehetőséget.
1. Válassza a **fájlok feltöltése** lehetőséget.
1. Nyissa meg a gyűjtemény és a környezeti fájlok mentési helyét.
1. Válassza ki a gyűjtemény és a környezet fájljait.
1. Válassza a **Megnyitás** lehetőséget. Megjelenik egy figyelmeztetés, amely szerint a fájlok nem lesznek importálva API-ként, de gyűjteményekként. Ez a figyelmeztetés rendben van. Ezt szeretné.

A gyűjtemény most a gyűjtemények BYOK jelenik meg. A környezeti változók is megjelennek a környezetekben.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>A gyűjtemény REST API-kéréseinek megismerése

A gyűjtemény a következő REST API kérelmeket biztosítja.

> [!NOTE]
>
>- A kérelmeket a megadott sorban kell elküldeni.
>- A legtöbb kérelemnek van olyan tesztelési szkriptje, amely dinamikusan hozza létre a globális változókat a következő kérelemhez a sorozatban.
>- Nem kell manuálisan létrehoznia globális változókat.

A Poster-ben ezek a változók zárójelek között találhatók. Például: `{{bearerToken}}`.

1. Azure AD-jogkivonat beszerzése: a teszt beállítja a **bearerToken** globális változót.
2. Microsoft Graph token beszerzése: a teszt beállítja a **graphToken** globális változót.
3. Egyszerű szolgáltatásnév részleteinek beolvasása: a teszt beállítja a **servicePrincipalObjectId** globális változót.
4. Storage-fiók létrehozása: a teszt beállítja a **storageAccountId** globális változót.
5. Rendszer által felügyelt identitással rendelkező Media Services fiók létrehozása: a teszt beállítja a globális változó **principalId**.
6. Kulcstartó létrehozása az egyszerű szolgáltatás elérésének biztosításához: a teszt beállítja a globális változó **keyVaultId**.
7. Key Vault token beszerzése: a teszt beállítja a **keyVaultToken** globális változót.
8. Hozza létre az RSA-kulcsot a Key vaultban: a test beállítja a **keyId** globális változót.
9. Frissítse a Media Services fiókot a kulcs a Storage-fiókkal való használatához: ehhez a kérelemhez nincs tesztelési parancsfájl.

## <a name="define-environment-variables"></a>Környezeti változók definiálása

1. Válassza ki a környezet legördülő listát, és váltson a letöltött környezetre.
1. Hozza létre a környezeti változókat a Poster-ben. Ezeket a zárójelek között szereplő változókként is használják. Például: `{{tenantId}}`.

    - **tenantId**: a bérlő azonosítója.
    - **servicePrincipalId**: az Ön által a kedvenc metódussal (például portál vagy parancssori felület) létrehozott egyszerű szolgáltatásnév azonosítója.
    - **servicePrincipalSecret**: az egyszerű szolgáltatáshoz létrehozott titok.
    - **előfizetés**: az előfizetés azonosítója.
    - **storageName**: a tárhelyhez adni kívánt név.
    - **accountName**: a használni kívánt Media Services fiók neve.
    - **keyVaultName**: a használni kívánt kulcstároló neve.
    - **resourceLocation**: a hely **CentralUs** , illetve az erőforrások, ahová el szeretné helyezni az erőforrásokat. Ez a gyűjtemény csak a **CentralUs**-mel lett tesztelve.
    - **resourceGroup**: az erőforráscsoport neve.

    Az alábbi változók az Azure-erőforrások használatának szabványos eszközei. Így nem kell módosítani őket.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>A kérések elküldése

A környezeti változók definiálása után a kérelmeket az előző sorozatban egyszerre is futtathatja. Vagy a Poster 's Runner használatával is futtathatja a gyűjteményt.

## <a name="change-the-key"></a>A kulcs módosítása

Media Services automatikusan észleli a kulcs módosításakor. A folyamat teszteléséhez hozzon létre egy másik kulcsfontosságú verziót ugyanahhoz a kulcshoz. Media Servicesnak 15 percnél rövidebbnek kell lennie a kulcs észleléséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a létrehozott erőforrások használatát, és *nem kívánja tovább használni a számlázást*, törölje őket.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan teheti meg a következőket:
> [!div class="nextstepaction"]
> [Távoli fájl kódolása URL-cím alapján és a videó továbbítása a REST-tel](stream-files-tutorial-with-rest.md)