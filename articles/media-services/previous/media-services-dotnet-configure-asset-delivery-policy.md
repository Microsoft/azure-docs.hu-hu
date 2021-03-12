---
title: Eszközök kézbesítési házirendjeinek konfigurálása .NET SDK-val | Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhat különböző eszközökre vonatkozó kézbesítési házirendeket Azure Media Services .NET SDK-val.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 82e152f257e867dfdbf97c92b041ce57c5a1bdbc
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014264"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Eszközök kézbesítési házirendjeinek konfigurálása a .NET SDK-val

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Áttekintés
Ha titkosított eszközök kézbesítését tervezi, a Media Services Content Delivery munkafolyamat egyik lépése az eszközök kézbesítési házirendjeinek konfigurálása. Az eszköz kézbesítési házirendje közli Media Services, hogyan szeretné kézbesíteni az eszközét: az adatstream protokollnak (például MPEG DASH, HLS, Smooth Streaming vagy all) az eszközét dinamikusan kell titkosítania, függetlenül attól, hogy szeretné-e dinamikusan titkosítani az eszközt és a (boríték vagy közös titkosítás).

Ez a cikk azt ismerteti, hogy miért és hogyan kell létrehozni és konfigurálni az eszközök kézbesítési házirendjeit.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>Továbbá a dinamikus csomagolás és a dinamikus titkosítás használatához az eszköznek adaptív sávszélességű MP4 vagy adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia.

Különböző házirendeket alkalmazhat ugyanarra az objektumra. Például alkalmazhat PlayReady titkosítást Smooth Streaming és AES-borítékos titkosításra az MPEG DASH és a HLS számára. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

Ha titkosított eszközt szeretne kézbesíteni, konfigurálnia kell az eszköz kézbesítési házirendjét. Az eszköz adatfolyamként való továbbítása előtt a streaming kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirenddel továbbítja a tartalmat. Ha például az eszköz titkosítását Advanced Encryption Standard (AES) boríték titkosítási kulccsal szeretné továbbítani, állítsa a szabályzat típusát **DynamicEnvelopeEncryption** értékre. A tároló titkosításának eltávolításához és az objektum kiürítésének törléséhez állítsa a házirend típusát **NoDynamicEncryption** értékre. Példák, amelyek bemutatják, hogyan konfigurálhatja ezeket a házirend-típusokat.

Az eszköz kézbesítési házirendjének konfigurálásának módjától függően dinamikusan csomagolhatja, titkosíthatja és továbbíthatja a következő folyamatos átviteli protokollokat: Smooth Streaming, HLS és MPEG DASH.

A következő lista a Smooth, a HLS és a DASH továbbításához használt formátumokat mutatja be.

Smooth Streaming:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Megfontolandó szempontok
* A AssetDeliveryPolicy törlése előtt törölnie kell az eszközhöz társított összes streaming-lokátort. Ha szeretné, később új AssetDeliveryPolicy hozhat létre.
* Nem hozható létre adatfolyam-kereső egy titkosított eszközön, ha nincs beállítva eszköz kézbesítési házirendje.  Ha az eszköz nem titkosítja a tárolót, a rendszer lehetővé teszi, hogy egy lokátort hozzon létre, és az objektumot a törlés nélkül továbbítsa az eszköz kézbesítési házirendjébe.
* Egyetlen objektumhoz több eszközre vonatkozó kézbesítési szabályzat is tartozhat, de csak egyetlen módszert adhat meg egy adott AssetDeliveryProtocol kezeléséhez.  Ez azt jelenti, hogy ha két kézbesítési házirendet próbál meg összekapcsolni, amely megadja a AssetDeliveryProtocol. SmoothStreaming protokollt, amely hibát eredményez, mivel a rendszer nem tudja, melyikre lesz szükség, amikor egy ügyfél Smooth Streaming kérelmet tesz.
* Ha rendelkezik egy meglévő adatfolyam-keresővel, akkor nem kapcsolhat új szabályzatot az eszközhöz (vagy leválaszthatja a meglévő szabályzatot az objektumból, vagy frissítheti az eszközhöz társított kézbesítési szabályzatot).  Először el kell távolítania az adatfolyam-keresőt, módosítania kell a házirendeket, majd újra létre kell hoznia a folyamatos átviteli lokátort.  Ugyanazt a locatorId használhatja, amikor újra létrehozza a folyamatos átviteli lokátort, de gondoskodnia kell arról, hogy ne okozzon problémát az ügyfelek számára, mivel a tartalmat a forrás vagy egy alárendelt CDN gyorsítótárazhatja.

## <a name="clear-asset-delivery-policy"></a>Eszköz kézbesítési házirendjének törlése

A következő **ConfigureClearAssetDeliveryPolicy** metódus azt adja meg, hogy ne alkalmazzon dinamikus titkosítást, és az adatfolyamot a következő protokollok bármelyikében kézbesítse: MPEG Dash, HLS és Smooth streaming protokollok. Előfordulhat, hogy ezt a házirendet szeretné alkalmazni a Storage-beli titkosított eszközökre.

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption-eszköz kézbesítési szabályzata

A következő **CreateAssetDeliveryPolicy** metódus létrehozza azt a **AssetDeliveryPolicy** , amely dinamikus Common encryption (**DynamicCommonEncryption**) protokollt használ egy Smooth Streaming Protocol (a többi protokoll le lesz tiltva a streamingből). A metódus két paramétert fogad **: az** adategységet (az adategységet, amelyre a kézbesítési házirendet alkalmazni kívánja) és a **IContentKey** (a **CommonEncryption** -típus tartalmi kulcsával kapcsolatos további információkért lásd: [tartalmi kulcs létrehozása](media-services-dotnet-create-contentkey.md#common_contentkey)).

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

A Azure Media Services a Widevine-titkosítás hozzáadását is lehetővé teszi. Az alábbi példa azt mutatja be, hogy a PlayReady és a Widevine is hozzá lett adva az eszköz kézbesítési házirendjéhez.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> A Widevine-mel történő titkosításkor csak a kötőjel használatával lehet kézbesíteni. Ügyeljen arra, hogy KÖTŐJELet határozzon meg az eszköz kézbesítési protokolljában.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption-eszköz kézbesítési szabályzata
A következő **CreateAssetDeliveryPolicy** metódus létrehozza azt a **AssetDeliveryPolicy** , amely a dinamikus borítékok titkosításának (**DynamicEnvelopeEncryption**) a Smooth streaming, a HLS és a Dash protokollokra való alkalmazására van konfigurálva (ha úgy dönt, hogy nem határoz meg bizonyos protokollokat, a rendszer letiltja a streamingtől). A metódus két paramétert fogad **: az** adategységet (az adategységet, amelyre a kézbesítési házirendet alkalmazni kívánja) és a **IContentKey** (a **EnvelopeEncryption** -típus tartalmi kulcsával kapcsolatos további információkért lásd: [tartalmi kulcs létrehozása](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

A AssetDeliveryPolicy létrehozásakor megadható értékekkel kapcsolatos információkért tekintse meg a [AssetDeliveryPolicy meghatározása szakaszban használt típusokat](#types) .   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>A AssetDeliveryPolicy definiálásához használt típusok

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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

