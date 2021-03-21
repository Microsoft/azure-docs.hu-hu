---
title: Windows rendszerű virtuális gép kiépítése a Azure Portal
description: Ez az útmutató azokat a lehetőségeket ismerteti, amelyekkel a Azure Portal használatával kiépítheti SQL Server a Windows rendszerű virtuális gépeken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f0b7e04becd42180df3393d7036294c1629cdc21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499492"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Windows rendszerű virtuális gép kiépítése a Azure Portal használatával SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez az útmutató azokat a lehetőségeket ismerteti, amelyekkel a Azure Portal használatával kiépíthető a SQL Server egy Windows rendszerű virtuális gépen (VM). Ez a cikk a [SQL Server VM](sql-vm-create-portal-quickstart.md)gyors üzembe helyezésének több konfigurációs beállítását mutatja be, amely mélyebben összpontosít egyetlen konfigurációra. 

Az útmutató segítségével saját SQL Server VM hozhat létre. Vagy használja hivatkozásként a Azure Portal elérhető lehetőségeire.

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server virtuálisgép-katalógus rendszerképei

SQL Server virtuális gép létrehozásakor kiválaszthatja a virtuális gépek katalógusában található számos előre konfigurált rendszerkép egyikét. A következő lépések bemutatják, hogyan választhatja ki a SQL Server 2017 rendszerképek egyikét.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás** lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe. 

   Az **Azure SQL** melletti csillagot is kiválaszthatja kedvencként való mentéshez, és a bal oldali navigációs elemként való hozzáadásához. 

1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A **Részletek megjelenítése** lehetőség kiválasztásával további információkat is megtekintheti. 
1. Írja be a *2017* értéket az **SQL Virtual machines** csempe SQL Server képkeresés mezőjébe, majd válassza az **ingyenes SQL Server licenc: SQL Server 2017 Developer on Windows Server 2016** elemet a legördülő menüből. 

   ![SQL VM-rendszerkép kiválasztása](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > A fejlesztői kiadás ebben a cikkben van használatban, mivel ez a SQL Server teljes funkcionalitású, ingyenes kiadása a fejlesztési teszteléshez. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. Azonban szabadon választhatja ki az útmutatóban használni kívánt rendszerképeket. Az elérhető lemezképek leírását a [SQL Server Windows Virtual Machines áttekintésében](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)találhatja meg.

   > [!TIP]
   > A SQL Server licencelési költségei a létrehozott virtuális gép másodpercenkénti díjszabásában vannak beépítve, és a kiadás és a magok alapján változnak. SQL Server Developer kiadás azonban ingyenes fejlesztési és tesztelési célokra, nem pedig éles környezetben. Emellett az SQL Express ingyenesen használható a könnyű számítási feladatokhoz (kevesebb, mint 1 GB memória, kevesebb mint 10 GB tárterület). Saját licencét (BYOL) is megadhatja, és csak a virtuális gép számára kell fizetnie. Az ilyen rendszerképek nevei {BYOL} előtagot kapnak. 
   >
   > A lehetőségekkel kapcsolatos további információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](pricing-guidance.md).


1. Válassza a **Létrehozás** lehetőséget.


## <a name="1-configure-basic-settings"></a>1. az alapszintű beállítások konfigurálása

Az **alapvető beállítások** lapon adja meg a következő információkat:

* A **projekt részletei** területen ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. 
* Az **erőforráscsoport** szakaszban vagy válasszon ki egy meglévő erőforráscsoportot a listából, vagy válassza az **új létrehozása** lehetőséget az új erőforráscsoport létrehozásához. Az erőforráscsoportok az Azure-ban egymáshoz kapcsolódó erőforrásokból (virtuális gépekből, tárfiókokból, virtuális hálózatokból stb.) álló gyűjtemények. 

  ![Előfizetés](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Az Azure szolgáltatásban futó SQL Server üzemelő példányok tesztelésekor vagy a velük való megismerkedéskor érdemes egy új erőforráscsoportot használni. A tesztelés befejezése után törölje az erőforráscsoportot a virtuális gép és az erőforráscsoporthoz társított összes erőforrás automatikus törléséhez. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../../../active-directory-b2c/overview.md)).


* A **példány részletei** területen:

    1. Adja meg a **virtuális gép egyedi nevét**.  
    1. Válasszon egy helyet a **régió** számára. 
    1. Ebben az útmutatóban hagyja, hogy a **rendelkezésre állási beállítások** ne legyenek _infrastruktúra-redundancia_ megadása. További információ a rendelkezésre állási lehetőségekről: [rendelkezésre állás](../../../virtual-machines/availability.md). 
    1. A **rendszerkép** listában válassza az _ingyenes SQL Server licenc: SQL Server 2017 Developer elemet a Windows Server 2016-on_.  
    1. A virtuális gép **méretének** **módosításához** válassza az **a2 alapszintű** ajánlat lehetőséget. Ne felejtse el megtisztítani az erőforrásokat, ha elkészült velük, hogy elkerülje a váratlan költségeket. Az éles számítási feladatok esetében ajánlott gépméretekért és -konfigurációkért tekintse meg [az SQL Server teljesítményének Azure Virtual Machines szolgáltatásbeli növelésével kapcsolatos ajánlott eljárásokat](performance-guidelines-best-practices.md).

    ![Példány adatai](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> A **Méret kiválasztása** ablakban megjelenő, becsült havi költségek nem tartalmazzák az SQL Server licencelési költségeit. Ez a becslés a virtuális gép költsége. A SQL Server expressz és fejlesztői kiadásai esetében ez a becsült összeg a teljes becsült díj. Más kiadások esetében tekintse meg a [Windows rendszerű virtuális gépek árképzését ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), és válassza ki az SQL Server megfelelő kiadását. Tekintse meg a SQL Server Azure-beli [virtuális gépekkel és-méretekkel kapcsolatos](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) [díjszabási útmutatót](pricing-guidance.md) is.

* A **rendszergazdai fiók** területen adja meg a felhasználónevet és a jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](./media/create-sql-vm-portal/basics-administrator-account.png)

* A **bejövő portszabályok** területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből. 

   ![Bejövőport-szabályok](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. választható funkciók konfigurálása

### <a name="disks"></a>Lemezek

A **lemezek** lapon adja meg a lemez beállításait. 

* Az **operációsrendszer-lemez típusa** területen válassza ki az operációs rendszerhez használni kívánt lemez típusát a legördülő menüből. A prémium üzemi rendszerek esetében ajánlott, de alapszintű virtuális gép esetén nem érhető el. Prémium SSD használatához módosítsa a virtuális gép méretét. 
* A **speciális** területen válassza az **igen** lehetőséget a **Managed Disks** használata elemnél.

   > [!NOTE]
   > A Microsoft a Managed Disks használatát javasolja az SQL Server esetében. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információ: az [Azure Managed Disks áttekintése](../../../virtual-machines/managed-disks-overview.md). A felügyelt lemezek a rendelkezésre állási csoportokban való használatával kapcsolatos részletekért lásd: [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel](../../../virtual-machines/availability.md).

![Az SQL virtuális gép lemezének beállításai](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Hálózatkezelés

A **hálózatkezelés** lapon konfigurálja a hálózati beállításokat. 

* Hozzon létre egy új **virtuális hálózatot** , vagy használjon egy meglévő virtuális hálózatot a SQL Server VM számára. Jelölje ki az **alhálózatot** is. 

* A **NIC hálózati biztonsági csoport** területen válassza az alapszintű biztonsági csoport vagy a speciális biztonsági csoport lehetőséget. Az alapszintű beállítás választása lehetővé teszi, hogy a SQL Server VM bejövő portjait válassza, amelyek ugyanazok az értékek, amelyek az **alapszintű** lapon vannak konfigurálva. A speciális beállítás kiválasztásával választhat egy meglévő hálózati biztonsági csoportot, vagy létrehozhat egy újat. 

* Más módosításokat is végezhet a hálózati beállításokon, vagy megtarthatja az alapértelmezett értékeket.

![Az SQL VM hálózatkezelési beállításai](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Figyelés

A **figyelés** lapon konfigurálja a figyelést és az automatikus leállítást. 

* Az Azure alapértelmezés szerint a virtuális gép számára kijelölt Storage-fiókkal teszi lehetővé a **rendszerindítási diagnosztika** használatát. Ezen a lapon módosíthatja ezeket a beállításokat, és engedélyezheti az **operációs rendszer vendégének diagnosztikai** szolgáltatásait. 
* Ezen a lapon engedélyezheti a **rendszerhez rendelt felügyelt identitást** és az **automatikus leállítást** is. 

![SQL VM-felügyeleti beállítások](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. SQL Server beállítások konfigurálása

A **SQL Server beállítások** lapon adja meg a SQL Server adott beállításait és optimalizálási lehetőségeit. A SQL Server következő beállításait konfigurálhatja:

- [Kapcsolódás](#connectivity)
- [Hitelesítés](#authentication)
- [Azure Key Vault integráció](#azure-key-vault-integration)
- [Tároló konfigurálása](#storage-configuration)
- [Automatikus javítás](#automated-patching)
- [Automatikus biztonsági mentés](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Kapcsolat

Az **SQL kapcsolatok** alatt adja meg, milyen típusú hozzáférést szeretne az ezen a virtuális gépen futó SQL Server-példányhoz. A jelen útmutatóban válassza a **nyilvános (Internet)** lehetőséget, hogy lehetővé tegye a kapcsolódást az interneten található gépekről vagy szolgáltatásokból SQL Server. Ha ez a beállítás be van jelölve, az Azure automatikusan konfigurálja a tűzfalat és a hálózati biztonsági csoportot, hogy engedélyezze a forgalmat a kiválasztott porton.

> [!TIP]
> Alapértelmezés szerint az SQL Server a jól ismert **1433-as** portot figyeli. A nagyobb biztonság érdekében az előző párbeszédpanelen módosítsa a portot egy nem alapértelmezett, például az 1401-es portra. Ha módosítja a portot, az adott portot bármely ügyféleszközök, például a SQL Server Management Studio (SSMS) használatával kell összekötnie.

![SQL virtuális gép biztonsága](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Ahhoz, hogy az SQL Serverhez az interneten keresztül csatlakozni lehessen, engedélyeznie kell az SQL Server-hitelesítést is, aminek ismertetését a következő szakaszban olvashatja.

Ha nem szeretné engedélyezni az adatbázis-alrendszerhez az interneten keresztül való csatlakozást, válassza az alábbi lehetőségek valamelyikét:

* A **Helyi (csak virtuális gépen belül)** beállítással az SQL Serverhez csak a virtuális gépen belülről lehet csatlakozni.
* A **Magánjellegű (virtuális hálózaton belül)** beállítással az SQL Serverhez az azonos virtuális hálózaton található gépek és szolgáltatások csatlakozhatnak.

Általánosságban elmondható, hogy a forgatókönyv által lehetővé tett legszigorúbb kapcsolódási korlátozás kiválasztásával növelhető a biztonság. Az összes lehetőség azonban a hálózati biztonsági csoport (NSG) szabályai és az SQL/Windows-hitelesítés használatával biztosítható. A virtuális gép létrehozása után szerkesztheti a NSG. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](security-considerations-best-practices.md).

### <a name="authentication"></a>Hitelesítés

Ha SQL Server hitelesítésre van szüksége, az **SQL Server beállítások** lapon válassza az **SQL-hitelesítés** alatt az **Engedélyezés** lehetőséget.

![SQL Server-hitelesítés](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Ha az interneten keresztül szeretné elérni SQL Server (a nyilvános kapcsolódási lehetőséggel), akkor itt kell engedélyeznie az SQL-hitelesítést. Az SQL Serverhoz való nyilvános hozzáféréshez SQL-hitelesítés szükséges.

Ha engedélyezi az SQL Server-hitelesítést, adjon meg egy **bejelentkezési nevet** és egy **jelszót**. Ez a bejelentkezési név SQL Server hitelesítési Bejelentkezési azonosítóként van konfigurálva, és tagja a **sysadmin** rögzített kiszolgálói szerepkörnek. További információkat a hitelesítési módokról [a hitelesítési mód kiválasztását leíró](/sql/relational-databases/security/choose-an-authentication-mode) cikkben talál.

Ha nem szeretné engedélyezni SQL Server hitelesítés használatát, a virtuális gépen a helyi rendszergazdai fiókkal csatlakozhat a SQL Server-példányhoz.

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integráció

Ha biztonsági titkot szeretne tárolni az Azure-ban a titkosításhoz, válassza a **SQL Server beállításokat**, majd görgessen le az  **Azure Key Vault-integráció** elemre. Válassza az **Engedélyezés** lehetőséget, majd adja meg a kért információkat. 

![Azure Key Vault-integráció](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

A következő táblázat felsorolja a Azure Key Vault (AKV) integrációjának konfigurálásához szükséges paramétereket.

| PARAMÉTER | LEÍRÁS | PÉLDA |
| --- | --- | --- |
| **Key Vault URL** |A Key Vault helye. |`https://contosokeyvault.vault.azure.net/` |
| **Egyszerű név** |Az Azure Active Directory szolgáltatás egyszerű neve. Ezt a nevet ügyfél-azonosítónak is hívják. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Egyszerű titok** |Az Azure Active Directory szolgáltatás egyszerű titka. Ezt a titkot ügyféltitoknak is hívják. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Hitelesítő adat neve** |**Hitelesítő adat neve**: a AKV-integráció egy hitelesítő adatot hoz létre SQL Serveron belül, és lehetővé teszi a virtuális gép számára a kulcstartó elérését. Válasszon egy nevet ennek a hitelesítő adatnak. |`mycred1` |

További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](azure-key-vault-integration-configure.md) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken.

### <a name="storage-configuration"></a>Tároló konfigurálása

A **SQL Server beállítások** lap **tárolási konfiguráció** területén válassza a **konfiguráció módosítása** lehetőséget a teljesítményre optimalizált tárolási konfiguráció lap megnyitásához, és adja meg a tárolási követelményeket.

![Képernyőkép, amely kiemeli, hogy a tárolási konfiguráció hogyan módosítható.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

A **Storage optimized for** (Tároló optimalizálási módja) területen válasszon az alábbi lehetőségek közül:

* A **General** (Általános) az alapértelmezett beállítás, amely a legtöbb számítási feladatot támogatja.
* A **tranzakciós feldolgozás** optimalizálja a tárolót a hagyományos adatbázis-OLTP munkaterhelésekhez.
* A **Data warehousing** (Adatraktározás) beállítás elemzési és jelentéskészítési számítási feladatokra optimalizálja a tárolót.

![SQL virtuális gép tárolójának konfigurációja](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Dönthet úgy is, hogy alapértelmezés szerint elhagyja az értékeket, vagy manuálisan is módosíthatja a tárolási topológiát, hogy megfeleljen a IOPS igényeinek. További információ: [tárolási konfiguráció](storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server licenc

Ha frissítési garanciával rendelkező ügyfelet használ, használhatja a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) saját SQL Server-licenc használatára, és mentheti az erőforrásokat. 

![SQL VM-licenc](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatikus javítás

Az **Automatikus javítás** alapértelmezés szerint engedélyezve van. Az automatizált javítás lehetővé teszi, hogy az Azure automatikus javításokat alkalmazzon az SQL Serveren és az operációs rendszeren. A karbantartási időszak beállításához adja meg a hét egy napját, egy időpontot és egy időtartamot. Az Azure ebben a karbantartási időszakban végzi el a javításokat. A karbantartási időszak ütemezett beállítása a virtuális gép területi beállítását használja. Ha nem szeretné, hogy az Azure automatikusan javítási SQL Server és az operációs rendszert, válassza a **Letiltás** lehetőséget.  

![SQL virtuális gép automatikus javítása](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

További információk: [Automated Patching for SQL Server in Azure Virtual Machines](automated-patching.md) (Az SQL Server automatikus javítása Azure virtuális gépeken).

### <a name="automated-backup"></a>Automatikus biztonsági mentés

Az **Automatikus biztonsági mentés** területen engedélyezheti az összes adatbázis automatikus mentését. Az automatikus biztonsági mentés alapértelmezés szerint le van tiltva.

Az SQL automatikus biztonsági mentésének engedélyezésekor konfigurálhatja a következő beállításokat:

* Biztonsági mentések megőrzési ideje (napokban)
* A biztonsági mentésekhez használt tárfiók
* Titkosítási beállítás és a biztonsági mentések jelszavas védelme
* Rendszeradatbázisok biztonsági mentése
* Biztonsági mentések ütemezésének konfigurálása

A biztonsági másolat titkosításához válassza az **Engedélyezés** lehetőséget. Ezután adja meg a **Jelszót**. Az Azure a biztonsági mentések titkosításához létrehoz egy tanúsítványt, amelyet a megadott jelszóval véd. Alapértelmezés szerint az ütemterv automatikusan be van állítva, de manuális ütemtervet is **létrehozhat.** 

![SQL virtuális gép automatikus biztonsági mentései](./media/create-sql-vm-portal/automated-backup.png)

További információk: [Automated Backup for SQL Server in Azure Virtual Machines](automated-backup-sql-2014.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).


### <a name="machine-learning-services"></a>Machine Learning Services

Lehetősége van a [Machine learning Services](/sql/advanced-analytics/)engedélyezésére. Ez a beállítás lehetővé teszi a gépi tanulás használatát a Python és az R használatával SQL Server 2017-es verzióban. Az **SQL Server beállítások** ablakban válassza az **Engedélyezés** lehetőséget.


## <a name="4-review--create"></a>4. felülvizsgálat + létrehozás

A **felülvizsgálat + létrehozás** lapon:
1. Tekintse át az összegzést.
1. Válassza a **Létrehozás** lehetőséget a virtuális géphez megadott SQL Server, erőforráscsoport és erőforrások létrehozásához.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit.

> [!NOTE]
> Egy példa arra, hogy az Azure egy SQL Server VM üzembe helyezését: az USA keleti régiójában kiépített teszt SQL Server VM az alapértelmezett beállításokkal körülbelül 12 percet vesz igénybe. Előfordulhat, hogy a régió és a kiválasztott beállítások alapján gyorsabban vagy lassabban üzemel az üzembe helyezési idő.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Nyissa meg a virtuális gépet Távoli asztal

A következő lépésekkel csatlakozhat a SQL Server virtuális géphez RDP protokoll (RDP) használatával:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Kapcsolódás SQL Server távolról

Ebben az útmutatóban a virtuális gép **nyilvános** hozzáférését választotta, és **SQL Server a hitelesítést**. Ezek a beállítások automatikusan úgy konfigurálták a virtuális gépet, hogy az az internetről bármely ügyfél számára engedélyezi az SQL Serverhez való csatlakozást (feltéve, hogy helyes SQL-bejelentkezési névvel rendelkeznek).

> [!NOTE]
> Ha az üzembe helyezés során nem a nyilvános hozzáférést választotta, akkor az üzembe helyezés után a portálon keresztül módosíthatja az SQL csatlakozási beállításait. További információ: [Az SQL csatlakozási beállításainak módosítása](ways-to-connect-to-sql.md#change).

Az alábbi fejezetek bemutatják, hogyan csatlakozhat az interneten keresztül a SQL Server VM-példányához.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Ez a példa a Common 1433-as portot használja. Ezt az értéket azonban módosítani kell, ha egy másik portot (például 1401) adott meg a SQL Server VM telepítése során. 


## <a name="next-steps"></a>Következő lépések

További információ az SQL Server használatáról az Azure-ban: [SQL Server on Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) (SQL Server az Azure virtuális gépeken) és [Frequently Asked Questions](frequently-asked-questions-faq.md) (Gyakran ismételt kérdések).