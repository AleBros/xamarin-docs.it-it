---
title: Profilo utente
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: bed346b33ac92f6a1c73cdd3b29fb70ba17c5e91
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509662"
---
# <a name="user-profile"></a>Profilo utente

Android ha supportato l'enumerazione dei contatti con il provider [ContactsContract](xref:Android.Provider.ContactsContract) a partire dal livello API 5. Ad esempio, l'elenco dei contatti è semplice come l'uso della classe [ContactContracts. Contacts](xref:Android.Provider.ContactsContract.Contacts) , come illustrato nell'esempio di codice seguente:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

A partire da Android 4 (livello API 14), la classe [ContactsContact. profile](xref:Android.Provider.ContactsContract.Profile) è disponibile tramite `ContactsContract` il provider. `ContactsContact.Profile` Consente di accedere al profilo personale per il proprietario di un dispositivo, che include i dati di contatto, ad esempio il nome e il numero di telefono del proprietario del dispositivo.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per leggere e scrivere i dati di contatto, le applicazioni `READ_CONTACTS` devono `WRITE_CONTACTS` richiedere rispettivamente le autorizzazioni e.
Inoltre, per leggere e modificare il profilo utente, le applicazioni devono richiedere `READ_PROFILE` le `WRITE_PROFILE` autorizzazioni e.

## <a name="updating-profile-data"></a>Aggiornamento dei dati del profilo

Una volta impostate queste autorizzazioni, un'applicazione può usare le normali tecniche di Android per interagire con i dati del profilo utente. Ad esempio, per aggiornare il nome visualizzato del profilo, chiamare [ContentResolver. Update](xref:Android.Content.ContentResolver.Update*) con un `Uri` oggetto recuperato tramite la proprietà [ContactsContract. profile. ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri) , come illustrato di seguito:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lettura dei dati del profilo

Il rilascio di una query a [ContactsContact. profile. ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) legge i dati del profilo. Nel codice seguente, ad esempio, viene letto il nome visualizzato del profilo utente:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>Spostamento al profilo utente

Infine, per passare al profilo utente, creare una finalità con un' `ActionView` azione `ContactsContract.Profile.ContentUri` e `StartActivity` quindi passarla al metodo come segue:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

Quando si esegue il codice precedente, il profilo utente viene visualizzato come illustrato nello screenshot seguente:

[![Screenshot del profilo che Visualizza il profilo utente di John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Lavorare con il profilo utente è simile all'interazione con altri dati in Android e offre un livello aggiuntivo di personalizzazione del dispositivo.

## <a name="related-links"></a>Collegamenti correlati

- [ContactsProviderDemo (esempio)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
