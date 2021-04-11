---
title: Csatlakozás a GitHubhoz
description: A GitHub használata a közös adatmodell-entitásokra vonatkozó hivatkozások megadásához
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222112"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>A GitHub használata a Common adatmodell-entitásokra vonatkozó hivatkozások olvasásához

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory GitHub-összekötő csak a [Common adatmodell](format-common-data-model.md) -formátumhoz tartozó entitás-hivatkozási séma fogadására szolgál az adatforgalom leképezése során.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A GitHub társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a **GitHub** értékre kell beállítani. | igen
| userName (Felhasználónév) | GitHub-Felhasználónév | igen |
| jelszó | GitHub-jelszó | igen |

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy [forrás-adatkészletet](data-flow-source.md) a leképezési adatfolyamban.