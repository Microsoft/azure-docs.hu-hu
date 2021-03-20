---
title: BLOB Storage-események küldése webes végpontnak – sablon
description: A blob Storage-fiók létrehozásához használja a Azure Event Grid és egy Azure Resource Manager sablont, és fizessen elő eseményeket. Küldje el az eseményeket egy webhookba. "
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: bfaee324f3e46f64fd4ad0d8b7e1240331b56c27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92093650"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-by-using-an-arm-template"></a>Gyors útmutató: blob Storage-események átirányítása a webes végpontba ARM-sablon használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben egy Azure Resource Manager sablont (ARM-sablont) használ egy blob Storage-fiók létrehozásához, előfizethet az adott blob-tároló eseményeire, és elindítja az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

### <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A Blob Storage-eseményekre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

    [Üzembe helyezés az Azure-ban](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-event-grid-subscription-and-storage/) közül származik.

:::code language="json" source="~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [**Microsoft. EventGrid/systemTopics**](/azure/templates/microsoft.eventgrid/systemtopics): hozzon létre egy rendszertémakört a Storage-fiókhoz megadott névvel.
* [**Microsoft. EventGrid/systemTopics/eventSubscriptions**](/azure/templates/microsoft.eventgrid/systemtopics/eventsubscriptions): hozzon létre egy Azure Event Grid-előfizetést a rendszertémakörhöz.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi hivatkozásra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Adja meg a **végpontot**: adja meg a webalkalmazás URL-címét, és adja hozzá `api/updates` a Kezdőlap URL-címét.
3. Válassza a **vásárlás** lehetőséget a sablon telepítéséhez.

  A Azure Portal a sablon üzembe helyezéséhez használja a rendszer. Használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid&pageNumber=1&sort=Popular)találhat további Azure Event Grid sablon mintákat.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

![Előfizetési esemény megtekintése](./media/blob-event-quickstart-portal/view-subscription-event.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak.

A Blob Storage-hoz egy eseményt egy fájl feltöltésével aktiválhat. A fájlnak nem kell tartalommal rendelkeznie. A cikkek feltételezik, hogy van egy testfile.txt fájlja, de bármilyen fájlt használhat.

Amikor feltölti a fájlt az Azure Blob Storage-ba, Event Grid üzenetet küld a feliratkozáskor konfigurált végpontnak. Az üzenet JSON formátumú, és egy vagy több eseményből álló tömböt tartalmaz. A következő példában a JSON-üzenet egyetlen eseményt tartalmazó tömböt tartalmaz. Tekintse meg a webalkalmazását, és észreveheti, hogy az fogadott egy blob által létrehozott eseményt.

![Eredmények megtekintése](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [törölje az erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Következő lépések

Azure Resource Manager-sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure Resource Manager dokumentációja](../azure-resource-manager/index.yml)
* [Erőforrások definiálása Azure Resource Manager-sablonokban](/azure/templates/)
* [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
