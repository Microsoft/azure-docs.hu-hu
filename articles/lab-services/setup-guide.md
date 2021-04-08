---
title: Gyorsított tesztkörnyezet beállítási útmutatója Azure Lab Services
description: Ha Ön labor Creator, ez az útmutató segítséget nyújt a labor-fiókok gyors beállításához az iskolában.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021730"
---
# <a name="lab-setup-guide"></a>Labor telepítési útmutatója

Ebből az útmutatóból megtudhatja, hogyan hozhat létre labort tanulók számára az iskolában.

A laborok tanulók számára történő közzétételének folyamata akár több órát is igénybe vehet. A beállítási idő mennyisége a laborban létrehozni kívánt virtuális gépek (VM-EK) számától függ. Engedélyezzen legalább egy napot annak biztosítására, hogy a labor megfelelően működjön, és hogy elegendő idő legyen a tanulói virtuális gépek közzétételére.

## <a name="understand-the-lab-requirements-of-your-class"></a>Az osztály laboratóriumi követelményeinek megismerése

Új Labor létrehozása előtt vegye figyelembe az alábbi kérdéseket.

### <a name="what-software-requirements-does-the-class-have"></a>Milyen szoftverek szükségesek az osztályban?

Tekintse meg az osztály tanulási célkitűzéseit, és döntse el, hogy milyen operációs rendszert, alkalmazásokat és eszközöket kell telepíteni a labor virtuális gépeken. A labor virtuális gépek beállításához három lehetőség közül választhat:

- **Azure Marketplace-rendszerkép használata**: az Azure Marketplace több száz képet biztosít, amelyeket a labor létrehozásakor használhat. Egyes osztályok esetében előfordulhat, hogy az egyik rendszerkép már tartalmaz mindent, amire szüksége van az osztályhoz.

- **Új egyéni rendszerkép létrehozása**: létrehozhat egy saját egyéni rendszerképet az Azure Marketplace-rendszerkép kiindulási pontként való használatával. Ezután testre szabhatja, ha további szoftvereket telepít, és a konfigurációs módosításokat végez.

- **Meglévő egyéni rendszerkép használata**: felhasználhatja a korábban létrehozott egyéni lemezképeket, illetve a más rendszergazdák vagy oktatók által az iskolában létrehozott képeket. Egyéni lemezképek használatához a rendszergazdáknak létre kell hozniuk egy megosztott képtárat.  A megosztott képgyűjtemény az Egyéni rendszerképek mentéséhez használt tárház.

> [!NOTE]
> A rendszergazdák felelősek az Azure Marketplace-lemezképek és az Egyéni rendszerképek engedélyezéséhez, hogy azok használhatók legyenek. Az informatikai részleg koordinálásával ellenőrizze, hogy a szükséges lemezképek engedélyezve vannak-e. A létrehozott egyéni rendszerképek automatikusan engedélyezve lesznek a saját laboratóriumokban való használatra.

### <a name="what-hardware-requirements-does-the-class-have"></a>Milyen hardverkövetelmények vonatkoznak az osztályra?

Számos számítási méret közül választhat:

- **Beágyazott virtualizálási méretek**: lehetővé teszi, hogy a tanulók olyan virtuális gépekhez férjenek hozzá, amelyek több, beágyazott virtuális gépet tárolhatnak. Például használhatja ezt a számítási méretet a hálózatkezeléshez vagy az etikai hackelési osztályokhoz.

- **GPU-méretek**: lehetővé teszi, hogy a tanulók számítógép-igényű típusú alkalmazásokat használjanak. Ezt a lehetőséget például gyakran használják mesterséges intelligenciával és gépi tanulással.

A virtuális gép megfelelő méretének kiválasztásával kapcsolatos útmutatásért lásd:
- [Virtuális gép méretezése](./administrator-guide.md#vm-sizing)
- [Áthelyezés fizikai laborból a Azure Lab Servicesba](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Mivel a számítási méret rendelkezésre állása régiónként változik, a laborban kevesebb méret is elérhető. Általában a legkisebb számítási méretet kell kiválasztania, amely megfelel az igényeinek. A Azure Lab Services segítségével később is beállíthat egy új labort, amely nagyobb számítási kapacitással rendelkezik, ha szükséges.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Milyen függőségek vannak az osztályban a külső Azure-vagy hálózati erőforrásokon?
Előfordulhat, hogy a laboratóriumi virtuális gépeknek hozzá kell férniük a külső erőforrásokhoz, például egy adatbázishoz, egy fájlmegosztáshoz vagy egy licencelési kiszolgálóhoz.  Ha engedélyezni szeretné, hogy a labor virtuális gépei külső erőforrásokat használjanak, egyeztessen a rendszergazdával.

> [!NOTE]
> Érdemes figyelembe vennie, hogy a tesztkörnyezet függőségét a külső erőforrásokkal is csökkentheti, ha a hálózati erőforrásokat közvetlenül a virtuális gépen biztosítja. Ha például el szeretné távolítani az adatok külső adatbázisból való beolvasásának szükségességét, az adatbázist közvetlenül a virtuális gépre is telepítheti.  

### <a name="how-will-you-control-costs"></a>Hogyan fogja szabályozni a költségeket?
A labor Services az utólagos elszámolású díjszabási modellt használja, ami azt jelenti, hogy csak akkor kell fizetnie, amikor egy Tesztkörnyezet virtuális gépe fut. A költségek szabályozásához használja az alábbi lehetőségek bármelyikét vagy mindegyikét:

- **Schedule**: az ütemtervek használatával automatikusan szabályozhatja, hogy mikor induljon el a tesztkörnyezet virtuális gépei, és leálljon.
- **Kvóta**: kvóták használatával szabályozhatja, hogy a tanulók hány óra alatt férhetnek hozzá egy virtuális géphez az ütemezett órákon kívül.  Ha egy tanuló virtuális gépet használ, és eléri a kvótát, a virtuális gép automatikusan leáll.  A tanuló nem tudja újraindítani a virtuális gépet, kivéve, ha megnövelte a kvótát.
- **Automatikus leállítás**: Ha engedélyezi az automatikus leállítási beállítást, a Windows rendszerű virtuális gépek automatikusan leállnak a tanulók RDP protokoll (RDP) munkamenetből való leválasztását követően. Alapértelmezés szerint ez a beállítás le van tiltva.

A költségek szabályozásáról további információt a következő témakörben talál:
- [A költségek megbecslése](./cost-management-guide.md#estimate-the-lab-costs)
- [Költségek kezelése](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Hogyan fogja megmenteni a tanulókat a munkájukat?
Minden egyes tanuló egy virtuális gépet rendel hozzá a labor élettartamához. A tanulók megmenthetik a munkájukat:

- A virtuális géphez.
- Egy külső helyre, például OneDrive vagy GitHubra. A OneDrive automatikusan konfigurálható a tanulók számára a laboratóriumi virtuális gépeken.

> [!NOTE]
> Annak biztosítása érdekében, hogy a tanulók továbbra is hozzáférjenek a mentett munkához a laboron kívül, valamint az osztály befejezése után, azt javasoljuk, hogy mentse a munkáját egy külső adattárba.

### <a name="how-will-students-connect-to-their-vms"></a>Hogyan kapcsolódnak a tanulók a virtuális gépekhez?
A Windows rendszerű virtuális gépek RDP-kapcsolatai esetén azt javasoljuk, hogy a tanulók a [Microsoft távoli asztal ügyfelet](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)használják. A Távoli asztal-ügyfél támogatja a Mac, a Chromebook és a Windows rendszerű eszközöket.

Linuxos virtuális gépek esetén a tanulók a Secure Shell (SSH) vagy az RDP protokollt használhatják. Ahhoz, hogy a tanulók RDP-kapcsolaton keresztül csatlakozhassanak, telepítenie és konfigurálnia kell a szükséges RDP-és grafikus felhasználói felületi csomagokat.

### <a name="will-students-also-use-microsoft-teams"></a>A tanulók a Microsoft csapatait is használják?
A Azure Lab Services együttműködik a Microsoft Teams szolgáltatással, így a tanárok a csapatokban hozhatnak létre és kezelhetik a laborokat.  Ehhez hasonlóan a tanulók is elérhetik a laborokat a csapatokban.

További információ: [Azure Lab Services a Microsoft Teams](./lab-services-within-teams-overview.md)szolgáltatásban.

## <a name="set-up-your-lab"></a>Tesztkörnyezet beállítása

Miután megértette az osztály laborjának követelményeit, készen áll a beállításra. Ennek megismeréséhez kövesse az ebben a szakaszban található hivatkozásokat. A Labs a csapatokban való beállításának utasításait is megadhatja.

1. **Hozzon létre egy labort**. Lásd az alábbi oktatóanyagokat:
    - [Osztályterem-tesztkörnyezet létrehozása](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Labor létrehozása a Teams szolgáltatásban](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Ha az osztályban beágyazott virtualizálás szükséges, tekintse meg a [beágyazott virtualizálás engedélyezése](./how-to-enable-nested-virtualization-template-vm.md)című témakört.

1. **Képek testreszabása és laboratóriumi virtuális gépek közzététele**. A sablonként szolgáló virtuális gép nevű speciális virtuális géphez való kapcsolódáshoz lásd:
    - [Sablonos virtuális gép létrehozása és kezelése](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Megosztott rendszerkép-katalógus használata](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Ha Windows rendszert használ, tekintse [meg a Windows-sablon virtuális gép beállítása](./how-to-prepare-windows-template.md)című témakört is. Ezek az utasítások a tanulók OneDrive és Microsoft Office beállításának lépéseit foglalják magukban.

1. **Virtuális gépek készletének és kapacitásának kezelése**. A virtuálisgép-kapacitást egyszerűen, az osztálya igény szerint méretezheti. Ne feledje, hogy a virtuális gépek kapacitásának növelése több óráig is eltarthat, mivel az új virtuális gépek beállítása folyamatban van. Lásd az alábbi cikkeket:
    - [Virtuálisgép-készlet beállítása és kezelése](./how-to-set-virtual-machine-passwords.md)
    - [Virtuálisgép-készlet kezelése a labor Servicesben a Teams szolgáltatásban](./how-to-manage-vm-pool-within-teams.md)

1. **Labor-felhasználók hozzáadása és kezelése**. Ha felhasználókat szeretne felvenni a laborba, tekintse meg a következőt:
   - [Felhasználók hozzáadása a laborhoz](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Meghívók küldése a felhasználóknak](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [A labor Services felhasználói listájainak kezelése a Teams szolgáltatásban](./how-to-manage-user-lists-within-teams.md)

    További információ a tanulók által használható fiókok típusairól: Student- [fiókok](./how-to-configure-student-usage.md#student-accounts).
  
1. A **Cost Controls beállítása**. Az ütemterv beállításához, kvóták létrehozásához és az automatikus leállítás engedélyezéséhez tekintse meg a következő oktatóanyagokat:

   - [Ütemterv beállítása](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > A telepített operációs rendszertől függően előfordulhat, hogy a virtuális gép elindításához több percet is igénybe vehet. Annak biztosítása érdekében, hogy a labor virtuális gépek az ütemezett órákon belül használatra készek legyenek, javasoljuk, hogy 30 perccel előre indítsa el.

   - [Kvóták beállítása a felhasználók](./how-to-configure-student-usage.md#set-quotas-for-users) számára, és [további kvóták beállítása adott felhasználók](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users) számára
  
   - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Az ütemtervek és a kvóták nem érvényesek a sablon virtuális gépre, de az automatikus leállítási beállítások érvényesek. 
        > 
        > Labor létrehozásakor a rendszer létrehozza a sablon virtuális gépet, de nem indult el. Elindíthatja a sablon virtuális gépet, csatlakozhat hozzá, telepítheti az előfeltételként szükséges szoftvereket a laborhoz, majd közzéteheti azt. Amikor közzéteszi a sablon virtuális gépet, automatikusan leáll, ha még nem tette meg manuálisan. 
        > 
        > A sablon virtuális gépei a futásakor *költségekkel* járnak, ezért győződjön meg arról, hogy a sablon virtuális gépe leáll, amikor nincs szüksége a futtatására.

    - [Labor Services-ütemtervek létrehozása és kezelése a Teams szolgáltatásban](./how-to-create-schedules-within-teams.md) 

1. **Használja az irányítópultot**. Útmutatásért lásd: [az osztályterem Lab irányítópultjának használata](./use-dashboard.md).

    > [!NOTE]
    > Az irányítópulton látható becsült költség azt a maximális költséget mutatja, amelyet a Student Lab-használat várhatóan felmerül. Például nem számítunk fel díjat a tanulók által fel *nem* használt időtartamokra. A becsült költségek *nem* tükrözik a virtuális gép, a megosztott képkatalógus vagy a tesztkörnyezet létrehozója által elindítható használati díjakat.

## <a name="next-steps"></a>Következő lépések

A laborok kezelésének részeként tekintse meg a következő cikkeket:
- [Tantermi labor használatának nyomon követése](tutorial-track-usage.md)  
- [Osztályterem-tesztkörnyezet elérése](tutorial-connect-virtual-machine-classroom-lab.md)
