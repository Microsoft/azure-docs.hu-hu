---
title: Népszerű forgatókönyvek Azure DevTest Labs használatára
description: Ez a cikk a Azure DevTest Labs és két általános elérési út használatát ismerteti a szervezeten belüli szolgáltatás használatának megkezdéséhez.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481595"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Népszerű forgatókönyvek Azure DevTest Labs használatára
A nagyvállalati igényektől függően a DevTest Labs konfigurálható úgy, hogy az megfeleljen a különböző követelményeknek.  Ez a cikk a népszerű forgatókönyveket ismerteti. Az egyes forgatókönyvek az DevTest Labs és az ilyen forgatókönyvek megvalósítására szolgáló erőforrások használatával járó előnyöket foglalják magukban.  

- Fejlesztői asztalok
- Tesztelési környezetek
- Tanfolyamok, gyakorlati laborok és ötletbörzékhez
- Munkaterülettel kapcsolatos vizsgálatok
- Osztályterem-tesztkörnyezetek

## <a name="developer-desktops"></a>Fejlesztői asztalok
A fejlesztőknek gyakran eltérő követelményekkel kell rendelkezniük a fejlesztői gépek különböző projektjeihez. A DevTest Labs segítségével a fejlesztők hozzáférhetnek a leggyakoribb forgatókönyvekhez konfigurált, igény szerinti virtuális gépekhez. A DevTest Labs a következő előnyöket biztosítja:

- A szervezetek közös fejlesztési gépeket biztosíthatnak a csapatok közötti konzisztencia biztosításához.
- A fejlesztők igény szerint gyorsan üzembe helyezhetik fejlesztői gépeikat, vagy [igényelhetnek egy meglévő előre konfigurált gépet](devtest-lab-add-claimable-vm.md).
- A fejlesztők önkiszolgáló módon hozhatnak létre erőforrásokat anélkül, hogy előfizetési szintű engedélyeket kellene fizetniük.
- Az informatikai részleg vagy [a rendszergazdák előre meghatározhatják a hálózati topológiát](devtest-lab-configure-vnet.md) , és a fejlesztők egyszerűen és intuitív módon használhatják azt anélkül, hogy speciális hozzáférésre lenne szükség.
- A fejlesztők igény szerint egyszerűen [testreszabhatják](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) a fejlesztői gépeket.
- A rendszergazdák a következőket biztosíthatják a költségek ellenőrzéséhez:
    - A fejlesztők [nem kaphatnak több virtuális](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) gépet, mint amennyit a fejlesztéshez szükségesek
    - A [virtuális gépek le vannak állítva](devtest-lab-set-lab-policy.md#set-auto-shutdown) , ha nincs használatban
    - Csak a virtuálisgép- [példányok méretének engedélyezése](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott Labs számára
    - A [Cost-célok és értesítések kezelése](devtest-lab-configure-cost-management.md) az egyes laborokhoz.

További információért lásd: [Azure DevTest Labs használata fejlesztők](devtest-lab-developer-lab.md)számára. 

## <a name="test-environments"></a>Tesztelési környezetek
A tesztelési környezetek vállalaton belüli létrehozása és kezelése jelentős erőfeszítést igényelhet. A DevTest Labs használatával a tesztelési környezetek könnyen létrehozhatók, frissíthetők vagy duplikálható. Lehetővé teszi a csapatok számára a teljes mértékben konfigurált környezetek elérését, amikor szükséges. Ebben az esetben a DevTest Labs a következő előnyöket biztosítja:

- A szervezetek közös tesztelési környezeteket biztosíthatnak a csapatok közötti konzisztencia biztosításához.
- A tesztelők a Windows-és Linux-környezetek gyors kiépítésével tudják tesztelni az alkalmazás legújabb verzióját az újrafelhasználható sablonok használatával.
- A rendszergazdák összekapcsolhatják a labort az Azure DevOps a DevOps-forgatókönyvek engedélyezéséhez
- A labor tulajdonosai a következőket biztosíthatják a költségek ellenőrzéséhez:
    - A [környezetekben lévő virtuális gépek leállnak](devtest-lab-set-lab-policy.md#set-auto-shutdown) , ha nincsenek használatban
    - Csak a virtuálisgép- [példányok méretének engedélyezése](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott Labs számára
    - A [Cost-célok és értesítések kezelése](devtest-lab-configure-cost-management.md) az egyes laborokhoz.

További információ: [Azure DevTest Labs használata a virtuális gépek és a Pásti tesztelési környezetekhez](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Munkaterülettel kapcsolatos vizsgálatok
A fejlesztők gyakran vizsgálják különböző technológiákat vagy infrastruktúra-kialakítást. Alapértelmezés szerint a DevTest Labs szolgáltatással létrehozott összes környezet a saját erőforráscsoporthoz jön létre. Az DevTest Labs-felhasználó csak olvasási hozzáférést kap ezekhez az erőforrásokhoz. Ahhoz azonban, hogy a fejlesztőknek több vezérlőre van szükségük, a tesztkörnyezet széles körű beállítása frissíthető, hogy [közreműködői jogokat](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) biztosítson a kezdeményező DevTest Labs-felhasználónak minden általuk létrehozott környezethez.  A DevTest Labs segítségével a fejlesztők automatikusan kapnak közreműködői engedélyt a laborban létrehozott környezetekhez.  Ez a forgatókönyv lehetővé teszi a fejlesztők számára, hogy a fejlesztési vagy tesztelési környezetekhez szükséges Azure-erőforrásokat adjanak hozzá és/vagy módosítsanak. Az [erőforrás](devtest-lab-configure-cost-management.md#view-cost-by-resource) -oldal díja lehetővé teszi, hogy a labor tulajdonosai nyomon kövessék a vizsgálatokhoz használt egyes környezetek költségeit.

További információ: [hozzáférési jogosultságok beállítása környezeti erőforráscsoporthoz](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Képzések, gyakorlati laborok és ötletbörzékhez 
A Azure DevTest Labsban található laborok nagyszerű tárolóként szolgálnak olyan átmeneti tevékenységekhez, mint például a workshopok, a gyakorlati laborok, a képzések vagy a ötletbörzékhez.  A szolgáltatás lehetővé teszi, hogy olyan labort hozzon létre, amelyben egyéni sablonokat biztosíthat, amelyekkel megegyező és elszigetelt környezetek hozhatók létre a képzéshez. Ebben az esetben a DevTest Labs a következő előnyöket biztosítja:

- A [szabályzatok](devtest-lab-set-lab-policy.md) biztosítják a tanulók számára, hogy csak a szükséges erőforrások számát kapják meg, például a virtuális gépeket.
- Az előre konfigurált és a létrehozott gépeket a gyakornoktól származó egyszeri művelettel kell [igényelni](devtest-lab-add-claimable-vm.md) .
- [A laborok megosztása a gyakornokok számára a tesztkörnyezet URL-címének](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)elérésével történik.
- A virtuális gépek [lejárati dátumai](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) biztosítják a gépek törlését, miután már nem szükségesek.
- Egyszerűen [törölheti a labort](devtest-lab-delete-lab-vm.md#delete-a-lab) és az összes [kapcsolódó erőforrást](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) , ha a képzés áthalad.

További információ: [Azure DevTest Labs használata a betanításhoz](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>A koncepció bizonyítása és a méretezett üzembe helyezés
Miután eldöntötte, hogy felfedezte a DevTest Labs-t, két általános útvonal áll rendelkezésére: a koncepció ellenőrzése és a méretezett üzembe helyezés.  

A **méretezett üzembe helyezés** hetek/hónapok közötti áttekintést és tervezést biztosít a DevTest Labs teljes vállalatának, amely több száz vagy akár több ezer fejlesztőt is tartalmaz.

A **koncepciók** üzembe helyezésének igazolása arra összpontosít, hogy egyetlen csapatnak a szervezeti értéket kell létrehoznia. Habár úgy tűnik, hogy egy méretezett központi telepítésre gondol, a megközelítés gyakrabban fog működni, mint a koncepció igazolása. Ezért javasoljuk, hogy kezdjen el kisméretű, Tanuljon az első csapattól, és ismételje meg ugyanezt a megközelítést két-három további csapattal, majd tervezze meg a kibővített telepítést a szerzett ismeretek alapján. A koncepció sikeres igazolása érdekében javasoljuk, hogy válasszon ki egy vagy két csapatot, és azonosítsa a forgatókönyveit (fejlesztői környezet és tesztelési környezetek), dokumentálja az aktuális használati eseteket, és telepítse a DevTest Labs szolgáltatást.

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket:

- [DevTest Labs-fogalmak](devtest-lab-concepts.md)
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)

