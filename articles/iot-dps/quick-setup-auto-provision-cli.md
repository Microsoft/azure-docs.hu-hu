---
title: Rövid útmutató – A Device Provisioning Service Azure IoT Hub beállítása az Azure CLI használatával
description: Rövid útmutató – Az Azure IoT Hub Device Provisioning Service (DPS) beállítása az Azure CLI használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3d52a83c8c0920c4d85aa5b4b6b89fd8d36e5fea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774952"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Rövid útmutató: A IoT Hub Device Provisioning Service beállítása az Azure CLI-val

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató azt részletezi, hogyan lehet az Azure CLI használatával IoT Hubot és IoT Hub Device Provisioning Service létrehozni, és összekapcsolni a két szolgáltatást. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Az ebben a rövid útmutatóban létrehozott IoT Hub és kiépítési szolgáltatás DNS-végpontként nyilvánosan felderíthető lesz. Ha úgy dönt, hogy megváltoztatja ezen erőforrások nevét, ügyeljen arra, hogy ne adjon meg bizalmas adatokat.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *my-sample-resource-group* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
>
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Egy IoT Hubot az [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) paranccsal hozhat létre.

A következő példában létrehozunk egy *my-sample-hub* nevű IoT Hubot a *westus* helyen. Az IoT Hub nevének globálisan egyedinek kell lennie az Azure-ban, ezért előfordulhat, hogy egyedi előtagot vagy utótagot szeretne hozzáadni a példanévhez, vagy teljesen új nevet szeretne választani. Győződjön meg arról, hogy a neve megfelel az IoT Hub megfelelő elnevezési konvencióinak: 3–50 karakter hosszúságú lehet, és csak kis- és nagybetűs alfanumerikus karaktereket vagy kötőjeleket (-) tartalmazhat. 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Device Provisioning Service létrehozása

Hozzon létre egy Device Provisioning Service-t [az az iot dps create paranccsal.](/cli/azure/iot/dps#az_iot_dps_create) 

Az alábbi példa létrehoz egy *my-sample-dps* nevű kiépítési szolgáltatást a *westus* helyen. Emellett globálisan egyedi nevet kell választania a saját kiépítési szolgáltatásának. Győződjön meg arról, hogy a megfelelő elnevezési konvenciókat követi a IoT Hub Device Provisioning Service esetében: 3–64 karakter hosszúságúnak kell lennie, és csak kis- és nagybetűs alfanumerikus karaktereket vagy kötőjeleket (-) tartalmazhat.

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> Ebben a példában az eszközkiépítési szolgáltatás az USA nyugati régiójában jön létre. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a helyek egyszavas vagy többszavas formátumban is megadva vannak; például: usa nyugati régiója, USA nyugati régiója, USA nyugati régiója stb. Az érték nem megkülönbözteti a kis- és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Az IoT Hub kapcsolati sztringjének lekérése

Szükség van az IoT Hub kapcsolati sztringjére a Device Provisioning Service-szel való összekapcsoláshoz. Az [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) használatával kérje le a kapcsolati sztringet, majd használja a kimenetét egy olyan változó megadásához, amelyet a két erőforrás összekötésére fog használni. 

Az alábbi példa a *hubConnectionString* változót a hub *iothubowner* szabályzatának elsődleges kulcsához megadott kapcsolati sztring értékével állítja be (a paraméterrel megadhat egy másik `--policy-name` szabályzatot). A korábban választott egyedi IoT Hub-névért a *my-sample-hub* nevet válassza. A parancs az Azure CLI [lekérdezés](/cli/azure/query-azure-cli) és [kimenet](/cli/azure/format-output-azure-cli#tsv-output-format) lehetőségeinek használatával nyeri ki a kapcsolati sztringet a parancskimenetből.

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
> Ha helyi Windows-/CMD-rendszerhéjat vagy PowerShell-gazdagépet használ, módosítsa a parancsokat úgy, hogy az a környezetnek megfelelő szintaxist használja.
>
> Ha héjablakot Azure Cloud Shell, ellenőrizze, hogy a rendszerhéj ablakának bal oldalán található környezet legördülő menüben a **Bash jelenik-e meg.**
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Az IoT Hub és az eszközkiépítési szolgáltatás csatolása

Az IoT Hub és az eszközkiépítési szolgáltatás csatolása az [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create) paranccsal. 

Az alábbi példa egy *my-sample-hub* nevű IoT Hubot csatlakoztat a *westus* helyen, és egy *my-sample-dps* nevű Device Provisioning Service-t. Ezeket a neveket a korábban választott egyedi IoT Hub- és Device Provisioning Service-nevekre cseréli ki. A parancs az Előző lépésben a *hubConnectionString* változóban tárolt IoT Hub kapcsolati sztringet használja.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

A parancs befejezése eltarthat néhány percig.

## <a name="verify-the-provisioning-service"></a>Eszközkiépítési szolgáltatás ellenőrzése

Eszközkiépítési szolgáltatás adatainak lekérése az [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show) paranccsal.

Az alábbi példa lekéri a *my-sample-dps* nevű eszközkiépítési szolgáltatás adatait. Ezt a nevet a Saját Device Provisioning Service-neveként nevezze el.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
A csatolt IoT Hub a *properties.iotHubs* gyűjteményben jelenik meg.

![A kiépítési szolgáltatás ellenőrzése](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a következő parancsok használatával törölheti az eszközkiépítési szolgáltatást, az IoT Hubot, vagy az erőforráscsoportot az ahhoz tartozó összes erőforrással együtt. Cserélje le az alább írt erőforrások nevét a saját erőforrásai nevére.

Az eszközkiépítési szolgáltatás törléséhez futtassa az [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete) parancsot:

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Az IoT Hub törléséhez futtassa az [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete) parancsot:

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa az [az group delete](/cli/azure/group#az_group_delete) parancsot:

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT Hubot és egy Device Provisioning Service-példányt, és összekapcsolta a két erőforrást. Ha meg szeretne ismerkedni vele, hogyan használható ez a beállítás egy szimulált eszköz üzembe helyezéséhez, folytassa a szimulált eszköz létrehozásával kapcsolatos rövid útmutatóval.

> [!div class="nextstepaction"]
> [Rövid útmutató szimulált eszköz létrehozásához](./quick-create-simulated-device.md)
