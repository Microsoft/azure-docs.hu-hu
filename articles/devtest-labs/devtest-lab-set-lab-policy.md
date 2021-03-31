---
title: Tesztkörnyezet-házirendek kezelése a Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan határozhat meg olyan labor-házirendeket, mint a virtuálisgép-méretek, a virtuális gépek maximális száma felhasználónként és a Leállítás automatizálása
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530352"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>A labor összes szabályzatának kezelése Azure DevTest Labs

Azure DevTest Labs segítségével szabályozhatja a költségeket, és a laborokban a házirendek (beállítások) kezelésével csökkentheti a hulladékokat a laborokban. Ez a cikk részletesen ismerteti az egyes házirendek beállításának lépéseit.  

## <a name="set-allowed-virtual-machine-sizes"></a>Engedélyezett virtuális gépek méretének beállítása
Az engedélyezett virtuálisgép-méretek beállítására szolgáló szabályzat segít a laboratóriumi hulladékok minimalizálásában azáltal, hogy meghatározza, hogy mely virtuálisgép-méretek engedélyezettek a laborban. Ha ez a szabályzat aktiválva van, a virtuális gépek létrehozásához csak a listából származó virtuálisgép-méretek használhatók.

1. A [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)válassza ki a labort, majd válassza a **konfiguráció és szabályzatok** lehetőséget.

    ![A labor konfigurációjának és házirendjeinek elérése](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. A labor **konfiguráció és házirendek** paneljén válassza az **engedélyezett virtuális gépek mérete** lehetőséget.
   
    ![Engedélyezett virtuális gépek méretei](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Ezzel **a** beállítással engedélyezheti a szabályzatot **, és** letilthatja azt.

1. Ha engedélyezi ezt a házirendet, válasszon ki egy vagy több virtuálisgép-méretet, amelyek a laborban hozhatók létre.

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-user"></a>Virtuális gépek beállítása felhasználónként
A **virtuális gépekre** vonatkozó házirend felhasználónként megadhatja, hogy az egyes felhasználók hány virtuális gépet hozhatnak létre. Ha a felhasználó egy virtuális gépet próbál létrehozni vagy igényelni, amikor a felhasználói korlát teljesült, egy hibaüzenet jelzi, hogy a virtuális gép nem hozható létre/nem igényelhető. 

1. A labor **konfiguráció és házirendek** paneljén válassza a **virtuális gépek felhasználónként** lehetőséget.
   
    ![Virtuális gépek felhasználónként](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Válassza az **Igen** lehetőséget a virtuális gépek számának korlátozásához felhasználónként. Ha nem szeretné, hogy felhasználónként korlátozza a virtuális gépek számát, válassza a **nem** lehetőséget. Ha az **Igen** lehetőséget választja, adjon meg egy numerikus értéket, amely azt jelzi, hogy hány virtuális gépet lehet létrehozni vagy igényelni egy felhasználó. 

1. Válassza az **Igen** lehetőséget, ha korlátozni szeretné az SSD-t (SSD-t) használó virtuális gépek számát. Ha nem szeretné korlátozni az SSD-t használó virtuális gépek számát, válassza a **nem** lehetőséget. Ha az **Igen** lehetőséget választja, adjon meg egy értéket, amely az SSD használatával létrehozható virtuális gépek számát jelzi. 

1. Kattintson a **Mentés** gombra.

## <a name="set-virtual-machines-per-lab"></a>Virtuális gépek beállítása tesztkörnyezetben
A **virtuális gépekre** vonatkozó szabályzat a laborban meghatározza az aktuális laborhoz létrehozható virtuális gépek számát. Ha a felhasználó egy virtuális gépet próbál létrehozni a labor korlátjának teljesülése esetén, a hibaüzenet azt jelzi, hogy a virtuális gép nem hozható létre. 

1. A labor **konfiguráció és házirendek** paneljén válassza a **virtuális gépek/labor** lehetőséget.
   
    ![Virtuális gépek/labor](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Válassza az **Igen** lehetőséget a virtuális gépek számának korlátozásához. Ha nem szeretné korlátozni a virtuális gépek számát laborban, válassza a **nem** lehetőséget. Ha az **Igen** lehetőséget választja, adjon meg egy numerikus értéket, amely azt jelzi, hogy hány virtuális gépet lehet létrehozni vagy igényelni egy felhasználó. 

1. Válassza az **Igen** lehetőséget, ha korlátozni szeretné az SSD-t (SSD-t) használó virtuális gépek számát. Ha nem szeretné korlátozni az SSD-t használó virtuális gépek számát, válassza a **nem** lehetőséget. Ha az **Igen** lehetőséget választja, adjon meg egy értéket, amely az SSD használatával létrehozható virtuális gépek számát jelzi. 

1. Kattintson a **Mentés** gombra.

## <a name="set-auto-shutdown"></a>Automatikus leállítás beállítása
Az automatikus leállítási szabályzat segít a laborban lévő virtuális gépek leállításának időpontjának meghatározásában.

1. A labor **konfigurációjának és házirendjeinek** ablaktábláján válassza az **automatikus leállítás** lehetőséget.
   
    ![Automatikus leállítás](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Ezzel **a** beállítással engedélyezheti a szabályzatot **, és** letilthatja azt.

1. Ha engedélyezi ezt a házirendet, az aktuális laborban lévő összes virtuális gép leállításához válassza ki az időt (és az időzónát).

1. Adja meg az **Igen** vagy a **nem** lehetőséget az értesítés 15 perccel a megadott automatikus leállítási idő előtti elküldéséhez. Ha az **Igen** lehetőséget választja, adjon meg egy WEBHOOK URL-végpontot vagy egy e-mail-címet, amely megadja, hogy hová szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és a Leállítás elhalasztására van lehetőség.

   A webhookokkal kapcsolatos további információkért lásd: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-bindings-http-webhook.md). 

1. Kattintson a **Mentés** gombra.

Alapértelmezés szerint, ha engedélyezve van, ez a házirend az aktuális laborban található összes virtuális gépre vonatkozik. Ha egy adott virtuális gépről szeretné eltávolítani a beállítást, nyissa meg a virtuális gép felügyeleti paneljét, és módosítsa az **automatikus leállítás** beállítást.

## <a name="set-auto-shutdown-policy"></a>Automatikus leállítási szabályzat beállítása
A labor tulajdonosaként beállíthatja a tesztkörnyezetben lévő összes virtuális gép leállítási ütemtervét. Ennek köszönhetően a nem használt gépektől (inaktív) származó költségek is megmenthetők. Központilag is kényszerítheti a leállítási szabályzatokat az összes laboratóriumi virtuális gépen, de mentheti a labor-felhasználókat arra, hogy ütemezést állítsanak be az egyes gépekhez. Ezzel a szolgáltatással beállíthatja a szabályzatot a labor-ütemtervre, kezdve a teljes hozzáférés szabályozása nélkül, a labor felhasználói számára. A labor tulajdonosaként a következő lépésekkel konfigurálhatja ezt a házirendet:

1. A labor kezdőlapján válassza a **konfiguráció és szabályzatok** lehetőséget.
2. Válassza a bal oldali menü **ütemtervek** szakaszának **automatikus leállítás házirend** elemét.
3. Válasszon egyet a lehetőségek közül. A következő fejezetekben részletesebb információkat talál ezekről a beállításokról: a set Policy csak a tesztkörnyezetben létrehozott új virtuális gépekre vonatkozik, nem pedig a már meglévő virtuális gépekre. 

    ![Automatikus leállítási házirend beállításai](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>A felhasználó beállít egy ütemtervet, és letilthatja
Ha a laborban ezt a házirendet állítja be, akkor a labor felhasználói felülbírálják vagy letiltják a labor-ütemtervet. Ez a beállítás lehetővé teszi a tesztkörnyezet felhasználói számára a virtuális gépek automatikus leállítási ütemtervének teljes körű felügyeletét. A labor felhasználói nem változnak a virtuális gép automatikus leállításának ütemterve lapon.

![Automatikus leállítási házirend-beállítás – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>A felhasználó beállít egy ütemtervet, és nem tud letiltani
Ha a laborban ezt a házirendet állítja be, a labor felhasználói felülbírálhatja a laborok ütemtervét. Azonban nem tudják letiltani az automatikus leállítási szabályzatot. Ezzel a beállítással gondoskodhat arról, hogy a laborban lévő összes gép automatikusan leállítási ütemterv alá kerüljön. A labor-felhasználók frissíthetik a virtuális gépek automatikus leállítási ütemtervét, és beállítják a leállítási értesítéseket.

![Automatikus leállítási házirend lehetőség – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>A felhasználónak nincs hozzáférése a labor rendszergazdája által beállított ütemtervhez
Ha a laborban ezt a házirendet állítja be, a labor-felhasználók nem tudják felülbírálni vagy letiltani a labor-ütemtervet. Ez a beállítás a tesztkörnyezet rendszergazdájának teljes körű vezérlését kínálja a laborban lévő összes gép számára. A labor felhasználói csak automatikus leállítási értesítéseket állíthatnak be a virtuális gépekhez.

![Automatikus leállítási házirend lehetőség – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Automatikus indítás beállítása
Az automatikus indítási házirend segítségével megadhatja, hogy mikor induljon el az aktuális laborban lévő virtuális gépek.  

1. A labor **konfigurációjának és házirendjeinek** ablaktábláján válassza az **Automatikus indítás** lehetőséget.
   
    ![A képernyőfelvételen a virtuális gép automatikus indítási beállításai láthatók.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Ezzel **a** beállítással engedélyezheti a szabályzatot **, és** letilthatja azt.

3. Ha engedélyezi ezt a házirendet, határozza meg az ütemezett kezdési időpontot, az időzónát és a hét azon napját, amelyre az idő vonatkozik. 

4. Kattintson a **Mentés** gombra.

Ha engedélyezve van, a rendszer nem alkalmazza automatikusan a szabályzatot az aktuális laborban található virtuális gépekre. Ha egy adott virtuális gépre szeretné alkalmazni a beállítást, nyissa meg a virtuális gép felügyeleti paneljét, és módosítsa az **automatikus indítási** beállítást.

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
[A virtuális gép létrehozásakor](devtest-lab-add-vm.md)megadhat egy lejárati dátumot. A **Speciális beállítások** területen válassza a naptár ikont annak a dátumnak a megadásához, amelyen a virtuális gép automatikusan törlődik. Alapértelmezés szerint a virtuális gép soha nem jár le.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
Miután meghatározta és alkalmazta a tesztkörnyezet különböző virtuálisgép-házirend-beállításait, a következő lépésekkel próbálkozhat:

* A [megosztott IP-címek ismertetése](devtest-lab-shared-ip.md) – ismerteti, hogyan használják a megosztott IP-címeket a DevTest Labs szolgáltatásban, hogy minimálisra csökkentsék a laboratóriumi virtuális gépekhez való kapcsolódáshoz szükséges nyilvános IP-címek számát.
* A [Cost Management konfigurálása](devtest-lab-configure-cost-management.md) – bemutatja, hogyan használható a **havi becsült költség trend** diagram  
  megtekintheti az aktuális hónap becsült költségeit és a tervezett hónap végi költségeket.
* [Egyéni rendszerkép létrehozása](devtest-lab-create-template.md) – virtuális gép létrehozásakor meg kell adnia egy alapot, amely lehet egyéni rendszerkép vagy Piactéri rendszerkép is. Ez a cikk bemutatja, hogyan hozhat létre egyéni rendszerképet egy VHD-fájlból.
* [Marketplace-lemezképek konfigurálása](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs támogatja a virtuális gépek Azure Marketplace-lemezképek alapján történő létrehozását. Ez a cikk bemutatja, hogyan határozható meg, hogy mely, ha van ilyen, Azure Marketplace-lemezképeket használhat a virtuális gépek tesztkörnyezetben való létrehozásakor.
* [Virtuális gép létrehozása laborban](devtest-lab-add-vm.md) – azt mutatja be, hogyan hozható létre virtuális gép egy alaprendszerképből (akár egyéni, akár piactér), és hogyan használhatók az összetevők a virtuális gépen.
