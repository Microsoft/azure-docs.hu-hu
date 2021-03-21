---
title: Ismerkedés a tartalom igény szerinti kézbesítésével a REST használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti az igény szerinti tartalom-továbbítási alkalmazás megvalósításának lépésein, Azure Media Services a REST API használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008875"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Ismerkedés a tartalom igény szerinti kézbesítésével a REST használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez a rövid útmutató végigvezeti egy igény szerinti video-továbbítási alkalmazás megvalósításának lépésein Azure Media Services (AMS) REST API-k használatával.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóanyag befejezésekor lehetősége van adatfolyamként továbbítani vagy fokozatosan letölteni a feltöltött, kódolt és letöltött minta médiafájlokat.

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

[![Diagram, amely a Azure Media Services Object adatmodell leggyakrabban használt objektumainak egy részét mutatja be a videók igény szerinti alkalmazásokban való fejlesztéséhez.](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek
A REST API-kkal való Media Services fejlesztésének megkezdéséhez a következő előfeltételek szükségesek.

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* A Media Services REST API fejlesztésének megismerése. További információ: [Media Services REST API áttekintése](media-services-rest-how-to-use.md).
* Az Ön által választott alkalmazás, amely HTTP-kéréseket és-válaszokat tud küldeni. Ez az oktatóanyag a [hegedűst](https://www.telerik.com/download/fiddler)használja.

Ebben a rövid útmutatóban a következő feladatok jelennek meg.

1. A streamvégpont elindítása (az Azure Portal használatával).
2. Kapcsolódjon a Media Services fiókhoz a REST API használatával.
3. Hozzon létre egy új adategységet, és töltsön fel egy videofájl REST API.
4. A forrásfájl kódolása adaptív sávszélességű MP4-fájlokba REST API.
5. Tegye közzé az adategységet, és szerezze be a streaming és a progresszív letöltési URL-címeket REST API.
6. Tartalom lejátszása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, például olyan lokátorokra vonatkozó házirendeket, amelyek hosszú ideig maradnak érvényben (nem feltöltési szabályzatok). További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

A cikkben használt AMS REST-entitásokkal kapcsolatos részletekért tekintse meg a [Azure Media Services REST API-referenciát](/rest/api/media/operations/azure-media-services-rest-api-reference). Lásd még: [Azure Media Services fogalmak](media-services-concepts.md).

>[!NOTE]
>A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont elindítása az Azure Portal használatával

Azure Media Services használatakor az egyik leggyakoribb forgatókönyv a videó továbbítása az adaptív sávszélességű streaming használatával. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/).
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére.
3. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Kapcsolódjon a Media Services fiókhoz a REST API

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Új eszköz létrehozása és videofájl feltöltése REST API

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az **eszköz** entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is).  A fájlok az objektumba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Az eszközök létrehozásakor szükséges értékek egyike az eszköz létrehozási beállításai. A **Options** tulajdonság egy enumerálási érték, amely leírja az eszköz által létrehozható titkosítási beállításokat. Érvényes érték az alábbi listából származó értékek egyike, nem a lista értékeinek kombinációja:

* **Nincs**  =  **0** – a rendszer nem használ titkosítást. Ha ezt a beállítást használja, a tartalmat a rendszer nem védi az átvitelben vagy a tárolás során.
    Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted**  =  **1** – titkosítja a tartalmakat helyileg AES-256 bites titkosítással, majd feltölti az Azure Storage-ba, ahol a tárolása titkosított állapotban van. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected**  =  **2** – ezt a lehetőséget akkor használja, ha olyan tartalmat tölt fel, amely már titkosítva van, és Common encryption vagy PlayReady DRM-mel védett (például a PlayReady DRM-mel védett Smooth streaming).
* **EnvelopeEncryptionProtected**  =  **4** – ezt a beállítást akkor használja, ha AES-titkosítással titkosított HLS tölt fel. A fájlokat kódolni és titkosítani kell az átalakító-kezelővel.

### <a name="create-an-asset"></a>Adategység létrehozása
Az eszköz a Media Servicesban található objektumok különböző típusaihoz vagy csoportjaihoz tartozó tároló, beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a kódolt feliratok fájljait. A REST APIban az adategység létrehozásához POST-kérést kell küldenie Media Services és az objektumra vonatkozó összes tulajdonságot a kérelem törzsében kell elhelyezni.

Az alábbi példa bemutatja, hogyan hozhat létre egy eszközt.

**HTTP-kérés**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 45

{"Name":"BigBuckBunny.mp4", "Options":"0"}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következőt adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT

{  
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny2.mp4",
   "Options":0,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

### <a name="create-an-assetfile"></a>AssetFile létrehozása
A [AssetFile](/rest/api/media/operations/assetfile) entitás egy blob-tárolóban tárolt videót vagy hangfájlt jelöl. Az adategységek mindig egy adott objektumhoz vannak társítva, és egy adott eszköz egy vagy több AssetFiles is tartalmazhat. A Media Services Encoder feladat meghiúsul, ha egy objektum nem egy blob-tárolóban lévő digitális fájllal van társítva.

Miután feltöltötte a digitális médiafájlt egy blob-tárolóba, az **egyesítési** HTTP-kérelem segítségével frissítheti a AssetFile a médiafájl információit használva (a témakör későbbi részében látható).

**HTTP-kérés**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 164

{  
   "IsEncrypted":"false",
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-válasz**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion":null,
   "EncryptionScheme":null,
   "IsEncrypted":false,
   "EncryptionKeyId":null,
   "InitializationVector":null,
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>A AccessPolicy létrehozása írási engedéllyel
A fájlok blob Storage-ba való feltöltése előtt állítsa be a hozzáférési házirend jogosultságait az eszközre való íráshoz. Ehhez HTTP-kérelmet kell küldenie a AccessPolicies entitás számára. Hozzon létre egy DurationInMinutes értéket a létrehozáskor, vagy ha a rendszer visszaküldi a 500 belső kiszolgálóhiba-hibaüzenetet. További információ a AccessPolicies: [AccessPolicy](/rest/api/media/operations/accesspolicy).

Az alábbi példa bemutatja, hogyan hozhat létre AccessPolicy:

**HTTP-kérés**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 74

{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 312
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
Server: Microsoft-IIS/8.5
request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:18:06 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "Created":"2015-01-18T22:18:06.6370575Z",
   "LastModified":"2015-01-18T22:18:06.6370575Z",
   "Name":"NewUploadPolicy",
   "DurationInMinutes":440.0,
   "Permissions":2
}
```

### <a name="get-the-upload-url"></a>A feltöltési URL-cím letöltése

A tényleges feltöltési URL-cím megszerzéséhez hozzon létre egy SAS-lokátort. A lokátorok határozzák meg a kapcsolati végpont kezdési idejét és típusát azon ügyfelek számára, akik egy adott eszköz fájljait szeretnék elérni. Több lokátor entitást is létrehozhat egy adott AccessPolicy és egy adategységhez a különböző ügyfelek kéréseinek és igényeinek kezeléséhez. Ezen lokátorok mindegyike a kezdő és a AccessPolicy DurationInMinutes értékét használja az URL-cím használatának időtartamának meghatározásához. További információ: [lokátor](/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

Vegye figyelembe a következőket:

* Egyszerre legfeljebb öt egyedi lokátor társítható egy adott eszközhöz. 
* Ha azonnal fel kell töltenie a fájlokat, az aktuális időpont előtt öt percre kell beállítania a kezdő időpontot. Ennek az az oka, hogy az ügyfélszámítógép és a Media Services között az óra változhat. Emellett a kezdő időpont értéke a következő DateTime formátumban kell, hogy legyen: éééé-hh-NNTÓÓ: PP: ssZ (például "2014-05-23T17:53:50Z").    
* A lokátor létrehozása után 30-40 másodperces késleltetés is lehetséges, ha a szolgáltatás elérhetővé válik. Ez a probléma a [sas URL-címére](../../storage/common/storage-sas-overview.md) és a forrás-lokátorokra is vonatkozik.

Az alábbi példa bemutatja, hogyan hozhat létre SAS URL-lokátort a kérelem törzsének Type tulajdonsága által meghatározott módon ("1" egy SAS-lokátorhoz, és "2" értéket egy igény szerinti Felderítőhöz). A visszaadott **path** tulajdonság tartalmazza azt az URL-címet, amelyet a fájl feltöltéséhez használnia kell.

**HTTP-kérés**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 178

{  
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Type":1
}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 949
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
Server: Microsoft-IIS/8.5
request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 03:01:29 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
   "ExpirationDateTime":"2015-02-19T00:05:53",
   "Type":1,
   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Name":null
}
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Fájl feltöltése blob Storage-tárolóba
Miután beállította a AccessPolicy és a lokátort, a rendszer feltölti a tényleges fájlt egy Azure Blob Storage-tárolóba az Azure Storage REST API-kon keresztül. A fájlokat fel kell töltenie blokk blobként. Azure Media Services nem támogatja az oldal blobokat.  

> [!NOTE]
> Fel kell vennie annak a fájlnak a fájlnevét, amelyet fel szeretne tölteni az előző szakaszban fogadott lokátor **path** értékre. Például: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

További információ az Azure Storage-Blobok használatáról: [blob Service REST API](/rest/api/storageservices/blob-service-rest-api).

### <a name="update-the-assetfile"></a>A AssetFile frissítése
Most, hogy feltöltötte a fájlt, frissítse a FileAsset méretét (és egyéb adatait). Például:

```console
MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net

{  
   "ContentFileSize":"1186540",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következőt adja vissza:

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>A lokátor és a AccessPolicy törlése
**HTTP-kérés**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következőt adja vissza:

```console
HTTP/1.1 204 No Content
...
```

**HTTP-kérés**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következőt adja vissza:

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>A forrásfájl kódolása adaptív sávszélességű MP4-fájlokba

Az eszközök Media Servicesba való betöltését követően az adathordozók kódolva, transmuxed, vízjellel és így tovább, az ügyfeleknek való kézbesítés előtt. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük, és minden feladat olyan atomi feladatokból áll, amelyek végrehajtják a tényleges munkát az adategységen (További információ: [feladat](/rest/api/media/operations/job), [feladat](/rest/api/media/operations/task) leírása).

Ahogy korábban is említettük, Azure Media Services az egyik leggyakoribb forgatókönyv esetén az adaptív sávszélességű adatfolyamot biztosít az ügyfeleknek. A Media Services az adaptív sávszélességű MP4-fájlok készletét az alábbi formátumokba tudja dinamikusan csomagolni: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A következő szakasz bemutatja, hogyan hozhat létre egy olyan feladatot, amely egy kódolási feladatot tartalmaz. A feladat azt adja meg, hogy a köztes fájlt átkódolja adaptív sávszélességű MP4 **Media Encoder standard** használatával. A szakasz azt is bemutatja, hogyan lehet figyelni a feladatok feldolgozási folyamatát. A feladatok elvégzése után létrehozhatók olyan lokátorok, amelyek szükségesek az eszközeihez való hozzáféréshez.

### <a name="get-a-media-processor"></a>Adathordozó-feldolgozó letöltése
Media Services a Media Processor egy olyan összetevő, amely egy adott feldolgozási feladatot kezel, például a kódolást, a konvertálást, a titkosítást vagy a médiatartalom visszafejtését. Az oktatóanyagban látható kódolási feladathoz a Media Encoder Standard fogjuk használni.

A következő kód a kódoló azonosítóját kéri le.

**HTTP-kérés**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-válasz**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 273
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 07:54:09 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```

### <a name="create-a-job"></a>Feladat létrehozása
Minden feladathoz egy vagy több feladat tartozhat, attól függően, hogy milyen típusú feldolgozást szeretne elvégezni. A REST APIon a feladatok és a hozzájuk kapcsolódó feladatok kétféleképpen hozhatók létre: a feladatok a feladat entitások navigációs tulajdonságában vagy a OData kötegelt feldolgozáson keresztül is meghatározhatók. A Media Services SDK batch-feldolgozást használ. A cikkben szereplő példák olvashatósága érdekében azonban a feladatok beágyazottnak vannak meghatározva. További információ a Batch-feldolgozásról: [Open adatprotokoll (OData) batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Az alábbi példa bemutatja, hogyan hozhat létre és küldhet el egy feladatot egy adott feladattal egy adott felbontás és minőség alapján. A következő dokumentációs szakasz tartalmazza a Media Encoder Standard processzor által támogatott összes [feladathoz tartozó beállításkészlet](./media-services-mes-presets-overview.md) listáját.  

**HTTP-kérés**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 482

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"Adaptive Streaming",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
            <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      }
   ]
}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1215
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
Server: Microsoft-IIS/8.5
request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:04:35 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
      },
      "Tasks":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
         }
      },
      "OutputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
         }
      },
      "InputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
         }
      },
      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "Name":"NewTestJob",
      "Created":"2015-01-19T08:04:34.3287057Z",
      "LastModified":"2015-01-19T08:04:34.3287057Z",
      "EndTime":null,
      "Priority":0,
      "RunningDuration":0,
      "StartTime":null,
      "State":0,
      "TemplateId":null,
      "JobNotificationSubscriptions":{  
         "__metadata":{  
            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
         },
         "results":[  

         ]
      }
   }
}
```

A feladatokra vonatkozó kérések néhány fontos Tudnivaló:

* A TaskBody tulajdonságainak literál XML-t kell használniuk a feladat által használt bemeneti vagy kimeneti eszközök definiálásához. A feladat cikke tartalmazza az XML-séma XML-sémájának definícióját.
* A TaskBody-definícióban minden belső értéknek `<inputAsset>` `<outputAsset>` JobInputAsset (Value) vagy JobOutputAsset (Value) értékűnek kell lennie.
* Egy feladat több kimeneti eszközzel is rendelkezhet. Egy JobOutputAsset (x) csak egyszer használható egy feladat tevékenységének kimenetében.
* A JobInputAsset vagy a JobOutputAsset megadható egy tevékenység bemeneti eszközeként.
* A feladatok nem lehetnek ciklusok.
* Az JobInputAsset vagy JobOutputAsset-re átadott Value paraméter az adott eszköz indexének értékét jelöli. A tényleges eszközök a InputMediaAssets és a OutputMediaAssets navigációs tulajdonságaiban vannak definiálva a feladatok entitásának definíciójában.

> [!NOTE]
> Mivel Media Services a OData v3-ra épül, a InputMediaAssets és a OutputMediaAssets navigációs tulajdonság gyűjteményében lévő egyedi eszközökre a "__metadata: URI" név-érték párok hivatkoznak.
>
>

* A InputMediaAssets egy vagy több olyan eszközre mutat, amelyet a Media Services hozott létre. A OutputMediaAssets a rendszer hozza létre. Nem hivatkoznak meglévő eszközre.
* A OutputMediaAssets a assetName attribútum használatával nevezhető el. Ha ez az attribútum nem található, akkor a OutputMediaAsset neve megegyezik az elem belső szöveges értékével, vagy a feladatnév értékének `<outputAsset>` vagy a feladattípus értékének (abban az esetben, ha nincs megadva a Name tulajdonság) utótagja. Ha például a assetName értéket állítja be a "Sample" értékre, akkor a OutputMediaAsset Name tulajdonság értéke "Sample". Ha azonban nem adott meg értéket a assetName számára, de a feladatot "NewJob" értékre állította be, akkor a OutputMediaAsset neve "JobOutputAsset (Value) _NewJob" lesz.

    Az alábbi példa bemutatja, hogyan állíthatja be a assetName attribútumot:

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* A feladatok láncolásának engedélyezése:

  * Egy feladatnak legalább két feladattal kell rendelkeznie
  * Legalább egy olyan feladatnak kell lennie, amelynek bemenete egy másik feladat kimenete a feladatban.

További információ: Encoding- [feladatok létrehozása a Media Services REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>A feldolgozási folyamat figyelése
A feladatok állapotát az állapot tulajdonsággal kérheti le az alábbi példában látható módon:

**HTTP-kérés**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 17
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/7.5
x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 13 May 2012 05:16:53 GMT

{"d":{"State":2}}
```

### <a name="cancel-a-job"></a>Feladat megszakítása
Media Services lehetővé teszi a futó feladatok megszakítását a CancelJob függvénnyel. Ez a hívás 400 hibakódot ad vissza, ha a feladat megszakítása, megszakítása, hibája vagy befejezése után megpróbál lemondani egy feladatot.

Az alábbi példa bemutatja, hogyan hívhatja meg a CancelJob.

**HTTP-kérés**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

Ha a művelet sikeres, a rendszer egy 204-es hibakódot ad vissza, és az üzenettörzs nélkül jelenik meg.

> [!NOTE]
> Meg kell adnia az URL-címet – a feladattípust (általában NB: JID: UUID: érték1), ha a paraméterként CancelJob.
>
>

### <a name="get-the-output-asset"></a>Kimeneti eszköz beolvasása
A következő kód bemutatja, hogyan kérhető le a kimeneti eszköz azonosítója.

**HTTP-kérés**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-válasz**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 445
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:28:13 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
   "value":[  
      {  
         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "State":0,
         "Created":"2015-01-19T07:52:15.603",
         "LastModified":"2015-01-19T07:52:15.603",
         "AlternateId":null,
         "Name":"Multibitrate MP4s",
         "Options":0,
         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "StorageAccountName":"storagetestaccount001"
      }
   ]
}
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Az objektum közzététele és a folyamatos letöltési URL-címek lekérése REST API

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez. 

A lokátorok létrehozása után létrehozhatja a fájlok továbbításához vagy letöltéséhez használt URL-címeket.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

Ez a szakasz bemutatja, hogyan végezheti el az eszközök közzétételéhez szükséges alábbi feladatokat.  

* A AccessPolicy létrehozása olvasási engedéllyel
* SAS URL-cím létrehozása tartalom letöltéséhez
* Forrás URL-cím létrehozása a streaming tartalomhoz

### <a name="creating-the-accesspolicy-with-read-permission"></a>A AccessPolicy létrehozása olvasási engedéllyel
A médiafájlok letöltése vagy továbbítása előtt először adjon meg egy olvasási engedéllyel rendelkező AccessPolicy, és hozza létre a megfelelő lokátor entitást, amely meghatározza az ügyfelek számára engedélyezni kívánt kézbesítési mechanizmus típusát. További információ az elérhető tulajdonságokról: AccessPolicy- [entitás tulajdonságai](/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Az alábbi példa bemutatja, hogyan határozhatja meg az adott eszköz olvasási engedélyeinek AccessPolicy.

```console
POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

Ha a művelet sikeres, a rendszer 201-as sikerességi kódot ad vissza, amely leírja a létrehozott AccessPolicy entitást. Ezután használja a AccessPolicy azonosítót és az eszköz azonosítóját, amely tartalmazza a kézbesíteni kívánt fájlt (például kimeneti eszközt) a lokátor entitás létrehozásához.

> [!NOTE]
> Ez az alapszintű munkafolyamat ugyanaz, mint amikor egy fájlt tölt fel egy eszköz betöltéséhez (ahogy azt a témakör korábbi részében tárgyalták). A fájlok feltöltésekor, ha Ön (vagy ügyfelei) azonnal el kell érnie a fájljait, állítsa a kezdő időpontot öt percre az aktuális időpont előtt. Ez a művelet azért szükséges, mert lehet, hogy az ügyfél és a Media Services között óra van elferdítve. A kezdő időpont értékének a következő DateTime formátumúnak kell lennie: éééé-hh-NNTÓÓ: PP: ssZ (például "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>SAS URL-cím létrehozása tartalom letöltéséhez
A következő kód bemutatja, hogyan kérhet le egy olyan URL-címet, amely a korábban létrehozott és feltöltött médiafájlok letöltésére használható. A AccessPolicy rendelkezik olvasási engedélyekkel, és a lokátor elérési útja egy SAS letöltési URL-címre hivatkozik.

```console
POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}
```

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1150
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
Server: Microsoft-IIS/7.5
x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:32 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
         }
      },
      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
      "ExpirationDateTime":"\/Date(1337049393000)\/",
      "Type":1,
      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "StartTime":"\/Date(1337031393000)\/"
   }
}
```

A visszaadott **elérési út** tulajdonság a sas URL-címet tartalmazza.

> [!NOTE]
> Ha letölti a tároló titkosított tartalmát, manuálisan kell visszafejtenie azt a megjelenítése előtt, vagy egy feldolgozási feladat tároló-visszafejtési MediaProcessor kell használnia a feldolgozott fájlok kimenetre való exportálásához a OutputAsset, majd le kell tölteni az adott eszközről. A feldolgozásról további információt a kódolási feladatok létrehozása a Media Services REST API című témakörben talál. Emellett a SAS URL-lokátorok nem frissíthetők a létrehozásuk után. Nem használhatja például ugyanazt a lokátort egy frissített kezdő értékkel. Ennek oka az SAS URL-címeinek létrehozása. Ha a lokátor lejárta után szeretné elérni egy eszköz letöltését, új kezdéssel létre kell hoznia egy újat.
>
>

### <a name="download-files"></a>Fájlok letöltése
Miután beállította a AccessPolicy és a lokátort, letöltheti a fájlokat az Azure Storage REST API-kon keresztül.  

> [!NOTE]
> Fel kell vennie annak a fájlnak a fájlnevét, amelyet le szeretne tölteni az előző szakaszban fogadott lokátor **path** értékre. Például `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4` ? . . .

További információ az Azure Storage-Blobok használatáról: [blob Service REST API](/rest/api/storageservices/blob-service-rest-api).

A korábban végrehajtott kódolási feladatoknak köszönhetően (az adaptív MP4-készletbe való kódolás) több MP4-fájl is van, amelyeket fokozatosan le is tölthet. Például:    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>Folyamatos átviteli URL-cím létrehozása streaming-tartalomhoz
A következő kód bemutatja, hogyan hozhat létre streaming URL-lokátort:

```console
POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}
```

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 981
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
Server: Microsoft-IIS/7.5
x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:39 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
         }
      },
      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
      "ExpirationDateTime":"\/Date(1337049395000)\/",
      "Type":2,
      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
      "StartTime":"\/Date(1337031395000)\/"
   }
}
```

Smooth Streaming eredetű URL-cím adatfolyamként való továbbításához a Path tulajdonságot a Smooth Streaming manifest-fájl nevével kell hozzáfűzni, amelyet a "/manifest" kifejezés követ.

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

A stream HLS a "/manifest" után fűzze hozzá a következőt: (Format = m3u8-AAPL).

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

Az MPEG DASH stream eléréséhez fűzze hozzá a (Format = mpd-Time-CSF) karakterláncot a "/manifest" után.

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>Tartalom lejátszása
A videótovábbításhoz használja az [Azure Media Services Player](https://aka.ms/azuremediaplayer) lejátszót.

A progresszív letöltés teszteléséhez illesszen be egy URL-címet egy böngészőbe (például IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]