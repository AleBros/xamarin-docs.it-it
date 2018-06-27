---
title: Disinstallazione di Xamarin
description: Questo documento descrive come disinstallare Xamarin in Mac e Windows. Fornisce istruzioni specifiche sulla disinstallazione di Mono, Xamarin.Android, Xamarin.iOS e altri strumenti.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: d5cf15b8ecd225fb75a3cfa0017cb84bc13cce1b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782021"
---
# <a name="uninstalling-xamarin"></a>Disinstallazione di Xamarin

Questa guida descrive come rimuovere Xamarin da macOS o da Visual Studio in Windows.

Se è necessario reinstallare Xamarin usando Universal Installer, è consigliabile riavviare sempre prima il computer.

## <a name="uninstalling-xamarin-on-macos"></a>Disinstallazione di Xamarin in macOS

Questa guida può essere usata per disinstallare singolarmente ogni prodotto spostandosi sulla sezione relativa. Seguendo questa guida è possibile disinstallare l'intero set di strumenti di Xamarin, inclusi i prodotti riportati nell'elenco:

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [Inspector e Workbooks](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Programma di installazione](#uninstallinstaller)

> [!TIP]
> È disponibile uno [script di disinstallazione](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) da usare quando si rimuove Xamarin dal computer macOS. Per altre informazioni sull'uso dello script, vedere la sezione [Uso dello script di disinstallazione](#uninstallscript) in questa guida.

### <a name="uninstalling-visual-studio-for-mac"></a>Disinstallazione di Visual Studio per Mac

Il primo passaggio della procedura di disinstallazione di Xamarin da un Mac consiste nell'individuare **Visual Studio.app** nella directory **/Applications** e trascinarla nel **Cestino**. In alternativa, fare clic con il pulsante destro del mouse e scegliere **Sposta nel cestino** come illustrato nella figura seguente:

![Spostare l'applicazione Visual Studio nel cestino](uninstalling-xamarin-images/uninstall-image1.png)

L'eliminazione di questo bundle dell'app causa la rimozione di Visual Studio per Mac, anche se possono rimanere nel file system altri file correlati con Xamarin.

Per rimuovere tutte le tracce di Visual Studio per Mac, eseguire i comandi seguenti in Terminal:

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> Per informazioni sulla disinstallazione di Visual Studio per Mac, fare riferimento alla [Guida alla disinstallazione](https://docs.microsoft.com/visualstudio/mac/uninstall) in docs.microsoft.com.

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Disinstallare Mono SDK (MDK)

Mono è un'implementazione open source di Microsoft .NET Framework ed è usato da tutti i prodotti Xamarin (Xamarin.iOS, Xamarin.Android e Xamarin.Mac) per consentire lo sviluppo per queste piattaforme in C#.

> [!WARNING]
> Vi sono altre applicazioni oltre a Xamarin che usano Mono, ad esempio Unity. Prima di disinstallare Mono, assicurarsi che non vi siano altre dipendenze.

Per rimuovere Mono Framework, eseguire i comandi seguenti in Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Disinstallare Xamarin.Android

Diversi elementi il cui uso è obbligatorio quando si usa Xamarin.Android, ad esempio Android SDK e Java SDK, dovranno essere rimossi durante la disinstallazione di Xamarin.Android. Questa sezione spiega come disinstallare tutte le parti necessarie.

Per rimuovere Xamarin.Android, usare i comandi seguenti in Terminal:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Disinstallare Android SDK e Java SDK

Android SDK è richiesto per lo sviluppo di applicazioni Android. Per rimuovere completamente tutte le parti di Android SDK, individuare il file in **~/Library/Developer/Xamarin/** e spostarlo nel **Cestino**.

Non è necessario disinstallare Java SDK (JDK) poiché è già inserito nel pacchetto come parte di Mac OS X.

#### <a name="uninstall-android-avd"></a>Disinstallare Android AVD

> [!WARNING]
> Vi sono altre applicazioni al di fuori di Visual Studio per Mac che usano Android AVD e questi componenti di Android aggiuntivi, quali Android Studio.
> La rimozione di questa directory può causare l'interruzione di progetti in Android Studio. 

Per rimuovere tutti gli Android AVD e i componenti di Android aggiuntivi, usare il comando seguente:

```bash
rm -rf ~/.android
```

Per rimuovere solo gli Android AVD, usare il comando seguente:

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Disinstallare Xamarin.iOS

Xamarin.iOS consente di sviluppare applicazioni iOS usando C# o F#. Per disinstallare Xamarin.iOS da un computer, seguire questa procedura:

Per rimuovere tutti i file di Xamarin.iOS, usare i comandi seguenti in Terminal:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Disinstallare Xamarin.Mac

È possibile rimuovere dal computer Xamarin.Mac usando i due comandi seguenti per eliminare rispettivamente il prodotto e la licenza dal Mac:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks-and-inspector"></a>Disinstallare Workbooks e Inspector

Per rimuovere Xamarin Inspector e Workbooks 1.2.2 e versioni successive, usare i comandi seguenti in Terminal:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Per le versioni precedenti, vedere la guida per la disinstallazione di [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>Disinstallare Xamarin Profiler

Per rimuovere Xamarin Profiler, usare i comandi seguenti in Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>Disinstallare il programma di installazione di Xamarin

Per rimuovere tutte le tracce di Xamarin Universal Installer, usare i comandi seguenti:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Uso dello script di disinstallazione

Questo script di disinstallazione consente di disinstallare Visual Studio per Mac e i componenti di Xamarin associati in un'unica operazione.

Lo script contiene la maggior parte dei comandi che si trovano nell'articolo. Due elementi principali sono stati omessi dallo script, a causa di possibili dipendenze esterne:

- Disinstallazione di Mono
- Disinstallazione di Android AVD

Per eseguire lo script, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sullo script e selezionare Salva con nome per salvare il file sul Mac.

2.  Aprire **Terminale** e modificare la directory di lavoro in cui lo script è stato scaricato:

        $ cd /location/of/file

3. Rendere eseguibile lo script ed eseguirlo con **sudo**:

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

4. Infine, eliminare lo script di disinstallazione.

A questo punto, Xamarin dovrebbe essere disinstallato dal computer.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Disinstallazione di Xamarin in Windows

Xamarin è supportato in:

- [Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [**non supportato**]
- [Xamarin Studio](#uninstallxamarinstudio) [**non supportato**]

<a name="uninstallvs2017" />

### <a name="visual-studio-2017"></a>Visual Studio 2017

Xamarin può essere disinstallato da Visual Studio 2017 usando l'app del programma di installazione:

1. Usare il **menu Start** per aprire il **programma di installazione di Visual Studio**.

2. Fare clic sul pulsante **Modifica** per l'istanza che si vuole modificare.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Fare clic sul pulsante Modifica")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Nella scheda **Carichi di lavoro** deselezionare l'opzione **Sviluppo di applicazioni per dispositivi mobili con .NET** (nella sezione **Dispositivi mobili e giochi**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Deselezionare il carico di lavoro Sviluppo di applicazioni per dispositivi mobili")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Fare clic sul pulsante **Modifica** in basso a destra nella finestra.

5. Il programma di installazione rimuoverà i componenti deselezionati (Visual Studio 2017 deve essere chiuso prima che il programma di installazione possa apportare modifiche).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Fare clic sul pulsante Modifica")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

I singoli componenti di Xamarin, ad esempio Profiler o Workbooks, possono essere disinstallati passando alla scheda **Singoli componenti** nel passaggio 3 e deselezionando i componenti specifici:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Disinstallare i singoli componenti")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Per disinstallare completamente Visual Studio 2017, scegliere **Disinstalla** dal menu con le tre barre accanto al pulsante **Avvia**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Disinstallare completamente Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Se si dispone di due (o più) istanze di Visual Studio installate side-by-side (SxS), ad esempio una versione di rilascio e una di anteprima, la disinstallazione di un'istanza potrebbe rimuovere alcune funzionalità di Xamarin dalle altre istanze di Visual Studio, tra cui:
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remoted iOS Simulator
> - Apple Bonjour SDK
>
> In determinate condizioni la disinstallazione di una delle istanze SxS può comportare la rimozione non corretta di queste funzionalità. Ciò può peggiorare le prestazioni della piattaforma Xamarin nelle istanze di Visual Studio che rimangono nel sistema dopo la disinstallazione dell'istanza SxS.
>
>Questo problema si risolve eseguendo l'opzione di **ripristino** nel programma di installazione di Visual Studio, che consente di reinstallare i componenti mancanti.


## <a name="uninstalling-older-and-unsupported-products"></a>Disinstallazione di prodotti precedenti e non supportati

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e versioni precedenti

Per disinstallare completamente Visual Studio 2015, usare [la risposta del supporto in visualstudio.com](https://www.visualstudio.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Xamarin può essere disinstallato da un computer Windows tramite il **Pannello di controllo**. Passare a **Programmi e funzionalità** o **Programmi > Disinstalla un programma** come illustrato di seguito:

 [![](uninstalling-xamarin-images/image3.png "Passare a Programmi e funzionalità o Programmi  Disinstalla un programma come illustrato qui")](uninstalling-xamarin-images/image3.png#lightbox) 

Disinstallare dal Pannello di controllo i seguenti elementi, se presenti:

- Xamarin
- Xamarin per Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin for Visual Studio

In Explorer eliminare eventuali file rimanenti dalle cartelle dell'estensione Xamarin Visual Studio (tutte le versioni, incluse le cartelle Programmi e Programmi (x86)):

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Eliminare la directory cache del componente MEF di Visual Studio, che dovrebbe trovarsi nel percorso seguente:

``` 
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Verificare nella directory **VirtualStore** se Windows ha archiviato eventuali file sovrimpressione per la directory **Extensions\Xamarin** o **ComponentModelCache**:

``` 
%LOCALAPPDATA%\VirtualStore
```

Aprire l'editor del Registro di sistema (regedit) e cercare la chiave seguente:

``` 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Individuare ed eliminare eventuali voci che corrispondono a questo modello:

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Cercare questa chiave:

``` 
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Eliminare tutte le voci che apparentemente possono essere correlate a Xamarin. Ad esempio, qualsiasi elemento che contiene i termini `mono` o `xamarin`.

Aprire un prompt dei comandi cmd.exe di amministratore ed eseguire i comandi `devenv /setup` e `devenv /updateconfiguration` per ogni versione installata di Visual Studio. Ad esempio, per Visual Studio 2015:

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>Disinstallare Xamarin Studio in Windows

Xamarin può essere disinstallato da un computer Windows usando il **Pannello di controllo**. Passare a **Programmi e funzionalità** o **Programmi > Disinstalla un programma** 

Per disinstallare Xamarin Studio, individuare **Xamarin Studio 5.x.x** nell'elenco dei programmi e fare clic sul pulsante **Disinstalla**. 

### <a name="uninstall-xamarin-studio-on-mac"></a>Disinstallare Xamarin Studio in Mac

Il primo passaggio per la disinstallazione di Xamarin Studio da un Mac consiste nell'individuare **Xamarin Studio.app** nella directory **/Applications** e trascinarlo nel **Cestino**. In alternativa, fare clic con il pulsante destro e selezionare **Sposta nel cestino** come illustrato di seguito:

 [![](uninstalling-xamarin-images/image1.png "In alternativa, fare clic con il pulsante destro del mouse e selezionare Sposta nel cestino come illustrato in questa schermata")](uninstalling-xamarin-images/image1.png#lightbox)

L'eliminazione di questo bundle dell'app causa la rimozione di Xamarin Studio, tuttavia ci sono altri file correlati a Xamarin nel file system.

Per rimuovere tutte le tracce di Xamarin Studio, occorre eseguire i comandi seguenti in Terminale:

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>Riepilogo

Questo articolo contiene istruzioni per la disinstallazione completa di Xamarin da un Mac usando i comandi di Terminal. Contiene inoltre istruzioni sulla disinstallazione di Xamarin da un computer Windows usando l'opzione **Programmi e funzionalità** (per Visual Studio 2015 e versioni precedenti) e il **programma di installazione di Visual Studio** per Visual Studio 2017.


## <a name="related-links"></a>Collegamenti correlati

- [Script di disinstallazione (esempio)](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
