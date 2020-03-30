---
title: 'Gyors útmutató: Cassandra API a Node.js-szel – Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Cassandra API profilalkalmazások létrehozására a Node.js használatával
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77210355"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Rövid útmutató: Cassandra-alkalmazás létrehozása node.js SDK-val és Az Azure Cosmos DB-vel

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban hozzon létre egy Azure Cosmos DB Cassandra API-fiókot, és a GitHubról klónozott Cassandra Node.js alkalmazás használatával hozzon létre egy Cassandra-adatbázist és -tárolót. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Alternatív lehetőségként [kipróbálhatja ingyenesen az Azure Cosmos DB-t](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés, díjfizetés és elköteleződés nélkül.

Emellett szüksége lesz a következőkre:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) verzió v0.10.29 vagy újabb
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most klónozzunk egy Cassandra API-alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git-terminálablakot, például a git basht. A `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. A kódrészletek mind a `uprofile.js` fájlból származnak, amely a `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` mappában található. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

* A felhasználónév és a jelszó értékei a kapcsolati sztring oldalán állíthatók be az Azure Portalon. A `path\to\cert` egy X509-tanúsítvány elérési útját tartalmazza. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* A `client` inicializálása contactPoint adataival történik. A contactPoint az Azure Portalról kérhető le.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* A `client` csatlakozik az Azure Cosmos DB Cassandra API-hoz.

    ```javascript
    client.connect(next);
    ```

* A rendszer létrehoz egy új kulcsteret.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Létrejön egy új tábla.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* A rendszer beszúrja a kulcs/érték entitásokat.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Lekérdezés az összes kulcsérték lekéréséhez.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Lekérdezés a kulcs-érték lekéréséhez.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza a **Kapcsolati karakterlánc**lehetőséget. 

    Válassza a ![a képernyő jobb oldalán található Másolás gombot](./media/create-cassandra-nodejs/copy.png) használatával (a képernyő jobb oldalán) másolja a vágólapra a felső, CONTACT POINT (Kapcsolódási pont) értéket.

    ![A CONTACT POINT (Kapcsolódási pont), USERNAME (Felhasználónév) és a PASSWORD (Jelszó) értékek másolása az Azure Portal kapcsolati sztring oldaláról](./media/create-cassandra-nodejs/keys.png)

2. Nyissa meg az `config.js` fájlt. 

3. Illessze be a CONTACT POINT értéket a portálról a `<FillMEIN>` helyére a 4. sorban.

    A 4. sornak ekkor a következőképp kell kinéznie: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Másolja a USERNAME értéket a portálról, és illessze be a `<FillMEIN>` helyére a 2. sorban.

    A 2. sornak ekkor a következőképp kell kinéznie: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Másolja a PASSWORD értéket a portálról, és illessze be a `<FillMEIN>` helyére a 3. sorban.

    A 3. sornak ekkor a következőképp kell kinéznie:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Mentse a `config.js` fájlt.
    
## <a name="use-the-x509-certificate"></a>Az X509-tanúsítvány használata

1. Töltse le a Baltimore CyberTrust [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)root tanúsítványt helyileg a ból. Nevezze át a fájlt `.cer` kiterjesztésűre.

   A tanúsítvány sorozatszáma `02:00:00:b9`, az SHA1 ujjlenyomata pedig `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Nyissa meg `uprofile.js` fájlt, és változtassa meg a `path\to\cert` útvonalat úgy, hogy az új tanúsítványára mutasson.

3. Mentse a `uprofile.js` fájlt.

> [!NOTE]
> Ha a későbbi lépésekben tanúsítványokkal kapcsolatos hibát tapasztal, és Windows rendszerű számítógépen fut, győződjön meg arról, hogy követte a .crt fájlok microsoftos .cer formátumba történő megfelelő konvertálásának folyamatát.
> 
> Kattintson duplán a .crt fájlra, és nyissa meg a tanúsítvány kijelzőjén. 
>
> ![A kimenet megtekintése és ellenőrzése](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Nyomja meg a Tovább gombot a Tanúsítvány varázslón. Válassza a Base-64 kódolású X.509 (. CER), majd tovább.
>
> ![A kimenet megtekintése és ellenőrzése](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Válassza a Tallózás gombot (a cél megkereséséhez), és írja be a fájlnevet.
> Válassza a Tovább, majd a Kész lehetőséget.
>
> Most már megfelelően formázott .cer fájllal kell rendelkeznie. Győződjön meg `uprofile.js` arról, hogy a fájl elérési útja a fájlra mutat.

## <a name="run-the-nodejs-app"></a>A Node.js-alkalmazás futtatása

1. A git terminálablakban győződjön meg arról, hogy a korábban klónozott mintakönyvtárban van:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Futtassa `npm install` a szükséges npm modulok telepítéséhez.

3. Futtassa a `node uprofile.js` parancsot a node-alkalmazás elindításához.

4. Ellenőrizze az eredményt a parancssorban.

    ![A kimenet megtekintése és ellenőrzése](./media/create-cassandra-nodejs/output.png)

    Nyomja le a CTRL+C billentyűkombinációt a program végrehajtásának leállításához és a konzolablak bezárásához. 

5. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    ![Adatok megtekintése az Adatkezelőben](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot cassandra API-val, és futtathatja a Cassandra Node.js alkalmazást, amely létrehoz egy Cassandra-adatbázist és -tárolót. Most már importálhat további adatokat az Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)