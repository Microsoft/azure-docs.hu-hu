---
title: 'Oktatóanyag: Azure Database for PostgreSQL rugalmas kiszolgáló és Azure App Service Webalkalmazás létrehozása ugyanabban a virtuális hálózatban'
description: Útmutató a Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozásához a webalkalmazással egy virtuális hálózaton
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9af90ca0b6b80ffece5ccd7d919c1d93e210c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657586"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Oktatóanyag: Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása a App Services webalkalmazással a Virtual Network szolgáltatásban

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure App Service webalkalmazást Azure Database for PostgreSQL-rugalmas kiszolgálóval (előzetes verzió) egy [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md)belül.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
>[!div class="checklist"]
> * PostgreSQL rugalmas kiszolgáló létrehozása virtuális hálózaton
> * Alhálózat létrehozása App Service delegálásához
> * Webalkalmazás létrehozása
> * A webalkalmazás hozzáadása a virtuális hálózathoz
> * Kapcsolódás a postgres a webalkalmazásból 

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/authenticate-azure-cli) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. **Az előfizetés-azonosító tulajdonságot** az előfizetés-azonosító helyőrzőbe írja be az előfizetéshez tartozó **bejelentkezési** kimenetből.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>PostgreSQL rugalmas kiszolgáló létrehozása új virtuális hálózaton

Hozzon létre egy privát rugalmas kiszolgálót egy virtuális hálózaton (VNET) a következő parancs használatával:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot.
- Ha nincs megadva, a kiszolgáló nevét hozza létre.
- Hozzon létre egy új virtuális hálózatot az új postgreSQL-kiszolgálóhoz. Jegyezze fel a kiszolgálóhoz létrehozott virtuális hálózat nevét és alhálózatának nevét, mivel a webalkalmazást ugyanahhoz a virtuális hálózathoz kell felvennie.
- Rendszergazdai felhasználónevet és jelszót hoz létre a kiszolgálóhoz, ha nincs megadva.
- Létrehoz egy **postgres** nevű üres adatbázist.

> [!NOTE]
> - Jegyezze fel a jelszót, amelyet a rendszer akkor hoz majd meg, ha nincs megadva. Ha elfelejti a jelszót, a parancs használatával kell alaphelyzetbe állítani a jelszót ``` az postgres flexible-server update```
> - Ha nem használ App Service Environment, engedélyeznie kell az Azure IP-címekről való hozzáférés engedélyezését ezen parancs használatával. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```

## <a name="create-subnet-for-app-service-endpoint"></a>Alhálózat létrehozása App Service végponthoz
Most szükség van olyan alhálózatra, amely App Service webalkalmazás-végpontra van delegálva. A következő parancs futtatásával hozzon létre egy új alhálózatot ugyanabban a virtuális hálózatban, amelyben az adatbázis-kiszolgáló létrejött. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Jegyezze fel a virtuális hálózat nevét és az alhálózat nevét a parancs után, ahogy azt a létrehozás után VNET-integrációs szabály hozzáadására lenne szükség a webalkalmazáshoz. 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
Ebben a szakaszban az App Host alkalmazást App Service alkalmazásban hozza létre, kapcsolja össze az alkalmazást a postgres-adatbázissal, majd telepítse a kódot a gazdagépre. Győződjön meg arról, hogy az alkalmazás kódjának tárház gyökerében van a terminálban. Megjegyzés: az alapszintű csomag nem támogatja a VNET-integrációt. Használja a standard vagy a prémium szintűt. 

Hozzon létre egy App Service alkalmazást (a gazdagép folyamatát) az az WebApp up paranccsal

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - A--Location argumentum esetében ugyanazt a helyet használja, mint az előző szakaszban található adatbázishoz.
> - Cserélje le az <app-Name>t egyedi névre az összes Azure-ban (a kiszolgálói végpont https:// \<app-name> . azurewebsites.net). <alkalmazás-neve> engedélyezett karakterek: A-Z, 0-9 és-. Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot. (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha nem létezik, hozza létre a App Service alkalmazást.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Töltse fel az adattárat a ZIP-telepítéssel a Build Automation használatával.

## <a name="add-the-web-app-to-the-virtual-network"></a>A webalkalmazás hozzáadása a virtuális hálózathoz
Az **az WebApp vnet-Integration** paranccsal adhat hozzá regionális virtuális hálózati integrációt egy webapphoz. Cserélje le <vnet> és <alhálózat neve> a virtuális hálózatra és az alhálózat nevére, amelyet a rugalmas kiszolgáló használ.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Környezeti változók konfigurálása az adatbázishoz való kapcsolódáshoz
Ha a kód most már telepítve van a App Servicere, a következő lépés az alkalmazás összekötése az Azure-beli rugalmas kiszolgálóval. Az alkalmazás kódja számos környezeti változóban várja az adatbázis-információk megkeresését. A környezeti változók App Serviceban történő beállításához a SET paranccsal hozhatja létre az "Alkalmazásbeállítások" kifejezést ```az webapp config appsettings``` .

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Cserélje ```postgres-server-name``` le ```username``` ```password``` a parancsot az újonnan létrehozott rugalmas kiszolgáló parancsra.
- Cserélje le a <username> és a <password> kapcsolót arra a hitelesítő adatokra, amelyet a parancs is létrehoz.
- Az erőforráscsoport és az alkalmazás neve a. Azure/config fájl gyorsítótárazott értékeiből származik.
- A parancs a (z ```DBHOST``` ),, ```DBNAME``` ```DBUSER``` és ```DBPASS``` . nevű beállításokat hozza létre. Ha az alkalmazás kódja más nevet használ az adatbázis-adatokhoz, akkor ezeket a neveket használja a kódban említett beállítások alapján.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az oktatóanyagban létrehozott összes erőforrást az alábbi parancs használatával. Ez a parancs törli az erőforráscsoport összes erőforrását.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
