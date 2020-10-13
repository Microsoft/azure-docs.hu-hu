---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649084"
---
Azure Active Directory (Azure AD) felügyelt identitása lehetővé teszi, hogy az alkalmazás könnyedén hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. További információ az Azure AD-beli felügyelt identitásokról: [felügyelt identitások az Azure-erőforrásokhoz](../articles/active-directory/managed-identities-azure-resources/overview.md).

Az alkalmazás két típusú identitást biztosíthat:

- A **rendszer által hozzárendelt identitás** az alkalmazáshoz van kötve, és törlődik, ha az alkalmazás törölve lett. Egy alkalmazásnak csak egy rendszerhez rendelt identitása lehet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amelyet az alkalmazáshoz rendelhet hozzá. Egy alkalmazáshoz több felhasználó által hozzárendelt identitás is tartozhat.