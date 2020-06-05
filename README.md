# CORS one liner command exploiter

A one liner Bash command cheatsheet which finds CORS missconfiguration in every possible method. Simply replace https://example.com with the URL you want to target. This will help you scan for CORS vulnerability without the need of an external tool.

## Basic Origin Reflection payload

`gau 'https://ac341f051f95be898074dc8a00b90040.web-security-academy.net/accountDetails' | while read url;do target=$(curl -s -I -H "Cookie: session=R82gI39R0yguU4hnylUEXh70xpLjacnl" -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $target;fi;done'`

## Basic Origin Reflection payload

`curl -s -I -H "Origin: https://evil.com" -X GET 'https://example.com' | if grep 'https://evil.com'; then echo [Potentional CORS Found]; fi`

## Trusted null Origin payload
`curl -s -I -H "Origin: null" -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found];fi`

## Whitelisted null origin value payload
`curl -I -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null';then echo [Potential CORS Found];fi`


## Requirement

- Download Gua from https://github.com/lc/gau (only for the first payload)
- Download cURL `sudo apt install curl` or `sudo apt-get install curl`
