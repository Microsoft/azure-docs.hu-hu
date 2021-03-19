---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88655150"
---
Itt láthatók a megírható Azure-objektumok méretei. Győződjön meg arról, hogy az összes feltöltött fájl megfelel a fenti korlátoknak.

| Azure-objektumtípus | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 TiB                                                 |
| Lapblob         | 8 TiB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-k és a VHDX 512 bájttal vannak igazítva. |
| Azure Files        | 1 TiB                                                      |
| Felügyelt lemezek     | 4 TiB <br> A mérettel és a korlátokkal kapcsolatos további információkért lásd: <li>[Standard SSD-k skálázhatósági céljai](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Prémium SSD-k skálázhatósági céljai](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[A standard HDD-k skálázhatósági céljai](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[A felügyelt lemezek díjszabása és számlázása](../articles/virtual-machines/disks-types.md#billing)</li>  
