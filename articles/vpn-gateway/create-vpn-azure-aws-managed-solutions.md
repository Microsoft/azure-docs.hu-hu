---
title: VPN létrehozása az Azure és az AWS között felügyelt megoldások használatával
description: VPN-kapcsolat létrehozása az Azure és az AWS között felügyelt megoldások használatával, virtuális gépek és berendezések helyett.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/03/2021
ms.author: ricmart
ms.openlocfilehash: 82161ac92566f6589ebfb64dddc221e296ec0992
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539128"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>VPN-kapcsolat létrehozása az Azure és az AWS között felügyelt megoldások használatával

Felügyelt megoldások használatával kapcsolatot létesíthet az Azure és az AWS között. Korábban olyan készüléket vagy virtuális gépet kell használnia, amely válaszadóként működik. Az AWS virtuális magánhálózati átjárót mostantól közvetlenül az Azure VPN Gatewayhoz is összekapcsolhatjuk anélkül, hogy aggódnia kellene a IaaS-erőforrások, például a virtuális gépek kezelésével. Ebből a cikkből megtudhatja, hogyan hozhat létre VPN-kapcsolatot az Azure és az AWS között csak a felügyelt megoldások használatával.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Architektúra diagram":::

## <a name="configure-azure"></a>Az Azure konfigurálása

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

Virtuális hálózat konfigurálása. Útmutatásért tekintse meg a [Virtual Network](../virtual-network/quick-create-portal.md)rövid útmutatót.

Ebben a cikkben a következő példában szereplő értékeket használjuk:

* **Erőforráscsoport:** RG-Azure-AWS
* **Régió:** USA keleti régiója
* **Virtuális hálózat neve:** vnet – Azure
* **IPv4-címterület:** 172.10.0.0/16
* **Alhálózat neve:** alhálózat-01
* **Alhálózati címtartomány:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>VPN-átjáró létrehozása

Hozzon létre egy VPN-átjárót a virtuális hálózat számára. Útmutatásért lásd [: oktatóanyag: VPN-átjáró létrehozása és kezelése](tutorial-create-gateway-portal.md).

Ebben a cikkben a következő példában szereplő értékeket és beállításokat használjuk:

* **Átjáró neve:** VPN-Azure-AWS
* **Régió:** USA keleti régiója
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **SKU:** VpnGw1
* **Generáció:** 1. generáció
* **Virtuális hálózat:** Azon VNet kell lennie, amelyhez létre kívánja hozni az átjárót.
* **Átjáró-alhálózati címtartomány:** 172.10.0.0/27
* **Nyilvános IP-cím:** Új létrehozása
* **Nyilvános IP-cím neve:** pip-VPN-Azure-AWS
* **Aktív-aktív üzemmód engedélyezése:** Megbénít
* A **BGP konfigurálása:** Megbénít

Példa:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Virtual Network Gateway – összefoglalás":::

## <a name="configure-aws"></a>AWS konfigurálása

1. Hozza létre a virtuális magánhálózati felhőt (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="VPC-információ létrehozása":::

1. Hozzon létre egy alhálózatot a VPC (virtuális hálózat) belsejében.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Az alhálózat létrehozása":::

1. Hozzon létre egy ügyfél-átjárót, amely az Azure VPN Gateway nyilvános IP-címére mutat. A **Customer Gateway** egy AWS-erőforrás, amely információkat tartalmaz az AWS-ről a Customer Gateway-eszközről, amely ebben az esetben az Azure VPN Gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Ügyfél-átjáró létrehozása":::

1. Hozza létre a virtuális magánhálózati átjárót.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Virtuális magánhálózati átjáró létrehozása":::

1. Csatlakoztassa a virtuális magánhálózati átjárót a VPC-hoz.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="A VPG csatlakoztatása a VPC-hoz":::

1. Válassza ki a VPC-t.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Csatolja":::

1. Helyek közötti VPN-kapcsolat létrehozása.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="VPN-kapcsolat létrehozása":::

1. Állítsa az útválasztási beállítást **statikus** értékre, és mutasson az Azure subnet-01 előtagra **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Statikus útvonal beállítása":::

1. A beállítások kitöltése után **hozza létre** a-kapcsolatokat.

1. Töltse le a konfigurációs fájlt. A megfelelő konfiguráció letöltéséhez módosítsa a gyártót, a platformot és a szoftvert az **általános** értékre, mert az Azure nem érvényes beállítás.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Konfiguráció letöltése":::

1. A konfigurációs fájl tartalmazza az AWS által létrehozott két IPsec-alagút előmegosztott kulcsát és a nyilvános IP-címét.

   **1. alagút**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="1. alagút":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="1. alagút konfigurációja":::

   **2. alagút**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="2. alagút":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="2. alagút konfigurációja":::

1. Az alagutak létrehozása után az alábbihoz hasonló példa jelenik meg.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="AWS VPN-kapcsolat részletei":::

## <a name="create-local-network-gateway"></a>Helyi hálózati átjáró létrehozása

Az Azure-ban a helyi hálózati átjáró egy olyan Azure-erőforrás, amely jellemzően egy helyszíni helyet jelöl. A szolgáltatás a helyszíni VPN-eszközhöz való csatlakozáshoz használt adatokkal van feltöltve. Ebben a konfigurációban azonban a helyi hálózati átjáró jön létre, és az AWS virtuális magánhálózati átjárójának elérhetőségi adataival van feltöltve. További információ az Azure-beli helyi hálózati átjárókkal kapcsolatban: [azure VPN Gateway Settings](vpn-gateway-about-vpn-gateway-settings.md#lng).

Hozzon létre egy helyi hálózati átjárót az Azure-ban. A lépéseket a [helyi hálózati átjáró létrehozása](tutorial-site-to-site-portal.md#LocalNetworkGateway)című témakörben tekintheti meg.

A következő értékeket kell megadni:

* **Név:** A példában az LNG-Azure-AWS-t használjuk.
* **Végpont:** IP-cím
* **IP-cím:** Az AWS virtuális magánhálózati átjáró és a VPC CIDR-előtag nyilvános IP-címe. A nyilvános IP-címet a korábban letöltött konfigurációs fájlban találja.

Az AWS két IPsec-alagutat hoz létre a magas rendelkezésre állás érdekében. Az alábbi példa a nyilvános IP-címet mutatja az IPsec-alagút #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Helyi hálózati átjáró":::

## <a name="create-vpn-connection"></a>VPN-kapcsolat létrehozása

Ebben a szakaszban létrehozza a VPN-kapcsolatot az Azure-beli virtuális hálózati átjáró és az AWS-átjáró között.

1. Hozza létre az Azure-kapcsolatokat. A kapcsolat létrehozásához szükséges lépéseket lásd: [VPN-kapcsolat létrehozása](tutorial-site-to-site-portal.md#CreateConnection).

   A következő példában a megosztott kulcsot a korábban letöltött konfigurációs fájlból szerezte be. Ebben a példában az AWS által létrehozott IPsec-alagút #1 értékeit használjuk, amelyet a konfigurációs fájl ismertet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure-beli kapcsolatok objektum":::

1. A kapcsolatok megtekintése. Néhány perc elteltével létrejött a kapcsolatok.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Munkahelyi kapcsolat":::

1. Ellenőrizze, hogy az AWS IPsec-alagút **#1 működik-e.**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Az AWS-alagút ellenőrzése":::

1. Szerkessze a VPC-hoz társított útválasztási táblázatot.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Az útvonal szerkesztése":::

1. Adja hozzá az útvonalat az Azure-alhálózathoz. Ez az útvonal a VPC-átjárón keresztül fog utazni.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Az útvonal konfigurációjának mentése":::

## <a name="configure-second-connection"></a>Második kapcsolatok konfigurálása

Ebben a szakaszban egy második kapcsolódást hoz létre a magas rendelkezésre állás biztosításához.

1. Hozzon létre egy másik helyi hálózati átjárót, amely az AWS-beli IPsec-alagút #2 nyilvános IP-címére mutat. Ez a készenléti átjáró.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="A helyi hálózati átjáró létrehozása":::

1. Hozza létre a második VPN-kapcsolatát az Azure-ból az AWS-be.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Készenléti helyi hálózati átjáró-kapcsolatok létrehozása":::

1. Tekintse meg az Azure VPN Gateway kapcsolatait.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Azure-beli kapcsolatok állapota":::

1. Az AWS-kapcsolatok megtekintése. Ebben a példában láthatja, hogy a kapcsolatok már létrejöttek.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="AWS-kapcsolatok állapota":::

## <a name="to-test-connections"></a>Kapcsolatok tesztelése

1. Adjon hozzá egy **Internet Gatewayt** a VPC-hoz az AWS-ben. Az Internet Gateway logikai kapcsolat az Amazon VPN és az internet között. Ez az erőforrás lehetővé teszi a teszt virtuális gépen keresztüli kapcsolódást az AWS nyilvános IP-címről az interneten keresztül. Ez az erőforrás nem szükséges a VPN-kapcsolathoz. Csak tesztelésre használjuk.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Az Internet-átjáró létrehozása":::

1. Válassza **a Csatolás a VPC-hoz** lehetőséget.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Az Internet Gateway csatlakoztatása a VPC-hoz":::

1. Válasszon egy VPC-t, és **csatolja az Internet-átjárót**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Az átjáró csatolása":::

1. Hozzon létre egy útvonalat, amely lehetővé teszi a **0.0.0.0/0** (Internet) kapcsolatok elérését az Internet-átjárón keresztül.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Az útvonal konfigurálása az átjárón keresztül":::

1. Az Azure-ban az útvonal automatikusan létrejön. Az útvonalat az Azure virtuális gépről úgy tekintheti meg, ha kijelöli a **virtuális gép > hálózatkezelés > hálózati adapter > a tényleges útvonalak** lehetőséget. Két útvonal jelenik meg, a kapcsolatok száma 1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="A hatályos útvonalak keresése":::

1. Ezt egy Linux rendszerű virtuális gépről tesztelheti az Azure-ban. Az eredmény az alábbi példához hasonlóan fog megjelenni.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Az Azure áttekintése Linux rendszerű virtuális gépről":::

1. Ezt a Linux rendszerű virtuális gépen is tesztelheti AWS-ben. Az eredmény az alábbi példához hasonlóan fog megjelenni.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="AWS – áttekintés Linux rendszerű virtuális gépről":::

1. Tesztelje a kapcsolatot az Azure-beli virtuális gépről.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Ping-teszt az Azure-ból":::

1. Tesztelje a kapcsolatot az AWS virtuális gépről.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Ping teszt az AWS-ből":::

## <a name="next-steps"></a>Következő lépések

* A IKEv2 AWS-támogatásáról az [AWS-cikkben](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)talál további információt.

* A többfelhős VPN nagy léptékű létrehozásával kapcsolatos további információkért tekintse meg a videó a [legjobb Többfelhős VPN kiépítését a skálán](https://www.youtube.com/watch?v=p7h-frLDFE0).
