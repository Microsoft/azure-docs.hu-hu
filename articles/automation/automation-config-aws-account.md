---
title: Azure Automation-forgatókönyvek hitelesítése az Amazon Web Services segítségével
description: Ez a cikk azt ismerteti, hogyan hitelesítheti a runbookok a Amazon Web Services használatával.
keywords: aws-hitelesítés, aws konfigurálása
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83837185"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Runbookok hitelesítése az Amazon Web Services segítségével

Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban. Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival. A hitelesítéshez rendelkeznie kell egy Azure-előfizetéssel.

## <a name="obtain-aws-subscription-and-credentials"></a>AWS-előfizetés és hitelesítő adatok beszerzése

Az AWS-sel való hitelesítéshez be kell szereznie egy AWS-előfizetést, és meg kell adnia az AWS hitelesítő adatok készletét, hogy hitelesítse a runbookok futó Azure Automation. A megadott hitelesítő adatok az AWS hozzáférési kulcs és a titkos kulcs. Lásd: [AWS hitelesítő adatok használata](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása

Egy meglévő Automation-fiókot használhat az AWS-sel való hitelesítéshez. Azt is megteheti, hogy hozzárendel egy fiókot az AWS-erőforrásokra irányuló runbookok. Ebben az esetben hozzon létre egy új [Automation-fiókot](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Az AWS hitelesítő adatainak tárolása

Az AWS hitelesítő adatait a Azure Automation eszközeiként kell tárolnia. A hozzáférési kulcs és a titkos kulcs létrehozásával kapcsolatos útmutatásért lásd: az [AWS-fiók hozzáférési kulcsainak kezelése](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) . Ha a kulcsok elérhetők, másolja a hozzáférési kulcs AZONOSÍTÓját és a titkos kulcs AZONOSÍTÓját egy biztonságos helyre. Letöltheti a kulcsfájl biztonságos tárolásához.

## <a name="create-credential-asset"></a>Hitelesítőadat-eszköz létrehozása

Miután létrehozta és átmásolta az AWS biztonsági kulcsait, létre kell hoznia egy hitelesítőadat-eszközt az Automation-fiókkal. Az eszköz lehetővé teszi az AWS-kulcsok biztonságos tárolását, és azok hivatkozását a runbookok. Lásd: [új hitelesítőadat-eszköz létrehozása a Azure Portal](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Adja meg a következő AWS-adatokat a megadott mezőkben:
    
* **Név**  -  **AWScred** vagy az elnevezési szabványokat követő megfelelő érték
* **Felhasználónév** – a hozzáférési azonosító
* **Jelszó** – a titkos kulcs neve 

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan hozhat létre runbookok az AWS-feladatok automatizálásához, tekintse meg a [Amazon Web Services virtuális gép üzembe helyezése runbook](automation-scenario-aws-deployment.md)című témakört.