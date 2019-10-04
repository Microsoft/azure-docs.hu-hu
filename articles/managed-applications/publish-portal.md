---
title: Közzététel az Azure-beli felügyelt alkalmazás a portálon keresztül |} A Microsoft Docs
description: Bemutatja, hogyan használható az Azure hozzon létre egy Azure-portál felügyelt alkalmazást, amely a szervezete tagjainak szól.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: f27d30d4709fbf373c8458629d0c8c5af4333acf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297020"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Az Azure portal használatával egy szolgáltatáskatalógusban elérhető alkalmazás közzététele

Az Azure portal segítségével közzététele [által felügyelt alkalmazások](overview.md) , amely a szervezet tagjai célokra szolgálnak. Az informatikai részleg közzétehet például olyan felügyelt alkalmazásokat, amelyek biztosítják a vállalati szabványoknak való megfelelést. Ezeket a felügyelt alkalmazásokat a szolgáltatáskatalóguson keresztül lehet elérni az Azure Marketplace helyett.

## <a name="prerequisites"></a>Előfeltételek

Ha egy felügyelt alkalmazás közzététele meg kell adnia az identitás, az erőforrások kezeléséhez. Azt javasoljuk, hogy megad egy Azure Active Directorybeli felhasználói csoportnak. Az Azure Active Directory felhasználói csoport létrehozásához lásd: [hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

A .zip fájlt, amely tartalmazza a felügyelt alkalmazás definíciójának keresztül egy URI-t elérhetőnek kell lennie. Azt javasoljuk, hogy egy storage-blobba feltölteni a .zip fájl. 

## <a name="create-managed-application-with-portal"></a>Felügyelt alkalmazás létrehozása a portállal

1. A bal felső sarokban, válassza ki **+ új**.

   ![Új szolgáltatás](./media/publish-portal/new.png)

1. Keresse meg **szolgáltatáskatalógus**.

1. Az eredmények között, amíg meg nem találja görgessen **Szolgáltatáskatalógusbeli felügyelt alkalmazás definíciók**. Válassza ki azt.

   ![Felügyeltalkalmazás-definíciók keresése](./media/publish-portal/select-managed-apps-definition.png)

1. Válassza ki **létrehozás** a felügyelt alkalmazás definíciójának létrehozása a folyamat elindításához.

   ![Felügyelt alkalmazás definíciójának létrehozása](./media/publish-portal/create-definition.png)

1. Adja meg nevet, megjelenítendő neve, leírása, hely, előfizetés és erőforráscsoport. Csomagfájl URI-t adja meg a létrehozott zip-fájl elérési útját.

   ![Adjon meg értékeket](./media/publish-portal/fill-application-values.png)

1. Ha a hitelesítési és zárolási szint szakaszt, válassza ki a **hozzáadása engedélyezési**.

   ![Engedélyezés hozzáadása](./media/publish-portal/add-authorization.png)

1. Válasszon ki egy Azure Active Directoryban felügyelheti az erőforrásokat, és válassza ki a **OK**.

   ![Engedélyezési csoport hozzáadása](./media/publish-portal/add-auth-group.png)

1. Ha megadta az összes értéket, válassza ki a **létrehozás**.

   ![Felügyelt alkalmazás létrehozása](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Felügyelt alkalmazás példákért lásd [mintaprojektekkel az Azure által felügyelt alkalmazások](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.