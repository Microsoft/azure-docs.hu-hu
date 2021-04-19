---
title: Az Azure Migrate-berendezés architektúrája
description: Áttekintést nyújt Azure Migrate kiszolgálófelderítéshez, -felméréshez és -migráláshoz használt berendezésről.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715009"
---
# <a name="azure-migrate-appliance-architecture"></a>Az Azure Migrate-berendezés architektúrája

Ez a cikk a Azure Migrate architektúráját és folyamatait ismerteti. A Azure Migrate berendezés egy helyszínen üzembe helyezett egyszerűsített berendezés, amely felderíti az Azure-ba való migráláshoz a kiszolgálókat.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate berendezést a következő forgatókönyvekben használjuk.

**Forgatókönyv** | **Eszköz** | **Itt használható:**
--- | --- | ---
**VMware-környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: Felderítés és értékelés | A VMware-környezetben futó kiszolgálók felderítése<br/><br/> A telepített szoftverleltár és az ügynök nélküli függőségelemzés felderítése, valamint a SQL Server és adatbázisok felderítése.<br/><br/> Gyűjtse össze a kiszolgálókonfiguráció és a teljesítmény metaadatait az értékelésekhez.
**VMware-környezetben futó kiszolgálók ügynök nélküli áttelepítése** | Azure Migrate:Server Migration | A VMware-környezetben futó kiszolgálók felderítése.<br/><br/> Kiszolgálók replikálása ügynökök telepítése nélkül.
**Hyper-V környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: Felderítés és értékelés | A Hyper-V-környezetben futó kiszolgálók felderítése.<br/><br/> Gyűjtse össze a kiszolgálókonfiguráció és a teljesítmény metaadatait az értékelésekhez.
**Fizikai vagy virtualizált kiszolgálók helyszíni felderítése és értékelése** |  Azure Migrate: Felderítés és értékelés |  Fizikai vagy virtualizált kiszolgálók felderítése a helyszínen.<br/><br/> Gyűjtse össze a kiszolgálókonfigurációt és a teljesítmény metaadatait az értékelésekhez.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A berendezés több módszerrel is üzembe helyezhető:

- A berendezés VMware- vagy Hyper-V-környezetben futó kiszolgálók sablonjának használatával helyezhető üzembe[(VMware esetén OVA-sablon,](how-to-set-up-appliance-vmware.md) [Hyper-V esetén VHD).](how-to-set-up-appliance-hyper-v.md)
- Ha nem szeretne sablont használni, üzembe helyezheti a berendezést VMware- vagy Hyper-V-környezetben egy [PowerShell-telepítő szkripttel.](deploy-appliance-script.md)
- A Azure Government üzembe kell helyeznie a berendezést egy PowerShell-telepítő szkript használatával. Az üzembe helyezés lépéseit itt [található.](deploy-appliance-script-government.md)
- A fizikai vagy virtualizált helyszíni kiszolgálók vagy bármely más felhő esetén a berendezést mindig Egy PowerShell-telepítő szkript használatával kell üzembe helyezni. Az üzembe helyezés lépéseit itt [található.](how-to-set-up-appliance-physical.md)
- A letöltési hivatkozások az alábbi táblázatokban érhetők el.

## <a name="appliance-services"></a>Berendezésszolgáltatások

A berendezés a következő szolgáltatásokkal rendelkezik:

- **Berendezés konfigurációkezelője:** Ez egy webalkalmazás, amely konfigurálható a kiszolgálók felderítésének és értékelésének a forrás adataival. 
- **Felderítési ügynök:** Az ügynök a kiszolgáló konfigurációs metaadatait gyűjti, amelyek felhasználhatók helyszíni értékelésekként való létrehozáshoz.
- **Értékelési ügynök:** Az ügynök gyűjti a kiszolgálói teljesítmény metaadatait, amelyek segítségével teljesítményalapú értékelések hozhatók létre.
- **Automatikus frissítési szolgáltatás:** A szolgáltatás naprakészen tartja a berendezésen futó összes ügynököt. 24 óránként automatikusan lefut.
- **DRA-ügynök:** Koordinálja a kiszolgálóreplikációt, és koordinálja a replikált kiszolgálók és az Azure közötti kommunikációt. Csak akkor használatos, ha kiszolgálókat replikál az Azure-ba ügynök nélküli migrálás használatával.
- **Átjáró:** Replikált adatokat küld az Azure-ba. Csak akkor használatos, ha kiszolgálókat replikál az Azure-ba ügynök nélküli migrálás használatával.
- **SQL-felderítési és -értékelési ügynök:** elküldi a konfigurációs és SQL Server metaadatait az Azure-nak.

> [!Note]
> Az utolsó 3 szolgáltatás csak a VMware-környezetben futó kiszolgálók felderítéséhez és értékeléséhez használt berendezésen érhető el.<br/> A VMware-SQL Server futó virtuális gépek és adatbázisok felderítése és értékelése jelenleg előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

## <a name="discovery-and-collection-process"></a>Felderítési és gyűjtési folyamat

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Berendezés architektúrája":::

A berendezés az alábbi folyamattal kommunikál a felderítési forrásokkal.

**Folyamat** | **VMware-berendezés** | **Hyper-V-berendezés** | **Fizikai berendezés**
---|---|---|---
**Felderítés elindítani** | A berendezés alapértelmezés szerint a 443-as TCP-porton kommunikál a vCenter-kiszolgálóval. Ha a vCenter-kiszolgáló egy másik porton figyel, konfigurálhatja azt a berendezés konfigurációkezelőjében. | A berendezés az 5985-ös (HTTP) WinRM-porton keresztül kommunikál a Hyper-V-gazdagépekkel. | A berendezés a Windows-kiszolgálókkal az 5985-ös (HTTP) WinRM-porton keresztül kommunikál a Linux-kiszolgálókkal a 22-es (TCP-) porton keresztül.
**Konfigurációs és teljesítménymetaadatok gyűjtése** | A berendezés a vSphere API vCenter Server használatával gyűjti a vCenter Server-on futó kiszolgálók metaadatait a 443-as porton (alapértelmezett port) vagy bármely más porton, amelyet vCenter Server figyel. | A berendezés a Hyper-V-gazdagépen futó kiszolgálók metaadatait gyűjti össze az 5985-ös porton található gazdagépekkel CIM (CIM) munkamenetben.| A berendezés az 5985-ös porton található kiszolgálókkal CIM (CIM) munkamenetben gyűjt metaadatokat a Windows-kiszolgálókról, a 22-es porton pedig SSH-kapcsolatot használó Linux-kiszolgálókról.
**Felderítési adatok küldése** | A berendezés elküldi az összegyűjtött adatokat a következő Azure Migrate: Felderítés és értékelés és Azure Migrate: Kiszolgáló migrálása a 443-as SSL-porton keresztül.<br/><br/>  A berendezés csatlakozhat az Azure-hoz az interneten keresztül, vagy ExpressRoute privát társviszony-létesítésen vagy Microsoft társviszony-létesítés-kapcsolati kapcsolaton keresztül. | A berendezés elküldi az összegyűjtött adatokat a következő Azure Migrate: Felderítés és értékelés a 443-as SSL-porton keresztül.<br/><br/> A berendezés csatlakozhat az Azure-hoz az interneten keresztül, vagy ExpressRoute privát társviszony-létesítésen vagy Microsoft társviszony-létesítés-kapcsolati kapcsolaton keresztül. | A berendezés elküldi az összegyűjtött adatokat a következő Azure Migrate: Felderítés és értékelés a 443-as SSL-porton keresztül.<br/><br/> A berendezés csatlakozhat az Azure-hoz az interneten keresztül, vagy ExpressRoute privát társviszony-létesítésen vagy Microsoft társviszony-létesítés-kapcsolati kapcsolaton keresztül. 
**Adatgyűjtés gyakorisága** | A rendszer 30 percenként gyűjti és küldi el a konfigurációs metaadatokat. <br/><br/> A rendszer 20 másodpercenként gyűjti be a teljesítmény-metaadatokat, és összesíti őket, hogy 10 percenként elküldjék az adatpontokat az Azure-nak. <br/><br/> A szoftverleltáradatokat a rendszer 12 óránként küldi el az Azure-nak. <br/><br/> Az ügynök nélküli függőségi adatokat a rendszer 5 óránként gyűjti össze, összesíti a berendezésen, és 6 óránként küldi el őket az Azure-nak. <br/><br/> A SQL Server 24 óránként frissülnek, a teljesítményadatok pedig 30 másodpercenként.| A konfigurációs metaadatokat a rendszer 30 naponta gyűjti és küldi el. <br/><br/> A rendszer 30 másodpercenként gyűjti be a teljesítmény-metaadatokat, és összesíti őket, hogy 10 percenként elküldjék az adatpontokat az Azure-nak.|  A konfigurációs metaadatokat a rendszer 30 perces gyűjtés után gyűjti és küldi el. <br/><br/> A rendszer 5 percenként gyűjti a teljesítmény-metaadatokat, és összesíti őket, hogy 10 percenként elküldjék az adatpontokat az Azure-nak.
**Értékelés és migrálás** | A berendezés által gyűjtött metaadatokból a felderítési és értékelési eszköz Azure Migrate hozhat létre értékeléseket.<br/><br/>Emellett elkezdheti a VMware-környezetben futó kiszolgálók migrálását a Azure Migrate: Server Migration eszközzel az ügynök nélküli kiszolgálóreplikáció vezénylése érdekében.| A berendezés által gyűjtött metaadatokból a felderítési és értékelési eszköz Azure Migrate hozhat létre értékeléseket. | A berendezés által gyűjtött metaadatokból a felderítési és felmérési eszköz Azure Migrate hozhat létre értékeléseket.

## <a name="next-steps"></a>Következő lépések

[Tekintse át](migrate-appliance.md) a berendezés támogatási mátrixát.