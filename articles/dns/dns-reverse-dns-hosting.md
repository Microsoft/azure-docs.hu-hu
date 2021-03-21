---
title: DNS-névkeresési zónák üzemeltetése Azure DNS
description: Megtudhatja, hogyan használhatja a Azure DNSt az IP-címtartományok fordított DNS-keresési zónáinak üzemeltetéséhez
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525127"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>DNS-névkeresési zónák üzemeltetése Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan futtathatja a fordított DNS-keresési zónákat a Azure DNS-beli hozzárendelt IP-címtartományok számára. A névkeresési zónák által reprezentált IP-tartományokat hozzá kell rendelni a szervezethez, jellemzően az INTERNETSZOLGÁLTATÓnál.

Ha az Azure-szolgáltatáshoz hozzárendelt Azure-beli IP-cím fordított DNS-címét szeretné beállítani, tekintse meg a [fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz](dns-reverse-dns-for-azure-services.md)című témakört.

A cikk elolvasása előtt ismernie kell a [fordított DNS és az Azure-támogatás áttekintését](dns-reverse-dns-overview.md).

Ez a cikk végigvezeti az első névkeresési DNS-zóna és-rekord létrehozásának lépésein a Azure Portal, a Azure PowerShell, az Azure klasszikus CLI vagy az Azure CLI használatával.

## <a name="create-a-reverse-lookup-dns-zone"></a>Névkeresési DNS-zóna létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **központi** menüben válassza az **új**  >  **hálózatkezelés**, majd a **DNS-zóna** lehetőséget.

   !["DNS-zóna" kiválasztása](./media/dns-reverse-dns-hosting/figure1.png)

1. A **DNS-zóna létrehozása** panelen nevezze el a DNS-zónát. A zóna neve eltér az IPv4-és IPv6-előtagoktól. Az [IPv4](#ipv4) -vagy [IPv6](#ipv6) -utasítások használatával nevezze el a zónát. Ha elkészült, válassza a **Létrehozás** lehetőséget a zóna létrehozásához.

### <a name="ipv4"></a>IPv4

Az IPv4 névkeresési zónák neve az általa reprezentált IP-tartományon alapul. A következő formátumúnak kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Példák: a [fordított DNS és a támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Ha az osztály nélküli fordított DNS-keresési zónákat Azure DNSban hozza létre, akkor a zóna nevében kötőjelet () kell használnia a `-` perjel ( `/` ) helyett.
>
> Például a 192.0.2.128/26 IP-címtartomány esetében a `128-26.2.0.192.in-addr.arpa` helyett a zóna nevét kell használnia `128/26.2.0.192.in-addr.arpa` .
>
> Bár a DNS-szabványok mindkét módszert támogatják, Azure DNS nem támogatja a továbbítás perjel () karaktert tartalmazó DNS-zónák nevét `/` .

Az alábbi példa bemutatja, hogyan hozhat létre egy Azure DNS nevű C. osztályú fordított DNS-zónát `2.0.192.in-addr.arpa` a Azure Portal használatával:

 ![Képernyőkép, amely bemutatja, hogyan hozhat létre egy 2.0.192.in-addr. arpa nevű C osztályú fordított DNS-zónát Azure DNS a Azure Portal használatával.](./media/dns-reverse-dns-hosting/figure2.png)

Az **erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

Az alábbi példák bemutatják, hogyan hajthatja végre ezt a feladatot a Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna nevének a következő formátumúnak kell lennie: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Példák: a [fordított DNS és a támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md#ipv6).


Az alábbi példa bemutatja, hogyan hozhat létre egy Azure DNS nevű IPv6-névlekérdezési zónát `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` a Azure Portal használatával:

 !["DNS-zóna létrehozása" panel, betöltött mezőkkel](./media/dns-reverse-dns-hosting/figure3.png)

Az **erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

Az alábbi példák bemutatják, hogyan hajthatja végre ezt a feladatot a Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Fordított DNS-keresési zóna delegálása

Most, hogy létrehozta a fordított DNS-keresési zónát, meg kell győződnie arról, hogy a zóna delegálva van a fölérendelt zónából. A DNS-delegálás lehetővé teszi a DNS-névfeloldási folyamat számára a fordított DNS-keresési zónát futtató névkiszolgálók megtalálását. Ezek a névkiszolgálók ezután megválaszolják a címtartomány IP-címeinek DNS-névkeresési lekérdezéseit.

A Címkeresési zónák esetében a DNS-zónák delegálásának folyamatát a [tartomány delegálása Azure DNSra](dns-delegate-domain-azure-dns.md)című témakörben találja. A névkeresési zónák delegálása ugyanúgy működik. Az egyetlen különbség, hogy konfigurálnia kell a névszervereket az IP-tartományt a tartománynév-regisztráló helyett biztosító INTERNETSZOLGÁLTATÓval.

## <a name="create-a-dns-ptr-record"></a>PTR típusú DNS-rekord létrehozása

### <a name="ipv4"></a>IPv4

Az alábbi példa végigvezeti egy PTR-rekordnak a Azure DNS fordított DNS-zónában való létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. A **DNS-zóna** ablaktábla felső részén válassza a **+ rekord beállítása** elemet a **rekord hozzáadása** ablaktábla megnyitásához.

   ![Képernyőfelvétel a DNS-zóna panelről egy nyílra a + rekord beállítása gombon.](./media/dns-reverse-dns-hosting/figure4.png)

1. A PTR rekordhoz tartozó rekord nevének a többi IPv4-címnek kell lennie fordított sorrendben. 

   Ebben a példában az első három oktett már fel van töltve a zóna neve (. 2.0.192) részeként. Ezért csak az utolsó oktett van megadva a **név** mezőben. Előfordulhat például, hogy egy olyan erőforráshoz nevezi a **15** . rekordot, amelynek IP-címe 192.0.2.15.  
1. A **Típus mezőben** válassza a **PTR** lehetőséget.  
1. A **tartománynév** mezőben adja meg az IP-címet használó erőforrás teljes tartománynevét (FQDN).
1. A DNS-rekord létrehozásához kattintson az **OK gombra** a panel alján.

   !["Rekordazonosító hozzáadása" panel, betöltött mezőkkel](./media/dns-reverse-dns-hosting/figure5.png)

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Az alábbi példa végigvezeti az új PTR-rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. A **DNS-zóna** ablaktábla felső részén válassza a **+ rekord beállítása** elemet a **rekord hozzáadása** ablaktábla megnyitásához.

   ![Rekord létrehozására szolgáló gomb](./media/dns-reverse-dns-hosting/figure6.png)

2. A PTR rekordhoz beállított rekord nevének a többi IPv6-címnek kell lennie fordított sorrendben. Nem tartalmazhat nulla tömörítést. 

   Ebben a példában az IPv6 első 64 bitje már fel van töltve a zóna neve (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. IP6. arpa) részeként. Ezért csak az utolsó 64 bit van megadva a **név** mezőben. Az IP-cím utolsó 64 bitje fordított sorrendben van megadva, és az egyes hexadecimális számok között elválasztó pontnak kell lennie. Például megadhatja az **e. 5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** nevű rekordot egy olyan erőforráshoz, amelynek IP-címe 2001:0db8: ABDC: 0000: f524:10bc: 1af9:405e.  
3. A **Típus mezőben** válassza a **PTR** lehetőséget.  
4. A **tartománynév** mezőben adja meg az IP-címet használó erőforrás teljes tartománynevét.
5. A DNS-rekord létrehozásához kattintson az **OK gombra** a panel alján.

![Képernyőkép, amely megjeleníti a "rekordazonosító hozzáadása" ablaktáblát, és egy nyilat mutat a Type (típus) mezőben található értékre.](./media/dns-reverse-dns-hosting/figure7.png)

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>A rekordok megtekintése

A létrehozott rekordok megtekintéséhez keresse meg a DNS-zónát a Azure Portalban. A **DNS-zóna** panel alsó részén láthatja a DNS-zóna rekordjait. Ekkor meg kell jelennie az alapértelmezett NS és SOA rekordoknak, valamint a létrehozott új rekordoknak. Az NS-és SOA-rekordok minden zónában létrejönnek. 

### <a name="ipv4"></a>IPv4

A **DNS-zóna** ablaktáblán az IPv4 PTR rekordok láthatók:

!["DNS-zóna" ablaktábla IPv4-rekordokkal](./media/dns-reverse-dns-hosting/figure8.png)

Az alábbi példák bemutatják, hogyan tekintheti meg a PTR-rekordokat a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

A **DNS-zóna** ablaktáblán az IPv6 PTR-rekordok láthatók:

!["DNS-zóna" ablaktábla IPv6-rekordokkal](./media/dns-reverse-dns-hosting/figure9.png)

Az alábbi példák bemutatják, hogyan tekintheti meg a rekordokat a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>GYIK

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Használhatok fordított DNS-keresési zónákat az INTERNETSZOLGÁLTATÓ által hozzárendelt IP-blokkokhoz Azure DNSon?

Igen. A névkeresési (ARPA) zónák tárolása a saját IP-címtartományok esetében Azure DNS teljes mértékben támogatott.

Hozza létre a névkeresési zónát Azure DNS a jelen cikkben leírtak szerint, majd működjön együtt az INTERNETSZOLGÁLTATÓval [a zóna delegálásához](dns-domain-delegation.md). Ezután az egyes névlekérdezésekhez tartozó PTR-rekordokat ugyanúgy kezelheti, mint más bejegyzéstípusokat.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Mennyibe kerül a fordított DNS keresési zóna ára?

Ha a fordított DNS-keresési zónát az INTERNETSZOLGÁLTATÓ által hozzárendelt IP-blokkhoz szeretné üzemeltetni, Azure DNS a [standard Azure DNS díjszabása](https://azure.microsoft.com/pricing/details/dns/)alapján számítjuk fel.

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Használhatok fordított DNS-keresési zónákat a Azure DNS IPv4-és IPv6-címeihez is?

Igen. Ez a cikk bemutatja, hogyan hozhat létre IPv4-és IPv6-névlekérdezési zónákat Azure DNSban.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Importálható egy meglévő fordított DNS-keresési zóna?

Igen. Az Azure CLI-vel meglévő DNS-zónákat importálhat Azure DNSba. Ez a metódus mindkét címkeresési zónához és névkeresési zónához használható.

További információ: [DNS-zónafájl importálása és exportálása az Azure CLI használatával](dns-import-export.md).

## <a name="next-steps"></a>Következő lépések

A fordított DNS-sel kapcsolatos további információkért lásd: [fordított DNS-keresés a wikipedia-ben](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Ismerje meg, hogyan [kezelheti az Azure-szolgáltatások fordított DNS-rekordjait](dns-reverse-dns-for-azure-services.md).
