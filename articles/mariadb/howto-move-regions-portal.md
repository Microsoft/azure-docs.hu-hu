---
title: Azure-régiók áthelyezése – Azure Portal – Azure Database for MariaDB
description: Egy Azure Database for MariaDB-kiszolgáló áthelyezése egyik Azure-régióból a másikba egy olvasási replika és a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: f4ce34bc1a1af7b2c0ee57a3297415bd9d033517
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664936"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Azure Database for MariaDB-kiszolgáló áthelyezése másik régióba a Azure Portal használatával

A meglévő Azure Database for MariaDB-kiszolgálók egyik régióból a másikba való áthelyezésének különböző forgatókönyvei vannak. Előfordulhat például, hogy a vész-helyreállítási tervezés részeként egy üzemi kiszolgálót szeretne áthelyezni egy másik régióba.

Egy Azure Database for MariaDB régiók [közötti olvasási replikát](concepts-read-replicas.md#cross-region-replication) használhat a másik régióba való áttérés befejezéséhez. Ehhez először létre kell hoznia egy olvasási replikát a célhelyen. Ezután állítsa le a replikálást az olvasási replika kiszolgálójára, hogy egy önálló kiszolgáló legyen, amely az olvasási és írási forgalmat is elfogadja. 

> [!NOTE]
> Ez a cikk arra összpontosít, hogy a kiszolgálót egy másik régióba helyezze át. Ha át szeretné helyezni a kiszolgálót egy másik erőforráscsoporthoz vagy előfizetésbe, tekintse meg az [áthelyezéssel](../azure-resource-manager/management/move-resource-group-and-subscription.md) foglalkozó cikket. 

## <a name="prerequisites"></a>Előfeltételek

- Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

- Győződjön meg arról, hogy a Azure Database for MariaDB forráskiszolgáló abban az Azure-régióban található, amelyet át szeretne helyezni.

## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

Ha a Azure Portal a régión belüli olvasási replika kiszolgálót kívánja létrehozni a célhelyen, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki azt a meglévő Azure Database for MariaDB kiszolgálót, amelyet forráskiszolgálóként kíván használni. Ez a művelet megnyitja az **Áttekintés** lapot.
1. Válassza a **replikálás** lehetőséget a menü **Beállítások** területén.
1. Válassza a **replika hozzáadása** lehetőséget.
1. Adja meg a replika kiszolgáló nevét.
1. Válassza ki a replika-kiszolgáló helyét. Az alapértelmezett hely ugyanaz, mint a forráskiszolgáló. Győződjön meg arról, hogy kiválasztotta azt a célhelyet, ahová a replikát telepíteni kívánja.
1. A replika létrehozásának jóváhagyásához kattintson **az OK gombra** . A replika létrehozásakor a rendszer az adatok átmásolását a forráskiszolgálóról a replikába. A létrehozási idő több percig is tarthat, a forráskiszolgáló méretének megfelelően.

>[!NOTE]
> Replika létrehozásakor nem örökli a forráskiszolgáló VNet szolgáltatási végpontját. Ezeket a szabályokat a replika egymástól függetlenül kell beállítani.

## <a name="move"></a>Áthelyezés

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

A replikálás megszakítása a másodpéldány-kiszolgálón, az azt eredményezi, hogy az önálló kiszolgáló lesz. Ha le szeretné állítani a replikálást a Azure Portal a replikára, kövesse az alábbi lépéseket:

1. A replika létrehozása után keresse meg és válassza ki a Azure Database for MariaDB forráskiszolgáló. 
1. Válassza a **replikálás** lehetőséget a menü **Beállítások** területén.
1. Válassza ki a másodpéldány-kiszolgálót.
1. Válassza a **replikálás leállítása** lehetőséget.
1. Az **OK** gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

## <a name="clean-up-source-server"></a>Forráskiszolgáló karbantartása

Érdemes lehet törölni a forrás Azure Database for MariaDB-kiszolgálót. Ehhez a következőket kell tennie:

1. A replika létrehozása után keresse meg és válassza ki a Azure Database for MariaDB forráskiszolgáló.
1. Az **Áttekintés** ablakban válassza a **Törlés** lehetőséget.
1. Írja be a forráskiszolgáló nevét annak megerősítéséhez, hogy törölni kívánja a kiszolgálót.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Database for MariaDB kiszolgálót helyezett át egyik régióból a másikba a Azure Portal használatával, majd megtisztította a szükségtelen forrás-erőforrásokat. 

- További információ az [olvasási replikáról](concepts-read-replicas.md)
- További információ [az olvasási replikák kezeléséről a Azure Portal](howto-read-replicas-portal.md)
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről