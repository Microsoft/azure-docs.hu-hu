---
title: 'Lekérdezés Azure Cosmos DB Gremlin API-val a TinkerPop Gremlin konzol használatával: oktatóanyag'
description: Az Azure Cosmos DB rövid útmutatója csúcsok, élek és lekérdezések az Azure Cosmos DB Gremlin API-val történő létrehozásához.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/10/2020
ms.author: chrande
ms.openlocfilehash: 20cb4cea52317022aea8a5b9e4b8280f8b88ff85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361511"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Rövid útmutató: Azure Cosmos DB gráfadatbázis létrehozása, lekérdezése és bejárása a Gremlin konzol használatával
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin-konzol](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Cosmos DB [GREMLIN API](graph-introduction.md) -fiókot, adatbázist és gráfot (tárolót) a Azure Portal használatával, majd az [Apache TinkerPop](https://tinkerpop.apache.org) [Gremlin-KONZOLját](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) használhatja a Gremlin API-adatokkal való együttműködéshez. Ebben az oktatóanyagban éleket és csúcspontokat hoz létre és kérdez le, csúcsponttulajdonságokat frissít, csúcspontokat kérdez le, bejárja a gráfot és elvet csúcspontokat.

:::image type="content" source="./media/create-graph-gremlin-console/gremlin-console.png" alt-text="Azure Cosmos DB az Apache Gremlin-konzolból":::

A Gremlin konzol egy Groovy/Java-alapú program, mely Linux, Mac és Windows rendszereken futtatható. A konzol az [Apache TinkerPop webhelyről](https://tinkerpop.apache.org/downloads.html) tölthető le.

## <a name="prerequisites"></a>Előfeltételek

A bevezető során az Azure Cosmos DB-fiók létrehozásához Azure-előfizetés szükséges.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Emellett a [Gremlin-konzolnak](https://tinkerpop.apache.org/downloads.html) telepítve kell lennie. Az **ajánlott verzió: v 3.4.3** vagy korábbi. (Ha Windows rendszeren szeretné használni a Gremlin-konzolt, telepítenie kell a [Java Runtime](https://www.oracle.com/technetwork/java/javase/overview/index.html)szolgáltatást).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="connect-to-your-app-servicegraph"></a><a id="ConnectAppService"></a>Kapcsolódás az App Service-hez/gráfhoz

1. A Gremlin konzol elindítása előtt hozza létre vagy módosítsa a remote-secure.yaml konfigurációs fájlt az `apache-tinkerpop-gremlin-console-3.2.5/conf` könyvtárban.
2. Adja meg a *host* (gazdagép), *port*, *username* (felhasználónév), *password* (jelszó), *ConnectionPool* (kapcsolatkészlet), és *serializer* (szerializáló) konfigurációkat az alábbi táblázat szerint:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    gazdagépek|[*fiók neve*. **Gremlin**. Cosmos.Azure.com]|Tekintse meg a következő képernyőképet. Ez a **GREMLIN URI** -érték a Azure Portal áttekintés lapján, szögletes zárójelben, a következő záróval: 443/eltávolítva. Megjegyzés: Ügyeljen arra, hogy a Gremlin értéket használja, és **ne** a [*fióknév*. Documents.Azure.com] végződésű URI-t, amely valószínűleg a "gazdagép nem válaszolt időben" kivételt eredményezett, amikor később megpróbálta végrehajtani a Gremlin-lekérdezéseket. 
    port|443|Állítsa 443 értékre.
    username|*Az Ön felhasználóneve*|A `/dbs/<db>/colls/<coll>` űrlap erőforrása, ahol a `<db>` az adatbázis neve és a `<coll>` a gyűjtemény neve.
    jelszó|*Az Ön elsődleges kulcsa*| Lásd az alábbiakban a második képernyőképet. Ez az Ön elsődleges kulcsa, amelyet az Azure Portal Kulcsok oldalának Elsődleges Kulcs mezőjéből kérdezhet le. Az érték másolásához használja a mező bal oldalán lévő Másolás gombot.
    kapcsolatkészlet|{enableSsl: true}|A TLS-hez tartozó kapcsolatok készletének beállítása.
    szerializáló|{ className: org.apache.tinkerpop.gremlin.<br>Driver. ser. GraphSONMessageSerializerV2d0,<br> config: { serializeResultToString: true }}|Állítsa be ezt az értéket, és törölje a `\n` sortöréseket az érték beillesztésekor.

   A gazdagépek értékéhez másolja a **GREMLIN URI** értékét az **Áttekintés** lapról:

   :::image type="content" source="./media/create-graph-gremlin-console/gremlin-uri.png" alt-text="Az Azure Portal Áttekintés lapján található Gremlin URI érték megtekintése és másolása":::

   A jelszó értékéhez másolja az **elsődleges kulcsot** a **kulcsok** lapról:

   :::image type="content" source="./media/create-graph-gremlin-console/keys.png" alt-text="Az elsődleges kulcs megtekintése és másolása a Azure Portal, kulcsok lapon":::

   A remote-secure.yaml fájlnak így kell kinéznie:

   ```yaml
   hosts: [your_database_server.gremlin.cosmos.azure.com] 
   port: 443
   username: /dbs/your_database_account/colls/your_collection
   password: your_primary_key
   connectionPool: {
     enableSsl: true
   }
   serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV2d0, config: { serializeResultToString: true }}
   ```

   Ügyeljen arra, hogy a gazdagépek paraméter értékét a szögletes zárójelben ([]) belül betakarja. 

1. A terminálban futtassa a `bin/gremlin.bat` vagy a `bin/gremlin.sh` parancsot a [Gremlin-konzol](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/) elindításához.

1. A terminálban futtassa a `:remote connect tinkerpop.server conf/remote-secure.yaml` parancsot az alkalmazásszolgáltatáshoz való csatlakozáshoz.

    > [!TIP]
    > Ha a rendszer `No appenders could be found for logger` hibát ad vissza, győződjön meg arról, hogy frissítette a szerializáló értékét a remote-secure.yaml fájlban a 2. lépésben leírtak szerint. Ha a konfiguráció helyes, akkor ez a figyelmeztetés nyugodtan figyelmen kívül hagyható, mivel nem befolyásolhatja a konzol használatát. 

1. Ezután a(z) `:remote console` futtatásával irányítson át minden konzolparancsot a távoli kiszolgálóra.

   > [!NOTE]
   > Ha nem futtatja a `:remote console` parancsot, azonban minden konzolparancsot a távoli kiszolgálóra szeretne irányítani, a parancs elé helyezze a(z) `:>` előtagot, tehát például a következőhöz hasonlóképp futtassa a parancsot: `:> g.V().count()`. Ez az előtag a parancs része, és fontos a Gremlin-konzol Azure Cosmos DB-vel való használatánál. Az előtag kihagyása arra utasítja a konzolt, hogy helyileg, gyakran egy, a memóriában tárolt gráfon hajtsa végre a parancsot. A(z) `:>` előtag alkalmazása távoli parancs végrehajtására utasítja a konzolt ebben az esetben az Azure Cosmos DB-n (a localhost emulátoron vagy egy Azure-példányon).

Nagyszerű! Most, hogy befejeztük a beállítást, futtassunk néhány konzolparancsot!

Próbáljon ki egy egyszerű count() parancsot. Írja be a következőket a konzolon, amikor a rendszer kéri:

```java
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Csúcsok és élek létrehozása

Először hozzon létre öt darab, egy-egy személyt jelölő csúcsot *Thomas*, *Mary Kay*, *Robin*, *Ben*, és *Jack* néven.

Bemenet (Thomas):

```java
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1).property('pk', 'pk')
```

Kimenet:

```bash
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```

Bemenet (Mary Kay):

```java
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2).property('pk', 'pk')

```

Kimenet:

```bash
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Bemenet (Robin):

```java
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3).property('pk', 'pk')
```

Kimenet:

```bash
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Bemenet (Ben):

```java
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4).property('pk', 'pk')

```

Kimenet:

```bash
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Bemenet (Jack):

```java
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5).property('pk', 'pk')
```

Kimenet:

```bash
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Ezután adjunk meg éleket. Ezek a személyek közötti kapcsolatokat jelölik.

Bemenet (Thomas -> Mary Kay):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Kimenet:

```bash
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Bemenet (Thomas -> Robin):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Kimenet:

```bash
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Bemenet (Robin -> Ben):

```java
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Kimenet:

```bash
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Csúcs frissítése

Most frissítsük a *Thomas* csúcspontot új életkor (*45*) megadásával.

Bemenet:
```java
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Kimenet:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Gráf lekérdezése

Futtassunk néhány lekérdezést a gráfon.

Első lépésként futtassunk egy lekérdezést olyan szűrővel, amely csak a 40 évesnél idősebb személyeket adja vissza.

Bemenet (szűrőlekérdezés):

```java
g.V().hasLabel('person').has('age', gt(40))
```

Kimenet:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Most jelenítsük meg a 40 évesnél idősebb személyek utónevét.

Bemenet (szűrő- és kivetítési lekérdezés):

```java
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Kimenet:

```bash
==>Thomas
```

## <a name="traverse-your-graph"></a>A gráf bejárása

A gráf bejárásával most kérdezzük le Thomas összes barátját.

Bemenet (Thomas barátai):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Kimenet: 

```bash
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Ezután folytassuk a csúcsok következő szintjével. A gráf bejárásával kérdezzük le Thomas barátainak minden barátját.

Bemenet (Thomas barátainak barátai):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Kimenet:

```bash
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Csúcs elvetése

Töröljünk egy csúcsot a gráfadatbázisból.

Bemenet (a Jack csúcs elvetése):

```java
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Gráf adatainak törlése

Végezetül töröljük az adatbázisból az összes csúcsot és élet.

Bemenet:

```java
g.E().drop()
g.V().drop()
```

Gratulálunk! Az Azure Cosmos DB: Gremlin API-oktatóanyag végére ért.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

A rövid útmutatóból megtudhatta, hogyan hozhat létre Azure Cosmos DB-fiókot, miként készíthet gráfokat az Adatkezelő használatával, és hogyan hozhat létre csúcsokat és éleket, valamint hogy hogyan járhat be gráfokat a Gremlin-konzollal. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)
