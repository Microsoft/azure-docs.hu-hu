---
title: A Citrix XenDesktop/XenApp vész-helyreállítás beállítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan állítható be a vész-helyreállítás a Citrix XenDesktop és a XenApp-környezetek Azure Site Recovery használatával.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134836"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>vész-helyreállítás beállítása többrétegű Citrix-XenApp és XenDesktop-telepítéshez



A Citrix XenDesktop egy asztali virtualizálási megoldás, amely asztali számítógépeket és alkalmazásokat biztosít OnDemand szolgáltatásként bármely felhasználónak, bárhol is. A FlexCast kézbesítési technológiával a XenDesktop gyorsan és biztonságosan biztosíthat alkalmazásokat és asztalokat a felhasználóknak.
Napjainkban a Citrix XenApp nem biztosít vész-helyreállítási lehetőségeket.

Egy jó vész-helyreállítási megoldás lehetővé teszi, hogy a fenti összetett alkalmazás-architektúrák körüli helyreállítási tervek modellezése és a különböző szintek közötti alkalmazás-hozzárendelések kezelésére szolgáló testreszabott lépések is felvehetők legyenek, így az alacsonyabb RTO eredményező katasztrófák esetén egyetlen kattintással kipróbálható a shot megoldás.

Ez a dokumentum részletes útmutatást nyújt egy vész-helyreállítási megoldás létrehozásához a helyszíni Citrix XenApp-környezetek számára a Hyper-V és a VMware vSphere platformon. Ez a dokumentum azt is ismerteti, hogyan hajtható végre a feladatátvételi teszt (vész-helyreállítási részletezés) és az Azure-ba történő nem tervezett feladatátvétel helyreállítási tervekkel, a támogatott konfigurációk és előfeltételek használatával.


## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt győződjön meg arról, hogy az alábbiakat ismeri fel:

1. [Virtuális gépek replikálása az Azure-ba](./vmware-azure-tutorial.md)
1. [Helyreállítási hálózat tervezése](./concepts-on-premises-to-azure-networking.md)
1. [Feladatátvételi teszt végrehajtása az Azure-ba](site-recovery-test-failover-to-azure.md)
1. [Feladatátvétel az Azure-ba](site-recovery-failover.md)
1. [Tartományvezérlő replikálása](site-recovery-active-directory.md)
1. [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Üzembe helyezési minták

A Citrix XenApp és a XenDesktop Farm jellemzően a következő üzembe helyezési mintával rendelkezik:

**Üzembe helyezési minta**

Citrix XenApp és XenDesktop üzembe helyezése AD DNS-kiszolgálóval, SQL Database-kiszolgálóval, Citrix Delivery Controller, kirakati kiszolgáló, XenApp Master (VDA), Citrix XenApp License Server

![1. üzembe helyezési minta](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery támogatása

Ebben a cikkben a vSphere 6,0/System Center VMM 2012 R2 által felügyelt VMware virtuális gépeken futó Citrix-telepítések a DR. telepítésére szolgáltak.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Nincs a hatókörben | Yes
**VMware** | Nincs a hatókörben | Yes
**Fizikai kiszolgáló** | Nincs a hatókörben | Yes

### <a name="versions"></a>Verziók
Az ügyfelek a Hyper-V-n vagy VMware-en vagy fizikai kiszolgálókon futó Virtual Machines XenApp-összetevőket telepíthetnek. Azure Site Recovery az Azure-ban fizikai és virtuális üzemelő példányok is védhetők.
Mivel a XenApp 7,7-es vagy újabb verziója támogatott az Azure-ban, csak az ezekkel a verziókkal rendelkező üzemelő példányok feladatátvétele lehetséges az Azure-ba a vész-helyreállítás vagy-áttelepítés

### <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra

1. A helyszíni központi telepítések védelme és helyreállítása kiszolgálói operációs rendszerű gépek használatával a közzétett XenApp-alkalmazások és a XenApp közzétett asztali számítógépek számára támogatott.

2. A helyszíni üzemelő példányok védelme és helyreállítása asztali operációs rendszerű gépekkel az asztali VDI az ügyfél virtuális asztalokhoz, például a Windows 10 rendszerű eszközökön való továbbításához nem támogatott. Ennek az az oka, hogy Site Recovery nem támogatja a gépek asztali OSes ' történő helyreállítását.  Emellett egyes ügyféloldali virtuális asztali operációs rendszerek (például A Windows 7) még nem támogatott az Azure-beli licenceléshez. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

3.  Azure Site Recovery nem tud replikálni és védelemmel ellátni a meglévő helyszíni MCS-vagy PVS-klónokat.
Ezeket a klónokat újra létre kell hoznia az Azure RM kiépítés a kézbesítési vezérlőből való használatával.

4. A NetScaler nem védhető a Azure Site Recovery használatával, mert a NetScaler a FreeBSD-re épül, és a Azure Site Recovery nem támogatja a FreeBSD operációs rendszer védelmét. Az Azure-ba történő feladatátvételt követően telepítenie és konfigurálnia kell egy új NetScaler-berendezést az Azure piactéren.


## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

A replikálás és a helyreállítás engedélyezéséhez a Citrix XenApp üzembe helyezésének következő összetevőit kell védeni.

* Az AD DNS-kiszolgáló védelme
* Az SQL Database-kiszolgáló védelme
* A Citrix Delivery Controller védelme
* A kirakati kiszolgáló védelme.
* A XenApp Master (VDA) védelme
* A Citrix XenApp licenckiszolgáló védelme


**AD DNS-kiszolgáló replikálása**

A tartományvezérlők Azure-ban való replikálásának és konfigurálásának útmutatója a [Active Directory és a DNS és a Azure site Recovery elleni védelemmel](site-recovery-active-directory.md) foglalkozó témakörben található.

**SQL Database-kiszolgáló replikálása**

Az SQL Server-kiszolgálók védelmének ajánlott lehetőségeiről a [SQL Server védelme SQL Server vész-helyreállítással és Azure site Recovery](site-recovery-sql.md) című témakörben talál részletes technikai útmutatást.

[Ezt az útmutatót](./vmware-azure-tutorial.md) követve megkezdheti a többi összetevő virtuális gépei az Azure-ba történő replikálását.

![XenApp-összetevők védelme](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Számítás és hálózati beállítások**

A gépek védelme után (az állapot "védett" állapotúként jelenik meg a replikált elemek alatt) a számítási és hálózati beállításokat konfigurálni kell.
A számítási és hálózati > számítási tulajdonságok lapon megadhatja az Azure-beli virtuális gép nevét és a célként megadott méretet.
Ha szükséges, írja át úgy a nevet, hogy az megfeleljen az Azure-követelményeknek. Az Azure-beli virtuális géphez hozzárendelt célként megadott hálózattal, alhálózattal és IP-címmel kapcsolatos információkat is megtekintheti és hozzáadhatja.

Vegye figyelembe a következőket:

* A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címeket állít be, amely nem érhető el a feladatátvétel során, a feladatátvétel nem fog működni. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.

* Az AD/DNS-kiszolgáló esetében a helyszíni címek megőrzése lehetővé teszi, hogy ugyanazt a címeket határozza meg, mint az Azure virtuális hálózat DNS-kiszolgálója.

A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

*   Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
*   Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
* Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.
*   Ha a virtuális gépnek több hálózati adaptere is van, akkor mind ugyanahhoz a hálózathoz csatlakoznak.
*   Ha a virtuális gépnek több hálózati adaptere van, akkor a listában szereplő első az alapértelmezett hálózati adapter lesz az Azure-beli virtuális gépen.


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

Miután a replikáció engedélyezve van a XenApp-összetevő virtuális gépei számára, a következő lépés egy helyreállítási terv létrehozása.
A helyreállítási terv azokat a virtuális gépeket csoportosítja, amelyek hasonló követelményekkel rendelkeznek a feladatátvételhez és a helyreállításhoz.  

**Helyreállítási terv létrehozásának lépései**

1. Adja hozzá a XenApp összetevő virtuális gépeket a helyreállítási tervhez.
2. Kattintson a helyreállítási tervek-> + helyreállítási terv elemre. Adjon meg egy intuitív nevet a helyreállítási tervhez.
3. VMware rendszerű virtuális gépek esetén: válassza a forrás lehetőséget VMware Process Server, Target as Microsoft Azure és üzemi modellként Resource managerként, majd kattintson az elemek kiválasztása elemre.
4. Hyper-V rendszerű virtuális gépek esetén: válassza a forrás VMM-kiszolgáló, cél mint Microsoft Azure és az üzembe helyezési modell erőforrás-kezelőként lehetőséget, majd kattintson az elemek kiválasztása lehetőségre, és válassza ki a XenApp üzembe helyezési virtuális gépeket

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz

A helyreállítási tervek testreszabhatók a feladatátvételi csoportok hozzáadásához adott indítási sorrend, parancsfájlok vagy manuális műveletek esetén. A következő csoportokat hozzá kell adni a helyreállítási tervhez.

1. Feladatátvételi Group1: AD DNS
2. Feladatátvételi Group2: SQL Server virtuális gépek
2. Feladatátvételi Group3: VDA fő rendszerkép VM
3. Feladatátvételi Group4: kézbesítési vezérlő és kirakati kiszolgáló virtuális gépei


### <a name="adding-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez

A parancsfájlok a helyreállítási terv adott csoportja előtt vagy után is futtathatók. A manuális műveletek a feladatátvétel során is szerepelhetnek és végezhetők el.

A testreszabott helyreállítási terv az alábbihoz hasonlóan néz ki:

1. Feladatátvételi Group1: AD DNS
2. Feladatátvételi Group2: SQL Server virtuális gépek
3. Feladatátvételi Group3: VDA fő rendszerkép VM

   >[!NOTE]     
   >A manuális vagy parancsfájl-műveleteket tartalmazó 4., 6. és 7. lépés csak a helyszíni XenApp >-környezetekre vonatkozik az MCS/PVS-katalógusokkal.

4. 3. csoport – manuális vagy parancsfájl-művelet: állítsa le a fő VDA virtuális gépet.
A fő VDA virtuális gép, amikor a feladatátvételt az Azure-ba futtatja, fut állapotban lesz. Ahhoz, hogy új MCS-katalógusokat hozzon létre az Azure-üzemeltetéssel, a fő VDA virtuális gépnek leállított (de lefoglalt) állapotban kell lennie. A virtuális gép leállítása Azure Portalról.

5. Feladatátvételi Group4: kézbesítési vezérlő és kirakati kiszolgáló virtuális gépei
6. Group3 manuális vagy parancsfájl-művelet 1:

    ***Azure RM-gazdagépi kapcsolatok hozzáadása***

    Hozzon létre Azure-gazdagépi kapcsolatokat az Azure-beli új MCS-katalógusok kiépítéséhez a kézbesítési vezérlőben. Kövesse az ebben a [cikkben](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)ismertetett lépéseket.

7. Group3 manuális vagy parancsfájl 2. művelet:

    ***Az MCS-katalógusok újbóli létrehozása az Azure-ban***

    Az elsődleges helyen meglévő MCS vagy PVS klónok nem lesznek replikálva az Azure-ba. Ezeket a klónokat újra létre kell hoznia a replikált fő VDA és az Azure-kiépítés a kézbesítési vezérlőből való használatával. A jelen [cikkben](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) ismertetett lépéseket követve hozzon létre MCS-katalógusokat az Azure-ban.

![Helyreállítási terv a XenApp-összetevőkhöz](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >A [helyen](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) található parancsfájlok segítségével frissítheti a DNS-t a feladatátvétel során felhasznált >virtuális gépek új IP-címeivel, vagy ha szükséges, egy terheléselosztó csatlakoztatását a feladatátvételi virtuális gépen.


## <a name="doing-a-test-failover"></a>Feladatátvételi teszt

Kövesse [ezt az útmutatót](site-recovery-test-failover-to-azure.md) a feladatátvételi teszt végrehajtásához.

![Helyreállítási terv](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Feladatátvétel végrehajtása

Ha feladatátvételt végez, kövesse [ezt az útmutatót](site-recovery-failover.md) .

## <a name="next-steps"></a>Következő lépések

[További](https://aka.ms/citrix-xenapp-xendesktop-with-asr) információ a Citrix XenApp és a XenDesktop üzemelő példányok replikálásáról ebben a tanulmányban. Tekintse meg az útmutatást [más alkalmazások replikálásához](site-recovery-workload.md) site Recovery használatával.
