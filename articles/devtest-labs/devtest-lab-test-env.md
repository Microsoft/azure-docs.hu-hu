---
title: Azure DevTest Labs használata a virtuális gépek és a Pásti tesztelési környezetekhez | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure DevTest Labst a virtuális gépek és a Péter tesztelési környezetével kapcsolatos forgatókönyvekhez.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 978071aa160098612a5effebfb30d5d58b777df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89299830"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Azure DevTest Labs használata a virtuális gépek és a Pásti tesztelési környezetekhez

Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a gépeket a tesztelők számára. 

Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A tesztelők gyorsan tesztelhetik az alkalmazás legújabb verzióját Windows- és Linux-környezetek újrahasznosítható sablonokkal és összetevőkkel történő kiépítésével.
- A tesztelők vertikálisan felskálázhatják a terhelésteszteket több tesztügynök kiépítésével.
- A rendszergazdák a következőket biztosíthatják a költségek ellenőrzéséhez:
  - A tesztelők nem kaphatnak több virtuális gépet, mint amennyit szükségesek.
  - Ha nincs használatban, a virtuális gépek le vannak állítva.

![DevTest Labs használata képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerheti a tesztelési követelmények teljesítéséhez használt különböző Azure DevTest Labs funkciókat, valamint a tesztkörnyezet beállításának részletes lépéseit.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Tesztelési környezetek megvalósítása Azure DevTest Labs
1. **A tesztkörnyezet létrehozása** 
   
    A Labs a Azure DevTest Labs kiindulási pontja. A labor létrehozása után olyan feladatokat hajthat végre, mint például a felhasználók (tesztelők) hozzáadása a laborhoz, szabályzatok beállítása a költségek szabályozására, a gyorsan készíthető virtuálisgép-rendszerképek definiálása és egyebek.  
   
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
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan engedélyezheti az Azure Marketplace-lemezképeket, így csak a tesztelők számára kívánt rendszerképeket lehet kiválasztani.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerképet a szükséges szoftver előzetes telepítésével, így a tesztelők gyorsan létrehozhatnak egy virtuális gépet az egyéni rendszerkép használatával.|
   | [Tudnivalók a rendszerkép-előállítóról](./devtest-lab-faq.md#blog-post) |Tekintsen meg egy videót, amely leírja, hogyan kell beállítani és használni a rendszerkép-előállítót.|

3. **Újrafelhasználható sablonok létrehozása tesztelési gépekhez** 
   
    A Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listáját tartalmazza. Létrehozhat egy képletet a laborban egy rendszerkép, egy virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat segítségével. Az egyes tesztelők megtekinthetik a képletet a laborban, és felhasználhatják egy virtuális gép létrehozásához. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs-képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet a rendszerkép, a virtuálisgép-méret (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával.|

3. **Több virtuális gépre kiterjedő tesztelési környezetek létrehozása** 
   
    Azure Resource Manager-sablonok segítségével meghatározhatja az Azure-megoldás infrastruktúráját és konfigurációját, és a több teszt virtuális gép ismételt üzembe helyezését konzisztens állapotban végezheti el.

    Az Azure-beli Pásti-erőforrások egy Resource Manager-sablon környezetében is üzembe helyezhetők, és megjelennek a Cost Tracking szolgáltatásban. A virtuális gép automatikus leállítása azonban nem vonatkozik a Pásti-erőforrásokra.

    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md) |Megtudhatja, hogyan helyezhet üzembe több virtuális gépet konzisztens állapotban a tesztkörnyezet számára.|

4. **Összetevők létrehozása a rugalmas virtuális gépek testreszabásának engedélyezéséhez**

   Az összetevők az alkalmazás üzembe helyezésére és konfigurálására szolgálnak a virtuális gép kiépítése után. Az összetevők a következőket tehetik:

   - A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
   - A virtuális gépen futtatni kívánt műveletek – például a tárház klónozása.
   - A tesztelni kívánt alkalmazások.

   Számos összetevő már elérhető az Ön számára. Ha azonban további testreszabásra van szüksége az adott igényekhez, létrehozhat saját egyéni összetevőket is.

   További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |Saját egyéni összetevőket hozhat létre a laborban található virtuális gépekhez.|
   | [Git-tárház hozzáadása az egyéni összetevők és Azure Resource Manager sablonok tárolásához Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevőket a saját privát git-tárházában.|

5. **A költségek szabályozása**
   
    Azure DevTest Labs lehetővé teszi, hogy házirendet állítson be a laborban a tesztkörnyezet által a tesztkörnyezetben létrehozható virtuális gépek maximális számának megadásához. 
   
    Ha a tesztelési csapat beállított munkatervtel rendelkezik, és az összes virtuális gépet le szeretné állítani a nap egy adott időpontjában, majd automatikusan újraindítja őket a következő napon, egyszerűen elvégezheti ezt az automatikus leállítás és a szabályzatok automatikus indításának beállításával. 
   
    Végül, ha az alkalmazásfejlesztés elkészült, egyetlen PowerShell-szkript futtatásával bármikor törölheti az összes virtuális gépet. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Szabályozza a költségeket a szabályzatok beállításával a laborban. |
   | [Az összes tesztkörnyezetben működő virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |A tesztelés befejezésekor törölje az összes labort egy művelettel.|

1. **Virtuális hálózat hozzáadása laborhoz** 
   
    A DevTest Labs létrehoz egy új virtuális hálózatot (VNET), amikor létrehoznak egy labort. Ha saját VNET konfigurált – például a ExpressRoute vagy a helyek közötti VPN használatával –, akkor ezt a VNET felveheti a labor virtuális hálózati beállításaiba, hogy elérhető legyen a virtuális gépek létrehozásakor.

    Emellett van olyan Azure Active Directory tartományhoz való csatlakozás, amely egy virtuális géphez a virtuális gép létrehozásakor csatlakozik egy tartományhoz. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása Azure DevTest Labsban](devtest-lab-configure-vnet.md) |Megtudhatja, hogyan konfigurálhat virtuális hálózatot Azure DevTest Labs a Azure Portal használatával.|

6. **A labor megosztása az egyes tesztelőkkel**
   
    A laborok közvetlenül is elérhetők a tesztelők használatával megosztott hivatkozással. Még nem kell Azure-fiókkal rendelkezniük, feltéve, hogy [Microsoft-fiókük](devtest-lab-faq.md#what-is-a-microsoft-account)van. A tesztelők nem látják a más tesztelők által létrehozott virtuális gépeket.  
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Tesztelő hozzáadása laborhoz Azure DevTest Labs](devtest-lab-add-devtest-user.md) |A Azure Portal használatával tesztelő adhat hozzá a laborhoz.|
   | [Tesztelők hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Használja a PowerShellt a tesztelők a laborba való felvételének automatizálásához. |
   | [A laborra mutató hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogy a tesztelők hogyan férhetnek hozzá közvetlenül a laborhoz egy hiperhivatkozás használatával.|

7. **A laborok létrehozásának automatizálása további csapatok számára** 
   
    Egy Resource Manager-sablon létrehozásával automatizálhatja a laborok létrehozását, beleértve az egyéni beállításokat is, és a használatával újra és újra létrehozhatja az azonos Labs-t. 
   
    További információt az alábbi táblázatban található hivatkozásokra kattintva talál:
   
   | Feladat | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Resource Manager-sablonnal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre laborokat a Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
