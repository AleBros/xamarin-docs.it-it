---
title: Profilo utente
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: 0613411e5436a0ea8ed08bf4af52dae84a9a701c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307957"
---
# <a name="user-profile"></a>Profilo utente

Android ha supportato l'enumerazione dei contatti con la [ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/) provider dall'API di livello 5. Ad esempio, l'elenco contatti è semplice come l'utilizzo di [ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/) classe come illustrato nell'esempio di codice seguente:

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

A partire da Android 4 (livello API 14), il [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/) classe è disponibile tramite il `ContactsContract` provider. Il `ContactsContact.Profile` fornisce l'accesso al profilo personale per il proprietario di un dispositivo, che include i dati di contatto, ad esempio il numero di telefono e nome del proprietario del dispositivo.


## <a name="required-permissions"></a>Autorizzazioni necessarie

Per leggere e scrivere i dati di contatto, le applicazioni devono richiedere il `READ_CONTACTS` e `WRITE_CONTACTS` autorizzazioni, rispettivamente.
Inoltre, per leggere e modificare il profilo utente, le applicazioni devono richiedere il `READ_PROFILE` e `WRITE_PROFILE` autorizzazioni.


## <a name="updating-profile-data"></a>L'aggiornamento dei dati di profilo

Dopo che sono state impostate queste autorizzazioni, un'applicazione può usare tecniche di Android normale per interagire con i dati del profilo utente. Ad esempio, per aggiornare il nome del profilo visualizzato, chiamare [ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update) con un `Uri` recuperati tramite il [ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/) proprietà, come mostrato di seguito:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lettura di dati di profilo

Emette una query per il [ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/) legge nuovamente i dati del profilo. Ad esempio, il seguente codice leggerà nome visualizzato del profilo utente:

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

## <a name="navigating-to-the-user-profile"></a>Passare al profilo utente

Infine, per passare al profilo utente, creare un Intent con un `ActionView` azione e una `ContactsContract.Profile.ContentUri` quindi passarlo al `StartActivity` metodo simile al seguente:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Quando si esegue il codice sopra riportato, il profilo utente viene visualizzato come illustrato nello screenshot seguente:

[![Screenshot del profilo di visualizzazione del profilo utente John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

L'utilizzo con il profilo utente è simile all'interazione con altri dati in Android, e offre un livello aggiuntivo di personalizzazione di dispositivo.



## <a name="related-links"></a>Collegamenti correlati

- [ContactsProviderDemo (sample)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](https://developer.android.com/sdk/android-4.0.html)
