---
title: Fájlfeltöltés konfigurálása IoT Hub Azure CLI-| Microsoft Docs
description: Fájlfeltöltés konfigurálása Azure IoT Hub platformfüggetlen Azure CLI használatával.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: ea6ec30ad5f3b1cdbc906cc94cb211295b84e802
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761726"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Fájlfeltöltés IoT Hub konfigurálása az Azure CLI használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Fájlok [eszközről való feltöltéséhez](iot-hub-devguide-file-upload.md)először társítania kell egy Azure Storage-fiókot az IoT Hubbal. Használhat egy meglévő tárfiókot, vagy létrehozhat egy újat.

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt [](https://azure.microsoft.com/pricing/free-trial/) létrehozhat egy ingyenes fiókot.

* [Azure CLI](/cli/azure/install-azure-cli).

* Egy Azure IoT Hub. Ha nem rendelkezik IoT Hubbal, az [ `az iot hub create` ](/cli/azure/iot/hub#az_iot_hub_create) paranccsal létrehozhat egyet, vagy létrehozhat [egy IoT Hubot a portál használatával.](iot-hub-create-through-portal.md)

* Egy Azure Storage-fiók. Ha még nincs Azure Storage-fiókja, létrehozhat egyet az Azure CLI használatával. További információ: [Tárfiók létrehozása](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Bejelentkezés és az Azure-fiók beállítása

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot](/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat](/cli/azure/account):

    ```azurecli
    az account list
    ```

    A következő paranccsal válassza ki azt az előfizetést, amely az IoT Hub létrehozásához szükséges parancsok futtatásához használható. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A tárfiók részleteinek lekérése

A következő lépések azt feltételezik, hogy a tárfiókot a klasszikus Resource Manager **modellel** hozta létre, nem pedig a **klasszikus** üzembe helyezési modellel.

Az eszközökről való fájlfeltöltés konfigurálához szüksége lesz egy Azure Storage-fiók kapcsolati sztringjére. A tárfióknak és az IoT Hubnak ugyanabban az előfizetésben kell lennie. Szüksége lesz egy blobtároló nevére is a tárfiókban. A tárfiók kulcsait a következő paranccsal használhatja:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Jegyezze fel a **connectionString** értékét. A következő lépésekben szüksége lesz rá.

A fájlfeltöltéshez használhat egy meglévő blobtárolót, vagy létrehozhat egy újat:

* A tárfiókban meglévő blobtárolók listához használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Blobtároló tárfiókban való létrehozásához használja a következő parancsot:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájlfeltöltés

Most már konfigurálhatja az IoT Hubot, hogy lehetővé tegye fájlok feltöltését az [IoT Hubra](iot-hub-devguide-file-upload.md) a tárfiók adataival.

A konfigurációhoz a következő értékekre van szükség:

* **Storage-tároló:** Blobtároló egy Azure-tárfiókban az aktuális Azure-előfizetésben, amely az IoT Hubhoz társítható. Az előző szakaszban lekérte a szükséges tárfiók-adatokat. IoT Hub automatikusan létrehozza az erre a blobtárolóra vonatkozó írási engedélyekkel rendelkező SAS URI-ket, amelyek használatával az eszközök fájlokat tölthetnek fel.

* **Értesítések fogadása a feltöltött fájlokról:** Fájlfeltöltési értesítések engedélyezése vagy letiltása.

* **SAS TTL:** Ez a beállítás a két eszköz által az eszközre visszaadott SAS URI-k IoT Hub. Alapértelmezés szerint egy órára van beállítva.

* **Fájlértesítési beállítások alapértelmezett TTL:A** fájlfeltöltési értesítés lejárt előtti ideje. Alapértelmezés szerint egy napra van beállítva.

* **Fájlértesítések maximális kézbesítési száma:** Az a szám, IoT Hub a rendszer megkísérli kézbesíteni a fájlfeltöltési értesítést. Az alapértelmezett érték 10.

A következő Azure CLI-parancsokkal konfigurálhatja a fájlfeltöltési beállításokat az IoT Hubon:

<!--Robinsh this is out of date, add cloud powershell -->

Bash-rendszerhéjban használja a következőt:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Az IoT Hub fájlfeltöltési konfigurációját a következő paranccsal tudja áttekintni:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Következő lépések

Az eszközök fájlfeltöltési képességeivel kapcsolatos IoT Hub lásd: Fájlok feltöltése [eszközről.](iot-hub-devguide-file-upload.md)

Az alábbi hivatkozások további információt tartalmaznak a Azure IoT Hub:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub figyelése](monitor-iot-hub.md)

A szolgáltatás képességeinek további IoT Hub:

* [IoT Hub útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
* [IoT-megoldásának biztosítása az aknával](../iot-fundamentals/iot-security-ground-up.md)