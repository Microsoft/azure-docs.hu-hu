---
title: Csatlakozás a GitHubhoz
description: A GitHub használata a közös adatmodell-entitásokra vonatkozó hivatkozások megadásához
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372280"
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