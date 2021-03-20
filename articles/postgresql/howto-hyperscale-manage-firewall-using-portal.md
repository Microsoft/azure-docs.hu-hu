---
title: Tűzfalszabályok kezelése – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Tűzfalszabályok létrehozása és kezelése a Azure Database for PostgreSQL-nagy kapacitású (Citus) számára a Azure Portal használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/11/2020
ms.openlocfilehash: dadd04497eae0e91bdf5ea3caad38beda35f7fa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91275421"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok kezelése
A kiszolgálói szintű tűzfalszabályok segítségével kezelheti a nagy kapacitású (Citus) koordinátor-csomópontokhoz való hozzáférést egy adott IP-cím vagy IP-címtartomány használatával.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- A kiszolgálócsoport [létrehoz egy Azure Database for PostgreSQL – nagy kapacitású (Citus) kiszolgálói csoportot](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

> [!NOTE]
> Ezek a beállítások egy Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoport létrehozása során is elérhetők. A **hálózatkezelés** lapon kattintson a **nyilvános végpont** elemre.

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Azure Portal – hálózatkezelés lap":::

1. A PostgreSQL-kiszolgáló csoport lapon, a biztonság fejléc alatt kattintson a **hálózatkezelés** elemre a tűzfalszabályok megnyitásához.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal kattintson a hálózatkezelés elemre":::

2. Kattintson az **aktuális ügyfél IP-címének hozzáadása** elemre a számítógép nyilvános IP-címével rendelkező tűzfalszabály létrehozásához az Azure-rendszer által észlelt módon.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal – kattintson az ügyfél IP-címének hozzáadása elemre.":::

Másik lehetőségként kattintson a **+ 0.0.0.0-255.255.255.255** (a B beállítástól jobbra) gombra, és nem csak az Ön IP-címét, hanem a teljes internetet a koordinátori csomópont 5432-as portjának eléréséhez. Ebben az esetben az ügyfeleknek továbbra is be kell jelentkezniük a megfelelő felhasználónévvel és jelszóval a fürt használatához. Azonban javasoljuk, hogy csak rövid ideig és csak a nem éles adatbázisok esetében engedélyezze a globális hozzáférést.

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos helyzetekben a Azure Portal által megfigyelt IP-cím eltér az Internet és az Azure-kiszolgálók elérésekor használt IP-címről. Ezért előfordulhat, hogy módosítania kell a kezdő IP-címet és a záró IP-címet, hogy a szabály a várt módon működjön.
   A saját IP-címének vizsgálatához használjon keresőmotort vagy más online eszközt. Keressen például a "mi az én IP-címe" kifejezésre.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Keresési Bing – mi az az IP-cím":::

4. További címtartományok hozzáadása. A tűzfalszabályok esetében egyetlen IP-címet vagy címtartományt is megadhat. Ha egyetlen IP-címhez szeretné korlátozni a szabályt, a kezdő IP-cím és a záró IP-cím mezőben adja meg ugyanazt a címet. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára, hogy hozzáférjenek a koordinátori csomóponthoz a 5432-es porton.

5. A kiszolgálói szintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várjon, amíg a rendszer megerősíti a tűzfalszabályok frissítésének sikerességét.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Az Azure-ban üzemeltetett alkalmazásokhoz (például Azure Web Apps alkalmazásokhoz vagy Azure-beli virtuális gépekhez) egyszerűen biztosítható nagy kapacitású (Citus) adatbázis-hozzáférés. Egyszerűen állítsa be az **Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálócsoport** számára beállítást **Igen** értékre a portálon a **hálózat** ablaktáblán, és kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a gombra az **aktuális ügyfél IP-címének hozzáadásához**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...] elemre, majd a szabály eltávolításához kattintson a **Törlés** gombra. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- További információ a [Tűzfalszabályok fogalmáról](concepts-hyperscale-firewall-rules.md), többek között a kapcsolódási problémák elhárításáról.
