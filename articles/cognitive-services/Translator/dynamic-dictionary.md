---
title: A dinamikus szótár – Translator Text API
titlesuffix: Azure Cognitive Services
description: Tudnivalók a Translator Text API dinamikus szótár funkcióját használja.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 72c23270d6e8cdef55f07d0b702fd1858a7710f8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389555"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>A dinamikus szótár funkciója a Translator Text API használata

Ha már ismeri a fordítást egy szót vagy kifejezést a alkalmazni szeretné, adja meg, mint belül a kérelem markup. A dinamikus szótár csak eszköze biztonságosan főnevek, mint például a megfelelő és a termék nevét.

**Szintaxis:**

< mstrans:dictionary fordítási = "kifejezés helyett szerepel fordítási" > kifejezést < / mstrans:dictionary >

**Példa: en-de:**

Forrás bemenet: A word < mstrans:dictionary fordítási =\"wordomatic\"> szó vagy kifejezés < / mstrans:dictionary > dictionary-bejegyzés.

Kimenet: Das Wort "wordomatic" Izraeli normál idő szerint ein Wörterbucheintrag.

Ez a funkció rendelkező és anélküli HTML mód ugyanúgy működik.

A szolgáltatás takarékosan. A megfelelő, és sokkal jobb megoldás a fordítás testreszabása módja egyéni Translator használatával. Egyéni a fordítót lehetővé teszi a környezetet és statisztikai valószínűségek teljes használatát. Ha van, vagy hozhat létre a betanítási adatok, amely megjeleníti a munkahelyi vagy az a kifejezés a környezetben, sokkal jobb eredményeket kap. További információ az egyéni Translator, annak [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
