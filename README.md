# forloops-for-bugbounty

# forloops-for-bugbounty


Brute Force List of subdomains using FFuf


for url in ` cat $filename `; do ffuf -c -w path.txt -u $url/FUZZ -o result.json ; done >> result.txt




