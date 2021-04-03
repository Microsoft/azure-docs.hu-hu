---
title: A kulcstároló konfigurálása Azure Data Boxhoz
description: Ismerje meg, hogyan használhatja a Ügyfélszéft a Azure Data Box használatával.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124911"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Azure Data Box Ügyfélszéf használata (előzetes verzió)

A Azure Data Box a vásárlói adatok Azure-ba történő átvitelére szolgál. Vannak olyan példányok, amelyekben előfordulhat, hogy a Microsoft ügyfélszolgálata egy Support request során el kell érniük az ügyféladatokat. Az adatelérési kérelmek áttekintéséhez és jóváhagyásához, illetve elutasításához használhatja a Ügyfélszéf felületet. 

Ez a cikk bemutatja, hogyan kezdeményezték a Ügyfélszéf kérelmeket, és nyomon kövessük Data Box importálási és exportálási rendeléseket. A cikk a Azure Data Box eszközökre és Azure Data Box Heavy eszközökre is vonatkozik. 

## <a name="devops-workflow-for-data-access"></a>DevOps munkafolyamat adateléréshez

A Microsoft támogatási és Data Box operatív csapata általában nem fér hozzá az ügyféladatok eléréséhez. Megpróbálják megoldani a problémákat a szabványos eszközök és telemetria használatával. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Ha a problémák nem oldhatók fel, és nem igényelnek Microsoft ügyfélszolgálatat az adatvizsgálathoz vagy az adatjavításhoz, akkor az igény szerinti hozzáférés a Just in time (JIT) portálon keresztül történik. A JIP-portál érvényesíti a jogosultsági szintet, többtényezős hitelesítést biztosít, valamint a belső Microsoft jóváhagyók jóváhagyását is tartalmazza. Például a jóváhagyó lehet DevOps-kezelő. 

Ha engedélyezte az emelt szintű hozzáférésre vonatkozó kérést a JIT-portálon keresztül, ha engedélyezve van a kulcstároló, a Microsoft a kifejezett hozzájárulást is megköveteli az információ eléréséhez. A hozzáférés kérése és nyomon követése a portál Ügyfélszéf szolgáltatásán keresztül történik. 

Ha nem engedélyezte a kulcstárolót, az Ön beleegyezik az adataihoz való hozzáférésre.


## <a name="prerequisites-for-access-request"></a>Hozzáférési kérelem előfeltételei

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Egy Azure Data Box sorrendet hozott létre a következők szerint:
    1. Oktatóanyag: az importálási rendelések [Azure Data Box sorrendje](data-box-deploy-ordered.md) .
    1. Oktatóanyag: az exportálási rendelések [Azure Data Box sorrendje](data-box-deploy-export-ordered.md) .

2. Data Boxhoz konfigurálta a Ügyfélszéf. Ez egy opt-in szolgáltatás. 

    1. A Ügyfélszéf jelenleg előzetes verzióban érhető el Data Box szolgáltatáshoz. Ha Ügyfélszéft szeretne engedélyezni a szervezet Data Box számára, regisztráljon az [Azure nyilvános előzetes](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)verziójának Ügyfélszéf.
    2. A Ügyfélszéf automatikusan elérhető minden olyan ügyfél számára, akik Azure-támogatási csomaggal rendelkeznek, és ez a fejlesztő minimális szintű. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Már meg van nyitva egy szolgáltatási kérelem vagy támogatási jegy a probléma megoldásához. A támogatási jegyekkel kapcsolatos információkért tekintse meg a [Data Box szolgáltatási kérelmét](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Nyomkövetés, kérelem jóváhagyása a Kulcstárolón keresztül

Az ügyféladatok elérésére irányuló kérelem nyomon követéséhez és jóváhagyásához kövesse az alábbi lépéseket:

1. A Microsoft észleli, hogy az Azure-adatközpontban az adatok feltöltésekor vagy letöltésekor probléma merül fel. Az **Adatmásolási** fázisban például a Data Box sorrend le van állítani. 

    A támogatási szakember a támogatási munkameneten keresztül csatlakozik Data Boxhoz, és a szabványos eszközök és telemetria használatával megkísérli elhárítani a problémát. Ha a Data Box lemezek zárolva vannak, és a megosztások nem érhetők el, akkor a támogatási szakember létrehoz egy kulcstároló-kérelmet. 
 
2. A kérelem létrehozásakor az értesítés általában az előfizetés-adminisztrátorra mutat, de az értesítésekhez csoport is konfigurálható. 

3. A kulcstároló-kérést a Azure Portalban tekintheti meg a jóváhagyáshoz. 

    ![Kérelem Azure Portal](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    A kulcstároló-kérelem portálról való jóváhagyásához válassza a **jóváhagyás** lehetőséget.

    ![Kérelem jóváhagyása](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    A kérés jóváhagyása után az eszköz lemezei fel lesznek oldva, és a megosztások elérhetők a támogatási munkamenetben.

4. A támogatási szakember feloldja a feltöltési problémát, és letiltja a támogatási munkamenetet.

A probléma megoldása után az adatmásolási feladatot a rendszer befejezi.


## <a name="next-steps"></a>Következő lépések

- [Ügyfélszéf a Microsoft Azure-hoz](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->