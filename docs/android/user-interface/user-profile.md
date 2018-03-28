---
title: Profilo utente
ms.topic: article
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/22/2018
ms.openlocfilehash: 1407266f987b36b72e32a82c8f6f43b4a734af5d
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="user-profile"></a>Profilo utente

Android è supportata l'enumerazione delle contatti con il [ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/) provider dall'API livello 5. Ad esempio, l'elenco di contatti è semplice come l'utilizzo di [ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/) classe come illustrato nell'esempio di codice seguente:

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

A partire da Android 4 (API livello 14), il [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/) classe è disponibile tramite il `ContactsContract` provider. Il `ContactsContact.Profile` fornisce l'accesso al profilo personale per il proprietario di un dispositivo, che include i dati di contatto, ad esempio il numero di telefono e nome del proprietario del dispositivo.


## <a name="required-permissions"></a>Autorizzazioni necessarie

Per leggere e scrivere dati di contatto, è necessario richiedere le applicazioni di `READ_CONTACTS` e `WRITE_CONTACTS` autorizzazioni, rispettivamente.
Inoltre, per leggere e modificare il profilo utente, le applicazioni devono richiedere la `READ_PROFILE` e `WRITE_PROFILE` le autorizzazioni.


## <a name="updating-profile-data"></a>Aggiornamento dei dati di profilo

Una volta queste autorizzazioni sono state impostate, un'applicazione può utilizzare le tecniche di Android normale per interagire con i dati del profilo utente. Ad esempio, per aggiornare il nome del profilo visualizzato, chiamare [ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update) con un `Uri` recuperati tramite il [ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/) proprietà, come mostrato di seguito:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lettura di dati di profilo

Esecuzione di una query per il [ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/) legge nuovamente i dati di profilo. Ad esempio, il codice seguente leggerà nome visualizzato del profilo utente:

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

Infine, per passare al profilo utente, creare un intento con un `ActionView` azione e una `ContactsContract.Profile.ContentUri` passarlo al `StartActivity` metodo simile al seguente:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Quando si esegue il codice sopra riportato, il profilo utente viene visualizzato come illustrato nella schermata seguente:

[![Schermata del profilo di visualizzazione del profilo utente John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

L'utilizzo con il profilo utente è simile all'interazione con altri dati in Android, e offre un livello aggiuntivo di personalizzazione di dispositivo.



## <a name="related-links"></a>Collegamenti correlati

- [ContactsProviderDemo (esempio)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
