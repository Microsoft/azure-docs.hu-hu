---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "72808814"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Az üzeneteket különböző forrásokhoz fogja irányítani azon tulajdonságok alapján, amelyeket a szimulált eszköz csatolt az üzenethez. Azokat az üzeneteket, amelyekhez nincs egyéni útválasztás beállítva, az alapértelmezett végpontra (üzenetek/események) küldi a rendszer. A következő oktatóanyagban üzeneteket küld a IoT Hubnek, és megtekintheti őket a különböző célhelyekre irányítva.

|Érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Ennek első lépéseként be kell állítania azt a végpontot, amelyhez az adattovábbítás történik. A második lépés a végpontot használó üzenet útvonalának beállítása. Az Útválasztás beállítása után megtekintheti a végpontokat és az üzenetek útvonalait a portálon.