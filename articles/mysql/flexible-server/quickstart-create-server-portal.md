---
title: 'Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozása – Azure Portal'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Database for MySQL rugalmas kiszolgálót percek alatt a Azure Portal használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 074b799a4f0e83c47aac0b2b3fca5386bd45429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521968"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozásához használja a Azure Portal

Azure Database for MySQL rugalmas kiszolgáló egy felügyelt szolgáltatás, amellyel a felhőben futtathatja, kezelheti és méretezheti a magasan elérhető MySQL-kiszolgálókat. Ez a rövid útmutató bemutatja, hogyan hozhat létre rugalmas kiszolgálót a Azure Portal használatával.

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg az [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló létrehozása

Hozzon létre egy rugalmas kiszolgálót a [számítási és tárolási erőforrások](./concepts-compute-storage.md)egy meghatározott készletével. A kiszolgálót egy [Azure-erőforráscsoporton](../../azure-resource-manager/management/overview.md) belül hozza létre.

Egy rugalmas kiszolgáló létrehozásához hajtsa végre a következő lépéseket:

1. **Azure Database for MySQL kiszolgálók** keresése és kiválasztása a portálon:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="A Azure Database for MySQL-kiszolgálók keresését bemutató képernyőkép.":::

2. Válassza a **Hozzáadás** lehetőséget. 

3. A **Azure Database for MySQL központi telepítési lehetőség kiválasztása** lapon válassza a **rugalmas kiszolgáló** lehetőséget a központi telepítési lehetőségként:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="A rugalmas kiszolgáló beállítást megjelenítő képernyőkép.":::    

4. Az **Alapvető beállítások** lapon adja meg a következőket: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Képernyőkép, amely a rugalmas kiszolgáló lap alapjai lapjait jeleníti meg."::: 
                                    
    |**Beállítás**|**Ajánlott érték**|**Leírás**|
    |---|---|---|
    Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben az erőforrásért díjat kell fizetnie.|
    Erőforráscsoport|**myresourcegroup**| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.|
    Kiszolgálónév |**mydemoserver**|Egy egyedi név, amely a rugalmas kiszolgálót azonosítja. A rendszer hozzáfűzi a tartománynevet `mysql.database.azure.com` a megadott kiszolgálónévhez. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A jelszó 3–63 karakterből állhat.|
    Rendszergazdai felhasználónév |**mydemouser**| Saját bejelentkezési fiókja, amelyet a kiszolgálóhoz való csatlakozáskor kell használni. A rendszergazda felhasználóneve nem lehet **azure_superuser**, **Admin**, **Administrator**, **root**, **Guest** vagy **Public**.|
    Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. Tartalmaznia kell a következő kategóriák közül legalább háromat is: angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0 – 9) és nem alfanumerikus karakterek (!, $, #,% stb.).|
    Region|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.|
    Verzió|**5.7**| Egy MySQL főverzió.|
    Számítás + tárolás | **Feltört**, **Standard_B1ms**, **10 GIB**, **7 nap** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása** lehetőséget. A **számítási szint**, a **számítási méret**, a **tárterület mérete** és a biztonsági másolatok **megőrzési időszaka** alapértelmezett értékei a következők: **feltört**, **Standard_B1ms**, **10 GIB** és **7 nap** . Ezeket az értékeket elhagyhatja, vagy módosíthatja is azokat. A számítási és a tárolási beállítások mentéséhez kattintson a **Mentés** gombra a konfiguráció folytatásához. A következő képernyőfelvételen a számítási és tárolási lehetőségek láthatók.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="A számítási és tárolási beállításokat megjelenítő képernyőkép.":::

5. Konfigurálja a hálózati beállításokat.

    A **hálózatkezelés** lapon megadhatja, hogyan legyen elérhető a kiszolgáló. Azure Database for MySQL rugalmas kiszolgáló két módszert biztosít a kiszolgálóhoz való kapcsolódásra: 
   - Nyilvános hozzáférés (engedélyezett IP-címek)
   - Privát hozzáférés (VNet-integráció) 
   
   Ha nyilvános hozzáférést használ, a kiszolgálóhoz való hozzáférés a tűzfalszabály számára hozzáadott engedélyezett IP-címekre korlátozódik. Ez a módszer megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely egy adott IP-cím vagy tartomány számára megnyitja a tűzfalat. Ha privát hozzáférést (VNet-integrációt) használ, a kiszolgálóhoz való hozzáférés a virtuális hálózatra korlátozódik. Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a nyilvános hozzáférést a kiszolgálóhoz való kapcsolódáshoz. [További információ a kapcsolati módszerekről a fogalmakat ismertető cikkben.](./concepts-networking.md)

    > [!NOTE]
    > A kapcsolati módszer nem módosítható a kiszolgáló létrehozása után. Ha például a kiszolgáló létrehozásakor a **nyilvános hozzáférés (engedélyezett IP-címek)** lehetőséget választja, akkor a kiszolgáló létrehozása után nem lehet a **privát hozzáférésre váltani (VNet-integráció)** . Javasoljuk, hogy a kiszolgálót a VNet-integráción keresztül a kiszolgálóhoz való hozzáférés biztonságossá tételéhez hozzon létre privát hozzáféréssel. [További információ a privát hozzáférésről a fogalmakat ismertető cikkben.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="A hálózatkezelés lapot megjelenítő képernyőkép.":::  

6. A rugalmas kiszolgáló konfigurációjának áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.

7. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A kiépítés néhány percet is igénybe vehet.

8. Az eszköztáron kattintson az **értesítések** elemre (a harang gombra) a telepítési folyamat figyeléséhez. Az üzembe helyezés befejezése után kiválaszthatja a **rögzítés az irányítópulton** lehetőséget, amely létrehoz egy csempét a rugalmas kiszolgáló számára a Azure Portal irányítópulton. Ez a csempe a kiszolgáló **Áttekintés** lapjára mutató parancsikon. Amikor kiválasztja az **erőforrás** megnyitása lehetőséget, megnyílik a kiszolgáló **Áttekintés** lapja.

Alapértelmezés szerint ezek az adatbázisok a kiszolgáló alatt jönnek létre: information_schema, MySQL, performance_schema és sys.

> [!NOTE]
> A kapcsolódási problémák elkerülése érdekében ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 3306-as porton keresztül, amelyet Azure Database for MySQL rugalmas kiszolgáló használ.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Kapcsolódás a kiszolgálóhoz a mysql.exe használatával

Ha a rugalmas kiszolgálót privát hozzáférés (VNet-integráció) használatával hozta létre, akkor a kiszolgálóval azonos virtuális hálózaton belüli erőforrással kell csatlakoznia a kiszolgálóhoz. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott virtuális hálózathoz. További tudnivalókért tekintse meg a [privát hozzáférési dokumentáció](how-to-manage-virtual-network-portal.md) konfigurálását ismertető témakört.

Ha a rugalmas kiszolgálót a nyilvános hozzáférés (engedélyezett IP-címek) használatával hozta létre, a helyi IP-címet hozzáadhatja a kiszolgálón található tűzfalszabályok listájához. További útmutatásért tekintse meg a [Tűzfalszabályok létrehozásával és kezelésével kapcsolatos dokumentációt](how-to-manage-firewall-portal.md) .

A helyi környezetből [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy [MySQL Workbench](./connect-workbench.md) használatával kapcsolódhat a kiszolgálóhoz. 

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Ha a rugalmas kiszolgálót **nyilvános hozzáférés** használatával állította be, akkor a [Azure Cloud Shell](https://shell.azure.com/bash) használatával is csatlakozhat a rugalmas kiszolgálóhoz az előre telepített MySQL-ügyféllel az alábbi ábrán látható módon:

Ahhoz, hogy a Azure Cloud Shellt a rugalmas kiszolgálóhoz való kapcsolódáshoz használhassa, engedélyeznie kell a hálózati hozzáférést a Azure Cloud Shell a rugalmas kiszolgálóra. Ennek eléréséhez nyissa meg a **hálózatkezelés** panelt Azure Portal a MySQL-hez készült rugalmas kiszolgálónál, és jelölje be a **tűzfal** szakaszban található jelölőnégyzetet, amely a "nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatásból az Azure-ban ezen a kiszolgálón" lehetőséget, majd kattintson a Mentés gombra a beállítás megőrzéséhez.

> [!NOTE]
> Annak ellenőrzése, **hogy a nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatáson belül erre a kiszolgálóra** csak fejlesztési vagy tesztelési célokra használható. Úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-szolgáltatásokhoz vagy-eszközökhöz lefoglalt IP-címekről érkező kapcsolatokat, beleértve a más ügyfelek előfizetéseit is.

Kattintson a **kipróbálás** gombra a Azure Cloud Shell elindításához és az alábbi parancsok használatával a rugalmas kiszolgálóhoz való kapcsolódáshoz. A parancsban használja a kiszolgáló nevét, felhasználónevét és jelszavát. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Ha a következő hibaüzenet jelenik meg, amikor a fenti parancsot követve csatlakozik a rugalmas kiszolgálóhoz, a korábban említett "nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatáshoz az Azure-ban erre a kiszolgálóra" beállítást nem sikerült beállítani, vagy a beállítás nincs mentve. Próbálkozzon újra a tűzfal beállításával, és próbálkozzon újra.

HIBA 2002 (HY000): nem lehet csatlakozni a MySQL-kiszolgálóhoz <servername> (115)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ezzel létrehozott egy Azure Database for MySQL rugalmas kiszolgálót egy erőforráscsoporthoz. Ha nem szeretné, hogy a jövőben is szükség lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével, vagy egyszerűen törölheti a MySQL-kiszolgálót is. Az erőforráscsoport törléséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok** elemet.
1. Az erőforráscsoportok listájában válassza ki az erőforráscsoport nevét.
1. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.
1. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A kiszolgáló törléséhez válassza a kiszolgáló **Áttekintés** lapján a **Törlés** lehetőséget, ahogy az itt látható:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="A kiszolgáló törlését bemutató képernyőkép.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [PHP-alapú (Laravel-) Webalkalmazás létrehozása MySQL-sel](tutorial-php-database-app.md)
