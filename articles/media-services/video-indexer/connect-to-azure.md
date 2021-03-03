---
title: Az Azure-hoz csatlakoztatott Video Indexer-fiók létrehozása
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan hozhat létre az Azure-hoz csatlakoztatott Video Indexer fiókot.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: fe6be5778997f0ef4a3f53ae45d17352eca60d8e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727260"
---
# <a name="create-a-video-indexer-account"></a>Video Indexer fiók létrehozása

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőséggel létrehoz egy Video Indexer fiókot, amely az Azure-előfizetéshez csatlakozik. Az indexelt percek megfizetése után további információért lásd a [Media Services díjszabását](https://azure.microsoft.com/pricing/details/media-services/).

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-előfizetéshez és egy Azure Media Services-fiókhoz kapcsolódó Video Indexer fiókot. A témakör lépéseit követve csatlakozhat az Azure-hoz az automatikus (alapértelmezett) folyamat használatával. Azt is bemutatja, hogyan csatlakozhat az Azure-hoz manuálisan (speciális).

Ha *próbaverzióról* *fizetett* video Indexer fiókra vált, az összes videó és modell testreszabását átmásolhatja az új fiókba, ahogy azt a [tartalom importálása a próbaverziós fiókból](#import-your-content-from-the-trial-account) című szakaszban tárgyaljuk.

A cikk a [video Indexer-fiók Azure Governmenthoz való összekapcsolását](#video-indexer-in-azure-government)is ismerteti.

## <a name="prerequisites-for-connecting-to-azure"></a>Az Azure-hoz való csatlakozás előfeltételei

* Azure-előfizetés.

    Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure Active Directory (Azure AD) tartomány.

    Ha nem rendelkezik Azure AD-tartománnyal, hozza létre ezt a tartományt az Azure-előfizetésével. További információ: [Egyéni tartománynevek kezelése az Azure ad-ben](../../active-directory/enterprise-users/domains-manage.md)
* Egy felhasználó az Azure AD-tartományban egy **alkalmazás-rendszergazdai** szerepkörrel. Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    A felhasználónak munkahelyi vagy iskolai fiókkal rendelkező Azure AD-felhasználónak kell lennie. Ne használjon személyes fiókot (például outlook.com, live.com vagy hotmail.com).

    ![minden Azure AD-felhasználó](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Az automatikus folyamat további előfeltételei

* Egy felhasználó és egy tag az Azure AD-tartományban.

    Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    Ennek a felhasználónak az Azure-előfizetésében egy **tulajdonosi** szerepkörrel, vagy a **közreműködő** és a **felhasználói hozzáférés rendszergazdai** szerepköreivel rendelkező tagnak kell lennie. A felhasználók kétszer is hozzáadhatók két szerepkörrel. Egyszer a közreműködővel és egyszer a felhasználói hozzáférés rendszergazdájával. További információ: [a felhasználó hozzáférésének megtekintése az Azure-erőforrásokhoz](../../role-based-access-control/check-access.md).

    ![hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>További előfeltételek a manuális adatforgalomhoz

* Regisztrálja a EventGrid erőforrás-szolgáltatót a Azure Portal használatával.

    A [Azure Portal](https://portal.azure.com/)lépjen az **előfizetések**-> [előfizetés] – >**ResourceProviders** elemre.

    Keressen rá a **Microsoft. Media** és a **Microsoft. EventGrid** kifejezésre. Ha nem a "regisztrált" állapotban van, kattintson a **regisztráció** elemre. A regisztráció néhány percet vesz igénybe.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Új fiók létrehozása az Azure-ban 

> [!NOTE]
> Ha az Azure-előfizetése tanúsítványalapú többtényezős hitelesítést használ, rendkívül fontos, hogy a következő lépéseket olyan eszközön végezze el, amelyen telepítve vannak a szükséges tanúsítványok.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Jelölje be a **korlátlan fiók létrehozása** gombot:

    ![Új Video Indexer fiók létrehozása](./media/create-account/create-unlimited-account.png)
1. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést.

    ![Video Indexer kapcsolódása az Azure-hoz](./media/create-account/new-account-on-azure-subscription.png)
1. Válasszon ki egy Azure-régiót a támogatott helyekről: USA 2. nyugati régiója, Észak-Európa vagy Kelet-Ázsia.
1. A **Azure Media Services fiók** területen válasszon egyet a következő lehetőségek közül:

    * Új Media Services-fiók létrehozásához válassza az **Új erőforráscsoport létrehozása** lehetőséget. Adja meg az erőforráscsoport nevét.

        Az Azure létrehoz egy új fiókot az előfizetésében, beleértve egy új Azure Storage-fiókot is.  
    * Meglévő Media Services-fiók használatához válassza a **meglévő erőforrás használata** lehetőséget. A fiókok listából válassza ki a fiókját.

        A Media Services-fióknak a Video Indexer-fiókkal megegyező régióval kell rendelkeznie.

        > [!NOTE]
        > Az indexelési időtartam és az alacsony átviteli sebesség minimálisra csökkentése érdekében erősen ajánlott a Media Services-fiókban lévő [fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) típusának és számának módosítása **10 S3 fenntartott egységre**. [A fenntartott egységek módosításához lásd: a portál használata](../previous/media-services-portal-scale-media-processing.md). A fenntartott egységeket a fiókja terheli, és megtekintheti a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * A kapcsolat manuális konfigurálásához válassza a **váltás manuális konfigurációra** hivatkozásra.

        Részletes információk: [Kapcsolódás az Azure-hoz manuálisan](#connect-to-azure-manually-advanced-option) (speciális beállítás) szakasz, amely az alábbiak szerint jelenik meg.
1. Ha elkészült, kattintson a **Létrehozás** gombra. Ez a művelet akár néhány percet is igénybe vehet.

    Miután csatlakozott az Azure-hoz, az új Video Indexer fiók megjelenik a fiók listában:

    ![új fiók](./media/create-account/new-account.png)
1. Győződjön meg arról, hogy a Media Services-fiók folyamatos átviteli végpontja fut, mielőtt le tudja játszani a videókat a Video Indexer webalkalmazásban (a Start gombra, ha a leállított állapotú).

> [!TIP]
> Ha szeretné, hogy a fiók felhasználóbarát megjelenítést állomásnév, lépjen a **Beállítások menüpontra**.

## <a name="connect-to-azure-manually-advanced-option"></a>Kapcsolódás az Azure-hoz manuálisan (speciális beállítás)

Ha nem sikerült csatlakozni az Azure-hoz, a probléma megoldásához manuálisan is csatlakozhat.

> [!NOTE]
> Javasoljuk, hogy a következő három fiókkal rendelkezzen ugyanabban a régióban: a Media Services-fiókkal összekapcsolt Video Indexer fiókkal, valamint az ugyanahhoz a Media Services fiókhoz csatlakoztatott Azure Storage-fiókkal.

### <a name="create-and-configure-a-media-services-account"></a>Media Services fiók létrehozása és konfigurálása

1. Az [Azure](https://portal.azure.com/) Portal használatával hozzon létre egy Azure Media Services fiókot a [fiók létrehozása](../previous/media-services-portal-create-account.md)című témakörben leírtak szerint.

     Győződjön meg arról, hogy a Media Services fiók a klasszikus API-kkal lett létrehozva. 
 
    ![Klasszikus API Media Services](./media/create-account/enable-classic-api.png)


    A Media Services-fiókhoz tartozó Storage-fiók létrehozásakor válassza a **StorageV2** és a **geo-redundáns** (GRS) beállítást a replikálási mezőkhöz.

    ![Új AMS-fiók](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Ügyeljen rá, hogy jegyezze fel a Media Services erőforrás és fiók nevét. Ehhez a következő szakaszban ismertetett lépéseket kell elvégeznie.
1. Állítsa be a [fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) típusát és számát **10 S3 fenntartott egységre** a létrehozott Media Services fiókban. [A fenntartott egységek módosításához lásd: a portál használata](../previous/media-services-portal-scale-media-processing.md).

    A fenntartott egységeket a fiókért kell fizetnie, a [díjszabás részleteinek](https://azure.microsoft.com/pricing/details/media-services/#analytics)megtekintéséhez. s
1. A videók a Video Indexer webalkalmazásban való lejátszása előtt el kell indítania az új Media Services-fiók alapértelmezett **adatfolyam-végpontját** .

    Az új Media Services fiókban válassza a **folyamatos átviteli végpontok** lehetőséget. Ezután válassza ki a folyamatos átviteli végpontot, és kattintson a Start gombra.

    ![Streamvégpontok](./media/create-account/create-ams-account-se.png)
4. A Media Services API-val való hitelesítéshez Video Indexer létre kell hozni egy AD-alkalmazást. Az alábbi lépések végigvezetik az Azure ad- [hitelesítés használatának első lépései a Azure Portal használatával](../previous/media-services-portal-get-started-with-aad.md)című témakörben ismertetett Azure ad-hitelesítési folyamaton:

    1. Az új Media Services fiókban válassza az **API-hozzáférés** lehetőséget.
    2. Válassza ki az [egyszerű szolgáltatás hitelesítési módszerét](../previous/media-services-portal-get-started-with-aad.md).
    3. Az ügyfél-azonosító és az ügyfél titkos kulcsának beolvasása

        Miután kiválasztotta a **Beállítások** -> **kulcsokat**, adja hozzá a **leírást**, nyomja meg a **Mentés** gombot, és a kulcs értéke feltöltve lesz.

        Ha a kulcs lejár, a fiók tulajdonosának kapcsolatba kell lépnie Video Indexer támogatással a kulcs megújításához.

        > [!NOTE]
        > Ügyeljen rá, hogy jegyezze fel a kulcs értékét és az alkalmazás AZONOSÍTÓját. Ehhez a következő szakaszban ismertetett lépések szükségesek.

### <a name="connect-manually"></a>Manuális kapcsolat

Az **új fiók létrehozása a** [video Indexer](https://www.videoindexer.ai/) oldalán található Azure-előfizetés párbeszédpanelen válassza a **váltás kézi konfigurációra** hivatkozásra.

A párbeszédpanelen adja meg a következő adatokat:

|Beállítás|Leírás|
|---|---|
|Video Indexer fiók régiója|Az Video Indexer-fiók régiójának neve. A jobb teljesítmény és az alacsonyabb költségek érdekében javasoljuk, hogy adja meg annak a régiónak a nevét, ahol a Azure Media Services erőforrás és az Azure Storage-fiók található. |
|Azure AD-bérlő|Az Azure AD-bérlő neve, például "contoso.onmicrosoft.com". A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó nevére a jobb felső sarokban. Keresse meg a **tartománytól** jobbra található nevet.|
|Előfizetés azonosítója|Az Azure-előfizetés, amely alatt ezt a kapcsolatokat létre kell hozni. Az előfizetés-azonosító a Azure Portalból kérhető le. Válassza a **minden szolgáltatás** lehetőséget a bal oldali panelen, és keressen rá az "előfizetések" kifejezésre. Válassza az **előfizetések** lehetőséget, majd válassza ki a kívánt azonosítót az előfizetések listájából.|
|Azure Media Services erőforráscsoport neve|Azon erőforráscsoport neve, amelyben létrehozta a Media Services fiókot.|
|Media Service-erőforrás neve|Az előző szakaszban létrehozott Azure Media Services fiók neve.|
|Alkalmazásazonosító|Az Azure AD-alkalmazás azonosítója (a megadott Media Services fiókra vonatkozó engedélyekkel), amelyeket az előző szakaszban hozott létre.|
|Alkalmazás kulcsa|Az előző szakaszban létrehozott Azure AD-alkalmazás kulcsa. |

### <a name="import-your-content-from-the-trial-account"></a>Tartalom importálása a *próbaverziós* fiókból

Új fiók létrehozásakor lehetősége van importálni a tartalmat a *próbaverziós* fiókból az új fiókba. Ha az **új fiók létrehozása az Azure-előfizetéshez** párbeszédpanelen az *Importálás* lehetőséget választja, a rendszer a *próbaverziós* fiókból az új fiókba másolja a média és a tartalom modell testreszabását.

A tartalom importálásának lehetősége a fent ismertetett automatizált és manuális megközelítések esetében is érvényes.

> [!NOTE]
> A tartalom csak egyszer importálható minden fiókból.
>
> A *próbaverziós* fiók nem availagle a Azure Government felhőben.

## <a name="azure-media-services-considerations"></a>Azure Media Services megfontolások

A következő Azure Media Services kapcsolódó megfontolások érvényesek:

* Ha meglévő Media Services-fiókhoz szeretne csatlakozni, győződjön meg arról, hogy a Media Services-fiók a klasszikus API-kkal lett létrehozva. 
 
    ![Klasszikus API Media Services](./media/create-account/enable-classic-api.png)
* Ha meglévő Media Services-fiókhoz csatlakozik, Video Indexer nem módosítja a meglévő Media szolgáltatás **számára fenntartott egységek** konfigurációját.

   Előfordulhat, hogy a tervezett terhelésnek megfelelően módosítania kell a Media szolgáltatás számára fenntartott egységek típusát és számát. Ne feledje, hogy ha a terhelés magas, és nincs elég egysége vagy sebessége, a videók feldolgozása időtúllépési hibát okozhat.
* Ha új Media Services-fiókhoz csatlakozik, Video Indexer automatikusan elindítja az alapértelmezett **folyamatos átviteli végpontot** :

    ![Media Services streaming végpont](./media/create-account/ams-streaming-endpoint.png)

    A streaming-végpontok jelentős indítási idővel rendelkeznek. Ezért több percet is igénybe vehet, amikor a fiókját az Azure-ba csatlakoztatta, amíg a videók nem továbbíthatók és nem nézték a Video Indexer webalkalmazásban.
* Ha meglévő Media Services-fiókhoz csatlakozik, Video Indexer nem módosítja az alapértelmezett adatfolyam-végpont konfigurációját. Ha nincs futó adatfolyam- **végpont**, akkor nem nézhet meg videókat ebből a Media Services fiókból vagy video Indexerból.
* Ha automatikusan csatlakozik, Video Indexer beállítja a Media szolgáltatás **számára fenntartott egységeket** 10 S3 egységre:

    ![Media Services fenntartott egységek](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>A Video Indexer fiók létrehozásának automatizálása

A fiók létrehozásának automatizálásához a következő két lépésből álló folyamat szükséges:
 
1. A Azure Resource Manager használatával hozzon létre egy Azure Media Services-fiókot és egy Azure AD-alkalmazást.

    Tekintse meg a [Media Services fiók létrehozási sablonjának](https://github.com/Azure-Samples/media-services-v3-arm-templates)példáját.
1. Hívja [a Create-Account fiókot a Media Services és az Azure ad-alkalmazással](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-indexer-in-azure-government"></a>Video Indexer a Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>A Azure Governmenthoz való csatlakozás előfeltételei

-   Azure-előfizetés [Azure Governmentban](../../azure-government/index.yml).
- Azure AD-fiók Azure Governmentban.
- Az engedélyekkel és erőforrásokkal kapcsolatos összes előzetes követelmény az [Azure-hoz való csatlakozás előfeltételeiben](#prerequisites-for-connecting-to-azure)leírtak szerint.

### <a name="create-new-account-via-the-azure-government-portal"></a>Új fiók létrehozása a Azure Government portálon keresztül

> [!NOTE]
> A Azure Government felhő nem tartalmaz Video Indexer *próbaverzióját* .

Fizetős fiók létrehozása a Video Indexer-portálon keresztül:

1. Nyissa meg a következőt: https://videoindexer.ai.azure.us 
1. Jelentkezzen be Azure Government Azure AD-fiókjával.
1.  Ha nem rendelkezik olyan Video Indexer fiókkal a Azure Governmentban, amelynek Ön a tulajdonosa vagy közreműködője, akkor a fiók létrehozásának megkezdéséhez egy üres felülettel fog rendelkezni. 

    A folyamat többi része a fentiekben leírtak szerint csak azok a régiók lesznek, amelyekről a video Indexer elérhető lesz. 

    Ha már létezik közreműködő vagy rendszergazda egy vagy több Video Indexer fiókban Azure Government, akkor a fiókba kerül, és innen elindíthat egy további fiók létrehozásához szükséges lépéseket, a fent leírtak szerint.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Új fiók létrehozása az Azure Government API-n keresztül

Ha fizetős fiókot szeretne létrehozni a Azure Governmentban, kövesse a következő témakör utasításait: [create-Paid-Account](). Ez az API-végpont csak kormányzati Felhőbeli régiókat tartalmaz.

### <a name="limitations-of-video-indexer-on-azure-government"></a>A Azure Government Video Indexer korlátozásai

*   Nem érhető el manuális tartalom-moderálás a kormányzati felhőben. 

    A nyilvános felhőben, ha a tartalom sértőnek minősül a tartalom moderálása alapján, az ügyfél megkérheti, hogy egy ember megtekintse a tartalmat, és esetleg visszaállítsa ezt a döntést.  
*   Nincsenek próbaverziós fiókok. 
* Bing Description – a gov Cloud szolgáltatásban nem jelennek meg az azonosított hírességek és nevesített entitások leírása. Ez csak a felhasználói felületi képesség. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, törölje azokat az erőforrásokat, amelyeket nem szeretne használni.

### <a name="delete-a-video-indexer-account"></a>Video Indexer fiók törlése

Ha Video Indexer fiókot szeretne törölni, törölheti a fiókot a Video Indexer webhelyről. A fiók törléséhez a tulajdonosnak kell lennie.

Válassza ki a fiók – > **Beállítások**  ->  **törölje ezt a fiókot**. 

A fiók 90 nap múlva véglegesen törölve lesz.

## <a name="next-steps"></a>Következő lépések

Programozott módon használhatja a próbaverziós fiókját és/vagy az Azure-hoz kapcsolódó Video Indexer-fiókokat a következő témakörben található utasítások végrehajtásával: API-k [használata](video-indexer-use-apis.md).

Ugyanazt az Azure AD-felhasználót kell használnia, amelyet az Azure-hoz való csatlakozáskor használt.