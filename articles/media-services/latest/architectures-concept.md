---
title: Media Services-architektúrák
description: Ez a cikk a Media Services architektúráit ismerteti.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98891488"
---
# <a name="media-services-architectures"></a>Media Services-architektúrák

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Digitális média élő közvetítése

Az élő adatfolyam-megoldás lehetővé teszi a videók valós idejű rögzítését és valós idejű közvetítését a fogyasztóknak, például a streaming interjúk, a konferenciák és a sportesemények online megszervezése. Ebben a megoldásban a videót egy videokamera rögzíti, és egy csatorna bemeneti végpontjának küldi el. A csatorna fogadja az élő bemeneti streamet, és elérhetővé teszi a streaming-végponton keresztül egy webböngészőhöz vagy egy mobil alkalmazáshoz. A csatorna előzetes figyelési végpontot is biztosít, amely a további feldolgozás és a kézbesítés előtt előnézeti és érvényesíti az adatfolyamot. A csatorna rögzítheti és tárolhatja a betöltött tartalmat a későbbi továbbítás érdekében (igény szerinti videó).

Ez a megoldás az Azure által felügyelt szolgáltatásokra épül: Media Services és Content Delivery Network. Ezek a szolgáltatások magas rendelkezésre állású, javított és támogatott környezetekben futnak, ami lehetővé teszi, hogy a környezetben futtatott környezet helyett a megoldásra koncentráljon.

Lásd: [élő streaming digitális médiatartalmak](/azure/architecture/solution-ideas/articles/digital-media-live-stream) az Azure Architecture Centerben.

## <a name="video-on-demand-digital-media"></a>Igény szerinti videó, digitális média

Egy alapszintű, igény szerinti videofilm-megoldás, amely lehetővé teszi a rögzített videotartalom, például filmek, hírforrások, sport szegmensek, oktatói videók és ügyfélszolgálati oktatóanyagok továbbítását bármely videóra alkalmas Endpoint-eszközhöz, mobil alkalmazáshoz vagy asztali böngészőhöz. A videofájlok fel lesznek töltve az Azure Blob Storage-ba, és a többszörös sávszélességű szabványos formátumba vannak kódolva, majd az összes jelentős Adaptív átviteli protokollon (HLS, MPEG-DASH, Smooth) keresztül terjeszthetők az Azure Media Player-ügyfélre.

Ez a megoldás az Azure által felügyelt szolgáltatásokra épül: Blob Storage, Content Delivery Network és Azure Media Player. Ezek a szolgáltatások magas rendelkezésre állású, javított és támogatott környezetekben futnak, ami lehetővé teszi, hogy a környezetben futtatott környezet helyett a megoldásra koncentráljon.

Az Azure Architecture Centerben tekintse [meg az igény szerinti digitális médiát](/azure/architecture/solution-ideas/articles/digital-media-video) .

## <a name="gridwich-media-processing-system"></a>Gridwich médiatartalom-feldolgozó rendszer

A Gridwich rendszer az Azure-beli multimédia-eszközök feldolgozására és továbbítására vonatkozó ajánlott eljárásokat testesíti meg. Bár a Gridwich-rendszer adathordozó-specifikus, az üzenetek feldolgozására és az eseményekre vonatkozó keretrendszer bármely állapot nélküli esemény-feldolgozási munkafolyamatra alkalmazható.

Lásd: [Gridwich Media Processing System](/azure/architecture/reference-architectures/media-services/gridwich-architecture) in the Azure Architecture Center.

## <a name="next-steps"></a>Következő lépések

> [Az Azure Media Services áttekintése](media-services-overview.md)