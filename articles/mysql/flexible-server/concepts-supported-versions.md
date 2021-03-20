---
title: Támogatott verziók – Azure Database for MySQL rugalmas kiszolgáló
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verzióit támogatja a Azure Database for MySQL rugalmas kiszolgáló
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242262"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL-rugalmas kiszolgáló támogatott verziói


> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.


Azure Database for MySQL rugalmas kiszolgálót a [MySQL Community Edition](https://www.mysql.com/products/community/)működteti, a InnoDB motor használatával.

A MySQL az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-57"></a>MySQL 5,7-es verzió

Hibajavítási kiadás: 5.7.29

A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A MySQL motor esetében az alverziók frissítései a tervezett karbantartási kiadás részeként is szerepelnek. A felhasználók úgy konfigurálhatják a javítási ütemtervet, hogy a rendszer felügyelhető legyen, vagy definiálja az egyéni ütemtervet. A karbantartási ütemterv során a javítás érvénybe lép, és a kiszolgáló a javítási folyamat részeként újraindítást igényelhet a frissítés befejezéséhez. Az egyéni ütemterv segítségével a felhasználók előre jelezheti a javítási ciklust, és olyan karbantartási időszakot választhatnak, amelynek minimális hatása van a vállalatra. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ.

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.29 a 5.7.30.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[PHP-alkalmazás létrehozása Windowson MySQL-sel](../../app-service/tutorial-php-mysql-app.md)<br/>
>[PHP-alkalmazás létrehozása Linuxon a MySQL-vel](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Java-alapú Spring app létrehozása MySQL-sel](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>