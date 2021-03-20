---
title: A fájlfeltöltés konfigurálása IoT Hub az Azure CLI használatával | Microsoft Docs
description: Fájlok feltöltésének konfigurálása az Azure IoT Hub a platformfüggetlen Azure CLI használatával.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536062"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>IoT Hub fájlfeltöltés konfigurálása az Azure CLI-vel

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Ha [fájlokat szeretne feltölteni egy eszközről](iot-hub-devguide-file-upload.md), először társítson egy Azure Storage-fiókot az IoT hubhoz. Használhat meglévő Storage-fiókot, vagy létrehozhat egy újat.

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* [Azure CLI](/cli/azure/install-azure-cli)-vel.

* Egy Azure IoT hub. Ha nem rendelkezik IoT-hubhoz, a [ `az iot hub create` paranccsal](/cli/azure/iot/hub#az-iot-hub-create) létrehozhat egyet, vagy [létrehozhat egy IoT hubot a portál használatával](iot-hub-create-through-portal.md).

* Egy Azure Storage-fiók. Ha nem rendelkezik Azure Storage-fiókkal, akkor az Azure CLI használatával létrehozhat egyet. További információ: [Tárfiók létrehozása](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be Azure-fiókját

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

    A következő parancs használatával válassza ki azt az előfizetést, amelyet az IoT hub létrehozásához használni kíván a parancsok futtatásához. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A Storage-fiók adatainak beolvasása

A következő lépések azt feltételezik, hogy a Storage-fiókot a **Resource Manager** -alapú üzemi modellel hozta létre, nem a **klasszikus** üzemi modellt.

A fájlfeltöltés az eszközökről való konfigurálásához szükség van egy Azure Storage-fiókhoz tartozó kapcsolódási karakterláncra. A Storage-fióknak ugyanahhoz az előfizetéshez kell tartoznia, mint az IoT hub-nak. Szüksége lesz egy blob-tároló nevére is a Storage-fiókban. A Storage-fiók kulcsainak lekéréséhez használja a következő parancsot:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Jegyezze fel a **ConnectionString** értéket. A következő lépésekben kell megadnia.

Használhat meglévő BLOB-tárolót a fájl feltöltéséhez, vagy létrehozhat egy újat:

* A Storage-fiókban található meglévő blob-tárolók listázásához használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* BLOB-tároló létrehozásához a Storage-fiókban használja a következő parancsot:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájlfeltöltés

Mostantól konfigurálhatja az IoT hubot, hogy lehetővé tegye a [fájlok feltöltését az IoT hubhoz](iot-hub-devguide-file-upload.md) a Storage-fiók adataival.

A konfigurációhoz a következő értékek szükségesek:

* **Storage Container**: egy Azure Storage-fiókban lévő blob-tároló az aktuális Azure-előfizetésben az IoT hub-hoz való hozzárendeléshez. Az előző szakaszban lekérte a szükséges Storage-fiók adatait. A IoT Hub automatikusan létrehoz egy írási engedéllyel rendelkező SAS URI-t a blob-tárolóhoz a fájlok feltöltésekor használandó eszközökhöz.

* **Értesítések fogadása a feltöltött fájlokról**: a fájlfeltöltés értesítéseinek engedélyezése vagy letiltása.

* **Sas TTL**: Ez a beállítás a IoT hub által az eszközre visszaadott sas URI-k élettartama. Alapértelmezés szerint egy órára van beállítva.

* **Fájl-értesítési beállítások alapértelmezett élettartama**: a fájl feltöltésével kapcsolatos értesítési idő a lejárta előtt. Alapértelmezés szerint egy napra van állítva.

* **Fájl értesítéseinek maximális kézbesítési száma**: az a szám, ahányszor a IoT hub megpróbált kézbesíteni egy fájlfeltöltés-értesítést. Alapértelmezés szerint 10 értékre kell állítani.

Az alábbi Azure CLI-parancsokkal konfigurálhatja a IoT hub fájlfeltöltés-beállításait:

<!--Robinsh this is out of date, add cloud powershell -->

Egy bash-rendszerhéjban használja a következőket:

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

Az IoT hub fájlfeltöltés-konfigurációját a következő parancs használatával tekintheti meg:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Következő lépések

További információ a IoT Hub fájl feltöltési képességeiről: [fájlok feltöltése eszközről](iot-hub-devguide-file-upload.md).

Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hub kezeléséről:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT hub figyelése](monitor-iot-hub.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
* [A IoT-megoldás biztonságossá tétele az alapoktól](../iot-fundamentals/iot-security-ground-up.md)