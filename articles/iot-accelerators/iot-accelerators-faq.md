---
title: IoT-megoldásgyorsítók – gyakori kérdések – Azure | Microsoft Docs
description: Ez a cikk az IoT-megoldásgyorsítókra vonatkozó gyakori kérdésekre ad választ. A GitHub-adattárakra mutató hivatkozásokat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713839"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Gyakran ismételt kérdések az IoT-megoldásgyorsítókról

Lásd még a [csatlakoztatott gyárra vonatkozó gyakori kérdéseket.](iot-accelerators-faq-cf.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Hol találom a megoldásgyorsítók forráskódját?

A forráskódot a következő GitHub-adattárak tárolják:

* [Csatlakoztatottgyár-megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)
* [Eszközszimulációs megoldásgyorsító](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Hol találom a távoli monitorozási és prediktív karbantartási megoldásgyorsítókat?

2020. december 10-től a távoli monitorozási és prediktív karbantartási gyorsítók el vannak távolítva a [Azure IoT solution accelerators](https://www.azureiotsolutions.com/Accelerators) webhelyről, és már nem érhetők el az új üzemelő példányok számára. Mindkét gyorsító GitHub-adattárai archiválva vannak. A kód továbbra is elérhető bárki számára, de az adattárak nem hoznak létre új közreműködést.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Mi történik a meglévő távoli monitorozási és prediktív karbantartási üzemelő példányokkal?

A meglévő üzemelő példányokat nem befolyásolja a távoli monitorozási és prediktív karbantartási megoldásgyorsítók eltávolítása, és továbbra is működni fognak. Az eltárolt adattárakra ez nincs hatással. A GitHubon található fő adattárak archiválva vannak.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Hogyan eszközszimulációs megoldásgyorsító üzembe helyezése?

Az eszközszimulációs megoldásgyorsító üzembe helyezéséhez tekintse meg az [eszközszimuláció](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) GitHub-adattárát.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Hol találok információkat az eltávolított megoldásgyorsítókról?

Tekintse meg a következő oldalakat az előző verziók webhelyén:

* [Távoli figyelés](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Prediktív karbantartás](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Milyen SDK-kkel fejlesztek eszköz ügyfeleket a megoldásgyorsítókhoz?

A különböző nyelvekre (C, .NET, Java, Node.js, Python) az [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) GitHub-adattárakban találhat hivatkozásokat.

Ha a DevKit eszközt használja, az erőforrásokat és mintákat az [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-adattárában találja.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném módosítani az előfizetésem és egy adott Azure AD-bérlő közötti címtárleképezést. Hogyan a feladatot?

Lásd: [Meglévő előfizetés hozzáadása az Azure AD-címtárhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Módosítani szeretnék egy szolgáltatás-rendszergazdát Co-Administrator vállalati fiókkal bejelentkezve

Ha szervezeti fiókkal van bejelentkezve, Co-Administrator szolgáltatás-rendszergazdai fiókkal [való bejelentkezést.](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg ez a hiba? "A fiókja nem rendelkezik a megfelelő engedélyekkel a megoldások létrehozásához. Forduljon a fiókad rendszergazdájához, vagy próbálkozzon egy másik fiókkal."

Útmutatásért nézze meg az alábbi ábrát:

![Engedélyek folyamatábra](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Ha az Azure AD-bérlő globális rendszergazdájának és az előfizetés társadi rendszergazdájának való érvényesítés utáni hibaüzenetet látja, a fiókad rendszergazdája távolítsa el a felhasználót, és rendelje hozzá újra a szükséges engedélyeket ebben a sorrendben. Először adja hozzá a felhasználót globális rendszergazdaként, majd adja hozzá a felhasználót az Azure-előfizetés társaddi rendszergazdájaként. Ha a problémák továbbra is fennállnak, [lépjen kapcsolatba a & ügyfélszolgálattal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért jelenik meg ez a hiba, ha Azure-előfizetéssel rendelkezik? "Az előre konfigurált megoldások létrehozásához Azure-előfizetés szükséges. Néhány perc alatt létrehozhat egy ingyenes próbafiókot."

Ha biztos abban, hogy rendelkezik Azure-előfizetéssel, ellenőrizze az előfizetés bérlőleképezését, és ellenőrizze, hogy a megfelelő bérlő van-e kiválasztva a legördülő menüben. Ha ellenőrzi, hogy a bérlő helyes-e, kövesse az előző diagramot, és ellenőrizze az előfizetés és az Azure AD-bérlő leképezését.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Mi a különbség egy erőforráscsoport törlése között a Azure Portal és a törlés gombra kattintva egy megoldásgyorsítón a azureiotsolutions.com?

* Ha törli a megoldásgyorsítót a [azureiotsolutions.com,](https://www.azureiotsolutions.com/)a megoldásgyorsító létrehozásakor üzembe helyezett összes erőforrást törli. Ha további erőforrásokat adott az erőforráscsoporthoz, ezek az erőforrások is törlődnek.
* Ha törli az erőforráscsoportot a Azure Portal [](https://portal.azure.com)erőforráscsoportot, csak az adott erőforráscsoport erőforrásait törli. A megoldásgyorsítóhoz társított Azure Active Directory is törölnie kell.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Továbbra is kihasználhatóak a meglévő befektetéseim a Azure IoT solution accelerators?

Igen. A jelenleg létező megoldások továbbra is működnek az Azure-előfizetésében, és a forráskód elérhető marad a GitHubon.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hány IoT Hub kiépíthet egy előfizetésben?

Alapértelmezés szerint előfizetésenként [10 IoT Hubot létesíthet.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) Létrehozhat egy új Azure-támogatás [a](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) korlát emelése érdekében. Ennek eredményeképpen, mivel minden megoldásgyorsító új IoT Hub, egy adott előfizetésben legfeljebb 10 megoldásgyorsítót lehet kiépítve.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hány Azure Cosmos DB kiépíthet egy előfizetésben?

Ötven. Létrehozhat egy új Azure-támogatás [a](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) korlát emeléséhez, de alapértelmezés szerint előfizetésenként csak 50 Cosmos DB kiépíteni.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Létrehozhatok megoldásgyorsítót, ha Microsoft Azure a DreamSparkhoz?

> [!NOTE]
> Microsoft Azure a DreamSparkhoz való Microsoft Imagine neve.

Jelenleg nem hozhat létre megoldásgyorsítót Microsoft Azure [a DreamSpark-fiókhoz.](https://azure.microsoft.com/pricing/member-offers/imagine/) Néhány perc alatt [](https://azure.microsoft.com/free/) azonban létrehozhat egy ingyenes azure-próbafiókot, amellyel megoldásgyorsítót hozhat létre.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan Azure AD-bérlőt?

Lásd Eric Golpe az Azure AD-bérlő törlését [bemutató blogbejegyzését.](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)

### <a name="next-steps"></a>Következő lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [Csatlakoztatottgyár-gyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](../iot-fundamentals/iot-security-ground-up.md)