---
title: Utilizzo degli elenchi di proprietà in xamarin. IOS
description: Questo documento introduce Visual Studio per l'editor dell'elenco (con estensione plist) delle proprietà avanzate e con interfaccia grafica del Mac per l'utilizzo di Info. plist ed Entitlements.plist. Viene illustrata l'impostazione di icone e le immagini di avvio per le applicazioni iOS da Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 6b22acab3fb19a6209fac8dcf6a0870763e601d2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784468"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Utilizzo degli elenchi di proprietà in xamarin. IOS

_Questo documento introduce Visual Studio per l'editor dell'elenco (con estensione plist) delle proprietà avanzate e con interfaccia grafica del Mac per l'utilizzo di Info. plist ed Entitlements.plist. Viene illustrata l'impostazione di icone e le immagini di avvio per le applicazioni iOS da Visual Studio per Mac._

Visual Studio per Mac è disponibile un editor di grafica. plist che rende la modifica di proprietà dell'app e funzionalità più semplice. Visual Studio per Mac ha due .plists - `Info.plist` per la modifica delle proprietà dell'app e icone, e `Entitlements.plist` per gestire le funzionalità di app. In questa guida vengono presentate le Info.plists e viene fornita una panoramica dell'utilizzo in Visual Studio per Mac. Per informazioni su Entitlements.plist, vedere il [funziona con i diritti](~/ios/deploy-test/provisioning/entitlements.md) Guida.

## <a name="infoplist"></a>Info. plist

L'elenco di proprietà di informazioni ( `Info.plist`) è un file necessario iOS che fornisce informazioni sulla configurazione dell'applicazione al sistema. Visual Studio per Mac personalizzata del `Info.plist` funzionalità dell'editor tre pannelli controllati dalle schede nella parte inferiore sinistra della finestra dell'editor:

 [![](property-lists-images/tabs.png "L'editor di Info. plist schede nella parte inferiore sinistra della finestra dell'editor")](property-lists-images/tabs.png#lightbox)

Ogni pannello controlli diverse proprietà, come descritto di seguito:

-  **Pannello applicazione** -un'interfaccia grafica per impostare le proprietà dell'applicazione, nonché le icone e avvio comuni immagini; specificare l'integrazione di mappe e backgrounding modalità.
-  **Advanced pannello** -pannello Avanzate è possibile specificare i tipi di documento supportati, UTIs e i tipi di URL.
-  **Origine pannello** -riquadro origine Controlla proprietà meno comuni, nonché le proprietà personalizzate per l'applicazione.


Tre sezioni provare a utilizzare le funzionalità di ciascun pannello in modo più dettagliato.

## <a name="application-panel"></a>Pannello applicazione

Visual Studio per Mac dispone di un'interfaccia grafica per la modifica comuni `Info.plist` voci per un'applicazione:

1.  Proprietà dell'applicazione
1.  Tipi di dispositivi supportati
1.  Orientamenti di supporto per ogni tipo di dispositivo
1.  Stile e il colore della barra di stato
1.  Icone e schermate di avvio
1.  Mappe e modalità di Background


Questi elementi sono descritti più dettagliatamente nelle sezioni successive.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Applicazione di destinazione di iOS

In questa sezione sono contenute importanti informazioni che descrive l'applicazione.
Il **identificatore** archiviate qui deve corrispondere all'identificatore di pacchetto che viene immesso in iTunes Connect (per App di App Store) e anche nell'elenco di ID dell'App portale di Provisioning iOS e lo sviluppo e distribuzione dei certificati.

 [![](property-lists-images/image24.png "Applicazione di destinazione di iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Distribuzione dei dispositivi

 [![](property-lists-images/deployment.png "Distribuzione dei dispositivi")](property-lists-images/deployment.png#lightbox)

Il dispositivo **distribuzione** sezioni di informazioni vengono visualizzate in modo selettivo, a seconda della selezione nel **dispositivi** elenco a discesa nella **applicazione di destinazione** sezione precedente. Il **interfaccia principale** elenco a discesa è impostato su **MainStoryboard** nelle applicazioni basate su uno Storyboard. Se l'interfaccia utente è completamente scritto in codice quindi può essere lasciata vuota.

### <a name="supported-device-orientations"></a>Orientamenti di dispositivi supportati

 **Dispositivo orientamenti supportati** controlla la modalità di risposta dell'app a rotazione del dispositivo. È molto comune per l'App iPhone/iPad supportano solo **verticale**, o tutti gli elementi ma **rovesciati**. In genere tutte le applicazioni ad eccezione di giochi iPad devono supportare tutti gli orientamenti.

### <a name="status-bar-styles"></a>Stili barra di stato

Il **stili barra di stato** sezione è un'interfaccia grafica per la modifica di un'applicazione `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Stili barra di stato")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Icone, le immagini di avvio e iTunes grafica

Informazioni sull'utilizzo di grafica, immagini e icone nel file Info. plist sono reperibile nel [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) Guida.




### <a name="maps-integration-and-background-modes"></a>Integrazione di mappe e modalità di Background

Il `Info.plist` contiene sezioni speciali per specificare l'integrazione di mappe e backgrounding modalità. Scelta delle opzioni di cui che si desidera supportare aggiungerà le proprietà necessarie per l'applicazione.

 [![](property-lists-images/maps.png "Integrazione di mappe")](property-lists-images/maps.png#lightbox)

Per ulteriori informazioni sull'utilizzo di mappe, vedere il Xamarin [iOS mappe](~/ios/user-interface/controls/ios-maps/index.md) Guida.

 [![](property-lists-images/bging.png "Modalità di background")](property-lists-images/bging.png#lightbox)

Per ulteriori informazioni sulla modalità di Background, vedere il Xamarin [Backgrounding in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) Guida.

## <a name="advanced-panel"></a>Pannello Avanzate

Pannello Avanzate controlla i tipi di documento e gli schemi URL che supporta l'applicazione.

 [![](property-lists-images/image34.png "Pannello Avanzate")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipi di documento

Per le applicazioni che supportano tipi di file specifici di apertura, iOS fornisce il `CFBundleDocumentTypes` chiave. Se si desidera che l'applicazione per supportare determinati tipi di file note, ad esempio PDF - il valore PDF verrà aggiunto alla chiave. In questa sezione fornisce un modo pratico per immettere i dati archiviati nel `CFBundleDocumentTypes` chiave nel `Info.plist` file.

Vedere la documentazione su [registra il File di tipi di App supporta](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) per informazioni dettagliate su come configurare questi valori.

## <a name="utis"></a>UTIs

In alcuni casi un'applicazione deve supportare l'apertura di un tipo di file personalizzato. Ad esempio, potrebbe voler aprire i file di immagine con un'estensione personalizzata *.xam*. Per specificare un tipo di file personalizzati, si creerà un UTI - identificatore di tipo universale - personalizzata usando il `UIExportedTypeDeclarations` chiave. La schermata riportata di seguito viene illustrato come creare un UTI personalizzato per l'estensione .xam:

 [![](property-lists-images/uti.png "Editor UTIs")](property-lists-images/uti.png#lightbox)

Tipo solo come esportato UTIs specificare UTIs personalizzati specifici dell'app, il *importato tipo UTIs* ( `UIImportedTypeDeclarations` chiave) specificare tipi personalizzati è supportata ma non di proprietà dell'applicazione.

Per ulteriori informazioni sull'utilizzo UTIs personalizzato, fare riferimento a Apple [la registrazione di File di tipi di App supporta](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) Guida.

## <a name="custom-urls"></a>URL personalizzati

Il nome di schema URL (detto anche protocollo) è la prima parte dell'URL. Ad esempio, `http://` e `https://` sono comuni schemi URL. È possibile creare uno schema URL personalizzato per l'applicazione. Schemi di URL vengono utilizzati per comunicare e inviare i dati e viceversa con altre applicazioni. Nella schermata seguente viene illustrata la creazione di un nuovo schema URL personalizzato chiamato `monkeys://`:

 [![](property-lists-images/url.png "URL personalizzati")](property-lists-images/url.png#lightbox)



Per ulteriori informazioni sull'implementazione di schemi URL personalizzati, fare riferimento a Apple [sezione di implementazione di schemi URL personalizzato di questa Guida](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Pannello di origine

Il **origine** scheda della finestra di `Info.plist` file consente di aggiungere o modificare valori personalizzati. Visual Studio per Mac fornisce un elenco di proprietà più comuni:

 [![](property-lists-images/image31.png "Aggiunta di una nuova proprietà da un elenco a discesa")](property-lists-images/image31.png#lightbox)

Per le proprietà note di Visual Studio per Mac sarà un elenco di valori validi, come illustrato nella schermata seguente:

 [![](property-lists-images/image32.png "Selezionare un valore da un elenco di valori noti")](property-lists-images/image32.png#lightbox)

Visual Studio per Mac rileva inoltre il tipo di proprietà, come illustrato:

 [![](property-lists-images/image33.png "I tipi di proprietà disponibili")](property-lists-images/image33.png#lightbox)

Esaminare Apple [risorse correlate App](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) collegamenti per ulteriori informazioni sulle proprietà facoltativa.

 <a name="Entitlements" />

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate utilizzando l'editor. plist con interfaccia grafica e avanzate per modificare configurazioni comuni di app nonché per specificare le icone e le immagini di avvio. Viene anche introdotto il `Entitlements.plist` per aggiungere e gestire le funzionalità delle app.


## <a name="related-links"></a>Collegamenti correlati

- [IDE](https://developer.xamarin.com/recipes/cross-platform/ide)
- [Risorse correlate di App](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registra il File di tipi supporta l'App](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementazione di schemi URL personalizzato](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Sui cataloghi di Asset](https://developer.apple.com/library/ioshttps://developer.xamarin.com/recipes/xcode_help-image_catalog-1.0/Recipe.html)
