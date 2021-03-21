---
title: Azure VMware-megoldás CloudSimple – saját felhőalapú webhely használata virtuális asztali infrastruktúra üzemeltetéséhez a VMware Horizon használatával
description: Ismerteti, hogyan használható a CloudSimple saját felhőalapú webhelye virtuális asztali infrastruktúra üzemeltetésére a VMware Horizon használatával
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 513af1ba2e354412278fb88af4ee6527c236fae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895597"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>A CloudSimple privát felhőalapú webhelyének használata virtuális asztali infrastruktúra futtatására a VMware Horizon használatával

A CloudSimple saját felhőalapú webhelyén üzemeltetheti a virtuális asztali infrastruktúrát (VDI) a VMware Horizon 7. x használatával. Az alábbi ábrán a VDI logikai megoldási architektúrája látható.

![Horizont üzembe helyezése](media/horizon-deployment.png)

Ezzel a megoldással teljes mértékben szabályozhatja a Horizon View Manager és az alkalmazás kötetét. Az ismerős felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

A CloudSimple-megoldáshoz a következőket kell tennie:

* A VMware Horizon 7. x telepítése, konfigurálása és kezelése a saját felhőben.
* Adja meg saját Horizon-licenceit.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy VDI-megoldást a saját Felhőbeli horizont használatával.

1. [Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek](#verify-that-vmware-product-versions-are-compatible)
2. [Az asztali környezet méretének becslése](#estimate-the-size-of-your-desktop-environment)
3. [Hozzon létre egy privát felhőt a környezetéhez](#create-a-private-cloud-for-your-environment)
4. [A VMware Horizon telepítése a saját felhőbe](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek

* Győződjön meg arról, hogy a horizont, az alkalmazás kötetei, az egyesített hozzáférésű átjáró és a felhasználói környezet kezelője aktuális és tervezett verziói kompatibilisek egymással, valamint a vCenter és a PSC-mel a privát felhőben. A kompatibilitási információk: [VMware kompatibilitási mátrix a Horizon 7,5-hez](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Ha szeretné megtudni a vCenter és a PSC aktuális verzióját a saját felhőben, lépjen az **erőforrások** elemre a [CloudSimple-portálon](access-cloudsimple-portal.md), válassza ki saját felhőjét, majd kattintson a **vSphere felügyeleti hálózat** fülre.

![vCenter és PSC verziók](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Az asztali környezet méretének becslése

* Ellenőrizze, hogy az azonosított konfiguráció VMware működési korlátokon belül van-e.
* Becsülje meg az összes asztali számítógép és a horizont Management-összetevői számára szükséges erőforrásokat.

### <a name="create-a-private-cloud-for-your-environment"></a>Hozzon létre egy privát felhőt a környezetéhez

1. Hozzon létre egy privát felhőt a CloudSimple portálon a [saját felhőalapú környezet konfigurálása](quickstart-create-private-cloud.md)című részben ismertetett útmutatás alapján.  A CloudSimple létrehoz egy "cloudowner" nevű alapértelmezett vCenter-felhasználót minden újonnan létrehozott privát felhőben. A saját felhőalapú felhasználói és engedélyezési modellel kapcsolatos részletekért lásd: [a privát felhőalapú engedélyek modell megismerése](learn-private-cloud-permissions.md).
2. Hozzon létre egy VLAN-t a saját felhőben a horizont felügyeleti síkon, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md). Ez az a hálózat, ahol az összes megoldás-összetevő (egyesített hozzáférési átjáró, kapcsolati kiszolgáló, alkalmazás-kötet kiszolgáló és felhasználói környezet-kezelő kiszolgáló) telepítve lesz.
3. Döntse el, hogy szeretne-e külső identitás-szolgáltatót használni saját Felhőbeli vCenter. Ha igen, válasszon egyet a következő lehetőségek közül:
    * Külső identitás-szolgáltatóként használhatja a helyszíni Active Directory. Útmutatásért lásd: [vCenter Identity sources](set-vcenter-identity.md).
    * Hozzon létre egy Active Directory kiszolgálót a saját felhőben a Horizon felügyeleti sík VLAN-ban, hogy külső identitás-szolgáltatóként használhassa. Útmutatásért lásd: [vCenter Identity sources](set-vcenter-identity.md).
    * Állítson be egy DHCP-és DNS-kiszolgálót a horizont felügyeleti sík VLAN-ban a privát felhőben. Útmutatásért lásd: [DNS-és DHCP-alkalmazások és-munkaterhelések beállítása a CloudSimple privát felhőben](dns-dhcp-setup.md).
4. Konfigurálja a DNS-továbbítást a privát felhőbe telepített DNS-kiszolgálón. Útmutatásért lásd: [feltételes továbbító létrehozása](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>A VMware Horizon telepítése a saját felhőbe

A következő üzembe helyezési ábrán egy privát felhőben üzembe helyezett Horizon-megoldás látható. Az egyesített hozzáférési átjáró, az AD/DC, a View és az App Volume Server telepítve van a felhasználó által létrehozott VLAN 234-ben. Az egyesített hozzáférési átjáró rendelkezik egy olyan hozzárendelt nyilvános IP-címmel, amely elérhető az internetről. A Horizon asztali készlet virtuális gépei a VLAN 235-ben vannak üzembe helyezve, hogy további elkülönítést és biztonságot nyújtsanak.

![Horizontos üzembe helyezés a privát felhőben](media/horizon-private-cloud.png)

Az alábbi részekben az ábrán láthatóhoz hasonló központi telepítés beállítására vonatkozó utasításokat vázoljuk. Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* A CloudSimple-portál használatával létrehozott privát felhő, amely elegendő kapacitással rendelkezik az asztali készletek futtatásához.
* Elegendő sávszélesség a helyszíni környezet és a privát felhőalapú környezet között az asztali számítógépek hálózati forgalmának támogatásához.
* Helyek közötti VPN-alagút, amely a helyszíni adatközpont és a privát felhő között állítható be.
* A helyszíni környezetben lévő végfelhasználói alhálózatok IP-elérhetősége a CloudSimple privát Felhőbeli alhálózatokra.
* Az AD/DHCP/DNS telepítve van a saját felhőhöz.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple-portál: dedikált VLAN/alhálózat létrehozása asztali készletekhez

Hozzon létre egy VLAN-t a Horizon Desktop-készletek számára, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md). Ez az a hálózat, ahol az összes asztali virtuális gép futni fog.

A horizonton üzemelő példányának biztonságossá tételéhez kövesse az általános biztonsági eljárásokat:

* Csak az asztali RDP-forgalom/SSH-forgalom engedélyezése az asztali virtuális gépeken.
* Csak a Horizon felügyeleti sík VLAN és az asztali készlet VLAN közötti felügyeleti forgalom engedélyezése.
* Csak a helyszíni hálózatról származó felügyeleti forgalom engedélyezése.

Az ajánlott eljárásokat a [Tűzfalszabályok](firewall.md) CloudSimple-portálon történő konfigurálásával kényszerítheti ki.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple-portál: tűzfalszabályok konfigurálása a horizont felügyeleti síkja védelméhez

Állítsa be az alábbi szabályokat a CloudSimple-portálon. Útmutatásért tekintse [meg a tűzfalak és szabályok beállítása](firewall.md)című témakört.

1. A CloudSimple N-S tűzfalon konfigurálja a tűzfalszabályok közötti kommunikációt, hogy lehetővé váljon a helyszíni alhálózatok és a horizont felügyeleti VLAN hálózata közötti kommunikáció, hogy csak a VMware Document [Horizon port listán](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) felsorolt hálózati portok engedélyezettek legyenek.

2. Hozzon létre E-W tűzfalszabályok a horizont Management VLAN és az asztali készlet VLAN között a privát felhőben.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple-portál: nyilvános IP-cím létrehozása az egyesített hozzáférésű átjáróhoz

Hozzon létre egy nyilvános IP-címet az egyesített hozzáférésű átjáró berendezés számára, amely lehetővé teszi az internetes asztali ügyfélkapcsolatok használatát. Útmutatásért lásd: [nyilvános IP-címek lefoglalása](public-ips.md).

Ha a telepítés befejeződött, a nyilvános IP-cím hozzárendelése és listázása a nyilvános IP-címek lapon történik.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple-portál: jogosultságok kiterjesztésének megvonása

Az alapértelmezett "cloudowner" felhasználó nem rendelkezik megfelelő jogosultságokkal a saját Felhőbeli vCenter a horizont telepítéséhez, így a felhasználó vCenter jogosultságait ki kell bővíteni. További információ: a [jogosultságok kiterjesztésének](escalate-private-cloud-privileges.md)megemelése.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter felhasználói felület: hozzon létre egy felhasználót a saját felhőben a horizont telepítéséhez

1. Jelentkezzen be a vCenter a "cloudowner" felhasználói hitelesítő adatok használatával.
2. Hozzon létre egy új felhasználót, "Horizon-soln-admin"-t a vCenter-ben, és vegye fel a felhasználót a vCenter rendszergazdák csoportjába.
3. Jelentkezzen ki a vCenter "cloudowner" felhasználóként, és jelentkezzen be "Horizon-soln-admin" felhasználóként.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter felhasználói felület: VMware Horizon telepítése

Ahogy azt a korábbi logikai architektúra szakaszban is említettük, a Horizon megoldás a következő összetevőket tartalmazza:

* VMware Horizon nézet
* VMware Unified Access Gateway
* VMware app Volume Manager
* VMware felhasználói környezet kezelője

Az összetevőket a következőképpen telepítheti:

1. Az egyesített hozzáférési átjáró telepítése és konfigurálása a VMware [Unified Access Gateway üzembe helyezésére és konfigurálására](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)szolgáló VMware-dokumentum utasításait követve.

2. A [telepítési útmutató megtekintése](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)című témakör útmutatását követve telepítse a horizont nézetet a privát felhőbe.

3. Telepítse az App Volume Managert a VMware- [alkalmazások telepítése és konfigurálása](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)című témakör utasításait követve.

4. Telepítse és konfigurálja a felhasználói környezet kezelőjét a [VMware User Environment Manager telepítésének és konfigurálásának](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)utasításait követve.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Támogatási kérelem a VMware Horizon előre csomagolt alkalmazások köteteinek feltöltéséhez

A telepítési folyamat részeként az App Volume Manager előre csomagolt köteteket használ az alkalmazások és az írható kötetek kiépítéséhez. Ezek a kötetek sablonként szolgálnak az alkalmazások és az írható kötetek számára.

A kötetek saját felhőalapú adattárolóba való feltöltéséhez az ESXi-gyökér jelszava szükséges. Segítségért küldje el a [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Csatolja a AppVolumes Installer-csomagot, hogy a CloudSimple-támogatási munkatársak fel tudják tölteni a sablonokat a saját felhőalapú környezetbe.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portál: jogosultságok kiterjesztésének megszüntetése

Mostantól megadhatja a "cloudowner" felhasználó [jogosultságait](escalate-private-cloud-privileges.md#de-escalate-privileges) .

## <a name="ongoing-management-of-your-horizon-solution"></a>A Horizon-megoldás folyamatos felügyelete

A horizont és az alkalmazás mennyiségi kezelő szoftvereit teljes mértékben szabályozhatja a saját felhőalapú környezetében, és a szükséges szoftveres életciklus-felügyeletet kell végrehajtania. A horizont vagy az alkalmazás kötetének frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver minden új verziója kompatibilis a saját felhőalapú vCenter és a PSC-vel.
