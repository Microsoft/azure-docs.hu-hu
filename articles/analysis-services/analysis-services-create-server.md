---
title: Rövid útmutató – Új Analysis Services létrehozása a Azure Portal | Microsoft Docs
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Analysis Services-kiszolgálópéldányt a Azure Portal.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: 78e4e008a4c4dfcee1e4f0a11b519e0a2bbf5f56
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535075"
---
# <a name="quickstart-create-a-server---portal"></a>Gyors útmutató: Kiszolgáló létrehozása – Portal

Ez a rövid útmutató bemutatja, hogyan hozhat létre erőforrást egy Azure Analysis Services-kiszolgálóhoz Azure-előfizetésében a portál használatával.

## <a name="prerequisites"></a>Előfeltételek 

* **Azure-előfizetés:** Fiók [létrehozásához látogasson el az Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/) webhelyre.
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia. Emellett be kell jelentkeznie az Azure-ba az adott Azure Active Directoryban található fiókkal. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra 

[Bejelentkezés a portálra](https://portal.azure.com)


## <a name="create-a-server"></a>A kiszolgáló létrehozása

1. Kattintson **az + Erőforrás-elemzés létrehozása**  >  **elemre**  >  **Analysis Services.**

    ![Portál](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Az **Analysis Services** területen töltse ki a kötelező mezőket, majd válassza a **Létrehozás** lehetőséget.
   
   * **Kiszolgáló neve**: Írjon be egy egyedi nevet a kiszolgálóra való hivatkozáshoz. A kiszolgáló nevének kisbetűvel kell kezdődnie, és 3–128 kisbetűt és számot kell tartalmaznia. Szóközök és speciális karakterek nem engedélyezettek.
   * **Előfizetés**: Válassza ki a kiszolgálóhoz társítani kívánt előfizetést.
   * **Erőforráscsoport**: Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoportok segítségével Azure-erőforrások gyűjteményét kezelheti. További információ: [erőforráscsoportok](../azure-resource-manager/management/overview.md).
   * **Hely**: Ez az Azure-adatközpont üzemelteti a kiszolgálót. A legnagyobb felhasználói bázisához legközelebb eső helyet válassza ki.
   * **Tarifacsomag**: Válasszon egy tarifacsomagot. Ha Ön éppen tesztel, és ezért a mintamodell-adatbázist kívánja telepíteni, válassza ki az ingyenes **D1** szintet. További tudnivalókat az [Azure Analysis Services díjszabását](https://azure.microsoft.com/pricing/details/analysis-services/) ismertető témakörben talál. 
   * **Rendszergazda**: Alapértelmezés szerint ezzel a fiókkal fog bejelentkezni. Másik fiókot az Azure Active Directoryból választhat.
   * **Biztonsági tárhely beállítása**: Nem kötelező. Ha már rendelkezik [tárfiókkal](../storage/common/storage-introduction.md), megadhatja azt a modelladatbázisok biztonsági másolatainak alapértelmezett tárolójaként. Később is megadhatja a [biztonsági mentés és visszaállítás](analysis-services-backup.md) beállításait.
   * **Tárkulcs lejárata**: Nem kötelező. Adjon meg egy tárkulcslejárati időszakot.

A kiszolgáló létrehozása általában nem egészen egy percet vesz igénybe. Ha a **Hozzáadás a Portalhoz** lehetőséget választotta, a portáljára lépve megtekintheti az új kiszolgálót. Vagy lépjen a **Minden szolgáltatás Analysis Services,**  >   hogy a kiszolgáló készen áll-e. A kiszolgálók támogatják a táblázatos modelleket az 1200-as és magasabb kompatibilitási szinteken. A modell kompatibilitási szintje a Visual Studio SSMS-ban van megadva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a kiszolgálót. Nyissa meg a kiszolgáló **Áttekintés** lapját, majd kattintson a **Törlés** elemre. 

 ![Felesleges tartalmak törlése](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Következő lépések
Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót az Azure-előfizetésében. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgáló tűzfalának konfigurálása – Portal](analysis-services-qs-firewall.md)   
