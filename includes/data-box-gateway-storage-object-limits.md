---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582089"
---
Itt láthatók a megírható Azure-objektumok méretei. Győződjön meg arról, hogy az összes feltöltött fájl megfelel a fenti korlátoknak.

| Azure-objektumtípus | Feltöltési korlát                                             |
|-------------------|-----------------------------------------------------------|
| BLOB letiltása        | ~ 4,75 TB                                                 |
| Oldal Blobja         | 1 TB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-és a VHDX 512 bájt van igazítva. |
| Azure Files         | 1 TB <br> Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, és a feltöltés sikertelen lesz. <br> A VHD-és a VHDX 512 bájt van igazítva. |

> [!IMPORTANT]
> A fájlok létrehozása (a tárolási típustól függetlenül) legfeljebb 5 TB lehet. Ha azonban olyan fájlt hoz létre, amelynek mérete meghaladja az előző táblázatban meghatározott feltöltési korlátot, a fájl nem töltődik fel. Manuálisan kell törölnie a fájlt a hely visszaigényléséhez.