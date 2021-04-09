---
title: Media Services Apple FairPlay-licencek támogatása
description: Ez a témakör áttekintést nyújt az Apple FairPlay-licencekre vonatkozó követelményekről és konfigurációról.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 187c1e60d97e0bebb3b6216b0055ddffe6e6cb4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454381"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay-licencek követelményei és konfigurálása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services lehetővé teszi a HLS-tartalmak titkosítását az **Apple Fairplay** (AES-128 CBC) használatával. A Media Services egy szolgáltatást is biztosít a FairPlay-licencek kézbesítéséhez. Amikor egy játékos megpróbál lejátszani a FairPlay-védelemmel ellátott tartalmat, a rendszer egy kérelmet küld a licenc kézbesítési szolgáltatásnak a licenc beszerzéséhez. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, az az ügyfélnek eljuttatott licencet adja ki, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Media Services olyan API-kat is biztosít, amelyek segítségével konfigurálhatja a FairPlay-licenceket. Ez a témakör a FairPlay-licencekre vonatkozó követelményeket ismerteti, és bemutatja, hogyan konfigurálhat **Fairplay** -licenceket Media Services API-k használatával. 

## <a name="requirements"></a>Követelmények

A következőkre akkor van szükség, amikor a Media Services használatával titkosítja a HLS-tartalmakat az **Apple Fairplay** , és a Fairplay-licencek kézbesítéséhez Media Services használ:

* Regisztráljon az [Apple fejlesztői programjával](https://developer.apple.com/).
* Az Apple megköveteli a tartalom tulajdonosának a [központi telepítési csomag](https://developer.apple.com/contact/fps/)beszerzését. Azt az állapotot, amelyet már megvalósított a kulcsfontosságú biztonsági modul (KSM) Media Servicesával, és a végső FPS-csomagot kéri. A végleges FPS-csomag utasításait a minősítés létrehozásához és az alkalmazás titkos kulcsának megszerzéséhez (KÉRDEZZEn) talál útmutatást. A FairPlay konfigurálására vonatkozó KÉRÉSt használ.
* A következő dolgokat kell beállítani Media Services kulcs/licenc kézbesítési oldalán:

    * **Alkalmazás-tanúsítvány (AC)**: ez egy. pfx fájl, amely tartalmazza a titkos kulcsot. Ezt a fájlt hozza létre és titkosítja jelszóval. A. pfx-fájlnak Base64 formátumúnak kell lennie.

        A következő lépések azt ismertetik, hogyan hozhatók elő a. pfx tanúsítványfájl a FairPlay:

        1. Telepítse az OpenSSL-t a rendszerből https://slproweb.com/products/Win32OpenSSL.html .

            Lépjen arra a mappára, ahol a FairPlay-tanúsítvány és az Apple által szállított egyéb fájlok találhatók.
        2. Futtassa az alábbi parancsot a parancssorból. Ezzel átalakítja a. cer fájlt egy. PEM-fájlba.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 – tájékoztassa a der-in FairPlay. cer-out FairPlay-out. PEM
        3. Futtassa az alábbi parancsot a parancssorból. Ez átalakítja a. PEM fájlt egy. pfx fájlba a titkos kulccsal. A. pfx fájlhoz tartozó jelszót az OpenSSL kéri.

            "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12/pfx-profil-export-out FairPlay-out. pfx-Inkey privatekey. PEM-in FairPlay-out. PEM-passin file:privatekey-pem-pass.txt
            
    * **Alkalmazás-tanúsítvány jelszava**: a. pfx fájl létrehozásához használt jelszó.
    * **Megkérdezés**: Ez a kulcs akkor érkezik, amikor a minősítést az Apple Developer Portal használatával állítja elő. Minden fejlesztői csapat egyedi KÉRÉSt kap. Mentse a kérés másolatát, és tárolja biztonságos helyen. Be kell állítania a KÉRDEZZEn FairPlayAsk-val Media Services.
    
* Az FPS-ügyfél oldalán a következő műveleteket kell megadnia:

  * **Alkalmazás-tanúsítvány (AC)**: ez egy. cer/. der fájl, amely tartalmazza a nyilvános kulcsot, amelyet az operációs rendszer használ a hasznos adatok titkosításához. Media Services tudnia kell róla, mert a lejátszónak szüksége van rá. A Key Delivery szolgáltatás visszafejti a megfelelő titkos kulccsal.

* A FairPlay titkosított streamek lejátszásához szerezze be először a valódi KÉRÉSt, majd állítson be egy valódi tanúsítványt. Ez a folyamat mindhárom részt létrehoz:

  * . der fájl
  * . pfx-fájl
  * a. pfx fájl jelszava
  
> [!NOTE]
> Azure Media Services nem vizsgálja meg a tanúsítvány lejárati dátumát a csomagolás vagy a kulcs kézbesítése során. A tanúsítvány lejárata után továbbra is működni fog.

## <a name="fairplay-and-player-apps"></a>FairPlay és Player-alkalmazások

Ha a tartalom az **Apple Fairplay**-mel van titkosítva, az egyes videó-és hangminták titkosítása az **AES-128 CBC** mód használatával történik. Az **Fairplay streaming** (FPS) integrálva van az eszköz operációs rendszereibe, natív támogatással az iOS és az Apple TV szolgáltatásban. A Safari az OS X-ben lehetővé teszi az FPS használatát a titkosított Media Extensions (EME) felület támogatásával.

A Azure Media Player támogatja a FairPlay lejátszását is. További információ: [Azure Media Player dokumentáció](https://amp.azure.net/libs/amp/latest/docs/index.html).

Az iOS SDK használatával fejlesztheti saját Player-alkalmazásait. A FairPlay-tartalom lejátszásához végre kell hajtania a License Exchange protokollt. Ezt a protokollt az Apple nem adja meg. Az egyes alkalmazásokban a kulcsok kézbesítésére vonatkozó kérések küldhetők. A Media Services FairPlay kulcs kézbesítési szolgáltatása arra vár, hogy a KOT a következő formában a www-Form-URL-ként kódolt üzenetként fog érkezni:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-konfiguráció .NET-példa

A FairPlay-licencek konfigurálásához Media Services API-t használhat. Ha a lejátszó megpróbálja lejátszani a FairPlay-védelemmel ellátott tartalmat, a rendszer elküldi a licenc kézbesítésére vonatkozó kérelmet a licenc beszerzéséhez. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

> [!NOTE]
> Általában csak egyszer kell konfigurálnia a FairPlay házirend-beállításokat, mert csak egy tanúsítvánnyal és egy KÉRDÉSsel rendelkezik.

Az alábbi példa a [Media Services .net SDK](/dotnet/api/microsoft.azure.management.media.models) -t használja a licenc konfigurálásához.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Következő lépések

További [információ a DRM elleni védelemről](protect-with-drm.md)
