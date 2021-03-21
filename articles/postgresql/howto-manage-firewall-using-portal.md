---
title: Tűzfalszabályok kezelése – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Tűzfalszabályok létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgáló számára a Azure Portal használatával
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 02bd4927216b6b60d2720e6f32c5768499e310bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022200"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Tűzfalszabályok létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgáló számára a Azure Portal használatával
A kiszolgálói szintű tűzfalszabályok egy adott IP-címről vagy IP-címtartományból származó Azure Database for PostgreSQL kiszolgáló elérésének kezelésére használhatók.

A Virtual Network-(VNet-) szabályok a kiszolgálóhoz való hozzáférés biztonságossá tételére is alkalmasak. További információ [Virtual Network szolgáltatási végpontok és szabályok létrehozásáról és kezeléséről a Azure Portal használatával](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy kiszolgáló [létrehoz egy Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
1. A PostgreSQL-kiszolgáló lapon, a beállítások fejléc alatt kattintson a **kapcsolatbiztonsági** elemre a Azure Database for PostgreSQL kapcsolatbiztonsági lapjának megnyitásához.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal – kattintson a kapcsolatbiztonsági lehetőségre":::

2. Kattintson a **saját IP-cím hozzáadása** elemre az eszköztáron. Ez automatikusan létrehoz egy tűzfalszabály a számítógép nyilvános IP-címével, az Azure-rendszer által észlelt módon.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal kattintson a saját IP-cím hozzáadása lehetőségre.":::

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos helyzetekben a Azure Portal által megfigyelt IP-cím eltér az Internet és az Azure-kiszolgálók elérésekor használt IP-címről. Ezért előfordulhat, hogy módosítania kell a kezdő IP-címet és a záró IP-címet, hogy a szabály a várt módon működjön.
   A saját IP-címének vizsgálatához használjon keresőmotort vagy más online eszközt. Keressen például a "mi az én IP-címe" kifejezésre.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Keresési Bing – mi az az IP-cím":::

4. További címtartományok hozzáadása. A Azure Database for PostgreSQL tűzfalszabályok esetében egyetlen IP-címet vagy címtartományt is megadhat. Ha egyetlen IP-címhez szeretné korlátozni a szabályt, a kezdő IP-cím és a záró IP-cím mezőben adja meg ugyanazt a címet. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára a PostgreSQL-kiszolgálón található bármely adatbázis elérését, amelyhez érvényes hitelesítő adatok tartoznak.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal – tűzfalszabályok":::

5. A kiszolgálói szintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várjon, amíg a rendszer megerősíti a tűzfalszabályok frissítésének sikerességét.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal – kattintson a Mentés gombra":::

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ha engedélyezni szeretné, hogy az Azure-alkalmazások csatlakozni tudjanak a Azure Database for PostgreSQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure-beli virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való kapcsolódáshoz. Az erőforrásoknak nem kell ugyanabban a Virtual Networkban (VNet) vagy erőforráscsoporthoz kell lenniük ahhoz, hogy engedélyezze ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezéséhez több módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Azt is megteheti **, hogy az** Azure- **szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre kattint a portálon a **kapcsolat biztonsági** paneljén, és megnyomja a **Mentés gombot**. Ha a kapcsolódási kísérlet nem engedélyezett, a kérelem nem éri el a Azure Database for PostgreSQL-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a gombra a + **saját IP-cím hozzáadása** lehetőségre. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...] elemre, majd a szabály eltávolításához kattintson a **Törlés** gombra. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- Ehhez hasonlóan parancsfájlokat is [létrehozhat Azure Database for PostgreSQL tűzfalszabályok létrehozásához és kezeléséhez az Azure CLI használatával](howto-manage-firewall-using-cli.md).
- További biztonságos hozzáférés a kiszolgálóhoz [Virtual Network szolgáltatási végpontok és szabályok létrehozásával és kezelésével a Azure Portal használatával](howto-manage-vnet-using-portal.md).
- Ha segítségre van a Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáshoz, tekintse meg a [Azure Database for PostgreSQL kapcsolódási kódtárait](concepts-connection-libraries.md).
