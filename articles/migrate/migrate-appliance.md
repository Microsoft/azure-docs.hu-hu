---
title: Azure Migrate-berendezés
description: A Azure Migrate készülék támogatásának összegzését tartalmazza.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 17ac10fd4e48043b79db5448fad2f29c5eb3f2a3
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986819"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk összefoglalja az Azure Migrate berendezés előfeltételeinek és támogatási követelményeit. 

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate készüléket a következő esetekben használja a rendszer.

**Forgatókönyv** | **Eszköz** | **Alkalmazási cél** 
--- | --- | ---
**VMware virtuális gép értékelése** | Azure Migrate: kiszolgáló értékelése | VMware virtuális gépek felderítése<br/><br/> Gépi alkalmazások és függőségek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.
**VMware VM ügynök nélküli Migrálás** | Azure Migrate: kiszolgáló áttelepítése | VMware virtuális gépek felderítése <br/><br/> VMware virtuális gépek replikálása ügynök nélküli áttelepítéssel.
**Hyper-V virtuális gépek felmérése** | Azure Migrate: kiszolgáló értékelése | Hyper-V virtuális gépek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.
**Fizikai gépek felmérése** |  Azure Migrate: kiszolgáló értékelése |  Fedezze fel a fizikai kiszolgálókat (vagy a fizikai kiszolgálóként kezelt virtuális gépeket).<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A készülék több módszer használatával is üzembe helyezhető:

- A készülék üzembe helyezhető a VMware virtuális gépek és a Hyper-V virtuális gépek (a VMware-hez vagy a virtuális merevlemezhez a Hyper-V-hez készült PETESEJTJEInek sablonja) használatával.
- Ha nem szeretne sablont használni, PowerShell-parancsfájl használatával telepítheti a készüléket a VMware vagy a Hyper-V rendszerbe.
- Azure Government a készüléket parancsfájl használatával kell központilag telepíteni.
- Fizikai kiszolgálók esetében a készüléket mindig egy parancsfájl használatával helyezheti üzembe.
- A letöltési hivatkozások az alábbi táblázatokban érhetők el.


## <a name="appliance---vmware"></a>Készülék – VMware 

A következő táblázat összefoglalja a VMware Azure Migrate készülékre vonatkozó követelményeit.

**Követelmény** | **VMware** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék webalkalmazását, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés összetevői** | A berendezés a következő összetevőkből áll:<br/><br/> - **Felügyeleti alkalmazás**: ez egy webalkalmazás felhasználói bevitelhez a készülék üzembe helyezése során. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/> - **Felderítési ügynök**: az ügynök a számítógép-konfigurációs adatokat gyűjti. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Gyűjtő ügynök**: az ügynök teljesítményadatokat gyűjt. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Automatikus frissítési szolgáltatás**: a készülék összetevőinek frissítése (24 óránként fut).<br/>- **DRA-ügynök**: a virtuális gépek replikálását és a replikált számítógépek és az Azure közötti kommunikáció koordinálását koordinálja. Csak akkor használható, ha a VMware virtuális gépeket az Azure-ba replikálja ügynök nélküli Migrálás használatával.<br/>- **Átjáró**: replikált adatokat küld az Azure-nak. Csak akkor használható, ha a VMware virtuális gépeket az Azure-ba replikálja ügynök nélküli Migrálás használatával.
**Támogatott központi telepítés** | Üzembe helyezés VMware virtuális gépen a petesejtek sablonnal.<br/><br/> Üzembe helyezés VMware-alapú virtuális gépként vagy fizikai gépen PowerShell-telepítési parancsfájl használatával.
**Projekt-támogatás** |  Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítési korlátok** | A készülékek akár 10 000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy berendezés egyetlen vCenter Serverhoz tud csatlakozni.
**PETESEJT-sablon** | Letöltés a portálról vagy [innen](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> A letöltési méret 11,9 GB.<br/><br/> A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**PowerShell-parancsprogram** | Tekintse meg ezt a [cikket](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Szoftver/hardver** |  A készüléknek a gépen kell futnia a Windows Server 2016, 32-GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárolás és egy külső virtuális kapcsoló alapján.<br/> A berendezéshez közvetlenül vagy proxyn keresztül kell internet-hozzáférést igényelni.<br/><br/> Ha a készüléket VMware virtuális gépen futtatja, elegendő erőforrásra van szüksége a vCenter Server egy olyan virtuális gép lefoglalásához, amely megfelel a követelményeknek.<br/><br/> Ha a készüléket fizikai gépen futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek.
**VMware-követelmények** | Ha a készüléket VMware virtuális gépként telepíti, akkor azt egy 5,5-es vagy újabb verziót futtató ESXi-gazdagépre kell telepíteni.<br/><br/> vCenter Server fut 5,5, 6,0, 6,5 vagy 6,7.
**VDDK (ügynök nélküli áttelepítés)** | Ha a készüléket VMware virtuális gépként telepíti, és ügynök nélküli áttelepítést futtat, akkor a VMware vSphere VDDK telepítve kell lennie a készülék virtuális gépén.
**Kivonatoló érték – petesejtek** | [Ellenőrizze](tutorial-discover-vmware.md#verify-security) a petesejt-sablon kivonatának értékeit.
**Kivonatoló érték – PowerShell-parancsfájl** | [Ellenőrizze](deploy-appliance-script.md#verify-file-security) a PowerShell-parancsfájl kivonatának értékeit.




## <a name="appliance---hyper-v"></a>Készülék – Hyper-V

**Követelmény** | **Hyper-V** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék webalkalmazását, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés összetevői** | A berendezés a következő összetevőkből áll:<br/><br/>- **Felügyeleti alkalmazás**: ez egy webalkalmazás felhasználói bevitelhez a készülék üzembe helyezése során. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/> - **Felderítési ügynök**: az ügynök a számítógép-konfigurációs adatokat gyűjti. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Gyűjtő ügynök**: az ügynök teljesítményadatokat gyűjt. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Automatikus frissítési szolgáltatás**: a készülék összetevőinek frissítése (24 óránként fut).
**Támogatott központi telepítés** | Hyper-V virtuális gépként történő üzembe helyezése VHD-sablonnal.<br/><br/> Telepítsen Hyper-V virtuális gépet vagy fizikai gépet PowerShell telepítési parancsfájl használatával.
**Projekt-támogatás** |  Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítési korlátok** | Egy készülék legfeljebb 5000 Hyper-V virtuális gépet képes felderíteni.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**VHD-sablon** | Tömörített mappa, beleértve a VHD-t. Töltse le a portálról [, vagy innen.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> A letöltési méret 8,91 GB.<br/><br/> A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**PowerShell-parancsprogram** | Tekintse meg ezt a [cikket](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Szoftver/hardver** _   |  A készüléknek a gépen kell futnia a Windows Server 2016, 16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárterület és egy külső virtuális kapcsoló alapján.<br/> A készüléknek statikus vagy dinamikus IP-címnek kell lennie, és közvetlenül vagy proxyn keresztül kell internet-hozzáférést biztosítania.<br/><br/> Ha a berendezést Hyper-V virtuális gépként futtatja, elegendő erőforrásra van szüksége a Hyper-V-gazdagépen a hardverkövetelmények lefoglalásához.<br/><br/> Ha a készüléket fizikai gépen futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek. 
A *Hyper-V követelményei** | Ha a készüléket a VHD sablonnal telepíti, a Azure Migrate által biztosított készülék virtuális gépe a Hyper-V VM 5,0-es verziója.<br/><br/> A Hyper-V-gazdagépen Windows Server 2012 R2 vagy újabb rendszernek kell futnia. 
**Kivonatoló érték – VHD** | [Ellenőrzés](tutorial-discover-hyper-v.md#verify-security) VHD-sablon kivonatának értékei.
**Kivonatoló érték – PowerShell-parancsfájl** | [Ellenőrizze](deploy-appliance-script.md#verify-file-security) a PowerShell-parancsfájl kivonatának értékeit.


## <a name="appliance---physical"></a>Berendezés – fizikai

**Követelmény** | **Fizikai** 
--- | ---
**Engedélyek** | Ha helyileg vagy távolról szeretné elérni a készülék webalkalmazását, rendelkeznie kell egy helyi vagy tartományi felhasználói fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a berendezés-kiszolgálón.
**Berendezés összetevői** | A berendezés a következő összetevőkből áll: <br/><br/> - **Felügyeleti alkalmazás**: ez egy webalkalmazás felhasználói bevitelhez a készülék üzembe helyezése során. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/> - **Felderítési ügynök**: az ügynök a számítógép-konfigurációs adatokat gyűjti. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Gyűjtő ügynök**: az ügynök teljesítményadatokat gyűjt. A gépeknek az Azure-ba való áttelepítéshez való kiértékeléséhez használatos.<br/>- **Automatikus frissítési szolgáltatás**: a készülék összetevőinek frissítése (24 óránként fut).
**Támogatott központi telepítés** | Telepítsen dedikált fizikai gépet vagy virtuális gépet egy PowerShell telepítési parancsfájl használatával. A szkript letölthető a portálról.
**Projekt-támogatás** |  Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítési korlátok** | A berendezések akár 1000 fizikai kiszolgálót is felfedezhetnek.
**PowerShell-parancsprogram** | Töltse le a szkriptet (AzureMigrateInstaller.ps1) egy tömörített mappában a portálról, vagy [innen.](https://go.microsoft.com/fwlink/?linkid=2140334) [További információ](tutorial-discover-physical.md).<br/><br/> A letöltési méret 85,8 MB.
**Szoftver/hardver** |  A készüléknek a gépen kell futnia a Windows Server 2016, 16 GB RAM, 8 vCPU, körülbelül 80 GB lemezes tárolással.<br/> A készüléknek statikus vagy dinamikus IP-címnek kell lennie, és közvetlenül vagy proxyn keresztül kell internet-hozzáférést biztosítania.<br/><br/> Ha a készüléket fizikai gépen futtatja, győződjön meg arról, hogy a Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelmények követelményeinek.<br/>_(Jelenleg a készülék központi telepítése csak Windows Server 2016 rendszeren támogatott.)_
**Kivonat értéke** | [Ellenőrizze](tutorial-discover-physical.md#verify-security) a PowerShell-parancsfájl kivonatának értékeit.

## <a name="url-access"></a>URL-hozzáférés

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés központi telepítésekor Azure Migrate a szükséges URL-címek kapcsolatát.
- Engedélyeznie kell a hozzáférést a lista összes URL-címéhez. Ha csak értékelést végez, kihagyhatja azokat az URL-címeket, amelyek a VMware ügynök nélküli Migrálás esetében kötelezőként vannak megjelölve.
-  Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.

### <a name="public-cloud-urls"></a>Nyilvános Felhőbeli URL-címek

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com <br/> *. office.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Azure Active Directory-(AD-) alkalmazásokat a berendezés számára a Azure Migratesal való kommunikációhoz.
management.azure.com | Azure AD-alkalmazások létrehozása a berendezés számára a Azure Migrate szolgáltatással való kommunikációhoz.
*.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése. Megjegyzés: Győződjön meg róla, hogy a replikálni kívánt gépek rendelkeznek hozzáféréssel.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz. Azure Migrate berendezés frissítéseihez használatos.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltéséről.
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
*.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*. vault.usgovcloudapi.net | A Azure Key Vault titkainak kezelése.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz. Azure Migrate berendezés frissítéseihez használatos.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltéséről.
*. servicebus.usgovcloudapi.net  | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*. hypervrecoverymanager.windowsazure.us | **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/> Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*. blob.core.usgovcloudapi.net  |  **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/>Adatok feltöltése a Storage-ba áttelepítéshez.
*. applicationinsights.us | A belső figyeléshez használt alkalmazás-naplók feltöltése.





## <a name="collected-data---vmware"></a>Összegyűjtött adatok – VMware

A készülék metaadatokat, teljesítményadatokat és függőségi elemzési adatokat gyűjt (ha az ügynök nélküli [függőségek elemzése](concepts-dependency-visualization.md) van használatban).

### <a name="metadata"></a>Metaadatok

Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a gépek és alkalmazások készen állnak-e az Azure-ba való áttelepítésre, a megfelelő méretű gépekre és alkalmazásokra, a csomagok költségeire és az alkalmazások függőségeinek elemzésére. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

Itt találja a készülék által gyűjtött és az Azure-ba küldött VMware VM-metaadatok teljes listáját.

**ADATOK** | **SZÁMLÁLÓ**
--- | --- 
**Gép részletei** | 
A virtuális gép azonosítója | vm.Config. Értékű 
a virtuális gép neve | vm.Config. név
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


### <a name="installed-apps-metadata"></a>Telepített alkalmazások metaadatainak

Az Application Discovery a telepített alkalmazásokat és az operációs rendszer adatait gyűjti.

#### <a name="windows-vm-apps-data"></a>Windowsos VM-alkalmazások – adatszolgáltatások

Itt láthatók a telepített alkalmazásadatok, amelyeket a készülék az alkalmazások felderítéséhez engedélyezett összes virtuális gépről gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok** | **Beállításjegyzékbeli hely** | **Kulcs**
--- | --- | ---
Alkalmazásnév  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Verzió  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Szolgáltató  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows rendszerű virtuális gép szolgáltatásainak adatvédelme

Itt találja azokat a funkciókat, amelyeket a készülék az alkalmazások felderítéséhez engedélyezett összes virtuális gépről gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok**  | **PowerShell-parancsmag** | **Tulajdonság**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Szolgáltatás típusa | Get-WindowsFeature  | FeatureType
Szülő  | Get-WindowsFeature  | Szülő

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM SQL Server metaadatok

Itt találja azokat az SQL Server-metaadatokat, amelyeket a készülék a Microsoft SQL Servert futtató virtuális gépekről gyűjt az alkalmazások felderítéséhez. Ezek az adatküldés az Azure-ba történik.

**Adatok**  | **Beállításjegyzékbeli hely**  | **Kulcs**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \ példány Names\SQL  | installedInstance
Kiadás  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Kiadás 
Szervizcsomag  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Verzió  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Verzió 

#### <a name="windows-vm-operating-system-data"></a>Windows rendszerű virtuális gép operációsrendszer-információi

Az operációs rendszer azon adatait, amelyeket a készülék az alkalmazások felderítéséhez engedélyezett összes virtuális gépre gyűjt. Ezek az adatküldés az Azure-ba történik.

Adatok  | WMI-osztály  | WMI-osztály tulajdonsága
--- | --- | ---
Name  | Win32_operatingsystem  | Képaláírás
Verzió  | Win32_operatingsystem  | Verzió
Architektúra  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux rendszerű virtuális gépekre vonatkozó alkalmazások

Itt láthatók a telepített alkalmazásadatok, amelyeket a készülék az alkalmazások felderítéséhez engedélyezett összes virtuális gépről gyűjt. A virtuális gép operációs rendszere alapján egy vagy több parancs fut. Ezek az adatküldés az Azure-ba történik.

Adatok  | Parancs
--- | --- 
Name | RPM, dpkg-Query, Snap
Verzió | RPM, dpkg-Query, Snap
Szolgáltató | RPM, dpkg-Query, Snap

#### <a name="linux-vm-operating-system-data"></a>Linux rendszerű virtuális gép operációsrendszer-információi

Az operációs rendszer azon adatait, amelyeket a készülék az alkalmazások felderítéséhez engedélyezett összes virtuális gépre gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok**  | **Parancs** 
--- | --- | ---
Name <br/> version | A következő fájlok közül egy vagy több összegyűjtése:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architektúra | uname


### <a name="app-dependencies-metadata"></a>Alkalmazás-függőségek metaadatai

Az ügynök nélküli függőségi elemzések összegyűjtik a kapcsolatokat és dolgozzák fel az adatokat.

#### <a name="windows-vm-app-dependencies-data"></a>Windows rendszerű virtuális gép alkalmazás függőségeinek adatvédelme

Itt láthatók azok a kapcsolatok, amelyeket a készülék az ügynök nélküli függőségek elemzéséhez engedélyezett összes virtuális gépről gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok** | **A parancs használatban van** 
--- | --- 
Helyi port | netstat
Helyi IP-cím | netstat
Távoli port | netstat
Távoli IP-cím | netstat
TCP-kapcsolatok állapota | netstat
Folyamatazonosító | netstat
Aktív kapcsolatok száma | netstat


Itt láthatók azok az adatok, amelyeket a készülék az ügynök nélküli függőségek elemzéséhez engedélyezett összes virtuális gépről gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
Folyamatnév | Win32_Process | ExecutablePath
Folyamat argumentumai | Win32_Process | CommandLine
Alkalmazásnév | Win32_Process | A ExecutablePath tulajdonság VersionInfo. ProductName paramétere

#### <a name="linux-vm-app-dependencies-data"></a>Linuxos VM-alkalmazás függőségeinek adatvédelme

Itt láthatók azok a kapcsolatok és folyamatok, amelyeket a készülék az ügynök nélküli függőségek elemzéséhez engedélyezett Linux rendszerű virtuális gépekről gyűjt. Ezek az adatküldés az Azure-ba történik.

**Adatok** | **A parancs használatban van** 
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

A készülék metaadatokat, teljesítményadatokat és függőségi elemzési adatokat gyűjt (ha az ügynök nélküli [függőségek elemzése](concepts-dependency-visualization.md) van használatban).

### <a name="metadata"></a>Metaadatok
Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a gépek és alkalmazások készen állnak-e az Azure-ba való áttelepítésre, a megfelelő méretű gépekre és alkalmazásokra, a csomagok költségeire és az alkalmazások függőségeinek elemzésére. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

Az alábbi lista tartalmazza a Hyper-V VM-metaadatok teljes listáját, amelyeket a készülék az Azure-ba gyűjt és küld.

**ADATOK** | **WMI-OSZTÁLY** | **WMI-OSZTÁLY TULAJDONSÁGA**
--- | --- | ---
**Gép részletei** | 
A BIOS sorozatszáma _ Msvm_BIOSElement | BIOSSerialNumber
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

Itt látható a virtuális gép által összegyűjtött és az Azure-ba küldött teljesítményadatok.

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

A készülék metaadatokat, teljesítményadatokat és függőségi elemzési adatokat gyűjt (ha az ügynök nélküli [függőségek elemzése](concepts-dependency-visualization.md) van használatban).

### <a name="windows-metadata"></a>Windows-metaadatok

Az Azure Migrate készülék által felderített metaadatok segítenek megállapítani, hogy a gépek és alkalmazások készen állnak-e az Azure-ba való áttelepítésre, a megfelelő méretű gépekre és alkalmazásokra, a csomagok költségeire és az alkalmazások függőségeinek elemzésére. A Microsoft nem használja ezeket az összes licenc-megfelelőségi naplózást.

Itt látható a Windows Server metaadatainak teljes listája, amelyet a készülék az Azure-ba gyűjt és küld.

**ADATOK** | **WMI-OSZTÁLY** | **WMI-OSZTÁLY TULAJDONSÁGA**
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

### <a name="linux-metadata"></a>Linux-metaadatok

Itt látható a Linux-kiszolgálói metaadatok teljes listája, amelyet a készülék az Azure-ba gyűjt és küld.

**ADATOK** | **LINUX** 
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

**Adatok** | **Linux** 
--- | --- 
Processzorhasználat | cat/proc/stat/| GREP "CPU"/proc/stat
Memóriahasználat | ingyenes \| grep mem \| awk {Print $3/$ 2 * 100,0}
Hálózati adapterek száma | lshw-Class hálózati \| grep ETH [0-60] \| WC-l
Fogadott adatmennyiség/NIC | Cat/sys/Class/net/ETH $ NIC/Statistics/rx_bytes
Továbbított adatok száma hálózati adapteren | Cat/sys/Class/net/ETH $ NIC/Statistics/tx_bytes
Lemezek száma | fdisk-l \| egrep "Disk. * Bytes" \| awk "{Print $2}" \| Cut-F1-d ":"
Lemez adatai | cat/proc/diskstats


## <a name="appliance-upgrades"></a>Berendezések frissítése

A készülék frissítve lett, mivel a készüléken futó Azure Migrate-ügynökök frissülnek. Ez automatikusan megtörténik, mivel alapértelmezés szerint engedélyezve van az automatikus frissítés a készüléken. Az alapértelmezett beállítás módosításával manuálisan frissítheti a berendezési szolgáltatásokat.

### <a name="turn-off-auto-update"></a>Automatikus frissítés kikapcsolása

1. A készüléket futtató gépen nyissa meg a beállításszerkesztőt.
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

Ha valamelyik összetevőnél régebbi verziót futtat, akkor el kell távolítania a szolgáltatást, és manuálisan kell frissítenie a legújabb verzióra.

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

