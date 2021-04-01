---
title: Felügyelt identitás hitelesítése Azure Active Directory
description: Ez a cikk az Azure Event Hubs-erőforrások eléréséhez Azure Active Directory felügyelt identitások hitelesítésével kapcsolatos információkat tartalmaz.
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805287"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez
Az Azure Event Hubs támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik Event Hubs erőforrásokhoz való hozzáférést az Azure Virtual Machines (VM), a Function apps, a Virtual Machine Scale Sets és más szolgáltatások által futtatott alkalmazások Azure AD-beli hitelesítő adataival. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure-beli virtuális gépek felügyelt identitásával való hozzáférést az Event hub számára.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen
Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz a virtuális gép Event Hubs erőforrásainak engedélyezéséhez, először engedélyeznie kell az Azure-erőforrások felügyelt identitásait a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitásnak az Azure AD-ben
Ahhoz, hogy az alkalmazásban egy felügyelt identitástól Event Hubs szolgáltatásra vonatkozó kérést engedélyezzen, először konfigurálja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beállításait az adott felügyelt identitáshoz. Az Azure Event Hubs olyan Azure-szerepköröket határoz meg, amelyek kiterjednek a Event Hubs küldésére és olvasására vonatkozó engedélyekre. Ha az Azure-szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap a megfelelő hatókörben Event Hubs adatokhoz.

Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hitelesítés a Azure Active Directory használatával Event Hubs erőforrásaihoz való hozzáféréshez](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Az Event Hubs használata felügyelt identitásokkal
Ha a Event Hubs felügyelt identitásokkal szeretné használni, hozzá kell rendelnie a szerepkört és a megfelelő hatókört az identitáshoz. Az ebben a szakaszban szereplő eljárás egy olyan egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és Event Hubs erőforrásokhoz fér hozzá.

Itt egy [Azure app Service](https://azure.microsoft.com/services/app-service/)-ban üzemeltetett minta webalkalmazást használunk. A webalkalmazások létrehozásával kapcsolatos részletes útmutatásért lásd: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Lépjen a **Beállítások** menüpontra, és válassza az **identitás** elemet. 
1. Válassza ki a **bekapcsolni** kívánt **állapotot** . 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Webalkalmazás felügyelt identitása":::
4. Válassza az **Igen** lehetőséget az információs üzenetben. 

    Ha engedélyezte ezt a beállítást, a rendszer létrehoz egy új szolgáltatás-identitást a Azure Active Directory (Azure AD), és az App Service gazdagépre van konfigurálva.

    Most rendeljen hozzá egy szerepkörhöz a szolgáltatás identitását a Event Hubs erőforrásaihoz szükséges hatókörben.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal használatával
Ha szerepkört szeretne hozzárendelni Event Hubs erőforrásokhoz, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás Access Control (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések szolgáltatás-identitási szerepkört rendelnek a Event Hubs névterekhez. Ugyanezeket a lépéseket követve rendelhet hozzá egy szerepkört egy Event Hubs erőforráshoz. 

1. A Azure Portal navigáljon a Event Hubs névtérhez, és jelenítse meg a névtér **áttekintését** . 
1. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget az Event hub hozzáférés-vezérlési beállításainak megjelenítéséhez.
1.  Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
3.  Válassza a **Hozzáadás** lehetőséget, majd válassza a * * szerepkör-hozzárendelés hozzáadása * * * lehetőséget.
4.  A **szerepkör-hozzárendelés hozzáadása** lapon kövesse az alábbi lépéseket:
    1. A **szerepkör** területen válassza ki a hozzárendelni kívánt Event Hubs szerepkört. Ebben a példában ez az **Azure Event Hubs-adattulajdonos**.
    1. A **hozzáférés hozzárendelése** mezőhöz területen válassza a **app Service** a **rendszerhez rendelt felügyelt identitás** területen. 
    1. Válassza ki azt az **előfizetést** , amelyben a webalkalmazás felügyelt identitása létrejött.
    1. Válassza ki a létrehozott webalkalmazás **felügyelt identitását** . Az identitás alapértelmezett neve megegyezik a webalkalmazás nevével. 
    1. Ezután válassza a **Mentés** lehetőséget. 
    
        ![Szerepkör-hozzárendelési lap hozzáadása](./media/authenticate-managed-identity/add-role-assignment-page.png)

    A szerepkör hozzárendelése után a webalkalmazás hozzáférhet a megadott hatókörben lévő Event Hubs erőforrásokhoz. 

    > [!NOTE]
    > A felügyelt identitásokat támogató szolgáltatások listáját itt tekintheti meg: az [Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>A webalkalmazás tesztelése
1. Hozzon létre egy Event Hubs névteret és egy Event hubot. 
2. Telepítse a webalkalmazást az Azure-ba. Tekintse meg a következő többoldalas szakaszt a webalkalmazásra mutató hivatkozásokon a GitHubon. 
3. Győződjön meg arról, hogy a SendReceive. aspx be van állítva a webalkalmazás alapértelmezett dokumentuma. 
3. A webalkalmazás **identitásának** engedélyezése. 
4. Rendelje hozzá ezt az identitást a **Event Hubs adat-tulajdonosi** szerepkörhöz a névtér szintjén vagy az Event hub szintjén. 
5. Futtassa a webalkalmazást, adja meg a névtér nevét és az Event hub nevét, egy üzenetet, és válassza a **Küldés** lehetőséget. Az esemény fogadásához válassza a **fogadás** lehetőséget. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messaging. EventHubs (legújabb)](#tab/latest)
Most már elindíthatja a webalkalmazást, és irányíthatja a böngészőt az aspx-minta lapra. Megtalálhatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)-tárházban található Event Hubs-erőforrásokból adatokat küldő és fogadó minta webalkalmazást.

Telepítse a legújabb csomagot a [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)-ből, és kezdje el az események küldését Event Hubs a **EventHubProducerClient** használatával, és fogadja az eseményeket a **EventHubConsumerClient** segítségével. 

> [!NOTE]
> Egy olyan Java-minta esetében, amely felügyelt identitást használ az események esemény-központba való közzétételéhez, lásd: [események közzététele az Azure Identity Sample on githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (örökölt)](#tab/old)
Most már elindíthatja a webalkalmazást, és irányíthatja a böngészőt az aspx-minta lapra. Megtalálhatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)-tárházban található Event Hubs-erőforrásokból adatokat küldő és fogadó minta webalkalmazást.

Telepítse a legújabb csomagot a [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)-ből, és kezdje el küldeni és fogadni az Event huboktól származó adatokat az EventHubClient a következő kódban látható módon: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>A Kafkához készült Event Hubs
Apache Kafka alkalmazások használatával üzeneteket küldhet és fogadhat az Azure Event Hubsból a felügyelt Identity OAuth használatával. Tekintse meg a következő mintát a GitHubon: [Event Hubs for Kafka – üzenetek küldése és fogadása felügyelt Identity OAuth használatával](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Példák
- **Azure. Messaging. EventHubs** -minták
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft. Azure. EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft. Azure. EventHubs** könyvtárat használják, de egyszerűen frissítheti a legújabb **Azure. Messaging. EventHubs** könyvtár használatával. Ha át szeretné helyezni a mintát a régi könyvtárból az új verzióra, tekintse meg a [Microsoft. Azure. EventHubs-ről az Azure. Messaging. EventHubs-re való Migrálás útmutatóját](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Ez a minta frissítve lett a legújabb **Azure. Messaging. EventHubs** könyvtár használatára.
- [Event Hubs for Kafka – üzenetek küldése és fogadása a felügyelt identitás OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Következő lépések
- Az Azure-erőforrások felügyelt identitásának megismeréséhez tekintse meg a következő cikket: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)
- Tekintse meg a következő kapcsolódó cikkeket:
    - [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
    - [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)
