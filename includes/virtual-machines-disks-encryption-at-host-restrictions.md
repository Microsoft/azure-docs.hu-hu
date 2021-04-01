---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231029"
---
- A nem támogatja az ultra-lemezeket.
- Nem engedélyezhető, ha a virtuális gépeken/virtuálisgép-méretezési csoportokon a Azure Disk Encryption (vendég-VM-titkosítás BitLocker/VM-visszafejtés használatával) engedélyezve van.
- Azure Disk Encryption nem engedélyezhető olyan lemezeken, amelyeken engedélyezve van az állomás titkosítása.
- A titkosítás engedélyezhető a meglévő virtuálisgép-méretezési csoportokban. Azonban csak a titkosítás engedélyezése után létrehozott új virtuális gépek automatikusan titkosítva lesznek.
- A meglévő virtuális gépeket fel kell osztani és újra le kell osztani a titkosítás érdekében.