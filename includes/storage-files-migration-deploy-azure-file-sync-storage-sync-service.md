---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075577"
---
Ebben a lépésben szüksége lesz az Azure-előfizetés hitelesítő adataira.

Az Azure File Sync számára konfigurálandó alapvető erőforrást *Storage Sync szolgáltatásnak* nevezzük. Azt javasoljuk, hogy csak egyet helyezzen üzembe az összes olyan kiszolgálón, amely most vagy a későbbiekben is szinkronizálja ezeket a fájlokat. Csak akkor hozzon létre több tárolási szinkronizációs szolgáltatást, ha olyan kiszolgálókat tartalmaz, amelyeknek soha nem kell cserélniük az adatcserét. Előfordulhat például, hogy olyan kiszolgálókkal rendelkezik, amelyeknek soha nem kell azonos Azure-fájlmegosztást szinkronizálnia. Ellenkező esetben az ajánlott eljárás egyetlen Storage Sync szolgáltatás.

Válasszon egy Azure-régiót a Storage Sync szolgáltatáshoz, amely közel van a helyhez. Minden más Felhőbeli erőforrást ugyanabban a régióban kell üzembe helyezni. A felügyelet egyszerűsítése érdekében hozzon létre egy új erőforráscsoportot az előfizetésben, amely a szinkronizálási és tárolási erőforrásokat is otthont ad.

További információ: a [Storage Sync szolgáltatás központi telepítése című rész](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) a Azure file Sync üzembe helyezéséről szóló cikkben. Csak a cikk ezen részének követése. A későbbi lépésekben a cikk további szakaszaira mutató hivatkozásokat talál.