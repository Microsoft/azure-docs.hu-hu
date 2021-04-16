---
title: Ismerkedés a Cloud Foundry a Microsoft Azure
description: OSS vagy pivotal Cloud Foundry futtatása Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 2f80f07cad0e82ee35fe71223e1282ea24f791bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530925"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry az Azure-ban

A Cloud Foundry egy olyan nyílt forráskódú „szolgáltatásként nyújtott platform” (PaaS), amely különböző nyelveken és keretrendszerekben fejlesztett, 12 faktoros alkalmazások létrehozását, telepítését és üzemeltetését teszi lehetővé. Ez a dokumentum az Azure-ban Cloud Foundry beállításokat és az első lépések útmutatóját ismerteti.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry ajánlatok

Az Azure-ban kétféle Cloud Foundry futtatható: nyílt forráskódú Cloud Foundry (OSS CF) és Pivotal Cloud Foundry (PCF). Az OSS CF [](https://github.com/cloudfoundry) a Cloud Foundry Foundation által felügyelt Cloud Foundry nyílt forráskódú verziója. A pivotal Cloud Foundry a Pivotal Software Inc által Cloud Foundry vállalati disztribúciója. A két ajánlat közötti különbségek közül mutatunk be néhányat.

### <a name="open-source-cloud-foundry"></a>Nyílt forráskódú Cloud Foundry

OsS-Cloud Foundry üzembe helyezéséhez először üzembe kell helyeznie egy BOSH-könyvtárat, majd üzembe kell Cloud Foundry a [GitHubon](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)található utasításokat követve. Az OSS CF használatával kapcsolatos [](https://docs.cloudfoundry.org/) további információkért tekintse meg a Cloud Foundry Foundation dokumentációját.

A Microsoft a következő közösségi csatornákon nyújt támogatást az OSS CF-hez:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>bosh-azure-cpi csatorna a [Cloud Foundry Slacken](https://slack.cloudfoundry.org/)
- [cf-bosh levelezési lista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- A CPI és a szolgáltatásközvetítő [GitHub-problémái](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) [](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Az Azure-erőforrások , például a virtuális gépek, amelyeken futtatja a Cloud Foundry, a támogatási Azure-támogatás alapul. Az erőfeszítésen áteső közösségi támogatás csak a Cloud Foundry összetevőkre vonatkozik.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

A pivotal Cloud Foundry az OSS-disztribúcióval azonos alapplatformot, valamint saját fejlesztésű felügyeleti eszközöket és vállalati támogatást tartalmaz. A PCF Azure-on való futtatásához licencet kell szereznie a Pivotaltól. Az Azure Marketplace PCF-ajánlata 90 napos próbaverziós licencet tartalmaz.

Az eszközök közé tartozik a [Pivotal Operations Manager,](https://docs.pivotal.io/ops-manager/2-10/install/)egy webalkalmazás, amely leegyszerűsíti egy Cloud Foundry-alap alap üzembe helyezését és kezelését, valamint a [Pivotal Apps Managert,](https://docs.pivotal.io/pivotalcf/console/)a felhasználók és alkalmazások felügyeletére szolgáló webalkalmazást.

A fenti OSS CF támogatási csatornáin kívül a PCF-licenc arra is feljogosítja, hogy a Pivotalhoz forduljon támogatásért. A Microsoft és a Pivotal olyan támogatási munkafolyamatokat is engedélyezett, amelyek lehetővé teszik, hogy segítségért forduljon bármelyik félhez, és a kérdésének megfelelő útválasztását a probléma helyétől függően tegye lehetővé.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry a ["12 tényezős alkalmazás"](https://12factor.net/) módszertant, amely elősegíti az állapot nélküli alkalmazásfolyamatok és az állapot-visszacsatolási szolgáltatások tiszta elkülönítését. [A szolgáltatásközvetítők](https://docs.cloudfoundry.org/services/api.html) konzisztens módokat kínálnak a háttérszolgáltatások építésére és alkalmazásokhoz kötésére. Az [Azure-szolgáltatásközvetítő](https://github.com/Azure/meta-azure-service-broker) ezen a csatornán keresztül biztosítja a legfontosabb Azure-szolgáltatások némelyikét, beleértve az Azure Storage-et és a Azure SQL.

Ha Pivotal-Cloud Foundry, a szolgáltatásközvetítő csempeként is elérhető [a](https://docs.pivotal.io/azure-sb/installing.html) Pivotal Networkből.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

### <a name="azure-devops-services-plugin"></a>Az Azure DevOps Services beépülő modulja

Cloud Foundry alkalmas agilis szoftverfejlesztésre, beleértve a folyamatos integráció (CI) és a folyamatos teljesítés (CD) használatát. Ha az Azure DevOps Servicest használja a projektek kezeléséhez, és be szeretne állítani egy CI/CD-folyamatot a Cloud Foundry célként, használhatja az Azure DevOps Services Cloud Foundry [buildbővítményt.](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension) A beépülő modullal egyszerűen konfigurálhatja és automatizálhatja az üzembe helyezéseket a Cloud Foundry, akár az Azure-ban, akár más környezetben futnak.

## <a name="next-steps"></a>Következő lépések

- [Pivotal-Cloud Foundry üzembe helyezése a Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Alkalmazás üzembe helyezése Cloud Foundry Azure-ban](./cloudfoundry-deploy-your-first-app.md)
