---
title: Visszaállítás – rugalmas skálázás (Citus) – Azure Database for PostgreSQL – Azure Portal
description: Ez a cikk bemutatja, hogyan hajthatja végre a visszaállítási műveleteket a Azure Database for PostgreSQL – rugalmas skálázás (Citus) a Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495041"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Új kiszolgálócsoport időponthoz rugalmas skálázás (Citus) visszaállítása

Ez a cikk lépésenként beható [](concepts-hyperscale-backup.md#point-in-time-restore-pitr) eljárásokat tartalmaz a biztonsági másolatokat használó, rugalmas skálázás (Citus) kiszolgálócsoport időponthoz rugalmas skálázás (Citus) helyreállításához. A visszaállítást a legkorábbi biztonsági mentésre vagy egy egyéni visszaállítási pontra is visszaállíthatja a megőrzési időszakon belül.

## <a name="restoring-to-the-earliest-restore-point"></a>Visszaállítás a legkorábbi visszaállítási pontra

Az alábbi lépéseket követve visszaállíthatja a rugalmas skálázás (Citus)-kiszolgálócsoportot a legkorábbi meglévő biztonsági másolatra.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a visszaállítani kívánt kiszolgálócsoportot.

2.  A **bal oldali panelen** kattintson az Áttekintés, majd a **Visszaállítás elemre.**

    > [!IMPORTANT]
    > Ha a **Visszaállítás** gomb még nincs jelen a kiszolgálócsoporthoz, nyisson meg egy Azure-támogatás kérést.

3.  A visszaállítási oldal megkéri,  hogy válasszon **a** Legkorábbi és az Egyéni visszaállítási pont között, és megjeleníti a legkorábbi dátumot.

4.  Válassza a **Legkorábbi visszaállítási pont lehetőséget.**

5.  Adjon meg egy új kiszolgálócsoportnevet a **Visszaállítás új kiszolgálóra mezőben.** A többi mező (előfizetés, erőforráscsoport és hely) megjelenik, de nem szerkeszthető.

6.  Kattintson az **OK** gombra.

7.  Megjelenik egy értesítés arról, hogy a visszaállítási művelet el lett indítva.

Végül kövesse a [visszaállítás utáni feladatokat.](#post-restore-tasks)

## <a name="restoring-to-a-custom-restore-point"></a>Visszaállítás egyéni visszaállítási pontra

Az alábbi lépéseket követve visszaállíthatja a rugalmas skálázás (Citus)-kiszolgálócsoportot egy Ön által választott dátumra és időpontra.

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a visszaállítani kívánt kiszolgálócsoportot.

2.  A **bal oldali panelen** kattintson az Áttekintés, majd a Visszaállítás **elemre.**

    > [!IMPORTANT]
    > Ha a **Visszaállítás** gomb még nincs jelen a kiszolgálócsoporthoz, nyisson meg egy Azure-támogatás kérést.

3.  A visszaállítási oldal megkéri, hogy válasszon **a** **Legkorábbi** és az Egyéni visszaállítási pont közül, és a legkorábbi dátumot jeleníti meg.

4.  Válassza **az Egyéni visszaállítási pont lehetőséget.**

5.  Válassza ki a visszaállítási pont **(UTC)** dátumát és időpontját, és adjon meg egy új kiszolgálócsoport-nevet a **Visszaállítás új kiszolgálóra mezőben.** A többi mező (előfizetés, erőforráscsoport és hely) megjelenik, de nem szerkeszthető.
 
6.  Kattintson az **OK** gombra.

7.  Megjelenik egy értesítés arról, hogy a visszaállítási művelet el lett indítva.

Végül kövesse a [visszaállítás utáni feladatokat.](#post-restore-tasks)

## <a name="post-restore-tasks"></a>Post-restore tasks

A visszaállítás után a következőket kell megtennie, hogy a felhasználók és az alkalmazások ismét el tudjanak indulni:

* Ha az új kiszolgálót az eredeti kiszolgáló cseréjére szánta, irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra
* Győződjön meg arról, hogy a felhasználók számára megfelelő kiszolgálószintű tűzfal működik. Ezeket a szabályokat a rendszer nem másolja az eredeti kiszolgálócsoportból.
* Szükség szerint állítsa be a PostgreSQL-kiszolgáló paramétereit. A paraméterek nem másolhatók az eredeti kiszolgálócsoportból.
* Győződjön meg arról, hogy a megfelelő bejelentkezési adatok és adatbázisszintű engedélyek meg vannak biztosítva.
* Szükség szerint konfigurálja a riasztásokat.

## <a name="next-steps"></a>Következő lépések

* További információ a biztonsági [mentésről és a visszaállításról](concepts-hyperscale-backup.md) a rugalmas skálázás (Citus).
* Állítson [be javasolt riasztásokat](./howto-hyperscale-alert-on-metric.md#suggested-alerts) rugalmas skálázás (Citus) kiszolgálócsoportokhoz.
