# 📋 GitOps Multi-Tier Workload Readiness Checklist

## 1. Architecture & State Contract
- [ ] **State Separation**: UI/stateless layers strictly decoupled from data/persistence tiers.
- [ ] **Stateful Topology**: Data workloads use `StatefulSet` + dedicated PVCs (avoiding ephemeral deployment storage for state).
- [ ] **HA / Topology**: Multi-replica master/replica, Sentinel, or managed backing services configured for production.

## 2. Ingress & L7 Traffic Routing
- [ ] **Explicit Path Splitting**: Frontend assets (`/`) cleanly separated from API/mutation routes (`/api/*`, `/r/*`).
- [ ] **No Catch-All Masking**: Root fallback rules (`index.html`) exclude API paths to prevent silent TCP/HTTP black-holes.
- [ ] **Target Group Mapping**: ALB target groups map appropriately to respective service ports.

## 3. Resiliency, Probes & Timeouts
- [ ] **Client-Side Timeouts**: Fetch/AJAX calls enforce hard timeout ceilings (3–5s) with explicit error state rendering.
- [ ] **Deep Health Probes**: Readiness/liveness probes validate actual downstream TCP/socket connectivity (not just UI root HTTP 200).
- [ ] **Disruption Budgets**: PDBs configured for stateful tiers to prevent simultaneous master/replica eviction during node drains.

## 4. GitOps & Sequencing (Argo CD)
- [ ] **Sync Waves / Dependencies**: Data tier services (`ClusterIP`) and storage reconcile *before* UI pods accept traffic.
- [ ] **Immutable Tagging**: Images pinned to specific tags/digests (`latest` prohibited).
- [ ] **Secret Safety**: Sensitive configuration injected via External Secrets/Vault/KSOPS rather than plaintext YAML.

## 5. Round-Trip Definition of Done (DoD)
- [ ] **Mutation Smoke Test**: `POST`/write request succeeds against live ingress endpoint.
- [ ] **Persistence Verification**: Data survives a pod restart/reschedule cycle on the data tier.
- [ ] **Read Verification**: UI or replica read queries reflect committed state changes.
