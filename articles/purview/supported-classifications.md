---
title: Támogatott besorolások listája
description: Ezen az oldalon az Azure hatáskörébe tartozó támogatott rendszerbesorolások szerepelnek.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 02/05/2021
ms.openlocfilehash: 37df9a276339b80a81e6ac5d5db14872de9edff4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065854"
---
# <a name="supported-classifications-in-azure-purview"></a>Támogatott besorolások az Azure hatáskörébe

Ez a cikk az Azure hatáskörébe (előzetes verzió) támogatott és definiált rendszerbesorolásokat sorolja fel.


- **Eltérő egyezési küszöbérték**: az oszlopokban a képolvasó által az adatmintázat futtatása előtt megtalált különböző adatértékek teljes száma. A rendszer besorolási szabályai szerint az egyes oszlopokban legalább 8 különböző értéknek kell lennie, hogy azok besorolásra vonatkozzanak. A rendszernek ezt az értéket kell megadnia ahhoz, hogy az oszlop elegendő adatmennyiséget tartalmazzon a képolvasó számára a pontos besoroláshoz. Például egy olyan oszlop, amely az 1 értéket tartalmazó több sort tartalmaz, nem lesz besorolva. Azokat az oszlopokat, amelyek egy értéket tartalmazó sort tartalmaznak, és a sorok többi része null értékű, nem lesz besorolva. Ha több mintázatot ad meg, ez az érték mindenre vonatkozik.

- **Minimális egyezési küszöb**: az adatértékek minimális százalékos aránya egy oszlopban, amelyet a lapolvasónak kell megkeresnie az alkalmazandó besoroláshoz. A rendszer besorolási értéke 60%.


## <a name="defined-system-classifications"></a>Definiált rendszerbesorolások

Az Azure-beli hatáskörébe a [regex](https://wikipedia.org/wiki/Regular_expression) és a [Bloom Filter](https://wikipedia.org/wiki/Bloom_filter)alapján osztályozhatja az adathalmazokat. Az alábbi listában az Azure-beli meghatározott rendszerbesorolások formátuma, mintája és kulcsszavai láthatók.

A besorolási nevek mindegyikét a MICROSOFT előre rögzíti.

## <a name="bloom-filter-classifications"></a>Bloom Filter besorolások

## <a name="city-country-and-place"></a>Város, ország és hely

A város, az ország és a hely szűrőit az adat előkészítéséhez rendelkezésre álló legjobb adatkészletek felhasználásával készítettük elő.

## <a name="person"></a>Személy

A person Bloom szűrő az alábbi két adatkészletből lett előkészítve.

- [2010 US népszámlálási adatok az utolsó nevekhez (162-K bejegyzések)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Népszerű Baby Names (SSN), az összes év 1880-2019 (98-K bejegyzések)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx besorolások

## <a name="aba-routing"></a>ABA-Útválasztás

### <a name="format"></a>Formátum

Kilenc számjegy, amely formázatlan vagy formázatlan mintában lehet

### <a name="pattern"></a>Mintázat

Formázott

- négy számjegy a 0, 1, 2, 3, 6, 7 vagy 8 karaktertől kezdődően
- kötőjel
- négy számjegy
- kötőjel
- egy számjegy

Formázatlan: kilenc egymást követő számjegy (0, 1, 2, 3, 6, 7 vagy 8)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Argentin nemzeti identitás (DNI) száma

### <a name="format"></a>Formátum

Nyolc számjegy vagy anélkül

### <a name="pattern"></a>Mintázat

Nyolc számjegy:

- két számjegy
- egy választható időszak
- három számjegy
- egy választható időszak
- három számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Ausztráliai bankszámla száma

### <a name="format"></a>Formátum

Hat – 10 számjegy a bank állapoti ág számával vagy anélkül

### <a name="pattern"></a>Mintázat

A fiók száma 6 – 10 számjegyből áll.

Ausztrál Bank állapoti ág száma:

- három számjegy
- kötőjel
- három számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Ausztráliai jogosítvány száma

### <a name="format"></a>Formátum
Kilenc betű és számjegy

### <a name="pattern"></a>Mintázat
kilenc betű és számjegy:

- két számjegy vagy betű (kis-és nagybetűk megkülönböztetése nélkül)
- két számjegy
- öt számjegy vagy betű (kis-és nagybetűk megkülönböztetése nélkül)

OR

- egy-két opcionális betű (nem megkülönbözteti a kis-és nagybetűket)
- négy – kilenc számjegy

OR

- kilenc számjegy vagy betű (kis-és nagybetűk megkülönböztetése nélkül)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Ausztráliai Medicare-szám

### <a name="format"></a>Formátum

10-11 számjegy

### <a name="pattern"></a>Mintázat

10-11 számjegy:

- az első számjegy a 2-6 tartományba esik
- a kilencedik számjegy egy ellenőrzési számjegy
- a tizedik számjegy a probléma számjegye
- a tizenegyedik számjegy (nem kötelező) az adott szám

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Ausztráliai útlevél száma

### <a name="format"></a>Formátum

Egy levél, amelyet hét számjegy követ

### <a name="pattern"></a>Mintázat

Egy betű (kis-és nagybetűk megkülönböztetése nélkül), majd hét számjegyből áll

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_passport"></a>Kulcsszó \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Kulcsszó \_ Ausztrália \_ Passport- \_ szám

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Ausztráliai adó-fájl száma

### <a name="format"></a>Formátum

Nyolc – kilenc számjegy

### <a name="pattern"></a>Mintázat

a következő nyolc – kilenc számjegy általában szóközökkel jelenik meg:

- három számjegy
- egy választható terület
- három számjegy
- egy választható terület
- kettő – három számjegy, ahol az utolsó számjegy egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_australia_tax_file_number"></a>Kulcsszó \_ ausztráliai \_ Adószám \_ \_

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Belgium nemzeti száma

### <a name="format"></a>Formátum

11 számjegy plusz választható határolójelek

### <a name="pattern"></a>Mintázat

11 számjegy és elválasztó karakter:

- hat számjegy és két opcionális időszak az éé formátumban. HH. DD a születési dátumhoz
- A ponttól, kötőjeltől, szóköztől választható elválasztó karakter
- három egymás utáni számjegy (nem igaz a hímek számára, még a nőstények esetében is)
- A ponttól, kötőjeltől, szóköztől választható elválasztó karakter
- két ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_belgium_national_number"></a>Kulcsszó \_ belga \_ nemzeti \_ szám

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brazília CPF száma

### <a name="format"></a>Formátum

11 számjegy, amely tartalmaz egy ellenőrzési számot, és formázható vagy formázható

### <a name="pattern"></a>Mintázat

Formázott

- három számjegy
- egy időszak
- három számjegy
- egy időszak
- három számjegy
- kötőjel
- két számjegy, amelyek ellenőrzési számjegyek

Formázatlan:

- 11 számjegy, ahol az utolsó két számjegy a számok jelölése

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_brazil_cpf"></a>Kulcsszó \_ Brazília \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brazília jogi személy száma (CNPJ)

### <a name="format"></a>Formátum

14 számjegy, amely tartalmazza a regisztrációs számot, az ág számát és az ellenőrzési számjegyeket, valamint a határolójeleket

### <a name="pattern"></a>Mintázat

14 számjegy, plusz határolójelek:

- két számjegy
- egy időszak
- három számjegy
- egy időszak
- három számjegy (az első nyolc számjegy a regisztrációs szám)
- perjel
- négyjegyű ág száma
- kötőjel
- két számjegy, amelyek ellenőrzési számjegyek

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_brazil_cnpj"></a>Kulcsszó \_ Brazília \_ CNPJ

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Brazília nemzeti azonosító kártya (RG)

### <a name="format"></a>Formátum

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Mintázat

:::no-loc text="Registro Geral (old format):":::

- két számjegy
- egy időszak
- három számjegy
- egy időszak
- három számjegy
- kötőjel
- egy számjegy, amely egy ellenőrzési számjegy

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 számjegy
- kötőjel
- egy számjegy, amely egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_brazil_rg"></a>Kulcsszó \_ Brazília \_ RG

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Kanadai bankszámla száma

### <a name="format"></a>Formátum

7 vagy 12 számjegy

### <a name="pattern"></a>Mintázat

A kanadai bankszámla száma 7 vagy 12 számjegy.

A kanadai bankszámla átviteli száma:

- öt számjegy
- kötőjel
- három számjegy vagy
- nulla &quot; 0&quot;
- nyolc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_canada_bank_account_number"></a>Kulcsszó \_ kanadai \_ bankszámla \_ \_ száma

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Kanadai jogosítvány száma

### <a name="format"></a>Formátum

Tartomány szerint változik

### <a name="pattern"></a>Mintázat

Az Alberta, a British Columbia, a Manitoba, a New Brunswick, a Fundland/Labrador, a Nova Scotia, a Ontario, a Prince Edward-sziget, a Québec és a Saskatchewan különböző mintái

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_province_name_drivers_license_name"></a>Kulcsszó \_ [tartomány \_ neve] \_ illesztőprogramok \_ licencének \_ neve

- A tartomány rövidítése, például: AB
- A tartomány neve, például Alberta

#### <a name="keyword_canada_drivers_license"></a>Kulcsszó \_ kanadai \_ illesztőprogram- \_ licenc

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Kanadai állapotfigyelő szolgáltatás száma

### <a name="format"></a>Formátum

10 számjegy

### <a name="pattern"></a>Mintázat

10 számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_canada_health_service_number"></a>Kulcsszó \_ Kanada \_ állapotfigyelő \_ szolgáltatásának \_ száma

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Kanadai útlevél száma

### <a name="format"></a>Formátum

két nagybetűt, hat számjegyet követve

### <a name="pattern"></a>Mintázat

két nagybetűt, hat számjegyet követve

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_canada_passport_number"></a>Kulcsszó \_ kanadai \_ Passport- \_ szám

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Kulcsszó \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Kanadai személyi állapot azonosítójának száma (PHIN)

### <a name="format"></a>Formátum

kilenc számjegy

### <a name="pattern"></a>Mintázat

kilenc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_canada_phin"></a>Kulcsszó \_ Kanada \_ phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Kulcsszó \_ kanadai \_ tartományok

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Kanadai társadalombiztosítási szám

### <a name="format"></a>Formátum

kilenc számjegy választható kötőjelekkel vagy szóközökkel

### <a name="pattern"></a>Mintázat

Formázott

- három számjegy
- kötőjel vagy szóköz
- három számjegy
- kötőjel vagy szóköz
- három számjegy

Formázatlan: kilenc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_sin"></a>Kulcsszó \_ Sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Kulcsszó- \_ bűn \_ együttműködési

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Chile személyazonosító igazolványának száma

### <a name="format"></a>Formátum

hét – nyolc számjegy, valamint elválasztó karakter vagy betű

### <a name="pattern"></a>Mintázat

hét – nyolc számjegy és határolójelek:

- egy-két számjegy
- egy választható időszak
- három számjegy
- egy választható időszak
- három számjegy
- kötőjel
- egy számjegy vagy betű (kis-és nagybetűk megkülönböztetése nélkül), amely egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_chile_id_card"></a>Kulcsszó \_ Chile \_ azonosító \_ kártya

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Kínai – rezidens személyazonosító kártya (PRC) száma

### <a name="format"></a>Formátum

18 számjegy

### <a name="pattern"></a>Mintázat

18 számjegy:

- hat számjegy, amely egy címterület
- nyolc számjegy a ÉÉÉÉHHNN formában, amely a születési dátum
- három számjegy, amely egy Rendelési kód
- egy számjegy, amely egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_china_resident_id"></a>Kulcsszóválasztó \_ kínai \_ rezidens \_ azonosító

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Hitelkártya száma

### <a name="format"></a>Formátum

14 – 16 számjegy, amely formázható vagy formázható (dddddddddddddddd), és amelyeknek át kell adni a Luhn tesztet.

### <a name="pattern"></a>Mintázat

Összetett és robusztus minta, amely az egész világon elérhető főbb márkák kártyáit észleli, beleértve a Visa, MasterCard, Discover Card, JCB, American Express, Gift Cards és Diner kártyákat.

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_cc_verification"></a>Kulcsszó \_ CC- \_ ellenőrzés

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Kulcsszó \_ CC- \_ neve

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Horvátország-illesztőprogram licencének száma

Ez a bizalmas adattípus-entitás csak az EU-illesztőprogram licencelési számának bizalmas adattípusában érhető el.

### <a name="format"></a>Formátum

nyolc számjegy szóközök és határolójelek nélkül

### <a name="pattern"></a>Mintázat

nyolc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_eu_drivers_license_number"></a>Kulcsszavak \_ EU-jogosítvány \_ \_ \_ száma

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Kulcsszavak \_ Horvátország \_ EU- \_ jogosítvány \_ \_ száma

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Horvátország személyazonosító igazolványának száma

Ez a bizalmas adattípusú entitás szerepel az EU nemzeti azonosító számának bizalmas információ típusa mezőben, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

kilenc számjegy

### <a name="pattern"></a>Mintázat

kilenc egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_croatia_id_card"></a>Kulcsszó \_ Horvátország \_ azonosító \_ kártya

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Horvátország személyes azonosítójának (OIB) száma

### <a name="format"></a>Formátum

11 számjegy

### <a name="pattern"></a>Mintázat

11 számjegy:

- 10 számjegy
- az utolsó számjegy egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_croatia_oib_number"></a>Kulcsszó \_ Horvátország \_ OIB \_ száma

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Dániai személyi azonosító száma

### <a name="format"></a>Formátum

kötőjelet tartalmazó 10 számjegy

### <a name="pattern"></a>Mintázat

10 számjegy:

- hat számjegy a NNHHÉÉ formátumban, amely a születési dátum
- kötőjel
- négy számjegy, ahol az utolsó számjegy egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_denmark_id"></a>Kulcsszó \_ dániai \_ azonosító

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>EU Debit kártya száma

### <a name="format"></a>Formátum

16 számjegy

### <a name="pattern"></a>Mintázat

Összetett és robusztus minta

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_eu_debit_card"></a>Kulcsszóval rendelkező EU-beli \_ \_ bankkártya \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Kulcsszó \_ kártya \_ használati feltételei \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Kulcsszó \_ kártya \_ biztonsági \_ feltételei \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Kulcsszó \_ kártya \_ lejárati \_ feltételei \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Az EU-illesztőprogram licencének száma

Ezek azok az entitások, amelyek az EU-illesztőprogram licencelési számának bizalmas információ típusát használják.

- Ausztria
- Belgium
- Bulgária
- Horvátország
- Ciprus
- Cseh
- Dánia
- Észtország
- Finnország
- Franciaország
- Németország
- Görögország
- Magyarország
- Írország
- Olaszország
- Lettország
- Litvánia
- Luxemburg
- Málta
- Hollandia
- Lengyelország
- Portugália
- Románia
- Szlovákia
- Szlovénia
- Spanyolország
- Svédország
- brit

## <a name="eu-national-identification-number"></a>EU nemzeti azonosító száma

Ezek az EU-beli nemzeti azonosítók számának bizalmas információ típusú entitásai.

- Ausztria
- Belgium
- Bulgária
- Horvátország
- Ciprus
- Cseh
- Dánia
- Észtország
- Finnország
- Franciaország
- Németország
- Görögország
- Magyarország
- Írország
- Olaszország
- Lettország
- Litvánia
- Luxemburg
- Málta
- Hollandia
- Lengyelország
- Portugália
- Románia
- Szlovákia
- Szlovénia
- Spanyolország
- brit

## <a name="eu-passport-number"></a>EU Passport-szám

Ezek az EU-beli Passport-szám bizalmas információit typeThese az EU Passport Number csomag entitásai.

- Ausztria
- Belgium
- Bulgária
- Horvátország
- Ciprus
- Cseh
- Dánia
- Észtország
- Finnország
- Franciaország
- Németország
- Görögország
- Magyarország
- Írország
- Olaszország
- Lettország
- Litvánia
- Luxemburg
- Málta
- Hollandia
- Lengyelország
- Portugália
- Románia
- Szlovákia
- Szlovénia
- Spanyolország
- Svédország
- brit

## <a name="eu-social-security-number-or-equivalent-identification"></a>KÖZÖSSÉGI társadalombiztosítási szám vagy azzal egyenértékű azonosító

Ezek azok az entitások, amelyek az EU társadalombiztosítási számában vagy az azzal egyenértékű azonosítási bizalmas adattípusban találhatók.

- Ausztria
- Belgium
- Horvátország
- Cseh
- Dánia
- Finnország
- Franciaország
- Németország
- Görögország
- Magyarország
- Portugália
- Spanyolország
- Svédország

## <a name="eu-tax-identification-number"></a>EU-adóazonosító szám

Ezek az entitások az EU-beli adóazonosító szám bizalmas adattípusa szerint vannak kitéve.

- Ausztria
- Belgium
- Bulgária
- Horvátország
- Ciprus
- Cseh
- Dánia
- Észtország
- Finnország
- Franciaország
- Németország
- Görögország
- Magyarország
- Írország
- Olaszország
- Lettország
- Litvánia
- Luxemburg
- Málta
- Hollandia
- Lengyelország
- Portugália
- Románia
- Szlovákia
- Szlovénia
- Spanyolország
- Svédország
- brit



## <a name="finland-national-id"></a>Finnország nemzeti azonosítója

### <a name="format"></a>Formátum

hat számjegy plusz egy évszázadot jelző karakter, valamint három számjegy és egy ellenőrzési számjegy

### <a name="pattern"></a>Mintázat

A mintának tartalmaznia kell a következőket:

- hat számjegy a NNHHÉÉ formátumban, amely a születési dátum
- századi jelölő (vagy "-", "+" vagy "a")
- háromjegyű személyes azonosító száma
- egy számjegy vagy betű (kis-és nagybetűk megkülönböztetése), amely egy ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Finnország Passport száma

Ez a bizalmas adattípusú entitás az EU-beli Passport-szám bizalmas információi típusában érhető el, és önálló bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

kilenc betű és számjegy kombinációja

### <a name="pattern"></a>Mintázat

kilenc betű és számjegy kombinációja:

- két betű (kis-és nagybetűk megkülönböztetése nélkül)
- hét számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_eu_passport_number_common"></a>Kulcsszavak \_ – \_ közös EU Passport- \_ szám \_

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Kulcsszó \_ Finnország \_ Passport- \_ szám

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>France-illesztőprogram licencének száma

Ez a bizalmas adattípusú entitás az EU-illesztőprogram licencelési számának bizalmas információ típusa területen érhető el, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

12 számjegy

### <a name="pattern"></a>Mintázat

12 számjegy a kedvezményes hasonló minták, például francia telefonszámok esetében történő érvényesítéssel

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_french_drivers_license"></a>Kulcsszó \_ francia \_ illesztőprogramok \_ licence

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Francia nemzeti azonosító kártya (CNI)

### <a name="format"></a>Formátum

12 számjegy

### <a name="pattern"></a>Mintázat

12 számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_france_eu_national_id_card"></a>Kulcsszavak \_ Franciaország \_ EU \_ nemzeti \_ azonosító \_ kártya

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Franciaországi Passport-szám

Ez a bizalmas adattípusú entitás az EU-beli Passport-szám bizalmas információi típusában érhető el, és önálló bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

kilenc számjegy és betű

### <a name="pattern"></a>Mintázat

kilenc számjegy és betű:

- két számjegy
- két betű (kis-és nagybetűk megkülönböztetése nélkül)
- öt számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_passport"></a>Kulcsszó \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Franciaország társadalombiztosítási száma (INSEE) vagy azzal egyenértékű azonosító

Ezt a bizalmas adattípus-entitást az EU társadalombiztosítási száma és az azzal egyenértékű azonosító megkülönböztető információ típusa tartalmazza, és önálló, bizalmas adattípusú entitásként is elérhető.

### <a name="format"></a>Formátum

15 számjegy

### <a name="pattern"></a>Mintázat

Meg kell egyeznie a két minta egyikével:

- 13 számjegy után egy szóközt, majd két számjegyet, vagy
- 15 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_fr_insee"></a>Kulcsszó \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Németország-illesztőprogram licencének száma

Ez a bizalmas adattípusú entitás szerepel az EU-illesztőprogram licencelési számának bizalmas információ típusa területen, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

11 számjegy és betű kombinációja

### <a name="pattern"></a>Mintázat

11 számjegy és betű (nem megkülönbözteti a kis-és nagybetűket):

- egy számjegy vagy egy levél
- két számjegy
- hat számjegy vagy betű
- egy számjegy
- egy számjegy vagy egy levél

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_german_drivers_license_number"></a>Kulcsszó \_ német \_ illesztőprogramok \_ licencének \_ száma

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Németországi személyazonosító igazolvány száma

### <a name="format"></a>Formátum

2010. november 1. óta: kilenc betű és számjegy

1987. április 1-jétől 2010:10 számjegy

### <a name="pattern"></a>Mintázat

2010. november 1. óta:

- egy betű (kis-és nagybetűk megkülönböztetése nélkül)
- nyolc számjegy

1987. április 1-jétől 2010. október 31-ig:

- 10 számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_germany_id_card"></a>Kulcsszó \_ Németország \_ azonosító \_ kártya

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Németországi Passport-szám

Ezt a bizalmas adattípus-entitást az EU-beli Passport-szám bizalmas információ típusa tartalmazza, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

10 számjegy vagy betű

### <a name="pattern"></a>Mintázat

A mintának tartalmaznia kell a következőket:

- az első karakter egy számjegy vagy egy betű a készletből (C, F, G, H, J, K)
- három számjegy
- öt számjegy vagy betű a készletből (C,-H, J-N, P, R, T, V-Z)
- egy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_german_passport"></a>Kulcsszó \_ német \_ Passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Görögországi nemzeti azonosító kártya

### <a name="format"></a>Formátum

7-8 betű és szám kombinációja, valamint egy kötőjel

### <a name="pattern"></a>Mintázat

Hét betű és szám (régi formátum):

- Egy betű (a görög ábécé bármely betűje)
- Kötőjel
- Hat számjegy

Nyolc betű és szám (új formátum):

- Két betű, amelynek nagybetűs karaktere a görög és a latin ábécében (ABEZHIKMNOPTYX) is előfordul
- Kötőjel
- Hat számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_greece_id_card"></a>Kulcsszó \_ Görögországban \_ azonosító \_ kártya

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Hong Kong Identity Card (HKID) száma

### <a name="format"></a>Formátum

8-9 betű és szám kombinációja, valamint opcionális zárójelek a végső karakter körül

### <a name="pattern"></a>Mintázat

8-9 betű kombinációja:

- 1-2 betű (kis-és nagybetűk megkülönböztetése nélkül)
- Hat számjegy
- Az utolsó karakter (bármely számjegy vagy az A betű), amely az ellenőrzési számjegy, és opcionálisan zárójelek közé van zárva.

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_hong_kong_id_card"></a>Kulcsszó \_ Hong \_ Kong \_ azonosító \_ kártya

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP-cím

### <a name="format"></a>Formátum

#### <a name="ipv4"></a>IPv4

Összetett minta, amely az IPv4-címek formázott (időszakos) és formázatlan (nem időszakos) verzióihoz tartozó fiókokat

#### <a name="ipv6"></a>IPv6

Összetett minta, amely a formázott IPv6-számok (például kettőspontok) fiókjaihoz tartozik.

### <a name="pattern"></a>Mintázat

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_ipaddress"></a>Kulcsszó \_ IP-címe

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Indiai állandó fiók száma (PAN)

### <a name="format"></a>Formátum

10 betű vagy számjegy

### <a name="pattern"></a>Mintázat

10 betű vagy számjegy:

- Három betű (kis-és nagybetűk megkülönböztetése nélkül)
- Betű a C, P, H, F, A, T, B, L, J, G karakterben (nem megkülönbözteti a kis-és nagybetűket)
- Egy betű
- Négy számjegy
- Betű (nem megkülönbözteti A kis-és nagybetűket)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_india_permanent_account_number"></a>A keyword \_ India \_ állandó \_ fiókjának \_ száma

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Indiai egyedi azonosító (Aadhaar) száma

### <a name="format"></a>Formátum

választható szóközöket vagy kötőjeleket tartalmazó 12 számjegy

### <a name="pattern"></a>Mintázat

12 számjegy:

- Egy számjegy, amely nem 0 vagy 1
- Három számjegy
- Választható szóköz vagy kötőjel
- Négy számjegy
- Választható szóköz vagy kötőjel
- Az utolsó számjegy, amely az ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_india_aadhar"></a>Kulcsszó \_ India \_ Icuka

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Indonéz személyazonosító kártya (KTP) száma

### <a name="format"></a>Formátum

nem kötelező időszakokat tartalmazó 16 számjegy

### <a name="pattern"></a>Mintázat

16 számjegy:

- Kétjegyű tartomány kódja
- Egy pont (nem kötelező)
- Kétjegyű Regency vagy városi kód
- Kétjegyű alkörzeti kód
- Egy pont (nem kötelező)
- Hat számjegy a NNHHÉÉ formátumban, amely a születési dátum
- Egy pont (nem kötelező)
- Négy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_indonesia_id_card"></a>Kulcsszó \_ Indonézia \_ azonosító \_ kártya

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Nemzetközi banki fiók száma (IBAN)

### <a name="format"></a>Formátum

Országkód (két betű) plusz ellenőrzési számjegyek (két számjegy) plusz :::no-loc text="bban"::: szám (legfeljebb 30 karakter)

### <a name="pattern"></a>Mintázat

A mintának tartalmaznia kell a következőket:

- Kétbetűs országkód
- Két ellenőrzési számjegy (amelyet egy opcionális szóköz követ)
- 1-7 négy betűből vagy számjegyből álló csoportok (szóközzel elválasztható)
- 1-3 betű vagy számjegy

Az egyes országok formátuma némileg eltérő. Az IBAN bizalmas információ típusa a következő 60 országra terjed ki:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Kulcsszavak

Nincsenek

## <a name="ireland-personal-public-service-pps-number"></a>Írországi személyes nyilvános szolgáltatás (PPS) száma

### <a name="format"></a>Formátum

Régi formátum (2012. december 31-ig):

- hét számjegy után 1-2 betű

Új formátum (1 Jan 2013 és újabb):

- hét számjegy, majd két betű

### <a name="pattern"></a>Mintázat

Régi formátum (2012. december 31-ig):

- hét számjegy
- egy-két betű (kis-és nagybetűk megkülönböztetése nélkül)

Új formátum (1 Jan 2013 és újabb):

- hét számjegy
- betű (kis-és nagybetűk megkülönböztetése nélkül), amely egy ABC-ellenőrzési számjegy
- Egy opcionális betű az A-I vagy &quot; W tartományban&quot;

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_ireland_eu_national_id_card"></a>Kulcsszavak \_ Írország \_ EU \_ nemzeti \_ azonosító \_ kártya

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Izraeli bankszámla száma

### <a name="format"></a>Formátum

13 számjegy

### <a name="pattern"></a>Mintázat

Formázott

- két számjegy
- kötőjel
- három számjegy
- kötőjel
- nyolc számjegy

Formázatlan:

- 13 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_israel_bank_account_number"></a>Kulcsszó \_ Izrael \_ Bank- \_ fiók \_ száma

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Izraeli nemzeti azonosító száma

### <a name="format"></a>Formátum

kilenc számjegy

### <a name="pattern"></a>Mintázat

kilenc egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_israel_national_id"></a>Kulcsszó \_ Izrael \_ nemzeti \_ azonosítója

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Olaszországi jogosítvány száma

Ez a bizalmas adattípusú entitás szerepel az EU-illesztőprogram licencelési számának bizalmas információ típusa területen, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

10 betű és számjegy kombinációja

### <a name="pattern"></a>Mintázat

10 betű és számjegy kombinációja:

- egy betű (kis-és nagybetűk megkülönböztetése nélkül)
- az &quot; a &quot; vagy V betű (a kis-és &quot; &quot; nagybetűk megkülönböztetése nélkül)
- hét számjegy
- egy betű (kis-és nagybetűk megkülönböztetése nélkül)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_italy_drivers_license_number"></a>A Kulcsszóválasztó \_ Olaszország \_ illesztőprogram- \_ licencének \_ száma

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Japán bankszámla száma

### <a name="format"></a>Formátum

hét vagy nyolc számjegy

### <a name="pattern"></a>Mintázat

bankszámla száma:

- hét vagy nyolc számjegy
- bankszámla ág kódja:
- négy számjegy
- szóköz vagy kötőjel (nem kötelező)
- három számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_jp_bank_account"></a>Kulcsszavas \_ JP- \_ bankszámla \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Kulcsszó \_ JP \_ Bank \_ ág \_ kódja

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Japán jogosítvány száma

### <a name="format"></a>Formátum

12 számjegy

### <a name="pattern"></a>Mintázat

12 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_jp_drivers_license_number"></a>Kulcsszó \_ JP \_ illesztőprogramok \_ licenc \_ száma

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japán Passport-szám

### <a name="format"></a>Formátum

két betű, majd hét számjegy

### <a name="pattern"></a>Mintázat

két betű (kis-és nagybetűk megkülönböztetése), majd hét számjegyből áll

#### <a name="keyword_jp_passport"></a>Kulcsszó \_ JP \_ Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japán tartózkodási kártya száma

### <a name="format"></a>Formátum

12 betű és számjegy

### <a name="pattern"></a>Mintázat

12 betű és számjegy:

- két betű (kis-és nagybetűk megkülönböztetése nélkül)
- nyolc számjegy
- két betű (kis-és nagybetűk megkülönböztetése nélkül)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_jp_residence_card_number"></a>Kulcsszó \_ JP \_ tartózkodási \_ kártya \_ száma

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japán – rezidens regisztrációs szám

### <a name="format"></a>Formátum

11 számjegy

### <a name="pattern"></a>Mintázat

11 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_jp_resident_registration_number"></a>Kulcsszó \_ JP \_ rezidens \_ regisztrációs \_ száma

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japán társadalombiztosítási szám (SIN)

### <a name="format"></a>Formátum

7-12 számjegy

### <a name="pattern"></a>Mintázat

7-12 számjegy:

- négy számjegy
- kötőjel (nem kötelező)
- hat számjegy vagy
- 7-12 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_jp_sin"></a>Kulcsszó \_ JP \_ Sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Malajzia azonosító kártya száma

### <a name="format"></a>Formátum

12 számjegy választható kötőjeleket tartalmaz

### <a name="pattern"></a>Mintázat

12 számjegy:

- hat számjegy a ÉÉHHNN formátumban, amely a születési dátum
- kötőjel (nem kötelező)
- kétbetűs születési kód
- kötőjel (nem kötelező)
- három véletlenszerű számjegy
- egyszámjegyű nemek kódja

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_malaysia_id_card_number"></a>Kulcsszó \_ Malajzia \_ azonosító \_ kártya \_ száma

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Holland állampolgári szolgáltatás (BSN) száma

### <a name="format"></a>Formátum

Nyolc-kilenc számjegy, amely opcionális szóközöket tartalmaz

### <a name="pattern"></a>Mintázat

Nyolc-kilenc számjegy:

- három számjegy
- szóköz (nem kötelező)
- három számjegy
- szóköz (nem kötelező)
- két-három számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_netherlands_eu_national_id_card"></a>Kulcsszavak \_ holland \_ EU \_ nemzeti \_ azonosító \_ kártya

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Új-zélandi Ministry of Health Number

### <a name="format"></a>Formátum

három betű, egy szóköz (opcionális) és négy számjegy

### <a name="pattern"></a>Mintázat

- három betű (nem megkülönbözteti a kis-és nagybetűket) az "I" és az "O" kivételével
- szóköz (nem kötelező)
- négy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_nz_terms"></a>Kulcsszavas \_ NZ- \_ kifejezések

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Norvégia azonosító száma

### <a name="format"></a>Formátum

11 számjegy

### <a name="pattern"></a>Mintázat

11 számjegy:

- hat számjegy a NNHHÉÉ formátumban, amely a születési dátum
- háromjegyű egyedi szám
- két ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_norway_id_number"></a>Kulcsszó \_ Norvégia \_ azonosítójának \_ száma

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Fülöp-szigetek egységes többcélú azonosító száma

### <a name="format"></a>Formátum

12 számjegy, kötőjelekkel elválasztva

### <a name="pattern"></a>Mintázat

12 számjegy:

- négy számjegy
- kötőjel
- hét számjegy
- kötőjel
- egy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_philippines_id"></a>Kulcsszó \_ Fülöp-szigetek \_ azonosítója

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Lengyel személyazonosító igazolvány

### <a name="format"></a>Formátum

három betű és hat számjegy

### <a name="pattern"></a>Mintázat

három betű (kis-és nagybetűk megkülönböztetése nélkül) hat számjegy után

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_poland_national_id_passport_number"></a>Kulcsszó \_ Lengyelország \_ nemzeti \_ azonosítójának \_ Passport- \_ száma

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Lengyel nemzeti azonosító (PESEL)

### <a name="format"></a>Formátum

11 számjegy

### <a name="pattern"></a>Mintázat

- 6 számjegy, amely a születési dátumot jelöli a ÉÉHHNN formátumban
- 4 számjegy
- 1 ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_pesel_identification_number"></a>Kulcsszó \_ Pesel- \_ azonosító \_ száma

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Lengyel útlevél száma

Ezt a bizalmas adattípus-entitást az EU-beli Passport-szám bizalmas információ típusa tartalmazza, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

két betű és hét számjegy

### <a name="pattern"></a>Mintázat

Két betű (kis-és nagybetűk megkülönböztetése), majd hét számjegyből áll

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_poland_national_id_passport_number"></a>Kulcsszó \_ Lengyelország \_ nemzeti \_ azonosítójának \_ Passport- \_ száma

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Portugál állampolgári kártya száma

### <a name="format"></a>Formátum

nyolc számjegy

### <a name="pattern"></a>Mintázat

nyolc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_portugal_citizen_card"></a>Kulcsszó \_ Portugália \_ Citizen \_ Card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Portugáliai illesztőprogram licencének száma

Ez a bizalmas adattípus-entitás csak az EU-illesztőprogram licencelési számának bizalmas adattípusában érhető el.

### <a name="format"></a>Formátum

két minta – két betű, majd 5-8 számjegy, speciális karakterekkel

### <a name="pattern"></a>Mintázat

1. minta: két betű, majd 5/6 speciális karakterekkel:

- Két betű (a kis-és nagybetűk megkülönböztetése nélkül)
- Kötőjel
- Öt vagy hat számjegy
- Egy szóköz
- Egy számjegy

2. minta: egy betűt, majd 6/8 számjegyet, speciális karakterekkel:

- Egy betű (nem megkülönbözteti a kis-és nagybetűket)
- Kötőjel
- Hat vagy nyolc számjegy
- Egy szóköz
- Egy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_eu_drivers_license_number"></a>Kulcsszavak \_ EU-jogosítvány \_ \_ \_ száma

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Kulcsszavak \_ portugáliai \_ EU- \_ jogosítvány \_ \_ száma

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Szaúd-arábiai nemzeti azonosító

### <a name="format"></a>Formátum

10 számjegy

### <a name="pattern"></a>Mintázat

10 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_saudi_arabia_national_id"></a>Kulcsszó \_ Szaúd \_ -Arábia \_ nemzeti \_ azonosítója

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Szingapúri nemzeti regisztrációs azonosító kártya (NRIC) száma

### <a name="format"></a>Formátum

kilenc betű és számjegy

### <a name="pattern"></a>Mintázat

- kilenc betű és számjegy:
- az &quot; F &quot; , &quot; G &quot; , &quot; S &quot; vagy T betű &quot; &quot; (nem megkülönbözteti a kis-és nagybetűket)
- hét számjegy
- ABC-ellenőrzési számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_singapore_nric"></a>Kulcsszó \_ Szingapúr \_ NRIC

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Dél-afrikai azonosító száma

### <a name="format"></a>Formátum

13 számjegy, amely tartalmazhat szóközöket

### <a name="pattern"></a>Mintázat

13 számjegy:

- hat számjegy a ÉÉHHNN formátumban, amely a születési dátum
- négy számjegy
- egyszámjegyű állampolgárságú kijelző
- a &quot; 8. &quot; és a &quot; 9. számjegy&quot;
- egy számjegy, amely egy ellenőrzőösszeg-számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_south_africa_identification_number"></a>Kulcsszó \_ Dél \_ -Afrika \_ azonosító \_ száma

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Dél-Korea rezidens regisztrációs száma

### <a name="format"></a>Formátum

13 számjegy kötőjelet tartalmaz

### <a name="pattern"></a>Mintázat

13 számjegy:

- hat számjegy a ÉÉHHNN formátumban, amely a születési dátum
- kötőjel
- egy számjegy a század és a nemek szerint meghatározva
- négyjegyű régió – születési kód
- egy számjegy, amellyel megkülönböztethető azoknak a személyeknek a száma, akiknél az előző számok megegyeznek
- egy ellenőrzési számjegy.

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_south_korea_resident_number"></a>Kulcsszó \_ Dél- \_ Korea \_ rezidens \_ száma

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Spanyolországi társadalombiztosítási szám (SSN)

Ezt a bizalmas adattípus-entitást az EU társadalombiztosítási száma vagy az azzal egyenértékű azonosító bizalmas adattípus tartalmazza, és önálló, bizalmas információ típusú entitásként érhető el.

### <a name="format"></a>Formátum

11-12 számjegy

### <a name="pattern"></a>Mintázat

11-12 számjegy:

- két számjegy
- perjel (nem kötelező)
- hét – nyolc számjegy
- perjel (nem kötelező)
- két számjegy

### <a name="keywords"></a>Kulcsszavak

Nincsenek

## <a name="sweden-national-id"></a>Svéd nemzeti azonosító

### <a name="format"></a>Formátum

10 vagy 12 számjegy és egy opcionális elválasztó

### <a name="pattern"></a>Mintázat

10 vagy 12 számjegy és egy választható elválasztó:

- két számjegy (nem kötelező)
- Hat számjegy a dátumformátum ÉÉHHNN
- &quot; - &quot; vagy (nem &quot; + &quot; kötelező)
- négy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_swedish_national_identifier"></a>Kulcsszavak \_ svéd \_ nemzeti \_ azonosító

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Svédország Passport-szám

Ezt a bizalmas adattípus-entitást az EU-beli Passport-szám bizalmas információ típusa tartalmazza, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

nyolc számjegy

### <a name="pattern"></a>Mintázat

nyolc egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_sweden_passport"></a>Kulcsszó \_ Svédország \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Kulcsszó \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT-kód

### <a name="format"></a>Formátum

négy betű után 5-31 betű vagy szám

### <a name="pattern"></a>Mintázat

négy betű után 5-31 betű vagy szám:

- négy levélből származó banki kód (kis-és nagybetűk megkülönböztetése nélkül)
- egy választható terület
- 4-28 betű vagy szám (az alapszintű bankszámla száma (BBAN))
- egy választható terület
- egy-három betű vagy számjegy (a BBAN hátralévő része)

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_swift"></a>Kulcsszó \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Tajvani nemzeti azonosító száma

### <a name="format"></a>Formátum

egy betű (angol nyelven), amelyet kilenc számjegy követ

### <a name="pattern"></a>Mintázat

egy betű (angol nyelven), amelyet kilenc számjegy követ:

- egy betű (angol nyelven, nem megkülönbözteti a kis-és nagybetűket)
- az &quot; 1. &quot; és a &quot; 2. számjegy&quot;
- nyolc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_taiwan_national_id"></a>Kulcsszó \_ Tajvan \_ nemzeti \_ azonosítója

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Tajvani Passport-szám

### <a name="format"></a>Formátum

- biometrikus útlevél száma: kilenc számjegy
- nem biometrikus útlevél száma: kilenc számjegy

### <a name="pattern"></a>Mintázat

biometrikus útlevél száma:

- a 3. karakter &quot;&quot;
- nyolc számjegy

nem biometrikus Passport-szám:

- kilenc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_taiwan_passport"></a>Kulcsszó \_ Tajvan \_ Passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Tajvan – rezidens tanúsítvány (ARC/TARC) száma

### <a name="format"></a>Formátum

10 betű és számjegy

### <a name="pattern"></a>Mintázat

10 betű és számjegy:

- két betű (kis-és nagybetűk megkülönböztetése nélkül)
- nyolc számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_taiwan_resident_certificate"></a>Kulcsszó \_ Tajvan \_ rezidens \_ tanúsítványa

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>brit illesztőprogram licencének száma

Ez a bizalmas adattípusú entitás szerepel az EU-illesztőprogram licencelési számának bizalmas információ típusa területen, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

18 betű és számjegy kombinációja a megadott formátumban

### <a name="pattern"></a>Mintázat

18 betű és számjegy:

- öt betű (nem megkülönbözteti a kis-és nagybetűket) vagy a 9-es számjegyet &quot; &quot; egy betű helyett
- egy számjegy
- öt számjegy a születési dátum MMDDY (a hetedik karakter értéke 50, ha az illesztőprogram nő, azaz 51 – 62 helyett 01 – 12)
- két betű (nem megkülönbözteti a kis-és nagybetűket) vagy a 9-es számjegyet &quot; &quot; egy betű helyett
- öt számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_uk_drivers_license"></a>Kulcsszavas \_ Egyesült királyságbeli \_ illesztőprogramok \_ licence

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>brit választási tekercs száma

### <a name="format"></a>Formátum

két betű, a 1-4 számjegyet követve

### <a name="pattern"></a>Mintázat

két betű (kis-és nagybetűk megkülönböztetése nélkül), majd 1-4 számú

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_uk_electoral"></a>Kulcsszó, \_ Egyesült királyságbeli \_ választási

- tanácsi jelölés
- jelölési űrlap
- választási regisztráció
- választási roll

## <a name="uk-national-health-service-number"></a>brit nemzeti állapotfigyelő szolgáltatás száma

### <a name="format"></a>Formátum

10-17 számjegy szóközzel elválasztva

### <a name="pattern"></a>Mintázat

10-17 számjegy:

- három vagy 10 számjegy
- egy szóköz
- három számjegy
- egy szóköz
- négy számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_uk_nhs_number"></a>Kulcsszavas \_ Egyesült királyságbeli \_ NHS- \_ szám

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Kulcsszavas \_ Egyesült királyságbeli \_ NHS \_ Szám1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Kulcsszó \_ Egyesült királyságbeli \_ NHS- \_ szám \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>brit nemzeti biztosítási szám (NINO)

Ez a bizalmas adattípusú entitás szerepel az EU nemzeti azonosító számának bizalmas információ típusa mezőben, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

hét karakter vagy kilenc karakter szóközökkel vagy kötőjelekkel elválasztva

### <a name="pattern"></a>Mintázat

két lehetséges minta:

- két betű (az érvényes NINO-EK csak bizonyos karaktereket használnak ebben az előtagban; ez a minta nem megkülönbözteti a kis-és nagybetűket)
- hat számjegy
- vagy "A", "B", "C" vagy "" (például az előtaghoz hasonlóan) csak bizonyos karakterek szerepelhetnek az utótagban; nem megkülönbözteti a kis-és nagybetűket.

OR

- két betű
- szóköz vagy kötőjel
- két számjegy
- szóköz vagy kötőjel
- két számjegy
- szóköz vagy kötőjel
- két számjegy
- szóköz vagy kötőjel
- vagy "A", "B", "C" vagy "'D"


### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_uk_nino"></a>Kulcsszó – \_ Egyesült Királyság – \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>brit Egyedi adófizető hivatkozási szám

Ez a bizalmas adattípus csak a következő alkalmazásokban használható:

- adatveszteség-megelőzési szabályzatok
- kommunikációs megfelelőségi szabályzatok
- tájékoztatási irányítás
- rekordok kezelése
- A Microsoft Cloud app Security

### <a name="format"></a>Formátum

10 számjegy szóközök és határolójelek nélkül

### <a name="pattern"></a>Mintázat

10 számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keywords_uk_eu_tax_file_number"></a>Kulcsszavak \_ Egyesült királyságbeli \_ EU- \_ Adószám \_ \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Egyesült államokbeli bankszámla száma

### <a name="format"></a>Formátum

6-17 számjegy

### <a name="pattern"></a>Mintázat

6-17 egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_usa_bank_account"></a>Kulcsszó \_ Egyesült államokbeli \_ bankszámla \_

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Egyesült államokbeli jogosítvány száma

### <a name="format"></a>Formátum

Az állapottól függ

### <a name="pattern"></a>Mintázat

az állapottól függ – például New York:

- a (z) DDD-ként formázott kilenc számjegy megegyezik.
- a kilenc számjegy, például a ddddddddd nem egyezik.

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_us_drivers_license_abbreviations"></a>Az \_ USA-beli \_ illesztőprogramok \_ licencének \_ rövidítései

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Kulcsszó \_ US- \_ illesztőprogram- \_ licenc

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Kulcsszó \_ [állapot \_ neve] \_ illesztőprogram- \_ licenc \_ neve

- állapot rövidítése (például: &quot; ny &quot; )
- állapot neve (például: &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Egyesült államokbeli egyedi adófizető-azonosító szám (ITIN)

### <a name="format"></a>Formátum

kilenc számjegy, amely 9 karakterből indul &quot; &quot; , és &quot; a &quot; &quot; negyedik számjegyből 7 vagy 8 karaktert tartalmaz &quot; , opcionálisan formázott szóközökkel vagy kötőjelekkel

### <a name="pattern"></a>Mintázat

formázott

- 9. számjegy &quot;&quot;
- két számjegy
- szóköz vagy kötőjel
- &quot;7 &quot; vagy &quot; 8&quot;
- egy számjegy
- szóköz vagy kötőjel
- négy számjegy

formázatlan

- 9. számjegy &quot;&quot;
- két számjegy
- &quot;7 &quot; vagy &quot; 8&quot;
- öt számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_itin"></a>Kulcsszó \_ ITIN

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Egyesült államokbeli társadalombiztosítási szám (SSN)

### <a name="format"></a>Formátum

kilenc számjegy, amely formázatlan vagy formázatlan mintában lehet

>[!Note]
> Ha a 2011-es közepe előtt állítja ki, az SSN formátuma erős, ha a szám egyes részeinek érvényesnek kell lenniük (de nincs ellenőrzőösszeg).
>

### <a name="pattern"></a>Mintázat

négy függvény keresi a SSNs négy különböző mintázatban:

- \_Az SSNs a következő, kötőjelekkel vagy szóközökkel formázott, előre 2011 erős formázással rendelkező
- \_A nem formázott \_ SSNs az 2011-es előtti, erős formázású, nem formázott, egymást követő számjegyeket (ddddddddd)
- \_Az függvény véletlenszerű \_ formázású \_ Taj-je a kötőjelekkel vagy szóközökkel formázott post-2011 SSNs-ket keres.
- A \_ \_ \_ nem formázott, a következő karakterrel elválasztott, formázatlan Taj-számú függvény megkeresi az 2011-es SSNs, amely nem formázható kilenc

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_ssn"></a>Kulcsszó \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>EGYESÜLT ÁLLAMOK/EGYESÜLT KIRÁLYSÁG Passport-szám

Egyesült Királyság a Passport-szám bizalmas információ típusú entitása az EU Passport-szám bizalmas információ típusa területen érhető el, és önálló, bizalmas adattípusú entitásként érhető el.

### <a name="format"></a>Formátum

kilenc számjegy

### <a name="pattern"></a>Mintázat

kilenc egymást követő számjegy

### <a name="keywords"></a>Kulcsszavak

#### <a name="keyword_passport"></a>Kulcsszó \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
