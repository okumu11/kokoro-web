<script lang="ts">
  import { 
    Globe, 
    Server, 
    Smartphone,
    Phone,
    MessageSquare,
    Cloud,
    Check,
    Clock,
    AlertCircle,
    Play,
    Pause,
    BarChart3,
    Settings,
    ExternalLink,
    Copy,
    Plus,
    Search,
    Filter,
    MoreVertical,
    Trash2,
    RefreshCw,
    Zap
  } from "lucide-svelte";
  import { toaster } from "$lib/client/toaster";

  // Mock deployment data
  let deployments = $state([
    {
      id: "dep_1",
      name: "Customer Support Bot",
      agent: "Support Assistant",
      platform: "web",
      status: "active",
      region: "us-east-1",
      endpoint: "https://api.voiceforge.io/v1/agents/support-bot",
      requests: 12453,
      latency: 145,
      uptime: 99.9,
      createdAt: new Date("2026-01-15"),
      lastDeployed: new Date("2026-03-10")
    },
    {
      id: "dep_2",
      name: "Sales Helper",
      agent: "Sales Agent",
      platform: "telephony",
      status: "active",
      region: "eu-west-1",
      endpoint: "+1-800-VOICE-AI",
      requests: 8921,
      latency: 178,
      uptime: 99.7,
      createdAt: new Date("2026-02-01"),
      lastDeployed: new Date("2026-03-08")
    },
    {
      id: "dep_3",
      name: "Mobile Assistant",
      agent: "General Assistant",
      platform: "mobile",
      status: "paused",
      region: "ap-southeast-1",
      endpoint: "voiceforge://agents/mobile-assistant",
      requests: 3245,
      latency: 156,
      uptime: 99.5,
      createdAt: new Date("2026-02-15"),
      lastDeployed: new Date("2026-03-01")
    },
    {
      id: "dep_4",
      name: "IoT Controller",
      agent: "Smart Home Agent",
      platform: "iot",
      status: "deploying",
      region: "us-west-2",
      endpoint: "mqtt://iot.voiceforge.io/smart-home",
      requests: 0,
      latency: 0,
      uptime: 0,
      createdAt: new Date("2026-03-14"),
      lastDeployed: new Date("2026-03-14")
    }
  ]);

  let searchQuery = $state("");
  let statusFilter = $state("all");
  let platformFilter = $state("all");
  let selectedDeployment = $state<string | null>(null);

  const platforms = [
    { id: "web", name: "Web", icon: Globe, color: "text-blue-500" },
    { id: "mobile", name: "Mobile", icon: Smartphone, color: "text-green-500" },
    { id: "telephony", name: "Telephony", icon: Phone, color: "text-purple-500" },
    { id: "iot", name: "IoT", icon: Server, color: "text-orange-500" },
    { id: "chat", name: "Chat", icon: MessageSquare, color: "text-pink-500" },
  ];

  const regions = [
    { id: "us-east-1", name: "US East (N. Virginia)" },
    { id: "us-west-2", name: "US West (Oregon)" },
    { id: "eu-west-1", name: "EU (Ireland)" },
    { id: "ap-southeast-1", name: "Asia Pacific (Singapore)" },
  ];

  function getPlatformInfo(platformId: string) {
    return platforms.find(p => p.id === platformId) || platforms[0];
  }

  function getStatusColor(status: string) {
    switch (status) {
      case "active": return "bg-accent text-accent-foreground";
      case "paused": return "bg-yellow-500 text-white";
      case "deploying": return "bg-primary text-primary-foreground";
      case "error": return "bg-red-500 text-white";
      default: return "bg-muted text-muted-foreground";
    }
  }

  function getStatusIcon(status: string) {
    switch (status) {
      case "active": return Check;
      case "paused": return Pause;
      case "deploying": return RefreshCw;
      case "error": return AlertCircle;
      default: return Clock;
    }
  }

  function copyEndpoint(endpoint: string) {
    navigator.clipboard.writeText(endpoint);
    toaster.success("Endpoint copied to clipboard");
  }

  function toggleDeployment(id: string) {
    deployments = deployments.map(d => {
      if (d.id === id) {
        return {
          ...d,
          status: d.status === "active" ? "paused" : "active"
        };
      }
      return d;
    });
    toaster.success("Deployment status updated");
  }

  function deleteDeployment(id: string) {
    deployments = deployments.filter(d => d.id !== id);
    toaster.success("Deployment deleted");
  }

  let filteredDeployments = $derived(
    deployments.filter(d => {
      const matchesSearch = d.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
                           d.agent.toLowerCase().includes(searchQuery.toLowerCase());
      const matchesStatus = statusFilter === "all" || d.status === statusFilter;
      const matchesPlatform = platformFilter === "all" || d.platform === platformFilter;
      return matchesSearch && matchesStatus && matchesPlatform;
    })
  );

  // Aggregate stats
  let stats = $derived({
    total: deployments.length,
    active: deployments.filter(d => d.status === "active").length,
    totalRequests: deployments.reduce((sum, d) => sum + d.requests, 0),
    avgLatency: Math.round(deployments.filter(d => d.latency > 0).reduce((sum, d) => sum + d.latency, 0) / deployments.filter(d => d.latency > 0).length) || 0
  });
</script>

<div class="min-h-screen bg-background px-4 py-8 md:px-8">
  <div class="mx-auto max-w-7xl">
    <!-- Header -->
    <div class="mb-8 flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
      <div>
        <h1 class="text-3xl font-bold">Deployments</h1>
        <p class="mt-1 text-muted">Manage your voice agent deployments across platforms</p>
      </div>
      <button class="flex items-center gap-2 rounded-lg bg-primary px-4 py-2 font-semibold text-primary-foreground transition-all hover:bg-primary/90">
        <Plus class="size-4" />
        New Deployment
      </button>
    </div>

    <!-- Stats Cards -->
    <div class="mb-8 grid gap-4 md:grid-cols-4">
      <div class="rounded-xl border border-border bg-card p-6">
        <div class="flex items-center gap-3">
          <div class="flex size-10 items-center justify-center rounded-lg bg-primary/10">
            <Cloud class="size-5 text-primary" />
          </div>
          <div>
            <div class="text-2xl font-bold">{stats.total}</div>
            <div class="text-sm text-muted">Total Deployments</div>
          </div>
        </div>
      </div>
      
      <div class="rounded-xl border border-border bg-card p-6">
        <div class="flex items-center gap-3">
          <div class="flex size-10 items-center justify-center rounded-lg bg-accent/10">
            <Check class="size-5 text-accent" />
          </div>
          <div>
            <div class="text-2xl font-bold">{stats.active}</div>
            <div class="text-sm text-muted">Active</div>
          </div>
        </div>
      </div>
      
      <div class="rounded-xl border border-border bg-card p-6">
        <div class="flex items-center gap-3">
          <div class="flex size-10 items-center justify-center rounded-lg bg-blue-500/10">
            <BarChart3 class="size-5 text-blue-500" />
          </div>
          <div>
            <div class="text-2xl font-bold">{stats.totalRequests.toLocaleString()}</div>
            <div class="text-sm text-muted">Total Requests</div>
          </div>
        </div>
      </div>
      
      <div class="rounded-xl border border-border bg-card p-6">
        <div class="flex items-center gap-3">
          <div class="flex size-10 items-center justify-center rounded-lg bg-orange-500/10">
            <Zap class="size-5 text-orange-500" />
          </div>
          <div>
            <div class="text-2xl font-bold">{stats.avgLatency}ms</div>
            <div class="text-sm text-muted">Avg Latency</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Filters -->
    <div class="mb-6 flex flex-col gap-4 md:flex-row md:items-center">
      <div class="relative flex-1">
        <Search class="absolute left-3 top-1/2 size-4 -translate-y-1/2 text-muted" />
        <input
          type="text"
          bind:value={searchQuery}
          placeholder="Search deployments..."
          class="w-full rounded-lg border border-border bg-card py-2 pl-10 pr-4 focus:border-primary focus:outline-none"
        />
      </div>
      
      <div class="flex gap-2">
        <select
          bind:value={statusFilter}
          class="rounded-lg border border-border bg-card px-3 py-2 focus:border-primary focus:outline-none"
        >
          <option value="all">All Status</option>
          <option value="active">Active</option>
          <option value="paused">Paused</option>
          <option value="deploying">Deploying</option>
          <option value="error">Error</option>
        </select>
        
        <select
          bind:value={platformFilter}
          class="rounded-lg border border-border bg-card px-3 py-2 focus:border-primary focus:outline-none"
        >
          <option value="all">All Platforms</option>
          {#each platforms as platform}
            <option value={platform.id}>{platform.name}</option>
          {/each}
        </select>
      </div>
    </div>

    <!-- Deployments List -->
    <div class="space-y-4">
      {#each filteredDeployments as deployment}
        {@const platform = getPlatformInfo(deployment.platform)}
        {@const StatusIcon = getStatusIcon(deployment.status)}
        
        <div class="rounded-xl border border-border bg-card transition-all hover:border-primary/30">
          <div class="p-6">
            <div class="flex flex-col gap-4 md:flex-row md:items-start md:justify-between">
              <!-- Left: Main Info -->
              <div class="flex items-start gap-4">
                <div class="flex size-12 items-center justify-center rounded-xl bg-background">
                  <svelte:component this={platform.icon} class="size-6 {platform.color}" />
                </div>
                <div>
                  <div class="flex items-center gap-3">
                    <h3 class="text-lg font-semibold">{deployment.name}</h3>
                    <span class="flex items-center gap-1 rounded-full px-2 py-0.5 text-xs font-medium {getStatusColor(deployment.status)}">
                      <StatusIcon class="size-3 {deployment.status === 'deploying' ? 'animate-spin' : ''}" />
                      {deployment.status.charAt(0).toUpperCase() + deployment.status.slice(1)}
                    </span>
                  </div>
                  <p class="mt-1 text-sm text-muted">
                    Agent: {deployment.agent} | Region: {deployment.region}
                  </p>
                  <div class="mt-2 flex items-center gap-2">
                    <code class="rounded bg-background px-2 py-1 text-xs">{deployment.endpoint}</code>
                    <button
                      onclick={() => copyEndpoint(deployment.endpoint)}
                      class="rounded p-1 text-muted transition-all hover:bg-background hover:text-foreground"
                    >
                      <Copy class="size-3" />
                    </button>
                  </div>
                </div>
              </div>

              <!-- Right: Stats & Actions -->
              <div class="flex items-center gap-6">
                <div class="grid grid-cols-3 gap-6 text-center">
                  <div>
                    <div class="text-lg font-semibold">{deployment.requests.toLocaleString()}</div>
                    <div class="text-xs text-muted">Requests</div>
                  </div>
                  <div>
                    <div class="text-lg font-semibold">{deployment.latency || "-"}ms</div>
                    <div class="text-xs text-muted">Latency</div>
                  </div>
                  <div>
                    <div class="text-lg font-semibold">{deployment.uptime || "-"}%</div>
                    <div class="text-xs text-muted">Uptime</div>
                  </div>
                </div>

                <div class="flex items-center gap-2">
                  <button
                    onclick={() => toggleDeployment(deployment.id)}
                    class="rounded-lg border border-border p-2 transition-all hover:border-primary/50"
                    title={deployment.status === "active" ? "Pause" : "Start"}
                  >
                    {#if deployment.status === "active"}
                      <Pause class="size-4" />
                    {:else}
                      <Play class="size-4" />
                    {/if}
                  </button>
                  <button
                    class="rounded-lg border border-border p-2 transition-all hover:border-primary/50"
                    title="Settings"
                  >
                    <Settings class="size-4" />
                  </button>
                  <button
                    onclick={() => deleteDeployment(deployment.id)}
                    class="rounded-lg border border-border p-2 text-red-500 transition-all hover:bg-red-500/10"
                    title="Delete"
                  >
                    <Trash2 class="size-4" />
                  </button>
                </div>
              </div>
            </div>

            <!-- Timeline -->
            <div class="mt-4 flex items-center gap-4 border-t border-border pt-4 text-xs text-muted">
              <span>Created: {deployment.createdAt.toLocaleDateString()}</span>
              <span>|</span>
              <span>Last deployed: {deployment.lastDeployed.toLocaleDateString()}</span>
            </div>
          </div>
        </div>
      {/each}

      {#if filteredDeployments.length === 0}
        <div class="flex flex-col items-center justify-center rounded-xl border border-dashed border-border py-16">
          <Cloud class="mb-4 size-12 text-muted" />
          <h3 class="text-lg font-semibold">No deployments found</h3>
          <p class="mt-1 text-sm text-muted">
            {searchQuery || statusFilter !== "all" || platformFilter !== "all" 
              ? "Try adjusting your filters" 
              : "Create your first deployment to get started"}
          </p>
          {#if !searchQuery && statusFilter === "all" && platformFilter === "all"}
            <button class="mt-4 flex items-center gap-2 rounded-lg bg-primary px-4 py-2 font-medium text-primary-foreground transition-all hover:bg-primary/90">
              <Plus class="size-4" />
              Create Deployment
            </button>
          {/if}
        </div>
      {/if}
    </div>

    <!-- Platform Guide -->
    <div class="mt-12">
      <h2 class="mb-6 text-xl font-semibold">Supported Platforms</h2>
      <div class="grid gap-4 md:grid-cols-3 lg:grid-cols-5">
        {#each platforms as platform}
          <div class="rounded-xl border border-border bg-card p-4 text-center transition-all hover:border-primary/50">
            <div class="mx-auto mb-3 flex size-12 items-center justify-center rounded-xl bg-background">
              <svelte:component this={platform.icon} class="size-6 {platform.color}" />
            </div>
            <h3 class="font-medium">{platform.name}</h3>
            <p class="mt-1 text-xs text-muted">
              {#if platform.id === "web"}
                REST API & WebSocket
              {:else if platform.id === "mobile"}
                iOS & Android SDKs
              {:else if platform.id === "telephony"}
                SIP & PSTN Integration
              {:else if platform.id === "iot"}
                MQTT & Custom Protocols
              {:else}
                Chat Widget & Webhooks
              {/if}
            </p>
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>
