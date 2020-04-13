---
title: Riassunto del capitolo 6. Clic sui pulsanti
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 6. Clic sui pulsanti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334715"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Riassunto del capitolo 6. Clic sui pulsanti

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

La [`Button`](xref:Xamarin.Forms.Button) è la visualizzazione che consente all'utente di avviare un comando. A `Button` è identificato dal testo (e facoltativamente da un'immagine come illustrato nel [capitolo 13, Bitmap](chapter13.md)). Di conseguenza, `Button` definisce molte `Label`delle stesse proprietà di :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`definisce anche tre proprietà che regolano l'aspetto del suo confine, ma il supporto di queste proprietà e la loro indipendenza reciproca è specifico della piattaforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)di tipo`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)di tipo`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)di tipo`Double`

`Button`eredita inoltre tutte le `VisualElement` `View`proprietà `BackgroundColor`di `HorizontalOptions`e `VerticalOptions`, tra cui , e .

## <a name="processing-the-click"></a>Elaborazione del clic

La `Button` classe definisce un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento che viene `Button`generato quando l'utente tocca il file . Il `Click` gestore `EventHandler`è di tipo . Il primo argomento `Button` è l'oggetto che genera l'evento; il secondo argomento `EventArgs` è un oggetto che non fornisce informazioni aggiuntive.

[**Nell'esempio ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) viene illustrata una gestione semplice. `Clicked`

## <a name="sharing-button-clicks"></a>Clic sul pulsante Condivisione

Più `Button` visualizzazioni possono `Clicked` condividere lo stesso gestore, `Button` ma il gestore in genere deve determinare quale è responsabile di un determinato evento. Un approccio consiste `Button` nell'archiviare i vari oggetti come campi e verificare quale sta generando l'evento nel gestore.

Il [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) esempio viene illustrata questa tecnica. Il programma dimostra inoltre [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) come impostare `false` la `Button` proprietà di a `Button` quando si preme il non è più valido. Un `Button` disabilitato non `Clicked` genera un evento.

## <a name="anonymous-event-handlers"></a>Gestori eventi anonimi

È possibile definire `Clicked` i gestori come funzioni lambda anonime, come illustrato nell'esempio [**ButtonLambdas.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) Tuttavia, i gestori anonimi non possono essere condivisi senza codice di reflection disordinato.

## <a name="distinguishing-views-with-ids"></a>Distinguere le visualizzazioni con gli ID

È `Button` inoltre possibile distinguere più [`StyleId`](xref:Xamarin.Forms.Element.StyleId) oggetti [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) impostando `string`la proprietà o la proprietà su un oggetto . Questa proprietà è `Element` definita da ma non viene utilizzata all'interno di Xamarin.Forms.This property is defined by but it is not used within Xamarin.Forms. È destinato ad essere utilizzato esclusivamente dai programmi applicativi.

L'esempio [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) usa lo stesso gestore eventi per tutti i 10 tasti `StyleId` numerici in un tastierino numerico e ne distingue con la proprietà:

[![Tripla schermata della tastiera più semplice](images/ch06fg04-small.png "Calcolatrice")](images/ch06fg04-large.png#lightbox "Calcolatrice")

## <a name="saving-transient-data"></a>Salvataggio dei dati temporanei

Molte applicazioni devono salvare i dati quando un programma viene terminato e ricaricare i dati quando il programma viene riavviato. La [`Application`](xref:Xamarin.Forms.Application) classe definisce diversi membri che consentono al programma di salvare e ripristinare i dati temporanei:The class defines several members that help your program save and restore transient data:

- La [`Properties`](xref:Xamarin.Forms.Application.Properties) proprietà è `string` un `object` dizionario con chiavi ed elementi. Il contenuto del dizionario viene salvato automaticamente nella memoria locale dell'applicazione prima della chiusura del programma e ricaricato all'avvio del programma.
- La `Application` classe definisce tre metodi virtuali protetti `App` di cui [`OnStart`](xref:Xamarin.Forms.Application.OnStart) [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)la [`OnResume`](xref:Xamarin.Forms.Application.OnResume)classe standard del programma esegue l'override: , e . Questi si riferiscono agli eventi *del ciclo di vita dell'applicazione.*
- Il [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo salva il contenuto del dizionario.

Non è necessario `SavePropertiesAsync`chiamare . Il contenuto del dizionario viene salvato automaticamente prima della chiusura del programma e recuperato prima dell'avvio del programma. È utile durante il test del programma per salvare i dati se il programma si blocca.

Utile anche:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una proprietà statica `Application` che restituisce l'oggetto `Properties` corrente che è quindi possibile utilizzare per ottenere il dizionario.

Il primo passaggio consiste nell'identificare tutte le variabili nella pagina che si desidera mantenere quando il programma termina. Se si conoscono tutti i luoghi in cui tali `Properties` variabili cambiano, è sufficiente aggiungerle al dizionario in quel punto. Nel costruttore della pagina, è possibile `Properties` impostare le variabili dal dizionario se la chiave esiste.

Un programma più grande sarà probabilmente necessario gestire gli eventi del ciclo di vita dell'applicazione. Il più importante `OnSleep` è il metodo. Una chiamata a questo metodo indica che il programma ha lasciato il primo piano. Forse l'utente ha premuto il pulsante **Home** sul dispositivo, o visualizzato tutte le applicazioni, o sta spegnendo il telefono. Una chiamata `OnSleep` a è l'unica notifica che un programma riceve prima di essere terminata. Il programma dovrebbe cogliere l'occasione per garantire che il `Properties` dizionario sia aggiornato.

Una chiamata `OnResume` per indicare che il programma non `OnSleep` è stato terminato dopo l'ultima chiamata a ma è ora in esecuzione nuovamente in primo piano. Il programma potrebbe sfruttare questa opportunità per aggiornare le connessioni Internet (ad esempio).

Una chiamata `OnStart` a si verifica durante l'avvio del programma. Non è necessario attendere fino a quando `Properties` questa chiamata al metodo per `App` accedere al dizionario perché il contenuto è già stato ripristinato quando viene chiamato il costruttore.

L'esempio [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) è molto simile a **SimplestKeypad,** ad eccezione del fatto che il programma utilizza l'override `OnSleep` per salvare la voce della tastiera corrente e il costruttore della pagina per ripristinare i dati.

> [!NOTE]
> Un altro approccio al salvataggio delle impostazioni del programma è fornito dalla classe Xamarin.Essentials [Preferences.](~/essentials/preferences.md)

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 6 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Esempi del capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Capitolo 6 Esempi di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Pulsante Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
