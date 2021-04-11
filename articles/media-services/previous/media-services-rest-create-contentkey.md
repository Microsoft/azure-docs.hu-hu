---
title: Tartalom-kulcsok létrehozása REST használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhatók létre biztonságos hozzáférést biztosító tartalmi kulcsok az eszközökhöz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a9960ece05b7cab4ddd26fdac7516f4adbccf5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017256"
---
# <a name="create-content-keys-with-rest"></a>Tartalom-kulcsok létrehozása REST használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

A Media Services lehetővé teszi a titkosított eszközök továbbítását. A **ContentKey** biztonságos hozzáférést biztosít az **eszközhöz**. 

Új eszköz létrehozásakor (például a [fájlok feltöltése](media-services-rest-upload-files.md)előtt) a következő titkosítási beállításokat adhatja meg: **StorageEncrypted**, **CommonEncryptionProtected** vagy **EnvelopeEncryptionProtected**. 

Amikor eszközöket továbbít az ügyfeleknek, beállíthatja, [hogy az eszközök dinamikusan legyenek titkosítva](media-services-rest-configure-asset-delivery-policy.md) a következő két titkosítás egyikével: **DynamicEnvelopeEncryption** vagy **DynamicCommonEncryption**.

A titkosított eszközöket társítani kell a **ContentKey** s-hez. Ez a cikk a tartalmi kulcs létrehozását ismerteti.

A következő általános lépésekkel hozhat létre olyan tartalmi kulcsokat, amelyeket a titkosítani kívánt eszközökkel társít. 

1. Véletlenszerűen generált 16 bájtos AES-kulcsot (a közös és a borítékos titkosításhoz) vagy egy 32 bájtos AES-kulcsot (a tárolási titkosításhoz). 
   
    Ez az eszköz tartalmi kulcsa, ami azt jelenti, hogy az adott objektumhoz társított összes fájlnak ugyanazt a tartalmi kulcsot kell használnia a visszafejtés során. 
2. Hívja meg a [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) és a [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) metódust a megfelelő X. 509 tanúsítvány lekéréséhez, amelyet a tartalmi kulcs titkosításához kell használnia.
3. Titkosítsa a tartalmi kulcsot az X. 509 tanúsítvány nyilvános kulcsával. 
   
   Media Services .NET SDK az RSA-t használja a OAEP a titkosítás végrehajtásakor.  Egy példát láthat a [EncryptSymmetricKeyData függvényben](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Hozzon létre egy ellenőrzőösszeg-értéket (az PlayReady AES-kulcs ellenőrzőösszeg-algoritmusa alapján) a kulcs-azonosító és a tartalmi kulcs használatával kiszámítva. További információ: "PlayReady AES Key ellenőrzőösszeg-algoritmus" szakasz, [itt](https://www.microsoft.com/playready/documents/)található a PlayReady fejlécének objektumára vonatkozó dokumentum.
   
    A következő .NET-példa kiszámítja az ellenőrzőösszeget a kulcs azonosítójának GUID része és a tartalom törlése kulcs használatával.

    ```console
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
     {
       byte[] array = null;
         using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
         {
             aesCryptoServiceProvider.Mode = CipherMode.ECB;
             aesCryptoServiceProvider.Key = contentKey;
             aesCryptoServiceProvider.Padding = PaddingMode.None;
             ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
             array = new byte[16];
             cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
         }
         byte[] array2 = new byte[8];
         Array.Copy(array, array2, 8);
         return Convert.ToBase64String(array2);
     }
    ```

5. Hozza létre a **EncryptedContentKey** (a Base64 kódolású karakterlánccá konvertált), a **ProtectionKeyId**, a **ProtectionKeyType**, a **ContentKeyType** és az **ellenőrzőösszeg** értékeit, amelyeket az előző lépésekben kapott.
6. Társítsa a **ContentKey** entitást az **eszköz** entitásához a $Links művelettel.

Ez a cikk nem mutatja be az AES-kulcs létrehozását, a kulcs titkosítását és az ellenőrzőösszeg kiszámítását. 

> [!NOTE]
> 
> A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="retrieve-the-protectionkeyid"></a>A ProtectionKeyId beolvasása
Az alábbi példa bemutatja, hogyan kérheti le a ProtectionKeyId, a tanúsítvány ujjlenyomatát a tartalmi kulcs titkosításakor használandó tanúsítványhoz. Ezzel a lépéssel győződjön meg róla, hogy már rendelkezik a megfelelő tanúsítvánnyal a gépen.

Kérés:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
```

Válasz:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>A ProtectionKeyId ProtectionKey beolvasása
Az alábbi példa bemutatja, hogyan kérhető le az X. 509 tanúsítvány az előző lépésben kapott ProtectionKeyId használatával.

Kérés:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```


Válasz:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String", "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

## <a name="create-the-contentkey"></a>A ContentKey létrehozása
Miután lekérte az X. 509 tanúsítványt, és a nyilvános kulcsát használta a tartalmi kulcs titkosításához, hozzon létre egy **ContentKey** -entitást, és ennek megfelelően állítsa be a tulajdonság értékét.

A tartalmi kulcs létrehozásakor beállított értékek egyike a típus. Az alábbi értékek közül választhat:

```console
public enum ContentKeyType
{
    /// <summary>
    /// Specifies a content key for common encryption.
    /// </summary>
    /// <remarks>This is the default value.</remarks>
    CommonEncryption = 0,

    /// <summary>
    /// Specifies a content key for storage encryption.
    /// </summary>
    StorageEncryption = 1,

    /// <summary>
    /// Specifies a content key for configuration encryption.
    /// </summary>
    ConfigurationEncryption = 2,

    /// <summary>
    /// Specifies a content key for Envelope encryption.  Only used internally.
    /// </summary>
    EnvelopeEncryption = 4
    }
```

Az alábbi példa bemutatja, hogyan hozhat létre egy **ContentKey** egy **ContentKeyType** készlettel ("1") és a **ProtectionKeyType** "0" értékre állítva, hogy JELEZZE, hogy a védelmi kulcs azonosítója az X. 509 tanúsítvány ujjlenyomata.  

Kérés

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Válasz:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="associate-the-contentkey-with-an-asset"></a>A ContentKey hozzárendelése egy eszközhöz
A ContentKey létrehozása után társítsa azt az eszközhöz a $links művelettel, az alábbi példában látható módon:

Kérés:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Válasz:

```console
HTTP/1.1 204 No Content 
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
