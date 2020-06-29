---
title: A tanterem Labs ismertetése a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre gyorsan egy tantermi tesztkörnyezet-környezetet a felhőben – beállíthatja a labort egy sablonos virtuális géppel az osztályhoz szükséges szoftverrel, és elkészítheti a virtuális gép másolatát a osztály minden tanulója számára.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: e6ff56a831282c3abf5eec644573b9a3e88c6543
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445627"
---
# <a name="introduction-to-classroom-labs"></a>Az osztályterem-tesztkörnyezetek bemutatása
Az Azure Lab Services lehetővé teszi egy osztályterem-tesztkörnyezet gyors beállítását a felhőben. Az oktató létrehoz egy osztályterem-tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket a tesztkörnyezetben, és elérhetővé teszi őket a diákok számára. A diákok osztályterem-tesztkörnyezetben található virtuális gépekhez csatlakoznak, és a projektjeikhez, feladataikhoz vagy osztálytermi gyakorlataikhoz használják őket. 

Az osztályterem Labs az Azure által felügyelt Lab-típusok. A szolgáltatás maga kezeli a felügyelt tesztkörnyezet összes infrastruktúra-kezelését, a virtuális gépek (VM-EK) a hibák kezeléséhez és az infrastruktúra skálázásához. Ön megadhatja, milyen típusú infrastruktúrát igényel, és telepítheti az eszközöket vagy szoftvereket, amelyekre az osztálynak szüksége van. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Az Azure-infrastruktúra és-méretezés automatikus felügyelete 
A Azure Lab Services felügyelt szolgáltatás, ami azt jelenti, hogy a szolgáltatás automatikusan kezeli a labor alapjául szolgáló infrastruktúra kiépítését és felügyeletét. A felhasználók számára a megfelelő labor-élmény előkészítésére koncentrálhat. Hagyja, hogy a szolgáltatás kezelje a REST-et, és állítsa be a laborban lévő virtuális gépeket a hallgatóságba. A labort több száz virtuális gépre méretezheti egyetlen kattintással.

## <a name="simple-experience-for-your-lab-users"></a>Egyszerű felhasználói felület a tesztkörnyezet felhasználói számára 
A laborba meghívó felhasználók azonnal hozzáférhetnek a laboratóriumokban meghívott erőforrásokhoz. Csak be kell jelentkeznie, hogy megtekintse a több laborban elérhető virtuális gépek teljes listáját. Egyetlen gombra kattintva csatlakozhat a virtuális gépekhez, és megkezdheti a munkát. A felhasználóknak nincs szükségük Azure-előfizetésekre a szolgáltatás használatához. 

## <a name="cost-optimization-and-tracking"></a>A Cost optimalizálása és nyomon követése  
Tartsa meg a költségvetését ellenőrzés alatt, hogy pontosan hány óra alatt használhatja a tesztkörnyezet felhasználói a virtuális gépeket. Állítsa be az ütemterveket a laborban, hogy a felhasználók csak a kijelölt időpontokban használják a virtuális gépeket, vagy az automatikus leállítás és a kezdési idő beállítását. Nyomon követheti az egyes felhasználók használati és beállított korlátait.

## <a name="example-class-types"></a>Példa típusú osztályok
A Azure Lab Services használatával számos különböző típusú osztályhoz beállíthat Labs-t. Tekintse [meg](class-types.md) az egyes példákat a Azure Lab Services cikkben, amely néhány példát mutat be, amelyekkel a laborokat beállíthatja a Azure Lab Services. 

## <a name="next-steps"></a>További lépések
Első lépésként az Azure Lab Services használatával hozzon létre egy tesztkörnyezetfiókot, amely az osztálytermi tesztkörnyezetek létrehozásához szükséges:

- [Tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)
