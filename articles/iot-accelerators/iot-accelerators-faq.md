---
title: IoT-megoldásgyorsítók – gyakori kérdések – Azure |} A Microsoft Docs
description: Gyakori kérdések az IoT-megoldásgyorsítók
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 59271a96c5ad1a92483ca585fc30f1e9de0ed4f7
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608749"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Gyakori kérdések az IoT-megoldásgyorsítók

Lásd még a [csatlakoztatott Factory-specifikus – GYIK](iot-accelerators-faq-cf.md) és a [távoli figyelés-specifikus – gyakori kérdések](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Hol található a forráskódban a a megoldásgyorsítók?

A forráskód következő GitHub-adattárában tárolja:

* [Távoli figyelés megoldásgyorsító (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Távoli figyelés megoldásgyorsító (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Prediktív karbantartási megoldásgyorsító](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Csatlakoztatott Factory-megoldásgyorsítók](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Milyen SDK-k fejlesztéséhez device-ügyfelek számára a megoldásgyorsítók használható?

IoT eszközoldali SDK-k más nyelven (C, .NET, Java, Node.js, Python) hivatkozásokat talál, az a [a Microsoft Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks) GitHub-adattárak.

Ha a fejlesztői készlet eszközt használ, Észreveheti, hogy erőforrásokat és a minták a [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-adattárban.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Az új mikroszolgáltatási architektúra érhető el az összes három megoldásgyorsítók?

Jelenleg csak a távoli figyelési megoldást használja a mikroszolgáltatási architektúra, azt ismerteti, hogy a legszélesebb körű forgatókönyv szerint.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Milyen előnyöket nyújt az új nyílt forráskódú mikroszolgáltatás-alapú architektúra az új frissítés?

Az utolsó két évek felhőarchitektúra jelentősen alakult. Mikroszolgáltatások rendelkezik kiderült nagyszerű mintaként elérése érdekében a skálázhatóságot és rugalmasságot, fejlesztés sebessége feláldozása nélkül. Architekturális mintáról használatú számos Microsoft-szolgáltatások remek megbízhatósággal és a méretezhetőség eredményeket. A Microsoft van ezek tapasztalatainkat gyakorlati üzembe a megoldásgyorsítók, így ügyfeleink kihasználhatják azokat.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném, ha az előfizetés és a egy adott közötti címtár leképezést módosítása az Azure AD-bérlővel. Hogyan végezhető el ez a feladat?

Lásd: [egy meglévő előfizetés hozzáadása az Azure AD-címtár](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Szeretném megváltoztatni a szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként szervezeti fiókkal bejelentkezve

Tekintse meg a támogatási cikk [módosítása szolgáltatás-rendszergazda és a társ-rendszergazdaként szervezeti fiókkal bejelentkezve](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg a hiba? "A fiók rendelkezik a megfelelő engedélyek megoldást hozhat létre. Ellenőrizze az a fiók rendszergazdája vagy próbálkozzon egy másik fiókkal."

Keresse meg a következő ábra útmutatást:

![Engedélyek folyamatábrája](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Ha a hibaüzenetet érvényesítése után továbbra is arra az Azure AD-bérlő globális rendszergazdája és az előfizetés társadminisztrátorának, a fiók rendszergazdája eltávolítja a felhasználót és újbóli hozzárendelése ebben a sorrendben szükséges engedélyekkel rendelkezik. Először adja hozzá a felhasználót egy globális rendszergazdaként, és adja hozzá a felhasználó az Azure-előfizetés társadminisztrátorai. Ha a problémák továbbra is fennállnak, lépjen kapcsolatba [Súgó és támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért látok a hiba, amikor az Azure előfizetéssel rendelkezem? ", Előre konfigurált megoldásokat hozhat létre Azure-előfizetés szükséges. Létrehozhat egy ingyenes próbafiókot mindössze néhány perc alatt."

Ha Ön bizonyos Azure-előfizetéssel rendelkezik, a bérlő az előfizetés-leképezés érvényesítése, és ellenőrizze, hogy a megfelelő bérlő van kiválasztva a legördülő listában. Ha ellenőrzése után a bérlő az megfelelő, kövesse a fentebbi ábra, és az előfizetés és az Azure AD-bérlő a leképezés érvényesítése.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Hol található információ a távoli figyelési megoldás előző verzióját?

A távoli figyelési megoldásgyorsító előző verziója néven volt ismert, az IoT Suite távoli figyelési előre konfigurált megoldás. Az archivált dokumentáció annak [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Az új megoldásgyorsító érhető el a meglévő megoldás azonos földrajzi régióban?

Igen, az új távoli megfigyelési érhető el az azonos földrajzi régióban.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Mi a különbség az Azure Portalon egy erőforráscsoport törlése és azureiotsuite.com megoldásgyorsítók a Törlés gombra való kattintás között?

* Ha törli a megoldásgyorsító a [azureiotsuite.com](https://www.azureiotsolutions.com/), amikor létrehozta a megoldásgyorsító üzembe helyezett összes erőforrást törli. Ha további erőforrásokat adott az erőforráscsoporthoz, ezeket az erőforrásokat is törli.
* Ha törli az erőforráscsoportot a [az Azure portal](https://portal.azure.com), csak az adott erőforráscsoportban lévő erőforrásokat törli. Akkor is törölnie kell a megoldásgyorsító társított Azure Active Directory alkalmazással.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Továbbra is fizethetek a meglévő befektetések felhasználása az Azure IoT-megoldásgyorsítók?

Igen. Minden olyan megoldás, amely létezik még ma továbbra is működik az Azure-előfizetésben, és a forráskód a Githubon elérhető marad.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hány az IoT Hub-példányt adhatok meg egy előfizetést?

Alapértelmezés szerint helyezheti üzembe a [előfizetésenként 10 IoT-központok](../azure-subscription-service-limits.md#iot-hub-limits). Létrehozhat egy [Azure támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését. Eredményeképpen minden megoldás gyorsító rendelkezések egy új IoT hubot, mivel csak telepíthet egy adott előfizetésben legfeljebb 10 megoldásgyorsítók.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hány Azure Cosmos DB-példányt adhatok meg egy előfizetést?

Ötven. Létrehozhat egy [Azure támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálattól a korlát növelését, de alapértelmezés szerint csak kiépíthetők előfizetésenként 50 Cosmos DB-példány.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Azure-előfizetésben csak két belső tranzakció szintjét 1 a Bing Maps Enterprise-csomagok is létrehozhat. A távoli figyelési megoldás a belső tranzakció Level 1 csomag alapértelmezés szerint ki van építve. Ennek eredményeképpen csak helyezhet legfeljebb két távoli figyelő megoldást a nem módosított előfizetésekben.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Ha a Microsoft Azure for DreamSpark is létrehozható megoldásgyorsítók?

> [!NOTE]
> A Microsoft Azure for DreamSpark most néven ismert Microsoft Imagine diákoknak.

Jelenleg nem hozható létre a megoldásgyorsítók egy [Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) fiókot. Létrehozhat azonban egy [ingyenes próbafiókot Azure](https://azure.microsoft.com/free/) mindössze néhány perc alatt, amely lehetővé teszi a megoldásgyorsítók létrehozása.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hogyan törölhetek Azure AD-bérlő?

Lásd Eric Golpe blogbejegyzését [bemutatója az Azure AD-bérlő törlése](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A távoli figyelési megoldásgyorsító funkcióinak megismerése](quickstart-remote-monitoring-deploy.md)
* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Okosgyár-megoldásgyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
