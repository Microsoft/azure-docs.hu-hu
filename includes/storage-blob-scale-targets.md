---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665604"
---
| Erőforrás | Cél | Cél (előzetes verzió) |
|-|-|-|
| Egy blob-tároló maximális mérete | Ugyanaz, mint a Storage-fiókok maximális kapacitása |  |
| Blokkok maximális száma egy blokk blobban vagy a blob hozzáfűzése | 50 000 blokk |  |
| Blokk maximális mérete egy blokk blobban | 100 MiB | 4000 MiB (előzetes verzió) |
| Blokk blob maximális mérete | 50 000 X 100 MiB (körülbelül 4,75 TiB) | 50 000 X 4000 MiB (körülbelül 190,7 TiB) (előzetes verzió) |
| Blokk maximális mérete egy hozzáfűző blobban | 4 MiB |  |
| Hozzáfűző blob maximális mérete | 50 000 x 4 MiB (körülbelül 195 GiB) |  |
| Oldal blobjának maximális mérete | 8 TiB<sup>2</sup> |  |
| Tárolt hozzáférési szabályzatok maximális száma blob-tárolón | 5 |  |
| Egy blobhoz tartozó célalkalmazás-kérelmek sebessége | Legfeljebb 500 kérelem másodpercenként |  |
| Cél átviteli sebesség egy egyoldalas blobhoz | Akár 60 MiB/másodperc<sup>2</sup> |  |
| Cél átviteli sebesség egyetlen blokk blobhoz | Legfeljebb Storage-fiók bejövő/kimenő korlátja<sup>1</sup> |  |

<sup>1</sup> az egyes Blobok átviteli sebessége több tényezőtől függ, többek között a következőktől: Egyidejűség, kérelmek mérete, teljesítményszint, a feltöltések forrása, valamint a letöltés céljának célja. A [nagy átviteli sebességű blokkos Blobok](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)teljesítményének növelésével kihasználhatja a nagyobb méretű Blobok vagy blokkok feltöltését. Pontosan hívja meg a [put blobot](/rest/api/storageservices/put-blob) vagy a [Letiltás](/rest/api/storageservices/put-block) műveletet egy olyan blob-vagy blokk-mérettel, amely nagyobb, mint 4 MIB a standard Storage-fiókokhoz. A Premium Block blob vagy a Data Lake Storage Gen2 Storage-fiókok esetében használjon 256 KiB-nál nagyobb blokk-vagy blob-méretet.

<sup>2</sup> az oldal Blobok még nem támogatottak azokban a fiókokban, amelyeken **hierarchikus névtér** van beállítva.

A következő táblázat a szolgáltatás verziója által engedélyezett maximális blokk-és blob-méreteket ismerteti.

| Szolgáltatás verziója | Maximális blokk mérete (Put blokkon keresztül) | BLOB maximális mérete (Put blokk lista használatával) | BLOB maximális mérete egyszeri írási művelettel (Put blobon keresztül) |
|-|-|-|-|
| 2019-12-12-es és újabb verziók | 4000 MiB (előzetes verzió) | Körülbelül 190,7 TiB (4000 MiB X 50 000 blokk) (előzetes verzió) | 5000 MiB (előzetes verzió) |
| 2016-05-31-es verzió az 2019-07-07-es verzióval | 100 MiB | Körülbelül 4,75 TiB (100 MiB X 50 000 blokk) | 256 MiB |
| 2016-05-31 előtti verziók | 4 MiB | Körülbelül 195 GiB (4 MiB X 50 000 blokk) | 64 MiB |
