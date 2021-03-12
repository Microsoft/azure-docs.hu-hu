---
title: A tartalomvédelem házirendjeinek konfigurálása a Azure Portal használatával | Microsoft Docs
description: Ez a cikk azt mutatja be, hogyan használható a Azure Portal a tartalomvédelem házirendjeinek konfigurálásához. A cikk azt is bemutatja, hogyan lehet engedélyezni a dinamikus titkosítást az eszközei számára.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c6620158965271ba99f80c2fbd36504fdbea7ecf
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010779"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>A tartalomvédelem házirendjeinek konfigurálása a Azure Portal használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).   > nem kerül be új funkciók vagy funkciók a Media Services v2-be. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)
>

 A Azure Media Services használatával biztonságossá teheti az adathordozót, amikor a számítógépét tárolás, feldolgozás és kézbesítés útján elhagyja. A Media Services használatával a tartalmakat a Advanced Encryption Standard (AES) használatával dinamikusan titkosíthatja a 128 bites titkosítási kulcsokkal. Ezt a közös titkosítással (CENC) is használhatja a PlayReady és/vagy a Widevine Digital Rights Management (DRM) és az Apple FairPlay használatával. 

A Media Services egy szolgáltatást biztosít a DRM-licencek és az AES-kulcsok továbbítására a hitelesítő ügyfelek számára. A Azure Portal használatával létrehozhat egy kulcs/licenc engedélyezési házirendet az összes titkosítási típushoz.

Ez a cikk bemutatja, hogyan konfigurálhat egy Content Protection-házirendet a portál használatával. A cikk azt is bemutatja, hogyan alkalmazhat dinamikus titkosítást az eszközeire.

## <a name="start-to-configure-content-protection"></a>A tartalomvédelem konfigurálásának megkezdése
Ha a portált a globális tartalomvédelem konfigurálására szeretné használni a Media Services-fiók használatával, hajtsa végre a következő lépéseket:

1. A [portálon](https://portal.azure.com/)válassza ki a Media Services fiókját.

1. Válassza a **Beállítások**  >  **tartalomvédelem** lehetőséget.

    ![Tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Kulcs/licenc engedélyezési házirendje
Media Services támogatja a kulcs-vagy licencelési kérelmeket használó felhasználók hitelesítésének több módját. Konfigurálnia kell a tartalomkulcs hitelesítési szabályzatát. Az ügyfélnek ezután meg kell felelnie a szabályzatnak, mielőtt a kulcs/licenc elérhetővé válik. A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások is.

A portálon létrehozhat egy kulcs/licenc engedélyezési házirendet az összes titkosítási típushoz.

### <a name="open-authorization"></a>Engedély megnyitása
A megnyitási korlátozás azt jelenti, hogy a rendszer minden olyan felhasználó számára biztosítja a kulcsot, aki a kulcs kérését végzi. Ez a korlátozás tesztelési célokra hasznos lehet. 

### <a name="token-authorization"></a>Jogkivonat-hitelesítés
A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a Simple Web Tokens (SWT) és a JSON Web Token (JWT) formátumú jogkivonatokat támogatja. Media Services nem biztosít STS-t. Létrehozhat egy egyéni STS-t, vagy használhatja az Azure Access Control Service a jogkivonatok kibocsátására. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. Ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek egyeznek a kulcshoz (vagy licenchez) konfigurált jogcímekkel, a Media Services Key Delivery Service visszaadja a kért kulcsot (vagy licencet) az ügyfélnek.

Ha a jogkivonat-korlátozott szabályzatot konfigurálja, meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrző kulcs tartalmazza azt a kulcsot, amelyet a jogkivonat aláírt. A kiállító az a biztonságos jogkivonat-szolgáltatás, amely kiadja a jogkivonatot. A célközönség (más néven hatókör) leírja a jogkivonat célját vagy azt az erőforrást, amelyet a jogkivonat engedélyez a hozzáféréshez. A Media Services Key Delivery Service ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

![Kulcs/licenc engedélyezési házirendje](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licencsablon
A PlayReady-licenc sablonja beállítja azokat a funkciókat, amelyek engedélyezve vannak a PlayReady-licencen. A PlayReady-licenccel kapcsolatos további információkért tekintse meg a [Media Services PlayReady-sablon áttekintése](media-services-playready-license-template-overview.md)című témakört.

### <a name="nonpersistent"></a>Nonpersistent
Ha nem állandóként konfigurálja a licencet, a rendszer csak akkor tárolja a memóriát, ha a lejátszó használja a licencet.  

![Nem állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Állandó
Ha állandóként konfigurálja a licencet, a rendszer az ügyfél állandó tárterületét menti.

![Állandó tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licencsablon
A Widevine-licenc sablonja beállítja a Widevine-licenceken engedélyezett funkciókat.

### <a name="basic"></a>Alapszintű
Ha az **alapszintű** lehetőséget választja, a sablon minden alapértelmezett értékkel jön létre.

### <a name="advanced"></a>Felsőfokú
A Widevine Rights sablonnal kapcsolatos további információkért tekintse meg a [Widevine-sablon áttekintése](media-services-widevine-license-template-overview.md)című témakört.

![Speciális tartalomvédelem](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguráció
A FairPlay titkosításának engedélyezéséhez válassza a **Fairplay konfiguráció** lehetőséget. Ezután válassza ki az alkalmazás **tanúsítványát** , és adja meg az **alkalmazás titkos kulcsát**. A FairPlay konfigurálásával és követelményeivel kapcsolatos további információkért lásd: [a HLS-tartalmak megvédése az Apple Fairplay vagy a Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Dinamikus titkosítás alkalmazása az eszközön
A dinamikus titkosítás kihasználásához kódolja a forrásfájlt egy adaptív sávszélességű MP4-fájlba.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Válasszon ki egy titkosítani kívánt eszközt
Az összes eszköz megjelenítéséhez válassza a **Beállítások**  >  **eszközök** elemet.

![Eszközök lehetőség](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Titkosítás AES vagy DRM használatával
Ha kijelöli egy eszköz **titkosítását** , a következő két lehetőség közül választhat: **AES** vagy **DRM**. 

#### <a name="aes"></a>AES
Az AES Clear Key encryption minden folyamatos átviteli protokollon engedélyezve van: Smooth Streaming, HLS és MPEG-DASH.

![Titkosítási konfiguráció](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. A **DRM** kiválasztása után különböző tartalomvédelem-házirendeket láthat (amelyeket ezen a ponton kell konfigurálni) és a folyamatos átviteli protokollok készletét:

    a. Az **PlayReady és WIDEVINE MPEG-Dash** dinamikusan titkosítja az MPEG-Dash streamet a PlayReady és a Widevine DRMs.

    b. Az **PlayReady és WIDEVINE MPEG-Dash + Fairplay és HLS** dinamikusan titkosítja az MPEG-Dash streamet a PlayReady és a Widevine DRMs. Ez a beállítás a HLS-streameket is titkosítja a FairPlay.

    c. A **PlayReady csak a Smooth streaming, a HLS és az MPEG-Dash** használatával dinamikusan titkosítja a Smooth streaming, a HLS és az MPEG-Dash streameket a PlayReady DRM használatával.

    d. A **Widevine csak az MPEG-Dash** dinamikusan titkosítja az MPEG-Dash-t a Widevine DRM-mel.
    
    e. A **Fairplay csak a HLS** dinamikusan titkosítja a HLS-streamet a Fairplay.

1. A FairPlay titkosításának engedélyezéséhez a **Content Protection globális beállítások** panelen válassza a **Fairplay konfiguráció** elemet. Ezután válassza ki az alkalmazás **tanúsítványát**, és adja meg az **alkalmazás titkos kulcsát**.

    ![Titkosítás típusa](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Miután elvégezte a titkosítás kijelölését, kattintson az **alkalmaz** gombra.

>[!NOTE] 
>Ha AES-titkosítású HLS szeretne lejátszani a Safariban, tekintse meg a következő blogbejegyzést a [Safari titkosított HLS](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
