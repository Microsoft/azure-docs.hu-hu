---
title: Tartalom titkosítása a Azure Portal
description: Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a tartalom titkosítását a Azure Portal Azure Media Services használatával.
ms.topic: quickstart
ms.date: 08/31/2020
ms.openlocfilehash: cef943d61b1f3073ced658454c702ac54a0eef6d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963155"
---
# <a name="quickstart-use-portal-to-encrypt-content"></a>Gyors útmutató: tartalom titkosítása a portál használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services segítségével biztonságossá teheti az adathordozót, amikor a számítógépet a tárolás, a feldolgozás és a kézbesítés teljes egészében elhagyja. A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek továbbítására a hitelesítő ügyfelek számára. 
 
Ha az adatfolyamban szeretné megadni a titkosítási beállításokat (ha vannak ilyenek), használjon **folyamatos átviteli házirendet** , és társítsa azt a folyamatos átviteli lokátorhoz. Hozza létre a **tartalmi kulcs házirendjét** annak konfigurálásához, hogy a tartalom kulcsa (amely biztonságos hozzáférést biztosít az **eszközeihez**) a végfelhasználók számára legyen továbbítva. Meg kell adnia a tartalmi kulcs házirendjének követelményeit (korlátozásait), amelyeknek teljesülniük kell ahhoz, hogy a megadott konfigurációval rendelkező kulcsok eljussanak az ügyfeleknek. 

> [!NOTE]
> A tartalmi kulcsra vonatkozó házirend nem szükséges a folyamatos átvitelhez vagy a letöltéshez.

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat az AES Clear Key vagy a DRM titkosítás használatával. Az adatfolyam visszafejtéséhez a Player Media Services Key Delivery Service-ből vagy a megadott Key Delivery szolgáltatásból kéri a kulcsot. Annak eldöntéséhez, hogy a felhasználó rendelkezik-e a kulcs lekéréséhez szükséges jogosultsággal, a szolgáltatás kiértékeli a kulcshoz megadott  **tartalmi kulcs házirendjét** .

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan tartalmi kulcsokra vonatkozó házirendet, amely megadja, hogy a rendszer milyen titkosítást alkalmazzon az eszközre az adatfolyamként. A rövid útmutató azt is bemutatja, hogyan állíthatja be a konfigurált titkosítást az eszközön.

### <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

* [Dinamikus titkosítás és kulcskézbesítés](content-protection-overview.md)
* [Streameléskeresők](streaming-locators-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcsszabályok](content-key-policy-concept.md)

## <a name="prerequisites"></a>Előfeltételek

Töltse fel és dolgozza fel a tartalmat az [Azure Portalban található eszközök kezelése](asset-create-asset-upload-portal-quickstart.md) című témakörben leírtak szerint

## <a name="create-a-content-key-policy"></a>Tartalmi kulcsokra vonatkozó szabályzat létrehozása

Hozza létre a **tartalmi kulcs házirendjét** annak konfigurálásához, hogy a tartalom kulcsa (amely biztonságos hozzáférést biztosít az **eszközeihez**) a végfelhasználók számára legyen továbbítva.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Media Services-fiókját, és kattintson rá.
1. Válassza a **tartalmi kulcs házirendjei (új)** lehetőséget.
1. Kattintson a **+ tartalom hozzáadása** lehetőségre az ablak tetején. 

Megjelenik a **tartalmi kulcs házirend létrehozása** ablak. Ebben az ablakban a titkosítási beállításokat kell választania. Az adathordozó védelméhez válassza a digitális jogkezelési (DRM), a speciális titkosítási szabvány (AES) lehetőséget, vagy mindkettőt.  

![Tartalmi kulcsokra vonatkozó szabályzat létrehozása](./media/encrypt-content-quickstart/create-content-key-policy.png)

Akár a DRM-beállítások valamelyikét, akár egy AES-128 Clear Key beállítást választ, javasoljuk, hogy adja meg, hogyan szeretné konfigurálni a korlátozásokat. Dönthet úgy, hogy nyitott vagy jogkivonat-korlátozást használ. Részletes magyarázatot a [tartalom-hozzáférés szabályozása](content-protection-overview.md#controlling-content-access)című témakörben talál.

### <a name="add-a-drm-content-key"></a>DRM-tartalom kulcsának hozzáadása

Megadhatja, hogy a tartalmat a Microsoft PlayReady és/vagy a Google Widevine vagy az Apple FairPlay használatával megvédje. Minden licenc-kézbesítési típus a hitelesítő adatok alapján titkosított formában ellenőrzi a tartalom kulcsait.

#### <a name="license-templates"></a>Licenc-sablonok

A licencelési sablonokkal kapcsolatos részletekért lásd:

* [Google Widevine-licenc sablonja](widevine-license-template-overview.md)

    > [!NOTE]
    > Létrehozhat egy olyan üres tanúsítványsablont, amely nem tartalmaz értéket, csak " {} ." Ezt követően a rendszer alapértelmezésekkel hozza létre a licenceket. Az alapértelmezett működés a legtöbb esetben.
* [Apple FairPlay-licencek követelményei és konfigurálása](fairplay-license-overview.md)
* [PlayReady-licencsablon](playready-license-template-overview.md)

### <a name="add-aes-clear-key"></a>AES tiszta kulcs hozzáadása

Hozzáadhat egy AES-128 titkosítatlan titkosítási lehetőséget is a tartalomhoz. A tartalmi kulcs titkosítatlan formában lesz továbbítva az ügyfélnek.

![AES titkosítatlan kulcs](./media/encrypt-content-quickstart/aes-clear-key-policy.png)

## <a name="create-a-streaming-locator-for-your-asset"></a>Adatfolyam-kereső létrehozása az eszközhöz

1. Keresse meg a Media Services-fiókját, és kattintson rá.
1. Válassza az **eszközök (új)** lehetőséget.
1. Az eszközök listájából válassza ki azt, amelyet titkosítani szeretne.  
1. A kiválasztott eszköz **folyamatos átviteli lokátor** szakaszában kattintson a **+ streaming-lokátor hozzáadása** elemre. 
1. Válasszon olyan **folyamatos átviteli házirendet** , amely megfelel a konfigurált **tartalmi kulcs házirendjének** .

    A [streaming](streaming-policy-concept.md) policys témakör részletesen ismerteti, hogy a folyamatos átviteli szabályzat milyen tartalmakat tartalmaz a tartalmi kulcs házirendjében.
1. Miután kiválasztotta a megfelelő folyamatos átviteli házirendet, kiválaszthatja a tartalmi kulcs házirendjét a legördülő listából.
1. A **Hozzáadás** gombra kattintva adja hozzá a folyamatos átviteli lokátort az eszközhöz.

    Ez közzéteszi az eszközt, és létrehozza a streaming URL-címeket.

![Egy adatfolyam-kereső](./media/encrypt-content-quickstart/multi-drm.png)

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben futtatta ezt a rövid útmutatót, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

[Eszközök kezelése](asset-create-asset-upload-portal-quickstart.md)
