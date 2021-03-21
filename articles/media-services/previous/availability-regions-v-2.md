---
title: Regionális elérhetőség Azure Media Services | Microsoft Docs
description: Ez a cikk áttekintést nyújt a Microsoft Azure Media Services szolgáltatásairól és a szolgáltatás regionális elérhetőségéről.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012258"
---
# <a name="media-services-regional-availability"></a>Regionális elérhetőség Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. Tekintse meg a legújabb, [Media Services v3](../latest/media-services-overview.md)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Microsoft Azure Media Services (AMS) lehetővé teszi különböző videó- és hangtartalmak biztonságos feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő streamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

Az AMS világszerte több régióban működik, így rugalmasságot biztosít az alkalmazások létrehozási helyének megválasztásához. Ez a cikk áttekintést nyújt a Microsoft Azure Media Services szolgáltatásairól és a szolgáltatás regionális elérhetőségéről.

A teljes Azure globális infrastruktúrával kapcsolatos további információkért lásd: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

## <a name="ams-accounts"></a>AMS-fiókok

Az [Azure-termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) való használata annak megállapítására, hogy Media Services elérhető-e egy adott régióban.

## <a name="streaming-endpoints"></a>Streamvégpontok

A Media Services ügyfelei **standard** szintű streamvégpontot vagy **prémium** szintű streamvégpontot választhatnak.

|Name|Állapot|Region
|---|---|---|
|Standard|FE|Mind|
|Prémium|FE|Mind|

## <a name="live-encoding"></a>Live Encoding

Minden régióban elérhető, kivéve a következőket: Németország, Dél-Brazília, Nyugat-India, Dél-India és Közép-India.

## <a name="encoding-media-processors"></a>Médiafeldolgozók kódolása

Az AMS két igény szerinti kódolót nyújt: a **Media Encoder Standard** kódolót és a **Media Encoder Premium-munkafolyamatot**. További információkért olvassa el [az Azure igény szerinti médiakódolók áttekintését és összehasonlítását](media-services-encode-asset.md).

|Médiafeldolgozó neve|Állapot|Régiók
|---|---|---|
|Media Encoder Standard|FE|Mind|
|Media Encoder Premium-munkafolyamat|FE|Kína kivételével|

## <a name="analytics-media-processors"></a>Elemzési médiafeldolgozók

A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. További információk: [Az Azure Media Services Elemző áttekintése](./legacy-components.md)

> [!NOTE]
> Egyes elemzési adathordozó-processzorok ki lesznek vonva. A nyugdíjazási dátumokért tekintse meg az [örökölt összetevőkkel](legacy-components.md) foglalkozó témakört.

|Médiafeldolgozó neve|Állapot|Region
|---|---|---|
|Azure Media Face Detector|Előnézet|Mind|
|Azure Media Indexer|FE|Mind|
|Azure Media Motion Detector|Előnézet|Mind|
|Azure Media OCR|Előnézet|Mind|
|Azure Media Redactor|FE|Mind|
|Azure Media Video Thumbnails|Előnézet|Mind|

## <a name="protection"></a>Védelem

A Microsoft Azure Media Services lehetővé teszi a médiatartalmak védelmét attól a ponttól kezdve, ahogy az elhagyja a számítógépét, egészen a tároláson, a feldolgozáson és a továbbításon át. További információért olvassa el az [AMS-tartalmak védelmét](media-services-content-protection-overview.md) ismertető cikket.

|Titkosítás|Állapot|Régiók|
|---|---|---| 
|Tárolás|FE|Mind|
|AES-128-kulcsok|FE|Mind|
|FairPlay|FE|Mind|
|PlayReady|FE|Mind|
|Widevine|FE|Az összes Németország, szövetségi kormány és Kína kivételével.

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="reserved-units-rus"></a>Fenntartott egységek (RU-k)

A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

Minden régióban elérhető.

## <a name="reserved-unit-ru-type"></a>Fenntartott egység (RU) típusa

A Media Services fiók egy fenntartott egységhez van társítva, amely meghatározza, hogy milyen sebességgel fejeződik be a média feldolgozási feladatai. A következő fenntartott egység típusok közül választhat: S1, S2 vagy S3.

|RU típusának neve|Állapot|Régiók
|---|---|---|
|S1|FE|Mind|
|S2|FE|Mindenhol, kivéve Dél-Brazíliát és Nyugat-Indiát|
|S3|FE|Mindenhol, kivéve Nyugat-Indiát|

## <a name="next-steps"></a>Következő lépések

[Migrálás a Media Services v3-ba](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]