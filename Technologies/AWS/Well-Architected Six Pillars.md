---
title: Amazon Web Services (AWS) Well-Architected Framework
publish: true
---
# Well-Architected Framework
## Six Pillars
### Operational Excellence

### Security

### Reliability

### Performance Efficiency

### Cost Optimization

### Sustainability

---

## Design Principles
### Perform Operations as Code
Invoke scripts for your infrastructure management based on event-driven triggers. 
### Make frequent, small, reversible changes
Commit your changes often, don't make major changes to the source code without versioning. Allow small reversible changes to aid in triangulating issues for quick resolution. Always commit with the idea of not impacting the customer. 
### Refine Operations Procedures Frequently
Consistently look for opportunities to improve operational procedures. As the workload evolves, evolve procedures appropriately. Utilize [[game days]] to review and validate that all procedures are effective. Use this time to familiarize the team with these procedures as well. 
### Anticipate Failure
Perform "pre-mortem" exercises to identify potential sources of failure so they can be removed or mitigated. A great starting point would be to review any active [[POAM]]. 
### Learn from Failure
Seriously, don't fear failure and don't let management prevent you from learning in a failure event. That's old school thinking. Share and document what is learned. 

---
