---
title: A Azure DNS DNS-rekordjainak kezelése az Azure CLI használatával
description: A DNS-rekordhalmazok és-rekordok kezelése Azure DNS a tartomány Azure DNS-on való üzemeltetése során.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011565"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>DNS-rekordok és-rekordhalmazok kezelése Azure DNS az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónához tartozó DNS-rekordokat a platformfüggetlen Azure CLI használatával, amely Windows, Mac és Linux rendszereken érhető el. A DNS-rekordokat [Azure PowerShell](dns-operations-recordsets.md) vagy a [Azure Portal](dns-operations-recordsets-portal.md)használatával is kezelheti.

A cikkben szereplő példák feltételezik, hogy már [telepítette az Azure CLI-t, bejelentkezett, és létrehozott egy DNS-zónát](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Bevezetés

Mielőtt létrehozná a DNS-rekordokat Azure DNS-ben, tisztában kell lennie azzal, hogyan rendezi az Azure DNS DNS-rekordhalmazokba a DNS-rekordokat.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Az Azure DNS DNS-rekordjaival kapcsolatos további információért tekintse meg a [DNS-zónákkal és -rekordokkal](dns-zones-records.md) foglalkozó cikket.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekord létrehozásához használja a `az network dns record-set <record-type> add-record` parancsot (ahol `<record-type>` a a rekord típusa, azaz a, SRV, txt stb.) További segítségért lásd: `az network dns record-set --help` .

Egy rekord létrehozásakor meg kell adni az erőforráscsoport, a zóna és a rekordhalmaz nevét, a rekordtípust és a létrehozandó rekord részletes adatait. A rekordtípus nevének *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zóna nevét.

Ha a rekordhalmaz még nem létezik, akkor a parancs létrehozza. Ha az adott rekordhalmaz már létezik, a parancs felveszi a megadott rekordot a meglévő rekordhalmazba.

Új rekordhalmaz létrehozásakor az alapértelmezett élettartam (time-to-live, TTL) értéke 3600 lesz. A különböző TTLs-használattal kapcsolatos utasításokért lásd: [DNS-rekordtípus létrehozása](#create-a-dns-record-set).

Az alábbi példaparancs a *MyResourceGroup* erőforráscsoport *contoso.com* zónájában egy *www* nevű, „A” típusú rekordot hoz létre. Az „A” rekord IP-címe: *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

A zóna csúcsán lévő rekordhalmaz létrehozásához (ebben az esetben "contoso.com") használja a "" nevű rekordot \@ , beleértve az idézőjeleket:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>DNS-rekord létrehozása

A fenti példákban a DNS-rekordot hozzáadták egy meglévő készlethez, vagy a rekordot *implicit módon* hozták létre. A rekordhalmaz *explicit módon* is létrehozható, mielőtt rekordokat adna hozzá. A Azure DNS támogatja az "üres" rekordhalmazokat, amelyek helyőrzőként működhetnek a DNS-név lefoglalásához a DNS-rekordok létrehozása előtt. Az üres rekordhalmazok a Azure DNS vezérlő síkon láthatók, de nem jelennek meg a Azure DNS névkiszolgálók között.

A rekordhalmazok a parancs használatával jönnek létre `az network dns record-set <record-type> create` . További segítségért lásd: `az network dns record-set <record-type> create --help`.

A rekord explicit módon történő létrehozása lehetővé teszi a rekordtípusok tulajdonságainak (például az [élettartam (TTL)](dns-zones-records.md#time-to-live) és a metaadatok) megadását. A [rekordszintű metaadatok](dns-zones-records.md#tags-and-metadata) segítségével az alkalmazásspecifikus adatokat az egyes bejegyzéstípusokkal társíthatja kulcs-érték párokként.

Az alábbi példa egy "A" típusú üres rekordot hoz létre egy 60-Second TTL-típussal a következő `--ttl` paraméter használatával: (rövid formátum `-l` ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

A következő példa egy olyan rekordot hoz létre, amely két metaadat-bejegyzést tartalmaz: "dept = Finance" és "Environment = Production", a (z) `--metadata` paraméter használatával:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Egy üres rekordhalmaz létrehozása után a rekordok hozzáadhatók a `azure network dns record-set <record-type> add-record` [DNS-rekord létrehozása](#create-a-dns-record)című témakörben leírtak szerint.

## <a name="create-records-of-other-types"></a>Más típusú rekordok létrehozása

Az "A" rekordok létrehozásának részletes ismertetését az alábbi példák bemutatják, hogyan hozhat létre a Azure DNS által támogatott egyéb bejegyzéstípusokat.

A rekordadatok megadásához használt paraméterek a rekord típusától függnek. Az „A” típusú rekordok esetén például a `--ipv4-address <IPv4 address>` paraméterrel lehet megadni az IPv4-címet. Az egyes bejegyzéstípusok paramétereinek felsorolása a következő használatával lehetséges: `az network dns record-set <record-type> add-record --help` .

Minden esetben bemutatjuk, hogyan hozhat létre egyetlen rekordot. A rekord hozzá lett adva a meglévő készlethez, vagy egy, implicit módon létrehozott rekordazonosító. A rekordhalmazok létrehozásával és a rekordhalmaz paraméter explicit módon való definiálásával kapcsolatos további információkért lásd: [DNS-rekordhalmaz létrehozása](#create-a-dns-record-set).

Nem adunk példát egy SOA-rekord létrehozására, mivel a SOAs létrehozása és törlése minden DNS-zónával megtörtént, és nem lehet külön létrehozni vagy törölni. [A SOA azonban módosítható, ahogy az egy későbbi példában is látható](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>AAAA-rekord létrehozása

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>CAA-rekord létrehozása

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>CNAME-rekord létrehozása

> [!NOTE]
> A DNS-szabványok nem engedélyezik a CNAME rekordokat egy zóna () csúcsán `--Name "@"` , és nem teszik lehetővé, hogy az egynél több rekordot tartalmazó rekordhalmazok.
> 
> További információ: [CNAME rekordok](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>MX-rekord létrehozása

Ebben a példában a rekordhalmaz neve \@ ("") használatával hozza létre az MX-rekordot a zóna csúcsán (ebben az esetben "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS-rekord létrehozása

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR-rekord létrehozása

Ebben az esetben a "my-arpa-zone.com" az IP-címtartományt jelképező ARPA zónát jelöli. A zóna minden PTR típusú rekordhalmaza az IP-címtartomány egyik IP-címének felel meg.  A "10" nevű rekord a rekordban szereplő IP-címtartomány utolsó oktettje.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>SRV-rekord létrehozása

[SRV-rekordok](dns-zones-records.md#srv-records)létrehozásakor adja meg a *\_ szolgáltatást* és a *\_ protokollt* a rekord készletének nevében. \@Ha SRV-rekordhalmazt hoz létre a zóna csúcspontján, a rekordhalmaz nevében nem szükséges a "" kifejezés belefoglalása.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>TXT-rekord létrehozása

Az alábbi példa azt szemlélteti, hogyan hozható létre TXT-rekord. A TXT-rekordok által támogatott maximális karakterlánc hosszával kapcsolatos további információkért lásd: [TXT-rekordok](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Rekord beolvasása

Meglévő beállításhalmaz beolvasásához használja a következőt: `az network dns record-set <record-type> show` . További segítségért lásd: `az network dns record-set <record-type> show --help`.

Egy rekord vagy rekord létrehozásakor a megadott rekordazonosító nevének *relatív* névnek kell lennie, ami azt jelenti, hogy ki kell zárnia a zóna nevét. Meg kell adnia a rekord típusát, a rekordot tartalmazó zónát és a zónát tartalmazó erőforráscsoportot is.

A következő példa az A típusú *www* -rekordot kérdezi le a (z) *MyResourceGroup* erőforráscsoport *contoso.com* :

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Rekordhalmazok listázása

A DNS-zónák összes rekordját a parancs használatával listázhatja `az network dns record-set list` . További segítségért lásd: `az network dns record-set list --help`.

Ez a példa a zóna *contoso.com* összes rekordhalmazát adja vissza az erőforráscsoport *MyResourceGroup*, a név vagy a rekordtípus típusától függően:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Ez a példa az összes olyan rekordhalmazt adja vissza, amelyek megfelelnek a megadott bejegyzéstípusnak (ebben az esetben az "A" rekordokat):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Rekord hozzáadása meglévő készlethez

A mindkettő használatával `az network dns record-set <record-type> add-record` létrehozhat egy rekordot egy új rekorddal, vagy hozzáadhat egy rekordot egy meglévő készlethez.

További információ: [DNS-rekord létrehozása](#create-a-dns-record) és [rekordok létrehozása a fenti más típusokból](#create-records-of-other-types) .

## <a name="remove-a-record-from-an-existing-record-set"></a>Rekord eltávolítása egy meglévő készletből.

DNS-rekord meglévő készletből való eltávolításához használja a következőt: `az network dns record-set <record-type> remove-record` . További segítségért lásd: `az network dns record-set <record-type> remove-record -h`.

Ez a parancs törli a DNS-rekordot egy rekordból. Ha a rekord utolsó rekordját törli, a rendszer szintén törli a rekordot. Ha inkább az üres rekordot szeretné megőrizni, használja a `--keep-empty-record-set` kapcsolót.

Meg kell adnia a törlendő rekordot és azt a zónát, amelyből törölni szeretné a rekordot a használatával `az network dns record-set <record-type> add-record` . Ezek a paraméterek a [DNS-rekord létrehozása](#create-a-dns-record) és a fenti [más típusú rekordok létrehozása](#create-records-of-other-types) című témakörben találhatók.

A következő példa törli a "1.2.3.4" értékkel rendelkező rekordot a (z) *contoso.com*, az erőforráscsoport *MyResourceGroup* található *www* nevű rekordból.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Meglévő rekord módosítása

Mindegyik rekordhalmaz [élettartam (TTL)](dns-zones-records.md#time-to-live), [metaadatok](dns-zones-records.md#tags-and-metadata)és DNS-rekordokat tartalmaz. A következő szakaszokban megtudhatja, hogyan módosíthatja ezeket a tulajdonságokat.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Egy, AAAA, CAA, MX, NS, PTR, SRV vagy TXT rekord módosítása

Az A, AAAA, CAA, MX, NS, PTR, SRV vagy TXT típusú meglévő rekordok módosításához először vegyen fel egy új rekordot, majd törölje a meglévő rekordot. A rekordok törlésével és hozzáadásával kapcsolatos részletes utasításokért tekintse meg a cikk korábbi szakaszait.

Az alábbi példa azt szemlélteti, hogyan lehet módosítani egy "A" rekordot az IP-1.2.3.4 az IP-5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Az automatikusan létrehozott NS-rekordhalmaz rekordjait nem lehet felvenni, eltávolítani vagy módosítani a zóna csúcspontján (az `--Name "@"` idézőjelekkel együtt). Ennél a rekordnál a csak az engedélyezett módosítások módosíthatják a set TTL és a metaadatokat.

### <a name="to-modify-a-cname-record"></a>CNAME rekord módosítása

A legtöbb más bejegyzéstípustól eltérően a CNAME-rekordok halmaza csak egyetlen rekordot tartalmazhat.  Ezért az aktuális értéket nem lehet lecserélni új rekord hozzáadásával és a meglévő rekord eltávolításával, a többi bejegyzéstípushoz hasonlóan.

A CNAME rekord módosításához használja a parancsot `az network dns record-set cname set-record` . További segítségért lásd: `az network dns record-set cname set-record --help`

A példa módosítja a (z) *www* CNAME-rekordot a (z) *contoso.com*, az erőforráscsoport *MyResourceGroup*, hogy a meglévő értéke helyett a "www.fabrikam.net" értékre mutasson:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>SOA-rekord módosítása

A legtöbb más bejegyzéstípustól eltérően a CNAME-rekordok halmaza csak egyetlen rekordot tartalmazhat.  Ezért az aktuális értéket nem lehet lecserélni új rekord hozzáadásával és a meglévő rekord eltávolításával, a többi bejegyzéstípushoz hasonlóan.

A SOA-rekord módosításához használja a parancsot `az network dns record-set soa update` . További segítségért lásd: `az network dns record-set soa update --help`.

Az alábbi példa bemutatja, hogyan állíthatja be a SOA rekord "e-mail" tulajdonságát a zóna *contoso.com* az erőforráscsoport *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS rekordok módosítása a zóna csúcspontján

A zóna csúcsán beállított NS-rekordhalmaz automatikusan létrejön minden DNS-zónával. A zónához rendelt Azure DNS névkiszolgálók nevét tartalmazza.

Ehhez az NS-rekordhoz további névkiszolgálók hozzáadásával több DNS-szolgáltatóval rendelkező közös üzemeltetésű tartományokat is támogathat. Módosíthatja a rekord ÉLETTARTAMát és metaadatait is. Az előre megadott Azure DNS névkiszolgálók azonban nem távolíthatók el és nem módosíthatók.

Vegye figyelembe, hogy ez csak a zóna csúcsán beállított NS-rekordra vonatkozik. A zónában lévő egyéb NS-rekordhalmazok (a gyermektartomány delegálásához használt módon) korlátozás nélkül módosíthatók.

Az alábbi példa bemutatja, hogyan adhat hozzá egy további névkiszolgálói-t az NS-rekordhalmazhoz a zóna csúcspontján:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Meglévő rekordazonosító ÉLETTARTAMának módosítása

Meglévő beállításhalmaz ÉLETTARTAMának módosításához használja a következőt: `azure network dns record-set <record-type> update` . További segítségért lásd: `azure network dns record-set <record-type> update --help`.

Az alábbi példa azt szemlélteti, hogyan lehet módosítani egy rekordot TTL-t, ebben az esetben 60 másodpercet:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Meglévő halmaz metaadatainak módosítása

A [rekordszintű metaadatok](dns-zones-records.md#tags-and-metadata) segítségével az alkalmazásspecifikus adatokat az egyes bejegyzéstípusokkal társíthatja kulcs-érték párokként. Meglévő beállításhalmaz metaadatainak módosításához használja a következőt: `az network dns record-set <record-type> update` . További segítségért lásd: `az network dns record-set <record-type> update --help`.

Az alábbi példa bemutatja, hogyan lehet módosítani egy olyan rekordot, amely két metaadat-bejegyzést tartalmaz: "dept = Finance" és "Environment = Production". Vegye figyelembe, hogy a meglévő metaadatokat a megadott értékek *váltja* fel.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Rekord törlése

A rekordhalmazokat a paranccsal lehet törölni `az network dns record-set <record-type> delete` . További segítségért lásd: `azure network dns record-set <record-type> delete --help`. Egy rekordhalmaz törlése szintén törli az összes rekordot a rekordhalmazon belül.

> [!NOTE]
> A SOA és az NS rekordhalmazt nem törölheti az APEX () zónában `--name "@"` .  Ezek automatikusan létrejönnek a zóna létrehozásakor, és automatikusan törlődnek a zóna törlésekor.

A következő példa törli az A típusú *www* -t a (z) *contoso.com* erőforráscsoport *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

A rendszer felszólítja, hogy erősítse meg a törlési műveletet. A kérdés mellőzéséhez használja a `--yes` kapcsolót.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure DNS található zónákkal és rekordokkal](dns-zones-records.md)kapcsolatban.
<br>
Megtudhatja, hogyan [védhető meg a zónák és a rekordok](dns-protect-zones-recordsets.md) a Azure DNS használatakor.
