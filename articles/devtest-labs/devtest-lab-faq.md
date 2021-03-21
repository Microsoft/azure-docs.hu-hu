---
title: Azure DevTest Labs GYIK | Microsoft Docs
description: Ez a cikk a Azure DevTest Labs kapcsolatos gyakori kérdések (GYIK) néhány válaszát tartalmazza.
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 9fcdc160754822d5c6f22b7349d0e72f0cf22633
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590273"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs GYIK
Választ kaphat a Azure DevTest Labsával kapcsolatos leggyakoribb kérdésekre.

## <a name="blog-post"></a>Blogbejegyzés
A DevTest Labs csapatának blogja a 2019. március 20-án lett kivonva. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Hol követhetem nyomon a szolgáltatás frissítéseit mostantól?
Mostantól a szolgáltatás frissítéseit és az informatív blogbejegyzéseket közzétesszük az Azure blogon és az Azure frissítésein. Ezek a blogbejegyzések szükség esetén a dokumentációra is hivatkoznak.

Fizessen elő a [DevTest Labs Azure blogra](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) és az [DevTest Labs Azure-frissítéseire](https://azure.microsoft.com/updates/?product=devtest-lab) , és tájékozódjon a DevTest Labs új funkcióiról.

### <a name="what-happens-to-the-existing-blog-posts"></a>Mi történik a meglévő blogbejegyzésekkel?
Jelenleg dolgozunk a meglévő blogbejegyzések áttelepítésén (kivéve a leállás-frissítéseket) a [DevTest Labs dokumentációjában](devtest-lab-overview.md). Ha az MSDN-blog elavult, a rendszer átirányítja a DevTest Labs dokumentációjának áttekintésére. Az átirányítást követően megkeresheti a "szűrés a következővel" című cikket. Még nem történt meg az összes bejegyzés áttelepítve, de ennek a hónapnak a végéig kell történnie. 


### <a name="where-do-i-see-outage-updates"></a>Hol találhatók a leállás frissítései?
A kiesési frissítéseket a Twitter-leírónk használatával tesszük elérhetővé a mostantól kezdődően. Kövessen minket a Twitteren az kimaradások és az ismert hibák legújabb frissítéseinek beszerzéséhez.

### <a name="twitter"></a>Twitter
A Twitter-leírónk: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Általános kérdések
### <a name="what-if-my-question-isnt-answered-here"></a>Mi a teendő, ha nem válaszolt a kérdésre?
Ha a kérdés nem szerepel a listán, tudassa velünk, hogy segítsen megtalálni a választ.

- Tegye fel kérdéseit a GYIK végén.
- Ha szélesebb közönséget szeretne elérni, tegye fel kérdéseit a [Microsoft Q&a Azure DevTest Labs vonatkozó kérdés oldalára](/answers/topics/azure-devtestlabs.html). Folytassa a Azure DevTest Labs csapatával és a Közösség többi tagjával.
- A szolgáltatások kéréseinek elküldéséhez küldje el kérelmeit és ötleteit [Azure DevTest Labs felhasználói hangra](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Mi az a Microsoft-fiók?
A Microsoft-fiók a Microsoft-eszközökkel és-szolgáltatásokkal kapcsolatos szinte mindenhez használt fiók. Ez egy e-mail-cím és jelszó, amelyet a Skype, a Outlook.com, a OneDrive, a Windows Phone, az Azure és az Xbox Live szolgáltatásba való bejelentkezéshez használ. Egyetlen fiók azt jelenti, hogy a fájlok, a fényképek, a névjegyek és a beállítások bármely eszközön nyomon követhetik.

> [!NOTE]
> Egy Microsoft-fiók Windows Live ID-ként lett meghívva.

### <a name="why-should-i-use-azure-devtest-labs"></a>Miért érdemes használni a Azure DevTest Labs?
Azure DevTest Labs mentheti a csapat idejét és pénzét. A fejlesztők számos különböző bázis használatával hozhatnak létre saját környezeteket. Emellett az alkalmazások gyors üzembe helyezésére és konfigurálására is használhatók. Egyéni rendszerképek és képletek használatával a virtuális gépek (VM-EK) sablonként menthetők, és egyszerűen reprodukálható a csapaton belül. A DevTest Labs számos konfigurálható szabályzatot is kínál, amelyek segítségével a labor-rendszergazdák csökkenthetik a hulladékot, és kezelhetik a csapat környezetét. Ezek a szabályzatok az automatikus leállítást, a költséghatékonyságot, a felhasználónként maximálisan engedélyezett virtuális gépeket és a virtuálisgép-méretet tartalmazzák. A DevTest Labs részletesebb ismertetését az [áttekintésben](devtest-lab-overview.md) vagy a [bevezető videóban](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)találja.

### <a name="what-does-worry-free-self-service-mean"></a>Mit jelent a "problémamentes önkiszolgáló szolgáltatás"?
Az ingyenes önkiszolgáló szolgáltatás azt jelenti, hogy a fejlesztők és a tesztelők igény szerint saját környezeteket hozhatnak létre. A rendszergazdák biztonságban tudhatják, hogy a DevTest Labs segíthet a megfelelő hozzáférés megadásában, a költségek csökkentésében és a szabályozás költségein. A rendszergazdák meghatározhatják, hogy mely virtuálisgép-méretek engedélyezettek, a virtuális gépek maximális száma, valamint a virtuális gépek indítása és leállítása. A DevTest Labs emellett megkönnyíti a költségek figyelését és a riasztások beállítását, így könnyebben tájékozódhat a labor-erőforrások használatáról.

### <a name="how-can-i-use-devtest-labs"></a>Hogyan használhatom a DevTest Labs szolgáltatást?
A DevTest Labs hasznos lehet, ha fejlesztési vagy tesztelési környezetekre van szüksége, és gyorsan szeretné reprodukálni őket, vagy a költségmegtakarítási szabályzatok használatával felügyelheti őket.
Íme néhány forgatókönyv, amelyekkel ügyfeleink a következő célokra használják a DevTest Labs szolgáltatást:

- Egy helyen kezelheti a fejlesztési és tesztelési környezeteket. A házirendek segítségével csökkentheti a költségeket, és egyéni lemezképeket hozhat létre a csapaton belüli buildek megosztásához.
- Az alkalmazások fejlesztéséhez egyéni rendszerképeket használva mentse a lemez állapotát a fejlesztési fázisok során.
- Nyomon követheti az előrehaladással kapcsolatos költségeket.
- Tömeges tesztelési környezetek létrehozása a minőségbiztosítási teszteléshez.
- Az összetevők és a képletek használatával egyszerűen konfigurálhatja és reprodukálhatja az alkalmazásokat különböző környezetekben.
- Terjesztheti a virtuális gépeket a ötletbörzékhez (együttműködési fejlesztési vagy tesztelési munkák), majd az esemény befejeződését követően egyszerűen kiépítheti őket.

### <a name="how-am-i-billed-for-devtest-labs"></a>Mennyit kell fizetnem a DevTest Labs szolgáltatásért?
A DevTest Labs egy ingyenes szolgáltatás. A laborok létrehozása és a házirendek, sablonok és összetevők konfigurálása a DevTest Labs szolgáltatásban ingyenes. Csak a laborokban használt Azure-erőforrások, például a virtuális gépek, a Storage-fiókok és a virtuális hálózatok után kell fizetnie. További információ a labor erőforrásainak költségéről: [Azure DevTest Labs díjszabása](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Biztonság

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Melyek a DevTest Labs különböző biztonsági szintjei?
A biztonsági hozzáférés meghatározása az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján történik. A hozzáférés működésének megismeréséhez segít megismerni az engedélyek, a szerepkörök és a hatókör közötti különbséget az Azure RBAC által meghatározottak szerint.

- **Engedély**: az engedély meghatározott hozzáférés egy adott művelethez. Az engedélyek például olvasási jogosultsággal rendelkezhetnek az összes virtuális géphez.
- **Szerepkör**: a szerepkör a felhasználók számára csoportosítható és hozzárendelhető engedélyek összessége. Egy előfizetéshez tartozó tulajdonosi szerepkörrel rendelkező felhasználó például hozzáférhet az előfizetésen belüli összes erőforráshoz.
- **Hatókör**: a hatókör az Azure-erőforrások hierarchiáján belüli szint. Egy hatókör lehet például egy erőforráscsoport, egy tesztkörnyezet vagy a teljes előfizetés.

A DevTest Labs hatókörén belül két típusú szerepkört határozhat meg, amelyek a felhasználói engedélyeket definiálják:

- **Tesztkörnyezet tulajdonosa**: a labor tulajdonosa a laborban található összes erőforráshoz hozzáfér. A labor tulajdonosa módosíthatja a házirendeket, olvasást és írást bármely virtuális gépre, megváltoztathatja a virtuális hálózatot, és így tovább.
- **Labor felhasználó**: A labor felhasználó megtekintheti az összes laboratóriumi erőforrást, például virtuális gépeket, házirendeket és virtuális hálózatokat. A tesztkörnyezet felhasználói azonban nem módosíthatják a házirendeket vagy a más felhasználók által létrehozott virtuális gépeket.

Létrehozhat egyéni szerepköröket is a DevTest Labs szolgáltatásban. Ha szeretné megtudni, hogyan hozhat létre egyéni szerepköröket a DevTest Labs szolgáltatásban, tekintse meg a [felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)című részt.

Mivel a hatókörök hierarchikusak, amikor egy felhasználó egy bizonyos hatókörön belül rendelkezik engedélyekkel, a felhasználó automatikusan megkapja ezeket az engedélyeket a hatókör minden alacsonyabb szintű hatókörében. Ha például egy felhasználó az előfizetés tulajdonosa szerepkörhöz van rendelve, a felhasználó hozzáférhet az előfizetéshez tartozó összes erőforráshoz. Ilyen erőforrások például a virtuális gépek, a virtuális hálózatok és a laborok. Az előfizetés tulajdonosa automatikusan örökli a tesztkörnyezet tulajdonosának szerepkörét. Az ellenkezője azonban nem igaz. A labor tulajdonosa egy laborhoz fér hozzá, amely az előfizetési szintnél alacsonyabb hatókörű. Így a tesztkörnyezet tulajdonosai nem láthatják a laboron kívüli virtuális gépeket, virtuális hálózatokat és egyéb erőforrásokat.

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hogyan az Azure szerepköralapú hozzáférés-vezérlésének meghatározása a saját DevTest Labs-környezetek számára annak biztosításához, hogy a fejlesztők és a tesztek hogyan tudják elvégezni a munkájukat?
Széles minta van, azonban a részletek a szervezettől függenek.

A központi informatikai részlegnek csak a szükséges mértékeket kell használnia, és lehetővé kell tennie a projekt és az alkalmazás csapatának, hogy rendelkezzen a szükséges szintű szabályozással. Ez általában azt jelenti, hogy a központi informatikai részleg az előfizetés tulajdonosa, és az informatikai funkciók, például a hálózati konfigurációk kezelése. Az előfizetéshez tartozó **tulajdonosok** készletének kicsinek kell lennie. Ezek a tulajdonosok további tulajdonosokat is megadhatnak, amikor szükség van rá, vagy előfizetési szintű szabályzatokat alkalmaznak, például "nincs nyilvános IP".

Előfordulhat, hogy a felhasználók olyan részhalmaza van, amelynek hozzáférésre van szüksége egy előfizetéshez, például a Tier1 vagy a 2. szint támogatásához. Ebben az esetben javasoljuk, hogy ezeknek a felhasználóknak a **közreműködők** számára biztosítson hozzáférést, hogy tudják kezelni az erőforrásokat, de nem biztosítanak felhasználói hozzáférést vagy szabályzatok módosítását.

A DevTest Labs-erőforrásnak olyan tulajdonosnak kell lennie, aki a projekt/alkalmazás csapathoz közeledik. Ennek oka, hogy megértik a gépekre és a szükséges szoftverekre vonatkozó követelményeket. A legtöbb szervezetben a DevTest Labs-erőforrás tulajdonosa általában a projekt/fejlesztési vezető. Ez a tulajdonos felügyelheti a felhasználókat és a házirendeket a tesztkörnyezet környezetében, és kezelheti az összes virtuális gépet a DevTest Labs-környezetben.

A Project/Application csapat tagjait fel kell venni a **DevTest Labs felhasználói** szerepkörbe. Ezek a felhasználók virtuális gépeket hozhatnak létre (a labor és az előfizetési szintű szabályzatok segítségével). Saját virtuális gépeket is kezelhetnek. Nem kezelhetik más felhasználókhoz tartozó virtuális gépeket.

További információkért lásd: [Azure Enterprise állvány – előfizetési útmutató irányítási dokumentációja](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hogyan hozzon létre egy szerepkört, amely lehetővé teszi a felhasználók számára egy adott feladat elvégzését?
Az egyéni szerepkörök létrehozásával és az engedélyek szerepkörhöz való hozzárendelésével kapcsolatos átfogó cikkért lásd: [felhasználói engedélyek megadása adott tesztkörnyezet-házirendekhez](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Íme egy példa egy olyan parancsfájlra, amely létrehozza a **DevTest Labs speciális felhasználót**, amely jogosult a laborban lévő összes virtuális gép elindítására és leállítására:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hogyan gondoskodhat a szervezet a vállalati biztonsági házirendekről?
A szervezetek a következő műveletek végrehajtásával érhetik el:

- Átfogó biztonsági szabályzat fejlesztése és közzététele. A szabályzat a szoftverekkel, a Felhőbeli objektumokkal kapcsolatos elfogadható használati szabályokat fogalmazza meg. Azt is meghatározza, hogy milyen egyértelműen sértse meg a szabályzatot.
- Egyéni rendszerkép, egyéni összetevők és központi telepítési folyamat fejlesztése, amely az Active Directoryval definiált biztonsági tartományon belüli előkészítést teszi lehetővé. Ez a megközelítés kikényszeríti a vállalati határt, és beállítja a környezeti vezérlőelemek közös készletét. Ezeket a környezeteket a fejlesztők úgy tekinthetik meg, mint a fejlesztésük és a biztonságos fejlesztési életciklusuk során, a teljes folyamat részeként. A cél az, hogy olyan környezetet biztosítson, amely nem túlságosan korlátozó, ami hátráltathatja a fejlesztést, de a vezérlőelemek egy ésszerű halmaza is. A labor virtuális gépeket tartalmazó szervezeti egység (OU) csoportházirendje az éles környezetben található összes csoportházirend részhalmaza lehet. Azt is megteheti, hogy egy további készletet is tartalmaz, hogy megfelelően enyhítse az azonosított kockázatokat.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hogyan biztosítja a szervezet az adatok integritását annak biztosítására, hogy a távelérési szolgáltatás nem tudja eltávolítani a programkódot vagy kártevő vagy nem jóváhagyott szoftvereket bevezetni?
A DevTest Labs szolgáltatásban való együttműködéshez számos különböző vezérlési réteg áll rendelkezésre a külső tanácsadók, alvállalkozók vagy olyan alkalmazottak fenyegetésének enyhítésére.

Ahogy azt korábban említettük, az első lépésnek rendelkeznie kell egy, a szabályzatnak megfelelő, meghatározott használati szabályzattal, amely világosan ismerteti a következményeket, amikor valaki sérti a házirendet.

A távelérési vezérlők első rétege egy távelérési házirend alkalmazása egy olyan VPN-kapcsolaton keresztül, amely nem kapcsolódik közvetlenül a laborhoz.

A vezérlőelemek második rétege olyan csoportházirend-objektumok készletét alkalmazza, amelyek megakadályozzák a másolást és beillesztést a távoli asztalon. Egy hálózati házirendet úgy lehetne megvalósítani, hogy ne engedélyezze a kimenő szolgáltatásokat a környezetből, például az FTP-és az RDP-szolgáltatásokat. A felhasználó által megadott útválasztás az összes Azure-beli hálózati forgalmat visszakényszerítheti a helyszíni környezetbe, de az Útválasztás nem tudott minden olyan URL-cím esetében, amely lehetővé teszi az adatok feltöltését, kivéve, ha a tartalom és a munkamenetek ellenőrzéséhez proxyn keresztül vezérli A nyilvános IP-címeket a DevTest Labs szolgáltatást támogató virtuális hálózaton korlátozni lehet, hogy ne engedélyezzék egy külső hálózati erőforrás áthidalóját.

Végső soron ugyanazokat a korlátozásokat kell alkalmazni a szervezeten belül, amely a cserélhető adathordozók és a külső URL-címek összes lehetséges módszerét figyelembe veszi, amelyek elfogadják a tartalom közzétételét. A biztonsági szabályzat áttekintéséhez és megvalósításához forduljon a biztonsági szakemberekhez. További javaslatok: [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hogyan labort létrehozni egy Resource Manager-sablonból?
Olyan GitHub-tárházat is kínálunk a [labor Azure Resource Manager sablonokhoz](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) , amelyet telepítheti a-ként, vagy módosíthatja egyéni sablonok létrehozására a laborokhoz. Minden sablon tartalmaz egy hivatkozást a labor üzembe helyezéséhez, amely a saját Azure-előfizetésében van. Vagy testreszabhatja a sablont, és [telepítheti a PowerShell vagy az Azure CLI használatával](../azure-resource-manager/templates/deploy-powershell.md)is.


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Lehet-e minden virtuális gépet létrehozni egy közös erőforráscsoporthoz ahelyett, hogy a saját erőforráscsoporthoz tartozó mindegyik gép?
Igen, a labor tulajdonosaként engedélyezheti a labornak az erőforráscsoport lefoglalását vagy az Ön által megadott közös erőforráscsoporthoz létrehozott összes virtuális gépet.

Különálló erőforráscsoport-forgatókönyv:
-   A DevTest Labs új erőforráscsoportot hoz létre minden nyilvános/magánhálózati IP virtuális géphez, amelyet felvesz
-   A DevTest Labs létrehoz egy erőforráscsoportot a megosztott IP-gépekhez, amelyek ugyanahhoz a mérethez tartoznak.

Általános erőforráscsoport-forgatókönyv:
-   Az összes virtuális gép megpördült a megadott általános erőforráscsoport szerint. További információ [az erőforráscsoport kiosztásáról a laborban](./resource-group-control.md).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hogyan fenntartani az DevTest Labs-környezet elnevezési konvencióját?
Érdemes lehet kiterjeszteni az aktuális vállalati elnevezési konvenciókat az Azure-műveletekre, és az DevTest Labs-környezetben is konzisztensen kell lennie. A DevTest Labs telepítésekor javasoljuk, hogy konkrét kezdő házirendekkel rendelkezzen. Ezeket a házirendeket központi parancsfájlokkal és JSON-sablonokkal helyezheti üzembe a konzisztencia kikényszerítása érdekében. Az elnevezési házirendek az előfizetési szinten alkalmazott Azure-szabályzatok segítségével valósíthatók meg. A Azure Policy JSON-minták esetében lásd: [Azure Policy minták](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hány labort hozhatok létre ugyanahhoz az előfizetéshez?
Az előfizetések alapján létrehozható laborok száma nem rendelkezik konkrét korlátozással. Az előfizetések által felhasznált erőforrások mennyisége azonban korlátozott. Az [Azure-előfizetések korlátaival és kvótákkal](../azure-resource-manager/management/azure-subscription-service-limits.md) , valamint a [korlátok növelésével](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)kapcsolatos tudnivalókat itt olvashatja.


### <a name="how-many-vms-can-i-create-per-lab"></a>Hány virtuális gépet hozhatok létre laborban?
A laborok által létrehozható virtuális gépek száma nem rendelkezik konkrét korlátozással. A felhasznált erőforrások (virtuálisgép-magok, nyilvános IP-címek stb.) azonban előfizetésre korlátozódnak. Az [Azure-előfizetések korlátaival és kvótákkal](../azure-resource-manager/management/azure-subscription-service-limits.md) , valamint a [korlátok növelésével](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)kapcsolatos tudnivalókat itt olvashatja.

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hogyan meghatározni a felhasználók és a laboratóriumok arányát, valamint a szervezeten belül szükséges laborok teljes számát?
Azt javasoljuk, hogy ugyanahhoz a fejlesztési projekthez tartozó üzleti egységek és fejlesztési csoportok ugyanahhoz a laborhoz legyenek társítva. Lehetővé teszi, hogy ugyanazokat a szabályzatokat, lemezképeket és leállítási házirendeket alkalmazza mindkét csoportra.

Előfordulhat, hogy a földrajzi határokat is figyelembe kell vennie. Az észak-keleti Egyesült Államok (Egyesült Államok) fejlesztői például egy Kelet-RÉGIÓJA kiépített labort használhatnak. A Dallasban, Texasban és Denverben lévő fejlesztők pedig az USA déli középső régiójában lévő erőforrások használatára irányíthatók. Ha egy külső harmadik féllel együttműködésen kívüli erőfeszítések vannak, akkor azokat olyan laborhoz lehet rendelni, amelyet a belső fejlesztők nem használnak.

Azure DevOps Projectson belül egy adott projekthez is használhat labort. Ezután egy megadott Azure Active Directory csoporton keresztül alkalmazza a biztonságot, amely lehetővé teszi az erőforrások mindkét készletének elérését. A laborhoz rendelt virtuális hálózat lehet egy másik határ, amellyel összevonhatja a felhasználókat.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hogyan lehet megakadályozni az erőforrások törlését a laboron belül?
Javasoljuk, hogy a megfelelő engedélyeket a labor szintjén állítsa be úgy, hogy csak a jogosult felhasználók törölhetnek erőforrásokat, vagy módosíthatják a labor-házirendeket. A fejlesztőket a **DevTest Labs-felhasználók** csoportba kell helyezni. A vezető fejlesztőnek vagy az infrastruktúra-érdeklődőnek a **DevTest Labs-tulajdonosnak** kell lennie. Javasoljuk, hogy csak két labor tulajdonosa legyen. Ez a szabályzat a sérülés elkerülése érdekében kiterjed a kód tárházára. A labor felhasználói jogosultak erőforrásokat használni, de nem frissíthetik a labor-házirendeket. Tekintse meg a következő cikket, amely felsorolja azokat a szerepköröket és jogosultságokat, amelyekkel az egyes beépített csoportok egy laborban találhatók: a [tulajdonosok és a felhasználók hozzáadása a Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hogyan megosztanak egy közvetlen hivatkozást a laborba?

1. A [Azure Portal](https://portal.azure.com)lépjen a laborba.
2. Másolja a **labor URL-címét** a böngészőjéből, majd ossza meg a labor felhasználóival.

> [!NOTE]
> Ha a tesztkörnyezet felhasználója olyan külső felhasználó, aki Microsoft-fiók rendelkezik, de nem tagja a szervezet Active Directory példányának, a felhasználó hibaüzenetet kap, amikor megpróbálnak hozzáférni a megosztott hivatkozáshoz. Ha egy külső felhasználó hibaüzenetet jelenít meg, kérje meg a felhasználót, hogy válassza ki a nevét a Azure Portal jobb felső sarkában. Ezután a menü címtár szakaszában a felhasználó kiválaszthatja azt a könyvtárat, ahol a labor létezik.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Miért nem látom a virtuális gépeket a DevTest Labs szolgáltatásban megjelenő Virtual Machines oldalon?
Amikor létrehoz egy virtuális gépet a DevTest Labs szolgáltatásban, engedélyt kap a virtuális géphez való hozzáférésre. A virtuális gépet a Labs lapon és a **Virtual Machines** oldalon is megtekintheti. A **DevTest Labs tulajdonosi** szerepkörhöz hozzárendelt felhasználók a labor **összes Virtual Machines** oldalán a laborban létrehozott összes virtuális gépet megtekinthetik. Az **DevTest Labs felhasználói** szerepkörrel rendelkező felhasználók azonban nem kapnak automatikusan olvasási hozzáférést a többi felhasználó által létrehozott virtuálisgép-erőforrásokhoz. Így ezek a virtuális gépek nem jelennek meg a **Virtual Machines** oldalon.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hogyan egyszerre több virtuális gépet létrehozni ugyanabból a sablonból?
Két lehetőség közül választhat egyszerre több virtuális gép létrehozásához ugyanabból a sablonból:

- Használhatja az [Azure DevOps Tasks bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Létrehozhat [egy Resource Manager-sablont](devtest-lab-add-vm.md#save-azure-resource-manager-template) , amikor létrehoz egy virtuális gépet, és [üzembe helyezi a Resource Manager-sablont a Windows powershellből](../azure-resource-manager/templates/deploy-powershell.md).
- Azt is megadhatja, hogy a virtuális gép létrehozása során a gép legfeljebb egy példánya legyen létrehozva. Ha többet szeretne megtudni a virtuális gépek több példányának létrehozásáról, tekintse meg a [labor virtuális gép létrehozásáról](devtest-lab-add-vm.md)szóló dokumentációt.

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hogyan a meglévő Azure-beli virtuális gépeket a DevTest Labs laborba?
Meglévő virtuális gépek másolása a DevTest Labs szolgáltatásba:

1.  Másolja a meglévő virtuális gép VHD-fájlját egy [Windows PowerShell-parancsfájl](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)használatával.
2.  Hozza létre az [Egyéni rendszerképet](devtest-lab-create-template.md) a DevTest Labs laborban.
3.  Hozzon létre egy virtuális gépet a laborban az egyéni rendszerképből.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Több lemezt is Csatolhatok a virtuális gépekhez?

Igen, több lemezt is csatolhat a virtuális gépekhez.

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>A DevTest Labs támogatja a 2. generációs képeket?
Igen. A DevTest Labs szolgáltatás a [2. generációs rendszerképeket](../virtual-machines/generation-2.md)támogatja. Ha azonban az 1. és a 2. generációs verziók egyaránt elérhetők egy képhez, a DevTest Labs csak a rendszerkép 1. generációs verzióját jeleníti meg a virtuális gép létrehozásakor. Ekkor megjelenik a rendszerkép, ha csak a 2. generációs verziója érhető el. 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Ha Windows operációsrendszer-rendszerképet kívánok használni a teszteléshez, meg kell vásárolni egy MSDN-előfizetést?
Ha a Windows ügyfél operációsrendszer-lemezképeit (Windows 7 vagy újabb verziót) szeretné használni a fejlesztéshez vagy a teszteléshez az Azure-ban, hajtsa végre a következő lépések egyikét:

- [Vásároljon MSDN-előfizetést](https://www.visualstudio.com/products/how-to-buy-vs).
- Ha Nagyvállalati Szerződés rendelkezik, hozzon létre egy Azure-előfizetést a [Enterprise dev/test ajánlattal](https://azure.microsoft.com/offers/ms-azr-0148p).

Az egyes MSDN-ajánlatokhoz tartozó Azure-kreditekről további információt a havi Azure-kreditek [a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)számára című témakörben talál.


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hogyan automatizálni a laborban lévő összes virtuális gép törlésének folyamatát?
A labor tulajdonosaként a Azure Portalon törölheti a virtuális gépeket a laborból. A laborban lévő összes virtuális gépet egy PowerShell-szkript használatával is törölheti. A következő példában a Megjegyzés **módosítása** elemnél módosítsa a paraméterek értékeit. A `subscriptionId` , a és az `labResourceGroup` értékeket a `labName` Azure Portal Lab paneljéről kérheti le.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Környezetek

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hogyan használhatom a Resource Manager-sablonokat a DevTest Labs-környezetben?
A Resource Manager-sablonokat egy DevTest Labs-környezetbe helyezheti üzembe a [DevTest Labs környezetek funkciójában](devtest-lab-test-env.md) ismertetett lépések segítségével. Alapvetően a Resource Manager-sablonokat egy git-tárházba (Azure Repos vagy GitHub) tekintheti meg, és hozzáadhat egy [privát tárházat a sablonokhoz](devtest-lab-test-env.md) a laborban. Ez a forgatókönyv nem lehet hasznos, ha DevTest Labs-t használ a fejlesztői gépek üzemeltetésére, de hasznos lehet, ha olyan átmeneti környezetet épít, amely az éles környezetre jellemző.

Azt is érdemes megjegyezni, hogy a virtuális gépek száma a laborban vagy felhasználónként beállítás csak a laborban natív módon létrehozott gépek számát korlátozza, nem pedig semmilyen környezet (Resource Manager-sablonok) számára.

## <a name="custom-images"></a>Egyéni rendszerképek

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hogyan állíthatok be egy könnyen ismételhető folyamatot az egyéni szervezeti lemezképek DevTest Labs-környezetben való üzembe helyezéséhez?
Tekintse [meg ezt a videót a rendszerkép-előállító mintán](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ez a forgatókönyv egy speciális forgatókönyv, és a megadott parancsfájlok csak példaként használható parancsfájlok. Ha bármilyen módosításra van szükség, felügyelni és karbantartani kell a környezetben használt parancsfájlokat.

A rendszerkép-előállító létrehozásával kapcsolatos részletes információkért lásd: [Egyéni rendszerkép-előállító létrehozása Azure DevTest Labsban](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Mi a különbség az Egyéni rendszerképek és a képletek között?
Az egyéni rendszerkép felügyelt rendszerkép. A képlet olyan rendszerkép, amelyet további beállításokkal is konfigurálhat, majd mentheti és reprodukálhatja. Érdemes lehet egyéni rendszerképet használni, ha gyorsan szeretne létrehozni több környezetet ugyanazzal az alapszintű, megváltoztathatatlan képpel. A képletek jobbak lehetnek, ha a virtuális gép konfigurációját a legújabb BITS szolgáltatással szeretné reprodukálni egy virtuális hálózat vagy alhálózat részeként, vagy egy adott méretű virtuális gép esetén. Részletesebb magyarázatért tekintse meg az [Egyéni rendszerképek és képletek összehasonlítása a DevTest Labs-ben](devtest-lab-comparing-vm-base-image-types.md)című témakört.

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Mikor érdemes képletet vagy egyéni rendszerképet használni?
Az ebben a forgatókönyvben szereplő döntési tényező általában a költségek és az újrafelhasználás. Ha van olyan forgatókönyv, amelyben sok felhasználó/labor igényel egy olyan rendszerképet, amely sok szoftvert tartalmaz az alaprendszerképhez, akkor az egyéni rendszerkép létrehozásával csökkentheti a költségeket. Ez azt jelenti, hogy a rendszerkép egyszer jön létre. Ez csökkenti a virtuális gép telepítési idejét és a telepítéskor futó virtuális gép miatt felmerülő költségeket.

A szoftvercsomag változásainak gyakorisága azonban egy további Megjegyzés. Ha napi buildeket futtat, és megköveteli, hogy a szoftver a felhasználók virtuális gépei legyenek, használjon egy képletet egyéni rendszerkép helyett.

Részletesebb magyarázatért tekintse meg az [Egyéni rendszerképek és képletek összehasonlítása](devtest-lab-comparing-vm-base-image-types.md) a DevTest Labs-ben című témakört.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hogyan automatizálni a VHD-fájlok feltöltésének folyamatát egyéni lemezképek létrehozásához?

A VHD-fájlok egyéni lemezképek létrehozására való feltöltésének automatizálásához két lehetőség közül választhat:

- A [AzCopy](../storage/common/storage-use-azcopy-v10.md) használatával másolhatja vagy feltöltheti a virtuális merevlemez-fájlokat a laborhoz társított Storage-fiókba.
- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használata. A Storage Explorer egy önálló alkalmazás, amely Windows, OS X és Linux rendszeren fut.

A laborhoz társított cél Storage-fiók megkeresése:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
3.  Keresse meg és válassza ki a laborhoz társított erőforráscsoportot.
4.  Az **Áttekintés** területen válassza ki a Storage-fiókok egyikét.
5.  Válassza a **Blobok** lehetőséget.
6.  Keresse meg a listában a feltöltéseket. Ha nincs ilyen, térjen vissza a 4. lépéshez, és próbálkozzon egy másik Storage-fiókkal.
7.  Az **URL-címet** használja célként a AzCopy parancsban.

Mikor érdemes a saját egyéni szervezeti rendszerképét használni az Azure Marketplace-en?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Mikor érdemes a saját egyéni szervezeti rendszerképét használni az Azure Marketplace-en?
Alapértelmezés szerint az Azure Marketplace-t kell használni, hacsak nem rendelkezik konkrét problémával vagy szervezeti követelményekkel. Néhány gyakori példa a következőkre:

- Összetett Szoftvertelepítés, amely megköveteli, hogy az alkalmazás szerepeljen az alaprendszerkép részeként.
- Az alkalmazások telepítése és beállítása több órát is igénybe vehet, ami nem hatékony a számítási idő Azure Marketplace-rendszerképbe való felvételéhez.
- A fejlesztőknek és a tesztelőknek gyorsan hozzá kell férniük egy virtuális géphez, és az új virtuális gép telepítési idejét szeretnék csökkenteni.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági szabályzatok), amelyeknek az összes gépen meg kell felelnie.
- Az egyéni lemezképek használata nem tekinthető könnyelműnek. További bonyolultságot jelentenek, mivel most a VHD-fájlokat a mögöttes alaplemezképekhez kell kezelnie. Ezeket az alapképeket is rendszeresen kell frissítenie a szoftverfrissítések használatával. Ezek a frissítések tartalmazzák az új operációs rendszer (OS) frissítéseit, valamint a szoftvercsomag szükséges frissítéseit és konfigurációs módosításait.

## <a name="artifacts"></a>Artifacts

### <a name="what-are-artifacts"></a>Mik azok az összetevők?
Az összetevők olyan testreszabható elemek, amelyek segítségével telepítheti a legújabb biteket, vagy üzembe helyezheti a fejlesztői eszközöket egy virtuális gépen. A virtuális gép létrehozásakor az összetevők csatlakoztatása a virtuális géphez. A virtuális gép kiépítése után az összetevők üzembe helyezik és konfigurálja a virtuális gépet. A [nyilvános GitHub-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)számos már meglévő összetevő elérhető. [Saját](devtest-lab-artifact-author.md)összetevőket is létrehozhat.

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>A virtuális gép létrehozása nem sikerült. Hogyan a hibakeresést?
Ha szeretné megtudni, hogyan szerezhet be naplókat a sikertelen összetevőkhöz, tekintse meg a [DevTest Labs összetevő hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)című témakört.

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Mikor érdemes egy szervezet nyilvános összetevő-tárházat vagy privát összetevőt használni a DevTest Labs szolgáltatásban?
A [nyilvános összetevők tárháza](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) a leggyakrabban használt szoftvercsomagok kezdeti készletét biztosítja. Segít a gyors üzembe helyezésben anélkül, hogy időt kellene fordítania a közös fejlesztői eszközök és bővítmények ismételt létrehozására. Dönthet úgy is, hogy saját privát tárházat helyez üzembe. A nyilvános és a privát tárházat párhuzamosan is használhatja. Dönthet úgy is, hogy letiltja a nyilvános tárházat. A privát tárház üzembe helyezésének feltételeit a következő kérdések és szempontok alapján kell ellátni:

- A szervezetnek van-e követelménye a vállalati licenccel rendelkező szoftvereknek a DevTest Labs-ajánlat részeként? Ha a válasz igen, akkor létre kell hozni egy privát tárházat.
- A szervezet olyan egyéni szoftvert fejleszt, amely egy adott műveletet biztosít, amely a teljes kiépítési folyamat részeként szükséges? Ha a válasz igen, akkor a rendszer egy privát tárházat helyez üzembe.
- Ha a szervezet irányítási szabályzata elszigetelést igényel, és a külső adattárak nem a szervezet közvetlen konfigurációjának felügyelete alatt állnak, akkor központilag kell telepíteni a privát összetevő-tárházat. A folyamat részeként a nyilvános tárház kezdeti másolata átmásolható és integrálható a privát tárházba. Ezután a nyilvános tárházat le lehet tiltani, hogy a szervezeten belül senki ne férhessen hozzá. Ez a megközelítés arra kényszeríti a szervezeten belüli felhasználókat, hogy csak egyetlen, a szervezet által jóváhagyott adattárral rendelkezzenek, és a konfigurációt csökkentsék.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Érdemes egy szervezetet megtervezni egy adott tárházhoz, vagy több tárházat is engedélyezni?
A szervezet általános irányítási és konfigurációs felügyeleti stratégiájának részeként javasolt központosított tárházat használni. Több tárház használata esetén előfordulhat, hogy az idő múlásával a nem felügyelt szoftverek silói lesznek. A központi tárházban több csapat is használhat a tárházból származó összetevőket a projektekhez. Kikényszeríti a szabványosítást, a biztonságot, a könnyű kezelhetőséget, és kiküszöböli az erőfeszítések ismétlődését. A központosított környezet részeként a következő műveletek ajánlottak a hosszú távú felügyelethez és a fenntarthatósághoz:

- Társítsa az Azure Repos-t ugyanazzal a Azure Active Directory Bérlővel, amelyet az Azure-előfizetés használ a hitelesítéshez és engedélyezéshez.
- Hozzon létre egy `All DevTest Labs Developers` központilag felügyelt Azure Active Directory nevű csoportot. Az összetevő-fejlesztéshez hozzájáruló fejlesztőknek ebbe a csoportba kell tartoznia.
- Ugyanaz a Azure Active Directory csoport használható az Azure Repos adattárhoz és a laborhoz való hozzáférés biztosításához.
- Az Azure-alapú adattárakban az elágazások és az elágazások különálló, az elsődleges üzemi tárházból származó fejlesztésen alapuló tárházban használhatók. A tartalmat a rendszer csak a fő ág számára adja hozzá egy lekéréses kérelemmel a megfelelő kód felülvizsgálatát követően. Miután a kód-véleményező jóváhagyja a változást, egy vezető fejlesztő, aki felelős a fő ág fenntartásáért, egyesíti a frissített kódot.

## <a name="cicd-integration"></a>CI/CD-integráció

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrálható a DevTest Labs a CI/CD-toolchain?
Ha Azure-DevOps használ, a [DevTest Labs-feladatok bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) használatával automatizálhatja a kiadási folyamatot a DevTest Labs szolgáltatásban. A bővítménnyel elvégezhető feladatok némelyike a következőket teszi:

- Virtuális gép automatikus létrehozása és üzembe helyezése. A virtuális gépet a legújabb buildtel is beállíthatja az Azure file Copy vagy a PowerShell Azure DevOps Services-feladatok használatával.
- A tesztelést követően automatikusan rögzítheti a virtuális gép állapotát, ha további vizsgálatot szeretne végezni ugyanazon a virtuális gépen.
- Törölje a virtuális gépet a kiadási folyamat végén, ha már nincs rá szükség.

A következő blogbejegyzések útmutatást és információkat nyújtanak az Azure DevOps Services bővítmény használatáról:

- [DevTest Labs és az Azure DevOps bővítmény](integrate-environments-devops-pipeline.md)
- [Új virtuális gép üzembe helyezése meglévő DevTest Labs-laborban az Azure DevOps Servicesből](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Az Azure DevOps Services kiadási felügyeletének használata a DevTest Labs folyamatos üzembe helyezéséhez](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Más folyamatos integrációs (CI)/Continuous kézbesítési (CD) eszközlánccal esetében ugyanezeket a forgatókönyveket [Azure PowerShell-parancsmagok](../azure-resource-manager/templates/deploy-powershell.md) és [.net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)-k használatával, [Azure Resource Manager-sablonokkal](https://azure.microsoft.com/resources/templates/) is elérheti. A [DevTest Labs REST API](https://aka.ms/dtlrestapis) -jait is használhatja a toolchain való integráláshoz.

## <a name="networking"></a>Hálózatkezelés

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Mikor hozzon létre egy új virtuális hálózatot a DevTest Labs-környezethez és egy meglévő virtuális hálózat használatával?
Ha a virtuális gépeknek kapcsolatba kell lépniük a meglévő infrastruktúrával, akkor érdemes lehet egy meglévő virtuális hálózatot használni a DevTest Labs-környezetben. Ha a ExpressRoute-t használja, érdemes lehet a virtuális hálózatok/alhálózatok számának minimalizálására, hogy az előfizetésekben használt IP-címtartomány ne legyen kiosztva.

Érdemes lehet használni a virtuális hálózati társítási mintát ([sugaras modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) is. Ez a megközelítés lehetővé teszi az vnet/alhálózati kommunikációt az előfizetések között. Ellenkező esetben minden DevTest Labs-környezet rendelkezhet saját virtuális hálózattal.

Az előfizetéshez tartozó virtuális hálózatok száma [korlátozott](../azure-resource-manager/management/azure-subscription-service-limits.md) . Az alapértelmezett érték 50, de ez a korlát 100-re is kiemelhető.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Mikor érdemes megosztott IP-címet vagy nyilvános IP-címet vagy magánhálózati IP-címet használni?

Ha helyek közötti VPN-t vagy expressz útvonalat használ, érdemes lehet privát IP-címeket használni, hogy a gépek a belső hálózaton keresztül is elérhetők legyenek, és a nyilvános interneten keresztül elérhetetlenek legyenek.

> [!NOTE]
> A labor tulajdonosai megváltoztathatják ezt az alhálózati házirendet annak biztosítása érdekében, hogy a virtuális gépekhez egyetlen véletlenül se hozzon létre nyilvános IP-címet. Az előfizetés tulajdonosának létre kell hoznia egy előfizetési szabályzatot, amely megakadályozza a nyilvános IP-címek létrehozását.

Megosztott nyilvános IP-címek használata esetén a laborban lévő virtuális gépek nyilvános IP-címet használnak. Ez a megközelítés akkor lehet hasznos, ha el szeretné kerülni az adott előfizetés nyilvános IP-címeire vonatkozó korlátok megszegését.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hogyan gondoskodjon arról, hogy a fejlesztési és tesztelési virtuális gépek nem tudják elérni a nyilvános internetet? Vannak ajánlott minták a hálózati konfiguráció beállításához?

Igen. Két szempontot kell figyelembe venni – a bejövő és a kimenő forgalmat.

- **Bejövő forgalom** – ha a virtuális gépnek nincs nyilvános IP-címe, akkor nem érhető el az Internet. A közös megközelítéssel biztosítható, hogy egy előfizetési szintű házirend legyen beállítva, például hogy egyetlen felhasználó sem hozhat létre nyilvános IP-címet.
- **Kimenő forgalom** – ha meg szeretné akadályozni, hogy a virtuális gépek közvetlenül hozzáférjenek a nyilvános internethez, és kényszerítse a forgalmat a vállalati tűzfalon keresztül, akkor a helyszíni forgalmat expressz útvonalon vagy VPN-en keresztül irányíthatja kényszerített útválasztás használatával.

> [!NOTE]
> Ha olyan proxykiszolgálót tartalmaz, amely a proxybeállítások nélkül blokkolja a forgalmat, ne felejtsen el kivételeket hozzáadni a labor összetevő-tárolási fiókjához.

Hálózati biztonsági csoportokat is használhat virtuális gépekhez vagy alhálózatokhoz. Ez a lépés egy további védelmi réteget ad hozzá a forgalom engedélyezéséhez/letiltásához.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Miért nem megfelelő a meglévő virtuális hálózat mentése?
Az egyik lehetőség, hogy a virtuális hálózat neve időszakokat tartalmaz. Ha igen, próbálja meg eltávolítani az időszakokat, vagy cserélje le őket kötőjelekkel. Ezután próbálja meg újra a virtuális hálózat mentését.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Miért jelenik meg "a szülő-erőforrás nem található" hibaüzenet, amikor a virtuális gépet kiépítem a PowerShellből?
Ha az egyik erőforrás szülő egy másik erőforráshoz tartozik, a szülő erőforrásnak léteznie kell a gyermek erőforrás létrehozása előtt. Ha a szülő erőforrás nem létezik, egy **ParentResourceNotFound** üzenet jelenik meg. Ha nem ad meg függőséget a fölérendelt erőforráshoz, előfordulhat, hogy a gyermek erőforrást a szülő előtt kell telepíteni.

A virtuális gépek alárendelt erőforrások egy erőforráscsoport laborjában. Ha Resource Manager-sablonok használatával helyez üzembe virtuális gépeket a PowerShell használatával, a PowerShell-parancsfájlban megadott erőforráscsoport neve legyen a labor erőforráscsoport-neve. További információ: [gyakori Azure-telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Hol találhatok további hibaüzeneteket, ha a virtuális gép telepítése meghiúsul?
A virtuális gépek üzembe helyezésével kapcsolatos hibák rögzítése a tevékenység naplófájljaiban történik. A labor virtuális gépek tevékenységének naplói a **naplók** vagy a **virtuálisgép-diagnosztika** területen találhatók a labor virtuális gép lapjának erőforrás menüjében (a lap akkor jelenik meg, ha kiválasztja a virtuális gépet a saját virtuális gépek listájából).

Előfordulhat, hogy az üzembe helyezési hiba a virtuális gép telepítésének megkezdése előtt történik. Ilyen például, ha a virtuális géppel létrehozott erőforrás előfizetési korlátja túllépve. Ebben az esetben a rendszer rögzíti a hiba részleteit a labor szintű tevékenységek naplóiban. A tevékenységek naplói a **konfiguráció és a házirendek** beállítások alján találhatók. További információ a Tevékenységnaplók Azure-beli használatáról: a [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózása érdekében](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Miért kapok "a hely nem érhető el az erőforrástípus" hibaüzenetet, amikor egy labort próbálok létrehozni?
A labor létrehozásakor a következőhöz hasonló hibaüzenet jelenhet meg:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

A hiba elhárításához hajtsa végre az alábbi lépések egyikét:

#### <a name="option-1"></a>1\. lehetőség
Győződjön meg arról, hogy az Azure-régiókban rendelkezésre áll-e az erőforrástípus az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/) lapon. Ha az erőforrástípus nem érhető el egy adott régióban, a DevTest Labs nem támogatja a laborok létrehozását az adott régióban. A tesztkörnyezet létrehozásakor válasszon másik régiót.

#### <a name="option-2"></a>2\. lehetőség
Ha az erőforrás típusa elérhető a régióban, ellenőrizze, hogy regisztrálva van-e az előfizetésében. Az előfizetés tulajdonosi szintjén végezhető el, ahogy az [ebben a cikkben](../azure-resource-manager/management/resource-providers-and-types.md)is látható.