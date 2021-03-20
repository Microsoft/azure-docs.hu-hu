---
title: A Azure DevTest Labs-infrastruktúra vertikális felskálázása
description: Ez a cikk útmutatást nyújt a Azure DevTest Labs infrastruktúra méretezéséhez.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478790"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>A Azure DevTest Labs-infrastruktúra vertikális felskálázása
A nagyvállalati szintű DevTest Labs megvalósítása előtt több kulcsfontosságú döntési pont is van. Ezen döntési pontok magas szinten való megismerése segíti a szervezetet a jövőbeli tervezési döntések meghozatalában. Ezek a pontok azonban nem tudják visszatartani a szervezetet a koncepció igazolásának elindításával. A kezdeti méretezési tervezés első három területe a következő:

- Hálózatkezelés és biztonság
- Előfizetési topológia
- Szerepkörök és felelősségek

## <a name="networking-and-security"></a>Hálózatkezelés és biztonság
A Hálózatkezelés és a biztonság minden szervezet számára sarokköve. A nagyvállalati szintű üzembe helyezés sokkal mélyebb elemzést tesz szükségessé, ezért kevesebb követelményt kell elvégeznie a koncepció igazolásának sikeres végrehajtásához. A hangsúly néhány kulcsfontosságú területe a következőkre terjed ki:

- **Azure-előfizetés** – a DevTest Labs üzembe helyezéséhez hozzá kell férnie egy Azure-előfizetéshez, amely megfelelő jogosultságokkal rendelkezik az erőforrások létrehozásához. Több módon is elérheti az Azure-előfizetéseket, beleértve az Nagyvállalati Szerződés és a fizetést. További információ az Azure-előfizetések elérésének beszerzéséről: [Az Azure licencelése a vállalat számára](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Hozzáférés a helyszíni erőforrásokhoz** – egyes szervezetek számára a DevTest Labs erőforrásainak megkövetelése a helyszíni erőforrások eléréséhez. A helyszíni környezet és az Azure közötti biztonságos kapcsolódásra van szükség. Ezért fontos, hogy az első lépések megkezdése előtt állítsa be vagy konfigurálja a VPN-t vagy az Express Route-kapcsolatát. További információ: [Virtual Networks Overview (virtuális hálózatok áttekintése](../virtual-network/virtual-networks-overview.md)).
- **További biztonsági követelmények** – az egyéb biztonsági követelmények, például a gépi házirendek, a nyilvános IP-címekhez való hozzáférés, az internethez való csatlakozás olyan forgatókönyvek, amelyeket a megvalósíthatósági gyakorlat megvalósítása előtt érdemes áttekinteni. 

## <a name="subscription-topology"></a>Előfizetési topológia
Az előfizetési topológia kritikus tervezési szempont, ha a DevTest Labs szolgáltatást a vállalatra telepíti. Azonban nincs szükség az összes döntés bemegszilárdítására addig, amíg meg nem történt a koncepció igazolása. A nagyvállalati megvalósításhoz szükséges előfizetések számának kiértékelése során két véglet van: 

- Egy előfizetés a teljes szervezet számára
- Előfizetés felhasználónként

Ezután kiemeljük az egyes megközelítések előnyeit.

### <a name="one-subscription"></a>Egy előfizetés
Az egyik előfizetés megközelítése gyakran nem kezelhető nagy vállalatokban. Az előfizetések számának korlátozása azonban a következő előnyöket biztosítja:

- A vállalat **előrejelzési** költségei.  A költségvetés-tervezés sokkal egyszerűbbé válik egy adott előfizetésben, mivel az összes erőforrás egyetlen készletben található. Ez a megközelítés lehetővé teszi az egyszerűbb döntéshozatalt abban az esetben, ha egy számlázási ciklusban egy adott időpontban a Cost Control-mértékek gyakorlása folyamatban van.
- A virtuális gépek, az összetevők, a képletek, a hálózati konfiguráció, az engedélyek, a házirendek stb. egyszerűbb **kezelhetősége** , mivel az összes frissítés csak az egyik előfizetéshez szükséges, és nem a sok előfizetésre vonatkozó frissítések.
- A **hálózatkezelési** erőfeszítések nagy mértékben leegyszerűsítve vannak olyan vállalatok esetében, amelyeknél követelmény a helyszíni kapcsolat. A virtuális hálózatok összekapcsolása az előfizetések között (sugaras modell) további előfizetésekre van szükség, amelyekhez további konfigurálásra, felügyeletre, IP-címekre stb. van szükség.
- A **csapatmunka** egyszerűbb, ha mindenki ugyanazon az előfizetésen dolgozik, például egyszerűbb egy virtuális gépet egy munkatárshoz rendelni, megoszthatja a csoport erőforrásait stb.

### <a name="subscription-per-user"></a>Előfizetés felhasználónként
Felhasználónként egy külön előfizetés biztosítja az alternatív spektrumot. A számos előfizetés előnyei többek között a következők:

- Az **Azure skálázási kvóták** nem akadályozzák az örökbefogadást. Az Azure írásakor például az 200 Storage-fiókok előfizetését teszik lehetővé. Az Azure legtöbb szolgáltatásának működési kvótái vannak (számos személyre szabható, de nem). A felhasználónkénti előfizetés ezen modelljében nagyon valószínűtlen, hogy a legtöbb kvótát elérte. Az aktuális Azure skálázási kvótákkal kapcsolatos további információkért lásd az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md)ismertető témakört.
-  A csoportokba vagy az egyéni fejlesztőkbe való jóváírások sokkal egyszerűbbé válnak, így a szervezetek a jelenlegi modelljük alapján tudják felvenni a költségeket.
- A DevTest Labs-környezetek **tulajdonosi & engedélyei** egyszerűek. Az előfizetési szintű hozzáférést a fejlesztőknek kell megadnia, és ezekért a 100% felelős, beleértve a hálózati konfigurációt, a labor-házirendeket és a virtuális gépek kezelését.

Előfordulhat, hogy a vállalaton belül a spektrum szélsőségesen megtalálhatók a korlátok. Ezért előfordulhat, hogy az előfizetéseket úgy kell beállítania, hogy a szélsőségesek közepébe essen. Az ajánlott eljárás a szervezet célja, hogy a lehető legkevesebb előfizetést használja az előfizetések teljes számának növelésére. Az előfizetési topológia kritikus fontosságú a DevTest Labs vállalati telepítéséhez, de nem késleltetheti a koncepció igazolását. További részleteket az [irányításról](devtest-lab-guidance-governance-policy-compliance.md) szóló cikkben talál, amely bemutatja, hogyan döntheti el az előfizetést és a labor részletességét a szervezeten belül.

## <a name="roles-and-responsibilities"></a>Szerepkörök és felelősségek
A DevTest Labs-igazolási koncepció három elsődleges szerepkört tartalmaz: az előfizetés tulajdonosa, a DevTest Labs tulajdonosa, a DevTest Labs-felhasználó és opcionálisan közreműködő.

- **Előfizetés tulajdonosa** – az előfizetés tulajdonosa rendelkezik olyan Azure-előfizetés felügyeletéhez szükséges jogokkal, mint a felhasználók kiosztása, a házirendek kezelése, & a hálózati topológia kezelése, a kvóta növelésének megadásával stb. További információkért tekintse meg [ezt a cikket](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs-tulajdonos** – a DevTest Labs-tulajdonos teljes körű rendszergazdai hozzáféréssel rendelkezik a laborhoz. Ez a személy felelős a felhasználók hozzáadásával/eltávolításával, a költségadatok kezelésével, az általános labor-beállításokkal és az egyéb virtuális gépekkel/összetevőkkel kapcsolatos feladatokkal. A labor tulajdonosa is rendelkezik egy DevTest Labs-felhasználó minden jogosultságával.
- **DevTest Labs-felhasználó** – a DevTest Labs-felhasználó létrehozhat és felhasználhatja a virtuális gépeket a laborban. Ezek a személyek bizonyos minimális felügyeleti képességekkel rendelkeznek az általuk létrehozott virtuális gépeken (a virtuális gépek indítása/leállítása/törlése/konfigurálása). A felhasználók nem kezelhetik más felhasználók virtuális gépeket.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a sorozat következő cikkét: [a Azure DevTest Labs megvalósításának](devtest-lab-guidance-orchestrate-implementation.md) összehangolása