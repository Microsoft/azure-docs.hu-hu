---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80420951"
---
Ez a cikk azt ismerteti, hogyan tölthetők fel virtuális merevlemezek a helyi gépről egy Azure-beli felügyelt lemezre, illetve hogyan másolhatók felügyelt lemezek egy másik régióba a AzCopy használatával. Ez a folyamat, a közvetlen feltöltés lehetővé teszi, hogy a virtuális merevlemezt akár 32 TiB-ra is feltöltheti közvetlenül egy felügyelt lemezre. A közvetlen feltöltés jelenleg a standard HDD, a standard SSD és a prémium szintű SSD által felügyelt lemezek esetében támogatott. Ultra-lemezek esetén nem támogatott.

Ha biztonsági mentési megoldást biztosít a IaaS virtuális gépek számára az Azure-ban, javasoljuk, hogy a közvetlen feltöltés használatával állítsa vissza az ügyfelek biztonsági másolatait a felügyelt lemezekre. Ha a virtuális merevlemezt az Azure-ból külső forrásból tölt fel, a sebesség a helyi sávszélességtől függ. Az Azure-beli virtuális gépekről történő feltöltéskor vagy másoláskor a sávszélesség ugyanaz, mint a standard HDD-k.