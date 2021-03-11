---
title: CI/CD-folyamat beállítása Azure Cosmos DB emulátor felépítési feladattal
description: Oktatóanyag a buildelési és kiadási munkafolyamatok Azure DevOpsban a Cosmos DB Emulator buildelési feladatával való beállításáról
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: c7246511a88e2d2756a8ef56c5adf51ddbfd3e58
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560531"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>CI/CD-folyamat beállítása a Azure Cosmos DB Emulator Build feladattal az Azure DevOps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az emulátor használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. 

Az Azure DevOps-hoz készült Azure Cosmos DB Emulator-felépítési feladat lehetővé teszi, hogy a CI-környezetben is megegyezzen. A buildelési feladat segítségével összeállítási és kiadási munkafolyamatai részeként futtathat teszteket az emulátorban. A feladat elindít egy Docker-tárolót, amelyben már fut az emulátor, és végpontot biztosít a builddefiníció további része számára. Tetszőleges számú emulátorpéldányt hozhat létre és indíthat el, amelyek mind külön tárolóban futnak. 

Ez a cikk bemutatja, hogyan állíthat be CI-folyamatot az Azure DevOpsban egy ASP.NET-alkalmazás számára, amely a Cosmos DB Emulator buildelési feladatával futtat teszteket. A CI-folyamat egy Node.js vagy egy Python-alkalmazáshoz való beállításához hasonló módszer használható. 

## <a name="install-the-emulator-build-task"></a>Az emulátor buildelési feladatának telepítése

A buildelési feladatot használat előtt telepítenie kell az Azure DevOps-szervezetben. Keresse meg az **Azure Cosmos DB Emulator** bővítményt a [Marketplace-en](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview), és kattintson a **Get it free** (Ingyenes beszerzés) elemre.

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Az Azure Cosmos DB Emulator buildelési feladatának megkeresése és telepítése az Azure DevOps Marketplace-ről":::

Ezután válassza ki a szervezetet, amelyben telepíteni kívánja a bővítményt. 

> [!NOTE]
> Egy bővítmény Azure DevOps-szervezetbe való telepítéséhez a fiók tulajdonosának vagy a projekt-gyűjtemény rendszergazdájának kell lennie. Ha nem rendelkezik engedélyekkel, de a fiók tagja, ehelyett kérheti a bővítményt. [Részletek](/azure/devops/marketplace/faq-extensions)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Válassza ki azt az Azure DevOps-szervezetet, amelyben telepíteni kívánja a bővítményt":::

## <a name="create-a-build-definition"></a>Builddefiníció létrehozása

Most, hogy telepítette a bővítményt, jelentkezzen be az Azure DevOps-szervezetbe, és keresse meg a projektet a projektek irányítópulton. Hozzáadhat egy új [buildfolyamatot](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav) a projekthez, vagy módosíthat egy meglévőt is. Ha már rendelkezik buildfolyamattal, továbbléphet [az Emulator buildelési feladatának hozzáadása egy builddefinícióhoz](#addEmulatorBuildTaskToBuildDefinition) részre.

1. Új builddefiníció létrehozásához lépjen az Azure DevOps **Builds** (Buildek) lapjára. Válassza az **+ új lehetőséget.** \> **Új buildfolyamat**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Új buildfolyamat létrehozása":::

2. Válassza ki a kívánt értékeket a következőkhöz: **Source** (Forrás), **Team project** (Csapatprojekt), **Repository** (Adattár) és **Default branch for manual and scheduled builds** (Manuális és ütemezett buildelések alapértelmezett ága). Miután megadta a kívánt beállításokat, kattintson a **Continue** (Folytatás) elemre.

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Válassza ki a csapatprojektet, az adattárat és az ágat a buildfolyamathoz.":::

3. Végül válassza ki a buildfolyamathoz használni kívánt sablont. Ebben az oktatóanyagban az **ASP.NET** sablont választjuk. Most már rendelkezik egy olyan összeállítási folyamattal, amely a Azure Cosmos DB Emulator Build feladatának használatára beállítható. 

> [!NOTE]
> A CI számára kijelölni kívánt ügynök-készletnek a Windows rendszerhez készült Docker-nek kell lennie, kivéve, ha a telepítést manuálisan végzik el egy korábbi feladatban a CI részeként. Tekintse meg a [Microsoft által üzemeltetett ügynökökkel](/azure/devops/pipelines/agents/hosted?tabs=yaml) foglalkozó cikket az ügynök-készletek kiválasztásához. Javasoljuk, hogy kezdje a következővel: `Hosted VS2017` .

Azure Cosmos DB emulátor jelenleg nem támogatja az üzemeltetett VS2019-ügynök készletét. Az emulátor azonban már telepítve van a VS2019, és a következő PowerShell-parancsmagokkal elindítja az emulátort. Ha a VS2019 használata során problémákba ütközik, az [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) csapata segítségért tájékozódhat:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Feladat hozzáadása buildfolyamathoz

1. Mielőtt hozzáadhatna egy feladatot a buildfolyamathoz, hozzá kell adnia egy ügynökfeladatot. Keresse meg a buildfolyamatot, kattintson a három pont (**...**) elemre, és válassza az **Add an agent job** (Ügynökfeladat hozzáadása) lehetőséget.

1. Ezután válassza ki az **+** ügynök feladata melletti szimbólumot az emulátor felépítési feladatának hozzáadásához. Keressen a **cosmos** kifejezésre a keresőmezőben, válassza ki az **Azure Cosmos DB Emulator** lehetőséget, és adja hozzá az ügynökfeladathoz. A buildelési feladat elindít egy tárolót, amelyben már fut a Cosmos DB Emulator egy példánya. Az Azure Cosmos DB Emulator-feladatot minden olyan feladat elé kell ütemezni, amelyhez az emulátor futása szükséges.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Az emulátor buildelési feladatának hozzáadása a builddefinícióhoz":::

Ebben az oktatóanyagban a feladatot a legelején adjuk hozzá, hogy biztosítsuk az emulátor elérhetőségét a tesztjeink futtatása előtt.

### <a name="add-the-task-using-yaml"></a>Feladat hozzáadása a YAML használatával

Ez a lépés nem kötelező, és csak akkor szükséges, ha YAML feladat használatával állítja be a CI/CD-folyamatot. Ilyen esetekben a YAML feladat a következő kódban látható módon adható meg:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Tesztek konfigurálása az emulátor használatához

Most konfigurálni fogjuk a tesztjeinket az emulátor használatához. Az emulátor buildelési feladata exportál egy környezeti változót (CosmosDbEmulator.Endpoint), amelyre a buildfolyamat összes további feladata küldhet kéréseket. 

Ebben az oktatóanyagban a [Visual Studio tesztelési feladatával](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) futtatunk olyan egységteszteket, amelyek **.runsettings** fájllal lettek konfigurálva. Az egységtesztek beállításáról a [dokumentációban](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017) talál további információt. A dokumentumban használt teljes Todo-alkalmazás kódjának mintája elérhető a [githubon](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Az alábbiakban mutatunk egy példát a **.runsettings** fájlra, amely definiálja egy alkalmazás egységtesztjeibe továbbítandó paramétereket. Figyelje meg, hogy a használt `authKey` változó az emulátor [jól ismert kulcsa](./local-emulator.md#authenticate-requests). Az `authKey` az emulátor buildelési feladatának várt kulcsa, és definiálva kell lennie a **.runsettings** fájlban.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Ha olyan alkalmazáshoz állít be CI/CD-folyamatot, amely a MongoDB Azure Cosmos DB API-ját használja, a csatlakozási karakterlánc alapértelmezés szerint a 10255 portszámot tartalmazza. Ez a port azonban jelenleg nincs megnyitva, Alternatív megoldásként az 10250-es portot kell használnia a kapcsolat létrehozásához. A MongoDB-kapcsolatok karakterláncának Azure Cosmos DB API-je változatlan marad, kivéve a támogatott portszámot 10250 10255 helyett.

Ezekre a paraméterekre (`TestRunParameters`) az alkalmazás tesztelési projektjei egy `TestContext` tulajdonsággal hivatkoznak. Íme egy példa a Cosmos DB-ben futó tesztre.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Lépjen az Execution Options (Végrehajtási beállítások) lapra a Visual Studio-tesztfeladatban. A **Settings file** (Beállításfájl) lehetőségnél adja meg, hogy a tesztek konfigurálása a **.runsettings** fájllal történjen. Az **Override test run parameters** (A tesztfuttatás paramétereinek felülbírálása) beállításnál adja hozzá az `-endpoint $(CosmosDbEmulator.Endpoint)` értéket. Ezzel úgy konfigurálja a tesztelési feladatot, hogy az emulátor buildelési feladatának végpontjára hivatkozzon a **.runsettings** fájlban definiált végpont helyett.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="A végpont változójának felülbírálása az emulátor buildelési feladatának végpontjával":::

## <a name="run-the-build"></a>A build futtatása

Most a **Save and queue** (Mentés és üzenetsorba helyezés) elemre kattintva mentse és helyezze az üzenetsorba a buildet. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="A képernyőképen megjelenik a Save & üzenetsor kiválasztására szolgáló Build.":::

Figyelje meg, hogy amint a build elindul, a Cosmos DB emulátor elkezdi a telepített emulátort tartalmazó Docker-rendszerkép lekérését. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="A képernyőfelvételen a Cosmos D B-emulátor feladatát láthatja.":::

Miután a build létrejött, láthatja, hogy a buildelési feladatból a Cosmos DB emulátorban futó tesztek sikeresen zárultak.

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="A képernyőképen az összefoglalás lapon látható a progresszió értéke.":::

## <a name="next-steps"></a>Következő lépések

További információ az emulátor helyi fejlesztéshez és teszteléshez való használatáról: [Az Azure Cosmos DB Emulator használata helyi fejlesztéshez és teszteléshez](./local-emulator.md).

Az Emulator TLS/SSL-tanúsítványok exportálásával kapcsolatban lásd: [a Azure Cosmos db Emulator-tanúsítványok exportálása Java, Python és Node.jshasználatával ](./local-emulator-export-ssl-certificates.md)
