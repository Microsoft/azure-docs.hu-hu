---
title: A Microsoft &reg; Smooth streaming ügyféloldali Porting Kit licencelése
description: További információ a Microsoft &reg; Smooth streaming ügyfél-Porting Kit licenceléséről.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: 51d2c4553a745d9a9dfb477694da4526588ed0ea
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951478"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>A Microsoft &reg; Smooth streaming ügyféloldali Porting Kit licencelése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming ügyfél-telepítési csomag (**SSPK** ) egy olyan Smooth streaming ügyfél-implementáció, amely a beágyazott eszközök gyártóinak, a kábeleknek és a mobil operátoroknak, a tartalomszolgáltatóknak, a kézibeszélő-gyártóknak, a független szoftvergyártóknak, valamint a megoldás-szolgáltatóknak az adaptív tartalmak Smooth streaming formátumban való továbbítására optimalizált termékeket és szolgáltatásokat hoz létre. A SSPK olyan Smooth Streaming-ügyfél eszköz-és platform-független implementációja, amelyet a licenccel bármely eszközre és platformra lehet átadni. 

Az alábbiakban látható egy magas szintű architektúra és IIS-Smooth Streaming a bevezetési készlet a Microsoft által biztosított Smooth Streaming ügyfél-implementáció, amely tartalmazza az Smooth Streaming-tartalmak lejátszásához szükséges összes alapvető logikát. Ezt a tartalmat egy adott eszközhöz vagy platformhoz tartozó partnerek a megfelelő felületek bevezetésével egy adott eszközhöz vagy platformhoz csatolják. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
A SSPK a kiváló üzleti értékkel rendelkező feltételekkel rendelkezik. A SSPK-licenc a következőket biztosítja az iparág számára:

* Smooth Streaming a Porting Kit forrása a C++-ban 
  * Smooth Streaming-ügyfél funkcióinak implementálása
  * formázási elemzés, heurisztikus, pufferelési logika stb.
* Player Application API-k 
  * programozási felületek a Media Player alkalmazással való interakcióhoz
* Platform absztrakciós réteg (PAL) felülete 
  * programozási felületek az operációs rendszerrel való interakcióhoz (szálak, szoftvercsatornák)
* Hardverabsztrakciós réteg (HAL) felülete 
  * programozási felületek a hardver A/V-dekóderekkel való interakcióhoz (dekódolás, renderelés)
* Digitális Rights Management (DRM) interfész 
  * a DRM kezelésére szolgáló programozási felületek a DRM absztrakciós réteg (DAL) használatával
  * A Microsoft PlayReady-portok különálló, de ezen a felületen keresztül integrálva vannak. A Microsoft PlayReady-eszközök licencelésével kapcsolatos további információkért kattintson [ide](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Megvalósítási minták 
  * minta PAL implementáció Linux rendszerhez
  * a GStreamer minta HAL-implementációja

## <a name="licensing-options"></a>Licencelési lehetőségek
A Microsoft Smooth Streaming ügyfél-előfizetési csomag elérhető két különálló licencszerződés keretében a licencek számára: az egyik a Smooth Streaming ügyfél ideiglenes termékeinek fejlesztése, valamint a Smooth Streaming ügyfél végleges termékeinek végfelhasználók számára történő terjesztése.

* A chipset-gyártók, rendszerintegrátorok vagy független szoftvergyártók (ISV-ket) esetében, akiknek a forráskód-előkészítési csomaggal kell rendelkezniük az ideiglenes termékek fejlesztéséhez, a Microsoft Smooth Streaming ügyfél-előkészítő csomag **ideiglenes licencét** kell végrehajtani.
* Azokhoz az eszközökhöz és szoftvergyártók számára, akik terjesztési jogokat igényelnek Smooth Streaming ügyfél végső termékeinek a végfelhasználók számára, a Microsoft Smooth Streaming ügyfél-telepítési csomag **végső termékének licencét** kell végrehajtani.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming ügyféloldali Porting Kit – ideiglenes termék licence
Ebben a licencben a Microsoft egy Smooth Streaming ügyfél-bekötési készletet és a szükséges szellemi tulajdoni jogokat biztosít a Smooth Streaming-ügyfél ideiglenes termékeinek fejlesztéséhez és terjesztéséhez más Smooth Streaming ügyfél-Porting Kit-eszközökre, amelyek az ügyfelek végső termékeit Smooth Streaming forgalmazzák.

#### <a name="fee-structure"></a>Díj szerkezete
Az USA $50 000-es egyszeri licencelési díja hozzáférést biztosít a Smooth Streaming ügyfél-Porting Kit-hez. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming ügyféloldali Porting Kit végleges termék licence
Ebben a licencben a Microsoft minden szükséges szellemi tulajdonjogot biztosít ahhoz, hogy Smooth Streaming ügyfél ideiglenes termékeit a többi Smooth Streaming ügyfél-előfizetéstől kapja, és a céges Smooth Streaming ügyfél végső termékeit a végfelhasználók számára terjessze.

#### <a name="fee-structure"></a>Díj szerkezete
A Smooth Streaming ügyfél végleges termékét a következők szerint kínáljuk a jogdíj-modell keretében:

* $0,10/eszköz megvalósításának elszállítása
* A jogdíj minden évben $50 000-kor van korlátozva
* Az első 10 000-es eszköz implementációja minden évben díjmentes 

## <a name="licensing-procedure-and-sspk-access"></a>Licencelési eljárás és SSPK-hozzáférés
[sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com)Az összes licencelési lekérdezésre vonatkozó e-mail-cím.

A SSPK terjesztési portál elérhető a regisztrált közbenső licencek számára.

Az ideiglenes és a végleges SSPK-licencek technikai kérdéseket is beküldhetnek [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>A Microsoft Smooth Streaming ügyfél ideiglenes termék-licencszerződése

* Ügyes Business Solutions, Inc
* Speciális digitális szórásos biztonsági társítás
* AirTies Kablosuz Iletism Sanayive dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC multimédia szoftver Co., Ltd.
* Cavium, Inc.
* EchoStar-vásárlási vállalat
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Sáfrányos digitális korlátozott
* Szecsuán Changhong, Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>A Microsoft Smooth Streaming ügyfél végleges termékre vonatkozó licencszerződései
* Speciális digitális szórásos biztonsági társítás
* AirTies Kablosuz Iletism Sanayive dis Ticaret A.S.
* AmTRAN Technology Co., Ltd 
* Arcadyan Technology Corporation
* Arcelik. S
* Compal Electronics, Inc.
* EXPRESSZ SZERENCSE TECHNOLOGY LIMITED
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Hisense International Co., Ltd.
* Hisense Visual Technology Co., Ltd
* HKC Corporation Limited
* Hong Kong Konka Ltd
* Innolux Corporation
* Innopia Technologies, Inc
* Jinpin elektromos vállalat Kft. Zhuhai. S. E. Z
* K-tronics (Suzhou) Technology Co., Ltd. 
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Mega Fame Electronics Co. Limited
* MIRC Electronics Limited
* MOKA INTERNATIONAL LIMITED
* Nintendo Co., Ltd.
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Panasonic Corporation
* Qingdao Haier OPTRONICS Co., Ltd.
* Shenzhen ATEKO PHOTO áramszolgáltató Co., Ltd.
* Shenzhen Chuangwei – RGB Elektronika Co., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxmade Technology Co., Ltd
* Shenzhen MTC Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Szecsuán Changhong, Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SMARDTV GLOBÁLIS SAS
* SoftAtHome
* Sony Corporation
* Technicolor kézbesítési technológiák, SAS
* Top Victory Investments, Ltd.
* UMC Lengyelország SP. z. o.o.
* Vezír, Inc.
* ZTE Corporation

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

