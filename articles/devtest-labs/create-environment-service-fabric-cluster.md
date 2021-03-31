---
title: Service Fabric-fürt környezetének létrehozása Azure DevTest Labs
description: Ismerje meg, hogyan hozhat létre környezetet önálló Service Fabric-fürttel, és hogyan indíthatja el és állíthatja le a fürtöt az ütemtervek használatával.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85484009"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Környezet létrehozása önálló Service Fabric-fürttel Azure DevTest Labs
Ez a cikk azt ismerteti, hogyan hozható létre környezet egy önálló Service Fabric-fürttel Azure DevTest Labsban. 

## <a name="overview"></a>Áttekintés
Az DevTest Labs az Azure Resource Management-sablonok által definiált, önálló tesztelési környezeteket hozhat létre. Ezek a környezetek a IaaS-erőforrásokat, például a virtuális gépeket és a Pásti-erőforrásokat, például a Service Fabric is tartalmazzák. A DevTest Labs lehetővé teszi, hogy a virtuális gépeket a virtuális gépek vezérlésére szolgáló parancsok megadásával felügyelje a környezetben. Ezek a parancsok lehetővé teszi, hogy a virtuális gépet egy adott időpontban indítsa el vagy állítsa le. Ehhez hasonlóan a DevTest Labs is segítheti Service Fabric-fürtök kezelését egy adott környezetben. Egy Service Fabric-fürtöt manuálisan vagy ütemterv szerint is elindíthat vagy leállíthat egy környezetben.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása
Service Fabric-fürtök a DevTest Labs környezetei segítségével hozhatók létre. Az egyes környezeteket a git-tárház Azure Resource Manager sablonja határozza meg. A DevTest Labs [nyilvános git-tárháza](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) tartalmazza a Resource Manager-sablont, amely Service Fabric fürtöt hoz létre a [ServiceFabric-cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) mappában. 

1. Először hozzon létre egy labort Azure DevTest Labsban az alábbi cikk utasításait követve: [tesztkörnyezet létrehozása](devtest-lab-create-lab.md). Figyelje meg, hogy a **nyilvános környezetek** beállítás alapértelmezés szerint **be van kapcsolva** . 
2. Az alábbi lépéseket követve ellenőrizze, hogy a Service Fabric szolgáltató regisztrálva van-e az előfizetéséhez:
    1. Válassza az **előfizetések** lehetőséget a bal oldali navigációs menüben, és válassza ki az **előfizetését**
    2. Az **előfizetés** lapon válassza az **erőforrás-szolgáltatók** lehetőséget a bal oldali menü **Beállítások** szakaszában. 
    3. Ha a **Microsoft. ServiecFabric** nincs regisztrálva, válassza a **regisztráció** lehetőséget. 
3. A labor **DevTest labor** lapján válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 
    
    ![Hozzáadás gomb az eszköztáron](./media/create-environment-service-fabric-cluster/add-button.png)
3. A **kiinduló kiválasztása** lapon válassza ki **Service Fabric labor-fürtöt** a listából. 

    ![Válassza ki Service Fabric labor-fürtöt a listában](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. A **beállítások konfigurálása** lapon hajtsa végre a következő lépéseket: 
    1. Adja meg a fürt **környezetének** **nevét** . Ez az Azure-beli erőforráscsoport neve, amelyben a Service Fabric-fürtöt létre kívánja hozni. 
    2. Válassza ki a fürt virtuális gépei **operációs rendszerét (os)** . Az alapértelmezett érték: **Windows**.
    3. Adja meg a fürt **rendszergazdájának** nevét. 
    4. **Jelszó** megadása a rendszergazdának. 
    5. A **tanúsítványnál** adja meg a tanúsítvány adatait Base64 kódolású karakterláncként. Tanúsítvány létrehozásához hajtsa végre a következő lépéseket:
        1. Töltse le a **Create-ClusterCertificate.ps1** fájlt a [git-tárházból](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Azt is megteheti, hogy a tárházat a gépen is klónozott. 
        2. Indítsa el a **PowerShellt**. 
        3. Futtassa a **ps1** -fájlt a parancs használatával `.\Create-ClusterCertificate.ps1` . Megjelenik egy, a Jegyzettömbben megnyitott szövegfájl, amely az oldalon található tanúsítványokkal kapcsolatos mezők kitöltéséhez szükséges információkat tartalmazza. . 
    6. Adja meg a **tanúsítványhoz tartozó jelszót**.
    7. Adja meg a tanúsítvány **ujjlenyomatát** .
    8. A **beállítások konfigurálása** lapon válassza a **Hozzáadás** lehetőséget. 

        ![Fürtkonfiguráció konfigurálása](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. A fürt létrehozása után egy erőforráscsoport jelenik meg az előző lépésben megadott környezet nevével. A kibontáskor megjelenik a Service Fabric-fürt. Ha az erőforráscsoport állapota megakad a **létrehozáskor**, válassza a **frissítés** lehetőséget az eszköztáron. A **Service Fabric-fürt** környezete egy 5 csomópontos 1 NodeType-fürtöt hoz létre Linux vagy Windows rendszeren.

    A következő példában a **mysfabricclusterrg** a kifejezetten a Service Fabric-fürthöz létrehozott erőforráscsoport neve. Fontos megjegyezni, hogy a labor-környezetek a létrehozás alatt álló erőforráscsoporthoz tartoznak. Ez azt jelenti, hogy a környezetet definiáló sablon, amely csak az újonnan létrehozott erőforráscsoport vagy [a labor általi használatra konfigurált virtuális hálózatok](devtest-lab-configure-vnet.md)erőforrásaihoz fér hozzá. A fenti minta az összes szükséges erőforrást létrehozza ugyanabban az erőforráscsoporthoz.

    ![Fürt létrehozva](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>A fürt elindítása vagy leállítása
A fürtöt elindíthatja vagy leállíthatja a DevTest labor oldalról, vagy a DevTest Labs által biztosított Service Fabric-fürt lapról. 

### <a name="from-the-devtest-lab-page"></a>A DevTest Lab oldaláról
A DevTest labor oldalán elindíthatja vagy leállíthatja a fürtöt a laborban. 

1. Válassza a **három pontot (...)** a Service Fabric-fürthöz az alábbi képen látható módon: 

    ![A fürt indítási és leállítási parancsai](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. A fürt **elindításához** és **leállításához** a helyi menüben két parancs jelenik meg. A Start parancs elindítja a fürt összes csomópontját. A leállítás parancs leállítja a fürt összes csomópontját. A fürt leállítása után a Service Fabric-fürt maga is üzemkész állapotban marad, de nem érhetők el csomópontok, amíg a tesztkörnyezet nem állítja vissza a Start parancsot a fürtön.

    A Service Fabric-fürtök tesztkörnyezetben való használatakor figyelembe kell venni néhány megfontolandó szempontot. A csomópontok újraindítása után eltarthat egy ideig, hogy a Service Fabric-fürt teljes mértékben kiszáradjon. Az adatok leállításról indításra való megőrzéséhez a virtuális géphez csatolt felügyelt lemezre kell menteni az adatait. A csatlakoztatott felügyelt lemezek használata teljesítménybeli következményekkel jár, ezért csak tesztelési környezetekben ajánlott. Ha az adattároláshoz használt lemez nincs támogatva, az adatvesztést követően a rendszer kiállítja a Leállítás parancsot a fürtön.

### <a name="from-the-service-fabric-cluster-page"></a>A Service Fabric-fürt lapról 
A fürt elindításának vagy leállításának másik módja van. 

1. Válassza ki a Service Fabric-fürtöt a DevTest Lab oldal fanézetében. 

    ![Fürt kiválasztása](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. A fürt **Service Fabric fürt** lapján megtekintheti a fürt elindításához vagy leállításához szükséges parancsokat az eszköztáron. 

    ![Parancsok elindítása vagy leállítása a Service Fabric-fürt lapon](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Az Automatikus indítás és az automatikus leállítás ütemtervének konfigurálása
A Service Fabric fürtök is elindíthatók vagy leállíthatók egy adott időpontban. Ez a tapasztalat hasonló a virtuális gépek tesztkörnyezetben való működéséhez. Ha pénzt szeretne megtakarítani, alapértelmezés szerint a laborban létrehozott összes fürt automatikusan leáll a labor [leállítási szabályzata](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)által meghatározott időpontban. A felülbíráláshoz megadhatja, hogy a fürtöt le kell-e állítani, vagy megadhatja a fürt leállításának időpontját. 

![Az Automatikus indítás és az automatikus leállítás meglévő Ütemtervei](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Az automatikus kezdési ütemterv engedélyezése
Az indítási ütemterv bekapcsolásához hajtsa végre a következő lépéseket:

1. Válassza az **Automatikus indítás** lehetőséget a bal oldali menüben
2. Jelölje be **a** be **lehetőséget a Service Fabric-fürt automatikus indításra való ütemezésének engedélyezéséhez**. Ez a lap csak akkor érhető el, ha a tesztkörnyezet tulajdonosa engedélyezte a felhasználóknak a virtuális gépek vagy Service Fabric fürtök automatikus indítását.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Automatikus csillag lap](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Automatikus leállítás beállításainak konfigurálása 
A Leállítás beállításainak módosításához hajtsa végre a következő lépéseket:

1. Válassza az **automatikus leállítás** lehetőséget a bal oldali menüben. 
2. Ezen az oldalon **engedélyezheti** az automatikus leállítást, **Ha az engedélyezve lehetőséget választja** . 
3. Ha **engedélyezve** van **a** beállítás, kövesse az alábbi lépéseket:
    1. A Leállítás **időpontjának** megadása.
    2. Itt adhatja meg az **időzónát** . 
    3. Itt adhatja meg, hogy szeretné-e, ha a DevTest Labs **értesítést** küldjön az automatikus leállítás előtt. 
    4. Ha az **Igen** lehetőséget választotta az értesítési beállításhoz, a **webhook URL** -címét és/vagy **e-mail-címét** az értesítések küldéséhez válassza. 
    5. Válassza az eszköztár **Save** (Mentés) elemét.

        ![Automatikus leállítás lap](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>A Service Fabric-fürt csomópontjainak megtekintése
A korábbi lépésekben látott Service Fabric-fürt lapja a DevTest Labs oldalára vonatkozik. Nem jeleníti meg a fürt csomópontjait. Ha további információkat szeretne látni a fürtről, kövesse az alábbi lépéseket:

1. A labor **DevTest labor** lapján válassza ki az **erőforráscsoportot** a **saját virtuális gépek** szakasz fanézetében.

    ![Erőforráscsoport kiválasztása](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Az **erőforráscsoport** lapon megtekintheti az erőforráscsoport összes erőforrását egy listában. Válassza ki a **Service Fabric-fürtöt** a listából. 

    ![Válassza ki a fürtöt a listában](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Megjelenik a fürt **Service Fabric-fürt** lapja. Ez az a lap, amelyet a Service Fabric biztosít. Megjelenik a fürtökkel kapcsolatos összes információ, például a csomópontok, a csomópontok típusai stb.

    ![Service Fabric fürt kezdőlapja](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Következő lépések
A környezetekkel kapcsolatos részletekért tekintse meg a következő cikkeket: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
