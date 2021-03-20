---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78941012"
---
A következő táblázat a Table Storage kapacitását, méretezhetőségét és teljesítményére vonatkozó célokat ismerteti.

| Erőforrás | Cél |
|----------|---------------|
| Táblák száma egy Azure Storage-fiókban | Csak a Storage-fiók kapacitása korlátozza |
| Egy tábla partícióinak száma | Csak a Storage-fiók kapacitása korlátozza |
| Egy partícióban lévő entitások száma | Csak a Storage-fiók kapacitása korlátozza |
| Egyetlen tábla maximális mérete | 500 TiB |
| Egyetlen entitás maximális mérete, beleértve az összes tulajdonság értékét | 1 MiB |
| Tulajdonságok maximális száma egy tábla entitásban | 255 (a három rendszertulajdonságot, a **PartitionKey**, a **RowKey** és az **időbélyeg**-t is beleértve) |
| Az entitásban lévő egyes tulajdonságok maximális teljes mérete | A tulajdonság típusa szerint változhat. További információ: **Property types** ( [a Table Service adatmodell ismertetése](/rest/api/storageservices/understanding-the-table-service-data-model)). |
| A **PartitionKey** mérete | Legfeljebb 1 KiB méretű sztring |
| A **RowKey** mérete | Legfeljebb 1 KiB méretű sztring |
| Entitás-csoport tranzakciójának mérete | Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adatnak 4 MiB-nél kisebbnek kell lennie. Az entitások csoportjainak tranzakciója csak egyszer tud frissíteni egy entitást. |
| Tárolt hozzáférési szabályzatok maximális száma táblában | 5 |
| Kérelmek maximális száma Storage-fiókban | 20 000 tranzakció/másodperc, amely egy 1 – KiB értékű entitás méretét feltételezi |
| Cél átviteli sebesség egyetlen tábla partícióhoz (1 KiB – entitások) | Legfeljebb 2 000 entitás másodpercenként |