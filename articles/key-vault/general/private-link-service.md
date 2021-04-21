---
title: Integrálás az Azure Private Link szolgáltatással
description: Ismerje meg, hogyan integrálható Azure Key Vault a Azure Private Link Service-sel
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749541"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>A Key Vault és az Azure Private Link integrálása

Azure Private Link Szolgáltatás lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban.

Az Azure privát végpontok olyan hálózati adapterek, amelyek privát módon és biztonságosan csatlakoztatják a virtuális Azure Private Link. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan behozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé minden forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra, illetve nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforráspéldányhoz, így a hozzáférés-vezérlésben a legmagasabb szintű részletességet használhatja.

További információ: [Mi a Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Előfeltételek

A key vault és a Azure Private Link integrálásához a következőkre lesz szüksége:

- Egy kulcstartó.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózatban.
- Tulajdonosi vagy közreműködői engedélyek a kulcstartóhoz és a virtuális hálózathoz.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Amikor kiválaszt egy régiót a privát végponthoz a portálon, az automatikusan csak az abban a régióban lévő virtuális hálózatokat szűri. A kulcstartó másik régióban is lehet.

A privát végpont magánhálózati IP-címet használ a virtuális hálózatban.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Hozzon létre privát kapcsolati kapcsolatot a Key Vault a Azure Portal 

Először hozzon létre egy virtuális hálózatot a Virtuális hálózat létrehozása [az](../../virtual-network/quick-create-portal.md) Azure Portal

Ezután létrehozhat egy új kulcstartót, vagy létrehozhat egy privát kapcsolati kapcsolatot egy meglévő kulcstartóval.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Új kulcstartó létrehozása és privát kapcsolat létrehozása

Létrehozhat egy új kulcstartót az [Azure Portal,](../general/quick-create-portal.md) [az Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell.](../general/quick-create-powershell.md)

A Kulcstartó alapjainak konfigurálása után válassza a Hálózat lapot, és kövesse az alábbi lépéseket:

1. Jelölje be a Privát végpont választógombot a Hálózat lapon.
1. Privát végpont hozzáadásához kattintson a "+ Hozzáadás" gombra.

    ![A "Kulcstartó létrehozása" oldal Hálózat lapját bemutató képernyőkép.](../media/private-link-service-1.png)
 
1. A Privát végpont létrehozása panel "Hely" mezőjében válassza ki azt a régiót, amelyben a virtuális hálózat található. 
1. A "Név" mezőben hozzon létre egy leíró nevet, amely lehetővé teszi a privát végpont azonosítását. 
1. A legördülő menüből válassza ki azt a virtuális hálózatot és alhálózatot, amelyből létre szeretné hozatni ezt a privát végpontot. 
1. Hagyja változatlanul az "integráció a privát zóna DNS-sel" beállítást.  
1. Válassza az "OK" lehetőséget.

    ![A "Privát végpont létrehozása" oldalt a kiválasztott beállításokkal bemutató képernyőkép.](../media/private-link-service-8.png)
 
Most már láthatja a konfigurált privát végpontot. Most már törölheti és szerkesztheti ezt a privát végpontot. Válassza az Áttekintés + létrehozás gombot, és hozza létre a kulcstartót. Az üzembe helyezés 5–10 percet vehet igénybe. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Privát kapcsolat létrehozása meglévő kulcstartóhoz

Ha már rendelkezik kulcstartóval, a következő lépésekkel hozhat létre privát kapcsolati kapcsolatot:

1. Jelentkezzen be az Azure Portalra. 
1. A keresősávba írja be a "key vaults" (kulcstartók) parancsot.
1. Válassza ki a kulcstartót a listából, amelyhez hozzá szeretne adni egy privát végpontot.
1. Válassza a "Hálózat" lapot a Beállítások alatt
1. Válassza a Privát végpont kapcsolatai lapot az oldal tetején
1. Válassza a "+ Privát végpont" gombot az oldal tetején.

    ![A Hálózat lap "+ Privát végpont" gombját bemutató képernyőkép.](../media/private-link-service-3.png)
    ![A "Privát végpont létrehozása (előzetes verzió) lap Alapvető beállítások lapját bemutató képernyőkép.](../media/private-link-service-4.png)

Ezen a panelen bármely Azure-erőforráshoz létrehozhat egy privát végpontot. A legördülő menük használatával kiválaszthat egy erőforrástípust, és kiválaszthat egy erőforrást a címtárban, vagy bármely Azure-erőforráshoz csatlakozhat egy erőforrás-azonosítóval. Hagyja változatlanul az "integráció a privát zóna DNS-sel" beállítást.  

![Képernyőkép egy privát végpont aktuális panelről való hozzáadásáról. ](../media/private-link-service-3.png)
 ![ Képernyőkép a "Privát végpont létrehozása (előzetes verzió) lapról.](../media/private-link-service-4.png)

Privát végpont létrehozásakor a kapcsolatot jóvá kell hagyni. Ha az erőforrás, amelyhez privát végpontot hoz létre, a címtárban található, akkor jóváhagyhatja a kapcsolatkérést, ha rendelkezik a megfelelő engedélyekkel; Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, amíg az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állam van:

| Szolgáltatás-szolgáltatási művelet | Szolgáltatás fogyasztói privát végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolat manuálisan jön létre, és függőben van a Private Link erőforrás tulajdonosa. |
| Jóváhagyás | Approved | A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A kapcsolatot a privát kapcsolati erőforrás tulajdonosa elutasította. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívsá válik, és törölni kell a törléshez. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Privát végpontkapcsolat kezelése a Key Vault használatával Azure Portal 

1. Jelentkezzen be az Azure Portalra.
1. A keresősávba írja be a "key vaults" (kulcstartók) parancsot.
1. Válassza ki a kezelni kívánt kulcstartót.
1. Válassza a "Hálózat" lapot.
1. Ha vannak függőben lévő kapcsolatok, megjelenik egy kapcsolat, amely kiépítési állapotban a "Függőben" állapotot sorolja fel. 
1. Válassza ki a jóváhagyni kívánt privát végpontot
1. Válassza a Jóváhagyás gombot.
1. Ha szeretne privát végponti kapcsolatokat elutasítani, akár függőben lévő kérésről, akár meglévő kapcsolatról van szó, válassza ki a kapcsolatot, és kattintson az Elutasítás gombra.

    ![Kép](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Hozzon létre privát kapcsolati kapcsolatot a Key Vault cli használatával (kezdeti beállítás)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Privát végpont létrehozása (automatikus jóváhagyás) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Privát végpont létrehozása (manuális jóváhagyás kérése) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Az Private Link kapcsolatok kezelése

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Rekord saját DNS hozzáadása
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>A privát kapcsolat kapcsolatának ellenőrzése

Ellenőrizze, hogy a privát végpont erőforrásának ugyanazon alhálózatán belüli erőforrások magánhálózati IP-címen keresztül csatlakoznak-e a kulcstartóhoz, és hogy a megfelelő privát DNS-zónaintegrációval rendelkezik-e.

Először hozzon létre egy virtuális gépet a Windows rendszerű virtuális gép [létrehozása a](../../virtual-machines/windows/quick-create-portal.md) Azure Portal

A "Hálózat" lapon:

1. Adja meg a Virtuális hálózat és az Alhálózat értéket. Létrehozhat egy új virtuális hálózatot, vagy kiválaszthat egy meglévőt. Ha egy meglévőt választ ki, ellenőrizze, hogy a régió egyezik-e.
1. Adjon meg egy nyilvános IP-erőforrást.
1. A "Hálózati adapter hálózati biztonsági csoport" mezőben válassza a "Nincs" lehetőséget.
1. A "Terheléselosztás" mezőben válassza a "Nem" lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Ha az ns lookup parancs futtatásával oldja fel egy kulcstartó IP-címét egy nyilvános végponton, az alábbihoz hasonló eredményt fog látni:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Ha az ns lookup parancs futtatásával oldja fel egy kulcstartó IP-címét egy privát végponton, az alábbihoz hasonló eredményt fog látni:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

* Ellenőrizze, hogy a privát végpont jóváhagyott állapotban van-e. 
    1. Ezt az Azure Portalon ellenőrizheti és javíthatja. Nyissa meg a Key Vault-erőforrást, és kattintson a Hálózat elemre. 
    2. Ezután válassza a Privát végponti kapcsolatok lapot. 
    3. Győződjön meg arról, hogy a kapcsolat állapota Jóváhagyva, a kiépítési állapot pedig Sikeres. 
    4. A privát végpont erőforrásához is navigálhat, és áttekintheti ugyanezeket a tulajdonságokat, és ellenőrizheti, hogy a virtuális hálózat megegyezik-e az Ön által használt hálózattal.

* Ellenőrizze, hogy van-e saját DNS Zóna erőforrás. 
    1. Egy névvel saját DNS erőforrásnak kell lennie: privatelink.vaultcore.azure.net. 
    2. Ennek beállítását az alábbi hivatkozáson keresztül saját állíthatja be. [saját DNS zónák](../../dns/private-dns-privatednszone.md)
    
* Győződjön meg arról, hogy a Privát DNS-zóna nincs virtuális hálózathoz csatolva. Ez akkor lehet a probléma, ha továbbra is a nyilvános IP-címet ad vissza. 
    1. Ha a privát dns-zóna nincs a virtuális hálózathoz kapcsolva, a virtuális hálózatról származó DNS-lekérdezés visszaadja a kulcstartó nyilvános IP-címét. 
    2. Lépjen a saját DNS zóna erőforráshoz a Azure Portal, és kattintson a virtuális hálózati kapcsolatok lehetőségre. 
    4. A kulcstartóhoz hívásokat végző virtuális hálózatnak fel kell sorolva lennie. 
    5. Ha nincs ott, adja hozzá. 
    6. Részletes lépésekért tekintse meg a Következő dokumentumban: [Hivatkozás Virtual Network a saját DNS zónához](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Ellenőrizze, hogy az saját DNS zónában nem hiányzik-e A rekord a kulcstartóhoz. 
    1. Lépjen a Saját DNS lapra. 
    2. Kattintson az Áttekintés elemre, és ellenőrizze, hogy szerepel-e egy A rekord a kulcstartó egyszerű nevével (pl. fabrikam). Ne adjon meg utótagot.
    3. Ne feledje el ellenőrizni a helyesírást, és hozza létre vagy javítsa az A rekordot. Használja a 3600 (1 óra) élettartam értéket. 
    4. Győződjön meg arról, hogy a megfelelő magánhálózati IP-címet adja meg. 
    
* Ellenőrizze, hogy az A rekord megfelelő IP-címmel rendelkezik-e. 
    1. Az IP-címet úgy erősítheti meg, ha megnyitja a Privát végpont erőforrást a Azure Portal.
    2. Nyissa meg a Microsoft.Network/privateEndpoints erőforrást az Azure Portalon (ez nem a Key Vault-erőforrás)
    3. Az áttekintő lapon keresse meg a Hálózati adapter et, és kattintson erre a hivatkozásra. 
    4. A hivatkozás a Hálózati adapter erőforrás áttekintését mutatja, amely a Magánhálózati IP-cím tulajdonságot tartalmazza. 
    5. Ellenőrizze, hogy ez az A rekordban megadott megfelelő IP-cím-e.

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

> [!NOTE]
> Az előfizetésenként engedélyezett privát végpontokkal rendelkező kulcstartók száma módosítható korlát. Az alábbiakban látható korlát az alapértelmezett korlát. Ha korlátozni szeretné a szolgáltatás korlátozását, küldjön egy e-mailt a akv-privatelink@microsoft.com címre. Ezeket a kérelmeket eseti alapon fogjuk jóváhagyni.

**Díjszabás:** Díjszabási információkért lásd: [Azure Private Link díjszabása.](https://azure.microsoft.com/pricing/details/private-link/)

**Korlátozások:** A Azure Key Vault privát végpontja csak nyilvános Azure-régiókban érhető el.

**Privát végpontok** maximális száma Key Vault: 64.

**Alapértelmezett kulcstartók száma előfizetésenként privát végpontokkal:** 400.

További információ: [Azure Private Link szolgáltatás korlátozásai](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Private Link](../../private-link/private-link-service-overview.md)
- További információ [a](overview.md) Azure Key Vault
