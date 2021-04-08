---
title: A fájlok feltöltésének konfigurálása a Azure PowerShell használatával | Microsoft Docs
description: A Azure PowerShell-parancsmagok használata az IoT hub konfigurálásához a csatlakoztatott eszközökről történő fájlfeltöltés engedélyezéséhez. A cél Azure Storage-fiók konfigurálásával kapcsolatos információkat tartalmaz.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536011"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>IoT Hub-fájlfeltöltés konfigurálása a PowerShell használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Ha IoT Hubban szeretné használni a [fájlfeltöltés funkciót](iot-hub-devguide-file-upload.md), először hozzá kell rendelnie egy Azure Storage-fiókot az IoT hub-hoz. Használhat meglévő Storage-fiókot, vagy létrehozhat egy újat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* [Azure PowerShell parancsmagok](/powershell/azure/install-Az-ps).

* Egy Azure IoT hub. Ha nem rendelkezik IoT-hubhoz, a [New-AzIoTHub parancsmag](/powershell/module/az.iothub/new-aziothub) használatával létrehozhat egyet, vagy a portál használatával [létrehozhat egy IoT hubot](iot-hub-create-through-portal.md).

* Egy Azure-tárfiók. Ha nem rendelkezik Azure Storage-fiókkal, akkor az [Azure Storage PowerShell-parancsmagjai](/powershell/module/az.storage/) segítségével létrehozhat egyet, vagy a portál használatával [létrehozhat egy Storage-fiókot](../storage/common/storage-account-create.md)

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be Azure-fiókját

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A PowerShell-parancssorban futtassa a következőt: **AzAccount** parancsmag:

    ```powershell
    Connect-AzAccount
    ```

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba való bejelentkezéssel elérheti a hitelesítő adataihoz társított összes Azure-előfizetést. Használja az alábbi parancsot a használni kívánt Azure-előfizetések listázásához:

    ```powershell
    Get-AzSubscription
    ```

    A következő parancs használatával válassza ki azt az előfizetést, amelyet az IoT hub kezelésére szolgáló parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>A Storage-fiók adatainak beolvasása

A következő lépések azt feltételezik, hogy a Storage-fiókot a **Resource Manager** -alapú üzemi modellel hozta létre, nem a **klasszikus** üzemi modellt.

A fájlfeltöltés az eszközökről való konfigurálásához szükség van egy Azure Storage-fiókhoz tartozó kapcsolódási karakterláncra. A Storage-fióknak ugyanahhoz az előfizetéshez kell tartoznia, mint az IoT hub-nak. Szüksége lesz egy blob-tároló nevére is a Storage-fiókban. A Storage-fiók kulcsainak lekéréséhez használja a következő parancsot:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Jegyezze fel a **key1** a Storage-fiók kulcsának értékét. A következő lépésekben kell megadnia.

Használhat meglévő BLOB-tárolót a fájlok feltöltésekor, vagy újat is létrehozhat:

* A Storage-fiókban található meglévő blob-tárolók listázásához használja az alábbi parancsokat:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* BLOB-tároló létrehozásához a Storage-fiókban használja a következő parancsokat:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Az IoT hub konfigurálása

Most már beállíthatja, hogy a IoT hub [fájlokat töltsön fel az IoT hubhoz](iot-hub-devguide-file-upload.md) a Storage-fiók adataival.

A konfigurációhoz a következő értékek szükségesek:

* **Storage Container**: egy Azure Storage-fiókban lévő blob-tároló az aktuális Azure-előfizetésben az IoT hub-hoz való hozzárendeléshez. Az előző szakaszban lekérte a szükséges Storage-fiók adatait. A IoT Hub automatikusan létrehoz egy írási engedéllyel rendelkező SAS URI-t a blob-tárolóhoz a fájlok feltöltésekor használandó eszközökhöz.

* **Értesítések fogadása a feltöltött fájlokról**: a fájlfeltöltés értesítéseinek engedélyezése vagy letiltása.

* **Sas TTL**: Ez a beállítás a IoT hub által az eszközre visszaadott sas URI-k élettartama. Alapértelmezés szerint egy órára van beállítva.

* **Fájl-értesítési beállítások alapértelmezett élettartama**: a fájl feltöltésével kapcsolatos értesítési idő a lejárta előtt. Alapértelmezés szerint egy napra van állítva.

* **Fájl értesítéseinek maximális kézbesítési száma**: az a szám, ahányszor a IoT hub megpróbált kézbesíteni egy fájlfeltöltés-értesítést. Alapértelmezés szerint 10 értékre kell állítani.

A következő PowerShell-parancsmaggal konfigurálhatja a fájlfeltöltés beállításait az IoT hub-ban:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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