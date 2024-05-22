---
lab:
  title: 'Lab: Bereitstellen und Verwalten einer Container-App mit Azure Container Apps'
  type: Answer Key
  module: 'Module 6: Deploy and manage a container app using Azure Container Apps'
---

# Lab: Bereitstellen und Verwalten einer Container-App mit Azure Container Apps
# Antwortschlüssel für Schülerlabs

## Anweisungen

In diesem Lab stellen Sie eine App mit Azure Container Apps bereit und verwalten sie. Um die Lösung zu implementieren, konfigurieren Sie zunächst eine Entwicklungsumgebung, die eine Kombination aus lokalen Tools und Azure-Ressourcen verwendet. Sobald die Umgebung vorbereitet ist, verwenden Sie Azure Container Registry, Azure Container Apps und Azure Pipelines, um Ihre App bereitzustellen und zu verwalten.  

In diesem Lab lernen Sie Folgendes:

1. Konfigurieren einer sicheren Verbindung zwischen einer Azure-Containerregistrierung und einer Azure Container Apps-App
1. Eine Containerapp in Azure Container Apps zu erstellen und zu konfigurieren.
1. Die Continuous Integration mithilfe von Azure Pipelines zu konfigurieren.
1. Eine bereitgestellte App in Azure-Container-Apps zu skalieren.
1. Verwalten von Revisionen in Azure Container Apps.

### Übung 1: Azure-Ressourcen konfigurieren

In dieser Übung konfigurieren Sie Azure-Ressourcen, die Ihre Azure Container Apps-Lösung unterstützen.

Sie werden für die folgenden Schritte ungefähr 10 bis 15 Minuten benötigen:

- Untersuchen der Ressourcengruppeneinstellungen
- Konfigurieren eines virtuellen Azure-Netzwerks mit Subnetzen
- Konfigurieren einer Azure Service Bus-Ressource
- Konfigurieren einer Azure Container Registry-Ressource

#### Aufgabe 1: Untersuchen der Ressourcengruppeneinstellungen

Führen Sie die folgenden Schritte aus, um die Standorteinstellung der Ressourcengruppe zu untersuchen, die in dieser Übung verwendet wird.

1. Öffnen Sie in der Laborumgebung ein Browserfenster und navigieren Sie dann zum Azure-Portal: `https://portal.azure.com/`

    Arbeiten Sie mit Ihrer Kursleitung zusammen, wenn Sie Hilfe benötigen, um sich beim Azure-Portal mit einem Abonnement/Konto anzumelden, das für dieses Lab geeignet ist.

1. Wählen Sie auf der Startseite des Azure-Portals unter **Navigieren** **Ressourcengruppen** aus.

1. Wählen Sie auf der Seite „Ressourcengruppen“ **RG1** aus.

    Wenn die Ressourcengruppe RG1 noch nicht erstellt wurde, erstellen Sie sie jetzt.

1. Notieren Sie sich die **Standorteinstellung**, die der Ressourcengruppe **RG1** zugewiesen ist.

    Sie werden in diesem Lab denselben Standort/dieselbe Region verwenden, wenn Sie andere Azure-Ressourcen erstellen.

1. Schließen Sie die Seite **RG1** und dann die Seite **Ressourcengruppen**.

#### Aufgabe 2: Konfigurieren eines virtuellen Netzwerks und der Subnetze

Führen Sie die folgenden Schritte aus, um ein virtuelles Netzwerk und die Subnetze zu konfigurieren.

1. Geben Sie in der oberen Suchleiste des Azure-Portals im Textfeld „Suchen“ **virtuelles Netzwerk** ein.

1. Wählen Sie in den Suchergebnissen **Virtuelle Netzwerke** aus.

1. Wählen Sie **Erstellen eines virtuellen Netzwerks** aus.

1. Konfigurieren Sie in der Registerkarte „Grundlagen“ Ihr virtuelles Netzwerk wie folgt:

    - Abonnement: Stellen Sie sicher, dass das Azure-Abonnement, das Sie für dieses angeleitete Projekt verwenden, ausgewählt ist.
    - Ressourcengruppenname: Wählen Sie **RG1**aus
    - Name des virtuellen Netzwerks: Geben Sie **VNET1** ein
    - Region: Stellen Sie sicher, dass die angegebene Region mit der Standorteinstellung Ihrer Ressourcengruppe übereinstimmt.

1. Wählen Sie die Registerkarte **IP-Adressen** aus.

1. Wählen Sie auf der Registerkarte „IP-Adressen“ unter **Subnetze** **Standard** aus.

1. Konfigurieren Sie auf der Seite „Subnetz bearbeiten“ das Subnetz wie folgt:

    - Name: Geben Sie **`PESubnet`** ein
    - Startadresse: Stellen Sie sicher, dass **10.0.0.0** angegeben ist.
    - Subnetzgröße: Stellen Sie sicher, dass **/24 (256 Adressen)** angegeben ist.

    Dieses erste Subnetz wird für einen privaten Endpunkt (Azure Container Registry) verwendet.

1. Wählen Sie **Speichern**.

1. Wählen Sie auf der Registerkarte „IP-Adressen“ die Option **+Subnetz hinzufügen** aus.

1. Konfigurieren Sie auf der Seite „Subnetz hinzufügen“ das Subnetz wie folgt:

    - Name: Geben Sie **`ACASubnet`** ein
    - Startadresse: Stellen Sie sicher, dass **10.0.4.0** angegeben ist.
    - Subnetzgröße: Stellen Sie sicher, dass **/23 (512 Adressen)** angegeben ist.

    Das Subnetz für Azure Container Apps erfordert einen Adressraum, der größer als 256 ist.

1. Wählen Sie **Hinzufügen** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Klicken Sie auf **Erstellen**, sobald die Überprüfung erfolgreich abgeschlossen wurde.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und schließen Sie dann die Seite VNET1.

#### Aufgabe 3: Konfigurieren des Service Bus

Führen Sie die folgenden Schritte aus, um eine Service Bus-Instanz zu konfigurieren.

1. Geben Sie in der oberen Suchleiste des Azure-Portals im Textfeld „Suchen“ **Servicebus** ein.

1. Wählen Sie in den Suchergebnissen **Service Bus** aus.

1. Wählen Sie **Erstellen eines Service Bus-Namespaces** aus.

1. Konfigurieren Sie auf der Registerkarte „Grundlagen“ den Servicebusnamespace wie folgt:

    - Abonnement: Stellen Sie sicher, dass das Azure-Abonnement, das Sie für dieses angeleitete Projekt verwenden, ausgewählt ist.
    - Ressourcengruppenname: Wählen Sie **RG1**aus
    - Name des Namespace: Geben Sie **sb-az2003-** gefolgt von Ihren Initialen und dem aktuellen Datum ein. Zum Beispiel: **sb-az2003-cah12oct**.
    - Standort: Stellen Sie sicher, dass der angegebene Standort mit der Standorteinstellung Ihrer Ressourcengruppe übereinstimmt.
    - Tarif: Wählen Sie **Basic** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Sobald die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist und schließen Sie dann die Seite Service Bus-Namespace.

#### Aufgabe 4: Konfigurieren der Azure Container Registry

Führen Sie die folgenden Schritte aus, um eine Container Registry-Instanz zu konfigurieren.

1. Geben Sie in der oberen Suchleiste des Azure-Portals im Textfeld „Suchen“ **Container Registry** ein.

1. Wählen Sie in den Suchergebnissen **Container Registry** aus.

1. Wählen Sie auf der Seite „Container Registry“ die Option **Container Registry erstellen** oder **+Erstellen** aus.

1. Geben Sie auf der Registerkarte „Basic“ der Seite „Container Registry erstellen“ die folgenden Informationen an:

    > [!NOTE]
    > Der Name der Registry muss eindeutig sein. Außerdem ist die Premium-Stufe für private Verknüpfungen mit privaten Endpunkten erforderlich.

    - Abonnement: Stellen Sie sicher, dass das Azure-Abonnement, das Sie für dieses angeleitete Projekt verwenden, ausgewählt ist.
    - Ressourcengruppe: Wählen Sie **RG1** aus.
    - Name der Registrierung: Geben Sie **acraz2003** gefolgt von Ihren Initialen und dem aktuellen Datum ein. Zum Beispiel: **acraz2003cah12oct**
    - Standort: Stellen Sie sicher, dass der angegebene Standort mit der Standorteinstellung Ihrer Ressourcengruppe übereinstimmt.
    - SKU: Wählen Sie **Premium** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

1. Öffnen Sie nach Abschluss der Bereitstellung Ihre Containerregistrierungsressource.

1. Wählen Sie im linken Menü der Seite „Containerregistrierung“ unter **Einstellungen** **Netzwerk** aus.

1. Stellen Sie auf der Seite „Netzwerk“ auf der Registerkarte „Öffentlicher Zugriff“ sicher, dass **alle Netzwerke** ausgewählt sind.

1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Eigenschaften** aus.

1. Wählen Sie auf der Seite „Eigenschaften“ **Administratorbenutzer** und dann **Speichern** aus.

1. Schließen Sie die Seite „Containerregistrierung“.

1. Schließen Sie das Browserfenster, in dem das Azure-Portal geöffnet ist.

### Übung 2: Konfigurieren von Entwicklertools in der Hostumgebung

In dieser Übung stellen Sie sicher, dass Skript- und Entwicklertools auf der VM ordnungsgemäß konfiguriert sind.

Sie werden für die folgenden Schritte ungefähr 20 bis 25 Minuten benötigen:

- Konfigurieren der Azure CLI-Erweiterungen
- Installieren Sie Docker Desktop.
- Installieren des .NET 8 SDK
- Aktualisieren und Konfigurieren von Visual Studio Code-Erweiterungen

#### Aufgabe 1: Deinstallieren von Visual Studio Code

Führen Sie die folgenden Schritte aus, um Visual Studio Code zu deinstallieren.

1. Öffnen Sie das Windows-Startmenü.

1. Wählen Sie im Startmenü **Einstellungen** aus.

1. Wählen Sie im linken Menü **Apps** und dann **Installierte Apps** aus.

1. Suchen Sie nach **Microsoft Visual Studio Code**.

1. Wählen Sie rechts neben Microsoft Visual Studio Code die Auslassungspunkte (…) und dann **Deinstallieren** aus.

1. Wählen Sie im Popupfenster **Deinstallieren** aus.

1. Wenn Sie dazu aufgefordert werden, wählen Sie **Ja** und dann **Ok** aus.

1. Schließen Sie „Einstellungen“.

#### Aufgabe 2: Konfigurieren von Azure CLI-Erweiterungen

Führen Sie die folgenden Schritte aus, um Azure CLI zu konfigurieren.

1. Öffnen Sie eine Befehlszeilen- oder Terminalanwendung, z. B. die Windows-Eingabeaufforderung.

1. Melden Sie sich mit dem `az login`-Befehl bei Azure an.

    Es wird ein Browserfenster geöffnet, in dem Sie das Azure-Konto auswählen können.

1. Folgen Sie den Anweisungen, um den Authentifizierungsprozess abzuschließen.

1. Schließen Sie das Browserfenster.

1. Geben Sie in der Befehlszeilen-App zum Installieren der Azure Container Apps-Erweiterung den folgenden Befehl ein: `az extension add --name containerapp --upgrade`

1. Schließen Sie die Befehlszeilen-App.

#### Aufgabe 3: Installieren von Docker Desktop

Führen Sie die folgenden Schritte aus, um Docker Desktop zu installieren.

1. Öffnen Sie ein Browserfenster und navigieren Sie dann zur Docker Desktop-Installationsseite: `https://docs.docker.com/desktop/install/windows-install/`

1. Wählen Sie **Docker Desktop for Windows** (Docker Desktop für Windows) aus, und warten Sie, bis die Installationsdatei heruntergeladen wird.

1. Öffnen Sie die heruntergeladene Installationsdatei, und folgen Sie dann den Anweisungen im Internet, um Docker Desktop zu installieren.

    Der Installationsvorgang dauert etwa fünf Minuten.

1. Wenn die Installation abgeschlossen ist wählen Sie **Close and restart** (Schließen und neu starten).

1. Wenn die aktualisierte VM neu gestartet wird, warten Sie, bis das Fenster **Docker Subscription Service Agreement** (Docker-Abonnement-Servicevertrag) angezeigt wird.

1. Wählen Sie auf der Seite „Docker Subscription Service Agreement“ die Option **Accept** (Akzeptieren) aus.

1. Wählen Sie auf der Seite „Finish setting up Docker Desktop“ (Einrichtung von Docker Desktop fertig stellen) die Option **Finish** (Abschließen) aus.

1. Wählen Sie auf der Seite Benutzerkontensteuerung **Yes** (Ja) aus.

1. Wählen Sie auf der Seite „Welcome to Docker Desktop“ (Willkommen bei Docker Desktop) die Option **Continue without signing in** (Weiter ohne Anmeldung).

1. Wählen Sie auf der Seite „Tell us about the work you do“ (Teilen Sie uns mit, woran Sie arbeiten) die Option **Skip** (Überspringen) aus.

1. Warten Sie, bis der Startvorgang des Docker-Moduls abgeschlossen ist, und minimieren Sie dann die Docker Desktop-App.

    Schließen Sie Docker Desktop nicht, sondern minimieren Sie einfach die ausgeführte App.

#### Aufgabe 4: Installieren des .NET 8 SDK

Führen Sie zum Installieren des .NET 8 SDK die folgenden Schritte aus.

1. Öffnen Sie ein Fenster im Webbrowser und navigieren Sie dann zur .NET 8 SDK-Downloadseite: `https://dotnet.microsoft.com/download`

1. Wählen Sie **.NET SDK x64** aus.

1. Nachdem der Download abgeschlossen ist, öffnen Sie die Installationsdatei, und folgen Sie den Anweisungen im Internet, um das .NET 8 SDK zu installieren.

1. Schließen Sie das Browserfenster.

#### Aufgabe 5: Installieren von Visual Studio Code mit den Erweiterungen C#, Docker und Azure App Service

Führen Sie die folgenden Schritte aus, um Visual Studio Code mit Erweiterungen zu konfigurieren.

1. Öffnen Sie ein Fenster im Webbrowser und navigieren Sie dann zur Downloadseite von Visual Studio Code: `https://code.visualstudio.com/download`

1. Wählen Sie **Windows** aus, und warten Sie, bis die Installationsdatei heruntergeladen ist.

1. Starten Sie die Installationsdatei für Visual Studio Code.

1. Akzeptieren Sie den Lizenzvertrag und dann die Standardeinstellungen, bis die Installation abgeschlossen ist.

1. Öffnen Sie Visual Studio Code.

1. Wählen Sie auf der **Aktivitätsleiste** die Option **Erweiterungen** aus.

    Die Aktivitätsleiste ist das vertikale Menü auf der linken Seite der Visual Studio Code-Benutzeroberfläche.

1. Geben Sie im Textfeld **Nach Erweiterungen in Marketplace suchen****C#** ein.

    Wenn Sie „C#“ eingeben, wird die Liste der Erweiterungen gefiltert, sodass nur die Erweiterungen mit Bezug zur C#-Codierung angezeigt werden.

1. Wählen Sie in der gefilterten Liste der verfügbaren Erweiterungen die Erweiterung „**C#-Dev Kit** – Offizielle C#-Erweiterung von Microsoft“ aus, die von Microsoft veröffentlicht wird.

1. Wählen Sie **Installieren** aus, um diese Erweiterung zu installieren.

1. Warten Sie, bis die Installation abgeschlossen ist.

    Es dauert etwa 1 Minute, bis das C# Dev Kit installiert ist.

1. Ersetzen Sie in der Ansicht **ERWEITERUNGEN** **C#-** durch **Docker**.

1. Wählen Sie in der gefilterten Liste der verfügbaren Erweiterungen die Erweiterung mit der Bezeichnung **Docker** aus, die von Microsoft veröffentlicht wurde.

1. Wählen Sie **Installieren** aus, um diese Erweiterung zu installieren.

1. Ersetzen Sie in der Ansicht **ERWEITERUNGEN** **Docker** durch **Azure App Service**.

1. Wählen Sie in der gefilterten Liste der verfügbaren Erweiterungen die Erweiterung mit der Bezeichnung **Azure App Service** aus, die von Microsoft veröffentlicht wurde.

1. Wählen Sie **Installieren** aus, um diese Erweiterung zu installieren.

1. Schließen Sie Visual Studio Code.

### Übung 3: Erstellen und Konfigurieren von App-Bereitstellungsressourcen

In dieser Übung konfigurieren Sie ein Azure DevOps-Projekt und eine Azure-Pipeline, erstellen und übertragen ein Docker-Image in Ihre Containerregistrierung und stellen einen selbst gehosteten Windows-Agent bereit.

Sie werden für die folgenden Schritte ungefähr 40 Minuten benötigen:

- Konfigurieren eines Azure DevOps-Projekts und Initialisieren Ihres Code-Repository
- Erstellen einer .NET-App und Synchronisieren mit Ihrem Azure DevOps-Repository
- Erstellen eines Docker-Image und Pushen des Image an Ihre Azure Container Registry
- Erstellen einer Azure Pipeline mit dem Namen Pipeline1
- Stellen Sie einen selbstgehosteten Windows-Agent bereit.

#### Aufgabe 1: Konfigurieren eines Azure DevOps-Projekts und Initialisieren des Code-Repository

Führen Sie die folgenden Schritte aus, um das Azure DevOps-Projekt zu konfigurieren.

1. Öffnen Sie ein Browserfenster und navigieren Sie dann zum Azure-Portal: `https://portal.azure.com/`

1. Geben Sie in der oberen Suchleiste im Textfeld „Suchen“ **DevOps** ein.

1. Wählen Sie in den Suchergebnissen **Azure DevOps-Organisationen** aus.

1. Wählen Sie **Meine Azure DevOps-Organisationen** aus.

1. Wählen Sie auf der Seite „Weitere Details“ **Weiter** aus.

1. Wählen Sie auf der Seite „Erste Schritte mit Azure DevOps“ **Neue Organisation erstellen** aus, und wählen Sie dann **Weiter** aus.

1. Geben Sie auf der Seite „Fast erledigt“ die angezeigten Zeichen ein, und wählen Sie dann **Weiter** aus.

1. Wählen Sie auf der Seite Ihrer DevOps-Organisation **Organisationseinstellungen** aus.

1. Wählen Sie im Menü auf der linken Seite unter **Sicherheit** die Option **Richtlinien** aus.

1. Legen Sie **Öffentliche Projekte zulassen** auf **Ein** fest, und wählen Sie dann **Speichern** aus.

1. Navigieren Sie zurück zur Seite Ihrer DevOps-Organisation.

1. Geben Sie unter „Projekt erstellen“ die folgenden Informationen ein:

    - Projektname: **AZ2003Project**
    - Beschreibung: **AZ2003-Codeprojekt**
    - Sichtbarkeit: **Öffentlich**

1. Wählen Sie **Projekt erstellen** aus.

1. Wählen Sie im linken Menü Ihrer AZ2003Project-Seite **Repositorys** aus.

1. Wählen Sie unter „Mainbranch mit einer README oder gitignore initialisieren“ **Initialisieren**aus.

1. Wählen Sie **Klonen** und anschließend **In VS Code klonen** aus.

1. Wählen Sie im Dialogfeld „Diese Website versucht, das Dialogfeld Visual Studio Code zu öffnen“ **Öffnen** aus.

1. Wählen Sie im Dialogfeld „Einer Erweiterung erlauben, diesen URI zu öffnen“ **Öffnen** aus.

1. Wählen Sie im Fenster „Ordner zum Klonen auswählen“ **Desktop** und dann **Neuer Ordner** aus, geben Sie **AZ2003** ein, und drücken Sie dann die Eingabetaste.

1. Wählen Sie **Als Repositoryziel auswählen**.

1. Wählen Sie im Dialogfeld „Möchten Sie das geklonte Repository öffnen“ **Öffnen** aus, und wählen Sie dann **Ja, ich vertraue den Autoren** aus.

#### Aufgabe 2: Erstellen einer .NET-App und Synchronisieren mit Ihrem Azure DevOps-Repository

Führen Sie die folgenden Schritte aus, um eine .NET-App zu erstellen und mit Ihrem Azure DevOps-Repository zu synchronisieren.

1. Wählen Sie im Visual Studio Code Terminal-Menü die Option **Neues Terminal** aus.

1. Geben Sie an der Terminal-Eingabeaufforderung den folgenden Befehl ein, um zu überprüfen, ob das .NET SDK ordnungsgemäß installiert ist:

    ```dotnetcli
    dotnet --version
    ```

    Wenn Sie eine Fehlermeldung erhalten, dass der Begriff „dotnet“ nicht erkannt wird, gehen Sie wie folgt vor:

    - Öffnen Sie im Windows-Startmenü Windows-**Einstellungen**.
    - Öffnen Sie unter Einstellungen die Registerkarte **Apps**, und wählen Sie dann **Installierte Apps** aus.
    - Suchen Sie in der Liste der installierten Apps nach **Microsoft .NET SDK 8.0.100 (x64)**.
    - Wählen Sie rechts neben Microsoft .NET SDK 8.0.100 (x64) die Auslassungspunkte (…) und dann **Ändern** aus.
    - Wählen Sie **Ja** aus, um der App das Vornehmen von Änderungen zu erlauben.
    - Wählen Sie im Microsoft .NET SDK 8.0.100-Fenster **Reparieren** aus.
    - Warten Sie, bis der Reparaturvorgang erfolgreich abgeschlossen ist, und wählen Sie dann **Schließen** aus.
    - Schließen Sie das Fenster Einstellungen.
    - Wechseln Sie zurück zum Visual Studio Code-Fenster und schließen Sie dann Visual Studio Code.
    - Öffnen Sie Visual Studio Code erneut.
    - Geben Sie im Visual Studio Code-Terminal in der Eingabeaufforderung `dotnet --version` ein.
    - Es sollte eine Versionsnummer angezeigt werden. Beispiele: **8.0.100**.

1. Verwenden Sie in der Terminal-Eingabeaufforderung den folgenden Befehl, um die Git-E-Mail-Einstellung zu konfigurieren:

    Geben Sie **git config --global user.email** gefolgt von den E-Mail-Informationen des Kontos in Ihrer Laborumgebung ein.

    Zum Beispiel: git config --global user.email LabUser-12345678@labhoster.onmicrosoft.com

1. Verwenden Sie in der Terminal-Eingabeaufforderung zum Konfigurieren des Git-Benutzernamens den folgenden Befehl:

    Geben Sie **git config --global user.name** gefolgt von dem Benutzernamen des Kontos in Ihrer Laborumgebung ein.

    Zum Beispiel: git config --global user.name LabUser-12345678

1. Wählen Sie im Menü „Ansicht“ die Option **Befehlspalette** aus.

1. Wählen Sie in der Eingabeaufforderung **.NET: Neues Projekt** aus und wählen Sie dann **ASP.NET Core Empty** aus.

1. Warten Sie, bis die Ressourcen geladen wurden, und geben Sie dann die folgenden Informationen ein:

    - Geben Sie im Textfeld „Neues Projekt benennen“ **AZ2003App** ein.
    - Übernehmen Sie das Standardverzeichnis.

1. Führen Sie in der Terminal-Eingabeaufforderung den folgenden dotnet CLI-Befehl aus:

    ```dotnetcli
    dotnet build
    ```

1. Erstellen Sie im Stammprojektordner eine GITIGNORE-Datei, welche die folgenden Informationen enthält:

    ```gitignore
    [Bb]in/
    [Oo]bj/
    ```

1. Wählen Sie im Menü „Datei“ **Alle speichern** aus.

1. Öffnen Sie die Ansicht „Quellcodeverwaltung“.

1. Geben Sie in das Textfeld „Commit Message“ (Commitnachricht) **Erster Commit** ein.

1. Wählen Sie **Commit** (Commit ausführen) und dann **Yes** (Ja) aus, um Änderungen zu stagen und zu committen.

1. Wählen Sie **Sync changes** (Änderungen synchronisieren) und dann **OK** aus, um Ihre Dateien mit dem DevOps-Repository zu synchronisieren.

1. Geben Sie im Dialogfeld „Git Credential Manager“ (Git Anmeldeinformationen-Manager) die Anmeldeinformationen (Benutzername und Kennwort) Ihrer Laborumgebung ein.

#### Aufgabe 3: Erstellen eines Docker-Image und Pushen des Image an Ihre Azure Container Registry

Führen Sie die folgenden Schritte aus, um ein Docker-Image zu erstellen und das Image an Ihre Azure-Containerregistrierung zu übertragen.

1. Stellen Sie sicher, dass Ihr Codeprojekt AZ2003 in Visual Studio Code geöffnet ist.

1. Führen Sie zum Erstellen einer Dockerfile-Datei in der Befehlspalette den folgenden Befehl aus: **Docker: Docker-Dateien dem Arbeitsbereich hinzufügen**.

1. Wenn Sie dazu aufgefordert werden, geben Sie die folgenden Informationen an:

    - Application Platform: **.NET ASP.NET Core**.
    - Betriebssystem: **Linux**.
    - Ports: **5000**.
    - Einschließen von Docker Compose-Dateien: **Nein**

1. Führen Sie zum Erstellen eines Docker-Image den folgenden Befehl in der Befehlspalette aus: **Docker-Images: Build-Image**.

1. Warten Sie, bis der Imagebuildvorgang abgeschlossen ist, und schließen Sie das Terminal.

1. Wählen Sie im linken Menü zum Öffnen der Docker-Ansicht Docker aus.

1. Wählen Sie in der DOCKER-Ansicht unter Registrierungen **Registrierung verbinden** aus, und wählen Sie dann **Azure Azure Container Registry** aus.

1. Erweitern Sie in der DOCKER-Ansicht **Azure** und wählen Sie dann **Zulassen** aus.

1. Wählen Sie im Browserfenster das Azure-Konto aus, das Sie für dieses Lab verwenden.

1. Kehren Sie zu Visual Studio Code zurück.

1. Erweitern Sie in der DOCKER-Ansicht das Azure-Abonnement, und vergewissern Sie sich, dass die von Ihnen erstellte Azure-Containerregistrierung aufgeführt ist.

1. Um das Docker-Image in die Azure Container Registry zu pushen, führen Sie die folgenden Befehle in der Befehlspalette aus: **Docker-Images: Push**.

1. Wenn der Befehl ausgeführt wird, führen Sie die folgenden Schritte aus:

    - Imagegruppe auswählen: Wählen Sie **az2003project** aus.
    - Image (Tag) auswählen: Wählen Sie das **Neuestes** aus.
    - Registrierungsanbieter auswählen: Wählen Sie **Azure** aus.
    - Wählen Sie Ihr Abonnement aus.
    - Azure Container Registry auswählen, in die gepusht werden soll: Wählen Sie die Containerregistrierung aus, die Sie erstellt haben. Zum Beispiel: acraz2003cah12oct.
    - Um das Image bereitzustellen, drücken Sie die Eingabetaste.

1. Warten Sie, bis der Image-Pushvorgang abgeschlossen ist, und schließen Sie das Terminal.

1. Öffnen Sie die Ansicht „Quellcodeverwaltung“, geben sie eine Commitnachricht ein und wählen Sie dann **Commit** und **Änderungen synchronisieren** aus.

#### Aufgabe 4: Erstellen einer Azure Pipeline mit dem Namen Pipeline1

Führen Sie die folgenden Schritte aus, um eine Azure-Pipeline mit dem Namen Pipeline1 zu erstellen.

1. Öffnen Sie das Azure DevOps-Projekt.

1. Wählen Sie im linken Menü **Pipelines** aus.

1. Wählen Sie **Pipeline erstellen** aus.

1. Wählen sie **Azure Repos Git** aus.

1. Wählen Sie auf der Seite „Repository auswählen“ **AZ2003Project** aus.

1. Wählen Sie **Starterpipeline** aus.

1. Wählen Sie unter „Speichern und Ausführen“ die Option **Speichern** und dann **Speichern** aus.

1. Führen Sie die folgenden Schritte aus, um den Namen Ihrer Pipeline in „Pipeline1“ zu ändern:

    1. Wählen Sie im linken Menü **Pipelines** aus.

    1. Wählen Sie rechts neben der AZ2003Project-Pipeline **Weitere Optionen** und dann **Umbenennen/Verschieben** aus.

    1. Geben Sie im Dialogfeld „Pipeline umbenennen/verschieben“ unter „Name“ **Pipeline1** ein und wählen Sie dann **Speichern** aus.

#### Aufgabe 5: Bereitstellen eines selbstgehosteten Windows-Agent

Für eine Azure-Pipeline zum Erstellen und Bereitstellen von Windows-, Azure- und anderen Visual Studio-Lösungen benötigen Sie mindestens einen Windows-Agent in der Hostumgebung.

Führen Sie die folgenden Schritte aus, um einen selbstgehosteten Windows-Agent bereitzustellen.

1. Navigieren Sie zur Startseite Ihrer DevOps-Organisation.

1. Wählen Sie in der oberen rechten Ecke **Benutzereinstellungen** aus.

1. Wählen Sie im Dialogfeld „Benutzereinstellungen“ **Persönliche Zugriffstoken** aus.

1. Um ein persönliches Zugriffstoken zu erstellen, wählen Sie die Option **+ Neues Token** aus.

1. Geben Sie unter Name den Wert **AZ2003** ein.

1. Wählen Sie unten im Fenster „Neues persönliches Zugriffstoken erstellen“ aus. Um die vollständige Liste der Bereiche anzuzeigen, wählen Sie die Option **Alle Bereiche anzeigen** aus.

1. Wählen Sie für den Bereich **Agentpools (lesen, verwalten)** und **Bereitstellungsgruppe (lesen, verwalten)** aus.

    Stellen Sie sicher, dass alle anderen Felder leer sind.

1. Klicken Sie auf **Erstellen**.

1. Um das Token zu kopieren, wählen Sie auf der Seite „Erfolg“ **In Zwischenablage kopieren** und dann **Schließen** aus.

1. Öffnen Sie den Editor und speichern Sie dann eine Kopie des Tokens im Editor.

    Sie verwenden dieses Token, wenn Sie den Agent konfigurieren.

1. Navigieren Sie zu Ihrer DevOps-Organisation und wählen Sie dann **Organisationseinstellungen** aus.

1. Wählen Sie im linken Menü unter „Pipelines“ die Option **Agentpools** aus.

1. Wenn sich das Dialogfeld **Agent abrufen** öffnet, fahren Sie mit dem nächsten Schritt fort, andernfalls führen Sie die folgenden Schritte aus:

    1. Um den Standardpool auszuwählen, wählen Sie **Standard** aus.

        Wenn der **Standardpool** nicht vorhanden ist, wählen Sie **Pool hinzufügen** aus und geben Sie dann die folgenden Informationen ein:

        1. Wählen Sie unter Pooltyp **selbst gehostet** aus.

        1. Geben Sie unter „Name“ **Standard** ein.

        1. Klicken Sie auf **Erstellen**.

        1. Um den soeben erstellten Pool zu öffnen, wählen Sie **Standard** aus.

    1. Wählen Sie unter „Standard“ die Registerkarte **Agents** und dann **Neuer Agent** aus.

1. Führen Sie im Dialogfeld Agent abrufen die folgenden Schritte aus:

    1. Wählen Sie die Registerkarte **Windows** aus.

    1. Wählen Sie im linken Bereich **x64** aus.

    1. Wählen Sie im rechten Bereich **Herunterladen** aus.

1. Warten Sie, bis der Vorgang abgeschlossen ist.

1. Schließen Sie das Dialogfeld „Agent abrufen“.

    In den nächsten Anleitungsschritten werden Sie durch den Prozess „Erstellen des Agents“ geführt.

1. Verwenden Sie den Windows Datei-Explorer, um den folgenden Ordnerspeicherort für den Agent zu erstellen:

    ```dos
    C:\agents
    ```

1. Verwenden Sie den Windows Datei-Explorer, um die heruntergeladene Agent-ZIP-Datei in das soeben erstellte Agents-Verzeichnis zu entpacken.

1. Warten Sie, bis der Dateiextraktionsvorgang abgeschlossen ist, und schließen Sie dann den Datei-Explorer.

1. Öffnen Sie die Windows PowerShell als Administrator*in, navigieren Sie zum Agents-Ordner und geben Sie dann den folgenden PowerShell-Befehl ein:

    ```powershell
    .\config
    ```

1. Antworten Sie wie folgt auf die Konfigurationsaufforderungen:

    - Eingeben der Server-URL >: Geben Sie die URL für Ihre DevOps-Organisation ein. Beispiel: `https://dev.azure.com/<your organization>`
    - Eingeben des Authentifizierungstyps (drücken Sie für PAT die Eingabetaste) >: Drücken Sie die Eingabetaste.
    - Eingeben des persönlichen Zugriffstokens >: Fügen Sie das persönliche Zugriffstoken ein, das Sie zuvor in den Editor kopiert haben.
    - Eingeben des Agentpools (drücken Sie für die Standardeinstellung die Eingabetaste) >: Drücken Sie die Eingabetaste.
    - Eingeben des Agentnamen (drücken Sie für „YOUR-PC-NAME“ die Eingabetaste) >: Geben Sie **az2003-agent** ein.
    - Eingeben des Arbeitsordner ein (drücken Sie für _work die Eingabetaste) >: Drücken Sie die Eingabetaste.
    - Agent als Dienst ausführen? (Y/N) (drücken Sie für N die Eingabetaste) >: Geben Sie **Y** ein.
    - Eingeben der Aktivierung von SERVICE_SID_TYPE_UNRESTRICTED für den Agentdienst (Y/N) (drücken Sie für N die Eingabetaste) >: Geben Sie **Y** ein.
    - Eingeben des Benutzerkontos, das für den Dienst verwendet werden soll (drücken Sie für NT AUTHORITY\NETWORK SERVICE die Eingabetaste) >: Drücken Sie die Eingabetaste.
    - Geben Sie an, ob der Dienst nicht sofort nach Abschluss der Konfiguration gestartet werden soll. (Y/N) (drücken Sie für N die Eingabetaste) >: Drücken Sie die Eingabetaste.

    Eine Meldung, die Sie informiert, dass der Agent erfolgreich gestartet wurde, wird angezeigt.

    Weitere Hilfe finden Sie in der folgenden Dokumentation: `https://learn.microsoft.com/azure/devops/pipelines/agents/windows-agent`

1. Schließen Sie Windows PowerShell.

### Übung 4: Konfigurieren der Azure Container Registry für eine sichere Verbindung mit Azure Container Apps

Sie wurden gebeten, Azure-Ressourcen zu konfigurieren, die die folgenden Anforderungen erfüllen:

- Ihre Ressourcengruppe muss eine vom Benutzer zugewiesene verwaltete Identität enthalten.
- Ihre Containerregistrierung muss in der Lage sein, die verwaltete Identität zum Abrufen von Artefakten zu verwenden.
- Der Zugriff für die verwaltete Identität muss mit dem Prinzip der geringsten Rechte eingeschränkt werden.
- Auf Ihre Containerregistrierung muss über einen privaten Endpunkt auf VNET1/PESubnet zugegriffen werden können.

In dieser Übung konfigurieren Sie eine Containerregistrierungsinstanz für eine sichere Verbindung mit einer Container-App.

Sie werden für die folgenden Schritte ungefähr 10 Minuten benötigen:

- Konfigurieren einer benutzerseitig zugewiesenen verwalteten Identität
- Konfigurieren Sie Ihre Containerregistrierung mit AcrPull-Berechtigungen für die verwaltete Identität.
- Konfigurieren Sie Ihre Containerregistrierung mit einer privaten Endpunktverbindung.

#### Aufgabe 1: Konfigurieren einer benutzerseitig zugewiesenen verwalteten Identität

Führen Sie die folgenden Schritte aus, um eine vom Benutzer zugewiesene verwaltete Identität zu konfigurieren.

1. Öffnen Sie Ihr Azure-Portal.

1. Geben Sie in der oberen Suchleiste des Azure-Portals **verwaltete Identität** ein.

1. Wählen Sie in der gefilterten Liste der Ressourcen die Option **Vom Benutzer zugewiesene verwaltete Identität** aus.

1. Geben Sie auf der Seite „Vom Benutzer zugewiesene verwaltete Identität erstellen“ die folgenden Informationen an:

    - Abonnement: Geben Sie das Azure-Abonnement an, das Sie für dieses angeleitete Projekt verwenden.
    - Ressourcengruppe: **RG1**
    - Region: Geben Sie die Region ein, die der Regionseinstellung Ihrer Ressourcengruppe entspricht.
    - Name: **uai-az2003**

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Warten Sie, während die Einstellungen überprüft werden, und wählen Sie dann **Erstellen** aus.

1. Schließen Sie die Seite „Verwaltete Identität“.

#### Aufgabe 2: Konfigurieren Ihrer Containerregistrierung mit AcrPull-Berechtigungen für die verwaltete Identität

Führen Sie die folgenden Schritte aus, um die Containerregistrierung mit AcrPull-Berechtigungen für die verwaltete Identität zu konfigurieren.

1. Öffnen Sie im Azure-Portal Ihre Containerregistrierungsressource.

1. Wählen Sie im Menü auf der linken Seite **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie auf der Seite Access Control (IAM) die Option **Rollenzuweisung hinzufügen** aus.

1. Suchen Sie nach der AcrPull-Rolle und wählen Sie dann **AcrPull** aus.

1. Wählen Sie **Weiter** aus.

1. Wählen Sie auf der Registerkarte „Mitglieder“ rechts neben „Zugriff zuweisen“ die Option **Verwaltete Identität** aus.

1. Wählen Sie **+ Mitglieder auswählen** aus.

1. Wählen Sie auf der Seite „Verwaltete Identitäten auswählen“ unter "Verwaltete Identität" die Option **Vom Benutzer zugewiesene verwaltete Identität** aus und wählen Sie dann die für dieses Projekt erstellte verwaltete Identität aus.

    Zum Beispiel: uai-az2003.

1. Wählen Sie auf der Seite „Verwaltete Identitäten auswählen“ die Option **Auswählen** aus.

1. Wählen Sie auf der Registerkarte „Mitglieder“ auf der Seite „Rollenzuweisung hinzufügen“ die Option **Überprüfen und Zuweisen** aus.

1. Klicken Sie auf der Registerkarte „Überprüfen + zuweisen“ auf die Option **Überprüfen + zuweisen**.

1. Warten Sie, bis die Rollenzuweisung hinzugefügt ist.

    Es wird eine Benachrichtigung angezeigt. Wenn Sie sie verpassen, können Sie auf der Registerkarte „Rollenzuweisungen“ überprüfen, ob uai-az2003 der Rolle „AcrPull“ zugewiesen wurde.

#### Aufgabe 3: Konfigurieren Ihrer Containerregistrierung mit einer privaten Endpunktverbindung

Führen Sie die folgenden Schritte aus, um Ihre Containerregistrierung mit einer privaten Endpunktverbindung zu konfigurieren.

1. Stellen Sie sicher, dass Ihre Containerregistrierungsressource im Portal geöffnet ist.

1. Wählen Sie unter Einstellungen die Option **Netzwerk** aus.

1. Wählen Sie auf der Registerkarte „Privater Zugriff“ die Option **+ Private Endpunktverbindung erstellen** aus.

1. Geben Sie auf der Registerkarte „Grundlagen“ unter „Projektdetails“ die folgenden Informationen an:

    - Abonnement: Geben Sie das Azure-Abonnement an, das Sie für dieses angeleitete Projekt verwenden.
    - Ressourcengruppe: **RG1**
    - Name: **pe-acr-az2003**
    - Region: Stellen Sie sicher, dass die angegebene Region mit der Regionseinstellung Ihrer Ressourcengruppe übereinstimmt.

1. Klicken Sie auf **Weiter: Ressource** aus.

1. Stellen Sie auf der Registerkarte „Ressource“ sicher, dass die folgenden Informationen angezeigt werden:

    - Abonnement: Stellen Sie sicher, dass das Azure-Abonnement, das Sie für dieses angeleitete Projekt verwenden, ausgewählt ist.
    - Ressourcentyp: Stellen Sie sicher, dass **Microsoft.ContainerRegistry/registries** ausgewählt ist.
    - Resource (Ressourcen): Stellen Sie sicher, dass der Name Ihrer Registrierung ausgewählt ist.
    - Zielunterressource: Stellen Sie sicher, dass die **Registrierung** ausgewählt ist.

1. Wählen Sie **Weiter: Virtuelles Netzwerk** aus.

1. Stellen Sie auf der Registerkarte „Virtuelles Netzwerk“ unter „Netzwerk“ sicher, dass die folgenden Informationen angezeigt werden:

    - Virtuelles Netzwerk: Stellen Sie sicher, dass VNET1 ausgewählt ist.
    - Subnetz: Stellen Sie sicher, dass PESubnet ausgewählt ist.

1. Wählen Sie **Weiter: DNS** aus.

1. Stellen Sie auf der Registerkarte „DNS“ unter „Private DNS-Integration“ sicher, dass die folgenden Informationen angezeigt werden:

    - Integration in eine private DNS-Zone: Vergewissern Sie sich, dass **Ja** ausgewählt ist.
    - Private DNS-Zone: Beachten Sie, dass **(neu) privatelink.azurecr.io** angegeben ist.

1. Klicken Sie auf **Weiter: Tags**.

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

1. Wählen Sie auf der Registerkarte „Überprüfen und Erstellen“ die Option **Erstellen** aus, wenn die Nachricht „Überprüfung bestanden“ angezeigt wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

1. Wenn sie sehen, dass **Ihre Bereitstellung abgeschlossen ist**, schließen Sie die Bereitstellungsseite für private Endpunkte.

### Übung 5: Erstellen und Konfigurieren einer Containerapp in Azure Container Apps

Sie wurden gebeten, eine Container-App zu konfigurieren, die die folgenden Anforderungen erfüllt:

- Wird in VNET1/ACASubnet bereitgestellt
- Ruft ein Image aus einer Containerregistrierung ab
- Authentifizierung mithilfe einer benutzerseitig zugewiesenen verwalteten Identität (uai-az2003)
- Verwendet eine Container-App zum Herstellen einer Verbindung mit einer Service Bus-Instanz mithilfe des .NET-Clienttyps
- Die App kann bis zu zwei Replikate ausführen, die hinzugefügt werden, wenn 1000 gleichzeitige HTTP-Anforderungen erfolgen.

In dieser Übung stellen Sie eine Container-App aus einem Image in der Azure Container Registry in die Azure Container Apps-Plattform bereit.

Sie werden für die folgenden Schritte ungefähr 20 bis 25 Minuten benötigen:

- Erstellen einer Container-App, die ein Azure Container Registry-Image verwendet
- Konfigurieren der Container-App für die Authentifizierung mithilfe der benutzerseitig zugewiesenen Identität
- Konfigurieren einer Verbindung zwischen der Container-App und Service Bus
- Konfigurieren von HTTP-Skalierungsregeln

#### Aufgabe 1:  Erstellen einer Container-App, die ein Azure Container Registry-Image verwendet

Führen Sie die folgenden Schritte aus, um eine Container-App zu erstellen, die ein Azure Container Registry-Image verwendet.

1. Geben Sie in der oberen Suchleiste des Azure-Portals **Container-App** ein.

1. Wählen Sie in der gefilterten Liste der Ressourcen **Container-Apps** aus.

1. Wählen Sie auf der Seite „Container Apps“ die Option **Container-App erstellen** aus.

1. Geben Sie auf der Registerkarte „Grundeinstellungen“ Folgendes an:

    - Abonnement: Wählen Sie das Azure-Abonnement aus, das Sie für dieses angeleitete Projekt verwenden.
    - Ressourcengruppe: **RG1**
    - Name der Container-App: **aca-az2003**
    - Region: Stellen Sie sicher, dass die angegebene Region mit der Regionseinstellung von VNET1 Ihrer Ressourcengruppe übereinstimmt (dies sollte auch dem Standort Ihrer Ressourcengruppe entsprechen).

        Die Container-App muss sich in derselben Region und am gleichen Standort wie das virtuelle Netzwerk befinden, damit Sie VNET1 für die verwaltete Umgebung auswählen können. Behalten Sie für dieses angeleitete Projekt alle Ressourcen in der Region bzw. dem Standort für Ihre Ressourcengruppe bei.

    - Container Apps-Umgebung: Wählen Sie **Neu erstellen** aus.

1. Wählen Sie auf der Seite „Container Apps-Umgebung erstellen“ die Registerkarte **Netzwerk** aus, und geben Sie dann Folgendes an:

    - Eigenes virtuelles Netzwerk verwenden: Klicken Sie auf **Ja**.
    - Virtuelles Netzwerk: Wählen Sie **VNET1** aus.
    - Infrastruktursubnetz: **ACASubnet**

    > [!NOTE]
    > Wenn das Subnetz „ACASubnet“ nicht aufgeführt ist, brechen Sie diesen Erstellungsprozess ab, öffnen Sie Ihre VNet-Ressource, passen Sie den ACASubnetzadressenbereich auf **10.0.2.0/23** an, und starten Sie die Schritte zum Erstellen der Container-App-Ressource neu.

1. Wählen Sie auf der Seite „Container Apps-Umgebung erstellen“ die Option **Erstellen** aus.

1. Wählen Sie auf der Seite „Container-App erstellen“ die Registerkarte „Container“ aus, und geben Sie dann Folgendes an:

    - Schnellstartimage verwenden: **Deaktivieren** Sie diese Einstellung.
    - Name: Stellen Sie sicher, dass **aca-az2003** angegeben ist.
    - Imagequelle: Stellen Sie sicher, dass **Azure Container Registry** ausgewählt ist.
    - Registrierung: Wählen Sie Ihre Containerregistrierung aus. Zum Beispiel: **acraz2003cah12oct.azurecr.io**
    - Bild: Wählen Sie **az2003project** aus.
    - Imagetag: Wählen Sie **latest** (neueste) aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Nachdem die Überprüfung erfolgreich abgeschlossen wurde, wählen Sie **Erstellen** aus.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

    > [!NOTE]
    > Diese Bereitstellung dauert etwa 5 bis 10 Minuten.

#### Aufgabe 2: Konfigurieren der Container-App für die Authentifizierung mithilfe der benutzerseitig zugewiesenen Identität

Führen Sie die folgenden Schritte aus, um die Container-App so zu konfigurieren, dass sie mithilfe der benutzerseitig zugewiesenen Identität authentifiziert wird.

1. Öffnen Sie im Azure-Portal die von Ihnen erstellte Container-App.

1. Wählen Sie unter „Einstellungen“ die Option **Identitäts** aus.

1. Wählen Sie die Registerkarte für **Benutzerseitig zugewiesen** aus.

1. Wählen Sie **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** aus.

1. Wählen Sie auf der Seite „Benutzerseitig zugewiesene verwaltete Identität hinzufügen“ zuerst **uai-az2003** und dann **Hinzufügen** aus.

#### Aufgabe 3: Konfigurieren einer Verbindung zwischen der Container-App und Service Bus

Führen Sie die folgenden Schritte aus, um eine Verbindung zwischen der Container-App und Service Bus zu konfigurieren.

1. Stellen Sie im Azure-Portal sicher, dass Ihre Container-App geöffnet ist.

1. Wählen Sie unter „Einstellungen“ die Option **Dienstconnector (Vorschau)** aus.

1. Wählen Sie **Mit Ihren Diensten verbinden** aus.

1. Geben Sie auf der Seite „Verbindung erstellen“ Folgendes an:

    - Diensttyp: Wählen Sie **Services Bus** aus.
    - Clienttyp: Wählen Sie **.NET** aus.

1. Wählen Sie **Weiter: Authentifizierung** aus.

1. Wählen Sie auf der Registerkarte „Authentifizierung“ **Benutzerseitig zugewiesene verwaltete Identität** aus.

1. Um Registerkarten zu ändern, wählen Sie **Überprüfen und erstellen** aus.

1. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

1. Warten Sie, bis die Verbindung hergestellt wurde.

     Die Service Bus-Verbindung wird auf der Seite „Dienstconnector (Preview)“ aufgeführt.

#### Aufgabe 4: Konfigurieren von HTTP-Skalierungsregeln

Führen Sie die folgenden Schritte aus, um HTTP-Skalierungsregeln für Ihre Container-App zu konfigurieren.

1. Stellen Sie sicher, dass Ihre Container-App im Portal geöffnet ist.

1. Wählen Sie im linken Menü unter „Anwendung“ die Option **Überarbeitungen und Replikate** aus.

1. Beachten Sie den Namen, der Ihrer aktiven Überarbeitung zugewiesen ist.

1. Wählen Sie im linken Menü unter „Anwendung“ die Option **Skalieren** aus.

1. Wählen Sie oben auf der Seite **Bearbeiten und bereitstellen** aus.

1. Wählen Sie unten auf der Seite die Option **Weiter: Skalieren** aus.

1. Konfigurieren Sie „Min./max. Anzahl von Replikaten“ folgendermaßen:

    - Mindestzahl von Replikaten: 0
    - Höchstzahl von Replikaten: 2

1. Wählen Sie unter „Skalierungsregel“ die Option **+ Hinzufügen** aus.

1. Geben Sie auf der Seite „Skalierungsregel hinzufügen“ Folgendes an:

    - Regelname: Geben Sie **scalerule-http** ein.
    - Geben Sie Folgendes ein: Wählen Sie **HTTP-Skalierung** aus.
    - Gleichzeitige Anforderungen: Legen Sie den Wert auf **1000** fest.

1. Wählen Sie auf der Seite „Skalierungsregel hinzufügen“ die Option **Hinzufügen** aus.

1. Wählen Sie auf der Seite „Erstellen und Bereitstellen einer neuen Revision“ die Option **Erstellen** aus.

1. Vergewissern Sie sich, dass die neue Skalierungsregel angezeigt wird.

    Wenn die Skalierungsregel nach dem Aktualisieren nicht angezeigt wird, überprüfen Sie die Registerkarte „Überarbeitungen“, um die aktuelle aktive Überarbeitung anzuzeigen, und passen Sie die ausgewählte Überarbeitung bei Bedarf auf der Seite „Skalierung“ und „Replikate“ an.

### Übung 6: Konfigurieren der Continuous Integration mithilfe von Azure Pipelines

Sie wurden aufgefordert, eine kontinuierliche Integrationsumgebung für Container-Apps zu konfigurieren, welche die folgenden Anforderungen erfüllt:

- Sie benötigen eine Azure Container Apps-Bereitstellungsaufgabe in Ihrer ADO-Umgebung.
- Pipeline1 muss ein Containerimage aus Ihrer Containerregistrierung mithilfe eines selbstgehosteten Agentpools in Ihrer Container-App bereitstellen.
- Sie müssen sicherstellen, dass die Pipeline das Image mindestens einmal bereitstellt.

In dieser Übung stellen Sie eine Container-App aus einem Image in der Azure Container Registry in der Azure Container Apps-Plattform bereit.

Sie werden für die folgenden Schritte ungefähr 10 Minuten benötigen:

- Konfigurieren Sie Pipeline1 für die Verwendung des selbstgehosteten Agentpools.
- Konfigurieren Sie Pipeline1 mit einer Bereitstellungsaufgabe für Azure Container Apps.
- Führen Sie die Pipeline1-Bereitstellungsaufgabe aus.

#### Aufgabe 1: Konfigurieren von Pipeline1 für die Verwendung des selbstgehosteten Agentpools

Führen Sie die folgenden Schritte aus, um Ihre Pipelines für die Verwendung des selbstgehosteten Agentpools zu konfigurieren.

1. Stellen Sie sicher, dass Ihre Azure DevOps-Organisation in einer eigenen Browserregisterkarte geöffnet ist.

    Öffnen Sie wenn nötig ein neues Browsertab, navigieren Sie zu `https://dev.azure.com` und öffnen Sie dann Ihre Azure DevOps-Organisation.

1. Wählen Sie **AZ2003Project** auf Ihrer Azure DevOps-Seite aus, um Ihr DevOps-Projekt zu öffnen.

1. Wählen Sie im linken Menü **Pipelines** aus.

1. Wählen Sie **Pipeline1** und wählen Sie dann **Bearbeiten** aus.

1. Um den selbst gehosteten Agentpool zu verwenden, aktualisieren Sie die Datei azure-pipelines.yml, wie im folgenden Beispiel gezeigt:

    ```yml
    trigger:
    - main

    pool:
      name: default

    steps:

    ```

1. Wählen Sie **Speichern**.

1. Geben Sie eine Commit-Nachricht ein und wählen Sie dann **Speichern** aus.

#### Aufgabe 2: Konfigurieren von Pipeline1 mit einer Bereitstellungsaufgabe für Azure Container Apps

Führen Sie die folgenden Schritte aus, um Pipeline1 mit einer Azure Container Apps-Bereitstellungsaufgabe zu konfigurieren.

1. Stellen Sie sicher, dass Sie Pipeline1 zur Bearbeitung geöffnet haben.

1. Geben Sie auf der rechten Seite unter „Aufgaben“ im Feld „Suchaufgaben“ den **Azure-Container** ein.

1. Wählen Sie in der gefilterten Liste der Aufgaben die Option **Bereitstellen von Azure-Container-Apps** aus.

1. Wählen Sie unter Azure Resource Manager-Verbindung das Abonnement aus, das Sie verwenden, und wählen Sie dann **Autorisieren** aus.

1. Öffnen Sie auf der Registerkarte „Azure-Portal“ Ihre Container-App-Ressource und öffnen Sie dann die Seite „Container“.

1. Kopieren Sie die folgenden Informationen in den Editor.

    - Name
    - Registrierung
    - Abbildung
    - Imagetag

1. Verwenden Sie die Informationen, die Sie von der Seite „Container“ kopiert haben, um die folgenden-Aufgabeninformationsfelder zu konfigurieren:

    - Docker Image zur Bereitstellung: Registrierung/Image:Image-Tag (setzen Sie Ihre Informationen aus dem Editor ein)
    - Name der Azure Container-App: Name (setzen Sie Ihre Informationen aus dem Editor ein)

    Beispiel:

    - Docker Image zur Bereitstellung: acraz2003cah12oct.azurecr.io/az2003project:latest
    - Name der Azure Container-App: aca-az2003

1. Geben Sie im Feld „Name der Azure Ressourcengruppe“ **RG1** ein.

    > [!NOTE]
    > Wenn Sie den Ressourcengruppennamen überprüfen müssen, finden Sie ihn auf der Seite „Übersicht“ Ihrer Container-App-Ressource.

1. Wählen Sie auf der Seite „Bereitstellen von Azure-Container-Apps“ die Option **Hinzufügen** aus.

    Die Yaml-Datei für Ihre Pipeline sollte jetzt die AzureContainerApps-Aufgaben wie folgt enthalten:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: '<Subscription>(<Subscription ID>)'
        imageToDeploy: '<Registry>/<Image>:<Image tag>' from Container App resource
        containerAppName: '<Name>' from Container App resource 
        resourceGroup: '<resource group name>'
    
    ```

    Hier ist ein Beispiel, das einen YAML-Konfigurationsausschnitt zeigt:

    ```yml
    trigger:
    - main
    pool:
      name: default
    steps:
    - task: AzureContainerApps@1
      inputs:
        azureSubscription: 'Visual Studio Enterprise(1111aaaa-22bb-33cc-44dd-555555eeeeee)'
        imageToDeploy: 'acraz2003cah12oct.azurecr.io/aspnetcorecontainer:latest'
        containerAppName: 'aca-az2003'
        resourceGroup: 'RG1'
    ```

1. Wählen Sie auf der Seite Ihrer „Pipeline1“ die Option **Speichern** aus, geben Sie eine Commitnachricht ein, und wählen Sie dann **Speichern** erneut aus, um einen Commit auszuführen.

#### Aufgabe 3: Ausführen der Pipeline1-Bereitstellungsaufgabe

Führen Sie die folgenden Schritte aus, um die Pipeline1-Bereitstellungsaufgabe auszuführen.

1. Stellen Sie sicher, dass Sie Pipeline1 in Azure DevOps geöffnet haben.

1. Um die AzureContainerApps-Aufgabe auszuführen, wählen Sie **Ausführen** aus.

1. Wählen Sie auf der Seite „Pipeline ausführen“ die Option **Ausführen** aus.

    Eine Pipelineseite wird geöffnet, um den zugeordneten Auftrag anzuzeigen. Der Auftragsabschnitt zeigt den Auftragsstatus an, der von „In der Warteschlange“ zu „Warten“ wechselt.

1. Überprüfen Sie, ob unter „Auftrag“ „Berechtigung erforderlich“ angezeigt wird.

    Wenn für den Auftrag die Berechtigung zum Fortfahren erforderlich ist, wählen Sie **Ansicht** und dann **Zulassen** aus, um die erforderlichen Berechtigungen bereitzustellen.

1. Überwachen Sie den Status des Ausführungsvorgangs, und stellen Sie sicher, dass die Ausführung erfolgreich ist.

    Es kann einige Minuten dauern, bis der Auftrag in der Warteschlange ausgeführt wird. Nach etwa einer Minute sollte der Auftragsstatus von „In Ausführung“ zu „Erfolg“ geändert werden.

### Übung 7: Verwalten von Überarbeitungen in Azure Container Apps

Sie wurden aufgefordert, die Datenverkehrsteilung für Ihre Container-Apps so zu konfigurieren, dass sie die folgenden Anforderungen erfüllen:

- Sie müssen eine neue Revision der Container-App erstellen, die als Zusatz v2 verwendet.
- Sie müssen sicherstellen, dass 25 Prozent der Anforderungen an Ihre App an die v2-Revision weitergeleitet werden.
- Sie müssen die Überarbeitung als „aktuell“ und „aktualisiert“ benennen und sicherstellen, dass die Anforderungen an die Revision „---updated“ an die Überarbeitung mit der Bezeichnung v2 weitergeleitet werden.

In dieser Übung stellen Sie eine neue Revision Ihrer Container-App bereit und konfigurieren die Datenverkehrsteilung zwischen zwei beschrifteten Revisionen.

Sie werden für die folgenden Schritte ungefähr 5 bis 10 Minuten benötigen:

- Legen Sie die Revisionsverwaltung auf mehrere fest.
- Erstellen Sie eine neue Revision mit einem v2-Zusatz.
- Konfigurieren Sie Bezeichnungen für die Revisionen.
- Konfigurieren Sie einen Datenverkehrsprozentsatz für die Revisionen.

#### Aufgabe 1: Festlegen der Überarbeitungsverwaltung auf „Mehrfach“

Führen Sie die folgenden Schritte aus, um die Überarbeitungsverwaltung auf „Mehrfach“ festzulegen.

1. Öffnen Sie im Azure-Portal Ihre Container-App-Ressource.

1. Wählen Sie im linken Menü unter „Anwendung“ die Option **Überarbeitungen und Replikate** aus.

1. Wählen Sie oben auf der Seite „Revisionen“ die Option **Revisionsmodus auswählen** aus.

1. Wählen Sie **Bestätigen** aus, um von einem einzelnen in den Modus für mehrere Revisionen zu wechseln.

1. Warten Sie auf der Seite „Revisionen“, bis die Einstellung für den **Revisionsmodus** aktualisiert wird.

    Der Überarbeitungsmodus wird nach dem Update auf **Mehrfach** festgelegt.

#### Aufgabe 2: Erstellen einer neuen Überarbeitung mit einem v2-Suffix

Führen Sie die folgenden Schritte aus, um eine neue Überarbeitung mit einem v2-Suffix zu erstellen.

1. Stellen Sie im Azure-Portal sicher, dass die Seite „Revisionen“ der Container-App-Ressource geöffnet ist.

1. Wählen Sie oben auf der Seite **+ Neue Revision erstellen**.

1. Führen Sie auf der Seite „Neue Revision erstellen und bereitstellen“ die folgenden Schritte aus:

    - Name / Suffix: Geben Sie **v2** ein.
    - Wählen Sie unter „Containerimage“ Ihr Containerimage aus. Zum Beispiel: aca-az2003.

1. Klicken Sie auf **Erstellen**.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

#### Aufgabe 3: Konfigurieren von Bezeichnungen für die Überarbeitungen

Eingehender Datenverkehr muss aktiviert sein, bevor Sie Überarbeitungsbezeichnungen oder die Datenverkehrsteilung konfigurieren können.

Führen Sie die folgenden Schritte aus, um Bezeichnungen für die Überarbeitungen zu konfigurieren.

1. Wählen Sie im linken Menü unter „Einstellungen“ die Option **Eingang** aus.

1. Wenn „Eingang“ nicht aktiviert ist, wählen Sie **Aktiviert** aus.

1. Geben Sie auf der Seite „Eingang“ die folgenden Informationen an:

    - Eingehender Datenverkehr: Wählen Sie **Datenverkehr von überall annehmen** aus.

    - Eingangstyp: Wählen Sie **HTTP** aus.

    - Clientzertifikatmodus: Stellen Sie sicher, dass **Ignorieren** ausgewählt ist.

    - Transport: Stellen Sie sicher, dass **Auto** ausgewählt ist.

    - Unsichere Verbindungen: Stellen Sie sicher, dass „Zulässig“ **NICHT** aktiviert ist.

    - Zielport: **5000** eingeben

    - Modus für IP-Sicherheitseinschränkungen: Stellen Sie sicher, dass **Allen Datenverkehr zulassen** ausgewählt ist.

1. Wählen Sie unten auf der Seite „Eingang“ die Option **Speichern** aus und warten Sie dann, bis das Update abgeschlossen ist.

1. Wählen Sie im linken Menü unter „Überarbeitungen“ die Option **Überarbeitungen und Replikate** aus.

1. Geben Sie für die v2-Revision unter „Bezeichnung“ den Eintrag **Aktualisiert** ein.

1. Geben Sie für die andere Revision den **aktuellen** Wert ein.

1. Klicken Sie am oberen Rand der Seite auf **Speichern**.

1. Warten Sie, bis die Einstellung für den eingehenden Datenverkehr aktualisiert wird.

#### Aufgabe 4: Konfigurieren eines Datenverkehrsprozentsatzes für die Überarbeitungen

Führen Sie die folgenden Schritte aus, um einen Datenverkehrsprozentsatz für die Überarbeitungen zu konfigurieren.

1. Stellen Sie sicher, dass die Seite „Revisionen“ geöffnet ist.

1. Geben Sie für die v2-Revision unter „Datenverkehr“ **25** als Prozentsatz ein.

1. Geben Sie für die andere Revision unter „Datenverkehr“ **75** als Prozentsatz ein.

1. Klicken Sie am oberen Rand der Seite auf **Speichern**.
