---
title: SQL Server virtuális gépeken rendelkezésre állási csoportok esetében használjon elosztott hálózati név (DNN) figyelőt.
description: Üzenet, amellyel átirányíthatja az ügyfeleket a DNN-figyelő használatára a VNN-figyelő helyett.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167992"
---
> [!NOTE]
> A SQL Server 2019 CU8 és újabb verziókban a Windows 2016-es és újabb verzióiban a hagyományos VNN-figyelő lecserélhető, és Azure Load Balancer egy [elosztott hálózati név (DNN) figyelővel](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md) . Ugorja át a jelen cikkben szereplő további lépéseket a figyelő és a terheléselosztó létrehozásához.
