---
title: Fájlok feltöltése az eszközökről az Azure Storage-ba | Microsoft Docs
description: Fájlok feltöltésének konfigurálása az eszközökről a felhőbe. A fájlfeltöltés beállítása után implementálja az eszközökön a fájlfeltöltés szolgáltatást.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796905"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Fájlok feltöltése az eszközökről a felhőbe

*Ez a témakör a rendszergazdákra és az eszközök fejlesztőire vonatkozik.*

IoT Central lehetővé teszi a média és más fájlok feltöltését a csatlakoztatott eszközökről a Felhőbeli tárolóba. A fájlfeltöltés funkciót a IoT Central alkalmazásban konfigurálhatja, majd végrehajthatja a fájlfeltöltés használatát az eszköz kódjában.

## <a name="prerequisites"></a>Előfeltételek

A fájlfeltöltés konfigurálásához rendszergazdának kell lennie a IoT Central alkalmazásban.

A feltöltött fájlok tárolásához Azure Storage-fiókra és tárolóra van szükség. Ha nem rendelkezik meglévő Storage-fiókkal és-tárolóval, hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Az eszköz fájlfeltöltés konfigurálása

Az eszköz fájlfeltöltés beállítása:

1. Navigáljon az alkalmazás **Adminisztráció** szakaszához.

1. Válassza ki az **eszköz fájljának feltöltését**.

1. Válassza ki a használni kívánt Storage-fiókot és-tárolót. Ha a Storage-fiók egy másik Azure-előfizetésben található az alkalmazásból, adja meg a Storage-fiókhoz tartozó kapcsolatok sztringjét.

1. Ha szükséges, módosítsa a feltöltési időtúllépést, amely meghatározza, hogy a feltöltési kérések meddig érvényesek maradnak. Az érvényes értékek 1 – 24 óra.

1. Kattintson a **Mentés** gombra. Ha az **állapot megjelenik,** készen áll arra, hogy fájlokat töltsön fel az eszközökről.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Fájlfeltöltés konfigurálása az alkalmazásban":::

## <a name="disable-device-file-uploads"></a>Az eszközök fájlfeltöltés-feltöltésének letiltása

Ha le szeretné tiltani az eszköz fájljának feltöltését a IoT Central alkalmazásban:

1. Navigáljon az alkalmazás **Adminisztráció** szakaszához.

1. Válassza ki az **eszköz fájljának feltöltését**.

1. Válassza a **Törlés** elemet.

## <a name="upload-a-file-from-a-device"></a>Fájl feltöltése eszközről

A IoT Central IoT Hub fájl feltöltési képességét használja az eszközök fájlok feltöltésének engedélyezéséhez. A fájlok eszközökről történő feltöltését bemutató mintakód: [IoT Central fájlfeltöltés eszköz minta](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan konfigurálhatja és implementálhatja az eszköz fájljának feltöltését IoT Centralban, a következő lépés a további eszköz-fájlok feltöltésének megismerése:

- [Fájlok feltöltése az eszközről a felhőbe IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Fájlok feltöltése az eszközről a felhőbe IoT Hub (Java) használatával](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Fájlok feltöltése az eszközről a felhőbe IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Fájlok feltöltése az eszközről a felhőbe IoT Hub (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)