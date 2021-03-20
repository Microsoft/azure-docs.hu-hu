---
title: A SQL Server 2008 & 2008 R2 támogatásának kiterjesztése
description: A SQL Server-példány Azure-ba való áttelepítésével, illetve a helyszíni példányok megtartására szolgáló kiterjesztett támogatás megvásárlásával bővítheti SQL Server 2008 és SQL Server 2008 R2 támogatását.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359166"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>A SQL Server 2008 és SQL Server 2008 R2 támogatásának kiterjesztése az Azure-ra
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A SQL Server 2008 és SQL Server 2008 R2 egyaránt elérte a [támogatási (EOS) életciklusának végét](https://www.microsoft.com/sql-server/sql-server-2008). Mivel számos ügyfél továbbra is mindkét verziót használja, több lehetőséget biztosítunk a támogatás folytatására. Áttelepítheti a helyszíni SQL Server példányait az Azure Virtual Machines szolgáltatásba (VM), áttelepítheti Azure SQL Database, vagy megtarthatja a helyszíni és a bővített biztonsági frissítéseket.

A felügyelt példányokkal ellentétben az Azure-beli virtuális gépekre való Migrálás nem igényli az alkalmazások újbóli hitelesítését. A helyszíni tartózkodástól eltérően az Azure-beli virtuális gépekre való Migrálás után ingyenes kibővített biztonsági javítások is megjelenhetnek.

A cikk további részében a SQL Server példány Azure-beli virtuális gépre való áttelepítésével kapcsolatos szempontokat ismertetjük.

További információ a támogatási lehetőségek megszűnéséről: [a támogatás vége](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Kiépítés

Az Azure Marketplace-en elérhető az utólagos elszámolású **SQL Server 2008 R2 a Windows Server 2008 R2** -es rendszerképben.

SQL Server 2008 rendszerű ügyfeleknek telepíteniük vagy frissíteniük kell SQL Server 2008 R2-re. Hasonlóképpen, a Windows Server 2008 rendszerű ügyfeleknek telepíteniük kell a virtuális gépet egy egyéni VHD-ről, vagy frissíteniük kell a Windows Server 2008 R2-re.

Az Azure Marketplace-en keresztül üzembe helyezett lemezképek előre telepítve vannak az SQL IaaS bővítménnyel. Az SQL IaaS bővítmény a rugalmas licencelés és az automatikus javítás követelménye. Az önállóan telepített virtuális gépeket telepítő ügyfeleknek manuálisan kell telepíteniük az SQL IaaS bővítményt. Az SQL IaaS bővítmény nem támogatott a Windows Server 2008 rendszeren.

> [!NOTE]
> Bár a SQL Server- **létrehozási** és- **kezelési** lapok a Azure Portal SQL Server 2008 R2 rendszerképpel működnek együtt, a következő funkciók _nem támogatottak_: automatikus biztonsági mentések, Azure Key Vault integráció, R-szolgáltatások és tárolási konfiguráció.

## <a name="licensing"></a>Licencek
Az utólagos elszámolású SQL Server 2008 R2-es üzemelő példányok [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-benefit/)konvertálhatók.

Ha frissítési garanciát (SA) alapuló licencet szeretne átváltani, az ügyfeleknek regisztrálniuk kell az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md). A regisztráció után az SQL-licenc típusa Azure Hybrid Benefit és utólagos elszámolású.

Az Azure-beli virtuális gépeken telepített SQL Server 2008-es vagy SQL Server 2008 R2-példányok regisztrálhatnak az SQL IaaS-ügynök bővítménnyel, és a licencük típusát az utólagos elszámolású verzióra válthatják.

## <a name="migration"></a>Áttelepítés
A manuális biztonsági mentési/visszaállítási módszerekkel áttelepítheti az EOS SQL Server példányait egy Azure-beli virtuális gépre. Ez a legelterjedtebb áttelepítési módszer a helyszínről egy Azure-beli virtuális gépre.

### <a name="azure-site-recovery"></a>Azure Site Recovery

A tömeges áttelepítések esetében javasoljuk a [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) szolgáltatást. Azure Site Recovery az ügyfelek a teljes virtuális gépet replikálják, beleértve a helyszíni és az Azure-beli virtuális gépek közötti SQL Server is.

SQL Server a helyreállítás biztosításához az alkalmazás-konzisztens Azure Site Recovery Pillanatképek szükségesek. Azure Site Recovery támogatja az alkalmazás-konzisztens pillanatképeket legalább 1 órás intervallummal. A Azure Site Recovery áttelepítéssel rendelkező SQL Server számára lehetséges minimális helyreállítási időkorlát (RPO) 1 óra. A helyreállítási időre vonatkozó célkitűzés (RTO) 2 óra plusz SQL Server helyreállítási idő.

### <a name="database-migration-service"></a>Database Migration Service

A [Azure Database Migration Service](../../../dms/dms-overview.md) az ügyfelek számára, ha a helyszínről egy Azure-beli virtuális gépre migrálnak, ha a SQL Servert a 2012 vagy újabb verzióra frissíti.

## <a name="disaster-recovery"></a>Vészhelyreállítás

Az Azure-beli virtuális gépeken az EOS SQL Server vész-helyreállítási megoldásai a következők:

- **SQL Server biztonsági mentések**: a Azure Backup segítségével megvédheti az EOS SQL Server 2008-es és 2008 R2-es verzióit a ransomware, a véletlen törlés és a sérülés ellenében 15 perces RPO és időpontra történő helyreállítással. További részletekért tekintse meg [ezt a cikket](../../../backup/sql-support-matrix.md#scenario-support).
- **Napló szállítása**: létrehozhat egy naplóbeli szállítási replikát egy másik zónában vagy az Azure-régióban folyamatos visszaállításokkal a RTO csökkentése érdekében. Manuálisan kell konfigurálnia a napló szállítását.
- **Azure site Recovery**: a virtuális gép a zónák és régiók között replikálható Azure site Recovery replikálással. SQL Server alkalmazás-konzisztens Pillanatképek szükségesek a helyreállítás biztosításához vészhelyzet esetén. Azure Site Recovery legalább 1 órás RPO, valamint egy 2 órás (plusz SQL Server helyreállítási idő) RTO biztosít az EOS SQL Server vész-helyreállításhoz.

## <a name="security-patching"></a>Biztonsági javítások

SQL Server virtuális gépek bővített biztonsági frissítései a Microsoft Update csatornákon keresztül érkeznek, miután a SQL Server VM regisztrálva lettek az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md). A javítások manuálisan vagy automatikusan is letölthetők.

Az *Automatikus javítás* alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. Megadhatja a hét egy napját, az időpontot és az időtartamot a karbantartási időszakra vonatkozóan, ha a SQL Server IaaS bővítmény telepítve van. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezése a virtuális gép területi beállítása szerinti időt használja. További információ: [SQL Server automatikus javítása az Azure Virtual Machines-on](automated-patching.md).


## <a name="next-steps"></a>Következő lépések

SQL Server VM migrálása az Azure-ba:

* [SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre](migrate-to-vm-from-sql-server.md)

Ismerkedjen meg az Azure Virtual Machines SQL Serverával:

* [SQL Servert futtató virtuális gép létrehozása az Azure Portalon](sql-vm-create-portal-quickstart.md)

Választ kaphat a SQL Server virtuális gépekkel kapcsolatos gyakori kérdésekre:

* [Az Azure-SQL Server kapcsolatos gyakori kérdések Virtual Machines](frequently-asked-questions-faq.md)

További információ a támogatási lehetőségek megszűnéséről és a bővített biztonsági frissítésekről:

* [Támogatás vége](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Bővített biztonsági frissítések](/sql/sql-server/end-of-support/sql-server-extended-security-updates)