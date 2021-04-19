---
title: Azure Migrate-berendezés
description: Összegzést nyújt a Azure Migrate támogatásról.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714667"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk a Azure Migrate előfeltételeit és támogatási követelményeit foglalja össze.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate berendezést a következő forgatókönyvekben használjuk.

**Forgatókönyv** | **Eszköz** | **Itt használható:**
--- | --- | ---
**VMware-környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: Felderítés és értékelés | A VMware-környezetben futó kiszolgálók felderítése<br/><br/> A telepített szoftverleltár és az ügynök nélküli függőségelemzés felderítése, valamint a SQL Server és adatbázisok felderítése.<br/><br/> Gyűjtse össze a kiszolgálókonfiguráció és a teljesítmény metaadatait az értékelésekhez.
**VMware-környezetben futó kiszolgálók ügynök nélküli áttelepítése** | Azure Migrate:Server Migration | A VMware-környezetben futó kiszolgálók felderítése. <br/><br/> Kiszolgálók replikálása ügynökök telepítése nélkül.
**Hyper-V környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: Felderítés és értékelés | A Hyper-V-környezetben futó kiszolgálók felderítése.<br/><br/> Gyűjtse össze a kiszolgálókonfiguráció és a teljesítmény metaadatait az értékelésekhez.
**Helyszíni fizikai vagy virtualizált kiszolgálók felderítése és értékelése** |  Azure Migrate: Felderítés és értékelés |  Fizikai vagy virtualizált kiszolgálók felderítése a helyszínen.<br/><br/> Gyűjtse össze a kiszolgálókonfiguráció és a teljesítmény metaadatait az értékelésekhez.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A berendezés több módszerrel is üzembe helyezhető:

- A berendezés VMware- vagy Hyper-V-környezetben futó kiszolgálók sablonjának használatával helyezhető üzembe[(VMware esetén OVA-sablon,](how-to-set-up-appliance-vmware.md) [Hyper-V esetén VHD).](how-to-set-up-appliance-hyper-v.md)
- Ha nem szeretne sablont használni, üzembe helyezheti a berendezést VMware- vagy Hyper-V-környezetben egy [PowerShell-telepítő szkripttel.](deploy-appliance-script.md)
- A Azure Government üzembe kell helyeznie a berendezést egy PowerShell-telepítő szkript használatával. Az üzembe helyezés lépéseit itt [található.](deploy-appliance-script-government.md)
- A fizikai vagy virtualizált helyszíni kiszolgálók vagy bármely más felhő esetén a berendezést mindig Egy PowerShell-telepítő szkript használatával kell üzembe helyezni. Az üzembe helyezés lépéseit itt [található.](how-to-set-up-appliance-physical.md)
- A letöltési hivatkozások az alábbi táblázatokban érhetők el.

## <a name="appliance---vmware"></a>Berendezés – VMware

Az alábbi táblázat összefoglalja a Azure Migrate VMware-re vonatkozó követelményeit.

> [!Note]
> A VMware-SQL Server futó virtuális gépek és adatbázisok felderítése és értékelése jelenleg előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

**Követelmény** | **VMware**
--- | ---
**Engedélyek** | A berendezéskonfiguráció-kezelő helyi vagy távoli eléréséhez rendszergazdai jogosultságokkal rendelkező helyi vagy tartományi felhasználói fiókkal kell lennie a berendezéskiszolgálón.
**Berendezésszolgáltatások** | A berendezés a következő szolgáltatásokkal rendelkezik:<br/><br/> - **Berendezés konfigurációkezelője:** Ez egy webalkalmazás, amely konfigurálható a forrás adataival a kiszolgálók felderítésének és értékelésének elkezdéséhez.<br/> - **VMware-felderítési ügynök:** Az ügynök a kiszolgáló konfigurációs metaadatait gyűjti, amelyek felhasználhatók helyszíni értékelésként való létrehozáshoz.<br/>- **VMware-értékelési ügynök:** Az ügynök a kiszolgálói teljesítmény metaadatait gyűjti, amelyek segítségével teljesítményalapú értékelések hozhatók létre.<br/>- **Automatikus frissítési szolgáltatás:** A szolgáltatás naprakészen tartja a berendezésen futó összes ügynököt. 24 óránként automatikusan lefut.<br/>- **DRA-ügynök:** Összehangolja a kiszolgálóreplikációt, és koordinálja a replikált kiszolgálók és az Azure közötti kommunikációt. Csak akkor használatos, ha kiszolgálókat replikál az Azure-ba ügynök nélküli migrálás használatával.<br/>- **Átjáró:** Replikált adatokat küld az Azure-ba. Csak akkor használatos, ha kiszolgálókat replikál az Azure-ba ügynök nélküli migrálás használatával.<br/>- **SQL-felderítési és -értékelési ügynök:** elküldi a konfigurációs és SQL Server metaadatait az Azure-nak.
**Projektkorlátok** |  Egy berendezés csak egyetlen projektben regisztrálható.<br/> Egy projekthez több regisztrált berendezés is lehet.
**Felderítési korlátok** | A berendezés akár 10 000, egy kiszolgálón futó kiszolgálót is vCenter Server.<br/> A berendezés csatlakozhat egyetlen vCenter Server.
**Támogatott központi telepítés** | Üzembe helyezés új kiszolgálón futó vCenter Server OVA-sablon használatával.<br/><br/> Üzembe helyezés Windows Server 2016-ot futtató meglévő kiszolgálón PowerShell-telepítő szkript használatával.
**OVA-sablon** | Letöltés a projektből vagy [innen](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> A letöltés mérete 11,9 GB.<br/><br/> A letöltött berendezéssablonhoz Egy Windows Server 2016 értékelési licenc is jár, amely 180 napig érvényes.<br/>Ha a kiértékelési időszak hamarosan lejár, javasoljuk, hogy töltsön le és helyezzen üzembe egy új berendezést az OVA-sablonnal, vagy aktiválja a berendezéskiszolgáló operációs rendszerének licencét.
**OVA-ellenőrzés** | [Ellenőrizze a](tutorial-discover-vmware.md#verify-security) projektből letöltött OVA-sablont a kivonatértékek ellenőrzésével.
**PowerShell-parancsprogram** | Tekintse meg ezt a [cikket](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) arról, hogyan helyezhet üzembe egy berendezést a PowerShell-telepítő szkript használatával.<br/><br/> 
**Hardver- és hálózati követelmények** |  A berendezésnek Windows Server 2016- és 32 GB RAM-mal, 8 virtuálispu-val, körülbelül 80 GB lemezterülettel és külső virtuális kapcsolóval rendelkező kiszolgálón kell futnia.<br/> A berendezéshez közvetlenül vagy proxyn keresztüli internet-hozzáférés szükséges.<br/><br/> Ha OVA-sablonnal telepíti a berendezést, elegendő erőforrásra lesz szüksége a vCenter Server a hardverkövetelményeknek megfelelő kiszolgáló létrehozásához.<br/><br/> Ha a berendezést egy meglévő kiszolgálón futtatja, ellenőrizze, hogy Windows Server 2016 fut-e, és megfelel-e a hardverkövetelményeknek.<br/>_(A berendezés üzembe helyezése jelenleg csak Windows Server 2016 rendszeren támogatott.)_
**VMware-követelmények** | Ha a berendezést kiszolgálóként telepíti az vCenter Server-ban, akkor azt egy 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verziójú vCenter Server és egy 5.5-ös vagy újabb verziót futtató ESXi-gazdagépen kell üzembe helyezni.<br/><br/> 
**VDDK (ügynök nélküli migrálás)** | Ahhoz, hogy a berendezést kihasználhatja a kiszolgálók ügynök nélküli áttelepítéséhez, VMware vSphere VDDK-t telepíteni kell a berendezéskiszolgálóra.

## <a name="appliance---hyper-v"></a>Berendezés – Hyper-V

**Követelmény** | **Hyper-V**
--- | ---
**Engedélyek** | A berendezéskonfiguráció-kezelő helyi vagy távoli eléréséhez rendszergazdai jogosultságokkal rendelkező helyi vagy tartományi felhasználói fiókkal kell lennie a berendezéskiszolgálón.
**Berendezésszolgáltatások** | A berendezés a következő szolgáltatásokkal rendelkezik:<br/><br/> - **Berendezés konfigurációkezelője:** Ez egy webalkalmazás, amely konfigurálható a forrás adataival a kiszolgálók felderítésének és értékelésének elkezdéséhez.<br/> - **Felderítési ügynök:** Az ügynök a kiszolgáló konfigurációs metaadatait gyűjti, amelyek felhasználhatók helyszíni értékelésekként való létrehozáshoz.<br/>- **Értékelési ügynök:** Az ügynök gyűjti a kiszolgálói teljesítmény metaadatait, amelyek segítségével teljesítményalapú értékelések hozhatók létre.<br/>- **Automatikus frissítési szolgáltatás:** A szolgáltatás naprakészen tartja a berendezésen futó összes ügynököt. 24 óránként automatikusan fut.
**Projektkorlátok** |  Egy berendezés csak egyetlen projektben regisztrálható.<br/> Egy projekthez több regisztrált berendezés is lehet.
**Felderítési korlátok** | Egy berendezés legfeljebb 5000 Hyper-V-környezetben futó kiszolgáló felderítésére képes.<br/> Egy berendezés legfeljebb 300 Hyper-V-gazdagéphez csatlakozhat.
**Támogatott központi telepítés** | Üzembe helyezés Hyper-V-gazdagépen futó kiszolgálóként VHD-sablon használatával.<br/><br/> Üzembe helyezés Windows Server 2016-ot futtató meglévő kiszolgálón PowerShell-telepítő szkript használatával.
**VHD-sablon** | VhD-t tartalmazó zip-fájl. Töltse le a projektet vagy [innen:](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> A letöltés mérete 8,91 GB.<br/><br/> A letöltött berendezéssablonhoz Egy Windows Server 2016 értékelési licenc is jár, amely 180 napig érvényes.<br/> Ha a kiértékelési időszak hamarosan lejár, javasoljuk, hogy töltsön le és helyezzen üzembe egy új berendezést, vagy aktiválja a berendezéskiszolgáló operációs rendszerének licencét.
**VHD-ellenőrzés** | [Ellenőrizze a](tutorial-discover-hyper-v.md#verify-security) projektből letöltött VHD-sablont a kivonatértékek ellenőrzésével.
**PowerShell-parancsprogram** | A berendezés [](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) PowerShell-telepítő szkript használatával való üzembe helyezéséről ebben a cikkben olvashat.<br/>
**Hardver- és hálózati követelmények**  |  A berendezésnek Windows Server 2016- és 16 GB RAM-mal, 8 virtuálispu-val, körülbelül 80 GB lemezterülettel és külső virtuális kapcsolóval rendelkező kiszolgálón kell futnia.<br/> A berendezésnek statikus vagy dinamikus IP-címre van szüksége, és internet-hozzáférést igényel közvetlenül vagy proxyn keresztül.<br/><br/> Ha a berendezést Egy Hyper-V-gazdagépen futó kiszolgálóként futtatja, elegendő erőforrásra lesz szüksége a gazdagépen a hardverkövetelményeknek megfelelő kiszolgáló létrehozásához.<br/><br/> Ha a berendezést egy meglévő kiszolgálón futtatja, ellenőrizze, hogy Windows Server 2016 fut-e, és megfelel-e a hardverkövetelményeknek.<br/>_(A berendezés üzembe helyezése jelenleg csak Windows Server 2016 rendszeren támogatott.)_
**A Hyper-V követelményei** | Ha a berendezést a VHD-sablonnal telepíti, az Azure Migrate a Hyper-V virtuális gép 5.0-s verziója lesz.<br/><br/> A Hyper-V-gazdagépen Windows Server 2012 R2 vagy újabb rendszernek kell futnia.

## <a name="appliance---physical"></a>Berendezés – Fizikai

**Követelmény** | **Fizikai**
--- | ---
**Engedélyek** | A berendezéskonfiguráció-kezelő helyi vagy távoli eléréséhez rendszergazdai jogosultságokkal rendelkező helyi vagy tartományi felhasználói fiókkal kell lennie a berendezéskiszolgálón.
**Berendezésszolgáltatások** | A berendezés a következő szolgáltatásokkal rendelkezik:<br/><br/> - **Berendezés konfigurációkezelője:** Ez egy webalkalmazás, amely konfigurálható a kiszolgálók felderítésének és értékelésének a forrás adataival.<br/> - **Felderítési ügynök:** Az ügynök a kiszolgáló konfigurációs metaadatait gyűjti, amelyek felhasználhatók helyszíni értékelésekként való létrehozáshoz.<br/>- **Értékelési ügynök:** Az ügynök gyűjti a kiszolgálói teljesítmény metaadatait, amelyek segítségével teljesítményalapú értékelések hozhatók létre.<br/>- **Automatikus frissítési szolgáltatás:** A szolgáltatás naprakészen tartja a berendezésen futó összes ügynököt. 24 óránként automatikusan lefut.
**Projektkorlátok** |  Berendezés csak egyetlen projektben regisztrálható.<br/> Egy projekthez több regisztrált berendezés is lehet.<br/>
**Felderítési korlátok** | A berendezés akár 1000 fizikai kiszolgálót is felderíthet.
**Támogatott központi telepítés** | Üzembe helyezés Windows Server 2016-ot futtató meglévő kiszolgálón PowerShell-telepítő szkript használatával.
**PowerShell-parancsprogram** | Töltse le a szkriptet (AzureMigrateInstaller.ps1) egy zip-fájlba a projektből vagy [innen.](https://go.microsoft.com/fwlink/?linkid=2140334) [További információ](tutorial-discover-physical.md).<br/><br/> A letöltés mérete 85,8 MB.
**Szkript ellenőrzése** | [Ellenőrizze a](tutorial-discover-physical.md#verify-security) projektből letöltött PowerShell-telepítő szkriptjét a kivonatértékek ellenőrzésével.
**Hardver- és hálózati követelmények** |  A berendezésnek Windows Server 2016, 16 GB RAM, 8 virtuálispu-val, körülbelül 80 GB lemezterülettel rendelkező kiszolgálón kell futnia.<br/> A berendezésnek statikus vagy dinamikus IP-címre van szüksége, és közvetlenül vagy proxyn keresztüli internet-hozzáférésre van szüksége.<br/><br/> Ha a berendezést egy meglévő kiszolgálón futtatja, győződjön meg arról, hogy Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelményeknek.<br/>_(A berendezés üzembe helyezése jelenleg csak Windows Server 2016 rendszeren támogatott.)_

## <a name="url-access"></a>URL-hozzáférés

A Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés üzembe helyezésekor a Azure Migrate ellenőrzi a szükséges URL-címeket.
- Engedélyeznie kell a hozzáférést a listában található összes URL-címhez. Ha csak értékelést ad, kihagyhatja a VMware ügynök nélküli áttelepítéséhez kötelezőként megjelölt URL-címeket.
- Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, győződjön meg arról, hogy a proxy feloldja az URL-címek kiása közben kapott CNAME-rekordokat.

### <a name="public-cloud-urls"></a>Nyilvános felhőBELI URL-címek

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon Azure Active Directory (AD) alkalmazásokat a berendezéshez a Azure Migrate.
management.azure.com | Hozzon létre Azure AD-alkalmazásokat a berendezéshez a Azure Migrate.
*.services.visualstudio.com | Belső monitorozáshoz használt berendezésnaplók feltöltése.
*.vault.azure.net | Titkos kulcsok kezelése a Azure Key Vault.<br/> Megjegyzés: Győződjön meg arról, hogy a replikálni szükséges kiszolgálók hozzáférnek ehhez.
aka.ms/* | Hozzáférés engedélyezése más néven hivatkozásokhoz; A a készülékszolgáltatások legújabb frissítésének letöltésére és telepítésére használható.
download.microsoft.com/download | Letöltések engedélyezése a Microsoft letöltőközpontból.
*.servicebus.windows.net | A berendezés és a Azure Migrate közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Csatlakozzon Azure Migrate szolgáltatás URL-címhez.
*.hypervrecoverymanager.windowsazure.com | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> Csatlakozzon Azure Migrate szolgáltatás URL-címhez.
*.blob.core.windows.net |  **VMware ügynök nélküli migráláshoz használatos**<br/><br/>Adatok feltöltése a tárolóba migráláshoz.

### <a name="government-cloud-urls"></a>Kormányzati felhőBELI URL-címek

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.us  | Lépjen az Azure Portalra.
graph.windows.net | Jelentkezzen be az Azure-előfizetésbe.
login.microsoftonline.us  | Hozzon Azure Active Directory (AD) alkalmazásokat a berendezéshez a Azure Migrate.
management.usgovcloudapi.net | Hozzon létre Azure AD-alkalmazásokat a berendezéshez, hogy kommunikáljon Azure Migrate szolgáltatással.
*.services.visualstudio.com | Töltse fel a belső monitorozáshoz használt berendezésnaplókat.
*.vault.usgovcloudapi.net | Titkos kulcsok kezelése a Azure Key Vault.
aka.ms/* | Hozzáférés engedélyezése más néven hivatkozásokhoz; A a készülékszolgáltatások legújabb frissítésének letöltésére és telepítésére használható.
download.microsoft.com/download | A Microsoft letöltőközpontból való letöltések engedélyezése.
*.servicebus.usgovcloudapi.net  | A berendezés és a Azure Migrate közötti kommunikáció.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Csatlakozás Azure Migrate szolgáltatás URL-címhez.
*.hypervrecoverymanager.windowsazure.us | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> Csatlakozás Azure Migrate szolgáltatás URL-címhez.
*.blob.core.usgovcloudapi.net  |  **VMware ügynök nélküli migráláshoz használatos**<br/><br/>Adatok feltöltése a tárolóba migráláshoz.
*.applicationinsights.us | Belső monitorozáshoz használt berendezésnaplók feltöltése.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>Nyilvános felhőBELI URL-címek a privát kapcsolati kapcsolatokhoz

A berendezésnek a következő URL-címekhez kell hozzáférnie (közvetlenül vagy proxyn keresztül) privát kapcsolati hozzáférésen keresztül és felett. 

**URL-cím** | **Részletek**  
--- | --- | 
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon Azure Active Directory (AD) alkalmazásokat a berendezéshez a Azure Migrate.
management.azure.com | Hozzon létre Azure AD-alkalmazásokat a berendezéshez a Azure Migrate.
*.services.visualstudio.com (nem kötelező) | Belső monitorozáshoz használt berendezésnaplók feltöltése.
aka.ms/* (nem kötelező) | Hozzáférés engedélyezése más néven hivatkozásokhoz; A a készülékszolgáltatások legújabb frissítésének letöltésére és telepítésére használható.
download.microsoft.com/download | Letöltések engedélyezése a Microsoft letöltőközpontból.
*.servicebus.windows.net | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> A berendezés és a Azure Migrate közötti kommunikáció.
*.vault.azure.net | **A VMware ügynök nélküli migráláshoz használatos**<br/><br/>  Győződjön meg arról, hogy a replikálni szükséges kiszolgálók hozzáférnek ehhez.
*.hypervrecoverymanager.windowsazure.com | **A VMware ügynök nélküli migráláshoz használatos**<br/><br/> Csatlakozás Azure Migrate szolgáltatás URL-címhez.
*.blob.core.windows.net |  **A VMware ügynök nélküli migráláshoz használatos**<br/><br/>Adatok feltöltése a tárolóba migráláshoz.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>Kormányzati felhőbeli URL-címek a privát kapcsolathoz való csatlakozáshoz   

A berendezésnek a következő URL-címekhez kell hozzáférnie (közvetlenül vagy proxyn keresztül) a privát kapcsolat elérésén keresztül és felett. 

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.us  | Lépjen az Azure Portalra.
graph.windows.net | Jelentkezzen be az Azure-előfizetésbe.
login.microsoftonline.us  | Hozzon Azure Active Directory (AD) alkalmazásokat a berendezéshez a Azure Migrate.
management.usgovcloudapi.net | Hozzon létre Azure AD-alkalmazásokat a berendezéshez, hogy kommunikáljon Azure Migrate szolgáltatással.
*.services.visualstudio.com (nem kötelező) | Töltse fel a belső monitorozáshoz használt berendezésnaplókat.
aka.ms/* (nem kötelező) | Hozzáférés engedélyezése más néven hivatkozásokhoz; A a készülékszolgáltatások legújabb frissítésének letöltésére és telepítésére használható.
download.microsoft.com/download | A Microsoft letöltőközpontból való letöltések engedélyezése.
*.servicebus.usgovcloudapi.net  | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> A berendezés és a Azure Migrate közötti kommunikáció. 
*.vault.usgovcloudapi.net | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> Titkos kulcsok kezelése a Azure Key Vault.
*.hypervrecoverymanager.windowsazure.us | **VMware ügynök nélküli migráláshoz használatos**<br/><br/> Csatlakozzon Azure Migrate szolgáltatás URL-címhez.
*.blob.core.usgovcloudapi.net  |  **VMware ügynök nélküli migráláshoz használatos**<br/><br/>Adatok feltöltése a tárolóba migráláshoz.
*.applicationinsights.us (nem kötelező) | Belső monitorozáshoz használt berendezésnaplók feltöltése.  

## <a name="collected-data---vmware"></a>Összegyűjtött adatok – VMware

A berendezés konfigurációs metaadatokat, teljesítmény-metaadatokat és kiszolgálófüggőség-adatokat gyűjt (ha ügynök nélküli [függőségelemzést](concepts-dependency-visualization.md) használ).

### <a name="metadata"></a>Metaadatok

A Azure Migrate berendezés által felderített metaadatok segítségével eldöntheti, hogy a kiszolgálók készen állnak-e az Azure-ba való migrálásra, megfelelő méretű kiszolgálókat, csomagköltségeket és az alkalmazásfüggőségeket elemzi. A Microsoft ezeket az adatokat semmilyen licencelési auditban nem használja fel.

Itt található a berendezés által gyűjtött és az Azure-ba küldött kiszolgálói metaadatok teljes listája.

**Adatok** | **Counter**
--- | ---
**Kiszolgáló adatai** |
Kiszolgálóazonosító | vm.Config. InstanceUuid (Példány)Uid
Kiszolgálónév | vm.Config. név
vCenter Server azonosítója | VMwareClient.Instance.Uuid
Kiszolgáló leírása | vm.Summary.Config. Megjegyzés
Licenctermék neve | vm.Client.ServiceContent.About.LicenseProductName
Operációs rendszer típusa | vm.SummaryConfig.GuestFullName
Rendszerindítás típusa | vm.Config. Firmware
Magok száma | vm.Config. Hardware.NumCPU
Memória (MB) | vm.Config. Hardware.MemoryMB
Lemezek száma | vm.Config. Hardware.Device.ToList(). FindAll(x => értéke VirtualDisk).count
Lemezméretek listája | vm.Config. Hardware.Device.ToList(). FindAll(x => is VirtualDisk)
Hálózati adapterek listája | vm.Config. Hardware.Device.ToList(). FindAll(x => értéke VirtualEthernet).count
Processzorhasználat | cpu.usage.average
Memóriahasználat |mem.usage.average
**Lemezenkénti részletek** |
Lemezkulcs értéke | Lemez. Kulcs
Dikunit-szám | Lemez. UnitNumber (Egységszám)
Lemezvezérlő kulcsértéke | Lemez. ControllerKey.Value
Kiépítve gigabájtok | virtualDisk.DeviceInfo.Summary
Lemez neve | Lemezzel létrehozott érték. UnitNumber, lemez. Kulcs, lemez. ControllerKey.VAlue
Olvasási műveletek száma másodpercenként | virtualDisk.numberReadAveraged.average
Írási műveletek száma másodpercenként | virtualDisk.numberWriteAveraged.average
Olvasási átviteli sebesség (MB/s) | virtualDisk.read.average
Írási átviteli sebesség (MB/s) | virtualDisk.write.average
**Hálózati adapterenkénti részletek** |
Hálózati adapter neve | Nic. Kulcs
MAC-cím | ((VirtualEthernetCard)nic). MacAddress (Mac-cím)
IPv4-címek | vm.Guest.Net
IPv6-címek | vm.Guest.Net
Olvasási átviteli sebesség (MB/s) | net.received.average
Írási átviteli sebesség (MB/s) | net.transmitted.average
**Leltár elérési útjának részletei** |
Name | Konténer. GetType(). név
A gyermekobjektum típusa | Konténer. ChildType (Gyermektípus)
Referencia részletei | Konténer. MoRef
Szülő részletei | Container.Parent
Mappa részletei kiszolgálónként | ((Mappa)tároló). ChildEntity.Type
Adatközpont részletei kiszolgálónként | ((Datacenter)container). VmFolder
Adatközpont részletei gazdagépmappánként | ((Datacenter)container). HostFolder (Gazdaszámítógép)
Fürt részletei gazdagépenként | ((ClusterComputeResource)container). Fogadó
Gazdagép adatai kiszolgálónként | ((HostSystem)container). Vm

### <a name="performance-data"></a>Teljesítményadatok

A berendezés által a VMware-ben futó kiszolgálóról gyűjtött és az Azure-ba küldött teljesítményadatok.

**Adatok** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Processzorhasználat | cpu.usage.average | Javasolt kiszolgálóméret/-költség
Memóriahasználat | mem.usage.average | Javasolt kiszolgálóméret/-költség
Lemez olvasási átviteli sebesség (MB/s) | virtualDisk.read.average | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Lemezírás átviteli sebesség (MB/s) | virtualDisk.write.average | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Lemez olvasási műveletei másodpercenként | virtualDisk.numberReadAveraged.average | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Lemezírási műveletek másodpercenként | virtualDisk.numberWriteAveraged.average  | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Hálózati adapter olvasási átviteli sebesség (MB/s) | net.received.average | A kiszolgáló méretének kiszámítása
Hálózati adapter írási átviteli sebesség (MB/s) | net.transmitted.average  |A kiszolgáló méretének kiszámítása

### <a name="installed-software-inventory"></a>Telepített szoftverleltár

A berendezés adatokat gyűjt a kiszolgálókon telepített szoftverleltárról.

#### <a name="windows-server-software-inventory-data"></a>Windows Server szoftverleltár adatai

A berendezés által a VMware-környezetben felderített windowsos kiszolgálókról gyűjtött szoftverleltár-adatok.

**Adatok** | **Beállításjegyzékbeli hely** | **Kulcs**
--- | --- | ---
Alkalmazásnév  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Verzió  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion (Verzió megjelenítése)
Szolgáltató  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Windows Server-szolgáltatások adatai

A berendezés által a VMware-környezetben felderített windowsos kiszolgálókról gyűjtött szolgáltatások adatai.

**Adatok**  | **PowerShell-parancsmag** | **Tulajdonság**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Funkciótípus | Get-WindowsFeature  | FeatureType (Jellemzőtípus)
Szülő  | Get-WindowsFeature  | Szülő

#### <a name="sql-server-metadata"></a>SQL Server metaadatok hozzáadása

Itt találhatóak a SQL Server vMware-környezetben felderített windowsos kiszolgálókról gyűjtött adatok.

**Adatok**  | **Beállításjegyzékbeli hely**  | **Kulcs**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Kiadás  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Kiadás
Szervizcsomag  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Sp
Verzió  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Verzió

#### <a name="windows-server-operating-system-data"></a>Windows Server operációs rendszer adatai

Itt vannak a berendezés által a VMware-környezetben felderített windowsos kiszolgálókról gyűjtött operációsrendszer-adatok.

**Adatok**  | **WMI-osztály**  | **WMI-osztálytulajdonság**
--- | --- | ---
Name  | Win32_operatingsystem  | Képaláírás
Verzió  | Win32_operatingsystem  | Verzió
Architektúra  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Linux-kiszolgáló szoftverleltárának adatai

A berendezés a VMware-környezetben felderített linuxos kiszolgálókról gyűjtött szoftverleltár-adatok. A kiszolgáló operációs rendszere alapján a rendszer egy vagy több parancsot futtat.

**Adatok**  | **Parancsok**
--- | ---
Name | rpm, dpkg-query, snap
Verzió | rpm, dpkg-query, snap
Szolgáltató | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Linux-kiszolgáló operációsrendszer-adatai

Itt vannak a berendezés által a VMware-környezetben felderített linuxos kiszolgálókról gyűjtött operációsrendszer-adatok.

**Adatok**  | **Parancsok**
--- | ---
Name <br/> version | A következő fájlok közül egy vagy többből gyűjtve:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architektúra | Uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server-példányok és adatbázisok adatai

A berendezés adatokat gyűjt SQL Server-példányról és -adatbázisokról.

> [!Note]
> A VMware-SQL Server futó virtuális gépek és adatbázisok felderítése és értékelése jelenleg előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

#### <a name="sql-database-metadata"></a>SQL Database-metaadatok

**Adatbázis metaadatai** | **Nézetek/SQL Server tulajdonságai**
--- | ---
Az adatbázis egyedi azonosítója | sys.databases
Kiszolgáló által megadott adatbázis-azonosító | sys.databases
Az adatbázis neve | sys.databases
Az adatbázis kompatibilitási szintje | sys.databases
Adatbázis rendezésneve | sys.databases
Az adatbázis állapota | sys.databases
Az adatbázis mérete (MBS) | sys.master_files
Adatfájlokat tartalmazó hely meghajtóbetűjele | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
Adatbázisfájlok listája | sys.databases, sys.master_files
A szolgáltatásközvetítő engedélyezve van vagy sem | sys.databases
Az adatbázis engedélyezve van a változásadatok rögzítésére vagy nem | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metaadatok

**Kiszolgáló metaadatai** | **Nézetek/ SQL Server tulajdonságai**
--- | ---
Kiszolgálónév |SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
FQDN | A telepített alkalmazások felderítése során származtatott kapcsolati sztring
Telepítési azonosító | sys.dm_server_registry
Kiszolgáló verziója | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Server edition (Kiszolgáló kiadása) | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Kiszolgálói gazdaplatform (Windows/Linux) | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
A kiszolgáló termékszintje (RTM SP CTP) | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Alapértelmezett biztonsági mentési útvonal | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Az adatfájlok alapértelmezett elérési útja | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
A naplófájlok alapértelmezett elérési útja | SERVERPROPERTY és Software\Microsoft\MSSQLServer\MSSQLServer
Nem. magok a kiszolgálón | sys.dm_os_schedulers, sys.dm_os_sys_info
Kiszolgáló rendezésének neve | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Nem. A kiszolgálón látható ONLINE állapotú magok | sys.dm_os_schedulers
Egyedi kiszolgálóazonosító | sys.dm_server_registry
A ha engedélyezve van vagy sem | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Pufferkészlet-bővítmény engedélyezve vagy nem | sys.dm_os_buffer_pool_extension_configuration
Konfigurált vagy nem konfigurált feladatátvevő fürt | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Csak Windows hitelesítési módot használó kiszolgáló | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
A kiszolgáló telepíti a PolyBase-t | SERVERPROPERTY (KISZOLGÁLÓTULAJDONSÁG)
Nem. logikai processzorok a rendszeren | sys.dm_server_registry, sys.dm_os_sys_info
Egy fizikai processzorcsomag által elérhetővé tért logikai vagy fizikai magok nemének aránya | sys.dm_os_schedulers, sys.dm_os_sys_info
A rendszeren a fizikai processzorok nem | sys.dm_os_schedulers, sys.dm_os_sys_info
A kiszolgáló legutóbbi elindulásának dátuma és időpontja | sys.dm_server_registry
Maximális kiszolgálómemória-használat (MBS) | sys.dm_os_process_memory
Összesen nem. az összes adatbázis felhasználóinak száma | sys.databases, sys.logins
Az összes felhasználói adatbázis teljes mérete | sys.databases
Az ideiglenes adatbázis mérete | sys.master_files, sys.config, sys.dm_os_sys_info
Nem. bejelentkezések | sys.logins
Csatolt kiszolgálók listája | sys.servers
Ügynök feladatának listája | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Teljesítmény-metaadatok

**Teljesítmény** | **Nézetek/ SQL-kiszolgáló tulajdonságai** | **Értékelés hatása**
--- | --- | ---
SQL Server cpu-kihasználtság| sys.dm_os_ring_buffers| Javasolt termékváltozat-méret (CPU-dimenzió)
SQL logikai PROCESSZORok száma| sys.dm_os_sys_info| Javasolt termékváltozat-méret (CPU-dimenzió)
Használatban álló SQL fizikai memória| sys.dm_os_process_memory| Használatlan
SQL-memória kihasználtságának százalékos aránya| sys.dm_os_process_memory | Használatlan
Adatbázis CPU-kihasználtsága| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Javasolt termékváltozat-méret (CPU-dimenzió)
Használatban található adatbázismemória (pufferkészlet)| sys.dm_os_buffer_descriptors| Javasolt termékváltozat-méret (memóriadimenzió)
Fájl olvasási/írási I/O-ját| sys.dm_io_virtual_file_stats, sys.master_files| Javasolt termékváltozat-méret (IO-dimenzió)
Az olvasások/írások fájlszáma| sys.dm_io_virtual_file_stats, sys.master_files| Javasolt termékváltozat-méret (átviteli sebesség dimenzió)
A fájl IO-jában található olvasási/írási/írási idő (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Javasolt termékváltozat-méret (IO-késés dimenzió)
Fájlméret| sys.master_files| Javasolt termékváltozat-méret (tárolási dimenzió)


### <a name="application-dependency-data"></a>Alkalmazásfüggőségek adatai

Az ügynök nélküli függőségelemzés a kapcsolati és a folyamatadatokat gyűjti.

#### <a name="windows-server-dependencies-data"></a>Windows Server függőségi adatai

A berendezés által az egyes Windows-kiszolgálókról gyűjtött kapcsolati adatok, amelyeken engedélyezve van az ügynök nélküli függőségelemzés.

**Adatok** | **Parancsok**
--- | ---
Helyi port | Netstat
Helyi IP-cím | Netstat
Távoli port | Netstat
Távoli IP-cím | Netstat
TCP-kapcsolat állapota | Netstat
Folyamatazonosító | Netstat
Aktív kapcsolatok száma | Netstat

A berendezés által az egyes Windows-kiszolgálókról gyűjtött kapcsolati adatok, amelyeken engedélyezve van az ügynök nélküli függőségelemzés.

**Adatok** | **WMI-osztály** | **WMI-osztálytulajdonság**
--- | --- | ---
Folyamatnév | Win32_Process | ExecutablePath (Végrehajthatópath)
Argumentumok feldolgozása | Win32_Process | Commandline
Alkalmazásnév | Win32_Process | A ExecutablePath tulajdonság VersionInfo.ProductName paramétere

#### <a name="linux-server-dependencies-data"></a>Linux-kiszolgáló függőségi adatai

Itt vannak a berendezés által az egyes Linux-kiszolgálókról gyűjtött kapcsolati adatok, amelyek engedélyezve vannak az ügynök nélküli függőségelemzéshez.

**Adatok** | **Parancsok**
--- | ---
Helyi port | Netstat
Helyi IP-cím | Netstat
Távoli port | Netstat
Távoli IP-cím | Netstat
TCP-kapcsolat állapota | Netstat
Aktív kapcsolatok száma | Netstat
Folyamatazonosító  | Netstat
Folyamatnév | Ps
Argumentumok feldolgozása | Ps
Alkalmazásnév | dpkg vagy rpm

## <a name="collected-data---hyper-v"></a>Összegyűjtött adatok – Hyper-V

A berendezés konfigurációs és teljesítmény-metaadatokat gyűjt a Hyper-V környezetben futó kiszolgálókról.

### <a name="metadata"></a>Metaadatok
A Azure Migrate berendezés által felderített metaadatok segítenek annak kiderítésében, hogy a kiszolgálók készen állnak-e az Azure-ba való migrálásra, megfelelő méretű kiszolgálókra és a csomagok költségeire. A Microsoft ezeket az adatokat semmilyen licencelési auditban nem használja fel.

Itt található a berendezés által gyűjtött és az Azure-ba küldött kiszolgálói metaadatok teljes listája.

**Adatok** | **WMI-osztály** | **WMI-osztálytulajdonság**
--- | --- | ---
**Kiszolgáló adatai** | 
BIOS sorozatszáma | Msvm_BIOSElement | BIOSSerialNumber
Kiszolgáló típusa (Gen 1 vagy 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Kiszolgáló megjelenítendő neve | Msvm_VirtualSystemSettingData | ElementName
Kiszolgáló verziója | Msvm_ProcessorSettingData | VirtualQuantity (Virtuálisquantitás)
Memória (bájt) | Msvm_MemorySettingData | VirtualQuantity (Virtuálisquantitás)
A kiszolgáló által maximálisan igénybeható memória | Msvm_MemorySettingData | Korlát
Dinamikus memória engedélyezve | Msvm_MemorySettingData | DynamicMemoryEnabled
Operációs rendszer neve/verziója/teljes tartományneve | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems névadatok
Kiszolgáló energiaállapota | Msvm_ComputerSystem | EnabledState
**Lemezenkénti részletek** |
Lemezazonosító | Msvm_VirtualHardDiskSettingData | VirtualDiskId (Virtuális lemezazonosító)
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Típus
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtuális merevlemez szülője | Msvm_VirtualHardDiskSettingData | ParentPath (Szülőpath)
**Hálózati adapterenkénti részletek** |
IP-címek (szintetikus hálózati protokollok) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP engedélyezve (szintetikus hálózati eszközök) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Hálózati adapter azonosítója (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Hálózati adapter MAC-címe (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
Hálózati adapter azonosítója (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting Data | InstanceID
Hálózati adapter MAC-azonosítója (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting Data | Cím

### <a name="performance-data"></a>Teljesítményadatok

A berendezés által gyűjtött és az Azure-ba küldött kiszolgálóteljesítmény-adatok.

**Teljesítményszámláló-osztály** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Hyper-V hipervizor virtuális processzor | Vendég futási ideje (% ) | Javasolt kiszolgálóméret/-költség
Hyper-V dinamikus memória Server | Aktuális nyomás (%)<br/> Vendég látható fizikai memória (MB) | Javasolt kiszolgálóméret/-költség
Hyper-V virtuális tárolóeszköz | Olvasási bájt/másodperc | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Hyper-V virtuális tárolóeszköz | Írási bájt/másodperc | A lemezméret, a tárolási költségek és a kiszolgálóméret kiszámítása
Hyper-V Virtual Network Adapter | Fogadott bájtok/másodperc | A kiszolgáló méretének kiszámítása
Hyper-V Virtual Network Adapter | Küldött bájtok másodpercenként | A kiszolgáló méretének kiszámítása

- A cpu-kihasználtság a kiszolgálóhoz csatolt összes virtuális processzor összes kihasználtságának összege.
- A memóriahasználat (aktuális nyomás * Vendég látható fizikai memória) / 100.
- A lemez- és hálózathasználati értékeket a rendszer a felsorolt Hyper-V teljesítményszámlálókból gyűjti.

## <a name="collected-data---physical"></a>Összegyűjtött adatok – Fizikai

A berendezés konfigurációs és teljesítmény-metaadatokat gyűjt a helyszínen futó fizikai vagy virtuális kiszolgálókról.

### <a name="metadata"></a>Metaadatok

Az Azure Migrate berendezés által felderített metaadatok segítenek annak kiderítésében, hogy a kiszolgálók készen állnak-e az Azure-ba való migrálásra, megfelelő méretű kiszolgálókra és csomagokra vonatkozó költségeket. A Microsoft ezeket az adatokat semmilyen licencelési auditban nem használja fel.

### <a name="windows-server-metadata"></a>Windows Server-metaadatok

Itt található a berendezés által az Azure-ba gyűjtött és az Azure-ba küldött Windows Server-metaadatok teljes listája.

**Adatok** | **WMI-osztály** | **WMI-osztálytulajdonság**
--- | --- | ---
FQDN | Win32_ComputerSystem | Tartomány, Név, PartOfDomain
Processzormagok száma | Win32_PRocessor | NumberOfCores (Magok száma)
Lefoglalt memória | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-sorozatszám | Win32_ComputerSystemProduct | IdentifyingNumber (Szám azonosítása)
BIOS GUID | Win32_ComputerSystemProduct | Uuid
Rendszerindítás típusa | Win32_DiskPartition | Ellenőrizze a type = **GPT:System** for EFI/BIOS partíciót
Operációs rendszer neve | Win32_OperatingSystem | Képaláírás
Operációs rendszer verziója |Win32_OperatingSystem | Verzió
Operációs rendszer architektúrája | Win32_OperatingSystem | OSArchitecture
Lemezek száma | Win32_DiskDrive | Modell, méret, eszközazonosító, mediatype, név
Lemezméret | Win32_DiskDrive | Méret
Hálózati adapterek listája | Win32_NetworkAdapterConfiguration | Leírás, Index
Hálózati adapter IP-címe | Win32_NetworkAdapterConfiguration | IPAddress
Hálózati adapter MAC-címe | Win32_NetworkAdapterConfiguration | MACAddress (MAC-cím)

### <a name="linux-server-metadata"></a>Linux-kiszolgáló metaadatai

Itt található a berendezés által gyűjtött és az Azure-ba küldött Linux-kiszolgálói metaadatok teljes listája.

**Adatok** | **Parancsok**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Processzormagok száma |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Lefoglalt memória | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS-sorozatszám | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
Rendszerindítás típusa | [ -d /sys/firmware/efi ] && echo EFI \| \| echo BIOS
Operációs rendszer neve/verziója | Ezeket a fájlokat az operációs rendszer verziójához és nevéhez tudjuk elérni:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Operációs rendszer architektúrája | Uname -m
Lemezek száma | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Rendszerindító lemez | df /boot \| sed -n 2p \| awk '{print $1}'
Lemezméret | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Hálózati adapterek listája | ip -o -4 addr show \| awk '{print $2}'
Hálózati adapter IP-címe | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Hálózati adapter MAC-címe | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows-teljesítményadatok

A berendezés által gyűjtött és az Azure-ba küldött Windows Server teljesítményadatok a következőek.

**Adatok** | **WMI-osztály** | **WMI-osztálytulajdonság**
--- | --- | ---
Processzorhasználat | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime (PercentIdleTime)
Memóriahasználat | Win32_PerfFormattedData_PerfOS_Memory | Rendelkezésre álló memória (MB)
Hálózati adapterek száma | Win32_PerfFormattedData_Tcpip_NetworkInterface | A hálózati eszközök számának lekért száma.
Fogadott adatok hálózati adapterenként | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Hálózati adapterenként továbbított adatok | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BájtokSentPersec
Lemezek száma | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Lemezek száma
Lemez részletei | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linuxos teljesítményadatok

A berendezés által gyűjtött és az Azure-ba küldött Linux-kiszolgáló teljesítményadatok a következőek.

| **Adatok** | **Parancsok** |
| --- | --- |
| Processzorhasználat | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| Memóriahasználat | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| Hálózati adapterek száma | lshw -class network \| grep eth[0-60] \| wc -l |
| Fogadott adatok hálózati adapterenként | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Továbbított adatok hálózati adapterenként | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Lemezek száma | fdisk -l \| egrep 'Disk. \* bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| Lemez részletei | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Berendezésfrissítések

A berendezés a Azure Migrate futó szolgáltatások frissítésekor frissül. Ez automatikusan történik, mert az automatikus frissítés alapértelmezés szerint engedélyezve van a berendezésen. Ezt az alapértelmezett beállítást módosíthatja a berendezés szolgáltatásainak manuális frissítéséhez.

### <a name="turn-off-auto-update"></a>Az automatikus frissítés kikapcsolása

1. A berendezést futtató kiszolgálón nyissa meg a Beállításszerkesztőt.
2. Lépjen a **következőHKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance:**.
3. Az automatikus frissítés kikapcsolához hozzon létre egy **AutoUpdate** key (Automatikus frissítési kulcs) beállításkulcsot 0 DWORD értékkel.

    ![Beállításkulcs beállítása](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Az automatikus frissítés bekapcsolás

Az automatikus frissítés az alábbi módszerek egyikével kapcsolhatja be:

- Az AutoUpdate beállításkulcs törlésével a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Kattintson a **Berendezésszolgáltatások megtekintése elemre** a legújabb frissítési ellenőrzések között az Előfeltételek beállítása **panelen** az automatikus frissítés bekapcsoláshoz.

A beállításkulcs törlése:

1. A berendezést futtató kiszolgálón nyissa meg a Beállításszerkesztőt.
2. Lépjen a **következőHKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance:**.
3. Törölje a korábban az automatikus frissítés kikapcsolához létrehozott **AutoUpdate** beállításkulcsot.

A berendezésről való Konfigurációkezelő a felderítés befejezése után:

1. A berendezéskonfiguráció-kezelőben válassza az **Előfeltételek beállítása panelt**
2. A legújabb frissítések ellenőrzéséhez  kattintson a Berendezésszolgáltatások megtekintése elemre, majd kattintson a hivatkozásra az automatikus frissítés bekapcsoláshoz.

    ![Automatikus frissítések bekapcsolás](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Ellenőrizze a berendezés szolgáltatásainak verzióját

A berendezés-szolgáltatások verzióját az alábbi módszerek egyikével ellenőrizheti:

- A Berendezéskonfiguráció-kezelőben válassza az **Előfeltételek beállítása panelt.**
- A berendezésen, a **Vezérlőpult**  >  **programok és szolgáltatások között.**

A Berendezéskonfiguráció-kezelő ellenőrzése:

1. A berendezéskonfiguráció-kezelőben válassza az **Előfeltételek beállítása panelt**
2. A legújabb frissítések ellenőrzéséhez kattintson a **Berendezésszolgáltatások megtekintése elemre.**

    ![Verzió ellenőrzése](./media/migrate-appliance/versions.png)

A következő Vezérlőpult:

1. A berendezésen kattintson a **Start Vezérlőpult** Programs and Features (Programok  >  **és**  >  **szolgáltatások) elemre.**
2. Ellenőrizze a berendezésszolgáltatások verzióit a listában.

    ![A verzió ellenőrzése a Vezérlőpult](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Régebbi verzió manuális frissítése

Ha valamelyik szolgáltatáshoz régebbi verziót futtat, el kell távolítania a szolgáltatást, és manuálisan frissítenie kell a legújabb verzióra.

1. A berendezésszolgáltatás legújabb verzióinak ellenőrzéséhez [töltse](https://aka.ms/latestapplianceservices) le a LatestComponents.jsfájlt.
2. A letöltés után nyissa meg LatestComponents.jsfájlt a Jegyzettömbben.
3. Keresse meg a fájlban a legújabb szolgáltatásverziót, és a hozzá elérhető letöltési hivatkozást. Például:

    "Name": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a6a81e75469b84"

4. Töltse le egy elavult szolgáltatás legújabb verzióját a fájlban található letöltési hivatkozással.
5. A letöltés után futtassa a következő parancsot egy rendszergazdai parancsablakban a letöltött MSI integritásának ellenőrzéséhez.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Például: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Ellenőrizze, hogy a parancs kimenete megegyezik-e a fájlban található szolgáltatás kivonatértékével (például a fenti MD5 kivonatértékkel).
6. Most futtassa az MSI-t a szolgáltatás telepítéséhez. Ez egy csendes telepítés, és a telepítési ablak bezárul, miután végzett.
7. A telepítés befejezése után ellenőrizze a szolgáltatás verzióját a Vezérlőpult  >  **Programok és szolgáltatások menüjében.** A szolgáltatásverziót most frissíteni kell a json-fájlban megjelenő legújabb verzióra.

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja,](how-to-set-up-appliance-vmware.md) hogyan állíthatja be a berendezést a VMware-hez.
- [Megtudhatja,](how-to-set-up-appliance-hyper-v.md) hogyan állíthatja be a berendezést a Hyper-V-hez.
- [Megtudhatja,](how-to-set-up-appliance-physical.md) hogyan állíthatja be a berendezést fizikai kiszolgálókhoz.
