---
title: Oktatóanyag – A fejlesztői portál elérése és testreszabása – Azure API Management | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan szabhatja testre a API Management fejlesztői portált, amely egy automatikusan létrehozott, teljes mértékben testreszabható webhely az API-k dokumentációjának használatával.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 7c341dee3106530715248355da4412b97ed30980
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739619"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Oktatóanyag: A fejlesztői portál elérése és testreszabása

A *fejlesztői portál* egy automatikusan létrehozott, teljes mértékben testreszabható webhely, amely az API-k dokumentációját tartalmazza. Az API-felhasználók itt deríthetik fel az API-kat, megismerhetik azok használatát, és kérhetnek hozzáférést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés a fejlesztői portál felügyelt verziójához
> * Navigálás a felügyeleti felületen
> * A tartalom testreszabása
> * A módosítások közzététele
> * A közzétett portál megtekintése

További részleteket a fejlesztői portálon talál az [Azure API Management fejlesztői portál áttekintésében.](api-management-howto-developer-portal.md)

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management fejlesztői portál – rendszergazdai mód" border="false":::

## <a name="prerequisites"></a>Előfeltételek

- A következő rövid útmutató befejezése: [Azure-beli virtuális API Management létrehozása](get-started-create-service-instance.md)
- Azure-beli virtuális API Management importálása és közzététele. További információ: Importálás [és közzététel](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>A portál elérése rendszergazdaként

A portál felügyelt verziójának eléréséhez kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. Válassza a **fejlesztői portál gombot** a felső navigációs sávon. Megnyílik egy új böngészőlap a portál felügyeleti verziójával.


## <a name="developer-portal-architectural-concepts"></a>A fejlesztői portál architekturális fogalmai

A portál összetevői logikailag két kategóriába sorolhatók: *kódra és* *tartalomra.*

### <a name="code"></a>Code

A kód az API Management GitHub-adattárban van [fenntartva, és](https://github.com/Azure/api-management-developer-portal) a következőket tartalmazza:

- **Widgetek** – vizuális elemeket képviselnek, és HTML-, JavaScript-, stílusképességet, beállításokat és tartalomleképezést kombinálnak. Ilyenek például a képek, a szöveges bekezdések, az űrlap, az API-k listája stb.
- **Stílusdefiníciók** – a widgetek stílusának megadása
- **Motor** – statikus weblapokat hoz létre a portál tartalmából, és JavaScriptben van megírva
- **Vizualizációszerkesztő** – lehetővé teszi a böngészőben való testreszabást és a szerzői műveleteket

### <a name="content"></a>Tartalom

A tartalom két alkategóriára  oszlik: portáltartalmakra és API Management *tartalomra.*

*A portál tartalma* a portálra jellemző, és a következőket tartalmazza:

- **Oldalak** – például kezdőlap, API-oktatóanyagok, blogbejegyzések
- **Média** – képek, animációk és egyéb fájlalapú tartalom
- **Elrendezések** – az URL-címekkel egyező sablonok, amelyek meghatározzák az oldalak megjelenítésének mikéntjét
- **Stílusok** – a stílusdefiníciók értékei, például betűtípusok, színek, szegélyek
- **Beállítások** – olyan konfigurációk, mint a favicon vagy a webhely metaadatai

    A portál tartalma a média kivételével JSON-dokumentumokként van kifejezve.

*API Management tartalom olyan* entitásokat tartalmaz, mint az API-k, a műveletek, a termékek és az előfizetések.
## <a name="understand-the-portals-administrative-interface"></a>A portál felügyeleti felületének a használata

### <a name="default-content"></a>Alapértelmezett tartalom 

Ha először fér hozzá a portálhoz, az alapértelmezett tartalom automatikusan ki lesz építve a háttérben. Az alapértelmezett tartalom úgy lett kialakítva, hogy bemutassa a portál képességeit, és minimálisra csökkentse a portál testreszabásához szükséges testreszabásokat. A portál tartalmának tartalmáról további információt az Azure API Management fejlesztői portál [áttekintésében talál.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Vizualizációszerkesztő

A portál tartalmát a vizualizációszerkesztővel szabhatja testre. 
* A bal oldali menüszakaszokkal oldalakat, médiatartalmakat, elrendezéseket, menüket, stílusokat vagy webhelybeállításokat hozhat létre vagy módosíthat. 
* Az alsó menüelemek lehetővé teszi a nézetportok (például mobil vagy asztali) közötti váltást, a portál hitelesített vagy névtelen felhasználók számára látható elemeinek megtekintését, illetve a műveletek mentését vagy visszavonását.
* Adjon hozzá sorokat egy oldalhoz egy pluszjeles kék ikonra kattintva. 
* A widgetek (például szövegek, képek vagy API-k listája) egy pluszjelet és egy szürke ikont lenyomva adhatja hozzá.
* Rendezze át az oldal elemeit húzással. 

### <a name="layouts-and-pages"></a>Elrendezések és oldalak

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Oldalak és elrendezések" border="false":::

Az elrendezések határozzák meg az oldalak megjelenítését. Az alapértelmezett tartalomban például két elrendezés van: az egyik a kezdőlapra, a másik pedig az összes többi oldalra vonatkozik.

Az elrendezés úgy lesz alkalmazva egy oldalra, hogy az URL-sablonja megegyezik az oldal URL-címével. Például egy URL-sablonnal megadott elrendezés lesz alkalmazva minden olyan oldalra, amely az `/wiki/*` `/wiki/` URL-címben a szegmenst tartalmazza: `/wiki/getting-started` , , `/wiki/styles` stb.

Az előző képen az elrendezéshez tartozó tartalom kék, az oldal pedig pirossal van jelölve. A menü szakaszai meg vannak jelölve.

### <a name="styling-guide"></a>Stíluskalauz

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Stíluskalauz" border="false":::

A stíluskalauz egy tervezők által létrehozott panel. Lehetővé teszi a portál összes vizuális elemének felügyeletét és stílusának beállítását. A stílus hierarchikus – sok elem örököl tulajdonságokat más elemektől. A gombelemek például a szöveg és a háttér színeit használják. A gomb színének módosítása az eredeti színvariánssal változtatható meg.

Egy változat szerkesztéséhez jelölje ki azt, és válassza a felette megjelenő ceruza ikont. Miután végrehajtotta a módosításokat az előugró ablakban, zárja be.

### <a name="save-button"></a>Mentés gomb

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Mentés gomb" border="false":::

Amikor módosítja a portált, manuálisan kell mentenie  azt az alsó menü Mentés gombjára kattintva, vagy a [Ctrl]+[S] billentyűkombinációval. A módosítások mentésekor a rendszer automatikusan feltölti a módosított tartalmat a API Management szolgáltatásba.

## <a name="customize-the-portals-content"></a>A portál tartalmának testreszabása

Mielőtt elérhetővé teszi a portált a látogatók számára, személyre kell szabni az automatikusan létrehozott tartalmat. A javasolt módosítások közé tartoznak az elrendezések, a stílusok és a kezdőlap tartalma.

> [!NOTE]
> Integrációs megfontolások miatt a következő oldalak nem távolíthatók el vagy nem mozgathatók másik URL-cím alatt: `/404` , , , , `/500` `/captcha` , `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` .

### <a name="home-page"></a>Kezdőlap

Az alapértelmezett **Kezdőlap** helyőrző tartalommal van feltöltve. Eltávolíthat teljes szakaszokat, amelyek tartalmazzák ezt a tartalmat, vagy megtarthatja a szerkezetet, és egyesével módosíthatja az elemeket. Cserélje le a létrehozott szöveget és képeket a saját szövegére, és győződjön meg arról, hogy a hivatkozások a kívánt helyekre mutatnak.

### <a name="layouts"></a>Elrendezések

Cserélje le a navigációs sávban automatikusan létrehozott emblémát a saját képére.

### <a name="styling"></a>Stílus

Bár nem kell módosítania egyik stílust sem, érdemes lehet egyes elemeket módosítania. Módosítsa például az elsődleges színt úgy, hogy megfeleljen a márka színének.

### <a name="customization-example"></a>Testreszabási példa

Az alábbi videó bemutatja, hogyan szerkesztheti a portál tartalmát, szabhatja testre a webhelyet, és hogyan tehet közzé módosításokat.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> A portál közzététele

Ahhoz, hogy a portált és a legutóbbi módosításokat elérhetővé tegye a látogatók számára, közzé *kell tennie.* A portált közzéteheti a portál felügyeleti felületén vagy a Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Közzététel a felügyeleti felületről

1. A Mentés ikon kiválasztásával győződjön meg arról, hogy mentette a **módosításokat.**
1. A menü **Műveletek szakaszában** válassza a Webhely **közzététele lehetőséget.** A művelet eltarthat néhány percig.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portál közzététele" border="false":::

### <a name="publish-from-the-azure-portal"></a>Közzététel a Azure Portal

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. A bal oldali menü fejlesztői **portáljának menüjében válassza** a **Portál áttekintése lehetőséget.**
1. A Portál **áttekintése ablakban** válassza a Közzététel **lehetőséget.**

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Portál közzététele a Azure Portal":::

> [!NOTE]
> A portált a szolgáltatás konfigurációjának API Management után újra közzé kell tennie. Például tegye közzé újra a portált egy egyéni tartomány hozzárendelése, az identitásszolgáltatók frissítése, a delegálás beállítása, illetve a bejelentkezés és a termék feltételei megadása után.


## <a name="visit-the-published-portal"></a>Keresse fel a közzétett portált

Miután közzétette a portált, a felügyeleti panellel azonos URL-címen férhet hozzá, `https://contoso-api.developer.azure-api.net` például: . Tekintse meg egy külön böngésző-munkamenetben (inkognitó vagy privát böngészési mód használatával) külső látogatóként.

## <a name="apply-the-cors-policy-on-apis"></a>CORS-szabályzat alkalmazása API-kon

Ha engedélyezni szeretné, hogy a portál látogatói teszteljék az API-kat a beépített interaktív konzolon, engedélyezze a CORS -t (az eredetközi erőforrás-megosztást) az API-kon. Részletekért tekintse meg az [Azure API Management fejlesztői portállal kapcsolatos gyakori kérdéseket.](developer-portal-faq.md#cors)

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [Áttelepítés az új fejlesztői portálra](developer-portal-deprecated-migration.md) az elavult örökölt portálról.