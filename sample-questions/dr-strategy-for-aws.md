## How would you do Disaster Recovery (DR) for my AWS application?
Most common answer: "replicate to another region"

But lets think of this from a customer-facing point of view, replicating to another region is a good answer but have you determined the requirements?

Some requirements that are important to understand are: budget, whether the team support a complex architecture?, and how much downtime/data loss can they afford?

Think about what type of application they are running, how much (if any) data is moving, what are the services (and their replication/failover strategies).

Here are some common DR strategies:

### Backup and Restore
Trade-offs: low cost, low complexity, high RPO/RTO (hours/days)

Accept longer downtime and greater data loss for the lowest possible cost. The RPO depends on the frequency of backups, while the RTO depends on the time it takes to restore from those backups.

Describe the solution: 

### Pilot Light
Trade-offs: medium cost, medium complexity, low RPO/RTO (minutes/hours)

A pre-configured core infrastructure "pilot light" exists in a recovery region, reducing RTO. The main trade-off is the higher operational cost for maintaining the pilot light versus the faster recovery speed.

Describe the solution:

### Warm Standby
Trade-offs: high cost, high complexity, very low RPO/RTO (minutes)

A scaled-down version of the production environment is always running in the recovery region. This minimizes RTO and RPO but significantly increases infrastructure and operational costs.

Describe the solution:

### Multi-site Active/Active
Trade-offs: very high cost, very high complexity, near-zero RPO/RTO

The most expensive and complex strategy, where data and applications are replicated in real-time across multiple active sites. The trade-off is paying the highest price for near-instantaneous recovery with virtually no data loss.

Describe the solution: