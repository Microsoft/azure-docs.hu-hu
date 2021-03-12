---
title: Azure IoT Edge futtatása Ubuntu-Virtual Machines | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Ubuntu 18,04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1cd89f3f772effce4997fb69b37858ce2077c1dc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201092"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Azure IoT Edge futtatása Ubuntu-Virtual Machines

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből.

Ha többet szeretne megtudni arról, hogyan működik a IoT Edge futtatókörnyezet és milyen összetevőket tartalmaz, tekintse meg [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md)című témakört.

Ez a cikk egy Ubuntu 18,04 LTS rendszerű virtuális gép üzembe helyezésének lépéseit sorolja fel a telepített és az előre megadott eszköz-kapcsolódási karakterlánc használatával konfigurált Azure IoT Edge futtatókörnyezettel. Az üzembe helyezés egy [Felhőbeli init](../virtual-machines/linux/using-cloud-init.md
) -alapú Azure Resource Manager- [sablon](../azure-resource-manager/templates/overview.md) használatával valósul meg, amelyet a [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) Project repository tart fenn.

Az első rendszerindítás során az Ubuntu 18,04 LTS virtuális gép [telepíti a Azure IoT Edge Runtime legújabb verzióját a Cloud-init használatával](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). A megadott kapcsolati karakterláncot is beállítja a futtatókörnyezet elindítása előtt, így egyszerűen konfigurálhatja és csatlakoztathatja az IoT Edge eszközt anélkül, hogy SSH-vagy távoli asztali munkamenetet kellene elindítania.

>[!NOTE]
>A cikkhez használt sablon az 1,1-es verziót telepíti IoT Edge.

## <a name="deploy-using-deploy-to-azure-button"></a>Üzembe helyezés az Azure-beli üzembe helyezéssel gomb

Az [üzembe helyezés az Azure-ban gomb](../azure-resource-manager/templates/deploy-to-azure-button.md) lehetővé teszi a githubon karbantartott [Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md) egyszerűsített telepítését.  Ez a szakasz bemutatja az üzembe helyezés az Azure-ban gomb használatát a [iotedge-VM-Deploy](https://github.com/Azure/iotedge-vm-deploy) Project adattárban.  


1. A iotedge-VM-Deploy Azure Resource Manager sablonnal üzembe helyezünk egy Azure IoT Edge engedélyezett linuxos virtuális gépet.  A kezdéshez kattintson az alábbi gombra:

    [![Az iotedge-vm-deploy sablon Üzembe helyezés az Azure-ban gombja](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Az újonnan elindított ablakban töltse ki a rendelkezésre álló űrlapmezők mezőket:

    > [!div class="mx-imgBorder"]
    > [![Az iotedge-vm-deploy sablon képernyőképe](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Előfizetés**: a virtuális gép üzembe helyezéséhez szükséges aktív Azure-előfizetés.

    **Erőforráscsoport**: egy meglévő vagy újonnan létrehozott erőforráscsoport, amely tartalmazza a virtuális gépet és a hozzá társított erőforrásokat.

    **DNS-címke előtagja**: a választott érték, amelyet a virtuális gép állomásnévének előtagjaként kell használni.

    **Rendszergazdai Felhasználónév**: a Felhasználónév, amely rendszergazdai jogosultságokkal fog szolgálni az üzembe helyezéshez.

    **Eszköz-összekapcsolási karakterlánc**: egy eszközhöz tartozó, a kívánt [IoT Hubban](../iot-hub/about-iot-hub.md)létrehozott eszköz- [kapcsolatok karakterlánca](./how-to-register-device.md) .

    **VM-méret**: a telepítendő virtuális gép [mérete](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu operációs rendszer verziója**: az Ubuntu operációs rendszer azon verziója, amelyet az alapszintű virtuális gépre kell telepíteni.

    **Hely**: az a [földrajzi régió](https://azure.microsoft.com/global-infrastructure/locations/) , amelybe a virtuális gépet üzembe helyezi, ez az érték alapértelmezés szerint a kiválasztott erőforráscsoport helyét jelöli.

    **Hitelesítés típusa**: válassza a **sshPublicKey** vagy a **jelszó** lehetőséget a beállítástól függően.

    **Rendszergazdai jelszó vagy kulcs**: az SSH nyilvános kulcs értéke vagy a jelszó értéke a választott hitelesítési típustól függően.

    Ha az összes mezőt kitöltötte, jelölje be az oldal alján található jelölőnégyzetet a feltételek elfogadásához, majd válassza a **vásárlás** lehetőséget a telepítés megkezdéséhez.

1. Ellenőrizze, hogy az üzembe helyezés sikeresen befejeződött-e.  A kiválasztott erőforráscsoportban meg kellett történnie egy virtuális gép erőforrás üzembe helyezésnek.  Jegyezze fel a gép nevét, amelynek formátuma a következő: `vm-0000000000000` . Jegyezze fel a hozzá tartozó **DNS-név** értékét is. Ennek formátuma: `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    A **DNS-név** az újonnan üzembe helyezett virtuális gép **Áttekintés** szakaszában állapítható meg az Azure Portalon.

    > [!div class="mx-imgBorder"]
    > [![A iotedge virtuális gép DNS-nevét bemutató képernyőkép](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a társított **DNS-nevet** a következő paranccsal:  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Üzembe helyezés az Azure CLI-ből

1. Győződjön meg arról, hogy telepítette az Azure CLI IOT bővítményt az alábbiakkal:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Ha az Azure CLI-t használja az asztalon, először jelentkezzen be a következővel:

   ```azurecli-interactive
   az login
   ```

1. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyet használni szeretne:
   1. Listázza az előfizetéseit:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Másolja a használni kívánt előfizetés SubscriptionID mezőjét.

   1. Állítsa be a munkaidő-előfizetést a másolt AZONOSÍTÓval:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Hozzon létre egy új erőforráscsoportot (vagy határozzon meg egy meglévőt a következő lépésekben):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Új virtuális gép létrehozása:

    A **authenticationType** használatához `password` tekintse meg az alábbi példát:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Ha SSH-kulccsal szeretne hitelesítést végezni, ezt megteheti egy **authenticationType** megadásával, majd megadhatja az `sshPublicKey` SSH-kulcs értékét a **adminPasswordOrKey** paraméterben.  Erre mutat példát az alábbi ábra.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Ellenőrizze, hogy az üzembe helyezés sikeresen befejeződött-e.  A kiválasztott erőforráscsoportban meg kellett történnie egy virtuális gép erőforrás üzembe helyezésnek.  Jegyezze fel a gép nevét, amelynek formátuma a következő: `vm-0000000000000` . Jegyezze fel a hozzá tartozó **DNS-név** értékét is. Ennek formátuma: `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    A **DNS-név** az előző lépés JSON-formátumú kimenetéről szerezhető be a **kimenetek** szakaszban a **nyilvános SSH** -bejegyzés részeként.  Ennek a bejegyzésnek az értéke az újonnan üzembe helyezett gépre való SSH-ba is használható.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    A **DNS-név** a Azure Portalon belüli újonnan telepített virtuális gép **Áttekintés** szakaszában is beszerezhető.

    > [!div class="mx-imgBorder"]
    > [![A iotedge virtuális gép DNS-nevét bemutató képernyőkép](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a társított **DNS-nevet** a következő paranccsal:  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Következő lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a IoT Edge futtatókörnyezet megfelelő telepítésével kapcsolatban, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

Ha meg szeretné nyitni a portokat a virtuális gép SSH-n vagy más bejövő kapcsolaton keresztüli eléréséhez, tekintse meg az Azure Virtual Machines dokumentációját a [portok és végpontok Linux rendszerű virtuális gépre való megnyitásához](../virtual-machines/linux/nsg-quickstart.md)