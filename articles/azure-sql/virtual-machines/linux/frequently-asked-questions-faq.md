---
title: SQL Server on Linux Virtual Machines – gyakori kérdések | Microsoft Docs
description: Ez a cikk a SQL Server on Linux virtuális gépek futtatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792548"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>SQL Server on Linux virtuális gépekkel kapcsolatos gyakori kérdések
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Ez a cikk a [SQL Server on Linux virtuális gépek](sql-server-on-linux-vm-what-is-iaas-overview.md)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Képek

1. **Milyen SQL Server virtuálisgép-katalógus lemezképei érhetők el?**

   Az Azure a virtuális gépek (VM) lemezképeit a SQL Server összes támogatott fő kiadására vonatkozóan a Linux és a Windows összes kiadásában biztosítja. További részletekért tekintse meg a Linux rendszerű [virtuális gépek rendszerképeinek](sql-server-on-linux-vm-what-is-iaas-overview.md#create) és a [Windowsos virtuálisgép-rendszerképeknek](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)a teljes listáját.

1. **Frissültek a meglévő SQL Server virtuálisgép-katalógus rendszerképei?**

   A virtuális gép galériájában SQL Server rendszerképek a legújabb Linux-és Windows-frissítésekkel frissülnek. Linux-rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. Windows-rendszerképek esetén ide tartoznak a Windows Update fontosként megjelölt frissítések, beleértve a fontos SQL Server biztonsági frissítéseket és szervizcsomagokat is. SQL Server kumulatív frissítések kezelése a Linux és a Windows rendszerben eltérően történik. A Linux esetében SQL Server kumulatív frissítések is szerepelnek a frissítésben. Ebben az esetben azonban a Windows rendszerű virtuális gépek nem frissülnek SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **Milyen kapcsolódó SQL Server csomagokat is telepíteni kell?**

   A SQL Server on Linux virtuális gépeken alapértelmezés szerint telepített SQL Server csomagok megtekintéséhez lásd: [telepített csomagok](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **El SQL Server tudja távolítani a virtuális gépek lemezképeit a katalógusból?**

   Igen. Az Azure csak egy rendszerképet tart fenn főverzióként és kiadásként. Ha például új SQL Server szervizcsomagot szabadít fel, az Azure új rendszerképet hoz létre az adott szervizcsomaghoz tartozó galériához. Az előző szervizcsomag SQL Server rendszerképét a rendszer azonnal eltávolítja a Azure Portalból. A következő három hónapban azonban továbbra is elérhető a PowerShellből való kiépítés. Három hónap elteltével az előző szervizcsomag-rendszerkép már nem érhető el. Ez az eltávolítási szabályzat akkor is érvényes, ha a SQL Server verziója nem támogatott, amikor eléri az életciklusának végét.

## <a name="creation"></a>Létrehozás

1. **Hogyan Linux rendszerű virtuális gépet létrehozni SQL Server-mel?**

   A legegyszerűbb megoldás egy olyan linuxos virtuális gép létrehozása, amely SQL Server tartalmaz. Az Azure-regisztrációval és a portálon SQL Server VM létrehozásával kapcsolatos oktatóanyagért lásd: [Linux rendszerű virtuális gép létesítése SQL Server a Azure Portal](sql-vm-create-portal-quickstart.md). Lehetősége van arra is, hogy manuálisan telepítse a SQL Servert egy olyan virtuális gépre, amely vagy egy ingyenes licenccel rendelkező kiadás (Developer vagy Express), vagy egy helyszíni licenc újrafelhasználásával. Ha saját licencet használ, rendelkeznie kell [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility)-ban.

1. **Miért nem tudok kiépíteni egy RHEL vagy SLES SQL Server VM egy költségkerettel rendelkező Azure-előfizetéssel?**

   A RHEL és a SLES virtuális gépekhez a költségkeret nélküli előfizetés, valamint az előfizetéshez társított ellenőrzött fizetési mód (általában bankkártyás) szükséges. Ha kiépít egy RHEL vagy SLES virtuális gépet a költségkeret eltávolítása nélkül, az előfizetés le lesz tiltva, és az összes virtuális gép/szolgáltatás leállt. Ha ezt az állapotot futtatja, az előfizetés újbóli engedélyezésével [távolítsa el a](https://account.windowsazure.com/subscriptions)költségkeretet. A fennmaradó kreditek vissza lesznek állítva az aktuális számlázási időszakra, de a RHEL vagy SLES virtuálisgép-lemezkép felára a hitelkártyára kerül, ha úgy dönt, hogy újraindul, és folytatja a futtatását.

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Először hozzon létre egy csak Linux RENDSZERű virtuális gépet. Ezután futtassa a [SQL Server telepítési lépéseket](/sql/linux/sql-server-linux-setup#platforms) a Linux-disztribúcióhoz. Ha nem a SQL Server ingyenes licenccel rendelkező kiadását telepíti, akkor az Azure-ban SQL Server licenccel és [licenchordozhatóság frissítési garancia keretébental](https://azure.microsoft.com/pricing/license-mobility/)is rendelkeznie kell.

1. **Van-e a SQL Server saját licencű (BYOL) linuxos virtuálisgép-lemezképei?**

   Jelenleg nincsenek BYOL linuxos virtuálisgép-lemezképek SQL Server számára. Azonban manuálisan is telepítheti a SQL Servert egy linuxos virtuális gépre az előző kérdésekben tárgyalt módon.

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Nem. A fizetések másodpercenkénti licencelését nem lehet a saját licenc használatára váltani. Létre kell hoznia egy új linuxos virtuális gépet, telepítenie SQL Servert, és át kell telepítenie az adatait. A saját licencek létrehozásával kapcsolatos további részletekért tekintse meg az előző kérdést.

## <a name="administration"></a>Felügyelet

1. **Kezelhetem a SQL Servert futtató Linux rendszerű virtuális gépeket SQL Server Management Studio (SSMS) használatával?**

   Igen, de a SSMS jelenleg csak Windows-eszköz. A Windows rendszerű gépekről távolról kell csatlakoznia ahhoz, hogy SSMS-t használjon SQL Server rendszert futtató linuxos virtuális gépekkel. A Linuxon helyileg az új [MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf) eszköz számos felügyeleti feladatot képes végrehajtani. Platformfüggetlen adatbázis-kezelő eszköz esetén tekintse meg a következőt: [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Eltávolíthatom teljesen az SQL Servert egy SQL Server-alapú virtuális gépről?**

   Igen, de továbbra is a SQL Server VMért kell fizetnie, az [SQL Server Azure-beli virtuális gépek díjszabási útmutatójában](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)leírtak szerint. Ha már nincs szüksége az SQL Serverre, üzembe helyezhet egy új virtuális gépet, majd oda migrálhatja az adatokat és az alkalmazásokat. Ezt követően már eltávolíthatja az SQL Servert futtató virtuális gépet.

## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan frissíteni egy Azure-beli virtuális gépen lévő SQL Server új verziójára/kiadására?**

   Jelenleg nincs helyszíni frissítés az Azure-beli virtuális gépeken futtatott SQL Serverhez. Hozzon létre egy új Azure-beli virtuális gépet a kívánt SQL Server-verzióval vagy -kiadással, majd migrálja az adatbázisait az új kiszolgálóra [a szokásos adatmigrálási eljárásokkal](/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Általános kérdések

1. **Az Azure-beli virtuális gépeken támogatott magas rendelkezésre állású megoldások SQL Server?**

   Jelenleg nem. Az Always On rendelkezésre állási csoportok és a feladatátvételi fürtszolgáltatás is szükség van egy Linux-alapú fürtözési megoldásra, például a pacemakerre. A SQL Server támogatott Linux-disztribúciói nem támogatják a magas rendelkezésre állású bővítményeket a felhőben.

## <a name="resources"></a>Források

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server kiépítése Linux rendszerű virtuális gépen](sql-vm-create-portal-quickstart.md)
* [SQL Server on Linux dokumentáció](/sql/linux/sql-server-linux-overview)

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server kiépítése Windows rendszerű virtuális gépen](../windows/sql-vm-create-portal-quickstart.md)
* [Gyakori kérdések (Windows)](../windows/frequently-asked-questions-faq.md)