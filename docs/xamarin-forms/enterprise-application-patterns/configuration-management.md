---
title: Gestione della configurazione
description: In questo capitolo viene illustrato come l'app per dispositivi mobili di eShopOnContainers implementa la gestione della configurazione per specificare le impostazioni dell'app e impostazioni utente.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995360"
---
# <a name="configuration-management"></a>Gestione della configurazione

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, consentendo il comportamento possono essere modificati senza ricompilare l'app. Esistono due tipi di impostazioni: le impostazioni dell'app e impostazioni utente.

Le impostazioni dell'App sono dati che un'app vengono create e gestite. Può includere dati, ad esempio endpoint del servizio web predefinito, le chiavi API e stato di runtime. Le impostazioni dell'App sono legate all'esistenza dell'app e solo sono significative per l'app.

Le impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente. Ad esempio, un'app potrebbe consentire all'utente specificare dove recuperare i dati da e come visualizzarli nella schermata.

Xamarin. Forms include un dizionario permanente che può essere utilizzato per archiviare i dati delle impostazioni. Questo dizionario è possibile accedere tramite il [ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties) proprietà e tutti i dati che vengano inseriti al suo interno viene salvato quando l'app passa a uno stato di sospensione e viene ripristinato quando l'app viene ripresa o è stato riavviato. Inoltre, il [ `Application` ](xref:Xamarin.Forms.Application) classe include anche una [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo che consente a un'app per le impostazioni salvate quando necessario. Per altre informazioni su questo dizionario, vedere [dizionario delle proprietà](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Un lato negativo per l'archiviazione dei dati utilizzando il dizionario permanente di xamarin. Forms è che non è facilmente associato ai dati. Pertanto, l'app per dispositivi mobili di eShopOnContainers Usa la libreria di Xam.Plugins.Settings, disponibile dal [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Questa libreria fornisce un approccio coerente, indipendente dai tipi, lo sviluppo multipiattaforma per la persistenza e il recupero delle app e le impostazioni utente, quando si usa la gestione delle impostazioni native fornita per ogni piattaforma. Inoltre, è semplice usare l'associazione dati per accedere ai dati delle impostazioni esposti dalla libreria.

> [!NOTE]
> Mentre la libreria Xam.Plugin.Settings possa archiviare le impostazioni utente e di app, non distingue tra i due.

## <a name="creating-a-settings-class"></a>Creazione di una classe di impostazioni

Quando si usa la libreria Xam.Plugins.Settings, deve essere creata una singola classe statica che conterrà l'app e le impostazioni utente necessarie per l'app. Esempio di codice seguente illustra la classe di impostazioni nell'app eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Le impostazioni possono essere letti e scritte attraverso i `ISettings` API, che viene fornito dalla libreria Xam.Plugins.Settings. Questa libreria fornisce un singleton che può essere utilizzato per accedere all'API `CrossSettings.Current`, e la classe di impostazioni dell'app deve esporre questo singleton tramite un `ISettings` proprietà.

> [!NOTE]
> Direttive using per gli spazi dei nomi Plugin.Settings e Plugin.Settings.Abstractions devono essere aggiunti a una classe che richiede l'accesso per i tipi della libreria Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Aggiunta di un'impostazione

Ogni impostazione è costituito da una chiave, valore predefinito e una proprietà. Esempio di codice seguente illustra tutti i tre elementi per un'impostazione utente che rappresenta l'URL di base per i servizi online che connette l'app per dispositivi mobili di eShopOnContainers:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

La chiave è sempre una stringa const che definisce il nome della chiave, con il valore predefinito per l'impostazione di un valore statico di sola lettura del tipo richiesto. Fornire un valore predefinito assicura che un valore valido è disponibile se viene recuperata un'impostazione non impostata.

Il `UrlBase` proprietà statica utilizza due metodi dal `ISettings` API per leggere o scrivere il valore dell'impostazione. Il `ISettings.GetValueOrDefault` metodo viene utilizzato per recuperare un valore dell'impostazione da un archivio specifico della piattaforma. Se per l'impostazione non è definito alcun valore, viene invece recuperato il valore predefinito. Analogamente, il `ISettings.AddOrUpdateValue` metodo viene utilizzato per rendere persistente un valore dell'impostazione in un archivio specifico della piattaforma.

Piuttosto che definiscono un valore predefinito all'interno di `Settings` (classe), il `UrlBaseDefault` stringa ottenga il valore dal `GlobalSetting` classe. Nell'esempio di codice riportato di seguito viene illustrato il `BaseEndpoint` proprietà e `UpdateEndpoint` metodo in questa classe:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Ogni volta che il `BaseEndpoint` è impostata, il `UpdateEndpoint` viene chiamato il metodo. Questo metodo viene aggiornata una serie di proprietà, ognuno dei quali si basano il `UrlBase` impostazione utente fornita dal `Settings` classi che rappresentano i diversi endpoint che l'app per dispositivi mobili di eShopOnContainers si connette a.

## <a name="data-binding-to-user-settings"></a>Associazione dati per le impostazioni utente

Nell'app per dispositivi mobili di eShopOnContainers il `SettingsView` espone due impostazioni dell'utente. Queste impostazioni consentono la configurazione del fatto che l'app deve recuperare dati da microservizi distribuiti come contenitori Docker, o se l'app deve recuperare dati da servizi fittizi che non richiedono una connessione a internet. Se si sceglie di recuperare dati da microservizi in contenitori, è necessario specificare un URL endpoint base per i microservizi. Figura 7-1 sono illustrati i `SettingsView` quando l'utente ha scelto di recuperare dati da microservizi in contenitori.

![](configuration-management-images/settings-endpoint.png "Impostazioni utente esposte dall'app per dispositivi mobili di eShopOnContainers")

**Figura 7-1**: le impostazioni utente esposte dall'app per dispositivi mobili di eShopOnContainers

Associazione dati è utilizzabile per recuperare e impostare le impostazioni esposte dal `Settings` classe. A questo scopo, i controlli sull'associazione alle proprietà del modello di visualizzazione che a sua volta accede alle proprietà nella visualizzazione di `Settings` classe e la generazione di una proprietà modificata notifica se viene modificato il valore di impostazioni. Per informazioni sul modo in cui l'app per dispositivi mobili di eShopOnContainers Crea vista modellano e li associano alle viste, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Nell'esempio di codice riportato di seguito viene illustrato il [ `Entry` ](xref:Xamarin.Forms.Entry) controllare dal `SettingsView` che consente all'utente di immettere un URL endpoint base per i microservizi in contenitori:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Ciò [ `Entry` ](xref:Xamarin.Forms.Entry) controllo viene associato ai `Endpoint` proprietà del `SettingsViewModel` classe, utilizzando un'associazione bidirezionale. Esempio di codice seguente mostra la proprietà dell'Endpoint:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Quando la `Endpoint` viene impostata la `UpdateEndpoint` viene chiamato il metodo, condizione che il valore fornito sia valido e modificare la proprietà notifica viene generata. Nell'esempio di codice riportato di seguito viene illustrato il `UpdateEndpoint` metodo:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Questo metodo aggiorna il `UrlBase` proprietà di `Settings` classe con il valore dell'URL endpoint base immesso dall'utente, in modo che essere resi persistenti in archiviazione di specifiche della piattaforma.

Quando la `SettingsView` ci si sposta, la `InitializeAsync` metodo nel `SettingsViewModel` classe viene eseguita. Esempio di codice seguente viene illustrato questo metodo:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Il metodo imposta il `Endpoint` il valore della proprietà di `UrlBase` proprietà nel `Settings` classe. L'accesso al `UrlBase` fa sì che la libreria Xam.Plugins.Settings recuperare il valore di impostazioni da un archivio specifico della piattaforma. Per informazioni sul modo in cui `InitializeAsync` metodo viene richiamato, vedere [passando i parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Questo meccanismo garantisce che ogni volta che un utente passa al SettingsView, impostazioni utente vengano recuperate da un archivio specifico della piattaforma e presentate tramite associazione dati. Quindi, se l'utente modifica i valori delle impostazioni, l'associazione dati in modo che vengono rese persistenti immediatamente all'archiviazione specifiche della piattaforma.

## <a name="summary"></a>Riepilogo

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, consentendo il comportamento possono essere modificati senza ricompilare l'app. Le impostazioni dell'App sono dati che un'app crea e gestisce e impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente.

La libreria Xam.Plugins.Settings fornisce un coerente, indipendente dai tipi, approccio multipiattaforma per la persistenza e il recupero delle app e impostazioni utente e l'associazione dati può essere utilizzato per accedere alle impostazioni create con la libreria.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
