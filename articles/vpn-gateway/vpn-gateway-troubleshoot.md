---
title: 'Azure VPN Gateway: konfigurációk és kapcsolatok hibaelhárítása'
description: Ez a cikk segítséget nyújt a cikkek VPN Gateway konfigurációjának, kapcsolatának és az átviteli sebesség ellenőrzésének hibakereséséhez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 01/31/2018
ms.author: cherylmc
ms.openlocfilehash: a74109594eec87dddf637542f6d85091ed49231b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720253"
---
# <a name="troubleshoot-vpn-gateway"></a>Hibakeresés VPN Gateway

A VPN Gateway kapcsolatok számos okból sikertelenek lehetnek. Ez a cikk hivatkozásokat tartalmaz a hibaelhárítással kapcsolatos lépések elkezdéséhez. A teljes listát lásd a tartalomjegyzékben **található, az** oldal bal oldalán található cikkekben.

## <a name="troubleshooting-scenarios-and-solutions"></a>Forgatókönyvek és megoldások hibaelhárítása

* [VPN virtuális hálózatra irányuló átviteli sebességének ellenőrzése](vpn-gateway-validate-throughput-to-vnet.md)<br>A VPN Gateway-kapcsolat lehetővé teszi, hogy biztonságos, létesítmények közötti kapcsolatot hozzon létre az Azure-ban és a helyszíni informatikai infrastruktúrán belüli Virtual Network között. Ez a cikk bemutatja, hogyan érvényesítheti a helyszíni erőforrások hálózati átviteli sebességét egy Azure-beli virtuális gépre (VM). Emellett hibaelhárítási útmutatót is tartalmaz.

* [VPN-és tűzfal-eszközbeállítások](vpn-gateway-third-party-settings.md)<br>Ez a cikk számos javasolt megoldást tartalmaz a harmadik féltől származó VPN-vagy tűzfal-eszközökhöz, amelyeket a VPN Gateway használ. A külső gyártótól származó VPN-vagy tűzfal-eszközök technikai támogatását az eszköz gyártója biztosítja.

* [Pont–hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>Ez a cikk az esetlegesen felmerülő, pont – hely kapcsolatokkal kapcsolatos gyakori problémákat sorolja fel. Emellett a problémák lehetséges okait és megoldásait is tárgyalja.

* [Helyek közötti kapcsolatok](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>Miután konfigurált egy helyek közötti VPN-kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között, a VPN-kapcsolat hirtelen leáll, és nem lehet újracsatlakozni. Ez a cikk a probléma megoldásához szükséges hibaelhárítási lépéseket ismerteti.

* [Az Azure VPN Gateway hibáinak megoldása diagnosztikai naplók használatával](troubleshoot-vpn-with-azure-diagnostics.md)<br>A diagnosztikai naplók segítségével több VPN Gateway-hez kapcsolódó eseményt is elháríthat, beleértve a konfigurációs tevékenységet, a VPN-alagút kapcsolatát, az IPsec-naplózást, a BGP-útvonalak cseréjét, a hely speciális naplózását. 

## <a name="next-steps"></a>Következő lépések

A [VNet és a VPN-kapcsolatok ellenőrzéséhez](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)ezeket a lépéseket is használhatja.
