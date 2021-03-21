---
title: Az Azure-on Windows Virtual Machines SQL Server GYIK | Microsoft Docs
description: Ez a cikk a SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 014bbe4421bf00f35b2d80505cea288e75f8ca94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224673"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Gyakori kérdések az Azure-beli virtuális gépek SQL Serveréről
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Ez a cikk a [Windows Azure Virtual Machines (VM) SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Képek

1. **Milyen SQL Server virtuálisgép-katalógus lemezképei érhetők el?** 

   Az Azure a Windows és a Linux összes kiadásában a SQL Server összes támogatott fő kiadásához virtuális gépi rendszerképeket tart fenn. További információkért tekintse meg a [Windowsos virtuálisgép-rendszerképek](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) és a Linux rendszerű virtuálisgép- [lemezképek](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)teljes listáját.

1. **Frissültek a meglévő SQL Server virtuálisgép-katalógus rendszerképei?**

   A virtuális gép galériájában SQL Server rendszerképek a legújabb Windows-és Linux-frissítésekkel frissülnek. Windows-rendszerképek esetén ide tartoznak a Windows Update fontosként megjelölt frissítések, beleértve a fontos SQL Server biztonsági frissítéseket és szervizcsomagokat is. Linux-rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. SQL Server kumulatív frissítések kezelése a Linux és a Windows rendszerben eltérően történik. A Linux esetében SQL Server kumulatív frissítések is szerepelnek a frissítésben. Ebben az esetben azonban a Windows rendszerű virtuális gépek nem frissülnek SQL Server vagy a Windows Server összesítő frissítéseivel.

1. **El SQL Server tudja távolítani a virtuális gépek lemezképeit a katalógusból?**

   Igen. Az Azure csak egy rendszerképet tart fenn főverzióként és kiadásként. Ha például új SQL Server szervizcsomagot szabadít fel, az Azure új rendszerképet hoz létre az adott szervizcsomaghoz tartozó galériához. Az előző szervizcsomag SQL Server rendszerképét a rendszer azonnal eltávolítja a Azure Portalból. A következő három hónapban azonban továbbra is elérhető a PowerShellből való kiépítés. Három hónap elteltével az előző szervizcsomag-rendszerkép már nem érhető el. Ez az eltávolítási szabályzat akkor is érvényes, ha a SQL Server verziója nem támogatott, amikor eléri az életciklusának végét.


1. **Lehet olyan régebbi rendszerképet telepíteni SQL Server, amely nem látható a Azure Portalban?**

   Igen, a PowerShell használatával. A SQL Server virtuális gépek PowerShell használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [SQL Server Virtual Machines szolgáltatás kiépítése a Azure PowerShellsal](create-sql-vm-powershell.md).
   
1. **Létre lehet hozni egy általános Azure Marketplace-SQL Server rendszerképet a saját SQL Server VM, és azt használva üzembe helyezhetem a virtuális gépeket?**

   Igen, de ezt követően [regisztrálnia kell az egyes SQL Server VMokat az SQL IaaS-ügynök bővítményével](sql-agent-extension-manually-register-single-vm.md) , hogy kezelje a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint az automatikus javítás és az automatikus biztonsági mentés. A bővítménnyel való Regisztráláskor meg kell adnia az egyes SQL Server VMokhoz tartozó licenc típusát is.

1. **Hogyan általánosíthatja SQL Server az Azure-beli virtuális gépen, és felhasználhatja az új virtuális gépek üzembe helyezéséhez?**

   Telepítheti a Windows Server rendszerű virtuális gépeket (SQL Server nélkül), és az [SQL Sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) folyamat használatával általánosíthatja SQL Server az Azure-beli virtuális gépen (Windows) az SQL Server telepítési adathordozóval. A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. Azok az ügyfelek, akik nem rendelkeznek frissítési garanciával, a kívánt kiadással rendelkező Azure Marketplace SQL Server VM-rendszerkép telepítési adathordozóját használhatják.

   Azt is megteheti, hogy az Azure Marketplace-en található SQL Server rendszerképek egyikét használja az Azure-beli virtuális gépen lévő SQL Server általánosításához. Vegye figyelembe, hogy a saját rendszerkép létrehozása előtt törölnie kell a következő beállításkulcsot a forrás rendszerképben. Ennek elmulasztása miatt a SQL Server telepítő rendszerindítási mappájának és/vagy az SQL IaaS-bővítménynek a meghibásodási állapotában való közelítése sikertelen volt.

   Beállításkulcs elérési útja:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server az Azure-beli virtuális gépeken, beleértve az egyéni általánosított rendszerképekből telepítetteket is, [regisztrálni kell az SQL IaaS-ügynök bővítménnyel](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) , hogy megfeleljenek a megfelelőségi követelményeknek, és olyan opcionális funkciókat használjanak, mint az automatikus javítás és az automatikus biztonsági mentés. A bővítmény lehetővé teszi az egyes SQL Server VMok [licencelési típusának megadását](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) is.

1. **Használhatom a saját VHD-t egy SQL Server VM üzembe helyezéséhez?**

   Igen, de ezt követően [regisztrálnia kell az egyes SQL Server VMokat az SQL IaaS-ügynök bővítményével](sql-agent-extension-manually-register-single-vm.md) , hogy kezelje a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint az automatikus javítás és az automatikus biztonsági mentés.

1. **Lehetséges a virtuálisgép-katalógusban nem látható konfigurációk beállítása (például Windows 2008 R2 + SQL Server 2012)?**

   Nem. SQL Server tartalmazó virtuálisgép-katalógusbeli rendszerképek esetén a megadott rendszerképek egyikét ki kell választania a Azure Portal vagy a [PowerShell](create-sql-vm-powershell.md)használatával. Azonban lehetősége van arra, hogy Windows rendszerű virtuális gépet helyezzen üzembe, és telepítse SQL Server. Ezután [regisztrálnia kell az SQL Server VMt az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md) , hogy az Azure Portal kezelje a SQL Server VM, valamint olyan funkciókat, mint az automatikus javítás és az automatikus biztonsági mentés. 


## <a name="creation"></a>Létrehozás

1. **Hogyan létrehoz egy Azure-beli virtuális gépet SQL Server?**

   A legegyszerűbb módszer egy SQL Servert tartalmazó virtuális gép létrehozása. Az Azure-regisztrációval és a portálon SQL Server VM létrehozásával kapcsolatos oktatóanyagért lásd: [SQL Server virtuális gép kiépítése a Azure Portal](create-sql-vm-portal.md). Kiválaszthat egy olyan virtuálisgép-rendszerképet, amely SQL Server másodpercenkénti fizetést használ, vagy használhat olyan rendszerképet, amely lehetővé teszi a saját SQL Server licencének használatát. Lehetősége van arra is, hogy manuálisan telepítse a SQL Servert egy olyan virtuális gépre, amely vagy egy ingyenes licenccel rendelkező kiadás (Developer vagy Express), vagy egy helyszíni licenc újrafelhasználásával. Ügyeljen arra, hogy [regisztrálja SQL Server VM az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md) , hogy kezelhesse a SQL Server VM a portálon, valamint olyan funkciókat is használhat, mint az automatikus javítás és az automatikus biztonsági mentés. Ha saját licencet használ, rendelkeznie kell [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility/)-ban. További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](pricing-guidance.md).

1. **Hogyan telepíthetem a helyszíni SQL Server-adatbázist a felhőbe?**

   Először hozzon létre egy SQL Server példánnyal rendelkező Azure-beli virtuális gépet. Ezután telepítse át a helyszíni adatbázisait erre a példányra. Az adatáttelepítési stratégiákat lásd: [SQL Server adatbázis áttelepítése egy Azure-beli virtuális gépen SQL Server](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Ezt háromféleképpen teheti meg. Ha Ön Nagyvállalati Szerződés (EA) ügyfél, kiépítheti a [licenceket támogató virtuálisgép-lemezképek](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)egyikét, amely a saját licenc (BYOL) néven is ismert. Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), engedélyezheti a [Azure Hybrid Benefit](licensing-model-azure-hybrid-benefit-ahb-change.md) egy meglévő utólagos elszámolású (TB) rendszerképben. Másik lehetőségként másolja át az SQL Server telepítési adathordozóját egy Windows Server rendszerű virtuális gépre, majd telepítse az SQL Servert a virtuális gépre. Ügyeljen arra, hogy regisztrálja a SQL Server VMt a portál felügyeletére, az automatikus biztonsági mentésre és az automatikus javításra szolgáló funkciók [bővítményével](sql-agent-extension-manually-register-single-vm.md) . 


1. **Az ügyfélnek szüksége van SQL Server ügyfél-hozzáférési licencekre (CAL) az Azure Virtual Machines-on futó, SQL Server utólagos elszámolású képhez való kapcsolódáshoz?**

   Nem. Az ügyfeleknek ügyféllicencre van szükségük a saját licenc használatakor, és a SQL Server SA-kiszolgáló/CAL virtuális gép áthelyezését az Azure-beli virtuális gépekre. 

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Igen. Az utólagos elszámolású (TB) katalógust egyszerűen átválthatja a saját licenc használatára (BYOL), ha engedélyezi a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  További információ: [a SQL Server VM licencelési modelljének módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md). Ez a lehetőség jelenleg csak nyilvános és Azure Government Felhőbeli ügyfelek számára érhető el.


1. **A licencelési modell módosítása során az SQL Server le fog állni?**

   Nem. [A licencelési modell módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md) nem igényel leállást SQL Server mivel a változás azonnal hatályba lép, és nem igényli a virtuális gép újraindítását. Ahhoz azonban, hogy a SQL Server VM az SQL IaaS-ügynök bővítménnyel regisztrálja, az [SQL IaaS bővítmény](sql-server-iaas-agent-extension-automate-management.md) előfeltétele, és az SQL IaaS-bővítmény _teljes_ módban történő telepítése újraindítja a SQL Server szolgáltatást. Ilyen esetben, ha az SQL IaaS bővítményt telepíteni kell, vagy a korlátozott funkcionalitás érdekében telepítse azt egy _egyszerű_ módban, vagy a karbantartási időszak során _teljes_ módba kell telepítenie. Az _egyszerűsített_ módban telepített SQL IaaS bővítmény bármikor frissíthető _teljes_ módba, de a SQL Server szolgáltatás újraindítását igényli. 
   
1. **Lehetőség van a licencelési modellek váltására a klasszikus modell használatával üzembe helyezett SQL Server VMon?**

   Nem. A licencelési modellek módosítása klasszikus virtuális gépen nem támogatott. Áttelepítheti a virtuális gépet a Azure Resource Manager modellbe, és regisztrálhatja az SQL IaaS-ügynök bővítményét. Miután regisztrálta a virtuális gépet az SQL IaaS-ügynök bővítménnyel, a licencelési modell módosításai elérhetők lesznek a virtuális gépen.

1. **Az Azure Portal használható egyszerre több példány ugyanazon a virtuális gépen történő kezelésére?**

   Nem. A portál felügyelete az SQL IaaS-ügynök bővítmény által biztosított szolgáltatás, amely a SQL Server IaaS-ügynök bővítményére támaszkodik. Ennek megfelelően ugyanazok a korlátozások vonatkoznak a bővítményre, mint a bővítményre. A portálon csak egy alapértelmezett példány vagy egy elnevezett példány kezelhető, feltéve, hogy megfelelően van konfigurálva. További információ ezekről a korlátozásokról: [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md). 

1. **A CSP-előfizetések aktiválhatják az Azure Hybrid Benefitet?**

   Igen, az Azure Hybrid Benefit elérhető a CSP-előfizetésekhez. A CSP-ügyfeleknek először telepíteniük kell az utólagos elszámolású lemezképet, majd [a licencelési modellt úgy kell megváltoztatnia](licensing-model-azure-hybrid-benefit-ahb-change.md) , hogy a saját licencét használja.
   
 
1. **Fizetnem kell az SQL Server licenceléséért egy Azure-beli virtuális gépen, ha csak készenlétben tartom vagy feladatátvételre használom?**

   Ahhoz, hogy a készenléti másodlagos rendelkezésre állási csoport vagy a feladatátvételi fürtszolgáltatás számára ingyenes passzív licenc legyen, meg kell felelnie a következő feltételeknek, amint azt a [termék licencelési feltételei](https://www.microsoft.com/licensing/product-licensing/products)ismertetik:

   1. Frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)rendelkezik a [licencek mobilitásával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) . 
   1. A passzív SQL Server-példány nem szolgáltat SQL Server-adatokat az ügyfeleknek vagy futtat aktív SQL Server-alapú számítási feladatokat. Csak az elsődleges kiszolgálóval való szinkronizálást végzi, a passzív adatbázist pedig üzemi tartalék állapotban tartja. Ha az adatok, például az aktív SQL Server munkaterheléseket futtató ügyfeleknek készült jelentések, vagy a termék használati feltételeiben megadott egyéb feladatok elvégzése, akkor azt fizetős licenccel rendelkező SQL Server példánynak kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbáziskonzisztencia-ellenőrzés vagy CheckDB, teljes biztonsági mentések, tranzakciós naplók biztonsági mentése, valamint az erőforrás-használati adatok monitorozása. Az elsődleges és a hozzá tartozó vészhelyreállítási példányt egyidejűleg is futtathatja 90 naponta a vészhelyreállítási tesztelés rövid időszakaira. 
   1. Az aktív SQL Server licencet a frissítési garancia szabályozza, és lehetővé teszi **egy** passzív másodlagos SQL Server példány használatát, amely akár a licenccel rendelkező aktív kiszolgálóval azonos mennyiségű számítási kapacitással is rendelkezik. 
   1. A másodlagos SQL Server VM a vész- [helyreállítási](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) licencet használja a Azure Portal.
   
1. **Mi tekintendő passzív példánynak?**

   A passzív SQL Server-példány nem szolgáltat SQL Server-adatokat az ügyfeleknek vagy futtat aktív SQL Server-alapú számítási feladatokat. Csak az elsődleges kiszolgálóval való szinkronizálást végzi, a passzív adatbázist pedig üzemi tartalék állapotban tartja. Ha adatokat szolgál ki, például az aktív SQL Server-alapú számítási feladatokat futtató ügyfeleknek készült jelentéseket, vagy a termék használati feltételeiben meghatározottaktól eltérő feladatokat végez, akkor fizetős licenccel rendelkező SQL Server-példánynak kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbáziskonzisztencia-ellenőrzés vagy CheckDB, teljes biztonsági mentések, tranzakciós naplók biztonsági mentése, valamint az erőforrás-használati adatok monitorozása. Az elsődleges és a hozzá tartozó vészhelyreállítási példányt egyidejűleg is futtathatja 90 naponta a vészhelyreállítási tesztelés rövid időszakaira.
   

1. **Milyen forgatókönyvek használhatják ki a vészhelyreállítás (DR) előnyeit?**

   A [licencelési útmutató](https://aka.ms/sql2019licenseguide) olyan forgatókönyveket biztosít, amelyekben a vész-helyreállítási juttatás használható. További információért tekintse meg a termék használati feltételeit, illetve forduljon a licencelési kapcsolattartóhoz vagy a fiókkezelőhöz.

1. **Milyen előfizetések támogatják a vészhelyreállítás (DR) előnyeit?**

   Az olyan átfogó programok, amelyek a Frissítési Garanciával egyenértékű előfizetési jogokat kínálnak rögzített előnyként, támogatják a vészhelyreállítási lehetőséget. Ide tartoznak az alábbiak: azonban nem korlátozódik a nyílt értékre (OV), az Open Value előfizetés (OVS), a Nagyvállalati Szerződés (EA), a Nagyvállalati Szerződés előfizetés (EAS), valamint a kiszolgáló és a Felhőbeli regisztráció (SCE) használatára. További információkért tekintse meg a [termék feltételeit](https://www.microsoft.com/licensing/product-licensing/products) , és forduljon a licencelési partnerekhez vagy a fiókkezelőhöz. 

   
 ## <a name="extension"></a>Mellék

1. **Regisztrálja a virtuális gépet az új SQL IaaS-ügynök bővítménnyel, hogy további költségekkel jár?**

   Nem. Az SQL IaaS-ügynök bővítménye csak további kezelhetőséget tesz lehetővé az Azure-beli virtuális gépeken SQL Server további költségek nélkül. 

1. **Elérhető az SQL IaaS-ügynök bővítmény az összes ügyfél számára?**
 
   Igen, ha a SQL Server VM a nyilvános felhőben a Resource Manager-modellel lett telepítve, nem a klasszikus modellt. Minden más ügyfél regisztrálhat az új SQL IaaS-ügynök bővítménnyel. Azonban csak a frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) rendelkező ügyfelek használhatják a saját licencét a [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server VM való aktiválásával. 

1. **Mi történik a bővítmény (_Microsoft. SqlVirtualMachine_) erőforrással, ha a virtuálisgép-erőforrás át van helyezve vagy el van dobva?** 

   A Microsoft. számítási/VirtualMachine erőforrás eldobása vagy áthelyezése után a rendszer a társított Microsoft. SqlVirtualMachine erőforrást a művelet aszinkron replikálására értesíti.

1. **Mi történik a virtuális géppel, ha a bővítmény (_Microsoft. SqlVirtualMachine_) erőforrása el van dobva?**

    A Microsoft. SqlVirtualMachine-erőforrás eldobásakor a Microsoft. számítás/VirtualMachine erőforrás nincs hatással. A licencelés módosításai azonban alapértelmezés szerint visszaállnak az eredeti rendszerkép forrására. 

1. **Lehetséges a saját üzembe helyezett SQL Server virtuális gépek regisztrálása az SQL IaaS-ügynök bővítménnyel?**

    Igen. Ha a saját adathordozóról telepített SQL Serverokat, és telepítette az SQL IaaS bővítményt, akkor az SQL IaaS bővítmény által biztosított kezelhetőségi előnyök beszerzéséhez regisztrálja SQL Server VM a bővítményben.    


## <a name="administration"></a>Felügyelet

1. **Telepíthetem a SQL Server egy második példányát ugyanarra a virtuális gépre? Megváltoztathatom az alapértelmezett példány telepített funkcióit?**

   Igen. A SQL Server telepítési adathordozó a **C** meghajtó egyik mappájában található. **Setup.exe** futtatása az adott helyről új SQL Server példányok hozzáadásához vagy a SQL Server egyéb telepített szolgáltatásainak módosításához a gépen. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, az automatikus javítás és a Azure Key Vault integráció, csak az alapértelmezett példányon vagy egy megfelelően konfigurált megnevezett példányon működnek (lásd a 3. kérdést). [A Azure Hybrid Benefiton](licensing-model-azure-hybrid-benefit-ahb-change.md) vagy az utólagos **elszámolású licencelési** modellen keresztül használó ügyfelek a SQL Server több példányát is telepíthetik a virtuális gépre anélkül, hogy további licencelési költségekkel kellene számolniuk. A további SQL Server példányok esetében a rendszererőforrások nem megfelelően vannak konfigurálva. 

1. **Mi a példányok maximális száma egy virtuális gépen?**
   SQL Server 2012 a SQL Server 2019 képes támogatni [50 példányokat](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) egy önálló kiszolgálón. Ez ugyanaz a korlát, függetlenül az Azure-ban a helyszínen. Az [ajánlott eljárások](performance-guidelines-best-practices.md#multiple-instances) című témakörből megtudhatja, hogyan készítheti elő a környezetét. 

1. **Eltávolíthatom az SQL Server alapértelmezett példányát?**

   Igen, de bizonyos szempontokat figyelembe kell venni. Először is SQL Server kapcsolódó számlázás a virtuális gép licencelési modelljétől függően továbbra is előfordulhat. Másodszor, ahogy az előző válaszban is szerepel, vannak olyan funkciók, amelyek a [SQL Server IaaS-ügynök bővítményére](sql-server-iaas-agent-extension-automate-management.md)támaszkodnak. Ha a IaaS-bővítmény eltávolítása nélkül távolítja el az alapértelmezett példányt, a bővítmény továbbra is megkeresi az alapértelmezett példányt, és Eseménynapló-hibákat eredményezhet. Ezek a hibák a következő két forrásból származnak: **Microsoft SQL Server hitelesítőadat-kezelés** és **Microsoft SQL Server IaaS-ügynök**. Az egyik hiba a következőhöz hasonló lehet:

      Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, hogy eltávolítja az alapértelmezett példányt, távolítsa el a [SQL Server IaaS-ügynök bővítményt](sql-server-iaas-agent-extension-automate-management.md) is. 

1. **Használhatom SQL Server megnevezett példányát az IaaS bővítménnyel?**
   
   Igen, ha a megnevezett példány az egyetlen példány a SQL Serveron, és az eredeti alapértelmezett példány [eltávolítása megfelelő](sql-server-iaas-agent-extension-automate-management.md#named-instance-support)volt. Ha nincs alapértelmezett példány, és több megnevezett példány van egyetlen SQL Server VM, akkor a SQL Server IaaS-ügynök bővítmény telepítése sikertelen lesz.  

1. **Eltávolíthatom SQL Server és a kapcsolódó licencek számlázását egy SQL Server VMból?**

   Igen, de további lépéseket kell tennie, hogy elkerülje a SQL Server-példány díját a [díjszabási útmutatóban](pricing-guidance.md)leírtak szerint. Ha teljes mértékben el szeretné távolítani a SQL Server példányt, áttelepítheti egy másik Azure-beli virtuális gépre anélkül, hogy SQL Server előre telepíteni a virtuális gépre, és törölni kívánja az aktuális SQL Server VM. Ha szeretné megtartani a virtuális gépet, de SQL Server számlázását, kövesse az alábbi lépéseket: 

   1. Ha szükséges, biztonsági mentést készíthet az összes adatairól, beleértve a rendszeradatbázisokat is. 
   1. Távolítsa el SQL Server teljesen, beleértve az SQL IaaS bővítményt (ha van).
   1. Telepítse az ingyenes [SQL Express kiadást](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Az SQL IaaS-ügynök bővítményét [egyszerűsített módban](sql-agent-extension-manually-register-single-vm.md)regisztrálja.
   1. választható Tiltsa le az expressz SQL Server szolgáltatást a szolgáltatás indításának letiltásával. 

1. **Az Azure Portal használható egyszerre több példány ugyanazon a virtuális gépen történő kezelésére?**

   Nem. A portál felügyeletét az SQL IaaS-ügynök bővítménye nyújtja, amely a SQL Server IaaS-ügynök bővítményére támaszkodik. Ezért ugyanezek a korlátozások érvényesek a kiterjesztésre. A portálon csak egy alapértelmezett példány, vagy egy elnevezett példány kezelhető, ha megfelelően van konfigurálva. További információ: [SQL Server IaaS-ügynök bővítménye](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan egy Azure-beli virtuális gépen SQL Server egy másik verziójára/kiadására váltani?**

   Az ügyfelek az SQL Server kívánt verzióját vagy kiadását tartalmazó telepítési adathordozóval módosíthatják az SQL Server verzióját/kiadását. A kiadás módosítása után az Azure Portalon módosítsa a virtuális gép kiadási tulajdonságát, hogy a virtuális gép számlázása a pontos adatokkal történjen. További információkért lásd: [SQL Server VM kiadásának módosítása](change-sql-server-edition.md). Az SQL Server különböző verziói esetében nincs különbség a számlázásban, így az SQL Server verziójának módosítása után nincs szükség további műveletre.

1. **Hol szerezhetem be az SQL Server kiadásának vagy verziójának módosításához szükséges telepítési adathordozót?**

   A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. Azok a felhasználók, akik nem rendelkeznek frissítési garanciával, a kívánt kiadással rendelkező Azure Marketplace SQL Server VM-rendszerkép telepítési adathordozóját használhatják.
   
1. **Hogyan történik a frissítések és a szervizcsomagok alkalmazása SQL Server-alapú virtuális gépeken?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetében manuálisan alkalmazhatja a Windows-frissítéseket, vagy engedélyezheti az [Automatikus javítás](automated-patching.md) nevű ütemezési szolgáltatást. Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

1. **Frissíthetem a SQL Server 2008/2008 R2 példányt az SQL IaaS-ügynök bővítménnyel való regisztráció után?**

   Ha az operációs rendszer Windows Server 2008 R2 vagy újabb, igen. Bármilyen telepítési adathordozót használhat a SQL Server verziójának és kiadásának frissítésére, majd az [SQL IaaS-bővítmény üzemmódjának](sql-server-iaas-agent-extension-automate-management.md#management-modes)frissítését a teljes _ügynök nélkül_ is . Ez lehetővé teszi az SQL IaaS bővítmény összes előnyének elérését, például a portál kezelhetőségét, az automatikus biztonsági mentést és az automatizált javításokat. Ha az operációs rendszer verziója Windows Server 2008, csak a nem ügynök mód támogatott. 

1. **Hogyan szerezhetek be ingyenes kibővített biztonsági frissítéseket a megszűnt támogatottságú SQL Server 2008- és SQL Server 2008 R2-példányokhoz?**

   Az Azure-beli virtuális gépekre való áttéréssel [ingyenes, kiterjesztett biztonsági frissítéseket](sql-server-2008-extend-end-of-support.md) kaphat SQL Server. További információért tekintse meg a [támogatottság megszűnésére vonatkozó lehetőségeket](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Általános kérdések

1. **Az Azure-beli virtuális gépeken támogatottak-e SQL Server feladatátvevő fürt példányai (a)?**

   Igen. A feladatátvevő fürt példányát telepítheti a tárolási alrendszer [prémium fájlmegosztás (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) vagy [közvetlen tárolóhelyek (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) használatával. A prémium szintű fájlmegosztás biztosítja a IOPS és az átviteli kapacitást, amely megfelel a sok számítási feladat igényeinek. Az IO-igényes számítási feladatokhoz érdemes lehet a közvetlen tárolóhelyeket használni a Kiemelt prémium vagy a rendkívül nagy méretű lemezek alapján. Azt is megteheti, hogy harmadik féltől származó fürtözési vagy tárolási megoldásokat is használhat a [magas rendelkezésre állás és a vész-helyreállítás az Azure Virtual Machines SQL Server](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Ekkor a _teljes_ [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md) nem támogatott az Azure-beli SQL Server-es verziója esetén. Javasoljuk, hogy távolítsa el a _teljes_ bővítményt a modulban részt vevő virtuális gépekről, és telepítse a bővítményt _egyszerűsített_ módban. Ez a bővítmény támogatja a szolgáltatásokat, például az automatikus biztonsági mentést és a javítást, valamint a SQL Server egyes portál-funkcióit. Ezek a funkciók a _teljes_ ügynök eltávolítása után nem fognak működni SQL Server virtuális gépeken.

1. **Mi a különbség a SQL Server virtuális gépek és a SQL Database szolgáltatás között?**

   Az Azure-beli virtuális gépeken futó SQL Server fogalma nem különbözik a távoli adatközpontok SQL Server futtatásának. Ezzel szemben a [Azure SQL Database](../../database/sql-database-paas-overview.md) szolgáltatásként kínált adatbázis-szolgáltatást. A SQL Database nem rendelkezik hozzáféréssel az adatbázisokat üzemeltető gépekhez. Teljes összehasonlításért tekintse [meg a felhőalapú SQL Server lehetőség választása: Azure SQL (Péter) adatbázis vagy SQL Server Azure-beli virtuális gépeken (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md)című témakört.

1. **Hogyan telepíteni az SQL-adateszközöket az Azure-beli virtuális gépre?**

    Töltse le és telepítse az SQL-adateszközöket [Microsoft SQL Server Adateszközökről – üzleti intelligencia a Visual Studio 2013-hez](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Támogatottak-e a SQL Server virtuális gépeken futó MSDTC-vel rendelkező elosztott tranzakciók?**
   
    Igen. A helyi DTC SQL Server 2016 SP2 és újabb rendszereken támogatott. Az Always On rendelkezésre állási csoportok használatakor azonban meg kell vizsgálni az alkalmazásokat, mivel a feladatátvétel során a repülés során végzett tranzakciók sikertelenek lesznek, és újra kell próbálkozni. A fürtözött DTC a Windows Server 2019-től kezdődően érhető el. 
    
1. **Az Azure SQL-alapú virtuális gépek régiónként helyezik át vagy tárolják az ügyféladatokat?**

   Nem. Valójában az Azure SQL-alapú virtuális gép és az SQL IaaS-ügynök bővítmény nem tárolja az ügyféladatokat.

## <a name="sql-server-iaas-agent-extension"></a>Az SQL Server IaaS-ügynök bővítménye

1. **Regisztrálhatom a SQL Server VM az Azure Marketplace-en egy SQL Server rendszerképből kiépítve?**

   Nem. A Microsoft automatikusan regisztrálja az Azure Marketplace-en SQL Server rendszerképből kiépített virtuális gépeket. A bővítményhez való regisztráció csak akkor szükséges, ha a virtuális gép *nem* lett kiépítve az Azure Marketplace SQL Server rendszerképeiből, és a SQL Server önálló telepítése történt.

1. **Elérhető az SQL IaaS-ügynök bővítmény az összes ügyfél számára?** 

   Igen. Az ügyfeleknek regisztrálniuk kell a SQL Server virtuális gépeket a kiterjesztéssel, ha nem használnak SQL Server rendszerképet az Azure Marketplace-ről, hanem önállóan telepített SQL Server, vagy ha az egyéni VHD-t hozták. Az összes típusú előfizetés (Direct, Nagyvállalati Szerződés és Cloud Solution Provider) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL IaaS-ügynök bővítménnyel.

1. **Mi az alapértelmezett felügyeleti mód az SQL IaaS-ügynök bővítménnyel való regisztráláskor?**

   Az SQL IaaS-ügynök bővítménnyel való regisztráláskor az alapértelmezett felügyeleti mód a *kis*-és nagyméretű. Ha a SQL Server felügyeleti tulajdonság nincs beállítva a bővítményhez való regisztráláskor, a mód egyszerűként lesz beállítva, és a SQL Server szolgáltatás nem fog újraindulni. Javasoljuk, hogy először az SQL IaaS-ügynök bővítménnyel regisztráljon, és a karbantartási időszak alatt a teljes verzióra frissítsen. Hasonlóképpen, az [automatikus regisztrálási funkció](sql-agent-extension-automatic-registration-all-vms.md)használata esetén az alapértelmezett felügyelet is egyszerű.

1. **Milyen előfeltételeket kell regisztrálni az SQL IaaS-ügynök bővítménnyel?**

   Az SQL IaaS-ügynök bővítmény nem rendelkezik előfeltételekkel, mint a virtuális gépre SQL Server telepítve. Vegye figyelembe, hogy ha az SQL IaaS-ügynök bővítménye teljes módban van telepítve, akkor a SQL Server szolgáltatás újraindul, így a karbantartási időszak során ajánlott.

1. **Regisztrálja az SQL IaaS-ügynök bővítményét, telepítsen ügynököt a virtuális gépre?**

   Igen, az SQL IaaS-ügynök bővítménnyel való regisztrálása teljes kezelhetőségi módban egy ügynököt telepít a virtuális gépre. A Lightweight vagy nem ügynök módban való regisztráció nem. 

   Ha az SQL IaaS-ügynök bővítményét egyszerűsített módban regisztrálja, a csak az SQL IaaS-ügynök bővítmény *bináris fájljait* másolja át a virtuális gépre, nem telepíti az ügynököt. Ezeket a bináris fájlokat a rendszer az ügynök telepítésére használja, ha a felügyeleti mód teljes verzióra frissül.


1. **Regisztrálja az SQL IaaS-ügynök bővítményét a virtuális gépen a SQL Server újraindításával?**

   Ez a regisztráció során megadott módtól függ. Ha a Lightweight vagy a nem ügynök mód van megadva, akkor a SQL Server szolgáltatás nem indul újra. Azonban a teljes felügyeleti mód megadásával a SQL Server szolgáltatás újraindul. Az automatikus regisztrálási funkció a SQL Server virtuális gépeket egyszerű módban regisztrálja, kivéve, ha a Windows Server verziója 2008, és ebben az esetben a SQL Server VMt a rendszer ügynöki módban regisztrálja. 

1. **Mi a különbség a könnyű és a nem ügynök közötti felügyeleti üzemmódok között az SQL IaaS-ügynök bővítménnyel való regisztráláskor?** 

   A nem ügynök-felügyeleti mód az egyetlen elérhető felügyeleti mód a SQL Server 2008 és SQL Server 2008 R2 rendszerhez Windows Server 2008 rendszeren. A Windows Server minden újabb verziójában a két elérhető kezelhetőségi mód könnyű és teljes. 

   A nem ügynök üzemmódhoz az ügyfélnek be kell állítania SQL Server Version és Edition tulajdonságokat. A könnyű mód lekérdezi a virtuális gépet a SQL Server példány verziójának és kiadásának megkereséséhez.

1. **Regisztrálhatok az SQL IaaS-ügynök bővítménnyel a SQL Server licenc típusának megadása nélkül?**

   Nem. Az SQL Server licenc típusa nem választható tulajdonság az SQL IaaS-ügynök bővítménnyel való regisztráláskor. Ha az SQL IaaS-ügynök bővítményt az összes kezelhetőségi módban (nem ügynök, könnyűsúly és teljes) regisztrálja, akkor a SQL Server Azure Hybrid Benefit licencfeltételeket kell beállítania. Ha a telepített SQL Server ingyenes verzióit (például a fejlesztői vagy a próbaverziós verziót) használja, regisztrálnia kell az utólagos elszámolású licenceléssel. Azure Hybrid Benefit csak SQL Server, például a nagyvállalati és a standard kiadások fizetős verzióihoz érhető el.

1. **Frissíthetem a SQL Server IaaS bővítményt a nem ügynök módból a teljes módba?**

   Nem. A kezelhetőségi mód teljes vagy egyszerű frissítése nem érhető el a nem ügynök módban. Ez a Windows Server 2008 technikai korlátozása. Először a Windows Server 2008 R2 vagy újabb verzióra kell frissítenie az operációs rendszert, majd frissítenie kell a teljes felügyeleti módra. 

1. **Frissíthetem a SQL Server IaaS-bővítményt az egyszerűsített módból a teljes módba?**

   Igen. A kezelhetőségi mód könnyűről teljesre történő frissítése Azure PowerShell vagy a Azure Portal használatával támogatott. Ez elindítja a SQL Server szolgáltatás újraindítását.

1. **Visszaválthatom a SQL Server IaaS-bővítményt a teljes módból a nem ügynökre vagy az egyszerűsített felügyeleti módra?**

   Nem. A SQL Server IaaS-bővítmény kezelhetőségi módjának lefokozása nem támogatott. A kezelhetőségi mód nem állítható vissza a teljes módból a könnyű vagy a nem ügynök módba, és nem lehet leértékelni az egyszerűsített üzemmódból a nem ügynök módba. 

   Ha módosítani szeretné a kezelhetőségi módot a teljes kezelhetőséggel, szüntesse meg a SQL Server VM [regisztrációját](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) az SQL IaaS-ügynök bővítményből az SQL-alapú virtuális gép _erőforrásának_ eldobásával, majd a SQL Server VM újbóli regisztrálásával az SQL IaaS-ügynök bővítményével egy másik felügyeleti módban.

1. **Regisztrálhatok az SQL IaaS-ügynök bővítménnyel a Azure Portal?**

   Nem. Az SQL IaaS-ügynök bővítménnyel való regisztráció nem érhető el a Azure Portal. Az SQL IaaS-ügynök bővítménnyel való regisztráció csak az Azure CLI vagy a Azure PowerShell esetén támogatott. 

1. **Regisztrálhatok egy virtuális gépet az SQL IaaS-ügynök bővítménnyel SQL Server telepítése előtt?**

   Nem. A virtuális gépnek rendelkeznie kell legalább egy SQL Server (adatbázismotor) példánnyal, hogy sikeresen regisztrálja az SQL IaaS-ügynök bővítményét. Ha a virtuális gépen nincs SQL Server példány, az új Microsoft. SqlVirtualMachine erőforrás sikertelen állapotba kerül.

1. **Regisztrálhatok egy virtuális gépet az SQL IaaS-ügynök bővítménnyel, ha több SQL Server példány is van?**

   Igen, a virtuális gépen alapértelmezett példány van megadva. Az SQL IaaS-ügynök bővítmény csak egy SQL Server (adatbázismotor) példányt regisztrál. Az SQL IaaS-ügynök bővítmény több példány esetén regisztrálja az alapértelmezett SQL Server példányt.

1. **Regisztrálhatok egy SQL Server feladatátvevő fürt példányát az SQL IaaS-ügynök bővítménnyel?**

   Igen. Az Azure-beli virtuális gépeken SQL Server feladatátvevő fürt példányai az SQL IaaS-ügynök bővítményét egyszerűsített módban lehet regisztrálni. SQL Server feladatátvevő fürt példányai azonban nem frissíthetők teljes kezelhetőségi módba.

1. **Regisztrálhatom a virtuális gépet az SQL IaaS-ügynök bővítménnyel, ha az Always On rendelkezésre állási csoport konfigurálva van?**

   Igen. Ha egy always on rendelkezésre állási csoport konfigurációjában vesz részt, az SQL IaaS-ügynök bővítménnyel nem lehet SQL Server példányt regisztrálni egy Azure-beli virtuális gépen.

1. **Mennyibe kerül az SQL IaaS-ügynök bővítménnyel való regisztráció, vagy a teljes kezelhetőségi módra való frissítés?**

   Nincsenek. Az SQL IaaS-ügynök bővítménnyel való regisztrációhoz és a három kezelhetőségi mód bármelyikéhez nem tartozik díj. A SQL Server VM kezelése a bővítménnyel teljesen ingyenes. 

1. **Milyen hatással van a teljesítmény a különböző kezelhetőségi módok használatával?**

   A nem befolyásolható a nem- *ügynök* és a *könnyű* kezelhetőségi mód használata. Az operációs rendszerre telepített két szolgáltatás *teljes* kezelhetőségi módjának használata minimális hatással van. Ezek a Feladatkezelő segítségével figyelhetők meg, és a beépített Windows-szolgáltatások konzolon láthatók. 

   A két szolgáltatás neve:
   - `SqlIaaSExtensionQuery` (Megjelenítendő név – `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Megjelenítendő név – `Microsoft SQL Server IaaS Agent` )

1. **Hogyan távolítsa el a bővítményt?**

   Távolítsa el a bővítményt a SQL Server VM SQL IaaS-ügynök bővítményből való [regisztrációjának](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) törlésével. 

## <a name="resources"></a>Erőforrások

**Windows rendszerű virtuális gépek**:

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server kiépítése Windows rendszerű virtuális gépen](create-sql-vm-portal.md)
* [Adatbázis migrálása SQL Server Azure-beli virtuális gépen](migrate-to-vm-from-sql-server.md)
* [Magas rendelkezésre állás és vész-helyreállítási SQL Server az Azure-ban Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](performance-guidelines-best-practices.md)
* [Alkalmazási minták és fejlesztési stratégiák az Azure-beli SQL Serverhoz Virtual Machines](application-patterns-development-strategies.md)

**Linux rendszerű virtuális gépek**:

* [Linux rendszerű virtuális gépen lévő SQL Server áttekintése](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server kiépítése Linux rendszerű virtuális gépen](../linux/sql-vm-create-portal-quickstart.md)
* [Gyakori kérdések (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux dokumentáció](/sql/linux/sql-server-linux-overview)
