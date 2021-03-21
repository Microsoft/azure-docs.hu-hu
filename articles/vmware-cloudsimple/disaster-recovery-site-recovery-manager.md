---
title: Azure VMware-megoldás a CloudSimple-ben – a VMware Site Recovery Manager használatával helyreállítási helyként állíthatja be a privát felhőt
description: Ismerteti, hogyan állíthatja be a CloudSimple saját Felhőjét vész-helyreállítási webhelyként a VMware Site Recovery Manager használatával.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897909"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Saját felhő beállítása vész-helyreállítási célként a VMware Site Recovery Managerrel

A CloudSimple saját Felhőjét vész-helyreállítási (DR) webhelyként használhatja a helyszíni VMware-munkaterhelésekhez.

A DR megoldás a vSphere Replication és a VMware Site Recovery Manager (SRM) szolgáltatáson alapul. Egy hasonló módszer követhető, amely lehetővé teszi, hogy a saját Felhőjét elsődleges helyként engedélyezze, amelyet a helyszíni helyreállítási hely véd.

A CloudSimple megoldás:

* Nem kell kifejezetten DR-hez beállítania az adatközpontot.
* Lehetővé teszi, hogy kihasználja azokat az Azure-telephelyeket, ahol a CloudSimple üzembe helyezése a globális földrajzi rugalmasság érdekében történik.
* Lehetőséget biztosít a központi telepítési költségek és a DR. alapításának teljes tulajdonlási költségeinek csökkentésére.

A CloudSimple-megoldáshoz a következőket kell tennie:

* A vSphere-replikáció és az SRM telepítése, konfigurálása és kezelése a saját felhőben.
* Saját licencek megadása az SRM számára, ha a titkos felhő a védett hely. Ha helyreállítási helyként használja, nincs szükség további SRM-licencekre a CloudSimple helyhez.

Ezzel a megoldással teljes mértékben szabályozhatja a vSphere-replikációt és az SRM-t. Az ismerős felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

![Site Recovery Manager üzembe helyezése](media/srm-deployment.png)

Használhatja a vRA és az SRM bármely olyan verzióját, amely kompatibilis a saját felhőalapú és helyszíni környezetekkel. Az útmutatóban szereplő példák a vRA 6,5 és az SRM 6,5 használatát használják. Ezek a verziók kompatibilisek a CloudSimple által támogatott vSphere 6,5-mel.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy DR-megoldást az SRM használatával a saját felhőben.

1. [Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek](#verify-that-vmware-product-versions-are-compatible)
2. [A DR környezet méretének becslése](#estimate-the-size-of-your-dr-environment)
3. [Hozzon létre egy privát felhőt a környezetéhez](#create-a-private-cloud-for-your-environment)
4. [Saját felhőalapú hálózatkezelés beállítása az SRM-megoldáshoz](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Helyek közötti VPN-kapcsolat beállítása a helyszíni hálózat és a privát felhő között, valamint a szükséges portok megnyitása](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Infrastruktúra-szolgáltatások beállítása a saját felhőben](#set-up-infrastructure-services-in-your-private-cloud)
7. [A vSphere replikációs berendezés telepítése a helyszíni környezetben](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [VSphere-replikációs berendezés telepítése saját felhőalapú környezetben](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Az SRM-kiszolgáló telepítése a helyszíni környezetben](#install-srm-server-in-your-on-premises-environment)
10. [Az SRM-kiszolgáló telepítése a saját felhőbe](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek

Az útmutatóban szereplő konfigurációkra az alábbi kompatibilitási követelmények vonatkoznak:

* Az SRM ugyanazon verzióját a saját felhőben és a helyszíni környezetben is üzembe kell helyezni.
* A vSphere-replikáció ugyanazon verzióját kell központilag telepíteni a saját felhőben és a helyszíni környezetben.
* A saját felhőben és a helyszíni környezetben lévő platform Services-vezérlő (PSC) verzióinak kompatibilisnek kell lenniük.
* A vCenter a saját felhőben és a helyszíni környezetben is kompatibilisnek kell lennie.
* Az SRM-és vSphere-replikáció verziójának kompatibilisnek kell lennie egymással, valamint a PSC és a vCenter verziójával.

A kapcsolódó VMware-dokumentációra és a kompatibilitási információkra mutató hivatkozásokat a [vmware site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) dokumentációjában talál.

A vCenter és a PSC saját Felhőbeli verzióinak megkereséséhez nyissa meg a CloudSimple portált. Lépjen az **erőforrások** elemre, válassza ki saját felhőjét, majd kattintson a **vSphere felügyeleti hálózat** fülre.

![vCenter & PSC-verziók a Private Cloud-ban](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>A DR környezet méretének becslése

1. Ellenőrizze, hogy az azonosított helyszíni konfiguráció a támogatott határértékeken belül van-e. A 6,5-es SRM esetében a határértékeket a VMware Tudásbázis a [site Recovery Manager 6,5 működési korlátairól](https://kb.vmware.com/s/article/2147110)szóló cikkében ismertetjük.
2. Győződjön meg arról, hogy megfelelő hálózati sávszélességgel rendelkezik a számítási feladatok méretének és a RPO követelményeinek megfelelően. A [vSphere replikálásához szükséges sávszélesség-követelmények kiszámítására vonatkozó,](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) a VMware Tudásbázisról szóló cikk útmutatást nyújt a sávszélesség korlátozásához.
3. A CloudSimple-méretezési eszköz segítségével megbecsülheti a DR-helyen szükséges erőforrásokat a helyszíni környezete elleni védelem érdekében.

### <a name="create-a-private-cloud-for-your-environment"></a>Hozzon létre egy privát felhőt a környezetéhez

Hozzon létre egy privát felhőt a CloudSimple portálon, és kövesse az utasításokat és a méretezési javaslatokat a [privát felhő létrehozása](create-private-cloud.md)című témakörben.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Saját felhőalapú hálózatkezelés beállítása az SRM-megoldáshoz

Nyissa meg a CloudSimple-portált, és állítsa be a saját felhőalapú hálózatkezelését az SRM-megoldáshoz.

Hozzon létre egy VLAN-t az SRM-megoldási hálózat számára, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Helyek közötti VPN-kapcsolat beállítása a helyszíni hálózat és a privát felhő között, valamint a szükséges portok megnyitása

Helyek közötti kapcsolat beállítása a helyszíni hálózat és a saját felhő között. Útmutatásért lásd: [VPN-kapcsolat konfigurálása saját CloudSimple-felhőhöz](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Infrastruktúra-szolgáltatások beállítása a saját felhőben

Konfigurálja az infrastruktúra-szolgáltatásokat a privát felhőben, így könnyebben kezelheti a számítási feladatokat és eszközöket.

Ha az alábbi műveletek egyikét szeretné elvégezni, vegyen fel egy külső Identitáskezelő-szolgáltatót az [Azure ad használata vCenter a CloudSimple Private Cloud](azure-ad.md) szolgáltatásban:

* Azonosítsa a helyszíni Active Directory (AD) felhasználóit a saját felhőben.
* Hozzon létre egy AD-t a saját felhőben az összes felhasználó számára.
* Használja az Azure AD-t.

Ha IP-címeket keres, IP-címek kezelését és névfeloldási szolgáltatásokat szeretne biztosítani a saját számítási feladataihoz a saját felhőben, állítson be egy DHCP-és DNS-kiszolgálót a [DNS-és DHCP-alkalmazások és-munkaterhelések beállítása a CloudSimple privát felhőben](dns-dhcp-setup.md)című részben leírtak szerint.

A *. cloudsimple.io tartományt a felügyeleti virtuális gépek és a saját Felhőbeli gazdagépek használják. A tartományra irányuló kérelmek feloldásához konfigurálja a DNS-továbbítást a DNS-kiszolgálón a [feltételes továbbító létrehozása](on-premises-dns-setup.md#create-a-conditional-forwarder)című témakörben leírtak szerint.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>A vSphere replikációs berendezés telepítése a helyszíni környezetben

Telepítse a vSphere-replikációs készüléket (vRA) a helyszíni környezetbe a VMware dokumentációjának követésével. A telepítés a következő magas szintű lépésekből áll:

1. Készítse elő a helyszíni környezetet a vRA telepítéséhez.

2. Telepítse a vRA a helyszíni környezetben a VR ISO-OVF a vmware.com-ből. A vRA 6,5 esetében [Ez a VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) rendelkezik a vonatkozó információkkal.

3. Regisztrálja helyszíni vRA a vCenter egyetlen Sign-On a helyszíni helyen. A vSphere replikálás 6,5-es verziójának részletes ismertetését lásd: VMware-dokumentum [VMware vSphere replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>VSphere-replikációs berendezés telepítése saját felhőalapú környezetben

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* IP-elérhetőség a helyszíni környezetben lévő alhálózatokról a saját felhő felügyeleti alhálózatára
* A helyszíni vSphere-környezet replikációs alhálózatának IP-elérhetősége a saját felhőben található SRM-megoldás alhálózatán

Útmutatásért lásd: [VPN-kapcsolat konfigurálása saját CloudSimple-felhőhöz](set-up-vpn.md). A lépések hasonlóak a helyszíni telepítéshez.

A CloudSimple az IP-címek helyett a vRA és az SRM telepítésekor javasolja a teljes tartománynevek használatát. Ha szeretné megtudni a vCenter és a PSC teljes tartománynevét a privát felhőben, nyissa meg a CloudSimple portált. Lépjen az **erőforrások** elemre, válassza ki saját felhőjét, majd kattintson a **vSphere felügyeleti hálózat** fülre.

![A vCenter/PSC teljes tartományneve megkeresése a privát felhőben](media/srm-resources.png)

A CloudSimple megköveteli, hogy a vRA és az SRM ne telepítse az alapértelmezett "cloudowner" felhasználót, hanem hozzon létre egy új felhasználót. Ez a saját felhőalapú vCenter-környezet magas rendelkezésre állásának és elérhetőségének biztosítására szolgál. A Private Cloud vCenter alapértelmezett cloudowner-felhasználója azonban megfelelő jogosultságokkal rendelkezik ahhoz, hogy új felhasználót hozzon létre rendszergazdai jogosultságokkal.

A vRA és az SRM telepítése előtt ki kell bővíteni a cloudowner-felhasználó vCenter-jogosultságait, majd létre kell hoznia egy rendszergazdai jogosultságokkal rendelkező felhasználót a vCenter SSO-tartományban. A saját felhőalapú felhasználói és engedélyezési modellel kapcsolatos részletekért tekintse [meg a privát felhőalapú engedélyezési modell megismerése](learn-private-cloud-permissions.md)című témakört.

A telepítés a következő magas szintű lépésekből áll:

1. [Kiterjesztési jogosultságok](escalate-private-cloud-privileges.md).
2. Hozzon létre egy felhasználót a saját felhőben a vSphere replikálás és az SRM telepítéséhez. Az alábbiakban ismertetett [vCenter felhasználói felület: hozzon létre egy felhasználót a vRA & SRM telepítéséhez](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Készítse elő saját felhőalapú környezetét a vRA telepítéséhez.
4. VRA üzembe helyezése saját felhőben a VR ISO-OVF a vmware.com-ből. A vRA 6,5 esetében [Ez a VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) releváns információkat tartalmaz.
5. A vRA vonatkozó tűzfalszabályok konfigurálása. Az alábbiakban ismertetett [CloudSimple-portálon: tűzfalszabályok konfigurálása a vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Saját Felhőbeli vRA regisztrálása a vCenter egyetlen Sign-On a saját felhőalapú webhelyén.
7. Konfigurálja a vSphere replikálási kapcsolatait a két készülék között. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a tűzfalak között. Tekintse meg [ezt a VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

A vSphere-replikáció 6,5-es verziójának részletes telepítési utasításait a VMware-dokumentum [VMware vSphere a replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)című cikkben tekintheti meg.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter felhasználói felület: felhasználó létrehozása saját felhőben a vRA és az SRM telepítéséhez

Jelentkezzen be a vCenter-be a cloudowner felhasználói hitelesítő adataival a CloudSimple-portálra vonatkozó jogosultságok kiterjesztését követően.

Hozzon létre egy új felhasználót, `srm-soln-admin` a vCenter-ben, és adja hozzá a rendszergazdák csoporthoz a vCenter-ben.
Jelentkezzen ki a vCenter cloudowner-felhasználóként, és jelentkezzen be az *SRM-soln-admin* felhasználóként.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple-portál: a vRA vonatkozó tűzfalszabályok konfigurálása

Konfigurálja a tűzfalszabályok beállításait a következő témakörben leírtak szerint: a [tűzfalak és szabályok beállítása](firewall.md) a portok megnyitásához a következők közötti kommunikáció engedélyezéséhez:

* vRA az SRM-megoldás hálózati és vCenter-és ESXi-gazdagépei a felügyeleti hálózaton.
* vRA készülékeket a két helyen.

Tekintse meg ezt a [VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Az SRM-kiszolgáló telepítése a helyszíni környezetben

Mielőtt elkezdené, ellenőrizze a következőket:

* a vSphere replikációs berendezés a helyszíni és a saját felhőalapú környezetekben van telepítve.
* A vSphere replikációs berendezések mindkét helyen csatlakoznak egymáshoz.
* Áttekintette a VMware-információkat az előfeltételekkel és az ajánlott eljárásokkal kapcsolatban. Az 6,5-es SRM esetében a VMware-dokumentumok [előfeltételei és ajánlott eljárásai](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)a következő témakörben találhatók: SRM 6,5.

Kövesse [a VMware-](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)dokumentációt az SRM-kiszolgálók telepítésének elvégzéséhez a "Kéthelyes topológia egy vCenter-példánnyal egy platform Services-vezérlőn" című témakörben leírtak szerint. Az SRM 6,5-es verziójának telepítési utasításai a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

### <a name="install-srm-server-in-your-private-cloud"></a>Az SRM-kiszolgáló telepítése a saját felhőbe

Mielőtt elkezdené, ellenőrizze a következőket:

* a vSphere replikációs berendezés a helyszíni és a saját felhőalapú környezetekben van telepítve.
* A vSphere replikációs berendezések mindkét helyen csatlakoznak egymáshoz.
* Áttekintette a VMware-információkat az előfeltételekkel és az ajánlott eljárásokkal kapcsolatban. Az SRM 6,5- [es verziójában a site Recovery Manager 6,5 Server telepítésének előfeltételeit és ajánlott eljárásait](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)tekintheti át.

A következő lépések a Private Cloud SRM telepítését ismertetik.

1. [vCenter felhasználói felület: SRM telepítése](#vcenter-ui-install-srm)
2. [CloudSimple-portál: az SRM tűzfalszabály-szabályainak konfigurálása](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter felhasználói felület: az SRM konfigurálása](#vcenter-ui-configure-srm)
4. [CloudSimple-portál: jogosultságok kiterjesztésének megszüntetése](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter felhasználói felület: SRM telepítése

Miután bejelentkezett a vCenter az SRM-soln-admin hitelesítő adatok használatával, kövesse a VMware dokumentációt az SRM-kiszolgáló telepítésének elvégzéséhez a "Kéthelyes topológia egy vCenter-példánnyal a platform Services-vezérlőkkel" című cikkben leírtak [szerint.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) Az SRM 6,5-es verziójának telepítési utasításai a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple-portál: az SRM tűzfalszabály-szabályainak konfigurálása

Konfigurálja a tűzfalszabályok beállításait a következő témakörben leírtak szerint: a [tűzfalak és szabályok beállítása](firewall.md) a közötti kommunikáció engedélyezéséhez:

Az SRM-kiszolgáló és a vCenter/PSC a privát felhőben.
Az SRM-kiszolgálók mindkét helyen

Tekintse meg [ezt a VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

#### <a name="vcenter-ui-configure-srm"></a>vCenter felhasználói felület: az SRM konfigurálása

Miután telepítette az SRM-t a privát felhőbe, hajtsa végre a következő feladatokat a VMware Site Recovery Manager telepítési és konfigurációs útmutatójának szakaszában leírtak szerint. Az 6,5-es SRM esetében az utasítások a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

1. Kapcsolja össze a Site Recovery Manager Server-példányokat a védett és a helyreállítási helyeken.
2. Hozzon létre egy ügyfélkapcsolatot a távoli Site Recovery Manager Server-példányhoz.
3. Telepítse a Site Recovery Manager licenckulcs.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portál: jogosultságok kiterjesztésének megszüntetése

A jogosultságok megszüntetéséhez tekintse meg a [jogosultságok dekiterjesztését](escalate-private-cloud-privileges.md#de-escalate-privileges)ismertető témakört.

## <a name="ongoing-management-of-your-srm-solution"></a>Az SRM-megoldás folyamatos felügyelete

Teljes körűen szabályozhatja a vSphere-replikációt és az SRM-szoftvereket a saját felhőalapú környezetében, és el kell végeznie a szükséges szoftveres életciklus-felügyeletet. A vSphere-replikáció vagy az SRM frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver minden új verziója kompatibilis a saját felhőalapú vCenter és a PSC-vel.

> [!NOTE]
> A CloudSimple jelenleg a felügyelt DR szolgáltatást kínáló lehetőségeket vizsgálja. 

## <a name="multiple-replication-configuration"></a>Több replikációs konfiguráció

 A [Array-alapú replikáció és a vSphere replikációs technológiák egyszerre is használhatók az SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) szolgáltatással együtt. Ezeket azonban a virtuális gépek elkülönített készletére kell alkalmazni (az adott virtuális gép a tömb alapú replikálás vagy a vSphere replikálás, de nem mindkettő) számára is védhető. Emellett a CloudSimple-hely több védett helyhez is konfigurálható helyreállítási helyként. További információ a többhelyes konfigurációkról: [SRM többhelyes beállításai](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Hivatkozások

* [A VMware Site Recovery Manager dokumentációja](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [A Site Recovery Manager 6,5 működési korlátai](https://kb.vmware.com/s/article/2147110)
* [A vSphere-replikáció sávszélesség-követelményének kiszámítása](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF-beállítások az vSphere-replikáció telepítésekor 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Előfeltételek és ajánlott eljárások az SRM 6,5-hez](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager egy Two-Site-topológiában a platform Services-vezérlő egy vCenter Server példányával](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6,5 telepítési és konfigurációs útmutató](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware blog az SRM-ben tömb alapú replikációval és vSphere-replikációval](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware blog az SRM többhelyes beállításaiban](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [A vSphere replikálás 5,8. x, 6. x és 8. között nyitva lévő portszámok](https://kb.vmware.com/s/article/2147112)
