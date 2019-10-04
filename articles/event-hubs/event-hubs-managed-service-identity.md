---
title: Felügyelt identitások az Azure-erőforrásokhoz – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk információt nyújt az Azure-erőforrások az Azure Event Hubs felügyelt identitások használatával
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978478"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Az Azure-erőforrásokhoz az Event Hubs felügyelt identitásokból

[Felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) több Azure-funkció, amely lehetővé teszi, hogy a központi telepítést, amely alatt az alkalmazás kódja fut társított biztonságos identitás létrehozása. Ezután társíthatja az identitásukat hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket adott, az alkalmazásnak szüksége van az Azure erőforrások eléréséhez. 

A felügyelt identitásokból az Azure platform kezeli a futtatókörnyezet identitást. Nem kell tárolja és védi a tárelérési kulcsok az alkalmazáskód vagy a konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges. Az Event Hubs ügyfélalkalmazás belüli Azure App Service-alkalmazáshoz vagy virtuális gépen fut az Azure-erőforrások támogatása engedélyezve van a felügyelt identitásokból nem kell kezelni a SAS-szabályok és a kulcsokat, vagy bármely más hozzáférési jogkivonatok. Az ügyfélalkalmazásnak csupán az Event Hubs-névtér végpont címe. Amikor az alkalmazás csatlakozik, az Event Hubs a felügyelt identitás környezet egy műveletben, amely a cikk későbbi részében egy példa látható az ügyfél van kötve.

Ha egy felügyelt identitás társítva, Event Hubs-ügyfél összes jogosult műveletek teheti meg. Az engedély megadása egy felügyelt identitás, az Event Hubs-szerepkörökhöz való társításával. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepkörök és engedélyek
Hozzáadhat egy felügyelt identitás a **Event Hubs-adatok tulajdonosa** Event Hubs-névtér szerepe. Ez a szerepkör az identitás, a névtérben lévő összes entitáshoz (a felügyeleti és az üzemeltetés) teljes hozzáférést biztosít.

>[!IMPORTANT]
> Korábban támogatva a felügyelt identitás hozzáadása a **tulajdonosa** vagy **közreműködői** szerepkör. Azonban, adat-hozzáférési jogosultságokat a **tulajdonosa** és **közreműködői** szerepkör már nem összes régió megfelel. Ha használja a **tulajdonosa** vagy **közreműködői** szerepkör, kapcsolót használó a **Event Hubs-adatok tulajdonosa** szerepkör.

Az új beépített szerepkör használja, kövesse az alábbi lépéseket: 

1. Lépjen az [Azure Portalra](https://portal.azure.com)
2. Keresse meg az Event Hubs-névtér.
3. Az a **Event Hubs-Namespace** lapon jelölje be **hozzáférés Control(IAM)** a bal oldali menüből.
4. Az a **hozzáférés-vezérlés (IAM)** lapon jelölje be **Hozzáadás** a a **egy szerepkör-hozzárendelés hozzáadása** szakaszban. 

    ![Szerepkör-hozzárendelés gomb hozzáadása](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. Az a **szerepkör-hozzárendelés hozzáadása** lapon, tegye a következőket: 
    1. A **szerepkör**válassza **Azure Event Hubs-adatok tulajdonosa**. 
    2. Válassza ki a **identitás** a szerepkörhöz kell hozzáadni.
    3. Kattintson a **Mentés** gombra. 

        ![Event Hubs-adatok tulajdonosi szerepkör](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. Váltson a **szerepkör-hozzárendelések** lapon, és győződjön meg arról, hogy a felhasználó nincs hozzáadva a **Azure Event Hubs-adatok tulajdonosa** szerepkör. 

    ![Győződjön meg arról, felhasználói adnak a szerepkörhöz](./media/event-hubs-managed-service-identity/role-assignments.png)
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Felügyelt identitások használható Event Hubs az Azure-erőforrások

Az alábbi szakasz az alábbi lépéseket:

1. Hozzon létre, és üzembe helyezünk egy mintaalkalmazást, amely egy felügyelt identitás alatt fut.
2. Event Hubs-névtér identitás hozzáférést.
3. Az alkalmazás hogyan kapcsolódik az event hubs használatával az identitásukat.

Ez a bevezető ismerteti a lévő üzemeltetett webalkalmazásban [Azure App Service](https://azure.microsoft.com/services/app-service/). Egy virtuális gép által üzemeltetett alkalmazás számára szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Hozzon létre egy App Service-webalkalmazás

Az első lépés az App Service ASP.NET-alkalmazás létrehozása. Ha még nem ismeri az ehhez az Azure-ban, hajtsa végre a [Ez az útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Azonban helyett, ahogyan az oktatóprogram MVC alkalmazás létrehozása, hozzon létre egy Web Forms-alkalmazást.

### <a name="set-up-the-managed-identity"></a>A felügyelt identitás beállítása

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure Portalon (az útmutató is látható), majd nyissa meg a **Felügyeltszolgáltatás-identitás** lapon, és engedélyezze a szolgáltatást: 

![Felügyelt Szolgáltatásidentitás lap](./media/event-hubs-managed-service-identity/msi1.png)
 
Miután engedélyezte a funkciót, egy új felügyeltszolgáltatás-identitás létrehozása az Azure Active Directoryban, és az App Service-ben gazdagépen konfigurált.

### <a name="create-a-new-event-hubs-namespace"></a>Hozzon létre egy új Event Hubs-névtér

Ezután [Event Hubs-névtér létrehozása](event-hubs-create.md). 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **szerepkör-hozzárendelés hozzáadása** felügyelt identitásnak hozzáadása a **tulajdonosa** szerepkör. Ehhez keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra. A felügyelt identitás a webes alkalmazás most már hozzáfér az Event Hubs-névtér és az event hubs korábban hozott létre. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most úgy módosítja az alapértelmezett oldalt, az ASP.NET alkalmazás hozott létre. Használhatja a webes alkalmazás kódot az [GitHub-adattárban](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Miután az alkalmazás elindításához, a böngésző EventHubsMSIDemo.aspx mutat. Is beállíthat, mint a kezdőlap. A kód a EventHubsMSIDemo.aspx.cs fájlban található. Az eredmény néhány számbeviteli mezők, valamint a minimális webalkalmazás **küldése** és **kap** küldése vagy események fogadása az Event Hubs csatlakozó gombok. 

Megjegyzés: a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. Helyett a szolgáltatóval közös hozzáférési jogkivonat (SAS-) token, a kódot hoz létre a felügyelt identitás a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` hívja. Ezért nincsenek nincs titkos kódok, mentése és használata. A jogkivonat-szolgáltatót, amely egyszerűbb, mint a SAS használatával modell automatikusan kezeli, a folyamat az Event Hubs és a hitelesítési kézfogás felügyelt identitás-környezet.

Miután végrehajtotta ezeket a módosításokat, közzététel, és futtathatja az alkalmazást. A helyes közzétételi adatok letöltésével és a egy közzétételi profilt a Visual Studióban, majd importálásával kaphat:

![Közzétételi profil importálása](./media/event-hubs-managed-service-identity/msi3.png)
 
Üzeneteket küldeni vagy fogadni, adja meg a névtér nevét és a létrehozott entitás nevét, majd kattintson **küldése** vagy **kap**. 
 
A felügyelt identitás csak belül az Azure-környezetet, és csak az App Service-környezet, amelyben be vannak állítva ezek az működik. Felügyelt identitások jelenleg nem működik az App Service üzembe helyezési pontok.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
