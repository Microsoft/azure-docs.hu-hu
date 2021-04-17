---
author: baanders
description: include file for Azure Digital Twins tutorials - prerequisites for the sample project
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 00d584690d37f1dcc47b785ef533abe888befec3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511772"
---
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban található lépések befejezéséhez először el kell látnia a következő előfeltételeket. 

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt **hozzon [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** létre egy ingyenes fiókot.

### <a name="get-required-resources"></a>A szükséges erőforrások lekérte

Az oktatóanyag befejezéséhez telepítse **[Visual Studio 2019-es,](https://visualstudio.microsoft.com/downloads/)16.5-ös** vagy újabb verzióját a fejlesztői gépen. Ha már telepítve van egy régebbi verzió, nyissa meg a *Visual Studio Installer* alkalmazást a számítógépen, és kövesse az utasításokat a telepítés frissítéséhez.

>[!NOTE]
> Győződjön meg arról, hogy Visual Studio 2019-es telepítése tartalmazza az **[Azure-fejlesztési számítási feladatot.](/dotnet/azure/configure-visual-studio)** Ez a számítási feladat lehetővé teszi, hogy az alkalmazások Azure-függvényeket tegyenek közzé, és egyéb Azure-fejlesztési feladatokat hajtsanak végre.

Az oktatóanyag egy C# nyelven írt mintaprojekten vezet. A minta itt található: [Azure Digital Twins teljes minták](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **A mintaprojektet** a mintahivatkozásra navigálva, majd a cím alatt található *Kód* tallózása gombra kattintva szerezze be a saját gépére. Ezzel a GitHub-adattárba fogja vinni a mintákat, amelyeket letölthet a *fájlba. A* ZIP-fájlhoz válassza a *Kód gombot,* majd *a ZIP letöltése gombot.*

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Képernyőkép a digital-twins-samples adattárról a GitHubon. A Kód gomb van kiválasztva, így egy kis párbeszédpanel jelenik meg, ahol a ZIP letöltése gomb ki van emelve." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Ezzel letölt egy *et. ZIP-mappát* a géphez a **következődigital-twins-samples-master.zip.** Bontsa ki a mappát, és bontsa ki a fájlokat.

### <a name="prepare-an-azure-digital-twins-instance"></a>Új Azure Digital Twins előkészítése

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
