---
title: Azure Certified Device program – oktatóanyag – a projekt létrehozása
description: Útmutató projekt létrehozásához az Azure Certified-eszköz portálon
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313997"
---
# <a name="tutorial-create-your-project"></a>Oktatóanyag: a projekt létrehozása

Gratulálunk, hogy az eszközt az Azure Certified Device programon keresztül tanúsítja. Most kiválasztotta az eszközének megfelelő minősítési programot, és készen áll a kezdésre a portálon.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Jelentkezzen be az [Azure Certified eszköz portálra](https://certify.azure.com/)
> * Új minősítési projekt létrehozása az eszközhöz
> * Adja meg a projekt alapszintű eszközének adatait

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy érvényes munkahelyi/iskolai [Azure Active Directory-fiókra](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).
- Szüksége lesz egy ellenőrzött Microsoft Partner Network (MPN) fiókra. Ha nem rendelkezik MPN-fiókkal, [csatlakoztassa a partner hálózatot a](https://partner.microsoft.com/) Kezdés előtt.

## <a name="signing-into-the-azure-certified-device-portal"></a>Bejelentkezés az Azure Certified-eszköz portálra

Első lépésként be kell jelentkeznie a portálra, ahol megadhatja az eszköz információit, elvégezheti a minősítési teszteket, és kezelheti az eszköz kiadványait az Azure Certified Device Catalog szolgáltatásban.

1. Nyissa meg az [Azure Certified Device portált](https://certify.azure.com).
1. Válassza ki a `Company profile` bal oldali oldalt, és frissítse a gyártó adatait.
   ![A vállalati profil szakasza](./media/images/company-profile.png)
1. Fogadja el a program szerződését a projekt megkezdéséhez.

## <a name="creating-your-project-on-the-portal"></a>A projekt létrehozása a portálon

Most, hogy minden be van állítva a portálon, megkezdheti a minősítési folyamatot. Először létre kell hoznia egy projektet az eszközhöz.

1. A kezdőképernyőn válassza a lehetőséget `Create new project` . Ekkor megnyílik egy ablak, amely alapszintű eszköz-információkat ad hozzá a következő szakaszban.

 ![Az új projekt létrehozása gomb képe](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Alapszintű eszköz adatainak azonosítása

Ezután meg kell adnia az alapvető eszközök adatait. Ezt az információt később is szerkesztheti.

1. Fejezze be a szakaszban kért mezőket `Basics` . A **kötelező** mezők tisztázására az alábbi táblázatban tájékozódhat:

    | Mezők                  | Description                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Projektnév           | Belső név, amely nem lesz látható az Azure Certified-eszköz katalógusában                                                        |
    | Eszköz neve            | Az eszköz nyilvános neve                                                                                                |
    | Eszköz típusa            | A befejezett termék vagy Solution-Ready fejlesztői csomag specifikációja.     A terminológiával kapcsolatos további információkért lásd: [minősítési Szószedet](./resources-glossary.md).                                                                     |
    | Eszköz osztálya           | Átjáró, érzékelő vagy más.  A terminológiával kapcsolatos további információkért lásd: [minősítési Szószedet](./resources-glossary.md).                                                                    |
    | Eszköz forráskódjának URL-címe | Ha Solution-Ready dev-csomagot tanúsít, egyéb esetben nem kötelező megadni. Az URL-címnek egy GitHub-helynek kell lennie az eszköz kódjában. |
1. Kattintson a `Next` gombra a lap folytatásához `Certifications` .

    ![Az új projekt létrehozása űrlap, minősítések lap képe](./media/images/create-new-project-certificationswindow.png)

1. Adja meg, hogy mely tanúsítvány (oka) t szeretné elérni az eszközön.
1. Válassza ki a elemet `Create` , és az új projekt el lesz mentve és látható a portál kezdőlapján.

    ![A Project Table képe](./media/images/project-table.png)

1. Válassza ki a projekt nevét a táblában. Ez elindítja a projekt összegzése lapot, ahol hozzáadhat és megtekintheti az eszközével kapcsolatos további adatokat.

    ![A projekt részletei lap képe](./media/images/device-details-section.png)

## <a name="next-steps"></a>Következő lépések

Most már készen áll az eszköz adatainak hozzáadására és az eszköz tesztelésére a tanúsítási szolgáltatás használatával. A következő cikkből megtudhatja, hogyan szerkesztheti az eszköz részleteit.
> [!div class="nextstepaction"]
> [Oktatóanyag: eszköz adatainak hozzáadása](tutorial-02-adding-device-details.md)
