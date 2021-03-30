---
title: További hivatkozások az Azure-hoz készült avere-vFXT
description: Ezekkel az erőforrásokkal további információkat találhat az Azure-hoz készült avere-vFXT, beleértve a avere-fürt dokumentációját és a vFXT-felügyeleti dokumentációt.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271090"
---
# <a name="additional-documentation"></a>További dokumentáció

Ez a cikk a avere Vezérlőpult felügyeleti felületével és a kapcsolódó témakörökkel kapcsolatos további dokumentációra mutató hivatkozásokat tartalmaz.

## <a name="avere-cluster-documentation"></a>Avere-fürt dokumentációja

További avere-fürt dokumentációja a webhelyén, a következő címen érhető el: <https://azure.github.io/Avere/> . Ezek a dokumentumok segítenek megérteni a fürt képességeit, valamint a beállítások konfigurálásának módját.

* A [FXT-fürt létrehozási útmutatója](<https://azure.github.io/Avere/#fxt_cluster>) olyan fürtökhöz lett tervezve, amelyek fizikai hardveres csomópontokból állnak, de a dokumentumban található információk a vFXT-fürtökhöz is relevánsak. Az új vFXT-fürt rendszergazdái az alábbi fejezetek elolvasását élvezhetik:
  * A [támogatási és figyelési beállítások testreszabása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) elmagyarázza, hogyan szabhatja testre a támogatás feltöltési beállításait és engedélyezheti a távoli figyelést.
  * A [VServers és a globális névtér konfigurálása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) az ügyfélre irányuló névtér létrehozásával kapcsolatos információkat tartalmaz.
  * [A DNS konfigurálása a AVERE-fürthöz](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) elmagyarázza, hogyan kell konfigurálni a ciklikus multiplexelés DNS-t.
  * [Háttérbeli tárolási dokumentumok hozzáadása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) a Core filers hozzáadásához.

* A [fürt konfigurációs útmutatója](<https://azure.github.io/Avere/#operations>) egy avere-fürt beállításainak és beállításainak teljes referenciája. A vFXT-fürtök ezen beállítások egy részhalmazát használják, de a legtöbb esetben ugyanazok a konfigurációs lapok érvényesek.

* Az [irányítópult-útmutató](<https://azure.github.io/Avere/#operations>) ismerteti, hogyan használható a avere Vezérlőpult fürt figyelési funkciói.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT-létrehozási és-kezelési dokumentáció

A vfxt.py, egy parancsfájl-alapú felhőalapú fürt létrehozási és felügyeleti segédprogramjának használatával kapcsolatos teljes útmutató a GitHubon érhető el: [felhőalapú fürt kezelése a vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)-mel.
