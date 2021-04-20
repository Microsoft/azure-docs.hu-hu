---
title: SQL Server azure Windows Virtual Machines – gyakori kérdések című témakörben | Microsoft Docs
description: Ez a cikk az Azure-beli virtuális gépeken SQL Server kérdésekre ad választ.
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
ms.openlocfilehash: 02bb1f539369cf72a5d5b6503a3584069589b19e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727347"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure-beli virtuális SQL Server kapcsolatos gyakori kérdések
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Ez a cikk a windowsos Azure SQL Server (virtuális gépek) Virtual Machines a leggyakoribb [kérdésekre ad választ.](https://azure.microsoft.com/services/virtual-machines/sql-server/)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Képek

1. **Milyen SQL Server virtuálisgép-katalógus rendszerképe érhető el?** 

   Az Azure a virtuálisgép-rendszerképeket az összes támogatott fő kiadáshoz SQL Server Windows és Linux összes kiadásán. További információkért tekintse meg a [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) rendszerű virtuálisgép-rendszerképek és [a Linux rendszerű virtuálisgép-rendszerképek teljes listáját.](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)

1. **Frissülnek SQL Server virtuálisgép-katalógus meglévő rendszerképei?**

   A virtuálisgép SQL Server katalógusban található rendszerképek két hónaponként frissülnek a legújabb Windows- és Linux-frissítésekkel. Windows-rendszerképek esetén ez magában foglalja az összes olyan frissítést, amely fontosként van megjelölve a Windows Update, beleértve a fontos SQL Server frissítéseket és szervizcsomagokat is. Linux-rendszerképek esetén ez tartalmazza a legújabb rendszerfrissítéseket. SQL Server kumulatív frissítések kezelése a Linux és a Windows esetén eltérő. Linux rendszeren SQL Server összesítő frissítések is szerepelnek a frissítésben. A Windows rendszerű virtuális gépek azonban jelenleg nem frissülnek a SQL Server windowsos vagy a Windows Server összesítve frissülő frissítésekkel.

1. **Eltávolíthatók SQL Server virtuálisgép-rendszerképek a katalógusból?**

   Igen. Az Azure főverziónként és kiadásonként csak egy rendszerképet tart fenn. Amikor például új SQL Server, az Azure új rendszerképet ad hozzá a szervizcsomag katalógusában. Az SQL Server szervizcsomag lemezképe azonnal el lesz távolítva a Azure Portal. A következő három hónapban azonban továbbra is elérhető a PowerShellből való üzembe építéshez. Három hónap után az előző szervizcsomag-rendszerkép már nem érhető el. Ez az eltávolítási szabályzat akkor is érvényes, ha egy SQL Server verziója nem támogatottá válik, amikor eléri az életciklusa végét.


1. **Üzembe lehet helyezni egy régebbi rendszerképet a SQL Server, amely nem látható a Azure Portal?**

   Igen, a PowerShell használatával. További információ a virtuális gépek PowerShell-SQL Server való üzembe helyezéséről: Virtuális gépek SQL Server üzembe helyezése a [Azure PowerShell.](create-sql-vm-powershell.md)
   
1. **Létre lehet hozni egy általánosított rendszerképet a Azure Marketplace SQL Server rendszerképről, SQL Server VM virtuális gépek üzembe helyezéséhez használjuk?**

   Igen, de ezt követően minden egyes SQL Server VM-t regisztrálnia kell az [SQL IaaS](sql-agent-extension-manually-register-single-vm.md) Agent bővítővel a SQL Server VM portálon való kezeléséhez, valamint olyan funkciókat kell használnia, mint az automatikus javítás és az automatikus biztonsági mentések. A bővítővel való regisztrációkor az egyes licenctípusokat is meg kell adnia az SQL Server VM.

1. **Hogyan azure-SQL Server általánosítani, majd új virtuális gépek üzembe helyezésére használni?**

   Üzembe helyezhet egy Windows Server rendszerű virtuális gépet (anélkül, hogy SQL Server telepítette volna), és az [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) folyamatával általánosíthatja a SQL Server-t az Azure-beli virtuális gépen (Windows) az SQL Server telepítési adathordozóval. A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. Azok az ügyfelek, akik nem Frissítési Garancia a telepítési adathordozót egy olyan Azure Marketplace SQL Server VM rendszerképből, amely a kívánt kiadással rendelkezik.

   Másik megoldásként használhatja az SQL Server egyik rendszerképét Azure Marketplace Azure-beli virtuális SQL Server általánosítani. Vegye figyelembe, hogy a saját rendszerkép létrehozása előtt törölnie kell a következő beállításkulcsot a forrásként kijelölt rendszerképből. Ha ezt elmulasztja, az a telepítő bootstrap SQL Server és/vagy az SQL IaaS-bővítmény meghiúsult állapotának felpuffadását eredményezheti.

   Beállításkulcs elérési útja:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server azure-beli virtuális gépeken lévő alkalmazásokat – beleértve az egyéni általános rendszerképekből üzembe helyezetteket is – regisztrálni kell az [SQL IaaS-ügynök](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) bővítményben a megfelelőségi követelmények teljesítéséhez és az olyan választható funkciók kihasználásához, mint az automatikus javítás és az automatikus biztonsági mentések. A bővítmény lehetővé teszi az egyes felhasználók [licenctípusának](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) SQL Server VM.

1. **Használhatom a saját VHD-SQL Server VM?**

   Igen, de ezt követően regisztrálnia kell az SQL Server VM-t az [SQL IaaS](sql-agent-extension-manually-register-single-vm.md) Agent bővítővel a SQL Server VM portálon való kezeléséhez, valamint olyan funkciókat kell használnia, mint az automatikus javítás és az automatikus biztonsági mentés.

1. **Lehetséges olyan konfigurációkat beállítani, amelyek nem jelennek meg a virtuálisgép-katalógusban (például Windows 2008 R2 + SQL Server 2012)?**

   Nem. A virtuálisgép-katalógus rendszerképe, SQL Server a megadott rendszerképek valamelyikét ki kell választania a Azure Portal [vagy a PowerShell használatával.](create-sql-vm-powershell.md) Ugyanakkor üzembe helyezhet egy Windows rendszerű virtuális gépet, és öntelepítést SQL Server telepíthet rá. Ezután regisztrálnia kell SQL Server VM sql [IaaS-ügynök](sql-agent-extension-manually-register-single-vm.md) bővítményt a SQL Server VM Azure Portal-ban való kezeléséhez, valamint olyan funkciókat kell használnia, mint az automatikus javítás és az automatikus biztonsági mentések. 


## <a name="creation"></a>Létrehozás

1. **Hogyan azure-beli virtuális gépet a SQL Server?**

   A legegyszerűbb módszer egy virtuális gép létrehozása, amely SQL Server. Az Azure-regisztrációval és a portálról SQL Server VM virtuális gép létrehozásával kapcsolatos oktatóanyagért lásd: Virtuális gép SQL Server létrehozása [a Azure Portal.](create-sql-vm-portal.md) Kiválaszthat egy olyan virtuálisgép-rendszerképet, amely másodpercenkénti fizetést SQL Server licencelést használ, vagy használhat olyan rendszerképet, amely lehetővé teszi saját SQL Server használatát. Emellett manuálisan is telepítheti a SQL Server egy szabadon licencelt kiadással (Developer vagy Express) rendelkező virtuális gépre, vagy egy helyszíni licenc újrahasználása által. Regisztrálja a SQL Server VM az [SQL IaaS](sql-agent-extension-manually-register-single-vm.md) Agent bővítvítményben a SQL Server VM kezeléséhez a portálon, valamint használja az olyan funkciókat, mint az automatikus javítás és az automatikus biztonsági mentések. Ha saját licencet hoz, akkor az Azure-ban licenchordozhatóság Frissítési Garancia keretében [kell lennie.](https://azure.microsoft.com/pricing/license-mobility/) További információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](pricing-guidance.md).

1. **Hogyan mirateálható a helyszíni SQL Server a felhőbe?**

   Először hozzon létre egy Azure-beli virtuális gépet egy SQL Server példányával. Ezután a helyszíni adatbázisokat erre a példányra mináljuk. Az adatáttelepítési stratégiákkal kapcsolatban [lásd: SQL Server-adatbázis SQL Server Azure-beli virtuális gépen való áttelepítése.](migrate-to-vm-from-sql-server.md)

## <a name="licensing"></a>Licencek

1. **Hogyan telepíthetem az SQL Server egy licencelt példányát egy Azure-beli virtuális gépen?**

   Ezt háromféleképpen teheti meg. Ha Ön egy Nagyvállalati Szerződés -ügyfél, kiépítheti az egyik [](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)olyan virtuálisgép-rendszerképet, amely támogatja a licenceket, más néven a saját licencét (BYOL). Ha már [Frissítési Garancia,](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)engedélyezheti a Azure Hybrid Benefit [](licensing-model-azure-hybrid-benefit-ahb-change.md) egy meglévő, pay-as-you-go (PAYG) rendszerképen. Másik lehetőségként másolja át az SQL Server telepítési adathordozóját egy Windows Server rendszerű virtuális gépre, majd telepítse az SQL Servert a virtuális gépre. Ne mindenképpen regisztrálja a SQL Server VM [](sql-agent-extension-manually-register-single-vm.md) a bővítményben olyan funkciókhoz, mint a portálkezelés, az automatikus biztonsági mentés és az automatikus javítás. 


1. **Szüksége van egy ügyfélnek SQL Server-licencre (CAL-re) egy Azure-SQL Server futó, használat alapján fizetéses rendszerképhez való Virtual Machines?**

   Nem. Az ügyfeleknek ügyféllicencekre van szükségük, amikor saját licenc használatával SQL Server SA-kiszolgálót/CAL virtuális gépet azure-beli virtuális gépekre. 

1. **Módosíthatok egy virtuális gépet, hogy a saját SQL Server-licencemet használja, ha az a használatalapú fizetéses katalógus egyik rendszerképéből lett létrehozva?**

   Igen. A pay-as-you-go (PAYG) katalógus rendszerképét egyszerűen átválthatja saját licencre (BYOL), ha engedélyezi a [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)  További információ: [How to change the licensing model for a SQL Server VM.](licensing-model-azure-hybrid-benefit-ahb-change.md) Ez a lehetőség jelenleg csak nyilvános és felhőalapú ügyfelek Azure Government érhető el.


1. **A licencelési modell módosítása során az SQL Server le fog állni?**

   Nem. [A licencelési modell módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md) nem igényel állásidőt a SQL Server mivel a módosítás azonnal érvénybe lép, és nem igényli a virtuális gép újraindítását. Ha azonban regisztrálni SQL Server VM SQL IaaS Agent bővítményben, az SQL IaaS-bővítmény előfeltétele, és az  SQL [IaaS-bővítmény](sql-server-iaas-agent-extension-automate-management.md) teljes módban való telepítése újraindítja a SQL Server szolgáltatást. Így ha telepíteni kell az SQL IaaS-bővítményt,  telepítse egyszerűsített módban a  korlátozott működés érdekében, vagy telepítse teljes módban egy karbantartási időszak alatt. Az egyszerűsített módban telepített  SQL IaaS-bővítmény bármikor frissíthető teljes módra, de ehhez újra kell indítani a SQL Server szolgáltatást.  
   
1. **Lehetséges licencelési modelleket váltani egy klasszikus SQL Server VM üzembe helyezett virtuális gépre?**

   Nem. A licencelési modellek módosítása klasszikus virtuális gépeken nem támogatott. A virtuális gépet át is mitálhatja a Azure Resource Manager modellbe, és regisztrálhat az SQL IaaS-ügynök bővítményben. Miután regisztrálta a virtuális gépet az SQL IaaS-ügynök bővítményben, a licencelési modell változásai elérhetők lesznek a virtuális gépen.

1. **Az Azure Portal használható egyszerre több példány ugyanazon a virtuális gépen történő kezelésére?**

   Nem. A portálkezelés az SQL IaaS-ügynök bővítmény által biztosított szolgáltatás, amely az IaaS SQL Server bővítményre támaszkodik. Ezért a bővítményre ugyanazok a korlátozások vonatkoznak, mint a bővítményre. A portálon csak egy alapértelmezett példány vagy egy elnevezett példány kezelhető, feltéve, hogy megfelelően van konfigurálva. További információ ezekről a korlátozásokról: [IaaS SQL Server bővítmény.](sql-server-iaas-agent-extension-automate-management.md) 

1. **A CSP-előfizetések aktiválhatják az Azure Hybrid Benefitet?**

   Igen, az Azure Hybrid Benefit elérhető a CSP-előfizetésekhez. A CSP-ügyfeleknek először üzembe kell helyezniük egy [](licensing-model-azure-hybrid-benefit-ahb-change.md) használati módú rendszerképet, majd módosítaniuk kell a licencelési modellt saját licencre.
   
 
1. **Fizetnem kell az SQL Server licenceléséért egy Azure-beli virtuális gépen, ha csak készenlétben tartom vagy feladatátvételre használom?**

   Ha egy készenléti másodlagos rendelkezésre állási csoporthoz vagy feladatátvevő fürtözött példányhoz ingyenes passzív licenccel rendelkezik, meg kell felelnie a terméklicencelési feltételekben ismertetett összes alábbi [feltételnek:](https://www.microsoft.com/licensing/product-licensing/products)

   1. A licenc [mobilitása a](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) [Frissítési Garancia.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. A passzív SQL Server-példány nem szolgáltat SQL Server-adatokat az ügyfeleknek vagy futtat aktív SQL Server-alapú számítási feladatokat. Csak az elsődleges kiszolgálóval való szinkronizálást végzi, a passzív adatbázist pedig üzemi tartalék állapotban tartja. Ha adatokat szolgál ki, például jelentéseket az aktív SQL Server számítási feladatokat futtató ügyfeleknek, vagy a termék feltételeiben megadottakon kívül bármilyen munkát végeznek, akkor fizetős licenccel rendelkező SQL Server kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbáziskonzisztencia-ellenőrzés vagy CheckDB, teljes biztonsági mentések, tranzakciós naplók biztonsági mentése, valamint az erőforrás-használati adatok monitorozása. Az elsődleges és a hozzá tartozó vészhelyreállítási példányt egyidejűleg is futtathatja 90 naponta a vészhelyreállítási tesztelés rövid időszakaira. 
   1. Az aktív SQL Server licencre a Frissítési Garancia vonatkozik,  és egy passzív másodlagos SQL Server-példányt tesz lehetővé, amely legfeljebb annyi számítási kapacitással rendelkezik, mint a licencelt aktív kiszolgáló. 
   1. A másodlagos SQL Server VM vészhelyreállítási [licencet](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) használ a Azure Portal.
   
1. **Mi tekintendő passzív példánynak?**

   A passzív SQL Server-példány nem szolgáltat SQL Server-adatokat az ügyfeleknek vagy futtat aktív SQL Server-alapú számítási feladatokat. Csak az elsődleges kiszolgálóval való szinkronizálást végzi, a passzív adatbázist pedig üzemi tartalék állapotban tartja. Ha adatokat szolgál ki, például az aktív SQL Server-alapú számítási feladatokat futtató ügyfeleknek készült jelentéseket, vagy a termék használati feltételeiben meghatározottaktól eltérő feladatokat végez, akkor fizetős licenccel rendelkező SQL Server-példánynak kell lennie. A másodlagos példányon a következő tevékenység engedélyezett: adatbáziskonzisztencia-ellenőrzés vagy CheckDB, teljes biztonsági mentések, tranzakciós naplók biztonsági mentése, valamint az erőforrás-használati adatok monitorozása. Az elsődleges és a hozzá tartozó vészhelyreállítási példányt egyidejűleg is futtathatja 90 naponta a vészhelyreállítási tesztelés rövid időszakaira.
   

1. **Milyen forgatókönyvek használhatják ki a vészhelyreállítás (DR) előnyeit?**

   A [licencelési útmutató olyan](https://aka.ms/sql2019licenseguide) forgatókönyveket tartalmaz, amelyekben a vészhelyreállítási előny felhasználható. További információért tekintse meg a termék használati feltételeit, illetve forduljon a licencelési kapcsolattartóhoz vagy a fiókkezelőhöz.

1. **Milyen előfizetések támogatják a vészhelyreállítás (DR) előnyeit?**

   Az olyan átfogó programok, amelyek a Frissítési Garanciával egyenértékű előfizetési jogokat kínálnak rögzített előnyként, támogatják a vészhelyreállítási lehetőséget. Ide tartoznak az alábbiak: de nem kizárólagosan: az Open Value (OV), az Open Value Subscription (OVS), az Nagyvállalati Szerződés (EA), az Nagyvállalati Szerződés Subscription (EAS), valamint a kiszolgáló- és felhőregisztráció (SCE). További információért tekintse meg [a termék használati](https://www.microsoft.com/licensing/product-licensing/products) feltételeit, és további információért beszéljen a licencelési kapcsolattartóval vagy a fiókkezelővel. 

   
 ## <a name="extension"></a>Mellék

1. **A virtuális gép új SQL IaaS-ügynökbővítményben való regisztrálása további költségekkel jár?**

   Nem. Az SQL IaaS-ügynök bővítmény további költségek nélkül SQL Server azure-beli virtuális gépeken való használatának további kezelhetőségét. 

1. **Az SQL IaaS-ügynök bővítmény minden ügyfél számára elérhető?**
 
   Igen, ha a SQL Server VM a nyilvános felhőben, az Resource Manager, és nem a klasszikus modellel lett üzembe stb. Minden más ügyfél regisztrálhat az új SQL IaaS-ügynök bővítményre. A kedvezményt aktiváló Frissítési Garancia [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) aktiválásával azonban csak az Frissítési Garancia kedvezményben részesülő ügyfelek SQL Server VM. [](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 

1. **Mi történik a bővítmény (_Microsoft.SqlVirtualMachine_) erőforrással, ha a virtuális gép erőforrását áthelyezik vagy elejtik?** 

   A Microsoft.Compute/VirtualMachine erőforrás eldobott vagy áthelyezett példánya után a társított Microsoft.SqlVirtualMachine erőforrás értesítést kap a művelet aszinkron replikálása érdekében.

1. **Mi történik a virtuális gépekkel, ha a bővítmény (_Microsoft.SqlVirtualMachine_) erőforrása el van dobva?**

    A Microsoft.Compute/VirtualMachine erőforrást nem befolyásolja a Microsoft.SqlVirtualMachine erőforrás eldobása. A licencelési módosítások azonban alapértelmezés szerint az eredeti rendszerképforrásra fognak visszaállítva. 

1. **Regisztrálhat önkiszolgálóan telepített virtuális SQL Server az SQL IaaS-ügynök bővítménysel?**

    Igen. Ha a SQL Server-t a saját adathordozóról telepítette, és telepítette az SQL IaaS-bővítményt, regisztrálhatja az SQL Server VM-t a bővítővel az SQL IaaS-bővítmény által nyújtott kezelhetőségi előnyök érdekében.    


## <a name="administration"></a>Felügyelet

1. **Telepíthetek egy második példánypéldányt SQL Server virtuális gépre? Módosít tudom az alapértelmezett példány telepített funkcióit?**

   Igen. A SQL Server adathordozó a C meghajtó egyik **mappájában** található. Futtassa **Setup.exe** az új SQL Server példányok hozzáadásához vagy a gépre telepített SQL Server más telepített szolgáltatásainak a telepítéshez. Vegye figyelembe, hogy egyes funkciók, például az automatikus biztonsági mentés, az automatikus javítás és az Azure Key Vault-integráció csak az alapértelmezett példányon vagy egy megfelelően konfigurált megnevezett példányon működnek (lásd a 3. kérdést). Az [Frissítési Garancia](licensing-model-azure-hybrid-benefit-ahb-change.md) Azure Hybrid Benefit vagy használat alapján történő  licencelési modellel használó ügyfelek további licencelési költségek nélkül telepíthet több SQL Server-példányt a virtuális gépre. További SQL Server példányok túlterhelést okozhatnak a rendszer erőforrásainak, hacsak nincs megfelelően konfigurálva. 

1. **Mi a virtuális gépen található példányok maximális száma?**
   SQL Server 2012 és 2019 SQL Server között [50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) példányt támogat egy önálló kiszolgálón. Ez a korlát az Azure helyszíni környezetétől függetlenül ugyanaz. Az [ajánlott eljárásokból](performance-guidelines-best-practices.md#multiple-instances) megtudhatja, hogyan készítheti elő jobban a környezetet. 

1. **Eltávolíthatom az SQL Server alapértelmezett példányát?**

   Igen, de bizonyos szempontokat figyelembe kell venni. Először is SQL Server társított számlázás továbbra is előfordulhat a virtuális gép licencmodelljétől függően. Másodszor, ahogy az előző válaszban is, vannak olyan funkciók, amelyek az [IaaS-ügynökbővítményre SQL Server támaszkodnak.](sql-server-iaas-agent-extension-automate-management.md) Ha az IaaS-bővítmény eltávolítása nélkül távolítja el az alapértelmezett példányt, a bővítmény továbbra is az alapértelmezett példányt keresse, és eseménynapló-hibákat okozhat. Ezek a hibák a következő két forrásból származóak: **Microsoft SQL Server hitelesítő** adatok kezelése és **Microsoft SQL Server IaaS-ügynök.** Az egyik hiba a következőhöz hasonló lehet:

      Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el.

   Ha úgy dönt, hogy eltávolítja az alapértelmezett példányt, akkor az [IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md) ügynökbővítményt is távolítsa el. 

1. **Használhatom a névvel SQL Server IaaS-bővítményt?**
   
   Igen, ha a megnevezett példány az egyetlen példány a SQL Server, és az eredeti alapértelmezett példány megfelelően [lett eltávolítva.](sql-server-iaas-agent-extension-automate-management.md#named-instance-support) Ha nincs alapértelmezett példány, és több megnevezett példány van egyetlen SQL Server VM, a SQL Server IaaS-ügynökbővítmény telepítése sikertelen lesz.  

1. **Eltávolítható a SQL Server és a társított licenc számlázása egy SQL Server VM?**

   Igen, de további lépéseket kell tennie, hogy elkerülje a szolgáltatáspéldányért SQL Server [díjszabási útmutatóban leírtak szerint.](pricing-guidance.md) Ha teljesen el szeretné távolítani a SQL Server-példányt, anélkül is áttelepíthet egy másik Azure-beli virtuális gépre, hogy előre telepítve lenne SQL Server virtuális gépre, és törölheti az aktuális SQL Server VM. Ha meg szeretné tartani a virtuális gépet, de le szeretné állítani SQL Server számlázását, kövesse az alábbi lépéseket: 

   1. Szükség esetén minden adatról biztonsági mentése, beleértve a rendszeradatbázisokat is. 
   1. Távolítsa SQL Server teljes egészében, beleértve az SQL IaaS-bővítményt is (ha van ilyen).
   1. Telepítse az [ingyenes SQL Express kiadást.](https://www.microsoft.com/sql-server/sql-server-downloads)
   1. Regisztráljon az SQL IaaS-ügynök bővítményre egyszerűsített [módban.](sql-agent-extension-manually-register-single-vm.md)
   1. [A számlázást a](change-sql-server-edition.md#change-edition-in-portal) SQL Server [expressz Azure Portal](https://portal.azure.com) módosíthatja.  
   1. (nem kötelező) Tiltsa le az Express SQL Server szolgáltatást a szolgáltatás indításának letiltásával. 

1. **Az Azure Portal használható egyszerre több példány ugyanazon a virtuális gépen történő kezelésére?**

   Nem. A portálkezelést az SQL IaaS-ügynök bővítmény biztosítja, amely az IaaS-ügynök SQL Server alapul. Ezért a bővítményre ugyanazok a korlátozások vonatkoznak, mint a bővítményre. A portál csak egy alapértelmezett példányt vagy egy elnevezett példányt kezelhet, ha az megfelelően van konfigurálva. További információ: [IaaS SQL Server bővítmény](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Frissítés és javítás

1. **Hogyan azure-beli virtuális gép SQL Server verziójára/kiadására?**

   Az ügyfelek az SQL Server kívánt verzióját vagy kiadását tartalmazó telepítési adathordozóval módosíthatják az SQL Server verzióját/kiadását. A kiadás módosítása után az Azure Portalon módosítsa a virtuális gép kiadási tulajdonságát, hogy a virtuális gép számlázása a pontos adatokkal történjen. További információkért lásd egy új kiadás [kiadásának SQL Server VM.](change-sql-server-edition.md) Az SQL Server különböző verziói esetében nincs különbség a számlázásban, így az SQL Server verziójának módosítása után nincs szükség további műveletre.

1. **Hol szerezhetem be az SQL Server kiadásának vagy verziójának módosításához szükséges telepítési adathordozót?**

   A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. A nem Frissítési Garancia ügyfelek a kívánt kiadással Azure Marketplace SQL Server VM rendszerkép telepítési adathordozóját.
   
1. **Hogyan történik a frissítések és a szervizcsomagok alkalmazása SQL Server-alapú virtuális gépeken?**

   A virtuális gépek lehetővé teszik a gazdagép vezérlését, például a frissítések alkalmazási időpontjának és módjának vezérlését. Az operációs rendszer esetében manuálisan alkalmazhatja a Windows-frissítéseket, vagy engedélyezheti az [Automatikus javítás](automated-patching.md) nevű ütemezési szolgáltatást. Az Automatikus javítás minden fontosként megjelölt frissítést telepít, így az e kategóriába eső SQL Server-frissítéseket is. Az SQL Server egyéb nem kötelező frissítéseit manuálisan kell telepíteni.

1. **Frissíthető a SQL Server 2008/2008 R2-példány, miután regisztráltam az SQL IaaS-ügynök bővítményben?**

   Ha az operációs rendszer Windows Server 2008 R2 vagy újabb, igen. Bármilyen telepítési adathordozóval frissítheti a SQL Server verzióját és kiadását, majd az [SQL IaaS-bővítmény](sql-server-iaas-agent-extension-automate-management.md#management-modes)üzemmódját ) ügynök nélkül teljes verzióra _frissítheti._  Ez hozzáférést biztosít az SQL IaaS-bővítmény összes előnyéhez, például a portál kezelhetőségéhez, az automatikus biztonsági mentéshez és az automatikus javításhoz. Ha az operációs rendszer verziója Windows Server 2008, csak a NoAgent mód támogatott. 

1. **Hogyan szerezhetek be ingyenes kibővített biztonsági frissítéseket a megszűnt támogatottságú SQL Server 2008- és SQL Server 2008 R2-példányokhoz?**

   Ingyenes kiterjesztett [biztonsági frissítéseket](sql-server-2008-extend-end-of-support.md) kaphat, ha a SQL Server egy Azure-beli virtuális gépre. További információért tekintse meg a [támogatottság megszűnésére vonatkozó lehetőségeket](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Általános kérdések

1. **Az SQL Server fürtpéldányok (FCI) támogatottak az Azure-beli virtuális gépeken?**

   Igen. A feladatátvevőfürt-példányokat prémium szintű [fájlmegosztások (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) vagy közvetlen tárolóhelyek [(S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) használatával telepítheti a tárolóalrendszerhez. A prémium fájlmegosztások olyan IOPS- és átviteli kapacitásokat biztosítanak, amelyek számos számítási feladat igényeinek megfelelnek. Az I/O-igényes számítási feladatokhoz érdemes lehet a közvetlen tárolóhelyeket használni a manged premium vagy ultralemezek alapján. Másik lehetőségként külső fürtözési vagy tárolási megoldásokat is használhat az Azure-beli virtuális SQL Server magas rendelkezésre állásával [és vészhelyreállításával](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)Virtual Machines.

   > [!IMPORTANT]
   > Jelenleg az  [IaaS SQL Server bővítmény](sql-server-iaas-agent-extension-automate-management.md) teljes körű támogatása nem támogatott SQL Server FCI-hez az Azure-ban. Javasoljuk, hogy  távolítsa el a teljes bővítményt az FCI-t résztvevő virtuális gépekről, és telepítse a bővítményt egyszerűsített _módban._ Ez a bővítmény támogatja az olyan szolgáltatásokat, mint az automatikus biztonsági mentés és a javítás, valamint a portál néhány SQL Server. Ezek a funkciók nem fognak működni SQL Server virtuális gépeken a _teljes_ ügynök eltávolítása után.

1. **Mi a különbség a virtuális SQL Server és a SQL Database között?**

   Elméleti szinten a SQL Server azure-beli virtuális gépeken való futtatása nem tér el a távoli SQL Server virtuális gépeken való futtatástól. Ezzel szemben [a Azure SQL Database](../../database/sql-database-paas-overview.md) szolgáltatásként kínál adatbázist. A SQL Database nincs hozzáférése az adatbázisokat kezelő gépekhez. A teljes összehasonlítást lásd: Felhőalapú adatbázis SQL Server [lehetőség: Azure SQL (PaaS) adatbázis vagy SQL Server Azure-beli virtuális gépeken (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Hogyan SQL Data-eszközöket az Azure-beli virtuális gépre?**

    Töltse le és telepítse az SQL Data-eszközöket a [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013 ről.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Támogatottak az MSDTC-vel elosztott tranzakciók SQL Server virtuális gépeken?**
   
    Igen. A helyi DTC a 2016 SP2 SQL Server és annál nagyobb verziók esetén támogatott. Az always On rendelkezésre állási csoportok használata során azonban az alkalmazásokat tesztelni kell, mivel a feladatátvétel során vitelt tranzakciók meghiúsulnak, és újra kell őket tesztelni. A fürtözött DTC a Windows Server 2019-től kezdve érhető el. 
    
1. **A Azure SQL áthelyezik vagy tárolják az ügyféladatokat a régión belül?**

   Nem. Valójában a Azure SQL és az SQL IaaS-ügynök bővítménye nem tárol ügyféladatokat.

## <a name="sql-server-iaas-agent-extension"></a>Az SQL Server IaaS-ügynök bővítménye

1. **Regisztrálnom kell a SQL Server VM kiépítve egy SQL Server rendszerképből a Azure Marketplace?**

   Nem. A Microsoft automatikusan regisztrálja a virtuális gépek SQL Server rendszerképeiből a Azure Marketplace. A bővítmény regisztrációja csak akkor szükséges, ha a virtuális gép nem a SQL Server rendszerképből lett kiépítve a Azure Marketplace, SQL Server öntelepítve lett. 

1. **Az SQL IaaS-ügynök bővítmény minden ügyfél számára elérhető?** 

   Igen. Az ügyfeleknek regisztrálniuk kell SQL Server virtuális gépeiket a bővítővel, ha nem az Azure Marketplace-ból származó SQL Server-rendszerképet használják, hanem öntelepített SQL Server-t, vagy ha az egyéni VHD-jüket hozták létre. A különböző típusú előfizetések (Közvetlen, Nagyvállalati Szerződés és Felhőszolgáltató) tulajdonában lévő virtuális gépek regisztrálnak az SQL IaaS-ügynök bővítményével.

1. **Mi az alapértelmezett felügyeleti mód az SQL IaaS-ügynök bővítményével való regisztrációkor?**

   Az SQL IaaS-ügynök bővítményével való regisztrációkor az alapértelmezett felügyeleti *mód egyszerű.* Ha a SQL Server-kezelési tulajdonság nincs beállítva a bővítővel való regisztrációkor, a mód egyszerűsített módként lesz beállítva, és a SQL Server szolgáltatás nem indul újra. Javasoljuk, hogy először regisztráljon az SQL IaaS-ügynök bővítményre egyszerűsített módban, majd frissítsen teljes verzióra egy karbantartási időszak alatt. Hasonlóképpen, az alapértelmezett felügyelet az automatikus regisztrációs funkció használata esetén [is](sql-agent-extension-automatic-registration-all-vms.md)egyszerű.

1. **Mik az SQL IaaS-ügynök bővítményben való regisztráció előfeltételei?**

   Az SQL IaaS-ügynökbővítményben való regisztrációnak nincs előfeltétele, csak SQL Server kell telepítenie a virtuális gépre. Vegye figyelembe, hogy ha az SQL IaaS-ügynökbővítmény teljes módban van telepítve, a SQL Server szolgáltatás újraindul, ezért ezt karbantartási időszakban javasoljuk.

1. **Az SQL IaaS-ügynökbővítményben való regisztráció ügynököt telepít a virtuális gépemre?**

   Igen, az SQL IaaS-ügynök bővítményre való teljes körű kezelhetőségi módban való regisztráció ügynököt telepít a virtuális gépre. Az egyszerűsített vagy NoAgent módban való regisztráció nem. 

   Az SQL IaaS-ügynök bővítmény egyszerűsített módban való regisztrálása csak  az SQL IaaS-ügynök bővítmény bináris fájlját másolja a virtuális gépre, az ügynököt nem telepíti. A rendszer ezeket a bináris fájlokat használja az ügynök telepítéséhez, amikor a felügyeleti mód teljes verzióra van frissítve.


1. **Az SQL IaaS-ügynökbővítménysel való regisztrációt a SQL Server újraindítom?**

   Ez a regisztráció során megadott módtól függ. Ha egyszerűsített vagy NoAgent mód van megadva, akkor a SQL Server szolgáltatás nem indul újra. Ha azonban a felügyeleti módot teljesként adja meg, a SQL Server szolgáltatás újraindul. Az automatikus regisztráció funkció kis SQL Server regisztrálja a virtuális gépeket, kivéve, ha a Windows Server 2008-as verziója, amely esetben a SQL Server VM noAgent módban lesz regisztrálva. 

1. **Mi a különbség az egyszerűsített és a NoAgent felügyeleti mód között az SQL IaaS-ügynök bővítményével való regisztrációkor?** 

   A NoAgent felügyeleti mód az egyetlen elérhető felügyeleti mód a SQL Server 2008-as és SQL Server 2008 R2-es verzióhoz Windows Server 2008 rendszeren. A Windows Server minden újabb verziója esetében a két rendelkezésre álló kezelhetőségi mód kis méretű és teljes. 

   A NoAgent mód SQL Server az ügyfél által beállított verzió- és kiadástulajdonságokat. Az egyszerűsített mód lekérdezi a virtuális gépet, hogy megtalálja a virtuális gép SQL Server kiadását.

1. **Regisztrálok az SQL IaaS-ügynök bővítményben a licenctípus megadása SQL Server nélkül?**

   Nem. Az SQL Server licenctípus nem választható tulajdonság az SQL IaaS-ügynök bővítményben való regisztrációkor. Az SQL IaaS-ügynökbővítményre való regisztrációkor a SQL Server-licenctípust "pay-as-you-go" vagy "Azure Hybrid Benefit" típusúként kell beállítania minden kezelhetőségi módban (NoAgent, lightweight és full). Ha az alkalmazás ingyenes SQL Server (például Fejlesztői vagy Próbaverzió) van telepítve, regisztrálnia kell a fizetéses licencelésre. Azure Hybrid Benefit csak a fizetős verziókhoz érhető el, például az Enterprise SQL Server Standard kiadásokhoz.

1. **Frissíthető a SQL Server IaaS-bővítmény NoAgent módról teljes módra?**

   Nem. A kezelhetőségi mód teljes vagy egyszerűsített módra való frissítése nem érhető el a NoAgent módban. Ez a Windows Server 2008 technikai korlátozása. Először frissítenie kell az operációs rendszert a Windows Server 2008 R2 vagy nagyobb verzióra, majd teljes körű felügyeleti módra kell frissítenie. 

1. **Frissíthető a SQL Server IaaS-bővítmény egyszerűsített módúról teljes módra?**

   Igen. A kezelhetőségi mód egyszerűsítettről teljes verzióra való frissítése a Azure PowerShell vagy a Azure Portal. Ez elindítja a SQL Server újraindítását.

1. **Vissza lehet váltani a SQL Server IaaS-bővítményt teljes módról NoAgent vagy egyszerűsített felügyeleti módra?**

   Nem. Az IaaS SQL Server kezelhetőségi módjának visszalépése nem támogatott. A kezelhetőségi mód nem váltható vissza teljes módról egyszerűsített vagy NoAgent módra, és nem lehet az egyszerűsített módról NoAgent módra visszaminősülni. 

   Ha módosítania kell a kezelhetőségi módot a teljes kezelhetőségről, akkor az SQL IaaS-ügynök bővítményből az SQL Server VM SQL IaaS-ügynök bővítményből való regisztrációját az SQL virtuális gép erőforrásának eldobása és az SQL Server VM sql IaaS-ügynökbővítményben való újra regisztrálása egy másik felügyeleti módban. [](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) 

1. **Regisztrálok az SQL IaaS-ügynök bővítményre a Azure Portal?**

   Nem. Az SQL IaaS-ügynökbővítményben való regisztráció nem érhető el a Azure Portal. Az SQL IaaS-ügynökbővítményben való regisztráció csak az Azure CLI vagy a Azure PowerShell. 

1. **Regisztrálok egy virtuális gépet az SQL IaaS-ügynök bővítményben a SQL Server előtt?**

   Nem. A virtuális gépnek legalább egy adatbázismotor SQL Server példánysal kell lennie ahhoz, hogy sikeresen regisztráljon az SQL IaaS-ügynök bővítményben. Ha nincs SQL Server példány a virtuális gépen, az új Microsoft.SqlVirtualMachine erőforrás hibás állapotban lesz.

1. **Regisztrálható virtuális gép az SQL IaaS-ügynök bővítményben, ha több SQL Server van?**

   Igen, feltéve, hogy a virtuális gépen van egy alapértelmezett példány. Az SQL IaaS-ügynök bővítmény csak egy SQL Server (adatbázismotor) példányt regisztrál. Az SQL IaaS-ügynök bővítmény regisztrálja az alapértelmezett SQL Server példány több példány esetén.

1. **Regisztrálok egy SQL Server feladatátvevőfürt-példányt az SQL IaaS-ügynök bővítmény használatával?**

   Igen. SQL Server Azure-beli virtuális gépen található feladatátvevőfürt-példányok regisztrálhatóak az SQL IaaS-ügynök bővítményéhez egyszerűsített módban. A SQL Server fürtpéldányok azonban nem frissíthetőek teljes körű kezelhetőségi módra.

1. **Regisztrálható a virtuális gépem az SQL IaaS-ügynök bővítménysel, ha always On rendelkezésre állási csoport van konfigurálva?**

   Igen. Ha Always On rendelkezésre állási csoport konfigurációjában vesz részt, nincs korlátozás arra, hogy egy Azure-beli virtuális gépen regisztráljon egy SQL Server példányt az SQL IaaS-ügynök bővítményével.

1. **Milyen költséggel jár az SQL IaaS-ügynök bővítményben való regisztráció vagy a teljes körű kezelhetőségi módra való frissítés?**

   Nincsenek. Az SQL IaaS-ügynök bővítményben vagy a három kezelhetőségi mód bármelyikének használatával való regisztrációnak nincs díja. A SQL Server VM kezelése a bővítvével teljesen ingyenes. 

1. **Milyen hatással van a különböző kezelhetőségi módok használata a teljesítményre?**

   A *NoAgent* és az egyszerűsített kezelhetőségi módok használata *nincs* hatással. A teljes kezelhetőségi  mód csak minimális hatással van az operációs rendszerre telepített két szolgáltatásra. Ezek a feladatkezelőn keresztül figyelhetőek, és a beépített Windows-szolgáltatások konzolján láthatók. 

   A két szolgáltatásnév a következő:
   - `SqlIaaSExtensionQuery` (Megjelenített név – `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Megjelenített név – `Microsoft SQL Server IaaS Agent` )

1. **Hogyan távolítsa el a bővítményt?**

   Távolítsa el a [bővítményt](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) úgy, hogy törli a SQL Server VM az SQL IaaS-ügynök bővítményből. 

## <a name="resources"></a>Források

**Windows rendszerű virtuális gépek:**

* [A windowsos SQL Server gépeken történő telepítés áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Üzembe SQL Server windowsos virtuális gépen](create-sql-vm-portal.md)
* [Adatbázis áttelepítése SQL Server Azure-beli virtuális gépen](migrate-to-vm-from-sql-server.md)
* [Magas rendelkezésre állás és vészhelyreállítás azure SQL Server Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Teljesítménnyel kapcsolatos ajánlott eljárások SQL Server Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Alkalmazásminták és fejlesztési stratégiák SQL Server Azure-Virtual Machines](application-patterns-development-strategies.md)

**Linux rendszerű virtuális gépek:**

* [Linux SQL Server virtuális gépeken való telepítésének áttekintése](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Üzembe SQL Server linuxos virtuális gépen](../linux/sql-vm-create-portal-quickstart.md)
* [Gyakori kérdések (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux dokumentációja](/sql/linux/sql-server-linux-overview)
