---
title: Támogatott verziók – Azure Database for MariaDB
description: Megtudhatja, hogy a MariaDB-kiszolgáló mely verziói támogatottak a Azure Database for MariaDB szolgáltatásban.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662440"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Támogatott Azure Database for MariaDB Server-verziók

A Azure Database for MariaDB a nyílt forráskódú [MariaDB-kiszolgálóról](https://downloads.mariadb.org/)lett kifejlesztve a InnoDB motor használatával.

A MariaDB az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y az alverzió, a Z pedig a javítás verziója.

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél megjeleníti az átjárón beállított MariaDB-verziót, nem pedig a MariaDB-kiszolgáló példányán futó tényleges verziót. A MariaDB Server-példány verziójának meghatározásához használja az `SELECT VERSION();` parancsot.

A Azure Database for MariaDB jelenleg a következő verziót támogatja:

## <a name="mariadb-version-102"></a>MariaDB 10,2-es verzió

Javítás verziója: 10.2.32

Tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/mariadb-10232-release-notes/) , és ismerkedjen meg a jelen verzióban található javításokkal és javításokkal.

## <a name="mariadb-version-103"></a>MariaDB 10,3-es verzió

Javítás verziója: 10.3.23

Tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/mariadb-10323-release-notes/) , és ismerkedjen meg a jelen verzióban található javításokkal és javításokkal.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a javítások frissítéseit. Például 10.2.21 a 10.2.23.  

Az al- vagy a főverzió frissítései jelenleg nem támogatottak. Például a MariaDB 10.2-es verziójáról a MariaDB 10.3-as verziójára történő frissítés nem támogatott. Ha 10,2-ről 10,3-re szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./howto-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>Következő lépések

- A **szolgáltatási szintek** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md).
