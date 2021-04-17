---
title: Vészhelyreállítás megvalósítása biztonsági mentés és visszaállítás használatával a API Management
titleSuffix: Azure API Management
description: Megtudhatja, hogyan használhatja a biztonsági mentést és a visszaállítást vészhelyreállítás végrehajtásához az Azure API Management.
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
ms.openlocfilehash: 090eda3c3310a1b793733e37725c62758445d6b2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587320"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben

Az API-k Azure API Management-n keresztül történő közzétételével és kezelésével kihasználhatja a hibatűrést és az infrastruktúra olyan képességeit, amelyet egyébként manuálisan tervezne meg, implementálna és kezelne. Az Azure platform a lehetséges hibák jelentős hányadát csökkenti a költségek töredékéért.

A szolgáltatását tartalmazó régiót érintő rendelkezésre állási problémák API Management bármikor készen áll a szolgáltatás egy másik régióban való újbóli pótlására. A helyreállítási időre vonatkozó célkitűzéstől függően érdemes lehet egy készenléti szolgáltatást egy vagy több régióban tartani. A helyreállítási időkorrekta alapján megpróbálhatja szinkronban tartani a konfigurációjukat és tartalmaikat az aktív szolgáltatással. A szolgáltatás biztonsági mentési és visszaállítási funkciói biztosítják a vészhelyreállítási stratégia megvalósításához szükséges építőelemeket.

A biztonsági mentési és visszaállítási műveletek a szolgáltatás konfigurációjának API Management is replikálhatók az operatív környezetek között, például fejlesztési és előkészítési környezetek között. Legyen óvatos, hogy a rendszer átmásolja a futásidejű adatokat, például a felhasználókat és az előfizetéseket is, ami nem mindig kívánatos.

Ez az útmutató bemutatja, hogyan automatizálhatja a biztonsági mentési és visszaállítási műveleteket, és hogyan biztosíthatja a biztonsági mentési és visszaállítási kérések sikeres hitelesítését a Azure Resource Manager.

> [!IMPORTANT]
> A visszaállítási művelet nem módosítja a célszolgáltatás egyéni állomásnév-konfigurációját. Javasoljuk, hogy használja ugyanazt az egyéni állomásnevet és TLS-tanúsítványt az aktív és a készenléti szolgáltatásokhoz is, hogy a visszaállítási művelet befejezése után a forgalom egy egyszerű DNS CNAME-módosítással ismét a készenléti példányra irányítható legyen.
>
> A biztonsági mentési művelet nem rögzíti az elemzés panel elemzés panelének jelentéseiben használt, előre összesített Azure Portal.

> [!WARNING]
> Minden biztonsági mentés 30 nap után lejár. Ha a 30 napos lejárati idő lejárta után megkísérli visszaállítani a biztonsági mentést, a visszaállítás egy üzenettel `Cannot restore: backup expired` meghiúsul.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Hitelesítő Azure Resource Manager kérések

> [!IMPORTANT]
> A REST API biztonsági mentéshez és visszaállításhoz használt Azure Resource Manager és más hitelesítési mechanizmussal rendelkezik, mint a REST API-k az API Management kezeléséhez. Az ebben a szakaszban található lépések a kérések hitelesítését Azure Resource Manager ismertetik. További információ: [Hitelesítő adatok Azure Resource Manager kérelmekhez.](/rest/api/index)

Az erőforrásokon az erőforrásokkal kapcsolatos összes feladatot Azure Resource Manager kell hitelesíteni Azure Active Directory következő lépésekkel:

-   Adjon hozzá egy alkalmazást a Azure Active Directory bérlőhöz.
-   Állítsa be a hozzáadott alkalmazás engedélyeinek beállítását.
-   Szerezze be a kérések hitelesítésének jogkivonatát Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Új Azure Active Directory létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A API Management szolgáltatáspéldányt tartalmazó előfizetéssel lépjen a  Alkalmazásregisztrációk (Kezelés/Alkalmazásregisztrációk) **Azure Active Directory** (Azure Active Directory > lapra).

    > [!NOTE]
    > Ha Azure Active Directory alapértelmezett címtár nem látható a fiókja számára, lépjen kapcsolatba az Azure-előfizetés rendszergazdájával, és adja meg a fiókhoz szükséges engedélyeket.

3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **jobb** oldalon megjelenik a Létrehozás ablak. Itt adhatja meg az AAD-alkalmazáshoz kapcsolódó információkat.

4. Adjon nevet az alkalmazásnak.
5. Az alkalmazás típusaként válassza a Natív **lehetőséget.**
6. Adjon meg egy helyőrző URL-címet, például az `http://resources` **Átirányítási URI** mezőben, mivel ez egy kötelező mező, de az értéket később nem használja. Az alkalmazás mentéshez kattintson a jelölőnégyzetre.
7. Kattintson a **Létrehozás** lehetőségre.

### <a name="add-an-application"></a>Alkalmazás hozzáadása

1. Az alkalmazás létrehozása után kattintson az **API-engedélyek elemre.**
2. Kattintson **a + Engedély hozzáadása elemre.**
4. Nyomja le **a Microsoft API-k kiválasztása lehetőséget.**
5. Válassza az **Azure Szolgáltatáskezelés lehetőséget.**
6. Nyomja le a **Select (Kijelölés) gombot.**

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Képernyőkép az alkalmazásengedélyek hozzáadásáról."::: 

7. Kattintson **az újonnan hozzáadott** alkalmazás melletti Delegált engedélyek elemre, és jelölje be az Access Azure Service Management **(preview) (Hozzáférés az Azure szolgáltatáskezeléshez (előzetes verzió) jelölőnégyzetet.**

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Képernyőkép a delegált alkalmazásengedélyek hozzáadásáról.":::

8. Nyomja le a **Select (Kijelölés) gombot.**
9. Kattintson **az Engedélyek hozzáadása elemre.**

### <a name="configuring-your-app"></a>Az alkalmazás konfigurálása

A biztonsági mentést készítő és visszaállító API-k meghívása előtt be kell szereznie egy jogkivonatot. Az alábbi példa a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot használja a jogkivonat lekérése érdekében.

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

Cserélje `{tenant id}` le a , és `{application id}` `{redirect uri}` helyére a következő utasításokat:

1. Cserélje le a helyére a `{tenant id}` Azure Active Directory alkalmazás bérlőazonosítóját. Az azonosítót a Végpontok elemre **kattintva Alkalmazásregisztrációk**  ->  **el.**

    ![Végpontok][api-management-endpoint]

2. Cserélje le a értéket a lekért értékre a Beállítások `{application id}` **lapra való navigálással.**
3. Cserélje le a értéket a saját alkalmazás `{redirect uri}` **Átirányítási URI-k** Azure Active Directory értékére.

    Az értékek megszabadása után a példakódnak az alábbi példához hasonló jogkivonatot kell visszaadni:

    ![Jogkivonat][api-management-arm-token]

    > [!NOTE]
    > A jogkivonat egy bizonyos idő után lejárhat. Új jogkivonat létrehozásához hajtsa végre ismét a kódmintát.

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek hívása

A REST API-k az [Api Management szolgáltatás – Biztonsági mentés](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) és az API Management szolgáltatás – [Visszaállítás.](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

A következő szakaszokban ismertetett "biztonsági mentési és visszaállítási" műveletek hívása előtt állítsa be a REST-hívás engedélyezési kérelemfejlécét.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Biztonsági alkalmazásszolgáltatás API Management biztonsági

Egy szolgáltatás biztonsági API Management a következő HTTP-kérést:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

ahol:

-   `subscriptionId` – Annak az előfizetésnek az azonosítója, amely API Management a biztonságimenteni próbált szolgáltatáshoz
-   `resourceGroupName` – az Azure API Management erőforráscsoport neve
-   `serviceName` – annak a API Management szolgáltatásnak a neve, amelyről biztonsági másolatot készít, a létrehozásakor megadott
-   `api-version` – cserélje le a következőre: `2019-12-01`

A kérelem törzsében adja meg a cél Azure Storage-fiók nevét, a hozzáférési kulcsot, a blobtároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Állítsa a kérés `Content-Type` fejlécének értékét `application/json` értékre.

A biztonsági mentés egy hosszú ideig futó művelet, amely több mint egy percet is igénybe vehet. Ha a kérés sikeres volt, és megkezdődött a biztonsági mentési folyamat, egy fejléccel együtt kapott `202 Accepted` válaszállapot-kódot `Location` kap. A művelet állapotának kiderítését a fejlécben található URL-címre mutató GET `Location` kérésekkel kérheti le. Amíg a biztonsági mentés folyamatban van, továbbra is 202 Accepted (Elfogadva) állapotkódot kap. A `200 OK` válaszkódja a biztonsági mentési művelet sikeres befejezését jelzi.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Új API Management visszaállítása

Ha vissza API Management egy korábban létrehozott biztonsági másolatból, a következő HTTP-kérést kell létrehoznia:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

ahol:

-   `subscriptionId` – Annak az előfizetésnek az azonosítója, API Management a biztonsági mentést a következőbe állítja vissza:
-   `resourceGroupName` – annak az erőforráscsoportnak a neve, amely azt az Azure API Management-szolgáltatást tartalmazza, amelybe a biztonsági mentést visszaállítja
-   `serviceName` – annak a API Management szolgáltatásnak a neve, amelybe a létrehozáskor vissza lesz adva
-   `api-version` – cserélje le a következőre: `api-version=2019-12-01`

A kérés törzsében adja meg a biztonsági mentési fájl helyét. Ez azt jelenti, hogy adja hozzá az Azure Storage-fiók nevét, a hozzáférési kulcsot, a blobtároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Állítsa a kérés `Content-Type` fejlécének értékét `application/json` értékre.

A visszaállítás egy hosszú ideig futó művelet, amely akár 30 vagy több percet is igénybe vehet. Ha a kérés sikeres volt, és a visszaállítási folyamat megkezdődött, egy fejléccel együtt kapott `202 Accepted` válaszállapot-kódot `Location` kap. A fejlécben található URL-címre "GET" kérésekkel deríthető ki `Location` a művelet állapota. Amíg a visszaállítás folyamatban van, továbbra is a 202 Accepted (Elfogadva) állapotkódot kapja. A válaszkód `200 OK` a visszaállítási művelet sikeres befejezését jelzi.

> [!IMPORTANT]
> **A helyre** visszaállított szolgáltatás termékváltozatának meg kell egyeznie a visszaállított biztonsági mentést eltárolt szolgáltatás termékváltozatának. 
>
> **A** szolgáltatás konfigurációján (például API-kon, szabályzaton, fejlesztői portálon való megjelenés) a visszaállítási művelet közben végrehajtott módosításokat felül lehet **írni.**

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> A biztonsági mentési és visszaállítási műveletek a PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) és [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) parancsaival is elvégezhetők.

## <a name="constraints-when-making-backup-or-restore-request"></a>Korlátozások a biztonsági mentési vagy visszaállítási kérések készítéskor

-   **A** kérelem törzsében megadott tárolónak **léteznie kell.**
-   Amíg a biztonsági mentés folyamatban **van,** kerülje a szolgáltatás felügyeleti módosításait, például a termékváltozatok frissítését vagy visszalépését, a tartománynév változását stb.
-   A biztonsági másolatok visszaállítása a létrehozás pillanata óta csak **30** napig garantált.
-   **Előfordulhat,** hogy a biztonsági mentési művelet közben a szolgáltatás konfigurációján (például API-kon, szabályzaton és megjelenésen) végrehajtott módosítások ki lesznek zárva a biztonsági mentésből, és **elvesznek.**
-   Ha az Azure [][azure-storage-ip-firewall] Storage-fiók engedélyezve van  a tűzfalon, az ügyfélnek engedélyeznie kell az Azure API Management vezérlősík IP-címkészletét a tárfiókon a biztonsági mentéshez vagy a visszaállításhoz a munkához. [][control-plane-ip-address] Az Azure Storage-fiók bármely Azure-régióban lehet, kivéve azt, ahol API Management szolgáltatás található. Ha például az API Management szolgáltatás az USA nyugati régiója, akkor az Azure Storage-fiók lehet az USA 2. nyugati régiója, és az ügyfélnek meg kell nyitnia a vezérlő 13.64.39.16 (API Management vezérlősík IP-címe az USA nyugati régiója) vezérlőt a tűzfalon. Ennek az az oka, hogy az Azure Storage-hoz való kérések nem egy nyilvános IP-címre vannak SNAT-n átvéve a Compute (Azure API Management vezérlősík) által ugyanabban az Azure-régióban. A régiók közötti tárolási kérések A nyilvános IP-címre lesznek SNAT-vé va.
-   [Az eredetközi erőforrás-megosztás (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) **nem** engedélyezhető az Azure Storage-fiók blobszolgáltatásában.
-   **A helyre** visszaállított szolgáltatás termékváltozatának meg kell egyeznie a visszaállított biztonsági mentést eltárolt szolgáltatás termékváltozatának. 

## <a name="what-is-not-backed-up"></a>Miről nem történik biztonságimentás
-   **Az elemzési** jelentések létrehozásához használt használati **adatok nem szerepelnek a** biztonsági másolatban. Az [Azure API Management REST API][azure api management rest api] rendszeres időközönként lekéri az elemzési jelentéseket a biztonság érdekében.
-   [Egyéni tartományi TLS-/SSL-tanúsítványok.](configure-custom-domain.md)
-   [Egyéni hitelesítésszolgáltatói tanúsítvány,](api-management-howto-ca-certificates.md)amely tartalmazza az ügyfél által feltöltött köztes vagy főtanúsítványokat.
-   [Virtuális hálózat integrációs](api-management-using-with-vnet.md) beállításai.
-   [Felügyelt identitás konfigurálása.](api-management-howto-use-managed-service-identity.md)
-   [Azure Monitor diagnosztika](api-management-howto-use-azure-monitor.md) Konfigurációs.
-   [Protokollok és titkosítási](api-management-howto-manage-protocols-ciphers.md) beállítások.
-   [A fejlesztői portál](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) tartalma.

A szolgáltatási biztonsági mentések gyakoriságát a helyreállítási időkorrekta befolyásolja. A minimálisra csökkentése érdekében javasoljuk a rendszeres biztonsági mentések és igény szerinti biztonsági mentések elvégzését, miután végrehajtotta a API Management szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az alábbi erőforrásokat a biztonsági mentési/visszaállítási folyamat különböző útmutatóiért.

-   [Azure-beli API Management fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Az API Management biztonsági mentésének és helyreállításának automatizálása a Logic Apps használatával](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Biztonsági mentés és konfiguráció visszaállítása](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     _A The Point által részletezett megközelítés nem felel meg a hivatalos útmutatónak, de érdekes._

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
