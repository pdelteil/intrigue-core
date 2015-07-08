### Environment and external dependencies

The following are presumed available and configured in your environment
 - redis
 - sudo
 - nmap
 - zmap
 - masscan
 - API keys (copy config/config.yml.default -> config/config.yml)

### To start the API and background task processing:

Make sure you have redis installed and running.

```
$ foreman start
```

### Web Interface

To use the (admittedly minimal) web interface, browse to http://localhost:5000

### API usage via curl:

Request the task type, specify an entity, and the appropriate options:
````
$ curl -s -X POST -H "Content-Type: application/json" -d '{ "task": "example", "entity": { "type": "IpAddress", "attributes": { "name": "8.8.8.8" } }, "options": {} }' http://localhost:5000/v1/task_runs/
````

### API usage via core-cli:

A command line utility has been added for convenience, core-cli.

List all available tasks:
```
$ bundle exec ./core-cli.rb list
```

Start a task:
```
$ bundle exec ./core-cli.rb start dns_lookup_forward DnsRecord#intrigue.io
```

Start a task with options:
```
$ bundle exec ./core-cli.rb start dns_brute_sub DnsRecord#intrigue.io resolver=8.8.8.8#brute_list=1,2,3,4,www#use_permutations=true
[+] Starting task
[+] Task complete!
[+] Start Results
  DnsRecord#www.intrigue.io
  IpAddress#192.0.78.13
[ ] End Results
[+] Task Log:
[ ] : Got allowed option: resolver
[ ] : Allowed option: {:name=>"resolver", :type=>"String", :regex=>"ip_address", :default=>"8.8.8.8"}
[ ] : Regex should match an IP Address
[ ] : No need to convert resolver to a string
[+] : Allowed user_option! {"name"=>"resolver", "value"=>"8.8.8.8"}
[ ] : Got allowed option: brute_list
[ ] : Allowed option: {:name=>"brute_list", :type=>"String", :regex=>"alpha_numeric_list", :default=>["mx", "mx1", "mx2", "www", "ww2", "ns1", "ns2", "ns3", "test", "mail", "owa", "vpn", "admin", "intranet", "gateway", "secure", "admin", "service", "tools", "doc", "docs", "network", "help", "en", "sharepoint", "portal", "public", "private", "pub", "zeus", "mickey", "time", "web", "it", "my", "photos", "safe", "download", "dl", "search", "staging"]}
[ ] : Regex should match an alpha-numeric list
[ ] : No need to convert brute_list to a string
[+] : Allowed user_option! {"name"=>"brute_list", "value"=>"1,2,3,4,www"}
[ ] : Got allowed option: use_permutations
[ ] : Allowed option: {:name=>"use_permutations", :type=>"Boolean", :regex=>"boolean", :default=>true}
[ ] : Regex should match a boolean
[+] : Allowed user_option! {"name"=>"use_permutations", "value"=>true}
[ ] : user_options: [{"resolver"=>"8.8.8.8"}, {"brute_list"=>"1,2,3,4,www"}, {"use_permutations"=>true}]
[ ] : Task: dns_brute_sub
[ ] : Id: fddc7313-52f6-4d5a-9aad-fd39b0428ca5
[ ] : Task entity: {"type"=>"DnsRecord", "attributes"=>{"name"=>"intrigue.io"}}
[ ] : Task options: [{"resolver"=>"8.8.8.8"}, {"brute_list"=>"1,2,3,4,www"}, {"use_permutations"=>true}]
[ ] : Option configured: resolver=8.8.8.8
[ ] : Option configured: use_file=false
[ ] : Option configured: brute_file=dns_sub.list
[ ] : Option configured: use_mashed_domains=false
[ ] : Option configured: brute_list=1,2,3,4,www
[ ] : Option configured: use_permutations=true
[ ] : Using provided brute list
[+] : Using subdomain list: ["1", "2", "3", "4", "www"]
[+] : Looks like no wildcard dns. Moving on.
[-] : Hit exception: no address for 1.intrigue.io
[-] : Hit exception: no address for 2.intrigue.io
[-] : Hit exception: no address for 3.intrigue.io
[-] : Hit exception: no address for 4.intrigue.io
[+] : Resolved Address 192.0.78.13 for www.intrigue.io
[+] : Creating entity: DnsRecord, {:name=>"www.intrigue.io"}
[+] : Creating entity: IpAddress, {:name=>"192.0.78.13"}
[ ] : Adding permutations: www1, www2
[-] : Hit exception: no address for www1.intrigue.io
[-] : Hit exception: no address for www2.intrigue.io
[+] : Ship it!
[ ] : Sending to Webhook: http://localhost:5000/v1/task_runs/fddc7313-52f6-4d5a-9aad-fd39b0428ca5
```

Check for a list of subdomains on intrigue.io:
```
$ bundle exec ./core-cli.rb start dns_brute_sub DnsRecord#intrigue.io resolver=8.8.8.8#brute_list=a,b,c,proxy,test,www
```

Check the Alexa top 1000 domains for the existence of security headers:
```
$ for x in `cat data/domains.txt | head -n 1000`; do bundle exec ./core-cli.rb start dns_brute_sub DnsRecord#$x;done
```
