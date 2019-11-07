---
title: Rövid útmutató – Analysis Services kiszolgáló létrehozása Azure Portalban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre gyorsan egy Azure Analysis Services Server-példányt a Azure Portal használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cfd624ce49add0f8fd04bfccca9d6b7c3f1f71bd
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573064"
---
# <a name="quickstart-create-a-server---portal"></a>Gyors útmutató: Kiszolgáló létrehozása – Portal

Ez a rövid útmutató bemutatja, hogyan hozhat létre erőforrást egy Azure Analysis Services-kiszolgálóhoz Azure-előfizetésében a portál használatával.

## <a name="prerequisites"></a>Előfeltételek 

* **Azure-előfizetés**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra 

[Bejelentkezés a portálra](https://portal.azure.com)


## <a name="create-a-server"></a>A kiszolgáló létrehozása

1. Kattintson **az + erőforrás létrehozása** > **Analytics** > **Analysis Services**elemre.

    ![Portál](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Az **Analysis Services** területen töltse ki a kötelező mezőket, majd válassza a **Létrehozás** lehetőséget.
   
   * **Kiszolgáló neve**: Írjon be egy egyedi nevet a kiszolgálóra való hivatkozáshoz. A kiszolgálónévnek kisbetűs karakterrel kell kezdődnie, és 3 – 128 kisbetűs karaktert és számot kell tartalmaznia. Szóközök és speciális karakterek használata nem engedélyezett.
   * **Előfizetés**: Válassza ki a kiszolgálóhoz társítani kívánt előfizetést.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoportok segítségével Azure-erőforrások gyűjteményét kezelheti. További információ: [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md).
   * **Hely**: Ez az Azure-adatközpont üzemelteti a kiszolgálót. A legnagyobb felhasználói bázisához legközelebb eső helyet válassza ki.
   * **Tarifacsomag**: Válasszon egy tarifacsomagot. Ha Ön éppen tesztel, és ezért a mintamodell-adatbázist kívánja telepíteni, válassza ki az ingyenes **D1** szintet. További tudnivalókat az [Azure Analysis Services díjszabását](https://azure.microsoft.com/pricing/details/analysis-services/) ismertető témakörben talál. 
   * **Rendszergazda**: Alapértelmezés szerint ezzel a fiókkal fog bejelentkezni. Másik fiókot az Azure Active Directoryból választhat.
   * **Biztonsági tárhely beállítása**: Nem kötelező. Ha már rendelkezik [tárfiókkal](../storage/common/storage-introduction.md), megadhatja azt a modelladatbázisok biztonsági másolatainak alapértelmezett tárolójaként. Később is megadhatja a [biztonsági mentés és visszaállítás](analysis-services-backup.md) beállításait.
   * **Tárkulcs lejárata**: Nem kötelező. Adjon meg egy tárkulcslejárati időszakot.

A kiszolgáló létrehozása általában nem egészen egy percet vesz igénybe. Ha a **Hozzáadás a Portalhoz** lehetőséget választotta, a portáljára lépve megtekintheti az új kiszolgálót. Az **Összes szolgáltatás** > **Analysis Services** területre lépve is ellenőrizheti, hogy készen áll-e a kiszolgáló. A kiszolgálók a 1200-es és magasabb kompatibilitási szinteken található táblázatos modelleket támogatják. A modell kompatibilitási szintje a Visual Studióban vagy a SSMS van megadva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a kiszolgálót. Nyissa meg a kiszolgáló **Áttekintés** lapját, majd kattintson a **Törlés** elemre. 

 ![Felesleges tartalmak törlése](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>További lépések
Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót az Azure-előfizetésében. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgálótűzfal konfigurálása – Portál](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
