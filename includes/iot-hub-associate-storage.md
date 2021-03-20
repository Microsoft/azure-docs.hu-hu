---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76021072"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage-fiók hozzárendelése IoT Hubhoz

Mivel a szimulált eszköz alkalmazás feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-account-create.md) -fiókkal, amely az IoT hubhoz van társítva. Ha egy Azure Storage-fiókot társít egy IoT hubhoz, az IoT hub létrehoz egy SAS URI-t. Az eszköz ezt az SAS URI-t használva biztonságosan tölthet fel egy fájlt egy blob-tárolóba. A IoT Hub szolgáltatás és az eszköz SDK-k összehangolják a SAS URI-t létrehozó folyamatot, és elérhetővé teszik azt egy olyan eszköz számára, amelyet a fájlok feltöltésére használhatnak.

Kövesse a [fájlfeltöltés konfigurálása a Azure Portal használatával](../articles/iot-hub/iot-hub-configure-file-upload.md)című témakör utasításait. Győződjön meg arról, hogy a blob-tároló társítva van az IoT hubhoz, és a fájl értesítései engedélyezve vannak.

![Fájlok értesítéseinek engedélyezése a portálon](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
