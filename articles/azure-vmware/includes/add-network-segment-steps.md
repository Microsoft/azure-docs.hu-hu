---
title: NSX-T hálózati szegmens hozzáadása
description: A NSX-T hálózati szegmensek Azure VMware-megoldáshoz való hozzáadásának lépései.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335063"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. A NSX-T Managerben válassza a **hálózati**  >  **szegmensek** , majd a **szegmens hozzáadása** elemet. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Új szegmens hozzáadását bemutató képernyőkép":::

1. Válassza a **szegmens hozzáadása** lehetőséget, és adja meg a szegmens nevét.

1. Válassza ki a Tier1-átjárót (TNTxx-T1) **csatlakoztatott átjáróként** , és hagyja a **típust** rugalmasként.

1. Válassza ki az előre konfigurált átfedéses **átviteli zónát** (TNTXX-OVERLAY-TZ), majd válassza az **alhálózatok beállítása** lehetőséget. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Állítsa be a szegmens nevét, a csatlakoztatott átjárót és a típust, valamint a szállítási zónát, majd válassza az alhálózat beállítása lehetőséget.":::

1. Adja meg az átjáró IP-címét, majd kattintson a **Hozzáadás** gombra. 

   >[!IMPORTANT]
   >Az IP-címnek egy nem átfedésben lévő RFC1918 kell lennie, amely biztosítja a kapcsolódást az új szegmensben lévő virtuális gépekhez.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Állítsa be az átjáró IP-címét az új szegmenshez, majd válassza a Hozzáadás lehetőséget.":::

1. Válassza az **alkalmaz** , majd a **Mentés** lehetőséget.

1. Válassza a **nem** lehetőséget a szegmens konfigurálásának folytatásához. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Az újonnan létrehozott hálózati szegmens további konfigurálásának elutasítása a nem lehetőség kiválasztásával.":::

1. Erősítse meg az új hálózati szegmens jelenlétét. Ebben a példában a **ls01** az új hálózati szegmens.

   1. A NSX-T Managerben válassza a **hálózati**  >  **szegmensek** lehetőséget. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Győződjön meg arról, hogy az új hálózati szegmens megtalálható a NSX-T-ben.":::

   1. A vCenter területen válassza a **hálózatkezelés > SDDC-Datacenter** elemet.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Győződjön meg arról, hogy az új hálózati szegmens megtalálható a vCenter-ben.":::