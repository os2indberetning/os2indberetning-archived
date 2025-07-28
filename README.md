# ARCHIVED

This project has been archived and replaced by the new OS2indberetning project, which is located in the os2indberetning repository.

----

# OS2Indberetning

OS2Indberetning was programmed by IT Minds ApS (http://it-minds.dk) for OS2 - Offentligt digitaliseringsfællesskab (http://os2.eu).

Copyright (c) 2016, OS2 - Offentligt digitaliseringsfællesskab.

OS2Indberetning is free software; you may use, study, modify and distribute it under the terms of version 2.0 of the Mozilla Public License. See the LICENSE file for details. If a copy of the MPL was not distributed with this file, You can obtain one at http://mozilla.org/MPL/2.0/.

All source code in this and the underlying directories is subject to the terms of the Mozilla Public License, v. 2.0.

## Indberetningsflow

![Indberetningsflow](https://github.com/os2indberetning/os2indberetning/blob/development/state.png)

## Installationsvejeledning

### Krav
Løsningen kræver adgang til en MySQL database.

### Installation

Database integreringen er lavet med entity framework code first, så når koden er hentet ned kan det åbnes i visual studio, og herfra kan databasen sættes op ved hjælp af migrations som ligger i Infrastructure.DataAccess projektet. Dette gøres ved at køre update-database fra package manager consolen i det nævntt projekt.

Løsningen består af 4 individuelle applikationer som alle er en del af en samlet solution, og de er beskrevet hver for sig herunder. 

Hvert program har brug for noget opsætning der er specifik for de enkelte kommuner, denne opsætning laves i to .config filer som beskrevet i afsnittet opsætning.

#### Presentation.Web
Dette er web applikationen som viser hjemmesiden for brugerne.

Denne skal deployes til en IIS. Da den genererer en IND01 fil til KMD, skal den application pool den kører under have adgang til filens placering som er sat op ved hjælp af PROTECTED_KMDFilePath som beskrevet i afsnittet Opsætning.
IIS'en skal desuden være sat op til kun at tillade windows authentication hvilket lader brugerne logge ind via deres AD informationer. Senere udvidelser til OS2indberetning vil muligvis også tillade nemID login.

#### Mail (under ConsoleApplications)
Når dette program kører ser det i databasen om de er sat en mail advisering op til i dag (opsættes af administratorere i web klienten), og sender i så fald mails til de personer der måtte mangle at godkende indberetninger.

Programmet kan sættes op til at køre som et scheduled task i windows.

#### DBUpdater (under ConsoleApplications)
Dette program står for at hente informationer fra views som kommunen stiller til rådighed angående personer, deres ansættelsesforhold samt organisationsenheder.

Programmet kan sættes op til at køre som et scheduled task i windows. Det skal sættes op til at køre efter at views er genereret af kommunen, men også før det forventes at brugere tilgår løsningen om morgenen.
Det er også dette program der opdaterer eventuelle beskidte adresser som en administrator har vasket i løbet af dagen, så vaskede adresser er altså først tilgængelige dagen efter.

Programmet skal tilpasses de database views som kommunen stiller tilrådighed. Vær opmærsom på at de views som var tilgængelige under udarbejdelsen af løsninge var fra en MS SQL server, og der lykkedes ikke at få entity framework til at spille sammen med de to database typer i det samme projekt, derfor hentes informationen fra views direkte med almindelige sql queries.

#### Infrastructure.DMZsync
Hvis mobil applikationen bruges, så kører den op mod en webserver og en database der ligger i en DMZ, hvor det personfølsomme data der ligger i DMZ databasen er krypteret. DMZsync sørger så for hver nat at synkronisere det data som webklienten har tilføjet samt det som mobil applikationen har synkroniseret op. 

## Opsætning

Tilpasning og opsætning af løsning gøres igennem konfigurationsfiler som ligger i projekternes rodmapper. Herunder gennemgåes de forskellige projekter og de relevante konfigurationsfiler, hvor der skal foretages ændringer.
Der er to konfigurationsfiler CustomSettings.config og connections.config som skal sættes op før projektet kan køre. CustomSettings.config og connections.config skal tilføjes til Solution Items mappen, hvorefter de automatisk vil være linket rundt til de relevante projekter. 

#### CustomSettings.config

CustomSettings.config ligger ikke i repositoriet og skal derfor tilføjes manuelt. Formatet er som følger og en beskrivelse af hvert felt står nedenunder:

```
<appSettings>
  <add key="PROTECTED_SMTP_PASSWORD" value=""/>
  <add key="PROTECTED_SMTP_HOST" value=""/>
  <add key="PROTECTED_SMTP_USER" value=""/>
  <add key="PROTECTED_SMTP_HOST_PORT" value=""/>
  <add key="PROTECTED_MAIL_FROM_ADDRESS" value=""/>
  <add key="PROTECTED_MAIL_SUBJECT" value=""/>
  <add key="PROTECTED_MAIL_BODY" value=""/>

  <add key="PROTECTED_KMDFilePath" value=""/>
  <add key="PROTECTED_KMDBackupFilePath" value=""/>
  <add key="PROTECTED_KMDFileName" value=""/>
  <add key="PROTECTED_KMDHeader" value=""/>
  <add key="PROTECTED_KMDStaticNr" value=""/>
  <add key="PROTECTED_CommuneNr" value=""/>
  <add key="PROTECTED_KMDReservedNr" value=""/>
  <add key="PROTECTED_AdministrativeCostCenterPrefix" value=""/>
  <add key="PROTECTED_AdministrativeAccount" value=""/>

  <add key="UseSD" value=""/>
  <add key="PROTECTED_SDUserName" value=""/>
  <add key="PROTECTED_SDUserPassword" value=""/>
  <add key="PROTECTED_institutionNumber" value=""/>
  
  <add key="PROTECTED_muniplicity" value=""/>

  <add key="MapStartStreetName" value=""/>
  <add key="MapStartStreetNumber" value=""/>
  <add key="MapStartZipCode" value=""/>
  <add key="MapStartTown" value=""/>

  <add key="PROTECTED_AD_DOMAIN" value=""/>
  <add key="PROTECTED_DailyErrorLogMail" value=""/>

  <add key="InformationHelpLink" value=""/>
  <add key="TableSortHelp" value=""/>
  <add key="FourKmRuleHelpText" value=""/>
  <add key="MobileTokenHelpText" value=""/>
  <add key="AlternativeWorkAddressHelpText" value=""/>
  <add key="AlternativeHomeAddressHelpText" value=""/>
  <add key="AlternativeWorkDistanceHelpText" value=""/>
  <add key="PrimaryLicensePlateHelpText" value=""/>
  <add key="PersonalApproverHelpText" value=""/>
  <add key="EmailHelpText" value=""/>
  <add key="PurposeHelpText" value=""/>
  <add key="ReadReportCommentHelp" value=""/>
  <add key="NoLicensePlateHelpText" value=""/>
  <add key="AccountHelpText" value=""/>

  <add key="SEPTIMA_API_KEY" value=""/>
  
  <add key="AlternativeCalculationMethod" value=""/>

  <add key="DATABASE_VIEW_MEDARBEJDER" value=""/>
  <add key="DATABASE_VIEW_ORGANISATION" value=""/>
  <add key="DATABASE_INTEGRATION" value=""/>
</appSettings>
```

##### Mailopsætning
Nedenfor beskrives de oplysninger der skal indtastes til opsætning af SMTP-server.
Serveren bruges til at sende mails til brugere, når en af deres indberetninger bliver afvist.

###### PROTECTED_SMTP_PASSWORD
Udfyldes med password til SMTP-server

###### PROTECTED_SMTP_HOST
udfyldes med adresse til SMTP-server

###### PROTECTED_SMTP_USER
udfyldes med brugernavn til SMTP-server

###### PROTECTED_SMTP_HOST_PORT
Udfyldes med port til SMTP-server

###### PROTECTED_MAIL_FROM_ADDRESS
Udfyldes med den mail der skal stå som afsender

###### PROTECTED_MAIL_SUBJECT
Udfyldes med emnet på mails om afventende indberetninger til ledere

###### PROTECTED_MAIL_BODY
Udfyldes med indholdet af mails om afventende indberetninger

##### KMD-opsætning
Nedenfor beskrives de oplysninger der skal indtastes i forbindelse med generering af fil til KMD ind01 snitflade.

###### PROTECTED_KMDFilePath
Udfyldes med den sti hvor filen til KMD skal gemmes, når en administrator vælger at generere den

###### PROTECTED_KMDBackupFilePath
Udfyldes med den sti hvor der skal tages en backup af den fil der er genereret til KMD

###### PROTECTED_KMDFileName
Navn på den genererede fil til KMD.

###### PROTECTED_KMDHeader
Første linje i fil til KMD

###### PROTECTED_KMDStaticNr
Udfyldes med statisk KMD-nummer

###### PROTECTED_CommuneNr
Udfyldes med kommunenummer til KMD

###### PROTECTED_KMDReservedNr
Udfyldes med reserveret KMD-nummer

###### PROTECTED_AdministrativeCostCenterPrefix
Administrative medarbejdere genkendes på at de har et omkostningssted som starter med en særlig sekvens f.eks. 1012

###### PROTECTED_AdministrativeAccount
Udfyldes med den konto hvor kørsel fra administrative medarbejdere skal konteres 

###### UseSD
true/false - angiver om der bruges SD (true) eller KMD (false) snitflade.

###### PROTECTED_SDUserName
Udfyldes med brugernavn til SD snitflade. 

###### PROTECTED_SDUserPassword
Udfyldes med password til SD snitflade.

###### PROTECTED_institutionNumber
Udfyldes med institutionsnummer til SD snitflade.

###### PROTECTED_muniplicity
Udfyldes med navn på kommune til brug i rapportmodul.

##### Startadresse til kort
Nedenfor beskrives de oplysninger der skal indtastes for at vælge den adresse kortet skal starte med at vise i kørselsindberetning.

###### MapStartStreetName
Udfyldes med vejnavn for startadresse.

###### MapStartStreetNumber
Udfyldes med vejnummer for startadresse.

###### MapStartZipCode
Udfyldes med postnummer for startadresse.

###### MapStartTown
Udfyldes med by for startadresse.

##### AD-domæne opsætning

###### PROTECTED_AD_DOMAIN
Udfyldes med AD-domænenavn.

###### PROTECTED_DailyErrorLogMail
Skal kun bruges i mail-projektet. Skal indeholde en kommasepareret liste af emails, hvortil der skal sendes oversigt over daglig aktivitet i logs. Kan f.eks. være "admin1@host.dk, admin2@host.dk".

##### Hjælpetekster
Nedenfor beskrives de felter der skal udfyldes for diverse hjælpetekster i OS2Indberetning. Hvis et felt efterlades tomt
vil der ikke blive vist et hjælpeikon på hjemmesiden.

###### InformationHelpLink
Udfyldes med link til den side der linkes til under Information og vejledning øverst til højre på hjemmesiden.

###### TableSortHelp
Udfyldes med den hjælpetekst der vises over tabeller med indberetninger.
Hjælpeteksten vises på Mine indberetninger/Godkend indberetninger og Admin->Indberetninger

###### FourKmRuleHelpText
Udfyldes med hjælpetekst der beskriver 4 km-reglen.
Hjælpeteksten vises under Indberet Tjenestekørsel

###### MobileTokenHelpText
Udfyldes med hjælpetekst der beskriver tokens til mobil-app.
Hjælpeteksten vises under Personlige indstillinger.

###### AlternativeWorkAddressHelpText
Udfyldes med hjælpetekst der beskriver afvigende arbejdsadresse.
Hjælpeteksten vises under Personlige indstillinger.

###### AlternativeHomeAddressHelpText
Udfyldes med hjælpetekst der beskriver afvigende hjemmeadresse.
Hjælpeteksten vises under Personlige indstillinger.

###### AlternativeWorkDistanceHelpText
Udfyldes med hjælpetekst der beskriver afvigende afstand mellem hjemme- og arbejdsadresse.
Hjælpeteksten vises under Personlige indstillinger.

###### PrimaryLicensePlateHelpText
Udfyldes med hjælpetekst der beskriver primær nummerplade.
Hjælpeteksten vises under Personlige indstillinger.

###### PersonalApproverHelpText
Udfyldes med hjælpetekst der beskriver personlig godkender.
Hjælpeteksten vises under Godkend indberetninger->Stedfortrædere/Godkendere og Admin->Stedfortrædere og godkendere

###### EmailHelpText
Udfyldes med hjælpetekst der beskriver email-notifikationer.
Hjælpeteksten vises under Admin->Emailadviseringer

###### PurposeHelpText
Udfyldes med hjælpetekst der beskriver formål med tjenestekørselsindberetning.
Hjælpeteksten vises under Indberet tjenestekørsel.

###### NoLicensePlateHelpText
Udfyles med hjælpetekst der fortæller brugeren han ingen nummerplader har.
Hjælpeteksten vises under Indberet tjenestekørsel, når man ingen nummerplader har.

###### ReadReportCommentHelp
Udfyldes med hjælpetekst der beskriver kommentar til aflæst tjenestekørselsindberetning.
Hjælpeteksten vises under Indberet tjenestekørsel, når man har valgt aflæst indberetning.

###### NoLicensePlateHelpText
Udfyles med hjælpetekst der fortæller brugeren han ingen nummerplader har. Hjælpeteksten vises under Indberet tjenestekørsel, når man ingen nummerplader har.

###### AccountHelpTextHelpText

##### Ruteberegning

###### SEPTIMA_API_KEY
Udfyldes med api-nøgle til Septimas ruteberegner.

###### AlternativeCalculationMethod
true/false - indikerer om der skal bruges Norddjurs Kommunes alternative beregningsmetode.

###### DATABASE_VIEW_MEDARBEJDER
Udfyldes med navn på tabellen med medarbejderdata i kommunens view.

###### DATABASE_VIEW_ORGANISATION
Udfyldes med navn på tabellen med organisations

###### DATABASE_INTEGRATION
SOFD/IDM - angiver om der bruges SOFD eller IDM.

#### connections.config
connections.config ligger ikke i repositoriet og skal derfor tilføjes manuelt. Formatet er som følger og en beskrivelse af de enkelte felter findes nedenunder.

```
<connectionStrings>
  <!-- Default connection for OS2 database  -->
  <add name="DefaultConnection" providerName="MySql.Data.MySqlClient" connectionString="Data Source=; port=; Initial Catalog=; uid=; pwd=; CharSet=utf8; Allow User Variables=True" />
  <!-- DBUpdaterConnection for connection to data views with Persons and OrgUnits -->
 <add name="DBUpdaterConnection" connectionString="data source=; initial catalog=; persist security info=True;user id=; password="/>
  <!-- DMZConnection for connection to app database in the DMZ -->
  <add name="DMZConnection" providerName="MySql.Data.MySqlClient" connectionString="Data Source=; port=; Initial Catalog=; uid=;  pwd=; CharSet=utf8; Allow User Variables=True" />
</connectionStrings>
```

Udfyldes med oplysninger om OS2Indberetnings databasen.

###### Data Source
Udfyldes med adressen på databaseserveren.

###### Initial Catalog
Udfyldes med navnet på databasen.

###### uid
Udfyldes med brugernavn til login på databasen.

###### pwd
Udfyldes med adgangskode til databasen

#### favicon.ico
Erstattes med kommunelogo til visning på fane i browser.
Filens findes i Presentation.Web/Content/favicon.ico

#### logo.png
Erstattes med kommunelogo til visning på hjemmesiden øverst til højre.
Filen findes i Presentation.Web/logo.png
Billedet skal være omkring 42 pixels i højden.

#### private.css
Indeholder attributten background-color, der bestemmer sidens baggrundsfarve, samt farven på markerede faneblade.
Filen findes i Presentation.Web/Content/private.css

#### connections.config

##### DefaultConnection
Bruges til at opsætte connection string for forbindelse til OS2Indberetningsdatabasen.

##### DBUpdaterConnection
Bruges til at oprette forbindelse til kommunedatabasen hvor information om medarbejdere og organisationsenheder hentes hver nat.

##### DMZConnection
Bruges til at oprette forbindelse til databasen der opbevarer data fra app'en, denne database ligger i en DMZn.

## Fejl og løsninger

### Mailadviseringer er ikke sendt

* Kontroller at der er opsat en mailadvisering under Administrator.
* Kontroller at alle felter er udfyldt korrekt under ConsoleApplications/Mail/CustomSettings.config
* Kontroller at alle felter er udfyldt korrekt under Presentation.Web/CustomSettings.config

### Fejlbeskeder i log

#### AD-bruger ikke fundet i databasen
* Kontroller at "PROTECTED_AD_DOMAIN" er udfyldt korrekt i Presentation.Web/CustomSettings.config

#### Gyldig domænebruger ikke fundet
* Kontroller at "PROTECTED_AD_DOMAIN" er udfyldt korrekt i Presentation.Web/CustomSettings.config
* Kontroller at brugeren har et gyldigt AD-login

#### Fejl ved adressevask
* Kontroller at de indtastede adresser findes og er i korrekt format (Vejnavn husnummer, postnr by)
* Kontroller at SEPTIMA_API_KEY er sat korrekt i CustomSettings.config

#### Fejl ved opslag af adressekoordinater
* Kontroller at de indtastede adresser findes og er i korrekt format (Vejnavn husnummer, postnr by)
* Kontroller at SEPTIMA_API_KEY er sat korrekt i CustomSettings.config
