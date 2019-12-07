---
title: Poster konfigurálása Azure Media Services REST API hívásokhoz
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Poster-t, hogy felhasználható legyen Azure Media Services (AMS) REST API-k meghívására.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: eee46bd8642f5ad4a8d036b9f6bf9a464c0d09f3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888104"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Poster konfigurálása Media Services REST API hívásokhoz

Ez a cikk bemutatja, hogyan konfigurálhatja a **Poster** -t, hogy felhasználható legyen Azure Media Services (AMS) REST API-k meghívására. A cikk bemutatja, hogyan importálhatja a környezet és a gyűjtemény fájljait a **Poster**-ba. A gyűjtemény a Azure Media Services (AMS) REST API-kat meghívó HTTP-kérések csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

A fejlesztés megkezdése előtt tekintse át [Media Services V3 API-k fejlesztését](media-services-apis-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md). Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét. 
- Az API-k [eléréséhez](access-api-cli-how-to.md) szükséges információk beolvasása
- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Code** REST beépülő modullal vagy **Telerik Fiddler**. 

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman konfigurálása

### <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Nyissa meg a **Poster** alkalmazást.
2. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Környezet felügyelete](./media/develop-with-postman/postman-import-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
2. Keresse meg az `Azure Media Service v3 Environment.postman_environment.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le.
6. Az **Azure Media Service v3 Environment** környezet hozzáadása megtörténik.

    > [!Note]
    > Frissítse a hozzáférési változókat a fenti **Hozzáférés a Media Services API-hoz** szakaszban található értékekkel.

7. Kattintson duplán a kiválasztott fájlra, és adja meg azokat az értékeket, amelyeket az API-k elérésére vonatkozó lépések követésével kapott.
8. Zárja be a párbeszédpanelt.
9. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3.postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3.postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

Az AMS v3-erőforrások kezelésének megkezdése előtt be kell szereznie és be kell állítania az Azure AD-tokent az egyszerű szolgáltatásnév hitelesítéséhez.

1. A Poster alkalmazás bal oldali ablakában válassza az "1. lépés: HRE-hitelesítési jogkivonat beszerzése" lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének.  

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Hibakeresés 

* Ha az alkalmazás a "HTTP 504: Gateway timeout" művelettel meghiúsul, győződjön meg arról, hogy a Location változó nincs explicit módon beállítva a Media Services fiók várt helyétől eltérő értékre. 
* Ha "a fiók nem található" hibaüzenet jelenik meg, ellenőrizze, hogy a szövegtörzs JSON-üzenetében található Location tulajdonság arra a helyre van-e beállítva, amelyben a Media Services fiók található. 

## <a name="see-also"></a>Lásd még:

- [Fájlok feltöltése egy Media Services fiókba – REST](upload-files-rest-how-to.md)
- [Szűrők létrehozása Media Services-REST használatával](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-alapú REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Következő lépések

- [Adatfolyam-fájlok a REST](stream-files-tutorial-with-rest.md)-tel.  
- [Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a videó – REST](stream-files-tutorial-with-rest.md)
