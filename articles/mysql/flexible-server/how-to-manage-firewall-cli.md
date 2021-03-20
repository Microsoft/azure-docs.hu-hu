---
title: Tűzfalszabályok kezelése – Azure CLI-Azure Database for MySQL – rugalmas kiszolgáló
description: Tűzfalszabályok létrehozása és kezelése Azure Database for MySQL-rugalmas kiszolgálóhoz az Azure CLI parancssorral.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84fdd3045d5a1d44ff611134d88fc9793ee203de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545072"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

A rugalmas Azure Database for MySQL-kiszolgáló két, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

- Nyilvános hozzáférés (engedélyezett IP-címek)
- Privát hozzáférés (VNet-integráció)

Ebben a cikkben a MySQL-kiszolgáló és a **nyilvános hozzáférés (engedélyezett IP-címek)** Azure CLI-vel való létrehozására összpontosítunk, és áttekintést nyújt az Azure CLI-parancsokról, amelyekkel a kiszolgáló létrehozása után létrehozhat, frissíthet, törölhet, listázhat és jeleníthet meg tűzfal-szabályokat. A *nyilvános hozzáférés (engedélyezett IP-címek)* esetében a MySQL-kiszolgálóval létesített kapcsolatok csak az engedélyezett IP-címekre korlátozódnak. Az ügyfél IP-címeit engedélyezni kell a tűzfalszabályok esetében. További információt a [nyilvános hozzáférés (engedélyezett IP-címek)](./concepts-networking.md#public-access-allowed-ip-addresses)című témakörben talál. A tűzfalszabályok meghatározhatók a kiszolgáló létrehozásakor (ajánlott), de később is hozzáadhatók.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngésző lapon is megnyithatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az [login](/cli/azure/reference-index#az-login) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account#az-account-set) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Tűzfalszabály létrehozása rugalmas kiszolgáló létrehozásakor az Azure CLI használatával

A `az mysql flexible-server --public access` paranccsal hozhatja létre a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* , és konfigurálhatja a tűzfalszabályok használatát a rugalmas kiszolgáló létrehozása során. A **--Public-Access** kapcsolóval megadhatja azokat az engedélyezett IP-címeket, amelyek képesek lesznek csatlakozni a kiszolgálóhoz. Megadhat egy vagy több IP-címet is, amelyet az IP-címek engedélyezett listája tartalmaz. Az IP-címtartomány kötőjelet kell elválasztani, és nem tartalmaz szóközt. Az alábbi példában látható módon létrehozhat egy rugalmas kiszolgálót a CLI használatával.

A konfigurálható CLI-paraméterek teljes listájáért tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) . Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és adja hozzá az ügyfél IP-címét a kiszolgálóhoz való hozzáféréshez
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és adja hozzá az IP-cím tartományát, hogy hozzáférhessen a kiszolgálóhoz

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és engedélyezze az Azure IP-címekről származó alkalmazások számára a rugalmas kiszolgálóhoz való kapcsolódást
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-szolgáltatások és-erőforrások nyilvános elérését az Azure-ban ezen a kiszolgálón, beleértve a többi ügyfél előfizetésével létesített kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
    >
- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és engedélyezze az összes IP-címet
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > A fenti parancs egy tűzfalszabály létrehozásához a Start IP Address = 0.0.0.0, a záró IP-cím = 255.255.255.255, és a rendszer nem blokkolja az IP-címeket. Az interneten található bármely gazdagép hozzáférhet ehhez a kiszolgálóhoz. Erősen ajánlott ezt a szabályt csak ideiglenesen használni, csak a bizalmas adatokat nem tartalmazó tesztelési kiszolgálókon.

- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel és IP-cím nélkül
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > nem javasoljuk, hogy tűzfal-szabályok nélkül hozzon létre egy kiszolgálót. Ha nem ad hozzá tűzfalszabályok-szabályokat, akkor egyetlen ügyfél sem tud csatlakozni a kiszolgálóhoz.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Tűzfalszabály létrehozása és kezelése a kiszolgáló létrehozása után
Az az **MySQL flexibilis-Server Firewall-Rule** parancs az Azure CLI-ből a tűzfalszabályok létrehozásához, törléséhez, listázásához, megjelenítéséhez és frissítéséhez használható.

Parancsok
- **Létrehozás**: rugalmas kiszolgálói tűzfalszabály létrehozása.
- **lista**: a rugalmas kiszolgálói tűzfalszabályok listázása.
- **frissítés**: egy rugalmas kiszolgálói tűzfalszabály frissítése.
- **Megjelenítés**: egy rugalmas kiszolgálói tűzfalszabály részleteit jeleníti meg.
- **Törlés**: egy rugalmas kiszolgálói tűzfalszabály törlése.

A konfigurálható CLI-paraméterek teljes listájáért tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) . Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

### <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása
A `az mysql flexible-server firewall-rule create` parancs használatával hozzon létre új tűzfalszabály-szabályt a kiszolgálón.
Az IP-címtartomány elérésének engedélyezéséhez adja meg az IP-címet a kezdő IP-cím és a záró IP-cím alapján, ahogy az ebben a példában is látható.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Egyetlen IP-cím elérésének engedélyezéséhez csak egyetlen IP-címet adjon meg, ahogy az ebben a példában is látható.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Ha engedélyezni szeretné az Azure IP-címekről származó alkalmazások számára a rugalmas kiszolgálóhoz való kapcsolódást, adja meg a 0.0.0.0 IP-címet a kezdő IP-címként, ahogy az ebben a példában is látható.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-szolgáltatások és-erőforrások nyilvános elérését az Azure-ban ezen a kiszolgálón, beleértve a többi ügyfél előfizetésével létesített kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Ha a művelet sikeres, az egyes létrehozási parancsok kimenete a létrehozott tűzfalszabály részletes adatait jeleníti meg JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

### <a name="list-firewall-rules"></a>Tűzfalszabályok listázása 
A (z `az mysql flexible-server firewall-rule list` ) parancs használatával listázhatja a kiszolgálón a meglévő kiszolgálói tűzfalszabályok listáját. Figyelje meg, hogy a kiszolgálónév attribútum meg van adva a **--Name** kapcsolóban.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
A kimenet a (alapértelmezés szerint) JSON formátumban listázza a szabályokat. A--output Table * * kapcsoló használatával az eredményeket olvashatóbb táblázatos formában jelenítheti meg.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Tűzfalszabály frissítése
A `az mysql flexible-server firewall-rule update` parancs használatával frissítsen egy meglévő tűzfalszabály a kiszolgálón. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissíteni kívánt kezdő IP-cím és záró IP-cím attribútumokat.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
A művelet sikeressége után a parancs kimenete tartalmazza a módosított tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt a frissítési parancs hozza létre.

### <a name="show-firewall-rule-details"></a>Tűzfalszabály részleteinek megjelenítése
A `az mysql flexible-server firewall-rule show` parancs használatával jelenítse meg a meglévő tűzfalszabály részleteit a kiszolgálóról. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
A művelet sikeressége után a parancs kimenete tartalmazza a megadott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Ha hiba lép fel, a kimenetben a hibaüzenet szövege jelenik meg.

### <a name="delete-a-firewall-rule"></a>Tűzfalszabály törlése
A `az mysql flexible-server firewall-rule delete` paranccsal törölhet egy meglévő tűzfalszabályet a-kiszolgálóról. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
A művelet sikere esetén nincs kimenet. Hiba esetén a hibaüzenet szövege jelenik meg.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./how-to-manage-firewall-portal.md).