# CORS one liner command exploiter

This is an extremely helpful and practical Cheatsheet for Bug Hunters, which helps you find CORS missconfiguration in every possible method. Simply replace https://example.com with the URL you want to target. This will help you scan for CORS vulnerability without the need of an external tool. What you have to do is to copy-and-paste the commands into your terminal and finger crossed for any possible CORS.

## 1 Basic Origin Reflection payload - (Automatic) Send request to every crawled endpoint of the website
### Workflow:  
HTTP Header payload is sent: `Origin: https://evil.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://evil.com`  

`site="https://example.com"; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $url;else echo Nothing on "$url";fi;done`

## 1.2 Basic Origin Reflection payload - (Manualy) Send request in only one endpoint
### Workflow:  
HTTP Header payload is sent: `Origin: https://evil.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://evil.com`  

`site="https://example.com";curl -s -I -H "Origin: https://evil.com" -X GET "$site" | if grep 'https://evil.com'; then echo [Potentional CORS Found]; else echo Nothing found;fi`

## 2 Trusted null Origin payload - (Automatic) Send request to every crawled endpoint of the website
### Workflow:  
HTTP Header payload is sent: `Origin: null`  
HTTP Header is excpected: `Access-Control-Allow-Origin: null`  

`site="https://example.com"; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: null" -X GET $url) | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found] "$url"; else echo Nothing on: "$url";fi;done`

## 2.2 Trusted null Origin payload - (Manualy) Send request in only one endpoint
### Workflow:  
HTTP Header payload is sent: `Origin: null`  
HTTP Header is excpected: `Access-Control-Allow-Origin: null`  

`site="https://example.com";curl -s -I -H "Origin: null" -X GET "$site" | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found]; else echo Nothing found;fi`

## 3 Whitelisted null origin value payload - (Automatic) Send request to every crawled endpoint of the website
### Workflow:  
No HTTP Header payload is sent   
HTTP Header is excpected: `Access-Control-Allow-Origin: null`  

`site="https://example.com" ; gau "$site" | while read url;do target=$(curl -s -I -X GET "$url") | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found] "$url"; else echo Nothing on: "$url";fi;done`

## 3.2 Whitelisted null origin value payload - (Manualy) Send request in only one endpoint
No HTTP Header payload is sent   
HTTP Header is excpected: `Access-Control-Allow-Origin: null`  

`site="https://example.com";curl -I -X GET "$site" | if grep 'Access-Control-Allow-Origin: null';then echo [Potential CORS Found];else echo Nothing found;fi`

## 4 Trusted subdomain in Origin payload [ *.example.com ] - (Manualy) Send request in only one endpoint
### Workflow:  
HTTP Header payload is sent: `Origin: evil.example.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: evil.example.com`  

`url="https://example.com" ; curl -s -I -H "Origin: evil.$url" -X GET "$url" | if grep "Access-Control-Allow-Origin: evil.$url"; then echo [Potential CORS Found]; else echo [No CORS found]; fi`

## 4.2 Trusted subdomain in Origin payload [ *.example.com ] - (Automatic) Send request to every crawled endpoint of the website
### Workflow:  
HTTP Header payload is sent: `Origin: evil.example.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: evil.example.com`  

`site="https://example.com" ; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: evil.$url" -X GET "$url") | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found] "$url"; else echo Nothing on: "$url";fi;done`

## 5 Abuse on not properly Domain validation - (Note: Replace URL with only domain - without http(s) protocols) - (Manualy)
### Workflow:  
HTTP Header payload is sent: `Origin: notexample.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://notexample.com`  

`site="example.com";curl -s -I -H "Origin: https://not$site" -X GET "$site"| if grep "Access-Control-Allow-Origin: https://not$site"; then echo [Potentional CORS Found]; else echo Nothing found;fi`

## 5.2 Abuse on not properly Domain validation - (Note: Replace URL with only domain - without http(s) protocols) - (Automatic) Send request to every crawled endpoint of the website
### Workflow:  
HTTP Header payload is sent: `Origin: notexample.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://notexample.com`  

`site="https://example.com" ; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: https://not$site" -X GET "$url") | if grep 'Access-Control-Allow-Origin: https://not$site'; then echo [Potentional CORS Found] "$url"; else echo Nothing on: "$url";fi;done`

## 6 Origin domain extension not validated vulnerability - (Manualy) Send request in only one endpoint
### Workflow:  
HTTP Header payload is sent: `Origin: example.com.evil.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://example.com.evil.com`  

`site="https://example.com";curl -s -I -H "Origin: $site.evil.com" -X GET "$site" | if grep "Origin: Access-Control-Allow-Origin: $site.evil.com"; then echo [Potentional CORS Found]; else echo Nothing found;fi`

## 6 Origin domain extension not validated vulnerability - (Manualy) Send request in only one endpoint
### Workflow:  
HTTP Header payload is sent: `Origin: example.com.evil.com`  
HTTP Header is excpected: `Access-Control-Allow-Origin: https://example.com.evil.com`  

`site="https://example.com" ; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: $site.evil.com" -X GET "$url") | if grep "Origin: Access-Control-Allow-Origin: $site.evil.com"; then echo [Potentional CORS Found] "$url"; else echo Nothing on: "$url";fi;done`

# Requirements

- Download Gua from https://github.com/lc/gau (only for the automatic crawler payloads)
- Download cURL `sudo apt install curl` or `sudo apt-get install curl`

# FaQ
**Q:** Automatic or Manual payloads?  
**A:** Automatic payloads give you the ability request to every possible directory and subdomain that the website has, as long as the server doesn't block it. If the server doesn't allow external connections or have a limit requests per IP, than try manual payloads.

**Q:** How to modify the request that I want to send?  
**A:** On Automatic payloads, you can add as much headers as you want, but you **CAN'T** add Cookies, as Gau doesn't support Cookies. However Manual payloads does support this. Simply add [-H "Cookies: season=value_of_cookie"]. Refering to the first payload, if you want to add cookie for authorization, it may look like this:  
`curl -s -I -H "Origin: https://evil.com" -H "Cookies: season=put_cookie_values_here" -X GET 'https://example.com' | if grep 'https://evil.com'; then echo [Potentional CORS Found]; fi`

**Q:** Why my request isn't responding?  
**A:** Some websites have set rules of blacklisting any externial. Check */robots.txt*  to see what the server allows.

**Q:** Why do I get the following error: "error gau not found" when trying to execute automatic payloads?  
**A:** Please take a look the #Requirements to make sure that you download all the requirements.

**Q:** Do I need Gau for manual payloads?  
**A:** No, you only need cURL, which is installed by default in every Linux distro.
