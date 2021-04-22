---
title: A GitHub Enterprise Server beállítása a Azure VMware Solution magánfelhőben
description: Megtudhatja, hogyan állíthatja be a GitHub Enterprise Servert a Azure VMware Solution magánfelhőben.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862938"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>A GitHub Enterprise Server beállítása a Azure VMware Solution magánfelhőben

Ez a cikk a GitHub Enterprise Server beállításának lépéseit, a GitHub.com [](https://github.com/)helyszíni verzióját, a magánfelhőben Azure VMware Solution lépéseit. A következő forgatókönyv egy GitHub Enterprise Server-példány, amely legfeljebb 3000 fejlesztőt képes kiszolgálni percenként 25 feladattal a GitHub Actions. Tartalmazza az előzetes verziójú funkciók  (például az előzetes verziók) (például a GitHub Actions. Az adott igényeknek megfelelően testre szabhatja a telepítést: [Installing GitHub Enterprise Server on VMware (A GitHub Enterprise Server telepítése VMware-re).](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)

## <a name="before-you-begin"></a>Előkészületek

A GitHub Enterprise Serverhez érvényes licenckulcs szükséges. Regisztrálhat próbaverziós [licencre.](https://enterprise.github.com/trial) Ha egy integráción keresztül kiterjeszti a GitHub Enterprise Server képességeit, akkor jogosult lehet egy ingyenes, ötüléses fejlesztői licencre. Ezt a licencet a [GitHub partnerprogramjában lehet alkalmazni.](https://partner.github.com/)

## <a name="installing-github-enterprise-server-on-vmware"></a>A GitHub Enterprise Server telepítése VMware-re

Töltse [le a GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) for VMware ESXi/vSphere (OVA) aktuális kiadását, és telepítse a letöltött [OVA-sablont.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="A GitHub futtatása a helyszínen vagy a felhőben.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Telepítse az OVA-sablont.":::  

Adjon egy felismerhető nevet az új virtuális gépnek, például GitHubEnterpriseServer. A kiadási adatokat nem kell a virtuális gép nevében szerepeltetni, mivel ezek az adatok elavadnak a példány frissítésekekor. Válassza ki az összes alapértelmezett beállítást (ezeket hamarosan szerkesztjük), és várja meg az OVA importálását.

Az importálás után [igény szerint](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) módosítsa a hardverkonfigurációt. Ebben a példaforgatókönyvben a következő konfigurációra lesz szükségünk.

| Erőforrás | Standard beállítás | Standard beállítás + "Bétaverziós funkciók" (Műveletek) |
| --- | --- | --- |
| vCPU-k | 4 | 8 |
| Memória | 32 GB | 61 GB |
| Csatlakoztatott tároló | 250 GB | 300 GB |
| Gyökértároló | 200 GB | 200 GB |

Az igényei azonban eltérőek lehetnek. A hardverekkel kapcsolatos szempontokról a [GitHub Enterprise Server VMware-re való telepítésével kapcsolatos útmutatóban található.](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) Lásd [még: Cpu- vagy memória-erőforrások hozzáadása a VMware-hez](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) a hardverkonfiguráció adott helyzet alapján való testreszabásához.

## <a name="configuring-the-github-enterprise-server-instance"></a>A GitHub Enterprise Server-példány konfigurálása

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Telepítse a GitHub Enterprise-t.":::  

Miután az újonnan kiépített virtuális gép (VM) be van kapcsolva, konfigurálja [azt a böngészőben.](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance) Fel kell töltenie a licencfájlt, és be kell állítania a felügyeleti konzol jelszavát. Mindenképpen írja fel ezt a jelszót egy biztonságos helyre.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="A rendszergazdai felület SSH-n keresztüli elérése.":::    

Javasoljuk, hogy legalább a következő lépéseket kövesse:

1. Töltsön fel egy nyilvános SSH-kulcsot a felügyeleti konzolra, hogy az SSH-n keresztül hozzáférjen a felügyeleti [rendszerhéjhoz.](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) 

2. [Konfigurálja a TLS-t](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) a példányon úgy, hogy megbízható hitelesítésszolgáltató által aláírt tanúsítványt használva használ.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="A példány konfigurálása.":::

Alkalmazza a beállításokat.  Amíg a példány újraindul, folytathatja a következő lépéssel: **Configuring Blob Storage for GitHub Actions.**

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Hozza létre a rendszergazdai fiókját.":::

A példány újraindítása után létrehozhat egy új rendszergazdai fiókot a példányon. Mindenképpen jegyezze fel a felhasználó jelszavát is.

### <a name="other-configuration-steps"></a>Egyéb konfigurációs lépések

A példány éles használatra való feljavatásos útjára az alábbi választható beállítási lépések ajánlottak:

1. Magas [rendelkezésre állás konfigurálása a](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) következő védelemhez:

    - Szoftveres összeomlások (operációs rendszer vagy alkalmazásszint)
    - Hardverhibák (tárhely, CPU, RAM és így tovább)
    - Virtualizálási gazdarendszerhibák
    - Logikailag vagy fizikailag sérült hálózat

2. [Konfigurálja](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) a [biztonsági mentési segédprogramokat,](https://github.com/github/backup-utils)amelyek az elsődleges példánytól különálló rendelkezésre állásban üzemeltetett, verzióval ellátott pillanatképeket szolgáltatásokat stb. biztosítják a vészhelyreállításhoz.
3. [Altartomány-elkülönítés beállítása](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)érvényes TLS-tanúsítvány használatával a helyközi parancsfájlok és egyéb kapcsolódó biztonsági rések mérséklése érdekében.

## <a name="configuring-blob-storage-for-github-actions"></a>Blob Storage konfigurálása GitHub Actions

> [!NOTE]
> GitHub Actions jelenleg korlátozott bétaverzióként érhető el [a GitHub Enterprise Server 2.22-es kiadásában.](https://docs.github.com/en/enterprise/admin/github-actions)

Külső blobtároló szükséges a GitHub Actions a GitHub Enterprise Serveren (jelenleg "bétaverzió" funkcióként érhető el). A műveletek ezt a külső blobtárolót használják az összetevők és naplók tárolására. A GitHub Enterprise Serveren a műveletek [Azure Blob Storage tárolószolgáltatóként](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (és másokként) is támogatják. Ezért kiépítünk egy új Azure [](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) Storage-fiókot BlobStorage típusú tárfióktípussal:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="A Azure Blob Storage kiépítése.":::

Miután az új BlobStorage erőforrás üzembe helyezése befejeződött, másolja ki és jegyezze fel a kapcsolati sztringet (amely a Hozzáférési kulcsok alatt érhető el). Erre a sztringre hamarosan szükségünk lesz.

## <a name="setting-up-the-github-actions-runner"></a>A GitHub Actions beállítása

> [!NOTE]
> GitHub Actions jelenleg korlátozott bétaverzióként érhető el [a GitHub Enterprise Server 2.22-es kiadásában.](https://docs.github.com/en/enterprise/admin/github-actions)

Ezen a ponton a GitHub Enterprise Server egy példányának futnia kell, és létre kell hoznunk egy rendszergazdai fiókot. Olyan külső blobtárolóval is kell GitHub Actions, amely az adatmegőrzéshez lesz használva.

Most hozzunk létre egy helyet, ahol GitHub Actions futtatni; itt is a Azure VMware Solution.

Először is létesítsünk egy új virtuális gépet a fürtön. A virtuális gépet az Ubuntu Server egy új kiadásán fogjuk [alapozni.](http://releases.ubuntu.com/20.04.1/)

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Új virtuális gép kiépítése.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Új virtuális gép kiépítése – 2. lépés.":::

A virtuális gép létrehozása után kapcsolja be, és csatlakozzon hozzá SSH-n keresztül.

Ezután telepítse az [Actions-futó alkalmazást,](https://github.com/actions/runner) amely egy feladatot futtat egy GitHub Actions munkafolyamatból. Azonosítsa és töltse le az Actions futtató legújabb Linux x64-kiadását a [kiadások](https://github.com/actions/runner/releases) oldalról vagy az alábbi gyors szkript futtatásával. Ehhez a szkripthez szükséges, hogy a curl és [a jq](https://stedolan.github.io/jq/) is jelen legyen a virtuális gépen.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Most már helyileg kell lennie egy fájlnak a virtuális gépen( actions-runner-linux-arm64- \* .tar.gz). Bontsa ki helyileg a tarballt:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Ez a kinyerés helyileg kicsomagol néhány fájlt, köztük egy és szkriptet is, amelyekre hamarosan `config.sh` `run.sh` visszatérünk.

## <a name="enabling-github-actions"></a>A GitHub Actions

> [!NOTE]
> GitHub Actions jelenleg korlátozott bétaverzióként érhető el [a GitHub Enterprise Server 2.22-es kiadásában.](https://docs.github.com/en/enterprise/admin/github-actions)

Majdnem kész! Konfiguráljuk és engedélyezze a GitHub Actions a GitHub Enterprise Server-példányon. A [GitHub Enterprise Server-példány](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)felügyeleti rendszerhéját SSH-kapcsolaton keresztül kell elérni, majd futtatni kell a következő parancsokat:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Következő futtatás:

`ghe-actions-check -s blob`

A következő kimenetnek kell Blob Storage" kimenet.

Most, GitHub Actions konfigurálva van, engedélyezze azt a felhasználók számára. Jelentkezzen be a GitHub Enterprise Server-példányba rendszergazdaként, és válassza a ![ Rakéta ikont.](media/github-enterprise-server/rocket-icon.png) a lap jobb felső sarkában. A bal oldali oldalsávon válassza a **Vállalati** áttekintés, majd a Szabályzatok, Műveletek **lehetőséget,** és válassza a Műveletek engedélyezése minden szervezet számára **lehetőséget.**

Ezután konfigurálja a futót a **Saját üzemeltetett futók lapról.** Válassza **az Új hozzáadása,** majd az Új futó **lehetőséget** a legördülő menüből.

A következő oldalon futtatandó parancsok egy készletét fogja látni. Csak ki kell  másolnunk a parancsot a futó konfigurálásáért, például:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Másolja ki `config.sh` a parancsot, és illessze be egy munkamenetbe az Actions-futón (korábban létrehozott).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Műveletekfutta.":::

Futtassa a run.sh parancsot *a futó* futtatásához:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Futtassa a futót.":::

Ahhoz, hogy ez a futó elérhető legyen a vállalatnál található szervezetek számára, szerkessze a szervezet hozzáférését:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="A futói hozzáférés szerkesztése.":::

Itt elérhetővé fogjuk tenni az összes szervezet számára, de a hozzáférést a szervezetek egy részéhez, sőt adott adattárakhoz is korlátozhatja.

## <a name="optional-configuring-github-connect"></a>(Nem kötelező) A GitHub Connect konfigurálása

Bár ez a lépés nem kötelező, javasoljuk, ha a szolgáltatásban elérhető nyílt forráskódú műveleteket GitHub.com. Lehetővé teszi, hogy mások munkája alapján építsen ezekre az újrahasználható műveletekre a munkafolyamatokban.

A GitHub Connect engedélyezéséhez kövesse a Következő lépéseit: Enabling automatic access to GitHub.com actions using GitHub Connect (Automatikus hozzáférés engedélyezése GitHub.com műveletekhez a [GitHub Connect használatával).](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)

Ha a GitHub Connect engedélyezve van, válassza a Kiszolgálót, hogy a munkafolyamat GitHub.com **műveleteket használja.**

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Engedélyezheti a munkafolyamat-GitHub.com műveleteket.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Az első munkafolyamat beállítása és futtatása

Most, hogy az Actions és a GitHub Connect be van állítva, használjuk ezt a munkát. Lássunk egy példa-munkafolyamatot, amely a kiváló [octokit/request-action](https://github.com/octokit/request-action)műveletre hivatkozik, lehetővé téve a GitHub "szkript" használatát a GitHub API-val való interakciók során, GitHub Actions.

Ebben az alapszintű munkafolyamatban a használatával nyitunk meg egy problémát `octokit/request-action` a GitHubon az API használatával.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Példa munkafolyamat.":::

>[!NOTE]
>GitHub.com a műveletet, de amikor a GitHub Enterprise Serveren fut, automatikusan a GitHub Enterprise Server API-t használja. 

Ha úgy döntött, hogy nem engedélyezi a GitHub Connectet, használhatja az alábbi alternatív munkafolyamatot.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatív példa munkafolyamat.":::

Lépjen a példány egyik repo elemére, és adja hozzá a fenti munkafolyamatot a következőként: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Egy másik példa munkafolyamat.":::

Az adatok **Műveletek** lapján várja meg a munkafolyamat végrehajtását.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Példaként végrehajtott munkafolyamat.":::

Azt is figyelheti, ahogy a futó feldolgozta.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="A futó által feldolgozott munkafolyamat.":::

Ha minden sikeresen lefutott, egy új, "Hello world" című problémát kell látnia az ön számára.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Példa a repoban.":::

Gratulálunk! Befejezte az első Actions-munkafolyamatot a GitHub Enterprise Serveren, amely a magánfelhőben Azure VMware Solution fut.

Ebben a cikkben egy új GitHub Enterprise Server-példányt hozunk létre, amely a GitHub.com helyi megfelelője, a saját Azure VMware Solution mellett. Ez a példány támogatja a GitHub Actions, Azure Blob Storage naplók és összetevők megőrzésére használja a tárolókat. De most csak a felszínt vakarjuk, hogy mire használhatja a GitHub Actions. Tekintse meg a Műveletek listáját a [GitHub Marketplace-én,](https://github.com/marketplace)vagy hozzon [létre egy sajátot.](https://docs.github.com/en/actions/creating-actions)

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan lehet a GitHub Enterprise Servert Azure VMware Solution magánfelhőben, a következővel ismerkedhet meg: 

- [A GitHub Actions](https://docs.github.com/en/actions)
- [Csatlakozás a bétaprogramhoz](https://resources.github.com/beta-signup/)
- [A GitHub Enterprise Server felügyelete](https://githubtraining.github.io/admin-training/#/00_getting_started)
