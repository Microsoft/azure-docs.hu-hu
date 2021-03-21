---
title: Hálózati hozzáférés-vezérlés
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: A Azure SQL Database és az Azure szinapszis Analytics hálózati hozzáférésének kezelése és szabályozása.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460007"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>A Azure SQL Database és az Azure szinapszis Analytics hálózati hozzáférés-vezérlés

Ha Azure SQL Database és az Azure szinapszis Analytics [Azure Portal](single-database-create-quickstart.md) hoz létre logikai SQL Server-kiszolgálót, az eredmény egy nyilvános végpont, amely a következő formátumú: *yourservername.database.Windows.net*.

A következő hálózati hozzáférés-vezérlésekkel szelektíven engedélyezheti az adatbázisokhoz való hozzáférést a nyilvános végponton keresztül:

- Azure-szolgáltatások engedélyezése: Ha be van kapcsolva, az Azure határán belüli egyéb erőforrások, például az Azure-beli virtuális gépek, hozzáférhetnek SQL Database
- IP-tűzfalszabályok: ezzel a funkcióval explicit módon engedélyezheti a kapcsolódást egy adott IP-címről, például a helyszíni gépekről.

Az adatbázishoz való privát hozzáférést is engedélyezheti a [virtuális hálózatoktól](../../virtual-network/virtual-networks-overview.md) a következő módon:

- Virtuális hálózati tűzfalszabályok: ezzel a szolgáltatással engedélyezheti az Azure határán belüli adott virtuális hálózat forgalmát
- Privát hivatkozás: ezzel a szolgáltatással létrehozhat egy privát végpontot a [logikai SQL Server](logical-servers.md) számára egy adott virtuális hálózaton belül

> [!IMPORTANT]
> Ez a cikk *nem* vonatkozik az **SQL felügyelt példányára**. A hálózati konfigurációval kapcsolatos további információkért lásd: [Csatlakozás az Azure SQL felügyelt példányához](../managed-instance/connect-application-instance.md) .

Tekintse meg az alábbi videót, amely részletesen ismerteti ezeket a hozzáférés-vezérléseket, és hogy mit tesznek:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure-szolgáltatások engedélyezése

Alapértelmezés szerint a Azure Portal új logikai SQL [-](single-database-create-quickstart.md)kiszolgáló létrehozásakor ez a beállítás **ki van kapcsolva**. Ez a beállítás akkor jelenik meg, ha a kapcsolat engedélyezett a nyilvános szolgáltatási végpont használatával.

Ezt a beállítást a tűzfal ablaktáblán is módosíthatja, miután a logikai SQL-kiszolgáló létrejött a következőképpen.
  
![Képernyőkép a kiszolgáló tűzfalának kezeléséről][2]

Ha **a** be értékre van állítva, a kiszolgáló engedélyezi az Azure határán belüli összes erőforrás kommunikációját, amely esetleg nem része az előfizetésnek.

Sok esetben a **on** beállítás nagyobb mértékben megengedhető, mint amit a legtöbb ügyfél szeretne. Előfordulhat, hogy ezt a beállítást ki szeretné **kapcsolni** , és lecseréli a szigorúbb IP-tűzfalszabályok vagy a virtuális hálózati tűzfalszabályok használatával. 

Ez azonban hatással van az Azure-beli virtuális gépeken futó alábbi szolgáltatásokra, amelyek nem részei a virtuális hálózatnak, ezért az Azure IP-címen keresztül csatlakoznak az adatbázishoz:

### <a name="import-export-service"></a>Exportálási szolgáltatás importálása

Az importálási exportálási szolgáltatás nem működik, ha az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás **ki van kapcsolva**. Azonban a probléma megoldásához manuálisan is [futtathatja sqlpackage.exe egy Azure](./database-import-export-azure-services-off.md) -beli virtuális gépről, vagy közvetlenül a kódban végezheti el az exportálást a DACFx API használatával.

### <a name="data-sync"></a>Adatszinkronizálás

Ahhoz, hogy az adatszinkronizálási funkciót az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** **kikapcsolva** értékre állítsa, egyedi tűzfalszabály-bejegyzéseket kell létrehoznia, hogy az **SQL-szolgáltatás címkéje** alapján [adja hozzá az IP-címeket](firewall-create-server-level-portal-quickstart.md) a **hub** -adatbázist üzemeltető régióhoz.
Adja hozzá ezeket a kiszolgálói szintű tűzfalszabályok a **hub** -és **tag** -adatbázisokat üzemeltető kiszolgálókhoz (amelyek különböző régiókban lehetnek)

Az alábbi PowerShell-szkripttel állítson elő IP-címeket az USA nyugati régiójában lévő SQL Service-címkének megfelelően

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag az SQL Service-címke globális tartományát adja vissza a Location paraméter megadása ellenére. Ügyeljen arra, hogy a szinkronizálási csoport által használt hub-adatbázist üzemeltető régióra szűrje

Vegye figyelembe, hogy a PowerShell-szkript kimenete osztály nélküli Inter-Domain útválasztási (CIDR) jelöléssel rendelkezik. Ezt a kezdő és a záró IP-cím formátumára kell konvertálni a [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) hasonló módon:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Ezt a további PowerShell-parancsfájlt használhatja a CIDR összes IP-címének a kezdő és a záró IP-cím formátumra való átalakításához.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Mostantól megadhatja ezeket különálló tűzfalszabályokként, majd beállíthatja, **hogy az Azure-szolgáltatások hozzáférjenek a kiszolgálóhoz**  .

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-alapú tűzfal az Azure-beli logikai SQL Server egyik funkciója, amely megakadályozza a kiszolgáló összes hozzáférését, amíg explicit módon nem [adja hozzá az ügyfélszámítógépek IP-címeit](firewall-create-server-level-portal-quickstart.md) .

## <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai

Az IP-szabályok mellett a kiszolgáló tűzfala lehetővé teszi a *virtuális hálózati szabályok* megadását.  
További információ: [Virtual Network szolgáltatás-végpontok és szabályok Azure SQL Database](vnet-service-endpoint-rule-overview.md) vagy a videó megtekintése:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure hálózati terminológia

A virtuális hálózati tűzfalszabályok megismerése során vegye figyelembe a következő Azure-hálózatkezelési feltételeket

**Virtuális hálózat:** Az Azure-előfizetéshez társított virtuális hálózatokkal is rendelkezhet

**Alhálózat:** Egy virtuális hálózat **alhálózatokat** tartalmaz. Az alhálózatokhoz társított bármely Azure-beli virtuális gép (VM). Egy alhálózat több virtuális gépet vagy más számítási csomópontot is tartalmazhat. A virtuális hálózatán kívüli számítási csomópontok nem férnek hozzá a virtuális hálózathoz, kivéve, ha úgy konfigurálja a biztonságot, hogy engedélyezze a hozzáférést.

**Virtuális hálózati szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. Ebben a cikkben a **Microsoft. SQL** típus neve érdekli, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik.

**Virtuális hálózati szabály:** A kiszolgálóhoz tartozó virtuális hálózati szabály egy alhálózat, amely a kiszolgáló hozzáférés-vezérlési listájában (ACL) szerepel. Ahhoz, hogy a SQL Databaseban lévő adatbázis ACL-je legyen, az alhálózatnak tartalmaznia kell a **Microsoft. SQL** típus nevét. Egy virtuális hálózati szabály közli a kiszolgálóval, hogy fogadja a kommunikációt az alhálózaton lévő összes csomópontról.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP-cím és virtuális hálózati tűzfalszabályok

A Azure SQL Database tűzfal lehetővé teszi olyan IP-címtartományok megadását, amelyekről a rendszer a kommunikációt SQL Databaseba fogadja. Ez a megközelítés az Azure-magánhálózaton kívüli stabil IP-címekre is kiváló. Az Azure-beli magánhálózaton lévő virtuális gépek (VM-EK) azonban *dinamikus* IP-címekkel vannak konfigurálva. A dinamikus IP-címek a virtuális gép újraindításakor változhatnak, és az IP-alapú tűzfalszabály érvénytelenné válik. Az éles környezetben nem lehet dinamikus IP-címet megadni egy tűzfalszabály számára.

Ezt a korlátozást a virtuális gép *statikus* IP-címének beszerzésével lehet megkerülni. Részletekért lásd: [virtuális gép létrehozása statikus nyilvános IP-címmel a Azure Portal használatával](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). A statikus IP-cím azonban nehezen kezelhető, és a méretezés során költséges lehet.

A virtuális hálózati szabályok egyszerűbben használhatók a virtuális gépeket tartalmazó adott alhálózat elérésének létrehozásához és kezeléséhez.

> [!NOTE]
> Még nem rendelkezhet SQL Database alhálózaton. Ha a kiszolgáló a virtuális hálózat egyik alhálózatának csomópontja volt, a virtuális hálózaton belüli összes csomópont kommunikálhat a SQL Databaseával. Ebben az esetben a virtuális gépek kommunikálhatnak a SQL Database anélkül, hogy virtuális hálózati szabályokat vagy IP-szabályokat kellene megadnia.

## <a name="private-link"></a>Private Link

A privát hivatkozás lehetővé teszi, hogy egy **privát végponton** keresztül kapcsolódjon egy kiszolgálóhoz. A privát végpont egy magánhálózati IP-cím egy adott [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) és alhálózaton belül.

## <a name="next-steps"></a>Következő lépések

- A kiszolgálói szintű IP-tűzfalszabály létrehozásával kapcsolatos rövid útmutató: [adatbázis létrehozása SQL Databaseban](single-database-create-quickstart.md).

- A kiszolgálói szintű virtuális hálózati tűzfalszabály létrehozásával kapcsolatos rövid útmutatóért lásd: [Virtual Network szolgáltatási végpontok és a Azure SQL Database szabályai](vnet-service-endpoint-rule-overview.md).

- A nyílt forráskódú vagy harmadik féltől származó alkalmazásokból származó SQL Database adatbázishoz való csatlakozással kapcsolatos segítségért lásd: ügyfél-útmutató [kódok mintái SQL Database](/previous-versions/azure/ee336282(v=azure.100)).

- További információ a megnyitható további portokról **: SQL Database** a [ADO.net 4,5-as és a 1433 SQL Database-es porton túli portok](adonet-v12-develop-direct-route-ports.md) területén

- Az Azure SQL Database kapcsolatok áttekintését lásd: [Azure SQL connectivity Architecture](connectivity-architecture.md)

- A Azure SQL Database biztonság áttekintését lásd: [az adatbázis biztonságossá tétele](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
