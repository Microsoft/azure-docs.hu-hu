---
title: Privát végpontok használata
titleSuffix: Azure Storage
description: A privát végpontok áttekintése a virtuális hálózatok Storage-fiókjainak biztonságos eléréséhez.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600462"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Privát végpontok használata az Azure Storage-hoz

Az Azure Storage-fiókokhoz [privát végpontokat](../../private-link/private-endpoint-overview.md) is használhat, amelyek lehetővé teszik a virtuális hálózat (VNet) ügyfelei számára, hogy biztonságosan hozzáférjenek az adataihoz egy [privát kapcsolaton](../../private-link/private-link-overview.md)keresztül. A privát végpont IP-címet használ a Storage-fiók szolgáltatás VNet. A VNet és a Storage-fiók ügyfelei közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül halad, ami kiküszöböli a nyilvános internetről való kitettséget.

Privát végpontok használata a Storage-fiókhoz a következőket teszi lehetővé:

- A Storage-fiók biztonságossá tételéhez konfigurálja a Storage-tűzfalat úgy, hogy az a tárolási szolgáltatás nyilvános végpontján lévő összes kapcsolatot letiltsa.
- A virtuális hálózat (VNet) biztonságának növelésével letilthatja a VNet kiszűrése adatait.
- Biztonságosan csatlakozhat a Storage-fiókokhoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../../expressroute/expressroute-locations.md) [-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a VNet.

## <a name="conceptual-overview"></a>Fogalmi áttekintés

![Az Azure Storage privát végpontjai – áttekintés](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

A privát végpontok egy speciális hálózati adapterek egy Azure-szolgáltatáshoz a [Virtual Networkban](../../virtual-network/virtual-networks-overview.md) (VNet). Amikor létrehoz egy privát végpontot a Storage-fiókjához, biztonságos kapcsolatot biztosít a VNet található ügyfelek és a tároló között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a tárolási szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A VNet lévő alkalmazások zökkenőmentesen kapcsolódhatnak a tárolási szolgáltatáshoz a magánhálózati végponton keresztül, **ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni** fognak. A magánhálózati végpontok a Storage-fiók által támogatott összes protokollal használhatók, beleértve a REST és az SMB protokollt is.

A magánhálózati végpontok olyan alhálózatokban hozhatók létre, amelyek [szolgáltatási végpontokat](../../virtual-network/virtual-network-service-endpoints-overview.md)használnak. Az alhálózaton lévő ügyfelek így egy privát végpont használatával csatlakozhatnak egy Storage-fiókhoz, míg más szolgáltatás-végpontok használatával is hozzáférhetnek.

Ha létrehoz egy privát végpontot a virtuális hálózaton található egyik tárolási szolgáltatáshoz, a rendszer egy hozzájárulási kérést küld a tárfiók tulajdonosának jóváhagyás céljából. Ha a privát végpont létrehozását kérő felhasználó a Storage-fiók tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja.

A Storage-fiók tulajdonosai a [Azure Portal](https://portal.azure.com)a Storage-fiókhoz tartozó *privát végpontok* lapján kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

> [!TIP]
> Ha csak a privát végponton keresztül szeretné korlátozni a Storage-fiók elérését, konfigurálja a tárolási tűzfalat a nyilvános végponton keresztüli hozzáférés megtagadásához vagy vezérléséhez.

A Storage-fiók úgy is biztosítható, hogy csak a VNet érkező kapcsolatokat fogadja el, ha úgy [konfigurálja a tárolási tűzfalat](storage-network-security.md#change-the-default-network-access-rule) , hogy alapértelmezés szerint megtagadja a hozzáférést a nyilvános végponton keresztül. Nincs szükség olyan tűzfalszabályre, amely engedélyezi a forgalmat egy privát végponttal rendelkező VNet, mivel a tárolási tűzfal csak a nyilvános végponton keresztüli hozzáférést vezérli. A magánhálózati végpontok Ehelyett az alhálózatokhoz való hozzáférést biztosító engedélyezési folyamaton alapulnak.

> [!NOTE]
> A Storage-fiókok közötti Blobok másolásakor az ügyfélnek mindkét fiókhoz hálózati hozzáféréssel kell rendelkeznie. Tehát ha úgy dönt, hogy csak egy fiókhoz (a forráshoz vagy a célhelyhez) szeretne privát hivatkozást használni, győződjön meg arról, hogy az ügyfélnek van hálózati hozzáférése a másik fiókhoz. További információ a hálózati hozzáférés konfigurálásának egyéb módjairól: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md?toc=/azure/storage/blobs/toc.json). 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Privát végpont létrehozása

Ha privát végpontot szeretne létrehozni az Azure Portal használatával, tekintse meg a következő témakört: [magánhálózati kapcsolat létrehozása Storage-fiókhoz a Azure Portal](../../private-link/tutorial-private-endpoint-storage-portal.md).

Ha privát végpontot szeretne létrehozni a PowerShell vagy az Azure CLI használatával, tekintse meg a fenti cikkek egyikét. Mindkettő egy Azure-webalkalmazást céloz meg célként, de a privát kapcsolatok létrehozásának lépései megegyeznek egy Azure Storage-fiókkal.

- [Privát végpont létrehozása az Azure CLI-vel](../../private-link/create-private-endpoint-cli.md)

- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)



Privát végpont létrehozásakor meg kell adnia a Storage-fiókot és a tárolási szolgáltatást, amelyhez csatlakozik. 

Külön privát végpontra van szükség minden olyan tárolási erőforráshoz, amelyhez hozzáférésre van szüksége, azaz [blobokat](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2okat](../blobs/data-lake-storage-introduction.md), [fájlokat](../files/storage-files-introduction.md), [várólistákat](../queues/storage-queues-introduction.md), [táblákat](../tables/table-storage-overview.md)vagy [statikus webhelyeket](../blobs/storage-blob-static-website.md). A privát végponton ezek a tárolási szolgáltatások a társított Storage **-fiók célként megadott alerőforrásként** vannak meghatározva. 

Ha létrehoz egy magánhálózati végpontot a Data Lake Storage Gen2 Storage-erőforráshoz, akkor a blob Storage-erőforráshoz is létre kell hoznia egyet. Ennek az az oka, hogy az Data Lake Storage Gen2 végpontot célzó műveletek átirányíthatók a blob-végpontba. Ha saját végpontot hoz létre mindkét erőforráshoz, győződjön meg arról, hogy a műveletek sikeresen elvégezhető.

> [!TIP]
> Hozzon létre egy külön privát végpontot a Storage szolgáltatás másodlagos példányához az RA-GRS-fiókok jobb olvasási teljesítményéhez.
> Mindenképpen hozzon létre egy általános célú v2 (standard vagy prémium szintű) Storage-fiókot.

Ha olvasási hozzáférést szeretne a másodlagos régióhoz a földrajzi redundáns tároláshoz konfigurált Storage-fiókkal, akkor a szolgáltatás elsődleges és másodlagos példányaihoz külön magánhálózati végpontokra van szükség. A **feladatátvételhez** nem kell létrehoznia privát végpontot a másodlagos példányhoz. A magánhálózati végpont automatikusan csatlakozni fog az új elsődleges példányhoz a feladatátvétel után. További információ a tárterület-redundancia lehetőségeiről: [Azure Storage redundancia](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Csatlakozás privát végponthoz

A privát végpontot használó VNet lévő ügyfeleknek ugyanazt a kapcsolati karakterláncot kell használniuk a Storage-fiókhoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldásra támaszkodva automatikusan átirányítja a kapcsolatokat a VNet a Storage-fiókba egy privát kapcsolaton keresztül.

> [!IMPORTANT]
> Ugyanazzal a kapcsolati karakterlánccal csatlakozhat a Storage-fiókhoz privát végpontok használatával, ahogy azt más célra is használni szeretné. Ne kapcsolódjon a Storage-fiókhoz az `privatelink` altartomány URL-címének használatával.

A VNet csatolt [saját DNS-zónát](../../dns/private-dns-overview.md) hozunk létre a privát végpontokhoz szükséges frissítésekkel, alapértelmezés szerint. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban. Az alábbi [DNS-változások](#dns-changes-for-private-endpoints) című szakasz a privát végpontokhoz szükséges frissítéseket ismerteti.

## <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai

Ha létrehoz egy privát végpontot, a rendszer a Storage-fiókhoz tartozó DNS CNAME erőforrásrekordot az előtaggal rendelkező altartományban lévő aliasra frissíti `privatelink` . Alapértelmezés szerint egy [saját DNS-zónát](../../dns/private-dns-overview.md)is hozunk létre, amely az `privatelink` altartományhoz tartozik, és a DNS a saját végpontokhoz tartozó erőforrásrekordokat is létrehoz.

Ha a VNet kívülről oldja fel a tárolási végpont URL-címét a privát végponttal, a rendszer a Storage szolgáltatás nyilvános végpontját oldja fel. A magánhálózati végpontot futtató VNet feloldva a tárolási végpont URL-címe feloldja a magánhálózati végpont IP-címét.

A fenti ábrán látható példában a "StorageAccountA" Storage-fiókhoz tartozó DNS-erőforrásrekordok a privát végpontot üzemeltető VNet kívülről történő feloldáskor a következők:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Amint azt korábban említettük, a VNet kívüli ügyfelek számára a nyilvános végponton keresztül megtagadhatja vagy vezérelheti a hozzáférést a Storage tűzfal használatával.

A StorageAccountA tartozó DNS-erőforrásrekordok, amikor a privát végpontot üzemeltető VNet-ügyfél feloldotta a következőt:

| Név                                                  | Típus  | Érték                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Ez a megközelítés lehetővé teszi, hogy a Storage-fiókhoz **ugyanazt a kapcsolati karakterláncot használja** , mint a privát végpontokat üzemeltető VNet lévő ügyfelek, valamint a VNet kívüli ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, az ügyfeleknek fel kell tudniuk oldani a Storage-fiók végpontjának teljes tartománynevét a magánhálózati végpont IP-címére. A DNS-kiszolgálót úgy kell konfigurálni, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a "*StorageAccountA.privatelink.blob.Core.Windows.net*" rekordját a magánhálózati végpont IP-címével.

> [!TIP]
> Egyéni vagy helyszíni DNS-kiszolgáló használatakor a DNS-kiszolgálót úgy kell konfigurálni, hogy az `privatelink` altartományban lévő Storage-fiók nevét a magánhálózati végpont IP-címére oldja fel. Ezt úgy teheti meg, hogy delegálja az `privatelink` altartományt a VNet magánhálózati DNS-zónájához, vagy konfigurálja a DNS-zónát a DNS-kiszolgálón, és hozzáadja a DNS-rekordot.

A tárolási szolgáltatásokhoz tartozó privát végpontok ajánlott DNS-zónái, valamint a hozzájuk tartozó végponti cél alerőforrások a következők:

| Tárolási szolgáltatás        | Célzott alerőforrás | Zóna neve                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob szolgáltatás           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | DFS                 | `privatelink.dfs.core.windows.net`   |
| Fájlszolgáltatások           | file                | `privatelink.file.core.windows.net`  |
| Queue szolgáltatás          | üzenetsor               | `privatelink.queue.core.windows.net` |
| Table service          | tábla               | `privatelink.table.core.windows.net` |
| Statikus webhelyek        | web                 | `privatelink.web.core.windows.net`   |

A saját DNS-kiszolgáló magánhálózati végpontok támogatására való konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Azure virtuális hálózatokon található erőforrások névfeloldása](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [A magánhálózati végpontok DNS-konfigurációja](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Ismert problémák

Tartsa szem előtt az Azure Storage-hoz készült privát végpontok következő ismert problémáit.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Tároló-hozzáférési megkötések a virtuális hálózatok-beli ügyfelek számára privát végpontokkal

A meglévő privát végpontokkal rendelkező virtuális hálózatok-ügyfelek megkötést kaptak a privát végpontokkal rendelkező más Storage-fiókok elérésekor. Tegyük fel például, hogy egy VNet N1 rendelkezik egy saját végponttal az A1-es Storage-fiókhoz a blob Storage-hoz. Ha az A2-es Storage-fiókhoz privát végpont tartozik egy VNet N2-ben a blob Storage-hoz, akkor az VNet N1-ben lévő ügyfeleknek a privát végpont használatával is hozzá kell férniük a blob Storage-fiókhoz. Ha az A2-es Storage-fiók nem rendelkezik privát végpontokkal a blob Storage-hoz, akkor a VNet N1-ben lévő ügyfelek privát végpont nélkül férhetnek hozzá a fiókhoz a blob Storage-ban.

Ez a megkötés a DNS-módosítások eredményeként történt, amikor az A2-es fiók létrehoz egy magánhálózati végpontot.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Hálózat biztonsági csoportok szabályai a privát végpontokkal rendelkező alhálózatok esetében

Jelenleg nem konfigurálhatja a [hálózati biztonsági csoport](../../virtual-network/network-security-groups-overview.md) (NSG) szabályait és a felhasználó által megadott útvonalakat a privát végpontokhoz. A privát végpontot működtető alhálózatra alkalmazott NSG-szabályok nem lesznek alkalmazva a privát végpontra. Ezeket csak más végpontokra alkalmazza a rendszer (például hálózati adapter-vezérlők). A probléma korlátozott megkerülő megoldásként implementálja a privát végpontok hozzáférési szabályait a forrás alhálózatokon, bár ennél a megközelítésnél magasabb szintű felügyeleti terhelésre lehet szükség.

### <a name="copying-blobs-between-storage-accounts"></a>Blobok másolása a Storage-fiókok között

A Storage-fiókok között csak akkor másolhat blobokat, ha az Azure REST API vagy a REST API használó eszközöket használja. Ezek az eszközök a következők: AzCopy, Storage Explorer, Azure PowerShell, Azure CLI és az Azure Blob Storage SDK-k. 

Csak a blob Storage-erőforrást tároló privát végpontok támogatottak. A Data Lake Storage Gen2 vagy a fájl erőforrást célzó privát végpontok még nem támogatottak. Emellett a Storage-fiókok a hálózati fájlrendszer (NFS) protokoll használatával történő másolása még nem támogatott. 

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
