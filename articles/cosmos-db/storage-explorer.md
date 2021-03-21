---
title: Azure Cosmos DB erőforrások kezelése Azure Storage Explorer használatával
description: Megtudhatja, hogyan csatlakozhat a Azure Cosmos DBhoz, és hogyan kezelheti erőforrásait Azure Storage Explorer használatával.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348653"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Cosmos DB erőforrások kezelése Azure Storage Explorer használatával
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Storage Explorer használatával csatlakozhat Azure Cosmos DBhoz. Lehetővé teszi az Azure-beli és a szuverén felhőkben üzemeltetett Azure Cosmos DB-fiókokhoz való kapcsolódást Windows, macOS vagy Linux rendszerről.

Ugyanazzal az eszközzel kezelheti a különböző Azure-entitásokat egy helyen. Felügyelheti Azure Cosmos DB entitásokat, kezelheti az adatkezelést, frissítheti a tárolt eljárásokat és triggereket más Azure-entitásokkal, például a tárolási Blobokkal és a várólistákkal együtt. Azure Storage Explorer támogatja az SQL, a MongoDB, a Graph és a Table API-khoz konfigurált Cosmos-fiókokat.

> [!NOTE]
> A Storage Explorer Azure Cosmos DB-integrációja elavult. A meglévő funkciók nem lesznek eltávolítva az ettől a kiadástól számított legalább egy évig. Ehelyett használja az [Azure Portalt](https://portal.azure.com/), az [Azure Portal Desktop alkalmazást](https://portal.azure.com/App/Download) vagy az önálló [Azure Cosmos db Explorert](data-explorer.md) . Az alternatív lehetőségek számos olyan új funkciót tartalmaznak, amelyeket a Storage Explorer jelenleg nem támogat.

## <a name="prerequisites"></a>Előfeltételek

Egy SQL API-val vagy egy Azure Cosmos DB API-val rendelkező Cosmos-fiók MongoDB. Ha nem rendelkezik fiókkal, létrehozhat egyet a Azure Portal. További információért lásd [Azure Cosmos db: SQL API-Webalkalmazás létrehozása .net-tel és a Azure Portal](create-sql-api-dotnet.md) .

## <a name="installation"></a>Telepítés

A legújabb Azure Storage Explorer BITS telepítéséhez lásd: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). A Windows-, Linux-és macOS-verziók támogatottak.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. A **Azure Storage Explorer** telepítése után válassza ki a **beépülő modul** ikont a bal oldali ablaktáblán.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Képernyőfelvétel: a beépülő modul ikonja a bal oldali ablaktáblán.":::

1. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Képernyőkép a Kapcsolódás az Azure Storage-hoz ablakról: az Azure-fiók hozzáadása választógomb be van jelölve, és az Azure-környezet legördülő menüje.":::

1. Az **Azure bejelentkezési** párbeszédpaneljén válassza a **Bejelentkezés** lehetőséget, majd adja meg az Azure-beli hitelesítő adatait.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Képernyőkép a bejelentkezési ablakról, amelyen megadhatja az Azure-előfizetéséhez tartozó hitelesítő adatokat.":::

1. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Képernyőfelvétel a fiókkezelés panelről, amely az előfizetések listáját és az Apply (alkalmaz) gombot jeleníti meg.":::

    Az Explorer ablaktábla frissíti és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Képernyőkép az Explorer ablaktábláról, amely a kijelölt előfizetésben lévő fiókok megjelenítésére lett frissítve.":::

    Az **Cosmos db-fiókja** az Azure-előfizetéshez csatlakozik.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Kapcsolati karakterlánc használata Azure Cosmos DBhoz való kapcsolódáshoz

Kapcsolati sztringet használhat egy Azure Cosmos DBhoz való kapcsolódáshoz. Ez a metódus csak az SQL és a Table API-kat támogatja. A kapcsolati karakterlánccal való kapcsolódáshoz kövesse az alábbi lépéseket:

1. Keresse meg a **helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal **Cosmos db fiókok** elemre, majd válassza a **Kapcsolódás Cosmos db** lehetőséget.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Képernyőfelvétel: a jobb gombbal a legördülő menüre kattintva, a Kapcsolódás az Azure Cosmos D B-hez kiemelve.":::

2. A **kapcsolódás Cosmos db** ablakban:
   1. Válassza ki az API-t a legördülő menüből.
   1. Illessze be a kapcsolatok karakterláncát a **kapcsolatok karakterlánc** mezőjébe. Az elsődleges kapcsolódási karakterlánc lekéréséhez lásd: [a kapcsolódási karakterlánc beolvasása](manage-with-powershell.md#list-keys).
   1. Adja meg a **fiók címkéjét**, majd kattintson a **tovább** gombra az összefoglalás vizsgálatához.
   1. Válassza a **Kapcsolódás** lehetőséget a Azure Cosmos db fiók összekapcsolásához.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Képernyőkép a Kapcsolódás a Cosmos D B ablakhoz, amely az API legördülő menüt, a kapcsolati karakterláncot és a fiók címkéjét jeleníti meg.":::

> [!NOTE]
> Ha a Azure Storage Explorer azt mutatja, hogy a Azure Cosmos DB-kapcsolatok karakterlánca érvénytelen formátumú, győződjön meg arról, hogy a (z) végpont () pontosvesszővel () végződik a kapcsolatok karakterlánca `;` . Érvényes Azure Cosmos DB-kapcsolatok karakterlánca például a következő: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Helyi emulátor használata Azure Cosmos DBhoz való kapcsolódáshoz

A következő lépésekkel csatlakozhat egy Azure Cosmos DB emulátorral. Ez a metódus csak az SQL-fiókokat támogatja.

1. Telepítse Cosmos DB emulátort, majd nyissa meg. Az emulátor telepítéséhez lásd: [Cosmos db Emulator](./local-emulator.md).

1. Keresse meg a **helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal **Cosmos db fiókok** elemre, majd válassza a **Kapcsolódás Cosmos db emulátorhoz** lehetőséget.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintva megjelenő menü, amely az Azure Cosmos D B-emulátorhoz csatlakozik.":::

1. A **kapcsolódás Cosmos db** ablakban:
   1. Illessze be a kapcsolatok karakterláncát a **kapcsolatok karakterlánc** mezőjébe. Az elsődleges kapcsolatok karakterláncának beolvasásával kapcsolatos információkért lásd: [a kapcsolatok karakterláncának](manage-with-powershell.md#list-keys)beolvasása.
   1. Adja meg a **fiók címkéjét**, majd kattintson a **tovább** gombra az összefoglalás vizsgálatához.
   1. Válassza a **Kapcsolódás** lehetőséget a Azure Cosmos db fiók összekapcsolásához.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Képernyőkép a Kapcsolódás a Cosmos D B ablakhoz, a kapcsolati karakterlánc mező és a fiók címkéje mező.":::

## <a name="azure-cosmos-db-resource-management"></a>Az Azure Cosmos DB-erőforrások kezelése

Azure Cosmos DB fiók kezeléséhez használja a következő műveleteket:

* Nyissa meg a fiókot a Azure Portalban.
* Adja hozzá az erőforrást a gyors elérési listához.
* Erőforrások keresése és frissítése.
* Adatbázisok létrehozása és törlése.
* Gyűjtemények létrehozása és törlése.
* Dokumentumok létrehozása, szerkesztése, törlése és szűrése.
* Tárolt eljárások, eseményindítók és felhasználó által definiált függvények kezelése.

### <a name="quick-access-tasks"></a>Feladatok gyors elérése

A jobb gombbal az Explorer ablaktáblán egy előfizetésre kattintva számos gyors művelettel kapcsolatos feladatot hajthat végre, például:

* Kattintson a jobb gombbal egy Azure Cosmos DB-fiókra vagy-adatbázisra, majd válassza a **Megnyitás a portálon** lehetőséget, hogy az erőforrást a Azure Portal böngészőben kezelhesse.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintva megjelenő menü, amely a Megnyitás a portálon lehetőségre mutat.":::

* Kattintson a jobb gombbal egy Azure Cosmos DB fiókra, adatbázisra vagy gyűjteményre, majd válassza a **Hozzáadás a gyors eléréshez** lehetőséget a gyorselérési menü hozzáadásához.

* Válassza a **Keresés innen** lehetőséget a kulcsszavas keresés engedélyezéséhez a kiválasztott elérési úton.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="A keresőmezőbe Kiemelt keresőmezőt ábrázoló képernyőkép.":::

### <a name="database-and-collection-management"></a>Adatbázisok és gyűjtemények kezelése

#### <a name="create-a-database"></a>Adatbázis létrehozása

1. Kattintson a jobb gombbal a Azure Cosmos DB fiókra, majd válassza az **adatbázis létrehozása** lehetőséget.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintás után megjelenő menü, az adatbázis létrehozása kiemelve.":::

1. Adja meg az adatbázis nevét, majd nyomja le az **ENTER** billentyűt a befejezéshez.

#### <a name="delete-a-database"></a>Adatbázis törlése

1. Kattintson a jobb gombbal az adatbázisra, majd válassza az **adatbázis törlése** lehetőséget. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintva megjelenő menü, az adatbázis törlése kiemelve.":::

1. Az előugró ablakban válassza az **Igen** lehetőséget. A rendszer törli az adatbázis-csomópontot, és automatikusan frissíti az Azure Cosmos DB-fiókot.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Képernyőkép a megerősítő ablakról az igen gombbal kiemelve.":::

#### <a name="create-a-collection"></a>Gyűjtemény létrehozása

1. Kattintson a jobb gombbal az adatbázisra, majd válassza a **gyűjtemény létrehozása** lehetőséget.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintva megjelenő menü, a létrehozási gyűjtemény kiemelésével.":::

1. A gyűjtemény létrehozása ablakban adja meg a kért információkat, például a **gyűjtemény azonosítóját** **és tárolókapacitását, és** így tovább. A befejezéshez kattintson **az OK gombra** .

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Képernyőfelvétel a gyűjtemény létrehozása ablakról, amely a gyűjtemény I D mezőjét és a tárolókapacitás gombokat mutatja.":::

1. Válassza a **korlátlan** lehetőséget, ha megad egy partíciós kulcsot, majd kattintson **az OK** gombra a befejezéshez.

   > [!NOTE]
   > Ha a gyűjtemény létrehozásakor egy partíciós kulcsot használ, a létrehozás befejezését követően a partíciós kulcs értéke nem módosítható a gyűjteményben.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Képernyőfelvétel a gyűjtemény létrehozása ablakról, amely a tárterület korlátlan kiválasztását mutatja, a partíciós kulcs mező pedig ki van emelve.":::

#### <a name="delete-a-collection"></a>Gyűjtemény törlése

- Kattintson a jobb gombbal a gyűjteményre, válassza a **gyűjtemény törlése** lehetőséget, majd az előugró ablakban válassza az **Igen** lehetőséget.

    A rendszer törli a gyűjteménycsomópontot, és automatikusan frissíti az adatbázist.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Képernyőfelvétel: a jobb gombbal kattintás után megjelenő menü, a gyűjtemény törlése kiemelve.":::

### <a name="document-management"></a>Dokumentumok kezelése

#### <a name="create-and-modify-documents"></a>Dokumentumok létrehozása és módosítása

- Nyissa meg a **dokumentumok** elemet a bal oldali ablaktáblán, válassza az **új dokumentum** lehetőséget, szerkessze a tartalmat a jobb oldali ablaktáblán, majd kattintson a **Mentés** gombra.
- Egy meglévő dokumentumot is frissíthet, majd a **Mentés** gombra kattinthat. A módosítások elvetéséhez válassza az **Elvetés** lehetőséget.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="A bal oldali ablaktáblán Kiemelt dokumentumokat bemutató képernyőkép. A jobb oldali panelen új dokumentum, mentés és Elvetés van kiemelve.":::

#### <a name="delete-a-document"></a>Dokumentum törlése

* Kattintson a **Törlés** gombra a kiválasztott dokumentum törléséhez.

#### <a name="query-for-documents"></a>Dokumentumok lekérdezése

* A dokumentum szűrő szerkesztéséhez írjon be egy [SQL-lekérdezést](./sql-query-getting-started.md), majd válassza az **alkalmaz** lehetőséget.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Képernyőkép a jobb oldali ablaktábláról: a szűrő és az alkalmazás gomb, az azonosító száma és a lekérdezési mező kiemelve.":::

### <a name="graph-management"></a>Gráfok kezelése

#### <a name="create-and-modify-a-vertex"></a>Csúcspont létrehozása és módosítása

* Új csúcspont létrehozásához nyissa meg a **Graph** elemet a bal oldali ablaktáblán, válassza az **új csúcspont** lehetőséget, szerkessze a tartalmat, majd kattintson **az OK gombra**.
* Egy meglévő csúcspont módosításához válassza a jobb oldali ablaktábla toll ikonját.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="A bal oldali panelen kijelölt gráfot ábrázoló képernyőkép, amely az új csúcspontot és a jobb oldali ablaktáblán látható toll ikont jeleníti meg.":::

#### <a name="delete-a-graph"></a>Gráf törlése

* Csúcspont törléséhez válassza a csúcspont neve melletti Lomtár ikont.

#### <a name="filter-for-graph"></a>Gráfok szűrése

* A Graph-szűrő szerkesztéséhez adjon meg egy [Gremlin-lekérdezést](gremlin-support.md), majd válassza a **szűrő alkalmazása** lehetőséget.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="A bal oldali ablaktáblán kijelölt gráfot ábrázoló képernyőfelvétel, valamint a jobb oldali ablaktáblán látható a szűrő alkalmazása és a lekérdezési mező.":::

### <a name="table-management"></a>Táblakezelés

#### <a name="create-and-modify-a-table"></a>Tábla létrehozása és módosítása

* Új tábla létrehozása:
   1. A bal oldali ablaktáblán nyissa meg az **entitások** elemet, majd válassza a **Hozzáadás** lehetőséget.
   1. Az **entitás hozzáadása** párbeszédpanelen szerkessze a tartalmat.
   1. A tulajdonság hozzáadásához kattintson a **tulajdonság hozzáadása** gombra.
   1. Válassza a **Beszúrás** lehetőséget.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Képernyőfelvétel: a bal oldali ablaktáblán Kiemelt entitások, valamint a jobb oldali ablaktáblán a Hozzáadás, a Szerkesztés, a Hozzáadás, a tulajdonság és a Beszúrás kiemelten látható.":::

* Egy tábla módosításához válassza a **Szerkesztés** lehetőséget, módosítsa a tartalmat, majd válassza a **frissítés** lehetőséget.

   

#### <a name="import-and-export-table"></a>Táblák importálása és exportálása

* Az importáláshoz kattintson az **Importálás** gombra, majd válasszon egy meglévő táblát.
* Az exportáláshoz válassza az **Exportálás** gombot, majd válasszon egy célhelyet.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Képernyőfelvétel: az importálási és exportálási gombok a jobb oldali ablaktáblán láthatók.":::

#### <a name="delete-entities"></a>Entitások törlése

* Válassza ki az entitásokat, majd kattintson a **Törlés** gombra.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Képernyőfelvétel: a jobb oldali panelen Kiemelt törlés gomb, valamint egy megerősítő előugró ablak, amely igen kiemelten jelenik meg.":::

#### <a name="query-a-table"></a>Tábla lekérdezése

- Válassza a **lekérdezés** gombot, adjon meg egy lekérdezési feltételt, majd kattintson a **lekérdezés végrehajtása** gombra. A lekérdezési ablaktábla bezárásához kattintson a **lekérdezés lezárása** gombra.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Képernyőfelvétel a jobb oldali ablaktábláról: a lekérdezés végrehajtása gomb és a Kiemelt lekérdezés lezárása gomb.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Tárolt eljárások, eseményindítók és felhasználói függvények kezelése

* Tárolt eljárás létrehozása:
  1. A bal oldali fában kattintson a jobb gombbal a **tárolt eljárások** elemre, majd válassza a **tárolt eljárás létrehozása** lehetőséget.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Képernyőkép a bal oldali panelről, amely a jobb gombbal, a létrehozott tárolt eljárás létrehozásával megjelenített menüt jeleníti meg.":::
  
  1. Adjon meg egy nevet a bal oldalon, írja be a tárolt eljáráshoz tartozó parancsfájlokat a jobb oldali ablaktáblán, majd válassza a **Létrehozás** lehetőséget.
  
* Meglévő tárolt eljárás szerkesztéséhez kattintson duplán az eljárásra, végezze el a frissítést, majd válassza a **frissítés** lehetőséget a mentéshez. A módosítás megszakításához válassza az **Elvetés** lehetőséget is.

* Az **Eseményindítók** és az **UDF** műveletek hasonlóak a **tárolt eljárásokhoz**.

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban a Storage Explorer Azure Cosmos DBjának használatakor felmerülő gyakori problémák megoldásait ismertetjük.

### <a name="sign-in-issues"></a>Bejelentkezési problémák

Először indítsa újra az alkalmazást, és ellenőrizze, hogy megoldotta-e a problémát. Ha a probléma továbbra is fennáll, folytassa a hibaelhárítást.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Önaláírt tanúsítványok a tanúsítványláncokban

Előfordulhat, hogy ezt a hibát tapasztalja, a két leggyakoribb a következő:

* Egy *transzparens proxy* mögött van. Valaki, mint az informatikai részleg, elfogja a HTTPS-forgalmat, visszafejti azt, majd egy önaláírt tanúsítvánnyal titkosítja azt.

* Szoftvereket, például víruskereső szoftvereket futtat. A szoftver egy önaláírt TLS/SSL-tanúsítványt szúr be a kapott HTTPS-üzenetbe.

Ha Storage Explorer megtalál egy önaláírt tanúsítványt, nem tudja, hogy a kapott HTTPS-üzenet illetéktelenül van-e módosítva. Ha rendelkezik az önaláírt tanúsítvány egy példányával, megadhatja, hogy Storage Explorer megbízzon. Ha nem biztos abban, hogy ki injektálta a tanúsítványt, akkor az alábbi lépéseket követve próbálja meg kideríteni:

1. Az OpenSSL telepítése:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): a világos verziók bármelyike ok.
     - macOS és Linux: az operációs rendszer részét képezi.

1. OpenSSL futtatása:
    * Windows: Nyissa meg a telepítési könyvtárat, majd a **/bin/**, majd kattintson duplán a **openssl.exe** elemre.
    * Mac és Linux: az **OpenSSL** végrehajtása terminálról.
1. Végrehajtás `s_client -showcerts -connect microsoft.com:443` .
1. Keresse meg az önaláírt tanúsítványokat. Ha nem biztos abban, hogy önaláírtak, akkor keresse meg a tárgy ("s:") és a kiállító ("i:") megegyezőjét.
1. Ha bármilyen önaláírt tanúsítványt talál, másolja ki és illessze be a **-----a tanúsítvány megkezdése-----** , hogy **-----a záró tanúsítvány-----** egy újat. A CER-fájl mindegyikhez.
1. Nyissa meg Storage Explorer, majd lépjen az  >  **SSL-tanúsítványok**  >  **importálása tanúsítványok** módosítása elemre. A file Picker használatával keresse meg, válassza ki, majd nyissa meg a következőt:. A létrehozott CER-fájlok.

Ha nem talál önaláírt tanúsítványokat, küldjön visszajelzést további segítségért.

#### <a name="unable-to-retrieve-subscriptions"></a>Az előfizetéseket nem sikerül lekérni

Ha nem tudja beolvasni az előfizetéseket a bejelentkezés után, próbálja meg a következő javaslatokat:

* Ellenőrizze, hogy a fiókja rendelkezik-e hozzáféréssel az előfizetésekhez. Ehhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
* Győződjön meg arról, hogy a megfelelő környezetbe jelentkezett be:
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Germany](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * Egyéni környezet/Azure Stack
* Ha proxy mögött található, győződjön meg arról, hogy a Storage Explorer proxy megfelelően van konfigurálva.
* Távolítsa el a fiókot, majd adja hozzá újra.
* Törölje a következő fájlokat a saját könyvtárából (például: C:\Users\ContosoUser), majd adja hozzá újra a fiókot:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Nyomja le az F12 billentyűt a fejlesztői konzol megnyitásához. A bejelentkezéskor tekintse meg a konzolon megjelenő hibaüzeneteket.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Képernyőkép a fejlesztői eszközök konzolról, amelyen a konzol ki van emelve.":::

#### <a name="unable-to-see-the-authentication-page"></a>A hitelesítési oldalt nem sikerül megjeleníteni

Ha nem látja a hitelesítési oldalt:

* A kapcsolódás sebességétől függően eltarthat egy ideig, amíg a bejelentkezési oldal betöltődik. Várjon legalább egy percet, mielőtt bezárná a hitelesítés párbeszédpanelt.
* Ha proxy mögött található, győződjön meg arról, hogy a Storage Explorer proxy megfelelően van konfigurálva.
* A fejlesztői eszközök konzolján (F12) tekintse meg a válaszokat, amelyekkel megtekintheti, hogy a hitelesítés miért nem működik.

#### <a name="cant-remove-an-account"></a>Nem lehet eltávolítani egy fiókot

Ha nem tudja eltávolítani a fiókot, vagy ha az újrahitelesítés hivatkozása nem tesz semmit:

* Törölje a következő fájlokat a saját könyvtárából, majd adja hozzá újra a fiókot:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Ha szeretné eltávolítani az SAS-hez csatolt Storage-erőforrásokat, törölje a következőket:
  * Windows rendszeren az %AppData%/StorageExplorer mappát
  * /Users/<your_name>/Library/Application Support támogatás/StorageExplorer macOS rendszerhez
  * Linux rendszeren a ~/.config/StorageExplorer mappát
  
  > [!NOTE]
  > Ha törli ezeket a fájlokat, **újra meg kell adnia az összes hitelesítő adatot**.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS-proxyval kapcsolatos probléma

A bal oldali fában nem listázhat Azure Cosmos DB csomópontokat, amikor HTTP/HTTPS-proxyt állít be a kiegészítő szolgáltatásban. A Azure Portalban a Azure Cosmos DB adatkezelőt is használhatja munkahelyként.

### <a name="development-node-under-local-and-attached-node-issue"></a>A „Helyi és csatolt” csomópont alatti „Fejlesztés” csomópont problémája

A bal oldali fában a **helyi és a csatolt** csomópont alatt válassza ki a **fejlesztési** csomópontot. Ennek így is kell lennie.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="A kiválasztott fejlesztési csomópontot bemutató képernyőkép.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Azure Cosmos DB fiók csatolása a **helyi és a csatolt** csomópont hibájában

Ha a következő hibaüzenetet látja, miután csatlakoztatta Azure Cosmos DB fiókot a **helyi és a csatolt** csomóponthoz, akkor győződjön meg arról, hogy a megfelelő kapcsolattípus-karakterláncot használja.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Képernyőfelvétel: nem sikerült beolvasni a gyermek erőforrások hiba előugró ablakát, amely jelzi a getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB-csomópont kibontása sikertelen

A bal oldali fában található csomópontok kibontásakor a következő hibaüzenet jelenhet meg.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Képernyőfelvétel: nem sikerült beolvasni a gyermek erőforrások hiba előugró ablakát, ami azt jelzi, hogy nem lehet kapcsolódni ehhez a Cosmos D B-fiókhoz.":::

Próbálja ki ezeket a javaslatokat:

* Ellenőrizze, hogy a Azure Cosmos DB-fiók üzembe helyezése folyamatban van-e. Próbálkozzon újra, ha a fiók létrehozása sikeres volt.
* Ha a fiók a **gyors hozzáférés** vagy a **helyi és a csatlakoztatott** csomópontok alatt van, ellenőrizze, hogy a fiók törölve van-e. Ha igen, manuálisan kell eltávolítania a csomópontot.

## <a name="next-steps"></a>Következő lépések

* Tekintse meg ezt a videót, amelyből megtudhatja, hogyan használhatja a Azure Cosmos DB a Azure Storage Explorerban: [Azure Cosmos db használata a Azure Storage Explorerban](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* További információk a Storage Explorerrel és további szolgáltatások csatlakoztatásával kapcsolatban: [Ismerkedés a Storage Explorer alkalmazással](../vs-azure-tools-storage-manage-with-storage-explorer.md).
