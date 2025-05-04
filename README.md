# Vorwort
Dieses Dokument wurde mit Architekten und erfahreneren Entwicklern als Zielgruppe erstellt.

Die JLib wurde mit Genehmigung meines Arbeitgebers im laufe mehrerer Projekte entworfen und weiterentwickelt. Ihr Ziel ist es, die Technische Komplexität aus dem Fachlichen Code zu entfernen, den Boilerplate Code zu reduzieren und so die Codequalität zu steigern.
Dies bedeutet leider auch, dass der Code der JLib sher komplex werden kann. 
Um die Pflegbarkeit dennoch zu gewährleisten, ist sie nach einem Zweibelschalen-Prinzip aufgebaut. Dies vereinfacht die Wartung und Erweiterung, da jede eben als ein in sich geschlossenes und testbares Konstrukt betrachtet werden kann.

Die Komponenten der JLib sind darauf ausgelegt eigenständig nutzbar zu sein, sie enthält aber auch ein paar Pakete welche ihre Bestandteile zu einem Opinionated Framework verbindet.
# Repository:
https://github.com/J0nnyI/JLib

# Basiskonzepte
## ValueTypes (Value Objects)
Sie haben ihren Ursprung im Domain Driven Design und fügen Domänenkontext zu einem Typen/Objekt hinzu.
So ist der Typ einer Variable z.B. nicht mehr `string`, sondern `Email` oder `UserName`.
https://github.com/J0nnyI/JLib/tree/dev/JLib.ValueTypes
https://github.com/J0nnyI/JLib/tree/dev/JLib.ValueTypes.Examples
# Innovative Konzepte
## Gecachte, typisierte Reflection
### Konzepte
## TypeValueTypes
Sie erweitern das Konzept der Reflection Types um ValueTypes, so dass man nicht mehr mit `Type`, sondern mit `ReadWriteEntity`, `ReadOnlyEntity` oder `AutoMapperProfile` arbeitet.
https://github.com/J0nnyI/JLib/blob/56b5aa75231d5afe498a09618f73e9e7c4c3f516/JLib.Reflection/ValueTypeHandling/TypeValueType.cs#L61
Definieren in ihren Attributen Bedingungen, die ein Type erfüllen muss um als diesem TypeValueType bereitgestellt zu werden.
### Type Package
Geben an, welche Typen für die Reflection berücksichtigt werden sollen. Dies kann ein einfaches laden aller Assemblies im aktuellen Startverzeichnis für die Anwendung selber sein oder eine vorsichtig kurierte Liste für Unittests.
https://github.com/J0nnyI/JLib/blob/dev/JLib.Reflection/TypePackage/TypePackageBuilder.cs
### TypeCache
Initialisiert die TypeValueTypes zur Startzeit der Anwendung.
Implementiert die fortgeschrittenen Funktionen der TypeValueTypes wie NavigationProperties und Deterministic Launchtime Validation
https://github.com/J0nnyI/JLib/blob/dev/JLib.Reflection/Typecache/TypeCache.cs
### Nutzungsbeispiel des TypeCaches:
Info:
- Ein TypeValueType kann `IMappedDataObjectType` implementieren und gibt so an, wie das Mapping-Verhältnis zwischen den Typen aussehen soll.
https://github.com/J0nnyI/JLib/blob/dev/JLib.DataProvider.AutoMapper/MappedDataObjectProfile.cs
#### Validierung
https://github.com/J0nnyI/JLib/blob/dev/JLib.DataProvider/FlagInterfaces/EntityType.cs
## Data Provider
Data Provider sind Automatisch erstellte Repositories Eine Vollständige Implementierung enthält ein global angewendetes, Profilbasiertes Autorisierungssystem.
https://github.com/J0nnyI/JLib/tree/dev/JLib.DataProvider
### Beispiele
Derzeit gibt es noch kein Beispielprojekt, 

## Test-Datengenerator
Situation:
- Das Domänenprojekt nutzt komplexe, relationale Daten
- Diese Daten sind für das testen des Codes notwendig
Paketbasiertes Servicekonstrukt, das die Dependency Injection und Vererbungen nutzt, um voll automatisch einen vollständigen Satz an Relationalen Daten zu erstellen, welche beliebig komplex sein können
https://github.com/J0nnyI/JLib/tree/dev/JLib.DataGeneration
### Beispiele:
https://github.com/J0nnyI/JLib/tree/dev/Examples/JLib.DataGeneration.Examples


## Vereinfachte & Gecachte Reflection
Minimal-Setup mit alter Type-Package Schreibweise
https://github.com/J0nnyI/JLib/blob/56b5aa75231d5afe498a09618f73e9e7c4c3f516/JLib.Reflection.Tests/TypeCacheTests.cs#L100
## Exceptionbuilder



# Genutzte Fortgeschrittene Kompetenzen
## Manuell geschriebene
https://github.com/J0nnyI/JLib/blob/dev/JLib.AutoMapper/TypeToDictionaryProfile.cs
## Nutzung der Basis um 
## Kreative Lösungen für Komplexe Probleme
### Request-Scoped Services
Situation:
- Die Berechtigungen eines Nutzers ändern sich im laufe einer Abfrage nicht
- Für die Abfrage der Berechtigungen ist eine Datenbankabfrage notwendig
- Durch die Funktionsweise von Hot Chocolate werden die Scopes nach einander aufgebaut, so dass sich die Abfragelatenz der Berechtigungen schnell addiert
Lösung:
- Der Autorisierungsservice wird nur 1 mal pro Abfrage initialisiert. Sub-Scopes verweisen auf die gleiche Instanz
https://github.com/J0nnyI/JLib/blob/56b5aa75231d5afe498a09618f73e9e7c4c3f516/JLib.AspNetCore/AspNetCoreServiceCollectionExtensions.cs#L238

## Definierbarer Umfang der Reflectionlogik
Situation:
- Es ist Notwendig, über die Klassen mit Reflection zu iterieren
- Peer-Dependencies sollen mit Berücksichtigt werden
	- Dies muss optional beim einbinden einer Dependency sein
- Man muss zu testzwecken in der Lage sein, Typen explizit ein und ausschließen zu können
- Man muss aus Kompatibilitätsgründen in der Lage sein, Assemblies von der Reflection auszuschließen bevor sie geladen werden
Lösung:
- Die Reflection nutzt ein Paket, welches die zu nutzenden Typen enthält
- Beim Bauen das Typenpakets wird definiert, was es enthalten soll
Notiz:
- Das Interface enthält Überflüssge Member um die Kompatibilität mit altem Code zu garantieren
