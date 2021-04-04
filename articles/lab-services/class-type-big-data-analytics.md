---
title: Tesztkörnyezet beállítása big data elemzések megtanításához Azure Lab Services használatával | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy labort, hogy megtanítsa a big data elemzést a Hortonworks adatplatform (HDP) Docker-telepítésének használatával.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659896"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Tesztkörnyezet beállítása big data Analitika számára a HortonWorks-adatplatform Docker-telepítésének használatával

Ez a cikk bemutatja, hogyan állíthat be egy labort egy big data Analytics-osztály tanításához.  Az ilyen típusú osztályok esetében a tanulók megismerhetik, hogyan kezelheti a nagy mennyiségű adatmennyiséget, és hogyan alkalmazhat gépi és statisztikai tanulási algoritmusokat az adatelemzések kinyeréséhez.  A tanulók számára a fő cél az, hogy megtanulja az adatelemzési eszközök használatát, mint például a [Apache Hadoop nyílt forráskódú szoftvercsomag](https://hadoop.apache.org/) , amely eszközöket biztosít a Big Data tárolásához, kezeléséhez és feldolgozásához.

Ebben a laborban a tanulók a [Cloudera](https://www.cloudera.com/)által biztosított Hadoop népszerű kereskedelmi verzióját használják a [Hortonworks-adatplatform (HDP)](https://www.cloudera.com/products/hdp.html)néven.  A tanulók a [HDP homokozó 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) -t használják, amely a platform egyszerűsített, könnyen használható verziója, amely díjmentes, és a tanuláshoz és kísérletezéshez készült.  Habár ebben az osztályban Windows vagy Linux rendszerű virtuális gépeket (VM) is használhat a HDP-alapú homokozóval, ez a cikk bemutatja, hogyan használható a Windows.

A labor egy másik érdekes aspektusa, hogy a HDP-alapú Sandboxot a [Docker](https://www.docker.com/) -tárolók használatával helyezi üzembe a labor virtuális gépeken.  Az egyes Docker-tárolók saját elszigetelt környezetet biztosítanak a szoftveres alkalmazások futtatásához.  Elméletileg a Docker-tárolók olyanok, mint a beágyazott virtuális gépek, és könnyen üzembe helyezhetők és futtathatók a [Docker hub](https://www.docker.com/products/docker-hub)által biztosított tároló-lemezképeken alapuló különféle szoftveralkalmazások.  A HDP sandbox Cloudera üzembe helyezési parancsfájlja automatikusan lekéri a [HDP homokozó 3.0.1 Docker-rendszerképet](https://hub.docker.com/r/hortonworks/sandbox-hdp) a Docker hub-ból, és két Docker-tárolót futtat:
  - homokozó – HDP
  - homokozó – proxy

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).  Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért lásd: a [piactér-rendszerképek elérhetővé tétele a labor-készítők](./specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
|Marketplace-beli rendszerkép| Engedélyezze a Windows 10 Pro-rendszerkép használatát a labor-fiókjában.|

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A tantermi labor beállításakor használja az alábbi táblázatban szereplő beállításokat.  A tantermi laborok létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Közepes (beágyazott virtualizálás). Ez a virtuálisgép-méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.  Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is.|  
|Virtuálisgép-rendszerkép| Windows 10 Pro|

> [!NOTE] 
> A HDP-homokozó a Docker használatával történő üzembe helyezése óta közepes (beágyazott virtualizálás) szükséges:
>   - Windows Hyper-V beágyazott virtualizálás
>   - Legalább 10 GB RAM

## <a name="template-machine-configuration"></a>Sablon Számítógép-konfigurációja

A sablon számítógépének beállításához a következőket fogjuk:
- A Docker telepítése
- HDP-homokozó üzembe helyezése
- A Docker-tárolók automatikus elindítása a PowerShell és a Windows Feladatütemező használatával

### <a name="install-docker"></a>A Docker telepítése

Az ebben a szakaszban szereplő lépések a [Docker-tárolók üzembe helyezésére vonatkozó Cloudera-utasításokon](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)alapulnak. 

A Docker-tárolók használatához először telepítenie kell a Docker Desktopot a sablon virtuális gépre:

1. Kövesse az [Előfeltételek című szakaszban](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) ismertetett lépéseket a [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)telepítéséhez. 

    > [!IMPORTANT] 
    > Győződjön meg arról, hogy a **Windows-tárolók használata Linux-tárolók helyett** beállítás nincs bejelölve.

1. Győződjön meg arról, hogy a **Windows-tárolók és a Hyper-V-funkciók** be vannak kapcsolva.
   ![Windows-szolgáltatások be- és kikapcsolása](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. A Docker memória-konfigurációjának konfigurálásához kövesse a [Windows memóriája](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) szakaszának lépéseit.

    > [!WARNING]
    > Ha véletlenül bejelöli a **Windows-tárolók használata Linux-tárolók helyett** lehetőséget a Docker telepítésekor, nem fogja látni a memória konfigurációs beállításait.  Ennek kijavításához váltson Linux-tárolók használatára a [Windows rendszertálca Docker ikonjára kattintva](https://docs.docker.com/docker-for-windows/#docker-settings-dialog). Amikor megnyílik a Docker Desktop menü, válassza a **váltás Linux-tárolók** lehetőséget.
 
### <a name="deploy-hdp-sandbox"></a>HDP-homokozó üzembe helyezése

Ebben a szakaszban üzembe helyezi a HDP-munkaterületet, majd a böngésző használatával a HDP-munkaterületet is elérheti.

1. Győződjön meg arról, hogy telepítette a [git bash](https://gitforwindows.org/) -t az útmutató [Előfeltételek szakaszában](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) , mivel ez a következő lépések elvégzéséhez ajánlott.

1. A [Cloudera üzembe helyezési és telepítési útmutatójának](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)használatával végezze el a következő részben ismertetett lépéseket:
   
   -    HDP-homokozó üzembe helyezése
   -    HDP-sandbox ellenőrzése

    > [!WARNING] 
    > A HDP legújabb. zip fájljának letöltésekor ügyeljen arra, hogy *ne mentse a* . zip fájlt olyan könyvtár elérési útjába, amely szóközt tartalmaz.

    > [!NOTE] 
    > Ha kivételt kap az üzembe helyezési folyamat során, a **meghajtó nem lett megosztva**, meg kell osztania a C meghajtót a Docker használatával, hogy a HDP Linux-tárolói hozzáférhessenek a helyi Windows-fájlokhoz.  A probléma megoldásához [kattintson a Docker ikonjára a Windows rendszer tálcáján](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) a Docker Desktop menüjének megnyitásához, majd válassza a **Beállítások** lehetőséget.  Amikor megnyílik a **Docker beállításai** párbeszédpanel, válassza az **erőforrások > a fájlmegosztás** elemet, és keresse meg a **C** meghajtót.  Ezután megismételheti a HDP-homokozó üzembe helyezésének lépéseit.

1. Amint üzembe helyezi és futtatja a HDP-Homokozóhoz tartozó Docker-tárolókat, a böngésző elindításával és a HDP irányítópultjának megnyitásával [a Cloudera](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) utasításait követve elérheti a környezetet.

    > [!NOTE] 
    > Ezek az utasítások azt feltételezik, hogy először leképezte a homokozó környezet helyi IP-címét a sablonban lévő virtuális gépen lévő sandbox-hdp.hortonworks.com.  Ha **nem** hajtja végre ezt a leképezést, a alkalmazásban elérheti a homokozó kezdőlapját `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Docker-tárolók automatikus elindítása, ha a tanulók bejelentkeznek

Ahhoz, hogy könnyen használható élményt nyújtson a tanulók számára, egy PowerShell-szkriptet fogunk használni, amely automatikusan:
  - Elindítja a HDP sandbox Docker-tárolókat, amikor egy tanuló elindul és csatlakozik a labor virtuális géphez.
  - Elindítja a böngészőt, és megnyitja a homokozó kezdőlapját.
A Windows Feladatütemező használatával automatikusan futtathatja ezt a parancsfájlt, amikor egy diák bejelentkezik a virtuális gépre.
Ennek beállításához kövesse az alábbi lépéseket: [Big adatelemzési parancsfájlok](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Költségbecslés

Ha a labor költségeit szeretné megbecsülni, használhatja a következő példát.

Egy 25 tanulós osztály esetében, amely 20 órányi ütemezett időpontot és 10 órányi munkafeladatot vagy hozzárendelést használ, a labor ára a következő lesz:
  - 25 tanuló * (20 + 10) óra * 55 labor egység * 0,01 USD/óra = 412,50 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés

Ez a cikk végigvezeti a tesztkörnyezet létrehozásához szükséges lépéseken, amelyekkel a Docker-ben üzembe helyezett Hortonworks-adatplatformot használó big data Analytics osztályhoz kell létrehoznia.  Az ehhez az osztályhoz tartozó beállítás a hasonló adatelemzési osztályokhoz használható.  Ez a beállítás más típusú osztályokra is alkalmazható, amelyek a Docker-t használják a telepítéshez.

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)