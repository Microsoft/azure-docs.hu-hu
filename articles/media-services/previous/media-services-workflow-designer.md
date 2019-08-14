---
title: Speciális kódolási munkafolyamatok létrehozása a Munkafolyamat-tervezővel | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre speciális kódolási munkafolyamatokat a Munkafolyamat-tervezővel.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 246a1461973dba129a5b1d12be8a09703eb99267
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015976"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel  
## <a name="overview"></a>Áttekintés
A **munkafolyamat-tervező** egy olyan Windows asztali eszköz, amellyel egyéni munkafolyamatokat tervezhet és hozhat létre **Media Encoder Premium workflow**használatával történő kódoláshoz.
A Munkafolyamat-tervező eszköz erejével megtervezheti és létrehozhatja azokat a komplex munkafolyamatokat, amelyek a **Media Encoder Premium**szolgáltatásban futnak.  

A munkafolyamatok magukban foglalhatják az ügyfelek döntési logikáját és az elágazást a bemeneti forrásfájl tulajdonságai alapján. Overridable-tulajdonságokkal és dinamikus értékekkel rendelkező munkafolyamatokat hozhat létre, hogy a legbonyolultabb kódolási feladatok könnyen ismételhetők és testreszabhatók legyenek a felhőben.

A létrehozhatók például a következők:

* Döntési alapú munkafolyamatok, amelyek megvizsgálják a forrás tartalmát a feloldáshoz, és csak a kívánt kimeneti számokat kódolják.  Ez hasznos lehet a forrás tartalmának véletlen skálázásával előállított, hulladékká vált számok eltávolításával.
* Több bemeneti fájl is használható a feliratok, átfedések és a tartalom összefűzéséhez. 

Ez az eszköz a [közzétett](media-services-workflow-designer.md#existing_workflows)munkafolyamatok bármelyikének módosítására is használható. 

> [!NOTE]
> A Munkafolyamat-tervező eszköz másolatának beszerzéséhez vegye fel mepd@microsoft.coma kapcsolatot a következővel:.

A munkafolyamat-fájl létrehozása után feltölthetők egy eszközként, majd a médiafájlok kódolására használhatók. A **Media Encoder Premium workflow** **.net**-tel való kódolásával kapcsolatos információkért lásd: [speciális kódolás a Media Encoder Premium workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Meglévő munkafolyamatok módosítása
Az alapértelmezett [közzétett](media-services-workflow-designer.md#existing_workflows) munkafolyamatokat a Designer eszközzel lehet módosítani. Az alapértelmezett munkafolyamat-fájlok [itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)olvashatók be. A mappa a fájlok leírását is tartalmazza.

A következő videók bemutatják, hogyan használhatja a tervezőt.

### <a name="day-1--getting-started"></a>1\. nap – Első lépések
1\. nap videó:

* A Designer áttekintése
* Alapszintű munkafolyamatok – ""Helló világ!"alkalmazás"
* Több kimeneti MP4-fájl létrehozása Azure Media Services streamingtel való használatra

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2\. napon
A 2. nap videó a következőket tartalmazza:

* Különböző forrásfájl-forgatókönyvek – hang-kezelő
* Speciális logikával rendelkező munkafolyamatok
* Gráf szakaszai

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3\. nap
A 3. nap videó a következőket tartalmazza:

* A munkafolyamatokban/tervrajzokon belüli parancsfájlok
* Az aktuális kódolóval kapcsolatos korlátozások
* Q & A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Ha támogatásra van szüksége, vagy kérdése van az egyéni munkafolyamatok létrehozásával kapcsolatban a Munkafolyamat-tervező eszközben, küldjön e-mailt a mepd@microsoft.comkövetkező címre:.

## <a name="see-also"></a>Lásd még:
[Azure Premium Encoder – munkafolyamat-tervezői képzés – videók](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

