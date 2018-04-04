---
title: Gestione della configurazione
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9318f86077c9cdb98e073e4816dae4fdabbfe568
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="configuration-management"></a>Gestione della configurazione

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, il comportamento possono essere modificati senza ricompilare l'app. Esistono due tipi di impostazioni: le impostazioni dell'app e impostazioni utente.

Impostazioni delle App sono dati che un'app vengono create e gestite. Può includere dati, ad esempio endpoint del servizio web predefinito, le chiavi API e stato di runtime. Le impostazioni dell'App sono vincolate all'esistenza dell'app e solo sono significative per tale app.

Impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente. Ad esempio, un'app potrebbe consentire all'utente di specificare la posizione in cui recuperare i dati e come per la visualizzazione sullo schermo.

Xamarin. Forms include un dizionario persistente che può essere utilizzato per archiviare i dati delle impostazioni. Questo dizionario è possibile accedere tramite il [ `Application.Current.Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) proprietà e i dati che si trova al suo interno viene salvato quando l'app passa a uno stato di sospensione e viene ripristinato quando l'app viene ripresa o è stato riavviato. Inoltre, il [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) classe include inoltre un [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) metodo che consente a un'app per le impostazioni salvate quando necessario. Per ulteriori informazioni su questo dizionario, vedere [dizionario delle proprietà](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Un svantaggio per l'archiviazione dei dati utilizzando il dizionario permanente di xamarin. Forms è che non è facilmente associato ai dati. Pertanto, l'app mobile eShopOnContainers utilizzi la libreria Xam.Plugins.Settings disponibile da [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Questa libreria fornisce un approccio coerenza, indipendente dai tipi, multipiattaforma per rendere persistente e il recupero delle app e le impostazioni utente, quando si utilizza la gestione di impostazioni nativo fornita da ogni piattaforma. Inoltre, è semplice da utilizzare l'associazione dati per accedere ai dati di impostazioni esposti dalla libreria.

> [!NOTE]
> Mentre la libreria Xam.Plugin.Settings possibile archiviare le impostazioni utente e di app, non distingue tra i due.

## <a name="creating-a-settings-class"></a>Creazione di una classe di impostazioni

Quando si utilizza la libreria Xam.Plugins.Settings, deve essere creata una sola classe statica che conterrà l'app e le impostazioni utente necessarie per l'applicazione. Esempio di codice seguente illustra la classe delle impostazioni nell'app mobile eShopOnContainers:

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

Le impostazioni possono essere letti e scritte tramite il `ISettings` API, che viene fornito dalla libreria Xam.Plugins.Settings. Questa libreria fornisce un singleton che può essere usato per accedere all'API, `CrossSettings.Current`, e la classe di impostazioni di un'app deve esporre questo singleton tramite un `ISettings` proprietà.

> [!NOTE]
> Direttive using per gli spazi dei nomi Plugin.Settings e Plugin.Settings.Abstractions devono essere aggiunti a una classe che richiede l'accesso per i tipi della libreria Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Aggiunta di un'impostazione

Ogni impostazione è costituita da una chiave e valore predefinito è una proprietà. Esempio di codice seguente mostra tutti i tre elementi per un utente che rappresenta l'URL di base per i servizi online che l'app mobile eShopOnContainers si connette a:

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

Il `UrlBase` proprietà statica utilizza due metodi di `ISettings` API per leggere o scrivere il valore dell'impostazione. Il `ISettings.GetValueOrDefault` metodo viene utilizzato per recuperare un valore dell'impostazione di archiviazione specifico della piattaforma. Se non è definito un valore per l'impostazione, viene recuperato invece il valore predefinito. Analogamente, il `ISettings.AddOrUpdateValue` metodo viene utilizzato per mantenere un valore dell'impostazione di archiviazione specifico della piattaforma.

Piuttosto che definiscono un valore predefinito all'interno di `Settings` (classe), il `UrlBaseDefault` stringa ottenga il valore dalla `GlobalSetting` classe. Nell'esempio di codice riportato di seguito viene illustrato il `BaseEndpoint` proprietà e `UpdateEndpoint` metodo nella classe:

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

Ogni volta che il `BaseEndpoint` è impostata, il `UpdateEndpoint` metodo viene chiamato. Questo metodo viene aggiornata una serie di proprietà, ognuno dei quali si basano le `UrlBase` impostazione utente fornito dalla `Settings` classe che rappresenta l'endpoint diversi che l'app mobile eShopOnContainers si connette a.

## <a name="data-binding-to-user-settings"></a>Associazione dati per le impostazioni utente

Nell'app mobile eShopOnContainers, il `SettingsView` espone due impostazioni utente. Queste impostazioni consentono la configurazione di indica se l'applicazione deve recuperare dati da microservizi che vengono distribuiti come contenitori di Docker, o se l'applicazione deve recuperare i dati da servizi fittizi che non richiedono una connessione internet. Se si sceglie di recuperare dati da microservizi nei contenitori, è necessario specificare un URL di endpoint di base per il microservizi. Figura 7-1 viene mostrata la `SettingsView` quando l'utente ha scelto recuperare dati da microservizi nei contenitori.

![](configuration-management-images/settings-endpoint.png "Impostazioni utente esposte dall'app per dispositivi mobili eShopOnContainers")

**Figura 7-1**: le impostazioni utente esposte dall'app per dispositivi mobili eShopOnContainers

Associazione dati consente di recuperare e impostare le impostazioni esposte dalla `Settings` classe. A questo scopo, l'associazione di visualizzazione per visualizzare le proprietà di modello che a sua volta accedere alle proprietà nei controlli di `Settings` classe e la generazione di una proprietà modificata notifica se è stato modificato il valore di impostazioni. Per informazioni sulle modalità app mobile eShopOnContainers costruisce vista modellano e li associano alle viste, vedere [automaticamente la creazione di un modello di visualizzazione con un indicatore di posizione di visualizzazione modello](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Nell'esempio di codice riportato di seguito viene illustrato il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo il `SettingsView` che consente all'utente di immettere un URL di endpoint di base per il microservizi nei contenitori:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Questo [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) associato il controllo di `Endpoint` proprietà del `SettingsViewModel` classe, utilizzando un'associazione bidirezionale. Esempio di codice seguente viene illustrata la proprietà di Endpoint:

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

Quando il `Endpoint` viene impostata la `UpdateEndpoint` metodo viene chiamato, purché il valore fornito sia valido e modificare la proprietà viene generata una notifica. Nell'esempio di codice riportato di seguito viene illustrato il `UpdateEndpoint` metodo:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Questo metodo aggiorna il `UrlBase` proprietà la `Settings` classe con il valore dell'URL endpoint base immessi dall'utente, in modo che devono essere rese persistenti nell'archiviazione specifico della piattaforma.

Quando il `SettingsView` si accede, il `InitializeAsync` metodo la `SettingsViewModel` classe viene eseguita. Esempio di codice seguente viene illustrato questo metodo:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Il metodo imposta la `Endpoint` il valore di proprietà di `UrlBase` proprietà nel `Settings` classe. L'accesso di `UrlBase` fa sì che la libreria Xam.Plugins.Settings recuperare il valore di impostazioni di archiviazione specifico della piattaforma. Per informazioni su come `InitializeAsync` metodo viene richiamato, vedere [passando i parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Questo meccanismo garantisce che ogni volta che un utente passa al SettingsView, le impostazioni utente vengono recuperate dall'archivio specifico della piattaforma e presentate tramite l'associazione dati. Quindi, se l'utente modifica i valori delle impostazioni, associazione dati garantisce che vengono rese persistenti immediatamente all'archiviazione specifico della piattaforma.

## <a name="summary"></a>Riepilogo

Le impostazioni consentono la separazione dei dati che consente di configurare il comportamento di un'app dal codice, il comportamento possono essere modificati senza ricompilare l'app. Le impostazioni dell'App sono dati di un'app vengono create e gestite e impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono frequenti regolazione nuovamente.

La libreria Xam.Plugins.Settings fornisce un coerente, indipendente dai tipi, approccio multipiattaforma per rendere persistente e il recupero delle app e impostazioni utente e l'associazione dati può essere utilizzato per accedere alle impostazioni create con la libreria.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
