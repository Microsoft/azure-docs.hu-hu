---
title: Fürt létrehozása az Azure Data Explorer hiba
description: Ez a cikk ismerteti a fürt létrehozása az Azure Data Explorer hibaelhárítási lépéseit.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0edf9ebcde2df7e639666f8fe7472baacdeb8640
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212182"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Elhárítása: Hiba fürt létrehozása az Azure Data Explorer

A fürtlétrehozás meghiúsult az Adatkezelőben az Azure nem túl valószínű esetben kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy fürtöt, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy nincs érvényesítési hiba kapcsolódó a fürt neve alatt megadott **fürt létrehozása** az Azure Portalon.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/>). Keresse meg a régióban, ahol próbál a fürt létrehozása az Azure adatkezelő állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürt létrehozása után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).