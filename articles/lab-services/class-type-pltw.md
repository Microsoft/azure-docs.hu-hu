---
title: A Project beállítása a laborok bevezetésének módja a Azure Lab Services
description: Megtudhatja, hogyan állíthatja be a laborokat a projekt vezetésének tanításához.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024619"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>A laborok beállítása a projecthez a következőképpen: osztályok

A [Project Lead the way (PLTW)](https://www.pltw.org/) egy nonprofit szervezet, amely PreK &ndash; 12 tantervet biztosít a számítógépes tudományok, a mérnöki tudományok és Egyesült Államok a biogyógyászat terén.  Minden PLTW osztályban a tanulók számos különböző alkalmazást használnak a gyakorlati tanulási élmény részeként.  Számos alkalmazáshoz gyors CPU vagy bizonyos esetekben egy GPU szükséges.  Ebből a cikkből megtudhatja, hogyan állíthatja be a laborokat a következő PLTW osztályokba, amelyek jellemzően a 9 12 osztályba tartozó diákok számára elérhetők &ndash; :

- **A mérnöki tervezés bemutatása**

    A tanulók bemutatják a mérnöki tervezés folyamatát, amely magában foglalja a 3D-modellezéshez készült [Autodesk Inventor számítógépes (CAD)](https://www.autodesk.com/products/inventor/new-features) szoftver használatát.

- **A mérnöki alapelvek**
    
    A tanulók megismerhetik a mérnöki mechanizmusokat, a szerkezeti és az anyagi szilárdságot és az automatizálást.  Ez az osztály olyan szoftvereket használ, mint például az [MD szilárdtestek](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), a [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)és az [America 's Army szimuláció](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Mélyépítés és architektúra**

    A tanulók az [Autodesk Revit](https://www.autodesk.com/products/revit/overview) Architecture Design szoftver használatával megismerhetik az építkezést és a telephely kialakítását és fejlesztését.

- **Számítógép-integrált gyártás**

    A tanulók a robotika és az Automation szolgáltatással foglalkozó modern gyártási folyamatokat is megismerhetik.   Ebben az osztályban a tanulók az [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) és az [Autodesk Inventor számítógép-támogatású Manufacturer (CAM)](https://www.autodesk.com/products/inventor-cam/overview) szoftvert használják. 

- **Digitális elektronika**

    A tanulók elektronikus logikai áramköröket és eszközöket tanulnak a [National Device Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) szimulációs és áramköri tervezési szoftver használatával.

- **Mérnöki tervezés és fejlesztés**

    A tanulók egy teljes körű megoldáshoz járulnak hozzá a kutatási, tervezési és tesztelési célból, hogy azok a mérnökök egy panelén jelennek meg.  Ebben az osztályban a tanulók az [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) szoftvert használják.

- **Computer Science Essentials**

    A tanulók a számítási fogalmakhoz és eszközökhöz lettek bevezetve.  Ezek a blokk-alapú programozással kezdődnek, majd a szöveges kódolást használják a kódolási környezetek, például a [VEXcode V5-blokkok](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)használatával.

- **Számítógép-tudományi alapelvek**
    
    A diákok a [Microsoft Visual Studio Code fejlesztői környezet](https://code.visualstudio.com/)használatával növelhetik a [Python](https://www.python.org/) programozási szakértelmét. 

- **Computer Science A**

    Ebben az osztályban a tanulók a mobil alkalmazások fejlesztésével bővítik a programozási képességeiket.  Ebben az osztályban a [Microsoft Visual Studio Code fejlesztői környezet](https://code.visualstudio.com/)használatával tanulják meg a [javát](https://www.java.com/) .  A tanulók olyan emulátort is használnak, amely lehetővé teszi a felhasználók számára a Mobile App-kód futtatását és tesztelését.  A Azure Lab Services emulátorának beállításával kapcsolatos információkért [forduljon a Azure Lab Serviceshoz](mailto:AzLabsCOVIDSupport@microsoft.com).

Az osztály-szoftverek teljes listáját az egyes osztályok [PLTW webhelyén](https://www.pltw.org/pltw-software) teheti meg.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A laborok PLTW való beállításának megkezdéséhez szüksége lesz egy Azure-előfizetésre és egy labor-fiókra. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). 

Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [labor-fiók beállítása](./tutorial-setup-lab-account.md). Használhat meglévő labor-fiókot is.

Labor-fiók beállítása után létre kell hoznia egy külön labort minden olyan PLTW osztály-munkamenethez, amelyet az iskolája biztosít.  Azt is javasoljuk, hogy hozzon létre külön rendszerképeket az egyes PLTW-osztályokhoz.  További információ a laborok és a képek struktúrájáról: a [fizikai laborból a Azure Lab Servicesba való áttérés](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)utáni blogbejegyzés.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze a labor-fiókok beállításait az alábbi táblázatban leírtak szerint. Az Azure Marketplace-lemezképek engedélyezésével kapcsolatos további információkért lásd: [Az Azure Marketplace-lemezképek elérhetővé tétele a labor-készítők](./specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| -------------------- | ----- |
| Marketplace-beli rendszerkép | Engedélyezze a Windows 10 Pro-rendszerkép használatát a labor-fiókjában. |

<br>

### <a name="lab-settings"></a>Tesztkörnyezet beállításai
A PLTW osztályokhoz használni kívánt virtuális gép (VM) mérete a tanulók által az osztályban végzett munkaterhelések típusaitól függ.  A korábban felsorolt osztályok esetében ajánlott kis GPU-t (vizualizációt) és nagyméretű virtuálisgép-méreteket használni. A PLTW osztályokhoz tartozó Labs beállításakor tekintse meg a következő táblázatban található útmutatást:

| Tesztkörnyezet-beállítás | Érték és leírás | Osztály javaslata |
| ------------ | ------------------ | --- |
| Virtuális gép mérete | **Kis GPU (vizualizáció)**<br>A legjobb megoldás a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz és a kódoláshoz olyan keretrendszerekkel, mint az OpenGL és a DirectX. | Javasoljuk, hogy ezt a méretet a következő PLTW osztályokba használja: mélyépítés és architektúra, digitális elektronika, számítógép-integrált gyártás, mérnöki tervezés és fejlesztés.
| Virtuális gép mérete | **Nagy**<br>A legjobb megoldás a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagy memória-gyorsítótárat igénylő alkalmazások számára. | Azt javasoljuk, hogy ezt a méretet a következő PLTW osztályokba használja: Bevezetés a mérnöki tervezésbe, a mérnöki alapelvekbe, a számítástechnika alapjaiba, A számítástechnikai alapelvekbe és A számítógép-tudományba. |

<br>

### <a name="license-server"></a>Licenckiszolgáló
A korábban említett PLTW-osztályokban használt szoftverek többsége *nem* igényli a licenckiszolgáló elérését.  Ha azonban a következő szoftverekhez szeretné használni az Autodesk hálózati licencelési modellt, akkor hozzá kell férnie egy licenckiszolgálóra:
-   Revit
-   Feltaláló
-   Inventor CAM

Ha a hálózati licencelést az Autodesk szoftverrel szeretné használni, a [PLTW részletesen ismerteti](https://www.pltw.org/pltw-software) az Autodesk Network License Manager telepítését a licenckiszolgálón.  Ez a licenckiszolgáló általában a helyszíni hálózatban található, vagy egy Azure-beli virtuális gépen (VM) van tárolva az Azure virtuális hálózaton belül.

A licenckiszolgáló beállítása után meg kell adnia [a virtuális hálózatot](./how-to-connect-peer-virtual-network.md) a [labor-fiókjával](./tutorial-setup-lab-account.md). A tesztkörnyezet létrehozása *előtt* el kell végeznie a hálózati társítást, hogy a labor virtuális gépei hozzáférhessenek a licenckiszolgálóhoz, és fordítva.

Az Autodesk által generált licencfájl beágyazza a licenckiszolgáló MAC-címeit.  Ha úgy dönt, hogy egy Azure-beli virtuális gép használatával üzemelteti a licenckiszolgálót, fontos, hogy ellenőrizze, hogy a licenckiszolgáló MAC-címe nem változik-e. Ha a MAC-címe megváltozik, újra kell létrehoznia a licencelési fájlokat. Ha meg szeretné akadályozni, hogy a MAC-címe megváltozzon, tegye a következőket:

- [Állítson be egy statikus magánhálózati IP-címet és MAC-címet](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) a licenckiszolgálót futtató Azure-beli virtuális géphez.
- Ügyeljen arra, hogy a labor-fiókját és a licenckiszolgáló virtuális hálózatát egy olyan régióban vagy helyen állítsa be, amely elegendő virtuálisgép-kapacitással rendelkezik, így nem kell újabb régióba vagy helyre áthelyeznie ezeket az erőforrásokat.

További információ: [Licenckiszolgáló beállítása megosztott erőforrásként](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Sablon számítógép
A PLTW szükséges telepítési fájlok némelyike nagy méretű. Ha a fájlokat egy labor sablon virtuális gépre tölti le, a másolás során hosszú időt is igénybe vehet.

Ahelyett, hogy a telepítési fájlokat a sablon gépére töltse le és ott telepítsen mindent, javasoljuk, hogy a PLTW-lemezképeket a fizikai környezetben hozza létre.  Ezután importálhatja az egyéni lemezképeket a megosztott rendszerkép-katalógusba, így a laborok létrehozásához használhatja őket.  További információ: [Egyéni rendszerkép feltöltése a megosztott rendszerkép-gyűjteménybe](./upload-custom-image-shared-image-gallery.md).

A javaslat követése során jegyezze fel a laborok beállításának főbb feladatait:

1. A fizikai környezetben hozza létre az osztály rendszerképét.

    a.  A telepítési fájlok letöltéséhez és a szükséges szoftverek telepítéséhez használja a PLTW részletes lépéseit.

    > [!NOTE]    
    > Az Autodesk alkalmazások telepítésekor a telepíteni kívánt számítógépnek képesnek kell lennie kommunikálni a licenckiszolgálóval. Az Autodesk telepítővarázsló kérni fogja, hogy adja meg annak a számítógépnek a nevét, amelyen a licenckiszolgáló található.  Ha egy Azure-beli virtuális gépen üzemelteti a licenckiszolgálót, előfordulhat, hogy várnia kell az Autodesk telepítését a labor sablon virtuális gépre, hogy a telepítővarázsló hozzáférhessen a licenckiszolgálóval.

    b.  [Telepítse és konfigurálja a OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) vagy más olyan biztonsági mentési beállításokat, amelyeket az iskolája használhat.
    
    c.  [Windows-frissítések telepítése és konfigurálása](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Töltse fel az egyéni rendszerképet a [labor-fiókjához csatolt megosztott képtárba](./how-to-attach-detach-shared-image-gallery.md).

1.  Hozzon létre egy labort, majd válassza ki az előző lépésben feltöltött egyéni rendszerképet.

1.  A tesztkörnyezet létrehozása után indítsa el a virtuális gépet, és kapcsolódjon a sablonhoz, és ellenőrizze, hogy a rendszerkép a várt módon működik-e.

1.  Végül tegye közzé a sablon virtuális gépet a tanulók virtuális gépek létrehozásához.

## <a name="student-devices"></a>Tanulói eszközök
A tanulók a Windows rendszerű számítógépekről, Mac gépekről és Chromebook is csatlakozhatnak a laboratóriumi virtuális gépekhez. További útmutatásért lásd:

- [Kapcsolódjon a Windows rendszerből](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac-kapcsolat](./connect-virtual-machine-mac-remote-desktop.md)
- [Kapcsolat a Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Költségek
Vegyük például a PLTW osztályok becsült költségeit  Ez a becslés nem tartalmazza a licenckiszolgáló futtatásának költségeit vagy megosztott képkatalógus használatát. Tegyük fel, hogy van egy 25 tanulós osztálya, amelynek mindegyike 20 órányi ütemezett időponttal rendelkezik.  Az egyes tanulók további 10 kvótát is igénybe vehetik az ütemezett osztályokon kívüli munkahelyi vagy hozzárendelési feladatokhoz.  A becsült költségek:

- **Nagyméretű virtuális gép**

    25 tanuló &times; (20 ütemezett óra + 10 kvóta óra) &times; 70 labor egység &times; /óra 0,01 USD = USD 525.00

- **Kis GPU (vizualizáció)**

    25 tanuló &times; (20 ütemezett óra + 10 kvóta óra) &times; 160 labor egység &times; /óra 0,01 USD = USD 1200.00

> [!IMPORTANT] 
> A költségbecslés csak példaként szolgál.  A jelenlegi díjszabási információkért lásd: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> A PLTW számos osztálya olyan alkalmazásokat használ, amelyek böngészőn keresztül érhetők el, például az MIT app Inventor használatával.  Ezek a böngészőalapú alkalmazások nem igényelnek gyors PROCESSZORt vagy GPU-t, és bármely internetkapcsolattal rendelkező eszközről hozzáférhetnek.  Ha a tanulók ezeket az alkalmazásokat használják, javasoljuk, hogy a böngészőt a fizikai eszközön használják a böngészőben, a saját laboratóriumi virtuális gépén. A tanulók csak a gyors CPU-t vagy GPU-t igénylő alkalmazások esetében segítenek megőrizni a költségeket.

## <a name="next-steps"></a>Következő lépések

A labor beállítása során tekintse meg a következő cikkeket:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóták beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users) 
