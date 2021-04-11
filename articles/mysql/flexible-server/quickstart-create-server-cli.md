---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure CLI-Azure Database for MySQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy Azure Database for MySQL rugalmas kiszolgáló Azure-erőforráscsoportbeli létrehozásához.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492728"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -parancsokat a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for MySQL rugalmas kiszolgáló öt perc alatt történő létrehozásához. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngésző lapon is megnyithatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az [login](/cli/azure/reference-index#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account#az-account-set) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Rugalmas kiszolgáló létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a `az group create` paranccsal, majd hozza létre a MySQL rugalmas kiszolgálót az erőforráscsoport belsejében. Egyedi nevet adjon meg. A következő példában létrehozunk egy `eastus2` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Hozzon létre egy rugalmas kiszolgálót a `az mysql flexible-server create` paranccsal. Egy kiszolgáló több adatbázist tartalmazhat. A következő parancs egy kiszolgálót hoz létre az Azure CLI [helyi környezetében](/cli/azure/local-context)lévő szolgáltatás-alapértékek és-értékek használatával: 

```azurecli-interactive
az mysql flexible-server create
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik: 
- Automatikusan generált kiszolgálónév, rendszergazdai Felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs meghatározva helyi környezetben), és az erőforráscsoporthoz megegyező helyen 
- Szolgáltatás alapértelmezett értékei a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (feltört), számítási méret/SKU (B1MS), biztonsági mentési megőrzési időtartam (7 nap) és MySQL-verzió (5,7)
- Az alapértelmezett kapcsolódási módszer a privát hozzáférés (VNet-integráció) egy automatikusan generált virtuális hálózattal és alhálózattal

> [!NOTE] 
> A kapcsolódási módszer nem módosítható a kiszolgáló létrehozása után. Ha például a létrehozás során a *privát hozzáférés (VNet-integráció)* lehetőséget választotta, akkor a létrehozás után nem lehet *nyilvános hozzáférésre váltani (engedélyezett IP-címek)* . Javasoljuk, hogy hozzon létre egy olyan kiszolgálót, amely privát hozzáféréssel rendelkezik a kiszolgáló biztonságos eléréséhez a VNet-integráció használatával. További információ a privát hozzáférésről a [fogalmakat ismertető cikkben](./concepts-networking.md).

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) a konfigurálható CLI-paraméterek teljes listájához. 

Néhány példa a kimenetre: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) a konfigurálható CLI-paraméterek teljes listájához. 

## <a name="create-a-database"></a>Adatbázis létrehozása
A következő parancs futtatásával hozzon létre egy adatbázist, **newdatabase** , ha még nem hozott létre egyet.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**. Alább látható egy példa a JSON-kimenetre: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>A kapcsolat csatlakoztatása és tesztelése az Azure CLI használatával

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi a MySQL-kiszolgálóhoz való kapcsolódást az Azure CLI ```az mysql flexible-server connect``` paranccsal. Ezzel a paranccsal tesztelheti a kapcsolatot az adatbázis-kiszolgálóval, létrehozhat egy gyors alapszintű adatbázist, és közvetlenül a kiszolgálón futtathatja a lekérdezéseket anélkül, hogy telepítenie kellene mysql.exe vagy a MySQL Workbench-t.  A parancs futtatása interaktív módban is végezhető több lekérdezés futtatásához.

Futtassa a következő szkriptet az adatbázishoz való kapcsolódás teszteléséhez és ellenőrzéséhez a fejlesztői környezetből.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Példa**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
A sikeres csatlakoztatáshoz a következő kimenetnek kell megjelennie:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Ha a kapcsolatok sikertelenek voltak, próbálkozzon a következő megoldásokkal:
- Ellenőrizze, hogy a 3306-es port nyitva van-e az ügyfélszámítógépen.
- Ha a kiszolgáló rendszergazdája felhasználóneve és jelszava helyes
- Ha konfigurálta az ügyfélszámítógép tűzfalszabály-szabályát
- Ha a kiszolgálót VPN-kapcsolaton keresztül konfigurálta a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

Futtassa az alábbi parancsot egyetlen lekérdezés végrehajtásához argumentum használatával ```--querytext``` ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Példa**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Ha többet szeretne megtudni a ```az mysql flexible-server connect``` parancs használatáról, tekintse meg a [Kapcsolódás és lekérdezés](connect-azure-cli.md) dokumentációját.

## <a name="connect-using-mysql-command-line-client"></a>Kapcsolat a MySQL parancssori ügyféllel

Ha a rugalmas kiszolgálót privát hozzáférés (VNet-integráció) használatával hozta létre, akkor a kiszolgálóval azonos virtuális hálózaton belüli erőforrással kell csatlakoznia a kiszolgálóhoz. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott virtuális hálózathoz. További tudnivalókért tekintse meg a [privát hozzáférési dokumentáció](how-to-manage-virtual-network-portal.md) konfigurálását ismertető témakört.

Ha a rugalmas kiszolgálót a nyilvános hozzáférés (engedélyezett IP-címek) használatával hozta létre, a helyi IP-címet hozzáadhatja a kiszolgálón található tűzfalszabályok listájához. További útmutatásért tekintse meg a [Tűzfalszabályok létrehozásával és kezelésével kapcsolatos dokumentációt](how-to-manage-firewall-portal.md) .

A helyi környezetből [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy [MySQL Workbench](./connect-workbench.md) használatával kapcsolódhat a kiszolgálóhoz. Azure Database for MySQL rugalmas kiszolgáló támogatja az ügyfélalkalmazások a MySQL szolgáltatáshoz való csatlakoztatását Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja a titkosított hálózati kapcsolatokat az adatbázis-kiszolgáló és az ügyfélalkalmazások között, így biztosítva a megfelelőségi követelmények betartását. A MySQL rugalmas kiszolgálóval való kapcsolódáshoz le kell töltenie a hitelesítésszolgáltató ellenőrzéséhez szükséges [nyilvános SSL-tanúsítványt](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) . Ha többet szeretne megtudni a titkosított kapcsolatokkal való csatlakozásról vagy az SSL letiltásáról, tekintse meg a [csatlakozás Azure Database for MySQL rugalmas kiszolgáló és a titkosított kapcsolatok](how-to-connect-tls-ssl.md) dokumentációja című témakört.

Az alábbi példa bemutatja, hogyan csatlakozhat a rugalmas kiszolgálóhoz a MySQL parancssori felület használatával. Ha már nincs telepítve, először telepítenie kell a MySQL-parancssort. Le fogja tölteni az SSL-kapcsolatokhoz szükséges DigiCertGlobalRootCA-tanúsítványt. A TLS/SSL-tanúsítvány ellenőrzésének érvényesítéséhez használja a--SSL-Mode = REQUIREd kapcsolati sztring beállítást. Adja át a helyi tanúsítványfájl elérési útját a--SSL-CA paraméternek. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Ha a rugalmas kiszolgálót **nyilvános hozzáférés** használatával állította be, akkor a [Azure Cloud Shell](https://shell.azure.com/bash) használatával is csatlakozhat a rugalmas kiszolgálóhoz az előre telepített MySQL-ügyféllel az alábbi ábrán látható módon:

Ahhoz, hogy a Azure Cloud Shellt a rugalmas kiszolgálóhoz való kapcsolódáshoz használhassa, engedélyeznie kell a hálózati hozzáférést a Azure Cloud Shell a rugalmas kiszolgálóra. Ennek eléréséhez nyissa meg a **hálózatkezelés** panelt Azure Portal a MySQL-hez készült rugalmas kiszolgálónál, és jelölje be a **tűzfal** szakaszban a "nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatáshoz az Azure-ban erre a kiszolgálóra" lehetőséget, ahogy az alábbi képernyőképen is látható, és kattintson a Mentés gombra a beállítás megőrzéséhez.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Képernyőkép, amely bemutatja, hogyan engedélyezhető Azure Cloud Shell hozzáférés a MySQL rugalmas kiszolgálóhoz a nyilvános hozzáférési hálózati konfigurációhoz.":::
 
 
> [!NOTE]
> Annak ellenőrzése, **hogy a nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatáson belül erre a kiszolgálóra** csak fejlesztési vagy tesztelési célokra használható. Úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-szolgáltatásokhoz vagy-eszközökhöz lefoglalt IP-címekről érkező kapcsolatokat, beleértve a más ügyfelek előfizetéseit is.

Kattintson a **kipróbálás** gombra a Azure Cloud Shell elindításához és az alábbi parancsok használatával a rugalmas kiszolgálóhoz való kapcsolódáshoz. A parancsban használja a kiszolgáló nevét, felhasználónevét és jelszavát. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Ha Azure Cloud Shell használatával csatlakozik a rugalmas kiszolgálóhoz, a--SSL = true paramétert kell használnia, és nem--SSL-Mode = REQUIREd.
> Az elsődleges ok az, Azure Cloud Shell előre telepített mysql.exe-ügyfelet tartalmaz az MariaDB Distribution szolgáltatásból, amely a--SSL paramétert igényli, míg az Oracle-alapú terjesztéshez szükséges MySQL-ügyfél a--SSL-Mode paramétert igényli.

Ha a következő hibaüzenet jelenik meg, amikor a fenti parancsot követve csatlakozik a rugalmas kiszolgálóhoz, a korábban említett "nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatáshoz az Azure-ban erre a kiszolgálóra" beállítást nem sikerült beállítani, vagy a beállítás nincs mentve. Próbálkozzon újra a tűzfal beállításával, és próbálkozzon újra.

HIBA 2002 (HY000): nem lehet csatlakozni a MySQL-kiszolgálóhoz <servername> (115)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket:

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja a `az mysql server delete` parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
> [Csatlakozási és lekérdezés az Azure CLI](connect-azure-cli.md) 
>  -vel [Csatlakozás Azure Database for MySQL-rugalmas kiszolgálóhoz titkosított kapcsolattal](how-to-connect-tls-ssl.md) 
>  [PHP-alapú (Laravel-) Webalkalmazás létrehozása MySQL-](tutorial-php-database-app.md) sel
