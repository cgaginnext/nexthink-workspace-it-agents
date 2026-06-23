# Monthly Patching Best Practices

## Pre-Deployment

Verify:

- Disk space
- Collector health
- BIOS currency
- Security agent health

---

## During Deployment

Monitor:

- Coverage progression
- Failures
- Reboots pending
- Rollbacks

Avoid:

- Waiting until deployment completion

---

## Deployment Rings

Recommended:

Ring 0:
- IT

Ring 1:
- Champions

Ring 2:
- Business users

Ring 3:
- Full production

---

## DEX Monitoring

Always compare:

Before deployment:
- 7 days baseline

After deployment:
- 7 days observation

Review:

- Crashes
- Freezes
- CPU
- Login duration
- Meeting quality

---

## Rollback Strategy

Immediately investigate when:

- failure rate > 5%
- rollback rate > 1%
- DEX degradation > 10%

---

## Executive Reporting

Track:

- Coverage
- Success rate
- Compliance
- Risk reduction
- DEX impact