---
title: A rendelkezésre állási csoportokkal és a DNN-figyelővel való együttműködés
description: 'Ismerje meg az Azure-beli virtuális gépeken futó always on rendelkezésre állási SQL Server csoporttal rendelkező egyes SQL Server szolgáltatások és az elosztott hálózati név (DNN) figyelővel való együttműködéssel kapcsolatos további szempontokat. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359404"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Funkciók együttműködése az AG-val és a DNN-figyelővel 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Vannak olyan SQL Server szolgáltatások, amelyek egy rögzített virtuális hálózat nevét (VNN) használják. Ilyen esetben, ha az Always On rendelkezésre állási csoporttal és az Azure-beli virtuális gépeken SQL Server az elosztott hálózat neve (DNN) figyelőt használja, előfordulhat, hogy további szempontokat is figyelembe kell vennie. 

Ez a cikk részletesen ismerteti SQL Server funkcióit, és együttműködik a rendelkezésre állási csoport DNN-figyelővel. 


## <a name="client-drivers"></a>Ügyfélillesztők

ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-és Node.js-illesztőprogramok esetén a felhasználóknak explicit módon meg kell adniuk a DNN-figyelő nevét és portját a kapcsolódási karakterláncban. A feladatátvétel gyors elérésének biztosításához adja hozzá `MultiSubnetFailover=True` a kapcsolati sztringet, ha az SQL-ügyfél támogatja azt. 

## <a name="tools"></a>Eszközök

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)és [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) felhasználóinak explicit módon meg kell adniuk a DNN figyelő nevét és portját a kapcsolati sztringben a figyelőhöz való csatlakozáshoz. 

A DNN-figyelő a SQL Server Management Studio (SSMS) grafikus felhasználói felülettel való létrehozása jelenleg nem támogatott. 


## <a name="availability-groups-and-fci"></a>Rendelkezésre állási csoportok és a verzió

Az Always On rendelkezésre állási csoportot a feladatátvevő fürt példányai (verzió) használatával konfigurálhatja a replikák egyikére. Ahhoz, hogy ez a konfiguráció működjön együtt a DNN-figyelővel, a [feladatátvevő fürt példányának a DNN is használnia kell](failover-cluster-instance-distributed-network-name-dnn-configure.md) , mert nincs mód arra, hogy a virtuális IP-címet az AG DNN IP-listába helyezze. 

Ebben a konfigurációban a (DNN)-replikához tartozó tükrözési végpont URL-címének a-t kell használnia. Hasonlóképpen, ha a rendszer csak olvasható replikát használ, a csak olvasható útválasztásnak az DNN-t kell használnia. 

A tükrözési végpont formátuma a következő: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Ha például a DNN DNS-neve az `dnnlsnr` , és az a `5022` (z) és a (z)%-os tükrözési végpontjának portja, a rendszer a végpont URL-címének létrehozásához a Transact-SQL (T-SQL) kódrészletet keresi: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Hasonlóképpen, a csak olvasható útválasztási URL-cím formátuma a következő: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Ha például a DNN DNS-neve, a `dnnlsnr` pedig a `1444` csak olvasási célként megadott SQL Server-es számú port, a T-SQL-kódrészlet a csak olvasható útválasztási URL-cím létrehozásához a következőképpen néz ki: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Ha az alapértelmezett 1433-as port, akkor kihagyhatja a portot az URL-címben. Megnevezett példány esetében állítson be egy statikus portot a megnevezett példányhoz, és adja meg azt a csak olvasható útválasztási URL-címben.  

## <a name="distributed-availability-group"></a>Elosztott rendelkezésre állási csoport

Az elosztott rendelkezésre állási csoportok jelenleg nem támogatottak a DNN-figyelővel. 

## <a name="replication"></a>Replikálás

Tranzakciós, egyesítési és pillanatkép-replikáció – a VNN-figyelőt a DNN figyelővel és a figyelőhöz csatlakozó replikációs objektumok portjával helyettesíti. 

További információ a rendelkezésre állási csoportokkal való replikáció használatáról: [közzétevő és AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [előfizető és AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server), valamint [terjesztő és AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

Mind a helyi, mind a fürtözött MSDTC támogatott, de az MSDTC dinamikus portot használ, amelyhez standard Azure Load Balancer szükséges a HA port konfigurálásához. Ennek megfelelően a virtuális gépnek szabványos IP-foglalást kell használnia, vagy az nem érhető el az interneten. 

Definiáljon két szabályt, egyet az RPC Endpoint Mapper 135-es porthoz, egy pedig a valódi MSDTC-porthoz. A feladatátvételt követően módosítsa az LB-szabályt az új MSDTC-portra, miután az új csomóponton módosult. 

Ha az MSDTC helyi, ügyeljen arra, hogy engedélyezze a kimenő kommunikációt. 

## <a name="distributed-query"></a>Elosztott lekérdezés 

Az elosztott lekérdezés egy csatolt kiszolgálóra támaszkodik, amely az AG DNN figyelővel és porttal konfigurálható. Ha a port nem 1433, válassza az **egyéb adatforrás használata** lehetőséget a SQL Server Management Studioban (SSMS) a csatolt kiszolgáló konfigurálásakor. 

## <a name="filestream"></a>FileStream

A FileStream támogatott, de nem olyan helyzetekben, amikor a felhasználók a Windows-fájl API használatával érik el a hatókörön belüli fájlmegosztást. 

## <a name="filetable"></a>Fájltábla

A leszűkíthető támogatott, de nem olyan helyzetekben, amikor a felhasználók a Windows-fájl API használatával érik el a hatókörön belüli fájlmegosztást. 

## <a name="linked-servers"></a>Társított kiszolgálók

Konfigurálja a csatolt kiszolgálót az AG DNN figyelő neve és port használatával. Ha a port nem 1433, válassza az **egyéb adatforrás használata** lehetőséget a SQL Server Management Studioban (SSMS) a csatolt kiszolgáló konfigurálásakor. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések


- Melyik SQL Server-verziót hozza ki az AG DNN-figyelő? 

   SQL Server 2019 CU8 és újabb verziók.

- Mi a várható feladatátvételi idő a DNN-figyelő használatakor?

   A DNN-figyelő esetében a feladatátvételi idő csak az AG feladatátvételi idejére lesz, további idő nélkül (például Azure Load Balancer).

- Van-e verzióra vonatkozó követelmény az SQL-ügyfelek számára az OLEDB és az ODBC DNN támogatásához?

   Javasoljuk `MultiSubnetFailover=True` , hogy a DNN-figyelőhöz a kapcsolatok sztringjét támogassa. A szolgáltatás az SQL Server 2012 (11. x) kezdetével érhető el.

- Szükségesek-e SQL Server konfiguráció módosítása a DNN-figyelő használatához? 

   A SQL Server nem igényel semmilyen konfigurációs módosítást a DNN használatához, de néhány SQL Server funkcióhoz több megfontolásra lehet szükség. 

- Támogatja a DNN a több alhálózattal rendelkező fürtöket?

   Igen. A fürt az alhálózattól függetlenül a rendelkezésre állásban lévő összes replika fizikai IP-címeivel köti össze a DNN a DNS-ben. Az SQL-ügyfél a DNS-név összes IP-címét megpróbálja az alhálózattól függetlenül. 



## <a name="next-steps"></a>Következő lépések

További információkért lásd: 

- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always On rendelkezésre állási csoport](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

