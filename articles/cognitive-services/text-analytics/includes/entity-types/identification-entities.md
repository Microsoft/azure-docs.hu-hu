---
title: Azonosító entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599304"
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

        Az entitás kategóriájának beszerzéséhez adja hozzá `ABARoutingNumber` a `pii-categories` paramétert a paraméterhez. `ABARoutingNumber` az API-válaszban is visszaadja, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
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

        Az entitás kategóriájának beszerzéséhez adja hozzá `SWIFTCode` a `pii-categories` paramétert a paraméterhez. `SWIFTCode` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Az entitás kategóriájának beszerzéséhez adja hozzá `CreditCardNumber` a `pii-categories` paramétert a paraméterhez. `CreditCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.

    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Az entitás kategóriájának beszerzéséhez adja hozzá `InternationlBankingAccountNumber` a `pii-categories` paramétert a paraméterhez. `InternationlBankingAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="2":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ARNationalIdentityNumber` a `pii-categories` paramétert a paraméterhez. `ARNationalIdentityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Ausztria

:::row:::
    :::column span="":::
        **Entitás**

        Osztrák személyazonosító igazolvány

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ATIdentityCard` a `pii-categories` paramétert a paraméterhez. `ATIdentityCard` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztria adóazonosító száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ATTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `ATTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztria-hozzáadottérték-adó (ÁFA) száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ATValueAddedTaxNumber` a `pii-categories` paramétert a paraméterhez. `ATValueAddedTaxNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Ausztrália

:::row:::
    :::column span="":::
        **Entitás**

        Ausztráliai bankszámla száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `AUDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `AUDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai üzleti szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `AUBusinessNumber` a `pii-categories` paramétert a paraméterhez. `AUBusinessNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai vállalat száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `AUCompanyNumber` a `pii-categories` paramétert a paraméterhez. `AUCompanyNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai jogosítvány  

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `AUDriversLicense` a `pii-categories` paramétert a paraméterhez. `AUDriversLicense` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai egészségügyi fiók száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `AUMedicalAccountNumber` a `pii-categories` paramétert a paraméterhez. `AUMedicalAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai útlevél száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ATPassportNumber` a `pii-categories` paramétert a paraméterhez. `ATPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ausztráliai adó-fájl száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ATTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `ATTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgium

:::row:::
    :::column span="":::
        **Entitás**

        Belgium nemzeti száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `BENationalNumber` a `pii-categories` paramétert a paraméterhez. `BENationalNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgium – hozzáadottérték-adó (ÁFA) száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `BEValueAddedTaxNumber` a `pii-categories` paramétert a paraméterhez. `BEValueAddedTaxNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brazília 

:::row:::
    :::column span="":::
        **Entitás**

        Brazília jogi személy száma (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `BRLegalEntityNumber` a `pii-categories` paramétert a paraméterhez. `BRLegalEntityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazília CPF száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `BRCPFNumber` a `pii-categories` paramétert a paraméterhez. `BRCPFNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazília nemzeti azonosító kártya (RG)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `BRNationalIDRG` a `pii-categories` paramétert a paraméterhez. `BRNationalIDRG` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entitás**

        Kanadai bankszámla száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `CABankAccountNumber` a `pii-categories` paramétert a paraméterhez. `CABankAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai jogosítvány száma

    :::column-end:::

    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `CADriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `CADriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai állapotfigyelő szolgáltatás száma

        
    :::column-end:::

    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `CAHealthServiceNumber` a `pii-categories` paramétert a paraméterhez. `CAHealthServiceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai útlevél száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `CAPassportNumber` a `pii-categories` paramétert a paraméterhez. `CAPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai személyi állapot azonosítójának száma (PHIN)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `CAPersonalHealthIdentification` a `pii-categories` paramétert a paraméterhez. `CAPersonalHealthIdentification` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadai társadalombiztosítási szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `CASocialInsuranceNumber` a `pii-categories` paramétert a paraméterhez. `CASocialInsuranceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entitás**

        Chile személyazonosító igazolványának száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `CLIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `CLIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Kína

:::row:::
    :::column span="":::
        **Entitás**

        Kínai rezidens személyazonosító kártya (PRC) száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `CNResidentIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `CNResidentIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Európai Unió (EU)

:::row:::
    :::column span="":::
        **Entitás**

        EU Debit kártya száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUDebitCardNumber` a `pii-categories` paramétert a paraméterhez. `EUDebitCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Az EU-illesztőprogram licencének száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `EUDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Az EU GPU koordinátái

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUGPSCoordinates` a `pii-categories` paramétert a paraméterhez. `EUGPSCoordinates` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU nemzeti azonosító száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUNationalIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `EUNationalIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUPassportNumber` a `pii-categories` paramétert a paraméterhez. `EUPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        KÖZÖSSÉGI társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUSocialSecurityNumber` a `pii-categories` paramétert a paraméterhez. `EUSocialSecurityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU adóazonosító szám (TIN)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `EUTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `EUTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Franciaország

:::row:::
    :::column span="":::
        **Entitás**

        France-illesztőprogram licencének száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `FRDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaország egészségbiztosítási száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRHealthInsuranceNumber` a `pii-categories` paramétert a paraméterhez. `FRHealthInsuranceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francia nemzeti azonosító kártya (CNI)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRNationalID` a `pii-categories` paramétert a paraméterhez. `FRNationalID` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaországi Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRPassportNumber` a `pii-categories` paramétert a paraméterhez. `FRPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Franciaországi társadalombiztosítási szám (INSEE)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRSocialSecurityNumber` a `pii-categories` paramétert a paraméterhez. `FRSocialSecurityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        France-adóazonosító szám (numéro SPI)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `FRTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        France-hozzáadottérték-adó (ÁFA) száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `FRValueAddedTaxNumber` a `pii-categories` paramétert a paraméterhez. `FRValueAddedTaxNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Németország

:::row:::
    :::column span="":::
        **Entitás**

        Német jogosítvány száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `DEDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `DEDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi személyazonosító igazolvány száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `DEIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `DEIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `DEPassportNumber` a `pii-categories` paramétert a paraméterhez. `DEPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi adóazonosító szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `DETaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `DETaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Németországi értéknövelő adó száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `DEValueAddedNumber` a `pii-categories` paramétert a paraméterhez. `DEValueAddedNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entitás**

        Hong Kong Identity Card (HKID) száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `HKIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `HKIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Magyarország

:::row:::
    :::column span="":::
        **Entitás**

        Magyarország személyes azonosítójának száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `HUPersonalIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `HUPersonalIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Magyarország adóazonosító száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `HUTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `HUTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Magyarország – értéknövelő adó száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `HUValueAddedNumber` a `pii-categories` paramétert a paraméterhez. `HUValueAddedNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entitás**

        Indiai állandó fiók száma (PAN)

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `INPermanentAccount` a `pii-categories` paramétert a paraméterhez. `INPermanentAccount` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Indiai egyedi azonosító (Aadhaar) száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `INUniqueIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `INUniqueIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonézia

:::row:::
    :::column span="":::
        **Entitás**

        Indonéz személyazonosító kártya (KTP) száma

    :::column-end:::
    :::column span="2":::

        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `IDIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `IDIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Írország

:::row:::
    :::column span="":::
        **Entitás**

        Írországi személyes nyilvános szolgáltatás (PPS) száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `IEPersonalPublicServiceNumber` a `pii-categories` paramétert a paraméterhez. `IEPersonalPublicServiceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Írország – személyes nyilvános szolgáltatás (PPS) száma v2

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `IEPersonalPublicServiceNumberV2` a `pii-categories` paramétert a paraméterhez. `IEPersonalPublicServiceNumberV2` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Entitás**

        Izraeli nemzeti azonosító

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ILNationalID` a `pii-categories` paramétert a paraméterhez. `ILNationalID` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Izraeli bankszámla száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ILBankAccountNumber` a `pii-categories` paramétert a paraméterhez. `ILBankAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Olaszország

:::row:::
    :::column span="":::
        **Entitás**

        Olaszország-illesztőprogram licenc-azonosítója

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ITDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `ITDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Olaszországi pénzügyi kód

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ITFiscalCode` a `pii-categories` paramétert a paraméterhez. `ITFiscalCode` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Olaszország értékével hozzáadott adószám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ITValueAddedTaxNumber` a `pii-categories` paramétert a paraméterhez. `ITValueAddedTaxNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japán

:::row:::
    :::column span="":::
        **Entitás**

        Japán bankszámla száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPBankAccountNumber` a `pii-categories` paramétert a paraméterhez. `JPBankAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán jogosítvány száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `JPDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán "saját szám" (személyes)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPMyNumberPersonal` a `pii-categories` paramétert a paraméterhez. `JPMyNumberPersonal` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán "saját szám" (vállalati)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPMyNumberCorporate` a `pii-categories` paramétert a paraméterhez. `JPMyNumberCorporate` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán rezidens regisztrációs szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ITValueAddedTaxNumber` a `pii-categories` paramétert a paraméterhez. `ITValueAddedTaxNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán tartózkodási kártya száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPResidenceCardNumber` a `pii-categories` paramétert a paraméterhez. `JPResidenceCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán társadalombiztosítási szám (SIN)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPSocialInsuranceNumber` a `pii-categories` paramétert a paraméterhez. `JPSocialInsuranceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japán Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `JPPassportNumber` a `pii-categories` paramétert a paraméterhez. `JPPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entitás**

        Luxemburgi nemzeti azonosító szám (természetes személyek)

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `LUNationalIdentificationNumberNatural` a `pii-categories` paramétert a paraméterhez. `LUNationalIdentificationNumberNatural` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgi nemzeti azonosító szám (nem természetes személyek)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `LUNationalIdentificationNumberNonNatural` a `pii-categories` paramétert a paraméterhez. `LUNationalIdentificationNumberNonNatural` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Málta

:::row:::
    :::column span="":::
        **Entitás**

        Málta személyazonosító igazolványának száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `MTIdentityCardNumber` a `pii-categories` paramétert a paraméterhez. `MTIdentityCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Málta-adóazonosító szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `MTTaxIDNumber` a `pii-categories` paramétert a paraméterhez. `MTTaxIDNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Új-Zéland

:::row:::
    :::column span="":::
        **Entitás**

        Új-zélandi bankszámla száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `NZBankAccountNumber` a `pii-categories` paramétert a paraméterhez. `NZBankAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi illesztőprogram licencének száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `NZDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `NZDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi belföldi bevétel száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `NZInlandRevenueNumber` a `pii-categories` paramétert a paraméterhez. `NZInlandRevenueNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Új-zélandi Ministry of Health Number

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `NZMinistryOfHealthNumber` a `pii-categories` paramétert a paraméterhez. `NZMinistryOfHealthNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Új-Zéland közösségi jóléti száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `NZSocialWelfareNumber` a `pii-categories` paramétert a paraméterhez. `NZSocialWelfareNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Fülöp-szigetek

:::row:::
    :::column span="":::
        **Entitás**

        Fülöp-szigetek egységes többcélú azonosító száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `PHUnifiedMultiPurposeIDNumber` a `pii-categories` paramétert a paraméterhez. `PHUnifiedMultiPurposeIDNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugália 

:::row:::
    :::column span="":::
        **Entitás**

        Portugál állampolgári kártya száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `PTCitizenCardNumber` a `pii-categories` paramétert a paraméterhez. `PTCitizenCardNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portugáliai adóazonosító szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `PTTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `PTTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Szingapúr

:::row:::
    :::column span="":::
        **Entitás**

        Szingapúri nemzeti regisztrációs azonosító kártya (NRIC) száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `PTTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `PTTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Dél-afrikai Köztársaság

:::row:::
    :::column span="":::
        **Entitás**

        Dél-afrikai azonosító száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ZAIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `ZAIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Dél-Korea

:::row:::
    :::column span="":::
        **Entitás**

        Dél-Korea rezidens regisztrációs száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `KRResidentRegistrationNumber` a `pii-categories` paramétert a paraméterhez. `KRResidentRegistrationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spanyolország

:::row:::
    :::column span="":::
        **Entitás**

        Spanyolországi DNI

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `ESDNI` a `pii-categories` paramétert a paraméterhez. `ESDNI` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanyolországi társadalombiztosítási szám (SSN)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ESSocialSecurityNumber` a `pii-categories` paramétert a paraméterhez. `ESSocialSecurityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanyol adóazonosító szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `ESTaxIdentificationNumber` a `pii-categories` paramétert a paraméterhez. `ESTaxIdentificationNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Svájc

:::row:::
    :::column span="":::
        **Entitás**

        Svájci társadalombiztosítási szám adja meg

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `CHSocialSecurityNumber` a `pii-categories` paramétert a paraméterhez. `CHSocialSecurityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tajvan 

:::row:::
    :::column span="":::
        **Entitás**

        Tajvani nemzeti azonosító

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `TWNationalID` a `pii-categories` paramétert a paraméterhez. `TWNationalID` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Tajvani rezidens tanúsítvány (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `TWResidentCertificate` a `pii-categories` paramétert a paraméterhez. `TWResidentCertificate` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tajvani Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `TWPassportNumber` a `pii-categories` paramétert a paraméterhez. `TWPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Egyesült Királyság

:::row:::
    :::column span="":::
        **Entitás**

        brit Illesztőprogram licencének száma

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `UKDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `UKDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       brit Választási tekercs száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `UKNationalInsuranceNumber` a `pii-categories` paramétert a paraméterhez. `UKNationalInsuranceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Nemzeti Állapotfigyelő szolgáltatás (NHS) száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `UKNationalHealthNumber` a `pii-categories` paramétert a paraméterhez. `UKNationalHealthNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Nemzeti biztosítási szám (NINO)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `UKNationalInsuranceNumber` a `pii-categories` paramétert a paraméterhez. `UKNationalInsuranceNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit vagy az Egyesült államokbeli útlevél száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `USUKPassportNumber` a `pii-categories` paramétert a paraméterhez. `USUKPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       brit Egyedi adófizető hivatkozási szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `UKUniqueTaxpayerNumber` a `pii-categories` paramétert a paraméterhez. `UKUniqueTaxpayerNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Egyesült Államok

:::row:::
    :::column span="":::
        **Entitás**

        Egyesült államokbeli társadalombiztosítási szám (SSN)

    :::column-end:::
    :::column span="2":::
        **Részletek**

        Az entitás kategóriájának beszerzéséhez adja hozzá `USSocialSecurityNumber` a `pii-categories` paramétert a paraméterhez. `USSocialSecurityNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::
      **Támogatott dokumentumok nyelvei**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli jogosítvány száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `USDriversLicenseNumber` a `pii-categories` paramétert a paraméterhez. `USDriversLicenseNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült Államok vagy Egyesült Királyság Passport-szám

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `USUKPassportNumber` a `pii-categories` paramétert a paraméterhez. `USUKPassportNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli egyedi adófizető-azonosító szám (ITIN)

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `USIndividualTaxpayerIdentification` a `pii-categories` paramétert a paraméterhez. `USIndividualTaxpayerIdentification` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli kábítószer-érvényesítő Ügynökség (DEA) száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `DrugEnforcementAgencyNumber` a `pii-categories` paramétert a paraméterhez. `DrugEnforcementAgencyNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Egyesült államokbeli bankszámla száma

    :::column-end:::
    :::column span="2":::

        Az entitás kategóriájának beszerzéséhez adja hozzá `USBankAccountNumber` a `pii-categories` paramétert a paraméterhez. `USBankAccountNumber` az API-válaszban lesz visszaadva, ha a rendszer észleli.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
