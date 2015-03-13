# Zeta Copos #
A small .NET library that encapsulates calls to the payment backend provider "COPOSweb" from Germany's Commerzbank.

![http://i3.codeplex.com/Project/Download/FileDownload.aspx?ProjectName=zetacopos&DownloadId=77865&img=image.png](http://i3.codeplex.com/Project/Download/FileDownload.aspx?ProjectName=zetacopos&DownloadId=77865&img=image.png)

## Introduction ##

When you want to sell your product to your customers through an online shop, you somehow have to enable the customers to pay for your products. There are several 3rd party companies (like e.g. [Plimus](http://home.plimus.com/ecommerce/)) that provides those services for online shops.

Since we wanted to give the customers a single UI experience and those 3rd party solutions usually do redirects to their own servers, we searched for an alternative solution to use in the online shop for our CMS [Zeta Producer](http://www.zeta-producer.com).

We found a web service (no real SOAP but similar) offered by Germany's Commerzbank, called "[COPOSweb](http://www.coposweb.de)".

Since no .NET interface to the COPOSweb web service existed, we developed our own wrapper/encapsulation library which is presented in this article.

## Requirements for using ##

In order to successfully use the library and the COPOSweb web service you must fulfill the following requirements:

  1. Have a .NET application/online shop to actually use the library (obviously).
  1. Be a German resident (unsure; maybe the sell their services worldwide, too).
  1. Sign a contract with Commerzbank to get account data.

## Using the code ##

The usage of the code should be rather straightforward; I have included a unit test in the source download.

To summarize a typical call to the COPOSweb web service through the ZetaCops library would look like:

```
// Each call needs a unique ID.
var id =
    Math.Abs( Guid.NewGuid().ToString( @"N" ).GetHashCode() ).ToString();

// Create a new client instance, providing your account login data.
var copos =
    new CoposClient(
        @"https://coposweb.companydirect.de/posh/cmd/posh/tpl/txn_result.tpl",
        @"COPOSUSER",
        @"COPOSPASSWORD" );

// Charge the given credit card with the given amount.
copos.DoPaymentCreditCard(
    id,
    @"1234123412341234",
    @"123",
    new DateTime( 2014, 04, 01 ),
    Iso4277CurrencyCodeEnum.EUR,
    1.0m );
```

In a real-world project the ` id ` would e.g. be your basket's unique ID and the credit card data would come from the online shop's front-end, entered by the customer. Similar, the amount and the currency depend on the product(s) being choosen by the customer to purchase.

Since it is a bad idea to hard-code your COPOSweb user name and password directly into the source code, I developed an extension that can be used inside your .NET application configuration file (usually "web.config") to configure all data through the config file only, without changing your source code.

A typical web.config file would look like:

```
<configuration>
    <configSections>
        <section 
            name="coposClient" 
            type="ZetaCopos.CoposClientConfigurationSectionHandler, ZetaCopos" />
    </configSections>
  
    ...    
  
    <coposClient
        coposWebServiceUri="..."
        coposUserName="..."
        coposPassword="..."
        timeoutSeconds="..."
        >
        <proxy
            address="..."
            credentialsUserName="..."
            credentialsPassword="..."
            credentialsDomain="..."
            />
    </coposClient>
</configuration>
```

The ` <proxy> ` tag allows you to define proxy settings for calling the COPOSweb web service.

## Epilog ##

In this article I introducted you a library to call a payment processor's backend web service. Probably the solutions is rather specific and tied to the COPOSweb web service of Commerzbank. If you want to use different backends you could at least take the source code described here as a basis for your own extensions.

We are using the library since approx. 3-4 years and having successfully processed several thousands of payments with it. So the code should be very stable and reliable.

As always, I would love to get your feedback, suggestions and enhancements. Feel free to drop me a note in the [Issues section](http://code.google.com/p/zetacopos/issues/list) of the article.

## History ##

  * **2009-08-04** - First release.