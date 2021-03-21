---
title: Streaming-végpontok méretezése a Azure Portalsal | Microsoft Docs
description: Ez az oktatóanyag végigvezeti a folyamatos átviteli végpontok és a Azure Portal skálázásának lépésein.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009751"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>A streamvégpont méretezése az Azure Portal használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. A streaming Endpoint types és a CDN konfigurációval kapcsolatos további információkért tekintse meg az [adatfolyam-végpont áttekintése](media-services-streaming-endpoints-overview.md) című témakört.
 
Ez a témakör bemutatja, hogyan méretezheti a streaming-végpontokat.

További információ a díjszabásról: [Media Services díjszabása](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Streaming-végpontok méretezése

A folyamatos átviteli egységek számának módosításához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** ablakban válassza a **folyamatos átviteli végpontok** lehetőséget.
3. Kattintson a méretezni kívánt streaming-végpontra. 

    > [!NOTE] 
    > Csak a **prémium** szintű adatfolyam-végpontokat lehet méretezni.

4. Mozgassa a csúszkát a folyamatos átviteli egységek számának megadásához.

    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

