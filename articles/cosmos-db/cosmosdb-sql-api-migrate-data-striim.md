---
title: Az adatáttelepítés Azure Cosmos DB SQL API-fiókba a Striim használatával
description: Ismerje meg, hogyan telepítheti át az Striim egy Oracle-adatbázisból egy Azure Cosmos DB SQL API-fiókba.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 136853182e353ad5cd71981db5935fc3babe162e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339601"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Az adatáttelepítés Azure Cosmos DB SQL API-fiókba a Striim használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Az Azure piactéren elérhető Striim-rendszerkép folyamatos, valós idejű adatáthelyezést biztosít az adattárházak és az adatbázisok között az Azure-ba. Az adatok áthelyezésekor elvégezhető a beágyazott denormalizálás, az adatok átalakítása, a valós idejű elemzések és az adatjelentéskészítési forgatókönyvek. A Striim könnyen megkezdhető a vállalati adatAzure Cosmos DB SQL API-ba való folyamatos áthelyezéséhez. Az Azure egy Piactéri ajánlatot biztosít, amely megkönnyíti a Striim üzembe helyezését és az Azure Cosmos DBba való áttelepítését. 

Ez a cikk bemutatja, hogyan telepítheti át az Striim egy **Oracle-adatbázisból** egy **Azure Cosmos db SQL API-fiókba**.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

* A helyszínen futó Oracle-adatbázis, amely valamilyen adattal rendelkezik.

## <a name="deploy-the-striim-marketplace-solution"></a>A Striim Marketplace-megoldás üzembe helyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **erőforrás létrehozása** lehetőséget, és keresse meg a **Striim** az Azure Marketplace-en. Válassza ki az első lehetőséget, és **hozzon létre**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Striim Marketplace-tétel keresése":::

1. Ezután adja meg a Striim-példány konfigurációs tulajdonságait. A Striim-környezet üzembe helyezése egy virtuális gépen történik. Az **alapvető beállítások** panelen adja meg a virtuális gép **felhasználónevét**, a **virtuális gép jelszavát** (ezt a jelszót használja a rendszer az SSH-ba a virtuális géphez). Válassza ki az **előfizetését**, az **erőforráscsoportot** és a **hely részleteit** , ahol a Striim telepíteni szeretné. Ha elkészült, kattintson **az OK gombra**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="A Striim alapszintű beállításainak konfigurálása":::

1. A **Striim-fürt beállításai** ablaktáblán válassza ki a Striim-telepítés típusát és a virtuális gép méretét.

   |Beállítás | Érték | Leírás |
   | ---| ---| ---|
   |Striim központi telepítési típus |Különálló | A Striim **önálló** vagy **Fürtbeli** központi telepítési típusokban is futtathatók. Az önálló mód a Striim-kiszolgálót egyetlen virtuális gépre telepíti, és az adatmennyiségtől függően kiválaszthatja a virtuális gépek méretét. A fürt mód két vagy több virtuális gépre telepíti a Striim-kiszolgálót a kiválasztott mérettel. A több mint 2 csomóponttal rendelkező fürt-környezetek automatikus magas rendelkezésre állást és feladatátvételt biztosítanak.</br></br> Ebben az oktatóanyagban a különálló lehetőséget választhatja. Használja az alapértelmezett "Standard_F4s" méretű virtuális gépet.  | 
   | A Striim-fürt neve|    <Striim_cluster_Name>|  A Striim-fürt neve.|
   | Striim-fürt jelszava|   <Striim_cluster_password>|  A fürt jelszava.|

   Az űrlap kitöltése után a folytatáshoz kattintson **az OK gombra** .

1. A **Striim-hozzáférési beállítások** panelen konfigurálja a **nyilvános IP-címet** (válassza ki az alapértelmezett értékeket), a **Striim**, a Striim felhasználói felületére való bejelentkezéshez használni kívánt **rendszergazdai jelszót** . Konfigurálja a VNET és az alhálózatot (válassza az alapértelmezett értékeket). A részletek kitöltése után kattintson **az OK gombra** a folytatáshoz.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim hozzáférési beállításai":::

1. Az Azure ellenőrzi a telepítést, és gondoskodik róla, hogy minden jól látható legyen; az ellenőrzés elvégzése néhány percet vesz igénybe. Az ellenőrzés befejezése után kattintson **az OK gombra**.
  
1. Végül tekintse át a használati feltételeket, és válassza a **Létrehozás** lehetőséget a Striim-példány létrehozásához. 

## <a name="configure-the-source-database"></a>A forrásadatbázis konfigurálása 

Ebben a szakaszban az Oracle-adatbázist konfigurálja az adatáthelyezés forrásaként.  Az Oracle-hez való kapcsolódáshoz szüksége lesz az [Oracle JDBC-illesztőprogramra](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Ha olvasni szeretné a forrás Oracle-adatbázis változásait, használhatja a [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) vagy a [xStream API-kat](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Az Oracle JDBC illesztőprogramjának jelen kell lennie a Striim Java-osztályútvonal az Oracle-adatbázisból származó adatok olvasásához, írásához és megőrzéséhez.

Töltse le a [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) illesztőprogramot a helyi gépre. Később telepíteni fogja a Striim-fürtön.

## <a name="configure-the-target-database"></a>A céladatbázis konfigurálása

Ebben a szakaszban a Azure Cosmos DB SQL API-fiókot fogja konfigurálni az adatáthelyezés céljaként.

1. Hozzon létre egy [Azure Cosmos db SQL API-fiókot](create-cosmosdb-resources-portal.md) a Azure Portal használatával.

1. Navigáljon a **adatkezelő** panelre az Azure Cosmos-fiókjában. Új tároló létrehozásához válassza az **új tároló** lehetőséget. Tegyük fel, hogy áttelepíti a *termékeket* , és az Oracle Database-ből Azure Cosmos DBra *rendeli* az adatokról. Hozzon létre egy **StriimDemo** nevű új adatbázist egy **Orders** nevű tárolóval. A tároló kiépítése a **1000 RUS** használatával (ez a példa a 1000 RUS-t használja, de a számítási feladathoz becsült átviteli sebességet kell használnia), és **/ORDER_ID** partíciós kulcsként. Ezek az értékek a forrásadatoktől függően eltérőek lesznek. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="SQL API-fiók létrehozása":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Az Oracle konfigurálása Azure Cosmos DB adatfolyamatra

1. Most térjünk vissza a Striim. A Striim való interakció előtt telepítse a korábban letöltött Oracle JDBC-illesztőprogramot.

1. Navigáljon a Azure Portal üzembe helyezett Striim-példányhoz. A felső menüsorban kattintson a **kapcsolat** gombra, és az **SSH** lapon másolja ki az URL-címet a **Bejelentkezés a virtuális gép helyi fiókja mező használatával** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Az SSH URL-cím beszerzése":::

1. Nyisson meg egy új Terminálablak-ablakot, és futtassa a Azure Portalból másolt SSH-parancsot. Ez a cikk a Terminalt használja a MacOS-ben, a hasonló utasításokat a PuTTY vagy egy másik SSH-ügyfél használatával követheti Windows-gépen. Ha a rendszer kéri, írja be az **Igen értéket** a folytatáshoz, és adja meg az előző lépésben a virtuális géphez beállított **jelszót** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Kapcsolódás Striim virtuális géphez":::

1. Most nyisson meg egy új terminál fület a korábban letöltött **ojdbc8. jar** fájl másolásához. A következő SZOLGÁLTATÁSKAPCSOLÓDÁSI parancs használatával másolja a jar-fájlt a helyi gépről az Azure-ban futó Striim-példány tmp mappájába:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Másolja a jar-fájlt a hely gépről a Striim-be":::

1. Ezután lépjen vissza az ablakba, ahol az SSH-t a Striim-példányhoz, és jelentkezzen be sudo-ként. Helyezze át a **ojdbc8. jar** fájlt a **/tmp** könyvtárból a Striim-példány **lib** könyvtárába a következő parancsokkal:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="A jar-fájl áthelyezése a lib mappába":::


1. Ugyanabból a terminálból indítsa újra a Striim-kiszolgálót a következő parancsok végrehajtásával:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. A Striim egy percet vesz igénybe. Ha meg szeretné tekinteni az állapotot, futtassa a következő parancsot: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Most lépjen vissza az Azure-ba, és másolja a Striim virtuális gép nyilvános IP-címét. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Striim virtuális gép IP-címének másolása":::

1. A Striim webes felhasználói felületének megkereséséhez nyisson meg egy új fület egy böngészőben, és másolja a nyilvános IP-címet, majd a következőt: 9080. Jelentkezzen be a **rendszergazdai** felhasználónévvel, valamint a Azure Portal megadott rendszergazdai jelszóval.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Bejelentkezés a Striim":::

1. Most megérkezik a Striim kezdőlapján. Három különböző ablaktábla van – **irányítópultok**, **alkalmazások** és **SourcePreview**. Az irányítópultok panelen valós időben helyezheti át az adatátvitelt, és megjelenítheti azt. Az alkalmazások ablaktábla tartalmazza a folyamatos átviteli adatfolyamatokat vagy az adatfolyamatokat. Az oldal jobb oldalán a SourcePreview, ahol megtekintheti az adatait a mozgatás előtt.

1. Válassza az **alkalmazások** panelt, és most erre a panelre fogunk összpontosítani. A Striim megtanulásához számos különböző minta alkalmazás használható, ebben a cikkben azonban a sajátját fogja létrehozni. Kattintson az **alkalmazás hozzáadása** gombra a jobb felső sarokban.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="A Striim alkalmazás hozzáadása":::

1. A Striim-alkalmazások létrehozása néhány különböző módon történik. A meglévő sablonnal való kezdéshez válassza a **kezdés sablon** alapján lehetőséget.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Az alkalmazás elindítása a sablonnal":::

1. A **keresési sablonok** mezőbe írja be a "Cosmos" kifejezést, és válassza a **cél: Azure Cosmos db** lehetőséget, majd válassza **az Oracle CDC elemet Azure Cosmos db**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Az Oracle CDC kiválasztása Cosmos DB":::

1. A következő oldalon nevezze el az alkalmazást. Megadhat egy nevet, például **oraToCosmosDB** , majd a **Mentés** lehetőséget.

1. Ezután adja meg a forrás Oracle-példány forrás-konfigurációját. Adja meg a **forrás nevének** értékét. A forrás neve csak a Striim alkalmazás elnevezési konvenciója, például **src_onPremOracle** használhat. Adja meg a forrás paraméterek **URL-címének**, a **felhasználónévnek** és a **jelszónak** a többi értékét, válassza a **LogMiner** lehetőséget az Oracle-adatok olvasására szolgáló olvasóként. A folytatáshoz válassza a **Tovább** gombot.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Forrás paramétereinek konfigurálása":::

1. A Striim ellenőrzi a környezetet, és ellenőrizze, hogy tud-e csatlakozni a forrás Oracle-példányhoz, rendelkezik-e a megfelelő jogosultságokkal, és hogy a CDC megfelelően van-e konfigurálva. Az összes érték ellenőrzése után válassza a **tovább** lehetőséget.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Forrás paramétereinek ellenőrzése":::

1. Válassza ki az áttelepíteni kívánt Oracle Database-táblákat. Válassza például a Megrendelések táblát, és kattintson a **tovább** gombra. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Forrástábla kiválasztása":::

1. A forrástábla kiválasztása után bonyolultabb műveleteket végezhet, például a leképezést és a szűrést. Ebben az esetben csak a forrástábla replikáját kell létrehoznia Azure Cosmos DBban. A cél konfigurálásához kattintson a **tovább** gombra.

1. Most konfigurálja a célt:

   * **Cél neve** – adjon egy rövid nevet a célnak. 
   * **Bemenet** – a legördülő listából válassza ki a forrás Oracle-konfigurációban létrehozott bemeneti adatfolyamot. 
   * **Gyűjtemények**– adja meg a cél Azure Cosmos db konfigurációjának tulajdonságait. A gyűjtemények szintaxisa: **SourceSchema. SourceTable forrástábla neveként, TargetDatabase. TargetContainer**. Ebben a példában az érték a "SYSTEM. ORDERs, StriimDemo. Orders ". 
   * **AccessKey** – az Azure Cosmos-fiók PrimaryKey.
   * **ServiceEndpoint** – az Azure Cosmos-fiók URI-ja a Azure Portal **kulcsok** szakaszában található. 

   Válassza a **Mentés** és a **tovább** lehetőséget.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Cél paramétereinek konfigurálása":::


1. Ezután megérkezik a flow designerbe, ahol a streaming-alkalmazások létrehozásához húzhatja a Box-összekötőket. Ezen a ponton nem végez módosítást a folyamaton. Ezért az alkalmazás **üzembe** helyezése gomb kiválasztásával folytassa az alkalmazást.
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Az alkalmazás üzembe helyezése":::

1. A központi telepítés ablakban megadhatja, hogy az alkalmazás bizonyos részeit az üzembe helyezési topológia adott részein kívánja-e futtatni. Mivel az Azure-on keresztül egyszerű üzembe helyezési topológián fut, az alapértelmezett beállítást fogjuk használni.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Az alapértelmezett beállítás használata":::

1. A telepítés után megtekintheti a streamet, hogy láthassa az átáramló adatfolyamokat. Válassza ki a **Wave** ikont és a szemgolyót a mellette. Válassza az **üzembe helyezett** gombot a felső menüsorban, majd válassza az **alkalmazás indítása** lehetőséget.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Az alkalmazás elindítása":::

1. A **CDC (adatváltozások rögzítése)** olvasó használatával a Striim csak az adatbázis új módosításait fogja kiválasztani. Ha a forrástábla használatával áramlik át a folyamatokat, akkor azt fogja látni. Mivel azonban ez egy bemutató táblázat, a forrás nem kapcsolódik egyetlen alkalmazáshoz sem. Ha minta típusú adatgenerátort használ, beillesztheti az események láncát az Oracle-adatbázisba.

1. Ekkor a Striim platformon keresztül áramlik az adatforgalom. A Striim a táblához tartozó összes metaadatot is felveszi, ami hasznos lehet az adatok figyelésére, és gondoskodni róla, hogy az adatok a jobb oldalon legyenek kiképezve.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="A CDC-folyamat konfigurálása":::

1. Végül jelentkezzen be az Azure-ba, és navigáljon az Azure Cosmos-fiókjához. Frissítse a Adatkezelőt, és láthatja, hogy az adatai megérkeztek.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Áttelepített adatértékek ellenőrzése az Azure-ban":::

Az Azure-beli Striim megoldás használatával folyamatosan áttelepítheti az adatok Azure Cosmos DB különböző forrásokból, például Oracle, Cassandra, MongoDB és másoktól, hogy Azure Cosmos DB. Ha többet szeretne megtudni, látogasson el a [Striim webhelyére](https://www.striim.com/), [töltse le a Striim 30 napos ingyenes próbaverzióját](https://go2.striim.com/download-free-trial), és az áttelepítési útvonal a Striim-vel való beállításakor vegye fel a [támogatási kérelmet.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Következő lépések

* Ha Azure Cosmos DB SQL API-ra végez áttelepítést, olvassa el az [adatáttelepítés Cassandra API-fiókba az Striim használatával](cosmosdb-cassandra-api-migrate-data-striim.md) című témakört.

* [Az adatok monitorozása és hibakeresése Azure Cosmos DB metrikákkal](use-metrics.md)