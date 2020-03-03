---
title: 'Xamarin.Essentials: Autorizzazioni'
description: Questo documento descrive la classe di autorizzazioni in Xamarin.Essentials che consente di controllare e richiedere le autorizzazioni di runtime.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2b1413c6bce7fc4ce43a190cd25494c69dbadedb
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555689"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Autorizzazioni

La classe di **autorizzazioni** consente di controllare e richiedere le autorizzazioni di runtime.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Uso delle autorizzazioni

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Controllo delle autorizzazioni

Per controllare lo stato corrente di un'autorizzazione, usare il metodo `CheckStatusAsync` insieme all'autorizzazione specifica di cui ottenere lo stato.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Se l'autorizzazione necessaria non è dichiarata, viene generata un'eccezione `PermissionException`.

## <a name="requesting-permissions"></a>Richiesta di autorizzazioni

Per richiedere un'autorizzazione agli utenti, usare il metodo `RequestAsync` insieme all'autorizzazione specifica da richiedere. Se l'utente ha precedentemente concesso l'autorizzazione e non l'ha revocata, questo metodo restituirà immediatamente `Granted` e non visualizzerà una finestra di dialogo. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Se l'autorizzazione necessaria non è dichiarata, viene generata un'eccezione `PermissionException`. 

Si noti che in alcune piattaforme è possibile attivare una richiesta di autorizzazione una sola volta. È necessario che lo sviluppatore gestisca ulteriori richieste per controllare se lo stato dell'autorizzazione è `Denied` e richieda all'utente di attivarla manualmente.

## <a name="permission-status"></a>Stato dell'autorizzazione

Quando si usa `CheckStatusAsync` o `RequestAsync` viene restituito `PermissionStatus` che verrà usato per determinare i passaggi successivi.

* Sconosciuto: lo stato dell'autorizzazione è sconosciuto
* Negato: l'utente ha negato la richiesta dell'autorizzazione
* Disabilitato: la funzionalità è disabilitata nel dispositivo
* Concesso: l'autorizzazione è concessa dall'utente oppure automaticamente
* Con restrizioni: lo stato ha restrizioni

## <a name="available-permissions"></a>Autorizzazioni disponibili

Xamarin.Essentials tenta di astrarre il maggior numero possibile di autorizzazioni, ma ogni sistema operativo ha un set di autorizzazioni di runtime diverso. Esistono inoltre alcune differenze nella possibilità di fornire una singola API per alcune autorizzazioni. Di seguito è riportata una guida per le autorizzazioni attualmente disponibili:

Guida alle icone:

* ![Supporto completo](~/media/shared/yes.png "supporto completo") - Supportato
* ![Non supportato](~/media/shared/no.png "Non supportato o obbligatorio") - Non supportato/obbligatorio

| Autorizzazioni | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| CalendarWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Fotocamera | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| ContactsRead | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| ContactsWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Torcia elettrica | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| LocationWhenInUse | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato")  | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| LocationAlways | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Supporti | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Microfono | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Phone | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Foto | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Reminders | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sensors | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sms | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sintesi vocale | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageRead | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |

Se un'autorizzazione è contrassegnata con ❌ restituirà sempre `Granted` quando viene selezionata o richiesta.

## <a name="general-usage"></a>Utilizzo generale
Di seguito è riportato un modello di utilizzo generale per la gestione delle autorizzazioni.

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

Ogni tipo di autorizzazione può includere un'istanza e i metodi possono essere chiamati direttamente.

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>Estensione delle autorizzazioni

L'API delle autorizzazioni è stata creata per essere flessibile ed estendibile per le applicazioni che richiedono una convalida o autorizzazioni aggiuntive non incluse in Xamarin.Essentials. Creare una nuova classe che eredita da `BasePermission` e implementare i metodi astratti richiesti. Quindi 

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

Quando si implementa un'autorizzazione in una piattaforma specifica, è possibile ereditare la classe `BasePlatformPermission`. Questa operazione offre ulteriori metodi helper della piattaforma per controllare automaticamente le dichiarazioni.

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Per le autorizzazioni è necessario impostare gli attributi di corrispondenza nel file manifesto di Android.

Altre informazioni sono disponibili nella documentazione [Autorizzazioni in Xamarin.Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions).

# <a name="ios"></a>[iOS](#tab/ios)

Le autorizzazioni devono includere una stringa di corrispondenza nel file `Info.plist`. Quando un'autorizzazione viene richiesta e viene negata, se si richiede l'autorizzazione una seconda volta, non viene più visualizzato un popup. È necessario richiedere all'utente di modificare manualmente l'impostazione nella schermata delle impostazioni delle applicazioni in iOS.

Altre informazioni sono disponibili nella documentazione [Funzionalità per la sicurezza e la privacy di iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy).

# <a name="uwp"></a>[UWP](#tab/uwp)

Per le autorizzazioni è necessario dichiarare le funzionalità di corrispondenza nel manifesto del pacchetto.

Altre informazioni sono disponibili nella documentazione [Dichiarazioni di funzionalità delle app](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

--------------

## <a name="api"></a>API

- [Codice sorgente delle autorizzazioni](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentazione dell'API delle autorizzazioni](xref:Xamarin.Essentials.Permissions)

