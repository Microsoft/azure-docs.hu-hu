---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt előfeltételei
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103621964"
---
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez először végre kell hajtania a következő előfeltételeket. 

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt **hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

### <a name="get-required-resources"></a>Szükséges erőforrások beolvasása

Az oktatóanyag elvégzéséhez **telepítse a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), 16,5-es vagy újabb verzióját** a fejlesztői gépére. Ha már telepítve van egy régebbi verziója, megnyithatja a *Visual Studio Installer* alkalmazást a gépen, és az utasításokat követve frissítheti a telepítést.

>[!NOTE]
> Győződjön meg arról, hogy a Visual Studio 2019 telepítése magában foglalja az **[Azure-fejlesztési számítási feladatokat](/dotnet/azure/configure-visual-studio)**. Ez a munkaterhelés lehetővé teszi az alkalmazások számára az Azure functions közzétételét és más Azure-fejlesztési feladatok elvégzését.

Az oktatóanyagot C# nyelven írt minta projekt vezérli. A minta itt található: [Azure digitális Twins végpontok közötti minták](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Szerezze be a minta projektet** a gépen a minta hivatkozásra való navigálással, majd a cím alatt található *tallózási kód* gombra kattintva. Ekkor megnyílik a minták GitHub-tárháza, amelyet a-ként tölthet le *. ZIP* -t a *Code (kód* ) gomb kiválasztásával és a *zip letöltésével*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="A Digital-Twins-Samples tárház áttekintése a GitHubon. A kód gomb be van jelölve, és létrehoz egy kis párbeszédpanelt, ahol ki van emelve a ZIP-Letöltés gomb." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Ezzel letölti a-t *. A ZIP* -mappát **digital-twins-samples-master.zipként**. Bontsa ki a mappát, és bontsa ki a fájlokat.

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány előkészítése

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
