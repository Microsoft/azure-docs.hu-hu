---
title: Azure Media Services-fiók létrehozása az Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével Azure Media Services-fiókot hozhat létre az Azure Portal használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011918"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Media Services fiók létrehozása a Azure Portal használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Az Azure Portalon gyorsan létrehozhat egy Azure Media Services- (AMS-) fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben. Amikor létrehoz egy Media Services fiókot, létrehoz egy társított Storage-fiókot is (vagy egy meglévőt használ). Egy Media Services-fiók törlésekor a kapcsolódó tárfiókban található blobok nem törlődnek.

A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárfiókokat és a Media Services-fiókot azonos helyen használni a további késés és kimenő adatforgalomból fakadó költségek elkerülése végett.

Ez a cikk bemutatja, hogyan hozhat létre egy Media Services fiókot a Azure Portal használatával.

> [!NOTE]
> A különböző régiókban található Azure Media Services-funkciók elérhetőségével kapcsolatos további információkért lásd: [AMS-szolgáltatások régiók között](availability-regions-v-2.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>AMS-fiók létrehozása

A jelen szakaszban ismertetett lépések bemutatják az AMS-fiók létrehozásának módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson **az + erőforrás létrehozása**  >  **adathordozó**-  >  **Media Services** elemre.
3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.

   1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
   2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
   3. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információ [itt](../../azure-resource-manager/management/overview.md#resource-groups).
   4. A **Location** (Hely) részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó médiafájlokat és metaadat-bejegyzéseket. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. 
   5. A **Storage-fiók** területen válassza ki azt a Storage-fiókot, amely a Media Services-fiókban lévő médiatartalom blob-tárolóját biztosítja. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy másik tárfiókot. A újonnan létrehozott tárfiók is ugyanahhoz a régióhoz fog tartozni. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.

       További információkat a tárhelyről [itt](../../storage/common/storage-introduction.md) talál.
   6. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
4. Kattintson az űrlap alján található **Létrehozás** lehetőségre.

    A fiók sikeres létrehozása után betöltődik az áttekintési oldal. A streamvégpont-táblázatban a fiók alapértelmezett streamvégpontja **Leállítva** állapotban lesz. 

    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
   
## <a name="to-manage-your-ams-account"></a>Az AMS-fiók kezelése

Az AMS-fiók kezeléséhez (például az AMS API-hoz való programozott kapcsolódáshoz, videók feltöltéséhez, objektumok kódolásához, tartalomvédelem konfigurálásához, a feladatok előrehaladásának figyeléséhez) válassza a portál bal oldalán lévő **Beállítások** elemet. A **Beállítások** területen keresse meg az egyik elérhető panelt (például: **API-hozzáférés**, **Objektumok**, **Feladatok**, **Tartalomvédelem**).

## <a name="next-steps"></a>Következő lépések

Most már feltölthet fájlokat AMS-fiókjába. További információk: [Fájlok feltöltése](media-services-portal-upload-files.md).

Ha programozott módon szeretné elérni az AMS API-t, lásd [az Azure Media Services Azure AD-hitelesítéssel történő elérését](media-services-use-aad-auth-to-access-ams-api.md) ismertető cikket.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
