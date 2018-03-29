---
title: Passaggio di argomenti in XAML
description: In questo articolo viene illustrato l'utilizzo di attributi XAML che possono essere utilizzati per passare argomenti a costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2016
ms.openlocfilehash: 232f60bb7afca7acf73e63bd7e11e1b6ec47fbd2
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="passing-arguments-in-xaml"></a>Passaggio di argomenti in XAML

_In questo articolo viene illustrato l'utilizzo di attributi XAML che possono essere utilizzati per passare argomenti a costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico._

## <a name="overview"></a>Panoramica

Spesso è necessario creare istanze di oggetti con costruttori che richiedono argomenti, oppure chiamando un metodo di creazione statica. Questo può essere realizzato in XAML tramite il `x:Arguments` e `x:FactoryMethod` attributi:

- Il `x:Arguments` attributo viene utilizzato per specificare gli argomenti del costruttore per un costruttore non predefinito, o per una dichiarazione di oggetto del metodo factory. Per ulteriori informazioni, vedere [passando gli argomenti del costruttore](#constructor_arguments).
- Il `x:FactoryMethod` attributo viene utilizzato per specificare un metodo factory che può essere utilizzato per inizializzare un oggetto. Per ulteriori informazioni, vedere [chiamando metodi Factory](#factory_methods).

Inoltre, il `x:TypeArguments` attributo può essere utilizzato per specificare gli argomenti di tipo generico per il costruttore di un tipo generico. Per ulteriori informazioni, vedere [specifica di un argomento di tipo generico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Il passaggio di argomenti del costruttore

Gli argomenti possono essere passati a un costruttore non predefinito utilizzando il `x:Arguments` attributo. Ogni argomento del costruttore deve essere delimitato all'interno di un elemento XML che rappresenta il tipo dell'argomento. Xamarin. Forms supporta i seguenti elementi per i tipi di base:

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

Esempio di codice seguente viene illustrato l'utilizzo di `x:Arguments` attributo con tre [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) costruttori:

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

Il numero di elementi all'interno di `x:Arguments` tag e i tipi di questi elementi, devono corrispondere a uno del [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) costruttori. Il `Color` [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/) con un singolo parametro richiede un valore di scala di grigi compreso tra 0 (nero) a 1 (bianco). Il `Color` [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/) con tre parametri richiede un valore di colore rosso, verde e blu, compreso tra 0 e 1. Il `Color` [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/) con quattro parametri consente di aggiungere un canale alfa come quarto parametro.

La schermata seguente mostra il risultato della chiamata ogni [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) costruttore con i valori dell'argomento specificato:

![](passing-arguments-images/passing-arguments.png "BoxView.Color specificato con X:Arguments")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>La chiamata di metodi Factory

Metodi factory possono essere chiamati in XAML, il metodo specificando nome usando il `x:FactoryMethod` attributo e i relativi argomenti utilizzando il `x:Arguments` attributo. Un metodo factory è un `public static` metodo che restituisce oggetti o valori dello stesso tipo della classe o struttura che definisce i metodi.

Il [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) struttura definisce una serie di metodi factory e il codice riportato di seguito viene illustrato come chiamante e tre i parametri:

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

Il numero di elementi all'interno di `x:Arguments` tag e i tipi di questi elementi, devono corrispondere gli argomenti del metodo factory chiamato. Il [ `FromRgba` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) metodo factory richiede quattro [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) parametri, che rappresentano i valori di rossi, verdi, blu e alfa, compreso tra 0 e 255 rispettivamente. Il [ `FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) metodo factory richiede quattro [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) parametri, che rappresentano il valori di tonalità, saturazione, luminosità e alfa, compreso tra 0 e 1 rispettivamente. Il [ `FromHex` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) metodo factory richiede un [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) che rappresenta il formato esadecimale (A) colore RGB.

La schermata seguente mostra il risultato della chiamata ogni [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) metodo factory con i valori dell'argomento specificato:

![](passing-arguments-images/factory-methods.png "BoxView.Color specificato con X:FactoryMethod e X:Arguments")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Specificare un argomento di tipo generico

Argomenti di tipo generico per il costruttore di un tipo generico possono essere specificati utilizzando il `x:TypeArguments` attributo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="WinPhone, Windows" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

Il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) classe è una classe generica e deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Nel [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) (classe), il [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) attributo può accettare un singolo `string` valore o più delimitato da virgole `string` valori. In questo esempio, il [ `StackLayout.Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) è impostata su una specifica della piattaforma [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/).

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate usando gli attributi XAML che possono essere utilizzati per passare argomenti a costruttori non predefiniti, per chiamare i metodi factory e per specificare il tipo di argomento generico.


## <a name="related-links"></a>Collegamenti correlati

- [Spazi dei nomi XAML](~/xamarin-forms/xaml/namespaces.md)
- [Il passaggio di argomenti del costruttore (esempio)](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [La chiamata di metodi Factory (esempio)](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
