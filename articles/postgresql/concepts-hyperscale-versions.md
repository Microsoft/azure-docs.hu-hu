---
title: Támogatott verziók – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: 'A PostgreSQL verziója elérhető a következőben: Azure Database for PostgreSQL-nagy kapacitású (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023959"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Támogatott adatbázis-verziók a Azure Database for PostgreSQL – nagy kapacitású (Citus)

## <a name="postgresql-versions"></a>PostgreSQL-verziók

> [!IMPORTANT]
> A nagy kapacitású (Citus) testreszabható PostgreSQL-verziói jelenleg előzetes verzióban érhetők el.  Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

A nagy kapacitású-(Citus-) kiszolgálócsoportban futó PostgreSQL verziója testreszabható a létrehozás során. Ha a 11-es verziótól eltérő elemet választ, jelenleg előzetes verziójú szolgáltatásként érhető el.

A nagy kapacitású (Citus) jelenleg a következő főbb verziókat támogatja:

### <a name="postgresql-version-13-preview"></a>PostgreSQL 13. verziója (előzetes verzió)

Az aktuális alverzió 13,2. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/13/static/release-13-2.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

### <a name="postgresql-version-12-preview"></a>PostgreSQL 12-es verziója (előzetes verzió)

Az aktuális alverzió 12,6. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/12/static/release-12-6.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

### <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió

Az aktuális alverzió 11,11. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-11.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL 10-es és régebbi verzió

Azure Database for PostgreSQL-nagy kapacitású (Citus) esetében nem támogatott a PostgreSQL 10-es és régebbi verziója.

## <a name="citus-and-other-extension-versions"></a>Citus és egyéb bővítmény-verziók

Attól függően, hogy a PostgreSQL melyik verzióját futtatja egy kiszolgálócsoport, a [postgres-bővítmények különböző verzióit](concepts-hyperscale-extensions.md) is telepíti a rendszer.  Különösen a postgres 13 a Citus 10, a korábbi postgres-verziók pedig a Citus 9,5-es verzióval rendelkeznek.

## <a name="next-steps"></a>Következő lépések

* Megtekintheti, hogy mely [bővítmények](concepts-hyperscale-extensions.md) vannak telepítve a verziókban.
* Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgáló csoportot](quickstart-create-hyperscale-portal.md).
