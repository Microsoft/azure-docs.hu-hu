---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure CLI – Azure Database for MySQL – Rugalmas kiszolgáló'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy rugalmas Azure Database for MySQL kiszolgáló létrehozására egy Azure-erőforráscsoportban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770236"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Rövid útmutató: Rugalmas Azure Database for MySQL létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan [](https://shell.azure.com) hozhat létre Azure Cloud Shell rugalmas Azure Database for MySQL öt perc alatt az Azure CLI-parancsokkal. [](/cli/azure/get-started-with-azure-cli) Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőlapot megnyithatja Cloud Shell böngészőlapon is, ha megnyitja a következőt: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be Cloud Shell, majd az **Enter** billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az login paranccsal jelentkezzen be [a fiókjába.](/cli/azure/reference-index#az_login) Jegyezze fel **az id** tulajdonságot, amely **az** Azure-fiók előfizetés-azonosítójára vonatkozik.

```azurecli-interactive
az login
```

Válassza ki a fiókjában az adott előfizetést [az az account set paranccsal.](/cli/azure/account#az_account_set) Jegyezze fel  az az **login** kimenet azonosítóértékét,  amely a subscription argumentum értékeként lesz használva a parancsban. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Rugalmas kiszolgáló létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) az paranccsal, majd hozza létre a rugalmas `az group create` MySQL-kiszolgálót ebben az erőforráscsoportban. Egyedi nevet adjon meg. A következő példában létrehozunk egy `eastus2` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Hozzon létre egy rugalmas kiszolgálót az `az mysql flexible-server create` paranccsal. Egy kiszolgáló több adatbázist tartalmazhat. A következő parancs létrehoz egy kiszolgálót a szolgáltatás alapértelmezett értékeinek és értékeinek használatával az Azure CLI helyi [környezetében:](/cli/azure/local-context) 

```azurecli-interactive
az mysql flexible-server create
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik: 
- Automatikusan létrehozott kiszolgálónév, rendszergazdai felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs megadva a helyi környezetben), és ugyanazon a helyen, ahol az erőforráscsoport található 
- Szolgáltatás alapértelmezései a fennmaradó kiszolgálókonfigurációkhoz: számítási szint (burstable), számítási méret/termékváltozat (B1MS), biztonsági másolatok megőrzési időtartama (7 nap) és MySQL-verzió (5.7)
- Az alapértelmezett csatlakozási módszer a privát hozzáférés (VNet-integráció) egy automatikusan létrehozott virtuális hálózattal és alhálózattal

> [!NOTE] 
> A kapcsolati módszer a kiszolgáló létrehozása után nem módosítható. Ha például a létrehozás során a Privát hozzáférés *(VNet-integráció)* beállítást választotta, akkor a létrehozás után nem lehet nyilvános hozzáférésre *(engedélyezett IP-címekre)* módosítani. Javasoljuk, hogy hozzon létre egy privát hozzáféréssel bíró kiszolgálót a kiszolgáló VNet-integrációval való biztonságos elérése érdekében. A privát hozzáféréssel kapcsolatos további információkért olvassa el [a fogalmakat.](./concepts-networking.md)

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure [CLI](/cli/azure/mysql/flexible-server) referenciadokumentációját a konfigurálható CLI-paraméterek teljes listájáért. 

Alább látható néhány példa a kimenetre: 

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

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure [CLI](/cli/azure/mysql/flexible-server) referenciadokumentációját a konfigurálható CLI-paraméterek teljes listájáért. 

## <a name="create-a-database"></a>Adatbázis létrehozása
Futtassa a következő parancsot  egy új adatbázis létrehozásához, ha még nem hozott létre adatbázist.

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Csatlakozás és a kapcsolat tesztelése az Azure CLI használatával

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi a mysql-kiszolgálóhoz való csatlakozást az Azure ```az mysql flexible-server connect``` CLI-paranccsal. Ezzel a paranccsal tesztelheti az adatbázis-kiszolgálóval való kapcsolatot, gyorsindító adatbázist hozhat létre, és lekérdezéseket futtathat közvetlenül a kiszolgálón anélkül, hogy telepítenie mysql.exe vagy a MySQL Workbench alkalmazást.  A parancsot interaktív módban is futtathatja több lekérdezés futtatásához.

Futtassa a következő szkriptet a fejlesztési környezetből származó adatbázissal való kapcsolat teszteléséhez és ellenőrzéséhez.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Példa**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
A sikeres csatlakozáshoz a következő kimenetnek kell látsza:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Ha a kapcsolat sikertelen volt, próbálkozzon az alábbi megoldásokkal:
- Ellenőrizze, hogy a 3306-os port nyitva van-e az ügyfélszámítógépen.
- ha a kiszolgáló-rendszergazda felhasználóneve és jelszava helyes
- ha konfigurált tűzfalszabályt az ügyfélszámítógéphez
- Ha privát hozzáféréssel konfigurálta a kiszolgálót a virtuális hálózatban, győződjön meg arról, hogy az ügyfélszámítógép ugyanabban a virtuális hálózatban van.

Egyetlen lekérdezés a argumentum használatával való végrehajtásához futtassa a következő ```--querytext``` ```-q``` parancsot: .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Példa**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
A parancs használatával kapcsolatos ```az mysql flexible-server connect``` további információkért tekintse meg a csatlakozás [és lekérdezés dokumentációját.](connect-azure-cli.md)

## <a name="connect-using-mysql-command-line-client"></a>Csatlakozás a mysql parancssori ügyféllel

Ha a rugalmas kiszolgálót privát hozzáféréssel (VNet-integrációval) hozta létre, akkor a kiszolgálóval azonos virtuális hálózaton belüli erőforrásból kell csatlakoznia a kiszolgálóhoz. Létrehozhat egy virtuális gépet, és hozzáadhatja a rugalmas kiszolgálóval létrehozott virtuális hálózathoz. További információért tekintse meg a [privát hozzáférés konfigurálásával](how-to-manage-virtual-network-portal.md) kapcsolatos dokumentációt.

Ha nyilvános hozzáféréssel hozta létre a rugalmas kiszolgálót (engedélyezett IP-címek), hozzáadhatja a helyi IP-címét a kiszolgáló tűzfalszabályainak listájához. Részletes [útmutatásért](how-to-manage-firewall-portal.md) tekintse meg a tűzfalszabályok létrehozása és kezelése dokumentációját.

A helyi [ környezetbőlmysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) a kiszolgálóhoz való csatlakozáshoz használhatja amysql.exevagy a [MySQL Workbench](./connect-workbench.md) megoldást. Azure Database for MySQL rugalmas kiszolgáló támogatja az ügyfélalkalmazások csatlakoztatását a MySQL szolgáltatáshoz Transport Layer Security (TLS) használatával, korábbi nevén SSL (SSL) használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely titkosított hálózati kapcsolatokat biztosít az adatbázis-kiszolgáló és az ügyfélalkalmazások között, így megfelel a megfelelőségi követelményeknek. A rugalmas MySQL-kiszolgálóhoz való csatlakozáshoz [](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) le kell töltenie a nyilvános SSL-tanúsítványt a hitelesítésszolgáltató ellenőrzéséhez. Ha többet szeretne megtudni a titkosított kapcsolatokkal való csatlakozásról vagy az SSL letiltásról, tekintse meg a Csatlakozás a Azure Database for MySQL – Rugalmas kiszolgáló [titkosított](how-to-connect-tls-ssl.md) kapcsolatokkal dokumentációt.

Az alábbi példa bemutatja, hogyan csatlakozhat a rugalmas kiszolgálóhoz a mysql parancssori felület használatával. Először telepítenie kell a mysql parancssort, ha még nincs telepítve. Le fogja tölteni az SSL-kapcsolatokhoz szükséges DigiCertGlobalRootCA-tanúsítványt. Használja az --ssl-mode=REQUIRED kapcsolati sztring beállítást a TLS/SSL-tanúsítvány-ellenőrzés kényszerítéhez. Adja át a helyi tanúsítványfájl elérési útját az --ssl-ca paraméternek. Cserélje le az értékeket a kiszolgáló tényleges nevére és jelszavára.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Ha nyilvános hozzáféréssel telepítette a rugalmas kiszolgálót, az Azure Cloud Shell használatával is csatlakozhat a rugalmas kiszolgálóhoz az előre telepített mysql-ügyfél használatával, az alábbi módon: [](https://shell.azure.com/bash)

Ahhoz, hogy Azure Cloud Shell a rugalmas kiszolgálóhoz való csatlakozáshoz, engedélyeznie kell a hálózati hozzáférést a Azure Cloud Shell a rugalmas kiszolgálóhoz. Ehhez a rugalmas MySQL-kiszolgáló Azure Portal hálózat paneljére kattintva jelölje be a  Tűzfal szakasz alatti "Nyilvános hozzáférés engedélyezése bármely Azure-szolgáltatásból ezen a kiszolgálón" jelölőnégyzetet az alábbi képernyőképen látható módon, majd kattintson a Mentés gombra a beállítás megőrzéséhez. 

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="A rugalmas MySQL-Azure Cloud Shell nyilvános hozzáférésű hálózati konfigurációhoz való hozzáférését bemutató képernyőkép.":::
 
 
> [!NOTE]
> A Nyilvános **hozzáférés engedélyezése bármely Azure-szolgáltatásból** ezen a kiszolgálón lehetőség be van vava, csak fejlesztési vagy tesztelési célokra használható. Úgy konfigurálja a tűzfalat, hogy engedélyezze a kapcsolatokat bármely Azure-szolgáltatás vagy -eszköz számára lefoglalt IP-címekről, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is.

Kattintson a **Try it (Próbálja** ki) elemre a Azure Cloud Shell, és az alábbi parancsokkal csatlakozzon a rugalmas kiszolgálóhoz. A parancsban használja a kiszolgálónevet, a felhasználónevet és a jelszót. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Amikor a rugalmas kiszolgálóhoz a Azure Cloud Shell csatlakozik, az --ssl=true paramétert kell használnia, és nem az --ssl-mode=REQUIRED paramétert.
> Az elsődleges ok az Azure Cloud Shell hogy a MariaDB-disztribúció előre telepített mysql.exe-ügyfelet használ, amely --ssl paramétert igényel, míg az Oracle disztribúcióból származó mysql-ügyfélhez --ssl-mode paraméter szükséges.

Ha az alábbi hibaüzenet jelenik meg, amikor korábban a parancsot követte a rugalmas kiszolgálóhoz való csatlakozáskor, kihagyta a tűzfalszabály beállítását a korábban említett "Bármely Azure-szolgáltatásból ezen a kiszolgálón elérhető bármely Azure-szolgáltatásból" beállítással, vagy a beállítás nincs mentve. Próbálkozzon újra a tűzfal beállításával, majd próbálkozzon újra.

2002-es HIBA (HY000): Nem lehet csatlakozni a MySQL-kiszolgálóhoz a <servername> következőn: (115)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket:

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtassa a `az mysql server delete` parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
> [Csatlakozás és lekérdezés az Azure CLI használatával](connect-azure-cli.md) 
>  [Csatlakozás Azure Database for MySQL – Rugalmas kiszolgáló titkosított kapcsolatokkal](how-to-connect-tls-ssl.md) 
>  [PHP- (Laravel-) webalkalmazás összeállítása a MySQL-lel](tutorial-php-database-app.md)
