# CORS one liner command

A one liner Bash command which finds CORS missconfiguration in every possible endpoint. Simply replace https://example.com with the URL you want. This will help you scan for CORS vulnerability without the need of an external tool.

## Basic Origin Reflection payload (non-authenticated) MAYBE DELETE THIS

`gau 'https://example.com' | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url | grep -o '(https://evil.com)'); echo -e "Trying endpoint:\e[1;31m $url\e[0m" "$target"; done | sed 's/evil.com/[Potentional CORS Found]/g'`

## Basic Origin Reflection payload (non-authenticated)

`curl -s -I -H "Origin: https://evil.com" -X GET 'https://example.com' | if grep 'https://evil.com'; then echo [Potentional CORS Found]; fi`

## Trusted null Origin payload
`curl -s -I -H "Origin: null" -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null'; then echo [Potentional CORS Found];fi`

## Whitelisted null origin value payload
`curl -I -X GET 'https://example.com' | if grep 'Access-Control-Allow-Origin: null';then echo [Potential CORS Found];fi`

## Requirement MAYBE DELETE THIS

Download Gua from https://github.com/lc/gau (only for the first payload)

## Workflow

First, all the website's endpoints are crawled by Gua. For each endpoint, we add an additional header (Origin: https://evil.com) to the request and we fire it up. 
For each request, **IF** only one of these 3 Headers is found on the request's response, the endpoint is vulnerable to CORS missconfiguration:
> Access-Control-Allow-Origin: https://evil.com 
