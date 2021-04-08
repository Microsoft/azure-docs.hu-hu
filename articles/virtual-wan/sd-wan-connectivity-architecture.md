---
title: Virtuális WAN és SD – WAN kapcsolati architektúrák
titleSuffix: Azure Virtual WAN
description: Tudnivalók a privát SD-WAN és az Azure Virtual WAN összekapcsolásáról
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564701"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>SD-WAN kapcsolati architektúra az Azure Virtual WAN-ral

Az Azure Virtual WAN egy hálózati szolgáltatás, amely számos felhőalapú és biztonsági szolgáltatást egyesít egyetlen operatív felületen. Ezen szolgáltatások közé tartoznak a telephelyek közötti VPN-kapcsolat, a távoli felhasználó (pont – hely VPN), a magánhálózati (ExpressRoute) kapcsolatok, a felhőn belüli tranzitív kapcsolat a virtuális hálózatok, a VPN és a ExpressRoute közötti kapcsolat, útválasztás, Azure Firewall és titkosítás a magánhálózati kapcsolathoz.

Habár maga az Azure virtuális WAN egy szoftver által definiált WAN (SD-WAN), a helyszíni SD-WAN technológiák és szolgáltatások zökkenőmentes összekapcsolását is lehetővé teszi. Számos ilyen szolgáltatást a [virtuális WAN](virtual-wan-locations-partners.md) -ökoszisztéma és az Azure Networking felügyelt szolgáltatások [(MSP-partnerek)](../networking/networking-partners-msp.md)kínál. Azok a vállalatok, amelyek az SD-WAN-ra alakítják át a saját WAN-t, a saját SD-WAN és az Azure Virtual WAN összekapcsolásával is rendelkeznek. A vállalatok választhatnak a következő lehetőségek közül:

* Közvetlen összekötő-modell
* Közvetlen összekötő modell a NVA-in-VWAN-hub-vel
* Közvetett Interconnect-modell
* Felügyelt hibrid WAN-modell a kedvenc felügyelt szolgáltatói [MSP](../networking/networking-partners-msp.md) használatával

Az összes ilyen esetben a virtuális WAN és az SD-WAN összekapcsolása hasonló a kapcsolati oldalhoz, de a koordinálás és a működési oldalon is változhat.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Közvetlen összekötő-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Közvetlen összekötő-modell":::

Ebben az architektúra-modellben az SD-WAN ág ügyfél-telephelyi berendezés (CPE) közvetlenül kapcsolódik az IPsec-kapcsolatokon keresztül a virtuális WAN-hubokhoz. A fiókirodai CPE más ágakhoz is kapcsolódhat a privát SD-WAN-kapcsolaton keresztül, vagy a virtuális WAN-t a fiókirodához való csatlakozáshoz használhatja. Azok az ágak, amelyeknek az Azure-beli számítási feladataihoz való hozzáférésre van szükségük, közvetlenül és biztonságosan érhetik el az Azure-t a virtuális WAN-csomópont (ok) ban leállított IPsec-alagutakon keresztül.

Az SD-WAN CPE-partnerek lehetővé teszik az automatizálást ahhoz, hogy automatizálják a normálisan unalmas és a hibákra hajlamos IPsec-kapcsolatot a megfelelő CPE-eszközökről. Az Automation lehetővé teszi, hogy az SD-WAN-vezérlő a virtuális WAN API-n keresztül kommunikáljon az Azure-ral a virtuális WAN-helyek konfigurálásához, valamint a szükséges IPsec-alagút konfigurációjának leküldéséhez a fiókiroda CPEs. A különböző SD-WAN-partnerek által használt virtuális WAN-kapcsolati automatizálás leírását a következő témakörben talál: [Automation-irányelvek](virtual-wan-configure-automation-providers.md) .

Az SD-WAN CPE továbbra is az a hely, ahol a forgalom optimalizálása és az elérési út kiválasztására is sor kerül. 

Ebben a modellben a valós idejű forgalmi mutatókon alapuló, a gyártótól származó tulajdonosi forgalom optimalizálása nem támogatott, mert a virtuális WAN-kapcsolat az IPsec protokollon keresztül történik, és az IPsec VPN a virtuális WAN VPN-átjárón leáll. Például a dinamikus elérési út a ág CPE-ben való kiválasztása a különböző hálózati csomagok más SD-WAN-csomóponttal való cseréje miatt valósítható meg, ezért a legjobb hivatkozás a különböző rangsorolt forgalomhoz való, dinamikusan a fiókirodában való használatra. Ez a funkció olyan területeken lehet hasznos, ahol a Last Mile Optimization (a legközelebbi Microsoft POP-hoz tartozó ág) megadása kötelező.

A Virtual WAN használatával a felhasználók beszerezhetik az Azure Path Selection-t, amely házirend-alapú útvonal kiválasztása több ISP-kapcsolaton keresztül a fiókirodából a virtuális WAN VPN-átjárók között. A virtuális WAN lehetővé teszi több hivatkozás (elérési út) telepítését ugyanazon SD-WAN ág CPE-ből. az egyes hivatkozások az SD-WAN CPE egyedi nyilvános IP-címétől az Azure Virtual WAN VPN Gateway két különböző példányára mutató kettős bújtatási kapcsolatot jelentenek. Az SD-WAN-szállítók az Azure optimális elérési útját tudják megvalósítani, a szabályzatok által a CPE-hivatkozásokon beállított forgalmi házirendek alapján. Az Azure-beli végponton a rendszer minden kapcsolatot egyformán kezel.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Közvetlen összekötő modell a NVA-in-VWAN-hub-vel

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Közvetlen összekötő modell a NVA-in-VWAN-hub-vel":::

Ez az architektúra-modell támogatja a harmadik féltől származó [hálózati virtuális berendezés (NVA) közvetlen telepítését a virtuális hubhoz](./about-nva-hub.md). Ez lehetővé teszi, hogy azok az ügyfelek, akik a fiókirodát a virtuális központ ugyanazon NVA szeretnék csatlakoztatni, így kihasználhatják az Azure-beli számítási feladatokhoz való csatlakozáshoz szükséges teljes körű SD-WAN-képességeket. 

Több virtuális WAN-partner is dolgozott olyan élmény biztosításához, amely automatikusan konfigurálja a NVA a telepítési folyamat részeként. Ha a NVA a virtuális hubhoz lett kiépítve, a NVA szükséges további konfigurációkat az NVA Partners portálon vagy a felügyeleti alkalmazáson keresztül kell elvégezni. A NVA való közvetlen hozzáférés nem érhető el. Az Azure-beli virtuális WAN-hubhoz központilag üzembe helyezhető NVA kifejezetten a virtuális központban való használatra lettek tervezve. A VWAN hub NVA támogató partnerei, valamint a telepítési útmutatók a [virtuális WAN-partnerek](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) című cikkben találhatók.

Az SD-WAN CPE továbbra is az a hely, ahol a forgalom optimalizálása és az elérési út kiválasztására is sor kerül.
Ebben a modellben a kereskedelmi forgalom a valós idejű adatforgalmi jellemzők alapján történő optimalizálása támogatott, mert a virtuális WAN-kapcsolat a hub SD-WAN NVA keresztül érhető el.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Közvetett Interconnect-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Közvetett Interconnect-modell":::

Ebben az architektúra-modellben az SD-WAN ág CPEs közvetetten csatlakoznak a virtuális WAN-hubokhoz. Ahogy az ábrán látható, egy SD-WAN virtuális CPE üzembe helyezése egy vállalati VNet történik. Ez a virtuális CPE az IPsec használatával csatlakozik a virtuális WAN hub (ok) hoz. A virtuális CPE SD-WAN-átjáróként szolgál az Azure-ban. Az Azure-beli számítási feladataik eléréséhez szükséges ágak a v-CPE-átjárón keresztül lesznek elérhetők.

Mivel az Azure-hoz való kapcsolódás a v-CPE-átjárón (NVA) keresztül történik, az Azure-beli számítási feladatok virtuális hálózatok és más SD-WAN-ágak felé irányuló összes forgalom a NVA keresztül halad. Ebben a modellben a felhasználó felelős az SD-WAN NVA felügyeletéhez és üzemeltetéséhez, beleértve a magas rendelkezésre állást, a méretezhetőséget és az útválasztást.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Felügyelt hibrid WAN-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Felügyelt hibrid WAN-modell":::

Ebben az architektúrában a vállalatok a felügyelt SD-WAN-partner által kínált felügyelt SD-WAN szolgáltatást használhatják. Ez a modell hasonló a fent ismertetett közvetlen vagy közvetett modellekhez. Ebben a modellben azonban az SD-WAN kialakítását, a koordinálást és a műveleteket az SD-WAN szolgáltató biztosítja.

Az Azure- [hálózatkezelési MSP-partnerek](../networking/networking-partners-msp.md) az [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) segítségével implementálják az SD-WAN és a Virtual WAN szolgáltatást a nagyvállalati ügyfél Azure-előfizetésében, valamint a teljes körű hibrid WAN-t az ügyfél nevében is üzemeltetheti. Ezek a MSP az Azure ExpressRoute is végrehajthatják a virtuális WAN-ban, és teljes körűen felügyelt szolgáltatásként működhetnek.

## <a name="additional-information"></a>További információ

* [GYIK belefoglalása](virtual-wan-faq.md)
* [Távoli kapcsolat megoldása](work-remotely-support.md)