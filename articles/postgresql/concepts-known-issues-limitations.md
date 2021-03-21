---
title: Ismert problémák és korlátozások – Azure Database for PostgreSQL – egyetlen kiszolgáló és egy rugalmas kiszolgáló (előzetes verzió)
description: Felsorolja azokat az ismert problémákat, amelyeket az ügyfeleknek ismerniük kell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100106405"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL – ismert problémák és korlátozások

Ez az oldal felsorolja az Azure Database for PostgreSQL ismert problémáit, amelyek hatással lehetnek az alkalmazásra. Emellett felsorolja a probléma megoldásához szükséges kockázatcsökkentő és javaslatokat is.

## <a name="intelligent-performance---query-store"></a>Intelligens teljesítmény – lekérdezési tároló

Azure Database for PostgreSQL – egyetlen kiszolgálóra alkalmazható.

| Alkalmazható | Ok | Szervizelés|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | A kiszolgáló paraméter bekapcsolása `pg_qs.replace_parameter_placeholders` bizonyos ritka helyzetekben a kiszolgáló leállításához vezethet. | Az Azure Portalon, a kiszolgálói paraméterek szakaszban kapcsolja be `pg_qs.replace_parameter_placeholders` és mentse a paraméter értékét `OFF` .   | 

## <a name="server-parameters"></a>Kiszolgálói paraméterek

Azure Database for PostgreSQL – egyetlen kiszolgáló és egy rugalmas kiszolgáló esetében alkalmazható

| Alkalmazható | Ok | Szervizelés| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Ha a kiszolgálói paramétert `max_locks_per_transaction` magasabb értékre szeretné módosítani, mint amit az [ajánlott](https://www.postgresql.org/docs/11/kernel-resources.html) , a kiszolgáló nem tud majd újraindulni. | Hagyja meg az alapértelmezett értéket (32 vagy 64), vagy módosítsa a PostgreSQL- [dokumentáció](https://www.postgresql.org/docs/11/kernel-resources.html)ésszerű értékét. <br> <br> A szolgáltatás oldaláról ez a munka, hogy az SKU alapján korlátozza a magas értéket.  | 

## <a name="next-steps"></a>Következő lépések
- Lásd: lekérdezési tároló – [ajánlott eljárások](./concepts-query-store-best-practices.md)
