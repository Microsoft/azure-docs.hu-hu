---
title: Oktatóanyag – LAMP és WordPress üzembe helyezése virtuális gépen
description: Ez az oktatóanyag bemutatja, hogyan telepítheti a LAMP vermet és a WordPresst Egy Linux rendszerű virtuális gépen az Azure-ban.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816347"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Oktatóanyag: LAMP-verem telepítése Azure-beli linuxos virtuális gépen

Ez a cikk ismerteti, hogyan helyezhet üzembe Apache-webkiszolgálót, MySQL-t és PHP-t (a LAMP-vermet) Ubuntu rendszerű virtuális gépen az Azure-ban. Ha szeretné működés közben megtekinteni a LAMP-vermet, telepíthet és konfigurálhat egy WordPress-webhelyet. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása 
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése 

Ez a telepítés gyors teszteléshez és megvalósíthatósági vizsgálatokhoz használható. További információt a LAMP-veremről – beleértve az éles környezetre vonatkozó javaslatokat – az [Ubuntu dokumentációjában](https://help.ubuntu.com/community/ApacheMySQLPHP) talál.

Ez az oktatóanyag a cli-t használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amely folyamatosan frissül a legújabb verzióra. A kód Cloud Shell válassza a **Kipróbálom** lehetőséget bármely kódblokk tetején.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A parancs emellett az *azureuser* nevet állítja be rendszergazdai felhasználónévként. Később ezt a nevet fogja használni a virtuális géphez való csatlakozáshoz. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím a később lépésekben használható a virtuális gép eléréséhez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Mivel ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. Nyissa meg a kívánt portot az [az vm open-port](/cli/azure/vm) paranccsal.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

További információ a portok a virtuális géphez való megnyitásáról: [Portok megnyitása.](nsg-quickstart.md)

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Ha még nem ismeri a virtuális gépéhez tartozó nyilvános IP-címet, futtassa az [az network public-ip list](/cli/azure/network/public-ip) parancsot. Erre az IP-címre több későbbi lépésben is szüksége lesz.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Helyettesítse be a virtuális gépe tényleges nyilvános IP-címét. Ebben a példában az IP-cím a következő: *40.68.254.142*. Az *azureuser* a virtuális gép létrehozásakor beállított rendszergazdai felhasználónév.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Az Apache, a MySQL és a PHP telepítése

Futtassa az alábbi parancsot az Ubuntu-csomag forrásainak frissítéséhez és az Apache, a MySQL és a PHP telepítéséhez. A parancs végén lévő beszúrási jel (^) a `lamp-server^` csomag nevének része. 


```bash
sudo apt update && sudo apt install lamp-server^
```

A rendszer felszólítja a csomagok és más függőségek telepítésére. Ezzel a folyamat telepíti a PHP MySQL-lel való használatához minimálisan szükséges PHP-bővítményeket.  

## <a name="verify-apache"></a>Az Apache ellenőrzése

Ellenőrizze az Apache verzióját a következő paranccsal:
```bash
apache2 -v
```

Most, hogy az Apache telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Az Apache2 Ubuntu alapértelmezett oldalának megtekintéséhez nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. Használja azt a nyilvános IP-címet, amellyel SSH-kapcsolatot létesített a virtuális géppel:

![Az Apache alapértelmezett oldala][3]


## <a name="verify-and-secure-mysql"></a>A MySQL ellenőrzése és biztonságossá minősítése

Ellenőrizze a MySQL verzióját a következő paranccsal (ügyeljen a nagybetűs `V` paraméterre):

```bash
mysql -V
```

Futtassa a szkriptet a MySQL telepítésének biztonságossá minősítéséhez, beleértve a gyökér szintű jelszó `mysql_secure_installation` beállítását is. 

```bash
sudo mysql_secure_installation
```

Igény szerint beállíthatja a Jelszó ellenőrzése beépülő modult (ajánlott). Ezután állítson be egy jelszót a MySQL gyökérfelhasználója számára, és konfigurálja a környezet fennmaradó biztonsági beállításait. Javasoljuk, hogy minden kérdésre válaszoljon az "Y" (igen) válaszra.

Ha ki szeretné próbálni a MySQL funkcióit (MySQL-adatbázis létrehozása, felhasználók hozzáadása vagy a konfigurációs beállítások módosítása), jelentkezzen be a MySQL-be. Ez a lépés nem kötelező az oktatóanyag elvégzéséhez.

```bash
sudo mysql -u root -p
```

Amikor végzett, a `\q` parancs beírásával lépjen ki a mysql parancssorból.

## <a name="verify-php"></a>A PHP ellenőrzése

Ellenőrizze a PHP verzióját a következő paranccsal:

```bash
php -v
```

Ha további tesztelést szeretne végezni, hozzon létre egy rövid PHP-információs oldalt. amely a böngészőben tekinthető meg. A következő paranccsal hozhatja létre a PHP-információs oldalt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Most ellenőrizheti a létrehozott PHP-információs oldalt. Nyisson meg egy böngészőt, és ugorjon a `http://yourPublicIPAddress/info.php` címre. Helyettesítse be a virtuális gép nyilvános IP-címét. Ennek a képen láthatóhoz hasonlóan kell kinéznie.

![PHP-információs oldal][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy LAMP-kiszolgálót helyezett üzembe az Azure-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu rendszerű virtuális gép létrehozása
> * A 80-as port megnyitása a webes adatforgalom számára
> * Az Apache, a MySQL és a PHP telepítése
> * A telepítés és a konfigurálás ellenőrzése
> * A WordPress telepítése a LAMP-kiszolgálón

A következő oktatóanyag azt is bemutatja, hogyan biztosíthatja a webkiszolgálók biztonságát TLS-/SSL-tanúsítványokkal.

> [!div class="nextstepaction"]
> [Webkiszolgáló biztosítása TLS-sel](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
