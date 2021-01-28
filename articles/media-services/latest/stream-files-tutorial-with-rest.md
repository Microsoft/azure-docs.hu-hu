---
title: Távoli fájl és stream kódolása Media Services használatával
description: Kövesse ennek az oktatóanyagnak a lépéseit egy fájl URL-cím alapján történő kódolásához és a tartalom továbbításához Azure Media Services a REST használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/12/2020
ms.author: inhenkel
ms.openlocfilehash: 21f7203af267f53d37e26390ea73c896ea9db76e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953988"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Oktatóanyag: Távoli fájl kódolása URL-cím alapján és videó streamelése – REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services lehetővé teszi a médiafájlok kódolását olyan formátumokba, amelyek számos böngészőben és eszközön játszhatók le. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. Kódolással kapcsolatos útmutatásért tekintse meg [a kódolás fogalmát](encoding-concept.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan kódolhat egy fájlt egy URL-cím alapján, és hogyan továbbíthatja a videót a Azure Media Services REST használatával. 

![Videó lejátszása](./media/stream-files-tutorial-with-api/final-video.png)

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Media Services-fiók létrehozása
> * Hozzáférés a Media Services API-hoz
> * Postman-fájlok letöltése
> * Postman konfigurálása
> * Kérések küldése a Postman használatával
> * A streamelési URL-cím tesztelése
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).

    Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Más alternatívák: **Visual Studio Code** a REST beépülő modullal vagy a **Telerik Hegedűs**. 

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>Hozzáférési API

Részletes információkat a [hitelesítő adatok Beszerzése Media Services API eléréséhez](access-api-howto.md) című témakörben talál.

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

7. Kattintson duplán a kiválasztott fájlra, és írja be az [API elérésének](#access-api) lépéseiben lekért értékeket.
8. Zárja be a párbeszédpanelt.
9. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3.postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3.postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Kérések küldése a Postman használatával

Ebben a szakaszban olyan kéréseket küldünk, amelyek a kódolás és az URL-ek létrehozása szempontjából fontosak, hogy streamelhesse a fájlját. Konkrétan a következő kéréseket küldi el a rendszer:

1. Azure AD-jogkivonat lekérése a szolgáltatásnév hitelesítéséhez
1. Adatfolyam-végpont elindítása
2. Kimeneti objektum létrehozása
3. Átalakítás létrehozása
4. Feladatok létrehozása
5. Adatfolyam-kereső létrehozása
6. A folyamatos átviteli lokátor elérési útjának listázása

> [!Note]
>  Ez az oktatóanyag azt feltételezi, hogy az összes erőforrást egyedi névvel hozza létre.  

### <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

1. A Poster alkalmazás bal oldali ablakában válassza az "1. lépés: HRE-hitelesítési jogkivonat beszerzése" lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének. Az „AccessToken” értéket beállító kód megtekintéséhez kattintson a **Tests** (Tesztek) fülre. 

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Adatfolyam-végpont elindítása

A folyamatos átvitel engedélyezéséhez először el kell indítania azt a [folyamatos átviteli végpontot](./streaming-endpoint-concept.md) , amelyről továbbítani szeretné a videót.

> [!NOTE]
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont futó állapotban van.

1. A Poster alkalmazás bal oldali ablakában válassza a "streaming and Live" lehetőséget.
2. Ezután válassza a "Start Streamvégpontok" lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **post** műveletet küldi el:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Ha a kérelem sikeres, a `Status: 202 Accepted` rendszer a visszaadott értéket adja vissza.

        Ez az állapot azt jelenti, hogy a kérelem feldolgozásra lett elfogadva; a feldolgozás azonban nem fejeződött be. A művelet állapotát a válasz fejlécében szereplő érték alapján kérdezheti le `Azure-AsyncOperation` .

        Például a következő GET művelet a művelet állapotát adja vissza:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        Az [aszinkron Azure-műveletek nyomon követése](../../azure-resource-manager/management/async-operations.md) című cikk részletesen ismerteti, hogyan követheti nyomon az aszinkron Azure-műveletek állapotát a válaszban visszaadott értékek alapján.

### <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása

A kimeneti [objektum](/rest/api/media/assets) tárolja a kódolási feladat eredményeit. 

1. A Poster alkalmazás bal oldali ablakában válassza az "eszközök" lehetőséget.
2. Ezután válassza a „Create or update an Asset” (Objektum létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **put** műveletet elküldi:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> A Storage-fiók és a tárolók nevét cserélje le a környezeti fájlból származó értékekre, vagy adja meg a sajátját.
>
> A cikk további részében ismertetett lépések végrehajtása során győződjön meg arról, hogy érvényes paramétereket ad meg a kérelem törzsei között.

### <a name="create-a-transform"></a>Átalakítás létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha új feladatokat küld minden új videóhoz, akkor ezt a receptet a könyvtárában lévő összes videóra alkalmazza. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információt az [átalakításokkal és feladatokkal](./transforms-jobs-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

Egy új [átalakításpéldány](/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. 

> [!Note]
> [Átalakítások](/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás. Ez az oktatóanyag azt feltételezi, hogy az átalakítást egyedi névvel hozza létre.

1. A Poster alkalmazás bal oldali ablakában válassza a "Encoding and Analysis" (kódolás és elemzés) lehetőséget.
2. Ezután kattintson a „Create Transform” (Átalakítás létrehozása) elemre.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Feladat létrehozása

A [feladat](/rest/api/media/jobs) a tényleges kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a feladatok bemenete egy HTTPS URL-cím ("https: \/ /nimbuscdn-nimbuspm.streaming.Mediaservices.Windows.net/2b533311-b215-4409-80af-529c3e853622/") alapján történik.

1. A Poster alkalmazás bal oldali ablakában válassza a "Encoding and Analysis" (kódolás és elemzés) lehetőséget.
2. Ezután válassza a „Create or Update Job” (Feladat létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. A feladat előrehaladásának megtekintéséhez az Event Grid használatát javasoljuk. Ez egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

#### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

A kódolási feladatok befejezése után a következő lépés az, hogy a videó a kimeneti **eszközön** elérhető legyen az ügyfelek számára a lejátszáshoz. Ezt két lépésben teheti meg: először hozzon létre egy [StreamingLocatort](/rest/api/media/streaminglocators), majd a streamelési URL-címeket, amelyeket az ügyfelek használhatnak. 

Az adatfolyam-kereső létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az adatfolyam-kereső azonnal érvényes az API-hívások létrehozása után, és addig tart, amíg meg nem történik a törlés, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat. 

A [StreamingLocator](/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában a folyamatos átvitelű (vagy nem titkosított) tartalmakat fogja használni, ezért a rendszer az előre definiált "Predefined_ClearStreamingOnly" folyamatos adatátviteli szabályzatot használja.

> [!IMPORTANT]
> Egyéni [StreamingPolicy](/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. 

A Media Service-fiókhoz tartozik egy kvóta a **folyamatos átviteli szabályzat** bejegyzéseinek számára. Ne hozzon létre új **folyamatos átviteli szabályzatot** minden egyes adatfolyam-keresőhöz.

1. A Poster alkalmazás bal oldali ablakában válassza a "folyamatos átviteli házirendek és lokátorok" lehetőséget.
2. Ezután válassza az "adatfolyam-kereső létrehozása (Clear)" lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Elérési utak listázása és streamelési URL-ek létrehozása

#### <a name="list-paths"></a>Elérési utak listázása

Most, hogy létrejött a [folyamatos átviteli lokátor](/rest/api/media/streaminglocators) , letöltheti a streaming URL-címeket

1. A Poster alkalmazás bal oldali ablakában válassza a "streaming policies" lehetőséget.
2. Ezután válassza a „List Paths” (Elérési utak listázása) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **POST** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * A műveletnek nincs törzse:
        
4. Jegyezze fel a streameléshez használni kívánt elérési utak egyikét, amelyet a következő szakaszban fog használni. Ebben az esetben a következő elérési utak lettek visszaadva:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Streamelési URL-címek létrehozása

Ebben a szakaszban egy HLS-streamelési URL-címet hozunk létre. Az URL-címek a következő értékekből állnak:

1. A protokoll, amelyen keresztül a rendszer az adatokat küldi. Ebben az esetben „https”.

    > [!NOTE]
    > Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

2. A StreamingEndpoint gazdaneve. Ebben az esetben a név „amsaccount-usw22.streaming.media.azure.net”.

    Az állomásnév beszerzéséhez használhatja a következő GET műveletet:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    Győződjön meg arról, hogy a és a paramétereket úgy állítja be, `resourceGroupName` `accountName` hogy az megfeleljen a környezeti fájlnak. 
    
3. Elérési út, amelyet az előző (Elérési utak listázása) szakaszban feljegyzett.  

Ennek eredményeként a következő HLS URL jött létre

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése


> [!NOTE]
> Győződjön meg arról, hogy az adatfolyam- **végpont** , amelyről adatfolyamot szeretne továbbítani, fut.

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL:** mezőbe illessze be a létrehozott URL-t. 
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában törölni kell mindent, kivéve azokat az objektumokat, amelyeket Ön használni szeretne (általában újra kell használni az **átalakításokat**, és meg kell őriznie a **streaming-lokátorokat** stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  

Egy erőforrás törléséhez válassza a „Delete ...” (Törlés) műveletet a törölni kívánt erőforrás alatt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.  

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
