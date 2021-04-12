---
title: Az eszközök kézbesítési házirendjeinek konfigurálása Media Services REST API használatával | Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhat különböző eszköz-kézbesítési házirendeket Media Services REST API használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017324"
---
# <a name="configuring-asset-delivery-policies"></a>Az eszközök kézbesítési házirendjeinek konfigurálása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Ha dinamikusan titkosított eszközök továbbítását tervezi, a Media Services Content Delivery munkafolyamat egyik lépése az eszközök kézbesítési házirendjeinek konfigurálása. Az eszköz kézbesítési házirendje közli Media Services, hogyan szeretné kézbesíteni az eszközét: az adatstream protokollnak (például MPEG DASH, HLS, Smooth Streaming vagy all) az eszközét dinamikusan kell titkosítania, függetlenül attól, hogy szeretné-e dinamikusan titkosítani az eszközt és a (boríték vagy közös titkosítás).

Ez a témakör azt ismerteti, hogy miért és hogyan kell létrehozni és konfigurálni az eszközök kézbesítési házirendjeit.

> [!NOTE]
> Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
> Továbbá a dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4 vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

Különböző házirendeket alkalmazhat ugyanarra az objektumra. Például alkalmazhat PlayReady titkosítást Smooth Streaming és AES-borítékos titkosításra az MPEG DASH és a HLS számára. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési házirendjét. Az eszköz adatfolyamként való továbbítása előtt a streaming kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirenddel továbbítja a tartalmat. Ha például az eszköz titkosítását Advanced Encryption Standard (AES) boríték titkosítási kulccsal szeretné továbbítani, állítsa a szabályzat típusát **DynamicEnvelopeEncryption** értékre. A tároló titkosításának eltávolításához és az objektum kiürítésének törléséhez állítsa a házirend típusát **NoDynamicEncryption** értékre. Példák, amelyek bemutatják, hogyan konfigurálhatja ezeket a házirend-típusokat.

Attól függően, hogy az eszköz kézbesítési házirendjét hogyan konfigurálja, akkor a dinamikusan, dinamikusan titkosíthatja és továbbíthatja a következő folyamatos átviteli protokollokat: Smooth Streaming, HLS, MPEG DASH streamek.

A következő lista a zökkenőmentes, HLS és KÖTŐJELek továbbításához használt formátumokat mutatja be.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)


További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="considerations"></a>Megfontolandó szempontok
* Az objektumhoz társított AssetDeliveryPolicy nem törölhető, miközben az adott eszközhöz OnDemand (streaming) lokátor található. Javasoljuk, hogy a szabályzat törlése előtt távolítsa el a szabályzatot az eszközről.
* Nem hozható létre adatfolyam-kereső egy titkosított eszközön, ha nincs beállítva eszköz kézbesítési házirendje.  Ha az eszköz nem titkosítja a tárolót, a rendszer lehetővé teszi, hogy egy lokátort hozzon létre, és az objektumot a törlés nélkül továbbítsa az eszköz kézbesítési házirendjébe.
* Egyetlen objektumhoz több eszközre vonatkozó kézbesítési szabályzat is tartozhat, de csak egyetlen módszert adhat meg egy adott AssetDeliveryProtocol kezeléséhez.  Ez azt jelenti, hogy ha két kézbesítési házirendet próbál meg összekapcsolni, amely megadja a AssetDeliveryProtocol. SmoothStreaming protokollt, amely hibát eredményez, mivel a rendszer nem tudja, melyikre lesz szükség, amikor egy ügyfél Smooth Streaming kérelmet tesz.
* Ha rendelkezik egy meglévő streaming-keresővel, nem kapcsolhat új szabályzatot az objektumhoz, leválaszthatja a meglévő szabályzatot az eszközről, vagy frissítheti az objektumhoz társított kézbesítési szabályzatot.  Először el kell távolítania az adatfolyam-keresőt, módosítania kell a házirendeket, majd újra létre kell hoznia a folyamatos átviteli lokátort.  Ugyanazt a locatorId használhatja, amikor újra létrehozza a folyamatos átviteli lokátort, de gondoskodnia kell arról, hogy ne okozzon problémát az ügyfelek számára, mivel a tartalmat a forrás vagy egy alárendelt CDN gyorsítótárazhatja.

> [!NOTE]
> 
> A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Eszköz kézbesítési házirendjének törlése
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Eszköz kézbesítési szabályzatának létrehozása
A következő HTTP-kérelem létrehoz egy eszköz-kézbesítési házirendet, amely megadja, hogy ne alkalmazza a dinamikus titkosítást, és az adatfolyamot a következő protokollok valamelyikében adja meg: MPEG DASH, HLS és Smooth Streaming protokollok. 

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .   

Kérés:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Válasz:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Eszköz csatolása az eszköz kézbesítési házirendjével
A következő HTTP-kérelem összekapcsolja a megadott eszközt az eszköz kézbesítési házirendjével.

Kérés:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Válasz:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption-eszköz kézbesítési szabályzata
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption-típus tartalmi kulcsának létrehozása és csatolása az eszközhöz
Az DynamicEnvelopeEncryption kézbesítési házirend megadásakor ügyeljen arra, hogy az eszközt a EnvelopeEncryption típusú tartalom-kulcshoz kapcsolja. További információkért lásd: [tartalmi kulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Kézbesítési URL-cím lekérése
Szerezze be az előző lépésben létrehozott tartalmi kulcs megadott kézbesítési módjának kézbesítési URL-címét. Az ügyfél a visszaadott URL-cím használatával igényel AES-kulcsot vagy PlayReady-licencet a védett tartalom lejátszásához.

Adja meg a HTTP-kérelem törzsében beolvasni kívánt URL-cím típusát. Ha a tartalmat a PlayReady-mel védi, kérjen egy Media Services PlayReady licenc-beszerzési URL-címet a keyDeliveryType: {"keyDeliveryType": 1}. Ha védi a tartalmat a boríték titkosításával, kérjen meg egy kulcs-beszerzési URL-címet a 2 érték megadásával a következő keyDeliveryType: {"keyDeliveryType": 2}.

Kérés:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Válasz:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Eszköz kézbesítési szabályzatának létrehozása
A következő HTTP-kérelem létrehozza azt a **AssetDeliveryPolicy** , amely a dinamikus Burkológörbe titkosítás (**DynamicEnvelopeEncryption**) a **HLS** protokollra való alkalmazására van konfigurálva (ebben a példában más protokollok is le lesznek tiltva a streamingtől). 

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .   

Kérés:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Válasz:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Eszköz csatolása az eszköz kézbesítési házirendjével
Lásd: [az eszköz csatolása az eszköz kézbesítési házirendjével](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption-eszköz kézbesítési szabályzata
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption-típus tartalmi kulcsának létrehozása és csatolása az eszközhöz
Az DynamicCommonEncryption kézbesítési házirend megadásakor ügyeljen arra, hogy az eszközt a CommonEncryption típusú tartalom-kulcshoz kapcsolja. További információkért lásd: [tartalmi kulcs létrehozása](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Kézbesítési URL-cím lekérése
Szerezze be az előző lépésben létrehozott PlayReady kézbesítési módszer kézbesítési URL-címét. Az ügyfél a visszaadott URL-cím használatával PlayReady-licencet kér le a védett tartalom lejátszásához. További információ: [kézbesítési URL-cím beolvasása](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Eszköz kézbesítési szabályzatának létrehozása
A következő HTTP-kérelem létrehozza azt a **AssetDeliveryPolicy** , amely a dinamikus közös titkosítás (**DynamicCommonEncryption**) alkalmazására van konfigurálva a **Smooth streaming** protokollra (ebben a példában a többi protokoll le lesz tiltva a streamingtől). 

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .   

Kérés:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Ha a tartalmat a Widevine DRM használatával szeretné védelemmel ellátni, frissítse a AssetDeliveryConfiguration-értékeket a WidevineLicenseAcquisitionUrl használatára (amelynek értéke 7), és adja meg a licenc kézbesítési szolgáltatásának URL-címét. Az alábbi AMS-partnerek segítségével Widevine-licenceket biztosíthat: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Például: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> A Widevine-mel történő titkosításkor csak a kötőjel használatával lehet kézbesíteni. Ügyeljen arra, hogy az eszköz kézbesítési protokolljában a kötőjel (2) legyen megadva.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Eszköz csatolása az eszköz kézbesítési házirendjével
Lásd: [az eszköz csatolása az eszköz kézbesítési házirendjével](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>A AssetDeliveryPolicy definiálásához használt típusok

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Az alábbi felsorolás az eszköz kézbesítési protokolljában beállítható értékeket ismerteti.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Az alábbi felsorolás az eszköz kézbesítési házirendjének típusához beállítható értékeket ismerteti.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

A következő felsorolás azokat az értékeket ismerteti, amelyekkel konfigurálhatja a tartalmi kulcs kézbesítési módszerét az ügyfélre.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Az alábbi felsorolás azokat az értékeket írja le, amelyekkel konfigurálhatja az eszközök kézbesítési házirendjének adott konfigurációjának beolvasásához használt kulcsokat.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

