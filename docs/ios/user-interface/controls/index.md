---
title: Controlli dell'interfaccia utente in Xamarin.iOS
description: Questo documento contiene collegamenti a guide che descrivono i vari controlli dell'interfaccia utente iOS disponibili per gli sviluppatori Xamarin.iOS. Il contenuto collegato illustra gli avvisi, i pulsanti, le visualizzazioni di raccolta, le immagini, i controlli manuali della fotocamera, le mappe, le etichette, i selezionatori, le selezioni di data e altro ancora.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a4bf0b89a9ab336bf47ddcd104760211d912f423
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663434"
---
# <a name="user-interface-controls-in-xamarinios"></a>Controlli dell'interfaccia utente in Xamarin.iOS

Questo documento introduce alcuni dei controlli più comuni dell'interfaccia utente iOS e spiega come usarli.

## <a name="alertsalertsmd"></a>[Alerts](alerts.md) (Avvisi)

A partire da iOS 8, UIAlertController ha completato la sostituzione di UIActionSheet e UIAlertView che ora sono deprecate.

## <a name="buttonsbuttonsmd"></a>[Pulsanti](buttons.md)

La classe UIButton viene usata per rappresentare diversi stili di pulsante nelle schermate iOS. Questa sezione presenta le diverse opzioni per l'uso dei pulsanti in iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Visualizzazioni raccolta](uicollectionview.md)

Le visualizzazioni di raccolta, disponibili nella classe `UICollectionView`, sono un nuovo concetto di iOS 6 che introduce la presentazione di più elementi sullo schermo usando i layout. I modelli per fornire i dati a un `UICollectionView` per creare elementi e interagire con tali elementi seguono gli stessi modelli di delega e origine dati usati comunemente nello sviluppo iOS.

## <a name="imagesimagemd"></a>[Immagini](image.md)

Per aggiungere immagini all'app sono necessari due passaggi: prima di tutto, aggiungere le immagini al progetto; quindi, aggiungere i controlli e il codice per visualizzarli in una schermata. Per informazioni più dettagliate sulla gestione delle immagini in Xamarin.iOS, vedere l'articolo [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) .

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Controlli della fotocamera manuali](intro-to-manual-camera-controls.md)

I controlli della fotocamera manuale, forniti dal `AVFoundation Framework` in iOS 8, consentono a un'applicazione per dispositivi mobili di assumere il controllo completo sulla fotocamera di un dispositivo iOS. Questo livello di controllo con granularità fine può essere usato per creare applicazioni per la fotocamera a livello professionale e fornire composizioni degli artisti modificando i parametri della fotocamera durante l'acquisizione di un'immagine o un video ancora.

## <a name="mapsios-mapsindexmd"></a>[Mappe](ios-maps/index.md)

Le mappe sono una funzionalità comune in tutti i moderni sistemi operativi mobili. iOS offre supporto per il mapping in modo nativo tramite il Framework del kit di mappe. Con Map Kit, le applicazioni possono aggiungere facilmente mappe interattive e complesse. Queste mappe possono essere personalizzate in diversi modi, ad esempio aggiungendo annotazioni per contrassegnare le posizioni su una mappa e sovrapponendo la grafica di forme arbitrarie. Map Kit include anche il supporto predefinito per visualizzare la posizione corrente di un dispositivo.

## <a name="labelslabelsmd"></a>[Etichette](labels.md)

Il controllo `UILabel` viene utilizzato per la visualizzazione di testo di sola lettura e a più righe.

## <a name="pickers-and-date-pickerspickermd"></a>[Selezionatori e selezionatori data](picker.md)

Il controllo selezione consente di visualizzare il controllo "Wheel-like" che contiene un elenco scorrevole di valori con il valore selezionato evidenziato. Gli utenti ruotano la rotellina per selezionare l'opzione desiderata.

Un caso utente specifico per i selezionatori per impostare la data e/o l'ora. Per consentire a questo Apple è stata creata una sottoclasse personalizzata della classe UIPickerView denominata UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicatori di stato e attività](progress-activity-indicator.md)

iOS offre due modi principali per indicare lo stato di avanzamento nell'app: gli indicatori di attività (incluso un indicatore di attività di _rete_ specifico) e le barre di stato.

## <a name="search-barssearchbarmd"></a>[Barre di ricerca](searchbar.md)

UISearchBar viene usato per eseguire la ricerca in un elenco di valori. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Dispositivi di scorrimento, interruttori e controlli segmentati](slider-switch-segmented-controls.md)

Il dispositivo di scorrimento consente di selezionare in modo semplice un valore numerico all'interno di un intervallo. iOS usa il `UISwitch` come input booleano che può essere rappresentato da un pulsante di opzione su altre piattaforme. Un controllo segmentato è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni.

## <a name="stack-viewuistackviewmd"></a>[Visualizzazione dello stack](uistackview.md)

Il controllo di visualizzazione stack (`UIStackView`) sfrutta la potenza del layout automatico e delle classi di dimensioni per gestire uno stack di visualizzazioni, orizzontalmente o verticalmente, che risponde dinamicamente all'orientamento e alle dimensioni dello schermo del dispositivo iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tabelle e celle](tables/index.md)

in questa sezione vengono presentate le classi utilizzate per creare e visualizzare le tabelle, quindi vengono forniti esempi di utilizzo in Xamarin.iOS. Verrà utilizzato l'aspetto predefinito per le tabelle, la personalizzazione del layout, l'implementazione della modifica e l'utilizzo di Xamarin iOS designer per progettare una tabella visivamente. In alcuni casi, la visualizzazione è ovviamente un elenco di righe (ad esempio l'app musica) e altre volte è difficile riconoscere il controllo tabella, ad esempio la modifica nell'app Contatti o una conversazione nell'app messaggi.

## <a name="text-inputtext-inputmd"></a>[Input di testo](text-input.md)

L'accettazione dell'input di testo utente viene eseguita con la `UITextField` per gli input a riga singola e UITextView per il testo modificabile su più righe. È possibile trascinare uno di questi controlli su una schermata e fare doppio clic per impostare il testo iniziale.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barre schede e controller di barra schede](creating-tabbed-applications.md)

le applicazioni iOS che usano un'interfaccia utente per la navigazione tramite tabulazione vengono create usando la classe UITabBarController. In questo articolo verrà illustrato come configurare un'applicazione a schede che contiene diversi controller e visualizzazioni. Si esaminerà quindi come caricare un UITabBarController quando non è il controller radice, ad esempio dopo una schermata di accesso.

## <a name="web-viewswebviewmd"></a>[Visualizzazioni Web](webview.md)

In questo articolo verranno illustrate le visualizzazioni Web fornite da Apple-`WKWebview` e `SFSafariViewController`, ovvero le similitudini e le differenze, nonché il modo in cui possono essere utilizzate.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
