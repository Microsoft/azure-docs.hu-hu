---
title: Smooth Streaming Protocol (MS-SSTR) HEVC-Azure-ra vonatkozó módosítása
description: 'Ez a specifikáció a töredezett MP4-alapú élő streamek protokollját és formátumát ismerteti Azure Media Services HEVC. Ebben a cikkben csak a HEVC továbbításához szükséges módosítások vannak megadva, kivéve a következőt: "(nincs változás)", amely azt jelzi, hogy a szöveg másolása csak pontosítás céljából történik.'
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6454bc863cb5fd628d581fff380c5ab61354f762
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87053051"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Smooth Streaming protokoll (MS-SSTR) HEVC vonatkozó módosítása 

## <a name="1-introduction"></a>1 bevezetés 

Ez a cikk részletes módosításokat tartalmaz az [MS-SSTR] Smooth Streaming protokoll-specifikációban, amely lehetővé teszi a HEVC-kódolt videók Smooth Streamingét. Ebben a specifikációban csak azokat a módosításokat vázoljuk, amelyek a HEVC videós kodek továbbításához szükségesek. A cikk ugyanazt a számozási sémát követi, mint az [MS-SSTR] specifikáció. A cikkben ismertetett üres főcímek az olvasót az [MS-SSTR] specifikációban foglalt helyükre irányítják.  "(Nincs változás)" – azt jelzi, hogy a szöveg másolása csak pontosítási célra történik.

A cikk technikai megvalósítási követelményeket támaszt a HEVC video codec ("hev1" vagy "hvc1" Format tracks) jelzésére egy Smooth Streaming-jegyzékben, és a normatív referenciák frissülnek, hogy azokra a jelenlegi MPEG-szabványokra hivatkozzon, amelyek tartalmazzák a HEVC, a HEVC Common Encryption, valamint az ISO-alapú adathordozó-fájlformátumhoz tartozó mezők neveit a legújabb specifikációkkal összhangban. 

A hivatkozott Smooth Streaming protokoll specifikációja [MS-SSTR] az élő és igény szerinti digitális médiatartalmak (például hang-és video) továbbítására szolgáló vezetékes formátumot ismerteti a következő módon: kódolóról webkiszolgálóra, kiszolgálóról másik kiszolgálóra, valamint egy kiszolgálóról egy HTTP-ügyfélre.
Az MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787)alapú adatstruktúra http-n keresztüli kézbesítése lehetővé teszi a közel valós időben történő zökkenőmentes váltást a tömörített médiatartalmak különböző minőségi szintjei között. Ennek eredményeképpen a HTTP-ügyfél végfelhasználója állandó lejátszási élményt nyújt, még akkor is, ha a hálózati és a videó megjelenítési feltételei változnak az ügyfélszámítógépen vagy az eszközön.

## <a name="11-glossary"></a>1,1 Szószedet 

A következő kifejezések definiálva vannak az *[MS-Glos]* alkalmazásban:

>   **globálisan egyedi azonosító (GUID), univerzálisan egyedi azonosító (UUID)**

A következő kifejezések a dokumentumra vonatkoznak:

>  **összeállítás időpontja:** Az az időpont, amikor egy minta megjelenik az ügyfélen, az   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)definícióban meghatározott módon.
> 
>   **CENC**: Common encryption, az [ISO/IEC 23001-7] második kiadásban meghatározottak szerint.
> 
>   **dekódolási idő:** A minta a (z)   [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695)által definiált, az ügyfélen való dekódolásához szükséges idő.

**töredék:** Egy vagy több **mintát** tartalmazó, független módon letölthető **adathordozó** .

>   **HEVC:** Nagy hatékonyságú videó kódolása az [ISO/IEC 23008-2] meghatározása szerint
> 
>   **jegyzékfájl:** A **bemutató** metaadatainak, amely lehetővé teszi, hogy az ügyfél **adathordozóra** vonatkozó kéréseket készítsen. **adathordozó:** Az ügyfél által a **bemutató** lejátszásához használt tömörített hang-, videó-és szöveges adatok. **adathordozó formátuma:** Egy jól definiált formátum, amely a hanganyagot vagy a videót tömörített **mintaként** jelöli.
> 
>   **bemutató:** Az összes **stream** és az ahhoz kapcsolódó metaadatok készlete, amely egyetlen film lejátszásához szükséges. **kérelem:** Az ügyfél és a kiszolgáló között a (z [) [RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **válaszban** meghatározott HTTP-üzenet, amely a kiszolgálóról az ügyfélnek küldött HTTP-üzenet, a következő módon: [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) .
> 
>   **minta:** A legkisebb alapvető egység (például keret), amelyben a rendszer tárolja és feldolgozza az   **adathordozót** .
> 
>   **lehet** , hogy nem kell: Ezek a kifejezések (az összes sapkában) a   [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) című témakörben leírtak szerint jelennek meg, a választható viselkedés bármely utasítása pedig lehet, hogy vagy nem.

## <a name="12-references"></a>1,2-referenciák

>   A Microsoft Open Specifications dokumentációra mutató hivatkozások nem tartalmazzák a közzétételi évet, mert a hivatkozások a dokumentumok legújabb verziójára vonatkoznak, amelyek gyakran frissülnek. A más dokumentumokra mutató hivatkozások egy közzétételi évet is tartalmaznak, ha az egyik elérhető.

### <a name="121-normative-references"></a>1.2.1 normatív referenciák 

>  [MS-SSTR] Smooth Streaming protokoll *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)
> 
>   [ISO/IEC 14496-12] International Organization for Standardization, "Information Technology--a hang-vizuális objektumok kódolása – 12. rész: ISO-alap médiafájl formátuma", ISO/IEC 14496-12:2014, 4. kiadás, plusz helyesbítés 1, 1 & 2. módosítás.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] International Organization for Standardization, "információtechnológia – hangvezérelt objektumok kódolása – 15. rész: a külső egység strukturált Videójának szállítása az ISO-alapú adathordozó-fájlformátumban", ISO 14496-15:2015, 3. kiadás.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Információtechnológia – nagy hatékonyságú kódolás és média-kézbesítés heterogén környezetekben – 2. rész: nagy hatékonyságú videó kódolása: 2013 vagy legújabb kiadás   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Információs technológia – MPEG Systems Technologies – 7. rész: általános titkosítás az ISO-alapú médiafájl formátumú fájlokban, CENC-kiadás 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "a" kód "és a" profilok "paraméter a" vödör "adathordozó-típusokhoz" <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Az MP4 regisztrációs szolgáltató, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "az RFC-k által a követelmények szintjének jelzésére használt kulcsszavak", BCP 14, RFC 2119, március 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 – tájékoztató referenciák 

>   [MS-GLOS] Microsoft Corporation, "*Windows-protokollok fő szószedete*"
> 
>   [RFC3548] Josefsson, S., Ed., "a Base16, a Base32 és a Base64 adatkódolások", RFC 3548, július 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., és Overell, P., "kibővített BNF a szintaxis specifikációi: ABNF", STD 68, RFC 5234, január 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1,3 – áttekintés 

>   Az alábbiakban csak a HEVC megadásához szükséges Smooth Streaming-specifikáció módosításait kell megadnia. A változatlan szakasz fejlécei a hivatkozott Smooth Streaming-specifikáció [MS-SSTR] című témakörben találhatók.

## <a name="14-relationship-to-other-protocols"></a>1,4 kapcsolat más protokollokkal 

## <a name="15-prerequisitespreconditions"></a>1,5 előfeltételek/előfeltételek 

## <a name="16-applicability-statement"></a>1,6 alkalmazhatósági nyilatkozat 

## <a name="17-versioning-and-capability-negotiation"></a>1,7 verziószámozás és képesség egyeztetése 

## <a name="18-vendor-extensible-fields"></a>1,8 gyártó – bővíthető mezők 

>   A következő módszert kell használni a streamek azonosítására a HEVC-formátum használatával:
> 
>   * **Egyéni leíró kódok adathordozó-formátumokhoz:** Ezt a képességet a **FourCC** mező adja meg a *2.2.2.5* szakaszban megadott módon.
>   A megvalósítások biztosítják, hogy a bővítmények ne ütköznek az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) paraméterben megadott kiterjesztési kódok az MPEG4-ra-vel való regisztrálásával.

## <a name="19-standards-assignments"></a>1,9 Standard hozzárendelés 

## <a name="2-messages"></a>2 üzenet 

## <a name="21-transport"></a>2,1 átvitel

## <a name="22-message-syntax"></a>2,2 üzenet szintaxisa 

### <a name="221-manifest-request"></a>2.2.1 jegyzékfájl-kérelem 

### <a name="222-manifest-response"></a>2.2.2 jegyzékfájl válasza 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (változó):** A jegyzékfájlhoz tartozó válaszüzenet másodlagos verziója. 2 értékre kell állítani. (Nincs változás)
> 
>   **Időskála (változó):** Az időtartam attribútum időskálája, amely a növekmények számát adja meg egy másodperc alatt. Az alapértelmezett érték 
> 1. (Nincs változás)
> 
>    Az ajánlott érték 90000, amely a képkockák és a töredékes frameráta videót (például 30/1.001 Hz) tartalmazó kódrészletek pontos időtartamát jelöli.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

A ProtectionElement akkor jelennek meg, ha Common Encryption (CENC) a videó-vagy hangadatfolyamokra lett alkalmazva. A HEVC titkosított streameknek meg kell felelniük Common Encryption 2. kiadásnak [ISO/IEC 23001-7]. A VCL-egységekben csak a szeletek adatmennyiségét kell titkosítani.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (változó):** Az adatfolyamban található időtartam-és időértékek időkorlátja, amely egy másodpercben megadott növekmények száma. A HEVC-adatfolyamok esetében a 90000 érték használata ajánlott. A hullámforma mintavételi gyakoriságának megfelelő érték (például 48000 vagy 44100) a hangstreamek esetében ajánlott.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (változó):** Egy négy karakterből álló kód, amely meghatározza, hogy melyik adathordozó-formátumot használja a rendszer az egyes mintákhoz. A következő adattartományok vannak fenntartva a következő szemantikai jelentésekkel:
> 
> * "hev1": videó minták ehhez a nyomon követéshez használja a HEVC videót az [ISO/IEC-14496-15] által meghatározott "hev1" leíró formátum használatával.
>
> * "hvc1": videó minták ehhez a nyomon követéshez használja a HEVC videót az [ISO/IEC-14496-15] által meghatározott "hvc1" leíró formátum használatával.
> 
>   **CodecPrivateData (változó):** Az adathordozó-formátumra jellemző paramétereket, valamint a nyomon követésben szereplő összes mintában közösen megadott adatok hexadecimális kódolású bájtok sztringként jelennek meg. A byte Sequence formátuma és szemantikai jelentése a következőképpen változik a **FourCC** mező értékével:
> 
>   * Ha egy TrackElement leírja a HEVC-videót, a **FourCC** mező értéke **"hev1"** vagy **"hvc1"** .
> 
>   A **CodecPrivateData** mező a következő bájtos sorozat hexadecimális kódolású karakterlánc-ábrázolását tartalmazza, amelyet a ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) adott meg: (nincs változás az MS-SSTR)
> 
>   * % x00% x00% x00% x01 SPSField% x00% x00% x00% x01 PPSField
> 
>   * A SPSField tartalmazza a Sequence paraméter készletét (SPS).
> 
>   * A PPSField tartalmazza a szelet paraméterének készletét (PPS).
> 
>   Megjegyzés: a video set (VPS) nem szerepel a CodecPrivateData-ben, de a tárolt fájlok fejlécében szerepelnie kell a "hvcC" mezőben. A Smooth Streaming protokollt használó rendszereknek további dekódolási paramétereket kell megadniuk (például HEVC szint) a "kodekek" egyéni attribútum használatával.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   A **SmoothStreamingMedia MajorVersion** mezőjét 2 értékre kell állítani, és a **MinorVersion** mezőnek 2 értékűnek kell lennie. (Nincs változás)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 töredék-kérelem 

>   **Megjegyzés**: a 2. és a "hev1", illetve a "Hvc1" **MinorVersion** esetében kért alapértelmezett adathordozó-formátum a "iso8" márka ISO-fájljának formátuma, amely az [ISO/IEC 14496-12] ISO Base Media File Format negyedik kiadásában, valamint [ISO/IEC 23001-7] Common encryption második kiadásban van megadva.

### <a name="224-fragment-response"></a>2.2.4-töredék válasz 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   A **TfxdBox** elavult, és a függvényt a (z) [ISO/IEC 14496-12] szakasz 8.8.12 szakaszában megadott töredék dekódolási időmező ("tfdt") követése váltja fel.
> 
>   **Megjegyzés**: Előfordulhat, hogy egy ügyfél kiszámítja a töredékek időtartamát a futtatási sor ("Trun") mezőben felsorolt mintavételi időtartamok összegzésével, vagy az alapértelmezett mintavételezési időtartammal rendelkező minták számának szorzatával. A "tfdt" baseMediaDecodeTime és a töredék időtartama megegyezik a következő töredék URL-cím-időparaméterével.
> 
>   Szükség szerint be kell szúrni a termelői hivatkozási idő mezőt ("prft") a film töredékének ("moof") előtt, hogy jelezze az első minta által hivatkozott, az [ISO/IEC 14496-12] szakaszban megadott, a film töredékének megadásához szükséges időpontra vonatkozó UTC-időt.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   A **TfrfBox** elavult, és a függvényt a (z) [ISO/IEC 14496-12] szakasz 8.8.12 szakaszában megadott töredék dekódolási időmező ("tfdt") követése váltja fel.
> 
>   **Megjegyzés**: Előfordulhat, hogy egy ügyfél kiszámítja a töredékek időtartamát a futtatási sor ("Trun") mezőben felsorolt mintavételi időtartamok összegzésével, vagy az alapértelmezett mintavételezési időtartammal rendelkező minták számának szorzatával. A "tfdt" baseMediaDecodeTime és a töredék időtartama megegyezik a következő töredék URL-cím-időparaméterével. Az előre megtekintett címek elavultak, mert késleltetik az élő adatfolyamot.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   A **TfhdBox** és a kapcsolódó mezők a töredékben lévő minták metaadatainak alapértelmezett értékeit ágyazzák be. A **TfhdBox** mező szintaxisa az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) szakasz 8.8.7 definiált követési töredék fejlécének szintaxisa.
> 
>   **BaseDataOffset (8 bájt):** Az eltolás bájtban kifejezve a **MdatBox** mező elejétől a **MdatBox** mező minta mezőjébe. Ennek a korlátozásnak a megadásához be kell állítani az alapértelmezett-Base-moof jelzőt (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   A **TrunBox** és a kapcsolódó mezők a kért töredékhez tartozó minta-metaadatokkal vannak beágyazva. A **TrunBox** szintaxisa a [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* szakaszban meghatározott, 1. verziójú adattöredék-futtatási lista szigorú részhalmaza.
> 
>   **SampleCompositionTimeOffset (4 bájt):** Az egyes minták összetételének időbeli eltolása úgy módosult, hogy a töredék első bemutatott mintájának megjelenítési ideje egyenlő legyen az első dekódolású minta dekódolási idejével. Negatív videó minta-összeállítási eltolásokat kell használni,
> 
>   az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) definícióban meghatározottak szerint.
> 
>   Megjegyzés: Ez elkerüli a videó-szinkronizálási hibát, amely a videó lemaradó hangjának a legnagyobb dekódolású képpuffer eltávolítási késleltetésével egyenlő, és a bemutató időzítését is fenntartja az alternatív töredékek között, amelyek eltérő eltávolítási késésekkel rendelkezhetnek.
> 
>   Az ebben a szakaszban megadott, a ABNF   [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) részben meghatározott mezők szintaxisa változatlan marad, kivéve a következőt:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9-töredék válaszának általános mezői 

### <a name="225-sparse-stream-pointer"></a>2.2.5 ritka adatfolyam mutatója 

### <a name="226-fragment-not-yet-available"></a>a 2.2.6 töredék még nem érhető el 

### <a name="227-live-ingest"></a>2.2.7 élő betöltés 

#### <a name="2271-filetype"></a>2.2.7.1 fájltípusa 

>   **Fájltípus (változó):** az MPEG-4 ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787) fájl altípusát és rendeltetésszerű használatát határozza meg, valamint a magas szintű attribútumokat.
> 
>   **MajorBrand (változó):** A médiafájl fő márkája. "Isml" értékre kell állítani.
> 
>   **MinorVersion (változó):** A médiafájl másodlagos verziója. 1 értékre kell állítani.
> 
>   **CompatibleBrands (változó):** Az MPEG-4 támogatott márkákat határozza meg.
>   Tartalmaznia kell a következőket: "CCFF" és "iso8".
> 
>   Az ebben a szakaszban a ABNF   [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) mezőben meghatározott mezők szintaxisa a következő:

```properties
FileType = MajorBrand MinorVersion CompatibleBrands
MajorBrand = STRING_UINT32
MinorVersion = STRING_UINT32
CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)
```

**Megjegyzés**: a "CCFF" és a "iso8" kompatibilitási márkák azt jelzik, hogy a töredékek megfelelnek a "Common Container File Format" és a Common encryption [ISO/IEC 23001-7] és ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 töredék 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 nyomkövetési töredékének kiterjesztett fejléce 

### <a name="228-server-to-server-ingest"></a>2.2.8-kiszolgáló – kiszolgáló betöltés 

## <a name="3-protocol-details"></a>3 protokoll részletei 


## <a name="31-client-details"></a>3,1 ügyfél adatai 

### <a name="311-abstract-data-model"></a>3.1.1 absztrakt adatmodell 

#### <a name="3111-presentation-description"></a>3.1.1.1 bemutatása – Leírás 

>   A bemutató leírása adatelem a bemutató összes metaadatát beágyazza.
> 
>   Megjelenítési metaadatok: a bemutatóban lévő összes adatfolyamra jellemző metaadatok összessége. A megjelenítési metaadatok a következő mezőkből állnak, amelyek a *2.2.2.1* szakaszban vannak megadva:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **Időskála**
> * **Időtartam**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   A HEVC streameket tartalmazó bemutatókat be kell állítani:

```properties
MajorVersion = 2
MinorVersion = 2
```

>   LookaheadCount = 0 (Megjegyzés: mezők elavult)
> 
>   A bemutatókat is be kell állítani:

```properties
TimeScale = 90000
```

>   Stream Collection: A *3.1.1.1.2* szakaszban meghatározott stream Description adatelemek gyűjteménye.
> 
>   Védelem leírása: A védelmi rendszer metaadatait leíró adatelemek gyűjteménye a *3.1.1.1.1* szakaszban meghatározottak szerint.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1-védelmi rendszermetaadatok leírása 

>   A védelmi rendszer metaadatainak Leírás adateleme egyetlen Content Protection rendszerhez tartozó metaadatokat ágyaz be. (Nincs változás)
> 
>   Protection header Description: az egyetlen Content Protection rendszerre vonatkozó tartalomvédelem metaadatai. A védelmi fejléc leírása a következő mezőkből áll, amelyek a *2.2.2.2* szakaszban vannak megadva:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2-adatfolyam leírása 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 követése – Leírás 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 egyéni attribútum leírása 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3-töredék leírása 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Track-Specific részlet leírása 

#### <a name="3112-fragment-description"></a>3.1.1.2-töredék leírása 

##### <a name="31121-sample-description"></a>3.1.1.2.1-minta leírása 

### <a name="312-timers"></a>3.1.2 időzítők 

### <a name="313-initialization"></a>3.1.3 inicializálás 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer indított események 

#### <a name="3141-open-presentation"></a>3.1.4.1 – nyitott bemutató 

#### <a name="3142-get-fragment"></a>3.1.4.2 lekérése 

#### <a name="3143-close-presentation"></a>3.1.4.3 bezárásának bemutatása 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5-feldolgozási események és sorrendi szabályok 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest-kérelem és jegyzékfájl-válasz 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2-töredék-kérelem és-töredék válasz

## <a name="32-server-details"></a>3,2-kiszolgáló részletei

## <a name="33-live-encoder-details"></a>3,3 élő kódoló részletei 

## <a name="4-protocol-examples"></a>4 protokoll – példák 

## <a name="5-security"></a>5 biztonság 

## <a name="51-security-considerations-for-implementers"></a>5,1 biztonsági megfontolások a végrehajtók számára

>   Ha a protokoll használatával szállított tartalom magas kereskedelmi értékkel rendelkezik, akkor a tartalom jogosulatlan használatának megakadályozása érdekében Content Protection rendszer használatát kell használni. A **ProtectionElement** használhatók a Content Protection rendszer használatával kapcsolatos metaadatok továbbítására. A védett hang-és videotartalom formátumát az MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7] által meghatározott módon kell titkosítani.
> 
>   **Megjegyzés**: a HEVC-videók esetében csak a VCL-NALs lévő slice-adatforgalom titkosítva van. A szeletek fejlécei és más NALs a visszafejtés előtt elérhetők a bemutató alkalmazások számára. a biztonságos videó elérési útja során a titkosított információk nem érhetők el a bemutató alkalmazások számára.

## <a name="52-index-of-security-parameters"></a>5,2 biztonsági paraméterek indexe 


| **Biztonsági paraméter**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption dobozok | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 Common Encryption dobozok

A következő mezők szerepelhetnek a töredék-válaszokban, ha Common Encryption alkalmazták, és az [ISO/IEC 23001-7] vagy [ISO/IEC 14496-12] értékben vannak megadva:

1.  Védelmi rendszerspecifikus fejléc mező ("pssh")

2.  Minta titkosítási mező ("senc")

3.  Mintául szolgáló kiegészítő információk eltolási mezője ("Móni")

4.  Példa a kiegészítő információk mérete mezőre ("Saiz")

5.  Minta csoport leírási mezője ("sgpd")

6.  Minta a csoport mezőbe ("sbgp")

---

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
