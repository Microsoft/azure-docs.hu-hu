---
title: Biztonság és hitelesítés – Azure Event Grid IoT Edge | Microsoft Docs
description: Biztonság és hitelesítés a IoT Edge Event Gridban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171380"
---
# <a name="security-and-authentication"></a>Biztonság és hitelesítés

A biztonság és a hitelesítés egy speciális koncepció, amely először a Event Grid alapismeretekkel való ismeretét igényli. Kezdje [itt](concepts.md) , ha új IoT Edge Event Grid. Event Grid modul a IoT Edge meglévő biztonsági infrastruktúrára épül. A részletekért és a beállításért tekintse meg [ezt a dokumentációt](../../iot-edge/security.md) .

A következő szakaszok részletesen ismertetik a beállítások védelmét és hitelesítését:

* TLS-konfiguráció
* Bejövő ügyfél-hitelesítés
* Kimenő kiszolgáló hitelesítése
* Kimenő ügyfél-hitelesítés

>[!IMPORTANT]
>Event Grid modul biztonsági és hitelesítési felhasználása a IoT Edge elérhető meglévő infrastruktúra. Feltételezi, hogy IoT Edge alrendszere biztonságos.

>[!IMPORTANT]
>A Event Grid konfiguráció **alapértelmezés szerint biztonságos**. A következő alszakaszok ismertetik a hitelesítés szempontjainak felülbírálásához használható összes lehetőséget és lehetséges értéket. A módosítások elvégzése előtt Ismerje meg a hatását. A módosítások életbe léptetéséhez újra kell telepíteni a Event Grid modult.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-konfiguráció (a. k. a kiszolgáló hitelesítése)

Event Grid modul HTTP-és HTTPS-végpontokat is üzemeltet. Minden IoT Edge modulhoz hozzá van rendelve egy kiszolgálói tanúsítvány a IoT Edge biztonsági démonával. A végpont védelméhez a kiszolgálói tanúsítványt használjuk. A lejáratkor a modul automatikusan frissíti a IoT Edge biztonsági démonból származó új tanúsítvánnyal.

Alapértelmezés szerint csak HTTPS-kommunikáció engedélyezett. Ezt a viselkedést  **inbound__serverAuth__tlsPolicy** konfiguráción keresztül bírálhatja felül. A következő táblázat a tulajdonság lehetséges értékeit rögzíti.

| Lehetséges érték(ek) | Description |
| ---------------- | ------------ |
| Szigorú | Default (Alapértelmezett): Csak HTTPS engedélyezése
| Engedélyezve | A HTTP és a HTTPS engedélyezése
| Disabled (Letiltva) | Csak a HTTP engedélyezése

## <a name="inbound-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfelek felügyeleti és/vagy futásidejű műveleteket végző entitások. Az ügyfelek lehetnek más IoT Edge modulok, nem IoT alkalmazások.

Event Grid modul két típusú ügyfél-hitelesítést támogat:

* Közös hozzáférésű aláírás (SAS) kulcs alapú
* tanúsítvány alapú

Alapértelmezés szerint a Event Grid modul úgy van konfigurálva, hogy csak a tanúsítványalapú hitelesítést fogadja el. Indításkor Event Grid modul beolvassa a "TrustBundle" elemet IoT Edge biztonsági démonból, és a használatával érvényesíti az összes ügyféltanúsítványt. Az ehhez a lánchoz nem feloldható ügyféltanúsítványok a következővel lesznek elutasítva: `UnAuthorized` .

### <a name="certificate-based-client-authentication"></a>Tanúsítványalapú ügyfél-hitelesítés

A tanúsítvány alapú hitelesítés alapértelmezés szerint be van kapcsolva. Dönthet úgy, hogy letiltja a tanúsítványalapú hitelesítést a ( **inbound__clientAuth__clientCert__enabled**) tulajdonság használatával. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték(ek) | Description |
| ----------------  | ------------ |
| true | Default (Alapértelmezett): Az összes kérelem beírása a Event Grid modulba az ügyféltanúsítvány bemutatása érdekében. Emellett konfigurálnia kell **inbound__clientAuth__clientCert__source**.
| hamis | Ne kényszerítse az ügyfelet a tanúsítvány bemutatására.

Az alábbi táblázat a lehetséges érték (eke) t rögzíti **inbound__clientAuth__clientCert__source**

| Lehetséges érték(ek) | Description |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): A IoT Edge Trustbundle használatával ellenőrzi az összes ügyféltanúsítványt.

Ha egy ügyfél önaláírt, alapértelmezés szerint a Event Grid modul elveti az ilyen kérelmeket. Dönthet úgy, hogy engedélyezi az önaláírt Ügyféltanúsítványok használatát **inbound__clientAuth__clientCert__allowUnknownCA** tulajdonság használatával. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték(ek) | Description |
| ----------------  | ------------|
| true | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres megjelenítését.
| hamis | Sikertelen kérések, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetekben érdemes lehet **Hamis értéket** beállítani **inbound__clientAuth__clientCert__allowUnknownCA** .

### <a name="sas-key-based-client-authentication"></a>SAS-kulcs alapú ügyfél-hitelesítés

A tanúsítványalapú hitelesítésen kívül a Event Grid modul is képes az SAS-kulcson alapuló hitelesítésre. Az SAS-kulcs olyan, mint a Event Grid modulban konfigurált titok, amelyet az összes bejövő hívás ellenőrzéséhez használnia kell. Az ügyfeleknek meg kell adniuk a titkot az "AEG-sas-Key" HTTP-fejlécben. Ha nem egyezik, a rendszer elutasítja a kérelmet `UnAuthorized` .

Az SAS-kulcs alapú hitelesítés vezérlésének beállítása **inbound__clientAuth__sasKeys__enabled**.

| Lehetséges érték(ek) | Description  |
| ----------------  | ------------ |
| true | Lehetővé teszi az SAS-kulcson alapuló hitelesítés használatát. **Inbound__clientAuth__sasKeys__key1** vagy **inbound__clientAuth__sasKeys__key2** szükséges
| hamis | Default (Alapértelmezett): Az SAS-kulcs alapú hitelesítés le van tiltva.

 a **inbound__clientAuth__sasKeys__key1** és a **inbound__clientAuth__sasKeys__key2** olyan kulcsok, amelyekkel a Event Grid modult konfigurálhatja a bejövő kérelmekkel való kereséshez. Legalább egy kulcsot be kell állítani. A kérést kérő ügyfélnek be kell mutatnia a kulcsot az "**AEG-sas-Key**" kérelem fejlécének részeként. Ha mindkét kulcs konfigurálva van, az ügyfél a kulcsok egyikét láthatja.

> [!NOTE]
>Mindkét hitelesítési módszert konfigurálhatja. Ebben az esetben az SAS-kulcs először van bejelölve, és ha ez nem sikerül, a rendszer a tanúsítványalapú hitelesítést hajtja végre. A sikeres kéréshez csak az egyik hitelesítési módszernek kell sikeresnek lennie.

## <a name="outbound-client-authentication"></a>Kimenő ügyfél-hitelesítés

A kimenő környezetben lévő ügyfél Event Grid modulra hivatkozik. Az elvégzendő művelet az előfizetőknek nyújt eseményeket. A feliratkozási modulok a kiszolgálónak tekintendők.

Minden IoT Edge modulhoz hozzá van rendelve egy azonosító tanúsítvány a IoT Edge biztonsági démonával. A kimenő hívásokhoz a személyazonossági tanúsítványt használjuk. A lejáratkor a modul automatikusan frissíti a IoT Edge biztonsági démonból származó új tanúsítvánnyal.

A kimenő ügyfél-hitelesítés vezérlésére szolgáló konfiguráció **outbound__clientAuth__clientCert__enabled**.

| Lehetséges érték(ek) | Description |
| ----------------  | ------------ |
| true | Default (Alapértelmezett): Az Event Grid modul összes kimenő kérelmét egy tanúsítvány bemutatására kéri. A **outbound__clientAuth__clientCert__source** konfigurálására van szükség.
| hamis | Nincs szükség Event Grid modulra a tanúsítvány bemutatásához.

A tanúsítvány forrását vezérlő konfiguráció **outbound__clientAuth__clientCert__source**.

| Lehetséges érték(ek) | Description |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): A modul IoT Edge biztonsági démon által konfigurált azonosító tanúsítványát használja.

### <a name="outbound-server-authentication"></a>Kimenő kiszolgáló hitelesítése

Egy Event Grid előfizető egyik típusa a "webhook". Alapértelmezés szerint a rendszer csak a HTTPS-végpontokat fogadja el az ilyen előfizetők számára.

A webhook célhely-házirend **outbound__webhook__httpsOnly** vezérlésére szolgáló konfiguráció.

| Lehetséges érték(ek) | Description |
| ----------------  | ------------ |
| true | Default (Alapértelmezett): Csak HTTPS-végponttal rendelkező előfizetőket engedélyez.
| hamis | Engedélyezi az előfizetők számára a HTTP-vagy HTTPS-végpontot.

Alapértelmezés szerint Event Grid modul érvényesíti az előfizető kiszolgálói tanúsítványát. Az érvényesítést kihagyhatja **outbound__webhook__skipServerCertValidation** felülbírálásával. Lehetséges értékek:

| Lehetséges érték(ek) | Description |
| ----------------  | ------------ |
| true | Ne érvényesítse az előfizető kiszolgálói tanúsítványát.
| hamis | Default (Alapértelmezett): Érvényesítse az előfizető kiszolgálói tanúsítványát.

Ha az előfizető tanúsítványa önaláírt, akkor alapértelmezés szerint Event Grid modul elveti az ilyen előfizetőket. Az önaláírt tanúsítványok engedélyezéséhez felülbírálhatja **outbound__webhook__allowUnknownCA**. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték(ek) | Description |
| ----------------  | ------------ |
| true | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres megjelenítését.
| hamis | Sikertelen kérések, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetben a **outbound__webhook__allowUnknownCA** **hamis** értékre kell állítani.

> [!NOTE]
>IoT Edge környezet önaláírt tanúsítványokat hoz létre. Javasoljuk, hogy a jóváhagyott hitelesítésszolgáltatók által kiadott tanúsítványokat előállítson a termelési számítási feladatokhoz, és állítsa a **allowUnknownCA** tulajdonságot mind a bejövő, mind a **kimenő értékre.**

## <a name="summary"></a>Összefoglalás

Az Event Grid-modul **alapértelmezés szerint biztonságos**. Javasoljuk, hogy ezeket az alapértelmezéseket az éles környezetekben is megőrizze.

A konfigurálás során a következő irányadó elveket kell használni:

* Csak HTTPS-kérelmek engedélyezése a modulnak.
* Csak tanúsítvány alapú ügyfél-hitelesítés engedélyezése. Csak a jól ismert hitelesítésszolgáltatók által kiadott tanúsítványokat engedélyezze. Önaláírt tanúsítványok letiltása.
* SASKey-alapú ügyfél-hitelesítés letiltása.
* Mindig jelen van Event Grid modul azonosító tanúsítványa a kimenő hívásokon.
* Csak HTTPS-előfizetők engedélyezése webhook-célhelyek számára.
* Mindig ellenőrizze az előfizető kiszolgálói tanúsítványát a webhook-célhelyek típusaihoz. Csak a jól ismert hitelesítésszolgáltatók által kiadott tanúsítványok engedélyezése. Önaláírt tanúsítványok letiltása.

Alapértelmezés szerint a Event Grid modul a következő konfigurációval van telepítve:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
