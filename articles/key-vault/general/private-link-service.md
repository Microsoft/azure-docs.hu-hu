---
title: Integrálás az Azure Private Link szolgáltatással
description: Ismerje meg, hogyan integrálható Azure Key Vault az Azure Private link Service használatával
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: a41eb5b38b741f8bdde59f8a4f1e8de2b4767903
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832772"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>A Key Vault és az Azure Private Link integrálása

Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Előfeltételek

A Key Vault Azure Private Linktel való integrálásához a következőkre lesz szüksége:

- Key Vault.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton.
- Tulajdonosi vagy közreműködői engedélyek a Key vaulthoz és a virtuális hálózathoz.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A kulcstartó lehet egy másik régióban is.

A privát végpont egy magánhálózati IP-címet használ a virtuális hálózaton.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Magánhálózati kapcsolat létesítése Key Vault a Azure Portal használatával 

Először hozzon létre egy virtuális hálózatot a [virtuális hálózat létrehozása a Azure Portal használatával](../../virtual-network/quick-create-portal.md) című témakör lépéseit követve.

Ezután létrehozhat egy új kulcstartót, vagy létesíthet kapcsolatot egy meglévő kulcstartóval.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Új kulcstartó létrehozása és magánhálózati kapcsolat létesítése

Azure Key Vault az [Azure Portal](../general/quick-create-portal.md), az [Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell](../general/quick-create-powershell.md)használatával hozhat létre.

A Key Vault alapalapjainak konfigurálása után válassza a hálózatkezelés fület, és kövesse az alábbi lépéseket:

1. Válassza a magánhálózati végpont választógombot a hálózatkezelés lapon.
1. Privát végpont hozzáadásához kattintson a "+ Hozzáadás" gombra.

    ![Képernyőkép, amely az első képernyőt mutatja a kulcstartó létrehozásakor.](../media/private-link-service-1.png)
 
1. A privát végpont létrehozása panel "location" mezőjében válassza ki azt a régiót, amelyben a virtuális hálózat található. 
1. A "név" mezőben hozzon létre egy leíró nevet, amely lehetővé teszi a privát végpont azonosítását. 
1. Válassza ki azt a virtuális hálózatot és alhálózatot, amelyhez a privát végpontot létre kívánja hozni a legördülő menüből. 
1. Hagyja változatlanul az "integráció a saját DNS-zónával" beállítást.  
1. Válassza az OK gombot.

    ![A privát végpontok konfigurálásához fontos mezőket bemutató képernyőkép.](../media/private-link-service-8.png)
 
Ekkor megtekintheti a konfigurált privát végpontot. Most már lehetősége van a privát végpont törlésére és szerkesztésére. Válassza a "felülvizsgálat + létrehozás" gombot, és hozza létre a Key vaultot. A telepítés befejezéséhez 5-10 percet vesz igénybe. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Magánhálózati kapcsolat létesítése meglévő kulcstartóval

Ha már rendelkezik kulcstartóval, a következő lépések végrehajtásával hozhat létre privát kapcsolati kapcsolatot:

1. Jelentkezzen be az Azure Portalra. 
1. A keresősáv mezőbe írja be a "Key Vaults" kifejezést.
1. Válassza ki a kulcstárolót a listából, amelyhez privát végpontot szeretne hozzáadni.
1. Válassza a "hálózatkezelés" fület a beállítások alatt.
1. Válassza ki a privát Endpoint Connections fület az oldal tetején.
1. Válassza ki a "+ Private Endpoint" gombot az oldal tetején.

    ![A + privát végpont gombját megjelenítő képernyőkép.](../media/private-link-service-3.png)
    ![A privát végpontok létrehozásának képernyőjét bemutató képernyőkép.](../media/private-link-service-4.png)

A panel használatával bármely Azure-erőforráshoz létrehozhat egy privát végpontot. A legördülő menük segítségével kiválaszthat egy erőforrástípust, és kiválaszthat egy erőforrást a címtárban, vagy bármely Azure-erőforráshoz kapcsolódhat erőforrás-azonosító használatával. Hagyja változatlanul az "integráció a saját DNS-zónával" beállítást.  

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Magánhálózati kapcsolat létesítése Key Vault a parancssori felület használatával

### <a name="login-to-azure-cli"></a>Bejelentkezés az Azure CLI-be
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>A Microsoft. kulcstartó regisztrálása szolgáltatóként 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Új Key Vault létrehozása
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Key Vault tűzfal bekapcsolása
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --default-action deny
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Alhálózat hozzáadása
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Virtual Network házirendek letiltása 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>saját DNS zóna hozzáadása 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>saját DNS zóna összekapcsolása Virtual Network 
```console
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="add-private-dns-records"></a>saját DNS rekordok hozzáadása
```console
# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g $rg_name
az network private-dns record-set a add-record -g $rg_name -z "privatelink.vaultcore.azure.net" -n $vault_name -a $kv_network_interface_private_ip
az network private-dns record-set list -g $rg_name -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup $vault_name.vault.azure.net
nslookup $vault_name.privatelink.vaultcore.azure.net
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Privát végpont létrehozása (automatikus jóváhagyás) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Privát végpont létrehozása (manuális kérelem jóváhagyása) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Kapcsolat állapotának megjelenítése 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Privát kapcsolat kapcsolatának kezelése

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás, amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a megfelelő engedélyekkel rendelkező kapcsolódási kérelmet. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatás-nyújtási művelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| Nincs | Függőben | A kapcsolat manuálisan lett létrehozva, és jóváhagyásra vár a Private link erőforrás-tulajdonostól. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Privát végponti kapcsolatok kezelése Key Vault a Azure Portal használatával 

1. Jelentkezzen be az Azure Portalra.
1. A keresősáv mezőbe írja be a "Key Vaults" kifejezést.
1. Válassza ki a kezelni kívánt kulcstartót.
1. Válassza a "hálózatkezelés" fület.
1. Ha van függőben lévő kapcsolat, a kiépítési állapotban "függőben" állapotú kapcsolat jelenik meg. 
1. Válassza ki a jóváhagyni kívánt privát végpontot
1. Kattintson a jóváhagyás gombra.
1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy létező kapcsolat, válassza ki a kapcsolatot, és kattintson az "elutasítás" gombra.

    ![Kép](../media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Privát végponti kapcsolatok kezelése Key Vault az Azure CLI használatával

### <a name="approve-a-private-link-connection-request"></a>Magánhálózati kapcsolati kérelem jóváhagyása
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Magánhálózati kapcsolatra vonatkozó kérelem elutasítása
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Magánhálózati kapcsolati kérelem törlése
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e

Ellenőriznie kell, hogy a magánhálózati végpont erőforrásának ugyanazon alhálózatán belüli erőforrásai csatlakoznak-e a kulcstartóhoz egy magánhálózati IP-címen keresztül, és hogy rendelkeznek-e a megfelelő magánhálózati DNS-zónák integrálásával.

Először hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A "hálózatkezelés" lapon:

1. Határozza meg a virtuális hálózatot és az alhálózatot. Létrehozhat egy új virtuális hálózatot, vagy kijelölhet egy meglévőt is. Ha egy meglévőt választ ki, győződjön meg arról, hogy a régió megfelel.
1. Nyilvános IP-erőforrást kell megadni.
1. A "NIC hálózati biztonsági csoport" területen válassza a "nincs" lehetőséget.
1. A "terheléselosztás" területen válassza a "nem" lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Ha a Key Vault IP-címének nyilvános végponton keresztüli feloldásához futtatja az NS lookup parancsot, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Ha a Key Vault IP-címének feloldásához egy privát végponton futtatja az NS lookup parancsot, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

* Győződjön meg arról, hogy a magánhálózati végpont jóváhagyott állapotban van. 
    1. Ezt az Azure Portalon ellenőrizheti és javíthatja. Nyissa meg a Key Vault-erőforrást, és kattintson a Hálózat elemre. 
    2. Ezután válassza a Private Endpoint Connections fület. 
    3. Győződjön meg arról, hogy a kapcsolatok állapota jóváhagyva, a kiépítési állapot pedig sikeres. 
    4. Navigáljon a privát végpont erőforráshoz, és tekintse át ugyanezeket a tulajdonságokat, és ellenőrizze, hogy a virtuális hálózat megegyezik-e az Ön által használttal.

* Ellenőrizze, hogy van-e saját DNS zóna erőforrása. 
    1. Rendelkeznie kell egy saját DNS zóna-erőforrással a pontos névvel: privatelink.vaultcore.azure.net. 
    2. Ha szeretné megtudni, hogyan állíthatja be ezt a műveletet, tekintse meg a következő hivatkozást. [saját DNS zónák](../../dns/private-dns-privatednszone.md)
    
* Győződjön meg arról, hogy a Privát DNS-zóna nincs virtuális hálózathoz csatolva. Ez lehet a probléma, ha továbbra is a visszaadott nyilvános IP-címet kapja. 
    1. Ha a privát zóna DNS-je nincs a virtuális hálózathoz kapcsolva, a virtuális hálózatból származó DNS-lekérdezés a kulcstartó nyilvános IP-címét fogja visszaadni. 
    2. Navigáljon a Azure Portal saját DNS zóna erőforrásához, és kattintson a virtuális hálózati kapcsolatok lehetőségre. 
    4. A Key Vault hívásait végrehajtó virtuális hálózatnak szerepelnie kell a listáján. 
    5. Ha nincs ott, vegye fel. 
    6. A részletes lépésekért tekintse meg a következő dokumentum [hivatkozását Virtual Network saját DNS zónához](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Győződjön meg arról, hogy a saját DNS zónában nem hiányzik egy rekord a kulcstartóhoz. 
    1. Navigáljon a saját DNS zóna lapra. 
    2. Kattintson az Áttekintés elemre, és ellenőrizze, hogy szerepel-e egy A rekord a kulcstartó egyszerű nevével (pl. fabrikam). Ne adjon meg utótagot.
    3. Ne feledje el ellenőrizni a helyesírást, és hozza létre vagy javítsa az A rekordot. Használja a 3600 (1 óra) élettartam értéket. 
    4. Győződjön meg arról, hogy a helyes magánhálózati IP-címet adta meg. 
    
* Győződjön meg arról, hogy az a rekord megfelelő IP-címmel rendelkezik. 
    1. Az IP-címet a Privát végpont erőforrás megnyitásával erősítheti meg az Azure Portalon 
    2. Nyissa meg a Microsoft.Network/privateEndpoints erőforrást az Azure Portalon (ez nem a Key Vault-erőforrás)
    3. Az Áttekintés oldalon keresse meg a hálózati adaptert, majd kattintson a hivatkozásra. 
    4. A hivatkozás megjeleníti a hálózati adapter erőforrásának áttekintését, amely tartalmazza a tulajdonság magánhálózati IP-címét. 
    5. Ellenőrizze, hogy az a rekordban megadott helyes IP-cím-e.

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

> [!NOTE]
> Az előfizetések által engedélyezett privát végpontokkal rendelkező kulcstartók száma állítható korlát. Az alább látható korlát az alapértelmezett korlát. Ha korlátozni szeretné a szolgáltatás korlátozását, küldjön e-mailt a címre akv-privatelink@microsoft.com . Ezeket a kéréseket eseti alapon kell jóváhagyni.

**Díjszabás**: díjszabási információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

**Korlátozások**: a Azure Key Vault magánhálózati végpontja csak az Azure nyilvános régióiban érhető el.

**Privát végpontok maximális száma Key Vault**: 64.

**A privát végpontok által előfizetett kulcstartók alapértelmezett száma**: 400.

További információ [: Azure Private link Service: korlátozások](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](../../private-link/private-link-service-overview.md)
- További információ a [Azure Key Vault](overview.md)
