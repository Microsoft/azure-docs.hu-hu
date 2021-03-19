---
title: Nyelvi modell testreszabása Video Indexer-Azure-ban
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Video Indexer nyelvi modelljéről és testreszabásáról.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047127"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Nyelvi modell testreszabása Video Indexer

Video Indexer támogatja az automatikus beszédfelismerést a Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)való integráción keresztül. A nyelvi modellt testreszabhatja úgy, hogy feltölti az adaptációs szöveget, azaz azt a tartományt, amelynek a szókincsét szeretné a motorhoz igazítani. A modell betanítása után a rendszer az adaptációs szövegben szereplő új szavakat fogja felismerni, feltételezve az alapértelmezett kiejtést, és a nyelvi modell új, valószínű szavakat fog tanulni. Az egyéni nyelvi modellek angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, Orosz, portugál, hindi és koreai nyelveken támogatottak. 

Vegyük például a "Kubernetes" (az Azure Kubernetes-szolgáltatás kontextusában) kifejezéseket. Mivel a szó új Video Indexer, "Közösségek" néven ismert. A modellt úgy kell betanítania, hogy felismerje a következőt: "Kubernetes". Más esetekben a szavak léteznek, de a nyelvi modell nem vár rá, hogy egy bizonyos kontextusban megjelenjenek. Például a "Container Service" nem egy kétszavas sorozat, amelyet a nem speciális nyelvi modell felismer a szavak adott csoportjaként.

Lehetősége van a szavak kontextus nélküli feltöltésére egy szövegfájlban található listában. Ez részleges alkalmazkodásnak minősül. Azt is megteheti, hogy a jobb alkalmazkodás érdekében feltölti a tartalommal kapcsolatos dokumentációt vagy mondatokat.

Az egyéni nyelvi modellek létrehozásához és szerkesztéséhez használhatja a Video Indexer API-kat vagy a webhelyet, a jelen témakör [következő lépések](#next-steps) szakaszának témakörök szakaszában leírtak szerint.

## <a name="best-practices-for-custom-language-models"></a>Ajánlott eljárások egyéni nyelvi modellekhez

Video Indexer a Word-kombinációk valószínűsége alapján tanul, így a legmegfelelőbb módon sajátíthatja el:

* Adjon meg elég valós példát a mondatok kimondása céljából.
* Soronként csak egy mondatot helyezzen el. Ellenkező esetben a rendszer a mondatok közötti valószínűségeket fogja megtanulni.
* Nem kell egy szót beírni mondatként, hogy növelje másokkal a szót, de a rendszer a legjobbat a teljes mondatok alapján tanulja meg.
* Ha lehetséges, új szavakat vagy mozaikszavakat kell megadnia, egy teljes mondatban annyi példát kell használnia, hogy a lehető legtöbb kontextust biztosítson a rendszernek.
* Próbáljon meg több adaptációs beállítást létrehozni, és nézze meg, hogyan működnek.
* Ne ismételje meg többször a pontos mondatot. A bemenet további részeit is létrehozhatja.
* Kerülje a nem gyakori szimbólumok (~, # @% &) elkerülését, mivel azok el lesznek vetve. A megjelenő mondatok is elvesznek.
* Ne helyezze túl nagy mennyiségű bemenetet, például több százezer mondatot, mert ezzel a művelettel felhígította a növelés hatását.

## <a name="next-steps"></a>Következő lépések

[Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)

[Nyelvi modell testreszabása a webhely használatával](customize-language-model-with-website.md)
