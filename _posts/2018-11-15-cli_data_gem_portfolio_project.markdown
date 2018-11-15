---
layout: post
title:      "CLI Data Gem Portfolio Project"
date:       2018-11-15 20:58:13 +0000
permalink:  cli_data_gem_portfolio_project
---


For my first project, I have decided to create a program that standardizes an address. Address standardization is the process of formatting an address so that it matches the approved format of the national postal authority. The USPS determines the official format for addresses in the United States.

Let's say you are responsible for shipping packages to customers. Unfortunately, one order from a customer has an address with missing information:

```
29851 AVENTURE #K
CA 92688
```

Standardizing the address should result to this:

```
29851 AVENTURA STE K
RANCHO SANTA MARGARITA
CA 92688-2014
```

By correcting errors and supplying a complete mailing address, the process of package delivery will be improved.

How does it work? The process is done in 3 steps:

1. Building the XML Request
2. Sending the XML Request
3. Unpacking the XML Response

### 1) Building the XML Request
After getting user input (asking the user for an address to standardize), it will wrap each input with the appropriate XML tag. Using the example above, the XML Request that it will later send to the USPS Shipping Web Tools server should look like this:

```
<AddressValidateRequest USERID="XXXX">
    <Address ID="0">
        <Address1></Address1>
        <Address2>29851 AVENTURE #K</Address2>
        <City></City>
        <State>CA</State>
        <Zip5>92688</Zip5>
        <Zip4></Zip4>
    </Address>
</AddressValidateRequest>
```

A username supplied by USPS is required to be able to use the API which can be requested by filling out this [form](https://registration.shippingapis.com/).

### 2) Sending the XML Request
The XML Request above will then be attached to the USPS Shipping Web Tools host, ```secure.shippingapis.com``` and name, ```Verify``` for address standardization. The format of the complete XML transaction should look this like this:

```https://secure.shippingapis.com/ShippingAPI.dll?API=Verify&XML=<AddressValidateRequest USERID="XXXX">...</AddressValidateRequest>```

### 3) Unpacking the XML Response
When the USPS Shipping Web Tools returns a response, it will either return a successful response (a standardized address) or an error explaining it:

![](https://i.imgur.com/TF6xsl8.png)

#### Scraping the XML Response
The scraping process will be done at this stage. With the help of Nokogiri and its CSS method, we can extract the ```text``` from each line. With the response being an XML, the tags are named conveniently so we can simply use the tag name as the target.

```
response = Nokogiri::XML(open(https://secure.shippingapis.com/ShippingAPI.dll?API=Verify&XML=<AddressValidateRequest USERID="XXXX">...</AddressValidateRequest>))

address_2 = response.css("Address2").text
city = response.css("City").text
state = response.css("State").text
zip_5 = response.css("Zip5").text
zip_4 = response.css("Zip4").text
```

With the information we have extracted, we can format and display it for better readability to the user:

```
Address:  29851 AVENTURA STE K
City:     RANCHO SANTA MARGARITA
State:    CA
ZIP Code: 92688
ZIP + 4:  9997
```

Doing this project was fun and has taught me a lot. It helped me better understand some topics I was confused about and wrap up everything I have learned so far from the curriculum. I used Pry extensively to look into methods and variables to make sure they are returning the correct values and [repl.it](https://repl.it) to experiment with and test ideas I had in mind.
