---
title: 'Azure VPN Gateway: titkosítási követelmények'
description: Ez a cikk a titkosítási követelményeket és az Azure VPN-átjárókat ismerteti.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: f1d30ef56350c3e1df796f20e3f9f39fdbc6428a
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666494"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>A titkosítási követelmények és az Azure VPN-átjárók

Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure VPN Gatewayt úgy, hogy az az Azure-ban a létesítmények közötti S2S VPN-alagutakra és VNet-VNet-kapcsolatokra vonatkozó titkosítási követelményeket is kielégítse.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Tudnivalók az Azure VPN-kapcsolatok IKEv1 és IKEv2

Az alapszintű SKU-hoz csak az alapszintű SKU-IKEv1 és a IKEv2-kapcsolat engedélyezett Az alapszintű SKU-ok csak 1 kapcsolódást és más korlátozásokat, például teljesítményeket biztosítanak, az olyan örökölt eszközöket használó ügyfelek, amelyek csak a IKEv1 protokollok használatát támogatják, korlátozott tapasztalattal rendelkeznek. Az IKEv1 protokollt használó ügyfelek felhasználói élményének fokozása érdekében mostantól engedélyezhető a IKEv1-kapcsolat az összes VPN Gateway-SKU esetében, az alapszintű SKU kivételével. További információ: [VPN Gateway SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)-i.

![Azure VPN Gateway IKEv1 és IKEv2 kapcsolatok](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Ha a IKEv1-és IKEv2-kapcsolatok ugyanarra a VPN-átjáróra vannak alkalmazva, akkor a két kapcsolat közötti átvitel automatikusan engedélyezve van.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Tudnivalók az Azure VPN-átjárók IPsec-és IKE-szabályzati paramétereinek használatáról

Az IPsec és az IKE protokoll standard verziója számos titkosítási algoritmust támogat különböző kombinációkban. Ha nem igényel titkosítási algoritmusok és paraméterek adott kombinációját, az Azure VPN Gateway az alapértelmezett javaslatok készletét használja. Az alapértelmezett szabályzatok úgy lettek kiválasztva, hogy maximalizálják az együttműködési képességet számos külső VPN-eszköz esetében az alapértelmezett konfigurációkban. Ennek eredményeképpen a szabályzatok és a javaslatok száma nem fedi le a rendelkezésre álló titkosítási algoritmusok és a kulcsfontosságú erősségek összes lehetséges kombinációját.

Az Azure VPN Gateway alapértelmezett házirendjének beállítása: [Tudnivalók a VPN-eszközökről és az IPSec/IKE-paraméterekről helyek közötti VPN Gateway kapcsolatok](vpn-gateway-about-vpn-devices.md)esetén.

## <a name="cryptographic-requirements"></a>Titkosítási követelmények

Az egyes titkosítási algoritmusokat vagy paramétereket igénylő kommunikációhoz, jellemzően a megfelelőségi vagy biztonsági követelmények miatt, mostantól konfigurálhatja az Azure VPN-átjárókat, hogy egyéni IPsec/IKE-házirendet használjanak bizonyos titkosítási algoritmusokkal és az alapértelmezett Azure-szabályzatok helyett a legfontosabb erősségek.

Az Azure VPN-átjárók IKEv2 alapmódú házirendjei például csak a Diffie-Hellman csoport 2 (1024 bit) használatát használják, míg előfordulhat, hogy az IKE-ben használni kívánt erősebb csoportokat kell megadnia, például a 14. csoport (2048 bites verzió), a csoport 24 (2048 bites MODP csoport) vagy az ECP (elliptikus görbe csoportok) 256 vagy 384 bit (19. csoport és 20. csoport). A hasonló követelmények az IPsec-alapú gyorsmódú házirendekre is érvényesek.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Egyéni IPsec/IKE-házirend Azure VPN Gateway átjárókkal

Az Azure VPN Gateway mostantól támogatja a kapcsolatonként, az Egyéni IPsec/IKE-házirendet. Helyek közötti vagy VNet kapcsolat esetén az IPsec és az IKE titkosítási algoritmusok adott kombinációját választhatja ki a kívánt VNet, ahogy az alábbi példában is látható:

![IPSec-IKE-Policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Létrehozhat egy IPsec/IKE-szabályzatot, és alkalmazhatja azt egy új vagy meglévő kapcsolódásra is.

### <a name="workflow"></a>Munkafolyamat

1. Hozza létre a virtuális hálózatokat, a VPN-átjárókat vagy a hálózati átjárókat a kapcsolati topológiához a további útmutató dokumentumokban leírtak szerint.
2. IPsec/IKE-szabályzat létrehozása
3. A szabályzatot S2S vagy VNet-VNet-kapcsolatok létrehozásakor is alkalmazhatja
4. Ha a kapcsolódás már létrejött, a szabályzatot alkalmazhatja vagy frissítheti egy meglévő kapcsolatban.

## <a name="ipsecike-policy-faq"></a>IPsec/IKE-házirend – gyakori kérdések

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Következő lépések

A kapcsolatok Egyéni IPsec/IKE-szabályzatának konfigurálásával kapcsolatos részletes útmutatásért lásd: [IPSec/IKE-házirend konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md) .

Lásd még: [több házirend-alapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) a UsePolicyBasedTrafficSelectors beállítással kapcsolatos további információkért.
