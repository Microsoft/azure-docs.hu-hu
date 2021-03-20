---
title: Oracle-megoldások az Azure Virtual Machines szolgáltatásban | Microsoft Docs
description: Ismerkedjen meg az Oracle virtuálisgép-rendszerképek támogatott konfigurációinak és korlátaival Microsoft Azureon.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 2f34e0bb3c4abcf4efba807f95decd798bbc1f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669061"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-rendszerképek és azok üzembe helyezése Microsoft Azure

Ez a cikk az Oracle által az Azure piactéren közzétett virtuálisgép-rendszerképeken alapuló Oracle-megoldásokkal kapcsolatos információkat ismerteti. Ha az Oracle-alapú felhőalapú infrastruktúrával kapcsolatos felhőalapú alkalmazások megoldásait érdekli, tekintse meg az [Oracle Application solutions Microsoft Azure és az Oracle Cloud Infrastructure integrálását](oracle-oci-overview.md)ismertető témakört.

A jelenleg elérhető rendszerképek listájának lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Június 2020-én a következő képek érhetők el:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Ezeket a lemezképeket "saját licenc használatára" számítjuk, ezért csak a virtuális gép futtatásával járó számítási, tárolási és hálózati költségekért kell fizetni.  Feltételezi, hogy Ön megfelelő licenccel rendelkezik az Oracle-szoftverek használatához, és az Oracle-vel meglévő támogatási szerződése van érvényben. Az Oracle garantálta a helyszíni Azure-ba történő licenc-mobilitást. A licencek mobilitásával kapcsolatos részletekért tekintse meg a közzétett [Oracle-és Microsoft-](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) megjegyzést.

A felhasználók dönthetnek úgy is, hogy a megoldásaikat az Azure-ban létrehozott egyéni rendszerképre alapozzák, vagy egyéni rendszerképet töltenek fel a helyszíni környezetből.

## <a name="oracle-database-vm-images"></a>Oracle Database VM-rendszerképek

Az Oracle támogatja a Oracle Database 12,1-es és újabb standard és Enterprise kiadásait az Azure-ban Oracle Linux-alapú virtuálisgép-rendszerképeken.  Az Azure-beli Oracle Database éles számítási feladataihoz a legjobb teljesítmény érdekében ügyeljen arra, hogy a virtuális gép rendszerképét megfelelően méretezze, és használja prémium SSD vagy ultra SSD Managed Disks. Az Azure-beli közzétett virtuálisgép-rendszerkép használatával gyorsan lekérhet egy Oracle Databaset az Azure-ban, és megtekintheti [Oracle Database a](oracle-database-quick-create.md)gyors üzembe helyezési útmutatót.

### <a name="attached-disk-configuration-options"></a>Csatolt lemez konfigurációs beállításai

A csatolt lemezek az Azure Blob Storage szolgáltatásra támaszkodnak. Minden standard lemez másodpercenként körülbelül 500 bemeneti/kimeneti műveletből áll (IOPS). A prémium szintű lemezes ajánlat nagy teljesítményű adatbázis-számítási feladatokhoz ajánlott, és lemezenként akár 5000 IOps-t is elérhet. Ha megfelel a teljesítmény igényeinek, egyetlen lemezt is használhat. Ha azonban több csatlakoztatott lemezt is használ, akkor javíthatja a hatékony IOPS teljesítményt, az adatbázis-adathalmazokat, majd az Oracle automatikus tárterület-kezelést (ASM) használhatja. További Oracle ASM-specifikus információk: az [Oracle automatikus tárolásának áttekintése](https://www.oracle.com/technetwork/database/index-100339.html) . Az Oracle ASM Linux Azure-beli virtuális gépen történő telepítésével és konfigurálásával kapcsolatos példát a következő témakörben talál: [Oracle automatizált tároló-kezelési oktatóanyag telepítése és konfigurálása](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Megosztott tároló konfigurációs beállításai

Azure NetApp Files úgy lett kialakítva, hogy megfeleljenek a nagy teljesítményű számítási feladatok, például az adatbázisok a felhőben való futtatásának alapvető követelményeinek, és biztosítson;

- Azure natív megosztott NFS Storage szolgáltatás Oracle-alapú számítási feladatok futtatásához virtuális gépek natív NFS-ügyfelén vagy Oracle dNFS
- Méretezhető teljesítményszint, amely a IOPS-igények valós tartományát tükrözi
- Kis késleltetés
- Magas rendelkezésre állás, magas tartósság és kezelhetőség nagy méretekben, jellemzően a kritikus fontosságú vállalati munkaterhelések (például az SAP és az Oracle) iránt
- Gyors és hatékony biztonsági mentés és helyreállítás, a legagresszívebb RTO és RPO SLA-k elérése érdekében

Ezek a képességek azért lehetségesek, mert a Azure NetApp Files a NetApp® ONTAP® az Azure-adatközpont-környezetben futó összes Flash-rendszerre épül, Azure-beli natív szolgáltatásként. Az eredmény egy ideális adatbázis-tárolási technológia, amely ugyanúgy kiépíthető és felhasználható, mint a többi Azure Storage-lehetőség. Az Azure NetApp Files NFS-kötetek üzembe helyezésével és elérésével kapcsolatos további információkért tekintse meg [Azure NetApp Files dokumentációját](../../../azure-netapp-files/index.yml) . Tekintse meg az [Oracle az Azure-beli üzembe helyezéssel kapcsolatos ajánlott eljárásokat ismertető útmutatót](https://www.netapp.com/us/media/tr-4780.pdf) , amely az Oracle-adatbázisok Azure NetApp Files-on való üzemeltetésére vonatkozó ajánlott eljárások Azure NetApp Files

## <a name="licensing-oracle-database--software-on-azure"></a>Licencelés Oracle Database & szoftver az Azure-ban

Microsoft Azure a Oracle Database futtatására szolgáló, jóváhagyott felhőalapú környezet. Az Oracle Core Factor tábla nem alkalmazható, ha Oracle-adatbázisokat engedélyez a felhőben. Ehelyett ha olyan virtuális gépeket használ, amelyeken engedélyezve van a Hyper-Threading technológia a nagyvállalati kiadású adatbázisokhoz, a két vCPU egy Oracle Processor-licenccel egyenértékűnek számít, ha engedélyezve van a feleznie (ahogy azt a szabályzat dokumentuma is jelzi). [Itt](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)megtalálja a szabályzat részleteit.
Az Oracle-adatbázisok általában nagyobb memóriát és IO-t igényelnek. Emiatt a memória- [optimalizált virtuális gépek](../../sizes-memory.md) ajánlottak ezekhez a számítási feladatokhoz. A számítási feladatok további optimalizálása érdekében [korlátozott alapszintű vCPU](../../constrained-vcpu.md) ajánlott olyan Oracle Database munkaterhelések esetén, amelyek nagy memóriát, tárterületet és I/O-sávszélességet igényelnek, de nem magas alapszám.

Az Oracle-szoftverek és-munkaterhelések helyi rendszerről Microsoft Azureba történő áttelepítésekor az Oracle a licencek mobilitását az Azure-beli [Oracle – gyakori kérdések](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) című szakaszban leírtak szerint

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vész-helyreállítási megfontolások

Ha Oracle-adatbázisokat használ az Azure-ban, az állásidő elkerülése érdekében a magas rendelkezésre állású és vész-helyreállítási megoldás végrehajtásáért felelős.

A Oracle Database Enterprise Edition magas rendelkezésre állása és vész-helyreállítása (az Oracle RAC-ra való támaszkodás nélkül) az Azure-ban az [Adatvédelem, az aktív adatvédelem](https://www.oracle.com/database/technologies/high-availability/dataguard.html)vagy az [Oracle-GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)használatával érhető el, két külön virtuális gépen két adatbázissal. Mindkét virtuális gépnek ugyanabban a [virtuális hálózatban](https://azure.microsoft.com/documentation/services/virtual-network/) kell lennie annak biztosításához, hogy a privát állandó IP-címen keresztül hozzáférhessenek egymáshoz.  Javasoljuk továbbá, hogy a virtuális gépeket ugyanazon rendelkezésre állási csoportba helyezze, hogy az Azure külön tartalék tartományba és frissítési tartományba helyezze őket. Ha szeretné, hogy a Geo-redundancia legyen, állítsa be a két adatbázist két különböző régió között, és kapcsolódjon a két példányhoz egy VPN Gateway.

Az Azure-beli [Oracle-adatvédelmet](configure-oracle-dataguard.md) bemutató oktatóanyag végigvezeti az Azure-beli alapszintű telepítési eljáráson.  

Az Oracle-adatvédelemmel a magas rendelkezésre állást egyetlen virtuális gép elsődleges adatbázisa, egy másodlagos (készenléti) adatbázis egy másik virtuális gépen, valamint a közöttük zajló egyirányú replikáció is lehetővé teheti. Az eredmény olvasási hozzáférés az adatbázis másolatához. Az Oracle GoldenGate használatával kétirányú replikációt konfigurálhat a két adatbázis között. Ha meg szeretné tudni, hogyan állíthat be magas rendelkezésre állású megoldást az adatbázisaihoz ezekkel az eszközökkel, tekintse meg az Oracle webhelyén az [aktív adatvédelmet](https://www.oracle.com/database/technologies/high-availability/dataguard.html) és a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentációját. Ha írási-olvasási hozzáféréssel kell rendelkeznie az adatbázis másolatához, használhatja az [Oracle aktív adatvédelmet](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Az Azure-beli [Oracle GoldenGate üzembe](configure-oracle-golden-gate.md) helyezése az Azure-beli alapszintű telepítési eljárás végigvezeti.

Az Azure-ban létrehozott HA-és DR-megoldás mellett egy biztonsági mentési stratégiával kell rendelkeznie az adatbázis visszaállításához. Az oktatóanyag [biztonsági mentése és helyreállítása egy Oracle Database](./oracle-overview.md) végigvezeti a konzisztens biztonsági mentés létrehozásához szükséges alapvető eljáráson.

## <a name="support-for-jd-edwards"></a>A JD Edwards támogatása

Az Oracle támogatási megjegyzése szerint a 2178595,1-as [doc-azonosító](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), a JD Edwards EnterpriseOne 9,2-es és újabb verziói **minden olyan nyilvános Felhőbeli ajánlat** esetében támogatottak, amely megfelel az adott `Minimum Technical Requirements` (MTR) szolgáltatásnak.  Olyan egyéni rendszerképeket kell létrehoznia, amelyek megfelelnek az operációs rendszer és a szoftver alkalmazás kompatibilitásának MTR-specifikációinak.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Az Oracle WebLogic Server virtuálisgép-ajánlatai

Az Oracle és a Microsoft együttműködve a WebLogic-kiszolgálót Azure-beli alkalmazás-ajánlatok gyűjteményének formájában hozza az Azure Marketplace-re.  Az ajánlatokat a következő cikk ismerteti: [Oracle Weblogic Server Azure Applications](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuálisgép-lemezképek

- **A fürtözés csak Enterprise Edition rendszeren támogatott.** A WebLogic-fürtözés csak akkor használható, ha az Oracle WebLogic Server Enterprise kiadását használja. Ne használjon fürtözést Oracle WebLogic Server Standard kiadással.
- **Az UDP csoportos küldés nem támogatott.** Az Azure támogatja az UDP-címes küldést, de nem csoportos küldést vagy szórást. Az Oracle WebLogic Server képes az Azure UDP egyedi küldési képességeire támaszkodni. Az UDP egyedi küldésre támaszkodó legjobb eredmények érdekében javasoljuk, hogy a WebLogic-fürt mérete statikus legyen, vagy ne legyen több, mint 10 felügyelt kiszolgáló.
- **Az Oracle WebLogic-kiszolgáló nyilvános és magánhálózati portokat vár a T3-hozzáféréshez (például vállalati JavaBeans használatakor).** Vegyünk például egy többrétegű forgatókönyvet, amelyben a Service Layer (EJB) alkalmazás két vagy több virtuális gépet tartalmazó Oracle WebLogic Server-fürtön fut egy *SLWLS* nevű virtuális hálózatban. Az ügyfél szintje ugyanabban a virtuális hálózatban található egy másik alhálózatban, és egy egyszerű Java-program futtatásával próbálkozik a EJB meghívásával a szolgáltatási rétegben. Mivel a szolgáltatási réteg terheléselosztása szükséges, egy nyilvános terheléselosztási végpontot kell létrehozni az Oracle WebLogic Server-fürtben található virtuális gépekhez. Ha a megadott privát port eltér a nyilvános porttól (például 7006:7008), akkor a következő hiba történik:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Ennek az az oka, hogy bármely távoli T3-hozzáférés esetén az Oracle WebLogic-kiszolgáló a terheléselosztó portját és a WebLogic felügyelt kiszolgáló portját is megegyezőként várja. Az előző esetben az ügyfél eléri a 7006-as portot (a terheléselosztó portját), és a felügyelt kiszolgáló figyeli a 7008 (a magánhálózati portot). Ez a korlátozás csak a T3-hozzáférésre vonatkozik, HTTP-n keresztül.

   A probléma elkerüléséhez használja a következő megkerülő megoldások egyikét:

- Ugyanazokat a magán-és nyilvános portszámokat használja a T3-hozzáféréshez dedikált elosztott terhelésű végpontokhoz.
- A következő JVM-paramétert adja meg az Oracle WebLogic Server indításakor:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

A kapcsolódó információkról a TUDÁSBÁZISCIKK **860340,1** -es cikkében olvashat <https://support.oracle.com> .

- **Dinamikus fürtözési és terheléselosztási korlátozások.** Tegyük fel, hogy egy dinamikus fürtöt szeretne használni az Oracle WebLogic-kiszolgálón, és egyetlen, nyilvános terheléselosztási végponton keresztül teszi elérhetővé az Azure-ban. Ezt akkor teheti meg, ha rögzített portszámot használ az egyes felügyelt kiszolgálókon (a tartományhoz nem dinamikusan van rendelve), és nem indít el több felügyelt kiszolgálót, mint a rendszergazda által nyomon követett gépek. A virtuális gépen nincs több felügyelt kiszolgáló. Ha a konfigurációban több Oracle WebLogic-kiszolgáló fut, mint a virtuális gépek (azaz ha több Oracle WebLogic Server-példány ugyanazt a virtuális gépet használja), akkor nem lehetséges, hogy az Oracle WebLogic-kiszolgálók több példánya is egy adott portszámhoz kötődik. Az adott virtuális gépen lévő többi nem sikerül.

   Ha úgy konfigurálja a felügyeleti kiszolgálót, hogy automatikusan rendeljen hozzá egyedi portszámokat a felügyelt kiszolgálókhoz, akkor a terheléselosztás nem lehetséges, mivel az Azure nem támogatja egyetlen nyilvános portról több privát portra való leképezést, ahogy ezt a konfigurációhoz szükséges lenne.
- **Az Oracle WebLogic Server több példánya egy virtuális gépen.** Az üzemelő példány követelményeitől függően érdemes lehet több Oracle WebLogic-kiszolgálót futtatni ugyanazon a virtuális gépen, ha a virtuális gép elég nagy méretű. Például egy közepes méretű virtuális gépen, amely két magot tartalmaz, dönthet úgy, hogy az Oracle WebLogic Server két példányát futtatja. Azonban azt javasoljuk, hogy ne vezessen be egyetlen meghibásodási pontot az architektúrába, ami akkor fordulhat elő, ha csak egy olyan virtuális gépet használt, amely az Oracle WebLogic Server több példányát futtatja. Ha legalább két virtuális gépet használ, jobb megközelítés lehet, és minden egyes virtuális gép az Oracle WebLogic Server több példányát futtathatja. Az Oracle WebLogic Server minden példánya továbbra is ugyanahhoz a fürthöz tartozik. Azonban jelenleg nem lehet az Azure-ban olyan terheléselosztási végpontokat használni, amelyeket az Oracle WebLogic Server-példányok ugyanazon a virtuális gépen tesznek elérhetővé, mert az Azure Load Balancer megköveteli a terheléselosztási kiszolgálók elosztását az egyedi virtuális gépek között.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuálisgép-rendszerképek

- **JDK 6 és 7 legújabb frissítések.** Habár javasoljuk, hogy a legújabb nyilvános, támogatott Java-verzió (jelenleg a Java 8) használatával az Azure a JDK 6 és 7 rendszerképeket is elérhetővé teszi. Ez olyan örökölt alkalmazások számára készült, amelyek még nem állnak készen a JDK 8 verzióra való frissítésre. Habár előfordulhat, hogy a korábbi JDK-lemezképek frissítései többé nem lesznek elérhetők az Oracle-hez készült Microsoft-partneri együttműködés miatt, az Azure által biztosított JDK 6 és 7 lemezképek egy újabb, nem nyilvános frissítést tartalmaznak, amelyet az Oracle általában csak az Oracle támogatott ügyfeleinek kiválasztott csoportja számára ajánl fel. A JDK-lemezképek új verziói időben elérhetővé válnak a JDK 6 és 7-es verziójának frissített kiadásaival.

   A JDK 6 és 7 lemezképben elérhető JDK, valamint a belőlük származtatott virtuális gépek és képek csak az Azure-on belül használhatók.
- **64 bites JDK.** Az Oracle WebLogic Server virtuálisgép-lemezképek és az Azure által biztosított Oracle JDK virtuálisgép-lemezképek a Windows Server és a JDK 64 bites verzióit tartalmazzák.

## <a name="next-steps"></a>Következő lépések

Most áttekintheti az aktuális Oracle-megoldásokat a Microsoft Azure virtuális gépek rendszerképei alapján. A következő lépés az első Oracle-adatbázis üzembe helyezése az Azure-ban.

> [!div class="nextstepaction"]
> [Oracle-adatbázis létrehozása az Azure-ban](oracle-database-quick-create.md)
