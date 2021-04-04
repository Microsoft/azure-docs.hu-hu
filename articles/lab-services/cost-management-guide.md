---
title: Cost Management útmutató a Azure Lab Services
description: Ismerje meg a labor-szolgáltatások költségeinek megtekintésére szolgáló különböző módszereket.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659726"
---
# <a name="cost-management-for-azure-lab-services"></a>Cost Management Azure Lab Services

Azure Lab Services esetében a Cost Management két különböző területre osztható: költségbecslés és költséghatékonyság. A tesztkörnyezet beállításakor a rendszer kiszámítja, hogy a labor kezdeti szerkezete a várt Költségvetésen belül elfér-e. A Cost Analysis általában a hónap végén fordul elő a szükséges műveletek meghatározásához a következő hónapban.

## <a name="estimate-the-lab-costs"></a>A tesztkörnyezet költségeinek becslése

Az egyes labor-irányítópultok **díja & számlázási** szakasz, amely egy durva becslést ad arra vonatkozóan, hogy a labor Milyen költségekkel jár a hónapban. A költségbecslés összegzi az óránkénti használatot a maximális számú felhasználó számára a becsült óradíj alapján. A legpontosabb becslés megszerzéséhez állítsa be a labort, beleértve az [ütemtervet](how-to-create-schedules.md)is. Az irányítópult a becsült költségeket fogja tükrözni. 

Előfordulhat, hogy ez a becslés nem jeleníti meg az összes lehetséges költséget. Néhány erőforrást nem tartalmaz:

- A sablon előkészítési díja. Ez jelentős mértékben változhat a sablon létrehozásához szükséges idő mennyiségétől függően. A sablon futtatásának díja megegyezik a labor óránkénti teljes díjával. 
- A [megosztott](how-to-use-shared-image-gallery.md) képkatalógus költségei, mivel a katalógusok több Labs között is megoszthatók. 
- A virtuális gépet (VM) elindító labor Creator órája.

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely az irányítópult becsült értékét jeleníti meg.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Az előző havi használat elemzése

A Cost Analysis az előző havi használat áttekintésére szolgál, amely segít meghatározni a laborban történt módosításokat. A korábbi költségek részletezését az [előfizetés költségeinek elemzése](../cost-management-billing/costs/quick-acm-cost-analysis.md)során találja. A Azure Portalban megadhatja az **előfizetéseket** a keresőmezőbe, majd kiválaszthatja az **előfizetések** lehetőséget. 

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely megjeleníti a keresőmezőt és az előfizetések lehetőséget.](./media/cost-management-guide/subscription-search.png)

Válassza ki az áttekinteni kívánt előfizetést.

> [!div class="mx-imgBorder"]
> ![Az előfizetés kijelölését bemutató képernyőkép.](./media/cost-management-guide/subscription-select.png)

Válassza a **Cost Analysis** elemet a bal oldali ablaktáblán **Cost Management** alatt.

> [!div class="mx-imgBorder"]
> ![A diagram előfizetési költségeit bemutató képernyőkép.](./media/cost-management-guide/subscription-cost-analysis.png)

Ez az irányítópult lehetővé teszi a részletes költségadatok használatát, beleértve a különböző fájltípusok ütemezett exportálásának lehetőségét is. További információ: [Cost Management + számlázás áttekintése](../cost-management-billing/cost-management-billing-overview.md).

A szűrés erőforrástípus alapján végezhető el. `microsoft.labservices/labaccounts`A használata csak a labor szolgáltatásokhoz kapcsolódó költségeket jeleníti meg.

## <a name="understand-the-usage"></a>A használat megismerése

Az alábbi képernyőképen egy példa a Cost Analysis szolgáltatásra.

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely egy előfizetésre vonatkozó, példaként szolgáló Cost-elemzést mutat be.](./media/cost-management-guide/cost-analysis.png)

Alapértelmezés szerint hat oszlop van: **erőforrás**, **Erőforrás típusa**, **hely**, **erőforráscsoport neve**, **címkék** és **Cost**. Az **erőforrás** oszlop tartalmazza a labor-fiók, a labor neve és a virtuális gép adatait. A labor-fiókot, a labor nevét és az alapértelmezett (második és harmadik sort) tartalmazó sorok a tesztkörnyezet díja. A használt virtuális gépeken a labor-fiókot, a labor nevét, az alapértelmezett és a virtuális gép nevét megjelenítő sorokra vonatkozó költségeket tekinthet meg. 

Ebben a példában az első és a második sor (az **aaalab/dockerlab**-val együtt) hozzáadásával a "aaalab" Lab-fiókban a "dockerlab" labor teljes költsége jelenik meg.

A képkatalógus teljes díjainak megszerzéséhez módosítsa az erőforrás típusát a következőre: `Microsoft.Compute/Galleries` . Előfordulhat, hogy egy megosztott képgyűjtemény nem jelenik meg a költségek között attól függően, hogy hol tárolja a katalógust.

> [!NOTE]
> Egy megosztott képkatalógus csatlakozik a labor-fiókhoz. Ez azt jelenti, hogy több labor is használhatja ugyanazt a rendszerképet.

## <a name="separate-the-costs"></a>A költségek elkülönítése

Egyes egyetemek a labor-fiókot és az erőforráscsoportot használták az osztályok elkülönítésének módjaként. Az egyes osztályok saját labor-fiókkal és erőforráscsoporthoz rendelkeznek. 

A Cost Analysis ablaktáblán adjon hozzá egy szűrőt az erőforráscsoport neve alapján az osztály megfelelő erőforráscsoport-nevével. Ezt követően csak az adott osztály költségei lesznek láthatók. Ez lehetővé teszi az osztályok közötti világosabb kiválasztást a költségek megtekintésekor. A Cost Analysis [ütemezett exportálás](../cost-management-billing/costs/tutorial-export-acm-data.md) funkciójának használatával letöltheti az egyes osztályok költségeit külön fájlokban.

## <a name="manage-costs"></a>Költségek kezelése

Az osztály típusától függően lehetőség van a költségek kezelésére a tanulók nélkül futó virtuális gépek példányainak csökkentése érdekében.

### <a name="automatic-shutdown-settings-for-cost-control"></a>A Cost Control automatikus leállítási beállításai

Az automatikus leállítási funkciók lehetővé teszik a virtuális gépek használati idejének elkerülését a laborokban. A következő beállításokkal a legtöbb esetben a felhasználók véletlenül hagyják el a virtuális gépeket:

> [!div class="mx-imgBorder"]
> ![A három automatikus leállítási beállítást bemutató képernyőkép.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Ezeket a beállításokat a Lab-fiók szintjén és a labor szintjén is konfigurálhatja. Ha a labor-fiók szintjén engedélyezi őket, a rendszer a labor-fiókban lévő összes laborra alkalmazza őket. Az új Lab-fiókok esetében ezek a beállítások alapértelmezés szerint be vannak kapcsolva. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint

> [!NOTE]
> Ez a beállítás csak a Windows rendszerű virtuális gépek esetében érhető el.

Ha a **felhasználók leválasztása, ha a virtuális gépek üresjárati** beállítás be van kapcsolva, a felhasználó kapcsolata megszakad a laborban lévő összes gépről, ha a Windows operációs rendszer a munkamenetet üresjáratban tartja (beleértve a sablon virtuális gépeket is). Az [inaktív Windows operációs rendszer definíciója](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) két feltételt használ: 

* Felhasználói hiányzás: nincs billentyűzet vagy egér bemenet.
* Erőforrás-felhasználás hiánya: az összes processzor és az összes lemez egy adott százalékban tétlen volt.

A felhasználók a kapcsolat bontása előtt a következőhöz hasonló üzenetet fognak látni a virtuális gépen: 

> [!div class="mx-imgBorder"]
> ![Képernyőkép arról, hogy a munkamenet üresjárati ideje túllépte az időkorlátot, és le lesz választva.](./media/cost-management-guide/idle-timer-expired.png)
 
A virtuális gép továbbra is fut, ha a felhasználó le van választva. Ha a felhasználó a bejelentkezést követően újracsatlakozik a virtuális géphez, a leválasztást megelőzően megnyitott vagy már nem mentett Windows-vagy-fájlok továbbra is elérhetők lesznek. Ebben az állapotban, mivel a virtuális gép fut, továbbra is aktívnak számít, és felmerül a díj. 
 
A kapcsolat nélküli Windows rendszerű virtuális gépek automatikus leállításához használja a **felhasználók leválasztása, ha a virtuális gépek üresjáratban vannak** , és **állítsa le a virtuális gépeket, amikor a felhasználók leválasztják** a beállításokat.

Ha például a beállításokat a következőképpen konfigurálja:
 
* **Felhasználók leválasztása, ha a virtuális gépek üresjáratban vannak**: 15 perccel az inaktív állapot észlelése után.
* **Virtuális gépek leállítása a felhasználók leválasztása** után: 5 perccel a felhasználó megszakadása után.
 
A Windows rendszerű virtuális gépek automatikusan leállnak 20 perccel azután, hogy a felhasználó abbahagyta a használatot. 
 
> [!div class="mx-imgBorder"]
> ![A virtuális gép automatikus leállítását eredményező beállítások kombinációját bemutató ábra.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Virtuális gépek automatikus leállítása a felhasználók leválasztásakor
 
A **virtuális gépek leállítása, amikor a felhasználók leválasztása** beállítás a Windows és a Linux rendszerű virtuális gépeket is támogatja. Ha ez a beállítás be van kapcsolva, az automatikus leállítás a következő esetekben fog megtörténni:
 
* Windows esetén a Távoli asztal (RDP) kapcsolat megszakad.
* Linux esetében az SSH-kapcsolat megszakad.
 
> [!NOTE]
> Csak [a Linux adott disztribúciói és verziói](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) támogatottak.
 
Megadhatja, hogy mennyi ideig várjon a virtuális gépek a felhasználó újrakapcsolódása előtt az automatikus leállítás előtt. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Az elindított virtuális gépek automatikus leállítása, de a felhasználók nem csatlakoznak
 
A laborban előfordulhat, hogy egy felhasználó elindít egy virtuális gépet, de soha nem csatlakozik hozzá. Például:
 
* A laborban lévő ütemterv minden virtuális gépet elindítja egy osztály-munkamenethez, de egyes tanulók nem jelennek meg, és nem csatlakoznak a gépekhez. 
* A felhasználó elindít egy virtuális gépet, de elfelejti a kapcsolódást. 
 
A **virtuális gépek leállítása, amikor a felhasználók nem csatlakoznak** , a rendszer ezeket az eseteket fogja megfogni, és automatikusan leállítja a virtuális gépeket. 
 
További információ a virtuális gépek leválasztásának konfigurálásáról és engedélyezéséről a következő cikkekben található:

* [Virtuális gépek automatikus leállításának beállítása labor-fiókhoz](how-to-configure-lab-accounts.md)
* [Virtuális gépek automatikus leállításának beállítása laborhoz](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Ütemezett idő és kvóta időpontja

Az [ütemezett idő](classroom-labs-concepts.md#schedules) és a [kvóta időpontjának](classroom-labs-concepts.md#quota) megismerése segít egy labor konfigurálásában, hogy jobban illeszkedjen a professzor és a tanulók igényeihez. 

Az ütemezett időpont egy beállított idő, amelyben az összes tanuló virtuális gép elindult, és elérhetők a csatlakozáshoz. A rendszer általában akkor használja az ütemezett időpontot, amikor az összes tanuló saját virtuális gépekkel rendelkezik, és a professzor utasításait a nap folyamán egy meghatározott időpontban (például órákban) követik. A hátránya, hogy az összes tanuló virtuális gép elindult, és a költségek merülnek fel, még akkor is, ha a tanuló nem jelentkezik be a virtuális gépre. 

A kvóta ideje az egyes tanulók számára a saját belátása szerint való használatra van lefoglalva, és gyakran használják a független tanulók számára. A virtuális gépek addig nem indulnak el, amíg a tanuló nem indítja el a virtuális gépet. 

A labor a kvóta vagy az ütemezett időpontot is használhatja, vagy a kettő kombinációját. Ha egy osztálynak nincs szüksége ütemezett időpontra, akkor csak a virtuális gépek leghatékonyabb használatára vonatkozó kvóta-időt használja.

### <a name="scheduled-event-stop-only"></a>Ütemezett esemény: csak leállítás

Az ütemtervben hozzáadhat egy leállítási esemény típusát, amely egy adott időpontban leállítja az összes gépet. Egyes laborok tulajdonosai csak leállítási eseményt állítottak be éjfélkor, hogy csökkentsék a költségeket és a kvóták használatát, amikor egy tanuló elfelejti leállítani a használt virtuális gépet. Az ilyen típusú események hátránya, hogy az összes virtuális gép le lesz állítva, még akkor is, ha a tanuló virtuális gépet használ.

### <a name="other-costs-related-to-labs"></a>A laborokkal kapcsolatos egyéb költségek 

Bizonyos költségek nem állnak a labor szolgáltatásba, de a labor szolgáltatáshoz is köthetők. Egy megosztott képtárat egy laborhoz is összekapcsolhat, de nem jelenik meg a labor Services költségei között, és költségeket is tartalmaz. Az általános költségek csökkentése érdekében távolítsa el a katalógusban fel nem használt lemezképeket, mivel a lemezképek belső tárolási költséggel rendelkeznek. 

A laborok más Azure-erőforrásokhoz is csatlakozhatnak egy virtuális hálózaton keresztül. Ha eltávolít egy labort, távolítsa el a virtuális hálózatot és a többi erőforrást.

## <a name="conclusion"></a>Összegzés

A cikkben szereplő információk remélhetőleg jobban megértették az eszközöket, amelyek segítségével csökkentheti a használati költségeket.