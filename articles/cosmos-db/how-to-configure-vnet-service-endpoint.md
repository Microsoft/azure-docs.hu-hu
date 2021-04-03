---
title: Virtuális hálózat alapú hozzáférés konfigurálása Azure Cosmos-fiókhoz
description: Ez a dokumentum ismerteti a Azure Cosmos DB virtuális hálózati szolgáltatás végpontjának beállításához szükséges lépéseket.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94637183"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Azure Cosmos DB hozzáférésének konfigurálása virtuális hálózatokból (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos-fiókot úgy is beállíthatja, hogy csak a virtuális hálózat (VNet) egy adott alhálózatáról engedélyezze a hozzáférést. A [szolgáltatás végpontjának](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózaton belüli alhálózathoz Azure Cosmos db való hozzáférésének engedélyezésével az adott alhálózatról érkező forgalmat a rendszer a Azure Cosmos db az alhálózat és a Virtual Network identitásával küldi el. Ha a Azure Cosmos DB szolgáltatás végpontja engedélyezve van, az alhálózathoz való hozzáférést az Azure Cosmos-fiókjához hozzáadva korlátozhatja.

Alapértelmezés szerint az Azure Cosmos-fiók bármely forrásból elérhető, ha a kérést érvényes engedélyezési jogkivonat kíséri. Ha egy vagy több alhálózatot ad hozzá a virtuális hálózatok-n belül, csak ezekből az alhálózatokból származó kérelmek érvényes választ kapnak. A más forrásból származó kérelmek 403 (tiltott) választ kapnak. 

Az Azure Cosmos DB-fiókok konfigurálhatók úgy, hogy csak egy Azure-beli virtuális hálózat adott alhálózatáról legyen engedélyezett a hozzáférés. Egy Azure Cosmos DB-fiókhoz való hozzáférés korlátozása egy virtuális hálózatban lévő alhálózattal létesített kapcsolattal:

1. Engedélyezze az alhálózatnak az alhálózat és a virtuális hálózat identitásának küldését Azure Cosmos DB. Ezt úgy érheti el, ha engedélyezi egy szolgáltatási végpontot az adott alhálózat Azure Cosmos DBához.

1. Adjon hozzá egy szabályt a Azure Cosmos DB-fiókban az alhálózat forrásként való megadásához, amely a fiók elérésére szolgál.

> [!NOTE]
> Ha a Azure Cosmos DB-fiókhoz tartozó szolgáltatási végpont engedélyezve van az alhálózaton, akkor a forgalom forrása Azure Cosmos DB a nyilvános IP-címekről egy virtuális hálózatra és alhálózatra vált. A forgalom váltás minden olyan Azure Cosmos DB-fiókra vonatkozik, amely ebből az alhálózatból elérhető. Ha a Azure Cosmos DB-fiókok IP-alapú tűzfallal engedélyezik ezt az alhálózatot, a szolgáltatással kompatibilis alhálózatról érkező kérések már nem felelnek meg az IP-tűzfalszabályok feltételeinek, és el lesznek utasítva.
>
> További információ: az [áttelepítés IP-tűzfallal szabályból a virtuális hálózati hozzáférés-vezérlési listára](#migrate-from-firewall-to-vnet) című szakasz lépései.

A következő szakaszok azt ismertetik, hogyan konfigurálható egy virtuális hálózati szolgáltatás végpontja egy Azure Cosmos DB-fiókhoz.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Szolgáltatási végpont konfigurálása a Azure Portal használatával

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Szolgáltatási végpont konfigurálása meglévő Azure-beli virtuális hálózathoz és alhálózathoz

1. A **minden erőforrás** panelen keresse meg a védeni kívánt Azure Cosmos db fiókot.

1. Válassza a **tűzfalak és virtuális hálózatok** lehetőséget a beállítások menüben, és válassza a hozzáférés engedélyezése a **kiválasztott hálózatokból** lehetőséget.

1. Ha hozzáférést szeretne biztosítani egy meglévő virtuális hálózat alhálózatához, a **virtuális hálózatok** területen válassza a **meglévő Azure-beli virtuális hálózat hozzáadása** elemet.

1. Válassza ki azt az **előfizetést** , amelyből hozzá szeretné adni az Azure-beli virtuális hálózatot. Válassza ki azokat az Azure-beli **virtuális hálózatokat** és **alhálózatokat** , amelyeknek hozzáférést kíván biztosítani a Azure Cosmos db-fiókjához. Ezután válassza az **Engedélyezés** lehetőséget a kiválasztott hálózatok engedélyezéséhez a "Microsoft. AzureCosmosDB" szolgáltatási végpontokkal. Ha elkészült, válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Válassza ki a virtuális hálózatot és az alhálózatot":::

1. Ha a Azure Cosmos DB fiók engedélyezve van egy virtuális hálózatról való hozzáférésre, akkor csak a kiválasztott alhálózatról érkező forgalmat engedélyezi. A hozzáadott virtuális hálózatnak és alhálózatnak az alábbi képernyőképen látható módon kell megjelennie:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="A virtuális hálózat és az alhálózat sikeresen konfigurálva":::

> [!NOTE]
> A virtuális hálózati szolgáltatás végpontok engedélyezéséhez a következő előfizetési engedélyek szükségesek:
>   * Előfizetés virtuális hálózattal: hálózati közreműködő
>   * Előfizetés Azure Cosmos DB fiókkal: DocumentDB-fiók közreműködői
>   * Ha a virtuális hálózat és a Azure Cosmos DB fiók különböző előfizetésekben található, győződjön meg arról, hogy a virtuális hálózattal rendelkező előfizetéshez erőforrás-szolgáltató is van `Microsoft.DocumentDB` regisztrálva. Erőforrás-szolgáltató regisztrálásához tekintse meg az [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md) című cikket.

Az előfizetés erőforrás-szolgáltatóval való regisztrálásának útmutatója.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Szolgáltatási végpont konfigurálása új Azure-beli virtuális hálózat és alhálózat számára

1. A **minden erőforrás** panelen keresse meg a védeni kívánt Azure Cosmos db fiókot.  

1. Válassza a **tűzfalak és az Azure-beli virtuális hálózatok** lehetőséget a beállítások menüben, és válassza a hozzáférés engedélyezése a **kiválasztott hálózatokból** lehetőséget.  

1. Ha hozzáférést szeretne biztosítani egy új Azure-beli virtuális hálózathoz, a **virtuális hálózatok** területen válassza az **új virtuális hálózat hozzáadása** lehetőséget.  

1. Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd válassza a **Létrehozás** lehetőséget. Az alhálózat a "Microsoft. AzureCosmosDB" számára engedélyezett szolgáltatási végponttal lesz létrehozva.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Válasszon ki egy virtuális hálózatot és alhálózatot egy új virtuális hálózat számára":::

Ha a Azure Cosmos DB-fiókot más Azure-szolgáltatások, például az Azure Cognitive Search vagy a stream Analytics vagy a Power BI használatával érik el, akkor a hozzáférést a **globális Azure-adatközpontokban lévő kapcsolatok fogadása** lehetőség választásával engedélyezheti.

Annak biztosítása érdekében, hogy a portálon Azure Cosmos DB metrikák hozzáférhessenek, engedélyeznie kell a **hozzáférést a Azure Portal** lehetőségek közül. Ha többet szeretne megtudni ezekről a lehetőségekről, tekintse meg az [IP-tűzfal konfigurálása](how-to-configure-firewall.md) című cikket. A hozzáférés engedélyezése után válassza a **Mentés** lehetőséget a beállítások mentéséhez.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Virtuális hálózat vagy alhálózat eltávolítása

1. A **minden erőforrás** panelen keresse meg azt a Azure Cosmos db fiókot, amelyhez hozzárendelte a szolgáltatási végpontokat.  

1. A beállítások menüben válassza a **tűzfalak és virtuális hálózatok** lehetőséget.  

1. Virtuális hálózat vagy alhálózat szabályának eltávolításához válassza a **...** lehetőséget a virtuális hálózat vagy alhálózat mellett, és válassza az **Eltávolítás** lehetőséget.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Virtuális hálózat eltávolítása":::

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Szolgáltatási végpont konfigurálása Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépésekkel konfigurálhatja a szolgáltatási végpontot egy Azure Cosmos DB-fiókra a Azure PowerShell használatával:  

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).  

1. Engedélyezze a szolgáltatási végpontot egy virtuális hálózat meglévő alhálózatához.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Ha a PowerShellt vagy az Azure CLI-t használja, ügyeljen arra, hogy az IP-szűrők és a virtuális hálózati ACL-ek teljes listáját adja meg a paraméterekben, nem csak azokat, amelyeket hozzá kell adnia.

1. Virtuális hálózati adatok beolvasása.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Cosmos DB Virtual Network szabály előkészítése

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Azure Cosmos DB fiók tulajdonságainak frissítése az új Virtual Network Endpoint konfigurációval: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a Azure Cosmos DB-fiókja frissítve lett-e az előző lépésben konfigurált Virtual Network Service-végponttal:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Szolgáltatási végpont konfigurálása az Azure CLI használatával

Az Azure Cosmos-fiókok a szolgáltatás-végpontokhoz konfigurálhatók, amikor később jönnek létre vagy frissülnek, ha az alhálózat már konfigurálva van. A szolgáltatási végpontok akkor is engedélyezhetők a Cosmos-fiókon, ha az alhálózat még nincs konfigurálva, és a későbbiekben is működni fog, ha az alhálózat később van konfigurálva. Ez a rugalmasság lehetővé teszi azoknak a rendszergazdáknak, akik nem férnek hozzá a Cosmos-fiókhoz és a virtuális hálózati erőforrásokhoz, hogy a konfigurációk egymástól függetlenek legyenek.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Új Cosmos-fiók létrehozása és egy új virtuális hálózat háttérbeli alhálózathoz való kapcsolódása

Ebben a példában a virtuális hálózatot és az alhálózatot olyan szolgáltatási végpontok hozzák létre, amelyek mind a létrehozáskor engedélyezve vannak.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos-fiók összekapcsolása és konfigurálása a háttérbeli alhálózatokhoz egymástól függetlenül

Ebből a példából megtudhatja, hogyan csatlakoztatható egy Azure Cosmos-fiók egy meglévő új virtuális hálózathoz, ahol az alhálózat még nincs konfigurálva a szolgáltatási végpontokhoz. Ezt a paraméter használatával végezheti el `--ignore-missing-vnet-service-endpoint` . Ez lehetővé teszi, hogy a Cosmos-fiók konfigurációja hiba nélkül befejeződjön, mielőtt a virtuális hálózat alhálózatának konfigurációja befejeződik. Az alhálózat-konfiguráció befejezése után a Cosmos-fiók elérhető lesz a konfigurált alhálózaton keresztül.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Porttartomány közvetlen mód használata esetén

Ha Azure Cosmos-fiókkal rendelkező szolgáltatási végpontokat használ közvetlen módú csatlakozással, meg kell győződnie arról, hogy a TCP-port 10000 és 20000 közötti tartománya nyitva van.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Áttelepítés IP-tűzfalszabály alapján virtuális hálózati ACL-re

Ha Azure Cosmos DB fiókot szeretne áttelepíteni az IP-tűzfalszabályok használatával a virtuális hálózati szolgáltatás végpontjának használatával, kövesse az alábbi lépéseket.

Ha egy alhálózathoz egy Azure Cosmos DB fiók van konfigurálva egy alhálózat szolgáltatási végpontja számára, a rendszer az adott alhálózat kérelmeit a forrás nyilvános IP-cím helyett a virtuális hálózattal és az alhálózati forrással kapcsolatos adatokkal Azure Cosmos DB küldi el. Ezek a kérések nem egyeznek meg a Azure Cosmos DB fiókban konfigurált IP-szűrővel, ezért az állásidő elkerüléséhez a következő lépések szükségesek.

A továbblépés előtt engedélyezze a Azure Cosmos DB szolgáltatási végpontot a virtuális hálózaton és az alhálózaton a "szolgáltatás végpontjának engedélyezése egy virtuális hálózat meglévő alhálózatához" című részében ismertetett lépéssel.

1. Virtuális hálózat és alhálózat adatainak beolvasása:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Új Virtual Network szabály objektum előkészítése a Azure Cosmos DB-fiókhoz:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Frissítse a Azure Cosmos DB fiókot a szolgáltatás-végponti hozzáférés engedélyezéséhez az alhálózatról:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ismételje meg az előző lépéseket az alhálózatról elért összes Azure Cosmos DB fióknál.

1. Távolítsa el az alhálózathoz tartozó IP-tűzfalszabály szabályát a Azure Cosmos DB fiók tűzfalszabály-szabályai alapján.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Íme néhány gyakori kérdés a virtuális hálózatok hozzáférésének konfigurálásáról:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>A notebookok és a Mongo/Cassandra shellek jelenleg kompatibilisek Virtual Network engedélyezett fiókokkal?

A [Mongo-rendszerhéj](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) és a [Cassandra Shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) -integrációk a Cosmos db adatkezelő és a [Jupyter Notebooks szolgáltatás](./cosmosdb-jupyter-notebooks.md)esetében nem támogatottak a VNET-hozzáférés esetén. Ez jelenleg aktív fejlesztés alatt áll.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Megadhatom a virtuális hálózati szolgáltatás végpontját és az IP-hozzáférés-vezérlési házirendet egy Azure Cosmos-fiókon? 

A virtuális hálózati szolgáltatás végpontját és egy IP-hozzáférés-vezérlési házirendet (más néven tűzfalat) is engedélyezhet az Azure Cosmos-fiókjában. Ez a két funkció kiegészíti és együttesen biztosítja az Azure Cosmos-fiók elkülönítését és biztonságát. Az IP-tűzfal használatával biztosíthatja, hogy a statikus IP-címek hozzáférhessenek a fiókjához. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hogyan korlátozza a virtuális hálózaton belüli alhálózat elérését? 

Két lépés szükséges az Azure Cosmos-fiókhoz való hozzáférés korlátozásához egy alhálózaton. Először is engedélyezheti az alhálózatról érkező adatforgalmat, hogy az alhálózatot és a virtuális hálózati identitást Azure Cosmos DBba vigye. Ezt úgy teheti meg, hogy engedélyezi a szolgáltatási végpontot Azure Cosmos DB az alhálózaton. A következő lépés egy olyan szabály hozzáadása az Azure Cosmos-fiókban, amely ezt az alhálózatot adja meg forrásként, amelyből a fiók elérhető.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>A virtuális hálózati ACL-ek és az IP-tűzfal elutasítja a kérelmeket és a kapcsolatokat? 

Az IP-tűzfal vagy a virtuális hálózati hozzáférési szabályok hozzáadásakor a rendszer csak az engedélyezett forrásokból érkező kérelmeket fogadja el érvényes válaszként. A többi kérelem elutasítása 403 (tiltott). Fontos, hogy megkülönböztetni az Azure Cosmos-fiók tűzfalát a kapcsolódási szintű tűzfallal. A forrás továbbra is csatlakozhat a szolgáltatáshoz, és maga a kapcsolat nem kerül elutasításra.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>A rendszer megkezdte a kérések letiltását, amikor Engedélyeztem a szolgáltatási végpontot az alhálózaton való Azure Cosmos DB. Mi történt?

Ha a Azure Cosmos DB szolgáltatás végpontja engedélyezve van az alhálózaton, akkor a fiók a nyilvános IP-címről a virtuális hálózatra és az alhálózatra irányuló forgalom forrása. Ha az Azure Cosmos-fiók csak IP-alapú tűzfallal rendelkezik, akkor a szolgáltatással kompatibilis alhálózatról érkező forgalom már nem felel meg az IP-tűzfalszabályok feltételeinek, ezért a rendszer elutasítja. Ugorjon át az IP-alapú tűzfalról a virtuális hálózati hozzáférés-vezérlésre való zökkenőmentes Migrálás lépéseire.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>További Azure RBAC-engedélyek szükségesek az Azure Cosmos-fiókokhoz a VNET Service-végpontokkal?

Miután hozzáadta a VNet szolgáltatás-végpontokat egy Azure Cosmos-fiókhoz, a Fiókbeállítások módosítása érdekében hozzá kell férnie az `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` Azure Cosmos-fiókban konfigurált összes virtuális hálózatok művelethez. Erre az engedélyre azért van szükség, mert az engedélyezési folyamat érvényesíti az erőforrásokhoz való hozzáférést (például az adatbázist és a virtuális hálózati erőforrásokat) a tulajdonságok kiértékelése előtt.
 
Az engedélyezés ellenőrzi a VNet erőforrás-művelet engedélyét akkor is, ha a felhasználó nem ad meg VNET ACL-eket az Azure CLI használatával. Az Azure Cosmos-fiók vezérlő síkja jelenleg az Azure Cosmos-fiók teljes állapotának beállítását támogatja. A vezérlési sík hívásának egyik paramétere a következő: `virtualNetworkRules` . Ha ez a paraméter nincs megadva, az Azure CLI beolvassa az adatbázis lekérését, `virtualNetworkRules` és ezt az értéket használja a frissítési hívásban.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Az elérhető virtuális hálózatok hozzáférhetnek az Azure Cosmos-fiókhoz is? 
Csak a virtuális hálózat és az Azure Cosmos-fiókhoz hozzáadott alhálózatok férnek hozzá. A társ virtuális hálózatok nem férhetnek hozzá a fiókhoz, amíg a virtuális hálózatok alhálózatai hozzá nem kerülnek a fiókhoz.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Legfeljebb hány alhálózat fér hozzá egyetlen Cosmos-fiókhoz? 
Jelenleg legfeljebb 256 alhálózat lehet egy Azure Cosmos-fiókhoz.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Engedélyezhető a hozzáférés a VPN-ről és az expressz útvonalról? 
Ahhoz, hogy az Azure Cosmos-fiókot a helyszíni expressz útvonalon keresztül lehessen elérni, engedélyeznie kell a Microsoft-partneri kapcsolatot. Az IP-tűzfal vagy a virtuális hálózat hozzáférési szabályainak megadása után hozzáadhatja a Microsoft-társhoz használt nyilvános IP-címeket az Azure Cosmos-fiók IP-tűzfalán, hogy engedélyezze a helyszíni szolgáltatások hozzáférését az Azure Cosmos-fiókhoz. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Frissíteni kell a hálózati biztonsági csoportok (NSG) szabályait? 
A NSG szabályok a virtuális hálózattal rendelkező alhálózatok és azok közötti kapcsolatok korlátozására szolgálnak. Amikor szolgáltatási végpontot ad hozzá Azure Cosmos DBhoz az alhálózathoz, nincs szükség a kimenő kapcsolat megnyitására az Azure Cosmos-fiók NSG. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Elérhetőek-e a szolgáltatási végpontok az összes virtuális hálózatok?
Nem, csak Azure Resource Manager virtuális hálózatokon engedélyezhető a szolgáltatás végpontja. A klasszikus virtuális hálózatok nem támogatják a szolgáltatási végpontokat.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>"Elfogadom a kapcsolatokat a nyilvános Azure-adatközpontokban", ha a szolgáltatás-végponti hozzáférés engedélyezve van a Azure Cosmos DBhoz?  
Erre csak akkor van szükség, ha azt szeretné, hogy a Azure Cosmos DB-fiókját más Azure-szolgáltatások, például az Azure-beli adat-előállító, az Azure Cognitive Search vagy bármely, az adott Azure-régióban üzembe helyezett szolgáltatás hozzáférhessen.

## <a name="next-steps"></a>Következő lépések

* Ha Azure Cosmos DB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatását](how-to-configure-firewall.md) ismertető cikket.
