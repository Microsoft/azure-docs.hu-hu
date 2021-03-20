---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93375878"
---
Navigáljon ahhoz a Storage-fiókhoz, amelyhez privát végpontot szeretne létrehozni. A Storage-fiók tartalomjegyzékében válassza a **privát végponti kapcsolatok** lehetőséget, majd a **+ privát végpontot** egy új privát végpont létrehozásához. 

[![A Storage-fiók tartalomjegyzékében található Private Endpoint Connections elemek képernyőképe](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Az eredményül kapott varázslónak több lapja is van.

Az **alapvető beállítások** panelen válassza ki a saját végpontja számára a kívánt erőforráscsoportot, nevet és régiót. Lehet, hogy bármit is szeretne, nem kell semmilyen módon megegyezniük a Storage-fiókkal, bár a magánhálózati végpontot abban a régióban kell létrehoznia, amelyben a privát végpontot létre kívánja hozni.

![A privát végpont létrehozása szakasz alapjai szakaszának képernyőképe](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Az **erőforrás** panelen válassza a **saját címtárban található Azure-erőforráshoz való kapcsolódáshoz** használandó választógombot. Az **erőforrástípus** területen válassza ki a **Microsoft. Storage/storageAccounts** elemet az erőforrás típushoz. Az **erőforrás** mező annak az Azure-fájlmegosztásnak a Storage-fiókja, amelyhez csatlakozni szeretne. A cél alerőforrás **fájl**, mivel Azure files.

A **konfiguráció** panelen kiválaszthatja azt a virtuális hálózatot és alhálózatot, amelyhez hozzá szeretné adni a privát végpontot. Ki kell választania egy különálló alhálózatot abból az alhálózatból, amelyhez a szolgáltatási végpontot hozzáadta. A konfiguráció panelen a saját DNS-zóna létrehozásához/frissítéséhez szükséges információk is szerepelnek. Azt javasoljuk, hogy az alapértelmezett `privatelink.file.core.windows.net` zónát használja.

![A konfigurációs szakasz képernyőképe](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

A privát végpont létrehozásához kattintson a **felülvizsgálat + létrehozás** gombra. 
