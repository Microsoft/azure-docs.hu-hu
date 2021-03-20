---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89043046"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Az adattárakat a blob Storage-ba lehet írni az [Apache Avro](https://avro.apache.org/) formátumban, amely az alapértelmezett vagy a JSON (előzetes verzió). 
>    
> A JSON-formátum kódolásának lehetősége minden olyan régióban előzetes verzióban érhető el, amelyben IoT Hub elérhető, kivéve az USA keleti régiója, az USA nyugati régiója és Nyugat-Európa. A kódolás formátuma csak akkor állítható be, amikor a blob Storage-végpont konfigurálva van. A már beállított végpontok formátuma nem módosítható. JSON-kódolás használatakor a contentType JSON-ra és a contentEncoding UTF-8 értékre kell állítania az üzenetrendszer tulajdonságai között. 
>
> A blob Storage-végpontok használatával kapcsolatos részletesebb információkért tekintse [meg az Útválasztás a Storage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)szolgáltatáshoz című témakör útmutatását.
>