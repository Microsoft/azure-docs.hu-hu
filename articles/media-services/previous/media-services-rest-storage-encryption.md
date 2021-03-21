---
title: A tartalom titkosítása a Storage encryption használatával AMS REST API
description: Megtudhatja, hogyan titkosíthatja a tartalmat a Storage encryption használatával az AMS REST API-kkal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015471"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>A tartalom titkosítása a Storage encryption szolgáltatással

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > nem kerül be új funkciók vagy funkciók a Media Services v2-be. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Ez a cikk áttekintést nyújt az AMS Storage-titkosításról, és bemutatja, hogyan tölthetők fel a Storage titkosított tartalma:

* Hozzon létre egy tartalomkulcsot.
* Hozzon létre egy eszközt. Állítsa be a AssetCreationOption StorageEncryption az eszköz létrehozásakor.
  
     A titkosított eszközök a tartalmi kulcsokkal vannak társítva.
* A tartalmi kulcs összekapcsolása az objektummal.  
* Állítsa be a titkosítással kapcsolatos paramétereket a AssetFile entitásokra.

## <a name="considerations"></a>Megfontolandó szempontok 

Ha titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési házirendjét. Az eszköz adatfolyamként való továbbítása előtt a streaming kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirenddel továbbítja a tartalmat. További információ: az [eszközök kézbesítési házirendjeinek konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Tárolási oldal titkosítása

|Titkosítási beállítás|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services Storage-titkosítás|AES-256 titkosítás, Media Services által felügyelt kulcs|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Inaktív adatok Storage Service Encryption](../../storage/common/storage-service-encryption.md)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, amelyet az Azure vagy az ügyfél felügyel|Támogatott|Támogatott|
|[Storage Client-Side titkosítás](../../storage/common/storage-client-side-encryption.md)|Az Azure Storage által kínált ügyféloldali titkosítás, amelyet az ügyfél felügyel Key Vault|Nem támogatott|Nem támogatott|

<sup>1</sup> míg Media Services támogatja a tartalom törlését, illetve titkosítás nélkül, nem ajánlott.

<sup>2</sup> Media Services v3-as verzióban a Storage encryption (AES-256 encryption) csak akkor támogatott a visszamenőleges kompatibilitás érdekében, ha az eszközök Media Services v2-mel lettek létrehozva. A v3 azt jelenti, hogy a meglévő tárolók titkosított eszközeivel működik együtt, de nem engedélyezi újak létrehozását.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>A Storage-titkosítás áttekintése
Az AMS tároló titkosítása **AES-CTR** módú titkosítást alkalmaz a teljes fájlra.  Az AES-CTR mód egy blokkos rejtjel, amely tetszőleges hosszúságú, kitöltés nélküli adatátvitelt képes titkosítani. Úgy működik, hogy egy számláló-blokkot titkosít egy AES algoritmussal, majd az AES kimenetét a titkosításhoz vagy visszafejtéshez szükséges adatokat kizáróan végzi.  A használt számláló-blokkot úgy kell létrehozni, hogy a számláló értékének 0 és 7 közötti InitializationVector másolja, és a számláló értékének 8 és 15 közötti értéke nulla. A 16 bájtos számláló, a 8 – 15. bájt (azaz a legkevésbé jelentős bájtok) egy egyszerű, 64 bites előjel nélküli egész szám, amely eggyel nő a feldolgozott adatblokkok esetében, és a hálózati bájtok sorrendje szerint van tárolva. Ha ez az egész szám eléri a maximális értéket (0xFFFFFFFFFFFFFFFF), akkor a növekményes érték nullára (8 – 15 bájt) visszaállítja a blokk számlálóját anélkül, hogy ez hatással lenne a számláló többi 64-es bitje (azaz 0 és 7 között).   Az AES-CTR módú titkosítás biztonságának fenntartása érdekében az egyes InitializationVector megadott kulcs azonosítójának értékének az egyes fájlokhoz egyedinek kell lennie, és a fájlokhoz nem lehet kevesebb, mint 2 ^ 64 blokk.  Ez az egyedi érték annak biztosítására szolgál, hogy a számláló értékét soha ne használják újra egy adott kulccsal. A CTR-móddal kapcsolatos további információkért tekintse meg [ezt a wiki-oldalt](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (a wiki-cikk az "alkalom" kifejezést használja "InitializationVector" helyett).

A **Storage encryption** használatával Titkosítsa a tartalmakat helyileg AES-256 bites titkosítással, majd töltse fel azt az Azure Storage-ba, ahol a tárolása titkosított állapotban van. A Storage encryption szolgáltatással védett eszközök titkosítása automatikusan titkosítva történik, és a kódolás előtt titkosított fájlrendszerbe kerül, és szükség esetén újra titkosítva lesz, mielőtt új kimeneti eszközként feltölti őket. A tárolás titkosításának elsődleges használati esete, ha a magas színvonalú bemeneti médiafájlokat erős titkosítással szeretné biztonságossá tenni a lemezen.

A titkosított adategységek kézbesítéséhez konfigurálnia kell az eszköz kézbesítési házirendjét, hogy Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz adatfolyamként való továbbítása előtt a streaming kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirenddel (például AES, Common encryption vagy nincs titkosítás) továbbítja a tartalmat.

## <a name="create-contentkeys-used-for-encryption"></a>Titkosításhoz használt Tartalomkulcsok létrehozása
A titkosított eszközök a Storage titkosítási kulcsaihoz vannak társítva. Hozza létre az adategység fájljainak létrehozása előtt a titkosításhoz használandó tartalmi kulcsot. Ez a szakasz a tartalmi kulcs létrehozását ismerteti.

A következő általános lépésekkel hozhat létre olyan tartalmi kulcsokat, amelyeket a titkosítani kívánt eszközökkel társít. 

1. A tárolási titkosításhoz véletlenszerűen állítson elő egy 32 bájtos AES-kulcsot. 
   
    Az 32 bájtos AES-kulcs az eszközhöz tartozó tartalmi kulcs, ami azt jelenti, hogy az adott eszközhöz társított összes fájlnak ugyanazt a tartalmi kulcsot kell használnia a visszafejtés során. 
2. Hívja meg a [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) és a [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) metódust a megfelelő X. 509 tanúsítvány lekéréséhez, amelyet a tartalmi kulcs titkosításához kell használnia.
3. Titkosítsa a tartalmi kulcsot az X. 509 tanúsítvány nyilvános kulcsával. 
   
   Media Services .NET SDK az RSA-t használja a OAEP a titkosítás végrehajtásakor.  A .NET-példákat a [EncryptSymmetricKeyData függvényben](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)tekintheti meg.
4. Hozzon létre egy ellenőrzőösszeg-értéket a kulcs azonosítója és a tartalmi kulcs alapján. A következő .NET-példa kiszámítja az ellenőrzőösszeget a kulcs azonosítójának GUID része és a tartalom törlése kulcs használatával.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Hozza létre a **EncryptedContentKey** (a Base64 kódolású karakterlánccá konvertált), a **ProtectionKeyId**, a **ProtectionKeyType**, a **ContentKeyType** és az **ellenőrzőösszeg** értékeit, amelyeket az előző lépésekben kapott.

    A tárolási titkosításhoz a következő tulajdonságokat kell tartalmaznia a kérés törzsében.

    Kérelem törzsének tulajdonsága    | Description
    ---|---
    Id | A ContentKey azonosítója a következő formátumban jön létre: "NB: Kid: UUID: \<NEW GUID> ".
    ContentKeyType | A tartalmi kulcs típusa egy egész szám, amely meghatározza a kulcsot. A tárolási titkosítási formátum értéke 1.
    EncryptedContentKey | Létrehozunk egy új, 256 bites (32 bájt) értékű tartalmi kulcs értéket. A kulcs titkosítása a Microsoft Azure Media Services által lekért Storage encryption X. 509 tanúsítvánnyal történik a GetProtectionKeyId és a GetProtectionKey metódusok HTTP GET kérelmének végrehajtásával. Példaként tekintse meg a következő .NET-kódot: az [itt](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)definiált **EncryptSymmetricKeyData** metódus.
    ProtectionKeyId | Ez a védelmi kulcs azonosítója a tartalmi kulcs titkosításához használt Storage encryption X. 509 tanúsítványhoz.
    ProtectionKeyType | A tartalom kulcsának titkosításához használt védelmi kulcs titkosítási típusa. Ez az érték StorageEncryption (1) a példánkban.
    Ellenőrzőösszeg |A tartalmi kulcs MD5 kiszámított ellenőrzőösszege. A rendszer a tartalom AZONOSÍTÓjának a tartalmi kulccsal való titkosításával számítja ki. A példában szereplő kód azt mutatja be, hogyan számítható ki az ellenőrzőösszeg.


### <a name="retrieve-the-protectionkeyid"></a>A ProtectionKeyId beolvasása
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
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>A ProtectionKeyId ProtectionKey beolvasása
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

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>A tartalom kulcsának létrehozása
Miután lekérte az X. 509 tanúsítványt, és a nyilvános kulcsát használta a tartalmi kulcs titkosításához, hozzon létre egy **ContentKey** -entitást, és ennek megfelelően állítsa be a tulajdonság értékét.

A tartalmi kulcs létrehozásakor beállított értékek egyike a típus. A tárolási titkosítás használatakor az értéket az "1" értékre kell beállítani. 

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

## <a name="create-an-asset"></a>Adategység létrehozása
Az alábbi példa bemutatja, hogyan hozhat létre egy eszközt.

**HTTP-kérés**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**HTTP-válasz**

Ha a művelet sikeres, a rendszer a következő választ adja vissza:

```console
HTP/1.1 201 Created
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
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
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

## <a name="create-an-assetfile"></a>AssetFile létrehozása
A [AssetFile](/rest/api/media/operations/assetfile) entitás egy blob-tárolóban tárolt videót vagy hangfájlt jelöl. Az adategységek mindig egy adott objektumhoz vannak társítva, és egy adott eszköz egy vagy több adatfájlt is tartalmazhat. A Media Services Encoder feladat meghiúsul, ha egy objektum nem egy blob-tárolóban lévő digitális fájllal van társítva.

A **AssetFile** példány és a tényleges médiafájl két különálló objektum. A AssetFile-példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl tartalmazza a tényleges médiatartalom tartalmát.

Miután feltöltötte a digitális médiafájlt egy blob-tárolóba, az **egyesítési** http-kéréssel frissíti a AssetFile a médiafájl információit használva (ez a cikk nem jelenik meg). 

**HTTP-kérés**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
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
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
