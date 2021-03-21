---
title: Privát hozzáférés engedélyezése privát hivatkozással (előzetes verzió) – parancssori felület
titleSuffix: Azure Digital Twins
description: Az Azure CLI használatával megtudhatja, hogyan engedélyezheti a privát hozzáférést az Azure Digital Twins-megoldásokhoz privát hivatkozással.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202943"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Privát hozzáférés engedélyezése privát hivatkozással (előzetes verzió): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Ez a cikk bemutatja, hogyan [engedélyezheti a privát hivatkozásokat az Azure Digital Twins-példányok privát végpontján](concepts-security.md#private-network-access-with-azure-private-link-preview) (jelenleg előzetes verzióban érhető el). Az Azure Digital Twins-példány privát végpontjának konfigurálása lehetővé teszi az Azure-beli digitális Twins-példányok védelmét, valamint a nyilvános kitettség kiiktatását, valamint az [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)származó adatok kiszűrése elkerülését.

Ez a cikk végigvezeti a folyamaton az [**Azure CLI**](/cli/azure/what-is-azure-cli)használatával.

A cikk a következő lépésekkel foglalkozik: 
1. Kapcsolja be a privát hivatkozást, és állítson be egy privát végpontot egy Azure Digital Twins-példányhoz.
1. A nyilvános hálózati hozzáférés jelzőit letilthatja vagy engedélyezheti, ha csak a magánhálózati kapcsolatok API-hozzáférését szeretné korlátozni.

## <a name="prerequisites"></a>Előfeltételek

A privát végpontok beállítása előtt szüksége lesz egy [**Azure Virtual Networkra (VNet)**](../virtual-network/virtual-networks-overview.md) , ahol a végpont telepíthető. Ha még nem rendelkezik VNet, a beállításához kövesse az [Azure Virtual Network rövid](../virtual-network/quick-create-portal.md) útmutatóinak egyikét.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Privát végpontok kezelése Azure Digital Twins-példányhoz 

Ha az [Azure CLI](/cli/azure/what-is-azure-cli)-t használja, privát végpontokat állíthat be egy olyan Azure digitális Twins-példányon, amely már létezik (nem adható hozzá a példány létrehozása részeként). Ezután további CLI-parancsokkal is megtekintheti és kezelheti azokat. 

>[!TIP]
> Privát kapcsolati végpontot is beállíthat a privát kapcsolati szolgáltatáson keresztül, az Azure Digital Twins-példányon keresztül. Ez ugyanazokat a konfigurációs beállításokat és a végeredményt is megadja.
>
> A magánhálózati kapcsolati erőforrások beállításával kapcsolatos további információkért tekintse meg a [Azure Portal](../private-link/create-private-endpoint-portal.md), az [Azure CLI](../private-link/create-private-endpoint-cli.md), az [ARM-sablonok](../private-link/create-private-endpoint-template.md)vagy a [PowerShell](../private-link/create-private-endpoint-powershell.md)saját hivatkozásokkal kapcsolatos dokumentációját.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Privát végpont hozzáadása meglévő példányhoz

Privát végpont létrehozásához és egy Azure Digital Twins-példánnyal való összekapcsolásához használja az az [**Network Private-Endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) parancsot. Azonosítsa az Azure Digital Twins-példányt a paraméterben szereplő teljes azonosító használatával `--private-connection-resource-id` .

Íme egy példa arra, hogy a parancs használatával hozzon létre egy privát végpontot, csak a szükséges paraméterekkel.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

A kötelező és választható paraméterek teljes listáját, valamint a privát végpontok létrehozására vonatkozó példákat az az [ **Network Private-Endpoint Create** Reference dokumentációban](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)találja.

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Magánhálózati végpontok kapcsolatainak kezelése a példányon

Miután létrehozta az Azure Digital Twins-példányhoz egy privát végpontot, az az [**DT Network Private-Endpoint Connection**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) paranccsal folytathatja a példányra vonatkozó privát végponti **kapcsolatok** kezelését. A műveletek a következők:
* Privát végponti kapcsolat megjelenítése
* A magánhálózati végponti kapcsolatok állapotának beállítása
* A Private-Endpoint-kapcsolatok törlése
* Egy példány összes magán-végponti kapcsolatának listázása

További információkat és példákat az az [ **DT Network Private-Endpoint** Reference dokumentációjában](/cli/azure/ext/azure-iot/dt/network/private-endpoint)talál.

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Egyéb privát hivatkozásokkal kapcsolatos információk kezelése Azure Digital Twins-példányon

A példány privát kapcsolati állapotáról az az [**DT Network Private-link**](/cli/azure/ext/azure-iot/dt/network/private-link) paranccsal kaphat további információkat. A műveletek a következők:
* Azure Digital Twins-példánnyal társított privát hivatkozások listázása
* A példányhoz társított privát hivatkozás megjelenítése

További információkat és példákat az az [ **DT Network Private-link** Reference dokumentációjában](/cli/azure/ext/azure-iot/dt/network/private-link)talál.

## <a name="disable--enable-public-network-access-flags"></a>Nyilvános hálózati hozzáférési jelzők letiltása/engedélyezése

Beállíthatja az Azure Digital Twins-példányát az összes nyilvános kapcsolat megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztül csatlakozhat a kapcsolatokhoz. Ez a művelet **nyilvános hálózati hozzáférési jelzővel** végezhető el. 

Ez a szabályzat lehetővé teszi, hogy csak a privát kapcsolati kapcsolatok API-hozzáférését korlátozza. Ha a nyilvános hálózati hozzáférés jelzője *Letiltva* értékre van állítva, akkor a nyilvános felhőből érkező összes REST API hívás az Azure digitális Twins-példányának adatsíkjával fog visszatérni `403, Unauthorized` . Azt is megteheti, hogy ha a házirend *le van tiltva* , és egy privát végponton keresztül kezdeményezi a kérést, az API-hívás sikeres lesz.

Ez a cikk bemutatja, hogyan frissítheti a hálózati jelző értékét az [Azure CLI](/cli/azure/) vagy a [ARMClient parancs](https://github.com/projectkudu/ARMClient)használatával. A Azure Portalával kapcsolatos útmutatásért tekintse meg a jelen cikk [portáljának verzióját](how-to-enable-private-link-portal.md) .

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure CLI-ben letilthatja vagy engedélyezheti a nyilvános hálózati hozzáférést úgy, hogy hozzáad egy `--public-network-access` paramétert a `az dt create` parancshoz. Ez a parancs egy új példány létrehozásához is használható, így a meglévő példányok tulajdonságainak szerkesztéséhez egy már létező példány nevét kell megadni. (A paranccsal kapcsolatos további információkért tekintse meg a [hivatkozási dokumentációt](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) , vagy az [Azure Digital Twins-példány beállításának általános utasításait](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Az Azure Digital Twins-példány nyilvános hálózati hozzáférésének **letiltásához** használja a `--public-network-access` következőhöz hasonló paramétert:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Ha egy olyan példányon szeretné **engedélyezni** a nyilvános hálózati hozzáférést, amely jelenleg le van tiltva, használja a következő hasonló parancsot:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>A ARMClient parancssori eszköz használata 

A [ARMClient parancssori eszközzel](https://github.com/projectkudu/ARMClient)a nyilvános hálózati hozzáférés engedélyezve van vagy le van tiltva az alábbi parancsokkal. 

A nyilvános hálózati hozzáférés **letiltása** :
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Nyilvános hálózati hozzáférés **engedélyezése** :  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Következő lépések

További információ az Azure-hoz készült privát hivatkozásról: 
* [*Mi az az Azure Private link Service?*](../private-link/private-link-service-overview.md)