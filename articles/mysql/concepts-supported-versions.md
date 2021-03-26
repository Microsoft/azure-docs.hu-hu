---
title: Támogatott verziók – Azure Database for MySQL
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verziói támogatottak a Azure Database for MySQL szolgáltatásban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 314462517ba4e63694266b5e49231cb8536f3635
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604726"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Az Azure Database for MySQL-kiszolgáló támogatott verziói

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB Storage Engine használatával. A szolgáltatás a Közösség által támogatott összes jelenlegi főverziót támogatja, azaz a MySQL 5,6, 5,7 és 8,0. A MySQL az X. Y. Z elnevezési sémát használja, ahol az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Kapcsolódás adott MySQL-verziót futtató átjáró-csomóponthoz

Az egyetlen kiszolgálóból álló központi telepítési beállításban az átjáró a kiszolgálói példányokkal való kapcsolat átirányítására szolgál. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában. Tekintse át a [kapcsolati architektúrát](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) , ha többet szeretne megtudni a Azure Database for MySQL szolgáltatási architektúrában található átjárókkal kapcsolatban.

Ahogy a Azure Database for MySQL támogatja a főverzió v 5.6-os, v 5.7-es és v 8.0-s verzióját, az alapértelmezett 3306-as port a Azure Database for MySQL a MySQL-ügyfél 5,6-es verziójában fut (a legkevésbé gyakori nevező), hogy támogassa a 3 támogatott főverziót használó kiszolgálókhoz való kapcsolatokat. Ha azonban az alkalmazásnak egy, a v 5.7 vagy a v 8.0-s verziójához való csatlakozásra van szüksége, akkor a kiszolgáló kapcsolati karakterláncában lévő port módosításával is megteheti azt.

Azure Database for MySQL szolgáltatásban az átjáró-csomópontok a 3308-es portot figyelik a v 5.7-ügyfelek és a 3309-es porton a v 8.0-ügyfelek számára. Más szóval, ha csatlakozni szeretne a v 5.7-átjáró ügyfeléhez, a teljes kiszolgálónevet és a 3308-es portot kell használnia a kiszolgálóhoz való csatlakozáshoz az ügyfélalkalmazás használatával. Hasonlóképpen, ha csatlakozni szeretne a v 8.0-s átjáró ügyfeléhez, a teljes kiszolgálónevet és a 3309-es portot használhatja a kiszolgálóhoz való csatlakozáshoz. További egyértelműség a következő példán látható.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Példa a különböző Gateway MySQL-verziókon keresztüli kapcsolódásra":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>A Azure Database for MySQL jelenleg a MySQL következő fő és másodlagos verzióit támogatja:


| Verzió | Önálló kiszolgáló <br/> Aktuális alverzió |Rugalmas kiszolgáló (előzetes verzió) <br/> Aktuális alverzió  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 5,6-es verzió |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (kivonva) | Nem támogatott|
|MySQL 5,7-es verzió | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 8,0-es verzió | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Tekintse meg a verzió támogatási szabályzatát a verziók [támogatásával kapcsolatos szabályzat dokumentációjában.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

A szolgáltatás jelenleg a főverzió frissítését támogatja a MySQL v 5.6-ról v 5.7-re való frissítéshez. További részletekért tekintse [meg a főbb verziófrissítések elvégzését ismertető témakört](how-to-major-version-upgrade.md). Ha 5,7-ről 8,0-re kíván frissíteni, javasoljuk, hogy a [memóriaképet és a visszaállítást](./concepts-migrate-dump-restore.md) olyan kiszolgálóra végezze, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>Következő lépések

- A Azure Database for MySQL verziószámozási szabályzatával kapcsolatos részletekért tekintse meg [a következő dokumentumot](concepts-version-policy.md):.
- A **szolgáltatási szintek** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)