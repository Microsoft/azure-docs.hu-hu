---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for MySQL
description: Megtudhatja, hogyan állíthatja be és kezelheti az olvasási replikákat a Azure Database for MySQL a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 26b503e7d55ed3d2f9bd06837551655e7af05a17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541940"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MySQL a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MySQL szolgáltatásban a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md) , amely forráskiszolgálóként lesz felhasználva.

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

Az olvasási replika kiszolgáló a következő lépések segítségével hozható létre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a meglévő Azure Database for MySQL kiszolgálót, amelyet főkiszolgálóként kíván használni. Ez a művelet megnyitja az **Áttekintés** lapot.

3. Válassza a **replikálás** lehetőséget a menü **Beállítások** területén.

4. Válassza a **replika hozzáadása** lehetőséget.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL – replikálás":::

5. Adja meg a replika kiszolgáló nevét.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL – replika neve":::

6. Válassza ki a replika-kiszolgáló helyét. Az alapértelmezett hely ugyanaz, mint a forráskiszolgáló.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL – replika helye":::

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

7. A replika létrehozásának jóváhagyásához kattintson **az OK gombra** .

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. A replika-kiszolgáló mindig ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a forráskiszolgálón. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](../azure-resource-manager/management/move-resource-group-and-subscription.md) . Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a forrásnál egyenlő vagy annál nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

A replika-kiszolgáló létrehozása után a **replikáció** panelről is megtekinthető.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL – replikák listázása":::

## <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást a forrás és a replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

A forrás és a replika közötti replikáció leállításához a Azure Portal hajtsa végre a következő lépéseket:

1. A Azure Portal válassza ki a forrás Azure Database for MySQL kiszolgálót. 

2. Válassza a **replikálás** lehetőséget a menü **Beállítások** területén.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelyen le szeretné állítani a replikálást.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL – replikálás leállítása kiszolgáló kiválasztása":::

4. Válassza a **replikálás leállítása** lehetőséget.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL – replikálás leállítása":::

5. Az **OK** gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL – replikálás leállítása – megerősítés":::

## <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Ha törölni szeretne egy olvasási replika kiszolgálót a Azure Portalről, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a forrás Azure Database for MySQL kiszolgálót.

2. Válassza a **replikálás** lehetőséget a menü **Beállítások** területén.

3. Válassza ki a törölni kívánt replika-kiszolgálót.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL – replika törlése kiszolgáló kiválasztása":::

4. **Replika törlésének** kiválasztása

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL – replika törlése":::

5. Írja be a replika nevét, és kattintson a **Törlés** gombra a replika törlésének megerősítéséhez.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL – replika törlése – megerősítés":::

## <a name="delete-a-source-server"></a>Forráskiszolgáló törlése

> [!IMPORTANT]
> A forráskiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a forráskiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A forráskiszolgáló Azure Portalból való törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a forrás Azure Database for MySQL kiszolgálót.

2. Az **Áttekintés** területen válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL – főkiszolgáló törlése":::

3. Írja be a forráskiszolgáló nevét, és kattintson a **Törlés** gombra a forráskiszolgáló törlésének megerősítéséhez.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL – főkiszolgáló törlésének megerősítése":::

## <a name="monitor-replication"></a>Replikáció monitorozása

1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt replikát Azure Database for MySQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **metrikák** elemet:

3. A rendelkezésre álló metrikák legördülő listájában válassza a **replikálás késése másodpercben** lehetőséget.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Replikációs késés kiválasztása":::

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi képen egy 30 perces időtartomány van kiválasztva.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Időtartomány kiválasztása":::

5. A kijelölt időtartomány replikációs késésének megtekintése. Az alábbi képen az elmúlt 30 perc látható.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Időtartomány kiválasztása 30 perc":::

## <a name="next-steps"></a>Következő lépések

- További információ az [olvasási replikáról](concepts-read-replicas.md)