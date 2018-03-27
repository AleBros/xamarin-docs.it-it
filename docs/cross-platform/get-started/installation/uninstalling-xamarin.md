---
title: Disinstallazione di Xamarin
description: Disinstallazione di prodotti Xamarin da un computer
ms.topic: article
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 2c2ba84167924916c3bec27379d33c47e8dab360
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="uninstalling-xamarin"></a>Disinstallazione di Xamarin

> [!IMPORTANT]
> In questo articolo viene illustrato come disinstallare Xamarin Studio o altri prodotti Xamarin da un computer Mac o Windows. Per informazioni sulla disinstallazione di Visual Studio per Mac, fare riferimento alla [Guida alla disinstallazione](https://docs.microsoft.com/visualstudio/mac/uninstall) in docs.microsoft.com.

Sono disponibili diversi prodotti Xamarin che consentono lo sviluppo di applicazioni multipiattaforma, incluse app autonome come Xamarin Studio ed estensioni per altre app come il supporto Xamarin in Visual Studio.

Questa guida descriverà come rimuovere la funzionalità Xamarin in macOS oppure da Visual Studio in Windows:

1.  [Disinstallazione di Xamarin Studio](#uninstallxamarinstudio)
  1.  [Disinstallazione di Mono](#uninstallmono)
  1.  [Disinstallazione di Xamarin.Android](#uninstallandroid)
  1.  [Disinstallazione di Xamarin.iOS](#uninstallios)
  1.  [Disinstallazione di Xamarin.Mac](#uninstallmac)
  2.  [Disinstallazione di Inspector e Workbooks](#uninstallworkbooks)
1.  [Disinstallazione di Xamarin da Windows](#uninstallwindows)
  1.  [Disinstallazione di Xamarin da Visual Studio 2015 e versioni precedenti](#uninstallvs2015)
  1.  [Disinstallazione di Xamarin da Visual Studio 2017](#uninstallvs2017)
1.  [Disinstallazione di Visual Studio per Mac](#uninstallvsmac)

Se è necessario reinstallare Xamarin tramite Universal Installer, è sempre consigliabile riavviare prima il computer.

## <a name="uninstalling-xamarin-on-mac"></a>Disinstallazione di Xamarin in Mac

Questa guida può essere usata per disinstallare singolarmente ogni prodotto spostandosi sulla sezione relativa. Seguendo questa guida è possibile disinstallare l'intero set di strumenti di Xamarin.

Per assistenza nell'uso dello [script di disinstallazione](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh), andare a [Uso dello script di disinstallazione](#uninstallscript) più avanti in questa guida.

<a name="uninstallxamarinstudio" />

### <a name="uninstall-xamarin-studio"></a>Disinstallare Xamarin Studio

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

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Disinstallare Mono SDK (MDK)

Mono è un'implementazione open source di Microsoft .NET Framework ed è usato da tutti i prodotti Xamarin (Xamarin.iOS, Xamarin.Android e Xamarin.Mac) per consentire lo sviluppo di queste piattaforme in C#.

> [!IMPORTANT]
> Vi sono altre applicazioni oltre a Xamarin che usano Mono, ad esempio Unity. Prima di disinstallare Mono, assicurarsi che non vi siano altre dipendenze.

Per rimuovere Mono Framework da un computer, eseguire i comandi in Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Disinstallare Xamarin.Android

Per l'installazione e l'uso di Xamarin.Android sono richiesti diversi elementi, ad esempio Android SDK e Java SDK. Altre informazioni su questi componenti richiesti sono disponibili nella [Guida all'installazione manuale](https://docs.microsoft.com/visualstudio/mac/installation/).

Usare i comandi seguenti per rimuovere Xamarin.Android:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Disinstallare Android SDK e Java SDK

Android SDK è richiesto per lo sviluppo di applicazioni Android. Per rimuovere completamente tutte le parti di Android SDK, individuare il file in **~/Library/Developer/Xamarin/** e spostarlo nel **Cestino**, come illustrato di seguito:

 [![](uninstalling-xamarin-images/image2.png "Per rimuovere completamente tutte le parti di Android SDK, individuare il file e spostarlo nel Cestino, come illustrato in questa schermata")](uninstalling-xamarin-images/image2.png#lightbox)

Non è necessario disinstallare Java SDK (JDK) poiché è già inserito nel pacchetto come parte di Mac OS X.

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Disinstallare Xamarin.iOS

Xamarin.iOS consente di sviluppare applicazioni iOS usando C# o F# con Xamarin Studio in un Mac.
Anche l'host di compilazione Xamarin viene installato automaticamente con le versioni precedenti di Xamarin.iOS per consentire lo sviluppo di iOS in Visual Studio. Per disinstallare entrambi da un computer, seguire questa procedura:

Usare i comandi seguenti in Terminal per rimuovere tutti i file Xamarin.iOS da un file system:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
```

#### <a name="uninstall-the-mac-build-host"></a>Disinstallare l'host di compilazione Mac

Nota: l'host potrebbe essere già stato rimosso se è già stato effettuato l'aggiornamento a Xamarin 4. Eseguire il comando seguente in Terminale per rimuovere l'applicazione host di compilazione:

```bash
sudo rm -rf "/Applications/Xamarin.iOS Build Host.app"
```

Il processo dell'host di compilazione o il processo `launchd` potrebbe essere ancora in esecuzione o in ascolto su determinate porte.
È possibile controllarne lo stato eseguendo `launchctl list | grep com.xamarin.mtvs.buildserver` in Terminale.

```bash
sudo launchctl unload /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
sudo rm -f /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Disinstallare Xamarin.Mac

Dopo la disinstallazione di Xamarin Studio, è possibile rimuovere dal computer Xamarin.Mac usando i due comandi seguenti per eliminare rispettivamente il prodotto e la licenza dal Mac:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks-and-inspector"></a>Disinstallare Workbooks e Inspector

Il seguente comando Bash rimuoverà Xamarin Inspector e Workbooks 1.2.2 e versioni successive:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Per le versioni precedenti, vedere la guida per la disinstallazione di [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

### <a name="uninstall-the-xamarin-installer"></a>Disinstallare il programma di installazione di Xamarin

Usare i comandi seguenti per rimuovere tutte le tracce di Xamarin Universal Installer:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Preferences/Xamarin/
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Uso dello script di disinstallazione

Lo [script di disinstallazione](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh) consentirà di rimuovere Xamarin da un computer. Per usare lo script di disinstallazione:

1.  Scaricare lo script di disinstallazione e prendere nota del percorso di download. Per impostazione predefinita, si tratta della directory **/Downloads**.
1.  Aprire **Terminale** e modificare la directory di lavoro in cui lo script è stato scaricato:

        $ cd /location/of/file

1. Rendere eseguibile lo script ed eseguirlo con **sudo**:

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

1. Infine, eliminare lo script di disinstallazione.

A questo punto, Xamarin dovrebbe essere disinstallato dal computer.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Disinstallazione di Xamarin in Windows

<a name="uninstallvs2015" />

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 e versioni precedenti

Xamarin può essere disinstallato da un computer Windows tramite il **Pannello di controllo**. Passare a **Programmi e funzionalità** o **Programmi > Disinstalla un programma** come illustrato di seguito:

 [![](uninstalling-xamarin-images/image3.png "Passare a Programmi e funzionalità o Programmi > Disinstalla un programma come illustrato in questa schermata")](uninstalling-xamarin-images/image3.png#lightbox) [![](uninstalling-xamarin-images/image3.png "Passare a Programmi e funzionalità o Programmi > Disinstalla un programma come illustrato in questa schermata")](uninstalling-xamarin-images/image3.png#lightbox)

Per disinstallare Xamarin Studio, individuare **Xamarin Studio 5.x.x** nell'elenco dei programmi e fare clic sul pulsante **Disinstalla**. Per rimuovere l'estensione Xamarin per Visual Studio e gli SDK, individuare **Xamarin** nell'elenco dei programmi e fare clic su **Disinstalla**, come illustrato nella schermata seguente:

 [![](uninstalling-xamarin-images/image4a.png "Questi programmi sono illustrati in questo screenshot")](uninstalling-xamarin-images/image4a.png#lightbox)

Anche questi programmi dovrebbero essere rimossi per rimuovere completamente tutti i componenti Xamarin:

-  Android SDK


  [![](uninstalling-xamarin-images/image5.png "Anche questi programmi dovrebbero essere rimossi per rimuovere completamente tutti i componenti di Xamarin")](uninstalling-xamarin-images/image5.png#lightbox)
-  GTK#


  [![](uninstalling-xamarin-images/image6.png "Anche questi programmi dovrebbero essere rimossi per rimuovere completamente tutti i componenti di Xamarin")](uninstalling-xamarin-images/image6.png#lightbox)
-  Xamarin Universal Installer


 [![](uninstalling-xamarin-images/image7.png "Anche questi programmi dovrebbero essere rimossi per rimuovere completamente tutti i componenti di Xamarin")](uninstalling-xamarin-images/image7.png#lightbox)
-  Java SDK (prestare attenzione durante la rimozione, in quanto potrebbero esserci altre dipendenze)


 [![](uninstalling-xamarin-images/image8.png "Prestare attenzione durante la rimozione di Java SDK, perché potrebbero esserci altre dipendenze")](uninstalling-xamarin-images/image8.png#lightbox)

Per disinstallare completamente Visual Studio, seguire le [istruzioni di Microsoft](https://msdn.microsoft.com/library/mt720585.aspx).


<a name="uninstallvs2017" />

## <a name="visual-studio-2017"></a>Visual Studio 2017

Xamarin può essere disinstallato da Visual Studio 2017 tramite l'app del programma di installazione:

1. Usare il **menu Start** per aprire il **programma di installazione di Visual Studio**.

  [![](uninstalling-xamarin-images/vs2017-01-sml.png "Avviare il programma di installazione di Visual Studio")](uninstalling-xamarin-images/vs2017-01.png#lightbox)

1. Fare clic sul pulsante **Modifica** per l'istanza che si vuole modificare.

  [![](uninstalling-xamarin-images/vs2017-02-sml.png "Fare clic sul pulsante Modifica")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

1. Nella scheda **Carichi di lavoro** deselezionare l'opzione **Sviluppo di applicazioni per dispositivi mobili con .NET** (nella sezione **Dispositivi mobili e giochi**).

  [![](uninstalling-xamarin-images/vs2017-03-sml.png "Deselezionare il carico di lavoro Sviluppo di applicazioni per dispositivi mobili")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

1. Fare clic sul pulsante **Modifica** in basso a destra nella finestra.
1. Il programma di installazione rimuoverà i componenti deselezionati (Visual Studio 2017 deve essere chiuso prima che il programma di installazione possa apportare modifiche).

  [![](uninstalling-xamarin-images/vs2017-04-sml.png "Fare clic sul pulsante Modifica")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

I singoli componenti di Xamarin (come il Profiler o Workbooks) possono essere disinstallati passando alla scheda **Singoli componenti** nel passaggio 3 e deselezionando i componenti specifici:

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
>Questo problema può essere risolto eseguendo l'opzione di **ripristino** nel programma di installazione di Visual Studio, che consente di reinstallare i componenti mancanti.

<a name="uninstallvsmac" />

## <a name="uninstalling-visual-studio-for-mac"></a>Disinstallazione di Visual Studio per Mac

Per disinstallare Visual Studio per Mac continuando tuttavia a usare Xamarin Studio, individuare **Visual Studio.app** nella directory **/Applications** e trascinarlo nel Cestino. In alternativa, fare clic con il pulsante destro e selezionare **Sposta nel cestino** come illustrato di seguito:

 [![](uninstalling-xamarin-images/image9.png "Fare clic con il pulsante destro del mouse sull'icona di Visual Studio e scegliere Sposta nel cestino")](uninstalling-xamarin-images/image9.png#lightbox)

Per disinstallare completamente Xamarin dal computer, eliminare innanzitutto Visual Studio per Mac e quindi seguire i passaggi elencati nella sezione [Disinstallare Xamarin Studio](#uninstallxamarinstudio).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come disinstallare completamente Xamarin da un Mac usando i comandi di Terminale e come disinstallare Xamarin da un computer Windows tramite l'opzione **Programmi e funzionalità** (per Visual Studio 2015 e versioni precedenti) e il **programma di installazione di Visual Studio** per Visual Studio 2017.


## <a name="related-links"></a>Collegamenti correlati

- [Script di disinstallazione (esempio)](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)
