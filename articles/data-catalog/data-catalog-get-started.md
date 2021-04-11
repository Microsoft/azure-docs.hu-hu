---
title: 'Gyors útmutató: Azure Data Catalog létrehozása'
description: Ez a rövid útmutató azt ismerteti, hogyan hozható létre Azure Data Catalog a Azure Portal használatával.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674733"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Gyors útmutató: Azure Data Catalog létrehozása

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Az Azure Data Catalog teljes körűen felügyelt felhőszolgáltatás, amely vállalati adategységek regisztrációs és felderítőrendszereként szolgál. A szolgáltatás részletes bemutatásáért olvassa el a [Mi az az Azure Data Catalog?](overview.md) című cikket.

Ez a rövid útmutató segítséget nyújt a Azure Data Catalog létrehozásának megkezdéséhez.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

> [!Note]
> Az Azure biztonsági követelményei miatt Azure Data Catalog enfored Transport Layer Security (TLS) 1,2. A TLS 1,0 és a TLS 1,1 le lett tiltva. Előfordulhat, hogy a regisztrációs eszköz futtatásakor hibák léptek fel, ha a gép nem frissült a TLS 1,2-hez. A TLS 1,2-es számítógép frissítéséhez lásd: [Transport Layer Security engedélyezése (1,2)](/mem/configmgr/core/plan-design/security/enable-tls-1-2) .

A kezdéshez a következőkre lesz szüksége:

* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetéssel.
* Saját [Azure Active Directory Bérlővel](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)kell rendelkeznie.

Data Catalog beállításához az Azure-előfizetés tulajdonosának vagy tulajdonosának kell lennie.

## <a name="create-a-data-catalog"></a>Adatkatalógus létrehozása

Szervezetenként (Azure Active Directory-tartományonként) mindössze egy adatkatalógust hozhat létre. Ezért ha a Azure Active Directory tartományhoz tartozó Azure-előfizetés tulajdonosa vagy társtulajdonosa már létrehozott egy katalógust, akkor nem hozhat létre katalógust, még akkor sem, ha több Azure-előfizetéssel rendelkezik. Ha szeretné megnézni, hogy az Ön Azure Active Directory-tartományában hozott-e már létre adatkatalógust valamelyik felhasználó, lépjen az [Azure Data Catalog honlapjára](http://azuredatacatalog.com), és ellenőrizze, hogy lát-e katalógust. Ha valaki már létrehozta a katalógust, ugorja át az itt látható eljárást, és folytassa a következő résszel.

1. Lépjen a [Azure Portal](https://portal.azure.com)  >  **erőforrás létrehozása** elemre, és válassza a **Data Catalog** lehetőséget.

    ![Azure Data Catalog létrehozás gomb](media/data-catalog-get-started/data-catalog-create.png)

2. Adja meg a adatkatalógus **nevét** , a használni kívánt **előfizetést** , a katalógus **helyét** és az **árképzési szintet**. Ezután kattintson a **Létrehozás** elemre.

3. Nyissa meg az [Azure Data Catalog kezdőlapját](http://azuredatacatalog.com), és kattintson az **Adatok közzététele** elemre.

   ![Azure Data Catalog – Adatok közzététele gomb](media/data-catalog-get-started/data-catalog-publish-data.png)

   Az első **lépések** lehetőség kiválasztásával a [Data Catalog szolgáltatás oldaláról](https://azure.microsoft.com/services/data-catalog) is elérheti Data Catalog kezdőlapját.

   ![Azure Data Catalog – marketinges kezdőlap](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Nyissa meg a **Beállítások** lapot.

    ![Azure Data Catalog – adatkatalógus létrehozása](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Bontsa ki a **díjszabást** , és ellenőrizze a Azure Data Catalog **kiadását** (ingyenes vagy standard).

    ![Azure Data Catalog – kiadás kiválasztása](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Ha a *standard* Edition csomagot az árképzési szintként választja, kibonthatja a **biztonsági csoportokat** , és engedélyezheti Active Directory biztonsági csoportok engedélyezését a Data Catalog eléréséhez és a számlázás automatikus beállításának engedélyezéséhez.

    ![Biztonsági csoportok Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Bontsa ki a **Katalógus felhasználói** csomópontot, majd kattintson a **Hozzáadás** gombra, és adja hozzá a kívánt felhasználókat az adatkatalógushoz. A rendszer automatikusan hozzáadja ezt a csoportot.

    ![Azure Data Catalog – felhasználók](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Ha a *standard* Edition csomagot az árképzési szintként választja, bontsa ki a **Szószedet-rendszergazdák** csomópontot, és kattintson a **Hozzáadás** gombra a Szószedet-rendszergazda felhasználók hozzáadásához. A rendszer automatikusan hozzáadja ezt a csoportot.

    ![Szószedet-rendszergazdák Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Bontsa ki a **Katalógus-rendszergazdák** csomópontot, majd kattintson a **Hozzáadás** gombra, így további rendszergazdákat rendelhet hozzá az adatkatalógushoz. A rendszer automatikusan hozzáadja ezt a csoportot.

    ![Azure Data Catalog – rendszergazdák](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Bontsa ki a **portál címét** , és adjon hozzá további szöveget, amely megjelenik a portál címében.

    ![Az Azure-beli adatCatalog-Portal címe](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Miután elvégezte a **Beállítások** lapot, a következő navigáljon a **közzétételi** lapra.

    ![Azure Data Catalog – létrehozva](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Adatkatalógus megkeresése az Azure Portalon

1. A böngésző egy másik lapján, illetve egy másik böngésző ablakában nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be ugyanazzal a fiókkal, amelyet az előző lépésnél az adatkatalógus létrehozásához használt.

2. Válassza **a minden szolgáltatás** lehetőséget, majd kattintson a **Data Catalog** elemre.

    ![Azure Data Catalog – Tallózás az Azure-ban](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Megjelenik a létrehozott adatkatalógus.

    ![Azure Data Catalog – katalóguslista megtekintése](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kattintson a korábban létrehozott katalógusra. Megjelenik a portál **Data Catalog** panele.

   ![Azure Data Catalog – panel a portálon](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Itt megtekintheti az adatkatalógus tulajdonságait, illetve frissítheti is őket. Kattintson például a **Tarifacsomag** elemre, és módosítsa a kiadást.

    ![Azure Data Catalog – tarifacsomag](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Data Catalog szervezete számára. Most már regisztrálhat adatforrásokat a adatkatalógusban.

> [!div class="nextstepaction"]
> [Adatforrások regisztrálása a Azure Data Catalogban](data-catalog-how-to-register.md)