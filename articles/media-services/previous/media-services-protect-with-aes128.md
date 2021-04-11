---
title: Az AES-128 Dynamic encryption és a Key Delivery Service használata | Microsoft Docs
description: Ez a témakör bemutatja, hogyan lehet dinamikusan titkosítani az AES-128-et, és hogyan használhatja a Key Delivery szolgáltatást.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7dacc01b37fb91f02fe67f76fe79c0bfa9375ea7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009538"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Media Services használatával HTTP Live Streaming (HLS) és az AES-vel titkosított Smooth Streaming titkosíthatja az 128 bites titkosítási kulcsok használatával. A Media Services a kulcsfontosságú kézbesítési szolgáltatást is biztosítja, amely titkosítási kulcsokat biztosít a hitelesítő felhasználók számára. Ha azt szeretné, hogy a Media Services titkosítson egy adategységet, rendeljen hozzá egy titkosítási kulcsot az objektumhoz, és konfigurálja a kulcs engedélyezési házirendjeit is. Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal használja fel a tartalom dinamikus titkosítását az AES-titkosítás használatával. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [Simple Web Tokens](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) és a [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT) formátumú jogkivonatokat támogatja. További információ: [a tartalmi kulcs engedélyezési házirendjének konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy).

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektummal kell rendelkeznie. Az eszköz kézbesítési szabályzatát is konfigurálnia kell (lásd a cikk későbbi részében). Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Ennek eredményeképpen csak egyetlen tárolási formátumban kell tárolnia a fájlokat és fizetnie. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ.

Ez a cikk olyan fejlesztők számára hasznos, akik a védett adathordozókat kézbesítő alkalmazásokon dolgoznak. A cikk bemutatja, hogyan konfigurálhatja a Key Delivery szolgáltatást engedélyezési házirendekkel, hogy csak a jogosult ügyfelek fogadhatnak titkosítási kulcsokat. Azt is bemutatja, hogyan használható a dinamikus titkosítás.

További információ arról, hogyan titkosíthatja a tartalmat az Advanced Encryption Standard (AES) használatával a macOS-hez készült Safarihoz való továbbításhoz. [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)olvashat.
A médiatartalmak AES-titkosítással való védelemmel kapcsolatos információkért tekintse meg ezt a [videót](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dinamikus titkosítási és kulcs kézbesítési szolgáltatás munkafolyamata

A következő általános lépéseket akkor hajtsa végre, ha az eszközöket AES-titkosítással titkosítja a Media Services Key Delivery Service és a dinamikus titkosítás használatával:

1. [Hozzon létre egy objektumot, és töltse fel a fájlokat az eszközre](media-services-protect-with-aes128.md#create_asset).

2. [Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4-készletbe](media-services-protect-with-aes128.md#encode_asset).

3. [Hozzon létre egy tartalmi kulcsot, és társítsa a kódolt](media-services-protect-with-aes128.md#create_contentkey)objektumhoz. A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.

4. [Adja meg a tartalmi kulcs engedélyezési házirendjét](media-services-protect-with-aes128.md#configure_key_auth_policy). Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek meg kell felelnie a szabályzatnak, mielőtt megkapná a tartalomkulcsot.

5. [Egy eszköz kézbesítési házirendjének konfigurálása](media-services-protect-with-aes128.md#configure_asset_delivery_policy). A kézbesítési szabályzat konfigurációja tartalmazza a kulcs-beszerzési URL-címet és az inicializálási vektort (IV). (Az AES-128 a titkosításhoz és a visszafejtéshez ugyanazt a IV-t igényli.) A konfiguráció emellett magában foglalja a kézbesítési protokollt (például MPEG-DASH, HLS, Smooth Streaming vagy mind) és a dinamikus titkosítás típusát (például boríték vagy nincs dinamikus titkosítás).

    Egy adott objektum különböző protokolljaira eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A továbbítási házirendben nem definiált protokollok nincsenek letiltva az adatfolyamból. (Ilyen például, ha egyetlen szabályzatot ad hozzá, amely csak a HLS adja meg.) Ez alól kivételt képez, ha nincs megadva eszközre vonatkozó kézbesítési szabályzat. Ebben az esetben a rendszer az összes protokollt engedélyezi.

6. [Hozzon létre egy OnDemand-lokátort](media-services-protect-with-aes128.md#create_locator) a streaming URL-cím lekéréséhez.

A cikk azt is bemutatja [, hogy egy ügyfélalkalmazás hogyan igényelhet kulcsot a Key Delivery szolgáltatástól](media-services-protect-with-aes128.md#client_request).

A cikk végén talál egy teljes [.net-példát](media-services-protect-with-aes128.md#example) .

Az alábbi képen az előzőekben leírt munkafolyamatot láthatja. Itt jogkivonatos hitelesítést használtuk.

![Védelem AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

A cikk további részében magyarázatokat és példákat talál, és olyan témakörökre mutató hivatkozásokat tartalmaz, amelyek bemutatják, hogyan érheti el a korábban leírt feladatokat.

## <a name="current-limitations"></a>Aktuális korlátozások
Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Eszköz létrehozása és fájlok feltöltése az objektumba
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából. 

További információkért tekintse át a [fájlok Media Services-fiókba történő feltöltésével](media-services-dotnet-upload-files.md) foglalkozó témakört.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>A fájlt tartalmazó objektum kódolása az adaptív sávszélességű, MP4 típusú beállításkészlettel
A dinamikus titkosítás segítségével egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot hoz létre. Ezután a manifest vagy a fragment kérelem megadott formátuma alapján az igény szerinti folyamatos átviteli kiszolgáló biztosítja, hogy megkapja az adatfolyamot a kiválasztott protokollon. Ezt követően csak egyetlen tárolási formátumban kell tárolnia a fájlokat és fizetnie. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ. További információkért olvassa át [a dinamikus becsomagolást áttekintő](media-services-dynamic-packaging-overview.md) témakört.

>[!NOTE]
>A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak Fut állapotban kell lennie. 
>
>Továbbá a dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4 vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

A kódolással kapcsolatos utasításokért lásd: [Objektum kódolása a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Hozzon létre egy tartalmi kulcsot, és társítsa a kódolt objektumhoz
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információkat a [tartalomkulcs létrehozásával](media-services-dotnet-create-contentkey.md) foglalkozó témakör tartalmaz.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>A tartalmi kulcs engedélyezési házirendjének konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek (Player) meg kell felelnie a szabályzatnak, mielőtt a kulcsot el lehetne küldeni az ügyfélnek. A tartalmi kulcs engedélyezési házirendje egy vagy több engedélyezési korlátozással rendelkezhet: nyitott, jogkivonat-korlátozás vagy IP-korlátozás.

További információkért lásd a [tartalomkulcs-hitelesítési szabályzat konfigurálásával](media-services-dotnet-configure-content-key-auth-policy.md) foglalkozó témakört.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A kulcs beszerzésének URL-címe. 
* A boríték-titkosításhoz használandó inicializálási vektor (IV). Az AES-128 ugyanazt a IV-t igényli a titkosításhoz és a visszafejtéshez. 
* Az objektumtovábbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy ezek mindegyike).
* A dinamikus titkosítás típusa (például AES-boríték) vagy nincs dinamikus titkosítás. 

További információkat lásd az [objektumtovábbítási szabályzat konfigurálását](media-services-dotnet-configure-asset-delivery-policy.md) ismertető témakörben.

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>OnDemand-lokátor létrehozása a streamelési URL-cím lekéréséhez
A felhasználók rendelkezésére kell bocsátania a Smooth Streaming, a DASH vagy a HLS streamelési URL-címét.

> [!NOTE]
> Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
> 
> 

További információk az objektumok közzétételéről és a streamelési URL-cím létrehozásáról: [Build a streaming URL](media-services-deliver-streaming-content.md) (Streamelési URL-cím létrehozása).

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
Kérje le a kulcshitelesítési szabályzatban használt jogkivonat-korlátozásoknak megfelelő tesztjogkivonatot.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

A stream kipróbálásához használja az [Azure Media Services-lejátszót](https://aka.ms/azuremediaplayer).

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hogyan kérheti le az ügyfél a kulcsot a Key Delivery szolgáltatástól?
Az előző lépésben létrehozta az URL-címet, amely egy manifest-fájlra mutat. Az ügyfélnek ki kell bontania a szükséges információkat a streaming manifest-fájlokból, hogy kérést küldjön a Key Delivery Service-nek.

### <a name="manifest-files"></a>Jegyzékfájlok
Az ügyfélnek ki kell bontania az URL-címet (amely szintén tartalmazza a tartalom-azonosító [Kid]) értékét a jegyzékfájlból. Az ügyfél ezután megpróbálja lekérni a titkosítási kulcsot a Key Delivery szolgáltatásból. Az ügyfélnek a IV értéket is ki kell bontania, és azt a stream visszafejtéséhez kell használnia. A következő kódrészlet a `<Protection>` Smooth streaming jegyzékfájl elemét mutatja be:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

A HLS esetében a gyökérszintű jegyzékfájl a szegmens fájlokba van bontva. 

A legfelső szintű jegyzékfájl például a következő: http: \/ /test001.Origin.Mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL). A szegmensek fájlneveit tartalmazó listát tartalmaz.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Ha egy szövegszerkesztőben (például a http: \/ /test001.Origin.Mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/QualityLevels (514369)/manifest (videó, Format = m3u8-AAPL) található szegmens fájlok egyikét nyitja meg, akkor #EXT-X-Key, amely azt jelzi, hogy a fájl titkosítva van.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Ha AES-titkosítású HLS szeretne lejátszani a Safariban, tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>A kulcs lekérése a Key Delivery Service-ből

A következő kód bemutatja, hogyan küldhet egy kérést a Media Services Key Delivery szolgáltatásnak egy, a jegyzékfájlból kinyert kulcs kézbesítési URI-vel és egy jogkivonattal. (Ez a cikk nem ismerteti, hogyan lehet SWTs beolvasni az STS-ből.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Tartalom biztosítása AES-128-mel a .NET használatával

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

2. Adja hozzá a következő elemeket a appSettings-hez a app.config fájlban megadott módon:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Például

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.
 
>[!NOTE]
>A különböző Media Services házirendek (például a lokátor házirend vagy a ContentKeyAuthorizationPolicy) esetében legfeljebb 1 000 000 szabályzat létezik. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, használja ugyanazt a házirend-azonosítót. Például szolgálnak erre az olyan keresők szabályzatai, amelyek hosszú ideig érvényben maradnak (nem feltöltött szabályzatok). További információkért tekintse meg az [eszközök és a kapcsolódó entitások kezelése a Media Services .net SDK-val](media-services-dotnet-manage-entities.md#limit-access-policies)című témakör "hozzáférési szabályzatok korlátozása" című szakaszát.

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
