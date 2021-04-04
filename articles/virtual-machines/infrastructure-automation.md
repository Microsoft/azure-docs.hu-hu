---
title: Infrastruktúra-automatizálási eszközök használata
description: Ismerje meg, hogyan hozhat létre és kezelhet virtuális gépeket az Azure-ban az infrastruktúra-automatizálási eszközök, például a Ansible, a Chef, a Puppet, a Terraform és a csomagoló használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 7a702fbf405011a9df3e7e77936a9a37f0fdfad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730638"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Infrastruktúra-automatizálási eszközök használata az Azure-beli virtuális gépekkel

Az Azure-beli virtuális gépek (VM-EK) egységes módon történő létrehozásához és kezeléséhez általában az automatizálás valamilyen formája szükséges. Számos eszköz és megoldás lehetővé teszi, hogy automatizálja az Azure-infrastruktúra teljes üzembe helyezését és a felügyeleti életciklusát. Ez a cikk az Azure-ban használható infrastruktúra-automatizálási eszközöket ismerteti. Ezek az eszközök általában a következő megközelítések egyikéhez illeszkednek:

- A virtuális gépek konfigurációjának automatizálása
    - Az eszközök közé tartozik a [Ansible](#ansible), a [Chef](#chef), a [Puppet](#puppet)és a [Azure Resource Manager sablon](#azure-resource-manager-template).
    - A virtuális gépek testreszabására szolgáló eszközök közé tartozik a [Cloud-init](#cloud-init) a linuxos virtuális gépekhez, a [PowerShell kívánt állapotának konfigurálása (DSC)](#powershell-dsc)és az [Egyéni Azure-szkriptek bővítménye](#azure-custom-script-extension) az összes Azure-beli virtuális géphez.

- Infrastruktúra-kezelés automatizálása
    - Az eszközök tartalmazzák a [csomagolót](#packer) az egyéni virtuálisgép-rendszerkép-buildek automatizálására és az infrastruktúra- [Terraform](#terraform) automatizálására.
    - A [Azure Automation](#azure-automation) az Azure-ban és a helyszíni infrastruktúrában is végezhet műveleteket.

- Alkalmazások üzembe helyezésének és kézbesítésének automatizálása
    - Ilyenek például az [Azure DevOps Services](#azure-devops-services) és a [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
A [Ansible](https://www.ansible.com/) egy automatizálási motor a konfiguráció felügyeletéhez, a virtuális gépek létrehozásához vagy az alkalmazások telepítéséhez. A Ansible ügynök nélküli modellt használ, jellemzően SSH-kulcsokkal a célszámítógépek hitelesítéséhez és kezeléséhez. A konfigurációs feladatok a forgatókönyvekben vannak meghatározva, és számos Ansible-modul érhető el az adott feladatok végrehajtásához. További információ: [how Ansible Works](https://www.ansible.com/how-ansible-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- A [Ansible telepítése és konfigurálása Linux rendszeren az Azure](/azure/developer/ansible/install-on-linux-vm)-hoz való használatra.
- [Hozzon létre egy linuxos virtuális gépet](/azure/developer/ansible/vm-configure).
- [Linux rendszerű virtuális gép kezelése](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
A [Chef](https://www.chef.io/) egy automatizálási platform, amely segít meghatározni az infrastruktúra konfigurálását, üzembe helyezését és kezelését. A további összetevők a Chef Habitat is tartalmazzák az alkalmazások életciklusának automatizálásához, nem pedig az infrastruktúrát, és a Chef Inspect, amely a biztonsági és a házirend követelményeinek való megfelelést segíti A Chef-ügyfeleket a rendszer a célszámítógépeken telepíti, és egy vagy több, a konfigurációkat tároló és kezelő központi Chef-kiszolgálóval rendelkezik. További információ: [Chef áttekintése](https://docs.chef.io/chef_overview.html).

Az alábbiak végrehajtásának módját ismerheti meg:

- [A Chef automatizálásának üzembe helyezése az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Telepítse a cheft Windows rendszeren, és hozzon létre Azure virtuális gépeket](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
A [Puppet](https://www.puppet.com) egy nagyvállalati használatra kész Automation platform, amely kezeli az alkalmazások kézbesítési és telepítési folyamatát. Az ügynökök a célszámítógépeken vannak telepítve, hogy a Puppet Master olyan jegyzékfájlokat futtasson, amelyek meghatározzák az Azure-infrastruktúra és a virtuális gépek kívánt konfigurációját. A báb más megoldásokkal, például a Jenkins-szel és a GitHubtal is integrálható a továbbfejlesztett devops-munkafolyamatokhoz. További információkért lásd: a [Puppet működése](https://puppet.com/products/how-puppet-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- A [báb üzembe helyezése](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.  A fájlok megfelelő formázásáról további információt `#cloud-config` a [Cloud-init dokumentációs webhelyén](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)talál.  `#cloud-config` a fájlok a Base64 kódolású szövegfájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott linuxos disztribúciós partnereinkkel, hogy elérhetők legyenek a Cloud-init-lemezképek az Azure piactéren. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal.
További információ az Azure-beli Cloud-init szolgáltatásról:

- [Cloud-init támogatás az Azure-beli linuxos virtuális gépekhez](./linux/using-cloud-init.md)
- [Próbálja ki a Cloud-init használatával történő automatizált virtuálisgép-konfigurációval kapcsolatos oktatóanyagot](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
A [PowerShell desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform, amely meghatározza a célszámítógépek konfigurációját. A DSC-t Linuxon is használhatja a [nyílt kezelési infrastruktúra (a (z) (a)-kiszolgáló](https://collaboration.opengroup.org/omi/)használatával.

A DSC-konfigurációk határozzák meg, hogy mit kell telepíteni a gépre, és hogyan kell konfigurálni a gazdagépet. A helyi Configuration Manager (LCD) motor minden olyan cél csomóponton fut, amely leküldéses konfigurációk alapján dolgozza fel a kért műveleteket. A lekéréses kiszolgáló egy olyan webszolgáltatás, amely egy központi gazdagépen fut a DSC-konfigurációk és a kapcsolódó erőforrások tárolásához. A lekéréses kiszolgáló az egyes célállomásokon lévő LCD-motorral kommunikál, hogy megadja a szükséges konfigurációkat, és jelentést készítsen a megfelelőségről.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy alapszintű DSC-konfigurációt](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [DSC lekéréses kiszolgáló konfigurálása](/powershell/scripting/dsc/pull-server/pullserver).
- [A Linux rendszerhez készült DSC használata](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure egyéni szkriptek futtatására szolgáló bővítmény
A [Linux](./extensions/custom-script-linux.md) vagy a [Windows rendszerhez](./extensions/custom-script-windows.md) készült egyéni parancsfájl-bővítmény letölti és végrehajtja az Azure-beli virtuális gépeken futó parancsfájlokat. A bővítményt a virtuális gép létrehozásakor vagy a virtuális gép használatba helyezésekor bármikor használhatja.

A parancsfájlok letölthetők az Azure Storage-ból vagy bármilyen nyilvános helyről, például egy GitHub-adattárból. Az egyéni szkriptek bővítménnyel bármilyen nyelven írhat parancsfájlokat, amelyek a forrás virtuális gépen futnak. Ezek a parancsfájlok az alkalmazások telepítéséhez vagy a virtuális gép igény szerinti konfigurálásához használhatók. A hitelesítő adatok védelme érdekében a bizalmas adatokat, például a jelszavakat védett konfigurációban lehet tárolni. Ezeket a hitelesítő adatokat a rendszer csak a virtuális gépen fejti vissza.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy linuxos virtuális gépet az Azure CLI-vel, és használja az egyéni szkriptek bővítményét](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Hozzon létre egy Windows rendszerű virtuális gépet Azure PowerShell, és használja az egyéni szkriptek bővítményét](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Csomagoló
A [csomagoló](https://www.packer.io) automatizálja a létrehozási folyamatot, amikor létrehoz egy egyéni virtuálisgép-rendszerképet az Azure-ban. A csomagoló használatával határozhatja meg az operációs rendszert, és futtathatja a konfigurálás utáni parancsfájlokat, amelyek testre szabják a virtuális gépet az adott igényeknek megfelelően. A konfigurálást követően a rendszer felügyelt lemezképként rögzíti a virtuális gépet. A csomagoló automatizálja a forrás virtuális gép, a hálózati és a tárolási erőforrások létrehozását, a konfigurációs parancsfájlok futtatását, majd létrehozza a virtuálisgép-rendszerképet.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Linuxos virtuálisgép-rendszerkép létrehozása az Azure-ban a csomagoló használatával](./linux/build-image-with-packer.md).
- [Hozzon létre egy Windows rendszerű virtuálisgép-rendszerképet az Azure-ban a csomagoló használatával](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
A [Terraform](https://www.terraform.io) egy automatizálási eszköz, amely lehetővé teszi egy teljes Azure-infrastruktúra definiálását és létrehozását egyetlen sablon formátumának nyelvével – a HashiCorp konfigurációs nyelvét (HCL). A Terraform segítségével olyan sablonokat határozhat meg, amelyek automatizálják az adott alkalmazási megoldás hálózati, tárolási és virtuálisgép-erőforrásainak létrehozását. Az Azure-ban más platformokhoz is használhatja meglévő Terraform-sablonjait, hogy biztosítsa az egységességet és egyszerűsítse az infrastruktúra központi telepítését anélkül, hogy Azure Resource Manager-sablonra kellene váltania.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Terraform telepítése és konfigurálása az Azure-](/azure/developer/terraform/getting-started-cloud-shell)ban.
- [Hozzon létre egy Azure-infrastruktúrát a Terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) a runbookok használatával dolgozza fel a célként megadott virtuális gépeken található feladatokat. A Azure Automation az infrastruktúra létrehozása helyett a meglévő virtuális gépek kezelésére szolgál. Azure Automation a Linux és a Windows rendszerű virtuális gépeken, valamint a hibrid runbook-feldolgozóval rendelkező helyszíni virtuális vagy fizikai gépeken is futhat. A runbookok tárolhatók, például a GitHubon. Ezek a runbookok futtathatók manuálisan vagy egy meghatározott ütemterven.

A Azure Automation egy kívánt állapot-konfiguráció (DSC) szolgáltatást is biztosít, amely lehetővé teszi a virtuális gépek adott készletének konfigurálásához szükséges definíciók létrehozását. A DSC ezt követően biztosítja, hogy a szükséges konfigurációt alkalmazza, és a virtuális gép konzisztens maradjon. Azure Automation DSC a Windows és a Linux rendszerű gépeken is fut.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy PowerShell-runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Hibrid Runbook-feldolgozók használata helyszíni erőforrások kezeléséhez](../automation/automation-hybrid-runbook-worker.md).
- [Azure Automation DSC használata](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
Az [Azure DevOps Services](https://www.visualstudio.com/team-services/) olyan eszközkészlet, amely segítséget nyújt a kódok megosztásában és nyomon követésében, az automatizált buildek létrehozásában, valamint egy teljes folyamatos integrációs és fejlesztési (CI/CD) folyamat létrehozásában. Az Azure DevOps Services integrálható a Visual Studióval és más szerkesztőkkel a használat egyszerűsítése érdekében. Az Azure DevOps Services Azure-beli virtuális gépeket is létrehozhat és konfigurálhat, majd programkódot telepíthet rájuk.

További információk:

- [Azure DevOps-szolgáltatások](/azure/devops/user-guide/index).


## <a name="jenkins"></a>Jenkins
A [Jenkins](https://jenkins.io) egy folyamatos integrációs kiszolgáló, amely megkönnyíti az alkalmazások üzembe helyezését és tesztelését, valamint automatizált folyamatokat hozhat létre a kód kézbesítéséhez. Több száz beépülő modult is ki lehet terjeszteni a Jenkins-platformra, és a webhookok segítségével számos más termékkel és megoldással is integrálható. Manuálisan telepítheti a Jenkins-t egy Azure-beli virtuális gépre, futtathatja a Jenkins-t egy Docker-tárolóból, vagy használhat egy előre elkészített Azure Marketplace-rendszerképet is.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Fejlesztési infrastruktúra létrehozása Linux rendszerű virtuális gépen az Azure-ban a Jenkins, a GitHub és a Docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) az Azure üzembe helyezési és kezelési szolgáltatása. Olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetésében lévő erőforrások létrehozását, frissítését és törlését. Az üzembe helyezés után az erőforrások biztonságossá tételéhez és rendszerezéséhez olyan felügyeleti funkciókat használhat, mint a hozzáférés-vezérlés, a zárolások és a címkék.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Helyszíni virtuális gépek üzembe helyezése Resource Manager-sablonnal](./linux/spot-template.md).
- [Hozzon létre egy Windows rendszerű virtuális gépet egy Resource Manager-sablonból](./windows/ps-template.md).
- [Töltse le a virtuális gép sablonját](/previous-versions/azure/virtual-machines/windows/download-template).
- [Hozzon létre egy Azure rendszerkép-szerkesztői sablont](./linux/image-builder-json.md).

## <a name="next-steps"></a>Következő lépések
Számos különböző lehetőség áll rendelkezésre az infrastruktúra-automatizálási eszközök használatára az Azure-ban. Ön szabadon használhatja az igényeinek és környezetének legmegfelelőbb megoldást. A kezdéshez és az Azure-ba beépített eszközök kipróbálásához lásd: [Linux](./linux/tutorial-automate-vm-deployment.md) vagy [Windows rendszerű](./windows/tutorial-automate-vm-deployment.md) virtuális gép testreszabásának automatizálása.
