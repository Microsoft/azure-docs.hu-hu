---
title: 'Gyors útmutató: Server-Azure Portal-Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozása'
description: Útmutató egy Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozásához és kezeléséhez a Azure Portal felhasználói felületének használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604753"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Gyors útmutató: Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása a Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for PostgreSQL rugalmas kiszolgálót körülbelül öt percen belül a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, és lépjen a [portálra](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Az Azure Database for PostgreSQL-kiszolgáló a [számítási és tárolási erőforrások](./concepts-compute-storage.md) konfigurált készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../../azure-resource-manager/management/overview.md) belül jön létre.

Azure Database for PostgreSQL-kiszolgáló létrehozásához kövesse az alábbi lépéseket:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL** elemet.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="A Azure Database for PostgreSQL a menüben":::

3. Válassza a **rugalmas kiszolgáló** üzembe helyezési lehetőséget.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Azure Database for PostgreSQL – rugalmas kiszolgáló üzembe helyezési lehetőségének kiválasztása":::

4. Töltse ki az **alapok** űrlapot a következő információkkal:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="A kiszolgáló létrehozása":::

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben az erőforrásért díjat kell fizetnie.
    Erőforráscsoport|*myresourcegroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.
    Rendszergazdai felhasználónév |*myadmin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **azure_pg_admin**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public**. Nem kezdődhet **pg_**.
    Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
    Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.
    Számítás + tárolás | **Általános célú**, **4 virtuális mag**, **512 GB**, **7 nap** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása** lehetőséget. *Általános célú*, *4 virtuális mag*, *512 GB* és *7 nap* a **számítási rétegek**, a **virtuális mag**, a **tárolók** és a **biztonsági másolatok megőrzési időszakának** alapértelmezett értékei. Ezeket a csúszkákat meghagyhatja, vagy módosíthatja is azokat. A tarifacsomag beállításának mentéséhez válassza az **OK** gombot. A következő képernyőkép ezeket a beállításokat tartalmazza.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="A díjszabási szintek panelje":::
    
5. Hálózati beállítások konfigurálása

    A hálózat lapon megadhatja, hogyan érhető el a kiszolgáló. Az Azure Database for PostgreSQL kiszolgálószinten hoz létre egy tűzfalat. Ez megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. Javasoljuk, hogy a kiszolgálót nyilvánosan elérhetővé tegye:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="A hálózatkezelés panel":::

    Majd korlátozza azt a saját ügyfél IP-címére:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Válassza az aktuális ügyfél IP-címének hozzáadása lehetőséget.":::

6. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

7. Az eszköztáron válassza az **Értesítések** ikont (csengő) az üzembehelyezési folyamat megfigyeléséhez. Az üzembe helyezés után kiválaszthatja a **Rögzítés az irányítópulton** lehetőséget, amellyel létrehoz egy csempét a kiszolgálóhoz az Azure Portal irányítópultján. A csempe a kiszolgáló **Áttekintés** oldalának közvetlen megnyitására szolgál. Az **Erőforrás megnyitása** lehetőség kiválasztásával megnyitja a kiszolgáló **Áttekintés** oldalát.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Az értesítések panel":::

   Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. (A másik alapértelmezett adatbázis az **azure_maintenance**. Az a feladata, hogy elkülönítse a felügyelt szolgáltatások folyamatait a felhasználói műveletektől. Ehhez az adatbázishoz nem lehet hozzáférni.)

    > [!NOTE]
    > A PostgreSQL-kiszolgálóhoz készült Azure-adatbázis kapcsolatai az 5432-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 5432-es portot.
    >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Az Azure Database for PostgreSQL-kiszolgáló létrehozásakor egy **postgres** nevű alapértelmezett adatbázis jön létre. Az adatbázis-kiszolgálóhoz való csatlakozáshoz a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra van szüksége. Ezeket a rövid útmutatóban korábban már feljegyezhette. Ha nem jegyezte fel, a kiszolgáló nevét és bejelentkezési adatait megtalálja a kiszolgáló **Áttekintés** lapján a portálon.

Nyissa meg kiszolgáló **Áttekintés** lapját. Jegyezze fel a **kiszolgálónevet** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Vigye az egérmutatót az egyes mezők fölé. Ekkor a szövegtől jobbra megjelenik a másolási szimbólum. Az értékek másolásához kattintson a másolási szimbólumra.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="A kiszolgáló áttekintő lapja":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. Használjuk a psql parancssori segédprogramot az Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql parancsot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   A csatlakozás után a psql segédprogram egy postgres promptot jelenít meg, ahová az SQL-parancsokat beírhatja. A rendszer a kezdeti kapcsolati kimenetben egy figyelmeztetést jeleníthet meg, mivel a psql használt verziója eltérhet az Azure Database for PostgreSQL-kiszolgáló verziójától.

   Példa psql kimenetre:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Ha a tűzfal nincs konfigurálva az ügyfél IP-címének engedélyezésére, a következő hiba jelenik meg:
   >
   > "psql: végzetes: nincs pg_hba. conf bejegyzés a gazdagéphez `<IP address>` ," myadmin "," postgres "adatbázis" ", SSL on végzetes: SSL-kapcsolat szükséges. Adja meg az SSL-beállításokat, és próbálkozzon újra.
   >
   > Győződjön meg arról, hogy az ügyfél IP-címe engedélyezve van a tűzfalszabályok fenti lépésében.

2. Hozzon létre egy mypgsqldb nevű üres adatbázist. Ehhez írja be a következő parancsot a parancssorba:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:

    ```bash
    \c mypgsqldb
    ```

4. A psql-ből való kilépéshez írja be a `\q` parancsot, majd nyomja le az Enter billentyűt.

Ezzel csatlakozott az Azure Database for PostgreSQL-kiszolgálóhoz a psql-en keresztül, és létrehozott egy üres felhasználói adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az Azure-erőforráscsoportot, amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak a kiszolgálói erőforrást törölje.

> [!TIP]
> Az ebben a gyűjteményben található többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy folytatja ezen rövid útmutatók használatát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutató segítségével létrehozott erőforrásokat a portálon.

Ha az újonnan létrehozott kiszolgálót is magában foglaló teljes erőforráscsoportot törölni szeretné:

1. Keresse meg az erőforráscsoportot a portálon. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Válassza ki az erőforráscsoport nevét (ebben a példában **myresourcegroup**).

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Adja meg az erőforráscsoport nevét, például a **myresourcegroup**, a szövegmezőben a törlés megerősítéséhez. Válassza a **Törlés** elemet.

Csak az újonnan létrehozott kiszolgáló törlése:

1. Keresse meg a kiszolgálót a portálon, ha nincs megnyitva. A bal oldali menüben válassza a **Minden erőforrás** elemet. Ezután keressen rá az Ön által létrehozott kiszolgálóra.

2. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="A törlés gomb":::

3. Erősítse meg a törölni kívánt kiszolgáló nevét, és tekintse meg az érintett adatbázisokat. Adja meg a kiszolgáló nevét a szövegmezőben, például: **mydemoserver**. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Django-alkalmazás üzembe helyezése App Service és PostgreSQL használatával](tutorial-django-app-service-postgres.md)