---
title: Helyi integrációs modul automatikus frissítésre és lejáratra vonatkozó értesítése
description: További információ a saját üzemeltetésű integrációs modul automatikus frissítéséről és a lejárati értesítésekről
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376258"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Helyi integrációs modul automatikus frissítésre és lejáratra vonatkozó értesítése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető a saját üzemeltetésű integrációs modul automatikus frissítése a legújabb verzióra, és hogyan kezeli az ADF a saját üzemeltetésű integrációs modul verzióit.

## <a name="self-hosted-integration-runtime-auto-update"></a>Saját üzemeltetésű Integration Runtime automatikus frissítése
Ha a helyi gépen vagy egy Azure-beli virtuális gépen telepít egy saját üzemeltetésű integrációs modult, akkor két lehetőség közül választhat a saját üzemeltetésű integrációs modul verziójának kezeléséhez: automatikus frissítés vagy manuális karbantartás. Az ADF általában a saját üzemeltetésű integrációs modul két új verzióját bocsátja rendelkezésére minden hónapban, amely új funkció kiadását, hibajavítását vagy javítását tartalmazza. Ezért javasoljuk, hogy frissítsen a legújabb verzióra a legújabb funkció és a fejlesztés érdekében.

A legkényelmesebb módszer az automatikus frissítés engedélyezése a saját üzemeltetésű integrációs modul létrehozásakor vagy szerkesztésekor. Ezt követően a rendszer automatikusan frissíti a legújabb verziót. Azt is megteheti, hogy a frissítést a legmegfelelőbb időpontra ütemezze, ahogy szeretné.

![Automatikus frissítés engedélyezése](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Az utolsó frissítés DateTime értéket a saját üzemeltetésű Integration Runtime-ügyfélben tekintheti meg.

![A frissítési idő ellenőrzésének képernyőképe](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> A saját üzemeltetésű integrációs modul stabilitásának biztosítása érdekében, bár két verziót bocsátunk ki, a rendszer havonta egyszer automatikusan frissíti. Néha azt is tapasztalja, hogy az automatikusan frissített verzió a legújabb verzió korábbi verziója. Ha a legújabb verziót szeretné beszerezni, lépjen a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Saját üzemeltetésű Integration Runtime elévülési értesítés
Ha manuálisan szeretné szabályozni a saját üzemeltetésű integrációs modul verziószámát, letilthatja az automatikus frissítés beállítását, és manuálisan is telepítheti azt. A saját üzemeltetésű integrációs modul minden verziója egy év múlva lejár. A lejáró üzenet az ADF-Portálon és a saját üzemeltetésű Integration Runtime Client **90 nappal** a lejárat előtt jelenik meg.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [az Integration Runtime fogalmait a Azure Data Factoryban](./concepts-integration-runtime.md).

- Ismerje meg, hogyan [hozhat létre saját üzemeltetésű integrációs modult a Azure Portal](./create-self-hosted-integration-runtime.md).
