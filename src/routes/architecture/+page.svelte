<script lang="ts">
  import { 
    Mic, 
    Brain, 
    MessageSquare,
    Zap,
    Globe,
    Server,
    Database,
    Shield,
    Code,
    ArrowRight,
    Layers,
    Cpu,
    Radio,
    Volume2,
    GitBranch,
    Box,
    Cloud,
    Lock,
    Activity,
    ChevronRight
  } from "lucide-svelte";

  const architectureLayers = [
    {
      name: "Input Layer",
      description: "Capture and process audio input from various sources",
      color: "from-blue-500 to-blue-600",
      components: [
        { name: "Speech Recognition (ASR)", description: "Real-time audio transcription with noise cancellation", icon: Mic },
        { name: "Audio Preprocessing", description: "Signal processing, VAD, and format normalization", icon: Radio },
        { name: "Input Adapters", description: "REST API, WebSocket, gRPC, and SDK integrations", icon: Code }
      ]
    },
    {
      name: "Processing Layer",
      description: "Understand intent and generate intelligent responses",
      color: "from-accent to-green-600",
      components: [
        { name: "NLU Engine", description: "Intent classification, entity extraction, and sentiment analysis", icon: Brain },
        { name: "Dialog Manager", description: "Context tracking, state management, and conversation flow", icon: MessageSquare },
        { name: "Response Generator", description: "LLM-powered response generation with templates", icon: Zap }
      ]
    },
    {
      name: "Output Layer",
      description: "Generate natural voice output and deliver to users",
      color: "from-primary to-blue-600",
      components: [
        { name: "TTS Engine (Kokoro)", description: "High-quality neural voice synthesis with emotions", icon: Volume2 },
        { name: "Audio Postprocessing", description: "Prosody adjustment, SSML support, and format conversion", icon: Activity },
        { name: "Output Adapters", description: "Multi-channel delivery to web, mobile, telephony, IoT", icon: Globe }
      ]
    }
  ];

  const systemComponents = [
    {
      category: "Core Services",
      items: [
        { name: "Agent Orchestrator", description: "Manages agent lifecycle and routes requests", icon: Layers },
        { name: "Session Manager", description: "Handles conversation state and context", icon: GitBranch },
        { name: "Model Registry", description: "Stores and versions trained NLU models", icon: Box }
      ]
    },
    {
      category: "Infrastructure",
      items: [
        { name: "Load Balancer", description: "Distributes traffic across service instances", icon: Server },
        { name: "Message Queue", description: "Async processing with Redis/RabbitMQ", icon: Radio },
        { name: "Cache Layer", description: "Response caching and model preloading", icon: Database }
      ]
    },
    {
      category: "Security & Compliance",
      items: [
        { name: "Auth Gateway", description: "OAuth 2.0, API keys, and JWT validation", icon: Lock },
        { name: "Encryption", description: "TLS 1.3, at-rest encryption, key management", icon: Shield },
        { name: "Audit Logging", description: "Complete request/response logging for compliance", icon: Activity }
      ]
    }
  ];

  const dataFlow = [
    { step: 1, title: "Audio Capture", description: "User speaks into device microphone or sends audio via API" },
    { step: 2, title: "Transcription", description: "ASR engine converts speech to text in real-time" },
    { step: 3, title: "Intent Recognition", description: "NLU engine identifies user intent and extracts entities" },
    { step: 4, title: "Context Update", description: "Dialog manager updates conversation state" },
    { step: 5, title: "Response Generation", description: "LLM generates contextual response text" },
    { step: 6, title: "Voice Synthesis", description: "Kokoro TTS converts response to natural speech" },
    { step: 7, title: "Delivery", description: "Audio streamed back to user device" }
  ];

  const techStack = [
    { category: "Speech", items: ["Kokoro TTS", "Whisper ASR", "WebRTC", "Opus Codec"] },
    { category: "AI/ML", items: ["ONNX Runtime", "Transformers", "spaCy", "OpenAI API"] },
    { category: "Backend", items: ["SvelteKit", "Node.js", "Python", "Redis"] },
    { category: "Infrastructure", items: ["Vercel", "Docker", "Kubernetes", "CloudFlare"] }
  ];

  const scalabilityFeatures = [
    { name: "Horizontal Scaling", description: "Auto-scale worker pods based on request volume", metric: "Up to 10K concurrent" },
    { name: "Geographic Distribution", description: "Edge deployment for low-latency global access", metric: "< 50ms edge latency" },
    { name: "Model Optimization", description: "Quantized models for efficient inference", metric: "80% smaller models" },
    { name: "Streaming Pipeline", description: "Chunked audio processing for real-time response", metric: "< 200ms first byte" }
  ];
</script>

<div class="min-h-screen bg-background px-4 py-8 md:px-8">
  <div class="mx-auto max-w-6xl">
    <!-- Header -->
    <div class="mb-12 text-center">
      <h1 class="text-4xl font-bold md:text-5xl">Platform Architecture</h1>
      <p class="mx-auto mt-4 max-w-2xl text-lg text-muted">
        A comprehensive overview of VoiceForge's modular, scalable architecture for building and deploying voice agents.
      </p>
    </div>

    <!-- Architecture Overview Diagram -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">System Overview</h2>
      
      <div class="rounded-2xl border border-border bg-card p-6 md:p-8">
        <div class="grid gap-6 md:grid-cols-3">
          {#each architectureLayers as layer, i}
            <div class="relative">
              <div class="mb-4 rounded-lg bg-gradient-to-r {layer.color} p-4 text-white">
                <h3 class="text-lg font-semibold">{layer.name}</h3>
                <p class="mt-1 text-sm opacity-90">{layer.description}</p>
              </div>
              
              <div class="space-y-3">
                {#each layer.components as component}
                  <div class="rounded-lg border border-border bg-background p-4">
                    <div class="flex items-start gap-3">
                      <div class="flex size-8 shrink-0 items-center justify-center rounded-lg bg-card">
                        <component.icon class="size-4 text-primary" />
                      </div>
                      <div>
                        <div class="font-medium">{component.name}</div>
                        <div class="text-xs text-muted">{component.description}</div>
                      </div>
                    </div>
                  </div>
                {/each}
              </div>

              {#if i < architectureLayers.length - 1}
                <div class="absolute -right-3 top-1/2 z-10 hidden md:block">
                  <ChevronRight class="size-6 text-muted" />
                </div>
              {/if}
            </div>
          {/each}
        </div>
      </div>
    </section>

    <!-- Data Flow -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">Request Flow</h2>
      
      <div class="rounded-2xl border border-border bg-card p-6 md:p-8">
        <div class="relative">
          <!-- Flow line -->
          <div class="absolute left-6 top-0 h-full w-0.5 bg-gradient-to-b from-primary via-accent to-primary md:left-1/2 md:-translate-x-1/2"></div>
          
          <div class="space-y-8">
            {#each dataFlow as item, i}
              <div class="relative flex items-start gap-4 md:gap-8 {i % 2 === 0 ? 'md:flex-row' : 'md:flex-row-reverse'}">
                <!-- Step indicator -->
                <div class="z-10 flex size-12 shrink-0 items-center justify-center rounded-full border-4 border-background bg-gradient-to-br from-primary to-accent text-white md:absolute md:left-1/2 md:-translate-x-1/2">
                  <span class="font-bold">{item.step}</span>
                </div>
                
                <!-- Content -->
                <div class="flex-1 rounded-lg border border-border bg-background p-4 md:w-5/12 {i % 2 === 0 ? 'md:mr-auto md:pr-16' : 'md:ml-auto md:pl-16'}">
                  <h4 class="font-semibold">{item.title}</h4>
                  <p class="mt-1 text-sm text-muted">{item.description}</p>
                </div>
              </div>
            {/each}
          </div>
        </div>
      </div>
    </section>

    <!-- System Components -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">System Components</h2>
      
      <div class="grid gap-6 md:grid-cols-3">
        {#each systemComponents as category}
          <div class="rounded-xl border border-border bg-card p-6">
            <h3 class="mb-4 text-lg font-semibold">{category.category}</h3>
            <div class="space-y-4">
              {#each category.items as item}
                <div class="flex items-start gap-3">
                  <div class="flex size-10 shrink-0 items-center justify-center rounded-lg bg-background">
                    <item.icon class="size-5 text-primary" />
                  </div>
                  <div>
                    <div class="font-medium">{item.name}</div>
                    <div class="text-sm text-muted">{item.description}</div>
                  </div>
                </div>
              {/each}
            </div>
          </div>
        {/each}
      </div>
    </section>

    <!-- Tech Stack -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">Technology Stack</h2>
      
      <div class="grid gap-4 md:grid-cols-4">
        {#each techStack as stack}
          <div class="rounded-xl border border-border bg-card p-6">
            <h3 class="mb-4 text-sm font-semibold uppercase tracking-wider text-muted">{stack.category}</h3>
            <div class="flex flex-wrap gap-2">
              {#each stack.items as item}
                <span class="rounded-full bg-background px-3 py-1 text-sm">{item}</span>
              {/each}
            </div>
          </div>
        {/each}
      </div>
    </section>

    <!-- Scalability -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">Scalability & Performance</h2>
      
      <div class="grid gap-4 md:grid-cols-2">
        {#each scalabilityFeatures as feature}
          <div class="flex items-start gap-4 rounded-xl border border-border bg-card p-6">
            <div class="flex size-12 shrink-0 items-center justify-center rounded-xl bg-primary/10">
              <Zap class="size-6 text-primary" />
            </div>
            <div class="flex-1">
              <div class="flex items-start justify-between">
                <h3 class="font-semibold">{feature.name}</h3>
                <span class="rounded-full bg-accent/10 px-2 py-0.5 text-xs font-medium text-accent">{feature.metric}</span>
              </div>
              <p class="mt-1 text-sm text-muted">{feature.description}</p>
            </div>
          </div>
        {/each}
      </div>
    </section>

    <!-- Integration APIs -->
    <section class="mb-16">
      <h2 class="mb-8 text-2xl font-bold">Integration APIs</h2>
      
      <div class="rounded-2xl border border-border bg-card p-6 md:p-8">
        <div class="grid gap-8 md:grid-cols-2">
          <div>
            <h3 class="mb-4 text-lg font-semibold">REST API</h3>
            <div class="rounded-lg bg-background p-4">
              <pre class="text-sm"><code class="text-muted"># Generate speech from text
POST /api/v1/audio/speech
{'{'}
  "input": "Hello, world!",
  "voice": "af_heart",
  "model": "model",
  "speed": 1.0
{'}'}</code></pre>
            </div>
          </div>
          
          <div>
            <h3 class="mb-4 text-lg font-semibold">WebSocket API</h3>
            <div class="rounded-lg bg-background p-4">
              <pre class="text-sm"><code class="text-muted"># Real-time conversation
WS /api/v1/conversation
{'{'}
  "type": "audio",
  "data": "&lt;base64_audio&gt;",
  "session_id": "sess_123"
{'}'}</code></pre>
            </div>
          </div>
        </div>

        <div class="mt-6 flex items-center justify-center gap-4">
          <a href="/api/v1/index.html" target="_blank" class="flex items-center gap-2 rounded-lg bg-primary px-4 py-2 font-medium text-primary-foreground transition-all hover:bg-primary/90">
            <Code class="size-4" />
            Full API Documentation
          </a>
        </div>
      </div>
    </section>

    <!-- Extensibility -->
    <section>
      <h2 class="mb-8 text-2xl font-bold">Extensibility</h2>
      
      <div class="grid gap-6 md:grid-cols-3">
        <div class="rounded-xl border border-border bg-card p-6">
          <div class="mb-4 flex size-12 items-center justify-center rounded-xl bg-primary/10">
            <Box class="size-6 text-primary" />
          </div>
          <h3 class="mb-2 text-lg font-semibold">Custom NLU Models</h3>
          <p class="text-sm text-muted">Train and deploy custom intent classifiers and entity extractors for domain-specific understanding.</p>
        </div>
        
        <div class="rounded-xl border border-border bg-card p-6">
          <div class="mb-4 flex size-12 items-center justify-center rounded-xl bg-accent/10">
            <Volume2 class="size-6 text-accent" />
          </div>
          <h3 class="mb-2 text-lg font-semibold">Voice Cloning</h3>
          <p class="text-sm text-muted">Create custom voice profiles by fine-tuning Kokoro TTS with your own voice samples.</p>
        </div>
        
        <div class="rounded-xl border border-border bg-card p-6">
          <div class="mb-4 flex size-12 items-center justify-center rounded-xl bg-blue-500/10">
            <GitBranch class="size-6 text-blue-500" />
          </div>
          <h3 class="mb-2 text-lg font-semibold">Plugin System</h3>
          <p class="text-sm text-muted">Extend functionality with custom plugins for integrations, analytics, and business logic.</p>
        </div>
      </div>
    </section>
  </div>
</div>
