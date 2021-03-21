---
title: Vész-helyreállítás implementálása biztonsági mentéssel és visszaállítással API Management
titleSuffix: Azure API Management
description: Ismerje meg, hogyan végezheti el a biztonsági mentést és a visszaállítást a vész-helyreállításhoz az Azure API Managementban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: 223d119786d99eac611ece597fc0e8de4fcaf6bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762403"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben

Az API-k Azure API Management használatával történő közzétételével és kezelésével kihasználhatja a hibatűrést és az olyan infrastrukturális képességeket, amelyeket manuálisan tervez, implementál és kezelhet. Az Azure platform csökkenti a lehetséges hibák nagy hányadát a költségek töredékéért.

A API Management szolgáltatást üzemeltető régiót érintő rendelkezésre állási problémák helyreállításához a szolgáltatást bármikor újra létre kell állítani egy másik régióban. A helyreállítási időre vonatkozó célkitűzéstől függően előfordulhat, hogy egy vagy több régióban szeretné megőrizni a készenléti szolgáltatást. Azt is megteheti, hogy az aktív szolgáltatással szinkronban tartja a konfigurációt és a tartalmakat a helyreállítási pont célkitűzése szerint. A szolgáltatás biztonsági mentési és visszaállítási funkciói lehetővé teszik a vész-helyreállítási stratégia megvalósításához szükséges építőelemeket.

A biztonsági mentési és visszaállítási műveletek API Management szolgáltatás konfigurációjának replikálására is használhatók az operatív környezetek, például a fejlesztés és az előkészítés során. Ügyeljen arra, hogy a futásidejű, például a felhasználók és az előfizetések adatait is másolja a rendszer, ami esetleg nem mindig kívánatos.

Ez az útmutató bemutatja, hogyan automatizálható a biztonsági mentési és visszaállítási műveletek, valamint a biztonsági mentési és visszaállítási kérelmek sikeres hitelesítése Azure Resource Manager alapján.

> [!IMPORTANT]
> A visszaállítási művelet nem módosítja a cél szolgáltatás egyéni állomásnév-konfigurációját. Azt javasoljuk, hogy ugyanazt az egyéni állomásnév-és TLS-tanúsítványt használja az aktív és a készenléti szolgáltatásokhoz is, hogy a visszaállítási művelet befejezése után a forgalom átirányítható legyen a készenléti példányra egy egyszerű DNS-CNAME változással.
>
> A biztonsági mentési művelet nem rögzíti a Azure Portal Analytics paneljén megjelenített jelentésekben szereplő, előre összevont naplózási adatokat.

> [!WARNING]
> Az egyes biztonsági másolatok 30 nap elteltével lejárnak. Ha a 30 napos lejárati időszak lejárta után megkísérli visszaállítani a biztonsági mentést, akkor a visszaállítás egy `Cannot restore: backup expired` üzenettel meghiúsul.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager kérelmek hitelesítése

> [!IMPORTANT]
> A biztonsági mentéshez és visszaállításhoz használt REST API Azure Resource Manager, és egy másik hitelesítési mechanizmussal rendelkezik, mint a API Management entitások felügyeletéhez szükséges REST API-k. Az ebben a szakaszban ismertetett lépések bemutatják, hogyan lehet hitelesíteni a Azure Resource Manager kérelmeket. További információ: [Azure Resource Manager kérelmek hitelesítése](/rest/api/index).

A Azure Resource Manager használó erőforrásokon végrehajtott összes feladatot a következő lépések végrehajtásával kell hitelesíteni a Azure Active Directory használatával:

-   Alkalmazás hozzáadása a Azure Active Directory bérlőhöz.
-   Állítsa be a hozzáadott alkalmazás engedélyeit.
-   Kérje le a jogkivonatot a kérelmek Azure Resource Manager való hitelesítéséhez.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az API Management Service-példányát tartalmazó előfizetés használatával navigáljon a Azure Active Directory **Alkalmazásregisztrációk** (Azure Active Directory  > felügyelet/Alkalmazásregisztrációk) lapjára.

    > [!NOTE]
    > Ha az Azure Active Directory alapértelmezett könyvtára nem látható a fiókjában, forduljon az Azure-előfizetés rendszergazdájához, és adja meg a szükséges engedélyeket a fiókjához.

3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **Létrehozás** ablak a jobb oldalon jelenik meg. Itt adhatja meg a HRE alkalmazáshoz kapcsolódó információkat.

4. Adjon nevet az alkalmazásnak.
5. Az alkalmazás típusa mezőben válassza a **natív** lehetőséget.
6. Adjon meg egy helyőrző URL-címet `http://resources` , például az **átirányítási URI** esetében, mivel ez egy kötelező mező, de az értéket nem használja később. Az alkalmazás mentéséhez kattintson a jelölőnégyzetre.
7. Kattintson a **Létrehozás** lehetőségre.

### <a name="add-an-application"></a>Alkalmazás hozzáadása

1. Az alkalmazás létrehozása után kattintson az **API-engedélyek** elemre.
2. Kattintson **az + engedély hozzáadása** lehetőségre.
4. Kattintson a **Microsoft API-k kiválasztása elemre**.
5. Válassza az **Azure Service Management** lehetőséget.
6. Kattintson a **kiválasztás** gombra.

    ![Engedélyek hozzáadása](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kattintson a **delegált engedélyek** elemre az újonnan hozzáadott alkalmazás mellett, jelölje be az **Access Azure Service Management (előzetes verzió)** jelölőnégyzetet.
8. Kattintson a **kiválasztás** gombra.
9. Kattintson az **engedélyek megadása** elemre.

### <a name="configuring-your-app"></a>Az alkalmazás konfigurálása

A biztonsági mentést létrehozó API-k meghívása és a visszaállítás előtt le kell kérnie a tokent. A következő példa a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot használja a jogkivonat lekéréséhez.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Cserélje le `{tenant id}` , `{application id}` és `{redirect uri}` használja az alábbi utasításokat:

1. Cserélje le a helyére a `{tenant id}` létrehozott Azure Active Directory alkalmazás bérlői azonosítóját. Az azonosítót **Alkalmazásregisztrációk**  ->  **végpontokra** kattintva érheti el.

    ![Végpontok][api-management-endpoint]

2. Cserélje le a `{application id}` értéket a beolvasott értékre a **Beállítások** lapon.
3. Cserélje le az `{redirect uri}` értéket a Azure Active Directory alkalmazás **átirányítási URI** -k lapjáról.

    Az értékek megadása után a kód példájának az alábbi példához hasonló tokent kell visszaadnia:

    ![Jogkivonat][api-management-arm-token]

    > [!NOTE]
    > A jogkivonat bizonyos idő elteltével lejárhat. Új jogkivonat létrehozásához hajtsa végre ismét a kód mintát.

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek meghívása

A REST API-k az [API Management szolgáltatás – biztonsági mentési](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) és [API Management szolgáltatás – visszaállítás](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

A következő szakaszokban leírt "biztonsági mentési és visszaállítási" műveletek meghívása előtt állítsa be a REST-hívás engedélyezési kérelmének fejlécét.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>API Management szolgáltatás biztonsági mentése

API Management szolgáltatás biztonsági mentése a következő HTTP-kérést adja ki:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

ahol:

-   `subscriptionId` -Annak az előfizetésnek az azonosítója, amely a API Management szolgáltatást tárolja, amelyről biztonsági másolatot szeretne készíteni
-   `resourceGroupName` -Az Azure API Management szolgáltatás erőforráscsoport neve
-   `serviceName` – annak a API Management-szolgáltatásnak a neve, amelyről biztonsági másolatot készít a létrehozás időpontjában.
-   `api-version` -csere a `2019-12-01`

A kérelem törzsében adja meg a cél Azure Storage-fiók nevét, a hozzáférési kulcsot, a blob-tároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Adja meg a kérelem fejlécének értékét a következőre: `Content-Type` `application/json` .

A Backup egy hosszú ideig futó művelet, amely több mint egy percet is igénybe vehet. Ha a kérelem sikeres volt, és a biztonsági mentési folyamat elindult, a rendszer egy `202 Accepted` fejlécet tartalmazó Response állapotkódot kap `Location` . A művelet állapotának megállapításához végezze el a "GET" kéréseket a fejlécben lévő URL-címhez `Location` . Amíg a biztonsági mentés folyamatban van, továbbra is "202 elfogadott" állapotkódot kap. A válasz kódja a `200 OK` biztonsági mentési művelet sikeres befejezését jelzi.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>API Management szolgáltatás visszaállítása

API Management szolgáltatás korábban létrehozott biztonsági másolatból történő visszaállításához végezze el a következő HTTP-kérést:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

ahol:

-   `subscriptionId` -Annak az előfizetésnek az azonosítója, amely a API Management szolgáltatást tárolja, és amelyről biztonsági másolatot kíván visszaállítani
-   `resourceGroupName` -Az Azure API Management szolgáltatást tároló erőforráscsoport neve, amelyről biztonsági másolatot kíván visszaállítani
-   `serviceName` – annak a API Management-szolgáltatásnak a neve, amelyet a rendszer a létrehozáskor adott vissza.
-   `api-version` -csere a `api-version=2019-12-01`

A kérelem törzsében határozza meg a biztonságimásolat-fájl helyét. Adja hozzá az Azure Storage-fiók nevét, a hozzáférési kulcsot, a blob-tároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Adja meg a kérelem fejlécének értékét a következőre: `Content-Type` `application/json` .

A visszaállítás egy hosszú ideig futó művelet, amely akár 30 vagy több percet is igénybe vehet. Ha a kérelem sikeres volt, és a visszaállítási folyamat elindult, egy `202 Accepted` fejlécet tartalmazó válasz állapotkódot kap `Location` . A művelet állapotának megállapításához végezze el a "GET" kéréseket a fejlécben lévő URL-címhez `Location` . Amíg a visszaállítás folyamatban van, továbbra is megkapja az "202 elfogadott" állapotkódot. A válasz kódja a `200 OK` visszaállítási művelet sikeres befejezését jelzi.

> [!IMPORTANT]
> A visszaállított szolgáltatás **SKU** -jának **meg kell egyeznie** a visszaállítani kívánt biztonsági másolati szolgáltatás SKU-jának.
>
> A szolgáltatás konfigurációjában (például API-k, házirendek, fejlesztői portál megjelenés) végrehajtott **módosítások** **felülírhatják** a visszaállítási műveletet.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> A biztonsági mentési és visszaállítási műveletek a PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) és a [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) parancsokkal is elvégezhetők.

## <a name="constraints-when-making-backup-or-restore-request"></a>Biztonsági mentési vagy visszaállítási kérelem végrehajtásakor megkötések

-   A kérelem törzsében megadott **tárolónak** **léteznie kell**.
-   A biztonsági mentés folyamatban van, így **elkerülhető a szolgáltatásban történt felügyeleti változások** , például az SKU frissítése vagy a lefokozás, a tartománynév módosítása és egyebek.
-   A biztonsági másolat visszaállítását csak a létrehozás időpontja óta **30 napig garantáljuk** .
-   A szolgáltatás konfigurációjában (például az API-k, a házirendek és a fejlesztői portál megjelenése) **végrehajtott módosítások** **kizárják a biztonsági mentés folyamatát, és elvesznek**.
-   Ha az Azure Storage-fiók engedélyezve van a [tűzfalon][azure-storage-ip-firewall] , az **ügyfélnek engedélyeznie kell az** [Azure API Management vezérlési sík IP-címeinek][control-plane-ip-address] készletét a Storage-fiókban a biztonsági mentéshez vagy a szolgáltatásból való helyreállításhoz. Az Azure Storage-fiók bármely Azure-régióban lehet, kivéve a API Management szolgáltatást tartalmazó helyet. Ha például az API Management-szolgáltatás az USA nyugati régiójában található, akkor az Azure Storage-fiók az USA nyugati régiójában lehet, és az ügyfélnek meg kell nyitnia a vezérlési sík IP-13.64.39.16 API Management (az USA nyugati régiójának IP-címének ellenőrzése) a tűzfalon. Ennek az az oka, hogy az Azure Storage-ba irányuló kérések nem címfordítást egy nyilvános IP-címhez a számítási (Azure API Management Control Plant) Azure-régióban. A címfordítást a nyilvános IP-címhez kerül.
-   A [több eredetű erőforrás-megosztást (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) **nem** szabad engedélyezni az Azure Storage-fiók blob szolgáltatásában.
-   A visszaállított szolgáltatás **SKU** -jának **meg kell egyeznie** a visszaállítani kívánt biztonsági másolati szolgáltatás SKU-jának.

## <a name="what-is-not-backed-up"></a>Mi nem biztonsági mentés
-   Az elemzési jelentések létrehozásához használt **használati adatok** **nem szerepelnek** a biztonsági mentésben. Az [Azure API Management REST API][azure api management rest api] használatával rendszeres időközönként beolvashatja az elemzési jelentéseket a megőrzéshez.
-   [Egyéni tartomány TLS/SSL-](configure-custom-domain.md) tanúsítványok.
-   [Egyéni hitelesítésszolgáltatói tanúsítvány](api-management-howto-ca-certificates.md), amely tartalmazza az ügyfél által feltöltött köztes vagy főtanúsítványokat.
-   [Virtuális hálózat](api-management-using-with-vnet.md) integrációs beállításai.
-   [Felügyelt identitás](api-management-howto-use-managed-service-identity.md) konfigurálása.
-   [Azure monitor diagnosztika](api-management-howto-use-azure-monitor.md) Configuration.
-   [Protokollok és titkosítási](api-management-howto-manage-protocols-ciphers.md) beállítások.
-   [Fejlesztői portál](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) tartalma.

A szolgáltatás biztonsági másolatának elvégzéséhez használt gyakoriság a helyreállítási pontok céljára is hatással van. A lehető legkisebbre csökkentése érdekében javasoljuk, hogy a rendszeres biztonsági mentéseket és az igény szerinti biztonsági mentéseket a API Management szolgáltatás módosítása után végezze el.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő forrásokat a biztonsági mentési/visszaállítási folyamat különböző lépéseihez.

-   [Azure API Management-fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Az API Management biztonsági mentésének és helyreállításának automatizálása a Logic Apps használatával](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: konfiguráció](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     biztonsági mentése és visszaállítása _A Stuart által részletezett megközelítés nem felel meg a hivatalos útmutatásnak, de érdekes._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
