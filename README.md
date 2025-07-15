# Try-out task for Vinted Storage SRE
## Choosing a tool

Proposed are 4 potential candidates:
* Shopify Record Store
* StackExchange's DNSControl
* OctoDNS
* HashiCorp Terraform

After prompting ChatGPT to compare them over various criteria (including scalability, speed and ease of implementation), OctoDNS and DNSControl stood out the most. Final decision is to use OctoDNS, since it seems easier to implement given a short timeframe for the task, it having a built-in provider for parsing `/etc/hosts` and adequate performance. 

Shopify solution is rejected - from what I could gather, has a smaller community (thus support) and no built-in Cloudflare provider. I doubt it would be a suitable solutions for large-scale enterprise environments.

Terraform solution is rejected - it could fit in nicely into existing Infrastructure-as-code workflows, as it is already widely used, yet I feel for this tryout task it would be unnecessarily complex.

DNSControl was a close contender for OctoDNS, yet it was also rejected partly due to gut feeling, partly due to the fact that OctoDNS uses YAML, which would be easier to write than DSL.

## Implementing a basic configuration

Having little experience with DaaC tools, this PoC is more of a frankstein baby consisting of OctoDNS basic setup example and ChatGPT configs. For testing out whether the tool really works, I've set up a simple `config/teamvinted.com.yaml` file consisting of a TXT record that returns "Hello, world!"

First of all, let's manually verify connectivity:

```bash
curl -s -H "X-API-Key: $PDNS_API_KEY" http://localhost:8081/api/v1/servers/localhost/zones
``` 
(for PowerDNS, should return list of zones or empty list)

```bash
curl -s -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
     -H "Content-Type: application/json" \
     "https://api.cloudflare.com/client/v4/zones?name=teamvinted.com" | jq .
``` 
(for Cloudflare DNS)

If connectivity is green, then we can deploy OctoDNS with

```bash
octodns-sync --config=config/config.yaml --doit
```

After deployment, let's check PowerDNS with

```bash
dig @localhost -p 8053 teamvinted.com TXT
```

Check Cloudflare DNS with:

```bash
# to do
```

If everything is working as expected, let's create a deployment workflow!

## Deployment workflow (Usage)

DNS records for a specific zone are stored in `dns/config/<zone-name>.yaml`. Thus if one wants to make changes to the records themselves, they must create a separate branch, make changes to the file in that branch, push and create a pull request. Once the pull request is created, GitHub agent automatically performs a dry-run and outputs the diff. From then on, multiple options are available:

1. Deploy DNS configuration to both providers
`/deploy` or `/deploy provider=both`

2. Deploy DNS configuration to specific provider
`/deploy provider=<specific provider>`
`/deploy provider=powerdns`
`/deploy provider=cloudflare`

3. Deploy DNS configuration to Cloudflare DNS and purge cache
`/deploy provider=cloudflare cf_purge=true`
Note: PowerDNS ignores the flags starting with `cf_`


