---
title: BareMetal kiépítése Oracle-hez
description: Ismerje meg az BareMetal-infrastruktúra Oracle-hez való üzembehelyezését.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559094"
---
# <a name="provision-baremetal-for-oracle"></a>BareMetal kiépítése Oracle-hez

Ebben a cikkben azt fogjuk látni, hogyan lehet kiépítni az BareMetal Infrastructure-példányokat Oracle számítási feladatokhoz. 

Az BareMetal-példányok üzembehelyének első lépése a Microsoft CSA-val való munka. Ezek segítenek az adott számítási feladatok igényeinek és az üzembe helyezett architektúrának megfelelően, legyen szó egypéldányosról, egy csomópontos RAC-ről vagy RAC-ről. További információ ezekről a topológiákról: [Architecture of BareMetal Infrastructure for Oracle](oracle-baremetal-architecture.md).

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Aktív Azure-előfizetés
> * Microsoft Premier támogatási szerződés
> * A 7.6-os Red Hat Enterprise Linux licencei
> * Oracle támogatási szerződés 
> * Oracle-licencek és szoftvertelepítési összetevők
> * Helyszíni ExpressRoute csatlakoztatása az Azure-hoz **(** Opcionálisan konfigurálhatja az ExpressRoute-Global Reach a helyszínről a helyszíni hálózatról a Oracle Database)    
> * Virtuális hálózat
> * Átjáró létrehozása
> * Virtuális gépek (virtuális gépek) a virtuális hálózaton (jumpboxes)

## <a name="information-to-provide-microsoft-operations"></a>A Microsoft Operations szolgáltatáshoz szükséges információk

A CSA-nak a következő információkat kell meg adnia:

1. Virtuális hálózat címterülete. Ennek a tartománynak /24 alhálózatnak kell lennie; például: 10.11.0.0/24.
2. P2P tartomány. Ennek a tartománynak /29 alhálózatnak kell lennie; például: 10.12.0.0/29.
3. Kiszolgálói IP-címkészlet. Az ajánlott tartomány : /24; például: 10.13.0.0/24.
4. Kiszolgáló IP-címe. Válasszon egy IP-címet a Kiszolgáló IP-címkészletből.

    > [!Note] 
    > Az első harminc IP-cím a Microsoft infrastruktúra-konfigurációja számára van fenntartva. Ebben a példában tehát a panel első elérhető IP-címe a 10.13.0.30 lesz.

5. Az Azure-régió szükséges; például az USA 2. nyugati régiója.
6. Az BareMetal-infrastruktúra szükséges termékváltozata; például S192 (két gép).

## <a name="storage-requirements"></a>Tárolási követelmények

A kiépítési kérelem során a CSA-képviselővel kell együtt dolgoznia a tárolási igények szempontjából, beleértve a várható tárolási igényeket is a jövőbeli növekedés alapján. A hozzáadott tárterület 1 TB-os növekményekben van hozzáadva.

A kötetek esetében az Oracle Optimális rugalmas architektúra [(OFA) szabványát követjük,](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)alapszintű csomaggal és vállalati konfigurációval. Ha a szabványos "T-shirt méretezéstől" kívül más egyéni tárolási követelményekkel is kell bajlkodni, az egyéni kérést a CSA-ján keresztül kell végrehajtania.

| Alapszintű konfiguráció (POC/ Tesztelés) | Leírás | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- | --- |
| /u01 | Oracle bináris fájlok | 500 GB | 500 GB | 500 GB |
| /u02 | Olvasásigényes/Rendszergazda | 500 GB | 1 TB | 5 TB |
| /u03 | Írásigényes/naplók | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Vállalati konfiguráció | Leírás | Kicsi | Közepes | Nagy | Extra nagy |
| --- | --- | --- | --- | --- | --- |
| /u01 | Oracle bináris fájlok | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Rendszergazda | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 – /u59 | Olvasásigényes | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 – /u89 | Írásigényes | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 – /u91 | Naplók újrabehozása | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Archívum | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Következő lépés

További információ az Oracle-hez szükséges BareMetal-infrastruktúráról.

> [!div class="nextstepaction"]
> [Mi az Az Azure BareMetal-infrastruktúrája?](../../concepts-baremetal-infrastructure-overview.md)
