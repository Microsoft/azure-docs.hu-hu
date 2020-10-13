---
title: Azure-régiók áthelyezése – Azure Portal – Azure Database for MySQL
description: Egy Azure Database for MySQL-kiszolgáló áthelyezése egyik Azure-régióból a másikba egy olvasási replika és a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: e0333c5cabec597261938765298b622bf2fe79a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542512"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Azure Database for MySQL-kiszolgáló áthelyezése másik régióba a Azure Portal használatával

A meglévő Azure Database for MySQL-kiszolgálók egyik régióból a másikba való áthelyezésének különböző forgatókönyvei vannak. Előfordulhat például, hogy a vész-helyreállítási tervezés részeként egy üzemi kiszolgálót szeretne áthelyezni egy másik régióba.

Egy Azure Database for MySQL régiók [közötti olvasási replikát](concepts-read-replicas.md#cross-region-replication) használhat a másik régióba való áttérés befejezéséhez. Ehhez először létre kell hoznia egy olvasási replikát a célhelyen. Ezután állítsa le a replikálást az olvasási replika kiszolgálójára, hogy egy önálló kiszolgáló legyen, amely az olvasási és írási forgalmat is elfogadja. 

> [!NOTE]
> Ez a cikk arra összpontosít, hogy a kiszolgálót egy másik régióba helyezze át. Ha át szeretné helyezni a kiszolgálót egy másik erőforráscsoporthoz vagy előfizetésbe, tekintse meg az [áthelyezéssel](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) foglalkozó cikket. 

## <a name="prerequisites"></a>Előfeltételek

- Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

- Győződjön meg arról, hogy a Azure Database for MySQL forráskiszolgáló abban az Azure-régióban található, amelyet át szeretne helyezni.

## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

Ha a Azure Portal a régión belüli olvasási replika kiszolgálót kívánja létrehozni a célhelyen, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/).
1. Válassza ki azt a meglévő Azure Database for MySQL kiszolgálót, amelyet forráskiszolgálóként kíván használni. Ez a művelet megnyitja az **Áttekintés** lapot.
1. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.
1. Válassza a **replika hozzáadása**lehetőséget.
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

1. A replika létrehozása után keresse meg és válassza ki a Azure Database for MySQL forráskiszolgáló. 
1. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.
1. Válassza ki a másodpéldány-kiszolgálót.
1. Válassza a **replikálás leállítása**lehetőséget.
1. Az **OK**gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

## <a name="clean-up-source-server"></a>Forráskiszolgáló karbantartása

Érdemes lehet törölni a forrás Azure Database for MySQL-kiszolgálót. Ehhez a következőket kell tennie:

1. A replika létrehozása után keresse meg és válassza ki a Azure Database for MySQL forráskiszolgáló.
1. Az **Áttekintés** ablakban válassza a **Törlés**lehetőséget.
1. Írja be a forráskiszolgáló nevét annak megerősítéséhez, hogy törölni kívánja a kiszolgálót.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Database for MySQL kiszolgálót helyezett át egyik régióból a másikba a Azure Portal használatával, majd megtisztította a szükségtelen forrás-erőforrásokat. 

- További információ az [olvasási replikáról](concepts-read-replicas.md)
- További információ [az olvasási replikák kezeléséről a Azure Portal](howto-read-replicas-portal.md)
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről
