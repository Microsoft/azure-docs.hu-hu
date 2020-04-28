---
title: Rendelkezésre állási csoport konfigurálása (Azure Gyorsindítás sablon)
description: Az Azure Gyorsindítás sablonjaival hozza létre a Windows feladatátvevő fürtöt, csatlakoztassa SQL Server virtuális gépeket a fürthöz, hozza létre a figyelőt, és konfigurálja a belső Load balancert az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74022387"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Azure-beli Gyorsindítás sablonok használata az Azure-beli virtuális gépek SQL Server rendelkezésre állási csoportjának konfigurálásához
Ez a cikk azt ismerteti, hogyan használható az Azure gyorsindítási sablonok az Azure-ban SQL Server virtuális gépekre vonatkozó always on rendelkezésre állási csoport konfigurációjának részleges automatizálására. Ebben a folyamatban két Azure rövid útmutató-sablont használunk: 

   | Sablon | Leírás |
   | --- | --- |
   | [101-SQL-VM-AG-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Létrehozza a Windows feladatátvevő fürtöt, és csatlakoztatja a SQL Server virtuális gépekhez. |
   | [101 – SQL-VM-aglistener – telepítés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Létrehozza a rendelkezésre állási csoport figyelőjét, és konfigurálja a belső Load balancert. Ez a sablon csak akkor használható, ha a Windows feladatátvevő fürt a **101-SQL-VM-AG-Setup** sablonnal lett létrehozva. |
   | &nbsp; | &nbsp; |

A rendelkezésre állási csoport konfigurációjának más részeit manuálisan kell elvégezni, például a rendelkezésre állási csoport létrehozását és a belső terheléselosztó létrehozását. Ez a cikk az automatikus és manuális lépések sorát ismerteti.
 

## <a name="prerequisites"></a>Előfeltételek 
Ha egy always on rendelkezésre állási csoport beállítását szeretné automatizálni a gyors üzembe helyezési sablonok használatával, a következő előfeltételek szükségesek: 
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy tartományvezérlővel rendelkező erőforráscsoport. 
- Egy vagy több [, az Azure-ban SQL Server 2016 (vagy újabb) Enterprise kiadást futtató,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) tartományhoz csatlakoztatott virtuális gép, amely ugyanabban a rendelkezésre állási csoport vagy rendelkezésre állási zónában található, és amelyek [regisztrálva vannak az SQL VM erőforrás-szolgáltatónál](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Két elérhető (bármely entitás nem használja) IP-címek: egyet a belső terheléselosztó számára, a rendelkezésre állási csoport figyelője pedig a rendelkezésre állási csoporttal azonos alhálózaton belül. Ha meglévő terheléselosztó használatban van, csak egy elérhető IP-címet kell használnia.  

## <a name="permissions"></a>Engedélyek
Az Always On rendelkezésre állási csoport Azure Gyorsindítás sablonok használatával történő konfigurálásához a következő engedélyek szükségesek: 

- Egy meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása** engedéllyel rendelkezik a tartományban.  Például egy tartományi rendszergazdai fióknak jellemzően megfelelő engedélye van (például: account@domain.com). _Ennek a fióknak a helyi rendszergazda csoportnak is szerepelnie kell az egyes virtuális gépeken a fürt létrehozásához._
- A SQL Server szolgáltatást vezérlő tartományi felhasználói fiók. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>1. lépés: a feladatátvevő fürt létrehozása és SQL Server virtuális gépek csatlakoztatása a fürthöz egy rövid útmutató sablon használatával 
Miután a SQL Server virtuális gépek regisztrálva lettek az SQL VM erőforrás-szolgáltatóval, csatlakoztathatja a SQL Server virtuális gépeket a *SqlVirtualMachineGroups*. Ez az erőforrás a Windows feladatátvevő fürt metaadatait határozza meg. A metaadatok tartalmazzák a verziót, a kiadást, a teljes tartománynevet, a Active Directory fiókokat, amelyek a fürt és a SQL Server szolgáltatás felügyeletét, valamint a Storage-fiókot Felhőbeli tanúsító kezelik. 

SQL Server virtuális gépeknek a *SqlVirtualMachineGroups* erőforráscsoporthoz való hozzáadása a Windows feladatátvevő fürtszolgáltatást a fürt létrehozásához, majd a fürthöz tartozó SQL Server virtuális gépekhez csatlakoztatja. Ez a lépés a **101-SQL-VM-AG-Setup** gyorsindítási sablonnal automatizálható. A következő lépések végrehajtásával valósítható meg:

1. Lépjen a [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Gyorsindítás sablonhoz. Ezután válassza az **üzembe helyezés az Azure** -ban lehetőséget a rövid útmutató sablon megnyitásához a Azure Portal.
1. Adja meg a szükséges mezőket a Windows feladatátvevő fürt metaadatainak konfigurálásához. A nem kötelező mezőket üresen hagyhatja.

   A következő táblázat a sablonhoz szükséges értékeket tartalmazza: 

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az az előfizetés, amelyben a SQL Server virtuális gépek léteznek. |
   |**Erőforráscsoport** | Az az erőforráscsoport, amelyben a SQL Server virtuális gépek találhatók. | 
   |**Feladatátvevő fürt neve** | Az új Windows feladatátvevő fürthöz használni kívánt név. |
   | **Meglévő virtuális gépek listája** | A rendelkezésre állási csoportban részt venni kívánó SQL Server virtuális gépek, amelyek az új fürt részét képezik. Ezeket az értékeket vesszővel és szóközzel válassza el (például: *SQLVM1, SQLVM2*). |
   | **SQL Server verziója** | A SQL Server virtuális gépek SQL Server verziója. Válassza ki a legördülő listából. Jelenleg csak SQL Server 2016 és SQL Server 2017 rendszerképek támogatottak. |
   | **Meglévő teljesen minősített tartománynév** | Annak a tartománynak a meglévő teljes tartományneve, amelyben a SQL Server virtuális gépek találhatók. |
   | **Meglévő tartományi fiók** | Egy meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása** engedéllyel rendelkezik a tartományban, mivel a [CNO](/windows-server/failover-clustering/prestage-cluster-adds) a sablon központi telepítése során jön létre. Például egy tartományi rendszergazdai fióknak jellemzően megfelelő engedélye van (például: account@domain.com). *Ennek a fióknak a helyi rendszergazda csoportnak is szerepelnie kell az egyes virtuális gépeken a fürt létrehozásához.*| 
   | **Tartományi fiók jelszava** | A korábban említett tartományi felhasználói fiók jelszava. | 
   | **Meglévő SQL-szolgáltatásfiók** | A [SQL Server szolgáltatást](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) a rendelkezésre állási csoport telepítése során vezérlő tartományi felhasználói fiók (például: account@domain.com). |
   | **SQL-szolgáltatás jelszava** | A SQL Server szolgáltatást vezérlő tartományi felhasználói fiók által használt jelszó. |
   | **Felhőbeli tanúsító neve** | Új Azure Storage-fiók, amely a Felhőbeli tanúsító számára lesz létrehozva és használva. Ezt a nevet módosíthatja. |
   | **\_összetevők helye** | Ez a mező alapértelmezés szerint be van állítva, és nem módosítható. |
   | **\_összetevők helye sas-token** | Ez a mező szándékosan üresen marad. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a használati feltételeket, jelölje be az Elfogadom **a fenti feltételeket és kikötéseket** jelölőnégyzetet. Ezután válassza a **vásárlás** lehetőséget a Gyorsindítás sablon üzembe helyezésének befejezéséhez. 
1. Az üzemelő példány figyeléséhez vagy válassza ki a telepítést a felső navigációs szalagcím **értesítési** harang ikonjában, vagy keresse meg az **erőforráscsoport** elemet a Azure Portal. Válassza a **Beállítások**területen a **központi telepítések** lehetőséget, majd válassza ki a **Microsoft. template** üzembe helyezését. 

>[!NOTE]
> A sablon központi telepítése során megadott hitelesítő adatokat a rendszer csak az üzemelő példány hosszára tárolja. Az üzembe helyezés befejeződése után a rendszer eltávolítja ezeket a jelszavakat. Ha további SQL Server virtuális gépeket ad hozzá a fürthöz, a rendszer kérni fogja, hogy adja meg őket. 


## <a name="step-2-manually-create-the-availability-group"></a>2. lépés: a rendelkezésre állási csoport manuális létrehozása 
A szokásos módon hozza létre manuálisan a rendelkezésre állási csoportot a [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), a [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy a [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)használatával. 

>[!IMPORTANT]
> Jelenleg *ne hozzon* létre figyelőt, mert az **101-SQL-VM-aglistener-Setup** rövid útmutató sablonja a 4. lépésben automatikusan elvégezhető. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>3. lépés: a belső terheléselosztó manuális létrehozása
Az Always On rendelkezésre állási csoport figyelője a Azure Load Balancer belső példányát igényli. A belső terheléselosztó egy "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelője számára, amely gyorsabb feladatátvételt és újracsatlakoztatást tesz lehetővé. Ha a rendelkezésre állási csoportban lévő SQL Server virtuális gépek ugyanazon rendelkezésre állási csoportba tartoznak, alapszintű Load balancert is használhat. Ellenkező esetben standard Load balancert kell használnia. 

> [!IMPORTANT]
> A belső terheléselosztónek ugyanabban a virtuális hálózatban kell lennie, mint a SQL Server VM példányoknak. 

Csak létre kell hoznia a belső Load balancert. A 4. lépésben a **101-SQL-VM-aglistener-Setup** Gyorsindítás sablon kezeli a többi konfigurációt (például a háttér-készletet, az állapot-mintavételt és a terheléselosztási szabályokat). 

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amely a SQL Server virtuális gépeket tartalmazza. 
2. Az erőforráscsoport területen válassza a **Hozzáadás**lehetőséget.
3. Keresse meg a **Load balancert**. A keresési eredmények között válassza a **Load Balancer**lehetőséget, amelyet a **Microsoft**tesz közzé.
4. A **Load Balancer** panelen válassza a **Létrehozás**lehetőséget.
5. A terheléselosztó **létrehozása** párbeszédpanelen konfigurálja a terheléselosztó a következőképpen:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |Adja meg a terheléselosztó nevét jelölő szöveges nevet. Írja be például a következőt: **sqlLB**. |
   | **Típus** |**Belső**: a legtöbb implementáció belső Load balancert használ, amely lehetővé teszi, hogy az ugyanazon a virtuális hálózaton lévő alkalmazások csatlakozzanak a rendelkezésre állási csoporthoz.  </br> **Külső**: lehetővé teszi, hogy az alkalmazások nyilvános internetkapcsolaton keresztül csatlakozzanak a rendelkezésre állási csoporthoz. |
   | **Virtuális hálózat** | Válassza ki azt a virtuális hálózatot, amelyre a SQL Server példányok tartoznak. |
   | **Alhálózat** | Válassza ki azt az alhálózatot, amelyhez a SQL Server példányok tartoznak. |
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Magánhálózati IP-cím** | Válasszon ki egy elérhető IP-címet az alhálózatból. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező jelenhet meg. Válassza ki azt az előfizetést, amelyet hozzá szeretne rendelni ehhez az erőforráshoz. Általában ugyanazt az előfizetést, mint a rendelkezésre állási csoport összes erőforrását. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelybe a SQL Server példányok tartoznak. |
   | **Hely** |Válassza ki azt az Azure-helyet, amelyen a SQL Server példányok szerepelnek. |
   | &nbsp; | &nbsp; |

6. Kattintson a **Létrehozás** gombra. 


>[!IMPORTANT]
> Az egyes SQL Server VMokhoz tartozó nyilvános IP-erőforrásnak standard SKU-nak kell lennie, hogy kompatibilis legyen a standard Load balancerrel. A virtuális gép nyilvános IP-címéhez tartozó SKU meghatározásához lépjen az **erőforráscsoport**elemre, válassza ki a **nyilvános IP-cím** erőforrását a SQL Server VMhoz, és keresse meg az értéket az **Áttekintés** ablaktáblán található **SKU** elemnél. 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>4. lépés: a rendelkezésre állási csoport figyelő létrehozása és a belső terheléselosztó konfigurálása a gyors üzembe helyezési sablonnal

Hozza létre a rendelkezésre állási csoport figyelőjét, és konfigurálja a belső terheléselosztó automatikus konfigurálását az **101-SQL-VM-aglistener-Setup** gyorsindító sablonnal. A sablon a Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener erőforrást is kiépíti. Az **101-SQL-VM-aglistener-Setup** Gyorsindítás sablon az SQL VM erőforrás-szolgáltatón keresztül a következő műveleteket végzi el:

- Létrehoz egy új előtér-IP-erőforrást (az üzembe helyezés során megadott IP-cím értéke alapján) a figyelőhöz. 
- A fürt és a belső terheléselosztó hálózati beállításainak konfigurálása. 
- A belső terheléselosztó, az állapot-mintavétel és a terheléselosztási szabályok számára konfigurálja a háttér-készletet.
- Létrehozza a rendelkezésre állási csoport figyelőjét a megadott IP-címmel és névvel.
 
>[!NOTE]
> A **101-SQL-VM-aglistener-Setup** csak akkor használható, ha a Windows feladatátvevő fürt a **101-SQL-VM-AG-Setup** sablonnal lett létrehozva.
   
   
A belső terheléselosztó konfigurálásához és a rendelkezésre állási csoport figyelő létrehozásához tegye a következőket:
1. Nyissa meg az [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) rövid útmutatót, és válassza az **üzembe helyezés az Azure** -ban lehetőséget a gyors útmutató sablon elindításához a Azure Portal.
1. Töltse ki a szükséges mezőket a belső terheléselosztó konfigurálásához, és hozza létre a rendelkezésre állási csoport figyelőjét. A nem kötelező mezőket üresen hagyhatja. 

   A következő táblázat a sablonhoz szükséges értékeket tartalmazza: 

   | **Mező** | Érték |
   | --- | --- |
   |**Erőforráscsoport** | Az az erőforráscsoport, amelyben a SQL Server virtuális gépek és a rendelkezésre állási csoport létezik. | 
   |**Meglévő feladatátvevő fürt neve** | Annak a fürtnek a neve, amelyhez a SQL Server virtuális gépek csatlakoznak. |
   | **Meglévő SQL rendelkezésre állási csoport**| Annak a rendelkezésre állási csoportnak a neve, amelynek a SQL Server virtuális gépei a részei. |
   | **Meglévő virtuális gépek listája** | A korábban említett rendelkezésre állási csoport részét képező SQL Server virtuális gépek nevei. A neveket vesszővel és szóközzel válassza el (például: *SQLVM1, SQLVM2*). |
   | **Figyelő** | A figyelőhöz hozzárendelni kívánt DNS-név. Alapértelmezés szerint ez a sablon a "aglistener" nevet adja meg, de módosíthatja is. A név nem lehet hosszabb 15 karakternél. |
   | **Figyelő portja** | A figyelő által használni kívánt port. Ez a port általában az alapértelmezett 1433. Ez az a portszám, amelyet a sablon meghatároz. Ha azonban az alapértelmezett port meg lett változtatva, a figyelő portjának Ehelyett ezt az értéket kell használnia. | 
   | **Figyelő IP-címe** | A figyelő által használni kívánt IP-cím. Ez a címe a sablon központi telepítése során jön létre, ezért olyant adjon meg, amely még nincs használatban.  |
   | **Meglévő alhálózat** | A SQL Server virtuális gépek belső alhálózatának neve (például: *default*). Ezt az értéket az **erőforráscsoport**, a virtuális hálózat kiválasztása, az **alhálózatok** kiválasztása a **Beállítások** panelen, majd a **név**alatt lévő érték másolásával határozhatja meg. |
   | **Meglévő belső Load Balancer** | A 3. lépésben létrehozott belső terheléselosztó neve. |
   | **Mintavételi port** | A belső terheléselosztó számára használni kívánt mintavételi port. Alapértelmezés szerint a sablon 59999-et használ, de ez az érték módosítható. |
   | &nbsp; | &nbsp; |

1. Ha elfogadja a használati feltételeket, jelölje be az Elfogadom **a fenti feltételeket és kikötéseket** jelölőnégyzetet. A Gyorsindítás sablon üzembe helyezésének befejezéséhez válassza a **vásárlás** lehetőséget. 
1. Az üzemelő példány figyeléséhez vagy válassza ki a telepítést a felső navigációs szalagcím **értesítési** harang ikonjában, vagy keresse meg az **erőforráscsoport** elemet a Azure Portal. Válassza a **Beállítások**területen a **központi telepítések** lehetőséget, majd válassza ki a **Microsoft. template** üzembe helyezését. 

>[!NOTE]
>Ha az üzemelő példány a felében nem sikerül, manuálisan [el kell távolítania az újonnan létrehozott figyelőt](#remove-the-availability-group-listener) a PowerShell használatával, mielőtt újra telepítené a **101-SQL-VM-aglistener-Setup** gyorsindító sablont. 

## <a name="remove-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelő eltávolítása
Ha később el kell távolítania a rendelkezésre állási csoport figyelőjét, amelyet a sablon konfigurált, el kell végeznie az SQL virtuális gép erőforrás-szolgáltatóját. Mivel a figyelő az SQL VM erőforrás-szolgáltatón keresztül van regisztrálva, csak a SQL Server Management Studio-en keresztüli törlés nem elegendő. 

A legjobb módszer az SQL VM erőforrás-szolgáltatón keresztüli Törlés az alábbi kódrészlettel a PowerShellben. Ezzel eltávolítja a rendelkezésre állási csoport figyelő metaadatait az SQL VM erőforrás-szolgáltatójából. Emellett fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Gyakori hibák
Ez a szakasz néhány ismert problémát és lehetséges megoldását tárgyalja. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Már létezik a rendelkezésre állási csoport figyelője a rendelkezésre állási csoporthoz (\<AG-Name>)
A rendelkezésre állási csoport figyelője számára az Azure Gyorsindítás sablonban használt kiválasztott rendelkezésre állási csoport már tartalmaz figyelőt. Vagy fizikailag a rendelkezésre állási csoporton belül van, vagy a metaadatok az SQL VM erőforrás-szolgáltatóján belül maradnak. Távolítsa el a figyelőt a [PowerShell](#remove-the-availability-group-listener) használatával az **101-SQL-VM-aglistener-Setup** gyorsindítási sablon újbóli üzembe helyezése előtt. 

### <a name="connection-only-works-from-primary-replica"></a>A kapcsolatok csak az elsődleges replikából működnek
Ez a viselkedés valószínűleg egy sikertelen **101-SQL-VM-aglistener –** telepítési sablon telepítése, amely inkonzisztens állapotban hagyta el a belső terheléselosztó konfigurációját. Ellenőrizze, hogy a háttér-készlet tartalmazza-e a rendelkezésre állási csoport listáját, valamint hogy léteznek-e szabályok az állapot-mintavételhez és a terheléselosztási szabályokhoz. Ha bármilyen hiányzik, a belső terheléselosztó konfigurációja inkonzisztens állapotú. 

A probléma megoldásához távolítsa el a figyelőt a [PowerShell](#remove-the-availability-group-listener)használatával, törölje a belső Load balancert a Azure Portalon keresztül, majd kezdje újra a 3. lépésben. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest – csak az SQL-alapú virtuális gépek listája frissíthető
Ez a hiba akkor fordulhat elő, ha az **101-SQL-VM-aglistener-Setup** sablont üzembe helyezi, ha a figyelőt SQL Server Management Studio (SSMS) használatával törölte, de az nem lett törölve az SQL VM erőforrás-szolgáltatótól. A figyelő a SSMS-n keresztül történő törlése nem távolítja el a figyelő metaadatait az SQL VM erőforrás-szolgáltatótól. A figyelőt törölni kell az erőforrás-szolgáltatóról a [PowerShell](#remove-the-availability-group-listener)használatával. 

### <a name="domain-account-does-not-exist"></a>A tartományi fiók nem létezik.
Ennek a hibának két oka lehet. Vagy a megadott tartományi fiók nem létezik, vagy hiányzik az egyszerű felhasználónév [(UPN)](/windows/desktop/ad/naming-properties#userprincipalname) . Az **101-SQL-VM-AG-Setup** sablon tartományi fiókot vár az UPN-űrlapon (azaz *user@domain.com*), de előfordulhat, hogy bizonyos tartományi fiókok hiányoznak. Ez általában akkor fordul elő, ha egy helyi felhasználó át lett telepítve az első tartományi rendszergazdai fiókba, ha a kiszolgálót tartományvezérlővé adták elő, vagy ha a felhasználó a PowerShell használatával lett létrehozva. 

Ellenőrizze, hogy létezik-e a fiók. Ha igen, lehet, hogy a második szituációban fut. A probléma megoldásához tegye a következőket:

1. A tartományvezérlőn nyissa meg a **Active Directory felhasználók és számítógépek** ablakot a Kiszolgálókezelő **eszközök** lehetőségével **Server Manager**. 
2. Nyissa meg a fiókot a bal oldali ablaktáblán a **felhasználók** lehetőség kiválasztásával.
3. Kattintson a jobb gombbal a fiókra, majd válassza a **Tulajdonságok**lehetőséget.
4. Válassza a **fiók** fület. Ha a **felhasználói bejelentkezési név** mező üres, akkor ez a hiba oka. 

    ![Az üres felhasználói fiók hiányzó UPN-t jelez](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. A **felhasználói bejelentkezési név** mezőben adja meg a felhasználó nevét, és válassza ki a megfelelő tartományt a legördülő listából. 
6. Kattintson az **alkalmaz** gombra a módosítások mentéséhez és a párbeszédpanel bezárásához az **OK gombra**kattintva. 

Miután elvégezte ezeket a módosításokat, próbálja meg még egyszer üzembe helyezni az Azure Gyorsindítás-sablont. 



## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [SQL Server virtuális gépek áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server virtuális gépekkel kapcsolatos gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [A SQL Server virtuális gépek díjszabási útmutatója](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server virtuális gépek kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM licencelési modelljeinek váltása](virtual-machines-windows-sql-ahb.md)



