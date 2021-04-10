---
title: HLS-tartalmak védett Microsoft PlayReady vagy Apple FairPlay – Azure | Microsoft Docs
description: Ez a témakör áttekintést nyújt, és bemutatja, hogyan használható a Azure Media Services a HTTP Live Streaming-(HLS-) tartalom dinamikus titkosításához az Apple FairPlay használatával. Azt is bemutatja, hogyan használhatja a Media Services a FairPlay-licenceket az ügyfeleknek.
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 870b567d5917c405a3eef70a27b1a19c021d7a80
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055144"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Az Apple FairPlay vagy a Microsoft PlayReady által védett HLS-tartalmak

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > nem kerül be új funkciók vagy funkciók a Media Services v2-be. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)
>

A Azure Media Services lehetővé teszi a HTTP Live Streaming-(HLS-) tartalom dinamikus titkosítását a következő formátumok használatával:  

* **AES-128 boríték tiszta kulcsa**

    A teljes adathalmaz titkosítása az **AES-128 CBC** mód használatával történik. Az iOS és az OS X lejátszó natív módon támogatja az adatfolyam visszafejtését. További információ: [az AES-128 dinamikus titkosítás és a Key Delivery Service használata](media-services-playready-license-template-overview.md).
* **Apple FairPlay**

    Az egyes videó-és hangminták titkosítása **AES-128 CBC** mód használatával történik. Az **Fairplay streaming** (FPS) integrálva van az eszköz operációs rendszereibe, natív támogatással az iOS és az Apple TV szolgáltatásban. A Safari az OS X-ben lehetővé teszi az FPS használatát a titkosított Media Extensions (EME) felület támogatásával.
* **Microsoft-PlayReady**

Az alábbi képen a **HLS + Fairplay vagy a PlayReady dinamikus titkosítási** munkafolyamat látható.

![Dinamikus titkosítási munkafolyamat diagramja](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Ez a cikk bemutatja, hogyan használható a Media Services a HLS-tartalom dinamikus titkosításához az Apple FairPlay. Azt is bemutatja, hogyan használhatja a Media Services a FairPlay-licenceket az ügyfeleknek.

> [!NOTE]
> Ha a HLS-tartalmat a PlayReady-mel is szeretné titkosítani, létre kell hoznia egy közös tartalmi kulcsot, és hozzá kell rendelnie az eszközhöz. Emellett konfigurálnia kell a tartalmi kulcs engedélyezési házirendjét is a következő témakörben leírtak szerint: [PlayReady Dynamic Common encryption (a dinamikus közös titkosítás használata](media-services-protect-with-playready-widevine.md)).
>
>

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások

A következőkre akkor van szükség, ha a Media Services használatával titkosítja a FairPlay-mel titkosított HLS, és FairPlay-licenceket kíván biztosítani:

  * Egy Azure-fiók. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Egy Media Services-fiók. A létrehozáshoz lásd: [Azure Media Services fiók létrehozása a Azure Portal használatával](media-services-portal-create-account.md).
  * Regisztráljon az [Apple fejlesztői programjával](https://developer.apple.com/).
  * Az Apple megköveteli a tartalom tulajdonosának a [központi telepítési csomag](https://developer.apple.com/contact/fps/)beszerzését. Azt az állapotot, amelyet már megvalósított a kulcsfontosságú biztonsági modul (KSM) Media Servicesával, és a végső FPS-csomagot kéri. A végleges FPS-csomag utasításait a minősítés létrehozásához és az alkalmazás titkos kulcsának megszerzéséhez (KÉRDEZZEn) talál útmutatást. A FairPlay konfigurálására vonatkozó KÉRÉSt használ.
  * Azure Media Services .NET SDK- **3.6.0** vagy újabb verzió.

A következő dolgokat kell beállítani Media Services kulcs kézbesítési oldalán:

  * **Alkalmazás-tanúsítvány (AC)**: ez egy. pfx fájl, amely tartalmazza a titkos kulcsot. Ezt a fájlt hozza létre és titkosítja jelszóval.

       A kulcsfontosságú kézbesítési szabályzatok konfigurálásakor a jelszót és a. pfx-fájlt Base64 formátumban kell megadnia.

      A következő lépések azt ismertetik, hogyan hozhatók elő a. pfx tanúsítványfájl a FairPlay:

    1. Telepítse az OpenSSL-t a rendszerből https://slproweb.com/products/Win32OpenSSL.html .

        Lépjen arra a mappára, ahol a FairPlay-tanúsítvány és az Apple által szállított egyéb fájlok találhatók.
    2. Futtassa az alábbi parancsot a parancssorból. Ezzel átalakítja a. cer fájlt egy. PEM-fájlba.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 – tájékoztassa a der-in FairPlay. cer-out FairPlay-out. PEM
    3. Futtassa az alábbi parancsot a parancssorból. Ez átalakítja a. PEM fájlt egy. pfx fájlba a titkos kulccsal. A. pfx fájlhoz tartozó jelszót az OpenSSL kéri.

        "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12/pfx-profil-export-out FairPlay-out. pfx-Inkey privatekey. PEM-in FairPlay-out. PEM-passin file:privatekey-pem-pass.txt
  * **Alkalmazás-tanúsítvány jelszava**: a. pfx fájl létrehozásához használt jelszó.
  * **Alkalmazás-tanúsítvány jelszava azonosító**: fel kell töltenie a jelszót, hasonlóan a más Media Services kulcsok feltöltéséhez. Használja az **ContentKeyType. FairPlayPfxPassword** Enum értéket az Media Services azonosító lekéréséhez. Ezt kell használniuk a Key Delivery Policy beállításban.
  * **IV**: Ez a 16 bájtos véletlenszerű érték. Meg kell egyeznie az eszköz kézbesítési házirendjének IV értékével. Létrehozta a IV-t, és mindkét helyen elhelyezi a következőket: az eszköz kézbesítési házirendje és a kulcs kézbesítési házirendje beállítás.
  * **Megkérdezés**: Ez a kulcs akkor érkezik, amikor a minősítést az Apple Developer Portal használatával állítja elő. Minden fejlesztői csapat egyedi KÉRÉSt kap. Mentse a kérés másolatát, és tárolja biztonságos helyen. Később be kell állítania a KÉRDEZZEn FairPlayAsk Media Services.
  * **Ask ID**: ezt az azonosítót a rendszer a Media Servicesba való feltöltéskor kéri le. A **ContentKeyType. FairPlayAsk** enumerálási érték használatával fel kell töltenie a kérdést. Ennek eredményeképpen a rendszer a Media Services azonosítót adja vissza, és ezt kell használni a kulcs kézbesítési házirend beállításának beállításakor.

Az FPS-ügyfél oldalán a következő műveleteket kell megadnia:

  * **Alkalmazás-tanúsítvány (AC)**: ez egy. cer/. der fájl, amely tartalmazza a nyilvános kulcsot, amelyet az operációs rendszer használ a hasznos adatok titkosításához. Media Services tudnia kell róla, mert a lejátszónak szüksége van rá. A Key Delivery szolgáltatás visszafejti a megfelelő titkos kulccsal.

A FairPlay titkosított streamek lejátszásához szerezze be először a valódi KÉRÉSt, majd állítson be egy valódi tanúsítványt. Ez a folyamat mindhárom részt létrehoz:

  * . der fájl
  * . pfx-fájl
  * a. pfx fájl jelszava

A következő ügyfelek támogatják a HLS **-et AES-128 CBC** encryption: Safari on OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>A FairPlay Dynamic encryption és a License Delivery Services konfigurálása
Az alábbi általános lépésekkel biztosíthatja az adategységek védelmét a FairPlay az Media Services-licenc kézbesítési szolgáltatásával, valamint a dinamikus titkosítás használatával is.

1. Hozzon létre egy objektumot, és töltse fel bele a fájlokat.
2. Kódolja a fájlt tartalmazó objektumot az adaptív sávszélességű MP4 típusú beállításkészlettel.
3. Hozzon létre egy tartalomkulcsot, és társítsa a kódolt objektumhoz.  
4. Konfigurálja a tartalomkulcs hitelesítési szabályzatát. Adja meg az alábbiakat:

   * A kézbesítési módszer (ebben az esetben a FairPlay).
   * FairPlay házirend-beállítások konfigurálása. A FairPlay konfigurálásával kapcsolatos további információkért tekintse meg az alábbi példában szereplő **ConfigureFairPlayPolicyOptions ()** metódust.

     > [!NOTE]
     > Általában csak egyszer kell konfigurálnia a FairPlay házirend-beállításokat, mert csak egy tanúsítvánnyal és egy KÉRDÉSsel rendelkezik.
     >
     >
   * Korlátozások (nyitott vagy jogkivonat).
   * A kulcs kézbesítési típusára vonatkozó információ, amely meghatározza, hogy a rendszer hogyan kézbesíti a kulcsot az ügyfélnek.
5. Adja meg az eszköz kézbesítési házirendjét. A kézbesítési szabályzat beállításai a következők:

   * A kézbesítési protokoll (HLS).
   * A dinamikus titkosítás típusa (közös CBC-titkosítás).
   * A licenc-beszerzési URL-cím.

     > [!NOTE]
     > Ha a FairPlay és egy másik digitális Rights Management (DRM) rendszerrel titkosított streamet szeretne továbbítani, külön kézbesítési házirendeket kell konfigurálnia:
     >
     > * Egy IAssetDeliveryPolicy dinamikus adaptív átvitelt konfigurálhat HTTP-n keresztül (DASH) Common Encryption (CENC) (PlayReady + Widevine) és zökkenőmentesen a PlayReady
     > * Másik IAssetDeliveryPolicy a HLS FairPlay konfigurálásához
     >
     >
6. Hozzon létre egy OnDemand-lokátort a streamelési URL-cím lekéréséhez.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay használata a Player-alkalmazások általi kézbesítéssel
A Player-alkalmazásokat az iOS SDK használatával fejlesztheti. A FairPlay-tartalom lejátszásához végre kell hajtania a License Exchange protokollt. Ezt a protokollt az Apple nem adja meg. Az egyes alkalmazásokban a kulcsok kézbesítésére vonatkozó kérések küldhetők. A Media Services FairPlay kulcs kézbesítési szolgáltatása arra vár, hogy a KOT a következő formában a www-Form-URL-ként kódolt üzenetként fog érkezni:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player támogatja a FairPlay lejátszását. További információért tekintse meg [Azure Media Player dokumentációját](https://amp.azure.net/libs/amp/latest/docs/index.html) .
>
>

## <a name="streaming-urls"></a>Streaming URL-címek
Ha az eszköz több DRM-mel lett titkosítva, használjon titkosítási címkét a streaming URL-címében: (Format = 'm 3u8-AAPL ', encryption = ' xxx ').

A következő szempontokat kell figyelembe venni:

* Csak nulla vagy egy titkosítási típus adható meg.
* A titkosítási típust nem kell megadni az URL-címben, ha csak egy titkosítás lett alkalmazva az eszközre.
* A titkosítási típus nem megkülönbözteti a kis-és nagybetűket.
* A következő titkosítási típusokat lehet megadni:  
  * **Cenc**: Common encryption (PlayReady vagy Widevine)
  * **CBCS-AAPL**: Fairplay
  * **CBC**: AES-borítékok titkosítása

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 
2. Adja hozzá a következő elemeket az app.config fájlban megadott **appSettings** szakaszhoz:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Példa

Az alábbi minta azt mutatja be, hogyan használhatók a Media Services a tartalom FairPlay való titkosításához. Ez a funkció a Azure Media Services SDK for .NET verzió 3.6.0 lett bevezetve. 

Írja felül a Program.cs fájlban található kódot az itt látható kóddal.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
