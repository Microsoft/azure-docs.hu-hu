---
title: SAP NetWeaver vész-helyreállítás beállítása Azure Site Recovery
description: Megtudhatja, hogyan állíthatja be a vész-helyreállítást az SAP NetWeaver Azure Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711943"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Vész-helyreállítás beállítása többrétegű SAP NetWeaver-alkalmazások telepítéséhez

A legtöbb nagy méretű és közepes méretű SAP-telepítés a vész-helyreállítási megoldás valamilyen formáját használja. A robusztus és tesztelhető vész-helyreállítási megoldások fontossága nagyobb, mint az alapvető üzleti folyamatok, például az SAP. A Azure Site Recovery SAP-alkalmazásokkal lett tesztelve és integrálva. Site Recovery meghaladja a legtöbb helyszíni vész-helyreállítási megoldás képességeit, valamint a versengő megoldásoknál alacsonyabb teljes tulajdonlási díjat.

A Site Recovery a következőket teheti:
* Az összetevők Azure-ba történő replikálásával engedélyezheti az SAP NetWeaver és a nem NetWeaver éles környezetben futó alkalmazások védelmét.
* Engedélyezze az Azure-on futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők másik Azure-adatközpontba való replikálásával.
* A Site Recovery segítségével áttelepítheti az Azure-ba az SAP üzemelő példányt, így leegyszerűsítheti a felhőre való átállást.
* Egyszerűsítse az SAP-projektek frissítéseit, tesztelését és a prototípus-készítést azáltal, hogy az SAP-alkalmazások teszteléséhez igény szerinti éles klónozást hoz létre.

A [Azure site Recovery](site-recovery-overview.md)használatával biztosíthatja az SAP NetWeaver-alkalmazások központi telepítésének biztosítását. Ez a cikk az Azure-beli háromrészes SAP NetWeaver-telepítések védelmének ajánlott eljárásait ismerteti, ha Site Recovery használatával replikál egy másik Azure-adatközpontba. A cikk a támogatott forgatókönyveket és konfigurációkat ismerteti, valamint azt, hogyan végezheti el a feladatátvételi teszteket (vész-helyreállítási gyakorlatok) és a tényleges feladatátvételeket.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan végezheti el a következő feladatokat:

* [Virtuális gép replikálása az Azure-ba](./azure-to-azure-tutorial-enable-replication.md)
* [Helyreállítási hálózat kialakítása](./azure-to-azure-about-networking.md)
* [Végezzen feladatátvételi tesztet az Azure-ba](./azure-to-azure-tutorial-dr-drill.md)
* [Feladatátvétel az Azure-ba](site-recovery-failover.md)
* [Tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server példány replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek

A következő esetekben használhatja a Site Recoveryt a vész-helyreállítási megoldások megvalósításához:
* Rendelkezik egy Azure-adatközpontban futó SAP-rendszerekkel, és egy másik Azure-adatközpontba (Azure – Azure vész-helyreállítás) replikálja őket. 
   További információ: [Azure – Azure replikációs architektúra](./azure-to-azure-architecture.md).
* A helyszíni VMware-(vagy fizikai) kiszolgálókon futó SAP-rendszerekkel rendelkezik. Az SAP-rendszereket az Azure-adatközpont (VMware – Azure vész-helyreállítás) vész-helyreállítási helyén is replikálja. 
   Ehhez a forgatókönyvhöz további összetevőkre van szükség. További információ: [VMware – Azure replikációs architektúra](./vmware-azure-architecture.md).
* A helyszíni Hyper-V-n futó SAP-rendszerek vannak. Az SAP-rendszereket az Azure-adatközpontokban (Hyper-V – Azure vész-helyreállítás) egy vész-helyreállítási helyre is replikálja.
   Ehhez a forgatókönyvhöz további összetevőkre van szükség. További információ: [Hyper-V – Azure replikációs architektúra](./hyper-v-azure-architecture.md).

Ebben a cikkben egy **Azure – Azure vész-** helyreállítási forgatókönyvet használunk. A forgatókönyv a Site Recovery SAP vész-helyreállítási képességeit mutatja be. Mivel Site Recovery replikáció nem alkalmazásspecifikus, a leírt folyamat várhatóan más forgatókönyvekre is vonatkozik.

### <a name="required-foundation-services"></a>Szükséges alapszolgáltatások
A cikkben ismertetett forgatókönyvben a következő alapszolgáltatások vannak telepítve:
* Azure ExpressRoute vagy Azure VPN Gateway
* Legalább egy Azure Active Directory tartományvezérlő és DNS-kiszolgáló, amely az Azure-ban fut

Javasoljuk, hogy a Site Recovery üzembe helyezése előtt hozza létre ezt az infrastruktúrát.

## <a name="reference-sap-application-deployment"></a>Az SAP-alkalmazások telepítésének referenciája

Ez a hivatkozási architektúra az Azure-ban magas rendelkezésre állású Windows-környezetben futtatja az SAP NetWeaver-t. Ez az architektúra olyan virtuálisgép-méretekkel van telepítve, amelyeket a szervezet igényeinek megfelelően módosíthat.

![Tipikus SAP-telepítési minta diagramja](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

A vész-helyreállításhoz képesnek kell lennie átadni a feladatátvételt egy másodlagos régióba. Az egyes szintek egy másik stratégiát használnak a vész-helyreállítási védelem biztosításához.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>SAP web diszpécser készleteket futtató virtuális gépek

A web diszpécser összetevő terheléselosztóként működik az SAP-alkalmazások közötti SAP-forgalomhoz. Ha magas rendelkezésre állást szeretne elérni a web diszpécser összetevőjénél, Azure Load Balancer megvalósítja a párhuzamos webes diszpécser telepítését. A web diszpécser ciklikusan megjelenő konfigurációt használ a HTTP (S) forgalom eloszlásához a balancers készletben elérhető webes diszpécserek között.

#### <a name="vms-running-application-servers-pools"></a>Application Server-készleteket futtató virtuális gépek
A SMLG tranzakció a ABAP alkalmazáskiszolgáló bejelentkezési csoportjait kezeli. A terheléselosztási függvényt használja a központi szolgáltatások SAPGUIs belül a számítási feladatok elosztásához a SAP-alkalmazáskiszolgáló-készletek között a és az RFC-alapú forgalom számára. Ezt a kezelést Site Recovery használatával replikálhatja.

#### <a name="vms-running-sap-central-services-clusters"></a>SAP Central Services-fürtöket futtató virtuális gépek
Ez a viszonyítási architektúra központi szolgáltatásokat futtat az alkalmazási szinten lévő virtuális gépeken. A központi szolgáltatások egyetlen virtuális gépen lehetséges meghibásodási pont. A tipikus központi telepítés és a magas rendelkezésre állás nem követelmény.

Magas rendelkezésre állású megoldás megvalósításához használhatja a megosztott fürtözött fürtöt vagy a fájlmegosztási fürtöt. A virtuális gépek megosztott lemezes fürthöz való konfigurálásához használja a Windows Server feladatátvevő fürtöt. Javasoljuk, hogy a Felhőbeli tanúsító kvórum tanúsító használja.

 > [!NOTE]
 > Mivel site Recovery nem replikálja a Felhőbeli tanút, javasoljuk, hogy a Felhőbeli tanúsító a vész-helyreállítási régióban telepítse.

A feladatátvevő fürt környezetének támogatásához a [SIOS DataKeeper cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) a fürt megosztott kötetei funkciót használja. A függvényben a SIOS DataKeeper-fürt replikálja a fürtcsomópontok tulajdonában lévő független lemezeket. Mivel az Azure nem támogatja natív módon a megosztott lemezeket, a SIOS által biztosított megoldásokat igényel.

A fürtözést fájlmegosztási fürt megvalósításával is kezelheti. Az [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nemrég módosította a központi szolgáltatások telepítési mintáját, hogy a/sapmnt globális könyvtárakat egy UNC elérési úton keresztül elérje. Továbbra is javasoljuk, hogy a/sapmnt UNC-megosztás legyen magasan elérhető. Megtekintheti a központi szolgáltatások példányát. Használja a Windows Server feladatátvevő fürtöt a kibővíthető fájlkiszolgáló (SOFS) és a Közvetlen tárolóhelyek (S2D) szolgáltatással a Windows Server 2016 rendszerben.

 > [!NOTE]
 > Site Recovery jelenleg csak a közvetlen tárolóhelyeket használó virtuális gépek összeomlás-konzisztens pontjának replikálását és a SIOS Datakeeper passzív csomópontját támogatja.


## <a name="more-disaster-recovery-considerations"></a>További vész-helyreállítási megfontolások

A Site Recovery használatával összehangolhatja a teljes SAP-telepítés feladatátvételét az Azure-régiók között.
A vész-helyreállítás beállításának lépései a következők:

1. Virtuális gépek replikálása
1. Helyreállítási hálózat kialakítása
1. Tartományvezérlő replikálása
1. Adatbázis-réteg replikálása
1. Futtasson egy feladatátvételi tesztet
1. Feladatátvétel futtatása

Az alábbiakban az ebben a példában használt egyes szintek vész-helyreállítási javaslata látható.

 **SAP-rétegek** | **Ajánlás**
 --- | ---
**SAP web diszpécser készlet** |  Replikálás Site Recovery használatával 
**SAP Application Server-készlet** |  Replikálás Site Recovery használatával 
**SAP központi szolgáltatások fürtje** |  Replikálás Site Recovery használatával 
**Active Directory-beli virtuális gépek** |  Az Active Directory-replikáció használata 
**SQL-adatbáziskiszolgálók** |  SQL Server always on replikáció használata

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az összes SAP-alkalmazás virtuális gép Azure vész-helyreállítási adatközpontba történő replikálásának megkezdéséhez kövesse a [virtuális gépek Azure](./azure-to-azure-tutorial-enable-replication.md)-ba történő replikálásának útmutatóját.

* A Active Directory és a DNS védelméről a [Active Directory és a DNS védelméről](site-recovery-active-directory.md)szóló útmutatóban olvashat.

* A SQL Server-on futó adatbázis-rétegek védelméről a következő témakörben talál útmutatást: [SQL Server védelme](site-recovery-sql.md).

## <a name="networking-configuration"></a>Hálózatkezelési konfiguráció

Ha statikus IP-címet használ, megadhatja azt az IP-címet, amelyet el szeretne végezni a virtuális gépen. Az IP-cím beállításához nyissa meg a **számítási és hálózati beállítások**  >  **hálózati adapter lapot**.

![A magánhálózati IP-cím megadását bemutató képernyőkép a Site Recovery hálózati adapter panelen](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv a feladatátvétel során a többrétegű alkalmazások különböző szintjeinek sorrendjét támogatja. Az előkészítés segíti az alkalmazások konzisztenciájának fenntartását. Ha többrétegű webalkalmazáshoz hoz létre helyreállítási tervet, hajtsa végre a [helyreállítási terv létrehozása a site Recovery használatával](site-recovery-create-recovery-plans.md)című témakörben ismertetett lépéseket.

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz

1. Hozzon létre egy helyreállítási tervet az alkalmazáskiszolgáló, a web diszpécser és az SAP Central Services-alapú virtuális gépek hozzáadásával.
1. Válassza a **Testreszabás** lehetőséget a virtuális gépek csoportosításához. Alapértelmezés szerint az összes virtuális gép az 1. csoport tagja.

### <a name="add-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez
Ahhoz, hogy alkalmazásai megfelelően működjenek, előfordulhat, hogy az Azure-beli virtuális gépeken műveleteket kell végrehajtania. Hajtsa végre ezeket a műveleteket a feladatátvétel vagy a feladatátvételi teszt során. Automatizálhat néhány feladatátvétel utáni műveletet is. Például frissítse a DNS-bejegyzést, és módosítsa a kötéseket és a kapcsolatokat úgy, hogy hozzáadja a megfelelő parancsfájlokat a helyreállítási tervhez.

Az **üzembe helyezés az Azure-** ba lehetőség kiválasztásával telepítheti a leggyakrabban használt site Recovery szkripteket a Azure Automation-fiókjába. Ha bármilyen közzétett parancsfájlt használ, kövesse a parancsfájl útmutatását.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. A SQL Server rendelkezésre állási csoport feladatátvételéhez adjon hozzá egy előkészítő parancsfájlt az 1. csoporthoz. Használja a minta parancsfájlokban közzétett ASR-SQL-FailoverAG szkriptet. Kövesse a parancsfájl útmutatását, és végezze el megfelelően a szükséges módosításokat a parancsfájlban.
1. Adjon hozzá egy művelet utáni parancsfájlt, amely egy terheléselosztó csatlakoztatását hajtja végre a webes réteg sikertelenül áthelyezett virtuális gépei számára (1. csoport). Használja a minta parancsfájlokban közzétett ASR-AddSingleLoadBalancer szkriptet. Kövesse a parancsfájl útmutatását, és szükség szerint végezze el a szükséges módosításokat a parancsfájlban.

![SAP helyreállítási terv](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A Azure Portal válassza ki a Recovery Services-tárolót.
1. Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
1. Kattintson a **Feladatátvétel tesztelése** elemre.
1. A feladatátvételi teszt folyamatának elindításához válassza ki a helyreállítási pontot és az Azure-beli virtuális hálózatot.
1. Ha a másodlagos környezet működik, végezze el az érvényesítést.
1. Az érvényesítések befejezése után törölje a feladatátvételi környezetet a **feladatátvételi teszt törlése** lehetőség kiválasztásával.

További információ: a [feladatátvétel tesztelése az Azure-ban site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. A Azure Portal válassza ki a Recovery Services-tárolót.
1. Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
1. Válassza a **Feladatátvétel** lehetőséget.
1. A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).

## <a name="next-steps"></a>Következő lépések
* További információ a vész-helyreállítási megoldás létrehozásáról az SAP NetWeaver-telepítésekhez Site Recovery használatával. Tekintse meg a letölthető tanulmányi [SAP NetWeaver: vész-helyreállítási megoldás létrehozása a site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). A tanulmány a különböző SAP-architektúrákkal kapcsolatos ajánlásokat tárgyalja. A támogatott alkalmazások és virtuálisgép-típusok az Azure-beli SAP-ben láthatók. A vész-helyreállítási megoldás tesztelésére is lehetőség van.
* További információ [más számítási feladatok replikálásáról](site-recovery-workload.md) site Recovery használatával.