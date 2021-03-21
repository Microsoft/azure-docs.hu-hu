---
title: Megosztott rendszerkép csatolása vagy leválasztása Azure Lab Servicesban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatolhat egy megosztott képtárat egy osztálytermi laborhoz Azure Lab Servicesban.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 99b944ad85e311cd66b0a1cec4e585de37f83500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786401"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgyűjtemény csatolása vagy leválasztása Azure Lab Services
Ebből a cikkből megtudhatja, hogyan csatolhat vagy leválaszthatja a megosztott képgyűjteményt egy labor-fiókhoz. 

> [!NOTE]
> Ha egy [tesztkörnyezet sablonjának rendszerképét](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) Azure Lab Services egy megosztott képtárba menti, a képet speciális képként feltölti a katalógusba. A [speciális képek](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) a gép-specifikus információkat és a felhasználói profilokat őrzik meg. Továbbra is közvetlenül feltöltheti az általánosított rendszerképet a katalógusba Azure Lab Serviceson kívül. 
>
> A tesztkörnyezet létrehozója létrehozhat egy sablonból álló virtuális gépet, amely a Azure Lab Services általánosított és speciális rendszerképein alapul. 

## <a name="scenarios"></a>Forgatókönyvek
Íme a funkció által támogatott néhány forgatókönyv: 

- A labor-fiók rendszergazdája egy megosztott képtárat csatol a labor-fiókhoz, és feltölt egy rendszerképet a közös rendszerkép-katalógusba egy labor környezetén kívül. Ezután a labor-készítők a megosztott képkatalógusból is használhatják ezt a rendszerképet a Labs létrehozásához. 
- A labor-fiók rendszergazdája egy megosztott képtárat csatol a labor-fiókhoz. A tesztkörnyezet létrehozója (oktató) elmenti a saját laborjának testreszabott képét a megosztott képgyűjteménybe. Ezután más labor-készítők is kiválaszthatják ezt a rendszerképet a megosztott képkatalógusból, hogy sablonokat hozzanak létre a laborokhoz. 

    Ha egy képet megosztott képkatalógusba ment, Azure Lab Services replikálja a mentett rendszerképet más, azonos [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)régiókban elérhető régiókba. Gondoskodik arról, hogy a rendszerkép elérhető legyen a más régiókban, ugyanabban a földrajzi régióban létrehozott laborokhoz. Ha a képeket egy megosztott képkatalógusba menti, további költségekkel jár, ami magában foglalja az összes replikált rendszerkép díját. Ez a díj eltér a Azure Lab Services használati díjaktól. A megosztott képkatalógus díjszabásával kapcsolatos további információkért lásd: [megosztott képgyűjtemény – számlázás](../virtual-machines/shared-image-galleries.md#billing).

> [!IMPORTANT]
> Megosztott képtárat használva a Azure Lab Services csak a 128 GB-nál kevesebb lemezképet támogatja az operációsrendszer-lemezterülettel. Az 128 GB-nál több lemezterületet tartalmazó képek vagy több lemez nem jelenik meg a virtuálisgép-rendszerképek listájában a labor létrehozása során.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a labor-fiók létrehozásakor
Labor-fiók létrehozásakor csatlakoztathat egy megosztott képtárat a labor-fiókhoz. Választhat egy meglévő megosztott képtárat a legördülő listából, vagy létrehozhat egy újat. Megosztott képtárat a labor-fiókhoz való létrehozásához és csatolásához válassza az **új létrehozása** lehetőséget, adja meg a katalógus nevét, majd írja be **az OK gombot**. 

![A megosztott képtárat a labor-fiók létrehozásakor konfigurálja](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurálás a labor-fiók létrehozása után
A labor-fiók létrehozása után a következő feladatokat végezheti el:

- Megosztott Képtár létrehozása és csatolása
- Megosztott képgyűjtemény csatolása a labor-fiókhoz
- Megosztott képgyűjtemény leválasztása a labor-fiókból

## <a name="create-and-attach-a-shared-image-gallery"></a>Megosztott Képtár létrehozása és csatolása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza a **labor Services** elemet a **DEVOPS** szakaszban. Ha a Star () lehetőséget választja a `*` **labor Services** mellett, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **Lab szolgáltatások** lehetőséget a **Kedvencek** alatt.

    ![Minden szolgáltatás – > labor Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Válassza ki a labor-fiókját, és tekintse meg a **labor-fiók** lapot. 
4. A bal oldali menüben válassza a **megosztott képgyűjtemény** lehetőséget, majd válassza a **+ Létrehozás** lehetőséget az eszköztáron.  

    ![Megosztott képgyűjtemény létrehozása gomb](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. A **megosztott képgyűjtemény létrehozása** ablakban adja meg a katalógus **nevét** , majd írja be az **OK gombot**. 

    ![Megosztott képgyűjtemény létrehozása ablak](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services létrehozza a megosztott rendszerkép-katalógust, és csatolja azt a labor-fiókhoz. Az ebben a labor-fiókban létrehozott összes labor hozzáfér a csatolt megosztott képtárhoz. 

    ![Csatolt rendszerkép-gyűjtemény](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Az alsó ablaktáblán a lemezképek láthatók a megosztott Képtárban. Ebben az új galériában nincsenek lemezképek. Amikor képeket tölt fel a katalógusba, ezen az oldalon láthatja őket.     

    A csatolt megosztott képtárban lévő összes rendszerkép alapértelmezés szerint engedélyezve van. A kijelölt lemezképek engedélyezéséhez vagy letiltásához jelölje ki őket a listában, és a **kijelölt lemezképek engedélyezése** vagy a **kijelölt képek letiltása** gomb használatával.

## <a name="attach-an-existing-shared-image-gallery"></a>Meglévő megosztott Képtár csatolása
A következő eljárás azt mutatja be, hogyan csatolhat egy meglévő megosztott képtárat egy labor-fiókhoz. 

1. A **labor-fiók** lapon válassza a bal oldali menüben a **megosztott képtára** lehetőséget, majd válassza az eszköztár **csatolás** elemét. 

    ![Megosztott képgyűjtemény – Hozzáadás gomb](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. A **meglévő megosztott rendszerkép csatolása** lapon válassza ki a megosztott képtárat, majd kattintson **az OK gombra**.

    ![Meglévő gyűjtemény kiválasztása](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. A következő képernyő jelenik meg: 

    ![Saját katalógus a listában](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Ebben a példában még nincsenek lemezképek a megosztott rendszerkép-katalógusban.

    Azure Lab Services identitást a rendszer a laborhoz csatolt megosztott Képtár közreműködőiként adja hozzá. Lehetővé teszi, hogy a pedagógusok/IT-rendszergazdák a virtuális gépek lemezképeit a megosztott rendszerkép-katalógusba mentse. Az ebben a labor-fiókban létrehozott összes labor hozzáfér a csatolt megosztott képtárhoz. 

    A csatolt megosztott képtárban lévő összes rendszerkép alapértelmezés szerint engedélyezve van. A kijelölt lemezképek engedélyezéséhez vagy letiltásához jelölje ki őket a listában, és a **kijelölt lemezképek engedélyezése** vagy a **kijelölt képek letiltása** gomb használatával. 

## <a name="detach-a-shared-image-gallery"></a>Megosztott Képtár leválasztása
Csak egy megosztott képtárat lehet csatlakoztatni egy laborhoz. Ha egy másik megosztott képtárat szeretne csatolni, válassza le az aktuálisat, mielőtt csatolja az újat. Ha egy megosztott képtárat szeretne leválasztani a laborból, válassza a **Leválasztás** lehetőséget az eszköztáron, és erősítse meg a leválasztási műveletet. 

![A megosztott képgyűjtemény leválasztása a labor-fiókból](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Következő lépések
Ha szeretne többet megtudni arról, hogyan mentheti a tesztkörnyezet rendszerképét a megosztott képkatalógusba, vagy hogyan hozhat létre virtuális gépet a megosztott rendszerkép-katalógusban, olvassa el a következő témakört: [a megosztott képgyűjtemény használata](how-to-use-shared-image-gallery.md).

A megosztott képtárakkal kapcsolatos további információkért lásd: [megosztott rendszerkép](../virtual-machines/shared-image-galleries.md)-katalógus.