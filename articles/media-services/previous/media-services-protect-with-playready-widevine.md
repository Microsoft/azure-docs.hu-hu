---
title: PlayReady és/vagy Widevine Dynamic Common Encryption használata | Microsoft Docs
description: Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP Live Streaming (HLS) típusú streamjeit Microsoft PlayReady DRM-védelemmel láthatja el. A rendszert Widevine DRM-védelemmel ellátott DASH-továbbításra is használhatja. Ez a témakör bemutatja, hogyan állíthat be dinamikus titkosítást a PlayReady vagy a Widevine DRM segítségével.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9a0e8a513023c093157a3f14a64de9f87de543ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009453"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>PlayReady és/vagy Widevine Dynamic Common Encryption használata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > nem kerül be új funkciók vagy funkciók a Media Services v2-be. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)
>   

## <a name="overview"></a>Áttekintés

 A Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP Live Streaming (HLS) típusú streamjeit [PlayReady DRM- (digitális jogkezelési)](https://www.microsoft.com/playready/overview/) védelemmel láthatja el. Ezenfelül titkosított DASH-streameket is továbbíthat Widevine DRM-licencek segítségével. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik. Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t.

A Media Services része egy szolgáltatás, amelynek segítségével PlayReady vagy Widevine DRM-licenceket továbbíthat. A Media Services ezenfelül API-kat is tartalmaz, amelyek segítségével beállíthatja azokat a jogokat és korlátozásokat, amelyeket szeretne betartatni a PlayReady vagy a Widevine DRM-futtatókörnyezettel, amikor egy felhasználó védett tartalmakat játszik le. Amikor a felhasználók DRM-védelemmel rendelkező tartalmat kérnek, a lejátszóalkalmazás licencet kér a Media Services licencelési szolgáltatástól. Ha a lejátszóalkalmazás hitelesítve van, a Media Services licencelési szolgáltatás kiadja a licencet. A PlayReady- vagy Widevine-licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

A Widevine-licencek biztosításához a következő AMS-partnereket is használhatja: 

* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

További információkért lásd az integrációt az [Axinom](media-services-axinom-integration.md) és a [castLabs](media-services-castlabs-integration.md) rendszerrel.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [Simple Web Tokens](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) és a [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT) formátumú jogkivonatokat támogatja. 

További információ: [a tartalmi kulcs engedélyezési házirendjének konfigurálása](media-services-protect-with-aes128.md#configure_key_auth_policy).

A dinamikus titkosítás által nyújtott előnyök kihasználásához többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumra van szükség. Ezenfelül be kell állítania az objektumhoz tartozó továbbítási szabályzatokat is (ennek módját a témakör későbbi részében írjuk le). Ezt követően az igényalapú streamelési kiszolgáló a streamelési URL-címben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal továbbítsa a streamet. Ennek eredményeképpen csak egyetlen tárolási formátumban tárolja a fájlokat és fizet a tárolásukért. A Media Services elkészíti és kiszolgálja az ügyféltől érkező kéréseknek megfelelő HTTP-választ.

Ez a cikk azon fejlesztők számára lehet hasznos, akik többféle DRM-mel (például PlayReadyvel és Widevine-nal) védett médiafájlok továbbításával foglalkoznak. A cikk ismerteti, hogyan konfigurálhatja a PlayReady-licenctovábbítási szolgáltatásra vonatkozó szabályzatokat úgy, hogy csak az arra jogosult ügyfelek kaphassák meg a PlayReady- és Widevine-licenceket. Ezenfelül azt is bemutatja, hogyan használja a dinamikus titkosítás funkciót a PlayReady vagy a Widevine DRM-mel a DASH-en keresztül.

>[!NOTE]
>A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak Fut állapotban kell lennie. 

## <a name="download-the-sample"></a>A minta letöltése
A cikkben leírt mintát a [GitHub Azure-mintái](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) közül töltheti le.

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>A Dynamic Common Encryption és a DRM-licenctovábbítási szolgáltatások konfigurálása

Hajtsa végre az alábbi általános lépéseket, amikor PlayReady-védelemmel látja el az objektumait a Media Services licenctovábbítási szolgáltatása, valamint a dinamikus titkosítás használata mellett:

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.

2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.

3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz. A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítási kulcsát.

4. Konfigurálja a tartalomkulcs engedélyezési házirendjét. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek meg kell felelnie a szabályzatnak, mielőtt megkapná a tartalomkulcsot.

    A tartalomkulcs hitelesítési szabályzatának létrehozásához be kell állítania a továbbítási módszert (PlayReady vagy Widevine) és a korlátozásokat (nyitott vagy jogkivonat). Meg kell adnia azokat az információkat is, amelyek azt határozzák meg, hogy a rendszer hogyan továbbítja a kulcsot az ügyfélnek ([PlayReady-](media-services-playready-license-template-overview.md) vagy [Widevine-](media-services-widevine-license-template-overview.md)licencsablon).

5. Konfigurálja az objektum továbbítási szabályzatát. A továbbítási szabályzat konfigurációjának részét képezi a továbbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy ezek mindegyike). A konfigurációnak a dinamikus titkosítás típusa (pl. Common Encryption) és a PlayReady- vagy Widevine-licenckérési URL-cím is a részét képezi.

    Egy adott objektum különböző protokolljaira eltérő szabályzatokat is alkalmazhat. Beállíthatja például, hogy a PlayReady-titkosítás csak a Smooth/DASH-re vonatkozzon, az AES Envelope pedig csak a HLS-re. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen eset lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

A cikk végén egy teljes .NET-példát talál.

Az alábbi képen az előzőekben leírt munkafolyamatot láthatja. Itt jogkivonatos hitelesítést használtuk.

![Védelem biztosítása a PlayReadyvel](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

A cikk további részében részletes magyarázatokat, kódmintákat és olyan témakörökre mutató hivatkozásokat talál, amelyek segítenek elérni az előzőekben ismertetett célokat.

## <a name="current-limitations"></a>Aktuális korlátozások
Objektumtovábbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden ahhoz tartozó lokátort, majd létre kell hoznia egy új lokátort.

Jelenleg több tartalomkulcs nem támogatott, ha Widevine-nal és a Media Services segítségével végzi a titkosítást. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Objektum létrehozása, majd fájlok feltöltése az objektumba
A videók kezeléséhez, kódolásához és streameléséhez először fel kell töltenie tartalmait a Media Services szolgáltatásba. A feltöltést követően tartalmai a biztonságos felhőtárhelyre kerülnek további feldolgozás és streamelés céljából.

További információkért tekintse át a [fájlok Media Services-fiókba történő feltöltésével](media-services-dotnet-upload-files.md) foglalkozó témakört.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>A fájlt tartalmazó objektum kódolása az adaptív sávszélességű, MP4 típusú beállításkészlettel
A dinamikus titkosítás segítségével egy többszörös sávszélességű MP4-fájlokat vagy Smooth Streaming-forrásfájlokat tartalmazó objektumot hoz létre. Ezt követően az igényalapú streamelési kiszolgáló a jegyzékfájlban és a töredékkérésben megadott formátumnak megfelelően gondoskodik arról, hogy a rendszer a kiválasztott protokollal biztosítsa Önnek a streamet. Ennek eredményeképpen csak egyetlen tárolási formátumban tárolja a fájlokat és fizet a tárolásukért. A Media Services összeállítja és kiszolgálja az ügyféltől érkező kéréseknek megfelelő választ. További információkért olvassa át [a dinamikus becsomagolást áttekintő](media-services-dynamic-packaging-overview.md) témakört.

A kódolással kapcsolatos utasításokért lásd: [Objektum kódolása a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Hozzon létre egy tartalmi kulcsot, és társítsa a kódolt objektumhoz
A Media Services szolgáltatásban a tartalomkulcs tartalmazza az objektum titkosítására használható kulcsot.

További információkat a [tartalomkulcs létrehozásával](media-services-dotnet-create-contentkey.md) foglalkozó témakör tartalmaz.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>A tartalmi kulcs engedélyezési házirendjének konfigurálása
A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzatnak, mielőtt megkapná a kulcsot. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is.

További információkért lásd a [tartalomkulcs-hitelesítési szabályzat konfigurálásával](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption) foglalkozó témakört.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Objektumtovábbítási szabályzat konfigurálása
Konfigurálja az objektum továbbítási szabályzatát. Az objektumtovábbítási szabályzat konfigurálásához többek között az alábbiak tartoznak:

* A DRM-licenckérési URL-cím.
* Az objektumtovábbítási protokoll (például MPEG-DASH, HLS, Smooth Streaming vagy ezek mindegyike).
* A dinamikus titkosítás típusa (ebben az esetben Common Encrpytion).

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
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

A stream kipróbálásához használja az [Azure Media Services-lejátszót](https://aka.ms/azuremediaplayer).

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint.

2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

Az alábbi mintában azokat a funkciókat mutatjuk be, amelyek a Media Services SDK for .NET 3.5.2-es verziójában kerültek bevezetésre. (Kifejezetten azt a funkciót, amellyel meghatározható egy Widevine-licencsablon, majd a Widevine-licenc lekérhető a Media Services szolgáltatásból.)

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.

>[!NOTE]
>A különböző Media Services-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 millió szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, a szabályzatazonosítónak is ugyanannak kell lennie. Például szolgálnak erre az olyan keresők szabályzatai, amelyek hosszú ideig érvényben maradnak (nem feltöltött szabályzatok). 

További információ: [Objektumok és kapcsolódó entitások felügyelete a Media Services .NET SDK-val](media-services-dotnet-manage-entities.md#limit-access-policies).

Módosítsa úgy a változókat, hogy a bemeneti fájlok tárolásához Ön által használt mappákra mutassanak.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString,
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
            {
                    new ContentKeySpecs
                    {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                    }
                },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még

* [CENC használata többszörös DRM-mel és hozzáférés-vezérléssel](media-services-cenc-with-multidrm-access-control.md)
* [Widevine-csomagolás konfigurálása a Media Services segítségével](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](./media-services-java-how-to-use.md)
* A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft/WindowsAzure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7). 
