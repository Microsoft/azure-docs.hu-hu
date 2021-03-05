---
title: Karbantartási időszak
description: Megtudhatja, hogyan konfigurálható a Azure SQL Database és a felügyelt példányok karbantartási ablaka.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/04/2021
ms.openlocfilehash: cf3404f364a7beee67cfa7dc523b9fd4b7b9985a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201311"
---
# <a name="maintenance-window-preview"></a>Karbantartási időszak (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A karbantartási időszak funkció lehetővé teszi a karbantartási ütemterv konfigurálását [Azure SQL Database](sql-database-paas-overview.md) és az [Azure SQL felügyelt példányainak](../managed-instance/sql-managed-instance-paas-overview.md) erőforrásaihoz, így kiszámítható és kevésbé zavaró karbantartási eseményeket biztosít a számítási feladatok számára. 

> [!Note]
> A karbantartási időszak funkció nem biztosítja a nem tervezett események (például a hardverhiba) elleni védelem lehetőségét, ami rövid kapcsolatok megszakítását okozhatja.

## <a name="overview"></a>Áttekintés

Az Azure rendszeres időközönként végrehajtja SQL Database és az SQL felügyelt példányok erőforrásának [tervezett karbantartását](planned-maintenance.md) . Az Azure SQL karbantartási eseménye során az adatbázisok teljes mértékben elérhetők, de a megfelelő rendelkezésre állási SLA-n belüli rövid feladatátvételek is vonatkozhatnak [SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) és az [SQL felügyelt példányára](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance), mivel bizonyos esetekben erőforrás-újrakonfigurálásra van szükség.

A karbantartási időszak olyan éles számítási feladatokhoz készült, amelyek nem rugalmasak az adatbázis vagy a példány feladatátvételéhez, és nem képesek felvenni a tervezett karbantartási események által okozott rövid kapcsolódási megszakításokat. Az előnyben részesített karbantartási időszak kiválasztásával csökkentheti a tervezett karbantartás hatását, mivel az a maximális munkaidőn kívül fog megvalósulni. A rugalmas munkaterhelések és a nem éles munkaterhelések az Azure SQL alapértelmezett karbantartási házirendjén alapulnak.

A karbantartási időszak a létrehozáskor vagy a meglévő Azure SQL-erőforrásoknál konfigurálható. Konfigurálható a Azure Portal, a PowerShell, a CLI vagy az Azure API használatával.

> [!Important]
> A karbantartási időszak konfigurálása hosszú ideig futó aszinkron művelet, hasonlóan az Azure SQL-erőforrás szolgáltatási rétegének módosításához. Az erőforrás a művelet során elérhető, kivéve a művelet végén előforduló rövid feladatátvételt, és általában akár 8 másodpercig is tart, akár a hosszan futó tranzakciók megszakítása esetén is. A feladatátvétel hatásának csökkentése érdekében a műveletet a csúcsidőben kívül kell végrehajtania.

### <a name="gain-more-predictability-with-maintenance-window"></a>A karbantartási időszak további kiszámíthatósága

Alapértelmezés szerint az Azure SQL karbantartási szabályzata minden nap 08:00 és 17:00 közötti időszakra blokkolja az érintett frissítéseket, hogy elkerülje a szokásos munkaidőn belüli fennakadások elkerülését. A helyi időt az erőforrást üzemeltető [Azure-régió](https://azure.microsoft.com/global-infrastructure/geographies/) határozza meg. Ez azt jelenti, hogy az _alapértelmezett karbantartási_ időszak a következő nap 5 és 08:00 közötti karbantartását teszi lehetővé naponta. A karbantartási frissítéseket az Azure SQL-erőforrások számára megfelelő időre módosíthatja, ha két További karbantartási időszakból álló tárolóhelyet választ:
 
* Hét napja, 22:00 – 06:00 helyi idő szerint hétfő – csütörtök
* Hétvégi időszak, 10 – 6 helyi idő szerint péntek – vasárnap

Miután kiválasztotta a karbantartási időszakot, és a szolgáltatás konfigurációja befejeződött, a tervezett karbantartás csak a választott ablakban fog történni.   

> [!Important]
> Nagyon ritka esetekben, amikor a művelet elhalasztása súlyos hatást okozhat, például a kritikus biztonsági javítások alkalmazása, a konfigurált karbantartási időszak átmenetileg felülbírálható. 

### <a name="cost-and-eligibility"></a>Cost és támogathatóság

A karbantartási időszak konfigurálása és használata ingyenesen használható az összes jogosult [ajánlati típushoz](https://azure.microsoft.com/support/legal/offer-details/): utólagos elszámolású, felhőalapú megoldás-szolgáltató (CSP), Microsoft nagyvállalati szerződés vagy Microsoft ügyfél-szerződés.

> [!Note]
> Az Azure-ajánlat az Ön Azure-előfizetésének típusa. Például az utólagos elszámolású előfizetések [, a](https://azure.microsoft.com/offers/ms-azr-0003p/) [Azure in Open Licencprogram](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)és a [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) minden Azure-ajánlat. Minden ajánlat vagy csomag eltérő feltételekkel és előnyökkel rendelkezik. Ajánlatát vagy tervét az előfizetés áttekintése mutatja be. Az előfizetés egy másik ajánlatra váltásával kapcsolatos további információkért lásd: [Azure-előfizetés módosítása egy másik ajánlatra](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Előzetes értesítések

A karbantartási értesítések beállítható úgy, hogy riasztást kapjanak a Azure SQL Database 24 órával későbbi tervezett karbantartási eseményekről, a karbantartás időpontjában, valamint a karbantartás befejezésekor. További információ: [Advance Notifications (előzetes értesítések](advance-notifications.md)).

## <a name="availability"></a>Rendelkezésre állás

### <a name="supported-service-level-objectives"></a>Támogatott szolgáltatási szint célkitűzései

Az alapértelmezetttől eltérő karbantartási időszak az összes slo elérhető, kivéve a **következőt**:
* Rugalmas skálázás 
* Példánykészletek
* Örökölt Gen4 virtuális mag
* Alapszintű, S0 és S1 
* DC, Fsv2, M-sorozat

### <a name="azure-region-support"></a>Azure-régió támogatása

Az alapértelmezetttől eltérő karbantartási időszak jelenleg a következő régiókban érhető el:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- Kelet-Japán
- NorthCentral minket
- Észak-Európa
- SouthCentral minket
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- USA nyugati régiója
- USA 2. nyugati régiója

## <a name="gateway-maintenance-for-azure-sql-database"></a>Az átjáró karbantartása Azure SQL Database

A karbantartási időszakok maximális előnyének lekéréséhez győződjön meg arról, hogy az ügyfélalkalmazások használják az átirányítás kapcsolódási házirendjét. Az átirányítás a javasolt kapcsolati házirend, amelyben az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz csatlakoznak, ami csökkenti a késést és a jobb teljesítményt.  

* Azure SQL Database a proxy kapcsolati házirendjét használó kapcsolatokat a kiválasztott karbantartási időszak és az átjáró-csomópont karbantartási időszaka is befolyásolhatja. Az átjáró-csomópontok karbantartási feladatátvétele azonban nem érinti a javasolt átirányítási kapcsolati házirendet használó ügyfélkapcsolatokat. 

* Az Azure SQL felügyelt példányaiban az átjáró-csomópontok a [virtuális fürtön](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) futnak, és a felügyelt példányokkal megegyező karbantartási időszaktal rendelkeznek, de az átirányítás kapcsolati házirendjének használata továbbra is ajánlott a karbantartási esemény során felmerülő fennakadások számának csökkentése érdekében.

További információ a Azure SQL Database az ügyfélkapcsolati házirendről: [Azure SQL Database-kapcsolatok házirendje](../database/connectivity-architecture.md#connection-policy). 

Az Azure SQL felügyelt példányának ügyfélkapcsolati házirendjével kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányok kapcsolatainak típusai](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considering-specifics-of-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányának sajátosságait figyelembe véve

Az Azure SQL felügyelt példánya olyan szolgáltatás-összetevőkből áll, amelyek az ügyfél virtuális hálózati alhálózatán belül futó elkülönített virtuális gépek dedikált készletén futnak. Ezek a virtuális gépek olyan [virtuális fürtöket](https://docs.microsoft.com/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) alkotnak, amelyek több felügyelt példányt tárolhatnak. Az egyik alhálózat példányain konfigurált karbantartási időszak befolyásolhatja az alhálózaton belüli virtuális fürtök számát, és a példányok elosztását a virtuális fürtök között. Ehhez néhány effektusra lehet szükség.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>A karbantartási időszak konfigurálása hosszú ideig futó művelet 
A virtuális fürtben üzemeltetett összes példány a karbantartási időszakot használja. Alapértelmezés szerint a rendszer az összes felügyelt példányt a virtuális fürtön tárolja az alapértelmezett karbantartási időszakkal. Egy másik karbantartási időszak megadása a felügyelt példányhoz a létrehozásakor vagy azt követően azt jelenti, hogy azt a megfelelő karbantartási időszaktal rendelkező virtuális fürtbe kell helyezni. Ha az alhálózat nem rendelkezik ilyen virtuális fürttel, először létre kell hoznia egy újat a példány befogadásához. Ha további példányokat kíván elhelyezni a meglévő virtuális fürtben, szükség lehet a fürt átméretezésére. Mindkét művelet hozzájárul a felügyelt példány karbantartási időszakának konfigurálásához.
A karbantartási időszak felügyelt példányon történő konfigurálásának várható időtartama a [példány-felügyeleti műveletek becsült időtartama](https://docs.microsoft.com/azure/azure-sql/managed-instance/management-operations-overview#duration)alapján számítható ki.

> [!Important]
> Egy rövid feladatátvétel a művelet végén történik, és általában akár 8 másodpercig is tart, akár megszakított, hosszan futó tranzakciók esetén is. A feladatátvétel hatásának csökkentése érdekében a műveletet a csúcsidőben kívül kell végrehajtania.

### <a name="ip-address-space-requirements"></a>Az IP-címtartomány követelményei
Az alhálózatban minden új virtuális fürthöz további IP-címek szükségesek a [virtuális fürt IP-címének kiosztása](https://docs.microsoft.com/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size)alapján. A meglévő felügyelt példány karbantartási [időszakának módosításakor ideiglenes további IP-kapacitásra](https://docs.microsoft.com/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) is szükség van, mint a megfelelő szolgáltatási réteg virtuális mag-forgatókönyvének skálázásához.

### <a name="ip-address-change"></a>IP-cím módosítása
A karbantartási időszak beállítása és módosítása miatt a példány IP-címének módosítása az alhálózat IP-címének tartományán belül történik.

> [!Important]
>  Győződjön meg arról, hogy a NSG és a tűzfalszabályok nem blokkolja az adatforgalmat az IP-címek módosítása után. 

## <a name="next-steps"></a>Következő lépések

* [Előzetes értesítések](advance-notifications.md)
* [Karbantartási időszak konfigurálása](maintenance-window-configure.md)

## <a name="learn-more"></a>Tudjon meg többet

* [Karbantartási időszak – gyakori kérdések](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Az Azure karbantartási eseményeinek megtervezése Azure SQL Database és az Azure SQL felügyelt példányain](planned-maintenance.md)




