---
title: "XAML Markup Extensions" Description: "l'articolo spiega come usare le Xamarin.Forms estensioni di markup XAML per estendere la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da origini diverse dalle stringhe di testo letterali."
ms. prod: Novell MS. AssetID: EB06C8B7-3FD5-47B7-A09C-A13063BD110F ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/05/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-markup-extensions"></a>Estensioni di markup XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Le estensioni di markup XAML consentono di estendere la potenza e la flessibilità di XAML consentendo l'impostazione degli attributi degli elementi da origini diverse dalle stringhe di testo letterali.

Ad esempio, normalmente si imposta la `Color` proprietà di `BoxView` simile alla seguente:

```xaml
<BoxView Color="Blue" />
```

In alternativa, è possibile impostarlo su un valore di colore RGB esadecimale:

```xaml
<BoxView Color="#FF0080" />
```

In entrambi i casi, la stringa di testo impostata sull' `Color` attributo viene convertita in un `Color` valore dalla [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) classe.

È invece preferibile impostare l' `Color` attributo da un valore archiviato in un dizionario risorse oppure dal valore di una proprietà statica di una classe creata dall'utente o da una proprietà di tipo `Color` di un altro elemento nella pagina o da valori di tonalità, saturazione e luminosità distinti.

Tutte queste opzioni sono possibili usando le estensioni di markup XAML. Ma non lasciare che la frase "estensioni di markup" risulti *insufficiente* : le estensioni di markup XAML non sono estensioni per XML. Anche con le estensioni di markup XAML, XAML è sempre un XML valido.

Un'estensione di markup è in realtà solo un modo diverso per esprimere un attributo di un elemento. Le estensioni di markup XAML sono in genere identificabili da un'impostazione di attributo racchiusa tra parentesi graffe:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualsiasi impostazione di attributo tra parentesi graffe è *sempre* un'estensione di markup XAML. Tuttavia, come si vedrà, è possibile fare riferimento alle estensioni di markup XAML anche senza usare parentesi graffe.

Questo articolo è suddiviso in due parti:

## <a name="consuming-xaml-markup-extensions"></a>[Utilizzo di estensioni di markup XAML](consuming.md)  

Usare le estensioni di markup XAML definite in Xamarin.Forms .

## <a name="creating-xaml-markup-extensions"></a>[Creazione di estensioni di markup XAML](creating.md)

Scrivere estensioni di markup XAML personalizzate.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capitolo del libro sulle estensioni di markup XAML Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
