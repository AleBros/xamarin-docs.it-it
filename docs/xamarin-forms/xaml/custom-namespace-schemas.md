---
title: Schemi dello spazio dei nomi personalizzati XAML in Novell. Forms
description: Uno schema dello spazio dei nomi personalizzato XAML può essere definito con la classe XmlnsDefinitionAttribute, che specifica un mapping tra un URL personalizzato e uno o più spazi dei nomi CLR. Lo schema dello spazio dei nomi personalizzato può quindi essere utilizzato nelle dichiarazioni dello spazio dei nomi XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 98da98c6ef7058264fac12f8271b3bc1848fdbe0
ms.sourcegitcommit: 1fb87ff74560d4d7c89f80018cc010c07646461c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139044"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Schemi dello spazio dei nomi personalizzati XAML in Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)

È possibile fare riferimento ai tipi in una libreria in XAML dichiarando uno spazio dei nomi XAML per la libreria, con la dichiarazione dello spazio dei nomi che specifica il nome dello spazio dei nomi Common Language Runtime (CLR) e il nome di un assembly:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly=MyCompany.Controls">
    ...
</ContentPage>
```

Tuttavia, la specifica di uno spazio dei nomi CLR e `xmlns` di un nome di assembly in una definizione può essere imbarazzante e soggetta a errori. Inoltre, potrebbero essere necessarie più dichiarazioni dello spazio dei nomi XAML se la libreria contiene tipi in più spazi dei nomi.

Un approccio alternativo consiste nel definire uno schema dello spazio dei nomi personalizzato `http://mycompany.com/schemas/controls`, ad esempio, che esegue il mapping a uno o più spazi dei nomi CLR. Ciò consente a una singola dichiarazione dello spazio dei nomi XAML di fare riferimento a tutti i tipi in un assembly, anche se si trovano in spazi dei nomi diversi. Consente inoltre a una singola dichiarazione dello spazio dei nomi XAML di fare riferimento ai tipi in più assembly.

Per altre informazioni sugli spazi dei nomi XAML, vedere [spazi dei nomi XAML in Novell. Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definizione di uno schema dello spazio dei nomi personalizzato

L'applicazione di esempio contiene una libreria che espone alcuni semplici controlli, ad `CircleButton`esempio:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Tutti i controlli nella libreria si trovano nello `MyCompany.Controls` spazio dei nomi. Questi controlli possono essere esposti a un assembly chiamante tramite uno schema dello spazio dei nomi personalizzato.

Uno schema dello spazio dei nomi personalizzato viene `XmlnsDefinitionAttribute` definito con la classe, che specifica il mapping tra uno spazio dei nomi XAML e uno o più spazi dei nomi CLR. `XmlnsDefinitionAttribute` Accetta due argomenti: il nome dello spazio dei nomi XAML e il nome dello spazio dei nomi CLR. Il nome dello spazio dei nomi XAML viene `XmlnsDefinitionAttribute.XmlNamespace` archiviato nella proprietà e il nome dello spazio dei nomi CLR `XmlnsDefinitionAttribute.ClrNamespace` viene archiviato nella proprietà.

> [!NOTE]
> La `XmlnsDefinitionAttribute` classe dispone inoltre di una proprietà `AssemblyName`denominata, che può essere facoltativamente impostata sul nome dell'assembly. Questa operazione è necessaria solo quando uno spazio dei nomi CLR a `XmlnsDefinitionAttribute` cui viene fatto riferimento da un oggetto si trova in un assembly esterno.

Il `XmlnsDefinitionAttribute` deve essere definito a livello di assembly nel progetto che contiene gli spazi dei nomi CLR di cui verrà eseguito il mapping nello schema dello spazio dei nomi personalizzato. Nell'esempio seguente viene illustrato il file **AssemblyInfo.cs** dell'applicazione di esempio:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Questo codice crea uno schema dello spazio dei nomi personalizzato `http://mycompany.com/schemas/controls` che esegue il `MyCompany.Controls` mapping dell'URL allo spazio dei nomi CLR. Inoltre, l' `Preserve` attributo viene specificato nell'assembly, per garantire che il linker manservi tutti i tipi nell'assembly.

> [!IMPORTANT]
> L' `Preserve` attributo deve essere applicato alle classi nell'assembly mappate tramite lo schema dello spazio dei nomi personalizzato o applicato all'intero assembly.

Lo schema dello spazio dei nomi personalizzato può quindi essere utilizzato per la risoluzione dei tipi nei file XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Utilizzo di uno schema dello spazio dei nomi personalizzato

Per utilizzare i tipi dello schema dello spazio dei nomi personalizzato, il compilatore XAML richiede che esista un riferimento al codice dall'assembly che utilizza i tipi, all'assembly che definisce i tipi. Questa operazione può essere eseguita aggiungendo una classe contenente un `Init` metodo all'assembly che definisce i tipi che verranno utilizzati tramite XAML:

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

Il `Init` metodo può quindi essere chiamato dall'assembly che utilizza i tipi dello schema dello spazio dei nomi personalizzato:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Se non si include un riferimento a un codice di questo tipo, il compilatore XAML non è in grado di individuare l'assembly contenente i tipi di schema dello spazio dei nomi personalizzato.

Per utilizzare il `CircleButton` controllo, viene dichiarato uno spazio dei nomi XAML con la dichiarazione dello spazio dei nomi che specifica l'URL dello schema dello spazio dei nomi personalizzato:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton`le istanze possono quindi essere aggiunte all' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto dichiarando il `controls` prefisso dello spazio dei nomi.

Per trovare i tipi di schema dello spazio dei nomi personalizzato, Novell. Forms eseguirà la ricerca degli assembly di riferimento per `XmlnsDefinitionAttribute` le istanze. Se l' `xmlns` attributo per un elemento in un file XAML corrisponde al `XmlNamespace` valore della proprietà in `XmlnsDefinitionAttribute`un oggetto, Novell. Forms tenterà di `XmlnsDefinitionAttribute.ClrNamespace` usare il valore della proprietà per la risoluzione del tipo. Se la risoluzione del tipo ha esito negativo, Novell. Forms continuerà a tentare la `XmlnsDefinitionAttribute` risoluzione del tipo in base a eventuali istanze corrispondenti aggiuntive.

Il risultato è che vengono `CircleButton` visualizzate due istanze:

![Pulsanti cerchio](custom-namespace-schemas-images/circle-buttons.png "Pulsanti cerchio")

## <a name="related-links"></a>Link correlati

- [Schemi dello spazio dei nomi personalizzati (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-customnamespaceschemas)
- [Prefissi consigliati per lo spazio dei nomi XAML](custom-prefix.md)
- [Spazi dei nomi XAML in Novell. Forms](namespaces.md)
