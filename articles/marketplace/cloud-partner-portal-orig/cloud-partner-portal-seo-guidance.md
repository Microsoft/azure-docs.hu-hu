---
title: Az Azure Marketplace-en keresőmotor-Optimalizálást útmutató
description: Keresés keresőmotor-optimalizálás (SEO) jelentős nyújt útmutatást.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pabutler
ms.openlocfilehash: f5b956ed1197e3898c9536bda3a93a41e8ee35c0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935122"
---
# <a name="azure-marketplace-seo-guidance"></a>Az Azure Marketplace-en keresőmotor-Optimalizálást útmutató

Ez a cikk azt ismerteti, hogyan maximalizálhatja az ajánlat felderíthetőség keresztül a keresési funkciókat a [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Algoritmus általános magyarázata

A piactér a Microsoft Azure Search szolgáltatást a webhely keresési képességekkel tárolóház használatára. Az algoritmus alapul kifejezés gyakorisága – inverz dokumentum gyakorisága ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). A standard [Lucene-elemzőt](https://lucene.apache.org/core/) szolgál.

Általában a teljes szöveg mezők, kategóriák és iparágak és bekerült a weightage a relevancia. Speciális feltételeket, a ritkán használt alkalmazások azonban gyakran az alkalmazásban való kereséssel egyezés magasabb pontszámot hoz létre. Így többek között például a "Virtuális gép" használati kis benefit járna, mivel a "Az Azure search" speciális sokkal lenne.
Az alábbiakban a leginkább megfelelő mezőket kell figyelembe venni.

 
|  Mező                   | Fontosság | Útmutatás                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Csomag neve               |  Magas      | A pontos vagy közel teljes egyezését a keresési lekérdezés magas prioritás előállításához.                       |
| Közzétevő neve           |  Magas      | A pontos vagy közel teljes egyezését a keresési lekérdezés magas prioritás előállításához.                       |
| Rövid leírás        |  Közepes    | Az alkalmazások és a közzétevő elnevezési megadott nevek szinte garantálja a nagy rangsoroló, nem lehet a dolgokat. Ebben az esetben egy rövid leírást, kritikus fontosságú. Tartsa meg a szöveg, rövid és lényegre törő. Kulcsszavak és várt keresési kifejezéseket is tartalmaznia kell a legjobb eredmény.  Kevésbé hatékony, mint például "Ez az a legjobb kiskereskedelmi POS-ra épülő teljes Dynamics 365" a "kiskereskedelmi POS (értékesítési pont), a Dynamics 365".  | 
| Hosszú leírás         |  Alacsony       | Leírás egy megoldást találhat javaslatokat kínál. A leghatékonyabb leírások ésszerű származnak, és a kulcsszavak szolgálnak.  Egy-az-ponthoz leírások kulcsszavakat használó több mint hosszú hosszú szöveges előnyeit. Győződjön meg arról, hogy a legfontosabb kifejezések, például az "IoT", a leírásban találhatók.  |
| Termékkategória       | Közepes     |  Termékkategóriák közzétevő lehetőségeket és a Microsoft kombinációja határozza meg. Válassza ki ezen kategóriák megfelelően, hogy a felhasználók könnyen megtalálhatják az alkalmazások a megfelelő kategóriában. |
|  |  |  |


## <a name="other-tips"></a>További tippek

-   Keresés beolvasása (nagy erőforrásigényű) felhasználói tevékenység javasol. Alkalmazás név vagy közzétevő egyezések értékelésénél azokat. Rövid leírás esetében a Ha a keresett kifejezés nem egyezik pontosan azzal a közzétevő neve/kulcsmező válik.
-   Letölthető dokumentumok keresési weightage nem szerepelnek.
-   Az alkalmazások tényleges beszerzési és használatát keresési rangsorolás is befolyásolja. Például két azonos alkalmazásokat, ahol technológiáikkal nagymértékben több felhasználó rendelkezik-e egy magasabb prioritást kap.
