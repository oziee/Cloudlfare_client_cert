So I wont go through setting up Cloudflare, creating accounts and how to setup a tunnel, there are plenty of write ups out there to show you how.  

There are two parts.. Part 1 is creating the certificate and part 2 is Cloudflare WAF

# PART 1
1. So log into you Cloudflare site and navigate to the SSL/TLS.
2. Make sure that your `Hosts` are configurated to you site.. ie *.example.com and example.com
3. Click `Create Certificate`
![p1](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/2da08ca8-4c8d-4551-8af6-8283fe3d4a51)



On this page, I only altered the validity period to 15 years and click `Create`
![p2](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/7827cab3-9e88-4082-bf2c-47c608d620e0)


Next is to create the certificate files
![p3](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/21782e0a-2f0b-4768-a79f-2e456071bf12)

Create a directory to store both these files we will create..
1. Copy each item ` Certificate` and `Private Key` (shown above) and create a file for each. The easist way is copy the text and head to a command prompt and type in something like `nano client.cert` and paste in the `Certifiate` text. Do not leave any blank or extra spaces/text after the `-----END CERTIFICATE-----`
2. Click `Control + O` and enter to save, then `Control + X` for close nano back the to prompt
3. Repeat the same for the `Private Key` with something like `nano client.key`
![cmd](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/0f7630bb-12e1-4e9b-90c6-2bd62025186b)
*Do no loose these files as this is the only time Cloudlfare will display for you the `Private Key` (if you loose it you need to start again)


Now that you have these two file you need to create this for iOS format using `OpenSSL`. MacOS and iOS will not accept certs make with OpenSSL without the `-legacy` flag. If you do not add this Apple devices will just repeat asking you for the password over and over. I do not have Android so I do not know if this legacy key is needed or not.

At the command prompt type `openssl version` and you might see something like `OpenSSL 3.1.3 19 Sep 2023 (Library: OpenSSL 3.1.3 19 Sep 2023)`
If you do not see an output then you most likely do not have OpenSSL installed and will need to do that now. Just google `how to install openssl on xxx` where xxx is windows or mac

Make sure that the directory you are in contains the two files
type in
`openssl pkcs12 -export -clcerts -in client.crt -inkey client.key -out device.p12 -legacy`

Do not worry about any warnings and you should be presented with `Enter Export Password:`. Enter in something not too hard as you have to be able to type this in on the device.. `Verifying - Enter Export Password:` type it again, and the prompt should end with no errors that you didnt retype the same password

This `device.p12` is your file to install on the device. On MacOS just double click to install it.. on your iOS device, email, txt, icloud file copy.. anymeans :) 

On iOS when you click the file you will see a message  
![p4](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/58d73506-d8c9-4701-aacc-3531dd5d281d)

Go you you Settings and click the `Profile Downloaded`
![p5](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/e84e6310-56b7-4deb-b6f1-9b8dd0412a63)

Click `Install` and follow the prompts
![p6](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/b90d69bf-d575-4f5c-a612-7bc78933bd5b)


# PART 2

In Cloudflare where we created the certificate click `Create mTLS Rule`.  
![p7](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/ebcbe782-52c8-4a23-a947-31c237d3dd44)


Create the rule for it looks something like this. You can add more rules if you like. Things like `Country != <your country>` this will weed out all the smapping bots etc from china etc from going any further. Make sure the rules are correct with `AND` and `County is NOT EQUAL` because this rule is to `BLOCK` so if you have Country EQUALS your country and you try to connect you will get blocked.     
![p8](https://github.com/oziee/Cloudlfare_client_cert/assets/1471841/a41f3a76-aaee-4f98-903a-f4f61e74e1da)




