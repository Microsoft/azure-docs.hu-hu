---
title: A tartalmi kulcs házirendjei a Media Services-ben – Azure
description: Ez a cikk magyarázatot nyújt a tartalmi kulcsokra vonatkozó házirendekről, valamint arról, hogy a Azure Media Services hogyan használják őket.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 1333bbcc7e2ea9219d28fcc1e63f21a833c2bc6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709563"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek továbbítására a hitelesítő ügyfelek számára. 

Az adatfolyam titkosítási beállításainak megadásához létre kell hoznia egy [adatfolyam-szabályzatot](streaming-policy-concept.md) , és hozzá kell rendelnie a [folyamatos átviteli lokátorhoz](streaming-locators-concept.md). Hozza létre a [tartalmi kulcs házirendjét](/rest/api/media/contentkeypolicies) annak konfigurálásához, hogy a tartalom kulcsa (amely biztonságos hozzáférést biztosít az [eszközeihez](assets-concept.md)) a végfelhasználók számára legyen továbbítva. Meg kell adnia a tartalmi kulcs házirendjének követelményeit (korlátozásait), amelyeknek teljesülniük kell ahhoz, hogy a megadott konfigurációval rendelkező kulcsok eljussanak az ügyfeleknek. A tartalmi kulcsra vonatkozó házirend nem szükséges a folyamatos átvitelhez vagy a letöltéshez. 

A tartalmi kulcs házirendjét általában a [folyamatos átviteli lokátorhoz](streaming-locators-concept.md)társítja. Azt is megteheti, hogy a tartalmi kulcsra vonatkozó házirendet egy [folyamatos átviteli házirenden](streaming-policy-concept.md) belül is megadhatja (ha speciális forgatókönyvekhez egyéni adatfolyam-szabályzatot hoz létre) 

## <a name="best-practices-and-considerations"></a>Ajánlott eljárások és szempontok

> [!IMPORTANT]
> Kérjük, tekintse át a következő javaslatokat.

* A Media Service-fiókhoz korlátozott számú szabályzatot kell terveznie, és újra fel kell használni őket a streaming-lokátorok számára, amikor ugyanazok a beállítások szükségesek. További információ: [kvóták és korlátozások](limits-quotas-constraints.md).
* A tartalmi kulcs házirendjei frissíthető. Akár 15 percet is igénybe vehet, amíg a kulcsok kézbesítési gyorsítótárai frissülnek, és felveszik a frissített szabályzatot. 

   A szabályzat frissítésével felülírja a meglévő CDN-gyorsítótárat, ami lejátszási problémát okozhat a gyorsítótárazott tartalmat használó ügyfelek számára.  
* Azt javasoljuk, hogy az egyes eszközökhöz ne hozzon létre új tartalmi kulcsokra vonatkozó házirendet. A legfontosabb előnye, hogy ugyanazokat a tartalmi kulcsokra vonatkozó szabályzatokat osztják meg egymás között, amelyek ugyanazokat a házirend-beállításokat igényelik:
   
   * Könnyebben kezelhető kis mennyiségű szabályzat.
   * Ha frissítenie kell a tartalmi kulcsra vonatkozó házirendet, a módosítások szinte azonnal érvénybe lépnek az összes új licencelési kérelemben.
* Ha új szabályzatot kell létrehoznia, létre kell hoznia egy új adatfolyam-keresőt az eszközhöz.
* Javasoljuk, hogy Media Services automatikusan létrehozza a tartalmi kulcsot. 

   Általában hosszú élettartamú kulcsot kell használnia, és ellenőriznie kell, hogy létezik-e a tartalmi kulcs házirendje a [Get](/rest/api/media/contentkeypolicies/get)paranccsal. A kulcs lekéréséhez külön műveleti módszert kell meghívnia a titkok és a hitelesítő adatok beszerzéséhez. lásd az alábbi példát.

## <a name="example"></a>Példa

A kulcs eléréséhez használja a alkalmazást `GetPolicyPropertiesWithSecretsAsync` , ahogy az az [aláíró kulcs beolvasása a meglévő házirendből](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) című példa mutatja.

## <a name="filtering-ordering-paging"></a>Szűrés, rendezés, lapozás

Lásd: [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md).

## <a name="additional-notes"></a>További megjegyzések

* A típusú tartalmi kulcsokra vonatkozó házirendek tulajdonságai `Datetime` mindig UTC formátumban jelennek meg.
* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

* [Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata](protect-with-aes128.md)
* [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
* [Alapszintű AES – titkosítatlan kulcs titkosítási és adatfolyam-mintakód](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
