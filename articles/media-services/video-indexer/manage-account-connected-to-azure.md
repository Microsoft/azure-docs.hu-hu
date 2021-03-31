---
title: Video Indexer fiók kezelése
titleSuffix: Azure Media Services
description: Útmutató az Azure-hoz csatlakoztatott Video Indexer-fiók kezeléséhez.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898443"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Az Azure-hoz csatlakoztatott Video Indexer-fiók kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakoztatott Video Indexer fiókot.

> [!NOTE]
> A jelen témakörben ismertetett fiók-konfigurációs módosítások elvégzéséhez Video Indexer fiók tulajdonosának kell lennie.

## <a name="prerequisites"></a>Előfeltételek

Csatlakoztassa Video Indexer-fiókját az Azure-hoz az [Azure-hoz csatlakoztatva](connect-to-azure.md).

Ügyeljen arra, hogy kövesse az [Előfeltételek](connect-to-azure.md#prerequisites-for-connecting-to-azure) és a felülvizsgálati [szempontokat](connect-to-azure.md#azure-media-services-considerations) a cikkben.

## <a name="examine-account-settings"></a>Fiókbeállítások vizsgálata

Ez a szakasz a Video Indexer fiókjának beállításait vizsgálja.

Beállítások megtekintése:

1. Kattintson a jobb felső sarokban található felhasználó ikonra, és válassza a **Beállítások** lehetőséget.

    ![Beállítások a Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. A **Beállítások** lapon válassza a **fiók** fület.

Ha a video Indexer-fiókja csatlakozik az Azure-hoz, a következő dolgokat látja:

* Az alapul szolgáló Azure Media Services fiók neve.
* A-t futtató és várólistára helyezett indexelési feladatok száma.
* A lefoglalt fenntartott egységek száma és típusa.

Ha a fióknak valamilyen módosításra van szüksége, a **Beállítások** lapon láthatja a fiók konfigurációjának megfelelő hibákat és figyelmeztetéseket. Az üzenetek a Azure Portal pontos helyeire mutató hivatkozásokat tartalmaznak, amelyekben módosítani kell a módosításokat. További információ: a következő, [hibákkal és figyelmeztetésekkel](#errors-and-warnings) foglalkozó szakasz.

## <a name="repair-the-connection-to-azure"></a>Az Azure-ral létesített kapcsolat kijavítása

A [video Indexer](https://www.videoindexer.ai/) lap **kapcsolatok frissítése Azure Media Services** párbeszédpanelén a rendszer megkéri, hogy adja meg a következő beállítások értékeit:

|Beállítás|Leírás|
|---|---|
|Azure-előfizetés azonosítója|Az előfizetés-azonosító a Azure Portalból kérhető le. A bal oldali panelen kattintson a **minden szolgáltatás** elemre, és keressen rá az "előfizetések" kifejezésre. Válassza az **előfizetések** lehetőséget, majd válassza ki a kívánt azonosítót az előfizetések listájából.|
|Azure Media Services erőforráscsoport neve|Azon erőforráscsoport neve, amelyben létrehozta a Media Services fiókot.|
|Alkalmazásazonosító|Az ehhez a Video Indexer-fiókhoz létrehozott Azure AD-alkalmazás azonosítója (a megadott Media Services fiókhoz tartozó engedélyekkel). <br/><br/>Az alkalmazás AZONOSÍTÓjának beszerzéséhez navigáljon Azure Portal. A Media Services fiók alatt válassza ki a fiókját, és lépjen az **API-hozzáférés** elemre. Válassza a **Kapcsolódás az Media Services API-hoz az egyszerű szolgáltatásnév**  ->  **Azure ad alkalmazás** lehetőséget. Másolja ki a megfelelő paramétereket.|
|Alkalmazás kulcsa|A fent megadott Media Services-fiókhoz társított Azure AD-alkalmazás kulcsa. <br/><br/>Az alkalmazás kulcsának beszerzéséhez navigáljon Azure Portal. A Media Services fiók alatt válassza ki a fiókját, és lépjen az **API-hozzáférés** elemre. Válassza a **Kapcsolódás az Media Services API-hoz lehetőséget az**  ->  **alkalmazás**-  ->  **tanúsítványok & titkok** kezelése szolgáltatással. Másolja ki a megfelelő paramétereket.|

## <a name="autoscale-reserved-units"></a>A fenntartott egységek automatikus skálázása

A **Beállítások** lapon állíthatja be a Media szolgáltatás számára fenntartott egységek (ru) automatikus skálázását. Ha a lehetőség **be van kapcsolva**, lefoglalhatja az RUS maximális számát, és biztos lehet benne, hogy a video Indexer automatikusan leállítja/elindítja az RUS-t. Ha ezt a lehetőséget választja, nem számítunk fel extra összeget a tétlen időpontig, de ne várjon, amíg az indexelési feladatok hosszú időt töltenek be, amikor az indexelési terhelés magas.

Az autoskálázás nem méretezi az 1 RU-t vagy az Media Services fiók alapértelmezett korlátját. A korlát növeléséhez hozzon létre egy szolgáltatási kérelmet. A kvótákkal és korlátozásokkal, valamint a támogatási jegyek megnyitásával kapcsolatos információkért lásd: [kvóták és korlátozások](../../media-services/previous/media-services-quotas-and-limitations.md).

![A fenntartott egységek méretezése Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Ha a fióknak valamilyen módosításra van szüksége, a **Beállítások** lapon megtekintheti a fiók konfigurációjának megfelelő hibákat és figyelmeztetéseket. Az üzenetek a Azure Portal pontos helyeire mutató hivatkozásokat tartalmaznak, amelyekben módosítani kell a módosításokat. Ez a szakasz további részleteket tartalmaz a hibákkal és a figyelmeztető üzenetekkel kapcsolatban.

* EventGrid

    Regisztrálnia kell a EventGrid erőforrás-szolgáltatót a Azure Portal használatával. A [Azure Portal](https://portal.azure.com/)lépjen az **előfizetések** > [előfizetés] > **ResourceProviders**  >  **Microsoft. EventGrid** elemre. Ha nincs **regisztrálva** állapotban, válassza a **regisztráció** lehetőséget. A regisztráció néhány percet vesz igénybe.

* Streamvégpont

    Győződjön meg arról, hogy az alapul szolgáló Media Services fiók rendelkezik az alapértelmezett **folyamatos átviteli végponttal** egy elindított állapotban. Ellenkező esetben nem tekintheti meg a videókat ebből a Media Services fiókból vagy Video Indexerból.

* Fenntartott médiaegységek

    A videók indexeléséhez le kell foglalni a Media szolgáltatás számára fenntartott egységeket a Media Service-erőforráshoz. Az optimális indexelési teljesítmény érdekében javasoljuk, hogy legalább 10 S3 fenntartott egységet foglaljon le. A díjszabással kapcsolatos információkért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.

## <a name="next-steps"></a>Következő lépések

Programozott módon használhatja a próbaverziós fiókját vagy az Azure-hoz kapcsolódó Video Indexer fiókokat a következő témakör utasításait követve: API-k [használata](video-indexer-use-apis.md).

Használja ugyanazt az Azure AD-felhasználót, amelyet az Azure-hoz való csatlakozáskor használt.
