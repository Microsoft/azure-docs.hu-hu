---
title: Az erőforrásokhoz való biztonságos hozzáférés kezelése küllős virtuális hálózatok a P2S-ügyfelek számára
titleSuffix: Azure Virtual WAN
description: Ebből a cikkből megtudhatja, hogyan használhatja az Azure Virtual WAN-t és Azure Firewall szabályokat a virtuális hálózatok biztonságos elérésének kezeléséhez a felhasználói VPN (pont – hely) ügyfelek számára.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048258"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Az erőforrásokhoz való biztonságos hozzáférés kezelése küllős virtuális hálózatok a felhasználói VPN-ügyfelek számára

Ez a cikk bemutatja, hogyan használhatja a virtuális WAN-t és a Azure Firewall szabályokat és szűrőket az Azure-beli erőforrásokhoz való kapcsolódás biztonságos elérésének kezeléséhez a pont – hely IKEv2 vagy a nyitott VPN-kapcsolatokon keresztül. Ez a konfiguráció akkor hasznos, ha olyan távoli felhasználókkal rendelkezik, akik számára korlátozni szeretné az Azure-erőforrásokhoz való hozzáférést, vagy az erőforrások védelmét az Azure-ban.

Az ebben a cikkben ismertetett lépések segítséget nyújtanak az architektúra létrehozásához a következő ábrán, amely lehetővé teszi, hogy a felhasználó VPN-ügyfelei egy adott erőforráshoz (VM1) hozzáférhessenek a virtuális hubhoz csatlakoztatott küllős VNet, de nem más erőforrásokhoz (VM2). Ezt az architektúrát példaként használhatja alapszintű útmutatásként.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Ábra: biztonságos virtuális központ" :::

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Elérhetők a használni kívánt hitelesítési konfigurációhoz tartozó értékek. Például RADIUS-kiszolgáló, Azure Active Directory hitelesítés vagy [tanúsítvány létrehozása és exportálása](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Virtuális WAN létrehozása

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>P2S-konfigurációs paraméterek megadása

A pont – hely (P2S) konfiguráció határozza meg a távoli ügyfelek csatlakoztatásának paramétereit. Ez a szakasz segítséget nyújt a P2S konfigurációs paramétereinek definiálásához, majd a VPN-ügyfél profilhoz használandó konfiguráció létrehozásához. A követett utasítások a használni kívánt hitelesítési módszertől függenek.

### <a name="authentication-methods"></a>Hitelesítési módszerek

A hitelesítési módszer kiválasztásakor három lehetőség közül választhat. Minden metódushoz konkrét követelmények vonatkoznak. Válassza ki az alábbi módszerek egyikét, majd hajtsa végre a lépéseket.

* **Azure Active Directory hitelesítés:** Szerezze be a következőket:

   * Az Azure AD-bérlőben regisztrált Azure VPN Enterprise-alkalmazás **alkalmazás-azonosítója** .
   * A **kibocsátó**. Példa: `https://sts.windows.net/your-Directory-ID`.
   * Az **Azure ad-bérlő**. Példa: `https://login.microsoftonline.com/your-Directory-ID`.

* **RADIUS-alapú hitelesítés:** Szerezze be a RADIUS-kiszolgáló IP-címét, a RADIUS-kiszolgáló titkos kulcsát és a tanúsítvány információit.

* **Azure-tanúsítványok:** Ehhez a konfigurációhoz tanúsítványokra van szükség. Tanúsítványokat kell létrehoznia vagy beszereznie. Minden ügyfélhez Ügyféltanúsítvány szükséges. Emellett a főtanúsítvány információit (nyilvános kulcs) is fel kell tölteni. A szükséges tanúsítványokkal kapcsolatos további információkért lásd: [tanúsítványok készítése és exportálása](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>A hub és az átjáró létrehozása

Ebben a szakaszban a virtuális hubot pont – hely átjáróval hozza létre. Konfiguráláskor a következő példában szereplő értékeket használhatja:

* **Központ magánhálózati IP-címének területe:** 10.0.0.0/24
* **Ügyfél címkészlet:** 10.5.0.0/16
* **Egyéni DNS-kiszolgálók:** Akár 5 DNS-kiszolgálót is listázhat

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN-ügyfél konfigurációs fájljainak előállítása

Ebben a szakaszban létrehozza és letölti a konfigurációs profil fájljait. Ezek a fájlok a natív VPN-ügyfél konfigurálására szolgálnak az ügyfélszámítógépen. Az ügyféloldali profil fájljainak tartalmával kapcsolatos információkért lásd: [pont – hely konfiguráció – tanúsítványok](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>VPN-ügyfelek konfigurálása

A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az egyes operációs rendszerekre vonatkozó eljárás eltérő, kövesse a rendszeren alkalmazott utasításokat.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>A küllős VNet összekötése

Ebben a szakaszban a küllős virtuális hálózatot a virtuális WAN-hubhoz csatolja.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Virtuális gépek létrehozása

Ebben a szakaszban két virtuális gépet hoz létre a VNet, a VM1 és a VM2. A hálózati diagramon a 10.18.0.4 és a 10.18.0.5 használjuk. A virtuális gépek konfigurálásakor ügyeljen arra, hogy kiválassza a létrehozott virtuális hálózatot (a hálózatkezelés lapon található). A virtuális gépek létrehozásának lépéseiért lásd: rövid útmutató [: virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>A virtuális központ biztonságossá tétele

A standard virtuális központ nem rendelkezik beépített biztonsági házirendekkel a küllős virtuális hálózatokban lévő erőforrások védelméhez. A biztonságos virtuális központ Azure Firewall vagy külső szolgáltató használatával kezeli a bejövő és a kimenő forgalmat az Azure-beli erőforrások védelméhez.

Alakítsa át a hubot egy biztonságos hubhoz a következő cikk használatával: [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Szabályok létrehozása a forgalom kezeléséhez és szűréséhez

Szabályokat hozhat létre, amelyek a Azure Firewall viselkedését írják le. A hub biztonságossá tételével biztosítjuk, hogy az Azure-erőforrások elérése előtt a virtuális központba beérkező összes csomag feldolgozása a tűzfalon keresztül történjen.

A lépések elvégzése után létrehozott egy architektúrát, amely lehetővé teszi a VPN-felhasználók számára a magánhálózati IP-10.18.0.4 való hozzáférést a virtuális GÉPHEZ, de **nem** fér hozzá a saját IP-10.18.0.5 rendelkező virtuális géphez.

1. A Azure Portal navigáljon a **tűzfal kezelőjéhez**.
1. A biztonság területen válassza a **Azure Firewall házirendek** elemet.
1. Válassza a **Azure Firewall házirend létrehozása** lehetőséget.
1. A **szabályzat részletei** területen adja meg a nevet, és válassza ki azt a régiót, amelyben a virtuális központ telepítve van.
1. Válassza a **Tovább: DNS-beállítások (előzetes verzió)** lehetőséget.
1. Válassza a **Tovább: szabályok** lehetőséget.
1. A **szabályok** lapon válassza a **szabálygyűjtemény hozzáadása** elemet.
1. Adja meg a gyűjtemény nevét. Adja meg a típust **hálózatként**. Adja meg a **100** prioritási értéket.
1. Adja meg a szabály nevét, a forrás típusát, a forrás, a protokoll, a célport és a célhely típusát az alábbi példában látható módon. Ezután válassza a **Hozzáadás** lehetőséget. Ez a szabály lehetővé teszi, hogy a VPN-ügyfél készlet bármely IP-címe hozzáférjen a virtuális GÉPHEZ a magánhálózati IP-10.18.04, de a virtuális hubhoz nem kapcsolódó egyéb erőforrásokhoz. Hozzon létre minden olyan szabályt, amely megfelel a kívánt architektúrának és engedélyek szabályainak.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Tűzfalszabályok" :::

1. Válassza a **Tovább: fenyegetés intelligencia** lehetőséget.
1. Válassza a **Next (tovább): hubok** lehetőséget.
1. A **hubok** lapon válassza a **virtuális hubok hozzárendelése** lehetőséget.
1. Válassza ki a korábban létrehozott virtuális hubot, majd válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

A folyamat befejezéséhez 5 percet is igénybe vehet.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Forgalom irányítása Azure Firewall

Ebben a szakaszban biztosítania kell, hogy a forgalom a Azure Firewall keresztül legyen átirányítva.

1. A portálon, a **tűzfal-kezelőben** válassza a **biztonságos virtuális hubok** lehetőséget.
1. Válassza ki a létrehozott virtuális hubot.
1. A **Beállítások** területen válassza a **biztonsági konfiguráció** elemet.
1. A **privát forgalom** területen válassza a **Küldés Azure Firewall használatával** lehetőséget.
1. Győződjön meg arról, hogy a VNet-kapcsolatok és a fiókirodák magánhálózati forgalmának védelmét Azure Firewall védi.
1. Kattintson a **Mentés** gombra.

## <a name="validate"></a><a name="validate"></a>Érvényesítés

Ellenőrizze a biztonságos központ telepítését.

1. Kapcsolódjon a **biztonságos virtuális hubhoz** VPN-kapcsolaton keresztül az ügyfél-eszközről.
1. Pingelje az IP- **10.18.0.4** az ügyfélről. Egy választ kell látnia.
1. Pingelje az IP- **10.18.0.5** az ügyfélről. A válasz nem jelenhet meg.

### <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy a biztonságos virtuális hubhoz **érvényes útvonalak tábla** a következő ugrást tartalmazza a tűzfalon keresztüli privát forgalomhoz. A hatályos útvonalak táblázat eléréséhez navigáljon a **virtuális hub** -erőforráshoz. A **kapcsolat** területen válassza az **Útválasztás** lehetőséget, majd válassza a **hatályos útvonalak** lehetőséget. Innen válassza ki az **alapértelmezett** útválasztási táblázatot.
* Ellenőrizze, hogy létrehozott-e szabályokat a [szabályok létrehozása](#create-rules) szakaszban. Ha ezek a lépések nem teljesülnek, az Ön által létrehozott szabályok nem lesznek ténylegesen társítva a központhoz, és az útválasztási táblázat és a csomag folyamata nem fogja használni a Azure Firewall.

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [virtuális WAN gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a Azure Firewallről: [Azure Firewall gyakori kérdések](../firewall/firewall-faq.yml).
