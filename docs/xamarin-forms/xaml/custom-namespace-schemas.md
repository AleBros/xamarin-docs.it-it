---
title: Schemi personalizzati Namespace XAML in xamarin. Forms
description: Uno schema personalizzato dello spazio dei nomi XAML può essere definito con la classe XmlnsDefinitionAttribute, che specifica un mapping tra un URL personalizzato e uno o più spazi dei nomi CLR. Schema spazio dei nomi personalizzato è quindi utilizzabile nelle dichiarazioni dello spazio dei nomi XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 2e09e89fe17956efaef910638e827b69a5795bc0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60857482"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Schemi personalizzati Namespace XAML in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

Tipi in una libreria sono reperibile in XAML con la dichiarazione di uno spazio dei nomi XAML per la libreria, con la dichiarazione dello spazio dei nomi specificando lo spazio dei nomi Common Language Runtime (CLR) e un nome di assembly:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

Tuttavia, specificando un nome di spazio dei nomi e assembly CLR in un `xmlns` definizione può essere difficile e tendente all'errore. Inoltre, più dichiarazioni dello spazio dei nomi XAML potrebbero essere necessarie se la libreria contiene i tipi in più spazi dei nomi.

Un approccio alternativo consiste nel definire uno schema spazio dei nomi personalizzato, ad esempio `http://mycompany.com/schemas/controls`, che esegue il mapping a uno o più spazi dei nomi CLR. In questo modo una singola dichiarazione dello spazio dei nomi XAML fare riferimento a tutti i tipi in un assembly, anche se si trovano in spazi dei nomi diversi. Consente inoltre una singola dichiarazione dello spazio dei nomi XAML per i tipi di riferimento in più assembly.

Per altre informazioni sugli spazi dei nomi XAML, vedere [spazi dei nomi XAML in xamarin. Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definizione di uno schema spazio dei nomi personalizzato

L'applicazione di esempio contiene una libreria che espone alcuni semplici controlli, ad esempio `CircleButton`:

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

Tutti i controlli nella libreria si trovano nel `MyCompany.Controls` dello spazio dei nomi. Questi controlli possano essere esposti a un assembly chiamante tramite uno schema spazio dei nomi personalizzato.

Schema spazio dei nomi personalizzato viene definito con la `XmlnsDefinitionAttribute` (classe), che specifica il mapping tra uno spazio dei nomi XAML e uno o più spazi dei nomi CLR. Il `XmlnsDefinitionAttribute` accetta due argomenti: il nome dello spazio dei nomi XAML e lo spazio dei nomi CLR. Il nome dello spazio dei nomi XAML viene archiviato nel `XmlnsDefinitionAttribute.XmlNamespace` proprietà e lo spazio dei nomi CLR viene archiviato nel `XmlnsDefinitionAttribute.ClrNamespace` proprietà.

> [!NOTE]
> Il `XmlnsDefinitionAttribute` classe include anche una proprietà denominata `AssemblyName`, che può essere facoltativamente impostato per il nome dell'assembly. Questo è solo necessario quando uno spazio dei nomi CLR a cui fa riferimento un `XmlnsDefinitionAttribute` si trova in un assembly esterno.

Il `XmlnsDefinitionAttribute` devono essere definiti a livello di assembly del progetto che contiene gli spazi dei nomi CLR che verranno mappati nello schema spazio dei nomi personalizzato. L'esempio seguente mostra le **AssemblyInfo.cs** file dall'applicazione di esempio:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Questo codice crea uno schema spazio dei nomi personalizzato che viene eseguito il mapping di `http://mycompany.com/schemas/controls` URL a cui il `MyCompany.Controls` dello spazio dei nomi CLR. Inoltre, il `Preserve` attributo è specificato nell'assembly, per garantire che il linker mantiene tutti i tipi nell'assembly.

> [!IMPORTANT]
> Il `Preserve` attributo deve essere applicato alle classi nell'assembly che sono mappati tramite lo schema spazio dei nomi personalizzato o applicato all'intero assembly.

Schema spazio dei nomi personalizzato è quindi utilizzabile per la risoluzione del tipo nei file XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Utilizzo di uno schema spazio dei nomi personalizzato

Per utilizzare i tipi di schema spazio dei nomi personalizzato, il compilatore XAML richiede che è presente un riferimento al codice dall'assembly che utilizza i tipi, per l'assembly che definisce i tipi. Questa operazione può essere eseguita mediante l'aggiunta di una classe che contiene un `Init` metodo all'assembly che definisce i tipi che verranno utilizzati tramite XAML:

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

Il `Init` può quindi essere chiamato il metodo dell'assembly che utilizza tipi dallo schema spazio dei nomi personalizzato:

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
> Errore di includere tale riferimento codice comporterà il compilatore XAML sia in grado di individuare l'assembly che contiene i tipi di schema spazio dei nomi personalizzato.

Per utilizzare il `CircleButton` (controllo), uno spazio dei nomi XAML è dichiarato, con la dichiarazione dello spazio dei nomi specificando l'URL di schema spazio dei nomi personalizzato:

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

`CircleButton` è quindi possibile aggiungere istanze per il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) dichiarandoli con il `controls` prefisso dello spazio dei nomi.

Per trovare lo spazio dei nomi personalizzato i tipi di schema, xamarin. Forms cercherà l'assembly di riferimento per `XmlnsDefinitionAttribute` istanze. Se il `xmlns` attributo per un elemento in un file XAML corrisponde il `XmlNamespace` valore della proprietà in un `XmlnsDefinitionAttribute`, xamarin. Forms proverà a usare il `XmlnsDefinitionAttribute.ClrNamespace` valore della proprietà per la risoluzione del tipo. Se la risoluzione del tipo non riesce, xamarin. Forms continuerà a tentare la risoluzione del tipo basata eventuali corrispondenze aggiuntive `XmlnsDefinitionAttribute` istanze.

Il risultato è che due `CircleButton` vengono visualizzate le istanze:

![Cerchio pulsanti](custom-namespace-schemas-images/circle-buttons.png "Circle pulsanti")

## <a name="related-links"></a>Collegamenti correlati

- [Schemi personalizzati Namespace (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [Prefissi consigliati per lo spazio dei nomi XAML](custom-prefix.md)
- [Spazi dei nomi XAML in xamarin. Forms](namespaces.md)
