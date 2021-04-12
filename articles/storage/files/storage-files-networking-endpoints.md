---
title: Azure Files hálózati végpontok konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure file Network-végpontokat.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 079d7aa9b654a318c7269a41605c3e146b08f127
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621331"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files hálózati végpontok konfigurálása

Azure Files két fő típusú végpontot biztosít az Azure-fájlmegosztás eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és bárhonnan elérhetők a világ bármely pontján.
- Saját végpontok, amelyek egy virtuális hálózaton belül találhatók, és rendelkeznek magánhálózati IP-címmel a virtuális hálózat címterület területén belülről.

A nyilvános és a privát végpontok az Azure Storage-fiókban találhatók. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Storage-fiók végpontját az Azure-fájlmegosztás közvetlen eléréséhez. A jelen dokumentumban ismertetett részletek többsége arra is vonatkozik, hogy a Azure File Sync hogyan működik együtt a Storage-fiókhoz tartozó nyilvános és magánhálózati végpontokkal, azonban a Azure File Sync központi telepítés hálózatkezelési szempontjaival kapcsolatos további információkért lásd: [Azure file Sync proxy és tűzfal beállításainak konfigurálása](storage-sync-files-firewall-and-proxy.md).

Javasoljuk, hogy olvassa el a [Azure Files hálózati megfontolásokat](storage-files-networking-overview.md) az útmutató elolvasása előtt.

## <a name="prerequisites"></a>Előfeltételek

- Ez a cikk azt feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.
- Ez a cikk azt feltételezi, hogy már létrehozott egy Azure-fájlmegosztást egy olyan Storage-fiókban, amelyhez csatlakozni szeretne a helyszíni környezetből. Az Azure-fájlmegosztás létrehozásáról az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című témakörben olvashat bővebben.
- Ha Azure PowerShell szeretne használni, [telepítse a legújabb verziót](/powershell/azure/install-az-ps).
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Végpont-konfigurációk

A végpontokat beállíthatja úgy, hogy korlátozza a hálózati hozzáférést a Storage-fiókhoz. A Storage-fiókokhoz való hozzáférés korlátozásának két módja van:

- [Hozzon létre egy vagy több privát végpontot a Storage-fiókhoz](#create-a-private-endpoint)  , és korlátozza a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhessenek hozzá az Azure-fájlmegosztás számára a Storage-fiókon belül.
- [Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra](#restrict-public-endpoint-access). Ez a *szolgáltatás végpontok* nevű virtuális hálózat funkciójának használatával működik. Ha egy szolgáltatási végponton keresztül korlátozza a forgalmat egy Storage-fiókra, akkor továbbra is a nyilvános IP-címen keresztül éri el a Storage-fiókot, de a hozzáférés csak a konfigurációban megadott helyekről lehetséges.

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ha létrehoz egy privát végpontot a Storage-fiókjához, a következő Azure-erőforrásokat fogja telepíteni:

- **Privát végpont**: a Storage-fiók privát végpontját jelképező Azure-erőforrás. Ezt olyan erőforrásként tekintheti meg, amely összekapcsolja a Storage-fiókot és egy hálózati adaptert.
- **Hálózati adapter (NIC)**: a megadott virtuális hálózaton/alhálózaton belüli magánhálózati IP-címet fenntartó hálózati adapter. Ez ugyanaz az erőforrás, amelyet a rendszer a virtuális gépek telepítésekor üzembe helyez, azonban a virtuális géphez való hozzárendelés helyett a magánhálózati végpont tulajdonosa.
- **Privát DNS-zóna**: Ha korábban még nem telepített privát végpontot ehhez a virtuális hálózathoz, a rendszer új magánhálózati DNS-zónát helyez üzembe a virtuális hálózathoz. Az ebben a DNS-zónában található Storage-fiókhoz tartozó DNS-rekord is létrejön. Ha már telepített egy privát végpontot ebben a virtuális hálózatban, a rendszer új rekordot ad a Storage-fiókhoz a meglévő DNS-zónához. A DNS-zónák üzembe helyezése nem kötelező, azonban kifejezetten ajánlott, és kötelező, ha az Azure-fájlmegosztást egy AD-szolgáltatással vagy a legelső API használatával csatlakoztatja.

> [!Note]  
> Ez a cikk a Storage-fiók DNS-utótagját használja az Azure-beli nyilvános régiókban `core.windows.net` . Ez a kommentár az Azure szuverén felhőkre is vonatkozik, mint például az Azure US government Cloud és az Azure China Cloud – csak a megfelelő utótagokat helyettesíti a környezetében. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Kapcsolat ellenőrzése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbítás beállítása című témakörben leírtak szerint, akkor tesztelheti, hogy a saját végpontja helyesen van-e beállítva. Ehhez futtassa a következő parancsokat a powershellből, a parancssorból vagy a terminálból (Windows, Linux vagy MacOS rendszeren működik). `<storage-account-name>`A nevet a megfelelő Storage-fiók nevével kell helyettesíteni:

```
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe (a Windowshoz megjelenített kimenet):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbításának beállítása című témakörben leírtak szerint, ellenőrizheti, hogy a saját végpontja helyesen van-e beállítva a következő parancsokkal:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbításának beállítása című témakörben leírtak szerint, ellenőrizheti, hogy a saját végpontja helyesen van-e beállítva a következő parancsokkal:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe. Az elérési út helyett továbbra is a storageaccount.file.core.windows.net-t kell használnia a fájlmegosztás csatlakoztatásához `privatelink` .

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Nyilvános végpontok hozzáférésének korlátozása

A nyilvános végpontok elérésének korlátozásához először le kell tiltania a nyilvános végponthoz való általános hozzáférést. A nyilvános végponthoz való hozzáférés letiltása nem érinti a privát végpontokat. A nyilvános végpont letiltása után kiválaszthatja azokat az adott hálózatokat vagy IP-címeket, amelyek továbbra is hozzáférhetnek. Általánosságban elmondható, hogy egy Storage-fiókhoz tartozó tűzfalszabályok többsége korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz.

#### <a name="disable-access-to-the-public-endpoint"></a>A nyilvános végpont elérésének letiltása

Ha a nyilvános végponthoz való hozzáférés le van tiltva, a Storage-fiók továbbra is elérhető a saját végpontján keresztül. Ellenkező esetben a rendszer elutasítja a Storage-fiók nyilvános végpontjának érvényes kérelmeit, hacsak nem [kifejezetten engedélyezett forrásból](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)származnak. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>A nyilvános végponthoz való hozzáférés korlátozása adott virtuális hálózatokra

Ha a Storage-fiókot adott virtuális hálózatokra korlátozza, a megadott virtuális hálózatokról érkező kéréseket engedélyez a nyilvános végpontnak. Ez a *szolgáltatás végpontok* nevű virtuális hálózat funkciójának használatával működik. Ez privát végpontokkal vagy anélkül is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Lásd még

- [Azure Files hálózati megfontolások](storage-files-networking-overview.md)
- [DNS-továbbítás konfigurálása az Azure Fileshoz](storage-files-networking-dns.md)
- [S2S VPN konfigurálása Azure Fileshoz](storage-files-configure-s2s-vpn.md)