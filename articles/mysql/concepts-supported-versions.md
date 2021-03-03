---
title: Támogatott verziók – Azure Database for MySQL
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verziói támogatottak a Azure Database for MySQL szolgáltatásban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 920f6a4fec1ec8a260a98641888268e4955bbf44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718777"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure Database for MySQL Server-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB Storage Engine használatával. A szolgáltatás a Közösség által támogatott összes jelenlegi főverziót támogatja, azaz a MySQL 5,6, 5,7 és 8,0. A MySQL az X. Y. Z elnevezési sémát használja, ahol az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Az egyetlen kiszolgálóból álló központi telepítési beállításban az átjáró a kiszolgálói példányokkal való kapcsolat átirányítására szolgál. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a MySQL következő fő és másodlagos verzióit támogatja:


| Verzió | Önálló kiszolgáló <br/> Aktuális alverzió |Rugalmas kiszolgáló (előzetes verzió) <br/> Aktuális alverzió  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 5,6-es verzió |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | Nem támogatott|
|MySQL 5,7-es verzió | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 8,0-es verzió | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

A szolgáltatás jelenleg a főverzió frissítését támogatja a MySQL v 5.6-ról v 5.7-re való frissítéshez. További részletekért tekintse [meg a főbb verziófrissítések elvégzését ismertető témakört](how-to-major-version-upgrade.md). Ha 5,7-ről 8,0-re kíván frissíteni, javasoljuk, hogy a [memóriaképet és a visszaállítást](./concepts-migrate-dump-restore.md) olyan kiszolgálóra végezze, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>Következő lépések

- A Azure Database for MySQL verziószámozási szabályzatával kapcsolatos részletekért tekintse meg [a következő dokumentumot](concepts-version-policy.md):.
- A **szolgáltatási szintek** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)
