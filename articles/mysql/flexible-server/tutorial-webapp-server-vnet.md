---
title: 'Oktatóanyag: Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) és Azure App Service Webalkalmazás létrehozása ugyanazon a virtuális hálózaton'
description: Gyors útmutató Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) létrehozásához a Web App használatával egy virtuális hálózaton
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a7b673dc8dfeb2ebf86aec5b7449df91c2ffd635
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534056"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Oktatóanyag: Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) létrehozása App Services webalkalmazással a Virtual Network szolgáltatásban

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure App Service webalkalmazást a MySQL rugalmas kiszolgálóval (előzetes verzió) egy [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md)belül.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/reference-index#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. **Az előfizetés-azonosító tulajdonságot** az előfizetés-azonosító helyőrzőbe írja be az előfizetéshez tartozó **bejelentkezési** kimenetből.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló létrehozása

Hozzon létre egy privát rugalmas kiszolgálót egy virtuális hálózaton (VNET) a következő parancs használatával:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot.
- Ha nincs megadva, a kiszolgáló nevét hozza létre.
- Hozzon létre egy új virtuális hálózatot az új MySQL-kiszolgálóhoz. Jegyezze fel a kiszolgálóhoz létrehozott virtuális hálózat nevét és alhálózatának nevét, mivel a webalkalmazást ugyanahhoz a virtuális hálózathoz kell felvennie.
- Rendszergazdai felhasználónevet és jelszót hoz létre a kiszolgálóhoz, ha nincs megadva.
- Létrehoz egy **flexibleserverdb** nevű üres adatbázist.

> [!NOTE]
> Jegyezze fel a jelszót, amelyet a rendszer akkor hoz majd meg, ha nincs megadva. Ha elfelejti a jelszót, a parancs használatával kell alaphelyzetbe állítani a jelszót ``` az mysql flexible-server update```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Ebben a szakaszban a App Service alkalmazásban hozza létre az App Host alkalmazást, és az alkalmazást a MySQL-adatbázishoz kapcsolja. Győződjön meg arról, hogy az alkalmazás kódjának tárház gyökerében van a terminálban.

Hozzon létre egy App Service alkalmazást (a gazdagép folyamatát) az az WebApp up paranccsal

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - A--Location argumentum esetében ugyanazt a helyet használja, mint az előző szakaszban található adatbázishoz.
> - Cserélje le az _&lt; app-Name>t_ egyedi névre az összes Azure-ban (a kiszolgálói végpont https:// \<app-name> . azurewebsites.net). <alkalmazás-neve> engedélyezett karakterek: A-Z, 0-9 és-. Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot. (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha nem létezik, hozza létre a App Service tervet az ```testappserviceplan``` alapszintű díjszabásban (B1). a--Plan és a--SKU megadása nem kötelező.
- Ha nem létezik, hozza létre a App Service alkalmazást.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Töltse fel az adattárat a ZIP-telepítéssel a Build Automation használatával.

## <a name="add-the-web-app-to-the-virtual-network"></a>A webalkalmazás hozzáadása a virtuális hálózathoz

Az **az WebApp vnet-Integration** paranccsal adhat hozzá regionális virtuális hálózati integrációt egy webapphoz. Cserélje le a _&lt; vnet-Name>_ és az _&lt; alhálózati név_ értékét a rugalmas kiszolgáló által használt virtuális hálózatra és alhálózatra.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Környezeti változók konfigurálása az adatbázishoz való kapcsolódáshoz

Ha a kód most már telepítve van a App Servicere, a következő lépés az alkalmazás összekötése az Azure-beli rugalmas kiszolgálóval. Az alkalmazás kódja számos környezeti változóban várja az adatbázis-információk megkeresését. A környezeti változók App Serviceban történő beállításához a SET paranccsal hozhatja létre az "Alkalmazásbeállítások" kifejezést ```az webapp config appsettings``` .

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Cserélje le a _&lt; MySQL-Server-Name>_ , a _&lt; username>_ és a _&lt; Password>_ nevet az újonnan létrehozott rugalmas kiszolgálói parancshoz.
- Cserélje le a _&lt; Felhasználónév>_ és a _&lt; jelszó>_ azokat a hitelesítő adatokat, amelyeket a parancs is generált.
- Az erőforráscsoport és az alkalmazás neve a. Azure/config fájl gyorsítótárazott értékeiből származik.
- A parancs a DBHOST, a DBNAME, a DBUSER és a DBPASS nevű beállításokat hozza létre. Ha az alkalmazás kódja más nevet használ az adatbázis-adatokhoz, akkor ezeket a neveket használja a kódban említett beállítások alapján.

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

Törölje az oktatóanyagban létrehozott összes erőforrást az alábbi parancs használatával. Ez a parancs törli az erőforráscsoport összes erőforrását.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)