---
title: Rendszerkép-előállító futtatása az Azure DevOps Azure DevTest Labs
description: Ez a cikk a rendszerkép-előállító Azure DevOps (korábban Visual Studio Team Services) való futtatásához szükséges előkészületeket ismerteti.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476138"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Rendszerkép-előállító futtatása az Azure DevOpsból
Ez a cikk a rendszerkép-előállító Azure DevOps (korábban Visual Studio Team Services) való futtatásához szükséges előkészületeket ismerteti.

> [!NOTE]
> Bármely előkészítési motor működni fog! Az Azure DevOps nem kötelező. A rendszerkép-előállító Azure PowerShell szkriptek használatával fut, így azt manuálisan is futtathatja a Windows Feladatütemező, más CI/CD rendszerek és így tovább.

## <a name="create-a-lab-for-the-image-factory"></a>Tesztkörnyezet létrehozása a rendszerkép-előállító számára
A rendszerkép-előállító beállításának első lépéseként létre kell hoznia egy labort Azure DevTest Labsban. Ez a tesztkörnyezet a lemezkép-előállító labor, ahol a virtuális gépeket hozunk létre, és az egyéni lemezképeket mentjük. Ezt a labort a teljes rendszerkép-előállító folyamat részeként kell figyelembe venni. Miután létrehozta a labort, mindenképpen mentse a nevet, mert később szüksége lesz rá.

## <a name="scripts-and-templates"></a>Parancsfájlok és sablonok
A csapat rendszerkép-előállítójának bevezetésének következő lépése az elérhetőség megismerése. A rendszerkép-előállító szkriptek és sablonok nyilvánosan elérhetők a [DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)-tárházban. Itt látható a darabok vázlata:

- Rendszerkép-előállító. Ez a gyökérmappa.
    - Configuration. A rendszerkép-előállító bemenetei
        - GoldenImages. Ez a mappa olyan JSON-fájlokat tartalmaz, amelyek az egyéni lemezképek definícióit jelölik.
        - Labs.jsbekapcsolva. Az a fájl, ahol a csapatok a megadott egyéni rendszerképek fogadására regisztrálhatnak.
- Parancsfájlok. A rendszerkép-előállító motorja.

Az ebben a szakaszban található cikkek további információt nyújtanak ezekről a parancsfájlokról és sablonokról.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps-csapat projekt létrehozása
Az Azure DevOps lehetővé teszi a forráskód tárolását, és egy helyen futtatja a Azure PowerShell. Ismétlődő futtatásokat is ütemezhet, hogy naprakészen tartsa a képeket. A problémák diagnosztizálásához jó lehetőség áll rendelkezésre az eredmények naplózására.  Az Azure DevOps használata nem követelmény, azonban használhat bármely olyan hám/motort, amely tud csatlakozni az Azure-hoz, és futtathatja Azure PowerShell.

Ha egy meglévő DevOps-fiókot vagy-projektet szeretne használni, hagyja ki ezt a lépést.

Első lépésként hozzon létre egy ingyenes fiókot az Azure DevOps. Látogasson el ide https://www.visualstudio.com/ , és válassza **az első lépések lehetőséget az** **Azure DevOps** (korábbi nevén vsts) területen. Válasszon egyedi fióknevet, és ügyeljen arra, hogy a git használatával kezelhesse a kódot. A létrehozást követően mentse az URL-címet a csapat projektbe. Itt látható egy minta URL-cím: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>A rendszerkép gyárának beadása a git-be
A rendszerkép-előállítóhoz tartozó összes PowerShell, sablon és konfiguráció a [nyilvános DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)-tárházban található. A leggyorsabb módszer a kód beszerzése az új Team-projektbe egy adattár importálása. Ez lekéri a teljes DevTest Labs-tárházat (így további dokumentumokat és mintákat kap).

1. Látogasson el az előző lépésben létrehozott Azure DevOps-projektre (az URL-cím a következőhöz hasonló **: https: \/ / \<accountname> . VisualStudio.com/MyFirstProject**).
2. Válassza **az adattár importálása** lehetőséget.
3. Adja meg a DevTest Labs **-tárház klónozási URL-címét** : `https://github.com/Azure/azure-devtestlab` .
4. Válassza az **Importálás** lehetőséget.

    ![Git-tárház importálása](./media/set-up-devops-lab/import-git-repo.png)

Ha úgy dönt, hogy csak a szükséges (a rendszerkép-előállító fájljait) szeretné bejelentkezni, kövesse az [itt](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) leírt lépéseket a git-tárház klónozásához, és csak a **parancsfájlok/ImageFactory** könyvtárban található fájlok leküldéséhez.

## <a name="create-a-build-and-connect-to-azure"></a>Build létrehozása és kapcsolódás az Azure-hoz
Ezen a ponton az Azure DevOps git-tárházában tárolt forrásfájlok találhatók. Most létre kell hoznia egy folyamatot a Azure PowerShell futtatásához. Ezek a lépések számos lehetőség közül választhatnak. Ebben a cikkben a Build definícióját használja az egyszerűség kedvéért, de együttműködik a DevOps Buildtel, a DevOps-kiadással (egy vagy több környezettel), más végrehajtó motorokkal, például a Windows Feladatütemezővel vagy bármely más, a Azure PowerShell végrehajtható eszközzel.

> [!NOTE]
> Fontos megjegyezni, hogy néhány PowerShell-fájl hosszú időt vesz igénybe, ha sok (10 +) egyéni rendszerkép hozható létre. Az ingyenes üzemeltetett DevOps-buildek és-kiadási ügynökök 30 perces időkorláttal rendelkeznek, így a sok rendszerkép kiépítésének megkezdése után nem használhatja az ingyenes üzemeltetett ügynököt. Ez az időtúllépési probléma a használni kívánt bármilyen célra vonatkozik, ezért érdemes ellenőrizni, hogy a hosszú ideig futó Azure PowerShell szkriptek esetében ki lehet-e terjeszteni a tipikus időtúllépéseket. Az Azure DevOps esetében fizetős üzemeltetett ügynököket használhat, vagy saját Build-ügynököt is használhat.

1. Az indításhoz válassza a **Build** beállítása elemet a DevOps-projekt kezdőlapján:

    ![A telepítő létrehozása gomb](./media/set-up-devops-lab/setup-build-button.png)
2. Adja meg a Build **nevét** (például: lemezképek létrehozása és továbbítása DevTest Labs számára).
3. Válasszon ki egy **üres** összeállítási definíciót, majd válassza az **alkalmaz** lehetőséget a Build létrehozásához.
4. Ezen a ponton **kiválaszthatja a Build** ügynököt.
5. **Mentse** a Build definícióját.

    ![Builddefiníció](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>A Build változóinak konfigurálása
A parancssori paraméterek egyszerűbbé tételéhez ágyazza be azokat a kulcs-értékeket, amelyek a rendszerkép-előállítót a Build változók készletére irányítják. Válassza a **változók** fület, és megtekintheti a több alapértelmezett változó listáját. Az alábbi lista tartalmazza az Azure-DevOps való belépéshez szükséges változókat:


| Változó neve | Érték | Jegyzetek |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Ez az adattár teljes elérési útja a **konfigurációs** mappába. Ha a fenti teljes tárházat importálta, a bal oldali érték helyes. Ellenkező esetben a frissítés a konfigurációs helyre mutat. |
| DevTestLabName | MyImageFactory | A laborban a lemezképek előállításához használt labor neve Azure DevTest Labs. Ha még nem rendelkezik ilyennel, hozzon létre egyet. Győződjön meg arról, hogy a labor ugyanahhoz az előfizetéshez tartozik, amelyhez a szolgáltatási végpont hozzáfér. |
| ImageRetention | 1 | Az egyes típusokból menteni kívánt lemezképek száma. Állítsa az alapértelmezett értéket 1-re. |
| MachinePassword | ******* | A virtuális gépek beépített rendszergazdai fiókjának jelszava. Ez egy átmeneti fiók, ezért győződjön meg róla, hogy biztonságos. Kattintson a jobb oldali kis zárolás ikonra a biztonságos karakterlánc biztosításához. |
| MachineUserName | ImageFactoryUser | A virtuális gépek beépített rendszergazdai fiókjának felhasználóneve. Ez egy átmeneti fiók. |
| StandardTimeoutMinutes | 30 | Az időkorlátot meg kell várni a normál Azure-műveletekre. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Annak az előfizetésnek az azonosítója, amelyben a labor létezik, valamint hogy a szolgáltatási végpont hozzáférhessen a szolgáltatáshoz. |
| VMSize | Standard_A3 | A **létrehozási** lépéshez használandó virtuális gép mérete. A létrehozott virtuális gépek átmenetiek. A méretnek a [laborhoz engedélyezettnek](devtest-lab-set-lab-policy.md)kell lennie. Ellenőrizze, hogy van-e elegendő [előfizetési magok kvótája](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Változók összeállítása](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
A következő lépés az egyszerű szolgáltatásnév beállítása. Ez a Azure Active Directory identitása, amely lehetővé teszi, hogy a DevOps-Build ügynök az Azure-ban működjön a felhasználó nevében. A beállításához először adja hozzá a Azure PowerShell Build lépést.

1. Válassza a **feladat hozzáadása** elemet.
2. **Azure PowerShell** keresése.
3. Ha megtalálta, a **Hozzáadás** gombra kattintva adja hozzá a feladatot a buildhez. Ha ezt teszi, a feladat megjelenik a bal oldalon a hozzáadáskor.

![PowerShell-lépés beállítása](./media/set-up-devops-lab/set-up-powershell-step.png)

Az egyszerű szolgáltatásnév beállításának leggyorsabb módja az, hogy az Azure-DevOps elvégezze számunkra.

1. Válassza ki az imént hozzáadott **feladatot** .
2. Az **Azure-kapcsolatok típusa** beállításnál válassza a **Azure Resource Manager** lehetőséget.
3. Válassza a **kezelés** hivatkozást az egyszerű szolgáltatásnév beállításához.

További információkat [ebben a blogbejegyzésben](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) talál. Ha kiválasztja a **kezelés** hivatkozást, a DevOps (második képernyőkép a blogbejegyzésben) a megfelelő helyen fog megjelenni az Azure-hoz való csatlakozás beállításához. Ha ezt a beállítást választja, ügyeljen arra, hogy **Azure Resource Manager szolgáltatási végpontot** válassza.

## <a name="complete-the-build-task"></a>A Build feladat befejezése
Ha kijelöli a felépítési feladatot, a jobb oldali ablaktáblán látható összes adat megjelenik, amelyet ki kell tölteni.

1. Először nevezze el a Build feladatot: **Create Virtual Machines**.
2. Válassza ki a létrehozott **szolgáltatásnevet** **Azure Resource Manager**
3. Válassza ki a **szolgáltatási végpontot**.
4. A **parancsfájl elérési útja** beállításnál válassza a **... lehetőséget. (három pont)** a jobb oldalon.
5. Navigáljon **MakeGoldenImageVMs.ps1** parancsfájlhoz.
6. A parancsfájl paramétereinek a következőhöz hasonlóan kell kinéznie: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![A Build definíciójának befejezése](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>A Build várólistája
Győződjön meg arról, hogy minden beállítás megfelelően van beállítva egy új Build sorba állításával. A Build futtatása közben váltson át a [Azure Portalre](https://portal.azure.com) , és válassza a rendszerkép-előállító laborjának **minden Virtual Machines** elemét annak ellenőrzéséhez, hogy minden megfelelően működik-e. A laborban három virtuális gépet kell létrehoznia.

![Virtuális gépek a laborban](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Következő lépések
A rendszerkép-előállító Azure DevTest Labs szerinti beállításának első lépése a Befejezés. Az adatsorozat következő cikkében a virtuális gépek általánosított és egyéni lemezképekre menthetők. Ezután az összes többi laborba el van osztva. Tekintse meg az adatsorozat következő cikkét: az [Egyéni rendszerképek mentése és a több laborba való terjesztése](image-factory-save-distribute-custom-images.md).
