---
title: Rövid útmutató – Azure CDN profil és végpont létrehozása
description: Ez a rövid útmutató egy új CDN-profil és -végpont létrehozásán keresztül ismerteti az Azure CDN aktiválását.
author: asudbring
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e8b3b5c28d00b4d7c81fa3e245113119ae3134ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555703"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Gyors útmutató: Azure CDN-profil és -végpont létrehozása

Ebben a rövid útmutatóban egy új CDN-profil létrehozásával engedélyezi az Azure Content Delivery Network (CDN) létrehozását, amely egy vagy több CDN-végpont gyűjteménye. Miután létrehozott egy profilt és egy végpontot, elindíthatja a tartalomszolgáltatást az ügyfelek felé.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy *cdnstorageacct123* nevű Azure Storage-fiók, amelyet a forrás állomásneve használ. A követelmény teljesítéséhez tekintse meg az [Azure Storage-fiók integrálása Azure CDNkal](cdn-create-a-storage-account-with-cdn.md)című témakört.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Új CDN-végpont létrehozása

Miután létrehozott egy CDN-profilt, a használatával végpontot hozhat létre.

1. Az Azure Portalon válassza ki a létrehozott CDN-profilt az irányítópulton. Ha nem találja, megnyithatja azt az erőforráscsoportot, amelyben létrehozta, vagy használhatja a portál felső részén található keresősávt, adja meg a profil nevét, és válassza ki a profilt az eredmények közül.
   
1. A CDN-profil lapon válassza a **+ végpont** lehetőséget.
   
    ![CDN-profil](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Megjelenik a **Végpont hozzáadása** panel.

3. Adja meg a következő beállítási értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Név** | Adja meg a *CDN-Endpoint-123* értéket a végponti állomásnévhez. Ennek a névnek globálisan egyedinek kell lennie az Azure-ban. Ha már használatban van, adjon meg másik nevet. A rendszer ezt a nevet használja a gyorsítótárazott erőforrások eléréséhez a tartomány _&lt; végpontjának neve &gt;_. azureedge.net.|
    | **Forrás típusa** | Válassza a **tárterület** lehetőséget. | 
    | **Forrás gazdaneve** | Válassza ki a legördülő listából a használni kívánt Azure Storage-fiók állomásnevét, például *cdnstorageacct123.blob.Core.Windows.net*. |
    | **Forrás elérési útvonala** | Hagyja üresen. |
    | **Forrás állomásfejléce** | Hagyja meg az alapértelmezett értéket (amely a Storage-fiók állomásneve). |  
    | **Protokoll** | Hagyja bejelölve az alapértelmezett **HTTP-** és **HTTPS-** beállításokat. |
    | **Forrásport** | Hagyja meg az alapértelmezett portértéket. | 
    | **Optimalizálva a következőre:** | Hagyja meg az alapértelmezett kijelölést, az **Általános webes kézbesítést**. |

    ![Végpont hozzáadása panel](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Új végpont létrehozásához kattintson a **Hozzáadás** gombra. A végpont a létrehozás után megjelenik a profil végpontjainak listájában.
    
   ![CDN-végpont](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   A végpont propagálásához szükséges idő a profil létrehozásakor kiválasztott árképzési szinttől függ. A **standard szintű Akamai** általában egy percen belül befejeződik, a **standard Microsoft** 10 perc, a **standard Verizon** és a **prémium Verizon** pedig akár 30 percig is eltarthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A korábbi lépésekben létrehozott egy CDN-profilt és egy végpontot egy erőforráscsoportban. Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan adhat egyéni tartományt a végpontjához. Ugyanakkor ha a jövőben nem várható ezen erőforrások használata, törölheti őket az erőforráscsoport törlésével, így elkerülheti a további díjakat:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **CDNQuickstart-RG** elemet.

2. Az **erőforráscsoport** lapon válassza az **erőforráscsoport törlése** elemet, írja be a *CDNQuickstart-RG* karakterláncot a szövegmezőbe, majd válassza a **Törlés** lehetőséget. Ez a művelet törli az ebben a rövid útmutatóban létrehozott erőforráscsoportot, profilt és végpontot.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: a CDN használata a statikus tartalmak kiszolgálására egy webalkalmazásból](cdn-add-to-web-app.md)
