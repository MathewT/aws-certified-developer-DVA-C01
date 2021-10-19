# Route53

## DNS Exam Tips
1. ELBs do not have static, pre-defined IPv4 addresses.  You resolve to the ELB server by DNS name, always
1. **Alias Record** is a Route53 specific record that allows the mapping of a naked domain name (zone apex record) 
to an ELB
1. **Exam tip** Alias Records allow DNS queries to resolve to specific AWS resources, CNAME records do not
1. Internet requests to a Route53 CNAME record will be charged by Amazon.  Requests to an Alias Record are not 
charged by Amazon
1. Given the choice, *always* choose an Alias Record over CNAME:  There is no charge for Alias Record requests and 
it allows you to resolve a domain name to an ELB
1. Route53 is a global service, not region specific
1. By default, Route53 allows you to only manage 50 domain names.  For additional domain names, you must contact 
AWS support
1. Supports MX records
1. Supports domain name registration

## Routing Policies and Scenarios
1. **Simple Routing Policy**
  - Default routing policy when you create a new record set
  - Most commonly used when you have a single AWS resource that performs a function for 
  your domain.  Example:  1 web server that responds with content for http://example.com  
  - User DNS request -> Route53 -> ELB -> EC2 or other resource
1. **Weighted Routing Policy**
  - Allows splitting of traffic to different ELBs based on weights (percentages)
  - Example User DNS request -> Route53 -> 20% traffic to us-east-1, 80% of traffic to us-west-1
  - Example A/B web site testing, **likely an exam scenario**
  - Doesn't have to be different regions, can be different ELBs in the same region
1. **Latency**
  - Route your traffic for lowest network latency for the end user (i.e. which region will return the 
  best response time based on network routing)
1. **Failover**
  - Active/Passive DR scenario
  - Example:  Primary site in eu-west-2 but secondary DR site in ap-southwest-2
  - Route53 will monitor the health of your primary site via health check
  - Health check can be an endpoint domain name
1. **Geolocation**
  - Assign and route traffec to AWS resources based on the geolocation of the request.  
  - Geolocation is based on the origination of the DNS request
  - Example:  requests from European locations are routed to EC2 instances optimized for European audiences