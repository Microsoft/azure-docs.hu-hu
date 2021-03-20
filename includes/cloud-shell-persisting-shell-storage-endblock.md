---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179456"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Helyi fájlok átvitele a Cloud Shellba
A `clouddrive` könyvtár a Azure Portal Storage paneljével szinkronizálódik. Ezen a panelen helyi fájlokat vihet át a fájlmegosztás vagy a fájlból. A fájlok Cloud Shellon belüli frissítése a fájl Storage grafikus felhasználói felületén jelenik meg, amikor frissíti a panelt.

### <a name="download-files"></a>Fájlok letöltése

![Helyi fájlok listája](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. A Azure Portal keresse meg a csatlakoztatott fájlmegosztást.
2. Válassza ki a célhelyet.
3. Kattintson a **Letöltés** gombra.

### <a name="upload-files"></a>Fájlok feltöltése

![Feltöltött helyi fájlok](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Nyissa meg a csatlakoztatott fájlmegosztást.
2. Kattintson a **Feltöltés** gombra.
3. Válassza ki a feltölteni kívánt fájlt vagy fájlokat.
4. Erősítse meg a feltöltést.

Ekkor meg kell jelennie a `clouddrive` címtárában elérhető fájlok Cloud shell.