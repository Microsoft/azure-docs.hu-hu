---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027639"
---
A Azure Batch futó feladatok a futásakor kimeneti adatokat hozhatnak létre. A feladat kimeneti adatait gyakran kell tárolni a feladat egyéb feladatai, a feladatot futtató ügyfélalkalmazás vagy mindkettő között. A tevékenységek a kimeneti adatokat egy batch számítási csomópont fájlrendszerére írják, de a csomóponton lévő összes adat elvész, amikor a rendszer újrarendszerképét végzi, vagy amikor a csomópont elhagyja a készletet. A feladatok is rendelkezhetnek fájl-megőrzési időtartammal, amely után a feladat által létrehozott fájlok törlődnek. Ezen okok miatt fontos a feladat kimenetének megőrzése, amelyre később szüksége lesz egy adattárba, például az [Azure Storage](../articles/storage/index.yml)-ba.

A Storage-fiók beállításai a Batchben: [Batch-fiókok és Azure Storage-fiókok](../articles/batch/accounts.md#azure-storage-accounts).