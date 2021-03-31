---
title: Azure DevTest Labs infrastruktúra irányítása – erőforrás
description: Ez a cikk a szervezeten belüli Azure DevTest Labs erőforrásainak igazítását és kezelését ismerteti.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480915"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Azure DevTest Labs infrastruktúra irányítása – erőforrások
Ez a cikk a szervezeten belüli DevTest Labs erőforrásainak igazítását és kezelését ismerteti. 

## <a name="align-within-an-azure-subscription"></a>Igazítás Azure-előfizetésen belül 

### <a name="question"></a>Kérdés
Hogyan DevTest Labs-erőforrásokat egy Azure-előfizetésen belül?

### <a name="answer"></a>Válasz
Mielőtt egy szervezet elkezdi használni az Azure-t az alkalmazások általános fejlesztéséhez, először tekintse át, hogyan vezesse be a képességet a szolgáltatások általános portfóliójának részeként. A felülvizsgálati területek a következő vonatkozásokkal foglalkoznak:

- Hogyan mérhető az alkalmazás-fejlesztési életciklushoz kapcsolódó díj?
- Hogyan igazítja a szervezet a javasolt szolgáltatási ajánlatot a vállalati biztonsági házirenddel? 
- A szegmentálás szükséges a fejlesztési és éles környezetek elkülönítéséhez? 
- Milyen vezérlőket vezetnek be a hosszú távú felügyelet, a stabilitás és a növekedés érdekében?

Az **első ajánlott eljárás** az, hogy áttekintse a szervezetek Azure-besorolását, ahol a termelési és fejlesztési előfizetések közötti megosztások szerepelnek. A következő ábrán a javasolt besorolás a fejlesztési, tesztelési és éles környezetek logikai elkülönítését teszi lehetővé. Ezzel a módszerrel a vállalatok számlázási kódokat is bevezethetnek, hogy nyomon kövessék az egyes környezetekhez kapcsolódó költségeket. További információ: a [előfizetési előfizetések irányítása](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Emellett az [Azure-címkék](../azure-resource-manager/management/tag-resources.md) használatával nyomon követheti az erőforrásokat követési és számlázási célokra.

A **második ajánlott eljárás** az DevTest-előfizetés engedélyezése az Azure Enterprise Portalon belül. Lehetővé teszi, hogy a szervezet olyan ügyféloldali operációs rendszereket futtasson, amelyek általában nem érhetők el egy Azure Enterprise-előfizetésben. Ezután használja a vállalati szoftvereket, ahol csak a számítási feladatokért kell fizetnie, és nem kell aggódnia a licenceléssel kapcsolatban. Biztosítja, hogy a kijelölt szolgáltatások (például a Microsoft SQL Server IaaS) számlázása csak felhasználáson alapuljon. Az Azure DevTest-előfizetéssel kapcsolatban [itt](https://azure.microsoft.com/offers/ms-azr-0148p/) talál további információt NAGYVÁLLALATI szerződés (EA) ügyfelek számára, és az ügyfelekért való fizetésért [itt](https://azure.microsoft.com/offers/ms-azr-0023p/) is tájékozódhat.

![Erőforrás-igazítás előfizetésekkel](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ez a modell lehetővé teszi a szervezet számára, hogy rugalmasan helyezzen üzembe Azure DevTest Labs a skálán. Egy szervezet több száz labort támogat a különböző üzleti egységek számára, párhuzamosan futó 100 – 1000 virtuális gépekkel. Egy központosított vállalati tesztkörnyezet fogalmát is támogatja, amely megoszthatja a konfiguráció-felügyeleti és biztonsági vezérlőkkel azonos alapelveket.

Ez a modell azt is biztosítja, hogy a szervezet Ne merítse ki az Azure-előfizetéséhez kapcsolódó erőforrás-korlátozásokat. Az előfizetési és a szolgáltatási korlátokkal kapcsolatos további információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md). A DevTest Labs kiépítési folyamata nagy számú erőforráscsoportot használhat fel. Az Azure DevTest-előfizetésben támogatási kéréssel megnövelheti a korlátokat. Az éles előfizetésben lévő erőforrásokat nem érinti a rendszer, mivel a fejlesztési előfizetés használatban van. A DevTest Labs méretezésével kapcsolatos további információkért lásd: [kvóták és korlátozások méretezése a DevTest Labs-ben](devtest-lab-scale-lab.md).

Általános előfizetési szint korlátja, amelyet figyelembe kell venni, hogy a hálózati IP-címtartomány hozzárendelései hogyan vannak lefoglalva a termelési és fejlesztési előfizetések támogatásához. Ezeknek a hozzárendeléseknek figyelembe kell venniük a növekedést az idő múlásával (feltéve, hogy a helyszíni kapcsolatot vagy egy másik hálózati topológiát igényelnek, amely megköveteli, hogy a vállalat az Azure implementációja helyett a hálózati verem kezelését hajtsa végre). Az ajánlott eljárás az, hogy néhány olyan virtuális hálózattal rendelkezzen, amely nagyméretű IP-címmel rendelkezik, és több nagy alhálózattal van elosztva, nem pedig több, kisebb alhálózattal rendelkező virtuális hálózattal. 10 előfizetéssel például megadhat 10 virtuális hálózatot (egyet az egyes előfizetésekhez). Az elkülönítést nem igénylő laborok ugyanazt az alhálózatot tudják megosztani az előfizetés vnet.

## <a name="maintain-naming-conventions"></a>Elnevezési konvenciók fenntartása

### <a name="question"></a>Kérdés
Hogyan fenntartani az DevTest Labs-környezet elnevezési konvencióját?

### <a name="answer"></a>Válasz
Érdemes lehet kiterjeszteni az aktuális vállalati elnevezési konvenciókat az Azure-műveletekre, és az DevTest Labs-környezetben is konzisztensen kell lennie.

A DevTest Labs telepítésekor javasoljuk, hogy konkrét kezdő házirendekkel rendelkezzen. Ezeket a házirendeket központi parancsfájlokkal és JSON-sablonokkal helyezheti üzembe a konzisztencia kikényszerítása érdekében. Az elnevezési házirendek az előfizetési szinten alkalmazott Azure-szabályzatok segítségével valósíthatók meg. A Azure Policy JSON-minták esetében lásd: [Azure Policy minták](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Felhasználók száma laborban és laborban/szervezetben

### <a name="question"></a>Kérdés 
Hogyan meghatározni a felhasználók és a laboratóriumok arányát, valamint a szervezeti egységekhez szükséges laborok teljes számát?

### <a name="answer"></a>Válasz
Azt javasoljuk, hogy ugyanahhoz a fejlesztési projekthez tartozó üzleti egységek és fejlesztési csoportok ugyanahhoz a laborhoz legyenek társítva. Lehetővé teszi, hogy ugyanazokat a szabályzatokat, lemezképeket és leállítási házirendeket alkalmazza mindkét csoportra. 

Előfordulhat, hogy a földrajzi határokat is figyelembe kell vennie. Az észak-keleti Egyesült Államok (Egyesült Államok) fejlesztői például egy Kelet-RÉGIÓJA kiépített labort használhatnak. A Dallasban, Texasban és Denverben lévő fejlesztők pedig az USA déli középső régiójában lévő erőforrások használatára irányíthatók. Ha egy külső harmadik féllel együttműködésen kívüli erőfeszítések vannak, akkor azokat olyan laborhoz lehet rendelni, amelyet a belső fejlesztők nem használnak. 

Az Azure DevOps projektjein belül egy adott projekthez is használhat labort. Ezután egy megadott Azure Active Directory csoporton keresztül alkalmazza a biztonságot, amely lehetővé teszi az erőforrások mindkét készletének elérését. A laborhoz rendelt virtuális hálózat lehet egy másik határ, amellyel összevonhatja a felhasználókat.

## <a name="deletion-of-resources"></a>Erőforrások törlése

### <a name="question"></a>Kérdés
Hogyan lehet megakadályozni az erőforrások törlését a laboron belül?

### <a name="answer"></a>Válasz
Javasoljuk, hogy a megfelelő engedélyeket a labor szintjén állítsa be úgy, hogy csak a jogosult felhasználók törölhetnek erőforrásokat, vagy módosíthatják a labor-házirendeket. A fejlesztőket a **DevTest Labs-felhasználók** csoportba kell helyezni. A vezető fejlesztőnek vagy az infrastruktúra-érdeklődőnek a **DevTest Labs-tulajdonosnak** kell lennie. Javasoljuk, hogy csak két labor tulajdonosa legyen. Ez a szabályzat a sérülés elkerülése érdekében kiterjed a kód tárházára. A labor a használati jogokkal rendelkezik az erőforrások használatához, de nem tudja frissíteni a labor-házirendeket. Tekintse meg a következő cikket, amely felsorolja azokat a szerepköröket és jogosultságokat, amelyekkel az egyes beépített csoportok egy laborban találhatók: a [tulajdonosok és a felhasználók hozzáadása a Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>A labor áthelyezése másik erőforráscsoport-csoportba 

### <a name="question"></a>Kérdés
Támogatott a labor áthelyezése egy másik erőforráscsoporthoz?

### <a name="answer"></a>Válasz
Igen. Navigáljon az erőforráscsoport lapra a labor kezdőlapján. Ezután válassza az **Áthelyezés** lehetőséget az eszköztáron, majd válassza ki azt a labort, amelyet másik erőforráscsoporthoz szeretne áthelyezni. Labor létrehozásakor a rendszer automatikusan létrehoz egy erőforráscsoportot. Előfordulhat azonban, hogy át szeretné helyezni a labort egy másik erőforráscsoporthoz, amely követi a vállalati elnevezési konvenciókat. 

## <a name="next-steps"></a>Következő lépések
Lásd: [a Cost és a tulajdonlás kezelése](devtest-lab-guidance-governance-cost-ownership.md).
