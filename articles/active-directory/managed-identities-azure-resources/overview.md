---
title: Azure-erőforrások felügyelt identitásai
description: Az Azure-erőforrások felügyelt identitásainak áttekintése.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/18/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2231d70e6c4368a7c896f9063b58cc97ee292f53
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682594"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Mik az Azure-erőforrások felügyelt identitások?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez.

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.

Az Azure-erőforrások felügyelt identitásai szolgáltatás ingyenesen használható az Azure Active Directoryval az Azure-előfizetésekben. Nincs további költség.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="terminology"></a>Terminológia

A következő kifejezések et az Azure-erőforrások dokumentációkészletének felügyelt identitásai ban használjuk:

- **Ügyfélazonosító** – az Azure AD által létrehozott egyedi azonosító, amely a kezdeti kiépítés során egy alkalmazáshoz és egyszerű szolgáltatáshoz van kötve.
- **Egyszerű azonosító** – a felügyelt identitás egyszerű szolgáltatásobjektumának objektumazonosítója, amely szerepköralapú hozzáférést biztosít egy Azure-erőforráshoz.
- **Azure Instance metaadat-szolgáltatás (IMDS)** – egy REST-végpont érhető el az Azure Resource Manager en keresztül létrehozott összes IaaS virtuális gépek. A végpont egy jól ismert, nem irányítható IP-címen érhető el (169.254.169.254), amely csak a virtuális gépen belül érhető el.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Hogyan működnek az Azure-erőforrások felügyelt identitásai?

A felügyelt identitásoknak két típusa létezik:

- A **rendszer által hozzárendelt felügyelt identitás** közvetlenül egy Azure-beli szolgáltatáspéldányon van engedélyezve. Az identitás engedélyezésekor az Azure létrehoz egy identitást a példány számára a példány előfizetése által megbízhatónak tekintett Azure AD-bérlőn. Az identitás létrehozása után a rendszer hozzárendeli a hitelesítő adatokat a példányon. A rendszer által hozzárendelt identitás életciklusa közvetlenül kötődik ahhoz az Azure-beli szolgáltatáspéldányhoz, amelyen engedélyezve van. A példány törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
- A **felhasználó által hozzárendelt felügyelt identitás** különálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve.

Belsőleg felügyelt identitások egy speciális típusú szolgáltatásnév, amely zárolva van, hogy csak azure-erőforrásokkal használható. A felügyelt identitás törlésekor a megfelelő egyszerű szolgáltatás automatikusan törlődik.
Emellett a felhasználó által hozzárendelt vagy rendszer-hozzárendelt identitás létrehozásakor a felügyelt identitás-erőforrás-szolgáltató (MSRP) belső tanúsítványt ad ki az adott identitásnak. 

A kód a felügyelt identitások használatával hozzáférési jogkivonatokat igényelhet az Azure AD-hitelesítést támogató szolgáltatásokhoz. Az Azure gondoskodik a szolgáltatáspéldány által használt hitelesítő adatok biztosításáról. 

## <a name="credential-rotation"></a>Hitelesítő adatok elforgatása
A hitelesítő adatok rotációját az Azure-erőforrást tároló erőforrás-szolgáltató szabályozza. A hitelesítő adatok alapértelmezett elforgatása 46 naponta történik. Az erőforrás-szolgáltatótól kell kérnie az új hitelesítő adatokat, így az erőforrás-szolgáltató 46 napnál tovább várhat.

Az alábbi ábrán a felügyelszolgáltatás-identitások az Azure-beli virtuális gépekkel (VM) való működése látható:

![Felügyeltszolgáltatás-identitások és Azure-beli virtuális gépek](media/overview/data-flow.png)

|  Tulajdonság    | Rendszerhez rendelt felügyelt identitás | Felhasználó által hozzárendelt felügyelt identitás |
|------|----------------------------------|--------------------------------|
| Létrehozás |  Egy Azure-erőforrás (például egy Azure-virtuális gép vagy az Azure App Service) részeként létrehozva | Önálló Azure-erőforrásként létrehozva |
| Életciklus | Megosztott életciklus az Azure-erőforrással, amelyhez a felügyelt identitás létrejön. <br/> A szülőerőforrás törlésekor a felügyelt identitás is törlődik. | Független életciklus. <br/> Explicit módon törölni kell. |
| Megosztás az Azure-erőforrások között | Nem osztható meg. <br/> Csak egyetlen Azure-erőforrással társítható. | Megosztható <br/> Ugyanazt a felhasználó által hozzárendelt felügyelt identitást több Azure-erőforráshoz is társíthatja. |
| Gyakori használati helyzetek | Egyetlen Azure-erőforrásban található számítási feladatok <br/> Olyan számítási feladatok, amelyekhez független identitásokra van szükség. <br/> Például egy egyetlen virtuális gépen futó alkalmazás | Olyan számítási feladatok, amelyek több erőforráson futnak, és amelyek egyetlen identitást oszthatnak meg. <br/> Olyan számítási feladatok, amelyek egy kiépítési folyamat részeként előzetes engedélyezést igényelnek egy biztonságos erőforráshoz. <br/> Olyan számítási feladatok, ahol az erőforrásokat gyakran újrahasznosítják, de az engedélyeknek konzisztenseknek kell maradniuk. <br/> Például egy olyan számítási feladat, ahol több virtuális gépnek kell elérnie ugyanazt az erőforrást |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a rendszer által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés a rendszer által hozzárendelt felügyelt identitás virtuális gépen történő engedélyezésére.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a virtuális gép identitása számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. Az Azure Resource Manager konfigurálja az identitást a virtuális gép en az Azure Instance metaadat-szolgáltatás identitásvégpontjának frissítésével az egyszerű szolgáltatásügyfél-azonosítóval és tanúsítvánnyal.

4. Miután a virtuális gép már rendelkezik identitással, a szolgáltatásnév-adatok használatával biztosítson hozzáférést a virtuális gépnek az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a virtuális gép szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

5. A virtuális gépen futó kód jogkivonatot kérhet az Azure Instance metaadatszolgáltatás-végpontjáról, amely csak a virtuális gépből érhető el:`http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.

7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Hogyan működnek együtt a felhasználó által hozzárendelt felügyelt identitások az Azure-beli virtuális gépekkel?

1. Az Azure Resource Managerbe érkezik egy kérés egy felhasználó által hozzárendelt felügyelt identitás létrehozására.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a felhasználó által hozzárendelt felügyelt identitás számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. Az Azure Resource Manager kap egy kérést, hogy konfigurálja a felhasználó által kijelölt felügyelt identitás egy virtuális gép, és frissíti az Azure-példány metaadat-szolgáltatás identitásvégponta a felhasználó által hozzárendelt felügyelt identitásszolgáltatás első ügyfélazonosítóés tanúsítvány.

4. Miután a felhasználó által hozzárendelt felügyelt identitás létrejött, a szolgáltatásnév-adatok használatával biztosítson hozzáférést az identitásnak az Azure-erőforrásokhoz. Az Azure Resource Manager meghívásához szerepköralapú hozzáférés-vezérlés (RBAC) használatával rendelje hozzá a megfelelő szerepkört a felhasználó által hozzárendelt identitás szolgáltatásnevéhez az Azure AD-ben. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

   > [!Note]
   > Ezt a lépést a 3. lépés előtt is végrehajthatja.

5. A virtuális gépen futó kód jogkivonatot kérhet az Azure Instance metaadat-szolgáltatás identitásának végpontjáról, amely csak a virtuális gépről érhető el:`http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - A client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez az érték akkor szükséges egyértelműsítés céljából, ha egyazon virtuális gépen több felhasználó által hozzárendelt identitás is létezik.
    - Az API version (API verziója) paraméter az Azure Instance Metadata szolgáltatás verzióját adja meg. Használja a(z) `api-version=2018-02-01` vagy újabb verziót.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hogyan használhatom az Azure-erőforrások felügyelt identitásait?

A különféle Azure-erőforrások felügyelt identitással való elérésének megismeréséért tekintse át az alábbi oktatóanyagokat.

> [!NOTE]
> A felügyelt identitásokról, köztük számos támogatott forgatókönyv részletes videóforgatókönyvéről a Microsoft Azure Resources-hez felügyelt [identitások megvalósítása](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) tanfolyamcímű webhelyen talál további információt.

Útmutató a felügyelt identitások használatához Windows rendszerű virtuális gépeken:

* [Az Azure Data Lake Store elérése](tutorial-windows-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-windows-vm-access-arm.md)
* [Az Azure SQL elérése](tutorial-windows-vm-access-sql.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-vm-windows-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-windows-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-windows-vm-access-nonaad.md)

Útmutató a felügyelt identitások használatához Linux rendszerű virtuális gépeken:

* [Hozzáférés az Azure Container Registryhez](../../container-registry/container-registry-authentication-managed-identity.md)
* [Az Azure Data Lake Store elérése](tutorial-linux-vm-access-datalake.md)
* [Az Azure Resource Manager elérése](tutorial-linux-vm-access-arm.md)
* [Az Azure Storage elérése hozzáférési kulcs használatával](tutorial-linux-vm-access-storage.md)
* [Az Azure Storage elérése közös hozzáférésű jogosultságkódok használatával](tutorial-linux-vm-access-storage-sas.md)
* [Nem Azure AD-erőforrások elérése az Azure Key Vaulttal](tutorial-linux-vm-access-nonaad.md)

Útmutató a felügyelt identitások használatához egyéb Azure-szolgáltatásokban:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry Tasks](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Data Factory](../../data-factory/data-factory-service-identity.md)


## <a name="what-azure-services-support-the-feature"></a>Mely Azure-szolgáltatások támogatják a szolgáltatást?<a name="which-azure-services-support-managed-identity"></a>

Az Azure-erőforrások felügyelt identitásai használatával hitelesítést végezhet az Azure AD-hitelesítést támogató szolgáltatásokban. Az Azure-erőforrások felügyelt identitásai szolgáltatást támogató Azure-szolgáltatások listájáért lásd [az Azure-erőforrások felügyelt identitásait támogató szolgáltatásokkal](services-support-msi.md) foglalkozó részt.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az Azure-erőforrások felügyelt identitásai szolgáltatással a következő rövid útmutatók segítségével:

* [Hozzáférés a Resource Managerhez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-windows-vm-access-arm.md)
* [Hozzáférés a Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-linux-vm-access-arm.md)
