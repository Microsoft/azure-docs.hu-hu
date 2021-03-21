---
title: A Widevine-licenc sablonjának áttekintése | Microsoft Docs
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine-licencekről.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4a3805e50ce48d1439696a3d9015d6ba8571470
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011187"
---
# <a name="widevine-license-template-overview"></a>A Widevine-licenc sablonjának áttekintése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Azure Media Services használatával konfigurálhatja és kérheti a Google Widevine-licenceket. Ha a lejátszó megpróbálja lejátszani a Widevine-védelemmel ellátott tartalmat, a rendszer egy kérelmet küld a licenc kézbesítési szolgáltatásnak a licenc beszerzéséhez. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Widevine-licencszerződés JSON-üzenetként van formázva.  

>[!NOTE]
> Létrehozhat egy érték nélküli üres üzenetet, amely csak " {} ." Ezt követően a rendszer alapértelmezésekkel hozza létre a licenceket. Az alapértelmezett működés a legtöbb esetben. A Microsoft-alapú licencek kézbesítési forgatókönyvei mindig az alapértelmezett értékeket használják. Ha a "provider" és a "content_id" értéket kell beállítania, a szolgáltatónak meg kell egyeznie a Widevine hitelesítő adataival.

```json
{  
   "payload": "<license challenge>",
   "content_id": "<content id>" 
   "provider": "<provider>"
   "allowed_track_types": "<types>",
   "content_key_specs": [  
      {  
         "track_type": "<track type 1>"
      },
      {  
         "track_type": "<track type 2>"
      },
      …
   ],
   "policy_overrides": {  
      "can_play": <can play>,
      "can persist": <can persist>,
      "can_renew": <can renew>,
      "rental_duration_seconds": <rental duration>,
      "playback_duration_seconds": <playback duration>,
      "license_duration_seconds": <license duration>,
      "renewal_recovery_duration_seconds": <renewal recovery duration>,
      "renewal_server_url": "<renewal server url>",
      "renewal_delay_seconds": <renewal delay>,
      "renewal_retry_interval_seconds": <renewal retry interval>,
      "renew_with_usage": <renew with usage>
   }
}
```

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
Ha már létezik egy meglévő házirend, nem kell megadnia a tartalmi kulcs specifikációjának egyik értékét sem. A tartalomhoz tartozó előre meglévő szabályzat a kimeneti védelem meghatározására szolgál, például a nagy sávszélességű digitális Content Protection (HDCP) és az általános felügyeleti rendszer (CGMS). Ha egy már meglévő házirend nincs regisztrálva a Widevine-kiszolgálón, a tartalomszolgáltató az értékeket a licencszerződésbe szúrhatja be.   

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
| policy_overrides. can_play |Boolean, True vagy FALSE |Azt jelzi, hogy a tartalom lejátszása engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_persist |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc az offline használat érdekében nem felejtő tárolóban maradhat. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_renew |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc megújítása engedélyezett. Igaz értéke esetén a licenc időtartama a szívveréssel bővíthető. Az alapértelmezett érték a false (hamis). |
| policy_overrides. license_duration_seconds |Int64 |Megadja az adott licenc időtartományát. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides. rental_duration_seconds |Int64 |Azt jelzi, hogy a lejátszás közben engedélyezett-e az időablak. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides. playback_duration_seconds |Int64 |A megtekintési idő a lejátszás után a licenc időtartamán belül kezdődik. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides. renewal_server_url |sztring |A licenchez tartozó összes szívverési (megújítási) kérelem a megadott URL-címre van irányítva. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides. renewal_delay_seconds |Int64 |A megújítás első megkísérlése után hány másodpercig license_start_time. Ez a mező csak akkor használható, ha a can_renew értéke igaz. Az alapértelmezett érték a 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |A későbbi licenc-megújítási kérelmek közötti késleltetést adja meg, meghibásodás esetén. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Az az időszak, amelyben a lejátszás folytatódni fog, miközben a rendszer megkísérli a megújítást, de a licenckiszolgálóval kapcsolatos háttérbeli problémák miatt nem sikerült. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides. renew_with_usage |Boolean, True vagy FALSE |Azt jelzi, hogy a rendszer a használat megkezdése után elküldi a licencet a megújításhoz. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Name | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 kódolású karakterlánc |Ezt a munkamenet-jogkivonatot visszaadja a licenc, és a későbbi megújításokban van. A munkamenet-jogkivonat nem marad meg a munkameneteken kívül. |
| provider_client_token |Base64 kódolású karakterlánc |Az ügyfél jogkivonata, amelyet vissza kell küldenie a licencelési válaszban. Ha a licencszerződés tartalmaz egy ügyfél-jogkivonatot, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél-jogkivonat a licencelési munkameneteken kívül is fennáll. |
| override_provider_client_token |Boolean, True vagy FALSE |Ha a False (hamis) értékre van beállítva, és a licencszerződés tartalmaz egy ügyfél-jogkivonatot, akkor is használja a jogkivonatot a kérelemből, ha meg van adva egy ügyfél-jogkivonat Ha az értéke igaz, mindig használja az ebben a struktúrában megadott jogkivonatot. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Widevine-licencek konfigurálása .NET-típusok használatával
Media Services biztosít a Widevine-licencek konfigurálásához használható .NET API-kat. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>A Media Services .NET SDK-ban meghatározott osztályok
A következő osztályok a típusok definícióit jelentik:

```dotnetcli
public class WidevineMessage
{
    public WidevineMessage();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public AllowedTrackTypes? allowed_track_types { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public ContentKeySpecs[] content_key_specs { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public object policy_overrides { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum AllowedTrackTypes
{
    SD_ONLY = 0,
    SD_HD = 1
}
public class ContentKeySpecs
{
    public ContentKeySpecs();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string key_id { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public RequiredOutputProtection required_output_protection { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public int? security_level { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string track_type { get; set; }
}

public class RequiredOutputProtection
{
    public RequiredOutputProtection();

    public Hdcp hdcp { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum Hdcp
{
    HDCP_NONE = 0,
    HDCP_V1 = 1,
    HDCP_V2 = 2
}
```

### <a name="example"></a>Példa
Az alábbi példa bemutatja, hogyan használható a .NET API-k egy egyszerű Widevine-licenc konfigurálására:

```dotnetcli
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
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[PlayReady és/vagy Widevine Dynamic Common Encryption használata](media-services-protect-with-playready-widevine.md)

