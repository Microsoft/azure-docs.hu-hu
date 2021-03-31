---
title: Kapcsolódás Oracle Databasehoz
description: Rekordok beszúrása és kezelése Oracle Database REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674813"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Ismerkedés az Oracle Database-összekötővel

Az Oracle Database-összekötő használatával olyan szervezeti munkafolyamatokat hozhat létre, amelyek a meglévő adatbázisban lévő adatait használják. Ez az összekötő egy helyszíni Oracle Databasehoz vagy egy Oracle Database telepített Azure-beli virtuális géphez tud csatlakozni. Ezzel az összekötővel a következőket teheti:

* Hozza létre a munkafolyamatot úgy, hogy új ügyfelet ad hozzá egy ügyfél-adatbázishoz, vagy megrendelést frissít egy Orders adatbázisban.
* Használjon műveleteket egy adatsor beszerzéséhez, szúrjon be egy új sort, és akár törölje is. Ha például egy rekordot hoz létre a Dynamics CRM Online-ban (egy triggerben), szúrjon be egy sort egy Oracle Databaseba (egy műveletbe). 

Ez az összekötő nem támogatja a következő elemeket:

* Megjelenítések 
* Bármely, kompozit kulccsal rendelkező tábla
* Beágyazott objektumtípusok a táblákban
* Nem skaláris értékkel rendelkező adatbázis-függvények

Ez a cikk bemutatja, hogyan használhatja a Oracle Database-összekötőt egy logikai alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

* Támogatott Oracle-verziók: 
    * Oracle 9-es és újabb verziók
    * 8\.1.7-es vagy újabb Oracle-ügyfélszoftver

* Telepítse a helyszíni adatátjárót. A [logikai alkalmazásokból a helyszíni adatokhoz való kapcsolódás](../logic-apps/logic-apps-gateway-connection.md) a lépéseket ismerteti. Az átjárót egy helyszíni Oracle Databasehoz vagy egy Oracle DB telepített Azure-beli virtuális géphez való kapcsolódáshoz kell megadnia. 

    > [!NOTE]
    > A helyszíni adatátjáró hídként működik, és biztonságos adatátvitelt biztosít a helyszíni adatok (a felhőben nem szereplő adatok) és a logikai alkalmazások között. Ugyanaz az átjáró több szolgáltatással és több adatforrással is használható. Így előfordulhat, hogy csak egyszer kell telepítenie az átjárót.

* Telepítse az Oracle-ügyfelet arra a gépre, amelyen a helyszíni adatátjárót telepítette. Ügyeljen arra, hogy a .NET-hez készült 64 bites Oracle-adatszolgáltatót az Oracle-ből telepítse:  

  [64 bites ODAC 12c 4-es kiadás (12.1.0.2.4) Windows x64 rendszerhez](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Ha az Oracle-ügyfél nincs telepítve, akkor hiba történik, amikor megpróbál létrehozni vagy használni a kapcsolódást. Tekintse meg a cikkben szereplő gyakori hibákat.


## <a name="add-the-connector"></a>Az összekötő hozzáadása

> [!IMPORTANT]
> Ez az összekötő nem rendelkezik eseményindítókkal. Csak műveletekkel rendelkezik. Tehát a logikai alkalmazás létrehozásakor adjon hozzá egy másik eseményindítót a logikai alkalmazás elindításához, például: **Schedule-ismétlődés**, vagy **kérelem/válasz válasz**. 

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást.

2. A logikai alkalmazás elején válassza a **kérelem/válasz-kérelem** triggert: 

    ![Megjelenik egy párbeszédpanel, amelyen az összes eseményindító kereshető. Egyetlen trigger is megjelenik, a "kérés/válasz-kérelem", egy kijelölési gombbal.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Kattintson a **Mentés** gombra. Mentéskor a rendszer automatikusan létrehoz egy kérelem URL-címét. 

4. Jelölje ki az **Új lépés**, majd a **Művelet hozzáadása** lehetőséget. Az elérhető műveletek megtekintéséhez írja be a következőt `oracle` : 

    ![A keresőmező "Oracle" kifejezést tartalmaz. A keresés egy "Oracle Database" címkével rendelkező találatot hoz létre. Van egy Többlapos oldal, egy lap, amely az "ESEMÉNYINDÍTÓk (0)", egy másik pedig a "műveletek (6)" mutatót jeleníti meg. Hat művelet szerepel a felsorolásban. Ezek közül az első a "sor beolvasása" előzetes verzió.](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Ez a leggyorsabb módja annak, hogy megtekintse az összekötők számára elérhető eseményindítókat és műveleteket. Írja be az összekötő nevének egy részét, például: `oracle` . A tervező felsorolja az összes eseményindítót és műveletet. 

5. Válassza ki az egyik műveletet, például **Oracle Database-beolvasás sort**. **A helyszíni adatátjárón keresztül válassza a kapcsolat** lehetőséget. Adja meg az Oracle-kiszolgáló nevét, a hitelesítési módszert, a felhasználónevet és a jelszót, majd válassza ki az átjárót:

    ![A párbeszédpanel "Oracle Database-sor beolvasása" címmel jelenik meg. A "kapcsolat helyszíni adatátjárón keresztül" feliratú jelölőnégyzet be van jelölve. Az alábbi öt másik szövegmező.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Csatlakozás után válasszon ki egy táblázatot a listából, és írja be a sor AZONOSÍTÓját a táblába. Ismernie kell az azonosítót a táblához. Ha nem tudja, forduljon a Oracle DB rendszergazdájához, és kérje le a kimenetét `select * from yourTableName` . Ez megadja a folytatáshoz szükséges azonosításra alkalmas adatokat.

    A következő példában a rendszer a feladatok adatait egy emberi erőforrás-adatbázisból adja vissza: 

    ![A "sor beolvasása (előzetes verzió)" feliratú párbeszédpanel két szövegmezőt tartalmaz: "táblanév", amely a "H R-feladatok" kifejezést tartalmazza, és rendelkezik egy legördülő listával, és "sor i d", amely "S A _ REP" karaktert tartalmaz.](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Ebben a következő lépésben használhatja a többi összekötőt a munkafolyamat felépítéséhez. Ha szeretné tesztelni az Oracle-adatok beolvasását, akkor küldjön Önnek egy e-mailt az Oracle-adatokkal az e-mailek küldése, például az Office 365 Outlook használatával. Az Oracle-táblázat dinamikus jogkivonatait használva hozza létre a `Subject` és az `Body` e-mail-címét:

    ![Két párbeszédpanel van. Az "e-mail küldése Box" mező a "szövegtörzs", a "tárgy" és a "to" címet határozza meg az e-mailben. A dinamikus tartalom hozzáadása párbeszédpanel a folyamat alkalmazásaiból és szolgáltatásaiból származó dinamikus tartalmak keresését teszi lehetővé.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Mentse** a logikai alkalmazást, majd válassza a **Futtatás** lehetőséget. Zárjuk be a tervezőt, és tekintse meg az állapot futtatási előzményeit. Ha nem sikerül, válassza a hibás üzenet sort. Megnyílik a tervező, és megmutatja, hogy melyik lépés nem sikerült, és a hiba adatait is megjeleníti. Ha ez sikeres, akkor e-mailt kell kapnia a hozzáadott információkkal.


### <a name="workflow-ideas"></a>Munkafolyamat-ötletek

* Figyelni szeretné a #oracle hashtaget, és egy adatbázisba helyezi a tweeteket, hogy lekérdezhető legyen, és más alkalmazásokban is használható legyen. Egy logikai alkalmazásban adja hozzá az `Twitter - When a new tweet is posted` triggert, és adja meg a **#oracle** hashtaget. Ezután adja hozzá a `Oracle Database - Insert row` műveletet, és válassza ki a táblát:

    ![Az "új Tweet közzétételekor" párbeszédpanel a "hashtag Oracle" kifejezést jeleníti meg keresési szövegként, és lehetővé teszi az ellenőrzési gyakoriság megadását. Ez a párbeszédpanel a "Oracle Database" párbeszédpanelt eredményezi, amely lehetővé teszi a művelet kiválasztását.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Az üzenetek küldése egy Service Bus várólistára történik. Ezeket az üzeneteket szeretné lekérni, és egy adatbázisba helyezi őket. A logikai alkalmazásban adja hozzá az `Service Bus - when a message is received in a queue` triggert, majd válassza ki a várólistát. Ezután adja hozzá a `Oracle Database - Insert row` műveletet, és válassza ki a táblát:

    ![A "Ha egy üzenet érkezik..." párbeszédpanel a "megrendelések" nevet jeleníti meg "várólista neve" néven, és lehetővé teszi az ellenőrzési gyakoriság megadását. Ez a mező a "sor beszúrása (előnézet)" párbeszédpanelt eredményezi, amely lehetővé teszi a "táblanév" kiválasztását.](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Gyakori hibák

#### <a name="error-cannot-reach-the-gateway"></a>**Hiba**: az átjáró nem érhető el

**OK**: a helyszíni adatátjáró nem tud csatlakozni a felhőhöz. 

Megoldás **: Ellenőrizze**, hogy az átjáró fut-e a helyszíni gépen, amelyen telepítve van, és hogy tud-e csatlakozni az internethez.  Azt javasoljuk, hogy ne telepítse az átjárót olyan számítógépre, amely kikapcsolható vagy alvó állapotba kerülhet. Újraindíthatja a helyszíni adatátjáró szolgáltatást (PBIEgwService) is.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Hiba**: a használt szolgáltató elavult: "System. OracleClient – az Oracle-ügyfélszoftver 8.1.7 vagy újabb verziója szükséges." Lásd: [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) a hivatalos szolgáltató telepítése.

**OK**: az Oracle Client SDK nincs telepítve azon a gépen, amelyen a helyszíni adatátjáró fut.  

**Megoldás**: töltse le és telepítse az Oracle Client SDK-t ugyanarra a számítógépre, mint a helyszíni adatátjárót.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Hiba**: a (z) "[táblanév]" tábla nem definiál kulcs oszlopokat

**OK**: a táblának nincs elsődleges kulcsa.  

**Megoldás**: a Oracle Database összekötőhöz meg kell adni egy elsődleges kulcs oszlopot tartalmazó táblát.
 
## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/oracle/)megjelenő korlátokat is. 

## <a name="get-some-help"></a>Segítség kérése

A [Microsoft Q&egy kérdés oldal a Azure Logic apps](/answers/topics/azure-logic-apps.html) számára, hogy kérdéseket tegyen fel, válaszoljon a kérdésekre, és megtekintheti a többi Logic apps-felhasználót. 

Segítheti a Logic Apps és összekötők fejlesztését a szavazással és az ötletek elküldésével [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Következő lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md), és fedezze fel az elérhető összekötőket Logic apps API-k [listájában](apis-list.md).