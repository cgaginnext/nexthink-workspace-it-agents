Decision framework and documentation template for suppressing or adjusting noisy alerts
# Alert Suppression & Threshold Guidance

## Suppression documentation template
Alert name      : <name>
Suppression date: <date>
Suppressed by   : <operator name>
Reason          : <explanation>
Review date     : <90 days from suppression>
Devices/scope   : <device list or filter>

## Threshold adjustment guidelines
Before changing a threshold, answer:
- What is the current threshold?
- What is the observed 30-day median for this metric?
- What would the new threshold be, and does it still catch real issues?
- Has the application owner / security team approved the change?

## Recommended review cadence
Weekly   : Review alerts that fired > 10 times with no outcome action
Monthly  : Review all active suppressions — confirm still valid
Quarterly: Full alert library review — remove stale, adjust thresholds