---
title: A GitHub Enterprise Server beállítása az Azure VMware-megoldás privát felhőben
description: Ismerje meg, hogyan állíthatja be a GitHub Enterprise Servert az Azure VMware-megoldás privát felhőben.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 59a76c3976f6fcda88423b7b78344f2abed1ea84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382021"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>A GitHub Enterprise Server beállítása az Azure VMware-megoldás privát felhőben

Ebből a cikkből megtudhatja, hogyan állíthatja be a GitHub Enterprise Servert, a [GitHub.com](https://github.com/)helyszíni verzióját az Azure VMware-megoldás privát felhőben. Az ismertetett forgatókönyv egy GitHub Enterprise Server-példány, amely akár 3 000 fejlesztőt is képes kiszolgálni akár 25 feladatot percenként a GitHub-műveleteken. Ez magában foglalja az *előzetes* verziójú szolgáltatások (például a GitHub-műveletek) telepítését. Ha testre szeretné szabni az adott igényeknek megfelelő beállítást, tekintse át a következő cikkben felsorolt követelményeket: a [GitHub Enterprise Server telepítése VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)-en.

## <a name="before-you-begin"></a>Előkészületek

A GitHub Enterprise Serverhez érvényes licenckulcs szükséges. Előfordulhat, hogy regisztrál egy [próbaverziós licencre](https://enterprise.github.com/trial). Ha egy integráción keresztül szeretné kiterjeszteni a GitHub Enterprise Server képességeit, egy ingyenes, öt fős fejlesztői licencre lehet érvényes. Ezt a licencet a [GitHub Partnerprogram](https://partner.github.com/)használatával kérheti le.

## <a name="installing-github-enterprise-server-on-vmware"></a>A GitHub Enterprise Server telepítése VMware rendszeren

Töltse le [a GitHub Enterprise Server jelenlegi kiadását](https://enterprise.github.com/releases/2.19.0/download) VMWare ESXi/VSPHERE (petesejtek), és [telepítse a letöltött petesejt-sablont](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) .

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Válassza a GitHub helyszíni vagy Felhőbeli futtatását.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Telepítse a petesejtek sablont.":::  

Adja meg az új virtuális gép felismerhető nevét, például GitHubEnterpriseServer. Nem kell megadnia a kiadás részleteit a virtuális gép nevében, mivel ezek az adatok elavultak lesznek a példány frissítésekor. Válassza ki az összes alapértelmezett értéket (ezeket a részleteket röviden szerkesztjük), és várjon, amíg a rendszer importálja a PETESEJTeket.

Az importálás után [állítsa be a hardver konfigurációját](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) az igényeinek megfelelően. A példánkban a következő konfigurációra van szükségünk.

| Erőforrás | Normál beállítás | Standard beállítása + "bétaverziós funkciók" (műveletek) |
| --- | --- | --- |
| vCPU-k | 4 | 8 |
| Memória | 32 GB | 61 GB |
| Csatlakoztatott tároló | 250 GB | 300 GB |
| Legfelső szintű tárterület | 200 GB | 200 GB |

Azonban az igényei eltérőek lehetnek. Tekintse át a hardverrel kapcsolatos szempontokat a [GitHub Enterprise Server VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)-en való telepítésével foglalkozó témakörben. Lásd még: [CPU-vagy memória-erőforrások hozzáadása a VMware-hez](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) a hardver konfigurációjának a helyzet alapján történő testreszabásához.

## <a name="configuring-the-github-enterprise-server-instance"></a>A GitHub Enterprise Server-példány konfigurálása

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Telepítse a GitHub Enterprise-t.":::  

Miután az újonnan kiépített virtuális gép (VM) be van kapcsolva, [konfigurálja a böngészőn keresztül](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). A licencfájl feltöltése és a felügyeleti konzol jelszavának beállítása szükséges. Ügyeljen rá, hogy biztonságos helyen jegyezze fel ezt a jelszót.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="A rendszergazdai rendszerhéj elérése SSH-n keresztül.":::    

Javasoljuk, hogy legalább a következő lépéseket tegye:

1. Töltsön fel egy nyilvános SSH-kulcsot a felügyeleti konzolra, hogy [SSH-n keresztül hozzáférhessen a felügyeleti rendszerhéjhoz](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. Konfigurálja a TLS-t a [példányon](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) , hogy egy megbízható hitelesítésszolgáltató által aláírt tanúsítványt használjon.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="A példány konfigurálása.":::

Alkalmazza a beállításokat.  A példány újraindítása közben folytathatja a következő lépéssel a **GitHub-műveletek blob Storage konfigurálását**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Hozzon létre egy rendszergazdai fiókot.":::

A példány újraindítása után új rendszergazdai fiókot hozhat létre a példányon. Ügyeljen arra, hogy jegyezze fel a felhasználó jelszavát is.

### <a name="other-configuration-steps"></a>Egyéb konfigurációs lépések

Ha a példányát éles használatra szeretné megerősíteni, a következő opcionális telepítési lépések ajánlottak:

1. [Magas rendelkezésre állás](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) konfigurálása a védelemhez:

    - Szoftverek összeomlása (operációs rendszer vagy alkalmazás szintje)
    - Hardveres hibák (tárolás, CPU, RAM stb.)
    - Virtualizációs gazdagép rendszerhibái
    - Logikai vagy fizikailag súlyos hálózat

2. [Konfigurálja](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) a [Backup-Utilities](https://github.com/github/backup-utils)szolgáltatást, amely az elsődleges példánytól eltérő rendelkezésre állású, verzióval ellátott pillanatképeket biztosít a vész-helyreállításhoz.
3. Az [altartományok elkülönítésének beállítása](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)érvényes TLS-tanúsítvánnyal, a helyek közötti parancsfájlok és az egyéb kapcsolódó sebezhetőségek enyhítése érdekében.

## <a name="configuring-blob-storage-for-github-actions"></a>BLOB Storage konfigurálása GitHub-műveletekhez

> [!NOTE]
> A GitHub-műveletek [jelenleg korlátozott bétaverzióként érhetők el a GitHub Enterprise Server 2,22 kiadásában](https://docs.github.com/en/enterprise/admin/github-actions).

A GitHub-műveleteknek a GitHub Enterprise Serveren való engedélyezéséhez külső blob Storage szükséges (jelenleg "bétaverzió" szolgáltatásként érhető el). Ezt a külső BLOB-tárolót az összetevők és naplók tárolására szolgáló műveletek használják. A GitHub Enterprise Server műveletei az [Azure Blob Storage tárolási szolgáltatóként](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (és másokkal) is támogatják. Ezért egy új Azure Storage-fiókot fogunk kiépíteni a [Storage-fiók](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Azure Blob Storage-fiók kiépítése.":::

Miután az új BlobStorage-erőforrás üzembe helyezése befejeződött, másolja és jegyezze fel a kapcsolati karakterláncot (az elérési kulcsok alatt érhető el). Rövidesen szükség lesz erre a karakterláncra.

## <a name="setting-up-the-github-actions-runner"></a>A GitHub-műveletek futójának beállítása

> [!NOTE]
> A GitHub-műveletek [jelenleg korlátozott bétaverzióként érhetők el a GitHub Enterprise Server 2,22 kiadásában](https://docs.github.com/en/enterprise/admin/github-actions).

Ezen a ponton a GitHub Enterprise Server egy példányát kell futtatnia, amely egy rendszergazdai fiókkal lett létrehozva. Emellett rendelkeznie kell külső blob Storage-tárolóval is, amelyet a GitHub-műveletek az adatmegőrzéshez fognak használni.

Most hozzon létre valahol a GitHub-műveletek futtatásához. ismét az Azure VMware-megoldást fogjuk használni.

Először is hozzon létre egy új virtuális gépet a fürtön. A virtuális gépet az [Ubuntu Server legújabb kiadására](http://releases.ubuntu.com/20.04.1/)alapozjuk.

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Hozzon létre egy új virtuális gépet.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Hozzon létre egy új virtuális gépet 2. lépéssel.":::

Miután létrehozta a virtuális gépet, kapcsolja be és csatlakozzon SSH-n keresztül.

Ezután telepítse [a Actions Runner](https://github.com/actions/runner) alkalmazást, amely egy feladatot futtat egy GitHub-műveletek munkafolyamatból. Azonosítsa és töltse le a műveletek futó legújabb linuxos x64-es kiadását a [kiadások lapról](https://github.com/actions/runner/releases) , vagy futtassa a következő gyors parancsfájlt. Ehhez a parancsfájlhoz a curl és a [jQ](https://stedolan.github.io/jq/) is szükség van a virtuális gépen.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Most egy fájlnak kell lennie helyileg a virtuális gépen, a műveletek-Runner-Linux-arm64- \* . tar. gz. A következő Fez kibontása helyileg:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Ez a kinyerés kicsomagol néhány fájlt helyileg, beleértve a `config.sh` és a `run.sh` szkriptet is, ami hamarosan visszakerül.

## <a name="enabling-github-actions"></a>GitHub-műveletek engedélyezése

> [!NOTE]
> A GitHub-műveletek [jelenleg korlátozott bétaverzióként érhetők el a GitHub Enterprise Server 2,22 kiadásában](https://docs.github.com/en/enterprise/admin/github-actions).

Majdnem ott! Konfigurálja és engedélyezze a GitHub-műveleteket a GitHub Enterprise Server-példányon. [Hozzá kell férnie a GitHub Enterprise Server-példány felügyeleti rendszerhéjához SSH-kapcsolaton keresztül](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh), majd futtatnia kell a következő parancsokat:

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

Következő Futtatás:

`ghe-actions-check -s blob`

A következő kimenetnek kell megjelennie: "Blob Storage állapota Kifogástalan".

Most, hogy a GitHub-műveletek konfigurálva vannak, engedélyezze azt a felhasználók számára. Jelentkezzen be a GitHub Enterprise Server-példányba rendszergazdaként, és válassza a ![ rakéta ikont.](media/github-enterprise-server/rocket-icon.png) a lap jobb felső sarkában. A bal oldali oldalsávon válassza a **vállalati áttekintés**, majd a **házirendek**, **műveletek** és a **műveletek engedélyezése az összes szervezet számára** lehetőséget.

Ezután konfigurálja a futót a saját üzemeltetésű **futók** lapról. Válassza a **Hozzáadás új** , majd az **új Runner** lehetőséget a legördülő menüből.

A következő oldalon megtekintheti a futtatandó parancsok készletét, ezért csak másolja a parancsot a Futtatás **konfigurálásához** , például:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Másolja a `config.sh` parancsot, és illessze be egy munkamenetbe a műveletek futóján (korábban létrehozva).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Futó műveletek.":::

Futtassa a run.sh parancsot a Runner *futtatásához* :

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Futtassa a futót.":::

Annak érdekében, hogy ez a Runner elérhető legyen a vállalata szervezetei számára, szerkessze a szervezeti hozzáférését:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="A Runner-hozzáférés szerkesztése.":::

Itt minden szervezet számára elérhetővé tesszük, de a hozzáférést korlátozhatja a szervezetek egy részhalmazához, és akár adott tárházhoz is.

## <a name="optional-configuring-github-connect"></a>Választható A GitHub-kapcsolat konfigurálása

Bár ez a lépés nem kötelező, javasoljuk, hogy ha a GitHub.com-on elérhető nyílt forráskódú műveleteket kíván használni. Lehetővé teszi, hogy a munkafolyamatokban lévő újrafelhasználható műveletekre hivatkozva mások munkájára támaszkodjon.

A GitHub-kapcsolódás engedélyezéséhez kövesse az [automatikus hozzáférés engedélyezése GitHub.com műveletekhez a GitHub-kapcsolat használatával](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)című témakör lépéseit.

Ha engedélyezve van a GitHub-kapcsolat, válassza ki azt a **kiszolgálót, amely a GitHub.com műveleteit használja a munkafolyamat-futtatások** beállításban.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="A GitHub.com műveleteinek használatának engedélyezése a munkafolyamat-futtatásokban.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Az első munkafolyamat beállítása és futtatása

Most, hogy a műveletek és a GitHub-kapcsolat be van állítva, a jó használat érdekében tegye ezt a munkát. Az alábbi példa egy olyan munkafolyamatot mutat be, amely a kiváló [octokit/kérelemre](https://github.com/octokit/request-action)hivatkozik, és lehetővé teszi, hogy a GitHub API-val, a GitHub-műveletek segítségével "parancsfájl" githubon keresztüli interakciókat használjon.

Ebben az alapszintű munkafolyamatban `octokit/request-action` az API-val csak a githubon keresztül nyitunk meg egy problémát.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Példa munkafolyamat.":::

>[!NOTE]
>A GitHub.com futtatja a műveletet, de amikor a GitHub Enterprise Serveren fut, *automatikusan* a GitHub Enterprise Server API-t használja.

Ha úgy döntött, hogy nem engedélyezi a GitHub-kapcsolatot, a következő alternatív munkafolyamatot használhatja.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatív példa a munkafolyamatra.":::

Nyisson meg egy tárházat a példányon, és adja hozzá a fenti munkafolyamatot a következőként: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Egy másik példa a munkafolyamatra.":::

A tárház **műveletek** lapján várjon, amíg végre nem hajtja a munkafolyamatot.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Példa a munkafolyamat végrehajtására.":::

Azt is megnézheti, hogy a futó a feldolgozás alatt áll.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="A futó által feldolgozott munkafolyamat.":::

Ha minden sikeresen lefutott, megjelenik egy új probléma a tárházban, amely a "Helló világ" címet kapja.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Példa a tárházban.":::

Gratulálunk! Most fejezte be az első műveletek munkafolyamatát a GitHub Enterprise Serveren, amely az Azure VMware-megoldás privát felhőben fut.

Ebben a cikkben a GitHub Enterprise Server új példányát hoztunk létre, amely a GitHub.com saját üzemeltetésű, az Azure VMware-megoldás privát felhője felett található. Ez a példány a GitHub-műveletek támogatását tartalmazza, és az Azure Blob Storage használja a naplók és összetevők megőrzéséhez. A GitHub-műveletekkel azonban egyszerűen megkarcoljuk a felületet. Tekintse meg a [GitHub piactéren](https://github.com/marketplace)végrehajtott műveletek listáját, vagy [hozza létre a sajátját](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte a GitHub Enterprise Server beállítását az Azure VMware-megoldás privát felhőben, érdemes megismernie az alábbiakat: 

- [A GitHub-műveletek első lépései](https://docs.github.com/en/actions).
- [A Beta programhoz való csatlakozás](https://resources.github.com/beta-signup/).
- [A GitHub Enterprise Server felügyelete](https://githubtraining.github.io/admin-training/#/00_getting_started).
