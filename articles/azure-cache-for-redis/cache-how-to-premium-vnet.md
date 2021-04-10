---
title: Virtuális hálózat konfigurálása – prémium szintű Azure cache a Redis-példányhoz
description: Ismerje meg, hogyan hozhat létre és kezelhet virtuális hálózati támogatást a prémium szintű Azure cache-hez a Redis-példányhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375272"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Virtuális hálózatok támogatásának konfigurálása prémium szintű Azure cache-hez a Redis-példányhoz

Az [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az alhálózatokkal, a hozzáférés-vezérlési szabályzatokkal és egyéb funkciókkal a hozzáférés további korlátozása érdekében. Ha egy Azure cache for Redis-példány virtuális hálózattal van konfigurálva, nem nyilvánosan címezhető, és csak a virtuális hálózatban lévő virtuális gépekről és alkalmazásokról érhető el. Ez a cikk azt ismerteti, hogyan konfigurálható a virtuális hálózatok támogatása egy prémium szintű Azure cache-hez a Redis-példányhoz.

> [!NOTE]
> A Redis készült Azure cache a klasszikus üzemi modellt és a Azure Resource Manager virtuális hálózatokat is támogatja.
> 

## <a name="set-up-virtual-network-support"></a>Virtuális hálózat támogatásának beállítása

A virtuális hálózatok támogatása a gyorsítótár létrehozásakor a **Redis új Azure-gyorsítótárában** van konfigurálva.

1. Prémium szintű gyorsítótár létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget. A Azure Portal gyorsítótárai létrehozásán kívül a Resource Manager-sablonok, a PowerShell vagy az Azure CLI használatával is létrehozhatja őket. További információ arról, hogyan hozható létre Azure cache a Redis-példányhoz. [a gyorsítótár létrehozása](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)című témakörben talál további információt.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Az erőforrás-létrehozást bemutató képernyőkép.":::
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget. Ezután válassza ki **Az Azure cache-t a Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Képernyőkép, amely az Azure cache kiválasztását mutatja a Redis.":::

1. Az **új Redis cache** lapon adja meg az új prémium szintű gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmazhat. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a *\<DNS name> . Redis.cache.Windows.net* lesz. |
   | **Előfizetés** | Válassza ki az előfizetését a legördülő listából. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. |
   | **Erőforráscsoport** | Válasszon ki egy erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre kell hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. |
   | **Hely** | Válasszon ki egy helyet a legördülő listából. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Gyorsítótár típusa** |A prémium szintű funkciók konfigurálásához válassza ki a prémium szintű gyorsítótárat a legördülő listából. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál. |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |

1. Válassza a **hálózatkezelés** fület, vagy kattintson az oldal alján található **hálózatkezelés** gombra.

1. A **hálózatkezelés** lapon válassza a **virtuális hálózatok** lehetőséget a kapcsolati módszerként. Ha új virtuális hálózatot szeretne használni, először hozza létre a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) vagy a [virtuális hálózat (klasszikus) létrehozása a Azure Portal használatával](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)című témakör lépéseit követve. Ezután térjen vissza az **új Azure cache for Redis** panelre a prémium szintű gyorsítótár létrehozásához és konfigurálásához.

   > [!IMPORTANT]
   > Ha az Azure cache-t egy Resource Manager-beli virtuális hálózatra telepíti, a gyorsítótárnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrásokat, kivéve az Azure cache for Redis instances Redis. Ha egy Azure cache-t próbál telepíteni a Redis-példányhoz egy olyan Resource Manager virtuális hálózati alhálózatra, amely más erőforrásokat is tartalmaz, a telepítés meghiúsul.
   > 
   > 

   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtuális hálózat** | Válassza ki a virtuális hálózatot a legördülő listából. | Olyan virtuális hálózatot válasszon, amely ugyanabban az előfizetésben és helyen található, mint a gyorsítótár. |
   | **Alhálózat** | Válassza ki az alhálózatot a legördülő listából. | Az alhálózat címtartományének CIDR-jelöléssel kell rendelkeznie (például 192.168.1.0/24). Ennek a virtuális hálózat címterület részét kell tartalmaznia. |
   | **Statikus IP-cím** | Választható Adjon meg egy statikus IP-címet. | Ha nem ad meg statikus IP-címet, a rendszer automatikusan választ egy IP-címet. |

   > [!IMPORTANT]
   > Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Az Azure-beli virtuális hálózati infrastruktúra által használt IP-címeken kívül az alhálózat Redis-példányának minden egyes Azure-gyorsítótára két IP-címet használ, és egy további IP-címet a terheléselosztó számára. A nem fürtözött gyorsítótár egy szegmensnek tekintendő.
   > 

1. Válassza a **következő: speciális** lapot, vagy válassza a **következő: speciális** gombot az oldal alján.

1. A prémium szintű gyorsítótár-példány **speciális** lapján adja meg a nem TLS port, a fürtözés és az adatmegőrzés beállításait.

1. Válassza a **Next: Tags (tovább** ) lapot, vagy válassza a **Next: Tags (tovább** ) gombot az oldal alján.

1. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** lapon az Azure ellenőrzi a konfigurációt.

1. Ha megjelenik az **átadott zöld érvényesítés** üzenet, válassza a **Létrehozás** lehetőséget.

Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra. A gyorsítótár létrehozása után megtekintheti a virtuális hálózat konfigurációját, ha az **erőforrás** menüben a **Virtual Network** lehetőségre kattint.

![Virtuális hálózat][redis-cache-vnet-info]

Ha virtuális hálózatot használ a Redis-példány Azure cache-hez való csatlakozáshoz, adja meg a gyorsítótár állomásnevét a kapcsolati karakterláncban, az alábbi példában látható módon:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Azure cache a Redis Virtual networkhez – gyakori kérdések

Az alábbi lista az Azure cache szolgáltatással történő Redis skálázással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* Melyek az Azure cache-vel kapcsolatos gyakori hibák a Redis és a virtuális hálózatok esetében?
* [Hogyan tudom ellenőrizni, hogy a gyorsítótár működik-e egy virtuális hálózaton?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Amikor megpróbálok csatlakozni az Azure cache-hez a Redis-példányhoz egy virtuális hálózaton, miért kapok hibaüzenetet arról, hogy a távoli tanúsítvány érvénytelen?
* [Használhatok virtuális hálózatokat standard vagy alapszintű gyorsítótárral?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Miért nem sikerül létrehozni egy Azure cache-t a Redis-példányhoz egyes alhálózatokban, de másokat nem?
* [Mik az alhálózati címtartomány követelményei?](#what-are-the-subnet-address-space-requirements)
* [Működik az összes gyorsítótár-funkció, ha a gyorsítótár egy virtuális hálózaton fut?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Melyek az Azure cache-vel kapcsolatos gyakori hibák a Redis és a virtuális hálózatok esetében?

Ha a Redis tartozó Azure cache-t egy virtuális hálózat üzemelteti, a rendszer a következő táblázatban található portokat használja.

>[!IMPORTANT]
>Ha a következő táblázatokban lévő portok le vannak tiltva, előfordulhat, hogy a gyorsítótár nem működik megfelelően. Ha a portok közül egy vagy több blokkolva van, akkor a leggyakoribb hiba a probléma, amikor az Azure cache-t használja a virtuális hálózat Redis.
> 

- [Kimenő portokkal kapcsolatos követelmények](#outbound-port-requirements)
- [Bejövő portokkal kapcsolatos követelmények](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Kimenő portokkal kapcsolatos követelmények

Kilenc kimenő portra vonatkozó követelmény van. Az ezekben a tartományokban lévő kimenő kérelmek vagy más olyan szolgáltatásoknak vannak kihagyva, amelyek szükségesek a gyorsítótár működéséhez vagy a belső Redis-alhálózathoz a csomópontok közötti kommunikációhoz. Földrajzi replikálás esetén további kimenő követelmények is léteznek az elsődleges és a replika gyorsítótár alhálózatai közötti kommunikációhoz.

| Portok | Irány | Átviteli protokoll | Cél | Helyi IP-cím | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Kimenő |TCP |Redis-függőségek az Azure Storage-ban/PKI-ben (Internet) | (Redis alhálózat) |* <sup>4</sup> |
| 443 | Kimenő | TCP | Redis függőség Azure Key Vault és Azure Monitor | (Redis alhálózat) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Kimenő |TCP/UDP |Redis-függőségek a DNS-ben (Internet/virtuális hálózat) | (Redis alhálózat) | 168.63.129.16 és 169.254.169.254 <sup>2</sup> és bármely egyéni DNS-kiszolgáló a <sup>3</sup> . alhálózathoz |
| 8443 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) | (Redis alhálózat) |
| 10221-10231 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) | (Redis alhálózat) |
| 20226 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 13000-13999 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 15000-15999 |Kimenő |TCP |Belső kommunikáció a Redis és a Geo-replikáláshoz | (Redis alhálózat) |(Redis alhálózat) (Geo-replika társ alhálózat) |
| 6379-6380 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> a Service Tags AzureKeyVault és a AzureMonitor erőforrás-kezelő hálózati biztonsági csoportokkal (NSG) is használható.

<sup>2</sup> ezek az IP-címek a Microsoft tulajdonában vannak, és a Azure DNSt kiszolgáló gazda virtuális gép kezelésére szolgálnak.

<sup>3</sup> ez az információ nem szükséges olyan alhálózatok esetén, amelyek nem rendelkeznek egyéni DNS-kiszolgálóval vagy újabb, az egyéni DNS-t figyelmen kívül Redis gyorsítótárral.

<sup>4</sup> további információ: [További virtuális hálózati kapcsolatokra vonatkozó követelmények](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Georeplikációs társportok követelményei

Ha az Azure Virtual Networks gyorsítótárai között geo-replikációt használ, a teljes alhálózathoz tartozó 15000-15999-es portok feloldása a bejövő *és* kimenő utasításokban mindkét gyorsítótárban. Ezzel a konfigurációval az alhálózat összes replika-összetevője közvetlenül tud kommunikálni egymással, még akkor is, ha van jövőbeli földrajzi feladatátvétel.

#### <a name="inbound-port-requirements"></a>Bejövő portokkal kapcsolatos követelmények

Nyolc bejövő porttartomány-követelmény van. Ezekben a tartományokban bejövő kérelmek vagy más, ugyanazon a virtuális hálózaton vagy a Redis alhálózaton belüli kommunikációban üzemeltetett szolgáltatások.

| Portok | Irány | Átviteli protokoll | Cél | Helyi IP-cím | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Bejövő |TCP |Redis-alapú ügyfél-kommunikáció, Azure-terheléselosztás | (Redis alhálózat) | (Redis alhálózat), (ügyfél-alhálózat), AzureLoadBalancer <sup>1</sup> |
| 8443 |Bejövő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 8500 |Bejövő |TCP/UDP |Azure-beli terheléselosztás | (Redis alhálózat) | AzureLoadBalancer |
| 10221-10231 |Bejövő |TCP |Ügyfél-kommunikáció a Redis-fürtökkel, belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat), AzureLoadBalancer, (ügyfél-alhálózat) |
| 13000-13999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtökkel, Azure-terheléselosztás | (Redis alhálózat) | (Redis alhálózat), (ügyfél-alhálózat), AzureLoadBalancer |
| 15000-15999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtökkel, Azure terheléselosztás és geo-replikáció | (Redis alhálózat) | (Redis alhálózat), (ügyfél-alhálózat), AzureLoadBalancer, (Geo-replika társ alhálózat) |
| 16001 |Bejövő |TCP/UDP |Azure-beli terheléselosztás | (Redis alhálózat) | AzureLoadBalancer |
| 20226 |Bejövő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> a NSG-szabályok létrehozásához használhatja a Resource Managerhez készült AzureLoadBalancer vagy a AZURE_LOADBALANCERt a klasszikus üzemi modellhez.

#### <a name="additional-virtual-network-connectivity-requirements"></a>További virtuális hálózati kapcsolati követelmények

Az Azure cache hálózati kapcsolati követelményei olyan Redis esetében fordulnak elő, amelyek esetleg nem feltétlenül teljesülnek egy virtuális hálózaton. A Redis készült Azure cache megköveteli, hogy a következő elemek megfelelően működjenek, ha a virtuális hálózaton belül van használatban:

* A kimenő hálózati kapcsolat az Azure Storage-végpontokkal világszerte. Azok a végpontok, amelyek ugyanabban a régióban találhatók, mint az Azure cache az Redis-példányhoz és *más* Azure-régiókban található tárolási végpontok is. Az Azure Storage-végpontok a következő DNS-tartományok alatt oldhatók fel: *table.Core.Windows.net*, *blob.Core.Windows.net*, *Queue.Core.Windows.net* és *file.Core.Windows.net*.
* Kimenő hálózati kapcsolat a *OCSP.Digicert.com*, a *crl4.digicert.com*, a *OCSP.msocsp.com*, a *mscrl.microsoft.com*, a *crl3.digicert.com* *, a cacerts.Digicert.com,* a *oneocsp.microsoft.com* és a *CRL.microsoft.com*. Ez a kapcsolat szükséges a TLS/SSL-funkciók támogatásához.
* A virtuális hálózat DNS-konfigurációjának képesnek kell lennie a korábbi pontokban említett összes végpont és tartomány feloldására. Ezek a DNS-követelmények teljesíthetők az érvényes DNS-infrastruktúra konfigurálásának és karbantartásának biztosításával a virtuális hálózat számára.
* Kimenő hálózati kapcsolat az alábbi Azure Monitor-végpontokkal, amelyek a következő DNS-tartományok alatt oldhatók fel: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-Red.prod.microsoftmetrics.com* és *shoebox3-Black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Hogyan tudom ellenőrizni, hogy a gyorsítótár működik-e egy virtuális hálózaton?

>[!IMPORTANT]
>Amikor egy virtuális hálózaton található Redis-példányhoz csatlakozik egy Azure-gyorsítótárhoz, a gyorsítótár-ügyfeleknek ugyanabban a virtuális hálózatban vagy virtuális hálózatban kell lenniük, amelyen az adott Azure-régióban engedélyezve van a virtuális hálózati kapcsolat. A globális virtuális hálózati társítás jelenleg nem támogatott. Ez a követelmény minden tesztelési alkalmazásra vagy diagnosztikai pingelési eszközre vonatkozik. Függetlenül attól, hogy az ügyfélalkalmazás hol található, a NSG vagy más hálózati réteget úgy kell konfigurálni, hogy az ügyfél hálózati forgalma elérje az Azure cache-t a Redis-példányhoz.
>

Miután az előző szakaszban leírtak szerint konfigurálta a portra vonatkozó követelményeket, a következő lépések végrehajtásával ellenőrizheti, hogy a gyorsítótár működik-e:

- [Indítsa újra](cache-administration.md#reboot) az összes gyorsítótár-csomópontot. Ha az összes szükséges gyorsítótár-függőség nem érhető el, a [bejövő portokra vonatkozó követelmények](cache-how-to-premium-vnet.md#inbound-port-requirements) és a [kimenő portokra vonatkozó követelmények](cache-how-to-premium-vnet.md#outbound-port-requirements)dokumentálása után a gyorsítótár nem fog tudni újraindulni.
- A gyorsítótár-csomópontok újraindítása után a Azure Portal gyorsítótárának állapota szerint a következő teszteket végezheti el:
  - Pingelje a gyorsítótár-végpontot a 6380-as port használatával egy olyan gépről, amely ugyanazon a virtuális hálózaton található, mint a gyorsítótár, a [tcping](https://www.elifulkerson.com/projects/tcping.php)használatával. Például:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Ha az `tcping` eszköz jelzi, hogy a port meg van nyitva, a gyorsítótár elérhető a virtuális hálózatban lévő ügyfelekkel való csatlakozáshoz.

  - A tesztelés egy másik módja, ha létrehoz egy teszt gyorsítótár-ügyfelet (ez lehet egy egyszerű, a StackExchange. Redis-t használó konzolos alkalmazás), amely csatlakozik a gyorsítótárhoz, és felveszi és lekéri néhány elemet a gyorsítótárból. Telepítse a minta ügyfélalkalmazás egy olyan virtuális GÉPRE, amely ugyanabban a virtuális hálózatban található, mint a gyorsítótár. Ezután futtassa a parancsot a gyorsítótárhoz való kapcsolódás ellenőrzéséhez.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Amikor megpróbálok csatlakozni az Azure cache-hez a Redis-példányhoz egy virtuális hálózaton, miért kapok hibaüzenetet arról, hogy a távoli tanúsítvány érvénytelen?

Amikor megpróbál csatlakozni egy Azure cache-hez a Redis-példányhoz egy virtuális hálózaton, egy tanúsítvány-érvényesítési hiba jelenik meg, például a következő:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Ennek oka az lehet, hogy az IP-cím alapján csatlakozik a gazdagéphez. Javasoljuk, hogy használja az állomásnevet. Más szóval a következő sztringet használja:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Kerülje a következő kapcsolódási karakterlánchoz hasonló IP-cím használatát:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ha nem tudja feloldani a DNS-nevet, egyes ügyféloldali kódtárak olyan konfigurációs beállításokat tartalmaznak `sslHost` , mint például a StackExchange. Redis ügyfél. Ez a beállítás lehetővé teszi a tanúsítvány-ellenőrzéshez használt állomásnév felülbírálását. Például:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Használhatok virtuális hálózatokat standard vagy alapszintű gyorsítótárral?

A virtuális hálózatok csak prémium szintű gyorsítótárak használata esetén használhatók.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Miért nem sikerül létrehozni egy Azure cache-t a Redis-példányhoz egyes alhálózatokban, de másokat nem?

Ha Azure cache-t telepít a Redis-példányhoz egy virtuális hálózatra, a gyorsítótárnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrástípust. Ha a Redis-példányhoz egy Azure-gyorsítótárat szeretne üzembe helyezni egy olyan Resource Manager virtuális hálózati alhálózatra, amely más erőforrásokat is tartalmaz, például az Azure Application Gateway-példányokat és a kimenő NAT-ot, a központi telepítés általában sikertelen lesz. A Redis-példányhoz tartozó új Azure cache létrehozása előtt törölnie kell más típusok meglévő erőforrásait.

Az alhálózaton is elérhetőnek kell lennie elegendő IP-címmel.

### <a name="what-are-the-subnet-address-space-requirements"></a>Mik az alhálózati címtartomány követelményei?

Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure-beli virtuális hálózati infrastruktúra által használt IP-címeken kívül az alhálózatban található Redis-példányok minden egyes Azure-gyorsítótára két IP-címet használ, és további IP-címeket is tartalmaz a további replikák számára, ha vannak ilyenek. A terheléselosztó egy további IP-címet használ. A nem fürtözött gyorsítótár egy szegmensnek tekintendő.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Működik az összes gyorsítótár-funkció, ha a gyorsítótár egy virtuális hálózaton fut?

Ha a gyorsítótár egy virtuális hálózat része, akkor csak a virtuális hálózatban lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Ennek eredményeképpen a következő gyorsítótár-kezelési funkciók nem működnek most:

* **Redis-konzol**: mivel a Redis-konzol a helyi böngészőben fut, amely általában olyan fejlesztői gépen fut, amely nem csatlakozik a virtuális hálózathoz, nem tud majd csatlakozni a gyorsítótárhoz.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>A ExpressRoute használata az Azure cache használatával a Redis

Az ügyfelek a virtuális hálózati infrastruktúrához kapcsolódhatnak az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -áramkörhöz. Ily módon kiterjesztik a helyszíni hálózatot az Azure-ra.

Alapértelmezés szerint az újonnan létrehozott ExpressRoute áramkör nem hajt végre kényszerített bújtatást (az alapértelmezett útvonal (0.0.0.0/0) hirdetményét egy virtuális hálózaton. Ennek eredményeképpen a kimenő internetkapcsolat közvetlenül a virtuális hálózatból is engedélyezett. Az ügyfélalkalmazások más Azure-végpontokhoz is csatlakozhatnak, beleértve a Redis-példányhoz tartozó Azure cache-t is.

A gyakori ügyfél-konfiguráció a kényszerített bújtatás használata (alapértelmezett útvonal meghirdetése), amely a helyi forgalom helyett a kimenő internetes forgalmat kényszeríti. Ez a forgalmi folyamat megszakítja a kapcsolatot az Azure cache-vel a Redis, ha a kimenő forgalom ezután le van tiltva a helyszínen, így az Azure cache for Redis-példány nem tud kommunikálni a függőségeivel.

A megoldás egy vagy több felhasználó által megadott útvonal (UDR) definiálása azon az alhálózaton, amely az Redis-példányhoz tartozó Azure cache-t tartalmazza. A UDR olyan alhálózat-specifikus útvonalakat határoznak meg, amelyeket az alapértelmezett útvonal helyett tiszteletben kell tartani.

Ha lehetséges, használja a következő konfigurációt:

* A ExpressRoute-konfiguráció a 0.0.0.0/0 értéket hirdeti meg, és alapértelmezés szerint az összes helyszíni kimenő forgalmat kényszeríti.
* A Redis-példányhoz tartozó Azure cache-t tartalmazó alhálózatra alkalmazott UDR a 0.0.0.0/0 értéket határozza meg a TCP/IP-forgalom nyilvános internethez való továbbításának munkaútvonalával. Például beállítja a következő ugrás típusát az *internetre*.

Ezen lépések együttes hatása, hogy az alhálózat szintű UDR elsőbbséget élveznek az ExpressRoute kényszerített bújtatásával szemben, amely biztosítja a kimenő internet-hozzáférést az Azure cache-ről a Redis-példányhoz.

A Redis-példány Azure cache-hez való csatlakoztatása egy helyszíni alkalmazásból a ExpressRoute használatával nem jellemző használati forgatókönyv a teljesítménnyel kapcsolatos okok miatt. A legjobb teljesítmény érdekében az Azure cache for Redis ügyfeleknek ugyanabban a régióban kell lenniük, mint a Redis-példány Azure cache-je.

>[!IMPORTANT]
>A UDR meghatározott útvonalaknak elég *egyedinek kell lenniük* ahhoz, hogy elsőbbséget élvezzenek a ExpressRoute-konfiguráció által meghirdetett útvonalakkal szemben. Az alábbi példa a széles 0.0.0.0/0 címtartományt használja, és így véletlenül felülbírálható a több megadott címtartományt használó útválasztási hirdetmények.

>[!WARNING]
>Az Redis-hez készült Azure cache nem támogatott olyan ExpressRoute-konfigurációk esetén, amelyek nem tesznek közzé olyan *útvonalakat, amelyek a nyilvános társ-létrehozási útvonalról a privát társítási útvonalra* kerülnek. A nyilvános ExpressRoute konfigurált konfigurációk a Microsofttól érkező útválasztási hirdetményeket fogadnak Microsoft Azure IP-címtartományok nagy készlete számára. Ha ezek a címtartományok helytelenül vannak kihirdetve a privát társítási útvonalon, akkor az eredmény az, hogy a Redis-példány alhálózatának Azure cache-ről érkező összes kimenő hálózati csomag helytelenül van kényszerítve az ügyfél helyszíni hálózati infrastruktúrája számára. Ez a hálózati folyamat megszakítja az Azure cache-t a Redis. A probléma megoldásának célja, hogy leállítsa az útvonalakat a nyilvános társítási útvonalról a privát társ-létrehozási útvonalra.

A UDR kapcsolatos háttér-információk a [virtuális hálózati forgalom útválasztásában](../virtual-network/virtual-networks-udr-overview.md)érhetők el.

További információ a ExpressRoute: a [ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Következő lépések

További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
