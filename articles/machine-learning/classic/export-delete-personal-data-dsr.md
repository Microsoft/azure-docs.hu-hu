---
title: 'ML Studio (klasszikus): exportálás & az adatai törlése – Azure'
description: A Azure Machine Learning Studio (klasszikus) által tárolt termékbeli adatok a Azure Portalon és a hitelesített REST API-kon keresztül is exportálhatók és törlésre használhatók. A telemetria-adatok az Azure adatvédelmi portálján érhetők el. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: b2d797cc6cb45a76a61a235e7c4c66ac6f243580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519864"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Terméken belüli felhasználói adatok exportálása és törlése Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




A Azure Portal, a Studio (klasszikus) felület, a PowerShell és a hitelesített REST API-k használatával törölheti vagy exportálhatja Azure Machine Learning Studio (klasszikus) tárolt termékeit. Ebből a cikkből megtudhatja, hogyan. 

A telemetria-adatok az Azure adatvédelmi portálján érhetők el. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Milyen típusú felhasználói adatokat gyűjt a Studio (klasszikus)?

A szolgáltatás esetében a felhasználói adatok a munkaterületek elérésére jogosult felhasználók információit és a szolgáltatással való felhasználói interakciók telemetria.

A Machine Learning Studio (klasszikus) kétféle felhasználói adattal rendelkezik:
- **Személyes fiókadatok:** A fiókhoz társított fiók-azonosítók és e-mail-címek.
- **Ügyféladatok:** Az elemzéshez feltöltött adatértékek.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>A Studio (klasszikus) fióktípus és az adattárolási módok

A Machine Learning Studio (klasszikus) háromféle fiókkal rendelkezik. Az Ön által megadott fiók típusa határozza meg az adattárolás módját, valamint azt, hogy miként lehet törölni vagy exportálni.

- A **vendég munkaterület** egy ingyenes, Névtelen fiók. A regisztrációhoz hitelesítő adatok, például e-mail cím vagy jelszó megadása nélkül regisztrálhat.
    -  Az adattisztítás a vendég munkaterület lejárta után törlődik.
    - A vendég felhasználó a felhasználói felület, a REST API-k vagy a PowerShell-csomag segítségével exportálhatja az ügyféladatokat.
- Az **ingyenes munkaterület** ingyenes fiók, amellyel bejelentkezhet Microsoft-fiók hitelesítő adataival – e-mail-cím és jelszó.
    - A személyes és az ügyféladatokat is exportálhatja és törölheti, amelyekre az adattulajdonosi jogosultsági (DSR) kérelmek vonatkoznak.
    - Az ügyféladatokat a felhasználói felület, a REST API-k vagy a PowerShell-csomag használatával exportálhatja.
    - Az Azure AD-fiókokat nem használó ingyenes munkaterületek esetében az telemetria az adatvédelmi portálon lehet exportálni.
    - Ha törli a munkaterületet, az összes személyes ügyfél-adatelemet törli.
- A **standard munkaterület** a bejelentkezési hitelesítő adatokkal való hozzáféréshez használt fizetős fiók.
    - A személyes és az ügyféladatokat is exportálhatja és törölheti, amelyekre a DSR-kérelmek vonatkoznak.
    - Az adatok az Azure Privacy Portalon keresztül érhetők el
    - Személyes és ügyféladatokat a felhasználói felületen, a REST API-kon vagy a PowerShell-csomagon keresztül is exportálhatunk
    - Az adatait törölheti a Azure Portalban.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Munkaterület-adattárolás törlése a Studióban (klasszikus) 

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A felhasználók törölhetik a munkaterületen lévő eszközöket, ha kijelölik őket, majd a Törlés gombra kattintanak.

![Eszközök törlése Machine Learning Studio (klasszikus)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Teljes munkaterület törlése

A felhasználók a teljes munkaterületet is törölhetik:
- Fizetős munkaterület: törölje a Azure Portalon keresztül.
- Szabad munkaterület: használja a Delete (Törlés) gombot a **Beállítások** ablaktáblán.

![Ingyenes munkaterület törlése Machine Learning Studio (klasszikus)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Studio-(klasszikus) adatexportálás a PowerShell-lel
A PowerShell használatával az összes adatát hordozható formátumba exportálhatja Azure Machine Learning Studio (klasszikus) parancsokkal. További információ: [Azure Machine learning Studio (klasszikus) PowerShell-modulja](powershell-module.md) .

## <a name="next-steps"></a>Következő lépések

A webszolgáltatásokra és a kötelezettségvállalási terv számlázására vonatkozó dokumentációért lásd: [Azure Machine learning Studio (klasszikus) REST API-hivatkozás](/rest/api/machinelearning/).