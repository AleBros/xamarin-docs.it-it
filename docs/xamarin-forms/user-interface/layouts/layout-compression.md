---
title: "compressione del layout" Descrizione: "la compressione del layout rimuove i layout specificati dalla struttura ad albero visuale nel tentativo di migliorare le prestazioni di rendering della pagina. Questo articolo illustra come abilitare la compressione del layout e i vantaggi che può offrire. "
ms. prod: Novell MS. AssetID: da9e1b26-9d31-4762-94c3-4039f306b7f2 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/13/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="layout-compression"></a>Compressione del layout

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_La compressione del layout rimuove i layout specificati dalla struttura ad albero visuale nel tentativo di migliorare le prestazioni di rendering della pagina. Questo articolo illustra come abilitare la compressione del layout e i vantaggi che può offrire._

## <a name="overview"></a>Panoramica

Xamarin.Formsesegue il layout utilizzando due serie di chiamate al metodo ricorsive:

- Il layout inizia dalla parte superiore della struttura ad albero visuale con una pagina e procede attraverso tutti i rami della struttura ad albero visuale per includere ogni elemento visivo in una pagina. Gli elementi padre di altri elementi sono responsabili del dimensionamento e del posizionamento degli elementi figlio relativi a se stessi.
- L'invalidamento è il processo mediante il quale una modifica in un elemento di una pagina attiva un nuovo ciclo di layout. Gli elementi vengono considerati non validi se non hanno più la dimensione o la posizione corretta. Ogni elemento della struttura ad albero visuale con elementi figlio viene avvisato quando uno dei relativi elementi figlio cambia dimensioni. Pertanto, una modifica delle dimensioni di un elemento nella struttura ad albero visuale può causare modifiche che ripplero l'albero.

Per ulteriori informazioni su come viene Xamarin.Forms eseguito il layout, vedere [creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

Il risultato del processo di layout è una gerarchia di controlli nativi. Tuttavia, questa gerarchia include renderer e wrapper aggiuntivi del contenitore per i renderer della piattaforma, incrementando ulteriormente la nidificazione della gerarchia di visualizzazione. Maggiore è il livello di annidamento, maggiore è la quantità di lavoro che Xamarin.Forms deve eseguire per visualizzare una pagina. Per i layout complessi, la gerarchia di visualizzazione può essere sia profonda che ampia, con più livelli di nidificazione.

Si consideri, ad esempio, il seguente pulsante dell'applicazione di esempio per l'accesso a Facebook:

![](layout-compression-images/facebook-button.png "Facebook Button")

Questo pulsante viene specificato come controllo personalizzato con la seguente gerarchia di visualizzazione XAML:

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

La gerarchia di visualizzazione nidificata risultante può essere esaminata con [Xamarin Inspector](~/tools/inspector/index.md). In Android la gerarchia della visualizzazione nidificata contiene 17 visualizzazioni:

![](layout-compression-images/no-compression.png "View Hierarchy for Facebook Button")

La compressione del layout, disponibile per le Xamarin.Forms applicazioni sulle piattaforme iOS e Android, mira a appiattire la nidificazione della visualizzazione rimuovendo i layout specificati dalla struttura ad albero visuale, che può migliorare le prestazioni di rendering delle pagine. Il vantaggio per le prestazioni fornito varia a seconda della complessità di una pagina, della versione del sistema operativo in uso e del dispositivo in cui è in esecuzione l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

> [!NOTE]
> Questo articolo è incentrato sui risultati dell'applicazione della compressione del layout in Android, ma è ugualmente applicabile a iOS.

## <a name="layout-compression"></a>Compressione del layout

In XAML è possibile abilitare la compressione del layout impostando la `CompressedLayout.IsHeadless` proprietà associata `true` su in una classe layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

In alternativa, può essere abilitato in C# specificando l'istanza del layout come primo argomento del `CompressedLayout.SetIsHeadless` Metodo:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Poiché la compressione del layout rimuove un layout dalla struttura ad albero visuale, non è adatta per i layout con aspetto visivo o che ottengono input tocco. Pertanto, i layout che impostano [`VisualElement`](xref:Xamarin.Forms.VisualElement) le proprietà (ad esempio,,, [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) o che accettano movimenti) non sono candidati per la compressione del layout. Tuttavia, l'abilitazione della compressione del layout in un layout che imposta le proprietà dell'aspetto visivo o che accetta movimenti non comporterà un errore di compilazione o di Runtime. Al contrario, la compressione del layout verrà applicata e le proprietà dell'aspetto visivo e il riconoscimento dei movimenti non riusciranno automaticamente.

Per il pulsante Facebook è possibile abilitare la compressione del layout nelle tre classi di layout:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

In Android questo risultato è una gerarchia di visualizzazioni annidate di 14 Visualizzazioni:

![](layout-compression-images/layout-compression.png "View Hierarchy for Facebook Button with Layout Compression")

Rispetto alla gerarchia di visualizzazione nidificata originale di 17 Visualizzazioni, rappresenta una riduzione del numero di visualizzazioni del 17%. Sebbene sia possibile che questa riduzione risulti non significativa, la riduzione della vista su un'intera pagina può essere più significativa.

### <a name="fast-renderers"></a>Renderer veloci

I renderer veloci riducono l'inflazione e i costi di rendering dei Xamarin.Forms controlli in Android, rendendo flat la gerarchia di visualizzazione nativa risultante. Ciò consente di migliorare ulteriormente le prestazioni creando un minor numero di oggetti, il che a sua volta produce una struttura ad albero visuale meno complessa e un minor utilizzo della memoria. Per altre informazioni sui renderer veloci, vedere [renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

Per il pulsante Facebook nell'applicazione di esempio, la combinazione della compressione del layout e dei renderer veloci produce una gerarchia di visualizzazioni annidate di 8 Visualizzazioni:

![](layout-compression-images/layout-compression-with-fast-renderers.png "View Hierarchy for Facebook Button with Layout Compression and Fast Renderers")

Rispetto alla gerarchia di visualizzazione nidificata originale di 17 Visualizzazioni, rappresenta una riduzione del 52%.

L'applicazione di esempio contiene una pagina estratta da un'applicazione reale. Senza compressione del layout e renderer veloci, la pagina produce una gerarchia di visualizzazione nidificata di 130 visualizzazioni in Android. L'abilitazione di renderer veloci e la compressione del layout in classi di layout appropriate riduce la gerarchia di visualizzazione nidificata a 70 visualizzazioni, una riduzione del 46%.

## <a name="summary"></a>Summary

La compressione del layout rimuove i layout specificati dalla struttura ad albero visuale nel tentativo di migliorare le prestazioni di rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)
