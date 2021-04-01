---
title: Rövid útmutató – Azure-IoT Hub Device Provisioning Service beállítása az Azure CLI-vel
description: Rövid útmutató – az Azure IoT Hub Device Provisioning Service (DPS) beállítása az Azure CLI használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660185"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Rövid útmutató: a IoT Hub Device Provisioning Service beállítása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy IoT hubot és egy IoT Hub Device Provisioning Servicet az Azure CLI használatával, és hogyan kapcsolhatja össze a két szolgáltatást. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Az IoT hub és az ebben a rövid útmutatóban létrehozott kiépítési szolgáltatás a DNS-végpontként nyilvánosan felderíthetővé válik. Ha úgy dönt, hogy megváltoztatja ezen erőforrások nevét, ügyeljen arra, hogy ne adjon meg bizalmas adatokat.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *my-sample-resource-group* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
>
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Egy IoT Hubot az [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal hozhat létre.

A következő példában létrehozunk egy *my-sample-hub* nevű IoT Hubot a *westus* helyen. Az IoT hub nevének globálisan egyedinek kell lennie az Azure-ban, ezért érdemes lehet egyedi előtagot vagy utótagot hozzáadni a példában szereplő névhez, vagy teljesen új nevet választani. Győződjön meg arról, hogy a neve megfelel az IoT hub megfelelő elnevezési konvencióinak: 3-50 karakter hosszúnak kell lennie, és csak kis-és nagybetűket (alfanumerikus karaktereket vagy kötőjeleket) tartalmazhat ("-"). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Eszköz-kiépítési szolgáltatás létrehozása

Hozzon létre egy eszköz-kiépítési szolgáltatást az az [IOT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) paranccsal. 

A következő példában létrehozunk egy *My-Sample-DPS* nevű kiépítési szolgáltatást a *westus* helyen. Emellett ki kell választania egy globálisan egyedi nevet a saját kiépítési szolgáltatásához. Győződjön meg arról, hogy az egy IoT Hub Device Provisioning Service megfelelő elnevezési konvenciókat használ: 3-64 karakter hosszúnak kell lennie, és csak kis-és nagybetűket, alfanumerikus karaktereket és kötőjeleket (-) tartalmazhat.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az eszközkiépítési szolgáltatás az USA nyugati régiójában jön létre. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a hely megadható egy vagy több szóból álló formátumban is. például: westus, USA nyugati régiója, USA nyugati régiója stb. Az érték nem megkülönbözteti a kis-és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Az IoT Hub kapcsolati sztringjének lekérése

Szükség van az IoT Hub kapcsolati sztringjére a Device Provisioning Service-szel való összekapcsoláshoz. Az [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) használatával kérje le a kapcsolati sztringet, majd használja a kimenetét egy olyan változó megadásához, amelyet a két erőforrás összekötésére fog használni. 

A következő példa a *hubConnectionString* változó értékét a hub *iothubowner* házirendjének elsődleges kulcsához tartozó kapcsolódási karakterlánc értékére állítja be (a `--policy-name` paramétert másik házirend megadására lehet használni). A korábban kiválasztott egyedi IoT hub-név kiértékelése a *My-Sample-hub* . A parancs az Azure CLI [lekérdezés](/cli/azure/query-azure-cli) és [kimenet](/cli/azure/format-output-azure-cli#tsv-output-format) lehetőségeinek használatával nyeri ki a kapcsolati sztringet a parancskimenetből.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

A `echo` paranccsal megtekintheti a kapcsolati sztringet:

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Ez a két parancs a Bash alatt futó gazdagépek esetében érvényesek.
> 
> Ha helyi Windows/CMD rendszerhéjt vagy PowerShell-gazdagépet használ, módosítsa a parancsokat úgy, hogy az adott környezetnek megfelelő szintaxist használják.
>
> Ha Azure Cloud Shell használ, ellenőrizze, hogy a környezet legördülő menüje a rendszerhéj ablakának bal oldalán található-e: **bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

Az IoT Hub és az eszközkiépítési szolgáltatás csatolása az [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) paranccsal. 

Az alábbi példa egy *My-Sample-hub* nevű IoT-hubot használ a *westus* helyen és egy *My-Sample-DPS* nevű Device kiépítési szolgáltatásban. Ezeket a neveket az egyedi IoT hub és a korábban kiválasztott eszközök kiépítési szolgáltatásának nevére kell kiforgalmaznia. A parancs az előző lépésben a *hubConnectionString* változóban tárolt IoT hub-hoz tartozó kapcsolatok karakterláncot használja.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

A parancs végrehajtása több percet is igénybe vehet.

## <a name="verify-the-provisioning-service"></a>Eszközkiépítési szolgáltatás ellenőrzése

Eszközkiépítési szolgáltatás adatainak lekérése az [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) paranccsal.

Az alábbi példa lekéri a *my-sample-dps* nevű eszközkiépítési szolgáltatás adatait. Ezt a nevet a saját eszköz kiépítési szolgáltatásának nevére kell kiforgalmazni.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
A csatolt IoT Hub a *properties.iotHubs* gyűjteményben jelenik meg.

![Kiépítési szolgáltatás ellenőrzése](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő parancsok használatával törölheti az eszközkiépítési szolgáltatást, az IoT Hubot, vagy az erőforráscsoportot az ahhoz tartozó összes erőforrással együtt. Cserélje le az alább írt erőforrások nevét a saját erőforrásainak nevére.

Az eszközkiépítési szolgáltatás törléséhez futtassa az [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) parancsot:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Az IoT Hub törléséhez futtassa az [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) parancsot:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa az [az group delete](/cli/azure/group#az-group-delete) parancsot:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT hubot és egy eszköz kiépítési szolgáltatási példányát, és összekapcsolta a két erőforrást. Ha szeretné megtudni, hogyan lehet szimulált eszközt kiépíteni a telepítővel, folytassa a szimulált eszköz létrehozására szolgáló rövid útmutatóval.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)
