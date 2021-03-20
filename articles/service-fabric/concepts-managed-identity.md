---
title: Felügyelt identitások az Azure-hoz
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure-hoz a Service Fabric használatával.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881720"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Felügyelt identitások használata az Azure-hoz a Service Fabric

A felhőalapú alkalmazások kiépítése során gyakori kihívás a hitelesítő adatok biztonságos kezelése a kódban a különböző szolgáltatásokhoz való hitelesítéshez anélkül, hogy azokat helyileg egy fejlesztői munkaállomáson vagy a forrás vezérlőelemen kellene menteni. A *felügyelt identitások az Azure* -ban a Azure Active Directory (Azure ad) összes erőforrása esetében a probléma megoldásához automatikusan felügyelt identitásokat biztosítanak az Azure ad-n belül. A szolgáltatás identitásával bármely olyan szolgáltatás hitelesíthető, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt is, a kódban tárolt hitelesítő adatok nélkül.

Az Azure- *erőforrások felügyelt identitásai* ingyenesek az Azure ad-vel az Azure-előfizetésekhez. Nincs további költség.

> [!NOTE]
> A *felügyelt identitások az Azure* -ban a korábbi Managed Service Identity (MSI) néven ismert szolgáltatás neve.

## <a name="concepts"></a>Alapelvek

Az Azure-hoz készült felügyelt identitások számos kulcsfontosságú koncepción alapulnak:

- **Ügyfél-azonosító** – az Azure ad által generált egyedi azonosító, amely egy alkalmazáshoz és egyszerű szolgáltatáshoz van kötve a kezdeti kiépítés során (lásd az [alkalmazás azonosítóját](../active-directory/develop/developer-glossary.md#application-id-client-id)is).

- **Elsődleges azonosító** – a felügyelt identitáshoz tartozó egyszerű szolgáltatásnév, amely az Azure-erőforrásokhoz való szerepköralapú hozzáférés biztosítására szolgál.

- **Egyszerű szolgáltatásnév** – egy Azure Active Directory objektum, amely egy adott bérlő HRE-alkalmazásának vetületét jelöli (az [egyszerű szolgáltatásnév](../active-directory/develop/developer-glossary.md#service-principal-object)is látható).

A felügyelt identitásoknak két típusa létezik:

- Egy **rendszerhez rendelt felügyelt identitás** közvetlenül van engedélyezve egy Azure-beli szolgáltatási példányon.  A rendszerhez rendelt identitás életciklusa egyedi az Azure-beli szolgáltatás azon példányán, amelyen engedélyezve van.
- A **felhasználó által hozzárendelt felügyelt identitás** különálló Azure-erőforrásként jön létre. Az identitás hozzárendelhető egy vagy több Azure-szolgáltatási példányhoz, és az adott példányok életciklusa külön felügyelhető.

A felügyelt identitások típusai közötti különbség megismeréséhez lásd: [hogyan működnek az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric alkalmazások támogatott forgatókönyvei

A Service Fabric felügyelt identitásai csak az Azure-ban telepített Service Fabric-fürtökön támogatottak, és csak az Azure-erőforrásként üzembe helyezett alkalmazásokhoz; egy nem Azure-erőforrásként telepített alkalmazás nem rendelhető hozzá identitáshoz. Elméletileg a felügyelt identitások támogatása az Azure Service Fabric-fürtben két fázisból áll:

1. Rendeljen hozzá egy vagy több felügyelt identitást az alkalmazás-erőforráshoz; egy alkalmazáshoz egyetlen rendszer által hozzárendelt identitás és/vagy 32 felhasználó által hozzárendelt identitás rendelhető hozzá.

2. Az alkalmazás definícióján belül leképezheti az alkalmazáshoz rendelt identitások egyikét az alkalmazást alkotó egyes szolgáltatásokhoz.

Egy alkalmazás rendszer által hozzárendelt identitása egyedi az adott alkalmazáshoz; a felhasználó által hozzárendelt identitás önálló erőforrás, amely több alkalmazáshoz is hozzárendelhető. Egy alkalmazásban az alkalmazás egyetlen identitása (akár rendszer által hozzárendelt vagy felhasználó által hozzárendelt) is hozzárendelhető több szolgáltatáshoz, de minden egyes szolgáltatáshoz csak egy identitás rendelhető hozzá. Végül a szolgáltatást explicit módon hozzá kell rendelni ahhoz, hogy hozzáférjenek ehhez a szolgáltatáshoz. Valójában az alkalmazásnak az összetevő-szolgáltatásaihoz való hozzárendelése lehetővé teszi az alkalmazáson belüli elkülönítést – a szolgáltatás csak a hozzá rendelt identitást használhatja.  

A szolgáltatás jelenleg a következő forgatókönyveket támogatja:

- Új alkalmazás üzembe helyezése egy vagy több szolgáltatással és egy vagy több hozzárendelt identitással

- Egy vagy több felügyelt identitás kiosztása egy meglévő (Azure-beli) alkalmazáshoz az Azure-erőforrások elérése érdekében

A következő forgatókönyvek nem támogatottak vagy nem ajánlottak; vegye figyelembe, hogy ezeket a műveleteket nem lehet letiltani, de az alkalmazásokban kimaradást eredményezhet:

- Egy alkalmazáshoz rendelt identitások eltávolítása vagy módosítása; Ha módosításokat kell végeznie, külön központi telepítéseket kell elküldenie, hogy először új identitás-hozzárendelést adjon hozzá, majd egy korábban hozzárendelt egyet. Az identitás meglévő alkalmazásból való eltávolítása nem kívánt hatással lehet, például nem frissíthető állapotban hagyja az alkalmazást. Biztonságosan törölheti az alkalmazást, ha szükség van az identitás eltávolítására; vegye figyelembe, hogy ez törli az alkalmazáshoz társított rendszer-hozzárendelt identitást (ha van ilyen), és eltávolítja az alkalmazáshoz rendelt felhasználó által hozzárendelt identitásokkal rendelkező társításokat.

- A felügyelt identitások Service Fabric támogatása jelenleg nincs integrálva a [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication).

## <a name="next-steps"></a>Következő lépések

- [Új Azure Service Fabric-fürt üzembe helyezése felügyelt identitások támogatásával](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Felügyelt identitás támogatásának engedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
- [Alkalmazás-titkok deklarálása és használata KeyVaultReferences-ként](./service-fabric-keyvault-references.md)