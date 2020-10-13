---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465123"
---
Navigáljon ahhoz a Storage-fiókhoz, amelyhez a nyilvános végpontot adott virtuális hálózatokra szeretné korlátozni. A Storage-fiók tartalomjegyzékében válassza a **tűzfalak és virtuális hálózatok**lehetőséget. 

A lap tetején válassza a **kiválasztott hálózatok** választógombot. Ezzel a beállítással a nyilvános végpont korlátozásának szabályozására nem kerül sor. Kattintson a **+ meglévő virtuális hálózat hozzáadása** lehetőségre azon adott virtuális hálózat kiválasztásához, amely számára engedélyezni kell a Storage-fiók nyilvános végponton keresztüli elérését. Ehhez ki kell választania egy virtuális hálózatot és egy alhálózatot a virtuális hálózathoz. 

Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a szolgáltatásfiók** -hoz, hogy a megbízható Microsoft-szolgáltatások, például a Azure file Sync a Storage-fiók elérését.

[![Képernyőfelvétel a tűzfalakról és a virtuális hálózatokról panel egy adott virtuális hálózattal, amely lehetővé teszi a Storage-fiók elérését a nyilvános végponton keresztül](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)