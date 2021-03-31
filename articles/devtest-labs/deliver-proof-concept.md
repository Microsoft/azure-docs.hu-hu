---
title: A koncepció igazolása – Azure DevTest Labs | Microsoft Docs
description: Megtudhatja, hogyan teheti elérhetővé a koncepciót, hogy Azure DevTest Labs sikeresen beépíthető legyen egy vállalati környezetbe.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288018"
---
# <a name="deliver-a-proof-of-concept"></a>A koncepció igazolása 

Azure DevTest Labs egyik fő forgatókönyve a Felhőbeli fejlesztési és tesztelési környezetek engedélyezése. Példák:

* Fejlesztői asztalok létrehozása a felhőben.
* Környezetek konfigurálása teszteléshez.
* A virtuális gépekhez és más Azure-erőforrásokhoz való hozzáférés engedélyezése.
* Egy sandbox-terület beállítása a fejlesztők számára a tanuláshoz és a kísérletezéshez.

A DevTest Labs emellett biztosítja a szabályzat guardrails és a költséghatékonyságot, amely lehetővé teszi a vállalat számára, hogy "önkiszolgáló" Azure-t biztosítson a vállalati biztonsági, szabályozási és megfelelőségi szabályzatoknak megfelelő fejlesztők számára. 

Minden vállalat különböző követelményekkel rendelkezik, hogy a Azure DevTest Labs hogyan lehet sikeresen beépíteni a környezetbe. Ez a cikk azokat a leggyakoribb lépéseket ismerteti, amelyeket a vállalatoknak végre kell hajtaniuk a koncepció sikeres igazolásának biztosítása érdekében. A sikeres végpontok közötti üzembe helyezés első lépése a koncepció igazolása. 

## <a name="getting-started"></a>Első lépések 

A koncepció bizonyításának megkezdéséhez. Fontos, hogy némi időt töltsön fel az Azure és a DevTest Labs megismerésére.  Íme néhány kezdő erőforrás: 

* [A Azure Portal ismertetése](https://azure.microsoft.com/features/azure-portal/)
* [Az DevTest Labs alapjai](devtest-lab-overview.md)
* [DevTest Labs – támogatott forgatókönyvek](devtest-lab-guidance-get-started.md)
* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Bevezetés az Azure hálózatkezelésbe](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Előfeltételek 

A DevTest Labs szolgáltatással való tesztelés vagy a koncepció bizonyításának sikeres elvégzéséhez néhány előfeltétel van: 

* **Azure-előfizetés**: a vállalatoknak gyakran van meglévő [nagyvállalati szerződésük](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) , amely lehetővé teszi az Azure-hoz való hozzáférést, és meglévő vagy új előfizetést használhat a DevTest Labs szolgáltatásban. Azt is megteheti, hogy a vállalatok [Visual Studio-előfizetést](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) használnak a próbaüzem során (kihasználva az ingyenes Azure-krediteket). Ha egyik lehetőség sem érhető el, a vállalatok létrehozhatnak és használhatnak [ingyenes Azure-fiókot](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Ha Nagyvállalati Szerződés van érvényben, a [Enterprise dev/test-előfizetés](https://azure.microsoft.com/offers/ms-azr-0148p/) használatával nagyszerű lehetőség a Windows 10/Windows 8,1 ügyfél operációs rendszereinek elérésére, valamint a fejlesztési és tesztelési feladatok kedvezményes díjszabására. 
* **Azure Active Directory bérlő**: Ha engedélyezni szeretné a felhasználók felügyeletét (például felhasználók hozzáadását vagy labor-tulajdonosok hozzáadását), ezeknek a felhasználóknak az Azure-előfizetésben használt [Azure Active Directory-bérlő](https://azure.microsoft.com/services/active-directory/) részét kell tartalmazniuk. A vállalatok gyakran [hibrid identitást](../active-directory/hybrid/whatis-hybrid-identity.md) állítanak be, amely lehetővé teszi a felhasználók számára, hogy a helyszíni identitást használják a felhőben, de erre nincs szükség a DevTest Labs-pilóta számára. 

## <a name="scoping-of-the-pilot"></a>A pilóta hatóköre 

A megvalósítás megkezdése előtt fontos a próbaüzem megtervezése. Előzetesen megtudhatja, hogy az erőforrások határozatlan ideig nem maradnak meg a próbaüzem felhasználói számára. 

> [!IMPORTANT]
> Nem tudjuk eléggé hangsúlyozni, hogy a pilóta éles hatókörű legyen, és elölről állítja be a várakozási sorrendet.

A próba elkezdése előtt válaszoljon a legfontosabb kérdésekre: 

* Mit szeretne tanulni, és mi a siker a pilóta számára? 
* Milyen számítási feladatokra vagy forgatókönyvekre vonatkozik a próbaüzem? Fontos, hogy csak egy kis készletet adjon meg, amely biztosítja, hogy a próbaüzem hatóköre és gyors befejezése is megtörténjen. 
* Milyen erőforrásoknak kell elérhetőnek lenniük a laborban? Például: egyéni lemezképek, Piactéri lemezképek, házirendek, hálózati topológia. 
* Kik azok a felhasználók és csapatok, akik a pilótában részt vesznek a felhasználói élmény ellenőrzésében?  
* Mi a pilóta időtartama? Olyan időkeretet válasszon, amely jól igazodik a tervezett hatókörhöz, például két hétig vagy egy hónapig. 
* A próba befejezése után mi fog történni a próbaüzem során használt lefoglalt erőforrásokkal? Tervezi a kísérleti erőforrások elvetését? Gondolná:
   
   "Ha megtervezjük a virtuális gépeket és a laborokat a pilóta végén, akkor beállíthat egy előfizetést a próbaüzem számára, és ott is elvégezheti a munkát, miközben megoldja a méretezési bevezetési kérdéseket párhuzamosan." 

Általános tendencia, hogy a próbaüzem "tökéletes", így az a végső állapotot jelenti, amely a szolgáltatás a vállalatnál való kivonása után válik ki. Ez egy hamis feltételezés. Minél közelebb kerül a "tökéletes" eléréshez, annál többet kell elvégeznie a próbaüzem megkezdése *előtt* . A próba célja, hogy a végső szolgáltatás horizontális felskálázásával és bevezetésével megfelelő döntéseket hozzon. 

A pilóta középpontjának a minimálisan szükséges számítási feladatok és függőségek kiválasztásával kell megválaszolnia, hogy Azure DevTest Labs a vállalat számára megfelelő szolgáltatás-e. Javasoljuk, hogy a lehető legkevesebb függőséggel válassza ki a legegyszerűbb munkaterhelést, így biztosítva a gyors és tiszta sikerességet. Ha ez nem lehetséges, válasszon ki egy olyan reprezentatív számítási feladatot, amely elérhetővé teszi a lehetséges bonyolultságot, hogy a kísérleti fázis sikere a kibővített fázisban is replikálható legyen. 

Az alábbi példa jól hatókörű koncepciót mutat be. 

## <a name="example-proof-of-concept-plan"></a>Példa: megvalósíthatósági terv 

Ez a szakasz egy olyan mintát mutat be, amely a DevTest Labs-hez készült próba koncepciójának meghatározására használható. 

> [!TIP]
> Ha csökkenteni szeretné a projekt hibához való beállításának lehetőségét, azt javasoljuk, hogy ne ugorja át az ebben a szakaszban ismertetett példát. 

### <a name="overview"></a>Áttekintés 

Az Azure-beli DevTest Labs-alapú új környezet fejlesztését tervezzük a szállítók számára a vállalati hálózattól elkülönített környezetként való használatra. Annak megállapításához, hogy a megoldás megfelel-e a követelményeknek, a rendszer kidolgozza a koncepció igazolását a végpontok közötti megoldás ellenőrzéséhez. Több szállítót is mellékeltünk a kipróbáláshoz és a felhasználói élmény ellenőrzéséhez. 

### <a name="outcomes"></a>Eredmények 

A megvalósíthatósági koncepció kiépítésekor először a eredményekre koncentrálunk (amit igyekszünk elérni). A koncepció igazolásának végére a következőket várjuk: 

* Teljes körű megoldás a szállítók számára a Azure Active Directory (Azure AD) vendég fiókjainak használatára az Azure elszigetelt környezetének eléréséhez. A környezetnek olyan erőforrásai vannak, amelyek szükségesek ahhoz, hogy hatékonyak legyenek. 
* A szélesebb körű használatot és bevezetést befolyásoló lehetséges blokkolási problémák enumerálása és értelmezése.
* A koncepció igazolásának kidolgozásában részt vevő személyeknek jól kell ismerniük az összes kódot. Emellett tisztában vannak az érintett biztosítékokkal és a szélesebb körű bevezetéssel.

### <a name="open-questions-and-prerequisites"></a>Kérdések és előfeltételek megnyitása 

* Létrehozunk egy előfizetést, amelyet használhatunk ehhez a projekthez? 
* Van-e az Azure ad-bérlő és egy globális rendszergazda, aki az Azure AD-vel kapcsolatos kérdéseivel kapcsolatban nyújt segítséget és útmutatást? 
* Van-e közös együttműködés a projektben dolgozó személyekkel? 

   * Forráskód és parancsfájlok (például az Azure Repos) 
   * Dokumentumok (például Microsoft Teams vagy SharePoint)  
   * Beszélgetések (például Microsoft Teams) 
   * Munkaelemek (például Azure-táblák) 
* Mik a szükséges erőforrások a szállítók számára? Ez magában foglalja a hálózaton elérhető alkalmazásokat is, helyileg a virtuális gépeken és más szükséges kiszolgálókon. 
* A virtuális gépek csatlakozni fognak az Azure-beli tartományhoz? Ha igen, akkor ez Azure Active Directory Domain Services (Azure AD DS) vagy valami más? 
* Azonosította azokat a csapatot vagy szállítókat, amelyek a koncepció igazolásának célja lesz? Kik lesznek a környezet ügyfelei?
* Melyik Azure-régiót fogjuk használni a koncepció igazolásához? 
* Vannak olyan szolgáltatások listája, amelyeket a szállítók a IaaS (VM) mellett DevTest Labs használatával is használhatnak? 
* Hogyan tervezjük a szállítókat/felhasználókat a labor használatával betanítani? 

### <a name="components-of-the-proof-of-concept-solution"></a>A megvalósíthatósági megoldás összetevői 

A következő összetevőkkel várjuk a megoldást: 

* A különböző szállítói csapatok több Labs-készletet használnak az Azure-ban.
* A Labs olyan hálózati infrastruktúrához csatlakozik, amely támogatja a követelményeket.
* A szállítók az Azure AD-vel és a szerepkör-hozzárendelésekkel férhetnek hozzá a laborokhoz.
* A szállítók sikeresen csatlakozhatnak az erőforrásaihoz. A helyek közötti VPN lehetővé teszi, hogy közvetlenül a nyilvános IP-címek nélkül férhessenek hozzá a virtuális gépekhez.
* Az összetevők készlete tartalmazza azt a szükséges szoftvert, amelyre a gyártóknak szüksége van a virtuális gépeken.

## <a name="additional-planning-and-design-decisions"></a>További tervezési és kialakítási döntések 

A teljes DevTest Labs-megoldás kiadása előtt fontos tervezési és kialakítási döntéseket kell hoznia. A megvalósíthatósági koncepció használatának tapasztalatai segíthetnek a döntések meghozatalában. A További szempontok a következők: 

* **Előfizetési topológia**: az Azure-beli erőforrásokra vonatkozó vállalati szintű követelmények a rendelkezésre álló kvótán kívülre is kiterjedhetnek [egy adott előfizetésen belül](../azure-resource-manager/management/azure-subscription-service-limits.md). Ehhez több Azure-előfizetésre és/vagy szolgáltatási kérelemre van szükség a kezdeti előfizetési korlátok növeléséhez. Fontos, hogy eldöntse, hogyan oszthat ki erőforrásokat az előfizetések között. Egy értékes erőforrás az [előfizetés döntési útmutatója](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , mert nehéz áthelyezni az erőforrásokat egy másik előfizetésbe. Egy labort például nem lehet áthelyezni egy másik előfizetésbe a létrehozása után.  
* **Hálózati topológia**: Előfordulhat, hogy a DevTest Labs által automatikusan létrehozott [alapértelmezett hálózati infrastruktúra](../app-service/networking-features.md) nem elegendő ahhoz, hogy megfeleljen a vállalati felhasználókra vonatkozó követelményeknek és korlátozásoknak. Gyakran előfordul, hogy az [Azure ExpressRoute csatlakoztatott virtuális hálózatokat](/azure/architecture/reference-architectures/hybrid-networking/), [valamint](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) az előfizetések közötti kapcsolatot, valamint a helyszíni csatlakozás biztosítására [kényszerített útválasztást](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) is meg kell tekinteni. A DevTest Labs lehetővé teszi, hogy a meglévő virtuális hálózatok a laborhoz kapcsolódjanak, ha új virtuális gépeket hoz létre a laborban. 
* **Virtuális gépek távelérése**: számos lehetőség van a DevTest Labs szolgáltatásban található virtuális gépek távoli elérésére. A legegyszerűbb a nyilvános IP-címek vagy a megosztott nyilvános IP-címek használata. Ezek a [laborban elérhető beállítások](devtest-lab-shared-ip.md). Ha ezek a lehetőségek nem elegendőek, a távelérési átjáró használata is lehetőség. Ez a beállítás a [DevTest Labs nagyvállalati referenciájának architektúráján](devtest-lab-reference-architecture.md) látható, és a [DevTest Labs távoli asztali átjáró dokumentációjában](configure-lab-remote-desktop-gateway.md)olvashat bővebben. A vállalatok a ExpressRoute vagy a helyek közötti VPN-t is használhatják a laboratóriumok helyszíni hálózathoz való összekapcsolásához. Ez a beállítás lehetővé teszi a közvetlen távoli asztal vagy SSH-kapcsolatok használatát a virtuális gépeken a magánhálózati IP-címük alapján, az internettel való érintkezés nélkül. 
* **Kezelési engedélyek**: a DevTest Labs szolgáltatásban gyakran használt két fő engedély a [tulajdonos és a labor felhasználó](devtest-lab-add-devtest-user.md). Fontos eldönteni, hogy a DevTest Labs széleskörű kivonása előtt a laborban minden egyes hozzáférési szinthez meg kell bíznia. A közös modell a költségvetés tulajdonosa (például a csapat vezetője), mint a labor tulajdonosa és a csapat tagjai labor felhasználóként. Ez a modell lehetővé teszi a költségvetésért felelős személy (csapat vezető) számára a házirend-beállítások módosítását és a csapat költségvetésen belüli megőrzését.  

## <a name="completing-the-proof-of-concept"></a>A koncepció igazolásának befejezése 

A várt beolvasás után itt az ideje, hogy elvégezze a próbaüzem befejezését. Itt az ideje, hogy visszajelzést kapjon a felhasználóktól, megállapítsa, hogy a próbaüzem sikeres volt-e, és döntse el, hogy a szervezet a DevTest Labs a vállalaton belüli bevezetésén halad-e. Emellett érdemes megfontolni a DevTest Labs és a kapcsolódó erőforrások üzembe helyezésének automatizálását is, hogy az egységesség biztosítható legyen a teljes körű bevezetés során. 

## <a name="next-steps"></a>Következő lépések 

* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Vállalati hivatkozási architektúra](devtest-lab-reference-architecture.md)
* [A DevTest Labs üzembe helyezésének vertikális felskálázása](devtest-lab-guidance-orchestrate-implementation.md)
* [A Azure DevTest Labs megvalósításának összehangolása](devtest-lab-guidance-orchestrate-implementation.md)
