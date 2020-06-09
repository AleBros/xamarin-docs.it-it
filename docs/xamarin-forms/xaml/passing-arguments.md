---
title: "passaggio di argomenti in XAML" Description: "in questo articolo viene illustrato l'utilizzo degli attributi XAML che possono essere utilizzati per passare argomenti a costruttori non predefiniti, per chiamare metodi factory e per specificare il tipo di un argomento generico".
ms. prod: Novell MS. AssetID: 8F3B267F-499E-4D79-9193-FCA99F199519 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/25/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="passing-arguments-in-xaml"></a>Passaggio di argomenti in XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Questo articolo illustra l'uso degli attributi XAML che possono essere usati per passare argomenti a costruttori non predefiniti, per chiamare metodi factory e per specificare il tipo di un argomento generico._

## <a name="overview"></a>Panoramica

Spesso è necessario creare un'istanza di oggetti con costruttori che richiedono argomenti oppure chiamando un metodo di creazione statica. Questa operazione può essere eseguita in XAML usando gli `x:Arguments` `x:FactoryMethod` attributi e:

- L' `x:Arguments` attributo viene usato per specificare gli argomenti del costruttore per un costruttore non predefinito o per una dichiarazione di oggetto del metodo factory. Per ulteriori informazioni, vedere [passaggio di argomenti del costruttore](#passing-constructor-arguments).
- L' `x:FactoryMethod` attributo viene utilizzato per specificare un metodo factory che può essere utilizzato per inizializzare un oggetto. Per ulteriori informazioni, vedere [chiamata dei metodi factory](#calling-factory-methods).

Inoltre, l' `x:TypeArguments` attributo può essere utilizzato per specificare gli argomenti di tipo generico per il costruttore di un tipo generico. Per ulteriori informazioni, vedere [specifica di un argomento di tipo generico](#specifying-a-generic-type-argument).

## <a name="passing-constructor-arguments"></a>Passaggio di argomenti del costruttore

Gli argomenti possono essere passati a un costruttore non predefinito usando l' `x:Arguments` attributo. Ogni argomento del costruttore deve essere delimitato all'interno di un elemento XML che rappresenta il tipo dell'argomento. Xamarin.Formssupporta i seguenti elementi per i tipi di base:

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo dell' `x:Arguments` attributo con tre [`Color`](xref:Xamarin.Forms.Color) costruttori:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Il numero di elementi all'interno del `x:Arguments` tag e i tipi di questi elementi devono corrispondere a uno dei [`Color`](xref:Xamarin.Forms.Color) costruttori. Il `Color` [Costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double)) con un solo parametro richiede un valore in scala di grigi da 0 (nero) a 1 (bianco). Il `Color` [Costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) con tre parametri richiede un valore rosso, verde e blu compreso tra 0 e 1. Il `Color` [Costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) con quattro parametri aggiunge un canale alfa come quarto parametro.

Gli screenshot seguenti mostrano il risultato della chiamata a ogni [`Color`](xref:Xamarin.Forms.Color) costruttore con i valori di argomento specificati:

![BoxView. Color specificato con x:Arguments](passing-arguments-images/passing-arguments.png)

## <a name="calling-factory-methods"></a>Chiamata di metodi factory

I metodi factory possono essere chiamati in XAML specificando il nome del metodo usando l' `x:FactoryMethod` attributo e i relativi argomenti usando l' `x:Arguments` attributo. Un metodo factory è un `public static` metodo che restituisce oggetti o valori dello stesso tipo della classe o della struttura che definisce i metodi.

La [`Color`](xref:Xamarin.Forms.Color) struttura definisce un numero di metodi factory e nell'esempio di codice seguente viene illustrata la chiamata a tre di essi:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Il numero di elementi all'interno del `x:Arguments` tag e i tipi di questi elementi devono corrispondere agli argomenti del metodo factory chiamato. Il [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) Metodo Factory richiede quattro [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) parametri, che rappresentano rispettivamente i valori rosso, verde, blu e alfa, compresi tra 0 e 255. Il [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) Metodo Factory richiede quattro [`Double`](https://docs.microsoft.com/dotnet/api/system.double) parametri, che rappresentano rispettivamente i valori di tonalità, saturazione, luminosità e alfa, compresi tra 0 e 1. Il [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) Metodo Factory richiede un oggetto [`String`](https://docs.microsoft.com/dotnet/api/system.string) che rappresenta il colore RGB esadecimale (a).

Gli screenshot seguenti mostrano il risultato della chiamata a ogni [`Color`](xref:Xamarin.Forms.Color) Metodo Factory con i valori di argomento specificati:

![BoxView. Color specificato con x:FactoryMethod e x:Arguments](passing-arguments-images/factory-methods.png)

## <a name="specifying-a-generic-type-argument"></a>Specifica di un argomento di tipo generico

Gli argomenti di tipo generico per il costruttore di un tipo generico possono essere specificati usando l' `x:TypeArguments` attributo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica ed è necessario crearne un'istanza con un `x:TypeArguments` attributo che corrisponda al tipo di destinazione. Nella [`On`](xref:Xamarin.Forms.On) classe l' [`Platform`](xref:Xamarin.Forms.On.Platform) attributo può accettare un singolo `string` valore o più valori delimitati da virgole `string` . In questo esempio, la [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) proprietà è impostata su un oggetto specifico della piattaforma [`Thickness`](xref:Xamarin.Forms.Thickness) .

Per ulteriori informazioni sugli argomenti di tipo generico, vedere [generics in Xamarin.Forms XAML](generics.md).

## <a name="related-links"></a>Collegamenti correlati

- [Passaggio di argomenti del costruttore (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Chiamata di metodi factory (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Generics in Xamarin.Forms XAML](generics.md)
