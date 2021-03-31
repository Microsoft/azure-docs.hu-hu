---
title: Kapcsolódás az IBM Informix-adatbázishoz
description: Automatizálja az IBM Informix-ben tárolt erőforrásokat kezelő feladatokat és munkafolyamatokat Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761644"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>IBM Informix-adatbázis-erőforrások kezelése Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Informix-összekötővel](/connectors/informix/)automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek egy IBM Informix-adatbázisban lévő erőforrásokat kezelnek. Ez az összekötő egy olyan Microsoft-ügyfelet tartalmaz, amely egy TCP/IP-hálózaton keresztül kommunikál a távoli Informix kiszolgáló-számítógépekkel, beleértve a felhőalapú adatbázisokat, például az Azure-virtualizálás és a helyszíni adatbázisokat futtató Windows-alapú IBM Informix-t a helyszíni [adatátjáró](../logic-apps/logic-apps-gateway-connection.md)használatakor. Ezekhez az Informix-platformokhoz és-verziókhoz kapcsolódhat, ha úgy vannak konfigurálva, hogy támogassák az elosztott kapcsolati adatbázisok architektúrájának (DRDA) ügyfélkapcsolatait:

* IBM Informix 12,1
* IBM Informix 11,7

Ebből a témakörből megtudhatja, hogyan használhatja az összekötőt egy logikai alkalmazásban az adatbázis-műveletek feldolgozásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Helyszíni adatbázisokhoz [töltse le és telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) egy helyi számítógépre, majd [hozzon létre egy Azure-beli adatátjáró-erőforrást a Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* Az a logikai alkalmazás, amelyben hozzá kell férnie az Informix-adatbázishoz. Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazásnak már kezdődnie kell egy eseményindítóval, például az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Informix-művelet hozzáadása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben, ha még nincs megnyitva.

1. Válassza ki az **új lépés** lehetőséget arra a lépésre, ahol az Informix-műveletet hozzá szeretné adni.

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be `informix` szűrőként a kifejezést. A műveletek listából válassza ki a kívánt műveletet, például:

   ![Válassza ki a futtatandó Informix műveletet](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Az összekötő biztosítja ezeket a műveleteket, amelyek a megfelelő adatbázis-műveleteket futtatják:

   * Táblák beolvasása – adatbázis-táblák listázása `CALL` utasítás használatával
   * Sorok beolvasása – az összes sor olvasása `SELECT *` utasítás használatával
   * Sor beolvasása – sorok beolvasása `SELECT WHERE` utasítás használatával
   * Sor hozzáadása `INSERT` utasítás használatával
   * Sor szerkesztése `UPDATE` utasítás használatával
   * Sor törlése `DELETE` utasítás használatával

1. Ha a rendszer kéri, hogy adja meg az Informix-adatbázis kapcsolati adatait, kövesse a [kapcsolat létrehozásához szükséges lépéseket](#create-connection), majd folytassa a következő lépéssel.

1. Adja meg a kiválasztott művelet adatait:

   | Művelet | Leírás | Tulajdonságok és leírások |
   |--------|-------------|-----------------------------|
   | **Táblák beolvasása** | Adatbázis-táblák listázása Informix CALL utasítás futtatásával. | Nincsenek |
   | **Sorok beolvasása** | A megadott táblában lévő összes sor beolvasása egy Informix- `SELECT *` utasítás futtatásával. | **Táblázat neve**: a kívánt Informix-tábla neve <p><p>Ha további tulajdonságokat szeretne hozzáadni ehhez a művelethez, válassza ki őket az **új paraméter hozzáadása** listából. További információ: az [összekötő hivatkozási témája](/connectors/informix/). |
   | **Sor beolvasása** | Sor beolvasása a megadott táblából egy Informix- `SELECT WHERE` utasítás futtatásával. | - **Táblázat neve**: a kívánt Informix-tábla neve <br>- **Sor azonosítója**: a sor egyedi azonosítója, például `9999` |
   | **Sor beszúrása** | Adjon hozzá egy sort a megadott Informix-táblához egy Informix-utasítás futtatásával `INSERT` . | - **Táblázat neve**: a kívánt Informix-tábla neve <br>- **elem**: a hozzáadandó értékekkel rendelkező sor |
   | **Sor frissítése** | Egy sor módosítása a megadott Informix-táblában egy Informix-utasítás futtatásával `UPDATE` . | - **Táblázat neve**: a kívánt Informix-tábla neve <br>- **Sor azonosítója**: a frissítendő sor egyedi azonosítója, például `9999` <br>- **Sor**: a frissített értékekkel rendelkező sor, például `102` |
   | **Sor törlése** | Sor eltávolítása a megadott Informix-táblából Informix-utasítás futtatásával `DELETE` . | - **Táblázat neve**: a kívánt Informix-tábla neve <br>- **Sor azonosítója**: a törlendő sor egyedi azonosítója, például `9999` |
   ||||

1. Mentse a logikai alkalmazást. Most [tesztelje a logikai alkalmazást](#test-logic-app) , vagy folytassa a logikai alkalmazás létrehozásával.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Kapcsolódás az Informix-hez

1. Ha a logikai alkalmazás egy helyszíni adatbázishoz csatlakozik, válassza **a csatlakozás helyszíni adatátjárón keresztül** lehetőséget.

1. Adja meg a kapcsolatok adatait, majd válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | JSON-tulajdonság | Kötelező | Példaérték | Description |
   |----------|---------------|----------|---------------|-------------|
   | Kapcsolat neve | `name` | Yes | `informix-demo-connection` | Az Informix-adatbázishoz való kapcsolódáshoz használandó név |
   | Kiszolgáló | `server` | Yes | Felhő `informixdemo.cloudapp.net:9089` <br>-Helyszíni: `informixdemo:9089` | Az IPv4-vagy IPv6-formátumú TCP/IP-cím vagy alias, amelyet egy kettőspont és egy TCP/IP-portszám követ |
   | Adatbázis | `database` | Yes | `nwind` | A DRDA-kapcsolatok adatbázisának neve (RDBNAM) vagy az Informix-adatbázis neve (dbname). Az Informix 128 bájtos karakterláncot fogad el. |
   | Hitelesítés | `authentication` | Csak helyszíni | **Alapszintű** vagy **Windows** (Kerberos) | Az Informix-adatbázis által igényelt hitelesítési típus. Ez a tulajdonság csak akkor jelenik meg, ha **a csatlakozás helyszíni adatátjárón keresztül** lehetőséget választja. |
   | Felhasználónév | `username` | No | <*adatbázis – felhasználó neve*> | Az adatbázis felhasználóneve |
   | Jelszó | `password` | No | <*adatbázis – jelszó*> | Az adatbázis jelszava |
   | Átjáró | `gateway` | Csak helyszíni | – <*Azure-előfizetés*> <br>-<*Azure-helyszíni-adatátjáró-erőforrás*> | Az Azure-előfizetés és az Azure-Erőforrás neve a Azure Portalban létrehozott helyszíni adatátjáróhoz. Az **átjáró** tulajdonság és az alárendelt tulajdonságok csak akkor jelennek meg, ha **a csatlakozás helyszíni adatátjárón keresztül** lehetőséget választja. |
   ||||||

   Például:

   * **Felhőbeli adatbázis**

     ![A felhő adatbázis-kapcsolatainak adatai](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Helyszíni adatbázis**

     ![Helyszíni adatbázis-kapcsolatok adatai](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Mentse a logikai alkalmazást.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A Logic app Designer eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás futtatása után megtekintheti a futtatási kimeneteket.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az Áttekintés ablaktábla **Összefoglalás**  >  **futtatása előzmények** területén válassza ki a legutóbbi futtatást.

1. A **logikai alkalmazás futtatása** területen válassza a **Futtatás részletek** lehetőséget.

1. A műveletek listából válassza ki a megtekinteni kívánt kimenetekkel rendelkező műveletet, például **Get_tables**.

   Ha a művelet sikeres, az **állapot** tulajdonsága sikeresként van **megjelölve.**

1. A bemenetek megtekintéséhez a **bemenetek hivatkozás** alatt válassza ki az URL-hivatkozást. A kimenetek megtekintéséhez a **kimenetek hivatkozás** hivatkozása alatt válassza ki az URL-hivatkozást. Íme néhány példa a kimenetekre:

   * **Get_tables** a táblák listáját jeleníti meg:

     ![Kimenetek a "táblák beolvasása" műveletből](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** a sorok listáját jeleníti meg:

     ![Kimenetek a "sorok beolvasása" műveletből](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** a megadott sort mutatja:

     ![Kimenetek a "sor beolvasása" műveletből](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** az új sort mutatja:

     ![Kimenetek a "sor beszúrása" műveletből](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** megjeleníti a frissített sort:

     ![Kimenetek a "sor frissítése" műveletből](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** a törölt sort mutatja:

     ![Kimenetek a "sor törlése" műveletből](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az eseményindítók, műveletek és korlátok részletes technikai részleteiért lásd az összekötők feladatainak leírását, és tekintse át az [összekötő hivatkozási oldalát](/connectors/informix/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](apis-list.md) megismerése
