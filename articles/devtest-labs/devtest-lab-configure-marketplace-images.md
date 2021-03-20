---
title: Azure Marketplace-rendszerkép beállításainak konfigurálása Azure DevTest Labs
description: Annak konfigurálása, hogy mely Azure Marketplace-rendszerképek használhatók virtuális gépek létrehozásakor Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512435"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure Marketplace-rendszerkép beállításainak konfigurálása Azure DevTest Labs
A DevTest Labs támogatja a virtuális gépek Azure Marketplace-lemezképek alapján történő létrehozását, attól függően, hogy az Azure Marketplace-lemezképek hogyan legyenek felhasználva a laborban. Ez a cikk bemutatja, hogyan határozhatja meg, hogy mely, ha van ilyen, Azure Marketplace-lemezképeket használhat a virtuális gépek tesztkörnyezetben való létrehozásakor. Biztosítja, hogy a csapata csak a szükséges Piactéri rendszerképekhez férhet hozzá. 

## <a name="specify-allowed-images-for-creating-vms"></a>Engedélyezett lemezképek megadása virtuális gépek létrehozásához
Az alábbi lépéseket követve meghatározhatja, hogy mely Azure Marketplace-lemezképek engedélyezettek a virtuális gépek létrehozásakor. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A Labs listából válassza ki a labort. 
4. A labor lap kezdőlapján válassza a **konfiguráció és házirendek** lehetőséget.
5. A labor **konfigurációs és házirendjei** lapon a **virtuálisgép-alapok** területen válassza a **piactér lemezképek** lehetőséget.
6. Itt adhatja meg, hogy az összes minősített Azure Marketplace-lemezkép elérhető legyen-e az új virtuális gép alapjaként való használatra. Ha az **Igen** lehetőséget választja, akkor az összes Azure Marketplace-rendszerkép, amely megfelel az alábbi feltételeknek, engedélyezett a laborban:
   
   * A rendszerkép egyetlen virtuális gépet hoz létre, **és**
   * A rendszerkép Azure Resource Managert használ a virtuális gépek kiépítéséhez, **és**
   * A rendszerkép nem igényel extra licencelési csomag megvásárlását
     
     Ha nem kívánja engedélyezni a lemezképek használatát, vagy szeretné megadni, hogy mely lemezképek használhatók, válassza a **nem** lehetőséget.
     
     ![Lehetőség arra, hogy az összes Piactéri lemezkép használható legyen a virtuális gépek alaprendszerképe](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Ha a **nem** lehetőséget választja az előző lépésben, az **engedélyezett lemezképek/az összes kijelölése** jelölőnégyzet engedélyezve van. 
   Ezt a lehetőséget a keresőmezőbe együtt használva gyorsan kiválaszthatja vagy kiválaszthatja a listában megjelenített elemeket.
   * Válassza ki azokat az Azure Marketplace-lemezképeket, amelyeket engedélyezni szeretne a virtuális gép létrehozásához az egyes lemezképek megfelelő jelölőnégyzetének ellenőrzésével.
   * Ha nem szeretné engedélyezni az Azure Marketplace-lemezképek használatát a laborban, válassza a Nothing elemet a listából.
   
     ![Megadhatja, hogy mely Azure Marketplace-lemezképek használhatók virtuális gépek alaprendszerképként való használatával](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Hibaelhárítás
Ha nem találja a laborhoz engedélyezni kívánt rendszerképet, kövesse az alábbi lépéseket: 

- Ellenőrizze, hogy látható-e a rendszerkép a számítási virtuális gép létrehozásakor.
- Előfordulhat, hogy a rendszerkép nem érhető el a használt előfizetés típusában. Kérdezze meg az előfizetés rendszergazdáját az előfizetés típusához (például: MSDN, ingyenes, utólagos fizetés stb.). 
- A 2. generációs rendszerképek támogatása a DevTest Labs szolgáltatásban korlátozott. Ha az 1. és a 2. generációs verziók egyaránt elérhetők egy képhez, a DevTest Labs csak a rendszerkép 1. generációs verzióját jeleníti meg a virtuális gép létrehozásakor. Megkerülő megoldásként hozzon létre egy egyéni Gen 2 rendszerképet a laboron kívül, és használja azt egy virtuális gép létrehozásához. Ha a rendszerképnek csak a Gen 2 verziója érhető el, az a DevTest Labs által támogatott és látható. 
      


## <a name="next-steps"></a>Következő lépések
Miután konfigurálta, hogy az Azure Marketplace-lemezképek hogyan engedélyezettek a virtuális gépek létrehozásakor, a következő lépés [egy virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md).

