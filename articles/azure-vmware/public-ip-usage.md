---
title: A nyilvános IP-funkciók használata az Azure VMware-megoldásban
description: Ez a cikk azt ismerteti, hogyan használható a nyilvános IP-funkció az Azure Virtual WAN-ban.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 640f359ed8934048bffd7f58b36e8a3eef71c2f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581380"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>A nyilvános IP-funkciók használata az Azure VMware-megoldásban

A nyilvános IP-cím az Azure VMware megoldás kapcsolatának új funkciója. Lehetővé teszi az erőforrások, például a webkiszolgálók, a virtuális gépek (VM-EK) és a nyilvános hálózaton keresztül elérhető gazdagépek elérését. 

A nyilvános internet-hozzáférés kétféleképpen engedélyezhető. 

- Az alkalmazások a HTTP/HTTPS-forgalom Application Gateway Load Balancer szolgáltatásában futtathatók és közzétehetők.
- Az Azure Virtual WAN nyilvános IP-funkcióival lett közzétéve.

Az Azure VMware-megoldás saját Felhőbeli üzembe helyezésének részeként a nyilvános IP-funkciók engedélyezése után a szükséges összetevőket az Automation Get hozza létre és engedélyezte:

-  Virtuális WAN

-  Virtuális WAN hub ExpressRoute-kapcsolattal

-  Szolgáltatások Azure Firewall nyilvános IP-címmel

Ez a cikk részletesen ismerteti, hogyan használhatja a nyilvános IP-funkciókat a virtuális WAN-ban.

## <a name="prerequisites"></a>Előfeltételek

- Azure VMware-megoldás környezet
- Azure VMware-megoldási környezetben futó webkiszolgáló.
- Egy új, nem átfedésben lévő IP-címtartomány a virtuális WAN-központ üzembe helyezéséhez, jellemzően a `/24` .

## <a name="reference-architecture"></a>Referenciaarchitektúra

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Nyilvános IP-architektúra diagramja" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Az architektúra ábrán egy Azure VMware megoldási környezetben üzemeltetett webkiszolgáló látható, amely RFC1918 magánhálózati IP-címekkel van konfigurálva.  A webszolgáltatás elérhetővé válik az interneten a virtuális WAN nyilvános IP-funkcióival.  A nyilvános IP-cím jellemzően Azure Firewall lefordított cél NAT. A DNAT-szabályokkal a tűzfalszabályok a nyilvános IP-címekre irányuló kéréseket privát címekre (webkiszolgálóra) fordítják egy porttal.

A felhasználói kérések megnyomják a tűzfalat egy nyilvános IP-címen, amelyet a rendszer a Azure Firewall DNAT-szabályainak használatával fordít a magánhálózati IP-címekre. A tűzfal ellenőrzi a NAT-táblázatot, és ha a kérelem megfelel egy bejegyzésnek, továbbítja a forgalmat a lefordított címről és portra az Azure VMware-megoldás környezetében.

A webkiszolgáló fogadja a kérést, és a kért információkat vagy oldalt válaszolja meg a tűzfalon, majd a tűzfal továbbítja az adatokat a felhasználónak a nyilvános IP-címen.

## <a name="test-case"></a>Teszteset
Ebben az esetben közzé fogja tenni az IIS webkiszolgálót az interneten. Az Azure VMware-megoldás nyilvános IP-funkciója segítségével közzéteheti a webhelyet egy nyilvános IP-címen.  A NAT-szabályokat a tűzfalon is konfigurálhatja, és a nyilvános IP-címmel elérheti az Azure VMware megoldás erőforrását (virtuális gépek webkiszolgálóval).

## <a name="deploy-virtual-wan"></a>A Virtual WAN üzembe helyezése

1. Jelentkezzen be a Azure Portalba, majd keresse meg és válassza ki az **Azure VMware-megoldást**.

1. Válassza ki az Azure VMware-megoldás saját felhőjét.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Képernyőkép az Azure VMware Solution Private Cloud-ról." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. A **kezelés** területen válassza a **kapcsolat** lehetőséget.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Képernyőfelvétel a kapcsolatról szakasz." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Válassza a **nyilvános IP-cím** lapot, majd válassza a **Konfigurálás** lehetőséget.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="A nyilvános IP-cím konfigurálását bemutató képernyőkép" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Fogadja el az alapértelmezett értékeket, vagy módosítsa, majd válassza a **Létrehozás** lehetőséget.

   - Virtuális WAN-erőforráscsoport

   - Virtuális WAN neve

   - Virtuális hub-címterület (új, nem átfedésben lévő IP-címtartomány használata)

   - Nyilvános IP-címek száma (1-100)

Az összes összetevő üzembe helyezésének befejezéséhez körülbelül egy órát vesz igénybe. Ezt az üzembe helyezést csak egyszer kell megtörténnie, hogy támogassa az Azure VMware-megoldás környezetének összes jövőbeli nyilvános IP-címeit.  

>[!TIP]
>Nyomon követheti az **értesítési** területek állapotát. 

## <a name="view-and-add-public-ip-addresses"></a>Nyilvános IP-címek megtekintése és hozzáadása

Az alábbi lépések végrehajtásával további nyilvános IP-címeket is megtekintheti és hozzáadhat.

1. A Azure Portal keresse meg és válassza a **tűzfal** lehetőséget.

1. Válasszon ki egy központilag telepített tűzfalat, majd válassza a **látogatás Azure Firewall Manager elemet a tűzfal konfigurálásához és kezeléséhez**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="A tűzfal konfigurálásának és kezelésének lehetőségét bemutató képernyőkép" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Válassza a **biztonságos virtuális hubok** lehetőséget, és a listából válassza ki a virtuális hubot.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="A Firewall Manager képernyőképe" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. A virtuális központ lapon válassza a **nyilvános IP-konfiguráció** lehetőséget, és adjon hozzá több nyilvános IP-címet, majd válassza a **Hozzáadás** lehetőséget. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Képernyőkép a nyilvános IP-konfiguráció hozzáadásáról a Firewall Managerben" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Adja meg a szükséges IP-címek számát, és válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Képernyőkép egy megadott számú nyilvános IP-konfiguráció hozzáadásához" border="true":::


## <a name="create-firewall-policies"></a>Tűzfalszabályok létrehozása

Az összes összetevő üzembe helyezése után megtekintheti őket a hozzáadott erőforráscsoporthoz. A következő lépés egy tűzfal-házirend hozzáadása.

1. A Azure Portal keresse meg és válassza a **tűzfal** lehetőséget.

1. Válasszon ki egy központilag telepített tűzfalat, majd válassza a **látogatás Azure Firewall Manager elemet a tűzfal konfigurálásához és kezeléséhez**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="A tűzfal konfigurálásának és kezelésének lehetőségét bemutató képernyőkép" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Válassza ki **Azure Firewall házirendeket** , majd válassza a **Azure Firewall házirend létrehozása** lehetőséget.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="A tűzfal-házirend létrehozása a tűzfal kezelőjében – képernyőkép" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Az **alapok** lapon adja meg a szükséges adatokat, és válassza a **Tovább: DNS-beállítások** elemet. 

1. A **DNS** lapon válassza a **Letiltás** lehetőséget, majd válassza a **Tovább: szabályok** elemet.

1. Válassza a **szabálygyűjtemény hozzáadása** lehetőséget, adja meg az alábbi adatokat, majd kattintson a **Hozzáadás** , majd a **Tovább gombra: fenyegetés intelligencia** elemre.

   -  Name
   -  Szabályok gyűjteményének típusa – DNAT
   -  Prioritás
   -  Szabály-gyűjtési művelet – engedélyezés
   -  Szabály neve
   -  Forrás típusa – **IP** -cím
   -  Forrás **\***
   -  Protokoll – **TCP**
   -  Célport – **80**
   -  Cél típusa – **IP-cím**
   -  Cél – **nyilvános IP-cím**
   -  Lefordított cím – **Azure VMware-megoldás webkiszolgáló magánhálózati IP-címe**
   -  Lefordított port – **Azure VMware-megoldás webkiszolgáló-portja**

1. Hagyja meg az alapértelmezett értéket, majd válassza a **Tovább: hubok** elemet.

1. Válassza a **virtuális központ hozzárendelése** lehetőséget.

1. Válasszon egy hubot a listából, és válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Képernyőkép, amely a védett virtuális hubokba konvertált kiválasztott hubokat jeleníti meg." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Kattintson a **Tovább gombra: címkék**. 

1. Választható Hozza létre a név és érték párokat az erőforrások kategorizálásához. 

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget.

## <a name="limitations"></a>Korlátozások

SDDCs legfeljebb 100 nyilvános IP-cím adható meg.

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte a nyilvános IP-funkciók használatát az Azure VMware megoldásban, érdemes megismernie az alábbiakat:

- Nyilvános IP-címek használata az [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md)használatával.
- [IPSec-alagút létrehozása az Azure VMware megoldásba](create-ipsec-tunnel.md).
