---
title: Controlli dell'interfaccia utente in xamarin. IOS
description: Questo documento include collegamenti alle guide che descrivono le varie iOS controlli dell'interfaccia utente disponibili per gli sviluppatori di xamarin. IOS. Contenuto collegato descrive avvisi, pulsanti, le visualizzazioni di raccolta, immagini, controlli della fotocamera manuali, mappe, etichette, controlli di selezione, i controlli selezione data e più.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d5b7d4a372704079343a357c1d341a5260fcf583
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157416"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controlli dell'interfaccia utente in xamarin. IOS

Questo documento illustra alcuni dei controlli dell'interfaccia utente iOS più comuni e come usarli.

## <a name="alertsalertsmd"></a>[Alerts](alerts.md) (Avvisi)

A partire da iOS 8, UIAlertController ha completato UIActionSheet sostituito e UIAlertView entrambe di cui sono stati deprecati.

## <a name="buttonsbuttonsmd"></a>[Pulsanti](buttons.md)

La classe UIButton viene utilizzata per rappresentare vari diversi stili di pulsante nelle schermate di iOS. Questa sezione illustra le diverse opzioni per l'uso di pulsanti in iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Visualizzazioni raccolta](uicollectionview.md)

Visualizzazioni di raccolta, disponibile nel `UICollectionView` di classi, sono un nuovo concetto in iOS 6 che introducono la presentazione di più elementi sullo schermo utilizzando i layout. I modelli per fornire dati a un `UICollectionView` per creare elementi e interagire con i seguenti elementi la delega stesso e i dati di origine i modelli comunemente usati nello sviluppo di iOS.

## <a name="imagesimagemd"></a>[Immagini](image.md)

Aggiunta di immagini per l'app richiede due passaggi: in primo luogo, aggiungere le immagini al progetto. quindi, aggiungere i controlli e codice per visualizzarli in una schermata. Vedere la [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni dettagliate di code coverage dell'immagine gestisce in xamarin. IOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controlli della fotocamera manuali](intro-to-manual-camera-controls.md)

I controlli della fotocamera manuali, fornito dal `AVFoundation Framework` in iOS 8, consentire a un'applicazione per dispositivi mobili assumere il controllo completo della fotocamera del dispositivo iOS. Questo livello di controllo con granularità fine è utilizzabile per creare applicazioni professionali e a livello della fotocamera e fornire le composizioni artista modificando i parametri della fotocamera durante la creazione di un'immagine o un video.

## <a name="mapsios-mapsindexmd"></a>[Mappe](ios-maps/index.md)

Le mappe sono una funzionalità comune in tutti i sistemi operativi per dispositivi mobili moderni. iOS offre il supporto di mapping in modo nativo tramite il framework Map Kit. Con il Framework Map Kit, le applicazioni possono aggiungere con facilità mappe avanzate e interattive. Queste mappe possono essere personalizzate in svariati modi, ad esempio l'aggiunta di annotazioni per contrassegnare le posizioni in una mappa e la sovrapposizione di elementi grafici di impatto su forme arbitrarie. Il Framework Map Kit include anche supporto predefinito per che mostra la posizione corrente di un dispositivo.

## <a name="labelslabelsmd"></a>[Etichette](labels.md)

Il `UILabel` controllo viene usato per la visualizzazione singola e a più righe, testo di sola lettura.

## <a name="pickers-and-date-pickerspickermd"></a>[Gli operatori e i controlli selezione data](picker.md)

Il controllo di selezione Visualizza controllo 'rotellina simile a' che contiene un elenco scorrevole dei valori con il valore selezionato viene evidenziato. Gli utenti ruota la rotellina per selezionare l'opzione desiderata.

Un utente specifico caso per controlli di selezione per impostare la data e / o ora. Per fornire per questo Apple ha creato una sottoclasse della classe UIPickerView chiamata UIDatePicker personalizzata.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicatori di stato e attività](progress-activity-indicator.md)

iOS offre due modi principali per indicare lo stato di avanzamento nell'app: Gli indicatori di attività (tra cui una determinata _rete_ indicatore dell'attività) e le barre di avanzamento.

## <a name="search-barssearchbarmd"></a>[Le barre di ricerca](searchbar.md)

Il UISearchBar viene utilizzato per eseguire ricerche in un elenco di valori. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Dispositivi di scorrimento, interruttori e controlli segmentati](slider-switch-segmented-controls.md)

Controllo dispositivo di scorrimento consente la semplice selezione di un valore numerico in un intervallo. iOS Usa il `UISwitch` come input un valore booleano che può essere rappresentato da un pulsante di opzione in altre piattaforme. Un controllo Segmented è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni.

## <a name="stack-viewuistackviewmd"></a>[Visualizzazione dello stack](uistackview.md)

Il controllo di visualizzazione dello Stack (`UIStackView`) sfrutta la potenza del Layout automatico e le classi di dimensioni per gestire una pila di visualizzazioni secondarie, orizzontalmente o verticalmente, che risponde dinamicamente alle dimensioni dello schermo e l'orientamento del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tabelle e celle](tables/index.md)

la sezione introduce le classi usate per creare e visualizzare tabelle, quindi vengono forniti esempi su come usarli in xamarin. IOS. Verrà coperto utilizzando l'aspetto predefinito per le tabelle, personalizzazione del layout, implementazione della modifica e l'uso di Xamarin iOS Designer progettare visivamente una tabella. In alcuni casi la visualizzazione è ovviamente un elenco di righe (ad esempio, l'app Music) e altre volte invece lo difficile da riconoscere il controllo della tabella (ad esempio modifica di una conversazione nell'app per i messaggi, o nell'app contatti).

## <a name="text-inputtext-inputmd"></a>[Input di testo](text-input.md)

Accettare input di testo dell'utente viene eseguita con il `UITextField` per gli input a riga singola e UITextView testo modificabile su più righe. È possibile trascinare uno di questi controlli in una schermata e fare doppio clic per impostare il testo iniziale.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barre schede e controller di barra schede](creating-tabbed-applications.md)

le applicazioni iOS usando un'interfaccia utente della scheda di navigazione vengono create usando la classe UITabBarController. In questo articolo illustra come configurare un'applicazione a schede che contiene diversi controller e visualizzazioni. Verrà quindi esaminato come caricare un UITabBarController quando non è il controller principale, ad esempio dopo una schermata di accesso.

## <a name="web-viewsuiwebviewmd"></a>[Visualizzazioni Web](uiwebview.md)

In questo articolo, verranno analizzati ognuna delle tre visualizzazioni Web fornite da Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, le analogie e differenze e come possono essere usate.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
