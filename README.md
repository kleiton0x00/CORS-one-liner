# CORS one liner command exploiter

A one liner Bash command cheatsheet which finds CORS missconfiguration in every possible method. Simply replace https://example.com with the URL you want to target. This will help you scan for CORS vulnerability without the need of an external tool.

## Basic Origin Reflection payload - Crawls the whole endpoints of a website

`gau 'https://example.com' | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $target;fi;done`

## Basic Origin Reflection payload

`curl -s -I -H "Origin: https://evil.com" -X GET 'https://example.com' | if grep 'https://evil.com'; then echo [Potentional CORS Found]; fi`

## Trusted null Origin payload
`curl -s -I -H "Origin: null" -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found];fi`

## Whitelisted null origin value payload
`curl -I -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null';then echo [Potential CORS Found];fi`

# Workflow
If the one-liner bash command displays output, it means that the website is vulnerable to the respective CORS missconfiguration. If no output is displayed while executed, no vulnerability was detected.

# Requirement

- Download Gua from https://github.com/lc/gau (only for the first payload)
- Download cURL `sudo apt install curl` or `sudo apt-get install curl`
