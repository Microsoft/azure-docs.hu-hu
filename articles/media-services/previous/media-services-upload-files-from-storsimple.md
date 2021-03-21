---
title: Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből | Microsoft Docs
description: Ez a cikk rövid áttekintést nyújt az Azure StorSimple Data Managerről. A cikk olyan oktatóanyagokra is hivatkozik, amelyekből megtudhatja, hogyan gyűjthet ki adatokat a StorSimple-ből, és töltheti fel azokat objektumként egy Azure Media Services-fiókba.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0521904f0ed46b4c5309e5f9df980b1cd7d7d858
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009028"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)
>
> 
> Az Azure StorSimple Data Manager jelenleg privát előzetes verzióban érhető el. 
> 

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az eszköz tartalmazhatja a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a lezárt feliratok fájljait (valamint a fájlokra vonatkozó metaadatokat is). A fájlok feltöltése után a rendszer biztonságosan tárolja a tartalmat a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Az [Azure StorSimple](../../storsimple/index.yml) a felhőalapú tárolót a helyszíni tároló kiterjesztéseként használja, és automatikusan rétegzi az adatokat a helyszíni és a felhőalapú tárolók közt. A StorSimple-eszköz deduplikálja és tömöríti az adatokat, mielőtt a felhőbe küldené azokat, így nagyon hatékony eszköz a nagyméretű fájlok a felhőbe továbbításához. A [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) szolgáltatás nyújtotta API-k segítségével az adatok kinyerhetők a StorSimple-ből, és megjeleníthetők AMS-objektumokként.

## <a name="get-started"></a>Bevezetés

1. [Hozzon létre egy Media Services-fiókot](media-services-portal-create-account.md), amelybe az objektumokat át szeretné vinni.
2. Regisztráljon a Data Manager előzetes verziójára a [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) című cikkben leírtak szerint.
3. Hozzon létre egy StorSimple Data Manager-fiókot.
4. Hozzon létre egy adatátalakítási feladatot, amely a futtatásakor adatokat gyűjt egy StorSimple-eszközről, és továbbítja azokat objektumokként egy AMS-fiókba. 

    Amikor a feladat futni kezd, a rendszer létrehoz egy tárolási sort. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével. A sor használatával megállapíthatja, hogy mikor készül el egy adott objektum, és meghívhatja és futtathatja rajta a kívánt Media Services-műveletet. Például a sor használatával aktiválhat egy Azure Functions-függvényt, amely tartalmazza a szükséges Media Services-kódot.

## <a name="see-also"></a>Lásd még

[A .NET SDK használata a feladatok elindításához a Data Manager](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).
