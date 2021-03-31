---
title: Azure Media Services kimeneti metaadatok sémája | Microsoft Docs
description: Ez a cikk áttekintést nyújt Azure Media Services kimeneti metaadat-sémáról.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008284"
---
# <a name="output-metadata"></a>Kimeneti metaadatok

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés
A kódolási feladatok olyan bemeneti adategységekhez (vagy eszközökhöz) vannak társítva, amelyeken bizonyos kódolási feladatokat kíván végrehajtani. Például kódoljon egy MP4-fájlt H. 264 MP4 adaptív sávszélességű készletbe; miniatűr létrehozása; hozzon létre átfedéseket. Egy feladat befejezése után a rendszer kimeneti eszközt állít elő.  A kimeneti eszköz tartalmaz videót, hangot, miniatűrt stb. A kimeneti eszköz egy olyan fájlt is tartalmaz, amely tartalmazza a kimeneti eszköz metaadatait. A metaadatok XML-fájljának neve a következő formátumú: &lt; source_file_name &gt;_manifest.xml (például BigBuckBunny_manifest.xml).  

A Media Services nem jelleggel a bemeneti eszközöket a metaadatok létrehozásához. A bemeneti metaadatok csak olyan összetevőként jönnek létre, amikor egy bemeneti objektumot dolgoz fel egy feladatban. Ezért ez az összetevő a kimeneti eszközre íródik. A bemeneti eszközök és a kimeneti eszközök metaadatainak létrehozásához különböző eszközök használhatók. Ezért a bemeneti metaadatok némileg eltérő sémával rendelkeznek, mint a kimeneti metaadatok.

Ha meg szeretné vizsgálni a metaadat-fájlt, létrehozhat egy **sas** -keresőt, és letöltheti a fájlt a helyi számítógépre.  

Ez a cikk a kimeneti metada ( &lt; source_file_name &gt;_manifest.xml) alapjául szolgáló XML-séma elemeit és típusait ismerteti. A bemeneti adategységre vonatkozó metaadatokat tartalmazó fájllal kapcsolatos információkért lásd a bemeneti metaadatokat.  

A jelen cikk végén található teljes séma-kód és XML-példa megtalálható.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> AssetFiles gyökérelem
A kódolási feladatokhoz tartozó AssetFile-bejegyzések gyűjteménye.  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs = "0" maxOccurs = "1" |Egy AssetFile elem, amely a AssetFiles gyűjtemény részét képezi. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile elem
Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br/><br/> Kötelező |**xs: karakterlánc** |A Media Asset fájljának neve. |
| **Méret**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: hosszú** |Az adategység fájljának mérete bájtban megadva. |
| **Időtartam**<br/><br/> Kötelező |**xs: időtartam** |Tartalom lejátszási ideje – időtartam. |

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Források** |A AssetFile létrehozásához feldolgozott bemeneti/forrás médiafájlok gyűjteménye. További információ: forrásoldali elem. |
| **VideoTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Minden fizikai AssetFile tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videó-zeneszámot. További információ: VideoTracks elem. |
| **AudioTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Minden fizikai AssetFile tartalmazhatnak nulla vagy több hangsávot a megfelelő tároló formátumba. Ezen hangsávok gyűjteménye. További információ: AudioTracks elem. |

## <a name="sources-element"></a><a name="Sources"></a> Források elem
A AssetFile létrehozásához feldolgozott bemeneti/forrás médiafájlok gyűjteménye.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Forrás**<br/><br/> minOccurs = "1" maxOccurs = "nem kötött" |Az eszköz létrehozásakor használt bemeneti/forrásfájl. További információ: forrásoldali elem. |

## <a name="source-element"></a><a name="Source"></a> Forrásoldali elem
Az eszköz létrehozásakor használt bemeneti/forrásfájl.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br/><br/> Kötelező |**xs: karakterlánc** |Bemeneti forrásfájl neve. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks elem
Minden fizikai AssetFile tartalmazhatnak nulla vagy több, a megfelelő tároló formátumba felhasználható videó-zeneszámot. A **VideoTracks** elem az összes videó sáv gyűjteményét jelöli.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs = "1" maxOccurs = "nem kötött" |Egy adott videó nyomon követése a szülő AssetFile. További információ: VideoTrack elem. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> VideoTrack elem
Egy adott videó nyomon követése a szülő AssetFile.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |A videó nyomon követésének nulla alapú indexe. **Megjegyzés:**  Ez az **azonosító** nem feltétlenül az MP4-fájlban használt TrackID. |
| **FourCC**<br/><br/> Kötelező |**xs: karakterlánc** |Videós kodek FourCC kódja |
| **Profil** |**xs: karakterlánc** |H264-profil (csak H264-kodekre alkalmazható). |
| **Szintű** |**xs: karakterlánc** |H264-szint (csak H264-kodekre alkalmazható). |
| **Szélesség**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Kódolt videó szélessége képpontban megadva |
| **Magasság**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Kódolt videó magassága képpontban megadva. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: dupla** |Videó megjelenítési oldalarányának számlálója |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: dupla** |Videó megjelenítési méretarányának nevezője |
| **Képkockasebesség**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: decimális** |A képkockák sebességét. 3F formátumban mérjük. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: decimális** |A célként megadott videó képkockájának sebessége. 3F formátumban. |
| **Sávszélességű**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Az átlagos videó átviteli sebessége kilobit/másodpercben, a AssetFile számítva. Csak az elemes adatfolyam-adattartalmat számolja, és nem tartalmazza a csomagolási terhelést. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |A videó nyomon követésére irányuló átlagos bitráta, a kódolási beállításkészlettől függően, kilobit/másodpercben. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**xs: int** |A videó nyomon követésére szolgáló maximális GOP-os átlagos sávszélesség (kilobit/mp). |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks elem
Minden fizikai AssetFile tartalmazhatnak nulla vagy több hangsávot a megfelelő tároló formátumba. A **AudioTracks** elem az összes hangsávok gyűjteményét jelöli.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs = "1" maxOccurs = "nem kötött" |Egy adott hangsávot a szülő AssetFile. További információ: AudioTrack elem. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> AudioTrack elem
Egy adott hangsávot a szülő AssetFile.  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |A hangsávok nulla alapú indexe. **Megjegyzés:**  Ez nem feltétlenül az MP4-fájlokban használt TrackID. |
| **Codec** |**xs: karakterlánc** |Hangsávok kodekének karakterlánca |
| **EncoderVersion** |**xs: karakterlánc** |Nem kötelező kódoló verziószáma, amely a EAC3 esetében szükséges. |
| **Csatornák**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Hangcsatornák száma |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Mintavételezési sebesség (minta/mp vagy Hz) |
| **Sávszélességű**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |A bitek másodpercenkénti átlagos átviteli sebessége a AssetFile számítva. Csak az elemes adatfolyam-adattartalmat számolja, és nem tartalmazza a csomagolási terhelést. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Kötelező |**xs: int** |Bit/minta a wFormatTag formátum típushoz. |

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs = "0" maxOccurs = "1" |Hangerő-mérési eredmények paramétereinek megadása További információ: LoudnessMeteringResultParameters elem. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters elem
Hangerő-mérési eredmények paramétereinek megadása  

Megtalálhatja az XML-példa XML- [példáját](#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs: karakterlánc** |A **Dolby** Professional hangmérési fejlesztési csomag verziószáma. |
| **DialogNormalization**<br/><br/> minInclusive = "-31" maxInclusive = "-1"<br/><br/> Kötelező |**xs: int** |A DPLM-en keresztül generált DialogNormalization szükséges, ha a LoudnessMetering be van állítva |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Kötelező |**xs: float** |Integrált hangerő |
| **IntegratedLoudnessUnit**<br/><br/> Kötelező |**xs: karakterlánc** |Integrált fiziológiai egység. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Kötelező |**xs: karakterlánc** |Kapuzás-azonosító |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**xs: float** |Beszéd tartalma a programban, százalékként. |
| **SamplePeak**<br/><br/> Kötelező |**xs: float** |A csúcsérték abszolút értéke, mivel az Alaphelyzetbe állítás vagy a legutóbbi törlés óta, csatornánként.  Az egységek dBFS. |
| **SamplePeakUnit**<br/><br/> rögzített = "dBFS"<br/><br/> Kötelező |**xs: anySimpleType** |Minta maximális egysége |
| **TruePeak**<br/><br/> Kötelező |**xs: float** |A maximális igaz csúcsérték, mint az ITU-R BS. 1770-2, mivel az Alaphelyzetbe állítás óta, vagy a legutóbbi törlés óta, csatornánként. Az egységek dBTP. |
| **TruePeakUnit**<br/><br/> rögzített = "dBTP"<br/><br/> Kötelező |**xs: anySimpleType** |Igaz csúcs egysége |

## <a name="schema-code"></a>Séma kódja
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

A következő XML egy példa a kimeneti metaadat fájlra.  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
