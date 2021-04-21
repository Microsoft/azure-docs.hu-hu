---
title: Felügyelt identitások azure-erőforrásokhoz Service Bus
description: Ez a cikk bemutatja, hogyan férhet hozzá felügyelt identitásokkal Azure Service Bus entitásokkal (üzenetsorokkal, témakörökvel és előfizetésekkel).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: 0558e00ac7e8ce67d2e5194b02d2de06f2d38ff1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785432"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory hozzáféréshez Azure Service Bus erőforrásokhoz
[Az Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/overview.md) identitása egy Azure-beli szolgáltatások közötti funkció, amely lehetővé teszi az alkalmazáskódot futtató üzemelő példányhoz társított biztonságos identitások létrehozására. Ezt az identitást ezután hozzáférés-vezérlési szerepkörökhöz társíthatja, amelyek egyéni engedélyeket adnak az alkalmazás számára szükséges adott Azure-erőforrások eléréséhez.

Felügyelt identitásokkal az Azure platform kezeli ezt a futásidejű identitást. A hozzáférési kulcsokat nem kell az alkalmazás kódjában vagy konfigurációjában tárolnia és védenie, sem magának az identitásnak, sem pedig az elérnie szükséges erőforrásoknak. Egy Azure App Service-alkalmazásban vagy azure-erőforrásokhoz engedélyezett felügyelt entitásokkal rendelkező virtuális gépen futó Service Bus-ügyfélalkalmazásnak nem kell SAS-szabályokat és -kulcsokat vagy más hozzáférési jogkivonatokat kezelnie. Az ügyfélalkalmazásnak csak az üzenetkezelési névtér végpontcímére Service Bus szüksége. Amikor az alkalmazás csatlakozik, Service Bus a felügyelt entitás környezetét egy olyan műveletben köti össze az ügyféllel, amely a cikk későbbi, egy példáját mutatja be. Miután társította egy felügyelt identitással, a Service Bus az összes engedélyezett műveletet. Az engedélyezés egy felügyelt entitás és az Service Bus társításával adható meg. 

## <a name="overview"></a>Áttekintés
Amikor egy rendszerbiztonsági tag (egy felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni egy Service Bus entitáshoz, a kérést engedélyezni kell. Az Azure AD-val az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszer hitelesíti a rendszerbiztonsági tag identitását, és egy OAuth 2.0-jogkivonatot ad vissza. A jogkivonat kérésének `https://servicebus.azure.net` erőforrásneve: .
 1. Ezután a jogkivonatot a rendszer egy, a Service Bus szolgáltatásnak a megadott erőforráshoz való hozzáférésre vonatkozó kérés részeként adja át.

A hitelesítési lépéshez az alkalmazáskérésnek egy OAuth 2.0 hozzáférési jogkivonatot kell tartalmazni futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési készleten vagy egy Azure-függvényalkalmazáson belül fut, felügyelt identitással férhet hozzá az erőforrásokhoz. 

Az engedélyezési lépéshez hozzá kell rendelni egy vagy több Azure-szerepkört a rendszerbiztonsági taghoz. Azure Service Bus azure-szerepköröket biztosít, amelyek az erőforrásokra vonatkozó engedélyek Service Bus foglalnak magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tag engedélyeit. További információ az Azure-szerepkörök Azure Service Bus való hozzárendelésről: Az Azure beépített szerepkörei [a Azure Service Bus.](#azure-built-in-roles-for-azure-service-bus) 

Az Azure AD-val olyan natív alkalmazások és webalkalmazások is Service Bus, amelyek kéréseket Service Bus az Azure AD-val. Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot, és hogyan használhatja azt az erőforrásokhoz Service Bus kérelmekhez. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-szerepkörök hozzárendelése hozzáférési jogosultságokként
Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC)](../role-based-access-control/overview.md)engedélyezi a biztonságos erőforrásokhoz való hozzáférési jogosultságokat. Azure Service Bus azure-beli beépített szerepköröket határoz meg, amelyek az Service Bus-entitások eléréséhez használt általános engedélykészleteket foglalják magukban, és egyéni szerepköröket is meghatározhat az adatok eléréséhez.

Ha egy Azure-szerepkör hozzá van rendelve egy Azure AD-rendszerbiztonsági taghoz, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz a rendszerbiztonsági tag számára. A hozzáférés az előfizetés, az erőforráscsoport vagy a névtér Service Bus lehet. Az Azure AD rendszerbiztonsági tag lehet felhasználó, csoport, alkalmazás-szolgáltatásnév vagy az Azure-erőforrások felügyelt identitása.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Beépített Azure-szerepkörök a Azure Service Bus
A Azure Service Bus a névterek és az összes kapcsolódó erőforrás kezelése a Azure Portal és az Azure Resource Management API használatával már védve van az Azure RBAC-modell használatával. Az Azure az alábbi beépített Azure-szerepköröket biztosítja egy virtuális Service Bus számára:

- [Azure Service Bus adattulajdonos:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Adatelérést biztosít Service Bus névtérhez és entitásaihoz (üzenetsorok, témakörök, előfizetések és szűrők)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Ezzel a szerepkörsel küldhet hozzáférést Service Bus névtérhez és entitásokhoz.
- [Azure Service Bus adat fogadója:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)Ezzel a szerepkörsel hozzáférést adhat Service Bus névtérhez és entitásokhoz. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt azure-beli szerepkört rendel egy rendszerbiztonsági taghoz, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt írják elő, hogy mindig a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken a Service Bus erőforrásokhoz való hozzáférés a legszűkebb hatókörtől kezdve elérhető:

- **Üzenetsor,** **témakör** vagy **előfizetés:** A szerepkör-hozzárendelés az adott Service Bus vonatkozik. Jelenleg a Azure Portal nem támogatja a felhasználók/csoportok/felügyelt identitások Hozzárendelését Service Bus Azure-szerepkörökhöz az előfizetés szintjén. Az alábbi példában az [az-role-assignment-create Azure CLI-parancs](/cli/azure/role/assignment?#az_role_assignment_create) használatával rendelhet identitást egy Service Bus Azure-szerepkörhöz: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus:** A szerepkör-hozzárendelés a névtér alatt Service Bus csoport teljes topológiáját és a hozzá társított fogyasztói csoportra is vonatkozik.
- **Erőforráscsoport:** A szerepkör-hozzárendelés az erőforráscsoport Service Bus összes erőforrásra vonatkozik.
- **Előfizetés:** A szerepkör-hozzárendelés az előfizetés Service Bus erőforráscsoport összes erőforráscsoportja összes erőforrására vonatkozik.

> [!NOTE]
> Ne feledje, hogy az Azure-beli szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet. 

A beépített szerepkörök meghatározásával kapcsolatos további információkért lásd a [szerepkör-definíciók fogalmát.](../role-based-access-control/role-definitions.md#management-and-data-operations) További információ az egyéni Azure-szerepkörök létrehozásáról: [Egyéni Azure-szerepkörök.](../role-based-access-control/custom-roles.md)

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése virtuális gépen
Ahhoz, hogy az Azure-erőforrások felügyelt identitását Service Bus a virtuális gépről származó erőforrások engedélyezéséhez, először engedélyeznie kell az Azure-erőforrások felügyelt identitását a virtuális gépen. Az Azure-erőforrások felügyelt identitásának engedélyezéséről az alábbi cikkekben talál további információt:

- [Azure Portalra](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-kódtárak létrehozása](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitáshoz az Azure AD-ban
Ha engedélyeznie kell a Service Bus szolgáltatásnak az alkalmazásban egy felügyelt identitástól való kérését, először konfigurálja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beállításait a felügyelt identitáshoz. Azure Service Bus olyan Azure-szerepköröket határoz meg, amelyek magukban foglalják a felhasználóktól való küldésre és olvasásra vonatkozó Service Bus. Ha az Azure-szerepkör hozzá van rendelve egy felügyelt identitáshoz, a felügyelt identitás hozzáférést kap Service Bus entitásokhoz a megfelelő hatókörben.

Az Azure-szerepkörök hozzárendelésére vonatkozó további információkért lásd: Hitelesítés és Azure Active Directory hozzáférés a Service Bus [erőforrásokhoz.](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Az Service Bus azure-erőforrások felügyelt identitásokkal való használata
A felügyelt Service Bus való használathoz hozzá kell rendelnie az identitást a szerepkörhöz és a megfelelő hatókörhöz. Az ebben a szakaszban található eljárás egy egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és hozzáfér Service Bus erőforrásokhoz.

Itt egy, a következőben üzemeltetett minta-webalkalmazást [Azure App Service.](https://azure.microsoft.com/services/app-service/) A webalkalmazások létrehozásának részletes útmutatója: [ASP.NET Core-webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. A Beállítások **menüben válassza** az **Identitás lehetőséget.** 
1. Az Állapot **mezőben** válassza a **Be lehetőséget.** 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Felügyelt identitás webalkalmazáshoz](./media/service-bus-managed-service-identity/identity-web-app.png)

Miután engedélyezte ezt a beállítást, létrejön egy új szolgáltatásidentitás a Azure Active Directory (Azure AD) szolgáltatásban, és konfigurálva lesz a App Service gazdagépen.

> [!NOTE]
> Felügyelt identitás használata esetén a kapcsolati sztring formátumának a következőnek kell lennie: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=ManagedIdentity` .

Most rendelje hozzá ezt a szolgáltatásidentitást egy szerepkörhöz a szükséges hatókörben a Service Bus erőforrásaiban.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök hozzárendelése a Azure Portal
Ha szerepkört szeretne hozzárendelni egy Service Bus névtérhez, lépjen a névtérre a Azure Portal. Jelenítse Access Control (IAM) beállításait az erőforráshoz, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> Az alábbi lépések egy szolgáltatásidentitás-szerepkört rendelnek a Service Bus névtereihez. Ugyanezekkel a lépésekkel rendelhet hozzá szerepköröket más támogatott hatókörökhöz (erőforráscsoporthoz és előfizetéshez). 
> 
> [Ha még Service Bus üzenetküldési](service-bus-create-namespace-portal.md) névteret, hozzon létre egy újat. 

1. A Azure Portal lépjen a saját Service Bus, és jelenítse meg a névtér **Áttekintés** lapját. 
1. Válassza **Access Control (IAM)** lehetőséget a bal oldali menüben a névtér hozzáférés-vezérlési beállításainak Service Bus megjelenítéséhez.
1.  Válassza a **Szerepkör-hozzárendelések lapot** a szerepkör-hozzárendelések listájának listájának kiválasztásához.
3.  Válassza **a Hozzáadás,** majd a **Szerepkör-hozzárendelés hozzáadása lehetőséget.**
4.  A **Szerepkör-hozzárendelés hozzáadása lapon** kövesse az alábbi lépéseket:
    1. A **Szerepkör** mezőben válassza Service Bus hozzárendelni kívánt szerepkört. Ebben a példában ez Azure Service Bus **adattulajdonos.**
    1. A Hozzáférés **hozzárendelése mezőben** válassza **a** App Service a Rendszer által **hozzárendelt felügyelt identitás területen.** 
    1. Válassza ki **azt az** előfizetést, amelyben a webalkalmazás felügyelt identitása létre lett hozva.
    1. Válassza ki **a létrehozott** webalkalmazás felügyelt identitását. Az identitás alapértelmezett neve megegyezik a webalkalmazás nevével. 
    1. Ezután válassza a **Mentés lehetőséget.**
        
        ![Szerepkör-hozzárendelés hozzáadása oldal](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    A szerepkör hozzárendelése után a webalkalmazás hozzáférhet a Service Bus hatókörben található entitásokhoz. 

    > [!NOTE]
    > A felügyelt identitásokat támogató szolgáltatások listájáért lásd: Az Azure-erőforrások felügyelt [identitását támogató szolgáltatások.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="run-the-app"></a>Az alkalmazás futtatása
Most módosítsa a létrehozott ASP.NET alapértelmezett lapját. A webalkalmazás kódját ebből a GitHub-adattárból [használhatja.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

A Default.aspx oldal a kezdőlapja. A kód a Default.aspx.cs fájlban található. Az eredmény egy minimális webalkalmazás néhány beviteli  mezővel, valamint az üzenetek küldésére és fogadására Service Bus gombok használatával. 

Figyelje meg, hogy a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálva van. A közös hozzáférésű jogkivonat (SAS) jogkivonat-szolgáltató használata helyett a kód létrehoz egy jogkivonat-szolgáltatót a hívással a felügyelt identitás `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` számára. Így nem kell titkos adatokat megőriznie és használnia. A jogkivonat-szolgáltató automatikusan kezeli a felügyelt identitás Service Bus folyamatát és az engedélyezési kézfogást. Ez egy egyszerűbb modell, mint az SAS használata.

Miután végrehajtotta ezeket a módosításokat, tegye közzé és futtassa az alkalmazást. Könnyedén beszerezheti a megfelelő közzétételi adatokat, ha letölt, majd importál egy közzétételi profilt a Visual Studio:

![Közzétételi profil lekérte](./media/service-bus-managed-service-identity/msi3.png)
 
Üzenetek küldését vagy fogadását a névtér nevével és a létrehozott entitás nevével adhatja meg. Ezután kattintson a **send (Küldés) gombra,** vagy **fogadja a (fogadása) gombra.**


> [!NOTE]
> - A felügyelt identitás csak az Azure-környezetben, az App Servicesben, az Azure-beli virtuális gépeken és a méretezési készletekben működik. .NET-alkalmazások esetén a Service Bus NuGet-csomag által használt Microsoft.Azure.Services.AppAuthentication kódtár absztrakciót biztosít a protokollhoz, és támogatja a helyi fejlesztési élményt. Ez a kódtár azt is lehetővé teszi, hogy helyileg tesztelje a kódot a fejlesztői gépen az Visual Studio, az Azure CLI 2.0 vagy Active Directory integrált hitelesítés felhasználói fiókjával. További információ a könyvtár helyi fejlesztési lehetőségeiről: Szolgáltatások között való hitelesítés a [.NET-Azure Key Vault való hitelesítéshez.](/dotnet/api/overview/azure/service-to-service-authentication)  

## <a name="next-steps"></a>Következő lépések

Az üzenetkezelésről Service Bus alábbi témakörökben olvashat bővebben:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
