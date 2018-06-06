---
title: Controlli dell'interfaccia utente in xamarin. IOS
description: Questo documento include collegamenti ad guide che descrivono le varie iOS controlli dell'interfaccia utente disponibili per gli sviluppatori di xamarin. IOS. Contenuto collegato descrive gli avvisi, pulsanti, viste di raccolta, immagini, i controlli dispositivo foto/video manuale, mappe, etichette, le selezioni, controlli selezione data e più.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 477cb4e479224b9e795f3460e0f59e0aa2038630
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790042"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controlli dell'interfaccia utente in xamarin. IOS

Questo documento vengono presentati alcuni dei controlli dell'interfaccia utente più comuni iOS e come utilizzarle.

## <a name="alertsalertsmd"></a>[Alerts](alerts.md) (Avvisi)

A partire da iOS 8, UIAlertController ha completato UIActionSheet sostituito e UIAlertView entrambi che ora sono deprecate.

## <a name="buttonsbuttonsmd"></a>[Pulsanti](buttons.md)

La classe UIButton viene utilizzata per rappresentare vari diversi stili di pulsante nelle schermate di iOS. Questa sezione vengono illustrate le diverse opzioni per l'utilizzo di pulsanti di iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Visualizzazioni raccolta](uicollectionview.md)

Viste di raccolta, disponibile nel `UICollectionView` classe, sono un nuovo concetto in iOS 6 che introducono presenta più elementi sullo schermo utilizzando i layout. I modelli per fornire dati a un `UICollectionView` per creare elementi e interagire con i seguenti elementi la delega stesso e i dati di origine modelli comunemente usati nello sviluppo di iOS.

## <a name="imagesimagemd"></a>[Immagini](image.md)

Aggiunta di immagini per l'app richiede due passaggi: innanzitutto, aggiungere le immagini al progetto. quindi, aggiungere controlli e codice per visualizzarli in una schermata. Consultare la [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni dettagliate copertura dell'immagine di gestione in xamarin. IOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controlli della fotocamera manuali](intro-to-manual-camera-controls.md)

I controlli di fotocamera manuale, fornito dal `AVFoundation Framework` in iOS 8, consentire a un'applicazione per dispositivi mobili assumere il controllo completo della fotocamera del dispositivo iOS. Questo livello di controllo con granularità fine è utilizzabile per creare applicazioni professionali fotocamera livello e fornire le composizioni artista modificando i parametri della camera durante la creazione di un'immagine o un video.

## <a name="mapsios-mapsindexmd"></a>[Mappe](ios-maps/index.md)

Le mappe sono una funzionalità comune in tutti i sistemi operativi moderni dispositivi mobili. iOS offre il supporto di mapping in modo nativo tramite il framework del Kit di mappa. Kit di mappa, le applicazioni possono aggiungere facilmente mappe dettagliate e interattive. Queste mappe possono essere personalizzate in diversi modi, ad esempio l'aggiunta di annotazioni per contrassegnare i percorsi su una mappa e la sovrapposizione di elementi grafici di impatto su forme arbitrarie. Kit di mappa ha anche il supporto incorporato per mostrare la posizione corrente di un dispositivo.

## <a name="labelslabelsmd"></a>[Etichette](labels.md)

Il `UILabel` controllo viene utilizzato per visualizzare uno o più righe, testo di sola lettura.

## <a name="pickers-and-date-pickerspickermd"></a>[Gli operatori e i controlli selezione data](picker.md)

Il controllo di selezione Visualizza ' rotellina ' controllo che contiene un elenco di valori con il valore selezionato viene evidenziato scorrevole. Gli utenti ruota la rotellina per selezionare l'opzione desiderata.

Un utente specifico del case per controlli di selezione per impostare la data e / o l'ora. Per specificare per questo Apple ha creato una sottoclasse della classe UIPickerView chiamata UIDatePicker personalizzata.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicatori di stato e attività](progress-activity-indicator.md)

iOS fornisce due metodi principali per indicare lo stato di avanzamento nell'app: gli indicatori di attività (incluso uno specifico _rete_ indicatore di attività) e indicatori di stato.

## <a name="search-barssearchbarmd"></a>[Barre di ricerca](searchbar.md)

Il UISearchBar viene utilizzato per cercare un elenco di valori. 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Dispositivi di scorrimento, i gestori di istruzioni e controlli segmentati](slider-switch-segmented-controls.md)

Il dispositivo di scorrimento consente di selezione semplice di un valore numerico in un intervallo. iOS utilizza il `UISwitch` come input di un valore booleano che può essere rappresentato da un pulsante di opzione in altre piattaforme. Un controllo segmentata è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni.

## <a name="stack-viewuistackviewmd"></a>[Visualizzazione dello stack](uistackview.md)

Controllo di visualizzazione dello Stack (`UIStackView`) si avvale di Layout automatico e le classi di dimensioni per gestire una pila di sottoviste, orizzontale o verticale, che rappresenta le risposte in modo dinamico per le dimensioni di schermata e orientamento del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tabelle e celle](tables/index.md)

la sezione vengono descritte le classi usate per creare e visualizzare le tabelle, quindi vengono forniti esempi su come utilizzarle in xamarin. IOS. Che verrà applicata mediante l'aspetto predefinito per le tabelle, personalizzazione del layout, l'implementazione di modifica e l'utilizzo di Xamarin iOS finestra di progettazione per progettare visivamente una tabella. In alcuni casi la visualizzazione è ovviamente un elenco di righe (ad esempio, l'app musica), mentre altre volte che è difficile riconoscono il controllo tabella (ad esempio la modifica, l'app dei contatti o una conversazione in app di messaggi).

## <a name="text-inputtext-inputmd"></a>[Input di testo](text-input.md)

Accettare input di testo dell'utente viene eseguita con il `UITextField` per gli input a riga singola e UITextView per più righe di testo modificabile. È possibile trascinare uno di questi controlli in una schermata e fare doppio clic per impostare il testo iniziale.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barre schede e controller di barra schede](creating-tabbed-applications.md)

le applicazioni iOS utilizzando un'interfaccia utente di spostamento tramite tabulazione vengono compilate mediante la classe UITabBarController. In questo articolo verrà esaminato come configurare un'applicazione a schede contenente più controller e visualizzazioni. Si esamineranno quindi illustrato come caricare un UITabBarController quando non è il controller principale, ad esempio dopo una schermata di accesso.

## <a name="web-viewsuiwebviewmd"></a>[Visualizzazioni Web](uiwebview.md)

In questo articolo vengono analizzate ognuna delle tre visualizzazioni Web fornite da Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, le analogie e differenze e come possono essere utilizzati.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
