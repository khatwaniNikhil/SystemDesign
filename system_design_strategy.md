# STRATEGY 
Scope -> Constraints(Traffic & Data) -> Abstract HLD -> Bottlenecks -> Updated HLD

## SCOPE
   ### Functional Requirements
     1. OLAP related supported flows
     2. OLTP related flows 
  ### Non Functional Requirements
     1. Scalabilty: Data size increase and traffic size increase planning
     2. Service level objectives: P95, P99 API percentiles, Availability
     3. Backup: critical components, backup frequency
     4. Security: PII handling verus third party delegation, regulatory requirements, password storage, OWASP
     5. External Integrations
    
## CONSTRAINTS - Traffic & Data (ask interviewer or take some inputs and do math yourself)
     1. Requests per second
     2. Data size to store

## Abstract HLD 
     1. Pass critera - Before proceeding further, do dry run of the HLD so that all the flows as mentioned in the scope
     are supported.
     
## Bottlenecks around meeting constraints
     1. Acknowledge the bottlenecks in the current design and what all constraints are still unmet. Examples of common bottlenecks:
        1. Requests side - Too many concurrent requests to handle
        2. Data side - Manage latency for the current query patterns fast via proper indexing, caching etc.
         
     3. General apporaches are adding LB, caching etc.

## Address bottlenecks using fundamentals of Scalability 
     

# Common bottlenecks
1. Traffic(requests per sec)
2. Data Lookup
3. Data storage

