---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179572"
---
Itt láthatók a megírható Azure-objektumok méretei. Győződjön meg arról, hogy az összes feltöltött fájl megfelel a fenti korlátoknak.

| Azure-objektumtípus | Feltöltési korlát                                             |
|-------------------|-----------------------------------------------------------|
| BLOB letiltása        | ~ 4,75 TB                                                 |
| Oldal Blobja         | 1 TB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-és a VHDX 512 bájt van igazítva. |
| Azure Files         | 1 TB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-és a VHDX 512 bájt van igazítva. |

> [!IMPORTANT]
> A fájlok létrehozása (a tárolási típustól függetlenül) legfeljebb 5 TB lehet. Ha azonban olyan fájlt hoz létre, amelynek mérete meghaladja az előző táblázatban meghatározott feltöltési korlátot, a fájl nem töltődik fel. Manuálisan kell törölnie a fájlt a hely visszaigényléséhez.