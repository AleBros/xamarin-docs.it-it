---
title: Utilizzo degli elenchi di proprietà in xamarin. IOS
description: Questo documento presenta Visual Studio per l'editor dell'elenco (con estensione plist) proprietà grafiche e avanzato del Mac per l'utilizzo di Info. plist ed entitlements. plist. Viene illustrata l'impostazione delle icone e immagini di avvio per le applicazioni iOS da Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 87015163eca9fdb2fb8cee35e74d5031314fd42e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245805"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Utilizzo degli elenchi di proprietà in xamarin. IOS

_Questo documento presenta Visual Studio per l'editor dell'elenco (con estensione plist) proprietà grafiche e avanzato del Mac per l'utilizzo di Info. plist ed entitlements. plist. Viene illustrata l'impostazione delle icone e immagini di avvio per le applicazioni iOS da Visual Studio per Mac._

Visual Studio per Mac offre un editor plist con interfaccia grafica che rende la modifica di proprietà dell'app e le funzionalità più semplice. Visual Studio per Mac include due .plists - `Info.plist` per la modifica delle proprietà dell'app e le icone, e `Entitlements.plist` per la gestione delle funzionalità delle app. Questa Guida introduce i Info.plists e fornisce una panoramica di utilizzarli in Visual Studio per Mac. Per informazioni su entitlements. plist, vedere la [uso degli Entitlement](~/ios/deploy-test/provisioning/entitlements.md) Guida.

## <a name="infoplist"></a>Info. plist

L'elenco di proprietà di informazioni ( `Info.plist`) è un file di iOS necessaria che fornisce informazioni sulla configurazione dell'applicazione al sistema. Visual Studio per Mac personalizzata del `Info.plist` funzionalità dell'editor di tre pannelli controllati da schede nella parte inferiore sinistra della finestra dell'editor:

 [![](property-lists-images/tabs.png "Schede editor Info. plist nella parte inferiore sinistra della finestra dell'editor")](property-lists-images/tabs.png#lightbox)

Ogni pannello consente di controllare diverse proprietà, come indicato di seguito:

-  **Pannello applicazioni** -un'interfaccia grafica per impostare le proprietà dell'applicazione, nonché le icone e avvio comuni immagini; specificare integrazione con mappe e le modalità di elaborazione in background in.
-  **Advanced pannello** -pannello Avanzate è possibile specificare tipi di documento supportati, uti e tipi di URL.
-  **Pannello di origine** -proprietà meno comuni, nonché le proprietà personalizzate per l'applicazione consente di controllare il pannello di origine.


Tre sezioni successive provare a utilizzare le funzionalità di ciascun pannello in modo più dettagliato.

## <a name="application-panel"></a>Pannello dell'applicazione

Visual Studio per Mac offre un'interfaccia grafica per la modifica comuni `Info.plist` voci per un'applicazione:

1.  Proprietà dell'applicazione
1.  Tipi di dispositivi supportati
1.  Orientamenti di supporto per ogni tipo di dispositivo
1.  Sulla barra di stile e il colore di stato
1.  Icone e schermate di avvio
1.  Mappe e le modalità in Background


Questi elementi sono descritti più dettagliatamente nelle sezioni successive.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Destinazione applicazione iOS

In questa sezione contiene informazioni importanti che descrivono l'applicazione.
Il **identificatore** archiviati qui deve corrispondere all'identificatore del Bundle che viene immesso in iTunes Connect (per le app di Store App) e anche nell'elenco di ID dell'App portale di Provisioning iOS e i certificati di sviluppo e distribuzione.

 [![](property-lists-images/image24.png "Destinazione applicazione iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Distribuzione del dispositivo

 [![](property-lists-images/deployment.png "Distribuzione del dispositivo")](property-lists-images/deployment.png#lightbox)

Il dispositivo **Deployment** sezioni di informazioni vengono visualizzate in modo selettivo, a seconda della selezione nel **dispositivi** elenco a discesa nel **destinazione applicazione** sezione precedente. Il **interfaccia principale** elenco a discesa è impostata su **MainStoryboard** in applicazioni basate su uno Storyboard. Se l'interfaccia utente è interamente scritta in codice quindi può essere lasciata vuota.

### <a name="supported-device-orientations"></a>Orientamenti dispositivo supportati

 **Orientamenti dispositivo supportati** controlla la risposta dell'app alla rotazione del dispositivo. È molto comune per le app iPhone/iPad supportare solo **ritratto**, o tutti gli elementi ma **capovolto**. In genere tutte le applicazioni di iPad tranne giochi devono supportare tutti gli orientamenti.

### <a name="status-bar-styles"></a>Stili barra di stato

Il **stili barra di stato** sezione è un'interfaccia grafica per la modifica di un'applicazione `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Stili barra di stato")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Illustrazione di iTunes, immagini di avvio e icone

Sono disponibili informazioni sull'uso di elementi grafici, immagini e icone nel file Info. plist nel [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) Guida.




### <a name="maps-integration-and-background-modes"></a>Integrazione con mappe e le modalità in Background

Il `Info.plist` contiene sezioni speciali per specificare l'integrazione con mappe e le modalità di elaborazione in background in. Scegliendo le opzioni desiderate per il supporto aggiungerà le proprietà necessarie per l'applicazione per l'utente.

 [![](property-lists-images/maps.png "Integrazione con mappe")](property-lists-images/maps.png#lightbox)

Per altre informazioni sull'uso di mappe, vedere il Xamarin [mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md) Guida.

 [![](property-lists-images/bging.png "Modalità in background")](property-lists-images/bging.png#lightbox)

Per ulteriori informazioni sulla modalità in Background, vedere il Xamarin [elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) Guida.

## <a name="advanced-panel"></a>Pannello Avanzate

Pannello Avanzate controlla i tipi di documento e schemi URL supportate dall'applicazione.

 [![](property-lists-images/image34.png "Advanced Panel")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipi di documento

Per le applicazioni che supportano l'apertura di determinati tipi di file, iOS fornisce il `CFBundleDocumentTypes` chiave. Se si vuole che l'applicazione per supportare determinati tipi di file note, ad esempio PDF - aggiungiamo il valore PDF per la chiave. In questa sezione fornisce un modo pratico per immettere i dati che verrà archiviato nel `CFBundleDocumentTypes` chiavi nel `Info.plist` file.

Vedere la documentazione sul [registrare il File di tipi di App supporta](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) per informazioni dettagliate su come configurare questi valori.

## <a name="utis"></a>Uti

A volte un'applicazione deve supportare l'apertura di un tipo di file personalizzato. Ad esempio, si potrebbe essere necessario aprire i file di immagine con un'estensione personalizzata *.xam*. Per specificare un tipo di file personalizzati, si creerà un UTI - identificatore di tipo universale - personalizzata usando il `UIExportedTypeDeclarations` chiave. Lo screenshot seguente illustra come creare un UTI personalizzato per l'estensione .xam:

 [![](property-lists-images/uti.png "Uti di Editor")](property-lists-images/uti.png#lightbox)

Uti dei tipi esportati solo come specificare uti personalizzati specifici per l'applicazione, il *uti dei tipi importate* ( `UIImportedTypeDeclarations` chiave) specificare i tipi personalizzati supportati ma non di proprietà dell'applicazione.

Per altre informazioni sull'uso di uti personalizzati, fare riferimento a Apple [la registrazione di File di tipi di App supporta](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) Guida.

## <a name="custom-urls"></a>URL personalizzati

Un nome di schema URL (denominato anche protocollo) è la prima parte dell'URL. Ad esempio, `http://` e `https://` alcuno schema di URL comuni. Hai la possibilità di creare uno schema URL personalizzato per l'applicazione. Schemi URL personalizzati vengono utilizzati per comunicare e inviare dati e viceversa con altre applicazioni. Lo screenshot seguente illustra la creazione di un nuovo schema URL personalizzato chiamato `monkeys://`:

 [![](property-lists-images/url.png "URL personalizzati")](property-lists-images/url.png#lightbox)



Per altre informazioni sull'implementazione di schemi URL personalizzati, fare riferimento a Apple [sezione di implementazione di schemi URL personalizzato di questa Guida](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Pannello origine

Il **origine** scheda della finestra di `Info.plist` file consente di essere aggiunti o modificati valori personalizzati. Visual Studio per Mac offre un elenco delle proprietà più comuni:

 [![](property-lists-images/image31.png "Aggiunta di una nuova proprietà da un elenco a discesa")](property-lists-images/image31.png#lightbox)

Per le proprietà note di Visual Studio per Mac sarà un elenco di valori validi, come illustrato nello screenshot seguente:

 [![](property-lists-images/image32.png "Selezionare un valore da un elenco di valori noti")](property-lists-images/image32.png#lightbox)

Visual Studio per Mac rileva anche il tipo di proprietà, come illustrato:

 [![](property-lists-images/image33.png "I tipi di proprietà disponibili")](property-lists-images/image33.png#lightbox)

Revisione di Apple [le risorse correlate di App](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) collegamenti per ulteriori informazioni sulla proprietà facoltative.

 <a name="Entitlements" />

## <a name="summary"></a>Riepilogo

Questo articolo illustrato utilizzando l'editor plist con interfaccia grafica e avanzate per modificare comuni le configurazioni delle app nonché per specificare icone e immagini di avvio. Viene anche introdotto il `Entitlements.plist` ad aggiungere e gestire le funzionalità delle app.


## <a name="related-links"></a>Collegamenti correlati

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Le risorse correlate alle App](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registra il File di tipi supportata dall'App](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementazione di schemi URL personalizzato](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Riferimento a un formato catalogo Asset](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
