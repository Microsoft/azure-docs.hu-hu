---
title: Azure DevTest Labs használata a betanításhoz | Microsoft Docs
description: Ez a cikk részletesen ismerteti, hogyan állíthatja be a labort a Azure DevTest Labs betanításához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294849"
---
# <a name="use-azure-devtest-labs-for-training"></a>Azure DevTest Labs használata képzéshez
A Azure DevTest Labs számos kulcsfontosságú forgatókönyvet valósíthat meg a fejlesztési és tesztelési funkciókon kívül is. Ezen forgatókönyvek egyike a képzéshez szükséges labor beállítása. Azure DevTest Labs lehetővé teszi, hogy egy olyan labort hozzon létre, ahol egyéni sablonokat biztosíthat, amelyekkel az egyes gyakornokok azonos és elszigetelt környezeteket hozhatnak létre a képzéshez. Házirendeket alkalmazhat annak biztosítására, hogy a képzési környezetek csak akkor legyenek elérhetők az egyes gyakornokok számára, ha szükségük van rájuk, és elegendő erőforrást (például virtuális gépeket) tartalmaznak, amelyek a képzéshez szükségesek Végül egyszerűen megoszthatja a labort a gyakornokokkal, amelyeket egyetlen kattintással elérhet.

![DevTest Labs használata képzéshez](./media/devtest-lab-training-lab/devtest-lab-training.png)

A Azure DevTest Labs megfelel a következő követelményeknek, amelyek szükségesek a betanítás bármely virtuális környezetben való végrehajtásához: 

* A gyakornokok nem látják a más gyakornokok által létrehozott virtuális gépeket
* Minden betanítási gépnek azonosnak kell lennie
* A gyakornokok gyorsan üzembe helyezhetik a képzési környezeteket
* Szabályozhatja a költségeket azáltal, hogy a gyakornokok nem tudnak több virtuális gépet lekérni, mint amennyit a képzéshez és a virtuális gépek leállításához, amikor nem használják őket
* Az oktatási laborok könnyedén megoszthatók az egyes gyakornokokkal
* Újra és újra újra felhasználja a betanítási labort

Ebben a cikkben megismerheti azokat a különböző Azure DevTest Labs funkciókat, amelyek a korábban leírt képzési követelmények teljesítéséhez használhatók, és részletesen ismertetjük azokat a lépéseket, amelyekkel labort állíthat be a képzéshez.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Képzés megvalósítása Azure DevTest Labs
1. **A tesztkörnyezet létrehozása** 
   
    A Labs a Azure DevTest Labs kiindulási pontja. A labor létrehozása után olyan feladatokat hajthat végre, mint például a felhasználók (gyakornokok) hozzáadása a laborhoz, szabályzatok beállítása a költségek szabályozására, a gyorsan készíthető virtuálisgép-rendszerképek definiálása és egyebek.   
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Megtudhatja, hogyan hozhat létre labort Azure DevTest Labs a Azure Portalban. |
2. **A kész Piactéri rendszerképek és az Egyéni rendszerképek használatával percek alatt hozhat létre képzési virtuális gépeket** 
   
    Az Azure Marketplace-en számos rendszerképből választhat előre elkészített képeket, és elérhetővé teheti azokat a laborban található gyakornokoknak. Ha a kész lemezképek nem felelnek meg a követelményeknek, létrehozhat egy egyéni rendszerképet úgy, hogy létrehoz egy, az Azure Marketplace-ről készített, előre elkészített rendszerképet használó Tesztkörnyezet-virtuális gépet, telepíti az összes szükséges szoftvert, és a virtuális gépet a laborban egyéni rendszerképként menti. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Ismerje meg, hogyan engedélyezheti az Azure Marketplace-lemezképeket; csak a betanításhoz használni kívánt rendszerképek kiválasztására használható. |
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerképet a betanításhoz szükséges szoftver előzetes telepítésével, így a gyakornokok gyorsan létrehozhatnak egy virtuális gépet az egyéni rendszerkép használatával. |
3. **Újrafelhasználható sablonok létrehozása a betanítási gépekhez** 
   
    A Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listáját tartalmazza. Létrehozhat egy képletet a laborban egy rendszerkép, egy virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat segítségével. Minden gyakornok láthatja a képletet a laborban, és felhasználhatja egy virtuális gép létrehozásához. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs-képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet a rendszerkép, a virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával. |
4. **A költségek szabályozása**
   
    Azure DevTest Labs lehetővé teszi, hogy házirendet állítson be a laborban a laborban gyakornokként létrehozott virtuális gépek maximális számának megadásához. 
   
    Ha többnapos képzést végez, és egy adott időpontban szeretné leállítani az összes virtuális gépet, majd automatikusan újraindítja őket a következő napon, egyszerűen elvégezheti ezt az automatikus leállítás és az automatikus indítási házirendek beállításával a laborban. 
   
    Végül, ha a képzés befejeződött, egyetlen PowerShell-szkript futtatásával bármikor törölheti az összes virtuális gépet. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Szabályozza a költségeket a szabályzatok beállításával a laborban. |
   | [Az összes tesztkörnyezetben működő virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |A képzés befejezésekor törölje az összes labort egy művelettel. |
5. **A labor megosztása minden gyakornoktal**
   
    A laborok közvetlenül is elérhetők egy, a gyakornokokkal megosztott hivatkozás használatával. A gyakornokoknak még egy Azure-fiókkal sem kell rendelkezniük, feltéve, hogy [Microsoft-fiókük](devtest-lab-faq.md#what-is-a-microsoft-account)van. A gyakornokok nem látják a más gyakornokok által létrehozott virtuális gépeket.  
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Gyakornok hozzáadása laborhoz Azure DevTest Labs](devtest-lab-add-devtest-user.md) |A Azure Portal használatával vehet fel gyakornokokat a betanítási laborba. |
   | [Gyakornokok hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A PowerShell használatával automatizálhatja a gyakornokok felvételét a képzési laborba. |
   | [A laborra mutató hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Megtudhatja, hogyan lehet közvetlenül hozzáférni a laborhoz egy hiperhivatkozáson keresztül. |
6. **Újra és újra újra újra a labort** 
   
    Egy Resource Manager-sablon létrehozásával automatizálhatja a laborok létrehozását, beleértve az egyéni beállításokat is, és a használatával újra és újra létrehozhatja az azonos Labs-t. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Resource Manager-sablonnal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre laborokat a Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
