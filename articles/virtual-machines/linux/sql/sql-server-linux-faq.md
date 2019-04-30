---
title: SQL Server on Linux Azure Virtual Machines – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk a Linux Azure virtuális gépeken futó SQL Server kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739267"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Gyakori kérdések az SQL Server Linux Azure Virtual Machines gépeken

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Ez a cikk ismerteti a futó kapcsolatos leggyakoribb kérdésekre adott válaszokat [Linux rendszerű Azure virtuális gépeken futó SQL Server](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Ez a cikk a Linux rendszerű virtuális gépeken futó SQL Serverre konkrét problémák összpontosít. Ha az SQL Server Windows virtuális gépeken futnak, tekintse meg a [Windows – gyakori kérdések](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Képek

1. **Milyen az SQL Server virtuálisgép-katalógus rendszerképek érhetők el?**

   Az Azure SQL Server összes kiadásában az összes támogatott fő kiadásához tartozó virtuálisgép-rendszerképek Linux és Windows tart. További részletekért tekintse meg a teljes listáját [Linuxos Virtuálisgép-rendszerkép](sql-server-linux-virtual-machines-overview.md#create) és [Windows Virtuálisgép-rendszerképek](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Frissülnek a meglévő SQL Server virtuálisgép-katalógus rendszerképek?**

   Minden két hónapra, SQL Server-rendszerképeket a virtuálisgép-katalógus frissülnek a legújabb Linux és Windows-frissítések. Linux-lemezképekhez ilyenek például a rendszerek legújabb frissítéseit. Windows-rendszerképeket ilyenek például a Windows Update, beleértve a fontos az SQL Server biztonsági frissítések és szervizcsomagok fontosként megjelölt frissítéseket. SQL Server összegző frissítések a Linux és Windows másképp kezeli. A Linux SQL Server összegző frissítései is megtalálhatók a frissítést. Azonban jelenleg Windows virtuális gépek nem frissíti az SQL Server vagy Windows Server összegző frissítései.

1. **Mi a kapcsolódó SQL Server-csomagok is telepítve van?**

   Az SQL Server Linux rendszerű virtuális gépek alapértelmezés szerint az SQL Server-csomagokat, olvassa el [telepített csomagokat](sql-server-linux-virtual-machines-overview.md#packages).

1. **Az SQL Server virtuálisgép-rendszerképek lekérése távolíthatók el a katalógusban?**

   Igen. Az Azure csak egy kép / fő verziójú és kiadású tart fenn. Például amikor egy új SQL Server szervizcsomag megjelenik, az Azure ad hozzá egy új rendszerképet a katalógusban, hogy a service Pack. Az SQL Server-lemezképet az előző service Pack azonnal törlődik az Azure Portalról. Azonban továbbra is elérhető, a következő három hónapban a Powershellből kiépítéshez. Három hónap elteltével az előző képen a service pack már nem érhető el. Ez a házirend eltávolítása is alkalmazni, ha egy SQL Server-verzió nem támogatott válik, amikor az életciklusa végére ér.

## <a name="creation"></a>Létrehozás

1. **Hogyan hozhatok létre Linux rendszerű Azure virtuális gép az SQL Server?**

   A legegyszerűbb megoldás az, amely tartalmazza az SQL Server Linux rendszerű virtuális gép létrehozása. Az Azure-regisztráció és a egy SQL virtuális gép létrehozása a portálról, olvassa el [az Azure Portalon az SQL Server Linux rendszerű virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md). Emellett lehetősége manuálisan az SQL Server telepítése a virtuális gép mindkettővel szabadon licenccel rendelkező kiadásra (Developer és Express) vagy az a helyi licencek újrafelhasználásával. Ha a saját licenc használata, rendelkeznie kell [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility).

1. **Miért nem helyezhető üzembe egy RHEL vagy SLES SQL Server rendszerű virtuális gép az Azure-előfizetéssel, amely rendelkezik a költségkeret?**

   RHEL és SLES virtuális gépek nem vonatkozik költségkeret, és a egy ellenőrzött fizetési mód (általában hitelkártya) az előfizetéshez tartozó előfizetés szükséges. A költségkeret eltávolítása nélkül üzembe egy RHEL vagy SLES VM, ha az előfizetése le lekérése tiltva, és minden virtuális gép vagy szolgáltatás leállt. Ha ebbe az állapotba, hogy az előfizetés újbóli engedélyezéséhez futtatásakor [el kell távolítania a költségkeretet](https://account.windowsazure.com/subscriptions). A fennmaradó kreditjeit visszaáll a az aktuális elszámolási időszakban, de ha úgy dönt, hogy indítsa újra, és folytatja annak futtatását egy RHEL vagy SLES VM kép felár kerül a hitelkártyáját terhelni.

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Először hozzon létre egy Linux operációs rendszer csak a virtuális gép. Ezután futtassa a [SQL Server telepítési lépéseket](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) a Linux-disztribúció. Kivéve, ha telepíti az SQL Server szabadon licenccel rendelkező kiadásainak egyikét, az SQL Server licenccel is rendelkeznie kell és [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility/).

1. **Vannak-e Bring-Your-saját licenc használata (BYOL) Linux virtuálisgép-lemezképek az SQL Server?**

   Jelenleg nincsenek az SQL Server nincs BYOL Linux virtuálisgép-lemezképeket. Azonban manuálisan telepítheti az SQL Server csak Linux rendszerű virtuális gépen a fenti kérdések leírt módon.

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Nem. Másodpercenkénti használatalapú licencelés saját licenc használata nem válthat. Kell új Linux rendszerű virtuális gép létrehozása, az SQL Server telepítése és az adatok áttelepítését. Tekintse meg az előző kérdésnél saját licence kapcsolatos további részletekért.

## <a name="administration"></a>Adminisztráció

1. **Kezelheti a Linux rendszerű SQL Server virtuális gép az SQL Server Management Studio (SSMS)?**

   Igen, de az ssms-ben jelenleg csak Windows eszköz. Meg kell távoli csatlakozás SSMS használata Linux rendszerű SQL Server virtuális gépek Windows gépről. Helyileg, Linux az új [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) eszköz számos rendszergazdai feladatot végrehajthatnak. Platformfüggetlen adatbázis felügyeleti eszköz, lásd: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Eltávolíthatom teljesen az SQL Servert egy SQL-alapú virtuális gépről?**

   Igen, de továbbra is az SQL virtuális gép kell fizetnie, leírtak szerint [az SQL Server Azure virtuális gépek díjszabási útmutatóját](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.

## <a name="updating-and-patching"></a>Frissítések és javítások

1. **Hogyan frissíthetem az SQL Server egy Azure-beli virtuális gépen új verzióra vagy kiadásra?**

   Jelenleg nincs helyszíni frissítés az Azure-beli virtuális gépeken futtatott SQL Serverhez. Hozzon létre egy új Azure-beli virtuális gépet a kívánt SQL Server-verzióval vagy -kiadással, majd migrálja az adatbázisait az új kiszolgálóra [a szokásos adatmigrálási eljárásokkal](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Általános kérdések

1. **Az SQL Server magas rendelkezésre állású megoldások Azure-beli virtuális gépeken támogatottak?**

   Jelenleg nem. Always On rendelkezésre állási csoportok és a Feladatátvételi fürtszolgáltatást mindkét Linux rendszeren, például a támasztja fürtözési megoldásra van szüksége. A támogatott Linux-disztribúciók az SQL Server nem támogatja a magas rendelkezésre állású bővítmények a felhőben.

## <a name="resources"></a>További források

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gép az SQL Server használatának áttekintése](sql-server-linux-virtual-machines-overview.md)
* [Az SQL Server Linux virtuális gép kiépítése](provision-sql-server-linux-virtual-machine.md)
* [A Linux rendszeren futó SQL Server dokumentációja](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows virtuális gépek**:

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Egy SQL Server Windows virtuális gép kiépítése](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Gyakori kérdések (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)