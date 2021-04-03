---
title: Biztonsági mentés és visszaállítás – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan állíthatja vissza a kiszolgálót Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 82cec4cc448f0ec30aecf6f8a69f399e0abbdde0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97706949"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Kiszolgálók biztonsági mentése és visszaállítása Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával

## <a name="backup-happens-automatically"></a>A biztonsági mentés automatikusan megtörténik
A visszaállítási funkciók engedélyezéséhez rendszeresen biztonsági mentést kell készíteni Azure Database for PostgreSQL-kiszolgálókról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló létrehozásakor a **díjszabási** szinten ablakban beállíthatja, hogy a kiszolgálót helyileg redundáns biztonsági mentésekhez vagy földrajzilag redundáns biztonsági másolatokhoz konfigurálja.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vagy helyileg redundáns, nem állítható be.
>

Amikor a Azure Portalon keresztül hoz létre kiszolgálót, a **díjszabási rétegek** ablakban a **helyi redundáns** vagy **földrajzilag redundáns** biztonsági másolatokat választhatja a kiszolgáló számára. Ezen az ablakban is kiválaszthatja a **biztonsági mentés megőrzési időtartamát** – mennyi ideig (hány nap múlva) kívánja tárolni a kiszolgáló biztonsági másolatait.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Díjszabási réteg – a biztonsági másolatok redundanciának kiválasztása":::

További információ ezekről az értékekről a létrehozás során: [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md)gyors üzembe helyezése.

A kiszolgáló biztonsági mentési megőrzési időszaka a következő lépésekkel módosítható:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját. Ez a művelet megnyitja az **Áttekintés** lapot.
3. Válassza ki az **árképzési szintet** a menü **Beállítások** területén. A csúszka használatával megváltoztathatja a **biztonsági mentés megőrzési időtartamát** 7 és 35 nap között.
Az alábbi képernyőképen a 34 napra nőtt.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="A biztonsági másolatok megőrzési időszaka nagyobb":::

4. A módosítás megerősítéséhez kattintson **az OK** gombra.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítást a következő szakasz ismerteti részletesebben. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás
Azure Database for PostgreSQL lehetővé teszi, hogy visszaállítsa a kiszolgálót egy adott időpontra, és a-kiszolgáló egy új példányára. Ezt az új kiszolgálót használhatja az adatok helyreállításához, vagy az ügyfélalkalmazások erre az új kiszolgálóra mutatnak.

Ha például egy táblázatot véletlenül a mai napig dobott el, akkor a visszaállíthatja a déli idő előtt, és lekérheti a hiányzó táblát és adatforrást a kiszolgáló új példányáról. Az időponthoz való visszaállítás a kiszolgáló szintjén, nem pedig az adatbázis szintjén történik.

A következő lépésekkel állíthatja vissza a kiszolgálót egy időpontra:
1. A Azure Portal válassza ki a Azure Database for PostgreSQL kiszolgálót. 

2. A kiszolgáló **Áttekintés** lapjának eszköztárán válassza a **visszaállítás** lehetőséget.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – áttekintés – visszaállítás gomb":::

3. Töltse ki a Visszaállítás űrlapot a szükséges információkkal:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Azure Database for PostgreSQL – adatok visszaállítása":::
   - **Visszaállítási pont**: válassza ki azt az időpontot, amelyet vissza szeretne állítani.
   - **Célkiszolgáló**: adja meg az új kiszolgáló nevét.
   - **Hely**: nem választhatja ki a régiót. Alapértelmezés szerint ugyanaz, mint a forráskiszolgáló.
   - **Díjszabási** csomag: ezeket a paramétereket nem módosíthatja az időponthoz tartozó visszaállítás során. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson **az OK** gombra, ha vissza szeretné állítani a kiszolgálót egy adott időpontra való visszaállításhoz. 

5. A visszaállítás befejeződése után keresse meg a létrehozott új kiszolgálót annak ellenőrzéséhez, hogy az Adathelyreállítás a várt módon történt-e.

Az időponthoz tartozó visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely érvényes volt a meglévő kiszolgáló számára a kiválasztott időpontban. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet módosítani.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályokkal vagy VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

Ha a forrás PostgreSQL-kiszolgáló az ügyfél által felügyelt kulcsokkal van titkosítva, további szempontokért tekintse meg a [dokumentációt](concepts-data-encryption-postgresql.md) .

## <a name="geo-restore"></a>Geo-visszaállítás

Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for PostgreSQL elérhető.  

1. Válassza az **erőforrás létrehozása** gombot (+) a portál bal felső sarkában. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL** elemet.

   :::image type="content" source="./media/howto-restore-server-portal/1-navigate-to-postgres.png" alt-text="Navigáljon Azure Database for PostgreSQL.":::

2. Válassza az **egykiszolgálós** telepítés lehetőséget.

   :::image type="content" source="./media/howto-restore-server-portal/2-select-deployment-option.png" alt-text="Válassza Azure Database for PostgreSQL – Egykiszolgálós telepítés lehetőséget.":::
 
3. Adja meg az előfizetést, az erőforráscsoportot és az új kiszolgáló nevét. 

4. **Adatforrásként** válassza a **biztonsági mentés** lehetőséget. Ez a művelet betölti a legördülő listát, amely felsorolja azokat a kiszolgálókat, amelyeken engedélyezve vannak a Geo redundáns biztonsági másolatok.
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore.png" alt-text="Válassza ki az adatforrást.":::
    
   > [!NOTE]
   > A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
   >

5. Válassza ki a **biztonsági mentés** legördülő menüt.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-geo-restore-backup.png" alt-text="Válassza a biztonsági mentés legördülő menüt.":::

6. Válassza ki a visszaállítani kívánt forráskiszolgáló-kiszolgálót.
   
   :::image type="content" source="./media/howto-restore-server-portal/6-select-backup.png" alt-text="Válassza a biztonsági mentés lehetőséget.":::

7. A kiszolgáló alapértelmezés szerint a **virtuális mag** számának, a **biztonsági másolatok megőrzési idejének**, a **biztonsági mentési redundancia beállításnak**, a **motor verziószámának** és a **rendszergazdai hitelesítő adatoknak** a értékét fogja megadni Válassza a **Folytatás** lehetőséget. 
   
   :::image type="content" source="./media/howto-restore-server-portal/7-accept-backup.png" alt-text="Folytassa a biztonsági mentéssel.":::

8. Adja meg a többi űrlapot a beállításokkal. Bármelyik **helyet** kiválaszthatja.

    A hely kiválasztása után a **kiszolgáló konfigurálása** lehetőség kiválasztásával frissítheti a **számítási generációt** (ha elérhető a kiválasztott régióban), a **virtuális mag**, a **biztonsági mentés megőrzési idejét** és a **biztonsági mentési redundancia beállítást**. Az **árképzési szint** (alapszintű, általános célú vagy memória optimalizálása) vagy a **tárolási** méret módosítása a visszaállítás során nem támogatott.

   :::image type="content" source="./media/howto-restore-server-portal/8-create.png" alt-text="Űrlap kitöltése"::: 

9. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. 

10. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

A földrajzi visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás kezdeményezésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályokkal vagy VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

Ha a forrás PostgreSQL-kiszolgáló az ügyfél által felügyelt kulcsokkal van titkosítva, további szempontokért tekintse meg a [dokumentációt](concepts-data-encryption-postgresql.md) .


## <a name="next-steps"></a>Következő lépések
- További információ a szolgáltatás [biztonsági mentéséről](concepts-backup.md).
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről.