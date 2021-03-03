---
title: Ügyfélszéf a Microsoft Azure-hoz
description: A Microsoft Azure Ügyfélszéf technikai áttekintése, amely lehetővé teszi a felhőalapú szolgáltatói hozzáférés szabályozását, ha a Microsoftnak szüksége lehet az ügyféladatok elérésére.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: 04fc020b2b08d4d3dc68b62c417eb8e2d2e85b97
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720613"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Ügyfélszéf a Microsoft Azure-hoz

> [!NOTE]
> Ennek a funkciónak a használatához a szervezetnek rendelkeznie kell egy minimális **fejlesztői** szintű [Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/) .

Az Ügyfélszéf a Microsoft Azure-hoz egy felületet biztosít az ügyfeleknek, ahol áttekinthetik, illetve jóváhagyhatják vagy elutasíthatják az ügyféladatok hozzáférési kérelmeit. Olyan esetekben használatos, amikor egy Microsoft-mérnöknek egy támogatási kérelem kezelése során hozzá kell férnie az ügyfél adataihoz.

Ez a cikk bemutatja, hogyan engedélyezheti a Ügyfélszéf és a kulcstároló-kérelmek kezdeményezését, nyomon követését és tárolását a későbbi felülvizsgálatok és auditálások számára.

<a id='supported-services-and-scenarios-in-preview'># # Támogatott szolgáltatások és forgatókönyvek (általánosan elérhető)

A következő szolgáltatások már általánosan elérhetők a Ügyfélszéf számára:

- Azure API Management
- Azure App Service
- Azure Cognitive Services
- Azure Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Azure-előfizetések átvitele
- Azure Synapse Analytics
- Virtuális gépek az Azure-ban (a távoli asztal elérését, a memóriaképek elérését és a felügyelt lemezeket is beleértve)

## <a name="enable-customer-lockbox"></a>Ügyfélszéf engedélyezése

Mostantól engedélyezheti a Ügyfélszéf a Ügyfélszéf panel [adminisztrációs moduljában](https://aka.ms/customerlockbox/administration) .  

> [!NOTE]
> A Ügyfélszéf engedélyezéséhez a felhasználói fióknak [hozzá kell rendelnie a globális rendszergazdai szerepkört](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Munkafolyamat

Az alábbi lépések egy Ügyfélszéf-kérelem tipikus munkafolyamatát ismertetik.

1. A munkahelyen valaki problémája van az Azure-munkaterheléssel.

2. Miután ez a személy elhárítja a problémát, de nem tudja kijavítani, megnyitnak egy támogatási jegyet a [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). A jegy hozzá van rendelve egy Azure ügyfélszolgálati szakemberhez.

3. Egy Azure-támogatási szakember áttekinti a szolgáltatási kérelmet, és meghatározza a probléma megoldásának következő lépéseit.

4. Ha a támogatási szakember nem tudja elhárítani a problémát a szabványos eszközök és telemetria használatával, a következő lépés az igény szerinti (JIT) hozzáférési szolgáltatás használatával emelt szintű engedélyek kérése. Ez a kérelem az eredeti támogatási szakembertől vagy egy másik mérnöktől is származhat, mert a problémát az Azure DevOps csapata is felhasználja.

5. Miután az Azure-mérnök elküldte a hozzáférési kérelmet, az igény szerinti szolgáltatás kiértékeli a kérelmet, figyelembe véve a következő tényezőket:
    - Az erőforrás hatóköre
    - Azt határozza meg, hogy a kérelmező izolált identitás-e vagy többtényezős hitelesítést használ-e
    - Engedélyek szintjei

    Az JIT-szabály alapján ez a kérelem tartalmazhatja a belső Microsoft-jóváhagyók jóváhagyását is. A jóváhagyó lehet például az ügyfél-támogatási érdeklődő vagy a DevOps-kezelő.

6. Ha a kérelemhez közvetlen hozzáférésre van szükség az ügyféladatok eléréséhez, a rendszer egy Ügyfélszéf kérelmet kezdeményez. Például távoli asztali hozzáférés az ügyfél virtuális géphez.

    A kérés most már egy **ügyfél által bejelentett** állapotban van, és az ügyfél jóváhagyására vár a hozzáférés megadása előtt.

7. Az ügyfél szervezetében az Azure-előfizetéshez tartozó [tulajdonosi szerepkörrel](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) rendelkező felhasználó e-mailt kap a Microsofttól, hogy értesítést kapjon a függőben lévő hozzáférési kérelemről. Ügyfélszéf kérelmek esetében ez a személy a kijelölt jóváhagyó.

    Példa e-mailre:

    ![Azure Ügyfélszéf – értesítő e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. Az e-mail-értesítés az adminisztrációs modul **Ügyfélszéf** paneljére mutató hivatkozást tartalmaz. Ha ezt a hivatkozást használja, a kijelölt jóváhagyó bejelentkezik a Azure Portalba, és megtekintheti a szervezete által Ügyfélszéf függőben lévő kérelmeket:

    ![Azure Ügyfélszéf – Kezdőlap](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   A kérelem négy napig az ügyfél-várólistában marad. Ezt követően a hozzáférési kérelem automatikusan lejár, és a Microsoft-mérnökök nem kapnak hozzáférést.

9. A függőben lévő kérelem részleteinek beszerzéséhez a kijelölt jóváhagyó kiválaszthatja a **függőben lévő kérelmekből** származó kulcstároló-kéréseket:

    ![Azure Ügyfélszéf – a függőben lévő kérelem megtekintése](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. A kijelölt jóváhagyó azt is kiválaszthatja, hogy a **szolgáltatási kérelem azonosítója** megtekintse az eredeti felhasználó által létrehozott támogatási jegyre vonatkozó kérelmet. Ez az információ kontextust biztosít a Microsoft ügyfélszolgálata és a jelentett probléma előzményeihez. Például:

    ![Azure Ügyfélszéf – a támogatási jegyre vonatkozó kérelem megtekintése](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. A kérelem áttekintése után a kijelölt jóváhagyó kiválasztja a **jóváhagyás** vagy a **Megtagadás** lehetőséget:

    ![Azure Ügyfélszéf – válassza a jóváhagyás vagy a Megtagadás lehetőséget](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    A kijelölés eredményeképpen:
    - **Jóváhagyás**: a Microsoft mérnöke hozzáférést kap. A hozzáférés az alapértelmezett nyolc órás időszakra van megadva.
    - **Megtagadás**: a Microsoft mérnök emelt szintű hozzáférési kérelmét a rendszer elutasítja, és nem végez további műveletet.

Naplózási célokra az ebben a munkafolyamatban végrehajtott műveletek naplózása [Ügyfélszéf kérelmek naplófájljaiban](#auditing-logs)történik.

## <a name="auditing-logs"></a>Auditnaplók

Az Ügyfélszéf-naplókat a rendszer a tevékenységnaplókban tárolja. A Azure Portal a Ügyfélszéf kérelmekkel kapcsolatos naplózási információk megtekintéséhez válassza a **tevékenységek naplóit** . Konkrét műveletekre is szűrhet, például a következőkre:
- **Széf letiltására irányuló kérelem**
- **Széf létrehozására irányuló kérelem**
- **Széf jóváhagyására irányuló kérelem**
- **Kulcstároló-kérelem lejárata**

Lássunk erre egy példát:

![Azure Ügyfélszéf – tevékenységek naplói](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>A Ügyfélszéf integrálása az Azure biztonsági teljesítménytesztjével

Bevezetünk egy új alapszintű vezérlőelemet ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) az Azure biztonsági teljesítménytesztben, amely a Ügyfélszéf alkalmazhatóságát fedi le. Az ügyfelek mostantól kihasználhatják a teljesítménytesztet, és áttekinthetik Ügyfélszéf alkalmazhatóságát a szolgáltatásokhoz.

## <a name="exclusions"></a>Kizárások

Ügyfélszéf kérelmeket a következő mérnöki támogatási helyzetekben nem indítja el a rendszer:

- Egy Microsoft-mérnöknek olyan műveletet kell végrehajtania, amely a szabványos működési eljárásokon kívül esik. Például akkor, ha nem várt vagy kiszámíthatatlan forgatókönyvekben szeretné helyreállítani vagy visszaállítani a szolgáltatásokat.
- A Microsoft-mérnök a hibaelhárítás során hozzáfér az Azure-platformhoz, és véletlenül az ügyféladatokhoz is hozzáfér. Például az Azure hálózati csapata olyan hibaelhárítást végez, amely egy hálózati eszközön lévő csomag rögzítését eredményezi. Ebben az esetben, ha az ügyfél az adatátvitel közben titkosítja az adatforgalmat, akkor a mérnök nem tudja beolvasni az adatforgalmat.

## <a name="next-steps"></a>Következő lépések

A Ügyfélszéf minden olyan ügyfél számára elérhető, aki egy minimális **fejlesztői** szintű [Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/) rendelkezik. A Ügyfélszéf a Ügyfélszéf panel [felügyeleti moduljának](https://aka.ms/customerlockbox/administration) használatával engedélyezheti.

Ügyfélszéf a kérelmeket egy Microsoft mérnök kezdeményezi, ha ez a művelet egy támogatási eset megvalósításához szükséges.
