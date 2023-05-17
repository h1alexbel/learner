## Kafka Log Cleanup Policies

```properties
log.cleanup.policy=delete
```
Default for all user topics.

Delete based on age of data (default is **1 week**).
Delete based on max size of log (default is -1 -> **infinite**).

```properties
log.retention.hours=240
```
Number of hours to keep data for (default is 168 - one week).

```properties
log.retention.bytes=
```
Max size in bytes for each partition (default is -1 -> **infinite**).

```properties
log.cleanup.policy=compact
```
Default for topic__consumer_offsets.

Delete based on keys of your messages.
Will delete old duplicate keys after the active segment is committed.
Infinite time and space retention.

```properties
log.cleaner.backoff.ms=1000
```
Default is 15 seconds, so the cleaner checks for work every 15 seconds.

If all your In Sync Replicas go offline (but you still have out of sync replicas up),
you have the following options: (since leader election can be done only with ISRs)
1. Wait for an ISR to come back online (default).
2. Enable `unclean.leader.election.enable=true` and start producing to non ISR partitions.

```properties
unclean.leader.election.enable=true
```

Thus, you can improve availability, but you can lose data.
Overall, this is a very dangerous setting.
