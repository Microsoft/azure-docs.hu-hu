---
title: Nyilvános beállításjegyzék-hozzáférés konfigurálása
description: Konfigurálja az IP-szabályokat az Azure Container Registryhez való hozzáférés engedélyezéséhez a kiválasztott nyilvános IP-címekről vagy címtartományokból.
ms.topic: article
ms.date: 03/08/2021
ms.openlocfilehash: 00912f0e66c84feff40e6439d59ccdfa82a4ab6a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785835"
---
# <a name="configure-public-ip-network-rules"></a>Nyilvános IP-hálózati szabályok konfigurálása

Az Azure Container Registry alapértelmezés szerint bármilyen hálózat gazdagépei számára internetes kapcsolatokat fogad. Ez a cikk bemutatja, hogyan konfigurálhatja a tároló-beállításjegyzéket úgy, hogy csak bizonyos nyilvános IP-címekről vagy címtartományok számára engedélyezze a hozzáférést. Az Egyenértékű lépések az Azure CLI és a Azure Portal meg.

Az IP-hálózati szabályok a nyilvános beállításjegyzék végpontjára vannak konfigurálva. Az IP-hálózati szabályok nem vonatkoznak az ip-címmel konfigurált privát [Private Link](container-registry-private-link.md)

Az IP-hozzáférési szabályok konfigurálása a **Prémium** tároló-beállításjegyzék szolgáltatásszinten érhető el. A beállításjegyzék szolgáltatási rétegeiről és korlátairól további információt a következő [Azure Container Registry tartalmaz:](container-registry-skus.md).

Minden beállításjegyzék legfeljebb 100 hálózati hozzáférési szabályt támogat.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Hozzáférés a kiválasztott nyilvános hálózatról – CLI

### <a name="change-default-network-access-to-registry"></a>A beállításjegyzék alapértelmezett hálózati hozzáférésének módosítása

A kijelölt nyilvános hálózathoz való hozzáférés korlátozásához először módosítsa az alapértelmezett műveletet a hozzáférés megtagadása beállításra. Helyettesítse be a regisztrációs adatbázis nevét a következő [az acr update parancsban:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az [az acr network-rule add paranccsal][az-acr-network-rule-add] adjon hozzá egy olyan hálózati szabályt a beállításjegyzékhez, amely engedélyezi a nyilvános IP-címről vagy -tartományból való hozzáférést. Helyettesítse be például a tároló-beállításjegyzék nevét és egy virtuális hálózatban lévő virtuális gép nyilvános IP-címét.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Szabály hozzáadása után néhány percet vesz igénybe, hogy a szabály életbe lép.

## <a name="access-from-selected-public-network---portal"></a>Hozzáférés a kiválasztott nyilvános hálózatról – portál

1. A portálon keresse meg a tároló-beállításjegyzéket.
1. A **Beállítások alatt** válassza a Hálózat **lehetőséget.**
1. A Nyilvános **hozzáférés lapon** válassza a Nyilvános hozzáférés engedélyezése lehetőséget a Kiválasztott hálózatokból való **nyilvános hozzáféréshez.**
1. A **Tűzfal területen** adjon meg egy nyilvános IP-címet, például egy virtuális hálózat virtuális gépének nyilvános IP-címét. Vagy adjon meg egy címtartományt CIDR-ként, amely tartalmazza a virtuális gép IP-címét.
1. Kattintson a **Mentés** gombra.

![Tűzfalszabály konfigurálása a tároló-beállításjegyzékhez][acr-access-selected-networks]

> [!NOTE]
> A szabály hozzáadása után néhány percet vesz igénybe, hogy a szabály életbe lép.

> [!TIP]
> Engedélyezheti a beállításjegyzékhez való hozzáférést egy helyi ügyfélszámítógépről vagy IP-címtartományból. A hozzáféréshez szüksége lesz a számítógép nyilvános IPv4-címére. Ezt a címet úgy találhatja meg, ha egy webböngészőben rákeres a "mi az IP-címem" szóra. Az aktuális ügyfél IPv4-címe is automatikusan megjelenik, amikor a portál **Hálózat** lapján konfigurálja a tűzfalbeállításokat.

## <a name="disable-public-network-access"></a>Nyilvános hálózati hozzáférés letiltása

Ha szeretné, tiltsa le a nyilvános végpontot a beállításjegyzékben. A nyilvános végpont letiltása felülbírálja az összes tűzfal-konfigurációt. Előfordulhat például, hogy le szeretné tiltani egy virtuális hálózatban védett beállításjegyzék nyilvános hozzáférését az [Private Link.](container-registry-private-link.md)

> [!NOTE]
> Ha [a](container-registry-vnet.md)beállításjegyzék egy szolgáltatásvégponttal rendelkezik egy virtuális hálózatban, a beállításjegyzék nyilvános végpontjának hozzáférésének letiltása a beállításjegyzékhez való hozzáférést is letiltja a virtuális hálózaton belül.

### <a name="disable-public-access---cli"></a>Nyilvános hozzáférés letiltása – CLI

Ha le szeretné tiltani a nyilvános hozzáférést az Azure CLI használatával, futtassa [az az acr update][az-acr-update] futtatását, és állítsa a következőre: `--public-network-enabled` `false` . Az `public-network-enabled` argumentumhoz az Azure CLI 2.6.0-s vagy újabb szükséges. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Nyilvános hozzáférés letiltása – portál

1. A portálon lépjen a tároló-beállításjegyzékhez, és válassza a **Hálózat > lehetőséget.**
1. A Nyilvános **hozzáférés lap** Nyilvános hálózati hozzáférés engedélyezése **lapján válassza** a **Letiltva lehetőséget.** Kattintson a **Mentés** gombra.

![Nyilvános hozzáférés letiltása][acr-access-disabled]


## <a name="restore-public-network-access"></a>Nyilvános hálózati hozzáférés visszaállítása

A nyilvános végpont újra engedélyezéséhez frissítse a hálózati beállításokat a nyilvános hozzáférés engedélyezéséhez. A nyilvános végpont engedélyezése felülbírálja az összes tűzfal-konfigurációt. 

### <a name="restore-public-access---cli"></a>Nyilvános hozzáférés visszaállítása – CLI

Futtassa [az az acr update et,][az-acr-update] és állítsa a `--public-network-enabled` beállítását a (beállítás) `true` beállításra. 

> [!NOTE]
> Az `public-network-enabled` argumentumhoz az Azure CLI 2.6.0-s vagy újabb szükséges. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Nyilvános hozzáférés visszaállítása – portál

1. A portálon lépjen a tároló-beállításjegyzékhez, és válassza a **Beállítások > lehetőséget.**
1. A Nyilvános **hozzáférés lap** Nyilvános hálózati hozzáférés engedélyezése **lapján** válassza a Minden **hálózat lehetőséget.** Kattintson a **Mentés** gombra.

![Nyilvános hozzáférés az összes hálózatról][acr-access-all-networks]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha nyilvános hálózati szabály van beállítva, vagy a beállításjegyzékhez való nyilvános hozzáférés le van tiltva, a nem engedélyezett nyilvános hálózatról a beállításjegyzékbe való bejelentkezés sikertelen lesz. A HTTPS-proxy mögötti ügyfél-hozzáférés szintén sikertelen lesz, ha nincs beállítva hozzáférési szabály a proxyhoz. Ehhez hasonló hibaüzenet jelenik meg: `Error response from daemon: login attempt failed with status: 403 Forbidden` vagy `Looks like you don't have access to registry` .

Ezek a hibák akkor is előfordulhatnak, ha hálózati hozzáférési szabály által engedélyezett HTTPS-proxyt használ, de a proxy nincs megfelelően konfigurálva az ügyfélkörnyezetben. Ellenőrizze, hogy a Docker-ügyfél és a Docker-démon is konfigurálva van-e a proxyviselkedéshez. Részletekért tekintse meg a [Docker dokumentációjának HTTP/HTTPS-proxyval](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) kapcsolatos dokumentumát.


## <a name="next-steps"></a>Következő lépések

* A regisztrációs adatbázishoz való hozzáférés virtuális hálózaton lévő privát végpont használatával való korlátozásához lásd: Configure Azure Private Link for an Azure Container Registry (Regisztrációs adatbázis konfigurálása [Azure-beli tároló-beállításjegyzékhez).](container-registry-private-link.md)
* Ha egy ügyfél tűzfal mögötti beállításjegyzék-hozzáférési szabályokat kell beállítania, tekintse meg a következőt: Configure rules to access an Azure Container Registry behind a firewall (Szabályok konfigurálása tűzfal mögötti [Azure Container Registry eléréséhez).](container-registry-firewall-access-rules.md)

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
