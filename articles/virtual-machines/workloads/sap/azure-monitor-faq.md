---
title: Gyakori kérdések – SAP-megoldások Azure Monitor | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan választ kaphat a Azure Monitor for SAP-megoldásokkal kapcsolatos gyakori kérdésekre (GYIK).
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 3732189c1d2e09b648a2fba0a39e7e4113a76d48
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675951"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>SAP-megoldások Azure Monitor – GYIK (előzetes verzió)
## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk az SAP-megoldások Azure Monitor kapcsolatos gyakori kérdésekre (GYIK) ad választ.  

 - **Kell-e fizetnem az SAP-megoldások Azure Monitorért?**  
Az SAP-megoldások Azure Monitor esetében nincs licencelési díj.  
Az ügyfelek azonban a felügyelt erőforráscsoport-összetevők költségeit terhelik.  

 - **Mely régiókban érhető el a szolgáltatás nyilvános előzetes verzióban?**  
A nyilvános előzetes verzióban ez a szolgáltatás az USA 2. keleti régiójában, az USA 2. nyugati régiójában, az USA keleti régiójában és Nyugat-Európában is elérhető.  

 - **Meg kell adni engedélyeket a felügyelt erőforráscsoport üzembe helyezésének engedélyezéséhez az előfizetésben?**  
Nem, explicit engedélyek nem szükségesek.  

 - **Hol található a gyűjtő virtuális gép?**  
A Azure Monitor SAP-megoldások erőforráshoz való üzembe helyezésének időpontjában azt javasoljuk, hogy az ügyfelek ugyanazt a VNET használják a SAP HANA-kiszolgálóként való figyeléshez. A gyűjtő virtuális gép ezért ajánlott a SAP HANA-kiszolgálóval megegyező VNET tárolni. Ha az ügyfelek nem HANA-adatbázist használnak, a gyűjtő virtuális gép ugyanabban a VNET fog tartózkodni, mint a nem HANA-adatbázis.  

 - **A HANA mely verziói támogatottak?**  
HANA 1,0 SPS 12 (Rev. 120 vagy újabb) és HANA 2,0 SPS03 vagy újabb  

 - **Milyen HANA-telepítési konfigurációk támogatottak?**  
A következő konfigurációk vannak támogatva:
   - Egyetlen csomópont (felskálázás) és több csomópontos (kibővített)  
   - Önálló adatbázis-tároló (HANA 1,0 SPS 12) és több adatbázis-tároló (HANA 1,0 SPS 12 vagy HANA 2,0)  
   - Automatikus gazdagép feladatátvétele (n + 1) és HSR  

 - **Mely SQL Server támogatott verziók használhatók?**  
SQL Server 2012 SP4 vagy újabb.  

 - **Mely SQL Server konfigurációk támogatottak?**  
A következő konfigurációk vannak támogatva:
   - Alapértelmezett vagy különálló példányok egy virtuális gépen  
   - Fürtözött példányok vagy példányok egy AlwaysOn-konfigurációban, ha a fürtözött erőforrás virtuális nevét vagy a AlwaysOn figyelő nevét használja. Jelenleg nincsenek összegyűjtve fürt-vagy AlwaysOn-specifikus mérőszámok    
   - A Azure SQL Database (Péter) jelenleg nem támogatott  

 - **Mi történik, ha véletlenül törölte a felügyelt erőforráscsoportot?**  
A felügyelt erőforráscsoport alapértelmezés szerint zárolva van. Ezért a felügyelt erőforráscsoport véletlen törlésének esélye az ügyfelek által apró.  
Ha az ügyfél törli a felügyelt erőforráscsoportot, az SAP-megoldások Azure Monitor nem fog működni. Az ügyfélnek új Azure Monitor kell telepítenie az SAP-megoldások erőforrásához, és újra kell kezdenie.  

 - **Milyen szerepkörökre van szükségem az Azure-előfizetésben a Azure Monitor SAP-megoldások erőforráshoz való telepítéséhez?**  
Közreműködő szerepkör.  

 - **Mi a termék SLA-ja?**  
Az előzetes verziók ki vannak zárva a szolgáltatói szerződésekből. Kérjük, olvassa el a Azure Monitor for SAP Solutions Marketplace-rendszerkép teljes licencét.  

 - **Nyomon követhető a teljes környezet a megoldáson keresztül?**  
A HANA-adatbázist, a mögöttes infrastruktúrát, a magas rendelkezésre állású fürtöt és a Microsoft SQL Servert jelenleg nyilvános előzetes verzióban figyelheti.  

 - **Lecseréli a szolgáltatás az SAP Solution Managert?**  
Nem. Az ügyfelek továbbra is használhatják az SAP Solution Managert az üzleti folyamatok figyelésére.  

 - **Mi a szolgáltatás értéke olyan hagyományos megoldásokon keresztül, mint például a SAP HANA cockpit/Studio?**  
Az SAP-megoldások Azure Monitor nem a HANA-adatbázisra jellemző. Az SAP-megoldások Azure Monitor támogatja a AnyDB is.  

## <a name="next-steps"></a>Következő lépések

- Hozza létre az első Azure Monitor az SAP Solutions-erőforráshoz.
