---
title: Azure-erőforrások felügyelt identitásai
description: Az Azure-erőforrások felügyelt identitásainak áttekintése.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/05/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c49ded056c642fa91331b7cc98d18da34d9c73a6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504352"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Mik azok az Azure-erőforrások felügyelt identitásai?

A fejlesztők számára a gyakori kihívás a különböző összetevők közötti kommunikáció biztosításához használt titkok és hitelesítő adatok kezelése. A felügyelt identitások nem szükségesek a fejlesztők számára a hitelesítő adatok kezeléséhez. A felügyelt identitások a Azure Active Directory (Azure AD) hitelesítést támogató erőforrásokhoz való csatlakozáskor használandó identitást biztosítanak. Az alkalmazások a felügyelt identitás használatával szerezhetnek be Azure AD-jogkivonatokat. Előfordulhat például, hogy egy alkalmazás felügyelt identitással fér hozzá olyan erőforrásokhoz, mint [Azure Key Vault](../../key-vault/general/overview.md) , ahol a fejlesztők biztonságos módon tárolhatják a hitelesítő adatokat, vagy hozzáférhetnek a Storage-fiókokhoz.

Mit használhat felügyelt identitás?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

A felügyelt identitások használatának előnyei a következők:

- Nem kell kezelnie a hitelesítő adatokat. A hitelesítő adatok még nem érhetők el.
- A felügyelt identitások használatával bármely olyan erőforráson hitelesíthető, amely támogatja az [Azure Active Directory hitelesítést](../authentication/overview-authentication.md) , beleértve a saját alkalmazásait is.
- A felügyelt identitások további díjak nélkül is használhatók.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="managed-identity-types"></a>Felügyelt identitások típusai

A felügyelt identitásoknak két típusa létezik:

- **Rendszer által hozzárendelt** Néhány Azure-szolgáltatás lehetővé teszi, hogy egy felügyelt identitást közvetlenül egy szolgáltatási példányon engedélyezzen. Ha engedélyezi a rendszer által hozzárendelt felügyelt identitást, az Azure AD-ben létrejön egy identitás, amely az adott szolgáltatási példány életciklusához van kötve. Így az erőforrás törlése után az Azure automatikusan törli az identitást. A tervezés szerint csak az Azure-erőforrások használhatják ezt az identitást tokenek igénylésére az Azure AD-ből.
- **Felhasználó által hozzárendelt** Felügyelt identitást is létrehozhat önálló Azure-erőforrásként. [Létrehozhat egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-portal.md) , és hozzárendelheti egy Azure-szolgáltatás egy vagy több példányához is. A felhasználó által hozzárendelt felügyelt identitások esetében az identitást az azt használó erőforrásoktól függetlenül kezeli a rendszer. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

Az alábbi táblázat a felügyelt identitások két típusa közötti különbségeket mutatja.

|  Tulajdonság    | Rendszer által hozzárendelt felügyelt identitás | Felhasználó által hozzárendelt felügyelt identitás |
|------|----------------------------------|--------------------------------|
| Létrehozás |  Azure-erőforrás részeként létrehozva (például egy Azure-beli virtuális gép vagy Azure App Service) | Önálló Azure-erőforrásként létrehozva |
| Életciklus | Megosztott életciklus az Azure-erőforrással, amelyet a felügyelt identitás létrehoz. <br/> A fölérendelt erőforrás törlésekor a felügyelt identitás is törlődik. | Független életciklus. <br/> Explicit módon törölni kell. |
| Megosztás az Azure-erőforrások között | Nem osztható meg. <br/> Csak egyetlen Azure-erőforráshoz társítható. | Megosztható <br/> Ugyanaz a felhasználó által hozzárendelt felügyelt identitás több Azure-erőforráshoz is társítható. |
| Gyakori használati helyzetek | Egyetlen Azure-erőforráson belül található munkaterhelések <br/> Olyan munkaterhelések, amelyekhez független identitásokra van szükség. <br/> Például egyetlen virtuális gépen futó alkalmazás | Több erőforráson futó munkaterhelések, amelyek egyetlen identitást is megoszthatnak. <br/> Olyan munkaterhelések, amelyeknek a létesítési folyamat részeként meg kell adni a biztonságos erőforráshoz való előzetes engedélyezést. <br/> Olyan munkaterhelések, amelyekben az erőforrások gyakran újrahasznosíthatók, de az engedélyek konzisztensek maradnak. <br/> Például egy olyan munkaterhelés, amelyben több virtuális gépnek is hozzá kell férnie ugyanahhoz az erőforráshoz |

>[!IMPORTANT]
>Függetlenül attól, hogy a felügyelt identitás milyen típusú identitást választott, egy speciális típusú szolgáltatásnév, amely csak az Azure-erőforrásokkal használható. A felügyelt identitás törlése után a rendszer automatikusan eltávolítja a megfelelő egyszerű szolgáltatást.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hogyan használhatom az Azure-erőforrások felügyelt identitásait?

![néhány példa arra, hogy a fejlesztők hogyan használhatják a felügyelt identitásokat, hogy a hitelesítési adatok kezelése nélkül hozzáférjenek az erőforrásokhoz a kódból.](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Mely Azure-szolgáltatások támogatják a szolgáltatást?<a name="which-azure-services-support-managed-identity"></a>

Az Azure-erőforrások felügyelt identitásai használatával hitelesítést végezhet az Azure AD-hitelesítést támogató szolgáltatásokban. Az Azure-erőforrások felügyelt identitásai szolgáltatást támogató Azure-szolgáltatások listájáért lásd [az Azure-erőforrások felügyelt identitásait támogató szolgáltatásokkal](./services-support-managed-identities.md) foglalkozó részt.

## <a name="next-steps"></a>Következő lépések

* [Hozzáférés a Resource Managerhez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-windows-vm-access-arm.md)
* [Hozzáférés a Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-linux-vm-access-arm.md)
* [Felügyelt identitások használata App Service és Azure Functions](../../app-service/overview-managed-identity.md)
* [Felügyelt identitások használata az Azure Container Instancesszel](../../container-instances/container-instances-managed-identity.md)
* [Felügyelt identitások megvalósítása Microsoft Azure erőforrásokhoz](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).