---
title: 'Távoli munka a P2S-szel: Azure VPN Gateway'
description: Ez az oldal azt ismerteti, hogyan használhatja a VPN Gatewayt a COVID-19 világjárvány miatti távoli munka lehetővé tételéhez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880084"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Távoli munka Azure VPN Gateway pont – hely kapcsolattal

>[!NOTE]
>Ez a cikk azt ismerteti, hogyan használhatja az Azure VPN Gateway, az Azure, a Microsoft Network és az Azure partner-ökoszisztémát a COVID-19 válság miatt felmerülő hálózati problémák megoldásához.
>

Ez a cikk azokat a lehetőségeket ismerteti, amelyekkel a szervezetek távelérést állíthatnak be a felhasználók számára, illetve a meglévő megoldásaikat a COVID-19 járványban további kapacitással kiegészítve.

Az Azure pont – hely megoldás felhőalapú, és gyorsan üzembe helyezhető, így a felhasználók egyre nagyobb mértékben dolgozhatnak otthonról. A szolgáltatás egyszerűen és gyorsan felskálázást végez, ha a megnövekedett kapacitás többé nem szükséges.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Tudnivalók a pont – hely VPN-ről

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás olyan ügyfelek számára hasznos, akik távoli helyről szeretnének csatlakozni az Azure virtuális hálózatok vagy a helyszíni adatközpontokhoz, például otthonról vagy konferenciáról. Ez a cikk azt ismerteti, hogyan engedélyezhető a felhasználók számára a távoli működés a különböző forgatókönyvek alapján.

Az alábbi táblázat az ügyfél operációs rendszereit és a számukra elérhető hitelesítési beállításokat mutatja be. Hasznos lehet a már használatban lévő ügyfél operációs rendszer alapján kiválasztani a hitelesítési módszert. Válassza például az OpenVPN tanúsítvány alapú hitelesítéssel lehetőséget, ha a csatlakozni kívánó ügyfél-operációs rendszerek keveréke van. Azt is vegye figyelembe, hogy a pont – hely VPN csak Route-alapú VPN-átjárók esetén támogatott.

![Képernyőkép, amely megjeleníti az ügyfél operációs rendszereit és a rendelkezésre álló hitelesítési beállításokat.](./media/working-remotely-support/ostable.png "Operációs rendszer")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>1. eset – a felhasználóknak csak az Azure-erőforrások elérésére van szükségük

Ebben az esetben a távoli felhasználóknak csak az Azure-ban található erőforrásokhoz kell hozzáférnie.

![Diagram, amely egy pont – hely típusú forgatókönyvet mutat be azon felhasználók számára, akiknek csak az Azure erőforrásaihoz kell hozzáférést biztosítaniuk.](./media/working-remotely-support/scenario1.png "1\. példa")

Magas szinten a következő lépések szükségesek ahhoz, hogy a felhasználók biztonságosan csatlakozhassanak az Azure-erőforrásokhoz:

1. Hozzon létre egy virtuális hálózati átjárót (ha az egyik nem létezik).
2. Pont – hely típusú VPN konfigurálása az átjárón.

   * A tanúsítványalapú hitelesítéshez kövesse [ezt a hivatkozást](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Az OpenVPN esetében kövesse [ezt a hivatkozást](vpn-gateway-howto-openvpn.md).
   * Az Azure AD-hitelesítéshez kövesse [ezt a hivatkozást](openvpn-azure-ad-tenant.md).
   * A pont – hely kapcsolatok hibaelhárításához kövesse [ezt a hivatkozást](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Töltse le és terjessze ki a VPN-ügyfél konfigurációját.
4. Terjessze ki a tanúsítványokat (ha a tanúsítványalapú hitelesítés van kiválasztva) az ügyfeleknek.
5. Csatlakozás az Azure VPN-hez.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>2. forgatókönyv – a felhasználóknak hozzáférésre van szükségük az Azure-ban és/vagy a helyszíni erőforrásokban lévő erőforrásokhoz

Ebben az esetben a távoli felhasználóknak hozzá kell férniük az Azure-ban és a helyszíni adatközpont (ok) ban található erőforrásokhoz.

![Diagram, amely egy pont – hely típusú forgatókönyvet mutat be azon felhasználók számára, akiknek hozzáférésre van szükségük az Azure-beli erőforrásokhoz.](./media/working-remotely-support/scenario2.png "2\. példa")

Magas szinten a következő lépések szükségesek ahhoz, hogy a felhasználók biztonságosan csatlakozhassanak az Azure-erőforrásokhoz:

1. Hozzon létre egy virtuális hálózati átjárót (ha az egyik nem létezik).
2. Pont – hely típusú VPN konfigurálása az átjárón (lásd: [1. forgatókönyv](#scenario1)).
3. Konfigurálja a helyek közötti alagutat az Azure virtuális hálózati átjárón a BGP-vel engedélyezve.
4. Konfigurálja a helyszíni eszközt az Azure-beli virtuális hálózati átjáróhoz való kapcsolódáshoz.
5. A pont – hely profil letöltése a Azure Portalról és terjesztés az ügyfeleknek

A két hálózat közötti pont-pont típusú VPN-alagút beállításával kapcsolatos további információkért tekintse meg [ezt a hivatkozást](./tutorial-site-to-site-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>A natív Azure tanúsítványalapú hitelesítéssel kapcsolatos gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>A RADIUS-hitelesítéssel kapcsolatos gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Következő lépések

* [P2S-kapcsolatok konfigurálása – Azure AD-hitelesítés](openvpn-azure-ad-tenant.md)

* [P2S-alapú kapcsolatok konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolatok konfigurálása – Azure natív tanúsítványalapú hitelesítés](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc védjegye.**