---
title: ARM-sablon használata az Azure IoT Hub, a Storage-fiók és az útválasztási üzenetek közzétételéhez
description: ARM-sablon használata az Azure IoT Hub, a Storage-fiók és az útválasztási üzenetek közzétételéhez
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981289"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Gyors útmutató: Azure IoT Hub és Storage-fiók üzembe helyezése ARM-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy olyan IoT Hub létrehozására, amely üzeneteket továbbít az Azure Storage-ba, valamint egy Storage-fiókot az üzenetek tárolásához. Miután manuálisan hozzáadta a virtuális IoT-eszközt a központhoz az üzenetek elküldéséhez, a kapcsolati információkat egy  *ARM-Read-Write* nevű alkalmazásban konfigurálja, hogy üzeneteket küldjön az eszközről a központba. A hub úgy van konfigurálva, hogy az adott hubhoz küldött üzenetek automatikusan a Storage-fiókhoz legyenek irányítva. A rövid útmutató végén megnyithatja a Storage-fiókot, és megtekintheti az elküldött üzeneteket.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablont `101-iothub-auto-route-messages` az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)hívja meg.

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>A sablon üzembe helyezése és a minta alkalmazás futtatása

Ez a szakasz a sablon üzembe helyezésének lépéseit, a virtuális eszközök létrehozását, valamint a ARM-Read-Write alkalmazás futtatását ismerteti az üzenetek elküldéséhez.

1. Hozza létre az erőforrásokat az ARM-sablon üzembe helyezésével.

    > [!TIP]
    > A sablon központi telepítésének elindításához kattintson az alábbi gombra. Amíg fut, állítsa be az ARM-Write-Write alkalmazást a futtatáshoz.

    [![Üzembe helyezés az Azure-ban](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Töltse le és csomagolja ki a [IoT C#-mintákat](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Nyisson meg egy parancssorablakot, és keresse meg azt a mappát, ahová a IoT C#-mintákat kibonthatja. Keresse meg az ARM-Read-Write. csproj fájlt tartalmazó mappát. Ebben a parancsablakban hozza létre a környezeti változókat. A kulcsok lekéréséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com) . Válassza az **erőforráscsoportok** lehetőséget, majd válassza ki az ehhez a rövid útmutatóhoz használt erőforráscsoportot.

   ![Válassza ki az erőforráscsoportot](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Megjelenik az ARM-sablon üzembe helyezésekor létrehozott IoT Hub-és Storage-fiók. A folytatás előtt várjon, amíg a sablon teljesen üzembe lett helyezve. Ezután válassza ki az erőforráscsoportot az erőforrások megtekintéséhez.

   ![Az erőforráscsoport erőforrásainak megtekintése](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. A **hub neve** szükséges. Válassza ki a hubot az erőforrások listájában. Másolja a hub nevét a IoT Hub szakasz elejéről a Windows vágólapra.

   ![A hub nevének másolása](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Helyettesítse be a hub nevét ebben a parancsban, ahol fel van tüntetve, és hajtsa végre ezt a parancsot a parancsablakban:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   Ez a következő példát fogja kikeresni:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. A következő környezeti változó a IoT eszköz kulcsa. Vegyen fel egy új eszközt a központba úgy, hogy kijelöli az **IOT eszközöket** a hub IoT hub menüjéből.

   ![IoT-eszközök kiválasztása](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Új eszköz hozzáadásához a képernyő jobb oldalán válassza az **+ új** lehetőséget.

   Adja meg az új eszköznév nevét. Ez a rövid útmutató a **contoso-test-Device** kezdetű nevet használja. Mentse az eszközt, majd nyissa meg újra a képernyőt az eszköz kulcsának lekéréséhez. (A rendszer létrehoz egy kulcsot a panel bezárásához.) Válassza ki az elsődleges vagy a másodlagos kulcsot, és másolja a Windows vágólapra. A parancsablakban állítsa be a parancsot a végrehajtáshoz, majd nyomja le az **ENTER** billentyűt. A parancsnak a következőhöz hasonlóan kell kinéznie:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Az utolsó környezeti változó az **eszköz azonosítója**. A parancssorablakban állítsa be a parancsot, és hajtsa végre.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   Ez a következő példához hasonlóan fog kinézni:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Ha meg szeretné tekinteni a definiált környezeti változókat, írja be a SET parancsot a parancssorba, majd nyomja le az **ENTER** billentyűt, és keresse meg a **IoT** kezdődően.

   ![Lásd: környezeti változók](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Most a környezeti változók vannak beállítva, futtassa az alkalmazást ugyanabból a parancsablakban. Mivel ugyanezt az ablakot használja, a változók a memóriában lesznek elérhetők az alkalmazás futtatásakor.

1. Az alkalmazás futtatásához írja be a következő parancsot a parancssorba, majd nyomja le az **ENTER** billentyűt.

    `dotnet run arm-read-write`

   Az alkalmazás az üzeneteket a konzolon hozza létre és jeleníti meg, mivel az egyes üzeneteket az IoT hub számára küldi el. A hub az ARM-sablonban úgy lett konfigurálva, hogy automatikus útválasztást legyen. A szöveget tartalmazó üzeneteket `level = storage` a rendszer automatikusan átirányítja a Storage-fiókba. Hagyja, hogy az alkalmazás 10 – 15 percig fusson, majd nyomja le az **ENTER billentyűt** egyszer vagy kétszer, amíg nem fut.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza ki az erőforráscsoportot, majd válassza ki a Storage-fiókot.

1. Tekintse meg a Storage-fiókot, amíg meg nem találja a fájlokat.

   ![Tekintse meg a Storage-fiók fájljait](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Válassza ki az egyik fájlt, és válassza a **Letöltés** lehetőséget, majd töltse le a fájlt egy olyan helyre, ahol később talál. A név numerikus, például 47. Adja hozzá a _. txt_ fájlt a végéhez, majd kattintson duplán a fájlra a megnyitásához.

1. A fájl megnyitásakor minden sor egy másik üzenetre mutat. az egyes üzenetek törzse is titkosítva van. Ahhoz, hogy az üzenet törzsén lekérdezéseket végezzen, meg kell adni.

   ![Az elküldött üzenetek megtekintése](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Ezek az üzenetek UTF-32 és Base64 kódolással vannak kódolva. Ha elolvasta az üzenetet, a Base64 és az UTF-32 értékről kell dekódolnia, hogy az ASCII-ként legyen olvasható. Ha érdekli, használhatja az útválasztási oktatóanyag ReadOneRowFromFile metódusát, hogy olvassa el az egyiket az egyes üzenetek közül, és dekódolja az ASCII-ben. A ReadOneRowFromFile a IoT C# Samples adattárában található, amelyet kibonthat ehhez a rövid útmutatóhoz. Itt látható a mappa tetején található elérési út: *./IOT-hub/tutorials/Routing/SimulatedDevice/program.cs.* Állítsa a logikai értéket True értékre `readTheFile` , és parancsmagba a fájl elérési útját a lemezen, és megnyitja és lefordítja a fájl első sorát.

Üzembe helyezett egy ARM-sablont egy IoT Hub és egy Storage-fiók létrehozásához, és futtatott egy programot, amely üzeneteket küld a hubhoz. Az üzeneteket a rendszer automatikusan tárolja a Storage-fiókban, ahol megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során hozzáadott erőforrások eltávolításához jelentkezzen be a [Azure Portalba](https://portal.azure.com). Válassza ki az **erőforráscsoportok** elemet, majd keresse meg az ehhez a rövid útmutatóhoz használt erőforráscsoportot. Válassza ki az erőforráscsoportot, majd válassza a *Törlés* lehetőséget. A csoport összes erőforrását törli.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
