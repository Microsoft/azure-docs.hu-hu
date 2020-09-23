---
title: Tűzfalszabályok kezelése – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Tűzfalszabályok létrehozása és kezelése Azure Database for MySQL-rugalmas kiszolgáló számára a Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 132319575147c2ff1075881b1f1faec8bc5029f4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940452"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Tűzfalszabályok létrehozása és kezelése Azure Database for MySQL-rugalmas kiszolgáló számára a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A rugalmas Azure Database for MySQL-kiszolgáló két, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

1. Nyilvános hozzáférés (engedélyezett IP-címek)
2. Privát hozzáférés (VNet-integráció)

Ebben a cikkben a MySQL-kiszolgáló **nyilvános hozzáféréssel (engedélyezett IP-címekkel)** való létrehozására összpontosítunk Azure Portal használatával, és áttekintést nyújt a tűzfalszabályok kezeléséről a rugalmas kiszolgáló létrehozása után. A *nyilvános hozzáférés (engedélyezett IP-címek)* esetében a MySQL-kiszolgálóval létesített kapcsolatok csak az engedélyezett IP-címekre korlátozódnak. Az ügyfél IP-címeit engedélyezni kell a tűzfalszabályok esetében. További információt a [nyilvános hozzáférés (engedélyezett IP-címek)](./concepts-networking.md#public-access-allowed-ip-addresses)című témakörben talál. A tűzfalszabályok meghatározhatók a kiszolgáló létrehozásakor (ajánlott), de később is hozzáadhatók. Ebben a cikkben áttekintjük, hogyan hozhatók létre és kezelhetők a tűzfalszabályok a nyilvános hozzáférés (engedélyezett IP-címek) használatával.

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Tűzfalszabály létrehozása kiszolgáló létrehozásakor

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.
2. Válassza az **adatbázisok**  >  **Azure Database for MySQL**elemet. Megadhatja a **MySQL** kifejezést is a keresőmezőbe a szolgáltatás megtalálásához.
3. Központi telepítési lehetőségként válassza a **rugalmas kiszolgáló** lehetőséget.
4. Töltse ki az **alapvető beállítások** űrlapot.
5. Lépjen a **hálózat** lapra, és konfigurálja, hogyan szeretne csatlakozni a kiszolgálóhoz.
6. A **kapcsolódási módszer**területen válassza a *nyilvános hozzáférés (engedélyezett IP-címek)* lehetőséget. A **Tűzfalszabályok**létrehozásához adja meg a tűzfalszabály nevét és az egyetlen IP-címet, vagy egy címtartományt. Ha a szabályt egyetlen IP-címhez szeretné korlátozni, írja be a címet a kezdő IP-cím és a záró IP-cím mezőbe. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára a MySQL-kiszolgálón található bármely adatbázis elérését, amelyhez érvényes hitelesítő adatok tartoznak.
   > [!Note]
   > Azure Database for MySQL rugalmas kiszolgáló létrehoz egy tűzfalat a kiszolgáló szintjén. Ez megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat.

7. A rugalmas kiszolgáló konfigurációjának áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.
8.  A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A kiépítés néhány percet is igénybe vehet.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Tűzfalszabály létrehozása a kiszolgáló létrehozása után

1. A [Azure Portal](https://portal.azure.com/)válassza ki azt a Azure Database for MySQL rugalmas kiszolgálót, amelyhez tűzfalszabályok hozzáadására van szükség.
2. A rugalmas kiszolgáló lapon a **Beállítások** fejléc alatt kattintson a **hálózatkezelés** elemre a rugalmas kiszolgáló hálózatkezelési lapjának megnyitásához.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. Kattintson az **aktuális ügyfél IP-címének hozzáadása** elemre a tűzfalszabályok között. Ez automatikusan létrehoz egy tűzfalszabály a számítógép nyilvános IP-címével, az Azure-rendszer által észlelt módon.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos helyzetekben a Azure Portal által megfigyelt IP-cím eltér az Internet és az Azure-kiszolgálók elérésekor használt IP-címről. Ezért előfordulhat, hogy módosítania kell a kezdő IP-címet és a záró IP-címet ahhoz, hogy a szabály a várt módon működjön.

   Használhatja a keresőmotort vagy más online eszközt a saját IP-címének vizsgálatához. Keressen például a "mi az én IP-címe" kifejezésre.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. További címtartományok hozzáadása. A Azure Database for MySQL rugalmas kiszolgáló tűzfalszabályok esetében egyetlen IP-címet vagy címtartományt is megadhat. Ha a szabályt egyetlen IP-címhez szeretné korlátozni, írja be a címet a kezdő IP-cím és a záró IP-cím mezőbe. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára a MySQL-kiszolgálón található bármely adatbázis elérését, amelyhez érvényes hitelesítő adatok tartoznak.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. Kattintson a **Save (Mentés** ) gombra az eszköztáron a tűzfalszabály mentéséhez. Várjon, amíg a rendszer megerősíti a tűzfalszabályok frissítésének sikerességét.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>Azure-beli kapcsolat

Érdemes lehet engedélyezni az Azure-ban üzembe helyezett erőforrásokat vagy alkalmazásokat a rugalmas kiszolgálóhoz való kapcsolódáshoz. Ide tartoznak a Azure App Serviceban üzemeltetett webalkalmazások, amelyek egy Azure-beli virtuális gépen, egy Azure Data Factory adatkezelési átjárón és sok más szolgáltatáson futnak.

Ha egy Azure-beli alkalmazás megpróbál csatlakozni a kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezettek-e. A beállítás engedélyezéséhez válassza az Azure **-szolgáltatások és-erőforrások nyilvános hozzáférésének engedélyezése az Azure-ban erre a kiszolgálóra** lehetőségre a **hálózat** lapon, majd a **Mentés**lehetőséget.

Az erőforrásoknak nem kell ugyanabban a virtuális hálózatban (VNet) vagy erőforráscsoporthoz a tűzfalszabály számára engedélyezniük a kapcsolatokat. Ha a kapcsolódási kísérlet nem engedélyezett, a kérés nem éri el a Azure Database for MySQL rugalmas kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
>
> Javasoljuk, hogy a **privát hozzáférést (VNet-integrációt)** a rugalmas kiszolgáló biztonságos eléréséhez válassza.
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Meglévő tűzfalszabályok kezelése a Azure Portal

A tűzfalszabályok kezeléséhez ismételje meg a következő lépéseket.

- Az aktuális számítógép hozzáadásához kattintson az + **aktuális ügyfél IP-címének hozzáadása** elemre a tűzfalszabályok között. Kattintson a **Mentés** gombra a módosítások mentéséhez.
- További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
- Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
- Meglévő szabály törléséhez kattintson a három pont [...] elemre, majd a szabály eltávolításához kattintson a **Törlés** gombra. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI-](./how-to-manage-firewall-cli.md)vel.