---
title: Tesztkörnyezet létrehozása a mélyreható tanuláshoz a Azure Lab Services használatával | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251456"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Tesztkörnyezet létrehozása a természetes nyelvi feldolgozással kapcsolatos mély tanuláshoz Azure Lab Services
Ebből a cikkből megtudhatja, hogyan állíthat be egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) formája, amely lehetővé teszi, hogy a számítógépek fordítással, beszédfelismeréssel és más nyelvi felismerési képességekkel rendelkeznek.  

Az NLP osztályba tartozó diákok linuxos virtuális gépet (VM) kapnak, amelyből megtudhatja, hogyan alkalmazhat neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mély tanulási modellek fejlesztéséhez. 

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Ha már rendelkezik Azure-előfizetéssel, létrehozhat egy új Labor-fiókot Azure Lab Services vagy használhat egy meglévő Lab-fiókot is. A következő oktatóanyagban talál egy új Labor-fiókot: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).
 
A labor-fiók létrehozása után engedélyezze a következő beállításokat a labor-fiókban: 

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ----------- | ------------ |  
| Marketplace-rendszerképek | Engedélyezze a Data Science Virtual Machine for Linux (Ubuntu) rendszerképet a labor-fiókban való használathoz.  A következő cikkben talál útmutatást: a [tesztkörnyezet-készítők számára elérhető Piactéri lemezképek megadása](specify-marketplace-images.md). | 

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, és alkalmazza a következő beállításokat:

| Tesztkörnyezet beállításai | Érték/utasítások | 
| ------------ | ------------------ |
| Virtuális gép (VM) mérete | **Kis GPU (számítás)**. Ez a méret a legjobb megoldás a nagy számítási igényű és a hálózati igényű alkalmazások, például a mesterséges intelligencia és a mély tanulás számára. |
| VM-rendszerkép | [Linux (Ubuntu) Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Ez a rendszerkép mély tanulási keretrendszert és eszközöket biztosít a gépi tanuláshoz és az adatelemzéshez. A képen található telepített eszközök teljes listájának megtekintéséhez tekintse meg a következő cikket: [Mit tartalmaz a DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Távoli asztali kapcsolat engedélyezése | <p>Az adatelemzési rendszerkép már úgy van konfigurálva, hogy a X2Go használja, hogy a tanárok és a tanulók a grafikus felhasználói felületen távoli asztal használatával csatlakozzanak.  A X2Go *nem* igényli, hogy a **Távoli asztali kapcsolat engedélyezése** beállítás engedélyezve legyen.  Erre a beállításra csak akkor van szükség, ha az RDP használatát választja.

>**Fontos**: bár javasoljuk, hogy a X2Go-t az adatelemzési képpel együtt használja, ha inkább RDP-t használ, a Linux rendszerű virtuális géphez először az SSH-t kell használnia, és telepítenie kell az RDP-és GUI-csomagokat.  Ezután a tanulók a Linux rendszerű virtuális géphez később is csatlakozhatnak RDP használatával.  További információ: [a grafikus távoli asztal engedélyezése Linux rendszerű virtuális gépekhez](how-to-enable-remote-desktop-linux.md).

A Linux-rendszerképekhez készült Data Science Virtual Machine az ilyen típusú osztályok számára szükséges mély tanulási keretrendszereket és eszközöket biztosít. Ennek eredményeképpen a sablon létrehozása után nem kell további testreszabást végeznie. A tanulók számára közzé lehet tenni. Kattintson a **Közzététel** gombra a sablon lapon a sablonnak a laborba való közzétételéhez.  

## <a name="cost"></a>Költségek
Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja: 

Egy 25 tanulós osztály esetében, amely 20 órányi időpontot és 10 órányi kvótát biztosít a munkahelyhez vagy a hozzárendelésekhez, a labor díja 25 diák * (20 + 10) óra * 139 labor egység * 0,01 USD/óra = 1042,5 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezeti a tesztkörnyezet a természetes nyelvi feldolgozó osztályhoz való létrehozásának lépésein. Hasonló beállításokat használhat más Deep learning-osztályokhoz.

## <a name="next-steps"></a>Következő lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users). 

