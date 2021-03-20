---
title: Azure DevTest Labs használata fejlesztőknek | Microsoft Docs
description: Ismerje meg a fejlesztői követelmények teljesítéséhez használható Azure DevTest Labs szolgáltatásokat, valamint a tesztkörnyezet beállításához szükséges részletes lépéseket.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4f195f843e30ad1a56bad7d5b9ca9e73888783b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89296243"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Azure DevTest Labs használata fejlesztőknek
Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a fejlesztői gépeket a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A fejlesztők igény szerint gyorsan üzembe helyezhetik fejlesztői számítógépeiket.
- A fejlesztők szükség esetén könnyedén testreszabhatják a fejlesztési gépeket.
- A rendszergazdák a következőket biztosíthatják a költségek ellenőrzéséhez:
  - A fejlesztők nem szerezhetnek be több virtuális gépet, mint amennyire a fejlesztéshez szükségük van.
  - Ha nincs használatban, a virtuális gépek le vannak állítva. 

![DevTest Labs használata képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerheti a fejlesztői követelmények teljesítéséhez használható különböző Azure DevTest Labs funkciókat, valamint a tesztkörnyezet beállításához szükséges részletes lépéseket.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Fejlesztői környezetek implementálása a Azure DevTest Labs
1. **A tesztkörnyezet létrehozása** 
   
    A Labs a Azure DevTest Labs kiindulási pontja. Miután létrehozta a labort, olyan feladatokat hajthat végre, mint például a felhasználók (fejlesztők) hozzáadása a laborhoz, házirendek beállítása a költségek szabályozására, a gyorsan készíthető virtuálisgép-lemezképek definiálása és egyebek.  
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Megtudhatja, hogyan hozhat létre labort Azure DevTest Labs a Azure Portalban. |
2. **Virtuális gépek létrehozása percek alatt, előre elkészített Piactéri rendszerképek és Egyéni rendszerképek használatával** 
   
    Az Azure Marketplace-en számos rendszerképből választhat előre elkészített képeket, és elérhetővé teheti őket a laborban. Ha a kész lemezképek nem felelnek meg a követelményeknek, létrehozhat egy egyéni rendszerképet úgy, hogy létrehoz egy, az Azure Marketplace-ről készített, előre elkészített rendszerképet használó, az összes szükséges szoftvert, és a virtuális gépet a laborban egyéni rendszerképként menti.

    Ha egyéni lemezképeket fog használni, érdemes lehet egy rendszerkép-előállítót használni a lemezképek létrehozásához és terjesztéséhez. A rendszerkép-előállító egy olyan konfigurációs kódú megoldás, amely rendszeresen épít és terjeszt automatikusan a konfigurált képeket. Ezzel a beállítással a rendszer manuális konfigurálásához szükséges idő takarítható meg, miután létrehozta a virtuális gépet az alapoperációs rendszerrel.
  
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan engedélyezheti az Azure Marketplace-lemezképeket, így csak a fejlesztőknek szánt rendszerképeket választhat.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerképet a szükséges szoftver előzetes telepítésével, így a fejlesztők gyorsan létrehozhatnak egy virtuális gépet az egyéni rendszerkép használatával.|
   | [Tudnivalók a rendszerkép-előállítóról](./devtest-lab-faq.md#blog-post) |Tekintsen meg egy videót, amely leírja, hogyan kell beállítani és használni a rendszerkép-előállítót.|

3. **Újrafelhasználható sablonok létrehozása a fejlesztői gépekhez** 
   
    A Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listáját tartalmazza. Létrehozhat egy képletet a laborban egy rendszerkép, egy virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat segítségével. Minden fejlesztő láthatja a képletet a laborban, és felhasználhatja egy virtuális gép létrehozásához. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs-képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet a rendszerkép, a virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával.|

4. **Összetevők létrehozása a rugalmas virtuális gépek testreszabásának engedélyezéséhez**

   Az összetevők az alkalmazás üzembe helyezésére és konfigurálására szolgálnak a virtuális gép kiépítése után. Az összetevők a következőket tehetik:

   - A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
   - A virtuális gépen futtatni kívánt műveletek – például a tárház klónozása.
   - A tesztelni kívánt alkalmazások.

   Számos összetevő már elérhető az Ön számára. Saját egyéni összetevőket is létrehozhat, ha további testreszabásra van szüksége az adott igényekhez.

   További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |Saját egyéni összetevőket hozhat létre a laborban található virtuális gépekhez.|
   | [Git-tárház hozzáadása az egyéni összetevők és Azure Resource Manager sablonok tárolásához Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevőket a saját privát git-tárházában.|

5. **A költségek szabályozása**
   
    Azure DevTest Labs lehetővé teszi, hogy a laborban beállítson egy szabályzatot, hogy meghatározza a fejlesztő által a laborban létrehozható virtuális gépek maximális számát. 
   
    Ha a fejlesztői csapatának van beállított munkaterve, és az összes virtuális gépet le szeretné állítani a nap egy adott időpontjában, majd automatikusan újraindítja őket a következő napon, egyszerűen elvégezheti ezt az automatikus leállítás és a szabályzatok automatikus indításának beállításával. 
   
    Végül, ha az alkalmazásfejlesztés elkészült, egyetlen PowerShell-szkript futtatásával bármikor törölheti az összes virtuális gépet. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Szabályozza a költségeket a szabályzatok beállításával a laborban. |
   | [Az összes tesztkörnyezetben működő virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |A fejlesztés befejezésekor törölje az összes labort egy művelettel.|

1. **Virtuális hálózat hozzáadása virtuális GÉPHEZ** 
   
    A DevTest Labs létrehoz egy új virtuális hálózatot (VNET), amikor létrehoznak egy labort. Ha saját VNET konfigurált – például a ExpressRoute vagy a helyek közötti VPN használatával –, akkor ezt a VNET felveheti a labor virtuális hálózati beállításaiba, hogy elérhető legyen a virtuális gépek létrehozásakor.

    Emellett van olyan Azure Active Directory tartományhoz való csatlakozás is, amely a virtuális gép létrehozásakor csatlakozik egy virtuális géphez a tartományhoz. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása Azure DevTest Labsban](devtest-lab-configure-vnet.md) |Megtudhatja, hogyan konfigurálhat virtuális hálózatot Azure DevTest Labs a Azure Portal használatával.|

6. **A labor megosztása minden fejlesztővel**
   
    A laborok közvetlenül is elérhetők egy, a fejlesztőknek megosztható hivatkozás használatával. Még nem kell Azure-fiókkal rendelkezniük, feltéve, hogy [Microsoft-fiókük](devtest-lab-faq.md#what-is-a-microsoft-account)van. A fejlesztők nem láthatják más fejlesztők által létrehozott virtuális gépeket.  
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Fejlesztő hozzáadása laborhoz Azure DevTest Labs](devtest-lab-add-devtest-user.md) |A Azure Portal használatával adhat hozzá fejlesztőket a laborhoz.|
   | [Fejlesztők hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A PowerShell használatával automatizálhatja a fejlesztők hozzáadását a laborhoz. |
   | [A laborra mutató hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Megtudhatja, hogyan férhetnek hozzá a fejlesztők közvetlenül a laborhoz egy hiperhivatkozás használatával.|

7. **A laborok létrehozásának automatizálása további csapatok számára** 
   
    Egy Resource Manager-sablon létrehozásával automatizálhatja a laborok létrehozását, beleértve az egyéni beállításokat is, és a használatával újra és újra létrehozhatja az azonos Labs-t. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Resource Manager-sablonnal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre laborokat a Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
