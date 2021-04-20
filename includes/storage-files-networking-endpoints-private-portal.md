---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732628"
---
Keresse meg azt a tárfiókot, amelyhez privát végpontot szeretne létrehozni. A tárfiók tartalomjegyzékében válassza a Hálózat, a Privát végpontkapcsolatok, majd a **+** Privát végpont lehetőséget egy új privát végpont létrehozásához. 

[![Képernyőkép a privát végponti kapcsolatok elemről a tárfiók tartalomjegyzékében](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Az eredményül kapott varázsló több lapot is tartalmaz.

Az Alapvető **beállítások panelen** válassza ki a kívánt erőforráscsoportot, nevet és régiót a privát végponthoz. Ezek bármilyenek lehetnek, de nem kell semmilyen módon egyezniük a tárfiókkal, bár a privát végpontot ugyanabban a régióban kell létrehoznia, mint ahol a virtuális hálózatot létre szeretné hozni.

![Képernyőkép a Privát végpont létrehozása szakasz Alapvető funkciók szakaszról](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Az Erőforrás **panelen** válassza a Csatlakozás a címtárban lévő Azure-erőforráshoz **választógombot.** Az **Erőforrás típusa alatt** válassza a **Microsoft.Storage/storageAccounts** erőforrástípust. Az **Erőforrás** mező az a tárfiók, amelybe az Azure-fájlmegosztást csatlakoztatni szeretné. A cél-alerőforrás a **fájl,** mivel ez a Azure Files.

A **Konfiguráció** panelen kiválaszthatja azt a virtuális hálózatot és alhálózatot, amelybe hozzá szeretné adni a privát végpontot. Ki kell választania egy eltérő alhálózatot abból az alhálózatból, amelybe a szolgáltatásvégpontot hozzáadta. A Konfiguráció panel a privát DNS-zóna létrehozására/frissítésére vonatkozó információkat is tartalmazza. Javasoljuk, hogy az alapértelmezett zónát `privatelink.file.core.windows.net` használja.

![Képernyőkép a Konfiguráció szakaszról](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Kattintson **az Áttekintés + létrehozás gombra** a privát végpont létrehozásához. 
