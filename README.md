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

