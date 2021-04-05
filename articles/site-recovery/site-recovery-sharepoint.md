---
title: Vész-helyreállítás egy többrétegű SharePoint-alkalmazáshoz Azure Site Recovery használatával
description: Ez a cikk bemutatja, hogyan állíthatja be a többrétegű SharePoint-alkalmazások vész-helyreállítását Azure Site Recovery képességekkel.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013751"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Vész-helyreállítás beállítása többrétegű SharePoint-alkalmazáshoz a vész-helyreállításhoz Azure Site Recovery használatával

Ez a cikk részletesen ismerteti, hogyan lehet védelemmel ellátni a SharePoint-alkalmazásokat a  [Azure site Recovery](site-recovery-overview.md)használatával.


## <a name="overview"></a>Áttekintés

A Microsoft SharePoint egy hatékony alkalmazás, amely segíthet egy csoport vagy részleg számára az adatok rendszerezését, együttműködését és megosztását. A SharePoint intranetes portálokat, dokumentum-és fájlkezelési, együttműködési, közösségi hálózatokat, extraneteket, webhelyeket, vállalati keresést és üzleti intelligenciát biztosíthat. Emellett a rendszerintegrációval, a folyamatok integrálásával és a munkafolyamat-automatizálási képességekkel is rendelkezik. A szervezetek jellemzően a leállás és az adatvesztés szempontjából érzékeny 1. rétegbeli alkalmazások.

A Microsoft SharePoint jelenleg nem biztosít beépített vész-helyreállítási lehetőségeket. A vészhelyzet típusától és méretezéstől függetlenül a helyreállítás olyan készenléti adatközpont használatát foglalja magában, amelyet helyreállíthat a farmon. A készenléti adatközpontok olyan forgatókönyvek esetén szükségesek, amelyekben a helyi redundáns rendszerek és biztonsági másolatok nem állíthatók helyre az elsődleges adatközpontból érkező kimaradások miatt.

A jó vész-helyreállítási megoldás lehetővé teszi a helyreállítási tervek modellezését az összetett alkalmazás-architektúrák, például a SharePoint köré. Emellett lehetővé kell tenni a testreszabott lépések hozzáadását is, amelyekkel kezelhető az alkalmazás-hozzárendelések a különböző rétegek között, és így egyetlen kattintással elvégezhető a feladatátvétel egy alacsonyabb RTO esetén.

Ez a cikk részletesen ismerteti, hogyan lehet védelemmel ellátni a SharePoint-alkalmazásokat a [Azure site Recovery](site-recovery-overview.md)használatával. Ez a cikk a három rétegű SharePoint-alkalmazás Azure-ba történő replikálásának ajánlott eljárásait ismerteti, hogyan végezhet vész-helyreállítási gyakorlatot, és hogyan végezhet feladatátvételt az alkalmazásban az Azure-ba.

A többrétegű alkalmazások Azure-ba történő helyreállításáról az alábbi videóban talál további információt.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt győződjön meg arról, hogy az alábbiakat ismeri fel:

1. [Virtuális gépek replikálása az Azure-ba](./vmware-azure-tutorial.md)
2. [Helyreállítási hálózat tervezése](./concepts-on-premises-to-azure-networking.md)
3. [Feladatátvételi teszt végrehajtása az Azure-ba](site-recovery-test-failover-to-azure.md)
4. [Feladatátvétel az Azure-ba](site-recovery-failover.md)
5. [Tartományvezérlő replikálása](site-recovery-active-directory.md)
6. [SQL Server replikálása](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-architektúra

A SharePoint egy vagy több olyan kiszolgálóra telepíthető, amely többrétegű topológiákat és kiszolgálói szerepköröket használ egy olyan farm kialakításának megvalósításához, amely megfelel bizonyos céloknak és célkitűzéseknek. Egy tipikus nagy, magas keresletű SharePoint Server-Farm, amely számos egyidejű felhasználót és nagyszámú tartalmi elemet támogat a skálázhatósági stratégia részeként. Ez a megközelítés magában foglalja a dedikált kiszolgálókon futó szolgáltatások futtatását, a szolgáltatások csoportosítását, majd a kiszolgálók csoportként történő skálázását. A következő topológia szemlélteti a szolgáltatás és a kiszolgáló csoportosítását egy három szintű SharePoint Server-farmhoz. A különböző SharePoint-topológiákkal kapcsolatos részletes útmutatásért tekintse meg a SharePoint dokumentációját és a termékcsoport architektúráit ismertető témakört. A SharePoint 2013 üzembe helyezéséről a [jelen dokumentumban](/SharePoint/sharepoint-server)talál további információt.



![1. üzembe helyezési minta](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery támogatása

Site Recovery az alkalmazás-független, és működnie kell a SharePoint bármely olyan verziójával, amely támogatott gépen fut. A cikk létrehozásához a Windows Server 2012 R2 Enterprise rendszerű VMware virtuális gépeket használták. A rendszer a SharePoint 2013 Enterprise Edition és az SQL Server 2014 Enterprise Edition verziót használta.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Yes
**VMware** | Igen | Yes
**Fizikai kiszolgáló** | Igen | Yes
**Azure** | NA | Igen


### <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra

Ha megosztott lemezes fürtöt használ bármely rétegként az alkalmazásban, akkor nem fogja tudni használni Site Recovery replikációt a virtuális gépek replikálásához. Használhatja az alkalmazás által biztosított natív replikációt, majd helyreállítási terv használatával az összes szintet feladatátvételt [hajthat](site-recovery-create-recovery-plans.md) végre.

## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

Kövesse [ezt az útmutatót](./vmware-azure-tutorial.md) a virtuális gép Azure-ba való replikálásának megkezdéséhez.

* A replikáció befejezését követően győződjön meg arról, hogy minden egyes szinten minden egyes virtuális gépre rákattintott, majd válassza ki ugyanazt a rendelkezésre állási készletet a "replikált elem > beállítások > tulajdonságok > számítás és hálózat" területen. Ha például a webes szintje 3 virtuális géppel rendelkezik, győződjön meg arról, hogy az összes 3 virtuális gép ugyanazon rendelkezésre állási csoport részeként van konfigurálva az Azure-ban.

    ![Készlet – rendelkezésre állás – készlet](./media/site-recovery-sharepoint/select-av-set.png)

* A Active Directory és a DNS védelmével kapcsolatos útmutatásért tekintse meg a [Active Directory és a DNS-dokumentum védelme](site-recovery-active-directory.md) című témakört.

* Az SQL Serveren futó adatbázis-rétegek védelméről a SQL Server dokumentumok [védelme](site-recovery-sql.md) című témakörben talál útmutatást.

## <a name="networking-configuration"></a>Hálózatkezelési konfiguráció

### <a name="network-properties"></a>Hálózati tulajdonságok

* Az alkalmazás és a webes szintű virtuális gépek esetében konfigurálja Azure Portal hálózati beállításait, hogy a virtuális gépek a feladatátvételt követően a megfelelő DR hálózathoz legyenek csatolva.

    ![Hálózat kiválasztása](./media/site-recovery-sharepoint/select-network.png)


* Ha statikus IP-címet használ, adja meg azt az IP-címet, amelyet a virtuális gépnek a **cél IP** -mezőben kell elvégeznie.

    ![Statikus IP-cím beállítása](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS-és forgalmi Útválasztás

Az internetre irányuló webhelyek esetében [hozzon létre egy Traffic Manager "priority" típusú profilt](../traffic-manager/quickstart-create-traffic-manager-profile.md) az Azure-előfizetésben. Ezután konfigurálja a DNS-és Traffic Manager-profilt a következő módon.


| **Ahol** | **Forrás** | **Cél**|
| --- | --- | --- |
| Nyilvános DNS | Nyilvános DNS a SharePoint-webhelyekhez <br/><br/> Pl.: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Helyszíni DNS | sharepointonprem.contoso.com | Nyilvános IP-cím a helyszíni farmon |


A Traffic Manager profilban [hozza létre az elsődleges és a helyreállítási végpontokat](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Használja a külső végpontot a helyszíni végponthoz és az Azure-végponthoz tartozó nyilvános IP-címhez. Győződjön meg arról, hogy a prioritás a helyszíni végpontnál magasabbra van állítva.

A SharePoint webes szintjein egy adott porton (például 800) lévő tesztoldalt kell üzemeltetni, hogy a Traffic Manager automatikusan felderítse a rendelkezésre állás utáni feladatátvételt. Ez egy áthidaló megoldás abban az esetben, ha nem engedélyezheti a névtelen hitelesítést a SharePoint-webhelyeken.

[Konfigurálja a Traffic Manager profilt](../traffic-manager/traffic-manager-configure-priority-routing-method.md) az alábbi beállításokkal.

* Útválasztási módszer – "priority"
* DNS élettartama (TTL) – "30 másodperc"
* Endpoint monitor beállításai – ha engedélyezheti a névtelen hitelesítést, adott webhely-végpontot is megadhat. Vagy használhat egy teszt oldalt egy adott porton is (például 800).

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv lehetővé teszi a különböző rétegek feladatátvételének előkészítését egy többrétegű alkalmazásban, így az alkalmazások konzisztenciájának fenntartása érdekében. Az alábbi lépéseket követve hozzon létre egy helyreállítási tervet egy többrétegű webalkalmazáshoz. [További információ a helyreállítási terv létrehozásáról](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz

1. Hozzon létre egy helyreállítási tervet az alkalmazás és a webes szintű virtuális gépek hozzáadásával.
2. A virtuális gépek csoportosításához kattintson a "Testreszabás" elemre. Alapértelmezés szerint az összes virtuális gép az "1. csoport" részét képezi.

    ![Az RP testreszabása](./media/site-recovery-sharepoint/rp-groups.png)

3. Hozzon létre egy másik csoportot (2. csoport), és helyezze át a webes szintű virtuális gépeket az új csoportba. Az alkalmazási szintű virtuális gépeknek a "csoport 1" részévé kell esniük, és a webes szintű virtuális gépeknek a "2. csoport" részévé kell esniük. Ezzel biztosíthatja, hogy az alkalmazási rétegek virtuális gépei először a webes szintű virtuális gépek után induljon el.


### <a name="adding-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez

A leggyakrabban használt Azure Site Recovery szkripteket az Automation-fiókjába telepítheti az alábbi "üzembe helyezés az Azure-ban" gombra kattintva. Ha bármilyen közzétett parancsfájlt használ, kövesse a parancsfájl útmutatását.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adjon hozzá egy előműveleti parancsfájlt az "1. csoporthoz" a feladatátvételi SQL rendelkezésre állási csoporthoz. Használja a minta parancsfájlokban közzétett "ASR-SQL-FailoverAG" szkriptet. Ügyeljen arra, hogy kövesse a parancsfájl útmutatását, és végezze el a szükséges módosításokat a parancsfájlban.

    ![Add-AG-script-1. lépés](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-script-2. lépés](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adjon hozzá egy post Action-szkriptet a terheléselosztó a webes szinten átadott virtuális gépeken (2. csoport) való csatolásához. Használja a minta parancsfájlokban közzétett "ASR-AddSingleLoadBalancer" szkriptet. Ügyeljen arra, hogy kövesse a parancsfájl útmutatását, és végezze el a szükséges módosításokat a parancsfájlban.

    ![Add-LB-script-1. lépés](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-script-2. lépés](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adjon hozzá egy manuális lépést a DNS-rekordok frissítéséhez, hogy az az Azure-beli új farmra mutasson.

    * Az internetre irányuló webhelyek esetében a feladatátvétel után nem szükséges DNS-frissítés. A Traffic Manager konfigurálásához kövesse a hálózatkezelési útmutató című szakaszban ismertetett lépéseket. Ha a Traffic Manager profilt az előző szakaszban leírtak szerint állították be, adjon hozzá egy parancsfájlt az Azure-beli virtuális gépen a dummy-port megnyitásához (a példában szereplő 800).

    * A belső elérésű helyek esetében adjon hozzá egy manuális lépést a DNS-rekord frissítéséhez, hogy az új webes szintű virtuális gép terheléselosztó IP-címére mutasson.

4. Adjon hozzá egy manuális lépést a keresési alkalmazás biztonsági másolatból való visszaállításához vagy egy új keresési szolgáltatás elindításához.

5. A keresési szolgáltatásalkalmazás biztonsági másolatból való visszaállításához kövesse az alábbi lépéseket.

    * Ez a módszer azt feltételezi, hogy a Search Service alkalmazás biztonsági másolata a katasztrofális esemény előtt lett elvégezve, és a biztonsági mentés elérhető a DR helyen.
    * Ez könnyen megvalósítható úgy, hogy ütemezi a biztonsági mentést (például naponta egyszer), és egy másolási eljárás használatával helyezi el a biztonsági mentést a DR helyen. A másolási eljárások tartalmazhatnak parancsfájlokból származó programokat, például a AzCopy (Azure Copy) vagy a DFSR (elosztott Fájlszolgáltatások replikációja) beállítását.
    * Most, hogy a SharePoint-farm fut, navigáljon a központi felügyelet, a biztonsági mentés és visszaállítás lehetőségre, és válassza a visszaállítás lehetőséget. A visszaállítás kikérdezi a megadott biztonsági mentési helyet (Előfordulhat, hogy frissítenie kell az értéket). Válassza ki a visszaállítani kívánt Search Service alkalmazás biztonsági másolatát.
    * A keresés vissza lett állítva. Ne feledje, hogy a visszaállítás arra vár, hogy ugyanazt a topológiát (azonos számú kiszolgálót) és a kiszolgálókhoz hozzárendelt merevlemez-betűjeleket is megkeresse. További információ: "a [keresési szolgáltatás alkalmazásának visszaállítása a SharePoint 2013"](/SharePoint/administration/restore-a-search-service-application) dokumentumban.


6. Az új keresési szolgáltatásalkalmazás elindításához kövesse az alábbi lépéseket.

    * Ez a módszer azt feltételezi, hogy a "Search Administration" adatbázis biztonsági mentése a DR webhelyen érhető el.
    * Mivel a többi Search Service alkalmazás-adatbázis nem replikálódik, újból létre kell őket hozni. Ehhez navigáljon a központi felügyelet lapra, és törölje a Search Service alkalmazást. A keresési indexet üzemeltető kiszolgálókon törölje az indexfájl fájljait.
    * Hozza létre újra a Search Service alkalmazást, és ezzel újra létrehozza az adatbázisokat. Azt javasoljuk, hogy egy előkészített parancsfájlt hozzon létre, amely újra létrehozza ezt a szolgáltatásalkalmazás-alkalmazást, mivel az összes műveletet nem lehet végrehajtani a grafikus felhasználói felületen. Például az index meghajtó helyének beállítása és a keresési topológia konfigurálása csak SharePoint PowerShell-parancsmagok használatával lehetséges. Használja a Windows PowerShell-parancsmagot Restore-SPEnterpriseSearchServiceApplication és határozza meg a naplózott és a replikált keresési felügyeleti adatbázist, Search_Service__DB. Ez a parancsmag megadja a keresési konfigurációt, a sémát, a felügyelt tulajdonságokat, a szabályokat és a forrásokat, és létrehozza a többi összetevő alapértelmezett készletét.
    * Miután a Search Service alkalmazást újra létrehozta, el kell indítania egy teljes bejárást minden tartalomforrás számára a Search Service visszaállításához. A helyszíni farmról bizonyos elemzési információk, például a keresési javaslatok elvesznek.

7. Az összes lépés elvégzése után mentse a helyreállítási tervet, és a végső helyreállítási terv a következőhöz hasonlóan fog kinézni.

    ![RP mentve](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Feladatátvételi teszt
Kövesse [ezt az útmutatót](site-recovery-test-failover-to-azure.md) a feladatátvételi teszt végrehajtásához.

1.  Lépjen a Azure Portalra, és válassza ki a Recovery Service-tárolót.
2.  Kattintson a SharePoint-alkalmazáshoz létrehozott helyreállítási tervre.
3.  Kattintson a "feladatátvételi teszt" elemre.
4.  Válassza a helyreállítási pont és az Azure Virtual Network lehetőséget a feladatátvételi teszt folyamatának elindításához.
5.  A másodlagos környezet létrehozása után elvégezheti az érvényességét.
6.  Az érvényesítések befejezését követően a helyreállítási terv "feladatátvételi teszt" gombjára kattinthat, és a teszt feladatátvételi környezet megtisztítása megtörténik.

Az AD és a DNS szolgáltatásban végzett feladatátvételi teszt végrehajtásával kapcsolatos útmutatásért tekintse meg az [ad és a DNS-dokumentum feladatátvételi szempontjait](site-recovery-active-directory.md#test-failover-considerations) .

Az SQL Always ON rendelkezésre állási csoportokhoz tartozó feladatátvételi teszt végrehajtásával kapcsolatos útmutatásért lásd: az [alkalmazás Dr és a Azure site Recovery végrehajtása és a feladatátvételi teszt](site-recovery-sql.md#disaster-recovery-of-an-application) dokumentálása.

## <a name="doing-a-failover"></a>Feladatátvétel végrehajtása
Kövesse [ezt az útmutatót](site-recovery-failover.md) a feladatátvétel végrehajtásához.

1.  Lépjen a Azure Portalra, és válassza ki a Recovery Services-tárolót.
2.  Kattintson a SharePoint-alkalmazáshoz létrehozott helyreállítási tervre.
3.  Kattintson a "feladatátvétel" elemre.
4.  A feladatátvételi folyamat elindításához válassza a helyreállítási pont lehetőséget.

## <a name="next-steps"></a>Következő lépések
További információ a Site Recovery használatával történő [más alkalmazások replikálásáról](site-recovery-workload.md) .
