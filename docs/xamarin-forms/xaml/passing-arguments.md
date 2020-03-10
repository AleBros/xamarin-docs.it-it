---
title: Passaggio di argomenti in XAML
description: Questo articolo viene illustrato come utilizzare gli attributi XAML che possono essere usati per passare argomenti per i costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911375"
---
# <a name="passing-arguments-in-xaml"></a>Passaggio di argomenti in XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Questo articolo illustra l'uso degli attributi XAML che possono essere usati per passare argomenti a costruttori non predefiniti, per chiamare metodi factory e per specificare il tipo di un argomento generico._

## <a name="overview"></a>Panoramica

È spesso necessario creare un'istanza degli oggetti con costruttori che richiedono argomenti oppure chiamando un metodo di creazione statica. Questa operazione può essere eseguita in XAML usando gli attributi `x:Arguments` e `x:FactoryMethod`:

- L'attributo `x:Arguments` viene usato per specificare gli argomenti del costruttore per un costruttore non predefinito o per una dichiarazione dell'oggetto Metodo Factory. Per ulteriori informazioni, vedere [passaggio di argomenti del costruttore](#constructor_arguments).
- L'attributo `x:FactoryMethod` viene usato per specificare un metodo factory che può essere usato per inizializzare un oggetto. Per ulteriori informazioni, vedere [chiamata dei metodi factory](#factory_methods).

Inoltre, è possibile usare l'attributo `x:TypeArguments` per specificare gli argomenti di tipo generico per il costruttore di un tipo generico. Per ulteriori informazioni, vedere [specifica di un argomento di tipo generico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Il passaggio di argomenti del costruttore

Gli argomenti possono essere passati a un costruttore non predefinito usando l'attributo `x:Arguments`. Ogni argomento di costruttore deve essere delimitato all'interno di un elemento XML che rappresenta il tipo dell'argomento. Xamarin. Forms supporta i seguenti elementi per i tipi di base:

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

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo dell'attributo `x:Arguments` con tre costruttori di [`Color`](xref:Xamarin.Forms.Color) :

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

Il numero di elementi all'interno del tag `x:Arguments` e i tipi di questi elementi devono corrispondere a uno dei costruttori di [`Color`](xref:Xamarin.Forms.Color) . Il [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double)) `Color` con un solo parametro richiede un valore in scala di grigi da 0 (nero) a 1 (bianco). Il [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) `Color` con tre parametri richiede un valore rosso, verde e blu compreso tra 0 e 1. Il [costruttore](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) `Color` con quattro parametri aggiunge un canale alfa come quarto parametro.

Gli screenshot seguenti mostrano il risultato della chiamata di ogni costruttore [`Color`](xref:Xamarin.Forms.Color) con i valori di argomento specificati:

![BoxView. Color specificato con x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Chiamata di metodi Factory

I metodi factory possono essere chiamati in XAML specificando il nome del metodo usando l'attributo `x:FactoryMethod` e i relativi argomenti usando l'attributo `x:Arguments`. Un metodo factory è un metodo `public static` che restituisce oggetti o valori dello stesso tipo della classe o della struttura che definisce i metodi.

La struttura [`Color`](xref:Xamarin.Forms.Color) definisce un numero di metodi factory e nell'esempio di codice riportato di seguito viene illustrata la chiamata a tre di essi:

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

Il numero di elementi all'interno del tag `x:Arguments` e i tipi di questi elementi devono corrispondere agli argomenti del metodo factory chiamato. Il metodo factory [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) richiede quattro parametri [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) , che rappresentano i valori rosso, verde, blu e alfa, compresi rispettivamente tra 0 e 255. Il metodo factory [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) richiede quattro parametri [`Double`](https://docs.microsoft.com/dotnet/api/system.double) , che rappresentano rispettivamente i valori di tonalità, saturazione, luminosità e alfa, compresi tra 0 e 1. Il metodo factory [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) richiede una [`String`](https://docs.microsoft.com/dotnet/api/system.string) che rappresenta il colore RGB esadecimale (a).

Gli screenshot seguenti mostrano il risultato della chiamata di ogni [`Color`](xref:Xamarin.Forms.Color) metodo factory con i valori di argomento specificati:

![BoxView. Color specificato con x:FactoryMethod e x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Se si specifica un argomento tipo generico

Gli argomenti di tipo generico per il costruttore di un tipo generico possono essere specificati usando l'attributo `x:TypeArguments`, come illustrato nell'esempio di codice seguente:

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

La classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) è una classe generica ed è necessario crearne un'istanza con un attributo `x:TypeArguments` corrispondente al tipo di destinazione. Nella classe [`On`](xref:Xamarin.Forms.On) l'attributo [`Platform`](xref:Xamarin.Forms.On.Platform) può accettare un singolo valore di `string` o più valori `string` delimitati da virgole. In questo esempio, la proprietà [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) è impostata su un [`Thickness`](xref:Xamarin.Forms.Thickness)specifico della piattaforma.

## <a name="summary"></a>Riepilogo

L'articolo descrive gli attributi XAML può essere usato per passare argomenti per i costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.

## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Passaggio di argomenti del costruttore (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Chiamata di metodi factory (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
