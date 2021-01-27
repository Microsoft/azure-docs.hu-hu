---
title: Adaptív hálózati megerősítés a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a tényleges forgalmi mintákat a hálózati biztonsági csoportok (NSG) szabályainak megerősítéséhez és a biztonsági helyzet javításához.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: ca60d5afa38a560492c8574aadd43d6170eca253
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916184"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptív hálózati megerősítés Azure Security Center
Ismerje meg, hogyan konfigurálhatja az adaptív hálózatok megerősítését Security Centerban.

## <a name="availability"></a>Rendelkezésre állás
|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender for Servers](defender-for-servers-introduction.md) használatát igényli|
|Szükséges szerepkörök és engedélyek:|Írási engedélyek a gép NSG|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Mi az adaptív hálózat megerősítése?
[Hálózati biztonsági csoportok (NSG-EK)](../virtual-network/network-security-groups-overview.md) alkalmazása az erőforrások felé irányuló és onnan érkező forgalom szűrésére, javítja a hálózat biztonsági helyzetét. Azonban továbbra is előfordulhatnak olyan esetek, amikor a NSG keresztül áramló tényleges forgalom a definiált NSG-szabályok részhalmaza. Ezekben az esetekben a NSG-szabályoknak a tényleges forgalmi minták alapján történő megerősítése révén a biztonsági helyzet további javítása is megvalósítható.

Az adaptív hálózat megerősítése ajánlásokat biztosít a NSG-szabályok további megerősítéséhez. Egy gépi tanulási algoritmust használ, amely a tényleges forgalom, az ismert megbízható konfiguráció, a veszélyforrások felderítése és más, a biztonsággal kapcsolatos mutatók szempontjából tényezőket tartalmaz, és javaslatokat tesz arra, hogy csak adott IP-/port-rekordok származó forgalmat engedélyezzen.

Tegyük fel például, hogy a meglévő NSG-szabály a 22-es porton engedélyezi a 140.20.30.10/24 forgalmat. A forgalom elemzése alapján az adaptív hálózat megerősítése javaslatot tehet a tartomány szűkítéséhez a 140.23.30.10/29-től érkező forgalom engedélyezéséhez, és az adott portra irányuló összes egyéb forgalom megtagadását.

>[!Note]
> Az adaptív hálózati korlátozási javaslatok csak a következő konkrét portokon támogatottak (UDP és TCP esetén egyaránt): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Megerősítő riasztások és szabályok megtekintése és kezelése

1. A Security Center menüjében nyissa meg az **Azure Defender** irányítópultját, és válassza ki az adaptív hálózat megerősítő csempéjét (1), vagy az adaptív hálózat megerősítéséhez (2) kapcsolódó bepillantást az adattábla elemre. 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Az adaptív hálózat megerősítő eszközeinek elérése" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Az Áttekintés panelen látható, hogy a virtuális gépek hány százalékát védi az adaptív hálózat megerősítése. 

1. Az adaptív hálózat korlátozási javaslatainak részletes oldalát alkalmazni kell az **internetre irányuló virtuális gépekre** vonatkozó ajánlásban, amely három lapra van csoportosítva:
   * Nem megfelelő **erőforrások**: azok a virtuális gépek, amelyeken jelenleg az adaptív hálózat megerősítő algoritmusának futtatásával aktiválva vannak javaslatok és riasztások. 
   * **Kifogástalan erőforrások**: riasztások nélküli virtuális gépek és javaslatok.
   * Nem **vizsgált erőforrások**: a következő okok egyike miatt nem futtathatók az adaptív hálózati kötési algoritmust használó virtuális gépek:
      * **A virtuális gépek klasszikus virtuális gépek**: csak Azure Resource Manager virtuális gépek támogatottak.
      * **Nem áll rendelkezésre elegendő mennyiségű adatok**: a pontos forgalom-megerősítési javaslatok létrehozásához Security Center legalább 30 napos forgalmi adatokat igényel.
      * A **virtuális gépet az Azure Defender nem védi**: csak az [Azure Defender által a kiszolgálókhoz](defender-for-servers-introduction.md) védett virtuális gépek jogosultak erre a szolgáltatásra.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Az ajánlott adaptív hálózatok megerősítésére vonatkozó javaslatok részleteit az internetre irányuló virtuális gépeken kell alkalmazni":::

1. A nem megfelelő **állapotú erőforrások** lapon válasszon ki egy virtuális gépet a riasztások megtekintéséhez, és a javasolt korlátozási szabályokat alkalmazza.

    - A **szabályok** lap felsorolja azokat a szabályokat, amelyekkel az adaptív hálózat megerősítő javasolja a hozzáadását
    - A **riasztások** lapon láthatók azok a riasztások, amelyek a forgalom miatt keletkeztek, az erőforrás felé áramlanak, amely nem az ajánlott szabályokban engedélyezett IP-tartományon belül van.

1. Szükség esetén szerkessze a szabályokat:

    - [Szabály módosítása](#modify-rule)
    - [Szabály törlése](#delete-rule) 
    - [A szabály beállítása](#add-rule)

3. Válassza ki a NSG alkalmazni kívánt szabályokat, majd kattintson a **kikényszerítés** gombra.

    > [!TIP]
    > Ha az engedélyezett forrás IP-címtartományok "None"-ként jelennek meg, az azt jelenti, hogy az ajánlott szabály egy *megtagadási* szabály, ellenkező esetben ez egy *engedélyezési* szabály.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Adaptív hálózat megerősítési szabályainak kezelése":::

      > [!NOTE]
      > A kényszerített szabályok hozzáadódnak a virtuális gépet védő NSG (ok) hoz. (A virtuális gépet egy hálózati adapterhez társított NSG, vagy a virtuális gépet tartalmazó alhálózattal, vagy mindkettővel védeni lehet.)

### <a name="modify-a-rule"></a>Szabály <a name ="modify-rule"></a> módosítása

Érdemes lehet módosítani egy javasolt szabály paramétereit. Előfordulhat például, hogy módosítani szeretné az ajánlott IP-tartományokat.

Néhány fontos útmutató az adaptív hálózat megerősítési szabályának módosításához:

* Az "engedélyezés" szabályok paramétereinek módosítása csak. 
* Az "engedélyezés" szabályok nem módosíthatók "megtagadás" szabályokként. 

  > [!NOTE]
  > A "megtagadás" szabályok létrehozása és módosítása közvetlenül a NSG történik. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../virtual-network/manage-network-security-group.md).

* A **minden forgalmi szabály megtagadása** az egyetlen "megtagadás" szabály, amely itt is szerepel, és nem módosítható. Azonban törölheti is (lásd: [szabály törlése](#delete-rule)).
  > [!NOTE]
  > A **minden forgalmi szabály megtagadása** ajánlott, ha az algoritmus futtatásának eredményeképpen a Security Center nem azonosítja az engedélyezett forgalmat a meglévő NSG-konfiguráció alapján. Ezért az ajánlott szabály az, hogy megtagadja a megadott portra irányuló összes forgalmat. Az ilyen típusú szabály neve "*rendszer által generált*" néven jelenik meg. A szabály érvénybe léptetése után annak tényleges neve a NSG lesz, amely a protokoll, a forgalom iránya, a "MEGTAGADÁS" és a véletlenszerű szám.

*Adaptív hálózati kötési szabály módosítása:*

1. Egy szabály egyes paramétereinek módosításához a **szabályok** lapon kattintson a három pontra (...) a szabály sorának végén, majd kattintson a **Szerkesztés** gombra.

   ![S szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. A **szabály szerkesztése** ablakban frissítse a módosítani kívánt adatokat, majd kattintson a **Mentés** gombra.

   > [!NOTE]
   > A **Mentés** gombra kattintva sikeresen módosította a szabályt. *Azonban nem alkalmazta azt a NSG.* Az alkalmazásához ki kell választania a szabályt a listában, majd a **kényszerítés** elemet kell választania (a következő lépésben leírtak szerint).

   ![Mentés kiválasztása](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. A frissített szabály alkalmazásához a listából válassza ki a frissített szabályt, majd kattintson a **kikényszerítés** gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Új szabály <a name ="add-rule"></a> hozzáadása

Hozzáadhat egy "Allow" szabályt, amelyet a Security Center nem ajánlott.

> [!NOTE]
> Itt csak az "engedélyezés" szabályok adhatók hozzá. Ha meg szeretné adni a "megtagadás" szabályokat, ezt közvetlenül a NSG teheti meg. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](../virtual-network/manage-network-security-group.md).

*Adaptív hálózati kötési szabály hozzáadása:*

1. Kattintson a **szabály hozzáadása** elemre (a bal felső sarokban található).

   ![szabály hozzáadása](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Az **új szabály** ablakban adja meg a részleteket, és kattintson a **Hozzáadás** gombra.

   > [!NOTE]
   > A **Hozzáadás** gombra kattintva sikeresen felvette a szabályt, és az szerepel a többi javasolt szabályban. Azonban nem alkalmazta azt a NSG. Az aktiváláshoz ki kell választania a szabályt a listában, majd a **kényszerítés** gombra kell kattintania (a következő lépésben leírtak szerint).

3. Az új szabály alkalmazásához a listából válassza ki az új szabályt, majd kattintson a **kikényszerítés** gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Szabály <a name ="delete-rule"></a> törlése

Ha szükséges, törölheti az aktuális munkamenet javasolt szabályát. Meghatározhatja például, hogy egy javasolt szabály alkalmazása blokkolhatja a legitim forgalmat.

*Adaptív hálózati kötési szabály törlése az aktuális munkamenethez:*

1. A **szabályok** lapon kattintson a három pontra (...) a szabály sor végén, majd kattintson a **Törlés** gombra.  

    ![Szabály törlése](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)