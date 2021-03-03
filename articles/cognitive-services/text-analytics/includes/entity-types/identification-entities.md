---
title: Azonosító entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750680"
---
### <a name="financial-account-identification"></a>Pénzügyi fiók azonosítása

Az entitás kategóriája pénzügyi információkat és az azonosítás hivatalos formáit tartalmazza.

#### <a name="category-aba-routing-number"></a>Kategória: ABA-útválasztási szám

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        ABA-útválasztási szám

    :::column-end:::
    :::column span="2":::
        **Részletek**

        American Banker Association (ABA) tranzit-útválasztási számok.
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Kategória: SWIFT-kód

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        SWIFT-kód

    :::column-end:::
    :::column span="2":::
        **Részletek**

        SWIFT-kódok a fizetési utasítással kapcsolatos információkhoz.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategória: hitelkártya

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Hitelkártya

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Hitelkártya-számok. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategória: nemzetközi banki fiók száma (IBAN) 

Ez a kategória a következő entitást tartalmazza:

:::row:::
    :::column span="":::
        **Entitás**

        Hitelkártya

    :::column-end:::
    :::column span="2":::
        **Részletek**

        IBAN-kódok a fizetési utasítással kapcsolatos információkhoz.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Kormányzati és ország/régió-specifikus azonosítás

> [!NOTE]
> A következő pénzügyi és országspecifikus entitások nem lesznek visszaadva a `domain=phi` paraméterrel:
> * Passport-számok
> * Adó-azonosítók

A következő entitások az ország szerint vannak csoportosítva és listázva:

#### <a name="argentina"></a>Argentína

:::row:::
    :::column span="":::
        **Entitás**

        Argentin nemzeti identitás (DNI) száma

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Ausztria

:::row:::
    :::column span="":::
        **Entitás**

        Osztrák személyazonosító igazolvány

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztria adóazonosító száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztria-hozzáadottérték-adó (ÁFA) száma

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Ausztrália

:::row:::
    :::column span="":::
        **Entitás**

        Ausztráliai bankszámla száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai üzleti szám

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai vállalat száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai jogosítvány  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai egészségügyi fiók száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai útlevél száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai útlevél száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai adó-fájl száma

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgium

:::row:::
    :::column span="":::
        **Entitás**

        Belgium nemzeti száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgium – hozzáadottérték-adó (ÁFA) száma

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brazília 

:::row:::
    :::column span="":::
        **Entitás**

        Brazília jogi személy száma (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazília CPF száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Brazília nemzeti azonosító kártya (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entitás**

        Kanadai bankszámla száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Kanadai jogosítvány száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai állapotfigyelő szolgáltatás száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai útlevél száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai személyi állapot azonosítójának száma (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Kanadai társadalombiztosítási szám

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entitás**

        Chile személyazonosító igazolványának száma

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Kína

:::row:::
    :::column span="":::
        **Entitás**

        Kínai rezidens személyazonosító kártya (PRC) száma

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Európai Unió (EU)

:::row:::
    :::column span="":::
        **Entitás**

        EU Debit kártya száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Az EU-illesztőprogram licencének száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU nemzeti azonosító száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU Passport-szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        KÖZÖSSÉGI társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU adóazonosító szám (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPS-koordináták

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Franciaország

:::row:::
    :::column span="":::
        **Entitás**

        France-illesztőprogram licencének száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaország egészségbiztosítási száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francia nemzeti azonosító kártya (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaországi Passport-szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaországi társadalombiztosítási szám (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        France-adóazonosító szám (numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        France-hozzáadottérték-adó (ÁFA) száma

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Németország

:::row:::
    :::column span="":::
        **Entitás**

        Német jogosítvány száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi személyazonosító igazolvány száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi Passport-szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi adóazonosító szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi értéknövelő adó száma

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entitás**

        Hong Kong Identity Card (HKID) száma

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Magyarország

:::row:::
    :::column span="":::
        **Entitás**

        Magyarország személyes azonosítójának száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Magyarország adóazonosító száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Magyarország – értéknövelő adó száma

    :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entitás**

        Indiai állandó fiók száma (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Indiai egyedi azonosító (Aadhaar) száma

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonézia

:::row:::
    :::column span="":::
        **Entitás**

        Indonéz személyazonosító kártya (KTP) száma

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Írország

:::row:::
    :::column span="":::
        **Entitás**

        Írországi személyes nyilvános szolgáltatás (PPS) száma

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Entitás**

        Izraeli nemzeti azonosító

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Izraeli bankszámla száma

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Olaszország

:::row:::
    :::column span="":::
        **Entitás**

        Olaszország-illesztőprogram licenc-azonosítója

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Olaszországi pénzügyi kód

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Olaszország értékével hozzáadott adószám

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japán

:::row:::
    :::column span="":::
        **Entitás**

        Japán bankszámla száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Japán jogosítvány száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán "saját szám" (személyes)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán "saját szám" (vállalati)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán rezidens regisztrációs szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán tartózkodási kártya száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán társadalombiztosítási szám (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán Passport-szám

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entitás**

        Luxemburgi nemzeti azonosító szám (természetes személyek)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgi nemzeti azonosító szám (nem természetes személyek)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Málta

:::row:::
    :::column span="":::
        **Entitás**

        Málta személyazonosító igazolványának száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Málta-adóazonosító szám

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Új-Zéland

:::row:::
    :::column span="":::
        **Entitás**

        Új-zélandi bankszámla száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi illesztőprogram licencének száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi belföldi bevétel száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi Ministry of Health Number

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Új-Zéland közösségi jóléti száma

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Fülöp-szigetek

:::row:::
    :::column span="":::
        **Entitás**

        Fülöp-szigetek egységes többcélú azonosító száma

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugália 

:::row:::
    :::column span="":::
        **Entitás**

        Portugál állampolgári kártya száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portugáliai adóazonosító szám

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Szingapúr

:::row:::
    :::column span="":::
        **Entitás**

        Szingapúri nemzeti regisztrációs azonosító kártya (NRIC) száma

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Dél-afrikai Köztársaság

:::row:::
    :::column span="":::
        **Entitás**

        Dél-afrikai azonosító száma

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Dél-Korea

:::row:::
    :::column span="":::
        **Entitás**

        Dél-Korea rezidens regisztrációs száma

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanyolország

:::row:::
    :::column span="":::
        **Entitás**

        Spanyolországi DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanyolországi társadalombiztosítási szám (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanyol adóazonosító szám

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Svájc

:::row:::
    :::column span="":::
        **Entitás**

        Svájci társadalombiztosítási szám adja meg

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tajvan 

:::row:::
    :::column span="":::
        **Entitás**

        Tajvani nemzeti azonosító

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Tajvani rezidens tanúsítvány (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tajvani Passport-szám

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Egyesült Királyság

:::row:::
    :::column span="":::
        **Entitás**

        brit Illesztőprogram licencének száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Választási tekercs száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Nemzeti Állapotfigyelő szolgáltatás (NHS) száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Nemzeti biztosítási szám (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit vagy az Egyesült államokbeli útlevél száma

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       brit Egyedi adófizető hivatkozási szám

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Egyesült Államok

:::row:::
    :::column span="":::
        **Entitás**

        Egyesült államokbeli társadalombiztosítási szám (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli jogosítvány száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült Államok vagy Egyesült Királyság Passport-szám

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli egyedi adófizető-azonosító szám (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli kábítószer-érvényesítő Ügynökség (DEA) száma

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli bankszámla száma

    :::column-end:::
:::row-end:::
