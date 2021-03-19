---
title: SQL Database lekérdezése a Azure Portal lekérdezési szerkesztőjével (előzetes verzió)
description: Megtudhatja, hogyan futtathat Transact-SQL-(T-SQL-) lekérdezéseket a lekérdezéstervező használatával a Azure SQL Database adatbázisán.
titleSuffix: Azure SQL Database
keywords: Kapcsolódás az SQL Database-hez, SQL Database lekérdezése, Azure Portal, portál, lekérdezés-szerkesztő
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594189"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Gyors útmutató: a Azure Portal lekérdezési szerkesztőjének (előzetes verzió) használata Azure SQL Database lekérdezéséhez
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A lekérdezés-szerkesztő egy eszköz a Azure Portalban, amellyel SQL-lekérdezéseket futtathat az adatbázisában az Azure szinapszis Analyticsben Azure SQL Database vagy adattárházban.

Ebben a rövid útmutatóban a lekérdezéstervező segítségével futtatja a Transact-SQL (T-SQL) lekérdezéseket egy adatbázison.

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-a-database-with-sample-data"></a>Adatbázis létrehozása mintaadatok használatával

A rövid útmutató elvégzéséhez a AdventureWorksLT mintaadatbázis szükséges. Ha nem rendelkezik a AdventureWorksLT mintaadatbázis munkapéldányával a SQL Databaseban, a következő rövid útmutató segítségével gyorsan létrehozhat egyet:

[Gyors útmutató: adatbázis létrehozása Azure SQL Database a Azure Portal, a PowerShell vagy az Azure CLI használatával](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Azure Active Directory-rendszergazda beállítása a kiszolgálóhoz (nem kötelező)

Egy Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitás használatával jelentkezzen be a Azure Portalba és az adatbázisába. Ha az Azure AD-t szeretné használni a lekérdezés-szerkesztőhöz való kapcsolódáshoz, kövesse az alábbi lépéseket.

Ez a folyamat nem kötelező, hanem az SQL-hitelesítés használatával csatlakozhat a lekérdezés-szerkesztőhöz.

> [!NOTE]
> * Az e-mail-fiókok (például outlook.com, gmail.com, yahoo.com stb.) még nem támogatottak az Azure AD-rendszergazdák számára. Győződjön meg arról, hogy az Azure AD-ben natív módon létrehozott vagy az Azure AD-ba összevont felhasználót választ.
> * Az Azure AD rendszergazdai bejelentkezés olyan fiókokkal működik, amelyeken engedélyezve van a 2 faktoros hitelesítés, de a Lekérdezéstervező nem támogatja a 2 faktoros hitelesítést.

1. A Azure Portal navigáljon az SQL Database-kiszolgálóhoz.

2. Az **SQL Server** menüben válassza a **Active Directory rendszergazda** elemet.

3. A SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **rendszergazda beállítása** lehetőséget.

    ![az Active Directory kiválasztása](./media/connect-query-portal/select-active-directory.png)

4. A **rendszergazda hozzáadása** oldalon a keresőmezőbe írja be a keresendő felhasználót vagy csoportot, válassza ki rendszergazdaként, majd kattintson a **kiválasztás** gombra.

5. Vissza a SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **Mentés** lehetőséget.

## <a name="using-sql-query-editor"></a>SQL-lekérdezési szerkesztő használata

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a lekérdezni kívánt adatbázist.

2. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Kapcsolat létesítése az adatbázissal

Annak ellenére, hogy bejelentkezett a portálra, továbbra is meg kell adnia a hitelesítő adatokat az adatbázis eléréséhez. Az adatbázishoz való kapcsolódáshoz SQL-hitelesítés vagy Azure Active Directory használatával csatlakozhat.

#### <a name="connect-using-sql-authentication"></a>Csatlakozás SQL-hitelesítés használatával

1. A **Bejelentkezés** lap **SQL Server-hitelesítés** területén adja meg az adatbázishoz hozzáféréssel rendelkező felhasználó **felhasználónevét** és **jelszavát** . Ha nem biztos benne, használja az adatbázis-kiszolgálójának kiszolgáló-rendszergazdájának felhasználónevét és jelszavát.

    ![bejelentkezés](./media/connect-query-portal/login-menu.png)

2. Válassza az **OK** lehetőséget.

#### <a name="connect-using-azure-active-directory"></a>Kapcsolat Azure Active Directory használatával

A **lekérdezés-szerkesztőben (előzetes verzió)** tekintse meg a **bejelentkezési** oldalt a **Active Directory hitelesítés** szakaszban. A hitelesítés automatikusan megtörténik, így ha Ön Azure AD-rendszergazda az adatbázisban, megjelenik egy üzenet, amely azt jelzi, hogy be van jelentkezve. Ezután válassza a **Folytatás** *\<your user or group ID>* gombot. Ha az oldal azt jelzi, hogy nem sikerült bejelentkeznie, lehet, hogy frissítenie kell a lapot.

### <a name="query-a-database-in-sql-database"></a>Adatbázis lekérdezése SQL Database

A következő példa lekérdezéseit sikeresen futtatni kell a AdventureWorksLT-mintaadatbázison.

#### <a name="run-a-select-query"></a>VÁLASZTÓ lekérdezés futtatása

1. Illessze be a következő lekérdezést a lekérdezés-szerkesztőbe:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Válassza a **Futtatás** lehetőséget, majd tekintse át a kimenetet az **eredmények** ablaktáblán.

   ![lekérdezésszerkesztő: eredmények](./media/connect-query-portal/query-editor-results.png)

3. Lehetőség van arra is, hogy mentse a lekérdezést. SQL-fájlként, vagy exportálja a visszaadott értékeket. JSON,. csv vagy. XML fájlként.

#### <a name="run-an-insert-query"></a>BESZÚRÁSi lekérdezés futtatása

Az alábbi [Insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-utasítás futtatásával vegyen fel egy új terméket a `SalesLT.Product` táblába.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Válassza a **Futtatás**  elemet egy új sor táblázatba való beszúrásához `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


#### <a name="run-an-update-query"></a>FRISSÍTÉSI lekérdezés futtatása

A következő [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-utasítás futtatásával módosítsa az új terméket.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a tábla megadott sorának frissítéséhez `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.

#### <a name="run-a-delete-query"></a>TÖRLŐ lekérdezés futtatása

Futtassa az alábbi [delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL-utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a tábla megadott sorának törléséhez `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="troubleshooting-and-considerations"></a>Hibaelhárítás és megfontolandó szempontok

A lekérdezés-szerkesztő használatakor néhány tudnivalót is megtudhat.

### <a name="configure-local-network-settings"></a>Helyi hálózati beállítások konfigurálása

Ha a lekérdezés-szerkesztőben a következő hibák valamelyike jelenik meg:
 - *Előfordulhat, hogy a helyi hálózati beállítások megakadályozhatják a lekérdezési szerkesztőtől a lekérdezések kiadását. A hálózati beállítások konfigurálásával kapcsolatos utasításokat ide kattintva tekintheti meg*
 - *Nem lehet csatlakozni a kiszolgálóhoz. Ez a helyi tűzfal konfigurációjának vagy a hálózati proxy beállításainak a problémájára is utalhat*

Ennek az az oka, hogy a Lekérdezéstervező a 443-es és a 1443-es portot használja a kommunikációhoz. Gondoskodnia kell arról, hogy engedélyezze a kimenő HTTPS-forgalmat ezeken a portokon. Az alábbi utasítások végigvezetik az operációs rendszertől függően. Előfordulhat, hogy a vállalati IT-vel való együttműködésre van szükség, hogy jóváhagyást adjon a helyi hálózaton való megnyitáshoz.

#### <a name="steps-for-windows"></a>A Windows lépései

1. A **Windows Defender-tűzfal** megnyitása
2. A bal oldali menüben válassza a **Speciális beállítások** lehetőséget.
3. A **fokozott biztonságú Windows Defender-tűzfalon** válassza a bal oldali menüben a **Kimenő szabályok** lehetőséget.
4. A jobb oldali menüben válassza az **új szabály..** . lehetőséget.

Az **új kimenő szabály varázslóban** kövesse az alábbi lépéseket:

1. Válassza a **port** lehetőséget a létrehozni kívánt szabály típusaként. Kattintson a **Tovább** gombra.
2. **TCP** kiválasztása
3. Válassza az **adott távoli portok** lehetőséget, és írja be a "443, 1443" értéket. Ezután válassza a **tovább** lehetőséget.
4. Válassza a "biztonságos hálózat engedélyezése" lehetőséget.
5. Kattintson a **Next (tovább** ) gombra, majd a **tovább** gombra.
5. A "domain", a "Private" és a "Public" megtartása minden kiválasztva
6. Adjon nevet a szabálynak, például: "Access Azure SQL Query Editor", és opcionálisan egy leírást. Ezután válassza a **Befejezés** lehetőséget.

#### <a name="steps-for-mac"></a>Mac-lépések
1. Nyissa meg a **rendszerbeállításokat** (Apple Menu > rendszerbeállítások).
2. Kattintson a **biztonsági & adatvédelem** lehetőségre.
3. Kattintson a **tűzfal** elemre.
4. Ha a tűzfal ki van kapcsolva, **kattintson a zárolás gombra a módosítások** alján, és válassza a **tűzfal bekapcsolása** lehetőséget.
4. Kattintson a **tűzfalbeállítások lehetőségre**.
5. A **biztonság & adatvédelem** ablakban válassza a következő lehetőséget: "a bejövő kapcsolatok fogadásának automatikus engedélyezése az aláírt szoftverek számára".

#### <a name="steps-for-linux"></a>A Linux lépései
Futtassa ezeket a parancsokat az iptables frissítéséhez
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Kapcsolatok szempontjai

* A lekérdezési szerkesztővel létesített nyilvános kapcsolatokhoz [hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok](firewall-create-server-level-portal-quickstart.md) számára az adatbázisok és az adattárházak eléréséhez.

* Ha a kiszolgálón beállított magánhálózati kapcsolati kapcsolattal rendelkezik, és a privát Virtual Network IP-címéhez kapcsolódik a lekérdezési szerkesztőhöz, a lekérdezés-szerkesztő úgy működik, hogy nem kell hozzáadnia az ügyfél IP-címét az SQL Database-kiszolgáló tűzfalszabály-szabályaihoz.

* A lekérdezési szerkesztő használatához szükséges legalapvetőbb RBAC engedélyek olvasási hozzáférést biztosítanak a kiszolgálóhoz és az adatbázishoz. Ezen hozzáférési szinttel bárki hozzáférhet a lekérdezés-szerkesztő szolgáltatáshoz. Ha korlátozni szeretné az egyes felhasználók hozzáférését, meg kell akadályoznia, hogy a Azure Active Directory-vagy SQL-hitelesítési hitelesítő adatokkal be tudja jelentkezni a lekérdezés-szerkesztőbe. Ha nem tudják saját magukhoz rendelni a kiszolgáló HRE-rendszergazdáját vagy SQL-rendszergazdai fiók elérését vagy hozzáadását, akkor nem használhatják a lekérdezés-szerkesztőt.

* A lekérdezés-szerkesztő nem támogatja az `master` adatbázishoz való kapcsolódást.

* A lekérdezés-szerkesztő nem tud kapcsolódni egy replika-adatbázishoz a következővel `ApplicationIntent=ReadOnly`

* Ha a következő hibaüzenetet látta: "az X-CSRF-Signature fejléce nem ellenőrizhető", hajtsa végre az alábbi műveleteket a probléma megoldásához:

    * Győződjön meg arról, hogy a számítógép órája a megfelelő idő-és időzónára van beállítva. Azt is megteheti, hogy megkeresi a számítógép időzónáját az Azure-ban. Ehhez keresse meg a példány helyének időzónáját, például az USA keleti régióját, a csendes-óceáni térséget stb.
    * Ha proxy hálózatot használ, győződjön meg arról, hogy az "X-CSRF-Signature" fejléc nem módosul vagy nem lett elvetve.

### <a name="other-considerations"></a>További szempontok

* Az **F5** billentyű lenyomásával frissíti a lekérdezés-szerkesztő oldalt, és a folyamatban lévő összes lekérdezés elvész.

* A lekérdezés végrehajtásának 5 perces időtúllépése van.

* A lekérdezéstervező csak a földrajzi adattípusok hengeres leképezését támogatja.

* Nem támogatott az IntelliSense az adatbázis-táblákhoz és a nézetekhez, de a szerkesztő támogatja a már beírt nevek automatikus kiegészítését.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure SQL Database által támogatott Transact-SQL (T-SQL) szolgáltatásról, tekintse meg a [Transact-SQL-különbségek feloldása az áttelepítés során SQL Databasere](transact-sql-tsql-differences-sql-server.md)című témakört.
