---
title: A szolgáltatás együttműködése SQL Server a & DNN
description: 'Ismerje meg a további szempontokat, amikor az Azure-beli virtuális gépeken futó feladatátvételi SQL Server fürtszolgáltatással rendelkező SQL Server-funkciókkal és elosztott hálózati DNN-erőforrásokkal dolgozik. '
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
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358214"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>A szolgáltatás együttműködése SQL Server a & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Vannak olyan SQL Server szolgáltatások, amelyek egy rögzített virtuális hálózat nevét (VNN) használják. Ennek megfelelően, ha az elosztott hálózati név (DNN) erőforrást használja a feladatátvevő fürt példányával, és SQL Server Azure-beli virtuális gépeken, további szempontokat is figyelembe kell venni. 

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a hálózati aliast a DNN-erőforrás használatakor, valamint azt, hogy mely SQL Server szolgáltatások igényelnek további szempontokat.

## <a name="create-network-alias-fci"></a>Hálózati alias létrehozása (-es)

Néhány kiszolgálóoldali összetevő egy rögzített VNN-értékre támaszkodik, és olyan hálózati aliast igényel, amely a VNN a DNN DNS-nevéhez rendeli a megfelelő működéshez. A [kiszolgáló aliasnevének létrehozása](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) című témakörben leírt lépéseket követve hozzon létre egy aliast, amely leképezi a VNN a DNN DNS-névre. 

Alapértelmezett példány esetén a VNN közvetlenül a DNN DNS-névre képezhető le, például VNN = DNN DNS-név.
Ha például a VNN neve, a `FCI1` példány neve `MSSQLSERVER` , a DNN pedig `FCI1DNN` (az ügyfelek, amelyekhez korábban kapcsolódtak `FCI` , és most csatlakoznak `FCI1DNN` ), akkor a VNN a DNN képezhetők le `FCI1` `FCI1DNN` . 

Megnevezett példány esetén a hálózati aliasok hozzárendelését a teljes példányhoz kell elvégezni, például: `VNN\Instance`  =  `DNN\Instance` . Ha például a VNN neve, a `FCI1` példány neve `instA` , a DNN pedig `FCI1DNN` (az ügyfelek, amelyekhez korábban kapcsolódtak `FCI1\instA` , és most csatlakoznak `FCI1DNN\instaA` ), akkor a VNN a DNN képezhetők le `FCI1\instaA` `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Ügyfélillesztők

ODBC-, OLEDB-, ADO.NET-, JDBC-, PHP-és Node.js-illesztőprogramok esetén a felhasználóknak explicit módon meg kell adniuk a DNN DNS-nevét a kapcsolódási karakterláncban. A feladatátvétel gyors elérésének biztosításához adja hozzá `MultiSubnetFailover=True` a kapcsolati sztringet, ha az SQL-ügyfél támogatja azt. 

## <a name="tools"></a>Eszközök

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)és [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) felhasználóinak explicit módon meg kell adniuk a DNN DNS-nevét a kapcsolódási karakterláncban a kiszolgáló neveként. 

## <a name="availability-groups-and-fci"></a>Rendelkezésre állási csoportok és a verzió

Az Always On rendelkezésre állási csoportot egy feladatátvevő fürt példányával konfigurálhatja a replikák egyikének használatával. Ebben a konfigurációban a (DNN)-replikához tartozó tükrözési végpont URL-címének a-t kell használnia. Hasonlóképpen, ha a rendszer csak olvasható replikát használ, a csak olvasható útválasztásnak az DNN-t kell használnia. 

A tükrözési végpont formátuma a következő: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Ha például a DNN DNS-neve, és az a-es `dnnlsnr` `5022` tükrözési végpontjának portja, akkor a végpont URL-címének létrehozásához a Transact-SQL (T-SQL) kódrészlet a következőre hasonlít: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Hasonlóképpen, a csak olvasható útválasztási URL-cím formátuma a következő: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Ha például a DNN DNS-neve, a `dnnlsnr` pedig a `1444` csak olvasási célként megadott SQL Server-es számú port, a T-SQL-kódrészlet a csak olvasható útválasztási URL-cím létrehozásához a következőképpen néz ki: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Ha az alapértelmezett 1433-as port, akkor kihagyhatja a portot az URL-címben. Megnevezett példány esetében állítson be egy statikus portot a megnevezett példányhoz, és adja meg azt a csak olvasható útválasztási URL-címben.  

## <a name="replication"></a>Replikáció

A replikáció három összetevőből áll: közzétevő, terjesztő, előfizető. Ezen összetevők bármelyike lehet feladatátvevő fürt példánya. Mivel a VNN-t a replikálási konfigurációban nagy mértékben használják, explicit módon és implicit módon, a replikálás működéséhez szükség lehet egy olyan hálózati aliasra, amely leképezi a VNN a DNN. 

A replikáció *konfigurálása előtt* ne használja a VNN-nevet a replikációban, hanem hozzon létre egy hálózati aliast a következő távoli helyzetekben:

| **Replikációs összetevő (DNN-sel)** | **Távoli összetevő** | **Hálózati aliasok térképe** | **Kiszolgáló hálózati térképpel**| 
|---------|---------|---------|-------- | 
|Publisher | Terjesztő | Közzétevő VNN a közzétevő DNN| Terjesztő| 
|Terjesztő|Előfizető |Terjesztői VNN a Disztribútor DNN| Előfizető | 
|Terjesztő|Publisher | Terjesztői VNN a Disztribútor DNN | Publisher| 
|Előfizető| Terjesztő| Előfizető VNN az előfizető DNN | Terjesztő| 

Tegyük fel például, hogy rendelkezik egy olyan közzétevővel, amely a DNN használatával egy replikációs topológiában van konfigurálva, és a terjesztő távoli. Ebben az esetben hozzon létre egy hálózati aliast a terjesztő kiszolgálón a közzétevő VNN a közzétevő DNN való leképezéséhez: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Konfigurálja a DNN DNS-nevet hálózati aliasként a SQL Server Konfigurációkezelő használatával." :::

Egy megnevezett példány teljes példányának nevét használhatja, például az alábbi ábrát: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="A megnevezett példányok hálózati aliasának konfigurálásakor használja a teljes példány nevét." :::

## <a name="database-mirroring"></a>Adatbázis-tükrözés

Az adatbázis-tükrözést az adatbázis-tükrözési partnerként is konfigurálhatja. A SQL Server Management Studio grafikus felhasználói felület helyett a [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) használatával konfigurálhatja. A T-SQL használatával biztosítható, hogy az adatbázis-tükrözési végpont a VNN helyett a DNN használatával legyen létrehozva. 

Ha például a DNN DNS-neve `dnnlsnr` , és az adatbázis-tükrözési végpont 7022, a következő T-SQL kódrészlet konfigurálja az adatbázis-tükrözési partnert: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Az ügyfél-hozzáférés esetében a **feladatátvételi partner** tulajdonsága képes kezelni az adatbázis-tükrözési feladatátvételt, de nem lehet feladatátvételt használni. 

## <a name="msdtc"></a>MSDTC

A (verzió) a Microsoft Distributed Transaction Coordinator (MSDTC) által koordinált elosztott tranzakciókban is részt vehet. Bár a fürtözött MSDTC és a helyi MSDTC is támogatott az Azure-beli DNN-mel, a fürtözött MSDTC esetében továbbra is a terheléselosztó szükséges. A DNN definiált, nem helyettesíti az Azure-beli fürtözött MSDTC Azure Load Balancer követelményét. 

## <a name="filestream"></a>FileStream

Bár a FileStream támogatott a FileStream-ben lévő adatbázisok esetében, és a DNN nem támogatja a fájlrendszer API-k használatával történő elérését. 

## <a name="linked-servers"></a>Társított kiszolgálók

A csatolt kiszolgáló (DNN) használata támogatott. Használja közvetlenül a DNN a csatolt kiszolgáló konfigurálásához, vagy használjon hálózati aliast a VNN hozzárendeléséhez a DNN. 


Ha például egy DNN DNS-névvel rendelkező csatolt kiszolgálót szeretne létrehozni az `dnnlsnr` elnevezett példányhoz `insta1` , használja a következő Transact-SQL (T-SQL) parancsot:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Azt is megteheti, hogy ehelyett a virtuális hálózat neve (VNN) használatával hozza létre a csatolt kiszolgálót, de ezután meg kell határoznia egy hálózati aliast, hogy leképezje a VNN a DNN. 

Például a `insta1` `vnnname` `dnnlsnr` következő Transact-SQL (T-SQL) parancs használatával hozzon létre egy csatolt kiszolgálót a VNN használatával: példánynév, VNN neve és DNN neve.

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Ezután hozzon létre egy hálózati aliast a leképezéshez `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Gyakori kérdések


- Melyik SQL Server-verzió támogatja a DNN-támogatást? 

   SQL Server 2019 CU2 és újabb verziók.

- Mi a várható feladatátvételi idő a DNN használata esetén?

   A DNN esetében a feladatátvételi idő csak a (z)%-os feladatátvételi idő lesz, anélkül, hogy hozzá kellene adni időt (például a Azure Load Balancer használatakor a mintavételi időt).

- Van-e verzióra vonatkozó követelmény az SQL-ügyfelek számára az OLEDB és az ODBC DNN támogatásához?

   Javasoljuk `MultiSubnetFailover=True` , hogy a DNN esetében a kapcsolatok karakterláncának támogatását. A szolgáltatás az SQL Server 2012 (11. x) kezdetével érhető el.

- Szükségesek-e SQL Server konfiguráció módosítása a DNN használatához? 

   A SQL Server nem igényel semmilyen konfigurációs módosítást a DNN használatához, de néhány SQL Server funkcióhoz több megfontolásra lehet szükség. 

- Támogatja a DNN a több alhálózattal rendelkező fürtöket?

   Igen. A fürt az alhálózattól függetlenül a fürt összes csomópontjának fizikai IP-címeivel köti össze a DNN a DNS-ben. Az SQL-ügyfél a DNS-név összes IP-címét megpróbálja az alhálózattól függetlenül. 



## <a name="next-steps"></a>Következő lépések

További információkért lásd: 

- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

