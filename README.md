# CORS one liner command exploiter

A one liner Bash command cheatsheet which finds CORS missconfiguration in every possible method. Simply replace https://example.com with the URL you want to target. This will help you scan for CORS vulnerability without the need of an external tool.

## 1 Basic Origin Reflection payload - Automatically send request to every crawled endpoint of the website

`gau -subs 'https://example.com' | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $target;fi;done`

## 1.2 Basic Origin Reflection payload - Focus in only one endpoint

`curl -s -I -H "Origin: https://evil.com" -X GET 'https://example.com' | if grep 'https://evil.com'; then echo [Potentional CORS Found]; fi`

## 2 Trusted null Origin payload - Automatically send request to every crawled endpoint of the website
`gau -subs 'https://example.com' | while read url;do target=$(curl -s -I -H "Origin: null" -X GET $url) | if grep 'Access-Control-Allow-Origin: null'; then [Potentional CORS Found]echo $target;fi;done`

## 2.2 Trusted null Origin payload - Focus in only one endpoint
`curl -s -I -H "Origin: null" -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found];fi`

## 3 Whitelisted null origin value payload - Automatically send request to every crawled endpoint of the website
`gau -subs 'https://example.com' | while read url;do target=$(curl -s -I -X GET $url) | if grep 'Access-Control-Allow-Origin: null'; then [Potentional CORS Found]echo $target;fi;done`

## 3.2 Whitelisted null origin value payload - Focus in only one endpoint
`curl -I -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null';then echo [Potential CORS Found];fi`

# Workflow
If the one-liner bash command displays output, it means that the website is vulnerable to the respective CORS missconfiguration. If no output is displayed while executed, no vulnerability was detected.

# Requirement

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
