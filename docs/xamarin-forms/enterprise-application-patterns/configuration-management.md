---
title: Gestione della configurazione
description: Questo capitolo illustra il modo in cui l'app eShopOnContainers mobile implementa la gestione della configurazione per fornire le impostazioni dell'app e le impostazioni utente.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: cc83a18cd8c391c6228cf9d813ecf8bca795caba
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760287"
---
# <a name="configuration-management"></a>Gestione della configurazione

Le impostazioni consentono la separazione dei dati che configura il comportamento di un'app dal codice, consentendo la modifica del comportamento senza ricompilare l'app. Esistono due tipi di impostazioni: le impostazioni dell'app e le impostazioni utente.

Le impostazioni dell'app sono dati che un'app crea e gestisce. Può includere dati quali endpoint del servizio Web fisso, chiavi API e stato di Runtime. Le impostazioni dell'app sono legate all'esistenza dell'app e sono significative solo per tale app.

Le impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono una riregolazione frequente. Ad esempio, un'app potrebbe consentire all'utente di specificare la posizione da cui recuperare i dati e come visualizzarla sullo schermo.

Xamarin.Forms include un dizionario permanente che può essere usato per archiviare i dati delle impostazioni. È possibile accedere a questo dizionario usando [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) la proprietà e tutti i dati inseriti vengono salvati quando l'app entra in uno stato di sospensione e viene ripristinata quando l'app viene ripresa o riavviata. Inoltre, la [`Application`](xref:Xamarin.Forms.Application) classe dispone anche di un [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo che consente di salvare le impostazioni di un'app quando richiesto. Per ulteriori informazioni su questo dizionario, vedere [dizionario delle proprietà](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Un aspetto negativo dell'archiviazione dei dati tramite il dizionario persistente Xamarin.Forms è che non è facile da associare ai dati. L'app per dispositivi mobili eShopOnContainers usa quindi la libreria xam. plugins. Settings, disponibile in [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Questa libreria offre un approccio coerente, indipendente dai tipi e multipiattaforma per la persistenza e il recupero delle impostazioni dell'applicazione e dell'utente, usando la gestione delle impostazioni native fornita da ogni piattaforma. Inoltre, è facile utilizzare data binding per accedere ai dati delle impostazioni esposti dalla libreria.

> [!NOTE]
> Sebbene la libreria xam. plugin. Settings possa archiviare le impostazioni dell'app e dell'utente, non fa distinzione tra i due.

## <a name="creating-a-settings-class"></a>Creazione di una classe Settings

Quando si usa la libreria xam. plugins. Settings, è necessario creare una singola classe statica che conterrà le impostazioni dell'app e dell'utente richieste dall'app. L'esempio di codice seguente illustra la classe Settings nell'app per dispositivi mobili eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Le impostazioni possono essere lette e scritte `ISettings` tramite l'API, fornita dalla libreria xam. plugins. Settings. Questa libreria fornisce un singleton che può essere usato per accedere all'API, `CrossSettings.Current`, e una classe di impostazioni dell'app deve esporre il singleton tramite `ISettings` una proprietà.

> [!NOTE]
> Le direttive using per gli spazi dei nomi plugin. Settings e plugin. Settings. abstracts devono essere aggiunte a una classe che richiede l'accesso ai tipi di libreria xam. plugins. Settings.

## <a name="adding-a-setting"></a>Aggiunta di un'impostazione

Ogni impostazione è costituita da una chiave, un valore predefinito e una proprietà. Nell'esempio di codice seguente vengono illustrati tutti e tre gli elementi per un'impostazione utente che rappresenta l'URL di base per il Servizi online a cui si connette l'app per dispositivi mobili eShopOnContainers:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

La chiave è sempre una stringa const che definisce il nome della chiave e il valore predefinito per l'impostazione è un valore di sola lettura statico del tipo richiesto. Se si specifica un valore predefinito, si garantisce la disponibilità di un valore valido se viene recuperata un'impostazione non impostata.

La `UrlBase` proprietà statica usa due metodi `ISettings` dell'API per leggere o scrivere il valore dell'impostazione. Il `ISettings.GetValueOrDefault` metodo viene usato per recuperare il valore di un'impostazione dall'archiviazione specifica della piattaforma. Se non viene definito alcun valore per l'impostazione, viene invece recuperato il relativo valore predefinito. Analogamente, `ISettings.AddOrUpdateValue` il metodo viene utilizzato per salvare in modo permanente un valore di un'impostazione nell'archiviazione specifica della piattaforma.

Anziché definire un valore predefinito all'interno della `Settings` classe, la `UrlBaseDefault` stringa ottiene il relativo valore dalla `GlobalSetting` classe. Nell'esempio di codice seguente vengono `BaseEndpoint` illustrati `UpdateEndpoint` la proprietà e il metodo in questa classe:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Ogni volta che `BaseEndpoint` la proprietà è impostata, `UpdateEndpoint` viene chiamato il metodo. Questo metodo aggiorna una serie di proprietà, tutte basate sull' `UrlBase` impostazione utente fornita `Settings` dalla classe che rappresenta endpoint diversi a cui si connette l'app per dispositivi mobili eShopOnContainers.

## <a name="data-binding-to-user-settings"></a>Data binding a impostazioni utente

Nell'app per dispositivi mobili eShopOnContainers, `SettingsView` espone due impostazioni utente. Queste impostazioni consentono di configurare se l'app deve recuperare i dati dai microservizi distribuiti come contenitori Docker o se l'app deve recuperare i dati da servizi fittizi che non richiedono una connessione Internet. Quando si sceglie di recuperare i dati da microservizi in contenitori, è necessario specificare un URL dell'endpoint di base per i microservizi. La `SettingsView` figura 7-1 Mostra quando l'utente ha scelto di recuperare i dati dai microservizi in contenitori.

![](configuration-management-images/settings-endpoint.png "Impostazioni utente esposte dall'app per dispositivi mobili eShopOnContainers")

**Figura 7-1**: Impostazioni utente esposte dall'app per dispositivi mobili eShopOnContainers

Il `Settings` Data Binding può essere usato per recuperare e impostare le impostazioni esposte dalla classe. Questa operazione viene eseguita dai controlli sull'associazione di visualizzazione per visualizzare le proprietà del modello che a loro volta `Settings` accedono alle proprietà nella classe e generano una notifica di modifica della proprietà se il valore delle impostazioni è stato modificato. Per informazioni sul modo in cui l'app per dispositivi mobili eShopOnContainers costruisce i modelli di visualizzazione e li associa alle visualizzazioni, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modelli di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Nell'esempio di codice seguente viene [`Entry`](xref:Xamarin.Forms.Entry) illustrato il controllo `SettingsView` di che consente all'utente di immettere un URL dell'endpoint di base per i microservizi in contenitori:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Questo [`Entry`](xref:Xamarin.Forms.Entry) controllo è associato `Endpoint` alla proprietà della `SettingsViewModel` classe usando un'associazione bidirezionale. Nell'esempio di codice seguente viene illustrata la proprietà endpoint:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Quando la `Endpoint` proprietà è impostata, `UpdateEndpoint` viene chiamato il metodo, purché il valore fornito sia valido e venga generata una notifica di modifica della proprietà. L'esempio di codice seguente illustra il metodo `UpdateEndpoint`:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Questo metodo aggiorna la `UrlBase` proprietà `Settings` nella classe con il valore dell'URL dell'endpoint di base immesso dall'utente, in modo che venga salvato in modo permanente nell'archiviazione specifica della piattaforma.

Quando si passa a, viene eseguito il `InitializeAsync` metodo nella `SettingsViewModel` classe. `SettingsView` L'esempio di codice seguente illustra il metodo:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Il metodo imposta la `Endpoint` proprietà sul valore `UrlBase` della proprietà nella `Settings` classe. L'accesso alla `UrlBase` proprietà comporta la libreria xam. plugins. Settings per recuperare il valore delle impostazioni dall'archiviazione specifica della piattaforma. Per informazioni sul modo in `InitializeAsync` cui viene richiamato il metodo, vedere [passaggio di parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Questo meccanismo garantisce che ogni volta che un utente passa a SettingsView, le impostazioni utente vengono recuperate dall'archiviazione specifica della piattaforma e presentate tramite data binding. Quindi, se l'utente modifica i valori delle impostazioni, data binding garantisce che vengano immediatamente salvati in modo permanente nell'archiviazione specifica della piattaforma.

## <a name="summary"></a>Riepilogo

Le impostazioni consentono la separazione dei dati che configura il comportamento di un'app dal codice, consentendo la modifica del comportamento senza ricompilare l'app. Le impostazioni dell'app sono dati che un'app crea e gestisce e le impostazioni utente sono le impostazioni personalizzabili di un'app che influiscono sul comportamento dell'app e non richiedono una riregolazione frequente.

La libreria xam. plugins. Settings fornisce un approccio coerente, indipendente dai tipi e multipiattaforma per la persistenza e il recupero delle impostazioni dell'app e dell'utente e data binding può essere usato per accedere alle impostazioni create con la libreria.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
