---
title: Alkalmazás hitelesítése Azure Service Bus entitásokhoz való hozzáféréshez
description: Ez a cikk a Azure Service Bus entitások (várólisták, témakörök stb.) eléréséhez Azure Active Directoryekkel való hitelesítéssel kapcsolatos információkat tartalmaz.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518228"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Alkalmazás hitelesítése és engedélyezése Azure Active Directory használatával Azure Service Bus entitások eléréséhez
Azure Service Bus támogatja a Azure Active Directory (Azure AD) használatát a Service Bus entitások (várólisták, témakörök, előfizetések vagy szűrők) kérésének engedélyezéséhez. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű szolgáltatásnév. További információ a szerepkörökről és a szerepkör-hozzárendelésekről: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) egy Service Bus entitáshoz próbál hozzáférni, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. A tokent kérő erőforrás neve `https://servicebus.azure.net` .
 1. Ezután a jogkivonat a Service Bus szolgáltatásnak küldött kérelem részeként a megadott erőforráshoz való hozzáférés engedélyezéséhez lesz átadva.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitás által küldött kéréseket Service Bus szolgáltatásra, tekintse meg a [Azure Service Bus erőforrásokhoz való hozzáférés hitelesítése Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](service-bus-managed-service-identity.md)című témakört. 

Az engedélyezési lépés megköveteli, hogy egy vagy több Azure-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. A Azure Service Bus olyan Azure-szerepköröket biztosít, amelyek a Service Bus erőforrásokra vonatkozó engedélyek készleteit foglalják magukban. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Ha többet szeretne megtudni az Azure-szerepkörök Azure Service Bushoz való hozzárendeléséről, tekintse meg [a Azure Service Bus Azure beépített szerepkörei](#azure-built-in-roles-for-azure-service-bus)című témakört. 

A Service Busra irányuló kérelmeket használó natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-t. Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket Service Bus erőforrásokhoz. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-szerepkörök kiosztása hozzáférési jogosultságokhoz
Azure Active Directory (Azure AD) engedélyezi a hozzáférési jogokat az [Azure RBAC](../role-based-access-control/overview.md)keresztül a biztonságos erőforrásokhoz. Azure Service Bus az Azure beépített szerepköreinek készletét határozza meg, amelyek magukban foglalják a Service Bus entitásokhoz való hozzáféréshez használt engedélyek közös készleteit, és egyéni szerepköröket is meghatározhat az adatok eléréséhez.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés az előfizetés, az erőforráscsoport vagy a Service Bus névtér szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Az Azure beépített szerepkörei Azure Service Bus
Azure Service Bus esetében a névterek és az összes kapcsolódó erőforrás a Azure Portal és az Azure Resource Management API-val való kezelése már védett az Azure RBAC-modell használatával. Az Azure az alábbi beépített Azure-szerepköröket biztosítja a Service Bus névtérhez való hozzáférés engedélyezéséhez:

- [Azure Service Bus adattulajdonos](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): lehetővé teszi az adathozzáférést Service Bus névterek és az entitások számára (várólisták, témakörök, előfizetések és szűrők)
- [Azure Service Bus adatfeladó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): ezt a szerepkört használva hozzáférést biztosíthat a Service Bus névtérhez és az entitásokhoz.
- [Azure Service Bus adatfogadó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): használja ezt a szerepkört, hogy fogadja a hozzáférést Service Bus névtérhez és az entitásokhoz. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt Azure-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférési hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel a Service Bus erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Üzenetsor**, **témakör** vagy **előfizetés**: a szerepkör-hozzárendelés az adott Service Bus entitásra vonatkozik. A Azure Portal jelenleg nem támogatja a felhasználók/csoportok/felügyelt identitások hozzárendelését az előfizetési szinten Service Bus Azure-szerepkörökhöz. 
- **Service Bus névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes Service Bus topológiáját fedi le.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Service Bus erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Service Bus erőforrására vonatkozik.

> [!NOTE]
> Ne feledje, hogy az Azure-szerepkör-hozzárendelések akár öt percet is igénybe vehetnek. 

További információ a beépített szerepkörök meghatározásáról: a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations). Az egyéni Azure-szerepkörök létrehozásával kapcsolatos információkért lásd: [Azure egyéni szerepkörök](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés kezeléséről az Azure RBAC és a Azure Portal használatával, tekintse meg [ezt a cikket](..//role-based-access-control/role-assignments-portal.md). 

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> Az alábbiakban ismertetett lépések szerepkört rendelnek a Service Bus-névtérhez. Ugyanezeket a lépéseket követve rendelhet hozzá szerepköröket más támogatott hatókörökhöz (erőforráscsoport, előfizetés stb.).

1. A [Azure Portal](https://portal.azure.com/)navigáljon a Service Bus-névtérhez. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget a névtér hozzáférés-vezérlési beállításainak megjelenítéséhez. Ha Service Bus névteret kell létrehoznia, kövesse a cikk utasításait: [hozzon létre egy Service Bus üzenetküldési névteret](service-bus-create-namespace-portal.md).

    ![Access Control kiválasztása a bal oldali menüben](./media/authenticate-application/select-access-control-menu.png)
1. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez. Kattintson a **Hozzáadás** gombra az eszköztáron, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet. 

    ![Hozzáadás gomb az eszköztáron](./media/authenticate-application/role-assignments-add-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Service Bus szerepkört** . 
    1. Keresse meg a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatásnév) megkeresését, amelyhez hozzá szeretné rendelni a szerepkört.
    1. A szerepkör-hozzárendelés mentéséhez válassza a **Mentés** lehetőséget. 

        ![Szerepkör társítása egy felhasználóhoz](./media/authenticate-application/assign-role-to-user.png)
    4. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy az Azure-felhasználók a Azure Service Bus adattulajdonosi szerepkörben vannak. 
        
        ![A listában szereplő felhasználó](./media/authenticate-application/user-in-list.png)

A hasonló lépések végrehajtásával hozzárendelhet egy olyan szerepkört, amely egy erőforráscsoporthoz vagy egy előfizetéshez tartozik. A szerepkör és a hozzá tartozó hatókör meghatározása után tesztelheti ezt a viselkedést a [githubon található mintákkal](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Hitelesítés egy alkalmazásból
Az Azure AD és a Service Bus használatának egyik legfőbb előnye, hogy a hitelesítő adatait már nem kell a kódban tárolni. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformról. Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (egy felhasználó, egy csoport vagy egy egyszerű szolgáltatásnév) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás a hozzáférési jogkivonatot használva engedélyezheti a kérelmeket Azure Service Bus.

A következő részben bemutatjuk, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft Identity platform 2,0-alapú hitelesítéshez. A Microsoft Identity platform 2,0-es verziójával kapcsolatos további információkért lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../active-directory/develop/v2-overview.md).

A OAuth 2,0 kód engedélyezési folyamatának áttekintését lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Alkalmazás regisztrálása Azure AD-Bérlővel
Az Azure AD használatának első lépése Service Bus entitások engedélyezéséhez az ügyfélalkalmazás regisztrálása egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com/). Az ügyfélalkalmazás regisztrálása után az alkalmazással kapcsolatos információkat adhat meg az AD szolgáltatáshoz. Az Azure AD egy ügyfél-azonosítót (más néven alkalmazás-azonosítót) biztosít, amellyel társíthatja az alkalmazást az Azure AD Runtime szolgáltatással. Az ügyfél-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). 

A következő képek a webalkalmazások regisztrálásának lépéseit mutatják be:

![Egy alkalmazás regisztrálása](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az átirányítási URI-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Az alkalmazás regisztrálását követően megjelenik az **alkalmazás (ügyfél) azonosítója** a **Beállítások** területen:

![A regisztrált alkalmazás alkalmazás-azonosítója](./media/authenticate-application/application-id.png)

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Jegyezze fel a **TenantId** és a **ApplicationId**. Ezekre az értékekre szüksége lesz az alkalmazás futtatásához.

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása   
Az alkalmazásnak szüksége van egy ügyfél titkos kulcsára, hogy igazolja az identitását a jogkivonat kérésekor. Az ügyfél titkos kulcsának hozzáadásához kövesse az alábbi lépéseket.

1. Ha még nem szerepel az oldalon, navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. Válassza ki a **tanúsítványok & Secrets** elemet a bal oldali menüben.
1. Az **ügyfél** titkos kulcsa területen válassza az **új** titkos kulcs lehetőséget egy új titok létrehozásához.

    ![Új ügyfél titka – gomb](./media/authenticate-application/new-client-secret-button.png)
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközt, majd kattintson a **Hozzáadás** gombra.

    ![Ügyfél titkos oldalának hozzáadása](./media/authenticate-application/add-client-secret-page.png)
1. Az új titok értékének azonnali másolása biztonságos helyre. A kitöltési érték csak egyszer jelenik meg.

    ![Titkos ügyfélkulcs](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>A Service Bus API engedélyei
Ha az alkalmazás egy konzolos alkalmazás, regisztrálnia kell egy natív alkalmazást, és hozzá kell adnia a **Microsoft. SERVICEBUS** API-engedélyeit a **szükséges engedélyekhez** . A natív alkalmazásokhoz szükség van egy **átirányítási URI-ra** is az Azure ad-ben, amely azonosítóként szolgál. az URI-nak nem kell hálózati célhelynek lennie. `https://servicebus.microsoft.com`Ezt a példát használja, mert a mintakód már használja ezt az URI-t.

### <a name="client-libraries-for-token-acquisition"></a>Az ügyfél kódtárai a tokenek beszerzéséhez  
Miután regisztrálta az alkalmazást, és engedélyezte az engedélyek küldését és fogadását Azure Service Busban, programkódot adhat hozzá az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és a OAuth 2,0 token beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft Identity platform hitelesítési kódtárainak](../active-directory/develop/reference-v2-libraries.md) egyikét vagy egy olyan nyílt forráskódú függvénytárat, amely támogatja az OpenID vagy a Connect 1,0-et. Az alkalmazás ezután a hozzáférési token használatával engedélyezheti a kérelmeket Azure Service Bus.

A jogkivonatok beszerzését támogató forgatókönyvek listáját a [Microsoft Authentication Library (MSAL) a .net GitHub-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) adattárhoz [című szakaszában találja](https://aka.ms/msal-net-scenarios) .

## <a name="sample-on-github"></a>Minta a GitHubon
Tekintse meg a következő mintát a GitHubon: a [Service Bus Azure szerepköralapú hozzáférés-vezérlése](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Használja az **ügyfél titkos bejelentkezési** beállítását, ne pedig az **interaktív felhasználói bejelentkezés** lehetőséget. Ha az ügyfél titkos kulcsát használja, nem jelenik meg egy előugró ablak. Az alkalmazás a bérlő AZONOSÍTÓját és az alkalmazás AZONOSÍTÓját használja a hitelesítéshez. 

### <a name="run-the-sample"></a>Minta futtatása

A minta futtatása előtt szerkessze a **app.config** fájlt, és a forgatókönyvtől függően állítsa be a következő értékeket:

- `tenantId`: A **TenantId** értékre van állítva.
- `clientId`: A **ApplicationId** értékre van állítva.
- `clientSecret`: Ha az ügyfél titkos kódjával szeretne bejelentkezni, hozza létre azt az Azure AD-ben. Natív alkalmazás helyett webalkalmazást vagy API-t is használhat. Emellett adja hozzá az alkalmazást **Access Control (iam)** alatt a korábban létrehozott névtérben.
- `serviceBusNamespaceFQDN`: Állítsa az újonnan létrehozott Service Bus névtér teljes DNS-nevére; például: `example.servicebus.windows.net` .
- `queueName`: Állítsa be a létrehozott üzenetsor nevét.
- Az alkalmazásban az előző lépésekben megadott átirányítási URI.

A konzol alkalmazás futtatásakor a rendszer kéri, hogy válasszon ki egy forgatókönyvet. A szám beírásával és az ENTER billentyű lenyomásával válassza az **interaktív felhasználói bejelentkezés** lehetőséget. Az alkalmazás egy bejelentkezési ablakot jelenít meg, amely kéri a Service Bus elérését, majd a szolgáltatás használatával futtatja a küldési/fogadási forgatókönyvet a bejelentkezési identitás használatával.


## <a name="next-steps"></a>Következő lépések
- További információ az Azure RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)?
- Az Azure-beli szerepkör-hozzárendelések Azure PowerShell, az Azure CLI vagy a REST API használatával történő hozzárendeléséről és kezeléséről a következő cikkekben talál további információt:
    - [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)
    - [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása a REST API használatával](../role-based-access-control/role-assignments-rest.md)
    - [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure Resource Manager sablonok használatával](../role-based-access-control/role-assignments-template.md)

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

- [Azure RBAC-minták Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
- [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
- [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)