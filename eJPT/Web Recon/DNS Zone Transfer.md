
DNS Zone Transfer is a process used to replicate DNS databases across DNS Servers. 

- The main DNS Server ( primary or master) has the full list of names and their matching IPs
- The BackUp DNS Server ( secondary or slave) needs a copy of that list to function.




### What is a Zone File?

A zone file is a text file that contains all the DNS records for a specific domain (called a DNS Zone). It tells the DNS server how to respond when someone tries to visit your website.


|**Record Type**|**What It Does**|**Example**|
|---|---|---|
|**A**|Maps a domain to an IP address|example.com → 192.0.2.1|
|**MX**|Mail server for the domain|mail.example.com|
|**CNAME**|Alias for another name|www.example.com → example.com|
|**NS**|Nameservers for the domain|ns1.example.com|
|**SOA**|Start of Authority (zone settings)|Info about the zone file itself|
|**TXT**|Text info (like SPF records)|Used for email verification|


### Why Zone Transfer?

Zone Transfer is done to share and sync the list of domain names between DNS servers



### Two Ways Of Zone Transfer

1. **AXFR** (Full Transfer): Copy the entire List of records
2. **IXFR** (Incremental Transfer): Only copy the new or changed entries




### How Zone Transfer Works?

1. The Secondary Server queries the primary for the **SOA** (Start Of Authority) record
2. It Compares the serial number in the SOA with its own
3. If the serial number is higher ( indicating changes), the secondary requests a zone transfer
4. The Primary server sends the full zone data (AXFR) or just the changes (IXFR)
5. The secondary server updates its zone files




### Why check for Zone Transfer? (As hackers)


Zone Transfer is a crucial step in reconnaissance. It can uncover sensitive information about the target domain, such as hidden subdomains, DNS zone records, mail servers, name servers. 


#### To Check for DNS Zone Transfer:

```bash
dig axfr @domain
```


