---
title: Alkalmazás hitelesítése az Azure Signaler szolgáltatás eléréséhez
description: Ez a cikk tájékoztatást nyújt az alkalmazások Azure Active Directory az Azure Signaler szolgáltatás eléréséhez való hitelesítéséről
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092612"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Alkalmazás hitelesítése Azure Active Directory az Azure Signaler szolgáltatás eléréséhez
A Microsoft Azure Azure Active Directory (Azure AD) alapján integrált hozzáférés-vezérlési felügyeletet biztosít az erőforrásokhoz és alkalmazásokhoz. Az Azure AD és az Azure Signaler szolgáltatás használatának egyik legfőbb előnye, hogy a hitelesítő adatokat többé nem kell a kódban tárolnia. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. A tokent kérő erőforrás neve `https://signalr.azure.com/` . Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (egy alkalmazás, erőforráscsoport vagy egyszerű szolgáltatásnév) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti az Azure Signaler szolgáltatás erőforrásainak kérését.

Ha egy szerepkört egy Azure AD-rendszerbiztonsági tag rendel hozzá, az Azure hozzáférést biztosít ezen erőforrásokhoz az adott rendszerbiztonsági tag számára. A hozzáférés az előfizetés, az erőforráscsoport vagy az Azure-jelző erőforrás szintjére is kiterjed. Az Azure AD-biztonság szerepköröket rendelhet egy felhasználóhoz, egy csoporthoz, egy egyszerű alkalmazáshoz vagy egy [felügyelt identitáshoz az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> A szerepkör-definíció engedélyek gyűjteménye. A szerepköralapú hozzáférés-vezérlés (RBAC) szabályozza, hogy ezek az engedélyek hogyan legyenek kikényszerítve a szerepkör-hozzárendeléssel. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből. További információ: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Alkalmazás regisztrálása Azure AD-Bérlővel
Az Azure AD az Azure Signaler szolgáltatás erőforrásainak engedélyezéséhez való használatának első lépéseként regisztrálja az alkalmazást egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com/). Az alkalmazás regisztrálásakor az alkalmazással kapcsolatos információkat adhat meg az AD szolgáltatáshoz. Az Azure AD egy ügyfél-azonosítót (más néven alkalmazás-azonosítót) biztosít, amellyel társíthatja az alkalmazást az Azure AD Runtime szolgáltatással. Az ügyfél-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). 

A következő képek a webalkalmazások regisztrálásának lépéseit mutatják be:

![Egy alkalmazás regisztrálása](./media/authenticate/app-registrations-register.png)

> [!Note]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az átirányítási URI-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Az alkalmazás regisztrálását követően megjelenik az **alkalmazás (ügyfél) azonosítója** a **Beállítások** területen:

![A regisztrált alkalmazás alkalmazás-azonosítója](./media/authenticate/application-id.png)

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása   
Az alkalmazásnak szüksége van egy ügyfél titkos kulcsára, hogy igazolja az identitását a jogkivonat kérésekor. Az ügyfél titkos kulcsának hozzáadásához kövesse az alábbi lépéseket.

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. Válassza ki a **tanúsítványok & titkok** beállítást.
1. Az **ügyfél** titkos kulcsa területen válassza az **új** titkos kulcs lehetőséget egy új titok létrehozásához.
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközt.
1. Az új titok értékének azonnali másolása biztonságos helyre. A kitöltési érték csak egyszer jelenik meg.

![Ügyfél titkos kulcsának létrehozása](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Az ügyfél titkos kódjának létrehozása helyett tanúsítványokat is feltölthet.

![Tanúsítvány feltöltése](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés kezeléséről az Azure RBAC és a Azure Portal használatával, tekintse meg [ezt a cikket](..//role-based-access-control/role-assignments-portal.md). 

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

1. A [Azure Portal](https://portal.azure.com/)navigáljon a signaler-erőforráshoz.
1. Válassza a **Access Control (iam)** lehetőséget az Azure-jelző hozzáférés-vezérlési beállításainak megjelenítéséhez. 
1. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez. Kattintson a **Hozzáadás** gombra az eszköztáron, majd válassza a **szerepkör-hozzárendelés hozzáadása** elemet. 

    ![Hozzáadás gomb az eszköztáron](./media/authenticate/role-assignments-add-button.png)

1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Azure-jelző szerepkört** . 
    1. Keresse meg a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatásnév) megkeresését, amelyhez hozzá szeretné rendelni a szerepkört.
    1. A szerepkör-hozzárendelés mentéséhez válassza a **Mentés** lehetőséget. 

        ![Szerepkör társítása egy alkalmazáshoz](./media/authenticate/assign-role-to-application.png)

    1. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy az alkalmazás `signalr-dev` `signalr-service` a Signaler app Server szerepkörben található. 
        
        ![Szerepkör-hozzárendelési lista](./media/authenticate/role-assignment-list.png)

A hasonló lépések végrehajtásával hozzárendelhet egy szerepkört az erőforráscsoport vagy az előfizetés hatóköréhez. Miután definiálta a szerepkört és a hatókörét, tesztelheti ezt a [GitHub-helyen található](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)mintákkal.

## <a name="sample-codes-while-configuring-your-app-server"></a>Mintakód az App Server konfigurálása során.

Adja hozzá a következő beállításokat, ha `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Vagy egyszerűen konfigurálja a `ConnectionString` fájlt a `appsettings.json` fájljában.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

A használatakor `certificate` módosítsa a `clientSecret` `clientCert` következőhöz hasonlót:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Következő lépések
- További információ a RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)?
- Az Azure-beli szerepkör-hozzárendelések Azure PowerShell, az Azure CLI vagy a REST API használatával történő hozzárendeléséről és kezeléséről a következő cikkekben talál további információt:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API](../role-based-access-control/role-assignments-rest.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure Resource Manager-sablonokkal](../role-based-access-control/role-assignments-template.md)

Tekintse meg a következő kapcsolódó cikkeket:
- [Felügyelt identitás hitelesítése Azure Active Directory az Azure Signaler szolgáltatás eléréséhez](authenticate-managed-identity.md)
- [Az Azure Signaler szolgáltatáshoz való hozzáférés engedélyezése Azure Active Directory használatával](authorize-access-azure-active-directory.md)