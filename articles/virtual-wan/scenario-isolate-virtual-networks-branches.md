---
title: 'Forgatókönyv: a virtuális hálózatok és ágak egyéni elkülönítése'
titleSuffix: Azure Virtual WAN
description: Útválasztási forgatókönyvek – megakadályozza, hogy a kiválasztott virtuális hálózatok és ágak képesek legyenek elérni egymással
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99408481"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Forgatókönyv: a virtuális hálózatok és ágak egyéni elkülönítése

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. A virtuális hálózatok (virtuális hálózatok) és ágak egyéni elkülönítési forgatókönyvében a cél az, hogy megakadályozza, hogy egy adott virtuális hálózatok egy másik virtuális hálózatok érjen el. Hasonlóképpen, az ágak (VPN/ER/User VPN) csak bizonyos virtuális hálózatok elérésére jogosultak.

Emellett azt a további követelményt is bevezetjük, hogy Azure Firewall meg kell vizsgálnia a vnet és a vnet a forgalmat, de **nem**  vnet a vnet-forgalomra.  

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

Ha szeretné kideríteni, hogy hány útválasztási táblázatra van szükség, létrehozhat egy kapcsolati mátrixot. Ennél a forgatókönyvnél a következőhöz hasonlóan fog kinézni, ahol minden cella azt jelöli, hogy egy forrás (sor) tud-e kommunikálni egy célhoz (oszlophoz):

| Forrás | Címzett:| *Kék virtuális hálózatok* | *Piros virtuális hálózatok* | *Piros ágak*| *Kék ágak*| 
|---|---|---|---|---|---|
| **Kék virtuális hálózatok** |   &#8594;|   Direct     |           |   |  AzFW|
| **Piros virtuális hálózatok**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Piros ágak**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Kék ágak**| &#8594;| AzFW  |   |Direct   | Direct

Az előző táblázatban szereplő összes cella azt ismerteti, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a sorfejlécek) kommunikál-e egy célhoz (a folyamat "to" oldalára, a dőlt betűs oszlopokra). A **közvetlen** forgalom közvetlenül a virtuális WAN-on keresztül zajlik, míg a **AzFW** azt jelzi, hogy a forgalmat Azure Firewall megvizsgálják a célhelyre való továbbítás előtt. Egy üres bejegyzés azt jelenti, hogy a folyamat le van tiltva a telepítőben.

Ebben az esetben a virtuális hálózatok két útválasztási táblázata szükséges ahhoz, hogy el lehessen érni a VNet elkülönítés célját az elérési út Azure Firewall nélkül. Ezeket az útvonalak táblázatait **RT_BLUE** és **RT_RED** kell meghívni.

Emellett az ágakat mindig az  **alapértelmezett** útválasztási táblázathoz kell társítani. Annak biztosítása érdekében, hogy az ágak felé irányuló és onnan érkező adatforgalmat Azure Firewall ellenőrizze, statikus útvonalakat adunk hozzá az **alapértelmezett**, **RT_RED** és **RT_BLUE** útválasztási táblázat, amely Azure Firewall a forgalomra mutat, és beállítja a hálózati szabályokat a kívánt forgalom engedélyezéséhez. Azt is biztosítjuk, hogy az ágak **ne** legyenek propagálva **RT_BLUE** és **RT_RED**.

Ennek eredményeképpen ez a végső terv:

* Kék virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_BLUE**
  * Propagálás az útválasztási táblákba: **RT_BLUE**
* Vörös virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_RED**
  * Propagálás az útválasztási táblákba: **RT_RED** 
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**
* Statikus útvonalak:
    * **Alapértelmezett útválasztási táblázat**: Virtual Network címterület a következő ugrás Azure Firewall
    * **RT_RED**: 0.0.0.0/0 a következő ugrással Azure Firewall
    * **RT_BLUE**: 0.0.0.0/0 a következő ugrással Azure Firewall
* Tűzfal hálózati szabályai:
    * **Szabály** **forrásának** engedélyezése: kék ág címe előtagok **célhelyének előtagja**: kék VNet előtag 
    * **Szabály**  **forrásának** engedélyezése: vörös ág címe előtagok **célhelyének előtagja**: vörös vnet előtagjai

> [!NOTE]
> Mivel az összes ágat hozzá kell rendelni az alapértelmezett útválasztási táblázathoz, és az azonos útválasztási táblákra kell terjeszteni, minden ág ugyanazzal a csatlakozási profillal fog rendelkezni. Más szóval a virtuális hálózatok vörös/kék koncepciója nem alkalmazható ágakra. Ahhoz azonban, hogy az ágakhoz egyéni útválasztást lehessen elérni, továbbíthatja a forgalmat az ágakból a Azure Firewallba.

> [!NOTE]
> A Azure Firewall alapértelmezés szerint megtagadja a forgalmat egy zéró megbízhatóságú modellben. Ha nincs olyan explicit **engedélyezési** szabály, amely megfelel a megvizsgálandó csomagnak, Azure Firewall elveszi a csomagot.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Munkafolyamat

Az **1. ábrán** kék és piros virtuális hálózatok, valamint a kék vagy vörös virtuális hálózatok elérésére képes ágak találhatók.

* A kék kapcsolatban álló virtuális hálózatok elérheti egymást, és elérheti az összes kék ág (VPN/ER/P2S) kapcsolatát. A diagramon a kék ág a két hálózat közötti pont-pont típusú VPN-hely.
* A piros kapcsolattal rendelkező virtuális hálózatok elérheti egymással, és elérheti az összes vörös ág (VPN/ER/P2S) kapcsolatát. A diagramon a piros ág a pont – hely VPN-felhasználók.

Az Útválasztás beállításakor vegye figyelembe a következő lépéseket.

1. Hozzon létre két egyéni útválasztási táblázatot a Azure Portalban, **RT_BLUE** és **RT_RED** az ezen virtuális hálózatok érkező forgalom testreszabásához.
2. Az útválasztási táblázat **RT_BLUE** a következő beállításokat alkalmazva biztosíthatja, hogy a kék virtuális hálózatok megismerjék az összes többi kék virtuális hálózatok tartozó előtagot.:
   * **Társítás**: válassza az összes kék virtuális hálózatok elemet.
   * **Propagálás**: válassza az összes kék virtuális hálózatok elemet.
3. Ismételje meg ugyanezeket a lépéseket a piros virtuális hálózatok **RT_RED** útválasztási táblázatához.
4. Azure Firewall kiépítése a virtuális WAN-ban. További információ a virtuális WAN-központ Azure Firewalláról: [Azure Firewall konfigurálása a Virtual WAN hub-ban](howto-firewall.md).
5. Adjon hozzá egy statikus útvonalat a virtuális központ **alapértelmezett** útválasztási táblájához, amely a vnet-címtartomány (kék és piros) felé irányuló összes forgalmat átirányítja a Azure Firewall. Ez a lépés biztosítja, hogy az ágakból érkező csomagokat a rendszer elküldje a Azure Firewallnek ellenőrzés céljából.
    * Példa: **cél előtagja**: 10.0.0.0/24 **következő ugrás**: Azure Firewall
    >[!NOTE]
    > Ezt a lépést a Firewall Managerrel is elvégezheti, ha kiválasztja a "biztonságos privát forgalom" lehetőséget. Ezzel hozzáad egy útvonalat az összes RFC1918 magánhálózati IP-címhez, amely virtuális hálózatok és ágakra is érvényes. Manuálisan kell hozzáadnia azokat az ágakat és virtuális hálózatokat, amelyek nem felelnek meg a RFC1918. 

6. Adjon hozzá egy statikus útvonalat a **RT_REDhoz** , és **RT_BLUE** az összes forgalmat Azure Firewallre irányítsa. Ez a lépés biztosítja, hogy a virtuális hálózatok ne lehessen közvetlenül hozzáférni az ágakhoz. Ez a lépés nem hajtható végre a Firewall Manager használatával, mert ezek a virtuális hálózatok nincsenek társítva az alapértelmezett útválasztási táblázathoz.
    * Példa: **cél előtag**: 0.0.0.0/0 **következő ugrás**: Azure Firewall

    > [!NOTE]
    > Az Útválasztás a leghosszabb előtag-egyezés (LPM) használatával történik. Ennek eredményeképpen a 0.0.0.0/0 statikus útvonalak **nem** lesznek előnyben részesíteni a **BLUE_RT** és **RED_RTban** található pontos előtagokkal. Ennek eredményeképpen a vnet-alapú forgalmat a Azure Firewall nem fogja megvizsgálni.

Ez azt eredményezi, hogy az útválasztási konfiguráció az alábbi ábrán látható módon módosul.

**1** 
 . ábra [ ![ 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png) . ábra](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
