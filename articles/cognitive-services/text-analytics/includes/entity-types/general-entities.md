---
title: Általános névvel ellátott entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: 43864d3593fda986031cf9b59251b920672476bb
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097266"
---
A Text Analytics a következő általános (nem azonosító) entitás-kategóriákat adja vissza. például a végpontra irányuló kérelmek küldésekor `/entities/recognition/general` .


| Kategória | Leírás                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Személy](#category-person)     | Személyek nevei.  |
| [PersonType](#category-persontype) | Egy személy által birtokolt feladattípusok vagy szerepkörök. |
| [Hely](#category-location)    | Természetes és emberi által készített tereptárgyak, struktúrák, földrajzi funkciók és geopolitikai entitások |
| [Szervezet](#category-organization)  | Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek.  |
| [Esemény](#category-event)  | A korábbi, a közösségi és a természetben előforduló események. |
| [Product](#category-product) | Különböző kategóriák fizikai objektumai. |
| [Ügyességi](#category-skill) | Képesség, szakértelem vagy szakértelem.  |
| [Cím](#category-address) | Teljes levelezési címek.  |
| [Telefonszám](#category-phonenumber) | Telefonszámok. |
| [E-mail](#category-email) | E-mail-címek. |
| [URL-cím](#category-url) | Webhelyek URL-címei. |
| [IP](#category-ip) | Hálózati IP-címek. |
| [Dátum/idő](#category-datetime) | Dátum és napszakok. |


### <a name="category-person"></a>Kategória: személy

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Személy

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Személyek nevei.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategória: PersonType

Ez a kategória a következő entitást tartalmazza:


:::row:::
    :::column span="":::
        **Entitás**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Egy személy által birtokolt feladatok típusai vagy szerepkörei
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategória: hely

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Hely

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Természetes és emberi által készített tereptárgyak, struktúrák, földrajzi funkciók és geopolitikai entitások.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Geopolitikai entitás (GPE)

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Városok, országok/régiók, Államok.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Szerkezeti

    :::column-end:::
    :::column span="2":::

        Az ember által okozott struktúrák. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Földrajzi

    :::column-end:::
    :::column span="2":::

        Földrajzi és természeti adottságok, mint például a folyók, az óceánok és a sivatagok.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategória: szervezet

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Szervezet

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Orvosi

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Orvosi vállalatok és csoportok.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Értéktőzsde

    :::column-end:::
    :::column span="2":::

        Tőzsdei csoportok. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sporttal kapcsolatos szervezetek.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategória: esemény

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Esemény

    :::column-end:::
    :::column span="2":::
        **Részletek**

        A korábbi, a közösségi és a természetben előforduló események.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`,,,, `es` `fr` ,, `de` `it` `zh-hans` `ja` , `ko` `pt-pt` és `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Kulturális

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Kulturális események és ünnepek.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Természetes

    :::column-end:::
    :::column span="2":::

        Természetesen előforduló események.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportesemények.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategória: termék

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Termék

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Különböző kategóriák fizikai objektumai.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Számítástechnikai termékek
    :::column-end:::
    :::column span="2":::
        **Részletek**

        Számítástechnikai termékek.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategória: szakértelem

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Ügyességi

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Képesség, szakértelem vagy szakértelem.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategória: címe

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Cím

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Teljes levelezési cím.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategória: telefonszám

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Telefonszámok (csak az USA-beli és az EU-telefonszámok).
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategória: E-mail

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Részletek**

        E-mail-címek.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategória: URL

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        URL-cím

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Webhelyek URL-címei. 
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategória: IP

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        IP

    :::column-end:::
    :::column span="2":::
        **Részletek**

        hálózati IP-címek. 
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategória: dátum és idő

Ez a kategória a következő entitásokat tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Dátum és napszakok. 
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Az ebben a kategóriában található entitások a következő alkategóriákkal rendelkezhetnek

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Date

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Naptári dátumok.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Idő

    :::column-end:::
    :::column span="2":::

        Napszakok.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Dátumtartomány

    :::column-end:::
    :::column span="2":::

        Dátumtartomány.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Időtartomány

    :::column-end:::
    :::column span="2":::

        Időtartományok.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Időtartam

    :::column-end:::
    :::column span="2":::

        Időtartamok.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Beállítás

    :::column-end:::
    :::column span="2":::

        Beállítás, ismétlődő időpontok.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategória: mennyiség

Ez a kategória a következő entitásokat tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Mennyiség

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Számok és numerikus mennyiségek.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Alkategóriák

Az ebben a kategóriában lévő entitás a következő alkategóriákkal rendelkezhet.

:::row:::
    :::column span="":::
        **Entitás alkategóriája**

        Szám

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Számok.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Százalék

    :::column-end:::
    :::column span="2":::

        Százalékos
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Sorszámok

    :::column-end:::
    :::column span="2":::

        Sorszámok száma
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Életkor

    :::column-end:::
    :::column span="2":::

        Alábbi korhatárt szabja.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Pénznem

    :::column-end:::
    :::column span="2":::

        Valuták
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimenziók

    :::column-end:::
    :::column span="2":::

        Méretek és mérések.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Hőmérséklet

    :::column-end:::
    :::column span="2":::

        Hőmérsékletek.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
