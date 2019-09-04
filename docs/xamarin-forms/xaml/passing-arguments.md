---
title: Passaggio di argomenti in XAML
description: Questo articolo viene illustrato come utilizzare gli attributi XAML che possono essere usati per passare argomenti per i costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 065c7284970ececacbdb298d773b19f654280513
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228084"
---
# <a name="passing-arguments-in-xaml"></a>Passaggio di argomenti in XAML

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Questo articolo viene illustrato come utilizzare gli attributi XAML che possono essere usati per passare argomenti per i costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico._

## <a name="overview"></a>Panoramica

È spesso necessario creare un'istanza degli oggetti con costruttori che richiedono argomenti oppure chiamando un metodo di creazione statica. Ciò può essere ottenuto in XAML utilizzando il `x:Arguments` e `x:FactoryMethod` attributi:

- Il `x:Arguments` attributo viene usato per specificare gli argomenti del costruttore per un costruttore non predefinito o per una dichiarazione dell'oggetto metodo factory. Per altre informazioni, vedere [passando gli argomenti del costruttore](#constructor_arguments).
- Il `x:FactoryMethod` attributo viene utilizzato per specificare un metodo factory che può essere utilizzato per inizializzare un oggetto. Per altre informazioni, vedere [chiamata di metodi Factory](#factory_methods).

Inoltre, il `x:TypeArguments` attributo può essere utilizzato per specificare gli argomenti di tipo generico per il costruttore di un tipo generico. Per altre informazioni, vedere [che specifica un argomento di tipo generico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Il passaggio di argomenti del costruttore

Argomenti possono essere passati a un costruttore non predefinito usando la `x:Arguments` attributo. Ogni argomento di costruttore deve essere delimitato all'interno di un elemento XML che rappresenta il tipo dell'argomento. Xamarin. Forms supporta i seguenti elementi per i tipi di base:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

Esempio di codice seguente viene illustrato come utilizzare il `x:Arguments` attributo con tre [ `Color` ](xref:Xamarin.Forms.Color) costruttori:

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

Il numero di elementi all'interno di `x:Arguments` tag e i tipi di questi elementi, devono corrispondere a uno del [ `Color` ](xref:Xamarin.Forms.Color) costruttori. Il `Color` [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double)) con un singolo parametro richiede un valore di scala di grigi compreso tra 0 (nero) su 1 (bianco). Il `Color` [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) con tre parametri richiede un valore di colore rosso, verde e blu, compreso tra 0 e 1. Il `Color` [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) con quattro parametri aggiunge un canale alfa come quarto parametro.

Le schermate seguenti illustrano il risultato della chiamata ognuno [ `Color` ](xref:Xamarin.Forms.Color) costruttore con i valori dell'argomento specificato:

![BoxView. Color specificato con x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Chiamata di metodi Factory

I metodi factory possono essere chiamati in XAML, specificando il metodo nome utilizzando il `x:FactoryMethod` attributo e i relativi argomenti usando le `x:Arguments` attributo. Un metodo factory è un `public static` metodo che restituisce oggetti o valori dello stesso tipo della classe o struttura che definisce i metodi.

Il [ `Color` ](xref:Xamarin.Forms.Color) struttura definisce una serie di metodi factory e il codice seguente viene illustrato chiamante tre parametri:

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

Il numero di elementi all'interno di `x:Arguments` tag e i tipi di questi elementi, devono corrispondere gli argomenti del metodo factory viene chiamato. Il [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) metodo factory richiede quattro [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) parametri, che rappresentano i valori rossi, verdi, blu e alfa, compreso tra 0 e 255 rispettivamente. Il [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) metodo factory richiede quattro [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) parametri, che rappresentano la tonalità, saturazione, luminosità e i valori alfa, compreso tra 0 e 1 rispettivamente. Il [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) metodo factory richiede un [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) che rappresenta il formato esadecimale (A) colore RGB.

Le schermate seguenti illustrano il risultato della chiamata ognuno [ `Color` ](xref:Xamarin.Forms.Color) metodo factory con i valori di argomento specificato:

![BoxView. Color specificato con x:FactoryMethod e x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Se si specifica un argomento tipo generico

Argomenti di tipo generico per il costruttore di un tipo generico possono essere specificati utilizzando il `x:TypeArguments` attributo, come illustrato nell'esempio di codice seguente:

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

Il [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica e deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Nel [ `On` ](xref:Xamarin.Forms.On) (classe), il [ `Platform` ](xref:Xamarin.Forms.On.Platform) attributo può accettare un singolo `string` valore o più valori delimitati da virgole `string` valori. In questo esempio, il [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin) è impostata su una specifica della piattaforma [ `Thickness` ](xref:Xamarin.Forms.Thickness).

## <a name="summary"></a>Riepilogo

L'articolo descrive gli attributi XAML può essere usato per passare argomenti per i costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.


## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Il passaggio di argomenti del costruttore (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Chiamata di metodi Factory (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
