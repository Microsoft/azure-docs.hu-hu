---
title: Az Azure Migrate-berendezés architektúrája
description: Áttekintést nyújt a kiszolgálók felderítéséhez, értékeléséhez és áttelepítéséhez használt Azure Migrate készülékről.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785240"
---
# <a name="azure-migrate-appliance-architecture"></a>Az Azure Migrate-berendezés architektúrája

Ez a cikk a Azure Migrate berendezés architektúráját és folyamatait ismerteti. A Azure Migrate készülék egy helyileg üzembe helyezett, egyszerűsített berendezés, amely az Azure-ba való Migrálás kiszolgálóinak felderítésére szolgál.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Azure Migrate készüléket a következő esetekben használja a rendszer.

**Forgatókönyv** | **Eszköz** | **A következőhöz használatos**
--- | --- | ---
**A VMware-környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: felderítés és Értékelés | A VMware-környezetben futó kiszolgálók felderítése<br/><br/> A telepített szoftverek leltárának, az ügynök nélküli függőségek elemzésének és a SQL Server példányok és adatbázisok felderítésének elvégzése.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.
**A VMware-környezetben futó kiszolgálók ügynök nélküli áttelepítése** | Azure Migrate: kiszolgáló áttelepítése | A VMware-környezetben futó kiszolgálók felderítése.<br/><br/> Kiszolgálók replikálása anélkül, hogy ügynököket kellene telepíteni rajtuk.
**A Hyper-V környezetben futó kiszolgálók felderítése és értékelése** | Azure Migrate: felderítés és Értékelés | A Hyper-V környezetben futó kiszolgálók felderítése.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.
**A helyszíni fizikai vagy virtualizált kiszolgálók felderítése és értékelése** |  Azure Migrate: felderítés és Értékelés |  Helyszíni fizikai vagy virtualizált kiszolgálók felderítése.<br/><br/> Összegyűjti a kiszolgáló konfigurációját és a teljesítménnyel kapcsolatos metaadatokat az értékelésekhez.

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A készülék több módszer használatával is üzembe helyezhető:

- A készülék a VMware vagy a Hyper-V környezetben futó kiszolgálók sablonjának használatával telepíthető (a VMware-hez vagy [a virtuális merevlemezhez a Hyper-v-hez](how-to-set-up-appliance-hyper-v.md)készült[petesejtek sablonja](how-to-set-up-appliance-vmware.md) ).
- Ha nem szeretne sablont használni, telepítheti a készüléket VMware vagy Hyper-V környezetben egy [PowerShell-telepítő parancsfájl](deploy-appliance-script.md)használatával.
- Azure Government a készüléket egy PowerShell-telepítő parancsfájl használatával kell telepítenie. Tekintse át az üzembe helyezés lépéseit [itt](deploy-appliance-script-government.md).
- A helyszíni vagy más Felhőbeli fizikai vagy virtualizált kiszolgálók esetében mindig PowerShell-telepítő parancsfájl használatával telepítse a készüléket. Tekintse át az üzembe helyezés lépéseit [itt](how-to-set-up-appliance-physical.md).
- A letöltési hivatkozások az alábbi táblázatokban érhetők el.

## <a name="appliance-services"></a>Berendezés-szolgáltatások

A készülék a következő szolgáltatásokat nyújtja:

- **Készülék Configuration Manager**: ez egy webalkalmazás, amely a forrás részleteivel konfigurálható a kiszolgálók felderítésének és értékelésének elindításához. 
- **Felderítési ügynök**: az ügynök a kiszolgálói konfigurációs metaadatokat gyűjti, amelyek a helyszíni értékelésekhez használhatók.
- **Értékelő ügynök**: az ügynök a kiszolgáló teljesítményére vonatkozó metaadatokat gyűjt, amelyek a teljesítmény-alapú értékelések létrehozásához használhatók.
- **Automatikus frissítési szolgáltatás**: a szolgáltatás naprakészen tartja a készüléken futó összes ügynököt. A szolgáltatás 24 óránként egyszer automatikusan fut.
- **DRA-ügynök**: összehangolja a kiszolgálók replikálását, és koordinálja a replikált kiszolgálók és az Azure közötti kommunikációt. Csak a kiszolgálók az Azure-ba való replikálásához használatos ügynök nélküli áttelepítés használatával.
- **Átjáró**: replikált adatokat küld az Azure-nak. Csak a kiszolgálók az Azure-ba való replikálásához használatos ügynök nélküli áttelepítés használatával.
- **SQL-felderítési és-értékelő ügynök**: SQL Server példányok és adatbázisok konfigurációs és teljesítménybeli metaadatait elküldi az Azure-nak.

> [!Note]
> Az utolsó 3 szolgáltatás csak a VMware-környezetben futó kiszolgálók felderítéséhez és értékeléséhez használható készüléken érhető el.<br/> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

## <a name="discovery-and-collection-process"></a>Felderítési és gyűjtési folyamat

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Berendezés architektúrája":::

A készülék a következő folyamattal kommunikál a felderítési forrásokkal.

**Folyamat** | **VMware készülék** | **Hyper-V készülék** | **Fizikai készülék**
---|---|---|---
**Felderítés indítása** | A készülék alapértelmezés szerint a 443-as TCP-porton kommunikál a vCenter-kiszolgálóval. Ha a vCenter-kiszolgáló egy másik portot figyel, azt a készülék Configuration Managerben állíthatja be. | A készülék a 5985-as (HTTP) WinRM-porton keresztül kommunikál a Hyper-V-gazdagépekkel. | A készülék a Windows-kiszolgálókkal kommunikál a WinRM port 5985 (HTTP) protokollal a 22-es porton keresztül (TCP).
**Konfigurációs és teljesítménybeli metaadatok összegyűjtése** | A készülék a vSphere API-k használatával gyűjti a vCenter Server-on futó kiszolgálók metaadatait az 443-as porton (alapértelmezett porton) vagy bármely más porton, vCenter Server figyeli. | A készülék a Hyper-V-gazdagépeken futó kiszolgálók metaadatait a 5985-es porton található gazdagépekkel CIM (CIM) munkamenettel gyűjti.| A készülék metaadatokat gyűjt a Windows-kiszolgálókról CIM (CIM) munkamenettel a 5985-es porton lévő kiszolgálókkal és a Linux-kiszolgálókkal a 22-es porton SSH-kapcsolat használatával.
**Felderítési adatainak küldése** | A készülék elküldi az összegyűjtött adatokat Azure Migrateba: felderítés és értékelés és Azure Migrate: a kiszolgáló áttelepítése a 443-as SSL-porton keresztül.<br/><br/> A készülék az interneten keresztül vagy ExpressRoute keresztül tud csatlakozni az Azure-hoz (Microsoft-társat igényel). | A készülék az összegyűjtött adatokat a 443-as SSL-porton keresztül küldi el Azure Migrate: felderítésre és értékelésre.<br/><br/> A készülék az interneten keresztül vagy ExpressRoute keresztül tud csatlakozni az Azure-hoz (Microsoft-társat igényel).| A készülék az összegyűjtött adatokat a 443-as SSL-porton keresztül küldi el Azure Migrate: felderítésre és értékelésre.<br/><br/> A készülék az interneten keresztül vagy ExpressRoute keresztül tud csatlakozni az Azure-hoz (Microsoft-társat igényel).
**Adatgyűjtés gyakorisága** | A konfigurációs metaadatok gyűjtése és küldése 30 percenként történik. <br/><br/> A teljesítmény metaadatainak gyűjtése 20 másodpercenként történik, és a rendszer összesíti az adatpontok Azure-ba történő küldését 10 percenként. <br/><br/> A szoftver leltározási szolgáltatásait 12 óránként egyszer küldik el az Azure-nak. <br/><br/> Az ügynök nélküli függőségi adatokat 5 percenként gyűjtöttük össze, a készüléken összesítve, és 6 óránként küldik az Azure-ba. <br/><br/> A SQL Server konfigurációs adatai 24 óránként frissülnek, és a teljesítményadatokat 30 másodpercenként rögzíti a rendszer.| A konfigurációs metaadatok gyűjtése és küldése 30 percenként történik. <br/><br/> A teljesítmény metaadatainak gyűjtése 30 másodpercenként történik, és a rendszer összesíti az adatpontok Azure-ba történő küldését 10 percenként.|  A konfigurációs metaadatok gyűjtése és küldése 30 percenként történik. <br/><br/> A teljesítmény metaadatainak gyűjtése 5 percenként történik, és a rendszer összesíti az adatpontok Azure-ba történő küldését 10 percenként.
**Értékelés és migrálás** | A készülék által gyűjtött metaadatok alapján a Azure Migrate: Discovery and Assessment Tool használatával lehet értékeléseket létrehozni.<br/><br/>Emellett a VMware-környezetben futó kiszolgálók áttelepítését is elindíthatja a Azure Migrate: Server áttelepítési eszközzel az ügynök nélküli kiszolgáló replikálásának előkészítéséhez.| A készülék által gyűjtött metaadatok alapján a Azure Migrate: Discovery and Assessment Tool használatával lehet értékeléseket létrehozni. | A készülék által gyűjtött metaadatok alapján a Azure Migrate: Discovery and Assessment Tool használatával lehet értékeléseket létrehozni.

## <a name="next-steps"></a>Következő lépések

[Tekintse át](migrate-appliance.md) a készülék támogatási mátrixát.