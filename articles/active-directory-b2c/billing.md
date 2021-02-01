---
title: Számlázási modell Azure Active Directory B2C
description: Ismerje meg az Azure AD B2C's havi aktív felhasználók (MAU) számlázási modelljét, hogyan kapcsolhatja össze az Azure AD B2C bérlőt egy Azure-előfizetéssel, és hogyan választhatja ki a megfelelő prémium szintű díjszabást.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225208"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Számlázási modell Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) díjszabása a havi aktív felhasználók (MAU) alapján történik, amely a naptári hónapon belül a hitelesítési tevékenységgel rendelkező egyedi felhasználók száma. Ez a számlázási modell a Azure AD B2C bérlőre és az [Azure ad vendég felhasználói együttműködésre (B2B)](../active-directory/external-identities/external-identities-pricing.md)is vonatkozik. A MAU számlázási szolgáltatással csökkentheti a költségeket, és az ingyenes szintet, valamint a rugalmas és kiszámítható díjszabást kínál. Ebből a cikkből megismerheti a MAU-számlázást, összekapcsolhatja Azure AD B2C bérlőit egy előfizetéssel, és módosíthatja az árképzési szintet.

> [!IMPORTANT]
> Ez a cikk nem tartalmazza a díjszabás részleteit. A használati számlázással és a díjszabással kapcsolatos legfrissebb információkért tekintse meg a [Azure Active Directory B2C díjszabását](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Tekintse meg az [Azure ad B2C régió rendelkezésre állását és az adattárolási adatokat](data-residency.md) , amelyekből megtudhatja, hol érhetők el a Azure ad B2C szolgáltatás, és hol tárolódnak a felhasználói adatok.

## <a name="what-do-i-need-to-do"></a>Mit kell tennem?

A MAU-számlázás kihasználása érdekében a Azure AD B2C bérlőt egy Azure-előfizetéshez kell kapcsolni. Előfordulhat, hogy a Azure AD B2C bérlőt egy másik díjszabási szintre kell váltania, ha olyan Azure AD B2C Premium P2 funkciókat szeretne használni, mint például a kockázatalapú feltételes hozzáférés.

|Ha a bérlő:  |A következőket kell tennie:  |
|---------|---------|
| Egy Azure AD B2C bérlőt már kiszámláztak a MAU alapján     | Nem kell tenni semmit. Amikor a felhasználók hitelesítik magukat a Azure AD B2C bérlőn, a rendszer automatikusan felszámolja a MAU-alapú számlázási modell használatával.        |
| Egy Azure AD B2C bérlő még nincs előfizetéshez csatolva     |  [Csatolja Azure ad B2C bérlőjét egy előfizetéshez](#link-an-azure-ad-b2c-tenant-to-a-subscription) a Mau-számlázás aktiválásához.     |
| Egy Azure AD B2C bérlő, amely a 2019. november 1. előtti előfizetéshez lett csatolva    | [Váltson a Mau-számlázásra (ajánlott)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants), vagy maradjon a hitelesítési számlázási modellen.     |
| Egy Azure AD B2C bérlő, és prémium szintű szolgáltatásokat kíván használni (például kockázatalapú feltételes hozzáférés)    | [Váltson át egy Azure ad díjszabási](#change-your-azure-ad-pricing-tier) csomagra, amely támogatja a használni kívánt funkciókat.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Tudnivalók a havi aktív felhasználók (MAU) számlázási modelljéről

A MAU-számlázás a Azure AD B2C bérlők számára a **2019. november 1-jén** lép érvénybe. Minden olyan Azure AD B2C bérlőt, amelyet az adott napon vagy azt követően előfizetéshez kapcsolódóan hozott létre, és amelyekhez kapcsolódott. Ha olyan Azure AD B2C Bérlővel rendelkezik, amely nincs előfizetéshez társítva, ezt most kell megtennie. Ha olyan meglévő Azure AD B2C Bérlővel rendelkezik, amely a 2019. november 1. előtti előfizetéshez van csatolva, javasoljuk, hogy frissítsen a havi aktív felhasználók (MAU) számlázási modelljére, vagy maradjon a hitelesítési számlázási modellen.
  
A Azure AD B2C bérlőt a használni kívánt funkciók alapján a megfelelő Azure-díjszabási csomaghoz is csatolni kell. A prémium szintű funkciókhoz Azure AD B2C [Premium P1 vagy P2 díjszabás](https://azure.microsoft.com/pricing/details/active-directory-b2c/)szükséges. Előfordulhat, hogy az új szolgáltatások használatakor frissítenie kell az árképzési szintet. A feltételes hozzáféréshez például ki kell választania a bérlőhöz Azure AD B2C Premium P2 díjszabási szintet.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C bérlő összekapcsolása egy előfizetéssel

Azure Active Directory B2C (Azure AD B2C) használati díjait egy Azure-előfizetésre számoljuk fel. Explicit módon össze kell kapcsolni egy Azure AD B2C-bérlőt egy Azure-előfizetéshez egy Azure AD B2C- *erőforrás* létrehozásával a cél Azure-előfizetésen belül. Több Azure AD B2C erőforrás is létrehozható egyetlen Azure-előfizetésben, valamint más Azure-erőforrásokkal, például a virtuális gépekkel, a Storage-fiókokkal és a Logic Appsokkal. Az előfizetésen belüli összes erőforrást megtekintheti az előfizetéshez társított Azure Active Directory (Azure AD) bérlőhöz.

Azure AD B2C bérlőhöz kapcsolódó előfizetés használható Azure AD B2C használat vagy más Azure-erőforrások, többek között a további Azure AD B2C erőforrások számlázására is. Nem használható további Azure-licenc-alapú szolgáltatások vagy Office 365-licencek hozzáadására a Azure AD B2C bérlőn belül.

### <a name="prerequisites"></a>Előfeltételek

* [Azure-előfizetés](https://azure.microsoft.com/free/)
* Az előfizetéshez csatolni kívánt [Azure ad B2C bérlő](tutorial-create-tenant.md)
  * Bérlői rendszergazdának kell lennie
  * A bérlőnek még nincs előfizetéshez csatolva

### <a name="create-the-link"></a>A hivatkozás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a használni kívánt Azure-előfizetést tartalmazza (*nem* pedig a Azure ad B2C bérlőt tartalmazó könyvtár).
3. Válassza az **erőforrás létrehozása** lehetőséget, írja be a `Active Directory B2C` **Keresés a piactéren** mezőbe, majd válassza a **Azure Active Directory B2C** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.
5. Válassza a **meglévő Azure ad B2C bérlő összekapcsolása az Azure-előfizetéssel** lehetőséget.
6. Válasszon ki egy **Azure ad B2C bérlőt** a legördülő listából. Csak azok a bérlők jelennek meg, amelyekhez Ön globális rendszergazda, és amelyek még nem kapcsolódnak előfizetéshez. A **Azure ad B2C erőforrás neve** mező a kiválasztott Azure ad B2C-bérlő tartománynevével van feltöltve.
7. Válasszon ki egy aktív Azure- **előfizetést** , amelynek Ön a rendszergazdája.
8. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, majd adja meg az **erőforráscsoport helyét**. Az erőforráscsoport beállításai nincsenek hatással a Azure AD B2C bérlői helyére, teljesítményére vagy számlázási állapotára.
9. Válassza a **Létrehozás** lehetőséget.

    ![A Azure Portal Azure AD B2C erőforrás-létrehozási lapja](./media/billing/portal-01-create-b2c-resource-page.png)

Miután elvégezte ezeket a lépéseket egy Azure AD B2C bérlő számára, az Azure-előfizetését az Azure Direct vagy Nagyvállalati Szerződés adatai alapján számítjuk fel, ha vannak ilyenek.

## <a name="change-your-azure-ad-pricing-tier"></a>Az Azure AD díjszabási szintjeinek módosítása

A bérlőt a Azure AD B2C Bérlővel használni kívánt funkciók alapján kell összekapcsolni a megfelelő Azure-díjszabási csomaggal. A prémium szintű szolgáltatásokhoz Azure AD B2C Premium P1 vagy P2 szükséges, a [Azure Active Directory B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/)szerint. Bizonyos esetekben az új funkciók használatakor frissítenie kell az árképzési szintet. Ha például az Identity Protection, a kockázatalapú feltételes hozzáférés és a Azure AD B2C-vel kapcsolatos jövőbeni prémium P2-képességek használatát szeretné használni, ki kell választania a bérlőhöz tartozó Azure AD B2C Premium P2 díjszabási szintet.

Az árképzési szintek módosításához kövesse az alábbi lépéseket.

1. Jelentkezzen be az Azure portálra.

2. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely az Azure B2C-bérlőhöz kapcsolódó Azure-előfizetést tartalmazza (*ne* válassza ki a Azure ad B2C bérlőt).

3. A portál felső részén található keresőmezőbe írja be a Azure AD B2C bérlő nevét. Ezután válassza ki a bérlőt a keresési eredmények között az **erőforrások** területen.

4. Az erőforrás- **Áttekintés** oldalon az **árképzési** csomag területen válassza a **módosítás** elemet.

   ![A tarifacsomag módosítása](media/billing/change-pricing-tier.png)
 
5. Válassza ki az árképzési szintet, amely tartalmazza az engedélyezni kívánt szolgáltatásokat.

   ![Válassza ki a díjszabási szintet](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Váltás a MAU-számlázásra (a 2019-es előAzure AD B2C bérlők előtt)

Ha a Azure AD B2C-bérlőt a **2019. november 1.** előtt csatlakoztatta egy előfizetéshez, az előző hitelesítési számlázási modellt használja. Javasoljuk, hogy frissítsen a havi aktív felhasználók (MAU) számlázási modelljére. A számlázási beállítások a Azure AD B2C erőforrásban konfigurálhatók.

A havi aktív felhasználók (MAU) számlázására való váltás **visszafordíthatatlan**. Ha egy Azure AD B2C erőforrást a MAU-alapú számlázási modellre konvertál, az erőforrást nem lehet visszaállítani a hitelesítési számlázási modellre.

A következőkben megtudhatja, hogyan válthat egy meglévő Azure AD B2C erőforrást a MAU-számlázásra:

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com) az előfizetés tulajdonosaként rendszergazdai hozzáféréssel a Azure ad B2C erőforráshoz.

2. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a Azure ad B2C könyvtárat, amelyet Mau-számlázásra szeretne frissíteni.<br/>

    ![Címtár-és előfizetés-szűrő a Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.

4. A Azure AD B2C bérlő **Áttekintés** lapján kattintson az **Erőforrás neve** alatt található hivatkozásra. A rendszer átirányítja az Azure AD-bérlő Azure AD B2C erőforrására.<br/>

    ![Azure AD B2C az erőforrás-hivatkozás ki van emelve a Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Az Azure AD B2C erőforrás **áttekintő** lapján a **számlázható egységek** területen válassza ki a **hitelesítést (a Mau-re váltás)** hivatkozásra.<br/>

    ![A Azure Portal Kiemelt MAU-hivatkozásra vált](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Válassza a **megerősítés** lehetőséget a frissítés a Mau-számlázásra való befejezéséhez.<br/>

    ![MAU-alapú számlázási megerősítő párbeszédpanel Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Mit kell várni a MAU-számlázásra való áttéréskor a hitelesítési számlázástól

A MAU-alapú mérés az előfizetés/erőforrás tulajdonosaként azonnal engedélyezve van, erősítse meg a változást. A havi számla a változás és a MAU új egységeinek változása után számlázza a hitelesítési egységeket.

A felhasználók nem számítanak fel kétszer az átmeneti hónap során. A módosítást megelőzően hitelesítő egyedi aktív felhasználóknak egy naptári hónapban kell fizetniük a hitelesítési díjak alapján. Ugyanezek a felhasználók nem szerepelnek a MAU-számításban az előfizetés számlázási ciklusának hátralévő részében. Például:

* A contoso B2C-bérlő 1 000 felhasználóval rendelkezik. 250 a felhasználók egy adott hónapban aktívak. Az előfizetés rendszergazdája a hónap 10. napján megváltoztathatja a hitelesítést a havi aktív felhasználók (MAU) esetében.
* Az 1 – 10. számlázási díj számlázása az egyes hitelesítési modell alapján történik.
  * Ha 100 felhasználó jelentkezik be ebben az időszakban (1 – 10.), ezek *a felhasználók a hónapra kifizetettként* vannak megjelölve.
* A 10 (az áttérés tényleges időpontjában) számlázása a MAU-díj alapján történik.
  * Ha egy további 150 felhasználó jelentkezik be ebben az időszakban (10 – 30.), akkor csak a további 150 lesz kiszámlázva.
  * Az első 100 felhasználó folyamatos tevékenysége nem érinti a naptári hónap hátralévő részének számlázását.

Az áttérés számlázási időszaka során az előfizetés tulajdonosa valószínűleg mindkét módszer (hitelesítés és/MAU) bejegyzéseinek megjelenítése az Azure-előfizetés számlázási utasításában jelenik meg:

* Egy bejegyzés a használathoz, amely a módosítás dátumát és időpontját tükrözi.
* A havi aktív felhasználókat (MAU) tükröző módosítás utáni használat bejegyzése.

A Azure AD B2C használati számlázásával és díjszabásával kapcsolatos legfrissebb információkért tekintse meg a [Azure Active Directory B2C díjszabását](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C bérlői erőforrások kezelése

Miután létrehozta a Azure AD B2C erőforrást egy Azure-előfizetésben, látnia kell egy "B2C-bérlő" típusú új erőforrást a többi Azure-erőforrással.

Ezt az erőforrást a következő célra használhatja:

* Keresse meg az előfizetést a számlázási adatok áttekintéséhez
* Azure AD B2C bérlő bérlői AZONOSÍTÓjának beolvasása GUID formátumban
* Ugrás a Azure AD B2C-bérlőre
* Támogatási kérelem beküldése
* Azure AD B2C bérlői erőforrás áthelyezése másik Azure-előfizetésre vagy-erőforráscsoporthoz

### <a name="regional-restrictions"></a>Regionális korlátozások

Ha az előfizetésében regionális korlátozásokat hozott létre az Azure-erőforrások létrehozásához, akkor ez a korlátozás megakadályozhatja a Azure AD B2C erőforrás létrehozását.

A probléma megoldásához lazítsa meg regionális korlátozásait.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure Cloud Solution Providers (CSP) előfizetések

A Azure AD B2C támogatja az Azure Cloud Solution Providers (CSP) előfizetéseket. A funkció API-kkal vagy a Azure AD B2Choz és az összes Azure-erőforráshoz Azure Portal érhető el. A CSP-előfizetések rendszergazdái kapcsolatokat kapcsolhatnak össze, helyezhetnek el és törölhetnek más Azure-erőforrásokkal végzett Azure AD B2Cokkal.

A szerepköralapú hozzáférés-vezérlést használó Azure AD B2C felügyeletét a Azure AD B2C bérlő és az Azure CSP-előfizetés közötti társítás nem érinti. A szerepköralapú hozzáférés-vezérlés a bérlői alapú szerepkörök, nem előfizetés-alapú szerepkörök használatával érhető el.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C bérlő számlázási előfizetésének módosítása

### <a name="move-using-azure-resource-manager"></a>Áthelyezés a Azure Resource Manager használatával

Azure AD B2C bérlők áthelyezhetők egy másik előfizetésbe Azure Resource Manager ha a forrás-és a cél-előfizetések ugyanabban a Azure Active Directory bérlőn belül vannak.

Ha szeretné megismerni, hogyan helyezhetők át az Azure-erőforrások, például a Azure AD B2C bérlője egy másik előfizetésbe, tekintse meg az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Mielőtt elkezdené az áthelyezést, olvassa el a teljes cikket, hogy teljes mértékben megértsék az ilyen áthelyezésre vonatkozó korlátozásokat és követelményeket. Az erőforrások áthelyezésére vonatkozó utasítások mellett olyan kritikus információkat is tartalmaz, mint például az áthelyezés előtti ellenőrzőlista, valamint a mozgatási művelet ellenőrzése.

### <a name="move-by-unlinking-and-relinking"></a>Áthelyezés leválasztással és újrakapcsolással

Ha a forrás-és a cél-előfizetések különböző Azure Active Directory bérlőhöz vannak társítva, akkor a fentiekben leírtak szerint nem hajtható végre az áthelyezés Azure Resource Manager. Ugyanakkor továbbra is elérheti ugyanezt az eredményt azáltal, hogy leválasztja a Azure AD B2C bérlőt a forrás-előfizetésből, és újracsatlakoztatja a cél előfizetéshez. Ez a módszer biztonságos, mert az egyetlen törölt objektum a *Számlázási hivatkozás*, nem pedig a Azure ad B2C bérlő. A felhasználók, alkalmazások, felhasználói folyamatok és egyéb eszközök egyikét sem érinti a rendszer.

1. Magát a Azure AD B2C címtárban hívja meg a [vendég felhasználót](user-overview.md#guest-user) a cél Azure ad-bérlőtől (az a cél, amelyhez az Azure-előfizetés kapcsolódik), és győződjön meg arról, hogy a felhasználó rendelkezik a **globális rendszergazdai** szerepkörrel a Azure ad B2C.
1. Navigáljon a forrás Azure-előfizetés Azure AD B2Cét képviselő *Azure-erőforráshoz* , amelyet a fenti [Azure ad B2C bérlői erőforrások kezelése](#manage-your-azure-ad-b2c-tenant-resources) című szakaszban ismertetett. Ne váltson a tényleges Azure AD B2C bérlőre.
1. Kattintson a **Törlés** gombra az **Áttekintés** oldalon. Ez *nem* törli a kapcsolódó Azure ad B2C bérlő felhasználóit vagy alkalmazásait. Csupán eltávolítja a számlázási hivatkozást a forrás-előfizetésből.
1. Jelentkezzen be a Azure Portalba az 1. lépésben Azure AD B2C rendszergazdaként hozzáadott felhasználói fiókkal. Ezután navigáljon a cél Azure Active Directory bérlőhöz kapcsolódó Azure-előfizetéshez. 
1. Hozza létre újra a számlázási hivatkozást a cél előfizetésben a fenti [hivatkozás létrehozása](#create-the-link) eljárás követésével.
1. A Azure AD B2C-erőforrás már át lett helyezve a cél Azure-előfizetésre (a célként megadott Azure Active Directoryhoz csatolva), és ezt az előfizetést követően kell kiszámlázni.

## <a name="next-steps"></a>Következő lépések

A legfrissebb díjszabási információkért lásd: [Azure Active Directory B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/).