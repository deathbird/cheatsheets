```bash
#Show details of specific pod
kubectl  describe pod <pod name> -n <namespace-name>

# View logs for specific pod
kubectl  logs <pod name> -n <namespace-name>

# Why a pod is crashing
kubectl get events --sort-by=.metadata.creationTimestamp

```

# CPU and Memory Limits in Kubernetes: Explained

Let me explain how the CPU and memory values in Kubernetes resource specifications relate to real-world hardware resources:

## CPU Resources

In Kubernetes, CPU resources are specified in units called "millicores" or "milliCPUs" (m):

- **1000m = 1 CPU core** (equivalent to one hyperthread on a hyperthreaded processor)
- **100m = 0.1 CPU cores** or 10% of a CPU core
- **50m = 0.05 CPU cores** or 5% of a CPU core

For the `ai-tasks-and-fillup` worker:
- `requestsCpu: 50m` means it requests 0.05 cores (5% of a single CPU core)
- `limitsCpu: 200m` means it can use up to 0.2 cores (20% of a single CPU core)

On a real machine with, for example, 32 CPU cores, you could theoretically run:
- 640 pods at the requested level (32 ÷ 0.05)
- 160 pods at the maximum limit (32 ÷ 0.2)

## Memory Resources

Kubernetes uses binary prefixes for memory:

- **Ki (Kibibyte)** = 1,024 bytes
- **Mi (Mebibyte)** = 1,024 Ki = 1,048,576 bytes
- **Gi (Gibibyte)** = 1,024 Mi = 1,073,741,824 bytes

For the `ai-tasks-and-fillup` worker:
- `requestsMemory: 300Mi` means it requests 300 Mebibytes ≈ 314.57 MB
- `limitsMemory: 400Mi` means it can use up to 400 Mebibytes ≈ 419.43 MB

On a machine with 64 GB RAM:
- You could theoretically run about 213 pods at the requested level (64 GB ÷ 300 Mi)
- Or about 160 pods at the maximum limit (64 GB ÷ 400 Mi)

## Practical Implications

1. **Requests vs. Limits**:
   - **Requests** are what Kubernetes uses for scheduling decisions. The scheduler ensures a node has at least the requested amount available.
   - **Limits** are enforced by the container runtime. If a container exceeds its CPU limit, it gets throttled. If it exceeds its memory limit, it may be terminated (OOM killed).

2. **CPU Throttling**: When a container hits its CPU limit, it doesn't crash but gets throttled, potentially causing performance degradation.

3. **Memory Enforcement**: Memory limits are a hard boundary. If a process tries to allocate more memory than the limit, the container will be terminated with an Out-Of-Memory (OOM) error.

4. **Real-world Performance**: For CPU-bound workloads, setting appropriate CPU limits is crucial. For the `ai-tasks-and-fillup` worker, the 200m limit means it can't use more than 20% of a CPU core even if the node has idle CPU capacity, which can impact performance if the worker needs more processing power.

The relatively low resource allocation for the `ai-tasks-and-fillup` worker (50m CPU request, 300Mi memory request) suggests this is either a lightweight workload or the architecture is designed to scale horizontally (adding more pods) rather than vertically (adding more resources per pod).


# Infrastructure Options and Horizontal Pod Autoscaling (HPA) in Kubernetes

## Infrastructure Options

### Spot Instances

```yaml
spot: true
```

**Spot instances** are a cost-saving option offered by cloud providers:

1. **Cost Reduction**: Spot instances can reduce costs by 60-90% compared to on-demand instances.

2. **How They Work**: Cloud providers sell unused capacity at discounted rates, but these instances can be reclaimed with minimal notice when the provider needs the capacity back.

3. **Trade-offs**:
   - **Pros**: Significantly lower cost
   - **Cons**: Potential for interruption, which means your workload must be resilient to node failures

4. **Use Cases**: Ideal for:
   - Batch processing jobs
   - Stateless applications
   - Fault-tolerant workloads
   - Non-critical services

For the `ai-tasks-and-fillup` worker, using spot instances suggests this is a non-critical workload that can handle occasional disruptions without affecting the overall system reliability.

### Alternatives to Spot Instances

In Kubernetes deployments on cloud providers, several instance types exist beyond spot instances:

1. **On-Demand Instances**:
   - Pay standard rates with no commitment
   - Full availability guarantees with no interruption risk
   - Highest cost but maximum reliability
   - Used for critical production workloads where reliability is crucial

2. **Reserved Instances**:
   - Pre-purchased capacity with 1-3 year commitments
   - Discounted rates (typically 30-60% cheaper than on-demand)
   - Guaranteed availability
   - Used for stable, predictable workloads with known resource requirements

3. **Burstable Instances** (such as AWS T-series):
   - Lower baseline performance with ability to "burst" when needed
   - Cost-effective for workloads with variable performance needs
   - Limited burst capacity based on CPU credits
   - Good for workloads with periodic processing needs

4. **Dedicated Hosts/Instances**:
   - Physical servers dedicated to a single customer
   - Used for compliance, licensing, or performance isolation requirements
   - Highest cost with maximum isolation
   - Suitable for regulated workloads or specialized software licensing



### ARM64 Architecture

```yaml
arch: arm64
```

**ARM64** refers to the 64-bit ARM architecture:

1. **Cost Efficiency**: ARM processors typically consume less power and are cheaper than traditional x86 processors.

2. **Cloud Support**: Major cloud providers offer ARM-based instances (e.g., AWS Graviton, Azure Ampere Altra).

3. **Performance Characteristics**:
   - Generally better performance-per-watt
   - Can be more cost-effective for specific workloads
   - May have different performance characteristics than x86

4. **Considerations**:
   - Application compatibility (though most modern software works on ARM)
   - Potentially different performance profiles for certain workloads

For the `ai-tasks-and-fillup` worker, using ARM64 suggests optimizing for cost efficiency while ensuring the workload is compatible with ARM architecture.

## Horizontal Pod Autoscaling (HPA)

```yaml
- name: ai-tasks-and-fillup
  minReplicas: 2
  maxReplicas: 5
  metricTargetValue: 200
  adapterMetricName: txc-prod-ai-tasks-and-fillups-worker
  type: worker
```

**HPA** automatically scales the number of pods based on observed metrics:

1. **Dynamic Scaling**: HPA adjusts the number of replicas to match current demand:
   - Scales up when load increases
   - Scales down when resources are underutilized

2. **Configuration Parameters**:
   - `minReplicas: 2` - Minimum number of pods, even during low demand
   - `maxReplicas: 5` - Maximum number of pods, even during high demand
   - `metricTargetValue: 200` - The target value for the metric that triggers scaling
   - `adapterMetricName: txc-prod-ai-tasks-and-fillups-worker` - Custom metric used to determine scaling

3. **Custom Metrics**: Instead of standard CPU/memory metrics, this HPA uses a custom metric specific to the application. This could be:
   - Queue depth
   - Requests per second
   - Processing latency
   - Any application-specific metric

4. **Scaling Logic**:
   - If the metric value exceeds 200, HPA will add pods (up to `maxReplicas`)
   - If the metric value falls below 200, HPA will remove pods (down to `minReplicas`)

For the `ai-tasks-and-fillup` worker, the HPA configuration ensures:
- There are always at least 2 pods for redundancy and minimum capacity
- The system can scale up to 5 pods during high demand
- Scaling decisions are based on a custom metric that likely reflects the AI task queue length or processing load

This configuration demonstrates a balanced approach to resource allocation - starting small to save costs but able to automatically scale to meet demand when needed.

When using custom metrics with HPA, the scaling behavior follows a proportional control algorithm:

### How It Determines When to Add Pods

1. **Calculation Formula**:
   ```
   desiredReplicas = ceil[currentReplicas * (currentMetricValue / targetMetricValue)]
   ```

2. **Scaling Example** for `ai-tasks-and-fillup`:
   - Target value: 200
   - Current replicas: 2 (starting point)

   Scenarios:
   - If metric = 200: stays at 2 pods (current is ideal)
   - If metric = 400: scales to 4 pods (2 × 400/200 = 4)
   - If metric = 300: scales to 3 pods (2 × 300/200 = 3)
   - If metric = 500: scales to 5 pods (2 × 500/200 = 5) - capped by maxReplicas
   - If metric = 600: remains at 5 pods (capped by maxReplicas)

3. **Progressive Scaling**:
   After each scaling action, there's a stabilization period (default 5 minutes) before another scaling decision. This prevents rapid fluctuations.

4. **Scale-Down Behavior**:
   Scale-down is more conservative to prevent premature resource reduction:
   - If metric = 100: scales to 1 pod (2 × 100/200 = 1)
   - However, minReplicas: 2 means it won't go below 2 pods

### Tuning Considerations

1. **Responsiveness vs. Stability**:
   - Lower values for targetMetricValue = more aggressive scaling
   - Higher values = more conservative scaling

2. **Event-based vs. steady-state workloads**:
   For bursty workloads, you might need to:
   - Reduce the stabilization window
   - Use a target value that triggers scaling earlier
   - Consider predictive scaling if available

3. **Resource Efficiency**:
   The proportional algorithm helps ensure you're not overprovisioning resources, as the number of pods should be roughly proportional to the workload.

The HPA configuration in this case tries to maintain approximately 200 units of work per pod, adding or removing pods as necessary to maintain this ratio, while staying within the 2-5 pod range.

