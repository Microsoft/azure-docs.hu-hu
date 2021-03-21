---
title: Az IIS-webalkalmazás vész-helyreállításának beállítása Azure Site Recovery használatával
description: Ismerje meg, hogyan replikálhatja az IIS webfarm-alapú virtuális gépeket Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 56ac58e47bffc73c7079af043ad567a77e8f3323
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735505"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Többrétegű IIS-alapú webalkalmazás vészhelyreállításának beállítása

Az alkalmazás szoftvere a szervezet üzleti hatékonyságának motorja. Különböző webalkalmazások különböző célokat szolgálhatnak a szervezetekben. Egyes alkalmazások, például a bérszámfejtési feldolgozáshoz, a pénzügyi alkalmazásokhoz és az ügyfelekhez kapcsolódó webhelyekhez használt alkalmazások kritikus fontosságúak lehetnek a szervezet számára. A hatékonyság megromlásának megelőzése érdekében fontos, hogy a szervezet folyamatosan felhasználja ezeket az alkalmazásokat. Még ennél is fontosabb, hogy ezek az alkalmazások folyamatosan elérhetők a szervezet márkájának vagy rendszerképének károsodásának megelőzése érdekében.

A kritikus webalkalmazások jellemzően többrétegű alkalmazásokként vannak beállítva: a web, az adatbázis és az alkalmazás különböző szinteken található. A különböző szinteken való elosztás mellett az alkalmazások az egyes szinteken több kiszolgálót is használhatnak a forgalom elosztása érdekében. Emellett a különböző rétegek és a webkiszolgálók közötti leképezések statikus IP-címeken alapulnak. A feladatátvétel során a leképezések némelyikét frissíteni kell, különösen akkor, ha több webhely van konfigurálva a webkiszolgálón. Ha a webalkalmazások TLS protokollt használnak, frissítenie kell a tanúsítvány kötéseit.

A nem replikáción alapuló hagyományos helyreállítási módszerek a különböző konfigurációs fájlok, a beállításjegyzék-beállítások, a kötések, az egyéni összetevők (COM vagy .NET), a tartalom és a tanúsítványok biztonsági mentését is magukban foglalják. A fájlok manuális lépésekkel állíthatók helyre. A fájlok biztonsági mentésének és manuális helyreállításának hagyományos helyreállítási módszerei nehézkesek, a hibákra hajlamosak, és nem méretezhetők. Előfordulhat például, hogy könnyen elfelejti a tanúsítványok biztonsági mentését. A feladatátvételt követően nem választhat, de új tanúsítványokat is vásárolhat a kiszolgálóhoz.

A jó vész-helyreállítási megoldás támogatja az összetett alkalmazás-architektúrák modellezésére szolgáló helyreállítási terveket. Emellett a helyreállítási tervhez is hozzáadhat testreszabott lépéseket a rétegek közötti alkalmazás-hozzárendelések kezeléséhez. Ha katasztrófa van, az alkalmazás-hozzárendelések egyetlen kattintással, biztos-shot megoldással szolgálnak, amely segít az alacsonyabb RTO.

Ez a cikk azt ismerteti, hogyan lehet védelemmel ellátni egy webalkalmazást Internet Information Services (IIS) alapján [Azure site Recovery](site-recovery-overview.md)használatával. A cikk az ajánlott eljárásokat mutatja be egy háromrétegű, IIS-alapú webalkalmazás az Azure-ba történő replikálásához, a vész-helyreállítási gyakorlat elvégzéséhez, valamint az alkalmazás Azure-ba történő feladatátvételéhez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan végezheti el a következő feladatokat:

* [Virtuális gép replikálása az Azure-ba](vmware-azure-tutorial.md)
* [Helyreállítási hálózat kialakítása](./concepts-on-premises-to-azure-networking.md)
* [Végezzen feladatátvételi tesztet az Azure-ba](site-recovery-test-failover-to-azure.md)
* [Feladatátvétel az Azure-ba](site-recovery-failover.md)
* [Tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Üzembe helyezési minták
Az IIS-alapú webalkalmazások jellemzően a következő telepítési minták egyikét követik:

**1. üzembe helyezési minta**

Egy, az Application Request Routing (ARR), egy IIS-kiszolgáló és SQL Server használatával rendelkező IIS-alapú webfarm.

![Egy három rétegből álló IIS-alapú webfarm diagramja](./media/site-recovery-iis/deployment-pattern1.png)

**2. üzembe helyezési minta**

Egy, az ARR-t, egy IIS-kiszolgálót, egy alkalmazáskiszolgáló és egy SQL Servert tartalmazó IIS-alapú webfarm.

![Egy négy rétegből álló IIS-alapú webfarm diagramja](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery támogatása

A cikkben szereplő példák esetében a VMware virtuális gépeket az IIS 7,5-es verziójával használjuk a Windows Server 2012 R2 Enterprise rendszeren. Mivel Site Recovery replikáció nem alkalmazásspecifikus, a cikkben szereplő ajánlásokat a következő táblázatban szereplő forgatókönyvekben és az IIS különböző verzióiban kell alkalmazni.

### <a name="source-and-target"></a>Forrás és cél

Eset | Egy másodlagos helyre | Az Azure-ba
--- | --- | ---
Hyper-V | Igen | Yes
VMware | Igen | Yes
Fizikai kiszolgáló | Nem | Igen
Azure|NA|Igen

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az összes IIS-webfarm virtuális gép az Azure-ba történő replikálásának megkezdéséhez kövesse a [feladatátvétel tesztelése az Azure-ban](site-recovery-test-failover-to-azure.md)című témakör útmutatását site Recovery.

Ha statikus IP-címet használ, megadhatja azt az IP-címet, amelyet el szeretne végezni a virtuális gépen. Az IP-cím beállításához nyissa meg a **számítási és hálózati beállítások**  >  **cél IP**-címet.

![A cél IP-cím megadását bemutató képernyőkép a Site Recovery számítási és hálózati ablaktáblán](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv a feladatátvétel során a többrétegű alkalmazások különböző szintjeinek sorrendjét támogatja. Az előkészítés segíti az alkalmazások konzisztenciájának fenntartását. Ha többrétegű webalkalmazáshoz hoz létre helyreállítási tervet, hajtsa végre a [helyreállítási terv létrehozása a site Recovery használatával](site-recovery-create-recovery-plans.md)című témakörben ismertetett lépéseket.

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz
Egy tipikus többrétegű IIS-webalkalmazás a következő összetevőkből áll:
* SQL-alapú virtuális gépeket tartalmazó adatbázis-rétegek.
* A webes csomag, amely egy IIS-kiszolgálót és egy alkalmazási szintet tartalmaz. 

Adja hozzá a virtuális gépeket különböző csoportokhoz a következő szintek alapján:

1. Hozzon létre egy helyreállítási tervet. Adja hozzá az adatbázis szintű virtuális gépeket az 1. csoportban. Ezzel biztosíthatja, hogy az adatbázis-rétegek virtuális gépei le legyenek állítva utoljára, és elsőként.
1. Adja hozzá az alkalmazás szintű virtuális gépeket a 2. csoportban. Ez biztosítja, hogy az alkalmazási rétegek virtuális gépei az adatbázis-rétegek felállítása után legyenek felkészülve.
1. Adja hozzá a webes rétegek virtuális gépeket a 3. csoportban. Ezzel biztosíthatja, hogy a webes rétegek virtuális gépei az alkalmazási szintet meghoztak.
1. Terheléselosztási virtuális gépek hozzáadása a 4. csoportban. Ez biztosítja, hogy a virtuális gépek terheléselosztását a rendszer a webes rétegek felállítása után hozza létre.

További információ: [a helyreállítási terv testreszabása](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Parancsfájl hozzáadása a helyreállítási tervhez
Ahhoz, hogy az IIS-webfarm megfelelően működjön, előfordulhat, hogy műveleteket kell végrehajtania az Azure-beli virtuális gépeken feladatátvétel utáni vagy feladatátvételi teszt során. Automatizálhat néhány feladatátvétel utáni műveletet. Frissítheti például a DNS-bejegyzést, módosíthatja a hely kötését, vagy megváltoztathatja a kapcsolati karakterláncot úgy, hogy a megfelelő parancsfájlokat hozzáadja a helyreállítási tervhez. [VMM-parancsfájl hozzáadása helyreállítási tervhez](./hyper-v-vmm-recovery-script.md) az automatizált feladatok parancsfájl használatával történő beállítását ismerteti.

#### <a name="dns-update"></a>DNS-frissítés
Ha a DNS dinamikus DNS-frissítésre van konfigurálva, a virtuális gépek általában az új IP-címmel frissítik a DNS-t az indításkor. Ha explicit lépést szeretne hozzáadni a DNS-nek a virtuális gépek új IP-címeivel való frissítéséhez, adjon hozzá egy [parancsfájlt a DNS IP-](https://aka.ms/asr-dns-update) címének frissítés utáni feladatátvételi művelete helyreállítási terv csoportjain.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Az alkalmazás web.configban található kapcsolatok karakterlánca
A kapcsolatok karakterlánca határozza meg azt az adatbázist, amellyel a webhely kommunikál. Ha a kapcsolatok karakterlánca a virtuális gép nevét adja meg, a feladatátvételt követő további lépések nem szükségesek. Az alkalmazás automatikusan tud kommunikálni az adatbázissal. Továbbá, ha a virtuális gép IP-címe megmarad, nem kell frissítenie a kapcsolódási karakterláncot. 

Ha a kapcsolódási karakterlánc IP-cím használatával hivatkozik az adatbázis virtuális gépre, frissíteni kell a feladatátvételt követően. A következő kapcsolódási karakterlánc például a-adatbázisra mutat az IP-127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

A webes szinten lévő kapcsolati sztring frissítéséhez adjon hozzá egy [IIS-kapcsolat frissítési parancsfájlt](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) a 3. csoport után a helyreállítási tervben.

#### <a name="site-bindings-for-the-application"></a>Az alkalmazáshoz tartozó hely kötései
Minden hely kötési adatokat tartalmaz. A kötési információ magában foglalja a kötés típusát, az IP-címet, amelyen az IIS-kiszolgáló figyeli a helyre irányuló kéréseket, a portszámot és a helyhez tartozó állomásnevek nevét. Előfordulhat, hogy a feladatátvétel során frissítenie kell ezeket a kötéseket, ha módosul a hozzájuk társított IP-cím.

> [!NOTE]
>
> Ha a helyhez tartozó kötést az **összes hozzá nem rendelt** értékre állítja be, nem kell frissítenie ezt a kötést a feladatátvétel után. Továbbá, ha a helyhez társított IP-cím nem módosul a feladatátvétel után, nem kell frissítenie a hely kötését. (Az IP-cím megőrzése az elsődleges és a helyreállítási helyekhez rendelt hálózati architektúrától és alhálózattól függ. Előfordulhat, hogy a frissítése nem valósítható meg a szervezet számára.)

![A TLS/SSL-kötés beállítását bemutató képernyőkép](./media/site-recovery-iis/sslbinding.png)

Ha az IP-címet egy helyhez társította, akkor az összes hely kötését az új IP-címmel frissítse. A hely kötéseinek módosításához adjon hozzá egy [IIS webes szintű frissítési parancsfájlt](/samples/browse/?redirectedfrom=TechNet-Gallery) a 3. csoport után a helyreállítási tervben.

#### <a name="update-the-load-balancer-ip-address"></a>A terheléselosztó IP-címének frissítése
Ha ARR virtuális géppel rendelkezik, az IP-cím frissítéséhez adjon hozzá egy [IIS ARR feladatátvételi parancsfájlt](/samples/browse/?redirectedfrom=TechNet-Gallery) a 4. csoport után.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>TLS/SSL-tanúsítvány kötése HTTPS-kapcsolathoz
Előfordulhat, hogy egy webhelyhez tartozik egy, a webkiszolgáló és a felhasználó böngészője közötti biztonságos kommunikációt biztosító TLS/SSL-tanúsítvány. Ha a webhely HTTPS-kapcsolattal rendelkezik, és az IIS-kiszolgáló IP-címéhez társított HTTPS-hely kötést is tartalmaz egy TLS/SSL-tanúsítvány kötéssel, akkor új hely kötést kell hozzáadnia a tanúsítványhoz az IIS-alapú virtuális gép feladatátvétel utáni közzétételének IP-címével.

A TLS/SSL-tanúsítvány a következő összetevőkkel állítható ki:

* A webhely teljes tartományneve.
* A kiszolgáló neve.
* A tartománynévhez tartozó helyettesítő tanúsítvány.  
* Egy IP-cím. Ha a TLS/SSL-tanúsítványt az IIS-kiszolgáló IP-címére állítja ki, akkor egy másik TLS-/SSL-tanúsítványt kell kiállítani az Azure-beli IIS-kiszolgáló IP-címére. A tanúsítványhoz további TLS-kötést kell létrehozni. Ezért javasoljuk, hogy ne használjon TLS/SSL-tanúsítványt az IP-címen. Ez a lehetőség kevésbé széles körben használatos, és hamarosan elavulttá válik az új hitelesítésszolgáltató/böngésző Fórum módosításaival összhangban.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>A webes rétegek és az alkalmazási rétegek közötti függőség frissítése
Ha a virtuális gépek IP-címén alapuló alkalmazásspecifikus függőség van, akkor frissítenie kell ezt a függőséget a feladatátvétel után.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. A Azure Portal válassza ki a Recovery Services-tárolót.
2. Válassza ki az IIS-webfarmhoz létrehozott helyreállítási tervet.
3. Kattintson a **Feladatátvétel tesztelése** elemre.
4. A feladatátvételi teszt folyamatának elindításához válassza ki a helyreállítási pontot és az Azure-beli virtuális hálózatot.
5. Ha a másodlagos környezet működik, elvégezheti az érvényesítést.
6. Az érvényesítések befejezése után a feladatátvételi teszt környezet tisztításához válassza az **érvényesítések kész** lehetőséget.

További információ: a [feladatátvétel tesztelése az Azure-ban site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. A Azure Portal válassza ki a Recovery Services-tárolót.
1. Válassza ki az IIS-webfarmhoz létrehozott helyreállítási tervet.
1. Válassza a **Feladatátvétel** lehetőséget.
1. A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).

## <a name="next-steps"></a>Következő lépések
* További információ [más alkalmazások replikálásáról](site-recovery-workload.md) site Recovery használatával.