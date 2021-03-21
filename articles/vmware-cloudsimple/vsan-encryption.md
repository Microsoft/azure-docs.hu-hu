---
title: Azure VMware-megoldás CloudSimple – vSAN-titkosítás konfigurálása a privát felhőhöz
description: Leírja, hogy miként konfigurálható a vSAN szoftveres titkosítási funkciója, hogy a CloudSimple saját felhője az Azure-beli virtuális hálózaton futó Kulcskezelő kiszolgálóval is működjön.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5da05c7f3c6878b0804799360e512676b9002d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899048"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>VSAN-titkosítás konfigurálása a CloudSimple privát felhőhöz

A vSAN titkosító funkciója beállítható úgy, hogy a CloudSimple saját felhője az Azure-beli virtuális hálózaton futó Kulcskezelő kiszolgálóval is működjön.

A VMware egy külső KMIP 1,1-kompatibilis, harmadik féltől származó kulcskezelő kiszolgáló (KMS) eszközt használ a vSAN-titkosítás használatakor. Kihasználhatja a VMware által hitelesített, támogatott KMS-t, és az Azure-hoz is elérhető.

Ez az útmutató ismerteti, hogyan használható az Azure Virtual Networkben futó HyTrust kulcskezelő KMS. Hasonló módszer használható bármilyen más, harmadik féltől származó, vSAN-alapú KMS-megoldáshoz.

Ehhez a KMS-megoldáshoz a következőket kell tennie:

* VMware Certified harmadik féltől származó KMS-eszköz telepítése, konfigurálása és kezelése az Azure Virtual Networkben.
* Saját licencek megadása a KMS-eszközhöz.
* Konfigurálja és kezelje a vSAN-titkosítást a privát felhőben az Azure Virtual Networkben futó, harmadik féltől származó KMS-eszköz használatával.

## <a name="kms-deployment-scenario"></a>KMS üzembe helyezési forgatókönyv

A KMS-kiszolgáló fürtje az Azure Virtual Networkben fut, és az IP-cím elérhető a saját felhő vCenter a konfigurált Azure ExpressRoute-kapcsolaton keresztül.

![.. /media/KMS-fürt az Azure Virtual Networkben](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás üzembe helyezése

Az üzembe helyezési folyamat a következő lépésekből áll:

1. [Ellenőrizze, hogy teljesülnek-e az Előfeltételek](#verify-prerequisites-are-met)
2. [CloudSimple-portál: ExpressRoute-társítási információk beszerzése](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure Portal: a virtuális hálózat összekötése a saját felhővel](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure Portal: HyTrust-vezérlő fürt üzembe helyezése a virtuális hálózaton](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMIP-kiszolgáló konfigurálása](#hytrust-webui-configure-the-kmip-server)
6. [vCenter felhasználói felület: a vSAN titkosításának konfigurálása KMS-fürt használatára az Azure-beli virtuális hálózaton](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Előfeltételek ellenőrzése

Ellenőrizze a következőket az üzembe helyezés előtt:

* A kiválasztott KMS-gyártó, eszköz és verzió a vSAN kompatibilitási listán található.
* A kiválasztott szállító támogatja az eszköz Azure-ban való futtatásának verzióját.
* A KMS-eszköz Azure-verziója KMIP 1,1-kompatibilis.
* Egy Azure Resource Manager és egy virtuális hálózat már létre van hozva.
* Már létre lett hozva egy CloudSimple saját felhő.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple-portál: ExpressRoute-társítási információk beszerzése

A telepítés folytatásához szüksége lesz az engedélyezési kulcsra és a ExpressRoute, valamint az Azure-előfizetéshez való hozzáférésre. Ezek az információk a CloudSimple-portál Virtual Network kapcsolatok lapján érhetők el. Útmutatásért lásd: [virtuális hálózati kapcsolatok beállítása a privát felhőhöz](virtual-network-connection.md). Ha bármilyen problémája van az információ beszerzésével, nyisson meg egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure Portal: a virtuális hálózat összekötése a saját felhővel

1. Hozzon létre egy virtuális hálózati átjárót a virtuális hálózathoz a [ExpressRoute virtuális hálózati átjáró konfigurálása a Azure Portal használatával](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)című témakör útmutatását követve.
2. Kapcsolja össze a virtuális hálózatot a CloudSimple ExpressRoute-áramkörrel a [virtuális hálózat csatlakoztatása egy ExpressRoute-áramkörhöz a portál használatával](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)című témakör útmutatását követve.
3. Az üdvözlő e-mailben kapott CloudSimple ExpressRoute-áramköri információinak használatával a CloudSimple-ből összekapcsolhatja a virtuális hálózatot az Azure-beli CloudSimple ExpressRoute-áramkörrel.
4. Adja meg az engedélyezési kulcsot és a társ-áramköri URI-t, adjon nevet a kapcsolatnak, majd kattintson **az OK** gombra.

![Adja meg a CS társközi áramköri URI-t a virtuális hálózat létrehozásakor](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure Portal: helyezzen üzembe egy HyTrust-vezérlő fürtöt a virtuális hálózat Azure Resource Managerjában

Ha HyTrust-vezérlő fürtöt szeretne üzembe helyezni a virtuális hálózat Azure Resource Managerban, hajtsa végre a következő feladatokat. A részletekért tekintse meg a [HyTrust dokumentációját](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) .

1. Hozzon létre egy Azure hálózati biztonsági csoportot (NSG-hytrust) a megadott bejövő szabályokkal a HyTrust dokumentációjának utasításait követve.
2. SSH kulcspár létrehozása az Azure-ban.
3. Telepítse a kezdeti távvezérlési csomópontot a lemezképből az Azure Marketplace-en.  Használja a generált kulcspár nyilvános kulcsát, és válassza a **NSG-hytrust** lehetőséget a kulcstartó csomóponthoz tartozó hálózati biztonsági csoportként.
4. Alakítsa át a vezérlő magánhálózati IP-címét statikus IP-címmé.
5. SSH-t a kulcstartó virtuális géphez a nyilvános IP-címe és a korábban említett kulcspár titkos kulcsa használatával.
6. Amikor a rendszer rákérdez az SSH-rendszerhéjra, válassza a `No` csomópont beállítása kezdeti vezérlő csomópontként lehetőséget.
7. További vezérlő-csomópontok hozzáadásához ismételje meg az eljárás 3-5. lépéseit, és válassza ki `Yes` , hogy mikor kér be egy meglévő fürthöz való hozzáadást.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: a KMIP-kiszolgáló konfigurálása

Nyissa meg a https://*Public-IP* címet, ahol a *Public-IP* a távvezérlési csomópont virtuális gépe nyilvános IP-címe. Kövesse az alábbi lépéseket a [HyTrust dokumentációjában](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [KMIP-kiszolgáló konfigurálása](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Tanúsítványfájl létrehozása VMware-titkosításhoz](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter felhasználói felület: a vSAN titkosításának konfigurálása KMS-fürt használatára az Azure-beli virtuális hálózaton

A HyTrust utasításait követve [hozzon létre egy KMS-fürtöt a vCenter-ben](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![KMS-fürt részleteinek hozzáadása a vCenter-ben](media/vsan-config01.png)

A vCenter-ben nyissa meg a **fürt > konfigurálja** és válassza a vSAN **általános** beállítását. Engedélyezze a titkosítást, és válassza ki azt a KMS-fürtöt, amelyet korábban a vCenter adott hozzá.

![VSAN titkosítás engedélyezése és KMS-fürt konfigurálása a vCenter-ben](media/vsan-config02.png)

## <a name="references"></a>Hivatkozások

### <a name="azure"></a>Azure

[Virtuális hálózati átjáró konfigurálása a ExpressRoute-hez a Azure Portal használatával](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Virtuális hálózat összekapcsolása ExpressRoute-kapcsolatcsoporttal a portálon](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl és Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMPI-kiszolgáló konfigurálása](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Tanúsítványfájl létrehozása VMware-titkosításhoz](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[A KMS-fürt létrehozása a vSphere-ben](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
