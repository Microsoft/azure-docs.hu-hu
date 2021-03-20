---
title: Marketplace-lemezképek megadása Azure Lab Servicesban található laborhoz
description: Ebből a cikkből megtudhatja, hogyan adhatja meg azokat a Piactéri rendszerképeket, amelyekkel a labor Creator használatával labor-fiókokat hozhat létre Azure Lab Servicesban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434736"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>A tesztkörnyezet-készítők számára elérhető Piactéri lemezképek meghatározása
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

## <a name="select-images-available-for-labs"></a>Válassza ki a Labs számára elérhető rendszerképeket
Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A listát úgy szűrheti, hogy csak az engedélyezett/letiltott képeket jelölje ki, ha a felül található legördülő listából kiválasztja az **engedélyezett csak** / **letiltott** lehetőséget. 
    
![Marketplace-beli rendszerképek oldal](./media/tutorial-setup-lab-account/marketplace-images-page.png)

A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
    
- Egyetlen virtuális gépet hoznak létre.
- A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
- Nincs szükség külön licenccsomag vásárlására.

## <a name="disable-images-for-a-lab"></a>A tesztkörnyezet rendszerképeinek letiltása 
Egy tesztkörnyezet egyetlen rendszerképének letiltásához válassza a **... lehetőséget. (három pont)** az utolsó oszlopban, majd válassza a **rendszerkép letiltása** lehetőséget. 

![Egy rendszerkép letiltása](./media/tutorial-setup-lab-account/disable-one-image.png) 

Azt is megteheti, hogy a kép neve előtt bejelöli a jelölőnégyzetet, majd kiválasztja a **kijelölt lemezképek letiltása** lehetőséget az eszköztáron. 

Ha egyszerre több lemezképet szeretne letiltani, jelölje be a jelölőnégyzeteket a lemezkép neve előtt, majd válassza a **kijelölt lemezképek letiltása** lehetőséget az eszköztáron. 

![Több rendszerkép letiltása](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Képek engedélyezése laborhoz
A letiltott képek engedélyezéséhez válassza a... lehetőséget. **(három pont)** az utolsó oszlopban válassza a **rendszerkép engedélyezése** lehetőséget. Azt is megteheti, hogy a kép neve előtt bejelöli a jelölőnégyzetet, majd kiválasztja a **kijelölt rendszerképek engedélyezése** lehetőséget az eszköztáron. 

Ha egyszerre több lemezképet szeretne letiltani, jelölje be a jelölőnégyzeteket a lemezkép neve előtt, majd válassza a **kijelölt lemezképek engedélyezése** lehetőséget az eszköztáron. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Képek engedélyezése a labor létrehozásakor
A labor létrehozásakor több lemezképet is engedélyezhet: 

1. Jelentkezzen be a [Azure Lab Services webhelyre](https://labs.azure.com) a **labor-fiók tulajdonosának** hitelesítő adataival
2. Válassza ki az alapértelmezett virtuálisgép-rendszerképet vagy a lefelé mutató nyilat. 
3. Válassza a **további képképek engedélyezése lehetőséget**. 

    ![További képbeállítások engedélyezése](./media/specify-marketplace-images/enable-more-images-menu.png)
4. A kiválasztott lemezképek engedélyezéséhez kövesse az előző szakasz utasításait. 
5. Előfordulhat, hogy be kell állítania az **új tesztkörnyezet** ablakát, és újra meg kell nyitnia az előző lépésben kiválasztott képek megtekintéséhez. 



## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként a hozzáférés Labs](how-to-use-classroom-lab.md)
