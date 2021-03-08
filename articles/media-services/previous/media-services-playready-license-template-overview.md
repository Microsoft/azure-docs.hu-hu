---
title: Media Services PlayReady-sablon áttekintése
description: Ez a témakör áttekintést nyújt a PlayReady-licencek konfigurálásához használt PlayReady-licencekről.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 6bcf6b439f8e49d194e1c0ea5dc1e996135589cf
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454311"
---
# <a name="media-services-playready-license-template-overview"></a>Media Services PlayReady-sablon áttekintése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services mostantól biztosít egy szolgáltatást a PlayReady-licencek kézbesítéséhez. Ha a lejátszó (például a Silverlight) megpróbálja lejátszani a PlayReady-védelemmel ellátott tartalmat, a rendszer egy kérelmet küld a licenc kézbesítési szolgáltatásnak a licenc beszerzéséhez. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, az az ügyfélnek eljuttatott licencet adja ki, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Media Services olyan API-kat is biztosít, amelyek segítségével konfigurálhatja a PlayReady-licenceket. A licencek tartalmazzák azokat a jogokat és korlátozásokat, amelyeket a PlayReady Digital Rights Management (DRM) futtatókörnyezete kényszerít, ha a felhasználó a védett tartalom lejátszását kísérli meg.
Íme néhány példa a PlayReady-licencek korlátozására:

* A licenc érvényességének dátuma és időpontja.
* A licenc érvényességének lejárta után a DateTime érték. 
* Ahhoz, hogy a licenc állandó tárolóban legyen mentve az ügyfélen. Az állandó licenceket általában a tartalom offline lejátszásának engedélyezésére használják.
* A lejátszó által a tartalom lejátszásához szükséges minimális biztonsági szint. 
* A audio\video kimeneti vezérlő kimeneti védelmi szintje. 
* További információ: "output Controls" (3,5) szakasz a [PlayReady megfelelőségi szabályok](https://www.microsoft.com/playready/licensing/compliance/) dokumentumban.

> [!NOTE]
> Jelenleg csak a PlayReady-licenc PlayRight lehet konfigurálni. Ezt a jogosultságot kötelező megadni. A PlayRight lehetővé teszi, hogy az ügyfél képes legyen lejátszani a tartalmat. A PlayRight használatával a lejátszásra vonatkozó korlátozásokat is konfigurálhat. További információ: [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

A PlayReady-licencek Media Services használatával történő konfigurálásához konfigurálnia kell a Media Services PlayReady-licenc sablonját. A sablon XML-ben van definiálva.

Az alábbi példa a legegyszerűbb (és leggyakoribb) sablont mutatja be, amely egy alapszintű streaming-licencet konfigurál. Ezzel a licenccel az ügyfelek le tudják játszani a PlayReady által védett tartalmat.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <PlayRight />
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

Az XML megfelel a "PlayReady-sablon XML-sémája" szakaszban meghatározott PlayReady-sablon XML-sémájának.

A Media Services olyan .NET-osztályokat határoz meg, amelyek segítségével szerializálható és deszerializálható az XML-fájlból és az XML-ből. A fő osztályok leírását a következő témakörben tekintheti meg: [Media Services .net-osztályok](media-services-playready-license-template-overview.md#classes) , amelyek a Tanúsítványsablonok konfigurálására szolgálnak.

A PlayReady-licencek sablonjának konfigurálására szolgáló, végpontok közötti példát a [PlayReady dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](media-services-protect-with-playready-widevine.md)című témakörben talál.

## <a name="media-services-net-classes-that-are-used-to-configure-license-templates"></a><a id="classes"></a>A licenc-sablonok konfigurálásához használt .NET-osztályok Media Services
A következő osztályok az Media Services PlayReady-licencek konfigurálásához használt fő .NET-osztályok. Ezek az osztályok a [PlayReady-licenc sablonjának XML-sémájában](media-services-playready-license-template-overview.md#schema)definiált típusokhoz képezhetők le.

A [MediaServicesLicenseTemplateSerializer](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_mediaserviceslicensetemplateserializer) osztály használatával szerializálhatja és deszerializálhatja a Media Services licenc sablonjának XML-fájlját.

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicenseresponsetemplate): ez az osztály a felhasználó számára visszaküldött válasz sablonját jelöli. Tartalmaz egy mezőt a licenckiszolgáló és az alkalmazás közötti egyéni adatkarakterlánchoz (ami hasznos lehet az egyéni alkalmazás-logika esetében). Emellett egy vagy több tanúsítványsablon listáját is tartalmazza.

A sablon-hierarchiában a legfelső szintű osztályként a válasz sablon tartalmazza a licenc-sablonok listáját. A licencek (közvetlenül vagy közvetve) az összes többi olyan osztályt tartalmazzák, amelyek felkészítik a sablonra vonatkozó adatfeldolgozást.

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicensetemplate): ez az osztály a felhasználók számára visszaadott PlayReady-licencek létrehozására szolgáló licenc-sablont jelöli. A licencben szereplő tartalmi kulcsban található adatokat tartalmazza. Emellett olyan jogokat és korlátozásokat is tartalmaz, amelyeket a PlayReady DRM-futtatókörnyezetnek meg kell kényszeríteni a tartalmi kulcs használatakor.

### <a name="playreadyplayright"></a><a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadyplayright): ez az osztály a PlayReady-licenc PlayRight jelöli. Lehetővé teszi, hogy a felhasználó a licencben konfigurált korlátozások és a PlayRight (a lejátszásra vonatkozó szabályzat esetében) a tartalomra vonatkozó korlátozásokat is lejátssza. A PlayRight vonatkozó szabályzatok nagy része olyan kimeneti korlátozásokra vonatkozik, amelyek a tartalom lejátszásához használható kimeneti típusokat szabályozzák. Emellett olyan korlátozásokat is tartalmaz, amelyeket meg kell tenni egy adott kimenet használatakor. Ha például a DigitalVideoOnlyContentRestriction engedélyezve van, a DRM-futtatókörnyezet csak a videó digitális kimeneteken való megjelenítését teszi lehetővé. (Az analóg videó kimenetei nem jogosultak a tartalom továbbítására.)

> [!IMPORTANT]
> Az ilyen típusú korlátozások lehetnek hatékonyak, de befolyásolhatják a felhasználói élményt is. Ha a kimeneti védelem túlságosan korlátozó, előfordulhat, hogy egyes ügyfeleken nem játszhatók le a tartalom. További információ: [PlayReady megfelelőségi szabályok](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

A Silverlight által támogatott védelmi szintek például a következő témakörben találhatók: [Silverlight-támogatás a kimeneti védelemhez](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838192(v=vs.95)).

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>PlayReady licenc sablonjának XML-sémája
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
  <xs:complexType name="AgcAndColorStripeRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
  <xs:simpleType name="ContentType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Unspecified" />
      <xs:enumeration value="UltravioletDownload" />
      <xs:enumeration value="UltravioletStreaming" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
  <xs:complexType name="PlayReadyContentKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
  <xs:complexType name="ContentEncryptionKeyFromHeader">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence />
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence>
          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
  <xs:complexType name="PlayReadyLicenseResponseTemplate">
    <xs:sequence>
      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
  <xs:complexType name="PlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
  <xs:simpleType name="PlayReadyLicenseType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Nonpersistent" />
      <xs:enumeration value="Persistent" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
  <xs:complexType name="PlayReadyPlayRight">
    <xs:sequence>
      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
  <xs:simpleType name="UnknownOutputPassingOption">
    <xs:restriction base="xs:string">
      <xs:enumeration value="NotAllowed" />
      <xs:enumeration value="Allowed" />
      <xs:enumeration value="AllowedWithVideoConstriction" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
  <xs:complexType name="ScmsRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]