---
title: Storage Sync szolgáltatás üzembe helyezése
description: Telepítse a Azure File Sync felhőalapú erőforrást egy Storage Sync szolgáltatással. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043130"
---
Ebben a lépésben szüksége lesz az Azure-előfizetés hitelesítő adataira.

Az Azure File Sync számára konfigurálandó alapvető erőforrást *Storage Sync szolgáltatásnak* nevezzük. Azt javasoljuk, hogy csak egyet helyezzen üzembe az összes olyan kiszolgálón, amely most vagy a későbbiekben is szinkronizálja ezeket a fájlokat. Csak akkor hozzon létre több tárolási szinkronizációs szolgáltatást, ha olyan kiszolgálókat tartalmaz, amelyeknek soha nem kell cserélniük az adatcserét. Előfordulhat például, hogy olyan kiszolgálókkal rendelkezik, amelyeknek soha nem kell azonos Azure-fájlmegosztást szinkronizálnia. Ellenkező esetben az ajánlott eljárás egyetlen Storage Sync szolgáltatás.

Válasszon egy Azure-régiót a Storage Sync szolgáltatáshoz, amely közel van a helyhez. Minden más Felhőbeli erőforrást ugyanabban a régióban kell üzembe helyezni. A felügyelet egyszerűsítése érdekében hozzon létre egy új erőforráscsoportot az előfizetésben, amely a szinkronizálási és tárolási erőforrásokat is otthont ad.

További információ: a [Storage Sync szolgáltatás központi telepítése című rész](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) a Azure file Sync üzembe helyezéséről szóló cikkben. Csak a cikk ezen részének követése. A későbbi lépésekben a cikk további szakaszaira mutató hivatkozásokat talál.