---
title: Felügyelt alkalmazások közzététele a portálon keresztül
description: Bemutatja, hogyan használható a Azure Portal egy olyan Azure-beli felügyelt alkalmazás létrehozásához, amely a szervezet tagjai számára készült.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651721"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Service Catalog-alkalmazás közzététele Azure Portal

A Azure Portal a szervezet tagjainak szánt [felügyelt alkalmazások](overview.md) közzétételére használható. Az informatikai részleg közzétehet például olyan felügyelt alkalmazásokat, amelyek biztosítják a vállalati szabványoknak való megfelelést. Ezeket a felügyelt alkalmazásokat a szolgáltatáskatalóguson keresztül lehet elérni az Azure Marketplace helyett.

## <a name="prerequisites"></a>Előfeltételek

A felügyelt alkalmazások közzétételekor meg kell adnia egy identitást az erőforrások kezeléséhez. Javasoljuk, hogy Azure Active Directory felhasználói csoportot határozzon meg. Azure Active Directory felhasználói csoport létrehozásához tekintse meg [a csoport létrehozása és a tagok hozzáadása Azure Active Directory-ben](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című témakört. 

A felügyelt alkalmazás definícióját tartalmazó. zip-fájlnak URI-n keresztül kell elérhetőnek lennie. Javasoljuk, hogy töltse fel a. zip fájlt egy Storage-blobba. 

## <a name="create-managed-application-with-portal"></a>Felügyelt alkalmazás létrehozása a portálon

1. A bal felső sarokban válassza az **+ új** lehetőséget.

   ![Új szolgáltatás](./media/publish-portal/new.png)

1. Keressen rá a **Service Catalog** kifejezésre.

1. Az eredmények között görgessen addig, amíg meg nem találja a **Service Catalog felügyelt alkalmazás definícióját**. Válassza ki.

   ![Felügyelt alkalmazás-definíciók keresése](./media/publish-portal/select-managed-apps-definition.png)

1. A **Létrehozás** gombra kattintva elindíthatja a felügyelt alkalmazás definíciójának létrehozásának folyamatát.

   ![Felügyelt alkalmazás definíciójának létrehozása](./media/publish-portal/create-definition.png)

1. Adja meg a név, a megjelenítendő név, a leírás, a hely, az előfizetés és az erőforráscsoport értékét. A Package file URI esetében adja meg a létrehozott zip-fájl elérési útját.

   ![Értékek megadása](./media/publish-portal/fill-application-values.png)

1. A hitelesítési és a zárolási szint szakaszban válassza az **Engedélyezés hozzáadása** lehetőséget.

   ![Engedélyezés hozzáadása](./media/publish-portal/add-authorization.png)

1. Válasszon ki egy Azure Active Directory csoportot az erőforrások kezeléséhez, majd kattintson **az OK gombra**.

   ![Engedélyezési csoport hozzáadása](./media/publish-portal/add-auth-group.png)

1. Ha megadta az összes értéket, válassza a **Létrehozás** lehetőséget.

   ![Felügyelt alkalmazás létrehozása](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* A felügyelt alkalmazások példáit lásd: [Sample projects for Azure Managed Applications](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.