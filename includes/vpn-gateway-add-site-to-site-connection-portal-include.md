---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479594"
---
1. Nyissa meg a virtuális hálózati átjáró lapját. Az átjáró eléréséhez nyissa meg az **VNet nevét – > áttekintés – > csatlakoztatott eszközök – az átjáró > nevét**, de több más módon is elvégezhető a mozgás.
1. Az átjáró lapon válassza a **kapcsolatok** lehetőséget. A kapcsolatok lap tetején kattintson a **+ Hozzáadás** elemre a **kapcsolat hozzáadása** lap megnyitásához.

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Helyek közötti kapcsolat":::
1. A **Kapcsolat hozzáadása** lapon állítsa be a kapcsolathoz tartozó értékeket.

   * **Név:** Nevezze el a kapcsolatot.
   * **Kapcsolattípus:** Válassza **a helyek közötti (IPSec)** lehetőséget.
   * **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
   * **Helyi hálózati átjáró:** Válassza a **helyi hálózati átjáró** kiválasztása lehetőséget, majd válassza ki a használni kívánt helyi hálózati átjárót.
   * **Megosztott kulcs:** Az értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az abc123 értéket használtuk, de lehetséges (és javasolt) ennél összetettebb értéket használni. A legfontosabb, hogy az itt megadott értéknek egyeznie kell azzal az értékkel, amelyet a VPN-eszköz konfigurálásakor ad meg.
   * Hagyja bejelölés nélkül **Az Azure Private IP-címének használatát** .
   * Hagyja üresen a **BGP engedélyezése** jelet.
   * Válassza a **IKEv2** lehetőséget.
   * A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

1. A kapcsolódás létrehozásához kattintson **az OK gombra** . A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
1. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg. Az Állapot *Ismeretlenről**Kapcsolódásra*, majd *Sikeresre* vált.
