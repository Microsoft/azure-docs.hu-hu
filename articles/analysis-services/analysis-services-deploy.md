---
title: Modell üzembe helyezése Azure Analysis Services a Visual Studio használatával | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy táblázatos modellt egy Azure Analysis Services-kiszolgálóra a Visual Studio használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9df10760164dcd0d207663c14107f72c46b76d25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96501243"
---
# <a name="deploy-a-model-from-visual-studio"></a>Modell üzembe helyezése a Visual Studióból

Miután létrehozott egy kiszolgálót az Azure-előfizetésében, készen áll a táblázatos modelladatbázis üzembe helyezésére. A Visual Studióban Analysis Services projektek segítségével készíthet és helyezhet üzembe egy táblázatos modell-projektet, amelyen dolgozik. 

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* **Analysis Services-kiszolgáló** az Azure-ban. További információkért lásd [az Azure Analysis Services-kiszolgáló létrehozásával kapcsolatos](analysis-services-create-server.md) témakört.
* **Táblázatos modell projekt** a Visual Studióban vagy egy meglévő táblázatos modell a 1200-as vagy magasabb kompatibilitási szinten. Korábban még nem hozott létre egyet sem? Próbálkozzon [az Adventure Works internetes értékesítési modell központi telepítésének útmutatójával](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Helyszíni átjáró** – Ha a szervezete hálózatában egy vagy több helyszíni adatforrás található, telepítenie kell egy [helyszíni adatátjárót](analysis-services-gateway.md). Az átjáróra azért van szükség, hogy a felhőben található kiszolgálója csatlakozni tudjon a helyszíni adatforrásaihoz a modellben található adatok feldolgozásához és frissítéséhez.

> [!TIP]
> Az üzembe helyezés előtt győződjön meg róla, hogy a tábláiban található adatok feldolgozhatók. A Visual Studióban kattintson a **modell**  >  **folyamat**  >  **összes feldolgozása** elemre. Ha a feldolgozás meghiúsul, nem fog sikerülni a telepítés.
> 
> 

## <a name="get-the-server-name"></a>Kiszolgáló nevének lekérése

Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Üzembe helyezés a Visual studióból

1. A Visual Studio > **megoldáskezelő** kattintson a jobb gombbal a projekt > **tulajdonságai** elemre. Ezután a **telepítési**  >  **kiszolgálón** illessze be a kiszolgáló nevét.   
   
    ![Az üzembehelyezési kiszolgáló tulajdonságához illessze be a kiszolgáló nevét.](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. A **Megoldáskezelőben** kattintson a jobb gombbal a **Tulajdonságok** elemre, majd kattintson az **Üzembe helyezés** lehetőségre. Lehet, hogy a rendszer arra kéri, hogy jelentkezzen be az Azure-ba.
   
    ![Üzembe helyezés kiszolgálóra](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Az üzembe helyezés állapota látható a kimeneti ablakban és az Üzembe helyezés területen is.
   
    ![Üzembe helyezés állapota](./media/analysis-services-deploy/aas-deploy-status.png)

Ennyi az egész!


## <a name="troubleshooting"></a>Hibaelhárítás

Ha a metaadatok telepítésekor a telepítés meghiúsul, akkor valószínű, hogy a Visual Studio nem tudott csatlakozni a kiszolgálóhoz. Győződjön meg arról, hogy SQL Server Management Studio (SSMS) használatával tud csatlakozni a kiszolgálóhoz. Ezt követően ellenőrizze, hogy helyes a projekt Üzembehelyezési kiszolgáló tulajdonsága.

Ha a telepítés egy táblán sikertelen, annak valószínűleg az az oka, hogy a kiszolgálója nem tudott csatlakozni egy adatforráshoz. Ha a szervezete hálózatában helyszíni adatforrás található, mindenképp telepítsen egy [helyszíni adatátjárót](analysis-services-gateway.md).

## <a name="next-steps"></a>Következő lépések

Miután sikeresen telepítette a kiszolgálóra a táblázatos modellt, azonnal csatlakozhat is hozzá. [SQL Server Management Studio (SSMS)](analysis-services-manage.md) használatával is csatlakozhat a kezeléséhez. Továbbá [csatlakozhat hozzá ügyféleszközzel](analysis-services-connect.md) is, például Power BI, Power BI Desktop vagy Excel segítségével, és megkezdheti a jelentések létrehozását.   

A speciális üzembe helyezési módszerekkel kapcsolatos további információkért lásd: [táblázatos modell megoldásának üzembe helyezése](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current&preserve-view=true).