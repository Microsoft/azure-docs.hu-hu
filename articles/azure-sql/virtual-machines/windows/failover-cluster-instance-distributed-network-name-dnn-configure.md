---
title: A feladatátvevő fürt példányához tartozó DNN konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy elosztott hálózati nevet (DNN) az Azure-beli virtuális gépen futó feladatátvevő fürtön lévő SQL Server forgalmának átirányításához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358316"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>DNN konfigurálása feladatátvevő fürt példányához
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure Virtual Machines az elosztott hálózat neve (DNN) átirányítja a forgalmat a megfelelő fürtözött erőforrásra. A szolgáltatás megkönnyíti a virtuális hálózat nevével (VNN) való kapcsolódást a SQL Server feladatátvételi fürt példányához ((), anélkül, hogy szükség lenne egy Azure Load Balancer. 

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy DNN-erőforrást úgy, hogy a magas rendelkezésre álláshoz és a vész-helyreállításhoz (HADR) SQL Server Azure-beli virtuális gépeken keresztül irányítsa a forgalmat 

A DNN szolgáltatás jelenleg csak SQL Server 2019 CU2 és újabb verziókban érhető el a Windows Server 2016-es és újabb verzióiban. 

Alternatív kapcsolódási lehetőségként vegye figyelembe a [virtuális hálózat nevét és Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) . 

## <a name="overview"></a>Áttekintés

Az elosztott hálózat neve (DNN) lecseréli a virtuális hálózat nevét (VNN) a kapcsolódási pontra, ha az [Always On feladatátvételi fürtszolgáltatást használja SQL Server virtuális gépeken](failover-cluster-instance-overview.md). Ezzel a művelettel megtagadja, hogy Azure Load Balancer útválasztási forgalmat a VNN, egyszerűsítse a telepítést, a karbantartást és javítja a feladatátvételt. 

A VNN-alapú üzembe helyezéssel a rendszer továbbra is létezik, de az ügyfél a VNN neve helyett a DNN DNS-nevéhez csatlakozik. 

## <a name="prerequisites"></a>Előfeltételek 

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- SQL Server 2019 CU2 vagy újabb verzióban Windows Server 2016 vagy újabb rendszeren
- Úgy döntött, hogy az elosztott hálózat neve a megfelelő [kapcsolódási lehetőség a HADR-megoldáshoz](hadr-cluster-best-practices.md#connectivity).
- Konfigurálta a [feladatátvevő fürt példányait](failover-cluster-instance-overview.md). 
- Telepítette a [PowerShell](/powershell/azure/install-az-ps)legújabb verzióját. 

## <a name="create-dnn-resource"></a>DNN-erőforrás létrehozása 

A DNN erőforrás ugyanabban a fürtben jön létre, mint a SQL Server-es verzió. A PowerShell használatával hozza létre a DNN-erőforrást a (z)-os fürtön belül. 

A következő PowerShell-parancs egy DNN-erőforrást hoz létre a SQL Server és a (z) egy erőforrás nevével `<dnnResourceName>` . Az erőforrás neve az erőforrás egyedi azonosítására szolgál. Használja az egyiket, amely logikus, és egyedi a fürtben. Az erőforrástípus csak a következő lehet: `Distributed Network Name` . 

Az `-Group` értéknek meg kell egyeznie annak a fürtnek a nevével, amely ahhoz a SQL Server, amelybe az elosztott hálózat nevét hozzá kívánja adni. Az alapértelmezett példány esetében a jellemző formátum a következő: `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Ha például a DNN-erőforrást az `dnn-demo` alapértelmezett SQL Server a következő PowerShell-paranccsal szeretné létrehozni, használja az alábbi PowerShell-parancsot:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Fürt DNN DNS-nevének megadása

Állítsa be a DNN-erőforrás DNS-nevét a fürtben. A fürt ezután ezt az értéket használja arra, hogy átirányítsa a forgalmat a SQL Server-t jelenleg üzemeltető csomópontra. 

Az ügyfelek a DNS-név használatával csatlakoznak a SQL Server. Egyedi értéket is kiválaszthat. Ha már van meglévő, és nem szeretné frissíteni az ügyfél-kapcsolódási karakterláncokat, beállíthatja, hogy a DNN az ügyfelek által már használt aktuális VNN használja. Ehhez [át kell neveznie a VNN](#rename-the-vnn) , mielőtt BEÁLLÍTJA a DNN a DNS-ben.

Ezzel a paranccsal állíthatja be a DNN DNS-nevét: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

Az az `DNSName` érték, amellyel az ügyfelek csatlakoznak a SQL Server. Ahhoz például, hogy az ügyfelek csatlakozzanak a `FCIDNN` szolgáltatáshoz, használja a következő PowerShell-parancsot:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Az ügyfelek mostantól `FCIDNN` a kapcsolati sztringbe lépnek, amikor csatlakoznak a SQL Server-es verzióhoz. 

   > [!WARNING]
   > Ne törölje az aktuális virtuális hálózat nevét (VNN), mert az az-os infrastruktúra szükséges összetevője. 


### <a name="rename-the-vnn"></a>VNN átnevezése 

Ha meglévő virtuális hálózata van, és azt szeretné, hogy az ügyfelek továbbra is ezzel az értékkel csatlakozzanak a SQL Server-es adatkapcsolathoz, át kell neveznie a jelenlegi VNN egy helyőrző értékre. Az aktuális VNN átnevezve megadhatja a DNS-név értékét a DNN a VNN. 

Bizonyos korlátozások vonatkoznak a VNN átnevezésére. További információkért lásd: [a-os átnevezése](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Ha a jelenlegi VNN használata nem szükséges a vállalata számára, ugorja át ezt a szakaszt. Miután átnevezte a VNN, [állítsa be a fürt DNN DNS-nevét](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>DNN-erőforrás online állapotának beállítása

Miután a DNN-erőforrás megfelelő névvel rendelkezik, és beállította a DNS-név értékét a fürtben, a PowerShell használatával állítsa be a DNN-erőforrást online állapotba a fürtben: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

A DNN-erőforrás elindításához például `dnn-demo` használja a következő PowerShell-parancsot: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Lehetséges tulajdonosok konfigurálása

Alapértelmezés szerint a fürt a fürt összes csomópontja számára a DNN DNS-nevet köti. Azonban a fürt azon csomópontjait, amelyek nem részei a (SQL Server) verziónak, ki kell zárni a lehetséges tulajdonosok DNN listájából. 

A lehetséges tulajdonosok frissítéséhez kövesse az alábbi lépéseket:

1. Lépjen a DNN-erőforráshoz Feladatátvevőfürt-kezelő. 
1. Kattintson a jobb gombbal a DNN-erőforrásra, és válassza a **Tulajdonságok** lehetőséget. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="A DNN erőforrás helyi menüje, a tulajdonságok paranccsal kiemelve.":::

1. Törölje a jelet a feladatátvevő fürt példányában nem részt vevő csomópontok jelölőnégyzetéből. A DNN-erőforrás lehetséges tulajdonosainak listáját meg kell egyeznie a SQL Server példány-erőforrás lehetséges tulajdonosainak listájával. Tegyük fel például, hogy a data3 nem vesz részt a (z)-es verzióban, az alábbi képen egy példa a DNN-erőforrás lehetséges tulajdonosainak listájáról való data3 eltávolítására: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Törölje a jelölőnégyzet jelölését azon csomópontok mellett, amelyek nem vesznek részt a DNN-erőforrás lehetséges tulajdonosainak":::

1. A beállítások mentéséhez kattintson **az OK gombra** . 


## <a name="restart-sql-server-instance"></a>SQL Server példány újraindítása 

A SQL Server példány újraindításához használja a Feladatátvevőfürt-kezelő. Kövesse az alábbi lépéseket:

1. Nyissa meg Feladatátvevőfürt-kezelő SQL Server erőforrását.
1. Kattintson a jobb gombbal a SQL Server erőforrásra, és kapcsolja offline állapotba. 
1. Miután az összes kapcsolódó erőforrás offline állapotba került, kattintson a jobb gombbal a SQL Server erőforrásra, majd újra online állapotba. 

## <a name="update-connection-string"></a>A kapcsolatok karakterláncának frissítése

A feladatátvétel gyors elérésének biztosítása érdekében vegye fel `MultiSubnetFailover=True` a kapcsolatot a kapcsolati karakterláncba, ha az SQL-ügyfél verziója a 4.6.1-nál korábbi. 

Ha a DNN nem az eredeti VNN használja, akkor a SQL Server-os-hez csatlakozó SQL-ügyfeleknek frissíteniük kell a kapcsolati karakterláncot a DNN DNS-nevére. A követelmény elkerüléséhez frissítse a DNS-név értéket a VNN nevére. [A meglévő VNN](#rename-the-vnn) azonban először a helyőrzőre kell cserélni. 

## <a name="test-failover"></a>Feladatátvétel tesztelése


A fürtözött erőforrás feladatátvételi tesztje a fürt működésének ellenőrzéséhez. 


A feladatátvétel teszteléséhez kövesse az alábbi lépéseket: 

1. Kapcsolódjon az egyik SQL Server fürtcsomópontok valamelyikéhez RDP használatával.
1. Nyissa meg **Feladatátvevőfürt-kezelő**. Válassza a **szerepkörök** lehetőséget. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.
1. Kattintson a jobb gombbal a SQL Server. 
1. Válassza az **Áthelyezés**, majd a **lehető legjobb csomópont** lehetőséget.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Ezután az erőforrások átkerülnek a másik csomópontba, majd ismét elérhetővé válnak.

## <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg **SQL Server Management Studio** , és kapcsolódjon a SQL Server a DNN DNS-név használatával.

Ha szükséges, [letöltheti SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>Az IP-ütközés elkerülése

Ez egy opcionális lépés, amellyel megakadályozható, hogy a (z)%-os erőforrás által használt virtuális IP-cím (VIP) duplikált módon legyen hozzárendelve az Azure-beli másik erőforráshoz. 

Bár az ügyfelek mostantól a DNN csatlakoznak a SQL Server-es verzióhoz, a virtuális hálózat nevét (VNN) és a virtuális IP-címet nem lehet törölni, mert a szükséges összetevők a (z)%-os infrastruktúra számára. Mivel azonban már nincs olyan terheléselosztó, amely az Azure-beli virtuális IP-címet használja, fennáll a veszélye annak, hogy a virtuális hálózat egy másik erőforrása ugyanahhoz az IP-címhez lesz hozzárendelve, mint a (z) által használt virtuális IP-cím. Ez esetleg ismétlődő IP-ütközési problémát eredményezhet. 

Konfiguráljon egy APIPA-címet vagy egy dedikált hálózati adaptert az IP-cím lefoglalásához. 

### <a name="apipa-address"></a>APIPA-címek

Az ismétlődő IP-címek használatának elkerüléséhez konfigurálja az APIPA-címeket (más néven a kapcsolat helyi címét). Ehhez futtassa az alábbi parancsot:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

Ebben a parancsban a "virtuális IP-cím" a fürtözött VIP-cím erőforrás neve, a "169.254.1.1" pedig a VIP-címhez kiválasztott APIPA-cím. Válassza ki az Ön üzleti igényeinek leginkább megfelelő címeket. Állítsa be `OverrideAddressMatch=1` úgy, hogy az IP-cím bármilyen hálózaton legyen, beleértve az APIPA-címtartomány használatát is. 

### <a name="dedicated-network-adapter"></a>Dedikált hálózati adapter

Másik lehetőségként beállíthat egy hálózati adaptert az Azure-ban a virtuális IP-cím erőforrás által használt IP-cím lefoglalásához. Ez azonban az alhálózati címtartomány során felhasználja a címeket, és a hálózati adapter más célra való használatának biztosítása további terhelést jelent.

## <a name="limitations"></a>Korlátozások

- Jelenleg egy DNN-vel rendelkező CU2 csak a Windows Server 2016-es és újabb verzióiban támogatott a SQL Server 2019-es és újabb verziókban. 
- További szempontokat is figyelembe kell venni, ha más SQL Server-funkciókkal dolgozik, és egy DNN-vel rendelkező egy-egy-egy-egy-egy További információ: [DNN együttműködési képesség](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure SQL Server HADR szolgáltatásairól, tekintse meg a [rendelkezésre állási csoportok](availability-group-overview.md) és a [feladatátvevő fürt példánya](failover-cluster-instance-overview.md)című témakört. Megtudhatja, [Hogyan](hadr-cluster-best-practices.md) konfigurálhatja a környezetet a magas rendelkezésre álláshoz és a vész-helyreállításhoz. 


