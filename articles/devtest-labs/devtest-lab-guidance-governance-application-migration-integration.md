---
title: Alkalmazások migrálása és integrációja Azure DevTest Labs
description: Ez a cikk útmutatást nyújt a Azure DevTest Labs-infrastruktúra irányításához az alkalmazások áttelepítése és integrációja kontextusában.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289346"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Azure DevTest Labs infrastruktúra irányítása – alkalmazások áttelepítése és integrációja
A fejlesztési/tesztelési labor környezet létrehozása után a következő kérdésekre kell gondolnia:

- Hogyan hasznosíthatja a környezetet a projekt csapata keretében?
- Hogyan gondoskodhat arról, hogy kövesse az összes szükséges szervezeti szabályzatot, és fenntartsa a rugalmasságot az alkalmazás értékének megadásához?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-lemezképek és Egyéni rendszerképek

### <a name="question"></a>Kérdés
Mikor érdemes a saját egyéni szervezeti rendszerképét használni az Azure Marketplace-en?

### <a name="answer"></a>Válasz
Alapértelmezés szerint az Azure Marketplace-t kell használni, hacsak nem rendelkezik konkrét problémával vagy szervezeti követelményekkel. Néhány gyakori példa a következőkre:

- Összetett Szoftvertelepítés, amely megköveteli, hogy az alkalmazás szerepeljen az alaprendszerkép részeként.
- Az alkalmazások telepítése és beállítása több órát is igénybe vehet, amelyek nem hatékonyan használják a számítási időt az Azure Marketplace-rendszerképben való hozzáadáshoz.
- A fejlesztőknek és a tesztelőknek gyorsan hozzá kell férniük egy virtuális géphez, és az új virtuális gép telepítési idejét szeretnék csökkenteni.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági szabályzatok), amelyeknek az összes gépen meg kell felelnie.

Az Egyéni rendszerképek használata nem tekinthető könnyelműnek. További bonyolultságot jelentenek, mivel mostantól a mögöttes alaplemezképekhez tartozó VHD-fájlokat kell kezelnie. Ezeket az alapképeket is rendszeresen kell frissítenie a szoftverfrissítések használatával. Ezek a frissítések tartalmazzák az új operációs rendszer (OS) frissítéseit, valamint a szoftvercsomag szükséges frissítéseit és konfigurációs módosításait.

## <a name="formula-vs-custom-image"></a>Képlet és egyéni rendszerkép

### <a name="question"></a>Kérdés
Mikor érdemes képletet vagy egyéni rendszerképet használni?

### <a name="answer"></a>Válasz
Az ebben a forgatókönyvben szereplő döntési tényező általában a költségek és az újrafelhasználás.

Ha van olyan forgatókönyv, amelyben sok felhasználó/labor igényel egy olyan rendszerképet, amely sok szoftvert tartalmaz az alaprendszerképhez, akkor az egyéni rendszerkép létrehozásával csökkentheti a költségeket. Ez azt jelenti, hogy a rendszerkép egyszer jön létre. Ez csökkenti a virtuális gép telepítési idejét és a telepítéskor futó virtuális gép miatt felmerülő költségeket.

A szoftvercsomag változásainak gyakorisága azonban egy további Megjegyzés. Ha napi buildeket futtat, és megköveteli, hogy a szoftver a felhasználók virtuális gépei legyenek, használjon egy képletet egyéni rendszerkép helyett.

## <a name="use-custom-organizational-images"></a>Egyéni szervezeti rendszerképek használata

### <a name="question"></a>Kérdés
Hogyan állíthatok be egy könnyen ismételhető folyamatot az egyéni szervezeti lemezképek DevTest Labs-környezetben való üzembe helyezéséhez?

### <a name="answer"></a>Válasz
Tekintse [meg ezt a videót a rendszerkép-előállító mintán](./devtest-lab-faq.md#blog-post). Ez a forgatókönyv egy speciális forgatókönyv, és a megadott parancsfájlok csak példaként használható parancsfájlok. Ha bármilyen módosításra van szükség, felügyelni és karbantartani kell a környezetben használt parancsfájlokat.

Egyéni rendszerkép-folyamat létrehozása az Azure-folyamatokban a DevTest Labs használatával:

- [Bevezetés: a virtuális gépek gyors üzembe helyezése a rendszerkép-előállító Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Rendszerkép-előállító – 2. rész! Az Azure-folyamatok és a Factory Lab beállítása virtuális gépek létrehozásához](./devtest-lab-faq.md#blog-post)
- [Image Factory – 3. rész: Egyéni rendszerképek mentése és terjesztés több laborba](./devtest-lab-faq.md#blog-post)
- [Videó: egyéni rendszerkép-előállító Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>A hálózati konfiguráció beállítására szolgáló minták

### <a name="question"></a>Kérdés
Hogyan gondoskodjon arról, hogy a fejlesztési és tesztelési virtuális gépek nem tudják elérni a nyilvános internetet? Vannak ajánlott minták a hálózati konfiguráció beállításához?

### <a name="answer"></a>Válasz
Igen. Két szempontot kell figyelembe venni – a bejövő és a kimenő forgalmat.

**Bejövő forgalom** – ha a virtuális gép nem rendelkezik nyilvános IP-címmel, akkor nem érhető el az Internet. A közös megközelítéssel biztosítható, hogy egy előfizetési szintű szabályzatot állítsanak be, hogy a felhasználók ne tudjanak nyilvános IP-címet létrehozni.

**Kimenő forgalom** – ha meg szeretné akadályozni, hogy a virtuális gépek közvetlenül a nyilvános internetre lépjenek, és egy vállalati tűzfalon keresztül kényszerítsék a forgalmat, akkor a helyszíni forgalmat expressz útvonalon vagy VPN-en keresztül irányíthatja kényszerített útválasztás használatával.

> [!NOTE]
> Ha olyan proxykiszolgálót tartalmaz, amely a proxybeállítások nélkül blokkolja a forgalmat, ne felejtsen el kivételeket hozzáadni a labor összetevő-tárolási fiókjához.

Hálózati biztonsági csoportokat is használhat virtuális gépekhez vagy alhálózatokhoz. Ez a lépés egy további védelmi réteget ad hozzá a forgalom engedélyezéséhez/letiltásához.

## <a name="new-vs-existing-virtual-network"></a>Új és meglévő virtuális hálózat

### <a name="question"></a>Kérdés
Mikor hozzon létre egy új virtuális hálózatot a DevTest Labs-környezethez és egy meglévő virtuális hálózat használatával?

### <a name="answer"></a>Válasz
Ha a virtuális gépeknek kapcsolatba kell lépniük a meglévő infrastruktúrával, érdemes megfontolnia, hogy a DevTest Labs-környezetben meglévő virtuális hálózatot használjon. Emellett, ha a ExpressRoute-t használja, érdemes lehet minimálisra csökkenteni a virtuális hálózatok/alhálózatok mennyiségét, hogy az előfizetésekben használt IP-címtartomány ne legyen kiosztva. Érdemes megfontolni a VNet-társítási minta használatát is (sugaras modell). Ez a megközelítés lehetővé teszi, hogy az egyes régiókban lévő előfizetések között vnet/alhálózati kommunikációt lehessen biztosítani, bár a régiók közötti együttműködés az Azure-hálózatkezelés egyik feltörekvő funkciója.

Ellenkező esetben minden DevTest Labs-környezet rendelkezhet saját virtuális hálózattal. Vegye figyelembe azonban, hogy az előfizetéshez tartozó virtuális hálózatok száma [korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md) . Az alapértelmezett érték 50, de ez a korlát 100-re is kiemelhető.

## <a name="shared-public-or-private-ip"></a>Megosztott, nyilvános vagy magánhálózati IP-cím

### <a name="question"></a>Kérdés
Mikor érdemes megosztott IP-címet vagy nyilvános IP-címet vagy magánhálózati IP-címet használni?

### <a name="answer"></a>Válasz
Ha helyek közötti VPN-t vagy expressz útvonalat használ, érdemes lehet privát IP-címeket használni, hogy a gépek a belső hálózaton keresztül is elérhetők legyenek, és a nyilvános interneten keresztül elérhetetlenek legyenek.

> [!NOTE]
> A labor tulajdonosai megváltoztathatják ezt az alhálózati házirendet annak biztosítása érdekében, hogy a virtuális gépekhez egyetlen véletlenül se hozzon létre nyilvános IP-címet. Az előfizetés tulajdonosának létre kell hoznia egy előfizetési szabályzatot, amely megakadályozza a nyilvános IP-címek létrehozását.

Megosztott nyilvános IP-címek használata esetén a laborban lévő virtuális gépek nyilvános IP-címet használnak. Ez a megközelítés akkor lehet hasznos, ha el szeretné kerülni az adott előfizetés nyilvános IP-címeire vonatkozó korlátok megszegését.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Virtuális gépek számának korlátozása felhasználónként vagy laborban

### <a name="question"></a>Kérdés
Van olyan szabály, amely azt határozza meg, hogy hány virtuális gépet kell beállítani felhasználónként vagy laborban?

### <a name="answer"></a>Válasz
Ha a virtuális gépek száma felhasználónként vagy laborban történik, három fő szempontot kell figyelembe venni:

- A csapat által az erőforrásokra fordított **teljes költség** a laborban. Könnyedén elindíthat sok gépet. A költségek szabályozása érdekében az egyik mechanizmus a virtuális gépek számának korlátozása felhasználónként és/vagy tesztkörnyezetben
- A laborban található virtuális gépek teljes számát a rendelkezésre álló [előfizetési szint kvótái](../azure-resource-manager/management/azure-subscription-service-limits.md) befolyásolják. Az egyik felső határérték 800 erőforráscsoport. A DevTest Labs jelenleg minden virtuális géphez létrehoz egy új erőforráscsoportot (kivéve, ha megosztott nyilvános IP-címek vannak használatban). Ha 10 Labs van egy előfizetésben, a Labs az egyes laborokban körülbelül 79 virtuális gépet fér hozzá (800 felső korlát – 10 erőforráscsoport a 10 Labs számára) = 79 virtuális gépek/labor.
- Ha a tesztkörnyezet az expressz útvonalon keresztül csatlakozik a helyszíni környezethez (például), a VNet/alhálózat számára **meghatározott IP-címek állnak rendelkezésre** . Annak biztosítása érdekében, hogy a laborban lévő virtuális gépek ne jöjjenek létre (hiba: nem lehet lekérni az IP-címet), a labor tulajdonosai megadhatják a laborok maximális számát az elérhető IP-címtartomány szerint.

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata

### <a name="question"></a>Kérdés
Hogyan használhatom a Resource Manager-sablonokat a DevTest Labs-környezetben?

### <a name="answer"></a>Válasz
A Resource Manager-sablonokat egy DevTest Labs-környezetbe helyezheti üzembe a [DevTest Labs környezetek funkciójában](devtest-lab-test-env.md) ismertetett lépések segítségével. Alapvetően a Resource Manager-sablonokat egy git-tárházba (Azure Repos vagy GitHub) tekintheti meg, és hozzáadhat egy [privát tárházat a sablonokhoz](devtest-lab-test-env.md) a laborban.

Ez a forgatókönyv nem lehet hasznos, ha DevTest Labs-t használ a fejlesztői gépek üzemeltetéséhez, de hasznos lehet, ha olyan átmeneti környezetet hoz létre, amely az éles környezetre jellemző.

Azt is érdemes megjegyezni, hogy a virtuális gépek száma a laborban vagy felhasználónként lehetőség csak a laborban natív módon létrehozott gépek számát korlátozza, nem pedig bármely környezet (Resource Manager-sablonok) számára.

## <a name="next-steps"></a>Következő lépések
Lásd: [környezetek használata a DevTest Labs szolgáltatásban](devtest-lab-test-env.md).
