---
title: Alkalmazásbeállítások – LUIS
description: Az Azure Cognitive Services Language Understanding-alkalmazások beállításai az alkalmazásban és a portálon vannak tárolva.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91319163"
---
# <a name="app-and-version-settings"></a>Alkalmazás- és verzióbeállítások

Ezek a beállítások az [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazásban tárolódnak, és a REST API-kkal vagy a Luis-portálon frissülnek.

Az alkalmazás verziójának beállításainak módosítása visszaállítja az alkalmazás betanítási állapotát a képzetlen értékre.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


A szöveges referenciák és példák a következők:

* [Központozási](#punctuation-normalization)
* [Mellékjelek](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Mellékjelek normalizálása

Az alábbi hosszúságú kimondott szöveg bemutatják, hogyan befolyásolják a Mellékjelek a hosszúságú kimondott szöveg:

|Ha a Mellékjelek hamis értékre vannak állítva|Ha a Mellékjelek értéke TRUE (igaz)|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Mellékjelek nyelvi támogatása

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazíliai portugál `pt-br` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Holland `nl-nl` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francia `fr-` mellékjelek

Ide tartozik a francia és a kanadai alkultúra is.

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Német `de-de` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Olasz `it-it` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spanyol `es-` mellékjelek

Ide tartozik a spanyol és a kanadai mexikói is.

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>Központozás normalizálása

A következő hosszúságú kimondott szöveg szemléltetik, hogy a központozás milyen hatással van a hosszúságú kimondott szöveg:

|Az írásjel hamis értékre van állítva|A központozás igaz értékre van állítva|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Írásjel eltávolítva

A következő írásjelek törlődnek a `NormalizePunctuation` tulajdonság értéke TRUE (igaz).

|Központozási|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Következő lépések

* A Mellékjelek és a központozás [fogalmának](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) megismerése.
