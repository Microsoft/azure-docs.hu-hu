---
title: Azure Migrate-berendezés
description: A Azure Migrate készülék támogatásának összegzését tartalmazza.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 08cd0e9d33dd88b9bdc418f3d1bbd382b2d80632
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038764"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk összefoglalja az Azure Migrate berendezés előfeltételeinek és támogatási követelményeit.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate készüléket a következő esetekben használja a rendszer.

**Forgatókönyv** | **Eszköz** | **A következőhöz használatos**
--- | --- | ---
**A VMware-környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: kiszolgáló értékelése | A VMware-környezetben futó kiszolgálók felderítése<br/><br/> Elvégezheti a telepített alkalmazások felderítését, az ügynök nélküli függőségek elemzését és SQL Server példányok és adatbázisok észlelését.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.
**A VMware-környezetben futó kiszolgálók ügynök nélküli áttelepítése** | Azure Migrate: kiszolgáló áttelepítése | A VMware-környezetben futó kiszolgálók felderítése. <br/><br/> Kiszolgálók replikálása anélkül, hogy ügynököket kellene telepíteni rajtuk.
**A Hyper-V környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: kiszolgáló értékelése | A Hyper-V környezetben futó kiszolgálók felderítése.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.
**A helyszíni fizikai vagy virtualizált kiszolgálók felderítése és értékelése** |  Azure Migrate: kiszolgáló értékelése |  Helyszíni fizikai vagy virtualizált kiszolgálók felderítése.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A készülék több módszer használatával is üzembe helyezhető:

- A készülék a VMware vagy a Hyper-V környezetben futó kiszolgálók sablonjának használatával telepíthető (a VMware-hez vagy [a virtuális merevlemezhez a Hyper-v-hez](how-to-set-up-appliance-hyper-v.md)készült[petesejtek sablonja](how-to-set-up-appliance-vmware.md) ).
- Ha nem szeretne sablont használni, telepítheti a készüléket VMware vagy Hyper-V környezetben egy [PowerShell-telepítő parancsfájl](deploy-appliance-script.md)használatával.
- Azure Government a készüléket egy PowerShell-telepítő parancsfájl használatával kell telepítenie. Tekintse át az üzembe helyezés lépéseit [itt](deploy-appliance-script-government.md).
- A helyszíni vagy más Felhőbeli fizikai vagy virtualizált kiszolgálók esetében mindig PowerShell-telepítő parancsfájl használatával telepítse a készüléket. Tekintse át az üzembe helyezés lépéseit [itt](how-to-set-up-appliance-physical.md).
- A letöltési hivatkozások az alábbi táblázatokban érhetők el.


## <a name="appliance---vmware"></a>Készülék – VMware

A következő táblázat összefoglalja a VMware Azure Migrate készülékre vonatkozó követelményeit.

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

**Követelmény** | **VMware** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék Configuration Managert, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés-szolgáltatások** | A készülék a következő szolgáltatásokat nyújtja:<br/><br/> - **Készülék Configuration Manager**: ez egy webalkalmazás, amely a forrás részleteivel konfigurálható a kiszolgálók felderítésének és értékelésének elindításához.<br/> - **VMware Discovery Agent**: az ügynök összegyűjti a kiszolgálói konfigurációs metaadatokat, amelyek segítségével helyszíni értékelésként hozható létre.<br/>- **VMware Assessment Agent**: az ügynök gyűjti a kiszolgáló teljesítményére vonatkozó metaadatokat, amelyek a teljesítmény-alapú értékelések létrehozásához használhatók.<br/>- **Automatikus frissítési szolgáltatás**: a szolgáltatás naprakészen tartja a készüléken futó összes ügynököt. A szolgáltatás 24 óránként egyszer automatikusan fut.<br/>- **DRA-ügynök**: összehangolja a kiszolgálók replikálását, és koordinálja a replikált kiszolgálók és az Azure közötti kommunikációt. Csak a kiszolgálók az Azure-ba való replikálásához használatos ügynök nélküli áttelepítés használatával.<br/>- **Átjáró**: replikált adatokat küld az Azure-nak. Csak a kiszolgálók az Azure-ba való replikálásához használatos ügynök nélküli áttelepítés használatával.<br/>- **SQL-felderítési és-értékelő ügynök**: SQL Server példányok és adatbázisok konfigurációs és teljesítménybeli metaadatait elküldi az Azure-nak.
**Projekt korlátai** |  A készülékek csak egyetlen projektben regisztrálhatók.<br/> Egyetlen projekt több regisztrált berendezéssel is rendelkezhet.
**Felderítési korlátok** | A készülékek vCenter Serveron futó, akár 10 000-es kiszolgálót is felfedezhetnek.<br/> Egy berendezés egyetlen vCenter Serverhoz tud csatlakozni.
**Támogatott központi telepítés** | Telepítsen vCenter Server-t futtató új kiszolgálóként a petesejtek sablon használatával.<br/><br/> Üzembe helyezés Windows Server 2016 rendszert futtató meglévő kiszolgálón PowerShell-telepítő parancsfájl használatával.
**PETESEJT-sablon** | Letöltés a projektből vagy [innen](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> A letöltési méret 11,9 GB.<br/><br/> A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes.<br/>Ha a próbaidőszak majdnem lejár, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket a petesejtek sablonnal, vagy aktiválja a berendezés-kiszolgáló operációs rendszerének licencét.
**PETESEJTEK ellenőrzése** | [Ellenőrizze](tutorial-discover-vmware.md#verify-security) a projektből letöltött petesejtek-sablont a kivonatoló értékek ellenőrzésével.
**PowerShell-parancsprogram** | Tekintse meg ezt a [cikket](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) , amely bemutatja, hogyan helyezhet üzembe egy készüléket a PowerShell telepítő parancsfájl használatával.<br/><br/> 
**Hardver-és hálózati követelmények** |  A készüléknek a kiszolgálón kell futnia a Windows Server 2016, 32-GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárolás és egy külső virtuális kapcsoló alapján.<br/> A berendezéshez közvetlenül vagy proxyn keresztül kell internet-hozzáférést igényelni.<br/><br/> Ha a készüléket a petesejtek sablonnal telepíti, elegendő erőforrásra van szüksége a vCenter Server egy olyan kiszolgáló létrehozásához, amely megfelel a hardverkövetelmények követelményeinek.<br/><br/> Ha a készüléket egy meglévő kiszolgálón futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek.<br/>_(Jelenleg a készülék központi telepítése csak Windows Server 2016 rendszeren támogatott.)_
**VMware-követelmények** | Ha a készüléket vCenter Server-kiszolgálóként telepíti, akkor azt a 5,5, 6,0, 6,5 vagy 6,7 rendszert futtató vCenter Server kell telepíteni, valamint egy 5,5 vagy újabb verziót futtató ESXi-gazdagépet.<br/><br/> 
**VDDK (ügynök nélküli áttelepítés)** | Ha a készüléket a kiszolgálók ügynök nélküli áttelepítésére szeretné használni, a VMware vSphere VDDK telepíteni kell a berendezés-kiszolgálóra.

## <a name="appliance---hyper-v"></a>Készülék – Hyper-V

**Követelmény** | **Hyper-V** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék Configuration Managert, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés-szolgáltatások** | A készülék a következő szolgáltatásokat nyújtja:<br/><br/> - **Készülék Configuration Manager**: ez egy webalkalmazás, amely a forrás részleteivel konfigurálható a kiszolgálók felderítésének és értékelésének elindításához.<br/> - **Felderítési ügynök**: az ügynök a kiszolgálói konfigurációs metaadatokat gyűjti, amelyek a helyszíni értékelésekhez használhatók.<br/>- **Értékelő ügynök**: az ügynök a kiszolgáló teljesítményére vonatkozó metaadatokat gyűjt, amelyek a teljesítmény-alapú értékelések létrehozásához használhatók.<br/>- **Automatikus frissítési szolgáltatás**: a szolgáltatás naprakészen tartja a készüléken futó összes ügynököt. A szolgáltatás 24 óránként egyszer automatikusan fut.
**Projekt korlátai** |  A készülékek csak egyetlen projektben regisztrálhatók.<br/> Egyetlen projekt több regisztrált berendezéssel is rendelkezhet.
**Felderítési korlátok** | A berendezések a Hyper-V környezetben futó, akár 5000-es kiszolgálót is felfedezhetnek.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Támogatott központi telepítés** | A Hyper-V-gazdagépen futó kiszolgálóként egy VHD-sablon használatával telepíthető.<br/><br/> Üzembe helyezés Windows Server 2016 rendszert futtató meglévő kiszolgálón PowerShell-telepítő parancsfájl használatával.
**VHD-sablon** | A VHD-t tartalmazó Zip-fájl. Letöltés a projektből vagy [innen.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> A letöltési méret 8,91 GB.<br/><br/> A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes.<br/> Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a berendezés-kiszolgáló operációs rendszerének licencét.
**VHD-ellenőrzés** | [Ellenőrizze](tutorial-discover-hyper-v.md#verify-security) a projektből letöltött VHD-sablont a kivonatoló értékek ellenőrzésével.
**PowerShell-parancsprogram** | Tekintse meg ezt a [cikket](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) , amely bemutatja, hogyan helyezhet üzembe egy készüléket a PowerShell telepítő parancsfájl használatával.<br/>
**Hardver-és hálózati követelmények**  |  A készüléknek a kiszolgálón kell futnia a Windows Server 2016, 16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárterület és egy külső virtuális kapcsoló esetében.<br/> A készüléknek statikus vagy dinamikus IP-címnek kell lennie, és közvetlenül vagy proxyn keresztül kell internet-hozzáférést biztosítania.<br/><br/> Ha a berendezést Hyper-V-gazdagépen futó kiszolgálóként futtatja, elegendő erőforrásra van szüksége a gazdagépen ahhoz, hogy olyan kiszolgálót hozzon létre, amely megfelel a hardverkövetelmények követelményeinek.<br/><br/> Ha a készüléket egy meglévő kiszolgálón futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek.<br/>_(Jelenleg a készülék központi telepítése csak Windows Server 2016 rendszeren támogatott.)_
**Hyper-V követelmények** | Ha a készüléket a VHD sablonnal telepíti, a Azure Migrate által biztosított készülék a Hyper-V VM 5,0-es verziója.<br/><br/> A Hyper-V-gazdagépen Windows Server 2012 R2 vagy újabb rendszernek kell futnia.

## <a name="appliance---physical"></a>Berendezés – fizikai

**Követelmény** | **Fizikai** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék Configuration Managert, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés-szolgáltatások** | A készülék a következő szolgáltatásokat nyújtja:<br/><br/> - **Készülék Configuration Manager**: ez egy webalkalmazás, amely a forrás részleteivel konfigurálható a kiszolgálók felderítésének és értékelésének elindításához.<br/> - **Felderítési ügynök**: az ügynök a kiszolgálói konfigurációs metaadatokat gyűjti, amelyek a helyszíni értékelésekhez használhatók.<br/>- **Értékelő ügynök**: az ügynök a kiszolgáló teljesítményére vonatkozó metaadatokat gyűjt, amelyek a teljesítmény-alapú értékelések létrehozásához használhatók.<br/>- **Automatikus frissítési szolgáltatás**: a szolgáltatás naprakészen tartja a készüléken futó összes ügynököt. A szolgáltatás 24 óránként egyszer automatikusan fut.
**Projekt korlátai** |  A készülékek csak egyetlen projektben regisztrálhatók.<br/> Egyetlen projekt több regisztrált berendezéssel is rendelkezhet.<br/> 
**Felderítési korlátok** | A berendezések akár 1000 fizikai kiszolgálót is felfedezhetnek.
**Támogatott központi telepítés** | Üzembe helyezés Windows Server 2016 rendszert futtató meglévő kiszolgálón PowerShell-telepítő parancsfájl használatával.
**PowerShell-parancsprogram** | Töltse le a szkriptet (AzureMigrateInstaller.ps1) egy [zip-fájlba](https://go.microsoft.com/fwlink/?linkid=2140334)a projektből vagy innen. [További információ](tutorial-discover-physical.md).<br/><br/> A letöltési méret 85,8 MB.
**Szkript ellenőrzése** | [Ellenőrizze](tutorial-discover-physical.md#verify-security) a PowerShell telepítőjének a projektből letöltött parancsfájlját a kivonatoló értékek ellenőrzésével.
**Hardver-és hálózati követelmények** |  A készüléknek a kiszolgálón kell futnia a Windows Server 2016, 16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárolással.<br/> A készüléknek statikus vagy dinamikus IP-címnek kell lennie, és közvetlenül vagy proxyn keresztül kell internet-hozzáférést biztosítania.<br/><br/> Ha a készüléket egy meglévő kiszolgálón futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek.<br/>_(Jelenleg a készülék központi telepítése csak Windows Server 2016 rendszeren támogatott.)_


## <a name="url-access"></a>URL-hozzáférés

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés központi telepítésekor Azure Migrate a szükséges URL-címek kapcsolatát.
- Engedélyeznie kell a hozzáférést a lista összes URL-címéhez. Ha csak értékelést végez, kihagyhatja a VMware ügynök nélküli áttelepítéshez szükségesként megjelölt URL-címeket.
-  Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.

### <a name="public-cloud-urls"></a>Nyilvános Felhőbeli URL-címek

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com <br/> *. office.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Azure Active Directory-(AD-) alkalmazásokat a berendezés számára a Azure Migratesal való kommunikációhoz.
management.azure.com | Hozzon létre Azure AD-alkalmazásokat a készülékhez a Azure Migrate való kommunikációhoz.
*.services.visualstudio.com | Belső figyeléshez használt készülék-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.<br/> Megjegyzés: Győződjön meg róla, hogy a replikálni kívánt kiszolgálók rendelkeznek hozzáféréssel.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz; a Appliance Services legújabb frissítéseinek letöltésére és telepítésére szolgál.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltőközpontból.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/> Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net |  **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/>Adatok feltöltése a Storage-ba áttelepítéshez.

### <a name="government-cloud-urls"></a>Kormányzati Felhőbeli URL-címek

**URL-cím** | **Részletek**  
--- | --- |
*. portal.azure.us  | Lépjen az Azure Portalra.
graph.windows.net | Jelentkezzen be az Azure-előfizetésbe.
login.microsoftonline.us  | Hozzon létre Azure Active Directory-(AD-) alkalmazásokat a berendezés számára a Azure Migratesal való kommunikációhoz.
management.usgovcloudapi.net | Azure AD-alkalmazások létrehozása a berendezés számára a Azure Migrate szolgáltatással való kommunikációhoz.
*.services.visualstudio.com | Belső figyeléshez használt készülék-naplók feltöltése.
*. vault.usgovcloudapi.net | A Azure Key Vault titkainak kezelése.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz; a Appliance Services legújabb frissítéseinek letöltésére és telepítésére szolgál.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltőközpontból.
*. servicebus.usgovcloudapi.net  | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*. hypervrecoverymanager.windowsazure.us | **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/> Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*. blob.core.usgovcloudapi.net  |  **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/>Adatok feltöltése a Storage-ba áttelepítéshez.
*. applicationinsights.us | Belső figyeléshez használt készülék-naplók feltöltése.


## <a name="collected-data---vmware"></a>Összegyűjtött adatok – VMware

A készülék a konfigurációs metaadatokat, a teljesítmény metaadatait és a kiszolgáló függőségeinek adatait gyűjti (ha az ügynök nélküli [függőségi elemzés](concepts-dependency-visualization.md) van használatban).

### <a name="metadata"></a>Metaadatok

Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a kiszolgálók készen állnak-e az Azure-ba való áttelepítésre, a megfelelő méretű kiszolgálókra, a csomagok költségeire és az alkalmazások függőségeinek elemzésére. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

Itt látható a készülék által gyűjtött és az Azure-ba küldött kiszolgálói metaadatok teljes listája.

**ADATOK** | **SZÁMLÁLÓ**
--- | --- 
**Kiszolgáló adatai** | 
Kiszolgáló azonosítója | vm.Config. Értékű 
Kiszolgálónév | vm.Config. név
vCenter Server azonosítója | VMwareClient. instance. UUID
Virtuális gép leírása | vm.Summary.Config. Jegyzet
Licenc terméknév | VM. Client. ServiceContent. about. LicenseProductName
Operációs rendszer típusa | VM. SummaryConfig. GuestFullName
Rendszerindítás típusa | vm.Config. Belső vezérlőprogram
Magok száma | vm.Config. Hardware. NumCPU
Memória (MB) | vm.Config. Hardware. MemoryMB
Lemezek száma | vm.Config. Hardver. Device. ToList (). FindAll (x => VirtualDisk). darabszám
Lemez mérete lista | vm.Config. Hardver. Device. ToList (). FindAll (x => VirtualDisk)
Hálózati adapterek listája | vm.Config. Hardver. Device. ToList (). FindAll (x => VirtualEthernet). darabszám
Processzorhasználat | CPU. használat. átlag
Memória kihasználtsága |mem. használat. átlag
**/Lemez adatai** | 
Lemez kulcsának értéke | lemez. Kulcs
Dikunit száma | lemez. UnitNumber
Lemezvezérlő-kulcs értéke | lemez. ControllerKey. Value
Kiépített gigabájt | virtualDisk. DeviceInfo. Summary
Lemez neve | A lemez használatával generált érték. UnitNumber, lemez. Kulcs, lemez. ControllerKey. VAlue
Olvasási műveletek másodpercenként | virtualDisk. numberReadAveraged. Average
Írási műveletek másodpercenként | virtualDisk. numberWriteAveraged. Average
Olvasási sebesség (MB/s) | virtualDisk. Read. Average
Írási sebesség (MB/s) | virtualDisk. Write. Average
**Hálózati adapter adatai** | 
Hálózati adapter neve | hálózati. Kulcs
MAC-cím | ((VirtualEthernetCard) NIC). MacAddress
IPv4-címek | vm.Guest.Net
IPv6-címek | vm.Guest.Net
Olvasási sebesség (MB/s) | net. Received. Average
Írási sebesség (MB/s) | net. továbbítandó. Average
**Leltár elérési útja – részletek** | 
Name | tároló. GetType (). név
Gyermekobjektum típusa | tároló. ChildType
Hivatkozás részletei | tároló. MoRef
Szülő részletei | Container. Parent
Mappa részletei virtuális gépenként | ((Mappa) tároló). ChildEntity. Type
Adatközpont részletei virtuális gépenként | (Datacenter) tároló. VmFolder
Az adatközpont részletei egy gazdagép mappájában | (Datacenter) tároló. HostFolder
Fürt adatai egy gazdagépen | ((ClusterComputeResource) tároló). Gazdagép
Gazdagép adatai virtuális gépenként | ((HostSystem) tároló). VM

### <a name="performance-data"></a>Teljesítményadatok


Itt látható a VMware virtuális gép teljesítményadatokat, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Processzorhasználat | CPU. használat. átlag | Ajánlott virtuális gép mérete/díja
Memória kihasználtsága | mem. használat. átlag | Ajánlott virtuális gép mérete/díja
Lemez olvasási sebessége (MB/s) | virtualDisk. Read. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási sebessége (MB/s) | virtualDisk. Write. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez olvasási műveletei másodpercenként | virtualDisk. numberReadAveraged. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási műveletei másodpercenként | virtualDisk. numberWriteAveraged. Average  | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hálózati adapter olvasási sebessége (MB/s) | net. Received. Average | A virtuális gép méretének kiszámítása
Hálózati adapter írási sebessége (MB/s) | net. továbbítandó. Average  |A virtuális gép méretének kiszámítása


### <a name="installed-applications-data"></a>Telepített alkalmazások

A készülék adatokat gyűjt a telepített alkalmazásokról, szerepkörökről és szolgáltatásokról a kiszolgálókon.

#### <a name="windows-server-application-data"></a>A Windows Server alkalmazásadatok

Itt láthatók azok az alkalmazások, amelyeket a készülék a VMware-környezetben felderített Windows serverekről gyűjt.

**Adatok** | **Beállításjegyzékbeli hely** | **Kulcs**
--- | --- | ---
Alkalmazásnév  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Verzió  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Szolgáltató  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>A Windows Server funkcióinak adatvédelme

Itt találja azokat a funkciókat, amelyeket a készülék a VMware-környezetben felderített Windows serverekről gyűjt.

**Adatok**  | **PowerShell-parancsmag** | **Tulajdonság**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Szolgáltatás típusa | Get-WindowsFeature  | FeatureType
Szülő  | Get-WindowsFeature  | Szülő

#### <a name="sql-server-metadata"></a>Metaadatok SQL Server

Itt láthatók azok a SQL Server adatok, amelyeket a készülék a VMware-környezetben felderített Windows serverekről gyűjt.

**Adatok**  | **Beállításjegyzékbeli hely**  | **Kulcs**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \ példány Names\SQL  | installedInstance
Kiadás  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Kiadás 
Szervizcsomag  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Verzió  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Verzió 

#### <a name="windows-server-operating-system-data"></a>Windows Server operációs rendszerre vonatkozó információ

Az alábbi, az operációs rendszer által a VMware-környezetben felderített Windows-kiszolgálókról gyűjtött operációsrendszer-adatok.

**Adatok**  | **WMI-osztály**  | **WMI-osztály tulajdonsága**
--- | --- | ---
Name  | Win32_operatingsystem  | Képaláírás
Verzió  | Win32_operatingsystem  | Verzió
Architektúra  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-application-data"></a>Linuxos kiszolgálói alkalmazásadatok

Itt láthatók a készülék által a VMware-környezetben felderített Linux-kiszolgálókról összegyűjtött alkalmazások adatai. A-kiszolgáló operációs rendszere alapján egy vagy több parancs fut.

**Adatok**  | **Parancsok**
--- | --- 
Name | RPM, dpkg-Query, Snap
Verzió | RPM, dpkg-Query, Snap
Szolgáltató | RPM, dpkg-Query, Snap

#### <a name="linux-server-operating-system-data"></a>Linux Server operációs rendszerre vonatkozó információ

A készülék által a VMware-környezetben felderített Linux-kiszolgálókról összegyűjtött operációsrendszer-adatok.

**Adatok**  | **Parancsok**
--- | --- 
Name <br/> version | A következő fájlok közül egy vagy több összegyűjtése:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architektúra | uname

### <a name="sql-server-instances-and-databases-data"></a>A példányok és az adatbázisok SQL Server

A készülék adatokat gyűjt SQL Server példányokról és adatbázisokról.

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

#### <a name="sql-database-metadata"></a>SQL Database-metaadatok

**Adatbázis-metaadatok** | **Nézetek/SQL Server tulajdonságai**
--- | ---
Az adatbázis egyedi azonosítója | sys.databases
Kiszolgáló által definiált adatbázis-azonosító | sys.databases
Az adatbázis neve | sys.databases
Adatbázis kompatibilitási szintje | sys.databases
Az adatbázis rendezési neve | sys.databases
Az adatbázis állapota | sys.databases
Az adatbázis mérete (MB) | sys.master_files
Az adatfájlokat tartalmazó hely meghajtóbetűjele | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
Adatbázisfájlok listája | sys. Databases, sys.master_files
A Service Broker engedélyezve van vagy nem | sys.databases
Az adatbázis az adatváltozások rögzítésére van engedélyezve, vagy nem | sys.databases

#### <a name="sql-server-metadata"></a>Metaadatok SQL Server

**Kiszolgálói metaadatok** | **Nézetek/SQL Server-tulajdonságok**
--- | ---
Kiszolgálónév |SERVERPROPERTY 
FQDN | Telepített alkalmazások felderítésével származtatott kapcsolatok karakterlánca
Telepítési azonosító | sys.dm_server_registry
Kiszolgáló verziója | SERVERPROPERTY
Server Edition | SERVERPROPERTY
Server Host platform (Windows/Linux) | SERVERPROPERTY
A kiszolgáló termékének szintje (RTM SP CTP) | SERVERPROPERTY 
Alapértelmezett biztonsági mentési útvonal | SERVERPROPERTY
Az adatfájlok alapértelmezett elérési útja | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
A naplófájlok alapértelmezett elérési útja | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
Nem. a-kiszolgálón lévő magok | sys.dm_os_schedulers, sys.dm_os_sys_info
Kiszolgáló rendezésének neve | SERVERPROPERTY
Nem. a-kiszolgálón lévő magok látható ONLINE állapottal | sys.dm_os_schedulers
Egyedi kiszolgáló azonosítója | sys.dm_server_registry
HA engedélyezve van, vagy nem | SERVERPROPERTY
Puffer-bővítmény engedélyezve vagy nem | sys.dm_os_buffer_pool_extension_configuration
A feladatátvevő fürt konfigurálva van vagy nem | SERVERPROPERTY
Csak Windows-hitelesítési módot használó kiszolgáló | SERVERPROPERTY 
A kiszolgáló alapszintű telepítést telepít | SERVERPROPERTY 
Nem. a rendszeren található logikai processzorok | sys.dm_server_registry, sys.dm_os_sys_info
Egy fizikai processzor-csomag által elérhető logikai vagy fizikai magok aránya | sys.dm_os_schedulers, sys.dm_os_sys_info
A rendszer nem rendelkezik fizikai processzorokkal | sys.dm_os_schedulers, sys.dm_os_sys_info
A dátum-és időkiszolgáló utolsó indítása | sys.dm_server_registry
Kiszolgáló maximális memóriájának használata (MB) | sys.dm_os_process_memory
Összesen nem. az összes adatbázisban lévő felhasználók | sys. Databases, sys. bejelentkezések
Az összes felhasználói adatbázis teljes mérete | sys.databases
A temp adatbázis mérete | sys.master_files, sys.configurations, sys.dm_os_sys_info
Nem. a bejelentkezések | sys. bejelentkezések
A csatolt kiszolgálók listája | sys. kiszolgálók
Ügynöki feladatok listája | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Teljesítmény metaadatainak

**Teljesítmény** | **Nézetek/SQL Server-tulajdonságok** | **Értékelés hatása**
--- | --- | ---
CPU-kihasználtság SQL Server| sys.dm_os_ring_buffers| Ajánlott SKU-méret (CPU-dimenzió)
SQL logikai PROCESSZORok száma| sys.dm_os_sys_info| Ajánlott SKU-méret (CPU-dimenzió)
SQL fizikai memória használatban| sys.dm_os_process_memory| Nem használt
SQL-memória kihasználtsága (%)| sys.dm_os_process_memory | Nem használt
Adatbázis CPU-kihasználtsága| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Ajánlott SKU-méret (CPU-dimenzió)
Használatban lévő adatbázis memória (puffer készlet)| sys.dm_os_buffer_descriptors| Ajánlott SKU-méret (memória dimenzió)
Fájl olvasási/írási IO| sys.dm_io_virtual_file_stats, sys.master_files| Ajánlott SKU-méret (IO-dimenzió)
Olvasás/írás típusú fájl| sys.dm_io_virtual_file_stats, sys.master_files| Ajánlott SKU-méret (átviteli dimenzió)
Fájl IO Stall írási/olvasási (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Ajánlott SKU-méret (IO-késési dimenzió)
Fájlméret| sys.master_files| Ajánlott SKU-méret (tárolási dimenzió)


### <a name="application-dependency-data"></a>Alkalmazás-függőségi adatértékek

Az ügynök nélküli függőségi elemzés gyűjti a kapcsolatokat és dolgozza fel az adatokat.

#### <a name="windows-server-dependencies-data"></a>A Windows Server függőségeinek adatvédelme

Itt láthatók azok a kapcsolatok, amelyekkel a készülék az ügynök nélküli függőségek elemzéséhez engedélyezett összes Windows-kiszolgálóról gyűjti az adatokat.

**Adatok** | **Parancsok** 
--- | --- 
Helyi port | netstat
Helyi IP-cím | netstat
Távoli port | netstat
Távoli IP-cím | netstat
TCP-kapcsolatok állapota | netstat
Folyamatazonosító | netstat
Aktív kapcsolatok száma | netstat

Itt láthatók azok a kapcsolatok, amelyekkel a készülék az ügynök nélküli függőségek elemzéséhez engedélyezett összes Windows-kiszolgálóról gyűjti az adatokat.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
Folyamatnév | Win32_Process | ExecutablePath
Folyamat argumentumai | Win32_Process | CommandLine
Alkalmazásnév | Win32_Process | A ExecutablePath tulajdonság VersionInfo. ProductName paramétere

#### <a name="linux-server-dependencies-data"></a>Linux-kiszolgáló függőségeinek adatvédelme

Itt láthatók a készülék által az ügynök nélküli függőségek elemzéséhez engedélyezett kapcsolatok adatai.

**Adatok** | **Parancsok** 
--- | ---
Helyi port | netstat 
Helyi IP-cím | netstat 
Távoli port | netstat 
Távoli IP-cím | netstat 
TCP-kapcsolatok állapota | netstat 
Aktív kapcsolatok száma | netstat
Folyamatazonosító  | netstat 
Folyamatnév | PS
Folyamat argumentumai | PS
Alkalmazásnév | dpkg vagy rpm


## <a name="collected-data---hyper-v"></a>Összegyűjtött adatok – Hyper-V

A készülék konfigurációs és teljesítménybeli metaadatokat gyűjt a Hyper-V környezetben futó kiszolgálókról.

### <a name="metadata"></a>Metaadatok
Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a kiszolgálók készen állnak-e az Azure-ba való Migrálás, a megfelelő méretű kiszolgálók és a csomagok költségeire. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

Itt látható a készülék által gyűjtött és az Azure-ba küldött kiszolgálói metaadatok teljes listája.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
**Kiszolgáló adatai** | 
BIOS sorozatszáma | Msvm_BIOSElement | BIOSSerialNumber
Virtuális gép típusa (1. vagy 2. generációs) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Virtuális gép megjelenítendő neve | Msvm_VirtualSystemSettingData | ElementName
VM-verzió | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bájt) | Msvm_MemorySettingData | VirtualQuantity
A virtuális gép által felhasználható maximális memória | Msvm_MemorySettingData | Korlát
Dinamikus memória engedélyezve | Msvm_MemorySettingData | DynamicMemoryEnabled
Operációs rendszer neve/verziója/teljes tartományneve | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems
Virtuális gép energiaellátási állapota | Msvm_ComputerSystem | EnabledState
**/Lemez adatai** | 
Lemez azonosítója | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Típus
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtuális merevlemez szülőjének | Msvm_VirtualHardDiskSettingData | ParentPath
**Hálózati adapter adatai** | 
IP-címek (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP engedélyezve (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-azonosító (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Hálózati adapter MAC-címe (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
NIC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting-adathalmazok | InstanceID
NIC MAC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting-adathalmazok | Cím

### <a name="performance-data"></a>Teljesítményadatok

Itt láthatók azok a kiszolgálói teljesítményadatok, amelyeket a készülék gyűjt és küld az Azure-nak.

**Teljesítményszámláló osztálya** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Hyper-V hypervisor virtuális processzor | Vendég futási ideje (%) | Ajánlott virtuális gép mérete/díja
Hyper-V dinamikus memória VM | Aktuális nyomás (%)<br/> Vendég látható fizikai memóriája (MB) | Ajánlott virtuális gép mérete/díja
Hyper-V virtuális tárolóeszköz | Olvasási bájt/másodperc | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hyper-V virtuális tárolóeszköz | Írási bájt/másodperc | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hyper-V Virtual Network adapter | Fogadott bájtok/másodperc | A virtuális gép méretének kiszámítása
Hyper-V Virtual Network adapter | Elküldett bájtok/másodperc | A virtuális gép méretének kiszámítása

- A CPU-kihasználtság a virtuális GÉPHEZ csatolt összes virtuális processzorhoz tartozó összes használat összege.
- A memória kihasználtsága (aktuális nyomás * vendég látható fizikai memória)/100.
- A rendszer a lemez-és hálózati kihasználtsági értékeket a felsorolt Hyper-V teljesítményszámlálók alapján gyűjti.


## <a name="collected-data---physical"></a>Összegyűjtött adatok – fizikai

A berendezés konfigurációs és teljesítménybeli metaadatokat gyűjt a helyszínen futó fizikai vagy virtuális kiszolgálókról.

### <a name="metadata"></a>Metaadatok

Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a kiszolgálók készen állnak-e az Azure-ba való Migrálás, a megfelelő méretű kiszolgálók és a csomagok költségeire. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

### <a name="windows-server-metadata"></a>Windows Server-metaadatok

Itt látható a Windows Server metaadatainak teljes listája, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
FQDN | Win32_ComputerSystem | Tartomány, név, PartOfDomain
Processzor magjainak száma | Win32_PRocessor | NumberOfCores
Lefoglalt memória | Win32_ComputerSystem | TotalPhysicalMemory
BIOS sorozatszáma | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
Rendszerindítás típusa | Win32_DiskPartition | A (z) = **GPT: System** for EFI/BIOS típusú partíció keresése
Operációs rendszer neve | Win32_OperatingSystem | Képaláírás
Operációs rendszer verziója |Win32_OperatingSystem | Verzió
Operációs rendszer architektúrája | Win32_OperatingSystem | OSArchitecture
Lemezek száma | Win32_DiskDrive | Modell, méret, DeviceID, MediaType, név
Lemezméret | Win32_DiskDrive | Méret
Hálózati adapterek listája | Win32_NetworkAdapterConfiguration | Leírás, index
Hálózati adapter IP-címe | Win32_NetworkAdapterConfiguration | IPAddress
Hálózati adapter MAC-címe | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux Server-metaadatok

Itt látható a Linux-kiszolgálói metaadatok teljes listája, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **Parancsok** 
--- | --- 
FQDN | cat/proc/sys/kernel/hostname, állomásnév-f
Processzor magjainak száma |  /proc/cpuinfo \| awk "/^ processzor/{print $3}" \| WC-l
Lefoglalt memória | cat/proc/meminfo \| grep MemTotal \| awk "{printf"%. 0f ", $2/1024}"
BIOS sorozatszáma | lshw \| grep "Serial:" \| Head-N1 \| awk "{Print $2}" <br/> /usr/sbin/dmidecode-t 1 \| grep "Serial" \| awk "{$1 =" "; $2 =" "; Print}"
BIOS GUID | Cat/sys/Class/DMI/ID/product_uuid
Rendszerindítás típusa | [-d/sys/firmware/EFI]  && echo EFI \| \| echo BIOS
Operációs rendszer neve/verziója | Ezeket a fájlokat az operációs rendszer verziójához és nevéhez is elérjük:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Operációs rendszer architektúrája | Uname – m
Lemezek száma | fdisk-l \| egrep "Disk. * Bytes" \| awk "{Print $2}" \| Cut-F1-d ":"
Rendszerindító lemez | DF/boot \| sed-n 2p \| awk {Print $1}
Lemezméret | fdisk-l \| egrep ' Disk. * bájtok ' \| egrep $Disk: \| awk ' {Print $5} '
Hálózati adapterek listája | IP-o-4 cím a \| (z) {print $2} awk megjelenítése
Hálózati adapter IP-címe | IP-cím megjelenítése $nic \| grep inet \| awk ' {Print $2} ' \| Cut-F1-d "/" 
Hálózati adapter MAC-címe | IP-cím megjelenítése $nic \| grep-éter  \| awk ({print $2})

### <a name="windows-performance-data"></a>Windows-teljesítményadatok

Az alábbi Windows Server teljesítményadatokat gyűjti és küldi el az Azure-nak.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
Processzorhasználat | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Memóriahasználat | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Hálózati adapterek száma | Win32_PerfFormattedData_Tcpip_NetworkInterface | A hálózati eszközök számának beolvasása.
Fogadott adatmennyiség/NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Továbbított adatok száma hálózati adapteren | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Lemezek száma | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Lemezek száma
Lemez adatai | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linuxos teljesítményadatok

Itt látható a Linux-kiszolgálói teljesítményadatok, amelyeket a készülék gyűjt és küld az Azure-nak.

**Adatok** | **Parancsok** 
--- | --- 
Processzorhasználat | cat/proc/stat/| GREP "CPU"/proc/stat
Memóriahasználat | ingyenes \| grep mem \| awk {Print $3/$ 2 * 100,0}
Hálózati adapterek száma | lshw-Class hálózati \| grep ETH [0-60] \| WC-l
Fogadott adatmennyiség/NIC | Cat/sys/Class/net/ETH $ NIC/Statistics/rx_bytes
Továbbított adatok száma hálózati adapteren | Cat/sys/Class/net/ETH $ NIC/Statistics/tx_bytes
Lemezek száma | fdisk-l \| egrep "Disk. * Bytes" \| awk "{Print $2}" \| Cut-F1-d ":"
Lemez adatai | cat/proc/diskstats


## <a name="appliance-upgrades"></a>Berendezések frissítése

A berendezés frissült, mivel a készüléken futó Azure Migrate szolgáltatások frissülnek. Ez automatikusan megtörténik, mivel alapértelmezés szerint engedélyezve van az automatikus frissítés a készüléken. Az alapértelmezett beállítás módosításával manuálisan frissítheti a berendezési szolgáltatásokat.

### <a name="turn-off-auto-update"></a>Automatikus frissítés kikapcsolása

1. A készüléket futtató kiszolgálón nyissa meg a beállításszerkesztőt.
2. Navigáljon **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Az automatikus frissítés kikapcsolásához hozzon létre egy, a 0 DWORD értékű beállításkulcs- **frissítési** kulcsot.

    ![Beállításkulcs beállítása](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Automatikus frissítés bekapcsolása

Az automatikus frissítés bekapcsolható a következő módszerek bármelyikével:

- Az AutoUpdate beállításkulcs HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureApplianceból való törlésével.
- Az automatikus frissítés bekapcsolásához kattintson a **berendezések megtekintése** lehetőségre a legújabb frissítési ellenőrzésekben az **Előfeltételek beállítása** panelen.

A beállításkulcs törlése:

1. A készüléket futtató gépen nyissa meg a beállításszerkesztőt.
2. Navigáljon **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Törölje az **automatikus frissítés** kikapcsolásához korábban létrehozott beállításkulcsot.

A berendezés Configuration Manager bekapcsolásához a felderítés befejezése után:

1. A készülék Configuration Manager felületén lépjen az **Előfeltételek beállítása** panelre.
2. A legújabb frissítések esetében kattintson a **készülék megtekintése** elemre, majd az automatikus frissítés bekapcsolásához kattintson a hivatkozásra.

    ![Automatikus frissítések bekapcsolása](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>A Appliance Services verziójának keresése

Az alábbi módszerek bármelyikével ellenőrizhető a Appliance Services-verzió:

- A készülék Configuration Manager felületén lépjen az **Előfeltételek beállítása** panelre.
- A készülék számítógépén, a **Vezérlőpult**  >  **programok és szolgáltatások** paneljén.

A készülék Configuration managerének beadásához:

1. A készülék Configuration Manager felületén lépjen az **Előfeltételek beállítása** panelre.
2. A legújabb frissítések keresése területen kattintson a **berendezés-szolgáltatások megtekintése** elemre.

    ![Verzió keresése](./media/migrate-appliance/versions.png)

A Vezérlőpulton való ellenőrzéshez:

1. A készüléken kattintson a   >  **Vezérlőpult**  >  **programok és szolgáltatások** indítása elemére.
2. A listában keresse meg a berendezés-szolgáltatások verzióját.

    ![A Vezérlőpult verziójának ellenőrzése](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Régebbi verzió manuális frissítése

Ha valamelyik szolgáltatáshoz egy régebbi verziót futtat, akkor el kell távolítania a szolgáltatást, és manuálisan kell frissítenie a legújabb verzióra.

1. A készülék legújabb verziójának megkereséséhez [töltse le](https://aka.ms/latestapplianceservices) a LatestComponents.jsfájlt.
2.    A letöltés után nyissa meg a LatestComponents.jsfájlt a Jegyzettömbben.
3. Keresse meg a legújabb szolgáltatási verziót a fájlban, és a letöltési hivatkozást. Például:

    "Név": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Töltse le egy elavult szolgáltatás legújabb verzióját a fájl letöltési hivatkozásával.
5. A letöltés után futtassa a következő parancsot egy rendszergazdai parancsablakban a letöltött MSI integritásának ellenőrzéséhez.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Például: C: \> Certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Győződjön meg arról, hogy a parancs kimenete megfelel a szolgáltatáshoz tartozó kivonatoló érték bejegyzésének (például a fenti MD5-kivonat értéke).
6. Most futtassa az MSI-t a szolgáltatás telepítéséhez. Ez egy csendes telepítés, és a telepítés befejezése után bezárul.
7. A telepítés befejezése után keresse meg a szolgáltatás verziószámát a **Vezérlőpult**  >  **programok és szolgáltatások** paneljén. A szolgáltatási verziót most frissíteni kell a JSON-fájlban látható legújabb verzióra.



## <a name="next-steps"></a>Következő lépések

- [Ismerje meg, hogyan](how-to-set-up-appliance-vmware.md) állíthatja be a készüléket a VMware rendszerhez.
- [Ismerje meg, hogyan](how-to-set-up-appliance-hyper-v.md) állíthatja be a készüléket a Hyper-V-hez.
- [Megtudhatja, hogyan](how-to-set-up-appliance-physical.md) állíthatja be a készüléket fizikai kiszolgálókhoz.

