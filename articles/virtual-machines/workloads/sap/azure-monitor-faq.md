---
title: Gyakori kérdések – Azure Monitor for SAP Solutions | Microsoft Docs
description: Ebben a cikkben az SAP-megoldásokra vonatkozó gyakori kérdésekre (GYIK) Azure Monitor kaphat választ.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377124"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor SAP-megoldásokhoz – gyakori kérdések (előzetes verzió)
## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk az SAP-megoldásokra vonatkozó gyakori kérdésekre (GYIK) Azure Monitor ad választ.  

 - **Fizetnem kell a Azure Monitor for SAP Solutions?**  
A licencelési díjak nem Azure Monitor for SAP Solutions.  
Azonban az ügyfelek felelnek a felügyelt erőforráscsoport-összetevők költségeiért.  

 - **Mely régiókban érhető el ez a szolgáltatás nyilvános előzetes verzióban?**  
A nyilvános előzetes verzióban ez a szolgáltatás az USA 2. keleti régiója, az USA 2. nyugati régiója, az USA keleti régiója és Nyugat-Európa területén lesz elérhető.  

 - **Meg kell adnom az engedélyeket, hogy lehetővé tegyem a felügyelt erőforráscsoport üzembe helyezését az előfizetésben?**  
Nem, explicit engedélyekre nincs szükség.  

 - **Hol található a gyűjtő virtuális gép?**  
Az erőforrás üzembe helyezése Azure Monitor for SAP Solutions javasoljuk, hogy az ügyfelek ugyanazt a VNET-et választják a monitorozási erőforráshoz, mint a SAP HANA kiszolgálójuk. Ezért javasoljuk, hogy a gyűjtő virtuális gép ugyanazon a VNET-en található, mint SAP HANA kiszolgáló. Ha az ügyfelek nem HANA-adatbázist használnak, a gyűjtő virtuális gép ugyanazon a VNET-en lesz, mint a nem HANA-adatbázis.  

 - **A HANA mely verziói támogatottak?**  
HANA 1.0 SPS 12 (120-as vagy újabb verzió) és HANA 2.0 SPS03 vagy újabb  

 - **Mely HANA-telepítési konfigurációk támogatottak?**  
A következő konfigurációk vannak támogatva:
   - Egycsomópontos (felskálás) és többcsomópontos (horizontális felskálás)  
   - Egyadatbázisú tároló (HANA 1.0 SPS 12) és több adatbázistároló (HANA 1.0 SPS 12 vagy HANA 2.0)  
   - Automatikus gazdagép-feladatátvétel (n+1) és HSR  

 - **Mely SQL Server támogatottak a verziók?**  
SQL Server 2012 SP4 vagy újabb verzió.  

 - **Mely SQL Server konfigurációk támogatottak?**  
A következő konfigurációk vannak támogatva:
   - Alapértelmezett vagy elnevezett önálló példányok egy virtuális gépen  
   - Az AlwaysOn konfigurációban lévő fürtözött példányok vagy példányok, ha a fürtözött erőforrás virtuális nevét vagy az AlwaysOn- listener nevét használják. Jelenleg nem gyűjt fürt- vagy AlwaysOn-specifikus metrikákat    
   - Azure SQL Database (PAAS) jelenleg nem támogatott  

 - **Mi történik, ha véletlenül törölem a felügyelt erőforráscsoportot?**  
A felügyelt erőforráscsoport alapértelmezés szerint zárolva van. Ezért az ügyfelek nem valószínű, hogy véletlenül törölnek egy felügyelt erőforráscsoportot.  
Ha egy ügyfél törli a felügyelt erőforráscsoportot, a Azure Monitor for SAP Solutions nem fog tovább dolgozni. Az ügyfélnek üzembe kell helyeznie egy új Azure Monitor for SAP Solutions erőforrást, és elölről kell kezdenie.  

 - **Milyen szerepkörökre van szükségem az Azure-előfizetésben a Azure Monitor for SAP Solutions üzembe helyezéséhez?**  
Közreműködői szerepkör.  

 - **Milyen SLA-t jelent ez a termék?**  
Az előzetes verziók ki vannak zárva a szolgáltatói szerződésekből. Olvassa el a teljes licencszerződést a Azure Monitor for SAP Solutions rendszerképén keresztül.  

 - **Nyomon követhetjük a teljes környezetemet ezzel a megoldással?**  
A nyilvános előzetes verzióban jelenleg a HANA-adatbázist, a mögöttes infrastruktúrát, a magas rendelkezésre állású fürtöt, a Microsoft SQL Servert, az SAP Netweaver rendelkezésre állását és az SAP Application Instance rendelkezésre állási metrikákat figyelheti.  

 - **Ez a szolgáltatás felváltja az SAP-megoldáskezelőt?**  
Nem. Az ügyfelek továbbra is használhatjak az SAP-megoldáskezelőt az üzleti folyamatok monitorozásához.  

 - **Mi ennek a szolgáltatásnak az értéke az olyan hagyományos megoldásokhoz SAP HANA Mint a SAP HANA/Studio?**  
Azure Monitor for SAP Solutions nem HANA-adatbázisspecifikus. Azure Monitor for SAP Solutions az AnyDB-t is támogatja.  

- **Mely SAP NetWeaver-verziók támogatottak?**  
SAP NetWeaver 7.0 vagy újabb.  

- **Mely SAP NetWeaver-konfigurációk támogatottak?**  
Támogatja az ABAP, a Java és a kettős veremű SAP NetWeaver alkalmazáskiszolgáló konfigurációit.

- **Miért kell feloldanom a metódusok vélmét az SAP NetWeaver alkalmazásfigyeléshez?**  
Az SAP->= 7.3-ban a legtöbb webszolgáltatási metódus alapértelmezés szerint védett. A rendelkezésre állási és teljesítménymetrikák ezen metódusok hívásával való lekéréséhez meg kell oldanunk a következő metódusok vélését: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic és GetSystemInstancelist.

- **Fennáll bármilyen kockázat az SAPCONTROL-webmetódák vélmének feloldása során?**  
Az SAPCONTROL webmethods védelme általában nem jelent [](https://launchpad.support.sap.com/#/notes/1439348)biztonsági kockázatot, azonban ha az ügyfelek az sapstartsrv kiszolgálói portjain (5XX13 / 5XX14) keresztül szeretnék korlátozni/megtiltani a hozzáférést a nem védett webmetódokhoz, ezt szűrő hozzáadásával érheti el az SAP Access Control List (ACL) szolgáltatásban, az [OSS](https://service.sap.com/sap/support/notes/1495075) megjegyzés leírja az ehhez szükséges konfigurációt. 

- **Újra kell indítani az SAP-példányokat az SAP NetWeaver szolgáltató beállításához szükséges rendszerkonfigurációk végrehajtása után?**  
Igen, ha az SAP konfigurációs módosításaival nem védett metódusokkal is védett, újra kell indítania a megfelelő SAP-rendszereket a konfigurációs módosítások frissítéséhez.  

## <a name="next-steps"></a>Következő lépések

- Hozza létre az első Azure Monitor SAP-megoldások erőforráshoz.
