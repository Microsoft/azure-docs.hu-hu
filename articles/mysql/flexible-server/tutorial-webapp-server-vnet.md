---
title: 'Oktatóanyag: Rugalmas Azure Database for MySQL létrehozása (előzetes verzió) és Azure App Service webalkalmazás létrehozása ugyanabban a virtuális hálózatban'
description: Rövid útmutató rugalmas Azure Database for MySQL (előzetes verzió) webalkalmazással való létrehozásához egy virtuális hálózaton
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770020"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Oktatóanyag: Rugalmas Azure Database for MySQL létrehozása (előzetes verzió) App Services webalkalmazással a virtuális hálózaton

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure App Service webalkalmazást a rugalmas MySQL-kiszolgálóval (előzetes verzió) egy [virtuális hálózaton belül.](../../virtual-network/virtual-networks-overview.md)

Ezen oktatóanyag segítségével megtanulhatja a következőket:
>[!div class="checklist"]
> * Rugalmas MySQL-kiszolgáló létrehozása virtuális hálózaton
> * Hozzon létre egy alhálózatot, amely a App Service
> * Webalkalmazás létrehozása
> * A webalkalmazás hozzáadása a virtuális hálózathoz
> * Csatlakozás a Postgreshez a webalkalmazásból 

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az login paranccsal jelentkezzen be [a fiókjába.](/cli/azure/reference-index#az_login) Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Az **előfizetése az** **login** output tulajdonságát helyettesítse be az előfizetés azonosítójának helyőrzőjére.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Rugalmas Azure Database for MySQL létrehozása

Hozzon létre egy privát, rugalmas kiszolgálót egy virtuális hálózaton (VNET) a következő paranccsal:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Másolja ki a kapcsolati sztringet és az újonnan létrehozott virtuális hálózat nevét. Ez a parancs a következő műveleteket végzi el, amelyek eltarthatnak néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot.
- Létrehoz egy kiszolgálónevet, ha nincs megtéve.
- Hozzon létre egy új virtuális hálózatot az új MySQL-kiszolgálóhoz. Jegyezze fel a kiszolgálóhoz létrehozott virtuális hálózat és alhálózat nevét, mivel a webalkalmazást ugyanerhez a virtuális hálózathoz kell hozzáadnia.
- Rendszergazdai felhasználónevet, jelszót hoz létre a kiszolgálóhoz, ha nincs megtéve.
- Létrehoz egy **flexibleserverdb** nevű üres adatbázist

> [!NOTE]
> Jegyezze fel a jelszót, amely akkor lesz létrehozva, ha nincs megtéve. Ha elfelejti a jelszót, az paranccsal alaphelyzetbe kell ``` az mysql flexible-server update``` állítania a jelszót

## <a name="create-subnet-for-app-service-endpoint"></a>Alhálózat létrehozása App Service végponthoz
Most olyan alhálózatra van szükségünk, amely delegálva van App Service webalkalmazás végpontjára. A következő parancs futtatásával hozzon létre egy új alhálózatot ugyanabban a virtuális hálózatban, mint az adatbázis-kiszolgálót. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Jegyezze fel a virtuális hálózat nevét és az alhálózat nevét a parancs után, mivel a parancsnak szüksége lesz rá a webalkalmazás VNET-integrációs szabályának hozzáadásához annak létrehozása után. 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Ebben a szakaszban létrehoz egy alkalmazásgazdát az App Service, és csatlakoztatja az alkalmazást a MySQL-adatbázishoz. Győződjön meg arról, hogy az alkalmazáskód tárházgyökerében van a terminálban.

Hozzon létre App Service alkalmazást (a gazdafolyamatot) az az webapp up paranccsal

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - A --location argumentumhoz használja ugyanazt a helyet, mint az előző szakaszban az adatbázishoz.
> - Cserélje _&lt; le az app-name>_ egy egyedi névre az összes Azure-ban (a kiszolgálóvégpont a \<app-name> .https://.azurewebsites.net). Az alkalmazásnév <karakterek engedélyezettek> A–Z, 0–9 és -. Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.
> - App Service alapszintű csomag nem támogatja a VNET-integrációt. Használja a Standard vagy a Prémium változatot. 

Ez a parancs a következő műveleteket végzi el, amelyek eltarthatnak néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot. (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha még App Service, hozza létre a csomagcsomagot az Alapszintű tarifacsomagban ```testappserviceplan``` (B1). A --plan és --sku megadása nem kötelező.
- Ha még App Service, hozza létre a App Service alkalmazást.
- Engedélyezze az alapértelmezett naplózást az alkalmazáshoz, ha még nincs engedélyezve.
- Töltse fel az adattárat ZIP-alapú üzembe helyezéssel, engedélyezett buildautomatizálással.

## <a name="add-the-web-app-to-the-virtual-network"></a>A webalkalmazás hozzáadása a virtuális hálózathoz

Az **az webapp vnet-integration paranccsal** regionális virtuális hálózati integrációt adhat hozzá egy webalkalmazáshoz. Cserélje _&lt; le a vnet-name>_ és az _&lt; alhálózat nevét_ a rugalmas kiszolgáló által használt virtuális hálózat és alhálózat nevére.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Környezeti változók konfigurálása az adatbázis csatlakoztatásához

Most, hogy a kódot üzembe App Service, a következő lépés az alkalmazás csatlakoztatása a rugalmas azure-beli kiszolgálóhoz. Az alkalmazáskód arra számít, hogy adatbázis-információkat fog találni számos környezeti változóban. A környezeti változók App Service a set paranccsal hozhatja létre az ```az webapp config appsettings``` "alkalmazásbeállításokat".

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Cserélje le a _&lt; mysql-server-name>,_ _&lt; a felhasználónév>_ és a jelszó _&lt;>_ az újonnan létrehozott rugalmas kiszolgáló parancshoz.
- Cserélje _&lt; le a username>_ és a password _&lt;>_ helyére a parancs által is létrehozott hitelesítő adatokat.
- Az erőforráscsoport és az alkalmazás neve az .azure/config fájl gyorsítótárazott értékeiből lesz kirajzolva.
- A parancs létrehozza a DBHOST, DBNAME, DBUSER és DBPASS nevű beállításokat. Ha az alkalmazáskód más nevet használ az adatbázis adataihoz, használja ezeket a neveket az alkalmazásbeállításokhoz a kódban említettek szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott összes erőforrást az alábbi paranccsal lehet megtisztítani. Ez a parancs törli az erőforráscsoport összes erőforrását.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése a Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
