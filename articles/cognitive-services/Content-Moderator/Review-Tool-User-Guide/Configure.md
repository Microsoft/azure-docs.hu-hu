---
title: Felülvizsgálati eszköz beállításainak konfigurálása – Content Moderator
titleSuffix: Azure Cognitive Services
description: A felülvizsgálati eszköz használatával konfigurálhatja vagy lekérheti a csapatot, a címkéket, az összekötőket, a munkafolyamatokat és a hitelesítő adatokat a Content Moderatorhoz.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912021"
---
# <a name="configure-the-review-tool"></a>A felülvizsgálati eszköz konfigurálása

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) számos fontos funkciót tartalmaz, amelyek az irányítópult **Beállítások** menüjében érhetők el.

![Content Moderator felülvizsgálati beállítások menü](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Csapat és alcsoportok kezelése

A **Team (csapat** ) lapon kezelheti azon felhasználók csoportjait és alcsoportjait &mdash; , akik értesítést kaphatnak bizonyos [emberi értékelések](../review-api.md#reviews) elindításáról. Csak egy csapattal rendelkezhet (amelyet a felülvizsgálati eszköz regisztrálásakor hoz létre), de több alcsapatot is létrehozhat. A csoport rendszergazdája meghívhatja a tagokat, megadhatja az engedélyeiket, és hozzárendelheti őket különböző alcsoportokhoz.

![Az eszköz-csoport beállításainak áttekintése](images/settings-2-team.png)

Az alcsoportok olyan eszkalációs csapatok vagy csapatok létrehozására használhatók, amelyek a tartalom adott kategóriáinak áttekintéséhez vannak hozzárendelve. Előfordulhat például, hogy a felnőtt tartalmat egy különálló csapatnak küldi el további áttekintés céljából.

Ez a szakasz azt ismerteti, hogyan hozhatók létre alcsoportok, és hogyan lehet gyorsan kiosztani az értékeléseket menet közben. A [munkafolyamatok](workflows.md) segítségével azonban meghatározott feltételek alapján rendelhet hozzá értékeléseket.

### <a name="create-a-subteam"></a>Alcsapat létrehozása

Nyissa meg az **alcsoportok** szakaszt, és kattintson az **alcsapat hozzáadása** lehetőségre. Adja meg az alcsoport nevét a párbeszédpanelen, majd kattintson a **Mentés** gombra.

![Alcsapat neve](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Csapattársak meghívása

Nem rendelhet hozzá valakit egy alcsapathoz, ha még nem tagjai az alapértelmezett csapatnak, ezért először hozzá kell adnia a véleményezőket az alapértelmezett csapathoz. Kattintson a **meghívás** elemre a **csapat** lapon.

![Felhasználók meghívása](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Csapattársak társítása az alcsapathoz

Kattintson a **tag hozzáadása** gombra az alapértelmezett csapat tagjainak egy vagy több alcsapathoz való hozzárendeléséhez. Csak meglévő felhasználókat adhat hozzá egy alcsapathoz. A felülvizsgálati eszközben nem szereplő új felhasználók hozzáadásához hívja meg őket a csapat beállításai lap "meghívás" gombjára kattintva.

![Alcsoport tagjainak kiosztása](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Értékelések társítása alcsoportokhoz

Miután létrehozta az alcsapatokat és a hozzárendelt tagokat, megkezdheti a tartalmi [felülvizsgálatok](../review-api.md#reviews) hozzárendelését az alcsoportokhoz. Ezt a webhely **Áttekintés** lapján végezheti el.
Ha tartalmat szeretne hozzárendelni egy alcsapathoz, kattintson a jobb felső sarokban található három pontra, válassza a **Áthelyezés** lehetőséget, majd válasszon egy alcsapatot.

![Rendszerkép-áttekintés társítása alcsoporthoz](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Váltás az alcsoportok között

Ha egynél több alcsapat tagja, válthat az alcsoportok között, hogy megváltoztassa az Ön számára megjelenített tartalmi értékeléseket. A **felülvizsgálat** lapon válassza az **alapértelmezett** legördülő menüt, és válassza az **alcsapat** kiválasztása lehetőséget. Megtekintheti a különböző alcsoportok tartalmi felülvizsgálatait, de csak azokat, amelyeknek Ön a tagja.

![Váltás az alcsoportok között](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Címkék

A **címkék** lapon egyéni moderálási címkéket határozhat meg a két alapértelmezett moderálási címke &mdash; **isadult** (**a**) és a **isracy** (**r**) mellett. Ha egyéni címkét hoz létre, az az alapértelmezett címkékkel együtt elérhetővé válik az értékelésekben. A láthatósági beállítások átváltásával megváltoztathatja, hogy mely címkék jelenjenek meg az értékelésekben.

![Címkék nézet, beleértve a "látható" jelölőnégyzeteket](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Egyéni címkék létrehozása

Új címke létrehozásához meg kell adnia egy rövid kódot, egy nevet és egy leírást a megfelelő mezőkben.

- **Rövid kód**: adjon meg egy kétbetűs kódot a címkéhez. Példa: **CB**
- **Név**: adjon meg egy rövid és egy leíró címkét a szóköz nélküli kisbetűs mezőben. Példa: **isbullying**.
- **Leírás**: (nem kötelező) adja meg annak a tartalomnak a leírását, amelyet a címkét céloz meg. Példa: **a számítógépes zaklatás ábrázolásai vagy példányai**.

Kattintson a **Hozzáadás** elemre egy címke hozzáadásához, majd kattintson a **Mentés** gombra, amikor elkészült a címkék létrehozásával.

![Az eszköz új címke létrehozása párbeszédpanelének áttekintése](images/settings-3-tags.png)

### <a name="delete-tags"></a>Címkék törlése

Az egyéni címkéket úgy törölheti, ha kiválasztja a Kuka ikont a címkék listán szereplő bejegyzések mellett, de az alapértelmezett címkéket nem törölheti.

## <a name="connectors"></a>Összekötők

Az **Összekötők** lapon kezelheti az összekötőket, amelyek olyan szolgáltatás-specifikus beépülő modulok, amelyek különböző módon dolgozzák fel a tartalmakat a tartalom- [munkafolyamatok](../review-api.md#workflows)részeként.

A munkafolyamat létrehozásakor az alapértelmezett összekötő az Content Moderator-összekötő, amely a tartalmat **felnőttként** vagy **zamatként** megjelölheti, megkeresheti a káromkodást és így tovább. Használhat azonban más összekötőket is, amelyeket itt láthat, ha rendelkezik a megfelelő szolgáltatásaihoz tartozó hitelesítő adatokkal (például [az arc-](../../face/overview.md) összekötő használatára).

A [felülvizsgálati eszköz](./human-in-the-loop.md) a következő összekötőket tartalmazza:

- Érzelem
- Face
- PhotoDNA Cloud Service
- Text Analytics

### <a name="add-a-connector"></a>Összekötő hozzáadása

Ha hozzá szeretne adni egy összekötőt (és elérhetővé teszi a tartalom- [munkafolyamatokban](../review-api.md#workflows)való használatra), válassza a megfelelő **Csatlakozás** gombot. A következő párbeszédpanelen adja meg az előfizetési kulcsot a szolgáltatáshoz. Ha elkészült, az új összekötőnek az oldal tetején kell megjelennie.

![Content Moderator összekötők beállításai](images/settings-4-connectors.png)

## <a name="workflows"></a>Munkafolyamatok

A **munkafolyamatok lapon** kezelheti a [munkafolyamatokat](../review-api.md#workflows). A munkafolyamatok felhőalapú szűrők a tartalomhoz, és összekötők segítségével rendezik a tartalmakat különböző módokon, és megtehetik a megfelelő műveleteket. Itt megadhatja, szerkesztheti és tesztelheti a munkafolyamatokat. További útmutatásért lásd: a [munkafolyamatok meghatározása és használata](Workflows.md) .

![Munkafolyamat-beállítások Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Hitelesítő adatok

A **hitelesítő adatok** lap gyors hozzáférést biztosít a Content moderator-előfizetési kulcshoz, amelyhez a moderálási szolgáltatásokhoz való hozzáféréshez a REST-hívások vagy az ügyféloldali SDK-nak szüksége lesz.

![Hitelesítő adatok Content Moderator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Külső hitelesítő adatok használata munkafolyamatokhoz

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) létrehoz egy ingyenes próbaverziót az Azure Content moderator Services szolgáltatásban a regisztráció során, de azt is beállíthatja, hogy egy meglévő kulcsot használjon Azure-fiókjából. Ez nagy léptékű forgatókönyvek esetén ajánlott, mivel az ingyenes próbaverziós kulcsok szigorú használati korlátokkal rendelkeznek ([díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Ha létrehozott egy Content Moderator- [erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) az Azure-ban, navigáljon a Azure Portal, és válassza a **kulcsok** panelt. Másolja ki az egyik kulcsot.

![Content Moderator kulcsok a Azure Portal](images/credentials-azure-portal-keys.PNG)

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) **hitelesítő adatai** lapon lépjen a **munkafolyamat beállításai** ablaktáblára, válassza a **Szerkesztés** lehetőséget, és illessze be a kulcsot a **OCP-APIM-Subscription-Key** mezőbe. A moderálási API-kat meghívó munkafolyamatok mostantól az Azure-beli hitelesítő adatait fogják használni.

> [!NOTE]
> A **munkafolyamat-beállítások** ablaktáblán a másik két mező az egyéni és a Képlista. Ezek megismeréséhez tekintse meg az [Egyéni feltételek](../try-terms-list-api.md) vagy az [Egyéni lemezképek](../try-image-list-api.md) útmutatóit.

### <a name="use-your-azure-account-with-the-review-apis"></a>Azure-fiók használata a felülvizsgálati API-kkal

Ahhoz, hogy az Azure-kulcsot a felülvizsgálati API-kkal használhassa, le kell kérnie az erőforrás-azonosítót. Nyissa meg a Content Moderator erőforrást a Azure Portal, és válassza a **Tulajdonságok panelt** . Másolja ki az erőforrás-azonosító értékét, és illessze be a felülvizsgálati eszköz **hitelesítő adatai** lap engedélyezett **erőforrás-azonosító (k)** mezőjébe.

![Content Moderator erőforrás-azonosító a Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Ha mindkét helyen megadta az előfizetési kulcsot, a felülvizsgálati eszköz fiókjához tartozó próbaverzió nem lesz használatban, de továbbra is elérhető marad.

## <a name="next-steps"></a>Következő lépések

Kövesse a [felülvizsgálati](../quick-start.md) eszköz rövid útmutatóját a felülvizsgálati eszköz a tartalom moderálási forgatókönyvekben való használatának megkezdéséhez.