---
title: A Azure DNS DNS-rekordjainak kezelése a Azure PowerShell használatával | Microsoft Docs
description: A DNS-rekordhalmazok és-rekordok kezelése Azure DNS a tartomány Azure DNS-on való üzemeltetése során. Minden PowerShell-parancs a rekordhalmazon és a rekordokon végzett műveletekhez.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011591"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>DNS-rekordok és-rekordhalmazok kezelése a Azure DNS használatával Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klasszikus Azure CLI](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ebből a cikkből megtudhatja, hogyan kezelheti a DNS-zónához tartozó DNS-rekordokat Azure PowerShell használatával. A DNS-rekordok a platformfüggetlen [Azure CLI](dns-operations-recordsets-cli.md) vagy a [Azure Portal](dns-operations-recordsets-portal.md)használatával is kezelhetők.

A cikkben szereplő példák feltételezik, hogy már [telepítette Azure PowerShell, bejelentkezett, és létrehozott egy DNS-zónát](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.


## <a name="create-a-new-dns-record"></a>Új DNS-rekord létrehozása

Ha az új rekord neve és típusa megegyezik egy meglévő rekorddal, [fel kell vennie azt a meglévő készletbe](#add-a-record-to-an-existing-record-set). Ha az új rekord neve és típusa megegyezik az összes létező rekorddal, létre kell hoznia egy új rekordhalmazt. 

### <a name="create-a-records-in-a-new-record-set"></a>"A" rekordok létrehozása új rekordhalmazban

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Rekordhalmaz létrehozásakor meg kell adnia a rekordhalmaz nevét, a zónát, az élettartamot (TTL), a bejegyzéstípust és a létrehozandó rekordokat.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Ha például egy "A" típusú rekordot használ, meg kell adnia az IP-címet a paraméter használatával `-IPv4Address` . Más bejegyzéstípusokat más paraméterek is használnak. További részletekért tekintse meg a további bejegyzéstípusokat.

Az alábbi példa egy "www" relatív névvel rendelkező rekordot hoz létre a (z) "contoso.com" DNS-zónában. A rekord teljes neve "www.contoso.com". A rekord típusa "A", az élettartam pedig 3600 másodperc. A rekordtípus egyetlen rekordot tartalmaz, amelynek IP-címe "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Egy olyan rekordhalmaz létrehozásához, amely egy zóna "APEX" (ebben az esetben a "contoso.com"), használja a rekordhalmaz nevét \@ (idézőjelek nélkül):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Ha egynél több rekordot tartalmazó rekordhalmazt kell létrehoznia, először hozzon létre egy helyi tömböt, és adja hozzá a rekordokat, majd adja át a tömböt a `New-AzDnsRecordSet` következőképpen:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

A [rekordszintű metaadatok](dns-zones-records.md#tags-and-metadata) segítségével az alkalmazásspecifikus adatokat az egyes bejegyzéstípusokkal társíthatja kulcs-érték párokként. Az alábbi példa bemutatja, hogyan hozhat létre két metaadat-bejegyzéssel rendelkező rekordot, a "dept = Finance" és a "környezet = termelés" elemet.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

A Azure DNS az "üres" rekordhalmazokat is támogatja, amelyek helyőrzőként működhetnek a DNS-név lefoglalásához a DNS-rekordok létrehozása előtt. Az üres rekordhalmazok a Azure DNS vezérlő síkon láthatók, de a Azure DNS névkiszolgálók esetében is megjelennek. Az alábbi példa egy üres rekordot hoz létre:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Az "A" rekordok létrehozásának részletes ismertetését az alábbi példák bemutatják, hogyan hozhatók létre rekordok a Azure DNS által támogatott egyéb bejegyzéstípusokból.

Minden esetben bemutatjuk, hogyan hozhat létre egyetlen rekordot tartalmazó rekordot. Az "A" rekordokra vonatkozó korábbi példák úgy módosíthatók, hogy más típusú rekordokat is tartalmazó rekordhalmazokat hozzon létre, metaadatokkal, vagy üres rekordhalmazokat hozzon létre.

Nem adunk példát egy SOA-rekord létrehozására, mivel a SOAs létrehozása és törlése minden DNS-zónával megtörtént, és nem lehet külön létrehozni vagy törölni. [A SOA azonban módosítható, ahogy az egy későbbi példában is látható](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó AAAA típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Egyetlen rekorddal rendelkező CAA-rekord létrehozása

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó CNAME típusú rekordhalmaz létrehozása

> [!NOTE]
> A DNS-szabványok nem engedélyezik a CNAME rekordokat egy zóna () csúcsán `-Name '@'` , és nem teszik lehetővé, hogy az egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információ: [CNAME rekordok](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó MX típusú rekordhalmaz létrehozása

Ebben a példában a "" rekordhalmazt használjuk \@ egy MX-rekord létrehozásához a zóna csúcsán (ebben az esetben "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó NS típusú rekordhalmaz létrehozása

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó PTR típusú rekordhalmaz létrehozása

Ebben az esetben a "my-arpa-zone.com" az IP-címtartományt jelképező ARPA névkeresési zóna. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg. A "10" nevű rekord a rekordban szereplő IP-címtartomány utolsó oktettje.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Egyetlen rekordot tartalmazó SRV típusú rekordhalmaz létrehozása

[SRV-rekordok](dns-zones-records.md#srv-records)létrehozásakor adja meg a *\_ szolgáltatást* és a *\_ protokollt* a rekord készletének nevében. \@Ha SRV-rekordhalmazt hoz létre a zóna csúcspontján, a rekordhalmaz nevében nem szükséges a "" belefoglalása.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Egyetlen rekorddal rendelkező TXT-rekord létrehozása

Az alábbi példa azt szemlélteti, hogyan hozható létre TXT-rekord. A TXT-rekordok által támogatott maximális karakterlánc hosszával kapcsolatos további információkért lásd: [TXT-rekordok](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Rekord beolvasása

Meglévő beállításhalmaz beolvasásához használja a következőt: `Get-AzDnsRecordSet` . Ez a parancsmag egy helyi objektumot ad vissza, amely az Azure DNSban beállított rekordot jelöli.

Ahogy a `New-AzDnsRecordSet` esetében, a megadott rekordazonosító nevének *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zóna nevét. Meg kell adnia a rekord típusát és a rekordot tartalmazó zónát is.

Az alábbi példa bemutatja, hogyan kérhet le egy rekordot. Ebben a példában a zóna a `-ZoneName` és paraméterrel van megadva `-ResourceGroupName` .

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Azt is megteheti, hogy a zónát egy, a paraméterrel átadott zóna objektummal is megadhatja `-Zone` .

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Rekordhalmazok listázása

`Get-AzDnsZone`Egy zónában lévő rekordhalmazok listázásához a `-Name` és/vagy paraméterek kihagyása mellett is használható `-RecordType` .

A következő példa a zónában lévő összes rekordhalmazt adja vissza:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Az alábbi példa azt szemlélteti, hogyan kérhető le egy adott típus összes rekordhalmaza, ha megadja a rekord típusát a rekordhalmaz nevének kihagyása közben:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Ha a megadott névvel rendelkező összes rekordhalmazt le szeretné kérdezni a rekordtípusok között, le kell kérnie az összes rekordhalmazt, majd szűrnie kell az eredményeket:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

A fenti példákban a zóna a `-ZoneName` `-ResourceGroupName` (z) és paraméter (ahogy látható) használatával, vagy egy zóna objektum megadásával adható meg:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása meglévő készlethez

Rekord meglévő készlethez való hozzáadásához kövesse az alábbi három lépést:

1. Meglévő rekord beolvasása

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adja hozzá az új rekordot a helyi rekordhoz. Ez egy off-line művelet.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse vissza a módosítást a Azure DNS szolgáltatásnak. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

A használatával `Set-AzDnsRecordSet` *lecseréli* a meglévő rekordhalmazt Azure DNS (és a benne található összes rekord) a megadott rekordhalmazra. A [ETAG-ellenőrzésekkel](dns-zones-records.md#etags) biztosítható, hogy az egyidejű módosítások ne legyenek felülírva. `-Overwrite`Ezeket az ellenőrzéseket a választható kapcsoló használatával tilthatja le.

A műveletek ezen sorozata *vezetékes* is lehet, ami azt jelenti, hogy a rekord-készlet objektumot a cső használatával adja át, nem pedig paraméterként.

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

A fenti példák azt mutatják be, hogyan adhat hozzá "A" rekordot egy meglévő "A" típusú rekordhoz. A hasonló műveletek a rekordok más típusú rekordhalmazhoz való hozzáadására szolgálnak, a paramétert pedig az `-Ipv4Address` `Add-AzDnsRecordConfig` egyes bejegyzéstípusokra jellemző egyéb paraméterekkel helyettesítve. Az egyes bejegyzéstípusok paramétereinek megegyeznek a parancsmaghoz tartozó paraméterekkel `New-AzDnsRecordConfig` , ahogy az a fentiekben ismertetett további példákban is látható.

A "CNAME" vagy "SOA" típusú rekordhalmazok nem tartalmazhatnak egynél több rekordot. Ez a megkötés a DNS-szabványokból származik. Ez nem a Azure DNS korlátozása.

## <a name="remove-a-record-from-an-existing-record-set"></a>Rekord eltávolítása meglévő készletből

Egy rekord egy rekordból való eltávolításának folyamata hasonló a rekord meglévő készlethez való hozzáadásának folyamatához:

1. Meglévő rekord beolvasása

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Távolítsa el a rekordot a helyi rekord beállítása objektumból. Ez egy off-line művelet. Az eltávolítandó rekordnak pontosan egyeznie kell egy meglévő rekorddal az összes paraméterben.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Véglegesítse vissza a módosítást a Azure DNS szolgáltatásnak. A nem kötelező `-Overwrite` kapcsolóval tilthatja le az egyidejű módosításokat a [ETAG-ellenőrzésekben](dns-zones-records.md#etags) .

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

A fenti sorozat használatával távolítsa el a rekordok utolsó rekordját a készletből, és ne törölje a rekordot, hanem üres rekordot hagy. A rekordok teljes eltávolításához tekintse meg [a rekord törlését](#delete-a-record-set)ismertető témakört.

Hasonlóképpen, ha rekordokat szeretne felvenni egy rekordhalmazba, a rekordhalmaz eltávolítására szolgáló műveletek sorozata is beállítható:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

A különböző bejegyzéstípusokat a megfelelő típus-specifikus paramétereknek a értékre való átadásával támogatja a rendszer `Remove-AzDnsRecordSet` . Az egyes bejegyzéstípusok paramétereinek megegyeznek a parancsmaghoz tartozó paraméterekkel `New-AzDnsRecordConfig` , ahogy az a fentiekben ismertetett további példákban is látható.


## <a name="modify-an-existing-record-set"></a>Meglévő rekord módosítása

Egy meglévő rekordhalmaz módosításának lépései hasonlóak a rekordhalmaz rekordjainak hozzáadásához vagy eltávolításához szükséges lépésekhez:

1. Kérje le a meglévő rekordot a használatával `Get-AzDnsRecordSet` .
2. Módosítsa a helyi rekordazonosító objektumot a alábbiak szerint:
    * Rekordok hozzáadása vagy eltávolítása
    * Meglévő rekordok paramétereinek módosítása
    * A Record set-metaadatok és az élettartam (TTL) módosítása
3. Véglegesítse a módosításokat a `Set-AzDnsRecordSet` parancsmag használatával. Ez *lecseréli* a meglévő, Azure DNSban lévő rekordot a megadott rekorddal.

A használatakor a `Set-AzDnsRecordSet` [ETAG ellenőrzi](dns-zones-records.md#etags) , hogy az egyidejű módosítások nincsenek-e felülírva. `-Overwrite`Ezeket az ellenőrzéseket a választható kapcsoló használatával tilthatja le.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Meglévő rekordazonosító rekordjának frissítése

Ebben a példában egy meglévő "A" rekord IP-címét módosítjuk:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>SOA-rekord módosítása

Nem adhat hozzá és nem távolíthat el rekordokat az automatikusan létrehozott SOA-rekordhalmazból a zóna csúcsán ( `-Name "@"` beleértve az idézőjeleket is). A SOA-rekordon belül azonban módosíthatja a paramétereket (a "gazdagép" kivételével) és a rekordhalmazt is.

Az alábbi példa bemutatja, hogyan módosíthatja a SOA *-rekord e-mail* tulajdonságát:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS rekordok módosítása a zóna csúcspontján

A zóna csúcsán beállított NS-rekordhalmaz automatikusan létrejön minden DNS-zónával. A zónához rendelt Azure DNS névkiszolgálók nevét tartalmazza.

Ehhez az NS-rekordhoz további névkiszolgálók hozzáadásával több DNS-szolgáltatóval rendelkező közös üzemeltetésű tartományokat is támogathat. Módosíthatja a rekord ÉLETTARTAMát és metaadatait is. Az előre megadott Azure DNS névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Vegye figyelembe, hogy ez csak a zóna csúcsán beállított NS-rekordra vonatkozik. A zónában lévő egyéb NS-rekordhalmazok (a gyermektartomány delegálásához használt módon) korlátozás nélkül módosíthatók.

Az alábbi példa bemutatja, hogyan adhat hozzá egy további névkiszolgálói-t az NS-rekordhalmazhoz a zóna csúcspontján:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>A Record set-metaadatok módosítása

A [rekordszintű metaadatok](dns-zones-records.md#tags-and-metadata) segítségével az alkalmazásspecifikus adatokat az egyes bejegyzéstípusokkal társíthatja kulcs-érték párokként.

Az alábbi példa bemutatja, hogyan módosítható egy meglévő rekordazonosító metaadatai:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Rekord törlése

A rekordhalmazok a parancsmag használatával törölhetők `Remove-AzDnsRecordSet` . Egy rekordhalmaz törlése szintén törli az összes rekordot a rekordhalmazon belül.

> [!NOTE]
> A SOA és az NS rekordhalmazt nem törölheti az APEX () zónában `-Name '@'` .  Azure DNS ezeket automatikusan létrehozta a zóna létrehozásakor, és automatikusan törli őket a zóna törlésekor.

Az alábbi példa azt mutatja be, hogyan lehet törölni a rekordokat. Ebben a példában a rekord készletének neve, a rekordtípus típusa, a zóna neve és az erőforráscsoport explicit módon van megadva.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Másik lehetőségként a rekord neve és típusa is megadható, valamint a megadott zóna a következő objektum használatával:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Harmadik lehetőségként maga a rekord is megadható a Record set objektum használatával:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Ha egy rekordazonosító objektummal adja meg a törlendő rekordot, a rendszer a [ETAG ellenőrzi](dns-zones-records.md#etags) , hogy az egyidejű módosítások ne legyenek törölve. `-Overwrite`Ezeket az ellenőrzéseket a választható kapcsoló használatával tilthatja le.

A rekordazonosító objektum nem lehet a paraméterként átadni, hanem a következőképpen:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` és `Remove-AzDnsRecordSet` parancsmagok mind támogatják a megerősítési kérések használatát.

Minden parancsmag megerősítést kér, ha a `$ConfirmPreference` PowerShell-preferencia változó értéke `Medium` vagy kisebb. Mivel az alapértelmezett értéke a `$ConfirmPreference` , a rendszer `High` nem adja meg ezeket az utasításokat az alapértelmezett PowerShell-beállítások használatakor.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést. 

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](/powershell/module/microsoft.powershell.core/about/about_preference_variables) tartalmazó részben talál.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure DNS található zónákkal és rekordokkal](dns-zones-records.md)kapcsolatban.
<br>
Megtudhatja, hogyan [védhető meg a zónák és a rekordok](dns-protect-zones-recordsets.md) a Azure DNS használatakor.
<br>
Tekintse át a [Azure DNS PowerShell-útmutató dokumentációját](/powershell/module/az.dns).