---
title: Riepilogo del capitolo 6. Fa clic sul pulsante
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 6. Fa clic sul pulsante'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9c8a4e6a6c4d62722e72ff3b27251987aa201284
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240749"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Riepilogo del capitolo 6. Fa clic sul pulsante

Il [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) è la visualizzazione che consente all'utente di avviare un comando. Oggetto `Button` viene identificata dal testo (e facoltativamente un'immagine come illustrato in [capitolo 13, bitmap](chapter13.md)). Di conseguenza, `Button` definisce molte delle stesse proprietà di `Label`:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/)
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontFamily/)
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontAttributes/)
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)

`Button` definisce inoltre le tre proprietà che controllano l'aspetto del bordo, ma il supporto di queste proprietà e la loro indipendenza reciproca è specifico della piattaforma:

- [`BorderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderColor/) di tipo `Color`
- [`BorderWidth`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderWidth/) di tipo `Double`
- [`BorderRadius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/) di tipo `Double`

`Button` eredita anche tutte le proprietà di `VisualElement` e `View`, tra cui `BackgroundColor`, `HorizontalOptions`, e `VerticalOptions`.

## <a name="processing-the-click"></a>Fare clic su di elaborazione

Il `Button` classe definisce un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) evento generato quando l'utente tocca il `Button`. Il `Click` gestore è di tipo `EventHandler`. Il primo argomento è il `Button` l'oggetto che genera l'evento; il secondo argomento è un `EventArgs` oggetto che non fornisce nessuna informazione aggiuntiva.

Il [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustra semplici `Clicked` gestione.

## <a name="sharing-button-clicks"></a>Pulsante di condivisione fa clic su

Più `Button` viste possono condividere lo stesso `Clicked` gestore, ma il gestore deve in genere determinare quale `Button` è responsabile di un determinato evento. Un approccio consiste nel memorizzare i diversi `Button` oggetti come campi e quello che sta generando l'evento nel gestore di controllo.

Il [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) illustra questa tecnica. Il programma viene inoltre illustrato come impostare il [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) proprietà di un `Button` a `false` quando si preme il `Button` non è più valido. Un disattivato `Button` non genera un `Clicked` evento.

## <a name="anonymous-event-handlers"></a>Gestori eventi anonimo

È possibile definire `Clicked` gestori come funzioni lambda anonima, come il [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) viene illustrato l'esempio. Tuttavia, non condivisibile gestori anonimi senza codice disordinato reflection.

## <a name="distinguishing-views-with-ids"></a>Distinzione viste con ID

Più `Button` oggetti sono inoltre possibile distinguere impostando il [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) proprietà o [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) proprietà per un `string`. Questa proprietà è definita da `Element` ma non viene utilizzato all'interno di xamarin. Forms. Destinato a essere utilizzato esclusivamente da parte delle applicazioni.

Il [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) l'esempio Usa lo stesso gestore eventi per tutte le chiavi numero 10 un tastierino numerico e consente di distinguere tra di essi con il `StyleId` proprietà:

[![Schermata di triplo del tastierino più semplice](images/ch06fg04-small.png "Calcolatrice")](images/ch06fg04-large.png#lightbox "calcolatrice")

## <a name="saving-transient-data"></a>Salvataggio di dati temporaneo

Molte applicazioni necessarie per salvare i dati quando un programma viene terminato e ricaricare i dati quando il programma è stato riavviato. Il [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) classe definisce molti membri che consentono di salvare e ripristinare i dati temporanei del programma:

- Il [ `Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) proprietà è un dizionario con `string` chiavi e `object` elementi. Il contenuto del dizionario venga automaticamente salvato nell'archiviazione locale di applicazione prima della chiusura del programma e ricaricato quando il programma di avvio.
- Il `Application` classe definisce tre metodi virtuali protetti che il programma della standard `App` classe sostituzioni: [ `OnStart` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnStart()/), [ `OnSleep` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnSleep()/), e [ `OnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnResume()/). Questi elementi fanno riferimento a *ciclo di vita dell'applicazione* eventi.
- Il [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) metodo salva il contenuto del dizionario.

Non è necessario chiamare `SavePropertiesAsync`. Il contenuto del dizionario viene automaticamente salvato prima della chiusura del programma e recuperato prima dell'avvio del programma. È utile durante il test del programma per salvare i dati se il programma si blocca.

Inoltre è stato utile:

- [`Application.Current`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Current/), una proprietà statica che restituisce l'attuale `Application` oggetto che è quindi possibile utilizzare per ottenere il `Properties` dizionario.

Il primo passaggio consiste nell'identificare tutte le variabili nella pagina che si desidera mantenere quando il programma termina. Se si conoscono tutte le posizioni in cui modificare tali variabili, è possibile aggiungere semplicemente che il `Properties` dizionario a quel punto. Nel costruttore della pagina, è possibile impostare le variabili dal `Properties` dizionario se la chiave esiste.

Un programma più vasto, probabilmente è necessario gestire gli eventi del ciclo di vita dell'applicazione. La più importante è il `OnSleep` metodo. Una chiamata a questo metodo indica che il programma ha lasciato il primo piano. Ad esempio l'utente ha premuto il **Home** pulsante sul dispositivo, visualizzare tutte le applicazioni o è in corso l'arresto del telefono. Una chiamata a `OnSleep` è la sola notifica che un programma riceve prima di essere terminata. Il programma deve sfruttare questa opportunità per assicurarsi che il `Properties` dizionario è aggiornato.

Una chiamata a `OnResume` indica che il programma non è stato terminato dopo l'ultima chiamata a `OnSleep` ma è ora nuovamente in esecuzione in primo piano. Il programma potrebbe scegliere questa opzione per aggiornare le connessioni internet (ad esempio).

Una chiamata a `OnStart` si verifica durante l'avvio del programma. Non è necessario attendere fino a quando non chiamare questo metodo per l'accesso di `Properties` dizionario perché il contenuto è già stati ripristinati quando il `App` costruttore viene chiamato.

Il [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) è molto simile all'esempio **SimplestKeypad** ad eccezione del fatto che il programma utilizza il `OnSleep` sostituzione consente di salvare la voce tastierino corrente, e il costruttore della pagina per ripristinare i dati.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 6 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Esempi di capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Esempi di capitolo 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
