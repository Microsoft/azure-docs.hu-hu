---
title: Felügyelt identitások az Azure-erőforrásokhoz Service Bus
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások Azure Service Bus entitások (várólisták, témakörök és előfizetések) elérésére.
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: bd985acd9b775d6baef0abf488952e28c17aef2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954310"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory használatával Azure Service Bus erőforrások eléréséhez
Az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) egy Azure-beli szolgáltatás, amely lehetővé teszi, hogy az alkalmazás kódjának futtatásához használt központi telepítéshez tartozó biztonságos identitást hozzon létre. Ezután társíthatja az identitást hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket biztosítanak az alkalmazás által igényelt egyes Azure-erőforrások eléréséhez.

A felügyelt identitások esetében az Azure platform kezeli ezt a futásidejű identitást. Az alkalmazás kódjában vagy konfigurációjában nem kell tárolnia és védelemmel ellátnia a hozzáférési kulcsokat, vagy magát az identitást, vagy a hozzáféréshez szükséges erőforrásokat. Egy Azure App Service alkalmazáson belül vagy az Azure-erőforrások támogatását engedélyező felügyelt entitásokkal rendelkező virtuális gépen futó Service Bus ügyfélalkalmazás nem szükséges az SAS-szabályok és-kulcsok, illetve más hozzáférési jogkivonatok kezeléséhez. Az ügyfélalkalmazás csak az Service Bus üzenetküldési névtér végpontjának megfelelőre van szüksége. Amikor az alkalmazás csatlakozik, a Service Bus a cikk későbbi részében ismertetett művelettel köti össze a felügyelt entitás környezetét az ügyfélhez. Ha egy felügyelt identitáshoz van társítva, az Service Bus-ügyfél elvégezheti az összes jóváhagyott műveletet. Az engedélyezést egy felügyelt entitás Service Bus szerepkörökkel való társításával biztosítjuk. 

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) egy Service Bus entitáshoz próbál hozzáférni, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. A tokent kérő erőforrás neve `https://servicebus.azure.net` .
 1. Ezután a jogkivonat a Service Bus szolgáltatásnak küldött kérelem részeként a megadott erőforráshoz való hozzáférés engedélyezéséhez lesz átadva.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. 

Az engedélyezési lépés megköveteli, hogy egy vagy több Azure-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. A Azure Service Bus olyan Azure-szerepköröket biztosít, amelyek a Service Bus erőforrásokra vonatkozó engedélyek készleteit foglalják magukban. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Ha többet szeretne megtudni az Azure-szerepkörök Azure Service Bushoz való hozzárendeléséről, tekintse meg [a Azure Service Bus Azure beépített szerepkörei](#azure-built-in-roles-for-azure-service-bus)című témakört. 

A Service Busra irányuló kérelmeket használó natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-t. Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket Service Bus erőforrásokhoz. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-szerepkörök kiosztása hozzáférési jogosultságokhoz
Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Azure Service Bus az Azure beépített szerepköreinek készletét határozza meg, amelyek magukban foglalják a Service Bus entitásokhoz való hozzáféréshez használt engedélyek közös készleteit, és egyéni szerepköröket is meghatározhat az adatok eléréséhez.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés az előfizetés, az erőforráscsoport vagy a Service Bus névtér szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy felügyelt identitás az Azure-erőforrásokhoz.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Az Azure beépített szerepkörei Azure Service Bus
Azure Service Bus esetében a névterek és az összes kapcsolódó erőforrás a Azure Portal és az Azure Resource Management API-val való kezelése már védett az Azure RBAC-modell használatával. Az Azure az alábbi beépített Azure-szerepköröket biztosítja a Service Bus névtérhez való hozzáférés engedélyezéséhez:

- [Azure Service Bus adattulajdonos](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): lehetővé teszi az adathozzáférést Service Bus névterek és az entitások számára (várólisták, témakörök, előfizetések és szűrők)
- [Azure Service Bus adatfeladó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): ezt a szerepkört használva hozzáférést biztosíthat a Service Bus névtérhez és az entitásokhoz.
- [Azure Service Bus adatfogadó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): használja ezt a szerepkört, hogy fogadja a hozzáférést Service Bus névtérhez és az entitásokhoz. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt Azure-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférési hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel a Service Bus erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Üzenetsor**, **témakör** vagy **előfizetés**: a szerepkör-hozzárendelés az adott Service Bus entitásra vonatkozik. A Azure Portal jelenleg nem támogatja a felhasználók/csoportok/felügyelt identitások hozzárendelését az előfizetési szinten Service Bus Azure-szerepkörökhöz. Íme egy példa az Azure CLI-parancs használatára: [az-role-hozzárendelés-Create](/cli/azure/role/assignment?#az-role-assignment-create) az identitás Service Bus Azure-szerepkörhöz való hozzárendeléséhez: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes Service Bus topológiáját fedi le.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Service Bus erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Service Bus erőforrására vonatkozik.

> [!NOTE]
> Ne feledje, hogy az Azure-szerepkör-hozzárendelések akár öt percet is igénybe vehetnek. 

További információ a beépített szerepkörök meghatározásáról: a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations). Az egyéni Azure-szerepkörök létrehozásával kapcsolatos információkért lásd: [Azure egyéni szerepkörök](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen
Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz a virtuális gép Service Bus erőforrásainak engedélyezéséhez, először engedélyeznie kell az Azure-erőforrások felügyelt identitásait a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitásnak az Azure AD-ben
Ha az alkalmazásban felügyelt identitással kívánja engedélyezni a Service Bus szolgáltatást, először konfigurálja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beállításait az adott felügyelt identitáshoz. Azure Service Bus olyan Azure-szerepköröket határoz meg, amelyek a Service Bus küldésére és olvasására vonatkozó engedélyeket foglalnak magukban. Ha az Azure-szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap a Service Bus entitásokhoz a megfelelő hatókörben.

Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hitelesítés és engedélyezés Azure Active Directory Service Bus erőforrásokhoz való hozzáféréshez](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus használata felügyelt identitásokkal az Azure-erőforrásokhoz
Ha a Service Bus felügyelt identitásokkal szeretné használni, hozzá kell rendelnie a szerepkört és a megfelelő hatókört. Az ebben a szakaszban szereplő eljárás egy olyan egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és Service Bus erőforrásokhoz fér hozzá.

Itt egy [Azure app Service](https://azure.microsoft.com/services/app-service/)-ban üzemeltetett minta webalkalmazást használunk. A webalkalmazások létrehozásával kapcsolatos részletes útmutatásért lásd: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Lépjen a **Beállítások** menüpontra, és válassza az **identitás** elemet. 
1. Válassza ki a **bekapcsolni** kívánt **állapotot** . 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Webalkalmazás felügyelt identitása](./media/service-bus-managed-service-identity/identity-web-app.png)

Ha engedélyezte ezt a beállítást, a rendszer létrehoz egy új szolgáltatás-identitást a Azure Active Directory (Azure AD), és az App Service gazdagépre van konfigurálva.

> [!NOTE]
> Felügyelt identitás használata esetén a kapcsolódási karakterlánc formátuma a (z): `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Most rendeljen hozzá egy szerepkörhöz a szolgáltatás identitását a Service Bus erőforrásaihoz szükséges hatókörben.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal használatával
Ha egy szerepkört Service Bus névtérhez szeretne rendelni, akkor navigáljon a Azure Portal névtérhez. Jelenítse meg az erőforrás Access Control (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések szolgáltatás-identitási szerepkört rendelnek a Service Bus névterekhez. Ugyanezeket a lépéseket követve rendelhet hozzá szerepköröket más támogatott hatókörökhöz (erőforráscsoport és előfizetés). 
> 
> Ha nem rendelkezik ilyennel, [hozzon létre egy Service Bus üzenetküldési névteret](service-bus-create-namespace-portal.md) . 

1. A Azure Portal navigáljon a Service Bus névtérhez, és jelenítse meg a névtér **áttekintését** . 
1. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget a Service Bus névtér hozzáférés-vezérlési beállításainak megjelenítéséhez.
1.  Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
3.  Válassza a **Hozzáadás**, majd a **szerepkör-hozzárendelés hozzáadása** elemet.
4.  A **szerepkör-hozzárendelés hozzáadása** lapon kövesse az alábbi lépéseket:
    1. A **szerepkör** területen válassza ki a hozzárendelni kívánt Service Bus szerepkört. Ebben a példában ez Azure Service Bus az **adattulajdonos**.
    1. A **hozzáférés hozzárendelése** mezőhöz területen válassza a **app Service** a **rendszerhez rendelt felügyelt identitás** területen. 
    1. Válassza ki azt az **előfizetést** , amelyben a webalkalmazás felügyelt identitása létrejött.
    1. Válassza ki a létrehozott webalkalmazás **felügyelt identitását** . Az identitás alapértelmezett neve megegyezik a webalkalmazás nevével. 
    1. Ezután válassza a **Mentés** lehetőséget.
        
        ![Szerepkör-hozzárendelési lap hozzáadása](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Miután hozzárendelte a szerepkört, a webalkalmazás hozzáfér a megadott hatókörben lévő Service Bus entitásokhoz. 

    > [!NOTE]
    > A felügyelt identitásokat támogató szolgáltatások listáját itt tekintheti meg: az [Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="run-the-app"></a>Az alkalmazás futtatása
Most módosítsa a létrehozott ASP.NET-alkalmazás alapértelmezett oldalát. A webalkalmazás kódját a [GitHub-adattárból](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)is használhatja.  

Az alapértelmezett. aspx lap a kezdőlapja. A kód az alapértelmezett. aspx. cs fájlban található. Az eredmény egy minimális webalkalmazás, amely néhány beviteli mezővel rendelkezik, valamint a **küldési** és **fogadási** gombokkal, amelyek a Service Bushoz csatlakoznak a küldési és fogadási üzenetekhez.

Figyelje meg, hogy a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálva van. A megosztott hozzáférési jogkivonat (SAS) jogkivonat-szolgáltató használata helyett a kód létrehoz egy jogkivonat-szolgáltatót a felügyelt identitáshoz a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` hívással. Így nem kell megőrizni és használni a titkokat. A felügyelt identitás környezetének Service Busre való áramlását, valamint az engedélyezési kézfogást a jogkivonat-szolgáltató automatikusan kezeli. Ez egy egyszerűbb modell, mint az SAS használata.

A módosítások elvégzése után tegye közzé és futtassa az alkalmazást. A Visual Studióban a közzétételi profil letöltésével és importálásával egyszerűen hozzájuthat a megfelelő közzétételi adatokhoz:

![Közzétételi profil beolvasása](./media/service-bus-managed-service-identity/msi3.png)
 
Üzenetek küldéséhez vagy fogadásához adja meg a névtér nevét és a létrehozott entitás nevét. Ezután kattintson a **Küldés** vagy a **fogadás** lehetőségre.


> [!NOTE]
> - A felügyelt identitás csak az Azure-környezetben, az App Servicesben, az Azure-beli virtuális gépeken és a méretezési csoportokon belül működik. A .NET-alkalmazások esetében a Service Bus NuGet-csomag által használt Microsoft. Azure. Services. AppAuthentication függvénytár egy absztrakciót biztosít a protokollhoz, és támogatja a helyi fejlesztési élményt. Ez a könyvtár lehetővé teszi a kód helyi tesztelését a fejlesztői gépen a Visual studióból, az Azure CLI 2,0-ből vagy Active Directory integrált hitelesítésből származó felhasználói fiók használatával. A könyvtár helyi fejlesztési lehetőségeivel kapcsolatos további információkért lásd: [szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával](/dotnet/api/overview/azure/service-to-service-authentication).  

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
