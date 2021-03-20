---
title: Kötelező összetevők megadása a Azure DevTest Labshoz | Microsoft Docs
description: Megtudhatja, hogyan adhatja meg azokat a kötelező összetevőket, amelyeket telepíteni kell, mielőtt a tesztkörnyezetben lévő virtuális gépekre (VM) telepíti a felhasználó által kiválasztott összetevőket.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480303"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>A laborhoz tartozó kötelező összetevők megadása Azure DevTest Labs
A labor tulajdonosaként megadhatja a laborban létrehozott összes gépen alkalmazott kötelező összetevőket. Képzelje el, hogy hol szeretné, hogy a laborban lévő egyes gépek a vállalati hálózathoz kapcsolódjanak. Ebben az esetben minden tesztkörnyezet-felhasználónak hozzá kell adnia egy tartományhoz való csatlakozást a virtuális gép létrehozása során, hogy a számítógépe csatlakoztatva legyen a vállalati tartományhoz. Más szóval a labor felhasználóknak lényegében újra létre kell hozniuk egy gépet abban az esetben, ha elfelejtik a kötelező összetevők alkalmazását a gépen. A labor tulajdonosaként a tartományhoz való csatlakozást kötelező összetevőként kell elvégeznie a laborban. Ez a lépés gondoskodik arról, hogy minden számítógép a vállalati hálózathoz kapcsolódjon, és megtakarítsa az időt és erőfeszítést a labor felhasználói számára.
 
Más kötelező összetevők tartalmazhatnak egy közös eszközt, amelyet a csapat használ, vagy egy platformhoz kapcsolódó biztonsági csomag, amelyet minden gépnek alapértelmezetten kell megadnia. Röviden, minden olyan általános szoftver, amelyet a laborban lévő összes gépnek kötelező összetevőnek kell lennie. Ha olyan számítógépről hoz létre egyéni rendszerképet, amely kötelező összetevőket tartalmaz, majd létrehoz egy új gépet az adott rendszerképből, a rendszer a kötelező összetevőket újra alkalmazza a gépen a létrehozás során. Ez a viselkedés azt is jelenti, hogy bár az egyéni rendszerkép régi, minden alkalommal, amikor létrehoz egy gépet az alkalmazásból, a rendszer a kötelező összetevők legfrissített verzióját alkalmazza a létrehozási folyamat során. 
 
Csak azok az összetevők támogatottak, amelyek nem rendelkeznek paraméterekkel. A labor-felhasználónak nem kell további paramétereket megadnia a labor létrehozása során, így egyszerűvé teszi a virtuális gépek létrehozásának folyamatát. 

## <a name="specify-mandatory-artifacts"></a>Kötelező összetevők megadása
A kötelező összetevőket külön is kiválaszthatja a Windows és a Linux rendszerű gépekhez. Ezeket az összetevőket attól függően is átrendezheti, hogy milyen sorrendben kívánja alkalmazni őket. 

1. A labor kezdőlapján válassza a **konfiguráció és házirendek** lehetőséget a **Beállítások** területen. 
3. Válassza a **kötelező** összetevők lehetőséget a **külső erőforrások** területen. 
4. Válassza a **Szerkesztés** lehetőséget a **Windows** szakaszban vagy a **Linux** szakaszban. Ez a példa a **Windows** kapcsolót használja. 

    ![Kötelező összetevők lap – Szerkesztés gomb](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Válasszon ki egy összetevőt. Ez a példa **7 – zip** lehetőséget használ. 
5. Az összetevő **hozzáadása** lapon válassza a **Hozzáadás** lehetőséget. 

    ![Kötelező összetevők lap – 7 – zip hozzáadása](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Egy másik összetevő hozzáadásához jelölje ki a cikket, majd válassza a **Hozzáadás** lehetőséget. Ez a példa hozzáadja a **Chrome-ot** a második kötelező összetevőként.

    ![Kötelező összetevők lap – Chrome hozzáadása](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. A **kötelező** összetevők lapon egy üzenet jelenik meg, amely megadja a kiválasztott összetevők számát. Ha az üzenetre kattint, megjelenik a kiválasztott összetevők. Válassza a **Mentés** lehetőséget a mentéshez. 

    ![Kötelező összetevők lap – összetevők mentése](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. A Linux rendszerű virtuális gépek kötelező összetevőinek megadásához ismételje meg a lépéseket. 
    
    ![Kötelező összetevők lap – Windows-és Linux-összetevők](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Ha **törölni** szeretne egy összetevőt a listáról, válassza a **... lehetőséget. (három pont)** a sor végén, és válassza a **Törlés** lehetőséget. 
10. A listában szereplő összetevők **átrendezéséhez** vigye az egérmutatót az összetevő fölé, majd válassza a **... lehetőséget. (három pont)** Ekkor megjelenik a sor elején, és húzza az elemet az új helyre. 
11. A kötelező összetevők a laborban való mentéséhez válassza a **Mentés** lehetőséget. 

    ![Kötelező összetevők lap – összetevők mentése a laborban](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. A tesztkörnyezet kezdőlapjára kattintva lépjen vissza a **konfiguráció és a házirendek** lapra (válassza az **X** lehetőséget a jobb felső sarokban).  

## <a name="delete-a-mandatory-artifact"></a>Kötelező összetevő törlése
Egy kötelező összetevő laborból való törléséhez hajtsa végre a következő műveleteket: 

1. A **Beállítások** területen válassza a **konfiguráció és szabályzatok** lehetőséget. 
2. Válassza a **kötelező** összetevők lehetőséget a **külső erőforrások** területen. 
3. Válassza a **Szerkesztés** lehetőséget a **Windows** szakaszban vagy a **Linux** szakaszban. Ez a példa a **Windows** kapcsolót használja. 
4. Válassza ki a felül található kötelező összetevők számát tartalmazó üzenetet. 

    ![Kötelező összetevők lap – válassza ki az üzenetet](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. A **kiválasztott** összetevők lapon válassza a **... lehetőséget. (három pont)** az összetevőt törölni kell, majd válassza az **Eltávolítás** lehetőséget. 
    
    ![Kötelező összetevők lap – összetevő eltávolítása](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. A **kiválasztott** összetevők oldal bezárásához kattintson **az OK gombra** . 
7. Válassza a **Mentés** elemet a **kötelező** összetevők lapon.
8. Szükség esetén ismételje meg a **Linux** -lemezképek lépéseit. 
9. Válassza a **Mentés** lehetőséget a tesztkörnyezet összes módosításának mentéséhez. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>A kötelező összetevők megtekintése virtuális gép létrehozásakor
A labor felhasználóként most megtekintheti a kötelező összetevők listáját, miközben létrehoz egy virtuális gépet a laborban. A labor tulajdonosa nem szerkesztheti vagy törölheti a laborban beállított kötelező összetevőket.

1. A labor kezdőlapján válassza az **Áttekintés** lehetőséget a menüből.
2. Ha hozzá szeretne adni egy virtuális gépet a laborhoz, válassza a **+ Hozzáadás** lehetőséget. 
3. Válasszon ki egy **alapképet**. Ez a példa a **Windows Server 1709-es verzióját** használja.
4. Figyelje meg, hogy megjelenik egy üzenet **, amely** tartalmazza a kiválasztott kötelező összetevők számát. 
5. Válassza **az** összetevők elemet. 
6. Győződjön meg arról, hogy a labor konfigurációjában és házirendjeiben megadott **kötelező** összetevők láthatók. 

    ![Virtuális gép létrehozása – kötelező összetevők](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [adhat hozzá egy git-](devtest-lab-add-artifact-repo.md)összetevőt a laborhoz.

