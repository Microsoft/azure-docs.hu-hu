---
title: Azure Lab Services használata a Hackathon
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Lab Services a ötletbörzékhez futtatásához használható laborok létrehozásához.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4648def48557d70547ef7d41d6c3e516671849e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452730"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Azure Lab Services használata a következő Hackathon
A Azure Lab Services könnyű és könnyen használható, így gyorsan üzembe helyezhet egy új, virtuális gépeket (VM) a Hackathon számára.  A következő ellenőrzőlista használatával biztosíthatja, hogy a Hackathon a lehető legzökkenőmentesebb legyen. Ezt az ellenőrzőlistát az informatikai részlegnek vagy a Hackathon-labor létrehozásával és felügyeletével megbízott szakoktatóknak kell elvégeznie. 

Ha labor-szolgáltatásokat szeretne használni a Hackathon, győződjön meg arról, hogy a labor-fiók és a labor legalább néhány nappal a Hackathon kezdete előtt jön létre. Továbbá kövesse az alábbi útmutatást:

## <a name="guidance"></a>Útmutató

- **Hozza létre a labort a résztvevőkhöz legközelebb eső régióban vagy helyen**. 

    A késés csökkentése érdekében hozza létre a labort egy olyan régióban, amely a legközelebb esik a Hackathon-résztvevőhöz.  Ha a résztvevői a világ minden részén találhatók, a legjobb ítélet alapján kell létrehoznia egy központilag működő labort.  Vagy Bontsa szét a Hackathon, hogy több Labs használatára legyenek alapozva a résztvevő helyein.
- **Válassza ki a használati igényeknek leginkább megfelelő számítási méretet**.

    Általában minél nagyobb a számítási méret, annál gyorsabb lesz a virtuális gép végrehajtása. A költségek korlátozása érdekében azonban a résztvevők igényei szerint ki kell választania a megfelelő számítási méretet. A rendelkezésre álló számítási méretek részleteiért tekintse meg [a virtuális gép méretezési információit a rendszergazdai útmutatóban](administrator-guide.md#vm-sizing) .
- **Konfigurálja a RDP\SSH a távoli asztali kapcsolathoz a Linux rendszerű virtuális gépekhez**.

    Ha a Hackathon Linux rendszerű virtuális gépeket használ, győződjön meg arról, hogy a távoli asztal engedélyezve van, így a résztvevők RDP (távoli asztali protokoll) vagy SSH (Secure Shell) használatával csatlakozhatnak a virtuális gépekhez. Ez a lépés csak Linux rendszerű virtuális gépek esetében szükséges, és a tesztkörnyezet létrehozásakor engedélyezni kell. Az RDP esetében előfordulhat, hogy a közzététel előtt telepítenie és konfigurálnia kell az RDP-kiszolgálót és a GUI-csomagokat a sablon virtuális gépén.  További információ: [útmutató a távoli asztal Linux rendszeren való engedélyezéséhez](how-to-enable-remote-desktop-linux.md).

- **Windows-frissítések telepítése és leállítása**. 

    Ha Windows-rendszerképet használ, javasoljuk, hogy telepítse a legújabb Windows-frissítéseket a labor [sablonjának virtuális](how-to-create-manage-template.md) gépén, mielőtt közzéteszi a laboratóriumok virtuális gépei létrehozásához. Biztonsági okokból, valamint annak megakadályozása, hogy a résztvevők megzavarják a Hackathon a frissítések telepítése során, ami azt is okozhatja, hogy a virtuális gépek újraindulnak. Érdemes lehet megfontolni a Windows-frissítések kikapcsolását is, hogy megakadályozza a jövőbeli megszakításokat. Lásd: [útmutató a Windows-frissítések telepítéséhez és konfigurálásához](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Döntse el, hogyan készíti el a tanulók a munkájukat**. 

    A tanulók mindegyike egy virtuális gépet rendelt a Hackathon élettartamára. A munkájukat közvetlenül a gépre menthetik, de javasoljuk, hogy a tanulók biztonsági mentést nyújtsanak a munkájuk elvégzéséhez, hogy hozzáférjenek a Hackathon. Egy külső helyre kell menteni például a OneDrive, a GitHubot és így tovább. A OneDrive használatához dönthet úgy, hogy automatikusan konfigurálja a tanulók számára a labor virtuális gépeken. A [OneDrive telepítéséhez és konfigurálásához](how-to-prepare-windows-template.md#install-and-configure-onedrive)tekintse meg a útmutató című témakört.
- A **virtuális gép kapacitása a résztvevők számának megfelelően állítható be**. 

    Győződjön meg arról, hogy a labor virtuálisgép-kapacitása a Hackathon várt résztvevők száma alapján van beállítva. Amikor közzéteszi a sablon virtuális gépet, több óráig is eltarthat, hogy az összes gépet létrehozza a laborban. Ezért javasoljuk, hogy a Hackathon elejére is lépjen tovább. További információ: [útmutató a labor kapacitásának frissítéséhez](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Döntse el, hogy szeretné-e korlátozni a tesztkörnyezet hozzáférését** 

    Amikor felhasználókat ad hozzá a laborhoz, a hozzáférés korlátozása beállítás alapértelmezés szerint engedélyezve van. Ehhez a funkcióhoz hozzá kell adnia az összes Hackathon-résztvevő e-mail-címét a listához, mielőtt a regisztrációs hivatkozás használatával regisztrálja és hozzáférhessen a laborhoz. Ha olyan Hackathon rendelkezik, amelyben nem tudja, kik a résztvevők az esemény előtt, letilthatja a hozzáférés korlátozása lehetőséget, amely lehetővé teszi, hogy bárki regisztrálhat a laborba a regisztrációs hivatkozás használatával. További információ: [útmutató a felhasználók hozzáadásához](how-to-configure-student-usage.md).

- Az **ütemterv, a kvóta és az automatikus leállítás beállításainak ellenőrzése**. 

    A labor Services több költségtakarékos megoldást biztosít a virtuális gépek használatának korlátozására. Ha azonban ezek a beállítások helytelenül vannak konfigurálva, akkor a labor virtuális gépei váratlanul leállnak. Győződjön meg arról, hogy ezek a beállítások megfelelően vannak konfigurálva a Hackathon, és ellenőrizze a következő beállításokat:

    **Schedule**: az [ütemterv](how-to-create-schedules.md) lehetővé teszi, hogy automatikusan vezérelje a labor gépek elindításának és leállításának idejét. Alapértelmezés szerint a rendszer nem konfigurálja az új Labor létrehozásakor ütemezett ütemtervet. Azonban győződjön meg arról, hogy a labor ütemterve be van állítva aszerint, hogy milyen értelme van a Hackathon.  Például, ha a Hackathon szombaton kezdődik a 8:00 órakor, és vasárnap a 5:00 ÓRAKOR ér véget, akkor létrehozhat egy olyan ütemtervet, amely automatikusan elindítja a 7:30 gépet szombaton (kb. 30 perccel a Hackathon kezdete előtt), és leállítja azt a vasárnapi 5:00 ÓRAKOR. Ehelyett dönthet úgy is, hogy egyáltalán nem használ ütemtervet.

    **Kvóta**: a [kvóta](how-to-configure-student-usage.md#set-quotas-for-users) azt határozza meg, hogy hány óra elteltével férhet hozzá a résztvevő egy virtuális géphez az ütemezett órákon kívül. Ha a kvóta eléri a résztvevőt, a rendszer automatikusan leállítja a gépet, és a résztvevő nem tud újraindulni, kivéve, ha a kvóta megnövekszik. Alapértelmezés szerint a labor létrehozásakor a kvóta 10 órára van beállítva. Ha még nem hozott létre ütemtervet, ügyeljen arra, hogy a kvótát úgy állítsa be, hogy elegendő időt biztosítson a Hackathon számára.

    Automatikus **Leállítás**: Ha engedélyezve van, az automatikus [Leállítás](how-to-enable-shutdown-disconnect.md) beállítás azt eredményezi, hogy a Windows rendszerű virtuális gépek bizonyos idő elteltével automatikusan leállnak, miután a tanuló leválasztotta az RDP-munkamenetet. Alapértelmezés szerint ez a beállítás le van tiltva.

- **Konfigurálja a tűzfal beállításait, hogy engedélyezze a kapcsolódást a labor virtuális gépekhez**. 

    Győződjön meg arról, hogy az iskolai vagy szervezet tűzfalbeállítások lehetővé teszik a RDP\SSH.-t használó labor virtuális gépekhez való csatlakozást További információ: [útmutató a hálózat tűzfala beállításainak konfigurálásához](how-to-configure-firewall-settings.md).

- **Telepítse a RDP\SSH-ügyfelet a résztvevő tablettákra, Mac-re, számítógépekre** stb.

    A Hackathon-résztvevőknek olyan RDP-és/vagy SSH-ügyféllel kell rendelkezniük, amely a labor-alapú virtuális gépekhez való kapcsolódáshoz használatos tablettán vagy laptopon telepítve van. Különböző RDP-vagy SSH-ügyfelek közül választhat, például:

    - A Microsoft **Távoli asztali kapcsolat** alkalmazás RDP-kapcsolatokhoz. A Távoli asztali kapcsolat alkalmazás különböző platformokon, például a Chromebook és a [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)gépeken is támogatott.
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) az SSH Linux rendszerű virtuális géphez való kapcsolódáshoz való használatához.
- **Tesztkörnyezet virtuális gépei ellenőrzése**. 

    Miután közzétette a labor virtuális gépeket, ellenőrizze, hogy megfelelően vannak-e konfigurálva. Ezt az ellenőrzést csak az egyik résztvevő labor Virtual Machines esetében kell elvégezni:

    1. Kapcsolódjon RDP and\or SSH használatával.
    2. Nyissa meg az alapszintű virtuális gép rendszerképének testreszabásához telepített további alkalmazásokat és eszközöket.
    3. A kiválasztott számítási méret alapján gondoskodhat néhány olyan alapszintű forgatókönyvről, amely a résztvevő által megteendő tevékenységekre jellemző.

## <a name="on-the-day-of-hackathon"></a>A Hackathon napján
Ez a szakasz a Hackathon napjának befejezésének lépéseit ismerteti.

1. **Indítsa el a labor virtuális gépeket**.

    Az operációs rendszertől függően a labor gépe akár 30 percet is igénybe vehet. Ennek eredményeképpen fontos a gépek elindítása a Hackathon elindítása előtt, így a résztvevőknek nem kell megvárniuk. Ha ütemtervet használ, győződjön meg arról, hogy a virtuális gépeket legalább 30 perccel korábban elindítják.
2. **Tanulók meghívásával regisztrálhatják és érhetik el a tesztkörnyezet virtuális gépét**. 

    Adja meg a résztvevői számára a következő információkat, hogy a résztvevők hozzáférhessenek a laboratóriumi virtuális gépekhez. 

    - A labor regisztrációs hivatkozása. 
    - A számítógéphez való kapcsolódáshoz használt hitelesítő adatok. Ez a lépés csak akkor érvényes, ha a labor Windows-alapú rendszerképet használ, és az összes virtuális gépet ugyanazzal a jelszóval konfigurálta.
    - Útmutatás arról, hogy a résztvevők hogyan and\or RDP-t a számítógépeik számára.

        További információ: [útmutató a felhasználók meghívásának elküldéséhez](how-to-configure-student-usage.md#send-invitations-to-users) és [a Linux rendszerű virtuális gépekhez való csatlakozáshoz](how-to-use-remote-desktop-linux-student.md). 

## <a name="next-steps"></a>Következő lépések
A labor-fiók létrehozásához a laborban kövesse az alábbi cikket: [oktatóanyag: labor-fiók beállítása Azure Lab Services](tutorial-setup-lab-account.md).
