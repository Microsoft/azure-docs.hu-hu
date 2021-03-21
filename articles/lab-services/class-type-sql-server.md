---
title: Tesztkörnyezet beállítása a Azure SQL Database kezeléséhez és fejlesztéséhez | Azure Lab Services
description: Ismerje meg, hogyan állíthat be egy labort a Azure SQL Database kezeléséhez és fejlesztéséhez.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659709"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Tesztkörnyezet beállítása a SQL Server kezeléséhez és fejlesztéséhez

Ez a cikk azt ismerteti, hogyan állíthat be egy labort egy alapszintű SQL Server felügyeleti és fejlesztési osztályhoz a Azure Lab Servicesban.  Az adatbázis-fogalmak az egyik bevezető tanfolyam, amelyet a főiskolán a Computer Science tanszékek többségében tanítanak. A Structured Query Language (SQL) egy nemzetközi szabvány.  Az SQL a kapcsolatok adatbázis-kezelésének szabványos nyelve, beleértve az adatbázisok tartalmának hozzáadását, elérését és kezelését.  A legtöbb esetben a gyors feldolgozás, a bevált megbízhatóság, a könnyű használat és a rugalmasság jellemzi.

Ebben a cikkben bemutatjuk, hogyan állíthat be egy virtuálisgép-sablont egy tesztkörnyezetben a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)és [Azure Data Studio](https://github.com/microsoft/azuredatastudio)használatával.  Ehhez a laborhoz egy megosztott [SQL Server adatbázist](../azure-sql/database/sql-database-paas-overview.md) fogunk használni a teljes laborhoz. A [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) az Azure-ból származó szolgáltatásként nyújtott platformként szolgáló adatbázismotor.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](./tutorial-setup-lab-account.md). Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért lásd: a [piactér-rendszerképek elérhetővé tétele a labor-készítők](specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
| Marketplace-beli rendszerkép | Engedélyezze a Visual Studio 2019 Community (legújabb kiadás) szolgáltatást a Windows 10 Enterprise N (x64) lemezképen a labor-fiókban való használathoz. |

### <a name="shared-resource-configuration"></a>Megosztott erőforrás-konfiguráció

Ha megosztott erőforrást szeretne használni a labor Servicesben, először létre kell hoznia a virtuális hálózatot és az erőforrásokat.  A virtuális hálózat létrehozásához és a laborhoz való kapcsolódásához kövesse a következő [témakört: tesztkörnyezet létrehozása megosztott erőforrással Azure Lab Servicesban](how-to-create-a-lab-with-shared-resource.md).  Ne feledje, hogy a laboratóriumi szolgáltatásokon kívüli összes erőforrás számlázása külön történik, és nem kerül bele a tesztkörnyezet költségbecslés közé.

>[!WARNING]
>A tesztkörnyezet megosztott erőforrásait a tesztkörnyezet létrehozása előtt kell beállítani.  Ha a vnet a tesztkörnyezet létrehozása *előtt* nem [a labor-fiókhoz](how-to-connect-peer-virtual-network.md) tartozik, akkor a tesztkörnyezet nem fér hozzá a megosztott erőforráshoz.

Most, hogy a dolgok hálózatkezelési oldalát kezelik, lehetővé teszi SQL Server adatbázis létrehozását.  Létre fogunk hozni egy [önálló adatbázist](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) , amely a leggyorsabb üzembe helyezési lehetőség a Azure SQL Database számára.  Egyéb központi telepítési lehetőségek esetén hozzon létre egy [rugalmas készletet](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [felügyelt példányt](../azure-sql/managed-instance/instance-create-quickstart.md)vagy SQL-alapú [virtuális gépet](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. A Azure Portal menüben válassza az **új erőforrás létrehozása** lehetőséget.
2. Válassza a **SQL Database** lehetőséget, majd kattintson a **Létrehozás** gombra.
3. Az **SQL-adatbázis létrehozása** űrlap **alapismeretek** lapján válassza ki az adatbázishoz tartozó erőforráscsoportot.  A *sqldb-RG* használatát fogjuk használni.
4. Az **adatbázis neve** mezőbe írja be a következőt: *classlabdb*.
5. A **kiszolgáló** beállítása területen kattintson az **új létrehozása** lehetőségre egy új kiszolgáló létrehozásához az adatbázis tárolásához.
6. Az **új kiszolgáló** előmenüben adja meg a kiszolgáló nevét.  A *classlabdbserver*-t fogjuk használni.  A kiszolgáló nevének globálisan egyedinek kell lennie.
7. Adja meg a kiszolgáló- **rendszergazdai bejelentkezéshez** tartozó *Azureus* értéket.
8. Adjon meg egy emlékezetes jelszót.  A jelszónak legalább nyolc karakterből kell állnia, és speciális karaktereket kell tartalmaznia.
9. Válasszon régiót a **helyhez**.  Ha lehetséges, a késés csökkentése érdekében adja meg ugyanazt a helyet, mint a labor-fiók és a vnet.
10. Az **OK** gombra kattintva térjen vissza a **create SQL Database** űrlapra.
11. Kattintson az **adatbázis konfigurálása** hivatkozásra a **számítás + tárolás** beállítás alatt.
12. Módosítsa az adatbázis beállításait szükség szerint az osztályhoz.  Választhat a kiépített és a kiszolgáló nélküli lehetőségek közül.  Ebben a példában az autoskálázást kiszolgáló nélküli beállítást használjuk, amelynek maximális virtuális mag 4, min virtuális mag 1. Az automatikus szüneteltetési beállítást legalább 1 órával megtartjuk. Kattintson az **Alkalmaz** gombra.
13. Kattintson a **Tovább gombra: hálózatkezelés** gomb.
14. A hálózatkezelés lapon válassza a magánhálózati végpont lehetőséget a **kapcsolódási módszerhez**.
15. A **privát végpontok** szakaszban kattintson a **privát végpont hozzáadása** lehetőségre.
16. A **privát végpont létrehozása** menüben válassza ki ugyanazt az erőforráscsoportot, mint a labor-fiókhoz tartozó virtuális hálózat.
17. A **hely** mezőben válassza ki a virtuális hálózattal megegyező helyet.
18. A **név** mezőbe írja be a következőt: *labsql-endpt*.
19. Hagyja meg a cél alerőforrást SqlServer értékre.
20. A **Virtual Network (virtuális hálózat**) területen válassza ki ugyanazt a virtuális hálózatot, amely a labor-fiókhoz csatlakozik.
21. Az **alhálózat** területen válassza ki azt az alhálózatot, amelyben a végpontot üzemelteti.  A végponthoz rendelt IP-cím az adott alhálózathoz rendelt tartományból fog származni.
22. A **nem** értékre állítsa **a saját DNS-sel való integrációt** . Az egyszerűség kedvéért az Azure DNS-t saját privát DNS-zónán vagy a saját DNS-kiszolgálóin fogjuk használni.
23. Kattintson az **OK** gombra.
24. Kattintson a **Tovább gombra: további beállítások**.
25. A **meglévő adatértékek használata** beállításnál válassza a **minta** lehetőséget.  Az adatbázis létrehozásakor a AdventureWorksLT-adatbázisból származó adatok lesznek használva.
26. Kattintson a **Felülvizsgálat + létrehozás** elemre.
27. Kattintson a **Létrehozás** lehetőségre.

Miután a SQL Database üzembe helyezése sikeresen befejeződött, létrehozhatjuk a labort, és telepíthetjük a szoftvereket a tesztkörnyezet-sablon számítógépén.

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A tantermi labor beállításakor használja az alábbi táblázatban szereplő beállításokat. A tantermi laborok létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ |
| Virtuális gép mérete | Közepes. Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. |
| Virtuálisgép-rendszerkép | Visual Studio 2019 Community (legújabb kiadás) Windows 10 Enterprise N rendszeren (x64) |

Most, hogy létrehozta a labort, módosítsa a sablon számítógépét a szükséges szoftverrel.

## <a name="visual-studio"></a>Visual Studio

A fenti képen a [Visual Studio 2019 közössége](https://visualstudio.microsoft.com/vs/community/)szerepel.  Az összes számítási feladat és eszközkészlet már telepítve van a rendszerképre.  A Visual Studio telepítőjének használatával [bármilyen opcionális eszközt telepíthet](/visualstudio/install/modify-visual-studio?view=vs-2019) , amelyre szüksége lehet.  A Community Edition zárolásának feloldásához [Jelentkezzen be a Visual studióba](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio) .

A Visual Studio tartalmazza az **adattárolási és-feldolgozási** eszközkészletet, amely SQL Server Data Tools (SSDT) tartalmaz.  További információ a SSDT képességeiről: [SQL Server Data Tools Overview (áttekintés](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)).  Ha ellenőrizni szeretné, hogy az osztály megosztott SQL Server kapcsolata sikeres-e, tekintse meg az [adatbázishoz való kapcsolódást és a meglévő objektumok tallózását](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)ismertető témakört. Ha a rendszer kéri, adja hozzá a sablon-számítógép IP-címét az SQL Server-példányhoz csatlakozni képes [számítógépek listájához](../azure-sql/database/firewall-configure.md) .

A Visual Studio számos számítási feladatot támogat, többek között a **Web & Cloud** és az **asztali & mobil** munkaterheléseket.  Mindkét számítási feladat támogatja az SQL Server adatforrásként. További információ a SQL Server ASP.NET Core használatával kapcsolatban: [ASP.net Core és SQL Database alkalmazás létrehozása Azure app Service](../app-service/tutorial-dotnetcore-sqldb-app.md) oktatóanyagban.  A [System. SqlClient](/dotnet/api/system.data.sqlclient) függvénytár használatával kapcsolódjon SQL Database egy [Xamarin](/xamarin) -alkalmazáshoz.

## <a name="install-azure-data-studio"></a>Azure Data Studio telepítése

A [Azure Data Studio](https://github.com/microsoft/azuredatastudio) egy több adatbázisból álló, platformfüggetlen asztali környezet, amely a helyszíni és a Felhőbeli adatplatformok családját használja Windows, MacOS és Linux rendszeren.

1. Töltse le a [Windows *rendszerhez* készült Azure Data Studio rendszertelepítőt](https://go.microsoft.com/fwlink/?linkid=2127432). A más támogatott operációs rendszerekhez tartozó telepítők kereséséhez nyissa meg a [Azure Data Studio](/sql/azure-data-studio/download) letöltési lapját.
2. A **licencszerződés** lapon válassza **az Elfogadom a szerződést** lehetőséget. Kattintson a **Tovább** gombra.
3. A **Telepítés helyének kiválasztása** lapon kattintson a **Tovább** gombra.
4. A **Start menü mappájának kiválasztása** lapon kattintson a **Tovább** gombra.
5. A **További feladatok kiválasztása** lapon jelölje be az **asztal létrehozása ikont** , ha asztali ikont szeretne használni.  Kattintson a **Tovább** gombra.
6. A **telepítésre kész lapon** kattintson a **tovább** gombra.
7. Várjon, amíg a telepítő futni próbál.  Kattintson a **Finish** (Befejezés) gombra.

Most, hogy Azure Data Studio telepítve van, állítsuk be a Azure SQL Databasehoz való kapcsolódást.

1. A Azure Data Studio **Üdvözöljük** lapon kattintson az **új kapcsolat** hivatkozásra.
2. A **kapcsolat részletei** mezőben adja meg a szükséges információkat.
    - **Kiszolgáló** *classlabdbserver.database.Windows.net* beállítása
    - **Felhasználónév** beállítása az *azureuser* -nek
    - **Jelszó** megadása az adatbázis létrehozásához használt jelszóhoz.
    - Győződjön meg róla, hogy a **Jelszó megjegyzése**.
    - Az **adatbázis** lapon válassza a *classlabdb* lehetőséget.
3. Kattintson a **Csatlakozás** gombra.

## <a name="install-sql-server-management-studio"></a>SQL Server Management Studio telepítése

A [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) egy integrált környezet, amely bármely SQL-infrastruktúra kezelésére alkalmas.  A SSMS egy olyan eszköz, amelyet az adatbázis-rendszergazdák használnak az adatinfrastruktúra üzembe helyezéséhez, figyeléséhez és frissítéséhez.

1. [Töltse le az SQL Server Management Studio](https://aka.ms/ssmsfullsetup). A letöltés után indítsa el a telepítőt.
2. Az **Üdvözöljük** lapon kattintson a **telepítés** gombra.
3. A **telepítés kész** lapon kattintson a **Bezárás** gombra.
4. Indítsa el az SQL Server Management Studio.  
5. A **függőségi konfigurációs folyamat** lapon kattintson a **Bezárás** gombra.

Nem, hogy a SSMS telepítve van, [csatlakozhat egy SQL Serverhoz, és lekérdezheti](/sql/ssms/tutorials/connect-query-sql-server)azokat. A csatlakozás beállításakor használja a következő értékeket:

- Kiszolgáló típusa: adatbázismotor
- Kiszolgáló neve: *classlabdbserver.database.Windows.net*
- Hitelesítés: SQL Server hitelesítés
- Bejelentkezés: *azureuser*
- Password (jelszó): az adatbázis létrehozásához használt jelszó.

## <a name="cost-estimate"></a>Költségbecslés

Az osztályra vonatkozó lehetséges költségbecslés. A becslés nem tartalmazza a SQL Server futtatásának költségeit.  Az adatbázis díjszabásáról a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database) ismertető cikk nyújt tájékoztatást.

25 tanulós osztályt fogunk használni. 20 órányi ütemezett idő van. Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára. A kiválasztott virtuálisgép-méret közepes, ami 42 labor egység.

Az alábbi példa egy lehetséges költségbecslés erre az osztályra vonatkozóan:

25 tanuló \* (20 ütemezett óra \+ 10 kvóta óra) * 0,42 USD/óra = 315,00 USD

>[!IMPORTANT]
>A költségbecslés csak példaként szolgál. A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása, kezelése és közzététele](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)