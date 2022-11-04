ipconfig /all | findstr DNS

findstr TEKSTITÄHÄN
Etsii propertyt jossa on DNS nimessä

ipconfig /displaydns
Hakee tallennetut DNS-tiedot


ipconfig /displaydns | clip

clip kopioi tiedot leikepöydälle.





### prosession käsittelyhä
tasklist
taskkill /f /pid TÄHÄNPID

### Tiedonhakua
netsh interface show interface
netsh ethernet show 
netsh interface ip show address
netsh interface ip show address | findstr "IP Address"
netsh interface ip show address | findstr "DNS Servers"

getmac /v
hakee mac-osoitteen

nslookup apaivinen.fi 8.8.8.8
nslookup  -type=mx apaivinen.fi
nslookup -type=txt apaivinen.fi
nslookup -type=ptr apaivinen.fi



### traceroute + traceroute without dns resolution
tracert apaivinen.fi
tracert -d apaivinen

netstat -af
netstat -o
netstat -e -t 5

### Routing table of your computer
route print


### restart to bios
shutdown -r -fw -f -t 0