---
title: Fájlok feltöltése egy Azure Media Services-fiókba REST használatával | Microsoft Docs
description: Megtudhatja, hogyan szerezhet be médiatartalmakat a Media Servicesba az eszközök REST használatával történő létrehozásával és feltöltésével.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012938"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése Media Services-fiókba a REST használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az [eszköz](/rest/api/media/operations/asset) entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is).  A fájlok az objektumba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez. 

Ebből az oktatóanyagból megtudhatja, hogyan tölthet fel egy fájlt és egyéb műveletet a hozzá társított módon:

> [!div class="checklist"]
> * A Poster beállítása az összes feltöltési művelethez
> * Kapcsolódás a Media Services szolgáltatáshoz 
> * Hozzáférési szabályzat létrehozása írási engedéllyel
> * Adategység létrehozása
> * SAS-lokátor létrehozása és a feltöltési URL-cím létrehozása
> * Fájl feltöltése a blob Storage-ba a feltöltési URL-cím használatával
> * Metaadatok létrehozása az eszközön a feltöltött médiafájlhoz

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Hozzon létre egy Azure Media Services fiókot a Azure Portal használatával](media-services-portal-create-account.md).
- Tekintse át a [hozzáférés Azure Media Services API-t a HRE-hitelesítés áttekintésével foglalkozó](media-services-use-aad-auth-to-access-ams-api.md) cikkben.
- További információkért tekintse át az [Azure ad-hitelesítés használata a Media Services API Rest-mel való elérését](./media-services-rest-connect-with-aad.md) ismertető cikket.
- Konfigurálja a **Poster** -t a következő témakörben leírtak szerint: [Media Services REST API hívások](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Megfontolandó szempontok

Media Services REST API használatakor a következő szempontokat kell figyelembe venni:
 
* Ha Media Services REST API használó entitásokhoz fér hozzá, meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md). <br/>Az oktatóanyagban használt Poster-gyűjtemény gondoskodik az összes szükséges fejléc beállításáról.
* A Media Services a IAssetFile.Name tulajdonság értékét használja a streaming tartalom URL-címeinek létrehozásakor (például http://{AMSAccount}. Origin. Mediaservices. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Emiatt a százalékos kódolás nem engedélyezett. A **Name (név** ) tulajdonság értéke nem lehet a következő [százalék-kódolásra fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)egyike:! * ' ();: @ &= + $,/?% # [] ". Emellett a fájlnévkiterjesztés csak egy "." lehet.
* A név hossza nem lehet nagyobb, mint 260 karakter.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.

## <a name="set-up-postman"></a>A Postman beállítása

Az oktatóanyaghoz tartozó Poster beállításának lépéseit lásd: [Poster konfigurálása](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

1. Adja hozzá a környezethez a kapcsolódási értékeket. 

    A [gyűjteményben](postman-collection.md)definiált műveletek végrehajtásának megkezdése előtt manuálisan kell beállítani a **MediaServices** - [környezet](postman-environment.md) részét képező változókat.

    Az első öt változó értékének beolvasásához tekintse meg [a Azure Media Services API Azure ad-hitelesítéssel való elérését](media-services-use-aad-auth-to-access-ams-api.md)ismertető témakört. 

    ![Képernyőkép, amely a jobb felső sarokban kiválasztott "fogaskerék" ikont és a "felügyeleti környezetek" lapról kiválasztott első öt változót jeleníti meg.](./media/media-services-rest-upload-files/postman-import-env.png)
2. A **MediaFileName** környezeti változó értékének megadása.

    Adja meg a feltölteni kívánt média fájlnevét. Ebben a példában a BigBuckBunny.mp4 fogja feltölteni. 
3. Vizsgálja meg a fájl **AzureMediaServices.postman_environment.jsét** . Látni fogja, hogy a gyűjtemény csaknem minden művelete "teszt" szkriptet hajt végre. A szkriptek a válasz által visszaadott értékeket és a megfelelő környezeti változókat határozzák meg.

    Például az első művelet beolvas egy hozzáférési jogkivonatot, és beállítja azt a **AccessToken** környezeti változón, amely minden más műveletben használatos.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. A **Poster** ablak bal oldalán kattintson az **1 gombra. HRE-hitelesítési jogkivonat** beszerzése  ->  **Azure ad-jogkivonat beszerzése az egyszerű szolgáltatásnév számára**.

    Az URL-cím része a **AzureADSTSEndpoint** környezeti változóval van kitöltve (az oktatóanyag korábbi részében a gyűjteményt támogató környezeti változók értékeit adja meg).

    ![Képernyőkép, amely az "1" kifejezést mutatja. Egy D hitelesítési jogkivonat beszerzése – Azure-beli D-token beszerzése a (z) "Poster" ablakból kiválasztva, a "Küldés" gomb kiválasztásával.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kattintson a **Küldés** gombra.

    Láthatja a "access_token" kifejezést tartalmazó választ. A "teszt" szkript ezt az értéket veszi át, és beállítja a **AccessToken** környezeti változót (a fent leírtak szerint). Ha megvizsgálja a környezeti változókat, láthatja, hogy ez a változó már tartalmazza a művelet többi részében használt hozzáférési jogkivonat (tulajdonosi jogkivonat) értékét. 

    Ha a jogkivonat lejár, ugorjon az "Azure AD-jogkivonat beszerzése az egyszerű szolgáltatásnév számára" lépésre. 

## <a name="create-an-access-policy-with-write-permission"></a>Hozzáférési szabályzat létrehozása írási engedéllyel

### <a name="overview"></a>Áttekintés 

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

A fájlok blob Storage-ba való feltöltése előtt állítsa be a hozzáférési házirend jogosultságait az eszközre való íráshoz. Ehhez HTTP-kérelmet kell küldenie a AccessPolicies entitás számára. Hozzon létre egy DurationInMinutes értéket a létrehozáskor, vagy ha a rendszer visszaküldi a 500 belső kiszolgálóhiba-hibaüzenetet. További információ a AccessPolicies: [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Hozzáférési szabályzat létrehozása

1. Válassza   ->  **a AccessPolicy létrehozás AccessPolicy lehetőséget a feltöltéshez**.
2. Kattintson a **Küldés** gombra.

    ![Képernyőkép, amely a bal oldali menüben a "AccessPolicy-Create AccessPolicy for upload" (feltöltés) elemet jeleníti meg, és a "Küldés" gomb van kiválasztva.](./media/media-services-rest-upload-files/postman-access-policy.png)

    A "teszt" szkript lekéri a AccessPolicy azonosítóját, és beállítja a megfelelő környezeti változót.

## <a name="create-an-asset"></a>Adategység létrehozása

### <a name="overview"></a>Áttekintés

Az [eszköz](/rest/api/media/operations/asset) a Media Servicesban található objektumok különböző típusaihoz vagy csoportjaihoz tartozó tároló, beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a kódolt feliratok fájljait. A REST APIban az adategység létrehozásához POST-kérést kell küldenie Media Services és az objektumra vonatkozó összes tulajdonságot a kérelem törzsében kell elhelyezni.

Az adategységek létrehozásakor felvehető tulajdonságok egyike a **Beállítások**. A következő titkosítási beállítások egyikét megadhatja: **none** (alapértelmezett, nem használt titkosítás), **StorageEncrypted** (az ügyféloldali tárolási titkosítással előre titkosított tartalomhoz), a **CommonEncryptionProtected** vagy a **EnvelopeEncryptionProtected**. Ha titkosított eszközzel rendelkezik, konfigurálnia kell egy kézbesítési házirendet. További információ: az [eszközök kézbesítési házirendjeinek konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

Ha az eszköz titkosítva van, létre kell hoznia egy **ContentKey** , és csatolnia kell az eszközhöz a következő cikkben leírtak szerint: [ContentKey létrehozása](media-services-rest-create-contentkey.md). Miután feltöltötte a fájlokat az objektumba, frissítenie kell a **AssetFile** entitás titkosítási tulajdonságait az **eszköz** titkosítása során kapott értékekkel. Ezt az **egyesítési** HTTP-kérelem használatával teheti meg. 

Ebben a példában egy titkosítatlan eszközt hozunk létre. 

### <a name="create-an-asset"></a>Adategység létrehozása

1. Válassza az **eszközök** eszköz  ->  **létrehozása** lehetőséget.
2. Kattintson a **Küldés** gombra.

    ![Képernyőfelvétel: a "gyűjtemények" menüből kiválasztott "eszközök – létrehozási egység", valamint a "Küldés" gomb kiválasztásának megjelenítése.](./media/media-services-rest-upload-files/postman-create-asset.png)

    A "teszt" szkript lekéri az eszköz azonosítóját, és beállítja a megfelelő környezeti változót.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>SAS-lokátor létrehozása és a feltöltési URL-cím létrehozása

### <a name="overview"></a>Áttekintés

Miután beállította a AccessPolicy és a lokátort, a rendszer feltölti a tényleges fájlt egy Azure Blob Storage-tárolóba az Azure Storage REST API-kon keresztül. A fájlokat fel kell töltenie blokk blobként. Azure Media Services nem támogatja az oldal blobokat.  

További információ az Azure Storage-Blobok használatáról: [blob Service REST API](/rest/api/storageservices/blob-service-rest-api).

A tényleges feltöltési URL-cím megszerzéséhez hozzon létre egy SAS-lokátort (lásd alább). A lokátorok határozzák meg a kapcsolati végpont kezdési idejét és típusát azon ügyfelek számára, akik egy adott eszköz fájljait szeretnék elérni. Több lokátor entitást is létrehozhat egy adott AccessPolicy és egy adategységhez a különböző ügyfelek kéréseinek és igényeinek kezeléséhez. Ezen lokátorok mindegyike a kezdő és a AccessPolicy DurationInMinutes értékét használja az URL-cím használatának időtartamának meghatározásához. További információ: [lokátor](/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következőket:

* Egyszerre legfeljebb öt egyedi lokátor társítható egy adott eszközhöz. További információ: lokátor.
* Ha azonnal fel kell töltenie a fájlokat, az aktuális időpont előtt öt percre kell beállítania a kezdő időpontot. Ennek az az oka, hogy az ügyfélszámítógép és a Media Services között az óra változhat. Emellett a kezdő időpont értéke a következő DateTime formátumban kell, hogy legyen: éééé-hh-NNTÓÓ: PP: ssZ (például "2014-05-23T17:53:50Z").    
* A lokátor létrehozása után 30-40 másodperces késleltetés is lehetséges, ha a szolgáltatás elérhetővé válik.

### <a name="create-a-sas-locator"></a>SAS-lokátor létrehozása

1. Válassza a **lokátor**  ->  **sas-lokátor létrehozása** lehetőséget.
2. Kattintson a **Küldés** gombra.

    A "teszt" szkript létrehozza a "feltöltési URL-címet" a megadott médiafájl neve és az SAS-lokátor adatai alapján, és beállítja a megfelelő környezeti változót.

    ![Képernyőkép: a "gyűjtemények" menüből kiválasztott "lokátor – s" lokátor létrehozása ", és a" Küldés "gomb van kiválasztva.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob Storage-ba a feltöltési URL-cím használatával

### <a name="overview"></a>Áttekintés

Most, hogy már rendelkezik a feltöltési URL-címmel, írnia kell egy kódot az Azure Blob API-k használatával közvetlenül a fájlnak a SAS-tárolóba való feltöltéséhez. További információért tekintse át a következő cikkeket:

- [Az Azure Storage REST API használata](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB elhelyezése](/rest/api/storageservices/put-blob)
- [Blobok feltöltése blob Storage-ba](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Poster-fájl feltöltése

Példaként használjuk a Poster-t egy kis. mp4-fájl feltöltésére. A bináris fájlok feltöltése a Poster-on keresztül lehetséges.

A feltöltési kérelem nem része a **AzureMedia** gyűjteménynek. 

Új kérelem létrehozása és beállítása:
1. Nyomja meg **+** az gombot egy új kérelem lap létrehozásához.
2. Az URL-címben válassza a **put** művelet és a **{{UploadURL}}** beillesztése lehetőséget.
2. Hagyja változatlanul az **Engedélyezés** lapot (ne állítsa a **tulajdonosi jogkivonatra**).
3. A **fejlécek** lapon adja meg a következőt: **kulcs**: "x-MS-blob-type" és **Value**: "BlockBlob".
2. A **törzs** lapon kattintson a **bináris** elemre.
4. Válassza ki a **MediaFileName** környezeti változóban megadott nevű fájlt.
5. Kattintson a **Küldés** gombra.

    ![Képernyőkép, amely megjeleníti a "(UploadU R L)" lapot.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Metaadatok létrehozása az eszközben

A fájl feltöltése után létre kell hoznia egy metaadatokat az adategységben az eszközhöz társított blob Storage-ba feltöltött médiafájlhoz.

1. Válassza ki a **AssetFiles**  ->  **CreateFileInfos**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-file-info.png)

A fájlt fel kell tölteni, és hozzá kell adni a metaadatokat.

## <a name="validate"></a>Érvényesítés

Annak ellenőrzéséhez, hogy a fájl feltöltése sikeresen megtörtént-e, érdemes lehet lekérdezni a [AssetFile](/rest/api/media/operations/assetfile) , és össze kell hasonlítani a **ContentFileSize** (vagy más részleteit) az új objektumban látható értékkel. 

A következő **beolvasási** művelet például az adatfájlhoz tartozó adatfájlok (vagy a BigBuckBunny.mp4 fájl) adatait hozza meg. A lekérdezés a korábban beállított [környezeti változókat](postman-environment.md) használja.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

A válasz a méretet, a nevet és az egyéb adatokat fogja tartalmazni.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Következő lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
