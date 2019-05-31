---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238609"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| **Korlát** | **Alapértelmezett** |
| --- | --- |
| Kiszolgálók és gépek, a tárolókban regisztrálható. | Windows Server/Windows Client/System Center Data Protection Manager: 50. <br/><br/> IaaS virtuális gépek: 1,000.  |
| Adatforrás méretét, egy tároló storage-ban. |54,400 GB-os maximális. IaaS VM biztonsági mentése a korlát nem vonatkozik. |
| Mentési tárolók az Azure-előfizetéssel. |régiónként 500 tárolóra. |
| Napi biztonsági mentés ütemezése. |A Windows-kiszolgáló vagy ügyfél: Három nap.<br/> System Center DPM: Két nap. <br/> IaaS virtuális gépek: Naponta egyszer.  |
| A biztonsági mentéshez egy Azure virtuális Géphez csatolt adatlemezek. | 16 |
| Biztonsági mentés az Azure virtuális Géphez csatlakoztatott adatlemez egyedi.| 4,095 GB|
