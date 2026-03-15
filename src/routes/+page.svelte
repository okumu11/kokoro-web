<script lang="ts">
  import { 
    Mic, 
    Brain, 
    Zap, 
    Globe, 
    Settings, 
    Play, 
    ChevronRight,
    Wand2,
    MessageSquare,
    Layers,
    Shield,
    BarChart3,
    Code,
    ArrowRight,
    Volume2
  } from "lucide-svelte";

  const features = [
    {
      icon: Mic,
      title: "Speech Recognition",
      description: "Advanced ASR with support for multiple languages and accents. Real-time transcription with high accuracy."
    },
    {
      icon: Brain,
      title: "Natural Language Understanding",
      description: "Context-aware NLU engine that understands intent, entities, and sentiment from user conversations."
    },
    {
      icon: MessageSquare,
      title: "Conversational AI",
      description: "Build multi-turn dialogues with memory, context retention, and intelligent response generation."
    },
    {
      icon: Wand2,
      title: "Voice Synthesis",
      description: "High-quality TTS with customizable voices, emotions, and speaking styles powered by Kokoro."
    },
    {
      icon: Globe,
      title: "Multi-Platform Deploy",
      description: "Deploy your agents to web, mobile, IoT devices, and telephony systems with a single click."
    },
    {
      icon: Shield,
      title: "Enterprise Security",
      description: "SOC 2 compliant with end-to-end encryption, GDPR support, and audit logging."
    }
  ];

  const workflow = [
    {
      step: "01",
      title: "Define Your Agent",
      description: "Configure voice, personality, capabilities, and domain expertise through our intuitive builder."
    },
    {
      step: "02",
      title: "Train & Customize",
      description: "Upload training data, define intents, and fine-tune NLU models for your specific use case."
    },
    {
      step: "03",
      title: "Test & Iterate",
      description: "Use the voice playground to test conversations, debug flows, and optimize responses."
    },
    {
      step: "04",
      title: "Deploy & Scale",
      description: "Launch to production with automatic scaling, monitoring, and analytics built-in."
    }
  ];

  const useCases = [
    { name: "Customer Support", icon: MessageSquare },
    { name: "Virtual Assistants", icon: Wand2 },
    { name: "Healthcare Bots", icon: Shield },
    { name: "E-commerce Helpers", icon: BarChart3 },
    { name: "Education Tutors", icon: Brain },
    { name: "IoT Controllers", icon: Settings }
  ];
</script>

<div class="min-h-screen bg-background text-foreground">
  <!-- Hero Section -->
  <section class="relative overflow-hidden px-4 pb-20 pt-16 md:px-8 lg:pt-24">
    <div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top,_var(--tw-gradient-stops))] from-primary/10 via-transparent to-transparent"></div>
    
    <div class="relative mx-auto max-w-6xl">
      <div class="mb-6 flex items-center justify-center gap-2">
        <span class="rounded-full border border-primary/30 bg-primary/10 px-4 py-1.5 text-sm font-medium text-primary">
          Now in Beta
        </span>
      </div>
      
      <h1 class="text-balance mx-auto max-w-4xl text-center text-4xl font-bold tracking-tight md:text-6xl lg:text-7xl">
        Build <span class="gradient-text">Voice Agents</span> That Understand
      </h1>
      
      <p class="mx-auto mt-6 max-w-2xl text-center text-lg leading-relaxed text-muted md:text-xl">
        Create customized AI voice assistants with speech recognition, natural language understanding, and seamless deployment across any platform.
      </p>

      <div class="mt-10 flex flex-col items-center justify-center gap-4 sm:flex-row">
        <a href="/builder" class="group flex items-center gap-2 rounded-lg bg-primary px-6 py-3 font-semibold text-primary-foreground transition-all hover:bg-primary/90">
          Start Building
          <ArrowRight class="size-4 transition-transform group-hover:translate-x-1" />
        </a>
        <a href="/voiceover" class="flex items-center gap-2 rounded-lg border border-border bg-card px-6 py-3 font-semibold transition-all hover:border-primary/50 hover:bg-card/80">
          <Volume2 class="size-4" />
          Text to Voice
        </a>
        <a href="/playground" class="flex items-center gap-2 rounded-lg border border-border bg-card px-6 py-3 font-semibold transition-all hover:border-primary/50 hover:bg-card/80">
          <Play class="size-4" />
          Try Playground
        </a>
      </div>

      <!-- Stats -->
      <div class="mt-16 grid grid-cols-2 gap-8 border-y border-border py-8 md:grid-cols-4">
        <div class="text-center">
          <div class="text-3xl font-bold text-foreground md:text-4xl">50+</div>
          <div class="mt-1 text-sm text-muted">Languages</div>
        </div>
        <div class="text-center">
          <div class="text-3xl font-bold text-foreground md:text-4xl">99.5%</div>
          <div class="mt-1 text-sm text-muted">ASR Accuracy</div>
        </div>
        <div class="text-center">
          <div class="text-3xl font-bold text-foreground md:text-4xl">{'<'}200ms</div>
          <div class="mt-1 text-sm text-muted">Latency</div>
        </div>
        <div class="text-center">
          <div class="text-3xl font-bold text-foreground md:text-4xl">10K+</div>
          <div class="mt-1 text-sm text-muted">Agents Created</div>
        </div>
      </div>
    </div>
  </section>

  <!-- Features Grid -->
  <section class="px-4 py-20 md:px-8">
    <div class="mx-auto max-w-6xl">
      <div class="mb-12 text-center">
        <h2 class="text-3xl font-bold md:text-4xl">Everything You Need</h2>
        <p class="mt-4 text-lg text-muted">Comprehensive tools for building production-ready voice agents</p>
      </div>

      <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        {#each features as feature}
          <div class="feature-card rounded-xl p-6">
            <div class="mb-4 flex size-12 items-center justify-center rounded-lg bg-primary/10">
              <feature.icon class="size-6 text-primary" />
            </div>
            <h3 class="mb-2 text-lg font-semibold">{feature.title}</h3>
            <p class="text-sm leading-relaxed text-muted">{feature.description}</p>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Architecture Overview -->
  <section class="border-y border-border bg-card/30 px-4 py-20 md:px-8">
    <div class="mx-auto max-w-6xl">
      <div class="mb-12 text-center">
        <h2 class="text-3xl font-bold md:text-4xl">Platform Architecture</h2>
        <p class="mt-4 text-lg text-muted">A modular, extensible system designed for scale</p>
      </div>

      <div class="relative">
        <!-- Architecture Diagram -->
        <div class="rounded-2xl border border-border bg-background p-6 md:p-10">
          <div class="grid gap-6 md:grid-cols-3">
            <!-- Input Layer -->
            <div class="space-y-4">
              <div class="flex items-center gap-2">
                <div class="size-3 rounded-full bg-primary"></div>
                <span class="font-semibold">Input Layer</span>
              </div>
              <div class="space-y-3">
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Mic class="size-5 text-primary" />
                    <div>
                      <div class="font-medium">ASR Engine</div>
                      <div class="text-xs text-muted">Speech-to-Text</div>
                    </div>
                  </div>
                </div>
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Code class="size-5 text-primary" />
                    <div>
                      <div class="font-medium">Text Input</div>
                      <div class="text-xs text-muted">API / WebSocket</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Processing Layer -->
            <div class="space-y-4">
              <div class="flex items-center gap-2">
                <div class="size-3 rounded-full bg-accent"></div>
                <span class="font-semibold">Processing Layer</span>
              </div>
              <div class="space-y-3">
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Brain class="size-5 text-accent" />
                    <div>
                      <div class="font-medium">NLU Engine</div>
                      <div class="text-xs text-muted">Intent & Entity</div>
                    </div>
                  </div>
                </div>
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Layers class="size-5 text-accent" />
                    <div>
                      <div class="font-medium">Dialog Manager</div>
                      <div class="text-xs text-muted">Context & State</div>
                    </div>
                  </div>
                </div>
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Zap class="size-5 text-accent" />
                    <div>
                      <div class="font-medium">Response Gen</div>
                      <div class="text-xs text-muted">LLM Powered</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Output Layer -->
            <div class="space-y-4">
              <div class="flex items-center gap-2">
                <div class="size-3 rounded-full bg-primary"></div>
                <span class="font-semibold">Output Layer</span>
              </div>
              <div class="space-y-3">
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Wand2 class="size-5 text-primary" />
                    <div>
                      <div class="font-medium">TTS Engine</div>
                      <div class="text-xs text-muted">Kokoro Voices</div>
                    </div>
                  </div>
                </div>
                <div class="rounded-lg border border-border bg-card/50 p-4">
                  <div class="flex items-center gap-3">
                    <Globe class="size-5 text-primary" />
                    <div>
                      <div class="font-medium">Delivery</div>
                      <div class="text-xs text-muted">Multi-Channel</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Connection Lines -->
          <div class="mt-6 flex items-center justify-center gap-4">
            <div class="flex items-center gap-2 text-sm text-muted">
              <ChevronRight class="size-4" />
              <span>Data flows through pipeline with {'<'}200ms latency</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Workflow Section -->
  <section class="px-4 py-20 md:px-8">
    <div class="mx-auto max-w-6xl">
      <div class="mb-12 text-center">
        <h2 class="text-3xl font-bold md:text-4xl">Build in Four Steps</h2>
        <p class="mt-4 text-lg text-muted">From concept to production in minutes</p>
      </div>

      <div class="grid gap-8 md:grid-cols-2 lg:grid-cols-4">
        {#each workflow as item, i}
          <div class="relative">
            <div class="text-6xl font-bold text-border">{item.step}</div>
            <h3 class="mt-2 text-lg font-semibold">{item.title}</h3>
            <p class="mt-2 text-sm leading-relaxed text-muted">{item.description}</p>
            {#if i < workflow.length - 1}
              <div class="absolute right-0 top-8 hidden lg:block">
                <ChevronRight class="size-6 text-muted" />
              </div>
            {/if}
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Use Cases -->
  <section class="border-y border-border bg-card/30 px-4 py-20 md:px-8">
    <div class="mx-auto max-w-6xl">
      <div class="mb-12 text-center">
        <h2 class="text-3xl font-bold md:text-4xl">Built For Any Domain</h2>
        <p class="mt-4 text-lg text-muted">Voice agents tailored to your industry</p>
      </div>

      <div class="grid grid-cols-2 gap-4 md:grid-cols-3 lg:grid-cols-6">
        {#each useCases as useCase}
          <div class="flex flex-col items-center rounded-xl border border-border bg-background p-6 text-center transition-all hover:border-primary/50">
            <useCase.icon class="mb-3 size-8 text-primary" />
            <span class="text-sm font-medium">{useCase.name}</span>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- CTA Section -->
  <section class="px-4 py-20 md:px-8">
    <div class="mx-auto max-w-4xl text-center">
      <div class="waveform-animation mx-auto mb-6">
        <div class="waveform-bar"></div>
        <div class="waveform-bar"></div>
        <div class="waveform-bar"></div>
        <div class="waveform-bar"></div>
        <div class="waveform-bar"></div>
      </div>
      
      <h2 class="text-3xl font-bold md:text-4xl">Ready to Build Your Voice Agent?</h2>
      <p class="mt-4 text-lg text-muted">Start creating intelligent voice experiences today</p>
      
      <div class="mt-8 flex flex-col items-center justify-center gap-4 sm:flex-row">
        <a href="/builder" class="group flex items-center gap-2 rounded-lg bg-primary px-8 py-4 font-semibold text-primary-foreground transition-all hover:bg-primary/90">
          Launch Agent Builder
          <ArrowRight class="size-4 transition-transform group-hover:translate-x-1" />
        </a>
        <a href="/architecture" class="flex items-center gap-2 rounded-lg border border-border px-8 py-4 font-semibold transition-all hover:border-primary/50">
          View Architecture
        </a>
      </div>
    </div>
  </section>
</div>
