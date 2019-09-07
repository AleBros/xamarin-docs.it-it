---
title: Uso degli elenchi di proprietà in Novell. iOS
description: Questo documento introduce l'editor grafico e avanzato delle proprietà (estensione plist) di Visual Studio per Mac per l'uso di info. plist e dei diritti. plist. Viene illustrata l'impostazione delle icone e delle immagini di avvio per le applicazioni iOS dall'interno Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 35c35e702b7f992e0661630783edd4f00abce5c7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767368"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Uso degli elenchi di proprietà in Novell. iOS

_Questo documento introduce l'editor grafico e avanzato delle proprietà (estensione plist) di Visual Studio per Mac per l'uso di info. plist e dei diritti. plist. Viene illustrata l'impostazione delle icone e delle immagini di avvio per le applicazioni iOS dall'interno Visual Studio per Mac._

Visual Studio per Mac include un editor grafico con estensione plist che semplifica la modifica delle proprietà e delle funzionalità dell'app. Visual Studio per Mac dispone di due plists `Info.plist` per la modifica delle proprietà e delle icone dell'app e `Entitlements.plist` per la gestione delle funzionalità delle app. Questa guida introduce info. plists e fornisce una panoramica sull'uso di tali informazioni in Visual Studio per Mac. Per informazioni sul file con estensione plist, vedere la Guida all' [uso dei diritti](~/ios/deploy-test/provisioning/entitlements.md) .

## <a name="infoplist"></a>Info. plist

Information Property List ( `Info.plist`) è un file iOS obbligatorio che fornisce informazioni sulla configurazione dell'applicazione al sistema. L'editor personalizzato `Info.plist` di Visual Studio per Mac include tre pannelli controllati da schede nella parte inferiore sinistra della finestra dell'Editor:

 [![](property-lists-images/tabs.png "Schede dell'editor info. plist nella parte inferiore sinistra della finestra dell'editor")](property-lists-images/tabs.png#lightbox)

Ogni pannello Controlla proprietà diverse, come descritto di seguito:

- **Pannello dell'applicazione** : interfaccia grafica per impostare le proprietà comuni dell'applicazione, nonché le icone e le immagini di avvio; specificare le modalità di integrazione delle mappe e di background.
- **Pannello avanzato** : il pannello Advanced è la posizione in cui specificare i tipi di documento, uti e i tipi di URL supportati.
- **Pannello di origine** : il pannello di origine controlla le proprietà meno comuni, nonché le proprietà personalizzate per l'applicazione.

Nelle tre sezioni successive vengono esaminate le funzionalità di ogni pannello in modo più dettagliato.

## <a name="application-panel"></a>Pannello applicazione

Visual Studio per Mac offre un'interfaccia grafica per la modifica `Info.plist` di voci comuni per un'applicazione:

1. Proprietà dell'applicazione
1. Tipi di dispositivo supportati
1. Supportare gli orientamenti per ogni tipo di dispositivo
1. Stile e colore della barra di stato
1. Icone e schermate di avvio
1. Mappe e modalità in background

Queste informazioni sono descritte più dettagliatamente nelle sezioni successive.

 <a name="iOS_Application_Target" />

### <a name="ios-application-target"></a>Destinazione applicazione iOS

Questa sezione contiene informazioni importanti che descrivono l'applicazione.
L' **identificatore** archiviato qui deve corrispondere all'identificatore del bundle immesso in iTunes Connect (per le app dello Store di app) e anche nell'elenco di ID app del portale di provisioning iOS e nei certificati di sviluppo e distribuzione.

 [![](property-lists-images/image24.png "Destinazione applicazione iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Distribuzione del dispositivo

 [![](property-lists-images/deployment.png "Distribuzione del dispositivo")](property-lists-images/deployment.png#lightbox)

Le sezioni informazioni sulla **distribuzione** del dispositivo vengono visualizzate in modo selettivo, a seconda della selezione nell'elenco a discesa **dispositivi** nella sezione **destinazione applicazione** precedente. L'elenco a discesa dell' **interfaccia principale** è impostato su **file mainstoryboard** nelle applicazioni basate su storyboard. Se l'interfaccia utente è interamente scritta nel codice, è possibile lasciarla vuota.

### <a name="supported-device-orientations"></a>Orientamenti dei dispositivi supportati

 Gli **orientamenti dei dispositivi supportati** controllano il modo in cui l'app risponde alla rotazione del dispositivo. Per le app iPhone/iPad è molto comune supportare solo il formato **verticale**o **tutto tranne.** In genere, tutte le applicazioni iPad tranne i giochi devono supportare tutti gli orientamenti.

### <a name="status-bar-styles"></a>Stili della barra di stato

La sezione **stili barra di stato** è un'interfaccia grafica per la modifica di `UIStatusBarStyle`un'applicazione:

 [![](property-lists-images/status.png "Stili della barra di stato")](property-lists-images/status.png#lightbox)

 <a name="Icons" />

### <a name="icons-launch-images-and-itunes-artwork"></a>Icone, immagini di avvio e illustrazioni di iTunes

Per informazioni sull'uso di icone, immagini e illustrazioni nel file INFO. plist, vedere la Guida [uso delle immagini](~/ios/app-fundamentals/images-icons/index.md) .

### <a name="maps-integration-and-background-modes"></a>Modalità di integrazione e background di Maps

Contiene `Info.plist` sezioni speciali per specificare le modalità di integrazione delle mappe e di background. Se si scelgono le opzioni che si desidera supportare, le proprietà necessarie vengono aggiunte all'applicazione.

 [![](property-lists-images/maps.png "Integrazione di Maps")](property-lists-images/maps.png#lightbox)

Per altre informazioni sull'uso delle mappe, vedere la Guida di Novell [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md) .

 [![](property-lists-images/bging.png "Modalità in background")](property-lists-images/bging.png#lightbox)

Per altre informazioni sulle modalità in background, vedere la guida Novell [in background in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) .

## <a name="advanced-panel"></a>Pannello avanzato

Il pannello Advanced controlla i tipi di documento e gli schemi URL supportati dall'applicazione.

 [![](property-lists-images/image34.png "Pannello avanzato")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />

## <a name="document-types"></a>Tipi di documento

Per le applicazioni che supportano l'apertura di tipi specifici di file, `CFBundleDocumentTypes` iOS fornisce la chiave. Se si vuole che l'applicazione supporti alcuni tipi di file noti, ad esempio i file PDF, aggiungere il valore PDF alla chiave. Questa sezione fornisce un modo pratico per immettere i dati che verranno archiviati nella `CFBundleDocumentTypes` chiave `Info.plist` del file.

Per informazioni dettagliate su come configurare questi valori, vedere la documentazione relativa alla [registrazione dei tipi di file supportati dall'app](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) .

## <a name="utis"></a>Uti

A volte un'applicazione deve supportare l'apertura di un tipo di file personalizzato. Ad esempio, potrebbe essere necessario aprire i file di immagine con un'estensione personalizzata *. xam*. Per specificare un tipo di file personalizzato, verrà creato un identificatore di tipo uti universale personalizzato, usando la `UIExportedTypeDeclarations` chiave. La schermata seguente illustra come creare un UTI personalizzato per l'estensione XAM:

 [![](property-lists-images/uti.png "Editor uti")](property-lists-images/uti.png#lightbox)

Proprio come il tipo esportato uti specifica uti personalizzati specifici per l'app, il *tipo importato uti* ( `UIImportedTypeDeclarations` chiave) specifica i tipi personalizzati supportati ma non di proprietà dell'applicazione.

Per altre informazioni sull'uso di uti personalizzati, vedere la pagina relativa alla procedura di [registrazione dei tipi di file](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) di Apple nell'app.

## <a name="custom-urls"></a>URL personalizzati

Un nome di schema URL (detto anche protocollo) è la prima parte dell'URL. Ad esempio, `http://` e `https://` sono schemi URL comuni. È possibile creare uno schema URL personalizzato per l'applicazione. Gli schemi URL personalizzati vengono utilizzati per comunicare e inviare dati da e verso altre applicazioni. Lo screenshot seguente illustra la creazione di un nuovo schema URL personalizzato `monkeys://`denominato:

 [![](property-lists-images/url.png "URL personalizzati")](property-lists-images/url.png#lightbox)

Per ulteriori informazioni sull'implementazione degli schemi URL personalizzati, vedere [la sezione relativa all'implementazione degli schemi URL personalizzati di Apple in questa guida](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Pannello di origine

La scheda **origine** del `Info.plist` file consente di aggiungere o modificare i valori personalizzati. Visual Studio per Mac fornisce un elenco delle proprietà più comuni:

 [![](property-lists-images/image31.png "Aggiunta di una nuova proprietà da un elenco a discesa")](property-lists-images/image31.png#lightbox)

Per le proprietà note Visual Studio per Mac viene indicato un elenco di valori validi, come illustrato nello screenshot seguente:

 [![](property-lists-images/image32.png "Selezionare un valore da un elenco di valori noto")](property-lists-images/image32.png#lightbox)

Visual Studio per Mac rileva anche il tipo di proprietà, come illustrato:

 [![](property-lists-images/image33.png "Tipi di proprietà disponibili")](property-lists-images/image33.png#lightbox)

Per ulteriori informazioni sulle proprietà facoltative, vedere i collegamenti [alle risorse correlate alle app](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) di Apple.

 <a name="Entitlements" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso degli editor grafici e avanzati con estensione plist per modificare le configurazioni comuni delle app, nonché per specificare le icone e le immagini di avvio. Introduce anche per l' `Entitlements.plist` aggiunta e la gestione delle funzionalità delle app.

## <a name="related-links"></a>Collegamenti correlati

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Risorse correlate all'app](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registrazione dei tipi di file supportati dall'app](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementazione di schemi URL personalizzati](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Riferimento al formato del catalogo asset](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
