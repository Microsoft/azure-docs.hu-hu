---
title: DevTest Labs-fogalmak | Microsoft Docs
description: Ismerje meg a DevTest Labs alapfogalmait, és hogy miként könnyítheti meg az Azure-beli virtuális gépek létrehozását, kezelését és figyelését
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327858"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-fogalmak
## <a name="overview"></a>Áttekintés
Az alábbi lista a főbb DevTest Labs-fogalmakat és-definíciókat tartalmazza:

## <a name="labs"></a>Tesztkörnyezetek
A labor olyan infrastruktúra, amely magában foglalja az erőforrások egy csoportját, például a Virtual Machines (VM), amely lehetővé teszi, hogy a korlátok és kvóták megadásával hatékonyabban kezelhesse ezeket az erőforrásokat.

## <a name="virtual-machine"></a>Virtuális gép
Az Azure-beli virtuális gépek az Azure által kínált különböző típusú [, igény szerinti, méretezhető számítási erőforrások](/azure/architecture/guide/technology-choices/compute-decision-tree) egyike. Az Azure-beli virtuális gépek lehetővé teszik a virtualizáció rugalmasságát anélkül, hogy az azt futtató fizikai hardvereket kellene vásárolnia és fenntartania, de továbbra is fenn kell tartania a virtuális gépet bizonyos feladatok (például a konfigurálás, a javítás és a rajta futó szoftver telepítése) végrehajtásával.

[Az Azure-beli Windows rendszerű virtuális gépek áttekintése](../virtual-machines/windows/overview.md) információt nyújt arról, hogy mit kell figyelembe vennie a virtuális gép létrehozása előtt, hogyan hozza létre, és hogyan kezelje.

## <a name="claimable-vm"></a>Igényelhető virtuális gép
Az Azure-beli igényelhető virtuális gépek egy olyan virtuális gép, amelyet bármely, engedélyekkel rendelkező labor-felhasználó használhat. A tesztkörnyezet rendszergazdája előkészítheti a virtuális gépeket meghatározott alapképekkel és összetevőkkel, és mentheti őket egy megosztott készletbe. A labor-felhasználók ezután igényelhetnek egy működő virtuális gépet a készletből, ha erre a konfigurációra van szükségük.

Egy igényelt virtuális gép kezdetben nincs hozzárendelve egy adott felhasználóhoz, de az összes felhasználó listájában megjelenik a "igényelhető virtuális gépek" alatt. Azt követően, hogy egy virtuális gépet egy felhasználó igényel, a rendszer áthelyezi a "saját virtuális gépek" területen, és más felhasználók már nem igényelhetik azt.

## <a name="environment"></a>Környezet
A DevTest Labs szolgáltatásban a környezet a tesztkörnyezetben található Azure-erőforrások gyűjteményére utal. [Ez a blogbejegyzés](./devtest-lab-faq.md#blog-post) a több virtuális gépre kiterjedő környezetek Azure Resource Manager-sablonokból való létrehozását ismerteti.

## <a name="base-images"></a>Alapként szolgáló rendszerképek
Az alaplemezképek a virtuális gépek gyors létrehozásához és konfigurálásához szükséges összes eszközt és beállítást tartalmazó virtuálisgép-lemezképek. A virtuális gép kiépítéséhez válasszon ki egy meglévő alapot, és adjon hozzá egy összetevőt a tesztelési ügynök telepítéséhez. Ezután mentheti a kiépített virtuális gépet úgy, hogy az a virtuális gép minden egyes üzembe helyezése után a tesztelési ügynök újratelepítése nélkül is használható legyen.

## <a name="artifacts"></a>Artifacts
Az összetevők az alkalmazás üzembe helyezésére és konfigurálására szolgálnak a virtuális gép kiépítése után. Az összetevők a következőket tehetik:

* A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
* A virtuális gépen futtatni kívánt műveletek – például a tárház klónozása.
* A tesztelni kívánt alkalmazások.

Az összetevők olyan JSON-fájlok [Azure Resource Manager](../azure-resource-manager/management/overview.md) , amelyek a telepítés végrehajtásához és a konfiguráció alkalmazásához szükséges utasításokat tartalmazzák.

## <a name="artifact-repositories"></a>Összetevő-Tárházak
Az összetevők tárházai olyan git-adattárak, amelyekben az összetevők be vannak jelölve. Az összetevő-Tárházak hozzáadhatók több, a szervezeten belüli laborhoz, amely lehetővé teszi az újbóli használatot és megosztást.

## <a name="formulas"></a>Képletek
Az alapképeken kívül a képletek lehetővé teszik a virtuális gépek üzembe helyezését. A DevTest Labs egyik képlete a tesztkörnyezet virtuális gépe létrehozásához használt alapértelmezett tulajdonságértékek listája.
A képletekkel azonos tulajdonságokkal rendelkező virtuális gépek (például az alaprendszerkép, a virtuálisgép-méret, a virtuális hálózat és az összetevők) létrehozhatók anélkül, hogy ezeket a tulajdonságokat minden alkalommal meg kellene adni. A virtuális gépek képletből való létrehozásakor az alapértelmezett értékek használhatók vagy módosíthatók.

## <a name="policies"></a>Házirendek
A szabályzatok segítenek a költségek szabályozásában a laborban. Létrehozhat például egy olyan szabályzatot, amely automatikusan leállítja a virtuális gépeket egy meghatározott ütemterv alapján.

## <a name="caps"></a>Sapka
A Caps egy olyan mechanizmus, amellyel a laborban csökkentheti a hulladékot. Beállíthat például egy korlátot, amely korlátozza a felhasználónként létrehozható virtuális gépek számát, vagy egy laborban.

## <a name="security-levels"></a>Biztonsági szintek
A biztonsági hozzáférés meghatározása az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján történik. A hozzáférés működésének megismeréséhez segít megérteni az engedélyek, a szerepkörök és az Azure RBAC által meghatározott hatókör közötti különbségeket.

* Engedély – az engedély meghatározott hozzáférés egy adott művelethez (például olvasási hozzáférés az összes virtuális géphez).
* Szerepkör – A szerepkör a felhasználók számára csoportosítható és hozzárendelhető engedélyek összessége. Az *előfizetés tulajdonosi* szerepköre például hozzáférhet az előfizetésen belüli összes erőforráshoz.
* Hatókör – a hatókör az Azure-erőforrások hierarchiáján belüli szint, például egy erőforráscsoport, egy tesztkörnyezet vagy a teljes előfizetés.

A DevTest Labs hatókörén belül kétféle szerepkör létezik a felhasználói engedélyek definiálásához: a labor tulajdonosa és a labor felhasználó.

* Tesztkörnyezet tulajdonosa – a labor tulajdonosa hozzáférhet a laborban található összes erőforráshoz. Ezért a labor tulajdonosa módosíthatja a szabályzatokat, megtekintheti és megírhatja a virtuális gépeket, megváltoztathatja a virtuális hálózatot, és így tovább.
* Labor felhasználó – A labor felhasználó megtekintheti az összes laboratóriumi erőforrást, például virtuális gépeket, házirendeket és virtuális hálózatokat, de nem módosíthatja a házirendeket vagy a más felhasználók által létrehozott virtuális gépeket.

Ha szeretné megtudni, hogyan hozhat létre egyéni szerepköröket a DevTest Labs szolgáltatásban, tekintse meg a következő cikket: [felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel a hatókörök hierarchikusak, amikor egy felhasználó egy bizonyos hatókörön belüli engedélyekkel rendelkezik, automatikusan megkapja ezeket az engedélyeket minden alacsonyabb szintű hatókörben. Ha például egy felhasználó az előfizetés tulajdonosa szerepkörhöz van rendelve, akkor az előfizetéshez tartozó összes erőforráshoz hozzáférhet, amely tartalmazza az összes virtuális gépet, az összes virtuális hálózatot és az összes labort. Az előfizetés tulajdonosa ezért automatikusan örökli a tesztkörnyezet tulajdonosának szerepkörét. Az ellenkezője azonban nem igaz. A labor tulajdonosa egy laborhoz fér hozzá, amely az előfizetési szintnél alacsonyabb hatókörű. Ezért a tesztkörnyezet tulajdonosai nem láthatják a virtuális gépeket és a virtuális hálózatokat, illetve a laboron kívüli erőforrásokat.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
Az ebben a cikkben tárgyalt fogalmakat Azure Resource Manager sablonok használatával konfigurálhatja, amelyek lehetővé teszik az Azure-megoldás infrastruktúrájának/konfigurációjának meghatározását, és a rendszer ismételt üzembe helyezését konzisztens állapotba.

[Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md#template-format) egy Azure Resource Manager sablon struktúráját és a sablon különböző részeiben elérhető tulajdonságokat ismerteti.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
[Labor létrehozása a DevTest Labs szolgáltatásban](devtest-lab-create-lab.md)
