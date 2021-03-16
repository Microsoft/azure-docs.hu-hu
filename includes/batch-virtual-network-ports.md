---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 02/16/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: baf146bdd89d45c5d7e1ed359822a35d383b7b6c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561914"
---
### <a name="general-requirements"></a>Általános követelmények

* A virtuális hálózatnak a Batch-fiókkal megegyező előfizetésben és régióban kell lennie.

* A készlethez meghatározott alhálózatnak elegendő hozzá nem rendelt IP-címmel kell rendelkeznie ahhoz, hogy helyet tudjon adni a készlethez kijelölt számú virtuális gépnek. Ez a szám a készlet `targetDedicatedNodes` és `targetLowPriorityNodes` tulajdonságának összege. Ha az alhálózaton nincs elegendő hozzá nem rendelt IP-cím, akkor a készlet részlegesen lefoglalja a számítási csomópontokat, és átméretezési hiba következik be.

* Az Azure Storage-végpontot bármely, a virtuális hálózatot kiszolgáló egyéni DNS-kiszolgálónak kell feloldania. Az `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` és `<account>.blob.core.windows.net` űrlap URL-címeinek feloldhatónak kell lenniük.

* Több készlet is létrehozható ugyanabban a VNet, vagy ugyanabban az alhálózatban (feltéve, hogy elegendő címterület van). Egyetlen készlet nem létezhet több virtuális hálózatok vagy alhálózat között.

A virtuális hálózat további követelményei eltérhetnek attól függően, hogy a Batch-készlet a virtuálisgép- vagy a Cloud Services-konfigurációban van-e. Új készlet virtuális hálózatba történő üzembe helyezéséhez a virtuálisgép-konfiguráció javasolt.

### <a name="pools-in-the-virtual-machine-configuration"></a>A virtuálisgép-konfigurációban lévő készletek

**Támogatott virtuális hálózatok** – Csak az Azure Resource Manager-alapú virtuális hálózatok

**Alhálózati azonosító** – Az alhálózat Batch API-kkal történő megadásakor használja az alhálózat *erőforrás-azonosítóját*. Az alhálózat azonosítója a következő formátumot követi:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Engedélyek** – Ellenőrizze, hogy a virtuális hálózat előfizetésén vagy erőforráscsoportján lévő biztonsági szabályzatok vagy zárolások korlátozzák-e egy felhasználó virtuális hálózat kezelésére vonatkozó engedélyét.

**További hálózati erőforrások** – a Batch automatikusan további hálózati erőforrásokat hoz létre a VNet tartalmazó erőforráscsoporthoz.

> [!IMPORTANT]
> Minden 100 dedikált vagy alacsony prioritású csomópont esetében a Batch a következőt hozza létre: egy hálózati biztonsági csoport (NSG), egy nyilvános IP-cím és egy terheléselosztó. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Nagy készletekhez szükség lehet a kvóta egy vagy több erőforrásra való megemelésének igénylésére.

#### <a name="network-security-groups-batch-default"></a>Hálózati biztonsági csoportok: Batch-alapértelmezés

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal, a számítási feladat igényeinek megfelelően. A virtuálisgép-konfigurációban lévő készletekhez a Batch az NSG-ket a számítási csomópontokhoz kapcsolt hálózati adapterek (NIC-k) szintjén adja hozzá. Ezek az NSG-k a következő kiegészítő szabályokkal vannak konfigurálva:

* A Batch szolgáltatás a `BatchNodeManagement` szolgáltatáscímkének megfelelő IP-címeiről érkező bejövő TCP-forgalom a 29876-os és a 29877-es portokon keresztül.
* A bejövő TCP-forgalom a 22-es porton (Linux-csomópontok) vagy a 3389-es porton (Windows-csomópontok) keresztül a távoli hozzáférés engedélyezéséhez. A Linuxon futó többpéldányos feladatok bizonyos típusai (például MPI) esetében is engedélyezni kell a 22-es SSH-port forgalmát a Batch számítási csomópontokat tartalmazó alhálózat IP-címei számára. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján blokkolható.
* Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján módosítható.
* Kimenő forgalom bármilyen porton keresztül az internetre. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján módosítható.

> [!IMPORTANT]
> Körültekintően járjon el, ha a Batch-konfigurált NSG módosítja vagy hozzáadja a bejövő vagy kimenő szabályokat. Ha a megadott alhálózaton lévő számítási csomópontok felé irányuló kommunikációt egy NSG megtagadja, a Batch szolgáltatás a számítási csomópontok állapotát **használhatatlanná** fogja állítani. Emellett nem kell erőforrás-zárolást alkalmazni a Batch által létrehozott összes erőforrásra, mivel ez megakadályozhatja az erőforrások törlését a felhasználó által kezdeményezett műveletek, például a készlet törlése miatt.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Hálózati biztonsági csoportok: Alhálózati szintű szabályok meghatározása

Nem kell megadnia a NSG a virtuális hálózat alhálózatának szintjén, mert a Batch konfigurálja a saját NSG (lásd fent). Ha van olyan NSG társítva az alhálózathoz, ahol a Batch számítási csomópontok vannak telepítve, vagy ha egyéni NSG-szabályokat szeretne alkalmazni az alapértelmezett beállítások felülbírálására, akkor a NSG-t legalább az alábbi táblázatban látható bejövő és kimenő biztonsági szabályokkal kell konfigurálnia.

Csak akkor konfigurálja a bejövő forgalmat a 3389-es porton (Windows) vagy a 22-es porton (Linux) keresztül, ha engedélyeznie kell a számítási csomópontok külső forrásból való távoli elérését. Linux rendszeren előfordulhat, hogy engedélyezni kell a 22-es portra vonatkozó szabályokat, ha többpéldányos, bizonyos MPI-futtatókörnyezetekkel rendelkező feladatok támogatására van szüksége. E portok forgalmának engedélyezése nem feltétlenül szükséges a készletezett számítási csomópontok használhatóságához.

> [!WARNING]
> A Batch szolgáltatás IP-címei idővel módosulhatnak. Ezért javasoljuk, hogy használja a `BatchNodeManagement` szolgáltatás címkéjét (vagy egy regionális változatot) az alábbi táblázatokban jelzett NSG-szabályokhoz. Kerülje a NSG-szabályok feltöltését adott batch szolgáltatás IP-címeivel.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrás szolgáltatáscímke | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[szolgáltatás címkéje](../articles/virtual-network/network-security-groups-overview.md#service-tags) (ha regionális változatot használ, a Batch-fiókkal megegyező régióban) | * | Bármelyik | 29876-29877 | TCP | Engedélyezés |
| Felhasználók forrás IP-címei a számítási csomópontok és/vagy egy számítási csomópont alhálózatának távoli eléréséhez a Linux többpéldányos feladatai esetében, amennyiben szükséges. | N/A | * | Bármelyik | 3389 (Windows), 22 (Linux) | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Cél szolgáltatáscímkéje | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| Bármelyik | * | [Szolgáltatáscímke](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (regionális változat használata esetén, a Batch-fiókkal megegyező régióban) | 443 | TCP | Engedélyezés |
| Bármelyik | * | [Szolgáltatáscímke](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (regionális változat használata esetén, a Batch-fiókkal megegyező régióban) | 443 | TCP | Engedélyezés |

Ahhoz, hogy a `BatchNodeManagement` számítási csomópontok, például a Feladatkezelő-feladatok esetében a Batch szolgáltatáshoz lépjen kapcsolatba, a kimenő érték szükséges.

### <a name="pools-in-the-cloud-services-configuration"></a>Készletek a Cloud Services konfigurációjában

> [!WARNING]
> A Cloud Service-konfigurációs készletek elavultak. Használja helyette a virtuális gépek konfigurációs készleteit.

**Támogatott virtuális hálózatok** – Csak a klasszikus virtuális hálózatok

**Alhálózati azonosító** – Az alhálózat Batch API-kkal történő megadásakor használja az alhálózat *erőforrás-azonosítóját*. Az alhálózat azonosítója a következő formátumot követi:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Engedélyek** – az `Microsoft Azure Batch` egyszerű szolgáltatásnak rendelkeznie kell az `Classic Virtual Machine Contributor` Azure-szerepkörrel a megadott VNet.

#### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal.

Nem kell megadnia NSG-t, mert a Batch szolgáltatás csak a Batch IP-címeiről a készletezett csomópontokra érkező bejövő kommunikációt konfigurálja. Ugyanakkor ha a megadott alhálózathoz hálózati biztonsági csoportok (NSG-k) és/vagy egy tűzfal van társítva, a következő táblázatokban látható módon konfigurálja a bejövő és kimenő biztonsági szabályokat. Ha a megadott alhálózaton lévő számítási csomópontok felé irányuló kommunikációt egy NSG megtagadja, a Batch szolgáltatás nem **használható** állapotba állítja a számítási csomópontok állapotát.

Windows rendszer esetében csak akkor konfigurálja a bejövő forgalmat a 3389-es porton keresztül, ha engedélyeznie kell az RDP-hozzáférést a készletezett csomópontokhoz. Ez nem szükséges ahhoz, hogy a készlet csomópontjai felhasználhatók legyenek.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- |
Bármelyik <br /><br />Bár ehhez tulajdonképpen „az összes engedélyezése” engedély szükséges, a Batch szolgáltatás minden egyes csomópont szintjén alkalmaz egy ACL-szabályt, amely kiszűri az összes olyan IP-címet, amely nem a Batch szolgáltatáshoz tartozik. | * | Bármelyik | 10100, 20100, 30100 | TCP | Engedélyezés |
| Nem kötelező, a számítási csomópontokhoz való RDP-hozzáférés engedélyezésére szolgál. | * | Bármelyik | 3389 | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármelyik | * | Bármelyik | 443  | Bármelyik | Engedélyezés |
