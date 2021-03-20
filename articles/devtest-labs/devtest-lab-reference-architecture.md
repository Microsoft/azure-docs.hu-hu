---
title: Nagyvállalati hivatkozási architektúra a Azure DevTest Labshoz
description: Ez a cikk a vállalat Azure DevTest Labsához nyújt útmutatást.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328844"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs a vállalatok hivatkozási architektúrája
Ez a cikk olyan hivatkozási architektúrát tartalmaz, amely a vállalatok Azure DevTest Labs alapján történő központi telepítésében nyújt segítséget. A következőket tartalmazza:
- Helyszíni kapcsolat az Azure ExpressRoute-on keresztül
- Távoli asztali átjáró a virtuális gépekre való távoli bejelentkezéshez
- A privát összetevőkhöz tartozó összetevő-tárházhoz való kapcsolódás
- A Labs-ben használt egyéb Pásti-szolgáltatások

![Hivatkozási architektúra diagramja](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektúra
Ezek a hivatkozási architektúra legfontosabb elemei:

- **Azure Active Directory (Azure ad)**: a DevTest Labs az [IDENTITÁSKEZELÉS az Azure ad szolgáltatással](../active-directory/fundamentals/active-directory-whatis.md)működik. Vegye figyelembe ezt a két fontos szempontot, amikor a felhasználók számára hozzáférést biztosít a DevTest Labs-alapú környezetekhez:
    - **Erőforrás-kezelés**: hozzáférést biztosít a Azure Portalhoz az erőforrások kezeléséhez (virtuális gépek létrehozása; környezetek létrehozása, a Start, a Leállítás, az újraindítás, a törlés és az összetevők alkalmazása stb.). Az erőforrás-kezelést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával végezheti el. Szerepköröket rendelhet a felhasználókhoz, és beállíthatja az erőforrás-és hozzáférési szintű engedélyeket.
    - **Virtuális gépek (hálózati szintű)**: az alapértelmezett konfigurációban a virtuális gépek helyi rendszergazdai fiókot használnak. Ha van elérhető tartomány ([Azure ad Domain Services](../active-directory-domain-services/overview.md), egy helyszíni tartomány vagy egy felhőalapú tartomány), a gépek csatlakoztathatók a tartományhoz. A felhasználók ezután a tartományon alapuló identitások használatával csatlakozhatnak a virtuális gépekhez.
- Helyszíni **kapcsolat**: az architektúra ábrán a [ExpressRoute](../expressroute/expressroute-introduction.md) van használatban. De használhat helyek közötti VPN- [t](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)is. Bár a ExpressRoute nem szükségesek a DevTest Labs szolgáltatáshoz, gyakran használják a vállalatokban. A ExpressRoute csak akkor szükséges, ha vállalati erőforrások elérésére van szüksége. Gyakori forgatókönyvek:
    - Olyan helyszíni adattal rendelkezik, amely nem helyezhető át a felhőbe.
    - Szeretné csatlakoztatni a labor virtuális gépeket a helyszíni tartományhoz.
    - A felhőalapú környezet összes hálózati forgalmát a biztonsági/megfelelőségi helyszíni tűzfalon keresztül szeretné kényszeríteni.
- **Hálózati biztonsági csoportok**: a forrás-és cél IP-címek alapján a Felhőbeli környezet (vagy a felhőalapú környezet) forgalmának korlátozása egy [hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md)használata. Tegyük fel például, hogy csak a vállalati hálózatról származó forgalmat kívánja engedélyezni a tesztkörnyezet hálózatai között.
- **Távoli asztali átjáró**: a vállalatok általában letiltják a kimenő távoli asztali kapcsolatokat a vállalati tűzfalon. Számos lehetőség áll rendelkezésre a DevTest Labs felhőalapú környezetéhez való kapcsolódás engedélyezéséhez, beleértve a következőket:
  - Használjon [Távoli asztali átjárót](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), és engedélyezze az átjáró Load Balancer statikus IP-címét.
  - A ExpressRoute/helyek közötti VPN-kapcsolaton keresztül [irányítsa az összes bejövő RDP-forgalmat](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) . Ez a funkció közös szempont, ha a vállalatok DevTest Labs-telepítést terveznek.
- **Hálózati szolgáltatások (virtuális hálózatok, alhálózatok)**: az [Azure hálózati](../networking/networking-overview.md) topológiája egy másik kulcsfontosságú elem a DevTest Labs architektúrában. Azt szabályozza, hogy a laborból származó erőforrások kommunikálhatnak-e, és hozzáférnek-e a helyszíni és az internethez. Az architektúra ábránk a leggyakoribb módszereket tartalmazza, amelyekkel az ügyfelek a DevTest Labs szolgáltatást használják: az összes labor [virtuális hálózati](../virtual-network/virtual-network-peering-overview.md) kapcsolaton keresztül csatlakozik egy [sugaras modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használatával a EXPRESSROUTE/helyek közötti VPN-kapcsolathoz a helyszíni környezethez. A DevTest Labs azonban közvetlenül az Azure Virtual Network-t használja, így nincs korlátozás a hálózati infrastruktúra beállításával kapcsolatban.
- **DevTest Labs**: a DevTest Labs a teljes architektúra kulcsfontosságú része. További információ a szolgáltatásról: [About DevTest Labs](devtest-lab-overview.md).
- **Virtuális gépek és egyéb erőforrások (SaaS, Péter, IaaS)**: a virtuális gépek kulcsfontosságú számítási feladatok, amelyeket a DevTest Labs más Azure-erőforrásokkal együtt támogat. A DevTest Labs segítségével egyszerűen és gyorsan biztosítható, hogy a vállalatok hozzáférjenek az Azure-erőforrásokhoz (beleértve a virtuális gépeket és más Azure-erőforrásokat). Tudjon meg többet az Azure-beli [fejlesztőknek](devtest-lab-developer-lab.md) és [tesztelőknek](devtest-lab-test-env.md)való hozzáférésről.

## <a name="scalability-considerations"></a>Méretezési szempontok
Bár a DevTest Labs nem rendelkezik beépített kvótákkal vagy korlátozásokkal, a laborok tipikus működésében használt egyéb Azure-erőforrásokhoz [előfizetési szintű kvóták](../azure-resource-manager/management/azure-subscription-service-limits.md)tartoznak. Tehát egy tipikus vállalati környezetben több Azure-előfizetésre van szükség a DevTest Labs nagyméretű üzembe helyezésének biztosításához. A vállalatok által leggyakrabban elérhető kvóták a következők:

- **Erőforráscsoportok**: az alapértelmezett konfigurációban a DevTest Labs létrehoz egy erőforráscsoportot minden új virtuális géphez, vagy a felhasználó létrehoz egy környezetet a szolgáltatás használatával. Az előfizetések [akár 980 erőforráscsoportot](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)is tartalmazhatnak. Tehát a virtuális gépek és a környezetek korlátja egy előfizetésben. Két másik konfigurációt kell figyelembe vennie:
    - **[Az összes virtuális gép ugyanarra az erőforráscsoporthoz kerül](resource-group-control.md)**: bár ez a telepítő segít az erőforráscsoport-korlátnak való megfelelésben, hatással van az erőforrás típusú erőforrás-csoportra vonatkozó korlátra.
    - **Megosztott nyilvános IP**-címek használata: az azonos méretű és régiót használó virtuális gépek ugyanabba az erőforráscsoporthoz kerülnek. Ez a konfiguráció "középutat" az erőforráscsoport-kvóták és az erőforrás-típus/erőforrás-csoport kvótái között, ha a virtuális gépek számára engedélyezett a nyilvános IP-címek használata.
- **Erőforrások** erőforráscsoport szerinti erőforrásai: az erőforrás-csoportonként az erőforrások alapértelmezett korlátja [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Ha az *összes virtuális gépet ugyanarra az erőforráscsoport* -konfigurációra használja, a felhasználók sokkal hamarabb elérik ezt az előfizetést, különösen akkor, ha a virtuális gépek sok további lemezzel rendelkeznek.
- **Storage-fiókok**: a DevTest Labs egyik laborja tartalmaz egy Storage-fiókot. A [Storage-fiókok régiónként megadott számú](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)Azure-kvóta 250. Az azonos régióban található DevTest-laborok maximális száma szintén 250.
- **Szerepkör-hozzárendelések**: a szerepkör-hozzárendeléssel megadhatja, hogy egy felhasználó vagy egy elsődleges hozzáférés legyen egy erőforráshoz (tulajdonos, erőforrás, jogosultsági szint). Az Azure-ban az [előfizetés legfeljebb 2 000 szerepkör-hozzárendelést](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits)foglal le. Alapértelmezés szerint a DevTest Labs szolgáltatás minden virtuális géphez létrehoz egy erőforráscsoportot. A tulajdonos *tulajdonosi* engedélyt kap a DevTest Labs virtuálisgép-és *olvasói* engedélye számára az erőforráscsoporthoz. Ily módon minden létrehozott új virtuális gép két szerepkör-hozzárendelést használ azon hozzárendelések mellett, amelyeket akkor használ, amikor a felhasználók engedélyt adnak a labornak.
- **API-olvasások/írások**: többféleképpen automatizálható az Azure és a DevTest Labs, beleértve a REST API-kat, a PowerShellt, az Azure CLI-t és az Azure SDK-t. Az Automation segítségével az API-kérelmek egy másik korlátja is megjelenhet: minden előfizetés legfeljebb [12 000 olvasási kérést és 1 200 írási kérést](../azure-resource-manager/management/request-limits-and-throttling.md)tesz lehetővé. A DevTest Labs automatizálásakor vegye figyelembe a korlátot.

## <a name="manageability-considerations"></a>Felügyeleti szempontok
A DevTest Labs kiváló rendszergazdai felhasználói felületet biztosít egyetlen laborhoz. Egy vállalatnál azonban valószínűleg több Azure-előfizetéssel és számos laborral rendelkezik. Az összes laborhoz való konzisztens módosítások a parancsfájlok és az automatizálás használatát igénylik. Íme néhány példa és az ajánlott felügyeleti eljárások a DevTest Labs üzembe helyezéséhez:

- A **labor beállításainak módosítása**: gyakori forgatókönyv egy adott tesztkörnyezet-beállítás frissítése az üzemelő példány összes laborjában. Egy új virtuálisgép-példány mérete például elérhető, és az összes labort frissíteni kell, hogy engedélyezze azt. A módosításokat a PowerShell-parancsfájlok, a parancssori felület vagy a REST API-k használatával automatizálhatja.  
- Összetevő- **tárház személyes hozzáférési jogkivonata**: a git-tárház személyes hozzáférési jogkivonatai általában 90 nap, egy év vagy két év múlva lejárnak. A folytonosság biztosítása érdekében fontos kiterjeszteni a személyes hozzáférési jogkivonatot, vagy újat létrehozni. Ezután az Automation használatával alkalmazza az új személyes hozzáférési tokent az összes laborba.
- **Tesztkörnyezet beállításainak korlátozása**: gyakran egy adott beállítást korlátozni kell (például a Piactéri lemezképek használatának engedélyezése). A Azure Policy használatával megakadályozhatja az erőforrástípus módosításait. Létrehozhat egyéni szerepkört is, és a *tulajdonos* szerepkör helyett a szerepkörhöz tartozó felhasználókat is megadhatja a tesztkörnyezetben. Ezt a tesztkörnyezet legtöbb beállításához használhatja (belső támogatás, labor bejelentése, engedélyezett VM-méretek stb.).
- **A virtuális gépeket az elnevezési konvenció követéséhez kell megkövetelni**: a kezelők gyakran szeretnék egyszerűen azonosítani a felhőalapú fejlesztési és tesztelési környezet részét képező virtuális gépeket. Ezt a [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)használatával teheti meg.

Fontos megjegyezni, hogy a DevTest Labs a mögöttes Azure-erőforrásokat használja, amelyek ugyanúgy kezelhetők: hálózatkezelés, lemezek, számítás stb. A Azure Policy például a laborban létrehozott virtuális gépekre vonatkozik. Azure Security Center tud jelentést készíteni a virtuális gépek megfelelőségéről. A Azure Backup szolgáltatás pedig rendszeres biztonsági mentést biztosít a virtuális gépek számára a laborban.

## <a name="security-considerations"></a>Biztonsági szempontok
Azure DevTest Labs a meglévő erőforrásokat használja az Azure-ban (számítás, hálózatkezelés stb.). Így automatikusan kihasználhatja a platform beépített biztonsági funkcióit. Ahhoz például, hogy a bejövő távoli asztali kapcsolatok csak a vállalati hálózatról származzanak, egyszerűen vegyen fel egy hálózati biztonsági csoportot a virtuális hálózatra a távoli asztali átjárón. Az egyetlen további biztonsági szempont azon engedélyek szintje, amelyeket a laborokat a napi rendszerességgel használó csapattagoknak biztosítanak. A leggyakoribb engedélyek a [ *tulajdonos* és a *felhasználó*](devtest-lab-add-devtest-user.md). További információ ezekről a szerepkörökről: [tulajdonosok és felhasználók hozzáadása Azure DevTest Labsban](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket ebben a sorozatban: [a Azure DevTest Labs infrastruktúra vertikális Felskálázása](devtest-lab-guidance-scale.md).