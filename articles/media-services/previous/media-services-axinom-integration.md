---
title: Widevine-licencek továbbítása a Axinom használatával a Azure Media Services | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Media Services (AMS) egy olyan stream továbbítására, amelyet az AMS dinamikusan titkosít az PlayReady és a Widevine DRMs. A PlayReady-licenc a Media Services PlayReady-licenckiszolgálóról származik, és a Widevine-licencet a Axinom-licenckiszolgáló továbbítja.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 532e982b02bb35074370db45fec8cc42a898c83e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015522"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Az Axinom használata a Widevine-licencek kézbestéséhez az Azure Media Servicesbe

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
Azure Media Services (AMS) a Google Widevine dinamikus védelmét vette fel (további részletekért lásd: [Mingfei blogja](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) ). Emellett a Azure Media Player (AMP) a Widevine-támogatással is bővült (további részletekért lásd az [amp-dokumentációt](https://amp.azure.net/libs/amp/latest/docs/) ). Ez a CENC által védett, több natív DRM-mel (PlayReady és Widevine) rendelkező, a modern böngészőkben található, az MSE és az EME platformmal ellátott streaming DASH-tartalmak jelentős megvalósítása.

A Media Services .NET SDK 3.5.2-es verziójától kezdve a Media Services lehetővé teszi a Widevine-licenc sablonjának konfigurálását és a Widevine-licencek beszerzését. A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) vagy [castLabs](https://castlabs.com/company/partners/azure/).

Ez a cikk a Axinom által felügyelt Widevine-licenckiszolgáló integrálását és tesztelését ismerteti. Pontosabban a következőket fedi le:  

* A dinamikus Common Encryption többplatformos DRM-sel (PlayReady és Widevine) való konfigurálása a megfelelő licenc-beszerzési URL-címekkel.
* JWT-token létrehozása a licenckiszolgáló követelményeinek kielégítése érdekében;
* Azure Media Player alkalmazás fejlesztése, amely a JWT jogkivonat-hitelesítéssel kezeli a licencek beszerzését;

A következő ábrán a teljes rendszeren és a tartalmi kulcs, a kulcs azonosítója, a kulcs magja, a JTW-token és a jogcímek is megadhatók:

![KÖTŐJEL és CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
A dinamikus védelem és a kulcsfontosságú kézbesítési szabályzat konfigurálásához tekintse meg a Mingfei blogja: [Widevine-csomagolás konfigurálása a Azure Media Services használatával](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

A dinamikus CENC-védelmet a többplatformos DRM-mel konfigurálhatja a DASH streaminghez a következők mindegyikével:

1. A Microsoft Edge és a IE11 PlayReady védelme a jogkivonat-engedélyezési korlátozásokkal rendelkezhet. A jogkivonat-korlátozott szabályzatot egy biztonságos jogkivonat-szolgáltatás (STS) által kiállított tokennek kell kísérnie, például Azure Active Directory;
2. A Chrome Widevine-védelméhez a jogkivonat-hitelesítés megkövetelése egy másik STS által kiállított token használatával. 

A [JWT jogkivonat-létrehozási](media-services-axinom-integration.md#jwt-token-generation) szakasza azt ismerteti, hogy miért Azure Active Directory nem használható STS-ként a Axinom Widevine-licenckiszolgálóhoz.

### <a name="considerations"></a>Megfontolandó szempontok
1. A Key Delivery szolgáltatás konfigurálásához a Axinom megadott Key Seed (8888000000000000000000000000000000000000) és a generált vagy a kiválasztott kulcs AZONOSÍTÓját kell használnia. A Axinom-licenckiszolgáló a tartalmi kulcsokat tartalmazó összes licencet a tesztelés és a gyártás esetében érvényes.
2. A Widevine licenc-beszerzési URL-címe teszteléshez: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . A HTTP és a HTTS egyaránt engedélyezve van.

## <a name="azure-media-player-preparation"></a>Azure Media Player előkészítése
Az AMP v 1.4.0 a PlayReady és a Widevine DRM használatával dinamikusan csomagolt AMS-tartalmak lejátszását támogatja.
Ha a Widevine-licenckiszolgáló nem igényel jogkivonat-hitelesítést, a Widevine által védett DASH-tartalmak teszteléséhez nincs további teendője. Az AMP csapat például egy egyszerű [mintát](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)biztosít, ahol a Microsoft Edge-ben és a IE11-ben a PlayReady és a Chrome-ban is működik a Widevine használatával.
A Axinom által biztosított Widevine-licenckiszolgáló a JWT-jogkivonat hitelesítését igényli. Az JWT tokent a "X-AxDRM-Message" HTTP-fejlécen keresztül kell elküldeni a licencszerződésbe. Erre a célra a forrás beállítása előtt hozzá kell adnia a következő JavaScriptet a weblapon az AMP:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

A többi AMP-kód a standard AMP API, mint az AMP [-dokumentum.](https://amp.azure.net/libs/amp/latest/docs/)

Az egyéni hitelesítési fejléc beállításának fenti javascriptje továbbra is rövid távú megközelítés, mielőtt az AMP-es hivatalos hosszú távú megközelítés megjelent.

## <a name="jwt-token-generation"></a>JWT jogkivonat létrehozása
A Axinom Widevine a teszteléshez JWT jogkivonat-hitelesítés szükséges. Emellett az JWT-jogkivonat egyik jogcíme összetett objektumtípus a primitív adattípus helyett.

Sajnos az Azure AD csak egyszerű típusokkal rendelkező JWT-tokeneket tud kiadni. Hasonlóképpen, a .NET-keretrendszer API-ját (System. IdentityModel. tokens. nincs securitytokenhandler regisztrálva és JwtPayload) csak az összetett objektumtípusok adhatók meg jogcímként. A jogcímek azonban továbbra is karakterláncként vannak szerializálva. Ezért a kettő közül bármelyiket nem használhatja a JWT-jogkivonat létrehozásához a Widevine-licenc iránti kérelemhez.

John Sheehan [JWT NuGet-csomagja](https://www.nuget.org/packages/JWT) megfelel a követelményeknek, ezért ezt a NuGet-csomagot fogjuk használni.

Az alábbi kód az JWT-jogkivonat előállításához szükséges jogcímeket mutatja be a Axinom Widevine-licenckiszolgáló általi teszteléshez:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.IdentityModel.Tokens;
using System.IdentityModel.Protocols.WSTrust;
using System.Security.Claims;

namespace OpenIdConnectWeb.Utils
{
    public class JwtUtils
    {
        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
        {
            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

            var payload = new Dictionary<string, object>()
            {
                { "version", 1 },
                { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
            };

            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

            return token;
        }

        //convert hex string to byte[]
        public static byte[] ConvertHexStringToByteArray(string hexString)
        {
            if (hexString.Length % 2 != 0)
            {
                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
            }

            byte[] HexAsBytes = new byte[hexString.Length / 2];
            for (int index = 0; index < HexAsBytes.Length; index++)
            {
                string byteValue = hexString.Substring(index * 2, 2);
                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
            }

            return HexAsBytes;
        }

    }  

}  
```

Axinom Widevine-licenckiszolgáló

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Megfontolandó szempontok
1. Annak ellenére, hogy az AMS PlayReady-licenc kézbesítési szolgáltatásához a "Bearer =" érték szükséges a hitelesítési jogkivonat előtt, a Axinom Widevine-kiszolgáló nem használja azt.
2. Az Axinom kommunikációs kulcsot a rendszer aláírási kulcsként használja. A kulcs egy hexadecimális karakterlánc, azonban a kódolás során nem karakterláncként kell kezelni. Ezt a ConvertHexStringToByteArray metódussal érheti el.

## <a name="retrieving-key-id"></a>Kulcs AZONOSÍTÓjának beolvasása
Lehet, hogy észrevette, hogy az JWT-token generálásához szükséges programkódban kulcs-azonosítót kell megadni. Mivel az JWT tokennek készen kell lennie az AMP-lejátszó betöltése előtt, a kulcs AZONOSÍTÓját le kell kérni a JWT jogkivonat létrehozásához.

Természetesen több módon is megszerezheti a Key ID-t. Előfordulhat például, hogy egy adatbázisban tárolja a kulcs AZONOSÍTÓját, valamint a tartalmi metaadatokat. Vagy lekérheti a Key ID-t a DASH MPD (Media Presentation Description) fájlból. Az alábbi kód az utóbbira mutat.

```csharp
//get key_id from DASH MPD
public static string GetKeyID(string dashUrl)
{
    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
    {
        dashUrl += "(format=mpd-time-csf)";
    }

    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
    objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
    objXmlNamespaceManager.PushScope();

    XPathNodeIterator objXPathNodeIterator;
    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

    string key_id = string.Empty;
    if (objXPathNodeIterator.MoveNext())
    {
        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
    }

    return key_id;
}
```

## <a name="summary"></a>Összefoglalás

A Azure Media Services Content Protection és a Azure Media Player Widevine-támogatásának legújabb részeként a következő modern böngészők esetében is megvalósítható a DASH + multi-Native-DRM (PlayReady + Widevine), valamint az AMS-ben és a Widevine-kiszolgálón található PlayReady-licenccel együtt.

* Chrome
* Microsoft Edge Windows 10 rendszeren
* IE 11 Windows 8,1 és Windows 10 rendszeren
* A Firefox (Desktop) és a Safari Mac gépen (nem iOS) is támogatott a Silverlight-n keresztül, és ugyanaz az URL-cím Azure Media Player

A következő paraméterek szükségesek a mini-megoldás kihasználó Axinom Widevine-licenckiszolgálóhoz. A Key ID kivételével a Axinom többi paraméterét a Widevine-kiszolgáló beállítása alapján biztosítjuk.

| Paraméter | Használatuk módja |
| --- | --- |
| Kommunikációs kulcs azonosítója |Szerepelnie kell a (z) "com_key_id" jogcím értékének az JWT-tokenben (lásd [ezt a](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |
| Kommunikációs kulcs |Az JWT-token aláíró kulcsaként kell használni (lásd [ezt](media-services-axinom-integration.md#jwt-token-generation) a szakaszt). |
| Kulcs magja |A tartalom kulcsának az adott tartalom-AZONOSÍTÓval való létrehozásához kell használni (lásd  [ezt a](media-services-axinom-integration.md#content-protection) szakaszt). |
| Widevine-licenc beszerzésének URL-címe |A DASH streaming eszközre vonatkozó kézbesítési szabályzatának konfigurálásához kell használni (lásd  [ezt a](media-services-axinom-integration.md#content-protection) szakaszt). |
| Tartalmi kulcs azonosítója |Szerepelnie kell a JWT-token jogosultsági üzenet jogcímének (lásd [Ez a](media-services-axinom-integration.md#jwt-token-generation) szakasz) értékének részeként. |

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Elismerések
Szeretnénk elismerni a következő személyeket, akik hozzájárultak a dokumentum létrehozásához: Ferenczi Jõgi of Axinom, Mingfei Yan, és barátságok.

