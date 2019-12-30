---
layout: post
status: publish
published: true
title: Adding an SSL Certificate to Mac OS X Server 10.10
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 566
wordpress_url: http://djmadeira.com/?p=566
date: '2015-02-06 01:14:47 -0500'
date_gmt: '2015-02-06 01:14:47 -0500'
tags: 
- archived
comments: []
---
I recently had to install a new wildcard SSL certificate on our servers at work. It was a painful process that involved a lot of trial and error, mostly because I could only ever find instructions for a piece of the process at a time, and they often had different ideas about how the previous steps had gone. I wanted to put everything I learned in one place to make the process easier on the next person (and future me). There may be trial and error ahead of you yet, but at least you'll have a consistent overview of the process to check in on.

I recommend you <strong>read the entire guide first</strong>, then check it against your Certificate Authority (CA)'s directions and the directions that came with OS X server. I'm going to differ from how OS X server recommends you create SSL certs, since my CA required it to be done a bit differently, and odds are so does yours, which is why you're here.

<strong>Update:</strong> We were never able to get a Comodo SSL cert to work with Open Directory. We later tried a RapidSSL cert, and that worked. Something to consider when deciding where to get your certificate.

<h2>Background</h2>
I'm going to quickly define some of terms, for your edification and to reduce confusion.

CSR: Certificate Signing Request. This is a file with information about your request for an SSL certificate, like your organization name and location. You need this to kick off the process.

CRT: Certificate. This is what says "Hey, you can trust that I am who I say I am, see? You can check with this other person you already trust!" The client connecting to your server has an internal list of certificates it trusts, and if one of those certificates was used to sign your certificate, it'll trust that the public key you're offering really belongs to you. This process is recursive; the authority that signed your certificate probably also had their certificate signed by somebody else, and so on, and the client will keep hopping up the chain until it finds someone it trusts.

CA: Certificate Authority. This is someone recognized by someone else to sign certificates. Odds are, you are not one. So you have to buy a certificate from someone who has authority to give them out. Having authority just means that somebody decided to say to everyone who trusts them "You can trust this guy too. He's cool." And they do the same thing for you! Now you're sitting at the feet of a mighty pyramid of trust going all the way up to... whoever your operating system manufacture decided to trust at the time your OS was last updated. (If you're curious, you can open Keychain Access and see who your "root" CAs are in the "System Roots" section.

PEM: This is the format for most of the files you'll work with as you go through this ordeal. They can have .pem extension, or they can have a more specific extension that tells you what they really are, but are just conventions to make keeping the files straight easier.

<h3>A note on formats</h3>
The formats and file extension used for this process are a total fluster cluck. Some instructions call certificate files .pem, some .crt, while you might get a CSR that has a .pem extension, a .csr, or even .crt. They all look roughly like this when you open them:
<pre><code>-----BEGIN CERTIFICATE-----
dGhlIHF1aWNrIGJyb3duIGZveCBqdW1wZWQgb3ZlciB0aGUgbGF6eSBkb2cgb3ZlciBhbmQgb3ZlciBhZ2FpbiB0aGUgcXVpY2sgYnJvd24gZm94IGp1bXBlZCBvdmVyIHRoZSBsYXp5IGRvZyBvdmVyIGFuZCBvdmVyIGFnYWluIHRoZSBxdWljayBicm93biBmb3gganVtcGVkIG92ZXIgdGhlIGxhenkgZG9nIG92ZXIgYW5kIG92ZXIgYWdhaW4gdGhlIHF1aWNrIGJyb3duIGZveCBqdW1wZWQgb3ZlciB0aGUgbGF6eSBkb2cgb3ZlciBhbmQgb3ZlciBhZ2FpbiB0aGUgcXVpY2sgYnJvd24gZm94IGp1bXBlZCBvdmVyIHRoZSBsYXp5IGRvZyBvdmVyIGFuZCBvdmVyIGFnYWluIHRoZSBxdWljayBicm93biBmb3gganVtcGVkIG92ZXIgdGhlIGxhenkgZG9nIG92ZXIgYW5kIG92ZXIgYWdhaW4gdGhlIHF1aWNrIGJyb3duIGZveCBqdW1wZWQgb3ZlciB0aGUgbGF6eSBkb2cgb3ZlciBhbmQgb3ZlciBhZ2FpbiB0aGUgcXVpY2sgYnJvd24gZm94IGp1bXBlZCBvdmVyIHRoZSBsYXp5IGRvZyBvdmVyIGFuZCBvdmVyIGFnYWluIHRoZSBxdWljayBicm93biBmb3gganVtcGVkIG92ZXIgdGhlIGxhenkgZG9nIG92ZXIgYW5kIG92ZXIgYWdhaW4gdGhlIHF1aWNrIGJyb3duIGZveCBqdW1wZWQgb3ZlciB0aGUgbGF6eSBkb2cgb3ZlciBhbmQgb3ZlciBhZ2FpbiB0aGUgcXVpY2sgYnJvd24gZm94IGp1bXBlZCBvdmVyIHRoZSBsYXp5IGRvZyBvdmVyIGFuZCBvdmVyIGFnYWluIAo=
-----END CERTIFICATE-----
</code></pre>

There's a header, some base64-encoded text, and a footer. Because of this, it can be easy to get them mixed up; but there's a big difference between your CSR and your CRT; so name your files carefully so you don't get confused. I tend to use:

<ul>
<li>CSR: .csr</li>
<li>Certificate: .crt</li>
<li>Private key: .key</li>
<li>PKCS12: .p12 (not an interchangeable format with the above)</li>
</ul>

<h2>Step 1: Generate your private key &amp; submit your CSR</h2>
<strong>This is the most important step to get right, as it's very hard to go back after submitting your CSR. Triple-check everything.</strong>

You can use OS X Server to generate your CSR, but sometimes your CA will require some fields that OS X server doesn't give you a chance to add. Furthermore, the rest of the guide will assume you'll generate your CSR according to <a href="http://www.rackspace.com/knowledge_center/article/generate-a-csr-with-openssl">these instructions</a>. I'd strongly recommend that; if you do it through the Server.app, you'll be on your own, although the rest of this guide might still be helpful.

When you're done, open up the .csr in a text editor and make sure it looks roughly like the example above. There should be a header that says "-----BEGIN CERTIFICATE REQUEST-----", a jumble of letters, and "-----END CERTIFICATE REQUEST-----". When you're sure you entered all the fields exactly right, <strong>back up your private key somewhere</strong> and submit the CSR to your CA. Oh, and <strong>back up your private key somewhere.</strong> There's no reseting that.

<h2>Step 2: Get your Certificate from your CA</h2>
When your CA generates your Certificate (you'll likely have to wait a few days), download it or copy the text provided and save as sslcert.crt.

<h2>Step 3: Combine CRT &amp; private key into .p12 and import into the Keychain</h2>
In order to import into the System keychain, you'll need to bundle the private key that you created when you generated the CSR and the .crt you just downloaded. You can do this by packing it into a PKCS12 file. Open Terminal, <code>cd</code> to where your .crt and private key are, and run this command:

<pre><code>$ openssl pkcs12 -export -inkey domain.com.key -in sslcert.crt -out sslcert.p12 -name "SSL Key"
</code></pre>

Open Keychain access, select and unlock the "System" keychain, click "File" -> "Import Items..." and select your sslcert.p12. You can't just double click the file, because Keychain Access won't add it to the right keychain.

<h2>Step 4: Get the CA Cert &amp; Domain Validation CRT</h2>
If, after you import the .p12 into your System keychain, your certificate says "This certificate has been signed by and unknown authority", you need to find the CA certificate that matches the <strong>exact</strong> name listed on the certificate for your domain. For example, if Keychain Access says "Issued By: Acme Domain SSL CA Company, Limited", google "Acme Domain Company SSL CA Company, Limited CA", and find the Certificate you need to get OS X to recognize your SSL certificate.

Follow the same procedure to import; do <strong>not</strong> double click to add the CRT. It must be added to the System keychain using File -> Import Items... in Keychain Access.
If you find and add the provider's relevant certificate and your certificate is still coming up unrecognized, you may also have to find the Intermediate CA CRT for your provider. This is the certificate in that pyramid I mentioned before that certifies the CA that signed your certificate.

<h2>Conclusion</h2>
If you did everything right, your certificate should show up in OS X Server's certificates. If it didn't, god help you.
Here are some things to try if my steps didn't work:

<ul>
<li><a href="http://support.apple.com/en-us/HT203731">OS X Server: Access Controls might prevent a certificate identity from working with Server services</a></li>
</ul>
Here's some resources that I found helpful:
<ul>
<li><a href="https://www.digicert.com/ssl-support/p12-import-export-mac-mavericks-server.htm">Mac OS X Mavericks: SSL Certificate Export and Import</a></li>
<li><a href="https://www.digicert.com/ssl-certificate-installation-mac-osx-mavericks.htm">Mac OS X Mavericks Server: SSL Certificate Installation</a></li>
<li><a href="http://serverfault.com/questions/9708/what-is-a-pem-file-and-how-does-it-differ-from-other-openssl-generated-key-file">What is a PEM file?</a></li>
</ul>
