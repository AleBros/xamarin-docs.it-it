---
Titolo: "Riepilogo del capitolo 6. Button click "Description:" Create Mobile Apps with Xamarin.Forms : riepilogo del capitolo 6. Button click "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: D4F9C429-A6CF-40FA-AC68-3F149307A5F9 Author: davidbritch ms. Author: dabritch ms. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-6-button-clicks"></a>Riepilogo del capitolo 6. Clic sui pulsanti

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

[`Button`](xref:Xamarin.Forms.Button)È la visualizzazione che consente all'utente di avviare un comando. Un oggetto `Button` viene identificato dal testo (e, facoltativamente, da un'immagine, come illustrato nel [capitolo 13, bitmap](chapter13.md)). Di conseguenza, `Button` definisce molte delle stesse proprietà di `Label` :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`definisce anche tre proprietà che regolano l'aspetto del bordo, ma il supporto di queste proprietà e la loro indipendenza reciproca sono specifiche della piattaforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)di tipo`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)di tipo`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)di tipo`Double`

`Button`eredita inoltre tutte le proprietà di `VisualElement` e `View` , tra cui `BackgroundColor` , `HorizontalOptions` e `VerticalOptions` .

## <a name="processing-the-click"></a>Elaborazione del clic

La `Button` classe definisce un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento che viene generato quando l'utente tocca `Button` . Il `Click` gestore è di tipo `EventHandler` . Il primo argomento è l' `Button` oggetto che genera l'evento; il secondo argomento è un `EventArgs` oggetto che non fornisce informazioni aggiuntive.

L'esempio [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustra una `Clicked` gestione semplice.

## <a name="sharing-button-clicks"></a>Clic sui pulsanti di condivisione

Più `Button` visualizzazioni possono condividere lo stesso `Clicked` gestore, ma in genere il gestore deve determinare quale `Button` è responsabile di un evento specifico. Un approccio consiste nell'archiviare i vari `Button` oggetti come campi e verificare quale sia la generazione dell'evento nel gestore.

Questa tecnica è illustrata nell'esempio [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) . Il programma illustra anche come impostare la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà di un `Button` su `false` quando la pressione di `Button` non è più valida. Un oggetto disabilitato non `Button` genera un `Clicked` evento.

## <a name="anonymous-event-handlers"></a>Gestori di eventi anonimi

È possibile definire `Clicked` i gestori come funzioni lambda anonime, come illustrato nell'esempio [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . Tuttavia, i gestori anonimi non possono essere condivisi senza alcuna confusione di codice di Reflection.

## <a name="distinguishing-views-with-ids"></a>Distinzione di viste con ID

`Button`È inoltre possibile distinguere più oggetti impostando la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) proprietà o la [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) proprietà su un oggetto `string` . Questa proprietà è definita da `Element` , ma non viene usata all'interno di Xamarin.Forms . Deve essere utilizzato esclusivamente dai programmi applicativi.

Nell'esempio [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) viene utilizzato lo stesso gestore eventi per tutte le 10 chiavi numeriche su un tastierino numerico e viene fatta distinzione tra di essi con la `StyleId` proprietà:

[![Schermata tripla del tastierino più semplice](images/ch06fg04-small.png "Calcolatrice")](images/ch06fg04-large.png#lightbox "Calcolatrice")

## <a name="saving-transient-data"></a>Salvataggio di dati temporanei

Molte applicazioni devono salvare i dati quando un programma viene terminato e ricaricare i dati quando il programma viene riavviato. La [`Application`](xref:Xamarin.Forms.Application) classe definisce diversi membri che consentono al programma di salvare e ripristinare i dati temporanei:

- La [`Properties`](xref:Xamarin.Forms.Application.Properties) proprietà è un dizionario con `string` chiavi ed `object` elementi. Il contenuto del dizionario viene salvato automaticamente nell'archivio locale dell'applicazione prima della terminazione del programma e viene ricaricato all'avvio del programma.
- La `Application` classe definisce tre metodi virtuali protetti che la classe standard del programma `App` sostituisce: [`OnStart`](xref:Xamarin.Forms.Application.OnStart) , [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) e [`OnResume`](xref:Xamarin.Forms.Application.OnResume) . Che fanno riferimento agli eventi del ciclo di vita *dell'applicazione* .
- Il [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo salva il contenuto del dizionario.

Non è necessario chiamare `SavePropertiesAsync` . Il contenuto del dizionario viene salvato automaticamente prima della terminazione del programma e recuperato prima dell'avvio del programma. È utile durante i test del programma per salvare i dati in caso di arresto anomalo del programma.

Utile anche:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), una proprietà statica che restituisce l' `Application` oggetto corrente che è possibile usare per ottenere il `Properties` dizionario.

Il primo passaggio consiste nell'identificare tutte le variabili nella pagina che si desidera salvare in modo permanente al termine del programma. Se si conoscono tutte le posizioni in cui tali variabili cambiano, è possibile aggiungerle semplicemente al `Properties` dizionario in quel momento. Nel costruttore della pagina è possibile impostare le variabili dal `Properties` dizionario se la chiave esiste.

Un programma di dimensioni maggiori probabilmente dovrà gestire gli eventi del ciclo di vita dell'applicazione. Il più importante è il `OnSleep` metodo. Una chiamata a questo metodo indica che il programma ha lasciato il primo piano. È probabile che l'utente abbia premuto il pulsante **Home** sul dispositivo o abbia visualizzato tutte le applicazioni o stia arrestando il telefono. Una chiamata a `OnSleep` è l'unica notifica ricevuta da un programma prima che venga terminata. Il programma dovrebbe avere questa opportunità per assicurarsi che il `Properties` dizionario sia aggiornato.

Una chiamata a `OnResume` indica che il programma non è stato terminato dopo l'ultima chiamata a `OnSleep` , ma ora è in esecuzione in primo piano. Il programma può utilizzare questa opportunità per aggiornare le connessioni Internet (ad esempio).

Una chiamata a `OnStart` si verifica durante l'avvio del programma. Non è necessario attendere il completamento della chiamata al metodo per accedere al `Properties` dizionario perché il contenuto è già stato ripristinato quando `App` viene chiamato il costruttore.

L'esempio [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) è molto simile a **SimplestKeypad** , ad eccezione del fatto che il programma usa la `OnSleep` sostituzione per salvare la voce del tastierino corrente e il costruttore della pagina per ripristinare i dati.

> [!NOTE]
> Un altro approccio al salvataggio delle impostazioni del programma è fornito dalla classe di Xamarin.Essentials [Preferenze](~/essentials/preferences.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 6 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Esempi del capitolo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Esempi del capitolo 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.FormsPulsante](~/xamarin-forms/user-interface/button.md)
