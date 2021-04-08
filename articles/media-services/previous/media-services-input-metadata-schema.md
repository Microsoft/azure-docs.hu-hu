---
title: Azure Media Services bemeneti metaadatok sémája | Microsoft Docs
description: Ez a cikk áttekintést nyújt Azure Media Services bemeneti metaadat-sémáról.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017409"
---
# <a name="input-metadata"></a>Bemeneti metaadatok

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A kódolási feladatok olyan bemeneti adategységekhez (vagy eszközökhöz) vannak társítva, amelyeken bizonyos kódolási feladatokat kíván végrehajtani.  Egy feladat befejezése után a rendszer kimeneti eszközt állít elő.  A kimeneti eszköz tartalmaz videót, hangot, miniatűrt, jegyzékfájlt stb. A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a bemeneti adategység metaadatait. A metaadatok XML-fájljának neve a következő formátumú: &lt; asset_id &gt;_metadata.xml (például 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), ahol a asset_id a &lt; &gt; bemeneti eszköz AssetID értéke.  

A Media Services nem jelleggel a bemeneti eszközöket a metaadatok létrehozásához. A bemeneti metaadatok csak olyan összetevőként jönnek létre, amikor egy bemeneti objektumot dolgoz fel egy feladatban. Ezért ez az összetevő a kimeneti eszközre íródik. A bemeneti eszközök és a kimeneti eszközök metaadatainak létrehozásához különböző eszközök használhatók. Ezért a bemeneti metaadatok némileg eltérő sémával rendelkeznek, mint a kimeneti metaadatok.

Ha meg szeretné vizsgálni a metaadat-fájlt, létrehozhat egy **sas** -keresőt, és letöltheti a fájlt a helyi számítógépre. Talál egy példát arra, hogyan hozhat létre SAS-lokátort, és hogyan tölthet le egy fájlt  [a Media Services .net SDK-bővítmények használatával](media-services-dotnet-get-started.md).  

Ez a cikk az XML-séma azon elemeit és típusait ismerteti, amelyeken a bemeneti metada ( &lt; asset_id &gt;_metadata.xml) alapul.  További információ a kimeneti eszköz metaadatait tartalmazó fájlról: [kimeneti metaadatok](media-services-output-metadata-schema.md).  

A [séma kódja](media-services-input-metadata-schema.md#code) egy [XML-példát](media-services-input-metadata-schema.md#xml) mutat be a cikk végén.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles elem (gyökérelem)
A [AssetFile elemének](media-services-input-metadata-schema.md#AssetFile)gyűjteményét tartalmazza a kódolási feladatokhoz.  

Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

| Név | Leírás |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "nem kötött" |Egyetlen alárendelt elem. További információ: [AssetFile elem](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile elem
 Egy adatfájlt leíró attribútumokat és elemeket tartalmaz.  

 Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br /><br /> Kötelező |**xs: karakterlánc** |Az eszköz fájljának neve. |
| **Méret**<br /><br /> Kötelező |**xs: hosszú** |Az adategység fájljának mérete bájtban megadva. |
| **Időtartam**<br /><br /> Kötelező |**xs: időtartam** |Tartalom lejátszási ideje – időtartam. Példa: időtartam = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Kötelező |**xs: int** |Az adatfájlban lévő adatfolyamok száma. |
| **FormatNames**<br /><br /> Kötelező |**xs: karakterlánc** |Formátumnevek. |
| **FormatVerboseNames**<br /><br /> Kötelező |**xs: karakterlánc** |Részletes nevek formázása |
| **StartTime** |**xs: időtartam** |Tartalom kezdési ideje Példa: kezdő = "PT 2.669 S". |
| **OverallBitRate** |**xs: int** |Az eszköz fájljának átlagos bitrátája kbps-ben. |

> [!NOTE]
> A következő négy alárendelt elemnek egy sorozatban kell megjelennie.  
> 
> 

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Programok**<br /><br /> minOccurs = "0" | |Minden [program elem](media-services-input-metadata-schema.md#Programs) gyűjteménye, amikor az adategység fájlja MPEG-TS formátumú. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Az egyes fizikai adatfájlok tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videókat. Ez az elem az adatfájl részét képező összes [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) gyűjteményét tartalmazza. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Az egyes fizikai adatfájlok tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható hangsávot. Ez az elem az adatfájl részét képező összes [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) gyűjteményét tartalmazza. |
| **Metaadatok**<br /><br /> minOccurs = "0" maxOccurs = "nem kötött" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Az adatfájl metaadatai key\value karakterláncként jelennek meg. Például:<br /><br /> **&lt;Metadata kulcs = "nyelv" érték = "ENG"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **ID**<br /><br /> Kötelező |**xs: int** |A hang-vagy videó nyomon követésének nulla alapú indexe.<br /><br /> Ez nem feltétlenül jelenti azt, hogy az MP4-fájlban használt TrackID. |
| **Codec** |**xs: karakterlánc** |Videó Track kodek karakterlánca |
| **CodecLongName** |**xs: karakterlánc** |Hang-vagy Video Track-kodek hosszú neve. |
| **Időalap**<br /><br /> Kötelező |**xs: karakterlánc** |Idő alapja. Például: időalap = "1/48000" |
| **NumberOfFrames** |**xs: int** |Képkockák száma (a videó sávok esetében). |
| **StartTime** |**xs: időtartam** |A kezdési idő nyomon követése. Példa: kezdő = "PT 2.669 S" |
| **Időtartam** |**xs: időtartam** |Követés időtartama. Példa: időtartam = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> A következő két alárendelt elemnek egy sorozatban kell megjelennie.  
> 
> 

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Törlése**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Megjelenítési adatokat tartalmaz (például azt, hogy egy adott hangsáv a vizuálisan korlátozott nézők számára készült-e). |
| **Metaadatok**<br /><br /> minOccurs = "0" maxOccurs = "nem kötött" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Általános kulcs/érték karakterláncok, amelyek különféle információk tárolására használhatók. Például: Key = "Language", és Value = "ENG". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (örököl a TrackType)
 A **AudioTrackType** egy globális összetett típus, amely a [TrackType](media-services-input-metadata-schema.md#TrackType)örököl.  

 A típus egy adott hangsávot jelöl az objektum fájljában.  

 Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **SampleFormat** |**xs: karakterlánc** |Minta formátuma |
| **ChannelLayout** |**xs: karakterlánc** |Csatorna elrendezése |
| **Csatornák**<br /><br /> Kötelező |**xs: int** |Az audio csatornák száma (0 vagy több). |
| **SamplingRate**<br /><br /> Kötelező |**xs: int** |Mintavételezési sebesség (minta/mp vagy Hz) |
| **Sávszélességű** |**xs: int** |Az adatfájlból kiszámított bit/másodperc átlagos hangátviteli sebessége. A rendszer csak az elemes adatfolyam-adattartalmat számítja ki, és a csomagolási terhelés nem szerepel ebben a számban. |
| **BitsPerSample** |**xs: int** |Bit/minta a wFormatTag formátum típushoz. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (örököl a TrackType)
A **VideoTrackType** egy globális összetett típus, amely a [TrackType](media-services-input-metadata-schema.md#TrackType)örököl.  

A típus egy adott videó-követési számot jelöl az objektum fájljában.  

Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **FourCC**<br /><br /> Kötelező |**xs: karakterlánc** |Videós kodek FourCC kódja |
| **Profil** |**xs: karakterlánc** |A Video Track profilja. |
| **Szintű** |**xs: karakterlánc** |A videó követési szintje. |
| **PixelFormat** |**xs: karakterlánc** |A Video Track képpontjának formátuma |
| **Szélesség**<br /><br /> Kötelező |**xs: int** |Kódolt videó szélessége képpontban megadva |
| **Magasság**<br /><br /> Kötelező |**xs: int** |Kódolt videó magassága képpontban megadva. |
| **DisplayAspectRatioNumerator**<br /><br /> Kötelező |**xs: dupla** |Videó megjelenítési oldalarányának számlálója |
| **DisplayAspectRatioDenominator**<br /><br /> Kötelező |**xs: dupla** |Videó megjelenítési méretarányának nevezője |
| **DisplayAspectRatioDenominator**<br /><br /> Kötelező |**xs: dupla** |Videó minta oldalarányának számlálója |
| **SampleAspectRatioNumerator** |**xs: dupla** |Videó minta oldalarányának számlálója |
| **SampleAspectRatioNumerator** |**xs: dupla** |Videó minta oldalarányának nevezője |
| **Frameráta**<br /><br /> Kötelező |**xs: decimális** |A képkockák sebességét. 3F formátumban mérjük. |
| **Sávszélességű** |**xs: int** |Az adatfájlból kiszámított átlagos video átviteli sebesség (kilobit/mp). A rendszer csak az elemes adatfolyam-adattartalmat számítja ki, és a csomagolási terhelés nem szerepel benne. |
| **MaxGOPBitrate** |**xs: int** |A videó nyomon követésére szolgáló maximális GOP-os átlagos sávszélesség (kilobit/mp). |
| **HasBFrames** |**xs: int** |A B képkockák számának nyomon követése. |

## <a name="metadatatype"></a><a name="MetadataType"></a> MetadataType
A **MetadataType** olyan globális összetett típus, amely az adatfájlok kulcs/érték karakterláncként való metaadatait írja le. Például: Key = "Language", és Value = "ENG".  

Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **kulcs**<br /><br /> Kötelező |**xs: karakterlánc** |A kulcs/érték párokban található kulcs. |
| **value**<br /><br /> Kötelező |**xs: karakterlánc** |A kulcs/érték párokban szereplő érték. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
A **ProgramType** egy globális összetett típus, amely egy programot ír le.  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **ProgramId**<br /><br /> Kötelező |**xs: int** |Program azonosítója |
| **NumberOfPrograms**<br /><br /> Kötelező |**xs: int** |Programok száma. |
| **PmtPid**<br /><br /> Kötelező |**xs: int** |A program-hozzárendelési táblázatok (PMTs-EK) a programok információit tartalmazzák.  További információ: [fiz](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Kötelező |**xs: int** |A dekóder használja. További információ: [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: hosszú** |A bemutató időbélyegének indítása. |
| **EndPTS** |**xs: hosszú** |A bemutató időbélyegzője véget ér. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
A **StreamDispositionType** egy globális összetett típus, amely leírja az adatfolyamot.  

Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Alapértelmezett**<br /><br /> Kötelező |**xs: int** |Ezt az attribútumot állítsa 1-re úgy, hogy ez az alapértelmezett bemutató. |
| **Dub**<br /><br /> Kötelező |**xs: int** |Ezt az attribútumot állítsa 1-re úgy, hogy ez a szinkronizált bemutató legyen. |
| **Eredeti**<br /><br /> Kötelező |**xs: int** |Állítsa az attribútumot 1-re, hogy jelezze, ez az eredeti bemutató. |
| **Megjegyzés**<br /><br /> Kötelező |**xs: int** |Ha ezt az attribútumot 1 értékre állítja, akkor a nyomon követési magyarázatot tartalmaz. |
| **Lyrics**<br /><br /> Kötelező |**xs: int** |Ha ezt az attribútumot 1 értékre állítja, akkor ez azt jelzi, hogy a nyomkövetés tartalmaz dalszövegeket. |
| **Karaoke**<br /><br /> Kötelező |**xs: int** |Ha ezt az attribútumot 1-re állítja, akkor ez jelzi a karaoke-sávot (háttérzene, vokál nélkül). |
| **Kénytelen**<br /><br /> Kötelező |**xs: int** |Ezt az attribútumot állítsa 1-re úgy, hogy jelezze ezt a kényszerített bemutatót. |
| **HearingImpaired**<br /><br /> Kötelező |**xs: int** |Ha ezt az attribútumot 1-re állítja, akkor a nyomon követést végző személyek esetében ezt a számot kell megadni. |
| **VisualImpaired**<br /><br /> Kötelező |**xs: int** |Ezt az attribútumot állítsa 1-re úgy, hogy jelezze, hogy ez a követés a vizuálisan korlátozott. |
| **CleanEffects**<br /><br /> Kötelező |**xs: int** |Ezt az attribútumot 1 értékre állítva jelezheti, hogy ez a szám tiszta hatással van. |
| **AttachedPic**<br /><br /> Kötelező |**xs: int** |Ha ezt az attribútumot 1 értékre állítja, akkor a nyomon követett képekkel jelezheti. |

## <a name="programs-element"></a><a name="Programs"></a> Programok elem
A burkoló elem több **program** elemet is tárol.  

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "nem kötött" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Az MPEG-TS formátumú fájlok esetében az adategységben található programokkal kapcsolatos információkat tartalmaz. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks elem
 Több **VideoTrack** elemet tároló burkoló elem.  

 Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "nem kötött" |[VideoTrackType (örököl a TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Az adatfájlban található videós sávokkal kapcsolatos információkat tartalmaz. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks elem
 Több **AudioTrack** elemet tároló burkoló elem.  

 Tekintse meg a cikk végén található XML-példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "nem kötött" |[AudioTrackType (örököl a TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Az adatfájlban lévő hangsávokkal kapcsolatos információkat tartalmaz. |

## <a name="schema-code"></a><a name="code"></a> Séma kódja
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> XML-példa
A következő példa a bemeneti metaadat-fájlt szemlélteti.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

