---
title: Hálózati Azure Files végpontok konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az Azure File hálózati végpontokat.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42e83facec7817b6588bf69977fea5ab74b6b10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759878"
---
# <a name="configuring-azure-files-network-endpoints"></a>Hálózati Azure Files konfigurálása

Azure Files két fő végponttípust biztosít az Azure-fájlmegosztások eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel és a világon bárhonnan elérhetők.
- Privát végpontok, amelyek egy virtuális hálózaton belül léteznek, és a virtuális hálózat címterületének magánhálózati IP-címével vannak.

Az Azure Storage-fiókban nyilvános és privát végpontok is vannak. A tárfiókok olyan felügyeleti szerkezetek, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe.

Ez a cikk a tárfiók végpontjainak konfigurálásán foglalkozik az Azure-fájlmegosztás közvetlen eléréséhez. A jelen dokumentumban megadott részletek nagy része arra is vonatkozik, hogy a Azure File Sync hogyan működik együtt a tárfiók nyilvános és privát végpontjaival, de az Azure File Sync-környezetek hálózati szempontjaival kapcsolatos további információkért lásd: Azure File Sync proxy- és tűzfalbeállítások [konfigurálása.](../file-sync/file-sync-firewall-and-proxy.md)

Javasoljuk, [Azure Files](storage-files-networking-overview.md) útmutató elolvasása előtt olvassa el a hálózati szempontokat.

## <a name="prerequisites"></a>Előfeltételek

- Ez a cikk feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, először [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) hozzon létre egy ingyenes fiókot.
- Ez a cikk feltételezi, hogy már létrehozott egy Azure-fájlmegosztást egy olyan tárfiókban, amelyhez csatlakozni szeretne a helyszínről. Az Azure-fájlmegosztások létrehozásáról az [Azure-fájlmegosztások létrehozása cikkből olvashat.](storage-how-to-create-file-share.md)
- Ha az alkalmazást szeretné Azure PowerShell, [telepítse a legújabb verziót.](/powershell/azure/install-az-ps)
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)

## <a name="endpoint-configurations"></a>Végpontkonfigurációk

A végpontokat konfigurálhatja úgy, hogy korlátozzák a tárfiókhoz való hálózati hozzáférést. A tárfiókhoz való hozzáférést kétféle módszer létezik egy virtuális hálózatra korlátozva:

- [Hozzon létre egy vagy több privát végpontot a tárfiókhoz,](#create-a-private-endpoint)  és korlátozza a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokról származó forgalom fér hozzá a tárfiókon belüli Azure-fájlmegosztáshoz.
- [Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra.](#restrict-public-endpoint-access) Ez a szolgáltatásvégpontnak nevezett virtuális hálózat *képességével működik.* Ha egy szolgáltatásvégponton keresztül korlátozza a tárfiók forgalmát, akkor is a nyilvános IP-címen keresztül fér hozzá a tárfiókhoz, de a hozzáférés csak a konfigurációban megadott helyekről lehetséges.

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ha privát végpontot hoz létre a tárfiókhoz, az a következő Azure-erőforrások üzembe helyezését eredményezi:

- **Privát végpont:** A tárfiók privát végpontját képviselő Azure-erőforrás. Ez egy tárfiókot és egy hálózati adaptert összekapcsoló erőforrás.
- **Hálózati adapter (NIC):** Az a hálózati adapter, amely magánhálózati IP-címet tart fenn a megadott virtuális hálózaton/alhálózaton belül. Ez pontosan ugyanaz az erőforrás, amelyet a rendszer a virtuális gép üzembe helyezésekor helyez üzembe, azonban ahelyett, hogy egy virtuális géphez rendeli, a privát végpont tulajdonában van.
- **Privát DNS-zóna:** Ha még soha nem helyezett üzembe privát végpontot ehhez a virtuális hálózathoz, egy új privát DNS-zóna lesz üzembe helyezni a virtuális hálózathoz. Egy DNS A rekord is létrejön az ebben a DNS-zónában található tárfiókhoz. Ha már üzembe helyezett egy privát végpontot ebben a virtuális hálózatban, a tárfiók új A rekordja lesz hozzáadva a meglévő DNS-zónához. A DNS-zóna üzembe helyezése nem kötelező, de erősen ajánlott, és kötelező, ha az Azure-fájlmegosztásokat egy AD-szolgáltatásnévvel vagy a FileREST API-val telepíti.

> [!Note]  
> Ez a cikk a tárfiók DNS-utótagját használja a nyilvános Azure-régiókhoz(). `core.windows.net` Ez a megjegyzés az Olyan Azure szuverén felhőkre is vonatkozik, mint az Azure US Government-felhő és az Azure China-felhő – csak helyettesítse be a környezetének megfelelő utótagokat. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Kapcsolat ellenőrzése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha virtuális géppel rendelkezik a virtuális hálózaton belül, vagy konfigurálta a DNS-továbbítást a [CONFIGURing DNS forwarding for Azure Files](storage-files-networking-dns.md)(DNS-továbbítás konfigurálása a Azure Files-hoz) című részben leírtak szerint, a következő parancsok PowerShellből, parancssorból vagy terminálból (Windows, Linux vagy macOS rendszeren) való futtatásával tesztelheti, hogy a privát végpont megfelelően lett-e beállítva. A helyére `<storage-account-name>` be kell helyettesítenie a megfelelő tárfióknevet:

```
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő kimenetnek kell látsza lennie, ahol a a virtuális hálózat privát végpontjának magánhálózati `192.168.0.5` IP-címe (a windowsos kimenet jelenik meg):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a virtuális hálózaton belül van egy virtuális gép, vagy konfigurálta a DNS-továbbítást a DNS-továbbítás konfigurálása a [Azure Files-hoz](storage-files-networking-dns.md)című résznek megfelelően, az alábbi parancsokkal tesztelheti, hogy a privát végpont megfelelően lett-e beállítva:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő kimenetnek kell látsza lennie, ahol a a virtuális hálózat privát `192.168.0.5` végpontjának magánhálózati IP-címe:

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

Ha a virtuális hálózaton belül van egy virtuális gép, vagy konfigurálta a DNS-továbbítást a DNS-továbbítás konfigurálása a [Azure Files-hoz](storage-files-networking-dns.md)című résznek megfelelően, az alábbi parancsokkal tesztelheti, hogy a privát végpont megfelelően lett-e beállítva:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő kimenetnek kell látszani, ahol a a virtuális hálózat privát `192.168.0.5` végpontjának magánhálózati IP-címe. Továbbra is a storageaccount.file.core.windows.net kell használnia a fájlmegosztás csatlakoztatását az elérési `privatelink` út helyett.

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

A nyilvános végpontok hozzáférésének korlátozásához először le kell tiltania a nyilvános végponthoz való általános hozzáférést. A nyilvános végponthoz való hozzáférés letiltása nincs hatással a privát végpontokra. A nyilvános végpont letiltása után kiválaszthat meghatározott hálózatokat vagy IP-címeket, amelyek továbbra is hozzáférhetnek. Általánosságban elmondható, hogy a tárfiókok legtöbb tűzfal-szabályzata egy vagy több virtuális hálózat hálózati hozzáférését korlátozza.

#### <a name="disable-access-to-the-public-endpoint"></a>A nyilvános végponthoz való hozzáférés letiltása

Ha a nyilvános végponthoz való hozzáférés le van tiltva, a tárfiók továbbra is elérhető a privát végpontjaikon keresztül. Ellenkező esetben a rendszer elutasítja a tárfiók nyilvános végpontjára vonatkozó érvényes kérelmeket, kivéve, ha azok kifejezetten engedélyezett [forrásból valók.](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks) 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>A nyilvános végponthoz való hozzáférés korlátozása adott virtuális hálózatokra

Ha a tárfiókot adott virtuális hálózatokra korlátozza, a nyilvános végpontra vonatkozó kéréseket a megadott virtuális hálózatokon belül engedélyezi. Ez a szolgáltatásvégpontnak nevezett virtuális hálózat *képességével működik.* Ez privát végpontokkal vagy anélkül is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Lásd még

- [Azure Files hálózati szempontok](storage-files-networking-overview.md)
- [DNS-továbbítás konfigurálása az Azure Fileshoz](storage-files-networking-dns.md)
- [A S2S VPN konfigurálása Azure Files](storage-files-configure-s2s-vpn.md)