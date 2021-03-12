---
title: Microsoft Azure Media Services gyakori forgatókönyvek | Microsoft Docs
description: Ez a cikk áttekintést nyújt Microsoft Azure Media Services forgatókönyvekről.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: cb7500489cc4516b8cf44dd029c0831103dc53a8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007933"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Gyakori forgatókönyvek Microsoft Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. Tekintse meg a legújabb, [Media Services v3](../latest/media-services-overview.md)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Microsoft Azure Media Services (AMS) lehetővé teszi különböző videó- és hangtartalmak biztonságos feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő streamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

Ez a cikk a tartalom élő vagy igény szerinti továbbításának általános forgatókönyveit mutatja be.

## <a name="overview"></a>Áttekintés

### <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com).
* Egy Azure Media Services-fiók. További információ: [Fiók létrehozása](media-services-portal-create-account.md)
* A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

    Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a streamvégpontnak **Fut** állapotban kell lennie.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Az AMS OData-modellen alapuló fejlesztések során leggyakrabban használt objektumok

A következő kép a Media Services OData-modellen alapuló fejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

[![Diagram, amely a leggyakrabban használt objektumokat mutatja be a Azure Media Services objektum adatmodelljével való fejlesztés során.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

A teljes modellt [itt](https://media.windows.net/API/$metadata?api-version=2.15) tekintheti meg.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Tartalom védelme a tárolón és folyamatos médiatovábbítás tisztán (titkosítatlanul)

![VoD-munkafolyamat](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe.

    A tárolási titkosítási lehetőség alkalmazása az objektumra, hogy megvédje a tartalmat a feltöltés során, és a tárhelyen tárolt adatok használata esetén is ajánlott.

1. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen.

    A tárolási titkosítási lehetőség alkalmazása a kimeneti eszközre a tartalom nyugalmi állapotának biztosítása érdekében ajánlott.

1. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás használ).

    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia **kell** az adategység továbbítási házirendjét.
1. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
1. Továbbítsa a közzétett tartalmat.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Tartalom védelme a tárolón és dinamikusan titkosított folyamatos médiatovábbítás

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe. Alkalmazzon az adategységen tárolótitkosítást.
1. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen. Alkalmazzon a kimeneti adategységen tárolótitkosítást.
1. Hozzon létre egy titkosítási tartalomkulcsot az adategységhez, amelyet a lejátszás során dinamikusan titkosítani kíván.
1. Konfigurálja a tartalomkulcs-engedélyezési házirendet.
1. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás és a dinamikus titkosítás használ).
1. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
1. Továbbítsa a közzétett tartalmat.

## <a name="deliver-progressive-download"></a>Progresszív letöltés továbbítása

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe.
1. A kódolás kimenete egyetlen MP4-fájl legyen.
1. Tegye közzé az adategységet egy OnDemand- vagy SAS-kereső létrehozásával. Az SAS-kereső használata esetén a tartalmat az Azure-blobtárolóból lehet letölteni. Nem kell megkezdett állapotban lennie a folyamatos átviteli végpontoknak.
1. Töltse le fokozatosan a tartalmat.

## <a name="delivering-live-streaming-events"></a>Események élő közvetítése

1. Élő tartalmakat dolgozhat fel különböző élő streamelési protokollok (például RTMP vagy Smooth Streaming) használatával.
1. A streamet adaptív sávszélességűvé kódolhatja (opcionális).
1. Megtekintheti az élő stream előnézetét.
1. Tartalom továbbítása a használatával:
    1. Gyakori folyamatos átviteli protokollok (például MPEG DASH, Smooth, HLS) közvetlenül az ügyfeleknek
    1. Egy Content Delivery Network (CDN) számára további terjesztéshez, vagy
    1. Rögzítse és tárolja a betöltött tartalmat későbbi továbbításra (igény szerinti videó).

Élő streameléskor a következő útvonalak egyikét választhatja:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Helyszíni kódolóktól többszörös átviteli sebességű adatfolyamot fogadó (áteresztő) csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az **áteresztő** munkafolyamatban.

![Az "áteresztő" munkafolyamatban részt vevő M S platform főbb részeit ábrázoló diagram.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Tovább információk: [Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Az Azure Media Services segítségével élő kódolásra képes csatornák használata

Az alábbi ábrán az AMS platform azon főbb részei láthatók, amelyek az élő közvetítési munkafolyamatban részt vesznek, ahol a csatorna engedélyezve van a Media Services élő kódolásra.

![Élő munkafolyamat](./media/scenarios-and-availability/media-services-live-streaming-new.png)

További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

## <a name="consuming-content"></a>Tartalmak felhasználása

Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek.

## <a name="enabling-azure-cdn"></a>Az Azure CDN engedélyezése

A Media Services támogatja az Azure CDN-integrációt. További információk az Azure CDN engedélyezéséről: [Adatfolyam-továbbítási végpontok kezelése egy Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)

## <a name="scaling-a-media-services-account"></a>Media Services-fiók méretezése

Az AMS-ügyfelek méretezhetik a streamvégpontokat, a médiafeldolgozást és a tárolást az AMS-fiókjukon.

* A Media Services ügyfelei **standard** szintű streamvégpontot vagy **prémium** szintű streamvégpontot választhatnak. A **standard** streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Ugyanazokkal a jellemzőkkel rendelkezik, mint a **prémium** szintű streamvégpontok, és automatikusan méretezi a kimenő sávszélességet.

    A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. A **prémium** szintű streamvégpontok méretezéséről további információt a [streamvégpontok méretezését](media-services-portal-scale-streaming-endpoints.md) ismertető témakörben talál.

* A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2** vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

    A fenntartott egység típusának meghatározása mellett megadhatja, hogy a fiók a **fenntartott egységekkel** (RUs) legyen kiépítve. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

    > [!NOTE]
    > A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

    További információkért olvassa el a [médiafeldolgozás méretezését](media-services-portal-scale-media-processing.md) ismertető cikket.

* A Media Services-fiókját tárfiókok hozzáadásával is méretezheti. Minden tárfiók legfeljebb 500 TB kapacitású lehet. További információkért olvassa el a [tárfiókok kezelését](./media-services-managing-multiple-storage-accounts.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

[Migrálás a Media Services v3-ba](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]