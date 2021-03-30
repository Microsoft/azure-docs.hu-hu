---
title: Tudnivalók Azure Site Recovery
description: Áttekintést nyújt az Azure Site Recovery szolgáltatásról, és összefoglalja a vészhelyreállítási és a migrálási üzembehelyezési forgatókönyveket.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: 19860e64182cd73fe9f9fa1246f440a03109d465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92281876"
---
# <a name="about-site-recovery"></a>Tudnivalók a Site Recovery szolgáltatásról

Üdvözli az Azure Site Recovery szolgáltatás! Ebben a cikkben a szolgáltatás rövid áttekintését olvashatja el.

Szervezetként olyan üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiát kell bevezetni, amely a tervezett és nem tervezett leállások esetén gondoskodik az adatok biztonságáról, valamint az alkalmazások és a munkaterhelések online állapotáról.

Az Azure Recovery Services hozzájárul a BCDR stratégiához:

- **Site Recovery szolgáltatás**: A Site Recovery azzal segít fenntartani az üzletmenet folytonosságát, hogy leállás esetén tovább futtatja az üzleti alkalmazásokat és a számítási feladatokat. A Site Recovery képes a fizikai és virtuális gépeken futó számítási feladatok replikálására egy elsődleges helyről egy másodlagos helyre. Ha az elsődleges helyen kimaradás történik, a rendszer átadja a feladatokat a másodlagos helynek, és az alkalmazások onnan lesznek elérhetők. Amint az elsődleges hely megint elérhetővé válik, visszatérhet hozzá.
- **Backup szolgáltatás**: a [Azure Backup](../backup/index.yml) szolgáltatás gondoskodik az adatai biztonságáról és helyreállításáról.

A Site Recovery a következők replikációját képes kezelni:

- Azure virtuális gépek replikációja Azure-régiók között.
- Helyszíni virtuális gépek, Azure Stack virtuális gépek és fizikai kiszolgálók.

## <a name="what-does-site-recovery-provide"></a>Mit nyújt a Site Recovery?

**Szolgáltatás** | **Részletek**
--- | ---
**Egyszerű BCDR-megoldás** | A Site Recovery segítségével beállítható és felügyelhető a replikáció, a feladatátvétel és feladat-visszavétel az Azure Portal egy helyéről.
**Azure-beli virtuális gép replikációja** | Beállíthatja az Azure-beli virtuális gépek vészhelyreállítását egy elsődleges régióról egy másodlagos régióra.
**Helyszíni virtuális gép replikációja** | Helyszíni virtuális gépeket és fizikai kiszolgálókat replikálhat az Azure-ba vagy egy másodlagos helyszíni adatközpontba. Az Azure-ba történő replikációval kiküszöbölhetők a másodlagos adatközpontok fenntartásával járó bonyodalmak és költségek.
**Számítási feladat replikációja** | A támogatott Azure-beli virtuális gépeken, helyszíni Hyper-V-alapú és VMware-alapú virtuális gépeken, valamint a Windows-/Linux-alapú fizikai kiszolgálókon futó bármilyen számítási feladat replikálható.
**Adatrugalmasság** | Site Recovery az alkalmazásadatok elfogása nélkül hangolja össze a replikálást. Az Azure-ba történő replikálás során az adatokat az Azure Storage tárolja, és az ezzel járó rugalmasságot is biztosítja. Feladatátvétel esetén a rendszer Azure-beli virtuális gépeket hoz létre a replikált adatok alapján.
**RTO- és RPO-célok** | A vállalati korlátokon belül tartja a helyreállítási idők célkitűzéseit (recovery time objectives, RTO-k) és a helyreállítási pont célkitűzéseit (recovery point objectives, RPO-k). A Site Recovery folyamatos replikációt biztosít Azure és VMware virtuális gépek esetén, és csupán 30 másodperces replikációs frekvenciát Hyper-V esetén. Az RTO tovább csökkenthető az [Azure Traffic Managerrel](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) való integrációval.
**Alkalmazás-konzisztencia feladatátvétel esetén** | Alkalmazáskonzisztens pillanatképekkel rendelkező helyreállítási pontokat használhat a replikáláshoz. Ezek a pillanatképek a lemez adatait, a memóriában lévő összes adatot és az összes folyamatban lévő tranzakciót is rögzítik.
**Megszakítás nélküli tesztelés** | Egyszerűen végrehajthat vészhelyreállítási próbákat anélkül, hogy ez hatással lenne a folyamatban lévő replikációra.
**Rugalmas feladatátvétel** | A várt kimaradások esetében a nulla adatvesztéssel rendelkező tervezett feladatátvételeket is futtathatja. Vagy nem tervezett feladatátvétel minimális adatvesztéssel, a replikálás gyakorisága alapján, váratlan katasztrófák esetén. Amint az elsődleges hely megint elérhetővé válik, egyszerűen visszaadhatja a feladatokat.
**Testre szabott helyreállítási tervek** | A helyreállítási tervek segítségével testre szabhatja és elvégezheti a több virtuális gépen futó többrétegű alkalmazások feladatátvételét és helyreállítását. Csoportosíthatja a gépeket a helyreállítási terveken belül, valamint opcionálisan szkripteket és manuális műveleteket vehet fel. A helyreállítási tervek integrálhatók az Azure Automation-runbook használatával.
**BCDR-integráció** | A Site Recovery más BCDR-technológiákkal integrálható. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server-háttérrendszerét, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében.
**Azure Automation-integráció** | Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
**Hálózatintegráció** | A Site Recovery az Azure-t integrálja az alkalmazások hálózatkezeléséhez. Például az IP-címek foglalásához konfigurálja a terheléselosztást, és használja az Azure Traffic Managert a hatékony hálózati hálózatváltást.

## <a name="what-can-i-replicate"></a>Miket replikálhatok?

**Támogatott** | **Részletek**
--- | ---
**Replikációs forgatókönyvek** | Azure-beli virtuális gépeket replikálhat egyik Azure-régióból a másikba.<br/><br/>  Helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket, fizikai kiszolgálókat (Windows és Linux) replikálhat, Azure Stack virtuális gépeket az Azure-ba.<br/><br/> AWS Windows-példányok replikálása az Azure-ba.<br/><br/> Helyszíni VMware virtuális gépeket, a System Center VMM által felügyelt Hyper-V virtuális gépeket, valamint fizikai kiszolgálókat replikálhat egy másodlagos helyre.
**Régiók** | Áttekintheti a Site Recovery által [támogatott régiókat](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery). |
**Replikált gépek** | Áttekintheti az [Azure-beli virtuális gépeknek](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), a [helyszíni VMware virtuális gépeknek és fizikai kiszolgálóknak](vmware-physical-azure-support-matrix.md#replicated-machines), valamint a [helyszíni Hyper-V virtuális gépeknek](hyper-v-azure-support-matrix.md#replicated-vms) a replikációs követelményeit.
**Számítási feladatok** | A támogatott replikációs gépeken futó bármilyen számítási feladat replikálható. És a Site Recovery csapata [számos](site-recovery-workload.md#workload-summary)alkalmazáshoz adott meg alkalmazásspecifikus teszteket.

## <a name="next-steps"></a>Következő lépések

- További információ a számítási [feladatok támogatásáról](site-recovery-workload.md).
- Első lépések: [Azure-beli virtuális gép replikálása régiók között](azure-to-azure-quickstart.md).
