---
title: Speciális kódolási munkafolyamatok létrehozása a Munkafolyamat-tervezővel | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre speciális kódolási munkafolyamatokat a Munkafolyamat-tervezővel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 744d302afd21e6521fb17bf7bef6e68d21fb8372
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639395"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés
A **munkafolyamat-tervező** egy olyan Windows asztali eszköz, amellyel egyéni munkafolyamatokat tervezhet és hozhat létre **Media Encoder Premium workflow** használatával történő kódoláshoz.
A Munkafolyamat-tervező eszköz erejével megtervezheti és létrehozhatja azokat a komplex munkafolyamatokat, amelyek a **Media Encoder Premium** szolgáltatásban futnak.  

A munkafolyamatok magukban foglalhatják az ügyfelek döntési logikáját és az elágazást a bemeneti forrásfájl tulajdonságai alapján. Overridable-tulajdonságokkal és dinamikus értékekkel rendelkező munkafolyamatokat hozhat létre, hogy a legbonyolultabb kódolási feladatok könnyen ismételhetők és testreszabhatók legyenek a felhőben.

A létrehozhatók például a következők:

* Döntési alapú munkafolyamatok, amelyek megvizsgálják a forrás tartalmát a feloldáshoz, és csak a kívánt kimeneti számokat kódolják.  Ez hasznos lehet a forrás tartalmának véletlen skálázásával előállított, hulladékká vált számok eltávolításával.
* Több bemeneti fájl is használható a feliratok, átfedések és a tartalom összefűzéséhez. 

Ez az eszköz a [közzétett munkafolyamatok](media-services-workflow-designer.md#existing_workflows)bármelyikének módosítására is használható. 

> [!NOTE]
> A Munkafolyamat-tervező eszköz másolatának beszerzéséhez vegye fel a kapcsolatot a következővel: mepd@microsoft.com .

A munkafolyamat-fájl létrehozása után feltölthetők egy eszközként, majd a médiafájlok kódolására használhatók. A **Media Encoder Premium workflow** **.net**-tel való kódolásával kapcsolatos információkért lásd: [speciális kódolás a Media Encoder Premium workflow](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Meglévő munkafolyamatok módosítása
Az alapértelmezett [közzétett munkafolyamatokat](media-services-workflow-designer.md#existing_workflows) a Designer eszközzel lehet módosítani. Az alapértelmezett munkafolyamat-fájlok [itt](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md)olvashatók be. A mappa a fájlok leírását is tartalmazza.

A következő videók bemutatják, hogyan használhatja a tervezőt.

### <a name="day-1--getting-started"></a>1. nap – Első lépések
1. nap videó:

* A Designer áttekintése
* Alapszintű munkafolyamatok – "Hello World"
* Több kimeneti MP4-fájl létrehozása Azure Media Services streamingtel való használatra

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2. nap
A 2. nap videó a következőket tartalmazza:

* Különböző forrásfájl-forgatókönyvek – hang-kezelő
* Speciális logikával rendelkező munkafolyamatok
* Gráf szakaszai

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3. nap
A 3. nap videó a következőket tartalmazza:

* A munkafolyamatokban/tervrajzokon belüli parancsfájlok
* Az aktuális kódolóval kapcsolatos korlátozások
* Q&A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Azure Premium Encoder – munkafolyamat-tervezői képzés – videók](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

