---
title: Azure VMware-megoldás CloudSimple használatával – tűzfalszabályok és szabályok beállítása
description: Leírja, hogyan állítható be a saját felhőalapú tűzfalak táblái és szabályai az alhálózatokon és a VLAN-ok forgalmának korlátozására.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895631"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Tűzfalszabályok és szabályok beállítása privát Felhőkhöz

A tűzfalak és a kapcsolódó szabályok lehetővé teszik, hogy korlátozásokat határozzon meg az egyes alhálózatokra és VLAN-okra érvényes adatforgalomra vonatkozóan.

* Az alhálózatok egy tűzfal táblához társíthatók.
* A tűzfal tábla több alhálózathoz is társítható.

## <a name="add-a-new-firewall-table"></a>Új tűzfalszabály hozzáadása

1. [Nyissa meg a CloudSimple-portált](access-cloudsimple-portal.md) , és válassza a **hálózat** lehetőséget az oldalsó menüben.
2. Válassza a **Tűzfalszabályok** lehetőséget.
3. Válassza a **Tűzfalszabály létrehozása** lehetőséget.

    ![VLAN/alhálózat lap](media/firewall-tables-page.png)

4. Adja meg a tábla nevét.
5. A táblázat alapértelmezett szabálya szerepel a táblázatban. További szabály létrehozásához kattintson az **új szabály létrehozása** lehetőségre. A részletekért tekintse meg a következő eljárást.
6. Kattintson a **kész** gombra a tűzfalszabály mentéséhez.

> [!IMPORTANT]
> Saját felhőben legfeljebb két tűzfal-táblázatot hozhat létre.

## <a name="firewall-rules"></a>Tűzfalszabályok

A tűzfalszabályok határozzák meg, hogy a tűzfal hogyan kezelje a különböző típusú forgalmat. A kiválasztott tűzfalszabály **szabályai** lap felsorolja az összes kapcsolódó szabályt.

![Tűzfalszabályok táblázata](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása

1. A következő módokon jelenítheti meg a tűzfalszabályok létrehozásához szükséges beállításokat:
    * A tűzfalszabály létrehozásakor kattintson a **szabály hozzáadása** elemre.
    * Válasszon ki egy adott tűzfal-táblázatot a **hálózat > a tűzfalszabályok** lapon, és kattintson az **Új tűzfalszabály létrehozása** lehetőségre.
2. Állítsa be a szabályt a következőképpen:
    * **Név**. Adjon nevet a szabálynak.
    * **Prioritás**. Rendeljen hozzá egy prioritást a szabályhoz. A rendszer először az alacsonyabb számú szabályokat hajtja végre.
    * **Forgalom típusa** Válassza ki, hogy a szabály a privát felhő, az Internet vagy a VPN-forgalom (állapot nélküli) vagy egy nyilvános IP-cím (állapot) esetén van-e.
    * **Protokoll**. Válassza ki a szabály által lefedett protokollt (TCP, UDP vagy bármely protokoll).
    * **Irány**. Válassza ki, hogy a szabály bejövő vagy kimenő forgalomra vonatkozik-e. Külön szabályokat kell megadnia a bejövő és a kimenő forgalomhoz.
    * **Művelet**. Válassza ki az elvégzendő műveletet, ha a szabály megfelel (Engedélyezés vagy megtagadás).
    * **Forrás**. Határozza meg a szabály által lefedett forrásokat (CIDR blokk, belső vagy bármilyen forrás).
    * **Forrásport tartománya**. A szabály hatálya alá eső portok tartományának meghatározása.
    * **Irány**. Válassza a bejövő vagy a kimenő lehetőséget.
    * **Cél**. Határozza meg a szabály által lefedett célhelyeket (CIDR blokk, belső vagy bármilyen forrás).
    * **Forrásport tartománya**. A szabály hatálya alá eső portok tartományának meghatározása.

    ![Tűzfalszabály hozzáadása szabály](media/firewall-rule-create.png)

3. Kattintson a **kész** gombra a szabály mentéséhez és a tűzfalszabály szabályainak listájához való hozzáadásához.

> [!IMPORTANT]
> Minden egyes tűzfalszabály legfeljebb 10 bejövő szabályt és 20 kimenő szabályt tartalmazhat. Ezeket a korlátokat a [támogatási szolgálattal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)növelheti.

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN-ok/alhálózatok csatolása

A tűzfalszabályok definiálása után megadhatja azokat az alhálózatokat, amelyek a táblázatban szereplő szabályok hatálya alá esnek.

1. A **hálózati**  >  **tűzfal táblái** lapon válasszon ki egy tűzfal-táblázatot.
2. Nyissa meg a **csatolt VLAN-ok/alhálózat** lapot.
3. Kattintson **a csatolás egy VLAN/alhálózat** elemre.
4. Válassza ki a privát felhőt és a VLAN-t. Megjelenik a társított alhálózat neve és CIDR-blokkja.
5. Kattintson a **Submit (Küldés**) gombra.
