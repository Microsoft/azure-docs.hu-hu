---
title: Media Services v3 Widevine-licenc sablonjának áttekintése
description: Ismerkedjen meg Azure Media Services a Widevine-licenccel, és hogyan konfigurálható a Widevine-licencek konfigurálása.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ef45e804b593f36171907395c564c8c6058c286
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453733"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>A Media Services v3 és a Widevine-licenc sablonjának áttekintése

A Azure Media Services lehetővé teszi a tartalom titkosítását a **Google Widevine**. A Media Services egy szolgáltatást is biztosít a Widevine-licencek kézbesítéséhez. A Widevine-licencek konfigurálásához Azure Media Services API-kat használhat. Amikor egy játékos megpróbál lejátszani a Widevine-védelemmel ellátott tartalmat, a rendszer elküldi a licenc kézbesítési szolgáltatásának a licenc beszerzésére irányuló kérelmet. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

A Widevine-licencszerződés JSON-üzenetként van formázva.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Létrehozhat egy érték nélküli üres üzenetet, amely csak " {} ." Ezt követően a rendszer alapértelmezésekkel hozza létre a licenceket. Az alapértelmezett működés a legtöbb esetben. A Microsoft-alapú licencek kézbesítési forgatókönyvei mindig az alapértelmezett értékeket használják. Ha a "provider" és a "content_id" értéket kell beállítania, a szolgáltatónak meg kell egyeznie a Widevine hitelesítő adataival.

## <a name="json-message"></a>JSON-üzenet

| Name | Érték | Leírás |
| --- | --- | --- |
| payload |Base64 kódolású karakterlánc |Az ügyfél által eljuttatott licencelési kérelem. |
| content_id |Base64 kódolású karakterlánc |Az egyes content_key_specs. track_type tartozó kulcs-azonosító és a tartalmi kulcs származtatása céljából használt azonosító. |
| Szolgáltató |sztring |A tartalmi kulcsok és szabályzatok keresésére szolgál. Ha a Microsoft Key Delivery szolgáltatás a Widevine-licencek kézbesítéséhez használatos, ezt a paramétert a rendszer figyelmen kívül hagyja. |
| policy_name |sztring |Egy korábban regisztrált szabályzat neve. Választható. |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Meghatározza, hogy mely tartalmi kulcsok szerepeljenek a licencekben. |
| content_key_specs |A JSON-struktúrák tömbje a "tartalmi kulcs specifikációi" című szakaszban található.  |A visszaadni kívánt tartalmi kulcsokat tartalmazó finomabb vezérlőelem. További információt a "tartalmi kulcsra vonatkozó specifikációk" című szakaszban talál. A allowed_track_types és a content_key_specs értékek közül csak az egyik adható meg. |
| use_policy_overrides_exclusively |Boolean, True vagy FALSE |Használja a policy_overrides által megadott házirend-attribútumokat, és hagyja ki az összes korábban tárolt házirendet. |
| policy_overrides |JSON-struktúra, tekintse meg a "szabályzat-felülbírálások" című szakaszt. |A licenchez tartozó házirend-beállítások.  Abban az esetben, ha ez az eszköz előre definiált szabályzattal rendelkezik, a rendszer ezeket a megadott értékeket használja. |
| session_init |JSON-struktúra: "munkamenet inicializálásának" szakasza. |A licenc nem kötelező. |
| parse_only |Boolean, True vagy FALSE |A rendszer elemzi a licencszerződést, de nem ad ki licencet. A válaszban azonban visszaadja a licencszerződés értékeit. |

## <a name="content-key-specs"></a>Tartalmi kulcs specifikációi
Ha már létezik meglévő szabályzat, nem kell megadnia a tartalmi kulcs specifikációjában szereplő értékeket. Az ehhez a tartalomhoz társított előre megadott szabályzat a kimeneti védelem meghatározására szolgál, például a nagy sávszélességű digitális Content Protection (HDCP) és a másolási általános felügyeleti rendszer (CGMS) használatával. Ha egy korábban már létező szabályzat nincs regisztrálva a Widevine-kiszolgálón, a tartalomszolgáltató az értékeket a licencszerződésbe szúrhatja be.   

Minden content_key_specs értéket meg kell adni az összes pályán, a use_policy_overrides_exclusively lehetőségtől függetlenül. 

| Name | Érték | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |sztring |A követési típus neve. Ha content_key_specs van megadva a licencelési kérelemben, ügyeljen arra, hogy explicit módon adja meg az összes nyomkövetési típust. Ennek elmulasztása miatt nem sikerült lejátszani az elmúlt 10 másodpercet. |
| content_key_specs  <br/> security_level |UInt32 |Meghatározza a lejátszáshoz szükséges ügyfél-megbízhatósági követelményeket. <br/> – A szoftveres alapú, fehér dobozos titkosítás szükséges. <br/> – A szoftveres titkosítás és a megzavarodott dekóder szükséges. <br/> – A kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres megbízható végrehajtási környezetben kell végrehajtani. <br/> – A tartalom titkosítását és visszafejtését hardveres megbízható végrehajtási környezetben kell végrehajtani.  <br/> – A titkosítást, a dekódolást és az adathordozó összes kezelését (tömörített és tömörítetlen) a hardveres megbízhatóságú végrehajtási környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection. HDC |karakterlánc, az egyik HDCP_NONE, HDCP_V1, HDCP_V2 |Azt jelzi, hogy a HDCP kötelező-e. |
| content_key_specs <br/>kulcs |Base64<br/>kódolt sztring |A nyomon követéshez használandó tartalmi kulcs. Ha meg van adva, a track_type vagy key_id megadása kötelező. A tartalomszolgáltató ezzel a kapcsolóval szúrhatja be a nyomkövetési kulcsot, ahelyett, hogy a Widevine-licenckiszolgáló létrehoz vagy megkeres egy kulcsot. |
| content_key_specs content_key_specs.key_id |Base64 kódolású karakterlánc, bináris, 16 bájt |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>Szabályzat felülbírálásai
| Name | Érték | Leírás |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, True vagy FALSE |Azt jelzi, hogy a tartalom lejátszása engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_persist |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc az offline használat érdekében nem felejtő tárolóban maradhat. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_renew |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc megújítása engedélyezett. Igaz értéke esetén a licenc időtartama a szívveréssel bővíthető. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;license_duration_seconds |Int64 |Megadja az adott licenc időtartományát. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Azt jelzi, hogy a lejátszás közben engedélyezett-e az időablak. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |A megtekintési idő a lejátszás után a licenc időtartamán belül kezdődik. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_server_url |sztring |A licenchez tartozó összes szívverési (megújítási) kérelem a megadott URL-címre van irányítva. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |A megújítás első megkísérlése után hány másodpercig license_start_time. Ez a mező csak akkor használható, ha a can_renew értéke igaz. Az alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |A későbbi licenc-megújítási kérelmek közötti késleltetést adja meg, meghibásodás esetén. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Az az időszak, amelyben a lejátszás folytatódni fog, miközben a rendszer megkísérli a megújítást, de a licenckiszolgálóval kapcsolatos háttérbeli problémák miatt nem sikerült. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renew_with_usage |Boolean, True vagy FALSE |Azt jelzi, hogy a rendszer a használat megkezdése után elküldi a licencet a megújításhoz. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Name | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 kódolású karakterlánc |Ezt a munkamenet-jogkivonatot visszaadja a licenc, és a későbbi megújításokban van. A munkamenet-jogkivonat nem marad meg a munkameneteken kívül. |
| provider_client_token |Base64 kódolású karakterlánc |Az ügyfél jogkivonata, amelyet vissza kell küldenie a licencelési válaszban. Ha a licencszerződés tartalmaz egy ügyfél-jogkivonatot, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél-jogkivonat a licencelési munkameneteken kívül is fennáll. |
| override_provider_client_token |Boolean, True vagy FALSE |Ha a False (hamis) értékre van beállítva, és a licencszerződés tartalmaz egy ügyfél-jogkivonatot, akkor is használja a jogkivonatot a kérelemből, ha meg van adva egy ügyfél-jogkivonat Ha az értéke igaz, mindig használja az ebben a struktúrában megadott jogkivonatot. |

## <a name="configure-your-widevine-license-with-net"></a>A Widevine-licenc konfigurálása a .NET-tel 

A Media Services egy olyan osztályt biztosít, amely lehetővé teszi a Widevine-licencek konfigurálását. A licenc létrehozásához továbbítsa a JSON-t a [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

A sablon konfigurálásához a következőket teheti:

### <a name="directly-construct-a-json-string"></a>JSON-karakterlánc közvetlen létrehozása

Ez a metódus hibákra hajlamos lehet. Ajánlott más módszert használni a [szükséges osztályok definiálása és a JSON-szerializálása](#classes)című témakörben leírtak szerint.

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> A szükséges osztályok definiálása és a JSON-szerializálás

#### <a name="define-classes"></a>Osztályok definiálása

Az alábbi példa egy példát mutat be, amely a JSON-Widevine leképezett osztályok definícióit mutatja be. Az osztályok a JSON-karakterláncba való szerializálás előtt hozhatók létre.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>A licenc konfigurálása

Az előző szakaszban meghatározott osztályok használata a [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)konfigurálásához használt JSON létrehozásához:

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

További [információ a DRM elleni védelemről](protect-with-drm.md)
