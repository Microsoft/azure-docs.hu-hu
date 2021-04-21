---
title: Tűzfalszabályok kezelése – Azure CLI – Azure Database for MySQL – Rugalmas kiszolgáló
description: Tűzfalszabályok létrehozása és kezelése a rugalmas Azure Database for MySQL Azure CLI parancssor használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0692e7e7452fef9577414e9aa5340d933f50b30e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776950"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Rugalmas kiszolgálói Azure Database for MySQL létrehozása és kezelése – Rugalmas kiszolgálói tűzfalszabályok az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

A rugalmas Azure Database for MySQL-kiszolgáló két, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

- Nyilvános hozzáférés (engedélyezett IP-címek)
- Privát hozzáférés (VNet-integráció)

Ebben a cikkben a nyilvános hozzáféréssel **(engedélyezett IP-címekkel)** elérhető MySQL-kiszolgáló Azure CLI használatával való létrehozására koncentrálunk, és áttekintést nyújtunk a kiszolgáló létrehozása után a tűzfalszabályok létrehozásához, frissítéséhez, törléséhez, listához és listához használható Azure CLI-parancsokról. A *Nyilvános hozzáférés (engedélyezett IP-címek)* esetén a MySQL-kiszolgálóval létesített kapcsolatok csak engedélyezett IP-címekre vannak korlátozva. Az ügyfél IP-címeit a tűzfalszabályokban kell engedélyezve lennie. További információért lásd: Nyilvános [hozzáférés (engedélyezett IP-címek)](./concepts-networking.md#public-access-allowed-ip-addresses). A tűzfalszabályok a kiszolgáló létrehozásakor határozhatóak meg (ajánlott), de később is hozzáadhatóak.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A lapot megnyithatja Cloud Shell böngészőlapon a következő megnyitásával: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be a Cloud Shell, majd az **Enter** billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az login paranccsal kell bejelentkeznie [a fiókjába.](/cli/azure/reference-index#az_login) Figyelje **meg az Id** tulajdonságot, amely az Azure-fiók előfizetés-azonosítójára vonatkozik. 

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókjában [az az account set paranccsal.](/cli/azure/account#az_account_set) Jegyezze fel  az az **login** output parancs id értékét, amely a subscription argumentum értékeként lesz használva a parancsban.  Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Tűzfalszabály létrehozása rugalmas kiszolgáló létrehozása során az Azure CLI használatával

Az paranccsal nyilvános hozzáféréssel (engedélyezett IP-címekkel) hozhatja létre a rugalmas kiszolgálót, és konfigurálhatja a `az mysql flexible-server --public access` tűzfalszabályokat  a rugalmas kiszolgáló létrehozása során. A **--public-access** kapcsolóval meg lehet adni az engedélyezett IP-címeket, amelyek csatlakozni tudnak a kiszolgálóhoz. Az IP-címek engedélyezett listájában egy vagy több IP-címet is szerepeltethet. Az IP-címtartományt kötőjellel kell elválasztani, és nem tartalmazhat szóközöket. Az alábbi példában látható módon többféle módon hozhat létre rugalmas kiszolgálót a CLI használatával.

A konfigurálható [CLI-paraméterek](/cli/azure/mysql/flexible-server) teljes listájáért tekintse meg az Azure CLI referenciadokumentációját. Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

- Rugalmas kiszolgáló létrehozása nyilvános hozzáféréssel, és ügyfél IP-címének hozzáadása a kiszolgálóhoz való hozzáféréshez
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és adja hozzá az IP-címtartományt a kiszolgálóhoz való hozzáféréshez

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Hozzon létre egy rugalmas kiszolgálót nyilvános hozzáféréssel, és engedélyezze az Azure IP-címekről származó alkalmazásoknak a rugalmas kiszolgálóhoz való csatlakozást
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze a nyilvános hozzáférést az Azure-szolgáltatásokból és -erőforrásokból a kiszolgálóhoz, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
    >
- Rugalmas kiszolgáló létrehozása nyilvános hozzáféréssel, és az összes IP-cím engedélyezése
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > A fenti parancs létrehoz egy tűzfalszabályt, amely a start IP address=0.0.0.0, end IP address=255.255.255.255, és nem blokkolja az IP-címeket. Ezt a kiszolgálót az interneten bármely gazdagép elérheti. Erősen ajánlott ezt a szabályt csak ideiglenesen és csak olyan tesztkiszolgálókon használni, amelyek nem tartalmaznak bizalmas adatokat.

- Rugalmas kiszolgáló létrehozása nyilvános hozzáféréssel és IP-cím nélkül
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > Nem javasoljuk, hogy tűzfalszabályok nélkül hozzon létre kiszolgálót. Ha nem ad hozzá tűzfalszabályokat, egyetlen ügyfél sem fog tudni csatlakozni a kiszolgálóhoz.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Tűzfalszabály létrehozása és kezelése a kiszolgáló létrehozása után
Az **az mysql flexible-server firewall-rule** paranccsal hozhat létre, törölhet, listálható, mutatható be és frissíthet tűzfalszabályokat az Azure CLI-ről.

Parancsok:
- **create**: Hozzon létre egy rugalmas kiszolgálói tűzfalszabályt.
- **list:** List the flexible server firewall rules .list the flexible server firewall rules.
- **update**: Rugalmas kiszolgálói tűzfalszabály frissítése.
- **show**: Egy rugalmas kiszolgálói tűzfalszabály részleteinek megjelenítése.
- **delete:** Rugalmas kiszolgálói tűzfalszabály törlése.

A konfigurálható [CLI-paraméterek](/cli/azure/mysql/flexible-server) teljes listájáért tekintse meg az Azure CLI referenciadokumentációját. Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

### <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása
Az `az mysql flexible-server firewall-rule create` paranccsal hozzon létre új tűzfalszabályt a kiszolgálón.
Egy IP-címtartományhoz való hozzáféréshez adja meg az IP-címet kezdő IP-címként és záró IP-címként, ahogy ebben a példában is.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Ha egyetlen IP-cím számára is engedélyeznie kell a hozzáférést, csak egyetlen IP-címet adjon meg, ahogy ebben a példában is.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Ha engedélyezni szeretne, hogy az Azure IP-címekről származó alkalmazások csatlakozzon a rugalmas kiszolgálóhoz, adja meg a 0.0.0.0 IP-címet kezdő IP-címként, ahogy ebben a példában is.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze a nyilvános hozzáférést az Azure-szolgáltatásokból és -erőforrásokból a kiszolgálóhoz, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres létrehozás esetén minden parancs kimenete JSON formátumban (alapértelmezés szerint) listázza a létrehozott tűzfalszabály részleteit. Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

### <a name="list-firewall-rules"></a>Tűzfalszabályok felsorolása 
Az `az mysql flexible-server firewall-rule list` paranccsal listába sorolja a kiszolgálón meglévő kiszolgálói tűzfalszabályokat. Figyelje meg, hogy a kiszolgálónév attribútum meg van adva a **--name kapcsolóban.**
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
A kimenet (alapértelmezés szerint) JSON formátumban listázza a szabályokat ( ha vannak). A --output table** kapcsolóval olvashatóbb táblázatos formátumban kimenetet kaphat az eredményekhez.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Tűzfalszabály frissítése
Az `az mysql flexible-server firewall-rule update` paranccsal frissítheti a kiszolgálón meglévő tűzfalszabályt. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissíteni szükséges kezdő IP-címet és záró IP-cím attribútumot.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Sikeresség esetén a parancs kimenete JSON formátumban (alapértelmezés szerint) listázza a frissített tűzfalszabály részleteit. Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt az update paranccsal lehet létrehozni.

### <a name="show-firewall-rule-details"></a>Tűzfalszabály részleteinek megjelenítése
A `az mysql flexible-server firewall-rule show` paranccsal a kiszolgálóról származó meglévő tűzfalszabály-részleteket mutatjuk be. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Sikeresség esetén a parancs kimenete megjeleníti a megadott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Hiba esetén a kimenetben ehelyett hibaüzenet jelenik meg.

### <a name="delete-a-firewall-rule"></a>Tűzfalszabály törlése
A `az mysql flexible-server firewall-rule delete` paranccsal törölhet egy meglévő tűzfalszabályt a kiszolgálóról. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Sikeresség esetén nincs kimenet. Hiba esetén megjelenik a hibaüzenet szövege.

## <a name="next-steps"></a>Következő lépések
- További információ a [rugalmas Azure Database for MySQL hálózatról](./concepts-networking.md)
- További információ a [rugalmas Azure Database for MySQL tűzfalszabályokról](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Rugalmas kiszolgálói tűzfalszabályok Azure Database for MySQL és kezelése a Azure Portal.](./how-to-manage-firewall-portal.md)