---
title: A DRM alrendszer (ök) hibrid tervezése Azure Media Services használatával | Microsoft Docs
description: Ez a témakör a DRM-alrendszer (k) hibrid kialakítását ismerteti Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: a48e761c4fb74802c6b1db63884ee192446720a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016270"
---
# <a name="hybrid-design-of-drm-subsystems"></a>A DRM-alrendszerek hibrid tervezése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a témakör a DRM-alrendszer (k) hibrid kialakítását ismerteti Azure Media Services használatával.

## <a name="overview"></a>Áttekintés

A Azure Media Services a következő három DRM-rendszer támogatását biztosítja:

* PlayReady
* Widevine (moduláris)
* FairPlay

A DRM-támogatás magában foglalja a DRM-titkosítást (dinamikus titkosítást) és a licencek kézbesítését, és Azure Media Player támogatja mind a 3 DRMs böngészőbeli SDK-val.

A DRM/CENC alrendszer tervezésének és megvalósításának részletes kezeléséhez tekintse meg a CENC című dokumentumot a [multi-DRM és a Access Control](media-services-cenc-with-multidrm-access-control.md)használatával.

Habár a három DRM-rendszer teljes körű támogatását kínáljuk, az ügyfeleknek esetenként a saját infrastruktúra/alrendszerek különböző részeit kell használniuk a hibrid DRM-alrendszer kiépítéséhez Azure Media Services mellett.

Az alábbiakban az ügyfelek által feltett gyakori kérdések olvashatók:

* Használhatom a saját DRM-licenccel rendelkező kiszolgálókat? (Ebben az esetben az ügyfelek befektettek a DRM-licencek kiszolgálói farmba beágyazott üzleti logikával).
* "Használhatom csak a DRM-licencek kézbesítését Azure Media Services a tartalom AMS-ben való üzemeltetése nélkül?"

## <a name="modularity-of-the-ams-drm-platform"></a>Az AMS DRM-platform modularitása

Egy átfogó felhőalapú videó-platform részeként Azure Media Services a DRM a rugalmasságot és a modularitást is tartalmazza. Az alábbi táblázatban ismertetett különböző kombinációk bármelyikével használhatja a Azure Media Servicest (a táblázatban használt jelölés magyarázata). 

|**& forrást üzemeltető tartalom**|**Tartalom titkosítása**|**DRM-licenckézbesítés**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Harmadik féltől származó|
|AMS|Harmadik féltől származó|AMS|
|AMS|Harmadik féltől származó|Harmadik féltől származó|
|Harmadik féltől származó|Harmadik féltől származó|AMS|

### <a name="content-hosting--origin"></a>& forrást üzemeltető tartalom

* AMS: a video Asset AMS-ben üzemel, és a streaming AMS streaming-végpontokon keresztül történik (de nem feltétlenül dinamikus csomagolás).
* Harmadik féltől származó: a videó egy harmadik féltől származó, AMS-en kívüli streaming platformon fut.

### <a name="content-encryption"></a>Tartalom titkosítása

* AMS: a tartalom titkosítását az AMS dinamikus titkosítása dinamikusan vagy igény szerint végzi.
* Harmadik féltől származó: a tartalom titkosítása az AMS-n kívül történik egy előzetes feldolgozási munkafolyamat használatával.

### <a name="drm-license-delivery"></a>DRM-licenckézbesítés

* AMS: az AMS licenc kézbesítési szolgáltatása kézbesíti a DRM-licencet.
* Harmadik féltől származó: a DRM-licencet egy harmadik féltől származó, AMS-en kívüli DRM-licenckiszolgáló továbbítja.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurálás a hibrid forgatókönyv alapján

### <a name="content-key"></a>Tartalmi kulcs

A tartalmi kulcs konfigurálásával a következő attribútumokat szabályozhatja az AMS dinamikus titkosítás és az AMS licenc kézbesítési szolgáltatásának használatával:

* A dinamikus DRM-titkosításhoz használt tartalmi kulcs.
* A licenc-kézbesítési szolgáltatások által kézbesített DRM-licenc tartalma: jogosultságok, tartalmi kulcs és korlátozások.
* A **tartalmi kulcs engedélyezési házirendjének korlátozása**: Open, IP vagy token korlátozás.
* Ha **a** rendszer a **tartalmi kulcs engedélyezési házirendjének korlátozását használja**, a **tartalom kulcsának engedélyezési házirendjének korlátozását** a licenc kiadása előtt kell teljesíteni.

### <a name="asset-delivery-policy"></a>Eszköz kézbesítési szabályzata

Az eszközök kézbesítési házirendjének konfigurálásával szabályozhatja a következő attribútumokat, amelyeket az AMS dinamikus csomagolása és egy AMS streaming-végpont dinamikus titkosítása használ:

* A Streaming Protocol és a DRM titkosítási kombinációja, például a kötőjel a CENC (PlayReady és Widevine) alatt, a Smooth streaming a PlayReady, a HLS alatt, Widevine vagy PlayReady alatt.
* Az egyes érintett DRMs alapértelmezett/beágyazott licencének kézbesítési URL-címei.
* A Widevine és a FairPlay tartozó HLS-azonosító lekérdezési karakterlánca tartalmazza-e a (LA_URLs) a DASH MPD vagy a lekéréses listában.

## <a name="scenarios-and-samples"></a>Forgatókönyvek és minták

Az előző szakaszban ismertetett magyarázat alapján a következő öt hibrid forgatókönyv a megfelelő **tartalmi kulcsú** - **eszközök kézbesítési szabályzatának** konfigurációs kombinációit használja (az utolsó oszlopban említett minták követik a táblázatot):

|**& forrást üzemeltető tartalom**|**DRM-titkosítás**|**DRM-licenckézbesítés**|**Tartalom kulcsának konfigurálása**|**Objektumtovábbítási szabályzat konfigurálása**|**Sample**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Igen|Yes|1. példa|
|AMS|AMS|Harmadik féltől származó|Igen|Yes|2. példa|
|AMS|Harmadik féltől származó|AMS|Igen|Nem|3. példa|
|AMS|Harmadik féltől származó|Kívül|Nem|Nem|4. példa|
|Harmadik féltől származó|Harmadik féltől származó|AMS|Igen|Nem|    

A mintákban a PlayReady-védelem mind a DASH, mind a Smooth streaming esetében működik. Az alábbi videó URL-címek Smooth streaming URL-címek. A megfelelő kötőjel URL-címek lekéréséhez egyszerűen fűzze hozzá a következőt: "(Format = mpd-Time-CSF)". Az [Azure Media test Player](https://aka.ms/amtest) használatával tesztelheti a böngészőt. Lehetővé teszi, hogy beállítsa, melyik Streaming Protocol-protokollt használja, amely alatt a tech. A IE11 és a Microsoft Edge a Windows 10-es rendszeren keresztül támogatja a PlayReady-t az EME-n keresztül. További információ: [a tesztelési eszköz részletei](./offline-playready-streaming-windows-10.md).

### <a name="sample-1"></a>1. példa

* Forrás (alap) URL-címe: `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady-LA_URL (kötőjel & Smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (kötőjel): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>2. példa

* Forrás (alap) URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady-LA_URL (kötőjel & Smooth): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>3. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (kötőjel & Smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>4. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (kötőjel & Smooth): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="summary"></a>Összefoglalás

Összefoglalva, Azure Media Services DRM-összetevők rugalmasak, a jelen témakörben leírtaknak megfelelően a tartalom és az eszköz kézbesítési szabályzatának megfelelő konfigurálásával hibrid forgatókönyvekben is használhatók.

## <a name="next-steps"></a>Következő lépések
Megtekintheti Media Services képzési útvonalakat.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]