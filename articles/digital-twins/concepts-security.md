---
title: Az Azure Digital Twins-megoldások biztonsága
titleSuffix: Azure Digital Twins
description: Az Azure digitális ikrekkel kapcsolatos ajánlott biztonsági eljárások ismertetése.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: f438f4393538478f2960e709d8e23beb6106510d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485749"
---
# <a name="secure-azure-digital-twins"></a>Biztonságos Azure digitális Twins

A biztonság érdekében az Azure Digital Twins pontos hozzáférés-vezérlést tesz lehetővé az üzembe helyezés meghatározott adatai, erőforrásai és műveletei számára. Ezt az **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** nevű részletes szerepkör és az engedély-kezelési stratégia teszi lehetővé. Az Azure RBAC általános alapelveiről [itt](../role-based-access-control/overview.md)olvashat.

Az Azure Digital Twins az inaktív adatok titkosítását is támogatja.

## <a name="granting-permissions-with-azure-rbac"></a>Engedélyek megadása az Azure RBAC

Az Azure RBAC az Azure Digital Twins számára biztosítható [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) integrációja révén.

Az Azure RBAC segítségével engedélyeket adhat egy *rendszerbiztonsági tag*számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítve van az Azure AD-ben, és egy OAuth 2,0 tokent kap vissza. Ez a jogkivonat egy Azure Digital Twins-példány hozzáférési kérelmének engedélyezésére használható.

### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Az Azure AD-vel a hozzáférés egy kétlépéses folyamat. Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni az Azure digitális Twinshoz, a kérést *hitelesíteni* és *engedélyezni*kell. 

1. Először a rendszerbiztonsági tag identitása *hitelesítve*van, és a rendszer egy OAuth 2,0 tokent ad vissza.
2. Ezután a tokent egy, az Azure Digital Twins szolgáltatásnak küldött kérelem részeként továbbítja a rendszer a megadott erőforráshoz való hozzáférés *engedélyezéséhez* .

A hitelesítési lépés megköveteli, hogy az alkalmazás kérése OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson (például egy [Azure functions](../azure-functions/functions-overview.md) alkalmazáson) fut, akkor a **felügyelt identitás** használatával férhet hozzá az erőforrásokhoz. További információk a felügyelt identitásokról a következő szakaszban olvashatók.

Az engedélyezési lépés megköveteli, hogy az Azure-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Az Azure Digital Twins olyan Azure-szerepköröket biztosít, amelyek az Azure digitális Twins erőforrásaihoz tartozó engedélyek készleteit foglalják magukban. Ezeket a szerepköröket a cikk későbbi részében ismertetjük.

Ha többet szeretne megtudni az Azure-ban támogatott szerepkörökről és szerepkör-hozzárendelésekről, tekintse meg az Azure RBAC dokumentációjának [*különböző szerepköreinek megismerése*](../role-based-access-control/rbac-and-directory-admin-roles.md) című témakört.

#### <a name="authentication-with-managed-identities"></a>Hitelesítés felügyelt identitásokkal

Az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) egy Azure-beli szolgáltatás, amely lehetővé teszi, hogy az alkalmazás kódjának futtatásához szükséges biztonságos identitást hozzon létre. Ezt az identitást hozzáférés-vezérlési szerepkörökkel társíthatja, így egyéni engedélyeket adhat meg az alkalmazás által igényelt egyes Azure-erőforrások eléréséhez.

A felügyelt identitások esetében az Azure platform kezeli ezt a futásidejű identitást. Az alkalmazás kódjában vagy konfigurációjában nem kell tárolnia és védelemmel ellátnia a hozzáférési kulcsokat, vagy magát az identitást, vagy a hozzáféréshez szükséges erőforrásokat. Egy Azure App Service alkalmazáson belül futó Azure Digital Twins-ügyfélalkalmazás nem igényel SAS-szabályokat és-kulcsokat, vagy bármely más hozzáférési jogkivonatot. Az ügyfélalkalmazás csak az Azure Digital Twins-névtér végpont-címeként szükséges. Az alkalmazás csatlakozásakor az Azure Digital Twins a felügyelt entitás környezetét az ügyfélhez köti. Ha egy felügyelt identitáshoz van társítva, az Azure digitális Twins-ügyfele minden jóváhagyott műveletet végrehajthat. Az engedélyezést ezután egy felügyelt entitás egy Azure digitális Twins Azure-szerepkörrel való társításával lehet megadni (lásd alább).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Engedélyezés: az Azure Digital Twins Azure-szerepkörei

Az Azure az alábbi Azure beépített szerepköröket biztosítja az Azure digitális Twins-erőforrásokhoz való hozzáférés engedélyezéséhez:
* *Azure digitális Twins-adattulajdonos* – ezzel a szerepkörrel teljes hozzáférést biztosíthat az Azure digitális Twins-erőforrásaihoz.
* *Azure Digital Twins Adatolvasó* – ezzel a szerepkörrel csak olvasási hozzáférést biztosíthat az Azure Digital Twins-erőforrásokhoz.

>[!NOTE]
> Ezeket a szerepköröket nemrég átnevezték a korábbi nevükről az előzetes verzióban:
> * *Az Azure Digital Twins-adattulajdonos korábban az* *Azure digitális ikrek tulajdonosa (előzetes verzió)* volt.
> * *Az Azure Digital Twins Adatolvasó* korábban *Azure digitális Twins-olvasó (előzetes verzió)* volt.

A beépített szerepkörök meghatározásával kapcsolatos további információkért lásd: a szerepkör- [*definíciók megismerése*](../role-based-access-control/role-definitions.md) az Azure RBAC dokumentációjában. Az egyéni Azure-szerepkörök létrehozásával kapcsolatos információkért lásd: [*Azure egyéni szerepkörök*](../role-based-access-control/custom-roles.md).

A szerepköröket kétféleképpen rendelheti hozzá:
* Az Azure Digital Twins hozzáférés-vezérlés (IAM) paneljén a Azure Portalban (lásd: [*Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal használatával*](../role-based-access-control/role-assignments-portal.md))
* szerepkör hozzáadására vagy eltávolítására szolgáló CLI-parancsok használatával

A részletes útmutatásért próbálja ki az Azure Digital Twins [*oktatóanyagban: teljes körű megoldás összekapcsolása*](tutorial-end-to-end.md).

### <a name="permission-scopes"></a>Engedélyek hatókörei

Mielőtt Azure-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférési hatókörét. Az ajánlott eljárások azt írják elő, hogy a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken az Azure Digital Twins-erőforrásokhoz való hozzáférés hatóköre elérhető.
* Modellek: az erőforrás műveletei szabályozzák az Azure digitális Twins-ban feltöltött [modellek](concepts-models.md) feletti irányítást.
* Digitális Twins-gráf lekérdezése: az erőforrás műveletei határozzák meg a [lekérdezési műveletek](concepts-query-language.md) futtatásának képességét a digitális ikrek számára az Azure digitális Twins-gráfon belül.
* Digital Twin: az erőforrás műveletei lehetővé teszik a SZIFILISZi műveletek vezérlését a [digitális ikrekben](concepts-twins-graph.md) a Twin gráfban.
* Digitális kettős kapcsolat: az erőforráshoz tartozó műveletek határozzák meg a SZIFILISZi műveletek vezérlését a digitális ikrek közötti [kapcsolatokon](concepts-twins-graph.md) a Twin gráfban.
* Esemény útvonala: az erőforrás műveletei határozzák meg az Azure digitális ikrek [eseményeinek továbbítására](concepts-route-events.md) vonatkozó engedélyeket egy végponti szolgáltatáshoz, például az [Event Hub](../event-hubs/event-hubs-about.md), a [Event Grid](../event-grid/overview.md)vagy a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Hibaelhárítási engedélyek

Ha egy felhasználó olyan műveletet próbál végrehajtani, amelyet nem engedélyeznek a szerepkörük, előfordulhat, hogy a szolgáltatáskérelem olvasása hibaüzenetet kap `403 (Forbidden)` . További információt és hibaelhárítási lépéseket a [*Hibaelhárítás: az Azure digitális Twins-kérelem sikertelen állapot: 403 (tiltott)*](troubleshoot-error-403.md)című témakörben talál.

## <a name="encryption-of-data-at-rest"></a>Inaktív adatok titkosítása

Az Azure Digital Twins inaktív és átvitel alatt álló adatok titkosítását teszi lehetővé az adatközpontokban, és visszafejti azt az eléréséhez. Ez a titkosítás egy Microsoft által felügyelt titkosítási kulcs használatával történik.

## <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)

Az Azure Digital Twins jelenleg nem támogatja a **több eredetű erőforrás-megosztást (CORS)**. Ennek eredményeképpen, ha egy böngésző-alkalmazásból, egy [API Management (APIM)](../api-management/api-management-key-concepts.md) vagy egy [Power apps](https://docs.microsoft.com/powerapps/powerapps-overview) -összekötőből REST API hív meg, akkor a rendszer házirend-hibát észlelt.

A hiba elhárításához a következők egyikét teheti:
* A CORS fejlécének leszalaga `Access-Control-Allow-Origin` az üzenetből. Ez a fejléc azt jelzi, hogy a válasz megosztható-e. 
* Alternatív megoldásként hozzon létre egy CORS-proxyt, és tegye elérhetővé az Azure digitális Twins REST API kérését. 

## <a name="next-steps"></a>Következő lépések

* Tekintse meg ezeket a fogalmakat működés közben [*: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md).

* Tekintse meg, hogyan kezelheti ezeket a fogalmakat az ügyfélalkalmazás kódjában az [*alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)című témakörben.

* További információ az [Azure RBAC](../role-based-access-control/overview.md)-ről.
