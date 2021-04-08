---
title: Azure Firewall üzembe helyezése és konfigurálása az Azure CLI-vel
description: Ebből a cikkből megtudhatja, hogyan helyezheti üzembe és konfigurálhatja Azure Firewall az Azure CLI használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 4d611a6d575fbc94a555006882f77e5a31753164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051428"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Azure Firewall üzembe helyezése és konfigurálása az Azure CLI-vel

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Az is előfordulhat, hogy korlátozni szeretné az elérhető kimenő IP-címeket és portokat.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg. A teljes tartománynév [tartalmazhat SQL-példányokat](sql-fqdn-filtering.md)is.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben a cikkben egy egyszerűsített egyszeri VNet hoz létre három alhálózattal az egyszerű üzembe helyezéshez. Éles környezetekben javasolt a [hub és a küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata. A tűzfal a saját VNet található. A munkaterhelés-kiszolgálók egy vagy több alhálózattal azonos régióban lévő, egymással azonos régióba tartozó virtuális hálózatok találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Tesztelési hálózati környezet beállítása
* Tűzfal üzembe helyezése
* Alapértelmezett útvonal létrehozása
* Alkalmazás-szabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
* Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
* A tűzfal tesztelése

Ha szeretné, az eljárást a [Azure Portal](tutorial-firewall-deploy-portal.md) vagy [Azure PowerShell](deploy-ps.md)használatával végezheti el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport az üzemelő példány összes erőforrását tartalmazza.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Ez a virtuális hálózat három alhálózattal rendelkezik.

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.
Ha a rendszer kéri, adjon meg egy jelszót a virtuális géphez.

Hozza létre a Srv-Jump virtuális gépet.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Hozzon létre egy hálózati adaptert a megadott DNS-kiszolgáló IP-címekkel rendelkező Srv-Workhoz, és a teszteléshez ne legyen nyilvános IP-cím.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Most hozza létre a munkaterhelési virtuális gépet.
Ha a rendszer kéri, adjon meg egy jelszót a virtuális géphez.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Most telepítse a tűzfalat a virtuális hálózatba.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Tábla létrehozása a BGP-útvonal propagálásával letiltva

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Hozza létre az útvonalat.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Az útválasztási táblázat hozzárendelése az alhálózathoz

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Az alkalmazási szabály lehetővé teszi a kimenő hozzáférést a www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

A hálózati szabály lehetővé teszi a kimenő hozzáférést két IP-címhez a 53-as porton (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, és ellenőrizze, hogy az a várt módon működik-e.

1. Jegyezze fel az **SRV-Work** virtuális gép magánhálózati IP-címét:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Csatlakoztasson egy távoli asztalt a **SRV-Jump** virtuális géphez, és jelentkezzen be. Innen nyisson meg egy távoli asztali kapcsolattal az **SRV-Work** magánhálózati IP-címet, és jelentkezzen be.

3. Az **SRV-Work** lapon nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsokat:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Mindkét parancsnak válaszokat kell visszaadnia, ami azt mutatja, hogy a DNS-lekérdezések bekerülnek a tűzfalon.

1. Futtassa az alábbi parancsot:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   A `www.google.com` kérések sikeresek lesznek, és a `www.microsoft.com` kérések sikertelenek lesznek. Ez azt mutatja, hogy a tűzfalszabályok a várt módon működnek.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.
* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban megtarthatja a tűzfal erőforrásait, vagy ha már nincs rá szükség, törölje a **test-FW-RG** erőforráscsoportot az összes tűzfalhoz kapcsolódó erőforrás törléséhez:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)