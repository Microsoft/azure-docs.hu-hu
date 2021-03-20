---
title: 'A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: (klasszikus) portál | Microsoft Docs'
description: Létrehozhat a helyszíni hálózatot a klasszikus Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolatot.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878053"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Helyek közötti kapcsolat létrehozása az Azure Portal használatával (klasszikus)

Ez a cikk bemutatja, hogyan használhatja az Azure Portalt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a klasszikus üzemi modellre vonatkoznak, és nem vonatkoznak az aktuális üzemi modellre, a Resource Managerre. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a klasszikus üzemi modellt kívánja használni. Ha a Resource Manager-alapú üzemi modellt szeretné használni, lásd: [Helyek közötti kapcsolat létrehozása (Resource Manager)](./tutorial-site-to-site-portal.md). Javasoljuk, hogy a Resource Manager-alapú üzemi modellt használja, mivel a klasszikus modell örökölt.
* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
* A megosztott kulcs megadásához és a VPN Gateway-kapcsolat létrehozásához a PowerShell szükséges. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Konfigurációs mintaértékek ehhez a gyakorlathoz

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. A Címterület IP-címeinek használatakor általában össze kell hangolni a hálózati rendszergazdával, hogy el lehessen kerülni a címtartomány átfedését, ami befolyásolhatja az útválasztást. Ebben az esetben cserélje le az IP-címek értékeit a sajátra, ha munkakapcsolatot szeretne létrehozni.

* **Erőforráscsoport:** TestRG1
* **Virtuális hálózat neve:** TestVNet1
* **Címterület:** 10.11.0.0/16
* **Alhálózat neve:** FrontEnd
* **Alhálózati címtartomány:** 10.11.0.0/24
* **Átjáróalhálózat:** 10.11.255.0/27
* **Régió:** (USA) USA keleti régiója
* **Helyi hely neve:** Site2
* **Ügyfélcímtér:** A helyszíni helyen található címtér.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Virtuális hálózat létrehozása

Amikor helyek közötti használatra hoz létre virtuális hálózatot, gondoskodnia kell róla, hogy a megadott címterek ne legyenek átfedésben az azon helyi helyekhez tartozó ügyfélcímterekkel, amelyekhez csatlakozni szeretne. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni.

* Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal.

* Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>A hely és az átjáró konfigurálása

### <a name="to-configure-the-site"></a>A hely konfigurálása

A helyi hely általában a használat helyszínét jelenti. Tartalmazza azon VPN-eszköz IP-címét, amelyhez kapcsolatot szeretne létesíteni, valamint azokat az IP-címtartományokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva.

1. A VNet lapjának **Beállítások** területén válassza a **helyek közötti kapcsolatok** lehetőséget.
1. A helyek közötti kapcsolatok lapon válassza a **+ Hozzáadás** lehetőséget.
1. A **VPN-kapcsolat és-átjáró konfigurálása** lapon a **kapcsolat típusa** beállításnál hagyja kiválasztva a **helyek** közötti beállítást. Ehhez a gyakorlathoz a [példában szereplő értékek](#values) és a saját értékek kombinációját kell használnia.

   * **VPN-átjáró IP-címe**: Ez a helyszíni hálózaton található VPN-eszköz nyilvános IP-címe. A VPN-eszköznek nyilvános IPv4 IP-címmel kell rendelkeznie. Adjon meg egy érvényes nyilvános IP-címet azon VPN-eszköznek, amelyhez csatlakozni kíván. Az Azure-nak elérhetőnek kell lennie. Ha nem ismeri a VPN-eszköz IP-címét, használhat egy helyőrző értéket (érvényes nyilvános IP-cím formátumban), amelyet később módosíthat.

   * **Ügyfélcímtér**: A helyszíni hálózatra ezen az átjárón keresztül átirányítani kívánt IP-címtartományok listája. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben olyan egyéb hálózatok tartományaival, amelyekhez a virtuális hálózat csatlakozik, illetve magának a virtuális hálózatnak a címtartományaival.
1. A lap alján ne válassza a felülvizsgálat + létrehozás lehetőséget. Ehelyett válassza a **Tovább: átjáró>** elemet.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>A virtuális hálózati átjáró konfigurálása

1. Az **átjáró** lapon válassza ki a következő értékeket:

   * **Méret:** Ez a virtuális hálózati átjáró létrehozásához használt átjáró SKU. A klasszikus VPN-átjárók a régi (örökölt) átjáró-termékváltozatokat használják. Az átjárók örökölt termékváltozatairól [a virtuális hálózati átjárók termékváltozatainak (régi termékváltozatok) használatát bemutató](vpn-gateway-about-skus-legacy.md) cikkben talál további információt. Ehhez a gyakorlathoz válassza a **standard** lehetőséget.

   * **Útválasztási típus:** Válassza ki az átjáró útválasztási típusát. Ez VPN-típus néven is ismert. Fontos, hogy a megfelelő típust válassza, mert az átjárót nem lehet egyik típusról a másikra konvertálni. A VPN-eszköznek kompatibilisnek kell lennie a kiválasztott útválasztási típussal. További információ az útválasztási típusról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype). A „RouteBased” és „PolicyBased” VPN-típusokkal további cikkek is foglalkozhatnak. A „Dynamic” (Dinamikus) a „RouteBased”, a „Static” (Statikus) a „PolicyBased” típusra utal. Általában dinamikus útválasztást szeretne használni.

   * **Átjáró-alhálózat:** A megadott átjáró-alhálózat mérete a létrehozni kívánt VPN-átjáró konfigurációjától függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt /27-eset vagy /28-asat használni. Ez nagyobb, több címet tartalmazó alhálózatot hoz létre. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

1. A beállítások ellenőrzéséhez kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. Válassza a **Létrehozás** elemet a telepítéshez. Akár 45 percet is igénybe vehet, hogy virtuális hálózati átjárót hozzon létre a kiválasztott átjáró-SKU-tól függően.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következő értékeket kell megadnia:

* Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Értékek beolvasása

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>A kapcsolat létrehozása

> [!NOTE]
> A klasszikus üzemi modell esetében ez a lépés nem érhető el a Azure Portal vagy a Azure Cloud Shellon keresztül. Az Azure PowerShell-parancsmagok Service Management (SM) verzióját helyileg kell használni az asztalról.
>

Ebben a lépésben az előző lépésekből származó értékeket használva beállítja a megosztott kulcsot, és létrehozza a kapcsolatokat. A beállított kulcsnak meg kell egyeznie a VPN-eszköz konfigurációjához használt kulccsal.

1. Állítsa be a megosztott kulcsot, és hozza létre a kapcsolatot.

   * Módosítsa a-VNetName értéket és a-LocalNetworkSiteName értéket. Amikor szóközt tartalmazó nevet ad meg, zárja az értéket szimpla idézőjelek közé.
   * A "-SharedKey" egy generált érték, majd adja meg a következőt:. A példában a "abc123"-t használtuk, de az is lehet, hogy a (és a) összetettebb. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Amikor létrejött a kapcsolat, az eredmény **Status: Successful** (Állapot: Sikeres) lesz.

## <a name="verify-your-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Ha nem tud csatlakozni, tekintse át a **Hibaelhárítás** című szakaszt a tartalomjegyzékben a bal oldali panelen.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>VPN-átjáró alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. A lépéseket lásd: [VPN Gateway alaphelyzetbe állítása](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Az átjárók termékváltozatainak módosítása

Az átjárók SKU-jának módosításának lépéseiért lásd: [ÁTJÁRÓ SKU-jának átméretezése](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml).
* Információk a kényszerített bújtatásról: [Információk a kényszerített bújtatásról](vpn-gateway-about-forced-tunneling.md).