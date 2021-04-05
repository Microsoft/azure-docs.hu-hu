---
title: Azure Cloud Services (bővített támogatás) def. webrole-séma | Microsoft Docs
description: A Cloud Services webes szerepkörével kapcsolatos információk (kiterjesztett támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: fef3e3cc63fb9e1ca6aa64cf799a620f187db76f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744503"
---
# <a name="azure-cloud-services-extended-support-definition-webrole-schema"></a>Azure Cloud Services (bővített támogatás) definíciós webszerepkör-séma

Az Azure webes szerepkör olyan szerepkör, amely az IIS 7 által támogatott webalkalmazás-programozáshoz van testreszabva, például ASP.NET, PHP, Windows Communication Foundation és FastCGI.

A szolgáltatás-definíciós fájl alapértelmezett bővítménye a csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Alapszintű szolgáltatás-definíciós séma webes szerepkörhöz  
A webes szerepkört tartalmazó szolgáltatás-definíciós fájl alapszintű formátuma a következő.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Séma elemei  
A szolgáltatás definíciós fájlja tartalmazza ezeket az elemeket, amelyeket a jelen témakör következő részeiben talál részletesen:  

[Webszerepkör](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Beállítás](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Végpontok](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Tanúsítványok](#Certificates)

[Tanúsítvány](#Certificate)

[Importálja](#Imports)

[Importálás](#Import)

[Futtatókörnyezet](#Runtime)

[Environment](#Environment)

[Változó](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Webhelyek](#Sites)

[Hely](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Kötések](#Bindings)

[Kötés](#Binding)

[Indítás](#Startup)

[Feladat](#Task)

[Tartalom](#Contents)

[Tartalom](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="webrole"></a><a name="WebRole"></a> Webszerepkör  
Az `WebRole` elem egy webalkalmazás-programozáshoz testre szabott szerepkört ismertet, amelyet az IIS 7 és a ASP.net támogat. Egy szolgáltatás nulla vagy több webes szerepkört is tartalmazhat.

Az alábbi táblázat az elem attribútumait ismerteti `WebRole` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A webes szerepkör neve. A szerepkör nevének egyedinek kell lennie.|  
|enableNativeCodeExecution|boolean|Választható. Az alapértelmezett érték `true` :; a natív kód végrehajtása és a teljes megbízhatóság alapértelmezés szerint engedélyezve van. Ezt az attribútumot úgy állítsa be, hogy `false` letiltsa a natív kód végrehajtását a webes szerepkör esetében, és használja helyette az Azure részleges megbízhatóságát.|  
|vmsize|sztring|Választható. Állítsa be ezt az értéket a szerepkörre kiosztott virtuális gép méretének módosításához. Az alapértelmezett érték `Small`. További információ: [Cloud Services virtuális gépek méretei](available-sizes.md).|  

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings  
Az `ConfigurationSettings` elem a webes szerepkör konfigurációs beállításainak gyűjteményét írja le. Ez az elem az elem szülője `Setting` .

##  <a name="setting"></a><a name="Setting"></a> Beállítás  
Az `Setting` elem egy név és egy érték párokat ír le, amely meghatározza a szerepkör egy példányának konfigurációs beállításait.

Az alábbi táblázat az elem attribútumait ismerteti `Setting` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A konfigurációs beállítás egyedi neve.|  

A szerepkör konfigurációs beállításai név és érték párok, amelyek deklarálva vannak a szolgáltatás-definíciós fájlban, és a szolgáltatás konfigurációs fájljában vannak megadva.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources  
Az `LocalResources` elem a webes szerepkör helyi tárolási erőforrásainak gyűjteményét írja le. Ez az elem az elem szülője `LocalStorage` .

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage  
Az `LocalStorage` elem egy helyi tárolási erőforrást azonosít, amely a szolgáltatás futtatásához rendelkezésre álló fájlrendszert biztosít. Egy szerepkör nulla vagy több helyi tárolási erőforrást is meghatározhat.

> [!NOTE]
>  Az `LocalStorage` elem gyermekként is megjelenhet az `WebRole` Azure SDK korábbi verzióival való kompatibilitás támogatásához.

Az alábbi táblázat az elem attribútumait ismerteti `LocalStorage` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A helyi tároló egyedi neve.|  
|cleanOnRoleRecycle|boolean|Választható. Azt jelzi, hogy a helyi tárolót meg kell-e tisztítani a szerepkör újraindításakor. Az alapértelmezett érték `true`.|  
|sizeInMb|int|Választható. A helyi tároló számára lefoglalni kívánt tárterület (MB). Ha nincs megadva, a lefoglalt alapértelmezett tárterület 100 MB. A lefoglalt tárterület minimális mérete 1 MB.<br /><br /> A helyi erőforrások maximális mérete a virtuális gép méretétől függ. További információ: [Cloud Services virtuális gépek méretei](available-sizes.md).|  
  
A helyi tárolási erőforráshoz lefoglalt könyvtár neve megegyezik a name attribútumhoz megadott értékkel.

##  <a name="endpoints"></a><a name="Endpoints"></a> Végpontok  
Az `Endpoints` elem ismerteti egy szerepkör bemeneti (külső), belső és példány típusú bemeneti végpontok gyűjteményét. Ez az elem a `InputEndpoint` , `InternalEndpoint` és elemek szülője `InstanceInputEndpoint` .

A bemeneti és a belső végpontok külön vannak lefoglalva. A szolgáltatás összesen 25 bemeneti, belső és példány típusú bemeneti végponttal rendelkezhet, amelyek lefoglalhatók a szolgáltatásban engedélyezett 25 szerepkör között. Ha például 5 szerepkörrel rendelkezik, legfeljebb 5 bemeneti végpontot foglalhat le, vagy 25 bemeneti végpontot foglalhat le egyetlen szerepkörhöz, vagy 1 bemeneti végpontot is lefoglalhat 25 szerepkörre.

> [!NOTE]
>  Minden telepített szerepkörhöz egy példány szükséges. Az előfizetéshez tartozó alapértelmezett kiépítés legfeljebb 20 magot jelent, így a szerepkör 20 példányára van korlátozva. Ha az alkalmazásnak több példányra van szüksége, mint amennyit az alapértelmezett kiépítés során, tekintse meg a [Számlázási, az előfizetés-kezelés és a kvóta támogatását](https://azure.microsoft.com/support/options/) a kvóta növelésével kapcsolatos további információkért.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint  
Az `InputEndpoint` elem egy webes szerepkörhöz tartozó külső végpontot ír le.

Több végpontot is megadhat, amelyek a HTTP-, HTTPS-, UDP-és TCP-végpontok kombinációját jelentik. Megadhatja a bemeneti végponthoz választott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük. Ha például azt adja meg, hogy egy webes szerepkör a HTTP-t és a 443-es portot használja a HTTPS-hez, akkor előfordulhat, hogy egy második webes szerepkör a 80-as portot 8080 használja a HTTP protokollhoz, és az 8043 portot HTTPS-kapcsolatra.

Az alábbi táblázat az elem attribútumait ismerteti `InputEndpoint` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A külső végpont egyedi neve.|  
|protokoll|sztring|Kötelező. A külső végpont átviteli protokollja. Webes szerepkör esetén a lehetséges értékek a következők:,, `HTTP` `HTTPS` `UDP` vagy `TCP` .|  
|port|int|Kötelező. A külső végpont portja. Megadhat bármely kiválasztott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  
|tanúsítvány|sztring|HTTPS-végponthoz szükséges. Egy elem által meghatározott tanúsítvány neve `Certificate` .|  
|localPort|int|Választható. A végponton belüli belső kapcsolatokhoz használt portot adja meg. Az `localPort` attribútum a végpont külső portját a szerepkör belső portjára képezi le. Ez olyan esetekben hasznos, amikor a szerepkörnek olyan belső összetevővel kell kommunikálnia egy olyan porton, amely különbözik a külsőleg elérhetőtől.<br /><br /> Ha nincs megadva, az értéke `localPort` megegyezik az `port` attribútummal. A "*" értékének beállításával `localPort` automatikusan hozzárendelhet egy nem lefoglalt portot, amely a FUTÁSIDEJŰ API használatával felderíthető.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> Az `localPort` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|  
|ignoreRoleInstanceStatus|boolean|Választható. Ha ennek az attribútumnak az értéke, akkor `true` a rendszer figyelmen kívül hagyja a szolgáltatás állapotát, és a terheléselosztó nem távolítja el a végpontot. Ennek az értéknek a beállításával hasznos lehet a `true` szolgáltatás foglalt példányainak hibakereséséhez. Az alapértelmezett érték `false`. **Megjegyzés:**  A végpontok akkor is fogadhatnak forgalmat, ha a szerepkör nem üzemkész állapotban van.|  
|loadBalancerProbe|sztring|Választható. A bemeneti végponthoz rendelt terheléselosztó-mintavétel neve. További információ: [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint  
Az `InternalEndpoint` elem egy webes szerepkör belső végpontját ismerteti. Egy belső végpont csak a szolgáltatáson belül futó egyéb szerepkör-példányok számára érhető el. a szolgáltatáson kívüli ügyfelek számára nem érhető el. Az elemet nem tartalmazó webes szerepkörök `Sites` csak egyetlen http-, UDP-vagy TCP belső végponttal rendelkezhetnek.

Az alábbi táblázat az elem attribútumait ismerteti `InternalEndpoint` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A belső végpont egyedi neve.|  
|protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek:,, `HTTP` `TCP` `UDP` vagy `ANY` .<br /><br /> A érték `ANY` azt határozza meg, hogy bármely protokoll, bármely port engedélyezett-e.|  
|port|int|Választható. A végponton belüli belső terheléselosztási kapcsolatokhoz használt port. Egy elosztott terhelésű végpont két portot használ. A nyilvános IP-címhez használt port, valamint a magánhálózati IP-címen használt port. Ezek általában ugyanazok, de különböző portok használatát is választhatja.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> Az `Port` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|  

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
Az `InstanceInputEndpoint` elem egy példány bemeneti végpontját írja le egy webes szerepkörhöz. Egy példány bemeneti végpontja egy adott szerepkör-példánnyal van társítva a terheléselosztó porton keresztüli továbbításával. Az egyes példányok bemeneti végpontja egy adott portra van leképezve a lehetséges portok köréből. Ez az elem az elem szülője `AllocatePublicPortFrom` .

Az `InstanceInputEndpoint` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `InstanceInputEndpoint` .
  
| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A végpont egyedi neve.|  
|localPort|int|Kötelező. Meghatározza azt a belső portot, amelyet az összes szerepkör-példány figyelni fog a terheléselosztó által továbbított bejövő forgalom fogadásához. A lehetséges értékek tartománya 1 és 65535 között van, beleértve a értéket.|  
|protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek: `udp` és `tcp`. `tcp`HTTP/HTTPS-alapú forgalomhoz használható.|  
  
##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
Az `AllocatePublicPortFrom` elem azt a nyilvános porttartomány ismerteti, amelyet a külső ügyfelek az egyes példányok bemeneti végpontjának eléréséhez használhatnak. A nyilvános (VIP) portszám le van foglalva ebből a tartományból, és az egyes szerepkör-példányok végpontja számára van hozzárendelve a bérlői telepítés és a frissítés során. Ez az elem az elem szülője `FixedPortRange` .

Az `AllocatePublicPortFrom` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort  
Az `FixedPort` elem a belső végponthoz tartozó portot adja meg, amely lehetővé teszi a terheléselosztás elosztott terhelésű kapcsolatait a végponton.

Az `FixedPort` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `FixedPort` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|port|int|Kötelező. A belső végpont portja. Ennek ugyanaz a hatása, mint a `FixedPortRange` min és a Max beállítása ugyanahhoz a porthoz.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange  
Az `FixedPortRange` elem megadja a belső végponthoz vagy példányhoz tartozó bemeneti végponthoz rendelt portok tartományát, és beállítja a végponton elosztott terhelésű kapcsolatokhoz használt portot.

> [!NOTE]
>  Az `FixedPortRange` elem eltérő módon működik attól függően, hogy melyik elemet tárolja. Ha az `FixedPortRange` elem szerepel a `InternalEndpoint` elemben, a a terheléselosztó összes portját a minimális és a maximális attribútumok tartományán belül nyitja meg az összes olyan virtuális géphez, amelyen a szerepkör fut. Ha a `FixedPortRange` elem szerepel a `InstanceInputEndpoint` elemben, az csak egy portot nyit meg a minimális és a maximális attribútum tartományán belül minden olyan virtuális gépen, amelyen a szerepkör fut.

Az `FixedPortRange` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `FixedPortRange` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|p|int|Kötelező. A tartomány minimális portszáma. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  
|max.|sztring|Kötelező. A tartományon belüli maximális port. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  

##  <a name="certificates"></a><a name="Certificates"></a> Tanúsítványok  
Az `Certificates` elem a webes szerepkör tanúsítványainak gyűjteményét írja le. Ez az elem az elem szülője `Certificate` . Egy szerepkörhöz a társított tanúsítványok száma is tartozhat. További információ a tanúsítványok elem használatáról: [a szolgáltatás definíciós fájljának módosítása tanúsítvánnyal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Tanúsítvány  
Az `Certificate` elem egy webes szerepkörhöz társított tanúsítványt ír le.

Az alábbi táblázat az elem attribútumait ismerteti `Certificate` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A tanúsítvány neve, amely akkor használható, ha egy HTTPS-elemmel társítva van `InputEndpoint` .|  
|storeLocation|sztring|Kötelező. Azon tanúsítványtároló helye, ahol ez a tanúsítvány megtalálható a helyi gépen. A lehetséges értékek a következők: `CurrentUser` és `LocalMachine` .|  
|storeName|sztring|Kötelező. Annak a tanúsítványtárolónak a neve, ahol a tanúsítvány a helyi gépen található. A lehetséges értékek közé tartoznak a beépített tárolók neve,,,,,,,, `My` `Root` `CA` `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` vagy bármely egyéni tároló neve. Ha meg van adva egy egyéni tároló neve, a rendszer automatikusan létrehozza az áruházat.|  
|permissionLevel|sztring|Választható. Megadja a szerepkör-folyamatok számára megadott hozzáférési engedélyeket. Ha azt szeretné, hogy csak emelt szintű folyamatok férhessenek hozzá a titkos kulcshoz, adja meg az `elevated` engedélyt. `limitedOrElevated` az engedély lehetővé teszi az összes szerepkör-folyamat számára a titkos kulcs elérését. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték `limitedOrElevated`.|  

##  <a name="imports"></a><a name="Imports"></a> Importálja  
Az `Imports` elem az importálási modulok gyűjteményét írja le egy webes szerepkörhöz, amely összetevőket ad hozzá a vendég operációs rendszerhez. Ez az elem az elem szülője `Import` . Ez az elem nem kötelező, és egy szerepkör csak egyetlen importálási blokkot tartalmazhat. 

Az `Imports` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="import"></a><a name="Import"></a> Importálása  
Az `Import` elem a vendég operációs rendszerhez hozzáadandó modult adja meg.

Az `Import` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `Import` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|moduleName|sztring|Kötelező. Az importálandó modul neve. Az érvényes importálási modulok a következők:<br /><br /> – RemoteAccess<br />- RemoteForwarder<br />-Diagnosztika<br /><br /> A RemoteAccess és a RemoteForwarder modulok lehetővé teszik a szerepkör-példány konfigurálását a távoli asztali kapcsolatokhoz. További információ: [bővítmények](extensions.md).<br /><br /> A diagnosztikai modul lehetővé teszi a szerepkör-példányok diagnosztikai adatainak gyűjtését.|  

##  <a name="runtime"></a><a name="Runtime"></a> Runtime  
Az `Runtime` elem a környezeti változók egy gyűjteményét írja le egy webes szerepkörhöz, amely az Azure-gazdagép folyamatának futásidejű környezetét vezérli. Ez az elem az elem szülője `Environment` . Ez az elem nem kötelező, és a szerepkörnek csak egy futásidejű blokkja lehet.

Az `Runtime` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

A következő táblázat a elem attribútumait ismerteti `Runtime` :  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|executionContext|sztring|Választható. Meghatározza azt a környezetet, amelyben a szerepkör-folyamat elindul. Az alapértelmezett környezet: `limited` .<br /><br /> -   `limited` – A folyamat rendszergazdai jogosultságok nélkül indul el.<br />-   `elevated` – A folyamat rendszergazdai jogosultságokkal van elindítva.|  

##  <a name="environment"></a><a name="Environment"></a> Környezet  
Az `Environment` elem egy webes szerepkörhöz tartozó környezeti változó beállításainak gyűjteményét ismerteti. Ez az elem az elem szülője `Variable` . Egy szerepkörhöz tetszőleges számú környezeti változó állítható be.

##  <a name="variable"></a><a name="Variable"></a> Változó  
Az `Variable` elem egy környezeti változót határoz meg, amely a vendég operációs szolgáltatásban állítható be.

Az `Variable` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

A következő táblázat a elem attribútumait ismerteti `Variable` :  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A beállítani kívánt környezeti változó neve.|  
|érték|sztring|Választható. A környezeti változóhoz beállított érték Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|  

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue  
Az `RoleInstanceValue` elem azt az xPath-értéket adja meg, amelyből be kell olvasni a változó értékét.

Az alábbi táblázat az elem attribútumait ismerteti `RoleInstanceValue` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|XPath|sztring|Választható. A példány telepítési beállításainak helyének elérési útja. További információ: [konfigurációs változók az XPath](../cloud-services/cloud-services-role-config-xpath.md)-ban.<br /><br /> Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|  

##  <a name="entrypoint"></a><a name="EntryPoint"></a> BelépésiPont  
Az `EntryPoint` elem a szerepkör belépési pontját adja meg. Ez az elem az elemek szülője `NetFxEntryPoint` . Ezek az elemek lehetővé teszik az alapértelmezett WaWorkerHost.exetól eltérő alkalmazások megadását szerepkör-belépési pontként.

Az `EntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint  
Az `NetFxEntryPoint` elem meghatározza a szerepkörhöz futtatandó programot.

> [!NOTE]
>  Az `NetFxEntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `NetFxEntryPoint` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|assemblyName|sztring|Kötelező. A belépési pontot tartalmazó szerelvény elérési útja és fájlneve. Az elérési út a **\\ %ROLEROOT%\Approot** mappához képest relatív (ne határozza meg a **\\ %ROLEROOT%\Approot** a alkalmazásban `commandLine` , feltételezve). **% ROLEROOT%** az Azure által fenntartott környezeti változó, amely a szerepkör gyökérkönyvtárának helyét jelöli. A **\\ %ROLEROOT%\Approot** mappa a szerepkörhöz tartozó alkalmazás mappáját jelöli.<br /><br /> ÜZEMELTETHETŐ WEBMAG-szerepkörök esetén az elérési út mindig a **\\ %ROLEROOT%\Approot\bin** mappához viszonyított.<br /><br /> Teljes IIS-és IIS Express webes szerepkörök esetén, ha a szerelvény nem található a **\\ %ROLEROOT%\Approot** mappához képest, a rendszer a **\\ %ROLEROOT%\Approot\bin** keresi.<br /><br /> Ez a teljes IIS-re vonatkozó biztonsági mentés nem ajánlott eljárás, és a későbbi verziókban valószínűleg el lesz távolítva.|  
|targetFrameworkVersion|sztring|Kötelező. A .NET-keretrendszer azon verziója, amelyen a szerelvény létrejött. Például: `targetFrameworkVersion="v4.0"`.|  

##  <a name="sites"></a><a name="Sites"></a> Helyek  
Az `Sites` elem a webes szerepkörben üzemeltetett webhelyek és webalkalmazások gyűjteményét ismerteti. Ez az elem az elem szülője `Site` . Ha nem ad meg egy `Sites` elemet, a webes szerepkör örökölt webes szerepkörként fut, és csak egy webhely fut a webes szerepkörben. Ez az elem nem kötelező, és egy szerepkör csak egy hellyel rendelkezhet.

Az `Sites` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="site"></a><a name="Site"></a> Hely  
Az `Site` elem a webes szerepkör részét képező webhelyet vagy webalkalmazást adja meg.

Az `Site` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `Site` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A webhely vagy alkalmazás neve.|  
|physicalDirectory|sztring|A hely gyökeréhez tartozó tartalom könyvtárának helye. A hely abszolút elérési úttal vagy a csdef helyhez viszonyítva is megadható.|  

##  <a name="virtualapplication"></a><a name="VirtualApplication"></a> VirtualApplication  
Az `VirtualApplication` elem a Internet Information Services (IIS) 7 alkalmazásban definiál egy alkalmazást, amely tartalmat biztosít vagy szolgáltatásokat biztosít protokollokon, például HTTP-n keresztül. Amikor létrehoz egy alkalmazást az IIS 7-es verziójában, az alkalmazás elérési útja a hely URL-címévé válik.

Az `VirtualApplication` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `VirtualApplication` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A virtuális alkalmazás azonosítására szolgáló nevet adja meg.|  
|physicalDirectory|sztring|Kötelező. Megadja a virtuális alkalmazást tartalmazó fejlesztői gép elérési útját. A Compute Emulator az IIS úgy van konfigurálva, hogy tartalmat kérjen le erről a helyről. Az Azure-ban való üzembe helyezéskor a fizikai könyvtár tartalma a szolgáltatás többi részével együtt van csomagolva. A szervizcsomag Azure-beli üzembe helyezése után az IIS a kicsomagolt tartalom helyével van konfigurálva.|  

##  <a name="virtualdirectory"></a><a name="VirtualDirectory"></a> Virtuáliskönyvtár  
Az `VirtualDirectory` elem megadja az IIS-ben megadott könyvtárnév (más néven elérési út) nevét, amely a helyi vagy távoli kiszolgálón található fizikai könyvtárra van leképezve.

Az `VirtualDirectory` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `VirtualDirectory` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A virtuális könyvtár azonosítására szolgáló nevet adja meg.|  
|érték|physicalDirectory|Kötelező. Megadja a webhelyet vagy a virtuális könyvtár tartalmát tartalmazó fejlesztői számítógép elérési útját. A Compute Emulator az IIS úgy van konfigurálva, hogy tartalmat kérjen le erről a helyről. Az Azure-ban való üzembe helyezéskor a fizikai könyvtár tartalma a szolgáltatás többi részével együtt van csomagolva. A szervizcsomag Azure-beli üzembe helyezése után az IIS a kicsomagolt tartalom helyével van konfigurálva.|  

##  <a name="bindings"></a><a name="Bindings"></a> Kötések  
Az `Bindings` elem egy webhely kötéseinek gyűjteményét írja le. Ez a elem szülő eleme `Binding` . Az elemnek minden elemhez kötelezőnek kell lennie `Site` . A végpontok konfigurálásával kapcsolatos további információkért lásd: [a szerepkör-példányok kommunikációjának engedélyezése](../cloud-services/cloud-services-enable-communication-role-instances.md).

Az `Bindings` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="binding"></a><a name="Binding"></a> Kötés  
Az `Binding` elem határozza meg a webhelyekkel vagy webalkalmazásokkal való kommunikációra irányuló kérésekhez szükséges konfigurációs adatokat.

Az `Binding` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. Megadja a kötés azonosítására szolgáló nevet.|  
|Végpontneve|sztring|Kötelező. A végpont nevét adja meg a kötéshez.|  
|állomásfejléccel|sztring|Választható. Megadja azt az állomásnevet, amely lehetővé teszi több, különböző állomásnévvel rendelkező hely üzemeltetését egyetlen IP-cím/portszám kombinációban.|  

##  <a name="startup"></a><a name="Startup"></a> Indítási  
Az `Startup` elem a szerepkör indításakor futtatott feladatok gyűjteményét írja le. Ez az elem lehet az elem szülője `Variable` . További információ a szerepkör-indítási feladatok használatáról: [indítási feladatok konfigurálása](../cloud-services/cloud-services-startup-tasks.md). Ez az elem nem kötelező, és a szerepkörnek csak egy indítási blokkja lehet.

Az alábbi táblázat az elem attribútumát ismerteti `Startup` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|prioritású|int|Csak belső használatra.|  

##  <a name="task"></a><a name="Task"></a> Feladat  
Az `Task` elem a szerepkör indításakor megjelenő indítási feladatot határozza meg. Az indítási feladatok olyan feladatok elvégzésére használhatók, amelyek előkészítik a szerepkört az ilyen telepítési szoftver-összetevők futtatására vagy más alkalmazások futtatására. A feladatok az Element blokkban megjelenő sorrendben futnak `Startup` .

Az `Task` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `Task` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|commandLine|sztring|Kötelező. Egy parancsfájl, például egy CMD-fájl, amely a futtatandó parancsokat tartalmazza. Az indítási parancsot és a Batch-fájlokat ANSI formátumban kell menteni. A byte-Order jelölőt a fájl elején beállított fájlformátumok nem fognak megfelelően feldolgozni.|  
|executionContext|sztring|Meghatározza azt a környezetet, amelyben a parancsfájl fut.<br /><br /> -   `limited` [Alapértelmezett] – futtassa ugyanazokkal a jogosultságokkal, mint a folyamatot üzemeltető szerepkör.<br />-   `elevated` – Futtatás rendszergazdai jogosultságokkal.|  
|taskType|sztring|Meghatározza a parancs végrehajtási viselkedését.<br /><br /> -   `simple` [Alapértelmezett] – a rendszer megvárja, amíg a feladat kilép, mielőtt más feladatok elindulnak.<br />-   `background` – A rendszer nem várja meg a feladat kilépését.<br />-   `foreground` – A háttérhez hasonlóan, a szerepkör nem indul újra, amíg az összes előtér-feladat ki nem fejeződik.|  

##  <a name="contents"></a><a name="Contents"></a> Tartalmát  
Az `Contents` elem a webes szerepkörök tartalmának gyűjteményét írja le. Ez az elem az elem szülője `Content` .

Az `Contents` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="content"></a><a name="Content"></a> Tartalom  
Az `Content` elem határozza meg az Azure-beli virtuális gépre másolandó tartalom forrásának helyét, valamint a cél elérési útját, ahová a rendszer átmásolja.

Az `Content` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `Content` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|destination|sztring|Kötelező. Annak az Azure-beli virtuális gépnek a helye, ahová a tartalom kerül. Ez a hely a **%ROLEROOT%\Approot** mappához képest relatív.|  

Ez az elem a elem szülő eleme `SourceDirectory` .

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory  
Az `SourceDirectory` elem határozza meg azt a helyi könyvtárat, amelyből a tartalmat másolni kívánja. Ezzel az elemmel adhatja meg az Azure-beli virtuális gépre másolandó helyi tartalmat.

Az `SourceDirectory` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az elem attribútumait ismerteti `SourceDirectory` .

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|path|sztring|Kötelező. Egy helyi könyvtár relatív vagy abszolút elérési útja, amelynek tartalmát a rendszer az Azure-beli virtuális gépre másolja. A környezeti változók kiterjesztése a könyvtár elérési útjában támogatott.|  
  
## <a name="see-also"></a>Lásd még
A [Cloud Service (bővített támogatás) definíciós sémája](schema-csdef-file.md).




