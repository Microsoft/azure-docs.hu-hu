---
title: A fájlok feltöltésének konfigurálása a Azure Portal használatával | Microsoft Docs
description: A Azure Portal használata a IoT hub konfigurálásához a csatlakoztatott eszközökről történő fájlfeltöltés engedélyezéséhez. A cél Azure Storage-fiók konfigurálásával kapcsolatos információkat tartalmaz.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92535875"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>IoT Hub-fájlfeltöltések konfigurálása az Azure Portallal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Fájlfeltöltés

Ha IoT Hub szeretné használni a [fájlfeltöltés funkciót](iot-hub-devguide-file-upload.md), először hozzá kell rendelnie egy Azure Storage-fiókot a hubhoz. Válassza a **fájlfeltöltés** lehetőséget a módosítandó IoT hub fájlfeltöltés-tulajdonságainak megtekintéséhez.

![IoT Hub fájlfeltöltés beállításainak megtekintése a portálon](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Tároló**: a Azure Portal használatával kiválaszthatja az Azure Storage-fiókban lévő BLOB-tárolót az aktuális Azure-előfizetésében, hogy társítsa a IoT hub. Szükség esetén létrehozhat egy Azure Storage-fiókot a **Storage-fiókok** panelen és a blob-tárolóban a **tárolók** panelen. A IoT Hub automatikusan létrehoz egy írási engedéllyel rendelkező SAS URI-t a blob-tárolóhoz a fájlok feltöltésekor használandó eszközökhöz.

   ![A fájl feltöltéséhez használt tárolók megtekintése a portálon](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Értesítések fogadása a feltöltött fájlokról**: a fájlfeltöltés értesítéseinek engedélyezése vagy letiltása a váltógomb használatával.

* **Sas TTL**: Ez a beállítás a IoT hub által az eszközre visszaadott sas URI-k élettartama. Alapértelmezés szerint egy órára van beállítva, de a csúszka használatával más értékekre is testreszabható.

* **Fájl-értesítési beállítások alapértelmezett élettartama**: a fájl feltöltésével kapcsolatos értesítési idő a lejárta előtt. Alapértelmezés szerint egy napra van beállítva, de a csúszka használatával más értékekre is testreszabható.

* **Fájl értesítéseinek maximális kézbesítési száma**: az a szám, ahányszor a IoT hub megpróbált kézbesíteni egy fájlfeltöltés-értesítést. Alapértelmezés szerint 10 értékre van állítva, de a csúszka használatával más értékekre is testreszabható.

   ![IoT Hub fájl feltöltésének konfigurálása a portálon](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Következő lépések

A IoT Hub fájlfeltöltés funkcióival kapcsolatos további információkért lásd: [fájlok feltöltése egy eszközről](iot-hub-devguide-file-upload.md) a IoT hub Fejlesztői útmutatóban.

Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hub kezeléséről:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [Az IoT hub figyelése](monitor-iot-hub.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
* [A IoT-megoldás biztonságossá tétele az alapoktól](../iot-fundamentals/iot-security-ground-up.md)