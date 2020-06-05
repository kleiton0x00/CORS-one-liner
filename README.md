# CORS one liner command

A one liner Bash command which finds CORS in every possible endpoint. Simply replace https://example.com with the URL you want. This will help you scan for CORS vulnerability without the need of an external tool.

## Payload

`gau 'https://example.com' | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url | grep -o '(Access-Control-Allow-Origin: https://evil.com|Access-Control-Allow-Origin: null|Access-Control-Allow-Credentials: true)'); echo -e "Trying endpoint:\e[1;31m $url\e[0m" "$target"; done | sed 's/evil.com/[Potentional CORS Found]/g'`

## Requirement

Be sure to download Gua from https://github.com/lc/gau.

## Workflow

First all the website's endpoints are crawled by Gua. For each endpoint, we add an additional header (Origin: https://evil.com) to the request and we send it. 
For each request, *IF* only one of these 3 Headers is found on the request's response, the endpoint is vulnerable:
> Access-Control-Allow-Origin: https://evil.com 

> Access-Control-Allow-Origin: null

> Access-Control-Allow-Credentials: true
