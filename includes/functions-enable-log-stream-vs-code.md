---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "68669669"
---
A streamnaplók bekapcsolása az Azure-ban a függvényalkalmazáshoz:

1. Az F1 billentyűparanccsal nyissa meg a parancskatalógust, és keresse meg, majd futtassa az **Azure Functions: Start Streaming Logs** parancsot.

1. Válassza ki a függvényalkalmazást az Azure-ban, majd válassza az **Igen** lehetőséget, hogy engedélyezze az alkalmazásnaplózást a függvényalkalmazás számára.

1. Aktiválja a függvényeket az Azure-ban. Ekkor a Visual Studio Code Output (Kimenet) ablakában naplózási adatok jelennek meg.

1. Ha végzett, ne feledje futtatni az **Azure Functions: Stop Streaming Logs** parancsot a függvényalkalmazás naplózásának leállításához.