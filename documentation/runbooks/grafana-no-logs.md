Created: 2026-05-25

When there are no logs seen in Grafana, then check if value of `tenant_id` (e.g. in Fluent Bit) put as `X-Scope-OrgID` custom header in Grafana **is the same across all the configurations**. Otherwise Grafana dashboard doesn't see any logs.
