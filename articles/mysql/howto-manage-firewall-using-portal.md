---
title: Tűzfalszabályok kezelése – Azure Portal – Azure Database for MySQL
description: Azure Database for MySQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540484"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Azure Database for MySQL tűzfalszabályok létrehozása és kezelése a Azure Portal használatával
A kiszolgálói szintű tűzfalszabályok használatával felügyelhető egy adott IP-címről vagy IP-címről érkező Azure Database for MySQL-kiszolgálóhoz való hozzáférés. 

A Virtual Network-(VNet-) szabályok a kiszolgálóhoz való hozzáférés biztonságossá tételére is alkalmasak. További információ [Virtual Network szolgáltatási végpontok és szabályok létrehozásáról és kezeléséről a Azure Portal használatával](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

1. A MySQL-kiszolgáló lapon, a beállítások fejléc alatt kattintson a **kapcsolatbiztonsági** elemre a Azure Database for MySQL kapcsolatbiztonsági lapjának megnyitásához.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal – kattintson a kapcsolatbiztonsági lehetőségre":::

2. Kattintson a **saját IP-cím hozzáadása** elemre az eszköztáron. Ez automatikusan létrehoz egy tűzfalszabály a számítógép nyilvános IP-címével, az Azure-rendszer által észlelt módon.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal kattintson a saját IP-cím hozzáadása lehetőségre.":::

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos helyzetekben a Azure Portal által megfigyelt IP-cím eltér az Internet és az Azure-kiszolgálók elérésekor használt IP-címről. Ezért előfordulhat, hogy módosítania kell a kezdő IP-címet és a záró IP-címet, hogy a szabály a várt módon működjön.

   A saját IP-címének vizsgálatához használjon keresőmotort vagy más online eszközt. Például keressen rá a "mi az IP-cím" kifejezésre.

4. További címtartományok hozzáadása. A Azure Database for MySQL tűzfalszabályok esetében egyetlen IP-címet vagy címtartományt is megadhat. Ha a szabályt egyetlen IP-címhez szeretné korlátozni, akkor a kezdő IP-cím és a végpont IP-címe mezőben adja meg ugyanazt a címet. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára a MySQL-kiszolgálón található bármely adatbázis elérését, amelyhez érvényes hitelesítő adatok tartoznak.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal – tűzfalszabályok":::

5. A kiszolgálói szintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várjon, amíg a rendszer megerősíti, hogy a tűzfalszabályok frissítése sikeresen megtörtént.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal – kattintson a Mentés gombra":::

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ha engedélyezni szeretné, hogy az Azure-alkalmazások csatlakozni tudjanak a Azure Database for MySQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure-beli virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való kapcsolódáshoz. Az erőforrásoknak nem kell ugyanabban a Virtual Networkban (VNet) vagy erőforráscsoporthoz kell lenniük ahhoz, hogy engedélyezze ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezéséhez több módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Azt is megteheti **, hogy az** Azure- **szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre kattint a portálon a **kapcsolat biztonsági** paneljén, és megnyomja a **Mentés gombot**. Ha a kapcsolódási kísérlet nem engedélyezett, a kérelem nem éri el a Azure Database for MySQL-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>A meglévő kiszolgálói szintű tűzfalszabályok kezelése a Azure Portal használatával
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a **+ saját IP-cím hozzáadása** lehetőségre. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához írja be a **szabály nevét**, az **IP-** cím és a **záró IP**-címet. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály bármelyik mezőjére, majd módosítsa a következőt:. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pontra [...], majd a **Törlés** gombra. Kattintson a **Mentés** gombra a módosítások mentéséhez.


## <a name="next-steps"></a>Következő lépések
- Ehhez hasonlóan parancsfájlokat is [létrehozhat Azure Database for MySQL tűzfalszabályok létrehozásához és kezeléséhez az Azure CLI használatával](howto-manage-firewall-using-cli.md).
- További biztonságos hozzáférés a kiszolgálóhoz [Virtual Network szolgáltatási végpontok és szabályok létrehozásával és kezelésével a Azure Portal használatával](howto-manage-vnet-using-portal.md).
- Ha segítségre van a Azure Database for MySQL-kiszolgálóhoz való csatlakozáshoz, tekintse meg a [Azure Database for MySQL kapcsolódási kódtárait](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
