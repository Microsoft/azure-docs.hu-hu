---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93375889"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Mi a teendő, ha a tanúsítvány-hitelesítéssel való kapcsolódáskor nem egyeznek a tanúsítványok?

Törölje a jelet a **"kiszolgáló identitásának ellenőrzése a tanúsítvány érvényesítésével"** jelölőnégyzetből, vagy **adja hozzá a kiszolgáló teljes tartománynevét a tanúsítványhoz** a profil manuális létrehozásakor. Ehhez futtassa a **Rasphone** parancsot a parancssorból, és válassza ki a profilt a legördülő listából.

A kiszolgáló identitás-ellenőrzésének megkerülése általában nem ajánlott, de az Azure tanúsítványalapú hitelesítés esetében ugyanazt a tanúsítványt használja a rendszer a kiszolgáló érvényesítéséhez a VPN Tunneling Protocol (IKEv2/SSTP) és az EAP protokoll esetében. Mivel a VPN Tunneling protokoll már érvényesíti a kiszolgálói tanúsítványt és a teljes tartománynevet, a rendszer redundánsan ellenőrzi ugyanezt az EAP-ben.

![pont – hely hitelesítés](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Kiszolgálótanúsítvány")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Használhatom a saját belső PKI legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját a pont – hely kapcsolatokhoz tartozó tanúsítványok létrehozásához?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Használhatok Azure Key Vault tanúsítványokat?

Nem.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Milyen eszközökkel hozhatok létre tanúsítványokat?

Használhatja a Vállalati nyilvános kulcsú infrastruktúra megoldást (belső nyilvános kulcsú infrastruktúrát), az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Elérhető útmutató a tanúsítvány beállításaihoz és paramétereihez?

* **Belső/vállalati nyilvános kulcsú infrastruktúra:** a lépéseket a [Tanúsítványok előállítása](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) pontban találja.

* **Azure PowerShell:** a lépéseket az [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) cikkében találja.

* **MakeCert:** a lépéseket a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) cikkében találja.

* **OpenSSL** 

    * A tanúsítványok exportálása során ügyeljen arra, hogy a főtanúsítványt Base64 formátumba konvertálja.

    * Ügyféltanúsítvány esetén:

      * Amikor létrehozza a titkos kulcsot, 4096 bites hosszt adjon meg.
      * Amikor létrehozza a tanúsítványt, az *-extensions* paraméter értéke *usr_cert* legyen.
