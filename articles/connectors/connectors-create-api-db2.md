---
title: Az IBM DB2-erőforrások elérése és kezelése
description: Az IBM DB2-erőforrások olvasása, szerkesztése, frissítése és kezelése az Azure Logic Apps használatával automatizált munkafolyamatok létrehozásával
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967401"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Az IBM DB2-erőforrások elérése és kezelése Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [IBM DB2-összekötővel](/connectors/db2/)automatizált feladatokat és munkafolyamatokat hozhat létre a DB2-adatbázisban tárolt erőforrások alapján. A munkafolyamatok csatlakozhatnak az adatbázis erőforrásaihoz, olvashatják és listázják az adatbázis-táblákat, sorok hozzáadását, sorok módosítását, sorok törlését és egyebeket. Olyan műveleteket is hozzáadhat a logikai alkalmazásokban, amelyek válaszokat kapnak az adatbázisból, és más műveletek számára elérhetővé teszik a kimenetet.

Ez a cikk bemutatja, hogyan hozhat létre olyan logikai alkalmazást, amely különböző adatbázis-műveleteket hajt végre. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Támogatott platformok és verziók

A DB2-összekötő tartalmaz egy Microsoft-ügyfelet, amely távoli DB2-kiszolgálókkal kommunikál egy TCP/IP-hálózaton keresztül. Ezt az összekötőt használhatja olyan felhőalapú adatbázisok eléréséhez, mint például az IBM DB2 az Azure virtualizációs szolgáltatásban futó Windows rendszerekhez. A helyszíni DB2-adatbázisokat a helyszíni [adatátjáró telepítése és beállítása](../logic-apps/logic-apps-gateway-connection.md)után is elérheti.

Az IBM DB2-összekötő támogatja ezeket az IBM DB2 platformokat és verziókat az IBM DB2-kompatibilis termékekkel együtt, amelyek támogatják az elosztott kapcsolati adatbázis architektúráját (DRDA) az SQL Access Manager (SQLAM) 10. és 11. verziójában:

| Platform | Verzió | 
|----------|---------|
| IBM DB2 for z/OS | 12, 11,1, 10,1 |
| IBM DB2 – i | 7,3, 7,2, 7,1 |
| IBM DB2 a LUW-hez | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Támogatott adatbázis-műveletek

Az IBM DB2-összekötő támogatja ezeket az adatbázis-műveleteket, amelyek az összekötőhöz kapcsolódó műveletekhez képezhetők le:

| Adatbázis-művelet | Összekötő művelete |
|--------------------|------------------|
| Adatbázis-táblák listázása | Táblák beolvasása |
| Egy sor beolvasása a SELECT használatával | Sor beolvasása |
| Az összes sor olvasása a SELECT paranccsal | Sorok beolvasása |
| Egy sor hozzáadása a BESZÚRÁSsal | Sor beszúrása |
| Egy sor szerkesztése a frissítés használatával | Sor frissítése |
| Egy sor eltávolítása a DELETE használatával | Sor törlése |
|||

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* IBM DB2-adatbázis, felhőalapú vagy helyszíni

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyen el szeretné érni a DB2-adatbázisát. Ez az összekötő csak műveleteket biztosít, ezért a logikai alkalmazás indításához válasszon egy különálló eseményindítót, például az **Ismétlődés** eseményindítóját.
A cikkben szereplő példák az **ismétlődési** eseményindítót használják.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2-művelet hozzáadása – táblák beolvasása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben, ha még nincs megnyitva.

1. Az trigger alatt válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "DB2" kifejezést. Ebben a példában a műveletek listában válassza a következő műveletet: **táblák beolvasása (előzetes verzió)**

   ![Művelet kiválasztása](./media/connectors-create-api-db2/select-db2-action.png)

   Ekkor a rendszer megkéri, hogy adja meg a DB2-adatbázis kapcsolati adatait.

1. Kövesse a [felhőalapú adatbázisok](#cloud-connection) vagy helyszíni [adatbázisok](#on-premises-connection)kapcsolatainak létrehozásához szükséges lépéseket.

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Kapcsolódás a Cloud DB2-hez

A kapcsolat beállításához adja meg a kapcsolódási adatokat, ha a rendszer kéri, válassza a **Létrehozás** lehetőséget, majd mentse a logikai alkalmazást:

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Kapcsolat helyszíni átjárón keresztül** | No | Csak helyszíni kapcsolatokra vonatkozik. |
| **Kapcsolat neve** | Yes | A kapcsolatok neve, például "MyLogicApp-DB2-kapcsolatok" |
| **Kiszolgáló** | Yes | A DB2-kiszolgáló címe vagy aliasának kettőspont-portszáma, például: "myDB2server.cloudapp.net:50000" <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amely egy TCP/IP-címet vagy aliast jelöl IPv4-vagy IPv6-formátumban, majd egy kettőspontot és egy TCP/IP-portszámot. |
| **Adatbázis** | Yes | Az adatbázis neve <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amely a DRDA-adatbázis nevét (RDBNAM) jelöli: <p>– A z/OS esetében a DB2 egy 16 bájtos karakterláncot fogad el, amelyben az adatbázis "IBM DB2 for z/OS" néven ismert. <br>-DB2 for i elfogadok egy 18 bájtos karakterláncot, amelyben az adatbázis "IBM DB2 for i" néven ismert. <br>-A DB2 for LUW 8 bájtos karakterláncot fogad el. |
| **Felhasználónév** | Yes | Az adatbázishoz tartozó Felhasználónév <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amelynek hossza az adott adatbázison alapul: <p><p>– A z/OS esetében a DB2 egy 8 bájtos karakterláncot fogad el. <br>-DB2 – 10 bájtos karakterláncot Fogadok el. <br>– A Linux vagy UNIX rendszerhez készült DB2 8 bájtos karakterláncot fogad el. <br>-A DB2 for Windows egy 30 bájtos karakterláncot fogad el. |
| **Jelszó** | Yes | Az adatbázis jelszava |
||||

Például:

![A felhőalapú adatbázisok kapcsolati adatai](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Kapcsolódás helyszíni DB2-hez

A kapcsolatok létrehozása előtt már telepítve kell lennie a helyszíni adatátjárónak. Ellenkező esetben nem lehet befejezni a kapcsolatok beállítását. Ha telepítette az átjárót, folytassa a kapcsolat részleteinek megadásával, majd válassza a **Létrehozás** lehetőséget.

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Kapcsolat helyszíni átjárón keresztül** | Yes | Akkor érvényes, ha helyszíni csatlakozást szeretne használni, és megjeleníti a helyszíni kapcsolódási tulajdonságokat. |
| **Kapcsolat neve** | Yes | A kapcsolatok neve, például "MyLogicApp-DB2-kapcsolatok" | 
| **Kiszolgáló** | Yes | A DB2-kiszolgáló címe vagy aliasának kettőspont-portszáma, például: "myDB2server: 50000" <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amely egy TCP/IP-címet vagy aliast jelöl IPv4-vagy IPv6-formátumban, majd egy kettőspontot és egy TCP/IP-portszámot. |
| **Adatbázis** | Yes | Az adatbázis neve <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amely a DRDA-adatbázis nevét (RDBNAM) jelöli: <p>– A z/OS esetében a DB2 egy 16 bájtos karakterláncot fogad el, amelyben az adatbázis "IBM DB2 for z/OS" néven ismert. <br>-DB2 for i elfogadok egy 18 bájtos karakterláncot, amelyben az adatbázis "IBM DB2 for i" néven ismert. <br>-A DB2 for LUW 8 bájtos karakterláncot fogad el. |
| **Hitelesítés** | Yes | A kapcsolatok hitelesítési típusa, például "alapszintű" <p><p>**Megjegyzés**: válassza ki ezt az értéket a listából, amely alapszintű vagy Windows (Kerberos) elemet tartalmaz. |
| **Felhasználónév** | Yes | Az adatbázishoz tartozó Felhasználónév <p><p>**Megjegyzés**: ez az érték olyan karakterlánc, amelynek hossza az adott adatbázison alapul: <p><p>– A z/OS esetében a DB2 egy 8 bájtos karakterláncot fogad el. <br>-DB2 – 10 bájtos karakterláncot Fogadok el. <br>– A Linux vagy UNIX rendszerhez készült DB2 8 bájtos karakterláncot fogad el. <br>-A DB2 for Windows egy 30 bájtos karakterláncot fogad el. |
| **Jelszó** | Yes | Az adatbázis jelszava |
| **Átjáró** | Yes | A telepített helyszíni adatátjáró neve <p><p>**Megjegyzés**: válassza ki ezt az értéket a listából, amely tartalmazza az Azure-előfizetésben és az erőforráscsoporthoz található összes telepített adatátjárót. |
||||

Például:

![A helyszíni adatbázisok kapcsolati adatai](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Kimeneti táblák megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

   ![Futtatási előzmények megtekintése](./media/connectors-create-api-db2/run-history.png)

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **táblák beolvasása** műveletet.

   ![Művelet kibontása](./media/connectors-create-api-db2/expand-action-step.png)

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek tartalmazzák a táblák listáját.

   ![Kimeneti táblák megtekintése](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Sor beolvasása

Egy DB2-adatbázistábla egyik rekordjának beolvasásához használja a logikai alkalmazás **sor beolvasása** műveletét. Ez a művelet egy DB2 `SELECT WHERE` -utasítást futtat, például: `SELECT FROM AREA WHERE AREAID = '99999'` .

1. Ha még soha nem használta a DB2-műveleteket a logikai alkalmazásban, tekintse át a következő témakör lépéseit: a [DB2 művelet – táblák beolvasása](#add-db2-action) szakasz, de adja hozzá a **sor beolvasása** műveletet, majd térjen vissza ide a folytatáshoz.

   A **sor beolvasása** művelet hozzáadása után a példaként szolgáló logikai alkalmazás jelenik meg:

   ![Sor beolvasása művelet](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Az összes szükséges tulajdonság (*) értékének megadása. A tábla kiválasztása után a művelet megjeleníti az adott tábla rekordjaira vonatkozó megfelelő tulajdonságokat.

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Yes | A kívánt rekordot tartalmazó tábla, például "térség" ebben a példában |
   | **Területi azonosító** | Yes | A kívánt rekord azonosítója (például "99999") ebben a példában |
   ||||

   ![Képernyőkép: "a sor beolvasása (előzetes verzió)" művelet a megnyitott "táblanév" listával és a "terület" érték kiválasztásával.](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="view-output-row"></a>Kimeneti sor megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **sor beolvasása** műveletet.

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek tartalmazzák a megadott sort.

   ![Kimeneti sor megtekintése](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Sorok beolvasása

Egy DB2-adatbázistábla összes rekordjának beolvasásához használja a logikai alkalmazás **sorok beolvasása** műveletét. Ez a művelet egy DB2 `SELECT` -utasítást futtat, például: `SELECT * FROM AREA` .

1. Ha még soha nem használta a DB2-műveleteket a logikai alkalmazásban, tekintse át a következő témakör lépéseit: a [DB2 művelet – táblák beolvasása](#add-db2-action) szakasz, de adja hozzá a **sorok beolvasása** műveletet, majd térjen vissza ide a folytatáshoz.

   Miután hozzáadta a **sorok beolvasása** műveletet, itt látható, hogyan jelenik meg a példaként szolgáló logikai alkalmazás:

   ![Sorok beolvasása művelet](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Nyissa meg a **tábla neve** listát, majd válassza ki a kívánt táblát, amely ebben a példában a "területen" látható:

   ![Képernyőkép a "sor beolvasása (előzetes verzió)" műveletről "a tábla neve" listában kiválasztott "terület" értékkel.](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Ha szűrőt vagy lekérdezést szeretne megadni az eredményekhez, válassza a **Speciális beállítások megjelenítése** lehetőséget.

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="view-output-rows"></a>Kimeneti sorok megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **sorok beolvasása** műveletet.

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek tartalmazzák a megadott tábla összes rekordját.

   ![Kimeneti sorok megtekintése](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Sor beszúrása

Ha egyetlen rekordot szeretne hozzáadni egy DB2-adatbázis táblához, használja a **sor beszúrása** műveletet a logikai alkalmazásban. Ez a művelet egy DB2 `INSERT` -utasítást futtat, például: `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` .

1. Ha még soha nem használta a DB2-műveleteket a logikai alkalmazásban, tekintse át a következő témakör lépéseit: a [DB2 művelet – táblák beolvasása](#add-db2-action) szakasz, de adja hozzá Ehelyett a **sor beszúrása** műveletet, majd térjen vissza ide a folytatáshoz.

   Miután hozzáadta a **sor beszúrása** műveletet, itt látható, hogyan jelenik meg a példa logikai alkalmazás:

   ![Sor beszúrása művelet](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Az összes szükséges tulajdonság (*) értékének megadása. A tábla kiválasztása után a művelet megjeleníti az adott tábla rekordjaira vonatkozó megfelelő tulajdonságokat.

   Ebben a példában a tulajdonságok a következők:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Yes | A rekord hozzáadására szolgáló tábla, például "térség" |
   | **Területi azonosító** | Yes | A hozzáadandó régió azonosítója, például "99999" |
   | **A környék leírása** | Yes | A felvenni kívánt területek leírása, például "99999. rész" |
   | **Régió azonosítója** | Yes | A hozzáadandó régió azonosítója, például: "102" |
   |||| 

   Például:

   ![Képernyőfelvétel: a Logic Apps Designer és a "sor beszúrása (előzetes verzió)" művelet és a példa tulajdonság értékei.](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="view-insert-row-outputs"></a>Sorok beszúrási kimenetének megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **sor beszúrása** műveletet.

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek tartalmazzák a megadott táblához hozzáadott rekordot.

   ![Kimenet megtekintése beszúrt sorral](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Sor frissítése

Ha egy DB2-adatbázistábla egyetlen rekordját szeretné frissíteni, használja a logikai alkalmazásban a **sor frissítése** műveletet. Ez a művelet egy DB2 `UPDATE` -utasítást futtat, például: `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` .

1. Ha még soha nem használta a DB2-műveleteket a logikai alkalmazásban, tekintse át a következő témakör lépéseit: a [DB2 művelet – táblák beolvasása](#add-db2-action) szakasz, de adja hozzá Ehelyett a **frissítési sor** műveletet, majd térjen vissza ide a folytatáshoz.

   Miután hozzáadta a **frissítési sor** műveletet, itt látható, hogyan jelenik meg a példa logikai alkalmazás:

   ![Sor frissítése művelet](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Az összes szükséges tulajdonság (*) értékének megadása. A tábla kiválasztása után a művelet megjeleníti az adott tábla rekordjaira vonatkozó megfelelő tulajdonságokat.

   Ebben a példában a tulajdonságok a következők:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Yes | A rekord frissítésére szolgáló tábla, például "térség" |
   | **Sor azonosítója** | Yes | A frissítendő rekord azonosítója, például: "99999" |
   | **Területi azonosító** | Yes | Az új területi azonosító, például "99999" |
   | **A környék leírása** | Yes | Az új környék leírása, például "frissítve 99999" |
   | **Régió azonosítója** | Yes | Az új régió azonosítója, például "102" |
   ||||

   Például:

   ![Képernyőfelvétel: a Logic Apps Designer és a "frissítési sor (előzetes verzió)" művelet, ahol kiválaszthat egy táblát.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="view-update-row-outputs"></a>Frissítési sor kimenetének megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **frissítési sor** műveletet.

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek közé tartozik a megadott táblában frissített rekord.

   ![Kimenet megtekintése frissített sorral](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Sor törlése

Ha egyetlen rekordot szeretne törölni egy DB2-adatbázis táblából, használja a **sor törlése** műveletet a logikai alkalmazásban. Ez a művelet egy DB2 `DELETE` -utasítást futtat, például: `DELETE FROM AREA WHERE AREAID = '99999'` .

1. Ha még soha nem használta a DB2-műveleteket a logikai alkalmazásban, tekintse át a következő témakör lépéseit: a [DB2 művelet – táblák beolvasása](#add-db2-action) szakasz, de vegye fel a **sor törlése** műveletet, majd térjen vissza ide a folytatáshoz.

   A **sor törlése** művelet hozzáadása után a példa logikai alkalmazás jelenik meg:

   ![Sor törlése művelet](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Az összes szükséges tulajdonság (*) értékének megadása. A tábla kiválasztása után a művelet megjeleníti az adott tábla rekordjaira vonatkozó megfelelő tulajdonságokat.

   Ebben a példában a tulajdonságok a következők:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Yes | A rekord törlésére szolgáló tábla, például "térség" |
   | **Sor azonosítója** | Yes | A törlendő rekord azonosítója, például: "99999" |
   ||||

   Például:

   ![Képernyőfelvétel: a Logic Apps Designer és a "sor törlése (előzetes verzió)" művelet, ahol kiválaszthat egy törölni kívánt táblát.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="view-delete-row-outputs"></a>Sorok törlési kimenetének megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futásának befejeződése után megtekintheti a kimenetet a futtatásból.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. Az **Összefoglalás** alatt, a **futtatási előzmények** szakaszban válassza ki a legutóbbi futtatást, amely a lista első eleme.

1. A **logikai alkalmazás futtatása** területen most már ellenőrizheti a logikai alkalmazás egyes lépéseinek állapotát, bemeneteit és kimeneteit.
Bontsa ki a **sor törlése** műveletet.

1. A bemenetek megtekintéséhez válassza a **nyers bemenetek megjelenítése** elemet.

1. A kimenetek megtekintéséhez válassza a **nyers kimenetek megjelenítése** lehetőséget.

   A kimenetek már nem tartalmazzák a megadott táblából törölt rekordot.

   ![Kimenet megtekintése törölt sor nélkül](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/db2/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

