---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798599"
---
Navigáljon ahhoz a Storage-fiókhoz, amelyhez a nyilvános végpontot adott virtuális hálózatokra szeretné korlátozni. A Storage-fiók tartalomjegyzékében válassza a **hálózatkezelés** lehetőséget. 

A lap tetején válassza a **kiválasztott hálózatok** választógombot. Ezzel a beállítással a nyilvános végpont korlátozásának szabályozására nem kerül sor. Kattintson a **+ meglévő virtuális hálózat hozzáadása** lehetőségre azon adott virtuális hálózat kiválasztásához, amely számára engedélyezni kell a Storage-fiók nyilvános végponton keresztüli elérését. Ehhez ki kell választania egy virtuális hálózatot és egy alhálózatot a virtuális hálózathoz. 

Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a szolgáltatásfiók** -hoz, hogy a megbízható Microsoft-szolgáltatások, például a Azure file Sync a Storage-fiók elérését.

[![Képernyőfelvétel a hálózatkezelési panelről egy adott virtuális hálózattal, amely lehetővé teszi a Storage-fiók elérését a nyilvános végponton keresztül](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)