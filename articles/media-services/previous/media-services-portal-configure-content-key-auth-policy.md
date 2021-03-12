---
title: A tartalmi kulcs engedélyezési házirendjének konfigurálása a Azure Portal használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy engedélyezési házirendet egy tartalmi kulcshoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008314"
---
# <a name="configure-a-content-key-authorization-policy"></a>A tartalmi kulcs engedélyezési házirendjének konfigurálása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 A Azure Media Services használatával a 128 bites titkosítási kulcsokkal vagy a [PlayReady digitális Rights Management (DRM)](https://www.microsoft.com/playready/overview/)használatával továbbíthatja a Advanced Encryption Standard (AES) védelemmel ellátott MPEG-DASH, Smooth Streaming és http Live Streaming (HLS) adatfolyamokat. A Media Services segítségével a Widevine DRM-mel titkosított DASH-streameket is továbbíthat. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

A Media Services egy kulcs/licenc kézbesítési szolgáltatást is biztosít, amelyből az ügyfelek AES-kulcsokat vagy PlayReady-vagy Widevine-licenceket szerezhetnek be a titkosított tartalom lejátszásához.

Ez a cikk bemutatja, hogyan konfigurálhatja a tartalom-kulcs engedélyezési házirendjét a Azure Portal használatával. A kulcs később a tartalom dinamikus titkosítására is használható. Jelenleg titkosíthatja a HLS, MPEG-DASH és Smooth Streaming formátumokat. A progresszív letöltések nem titkosíthatók.

Ha egy lejátszó egy dinamikusan titkosított adatfolyamot kér, Media Services a konfigurált kulccsal titkosítja a tartalmat AES vagy DRM titkosítás használatával. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

Ha több tartalmi kulcsot szeretne használni, vagy egy kulcs/licenc kézbesítési szolgáltatás URL-címét szeretné megadni a Media Services Key Delivery Service-ben kívül, használja a Media Services .NET SDK-t vagy a REST API-kat. További információkért lásd:

* [A tartalmi kulcs engedélyezési házirendjének konfigurálása a Media Services .NET SDK használatával](media-services-dotnet-configure-content-key-auth-policy.md)
* [A tartalmi kulcs engedélyezési házirendjének konfigurálása a Media Services használatával REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Néhány szempontot figyelembe kell venni
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. A tartalom folyamatos átvitelének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a folyamatos átviteli végpontnak "Running" állapotban kell lennie. 
* Az eszköznek az adaptív sávszélességű MP4 vagy az adaptív sávszélességű Smooth Streaming fájlokat kell tartalmaznia. További információ: [eszköz kódolása](media-services-encode-asset.md).
* A Key Delivery szolgáltatás 15 percig gyorsítótárazza a ContentKeyAuthorizationPolicy és kapcsolódó objektumait (házirend-beállítások és korlátozások). Létrehozhat egy ContentKeyAuthorizationPolicy, és megadhatja, hogy jogkivonat-korlátozást használjon, tesztelje, majd frissítse a szabályzatot a megnyitási korlátozásra. Ez a folyamat nagyjából 15 percet vesz igénybe, mielőtt a házirend a megnyitott verzióra vált.
* A Media Services streaming végpontja a CORS Access-Control-Allow-Origin fejléc értékét az elővizsgálati válaszban a következő helyettesítő karakterrel állítja be: " \* ". Ez az érték jól működik a legtöbb játékossal, beleértve a Azure Media Player, a Roku és a JWPlayer és egyebeket. Egyes dash.jst használó játékosok azonban nem működnek, mivel a hitelesítő adatok "include" értékre van állítva, a dash.js nem engedélyezik a "" helyettesítő karaktert a \* hozzáférés-vezérlés – engedélyezés – forrás értékeként. Ha az ügyfelet egyetlen tartományból futtatja, megkerülő megoldásként dash.js a korlátozást, Media Services az elővizsgálati válasz fejlécében megadhatja az adott tartományt. Segítségért nyisson meg egy támogatási jegyet a Azure Portalon keresztül.

## <a name="configure-the-key-authorization-policy"></a>A kulcs engedélyezési házirendjének konfigurálása
A kulcs-engedélyezési házirend konfigurálásához válassza a **tartalomvédelem** lapot.

Media Services több módszert is támogat a kulcsfontosságú kérelmeket küldő felhasználók hitelesítésére. A tartalmi kulcs engedélyezési házirendje rendelkezhet nyitott, jogkivonat-vagy IP-engedélyezési korlátozásokkal. (Az IP beállítható a REST vagy a .NET SDK használatával is.)

### <a name="open-restriction"></a>Nyitott korlátozás
A nyílt korlátozás azt jelenti, hogy a rendszer a kulcsot minden olyan személy számára biztosítja, aki a kulcs kérését végzi. Ez a korlátozás tesztelési célokra is hasznos lehet.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Jogkivonat-korlátozás
A jogkivonat-korlátozási szabályzat kiválasztásához kattintson a **jogkivonat** gombra.

A jogkivonat-korlátozott szabályzatot egy biztonságijogkivonat-szolgáltatás (STS) által kiállított tokennek kell kísérnie. Media Services támogatja a tokeneket az egyszerű webes jogkivonat ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) és a JSON web token (JWT) formátumokban. További információ: JWT- [hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t a jogkivonatok kibocsátásához. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek egyeznek a tartalmi kulcshoz konfigurált jogcímekkel, a Media Services Key Delivery Service visszaadja a titkosítási kulcsot az ügyfélnek.

Ha a jogkivonat-korlátozott szabályzatot konfigurálja, meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrző kulcs tartalmazza azt a kulcsot, amelyet a jogkivonat aláírt. A kibocsátó a jogkivonatot kiállító STS. A célközönség (más néven hatókör) leírja a jogkivonat célját vagy azt az erőforrást, amelyet a jogkivonat engedélyez a hozzáféréshez. A Media Services Key Delivery Service ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

### <a name="playready"></a>PlayReady
Ha a tartalmat a PlayReady-mel védik, az engedélyezési házirendben megadott egyik dolog egy XML-karakterlánc, amely meghatározza a PlayReady-licenc sablonját. Alapértelmezés szerint a következő házirend van beállítva:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

Kiválaszthatja a **szabályzat XML importálása** gombot, és megadhat egy másik XML-t, amely megfelel a [Media Services PlayReady-sablon ÁTTEKINTÉSÉBEN](media-services-playready-license-template-overview.md)meghatározott XML-sémának.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
