---
title: Oktatóanyag – a fejlesztői portál elérése és testreszabása – Azure API Management | Microsoft Docs
description: Kövesse ezt az oktatóanyagot, amelyből megtudhatja, hogyan szabhatja testre a API Management fejlesztői portált, amely egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012977"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Oktatóanyag: a fejlesztői portál elérése és testreszabása

A *fejlesztői portál* egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, és hogyan kérhetnek hozzáférést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fejlesztői portál felügyelt verziójának elérése
> * Navigáljon a felügyeleti felületén
> * A tartalom testreszabása
> * A módosítások közzététele
> * A közzétett portál megtekintése

A fejlesztői portálról az [Azure API Management fejlesztői portál áttekintésében](api-management-howto-developer-portal.md)talál további információt.

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management fejlesztői portál – rendszergazdai mód" border="false":::

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Azure API Management-példány importálása és közzététele. További információ: [Importálás és közzététel](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>A portál elérése rendszergazdaként

Kövesse az alábbi lépéseket a portál felügyelt verziójának eléréséhez.

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Kattintson a **fejlesztői portál** gombra a felső navigációs sávon. Megnyílik a portál felügyeleti verzióját tartalmazó új böngésző lap.

## <a name="understand-the-portals-administrative-interface"></a>A portál felügyeleti felületének megismerése

### <a name="default-content"></a>Alapértelmezett tartalom 

Ha első alkalommal éri el a portált, a rendszer automatikusan kiépíti az alapértelmezett tartalmat a háttérben. Az alapértelmezett tartalom úgy lett kialakítva, hogy bemutassa a portál képességeit, és csökkentse a portál személyre szabásához szükséges testreszabásokat. Ha többet szeretne megtudni arról, hogy mit tartalmaz a portál tartalma az [Azure API Management fejlesztői portálon – áttekintés](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Vizuális szerkesztő

Testreszabhatja a portál tartalmát a vizualizáció szerkesztővel. 
* A bal oldali menüben a lapok, a média, az elrendezések, a menük, a stílusok és a webhely beállításai hozhatók létre vagy módosíthatók. 
* Az alsó menüelemek lehetővé teszik a munkaterületek (például a mobil vagy az asztali) közötti váltást, megtekintheti a portál elemeit a hitelesített vagy névtelen felhasználók számára, vagy mentheti vagy visszavonhatja a műveleteket.
* Sorok hozzáadása egy laphoz egy pluszjelet tartalmazó kék ikonra kattintva. 
* A widgetek (például szöveg, képek vagy API-k) hozzáadhatók egy pluszjelet tartalmazó szürke ikon megnyomásával.
* Egy oldalon lévő elemek átrendezése a fogd és vidd interakcióval. 

### <a name="layouts-and-pages"></a>Elrendezések és lapok

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Lapok és elrendezések" border="false":::

Az elrendezések határozzák meg a lapok megjelenítésének módját. Az alapértelmezett tartalomban például két elrendezés van: az egyik a kezdőlapra, a másik pedig az összes többi oldalra vonatkozik.

A rendszer egy elrendezést alkalmaz egy lapra úgy, hogy az URL-sablonját a lap URL-címéhez társítja. Például egy URL-sablonnal rendelkező elrendezés lesz `/wiki/*` alkalmazva az `/wiki/` URL-cím szegmensét tartalmazó összes oldalra: `/wiki/getting-started` , `/wiki/styles` stb.

Az előző képen az elrendezéshez tartozó tartalom kék színnel van megjelölve, míg a lap piros színnel van megjelölve. A menüpontok vannak megjelölve.

### <a name="styling-guide"></a>Útmutató a stílushoz

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Útmutató a stílushoz" border="false":::

A Styling útmutató tervezőkkel létrehozott panel. Lehetővé teszi a portál összes vizualizációs elemének áttekintését és formázását. A stílus hierarchikus – számos elem örökli a tulajdonságokat a többi elemtől. A gomb elemei például a szöveg és a háttér színét használják. A gomb színének módosításához módosítania kell az eredeti szín változatát.

Egy változat szerkesztéséhez jelölje ki azt, és válassza ki a tetején megjelenő ceruza ikont. Miután elvégezte a módosításokat az előugró ablakban, zárjuk le.

### <a name="save-button"></a>Mentés gomb

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Mentés gomb" border="false":::

Amikor változást hajt végre a portálon, manuálisan kell mentenie a **Save (Mentés** ) gombra a menü alján, vagy a [Ctrl] + [S] billentyűkombinációt. A módosítások mentésekor a rendszer automatikusan feltölti a módosított tartalmat a API Management szolgáltatásba.

## <a name="customize-the-portals-content"></a>A portál tartalmának testreszabása

Ahhoz, hogy a portál elérhető legyen a látogatók számára, testre kell szabnia az automatikusan generált tartalmat. Az ajánlott módosítások közé tartozik a Kezdőlap elrendezései, stílusa és tartalma.

> [!NOTE]
> Az integrációs megfontolások miatt a következő lapok nem távolíthatók el és nem helyezhetők át másik URL-cím alá:,,,,,,,,,,, `/404` `/500` `/captcha` `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` , `/signup` .

### <a name="home-page"></a>Kezdőlap

Az alapértelmezett **Kezdőlap** helyőrző tartalommal van kitöltve. Eltávolíthatja a tartalmat tartalmazó teljes szakaszt, vagy megtarthatja a struktúrát, és egyenként is módosíthatja az elemeket. Cserélje le a generált szöveget és képeket a saját adataira, és győződjön meg arról, hogy a hivatkozások a kívánt helyekre mutatnak.

### <a name="layouts"></a>Elrendezések

Cserélje le az automatikusan generált emblémát a navigációs sávon a saját képére.

### <a name="styling"></a>Stílus

Bár nem kell módosítania a stílusokat, érdemes lehet módosítani bizonyos elemeket. Például módosítsa az elsődleges színt úgy, hogy az megfeleljen a márka színének.

### <a name="customization-example"></a>Testreszabási példa

A következő videóban bemutatjuk, hogyan szerkesztheti a portál tartalmát, testreszabhatja a webhely megjelenését, és közzéteheti a módosításokat.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> A portál közzététele

Ahhoz, hogy a portál és a legújabb módosításai elérhetők legyenek a látogatók számára, *közzé* kell tennie. A portált a portál felügyeleti felületén vagy a Azure Portal teheti közzé.

### <a name="publish-from-the-administrative-interface"></a>Közzététel a felügyeleti felületen

1. A **Mentés** ikon kiválasztásával ellenőrizze, hogy mentette-e a módosításokat.
1. A menü **műveletek** területén válassza a **Közzététel webhely** lehetőséget. A művelet eltarthat néhány percig.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portál közzététele" border="false":::

### <a name="publish-from-the-azure-portal"></a>Közzététel a Azure Portal

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. A bal oldali menüben a **fejlesztői portál** területen válassza a **portál áttekintés** elemet.
1. A **portál áttekintés** ablakában válassza a **Közzététel** lehetőséget.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Portál közzététele Azure Portal":::

> [!NOTE]
> A portált újra közzé kell tenni API Management szolgáltatás konfigurációjának módosítása után. Tegyük fel például, hogy újból közzéteszi a portált egy egyéni tartomány kiosztása, az identitás-szolgáltatók frissítése, a delegálás beállítása vagy a bejelentkezés és a termékek megadása után.


## <a name="visit-the-published-portal"></a>A közzétett portál meglátogatása

A portál közzététele után a felügyeleti panellel megegyező URL-címen férhet hozzá, például: `https://contoso-api.developer.azure-api.net` . Megtekintheti egy különálló böngésző-munkamenetben (inkognitóban vagy privát böngészési mód használatával) külső látogatóként.

## <a name="apply-the-cors-policy-on-apis"></a>A CORS szabályzat alkalmazása az API-kra

Annak érdekében, hogy a portál látogatói a beépített interaktív konzolon tesztelik az API-kat, engedélyezze a CORS (több eredetű erőforrás-megosztás) az API-kon. Részletekért tekintse meg az [Azure API Management fejlesztői portál áttekintését](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [Migrálás az új fejlesztői portálra](developer-portal-deprecated-migration.md) az elavult örökölt portálról.