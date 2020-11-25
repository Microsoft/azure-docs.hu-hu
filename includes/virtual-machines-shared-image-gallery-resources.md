---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015992"
---
| Erőforrás | Leírás|
|----------|------------|
| **Rendszerkép forrása** | Ez egy olyan erőforrás, amellyel rendszerkép- **verziót** hozhat létre egy Képtárban. A rendszerkép forrása lehet egy meglévő Azure-beli virtuális gép, amely [általánosított vagy specializált](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), felügyelt képet, pillanatképet vagy rendszerkép-verziót használ egy másik rendszerkép-katalógusban. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A képdefiníciók egy gyűjteményen belül jönnek létre, és a rendszerképekkel és a belső használat követelményeivel kapcsolatos információkat hordozzák. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerképverzió** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |