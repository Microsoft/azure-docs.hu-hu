---
title: Oktatóanyag – Költségek kezelése a Cloudynnel az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan kezelheti a költségeket költséglefoglalási, költséghelyi visszacsatolási és költséghelyi elszámolási jelentésekkel.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: a492f611ed5a785ae148894baf3f24c2df773df2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474577"
---
# <a name="tutorial-manage-costs-by-using-cloudyn"></a>Oktatóanyag: Költségek kezelése a Cloudyn használatával

A Cloudynben a költségeket címkék alapján foglalhatja le a költségek kezelése és a visszacsatolási jelentések előállítása során. A költséglefoglalási folyamat során költségeket rendel hozzá a felhasznált felhőalapú erőforrásokhoz. A költségek teljes lefoglalása akkor történik meg, ha az összes erőforrást kategóriákba sorolta címkék segítségével. A költségek lefoglalását követően irányítópultokon és jelentéseken keresztül biztosíthat költséghelyi visszacsatolást és elszámolást a felhasználóknak. Azonban a Cloudyn használatba vételekor előfordulhat, hogy számos erőforrás nem rendelkezik vagy nem látható el címkével.

Például előfordulhat, hogy szeretné megtéríttetni a műszaki tervezési költségeket. Be kell tudnia mutatni a műszaki csapat számára az adott igényelt összeget az erőforrásköltségek alapján. Mutathat nekik egy jelentést, amely a *műszaki tervezés* címkével ellátott összes felhasznált erőforrást tartalmazza.

A cikkben a címkéket és a kategóriákat helyenként azonos értelemben használjuk. A kategóriák tág gyűjtemények, melyek sok mindent jelölhetnek. Ezek lehetnek üzleti egységek, költséghelyek, webszolgáltatások vagy bármely felcímkézett dolog. A címkék olyan név/érték párok, amelyek segítségével kategóriákba sorolhatja az erőforrásokat, és ha ugyanazt a címkét több erőforrásra és erőforráscsoportra is alkalmazza, ezzel együtt jelenítheti meg és kezelheti azok összesített számlázási adatait. Az Azure Portal korábbi verzióiban a *címkenév* helyett a *kulcs* kifejezést alkalmaztuk. A címkék egy adott Azure-előfizetésre vonatkozóan hozhatók létre és abban tárolhatók. Az AWS címkéi kulcs/érték párok. Mivel az Azure és az AWS is a *kulcs* kifejezést használta, a Cloudyn is megmaradt ennél. A Category Manager kulcsok (címkenevek) használatával egyesíti a címkéket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségek lefoglalása egyéni címkék használatával.
> * Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-fiókkal.
- Rendelkeznie kell a Cloudyn próbaverziójával vagy fizetett előfizetésével.
- [A nem aktivált fiókokat aktiválni kell](activate-subs-accounts.md) a Cloudyn portálon.
- A [vendégszintű monitorozást](azure-vm-extended-metrics.md) engedélyezni kell a virtuális gépeken.


## <a name="use-custom-tags-to-allocate-costs"></a>Költségek lefoglalása egyéni címkék használatával

A Cloudyn az erőforráscsoportok címkeadatait az Azure-ból kéri le, és automatikusan propagálja a címkeinformációkat az erőforrásokra. A költséglefoglalásokban a költségek erőforráscímke szerint tekinthetők meg.

A Költséglefoglalási modell használatával kategóriákat (címkéket) definiálhat, amelyeket a rendszeren belül a kategorizálatlan (címkézetlen) erőforrásokra alkalmazva csoportokba rendezheti a költségeket, és szabályokat határozhat meg a címkézetlen költségek kezelésére. A költséglefoglalási szabályok olyan mentett utasítások, amelyek egy szolgáltatás költségeit egy másik szolgáltatásra osztják le. Ezt követően a létrehozott modellt kiválasztva ezek az erőforrások megjelenítik a címkéket/kategóriákat a *költséglefoglalási* jelentésekben.

Vegye figyelembe, hogy a címkeinformációk nem jelennek meg ezeknél az erőforrásoknál a *költségelemzési* jelentésekben. Emellett a Cloudynben a költséglefoglalás használatával alkalmazott címkék nem lesznek továbbítva az Azure-ba, így nem láthatja azokat az Azure Portalon.

A költséglefoglalás első lépéseként a hatókört kell definiálni egy költségmodell segítségével. A költségmodell nem módosítja a költségeket, csak leosztja azokat. A költségmodell létrehozásakor az adatokat költségentitás, fiók vagy előfizetés, valamint több címke alapján szegmentálja. A címkék általában lehetnek számlázási kódok, költséghelyek vagy csoportnevek. A címkék emellett abban is segítenek, hogy költséghelyi visszacsatolást és elszámolást végezzen a vállalat egyéb részeire vonatkozóan.

Egyéni költséglefoglalási modell létrehozásához válassza a **Költségek** &gt; **Költségkezelés** &gt; **Cost Allocation 360°** lehetőséget a jelentés menüjében.

![Példa az irányítópultra ahol kiválaszthatja a Cost Allocation 360-at](./media/tutorial-manage-costs/cost-allocation-360.png)

A **Cost Allocation 360** lapon kattintson a **Hozzáadás** elemre, majd adja meg a költségmodell nevét és leírását. Válassza ki az összes fiókot vagy válasszon egy egyénit. Egyéni fiókok kiválasztása esetén egyszerre több fiókot is választhat akár különböző felhőszolgáltatóktól is. Ezután kattintson a **Kategorizálás** gombra a költségadatok kategorizálásához használt felderített címkék kiválasztásához. Válassza ki a modellbe felvenni kívánt címkéket (kategóriákat). A következő példában az **Egység** címke van kiválasztva.

![Példa a költségmodellek kategorizálására](./media/tutorial-manage-costs/cost-model01.png)

A példában látható, hogy 19 680 dollár nincs kategorizálva (nem rendelkezik címkével).

Ezután kattintson a **Kategorizálatlan erőforrások** gombra, és válassza ki a szolgáltatásokat, amelyekhez le nem lefoglalt költségek tartoznak. Ezután definiáljon szabályokat a költségek lefoglalásához.

Például előfordulhat, hogy az Azure Storage költségeit szeretné egyenletesen elosztani az Azure-beli virtuális gépek (VM-ek) között. Ehhez válassza az **Azure/Storage** szolgáltatást, **A kategorizáltakkal arányosan**, majd az **Azure/VM** lehetőséget. Ezután kattintson a **Létrehozás** elemre.

![Példa az egyenlő leosztáshoz létrehozott költségmodell-lefoglalási szabályra](./media/tutorial-manage-costs/cost-model02.png)



Egy másik példa kedvéért tegyük fel, hogy az Azure-hálózattal kapcsolatos összes költséget a vállalat egy adott üzleti egységéhez szeretné rendelni. Ehhez válassza az **Azure/Hálózat** szolgáltatást, majd a **Lefoglalási szabály definiálása** alatt a **Kifejezett leosztás** lehetőséget. Ezután állítsa a leosztás százalékos arányát 100-ra, és válassza ki az üzleti egységet – ez a **G&amp;A** a következő képen:

![Példa egy adott üzleti egységhez létrehozott költségmodell-lefoglalási szabályra](./media/tutorial-manage-costs/cost-model03.png)



Hozzon létre további lefoglalási szabályokat a többi kategorizálatlan erőforráshoz is.

Ha ki nem osztott Amazon Web Services (AWS) lefoglalt példányokkal rendelkezik, a **Lefoglalt példányok** lehetőséggel címkézett kategóriákhoz rendelheti azokat.

A költségek lefoglalásával kapcsolatos választásainak áttekintéséhez kattintson az **Összefoglalás** gombra. Ha mentené az adatokat, és később térne vissza a további szabályok létrehozásához, kattintson a **Mentése vázlatként** gombra. Választhatja a **Mentés és aktiválás** lehetőséget is az adatok mentéséhez. Ekkor a Cloudyn megkezdi a költséglefoglalási modell feldolgozását.

A költségmodellek listájában az új költségmodell **Feldolgozás alatt** állapotúként jelenik meg. Beletelhet némi időbe, mire a Cloudyn adatbázisa frissül a költségmodelljével. A feldolgozás végeztével a modell **Kész** állapotúra vált. Ezt követően a költségmodell adatai a Költségelemzés jelentésben tekinthetők meg a **Bővített szűrők** &gt; **Költségmodell** menüpont alatt.

### <a name="category-manager"></a>Kategóriakezelő

A Kategóriakezelő egy adattisztító eszköz, amellyel több kategória (címke) értékeinek egyesítésével új kategóriák hozhatóak létre. Ez egy egyszerű szabályalapú eszköz, amelyben egy kategóriát kiválasztva szabályokat hozhat létre a meglévő értékek egyesítésére. Például rendelkezhet az **R&amp;D** és a **dev** meglévő kategóriákkal, ahol mindkettő a fejlesztői csapatot jelöli.

A Cloudyn portálon kattintson a fogaskerék ikonra a jobb felső sarokban, és válassza a **Kategóriakezelő** elemet. Új kategória létrehozásához kattintson a plusz ( **+** ) jelre. Adja meg a kategória nevét, majd a **Kulcsok** mezőben adja meg a kategóriakulcsokat, amelyeket fel szeretne venni az új kategóriába.

A szabályok definiálásakor több értéket is hozzáadhat az OR (VAGY) feltétel használatával. Emellett néhány alapvető sztringműveletet is végrehajthat. Mindkét esetben kattintson a három pontra ( **…** ) a **Szabály** elem jobb oldalán.

Új szabály definiálásához a **Szabályok** területen hozza létre az új szabályt. Például adja meg a **dev** elemet a **Szabályok** alatt, majd az **R&amp;D** elemet a **Műveletek** alatt. Miután végzett, mentse az új kategóriát.

Az alábbi képen a **Work-Load** elnevezésű új kategóriához létrehozott szabályokat bemutató példa látható.

![Példa az új work-load kategóriára](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Címkék forrásai és jelentések

A Cloudyn jelentésekben szereplő címkeadatok három helyről származhatnak:

- A felhőszolgáltatók erőforrásainak API-jai
- A felhőszolgáltatók számlázási API-jai
- A következő forrásokból származó manuálisan létrehozott címkék:
    - Cloudyn-entitáscímkék – a Cloudyn-entitásokra alkalmazott felhasználói metaadatok
    - Kategóriakezelő – a meglévő címkékre alkalmazott szabályok alapján új címkéket létrehozó adattisztító eszköz

A felhőszolgáltatói címkék a Cloudyn-költségjelentésekben való megjelenítéséhez egyéni költséglefoglalási modellt kell létrehoznia a Cost Allocation 360 használatával. Ehhez nyissa meg a **Költségek** > **Költségkezelés** > **Cost Allocation 360** eszközt, válassza ki a kívánt címkéket, majd adja meg a szabályokat a címkézetlen költségek kezeléséhez. Ezután hozzon létre egy új költségmodellt. Ezt követően a Költséglefoglalási elemzésben elérhető jelentésekben megtekintheti, szűrheti és rendezheti az Azure-erőforráscímkéket.

Az Azure-erőforráscímkék kizárólag a **Költségek** > **Költséglefoglalási elemzés** jelentésekben jelennek meg.

A felhőszolgáltatói számlázási címkék az összes költségjelentésben megjelennek.

A Cloudyn-entitáscímkék és a manuálisan létrehozott címkék az összes költségjelentésben megjelennek.


## <a name="create-showback-and-chargeback-reports"></a>Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása

Az egyes vállalatok egészen különböző módszereket alkalmazhatnak a költséghelyi visszacsatolások és a költséghelyi elszámolások készítéséhez. Azonban a Cloudyn portál bármely irányítópultja és jelentése használható mindkét célra. A vállalat bármely tagja számára adhat felhasználói hozzáférést, így igény szerint megtekinthetik az irányítópultokat és jelentéseket. A visszacsatolást az összes Költségelemzési jelentés támogatja, mivel megmutatják a felhasználók számára, hogy mely erőforrásokat használták fel. Emellett lehetővé teszik, hogy a felhasználók a saját vállalati egységükhöz tartozó költség- és használati adatokat részleteikben is megtekintsék.

A költséglefoglalási eredmények megtekintéséhez nyissa meg a Költségelemzési jelentést, és válassza ki a létrehozott költségmodellt. Ezután hozzon létre csoportosítást a költségmodellben kiválasztott egy vagy több címke alapján.

![Költségelemzési jelentés az új költségből származó példaadattal](./media/tutorial-manage-costs/cost-analysis.png)

Egyszerűen hozhat létre és menthet az adott csoportok által használt adott szolgáltatásokra összpontosító jelentéseket. Például elképzelhető, hogy az egyik részleg jelentős mértékben használ Azure-beli virtuális gépeket. Létrehozhat egy, az Azure-beli virtuális gépekre szűrt jelentést a használat mértékének és a költségeknek a megjelenítéséhez.

Amennyiben szeretné a csapatokat a pillanatnyi állapotokat mutató jelentésekkel ellátni, a jelentéseket exportálhatja PDF- vagy CSV-formátumba.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségek lefoglalása egyéni címkék használatával.
> * Költséghelyi visszacsatolási és költséghelyi elszámolási jelentések létrehozása.



Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan szabályozható az adatokhoz való hozzáférés.

> [!div class="nextstepaction"]
> [Az adatok hozzáférésének szabályozása](tutorial-user-access.md)
