---
title: 'Azure VPN Gateway: tudnivalók a P2S-útválasztásról'
description: Ismerje meg az Azure pont – hely VPN-útválasztást a különböző operációs rendszerekhez, a távelérési protokollokhoz és a virtuális hálózati konfigurációkhoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827195"
---
# <a name="about-point-to-site-vpn-routing"></a>Tudnivalók a pont–hely VPN-útválasztásról

Ebből a cikkből megtudhatja, hogyan viselkedik az Azure pont – hely VPN-útválasztás. A P2S VPN-útválasztási viselkedés függ az ügyfél operációs rendszertől, a VPN-kapcsolathoz használt protokolltól, valamint arról, hogy a virtuális hálózatok (virtuális hálózatok) hogyan kapcsolódnak egymáshoz.

Az Azure jelenleg két protokollt támogat a táveléréshez, a IKEv2 és az SSTP-hez. A IKEv2 számos ügyfél-operációs rendszeren támogatott, többek között Windows, Linux, macOS, Android és iOS rendszereken. Az SSTP csak Windows rendszeren támogatott. Ha módosítja a hálózat topológiáját, és Windows VPN-ügyfeleket használ, a Windows-ügyfelekhez tartozó VPN-ügyfélszoftvert le kell tölteni és újra kell telepíteni ahhoz, hogy a módosítások érvénybe lépjenek az ügyfélen.

> [!NOTE]
> Ez a cikk csak a IKEv2 vonatkozik.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Tudnivalók a diagramokról

Ebben a cikkben számos különböző diagram szerepel. Mindegyik szakasz egy másik topológiát vagy konfigurációt mutat be. A jelen cikk, a helyek közötti (S2S) és a VNet-VNet kapcsolatok ugyanúgy működnek, mint az IPsec-alagutak. A cikk összes VPN-átjárója Route-alapú.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Egy elkülönített VNet

Ebben a példában a pont – hely VPN Gateway-kapcsolat olyan VNet vonatkozik, amely nincs csatlakoztatva, vagy más virtuális hálózattal (VNet1) van társítva. Ebben a példában az ügyfelek hozzáférhetnek a VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Elkülönített VNet-Útválasztás" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek hozzáférhetnek a VNet1

* A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Több társas virtuális hálózatok

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 a VNet2-mel van társítva. A VNet 2 a VNet3. A VNet1 a Vnet4 felé-mel van társítva. Nincs közvetlen társítás a VNet1 és a VNet3 között. A VNet1 "átjáró engedélyezése", a VNet2 és a Vnet4 felé esetében engedélyezve van a "távoli átjárók használata" beállítás.

A Windowst használó ügyfelek közvetlenül hozzáférhetnek a virtuális hálózatok, de a VPN-ügyfelet újra le kell tölteni, ha a VNet vagy a hálózati topológia bármilyen módosítást végez. A nem Windows rendszerű ügyfelek közvetlenül is hozzáférhetnek a virtuális hálózatok. A hozzáférés nem tranzitív, és csak a közvetlenül összetartozó virtuális hálózatok korlátozódik.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Több társas virtuális hálózatok" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Címterület:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Vnet4 felé: 10.4.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2 és a Vnet4 felé, de a VPN-ügyfelet újra le kell tölteni, hogy az összes topológiai módosítás érvénybe lép.

* A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet1, a VNet2 és a Vnet4 felé

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Több virtuális hálózatok csatlakozik egy S2S VPN használatával

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 helyek közötti VPN-kapcsolattal csatlakozik a VNet2-hez. A VNet2 helyek közötti VPN-kapcsolattal csatlakozik a VNet3-hez. A VNet1 és a VNet3 között nincs közvetlen vagy helyek közötti VPN-kapcsolat. A helyek közötti kapcsolatok nem futnak BGP-vel az útválasztáshoz.

A Windows vagy más támogatott operációs rendszert használó ügyfelek csak a VNet1 férhetnek hozzá. További virtuális hálózatok eléréséhez a BGP-t kell használni.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Több virtuális hálózatok és S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek csak a VNet1 férhetnek hozzá

* A nem Windows rendszerű ügyfelek csak a VNet1 férhetnek hozzá

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Több virtuális hálózatok csatlakozik egy S2S VPN (BGP) használatával

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 helyek közötti VPN-kapcsolattal csatlakozik a VNet2-hez. A VNet2 helyek közötti VPN-kapcsolattal csatlakozik a VNet3-hez. A VNet1 és a VNet3 között nincs közvetlen vagy helyek közötti VPN-kapcsolat. A helyek közötti kapcsolatok BGP-t futtatnak az útválasztáshoz.

A Windows vagy más támogatott operációs rendszert használó ügyfelek hozzáférhetnek a két hálózat közötti pont-pont típusú VPN-kapcsolattal összekapcsolt összes virtuális hálózatok, de a csatlakoztatott virtuális hálózatok útvonalakat manuálisan kell hozzáadni a Windows-ügyfelekhez.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Több virtuális hálózatok és S2S (BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek elérhetik a VNet1, a VNet2 és a VNet3, de a VNet2 és a VNet3 útvonalakat manuálisan kell hozzáadni.

* A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet1, a VNet2 és a VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Egy VNet és egy fiókiroda

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 nincs csatlakoztatva/társítva más virtuális hálózattal, de egy helyszíni helyhez csatlakozik egy olyan helyek közötti VPN-kapcsolaton keresztül, amely nem BGP-t futtat.

A Windows és a nem Windows rendszerű ügyfelek csak a VNet1 férhetnek hozzá.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="Útválasztás VNet és fiókirodával" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* Hely1:10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek csak VNet1 férhetnek hozzá

* A nem Windows rendszerű ügyfelek csak a VNet1 férhetnek hozzá

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Egy VNet és egy fiókiroda (BGP)

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 nincs csatlakoztatva vagy más virtuális hálózattal társítva, de egy helyszíni helyhez (hely1) van csatlakoztatva, amely BGP-t futtató, helyek közötti VPN-kapcsolaton keresztül csatlakozik.

A Windows-ügyfelek elérhetik a VNet és a fiókirodát (hely1), de a hely1 útvonalakat manuálisan kell hozzáadni az ügyfélhez. A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet és a helyszíni fiókirodához is.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="Útválasztás VNet és fiókirodával – BGP" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* Hely1:10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek hozzáférhetnek a VNet1 és a hely1, de a hely1 útvonalakat manuálisan kell hozzáadni.

* A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet1 és a hely1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Több virtuális hálózatok van csatlakoztatva a S2S és a fiókirodák használatával

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 helyek közötti VPN-kapcsolattal csatlakozik a VNet2-hez. A VNet2 helyek közötti VPN-kapcsolattal csatlakozik a VNet3-hez. A VNet1 és a VNet3 hálózatok között nincs közvetlen vagy helyek közötti VPN-alagút. A VNet3 helyek közötti VPN-kapcsolat használatával csatlakozik egy fiókirodához (hely1). Az összes VPN-kapcsolat nem fut BGP-vel.

Minden ügyfél csak a VNet1 fér hozzá.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Több VNet S2S és fiókirodát ábrázoló diagram" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Hely1:10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek csak a VNet1 férhetnek hozzá

* A nem Windows rendszerű ügyfelek csak a VNet1 férhetnek hozzá

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Több virtuális hálózatok van csatlakoztatva a S2S és a fiókiroda (BGP) használatával

Ebben a példában a pont – hely VPN Gateway-kapcsolat a VNet1. A VNet1 helyek közötti VPN-kapcsolattal csatlakozik a VNet2-hez. A VNet2 helyek közötti VPN-kapcsolattal csatlakozik a VNet3-hez. A VNet1 és a VNet3 hálózatok között nincs közvetlen vagy helyek közötti VPN-alagút. A VNet3 helyek közötti VPN-kapcsolat használatával csatlakozik egy fiókirodához (hely1). Minden VPN-kapcsolat BGP-t futtat.

A Windowst használó ügyfelek hozzáférhetnek a helyek közötti VPN-kapcsolattal összekapcsolt virtuális hálózatok és webhelyekhez, de a VNet2, a VNet3 és a hely1 útvonalakat manuálisan kell hozzáadni az ügyfélhez. A nem Windows rendszerű ügyfelek a két hálózat közötti pont-pont típusú VPN-kapcsolaton keresztül csatlakoztatott virtuális hálózatok és webhelyeket nem manuális beavatkozás nélkül érhetik el. A hozzáférés tranzitív, és az ügyfelek hozzáférhetnek az erőforrásokhoz az összes csatlakoztatott virtuális hálózatok és telephelyen (helyszíni).

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="Multi-VNet S2S és fiókirodák" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Címtér

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Hely1:10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* A nem Windows rendszerű ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* A Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2, a VNet3 és a hely1, de a VNet2, a VNet3 és a hely1 útvonalakat manuálisan kell hozzáadni az ügyfélhez.

* A nem Windows rendszerű ügyfelek hozzáférhetnek a VNet1, a Vnet2, a VNet3 és a hely1.

## <a name="next-steps"></a>Következő lépések

A P2S VPN létrehozásának megkezdéséhez tekintse meg [a P2S VPN létrehozása a Azure Portal használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md) című témakört.
