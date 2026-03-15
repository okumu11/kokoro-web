<script lang="ts">
  import { onMount } from "svelte";
  import { 
    Mic, 
    Brain, 
    MessageSquare, 
    Zap, 
    Globe, 
    ChevronRight,
    Save,
    Play,
    Settings,
    User,
    Sparkles,
    Volume2,
    Wand2,
    Check,
    Plus,
    Trash2,
    Copy
  } from "lucide-svelte";
  import { voices, langs } from "$lib/shared/resources";
  import { toaster } from "$lib/client/toaster";

  // Agent configuration state
  let agentConfig = $state({
    name: "My Voice Agent",
    description: "A helpful voice assistant",
    domain: "general",
    personality: "friendly",
    voice: "af_heart",
    language: "en-us",
    speed: 1.0,
    greetingMessage: "Hello! How can I assist you today?",
    fallbackMessage: "I'm sorry, I didn't quite understand that. Could you please rephrase?",
    capabilities: {
      speechRecognition: true,
      naturalLanguage: true,
      contextAware: true,
      multiTurn: true,
      sentimentAnalysis: false,
      entityExtraction: true
    },
    intents: [
      { name: "greeting", examples: ["hello", "hi", "hey there"], response: "Hello! How can I help you?" },
      { name: "goodbye", examples: ["bye", "goodbye", "see you"], response: "Goodbye! Have a great day!" },
      { name: "help", examples: ["help", "I need help", "assist me"], response: "I'm here to help! What do you need assistance with?" }
    ],
    customResponses: [] as { trigger: string; response: string }[]
  });

  let currentStep = $state(1);
  let isSaving = $state(false);
  let newIntent = $state({ name: "", examples: "", response: "" });
  let newCustomResponse = $state({ trigger: "", response: "" });

  const domains = [
    { id: "general", name: "General Assistant", description: "Versatile helper for various tasks" },
    { id: "customer_support", name: "Customer Support", description: "Handle inquiries and issues" },
    { id: "sales", name: "Sales Assistant", description: "Product recommendations and orders" },
    { id: "healthcare", name: "Healthcare", description: "Medical information and scheduling" },
    { id: "education", name: "Education", description: "Learning and tutoring assistance" },
    { id: "ecommerce", name: "E-commerce", description: "Shopping and order management" }
  ];

  const personalities = [
    { id: "friendly", name: "Friendly", emoji: "Warm and approachable" },
    { id: "professional", name: "Professional", emoji: "Formal and business-like" },
    { id: "casual", name: "Casual", emoji: "Relaxed and informal" },
    { id: "enthusiastic", name: "Enthusiastic", emoji: "Energetic and excited" },
    { id: "empathetic", name: "Empathetic", emoji: "Understanding and caring" },
    { id: "concise", name: "Concise", emoji: "Brief and to the point" }
  ];

  const steps = [
    { number: 1, title: "Basic Info", icon: User },
    { number: 2, title: "Voice & Language", icon: Volume2 },
    { number: 3, title: "Capabilities", icon: Brain },
    { number: 4, title: "Intents & Responses", icon: MessageSquare },
    { number: 5, title: "Review & Deploy", icon: Zap }
  ];

  function nextStep() {
    if (currentStep < 5) currentStep++;
  }

  function prevStep() {
    if (currentStep > 1) currentStep--;
  }

  function addIntent() {
    if (newIntent.name && newIntent.examples && newIntent.response) {
      agentConfig.intents = [...agentConfig.intents, {
        name: newIntent.name,
        examples: newIntent.examples.split(",").map(e => e.trim()),
        response: newIntent.response
      }];
      newIntent = { name: "", examples: "", response: "" };
      toaster.success("Intent added successfully");
    }
  }

  function removeIntent(index: number) {
    agentConfig.intents = agentConfig.intents.filter((_, i) => i !== index);
  }

  function addCustomResponse() {
    if (newCustomResponse.trigger && newCustomResponse.response) {
      agentConfig.customResponses = [...agentConfig.customResponses, { ...newCustomResponse }];
      newCustomResponse = { trigger: "", response: "" };
    }
  }

  function removeCustomResponse(index: number) {
    agentConfig.customResponses = agentConfig.customResponses.filter((_, i) => i !== index);
  }

  async function saveAgent() {
    isSaving = true;
    // Simulate save
    await new Promise(resolve => setTimeout(resolve, 1500));
    isSaving = false;
    toaster.success("Agent configuration saved!");
  }

  function getVoiceName(voiceId: string) {
    const voice = voices.find(v => v.id === voiceId);
    return voice ? voice.name : voiceId;
  }

  function getLangName(langId: string) {
    const lang = langs.find(l => l.id === langId);
    return lang ? lang.name : langId;
  }
</script>

<div class="min-h-screen bg-background px-4 py-8 md:px-8">
  <div class="mx-auto max-w-6xl">
    <!-- Header -->
    <div class="mb-8">
      <h1 class="text-3xl font-bold md:text-4xl">Agent Builder</h1>
      <p class="mt-2 text-muted">Create and configure your custom voice agent</p>
    </div>

    <!-- Progress Steps -->
    <div class="mb-8 overflow-x-auto">
      <div class="flex min-w-max items-center justify-between gap-2">
        {#each steps as step, i}
          <button
            onclick={() => currentStep = step.number}
            class="flex items-center gap-3 rounded-lg px-4 py-3 transition-all {currentStep === step.number ? 'bg-primary text-primary-foreground' : currentStep > step.number ? 'bg-accent/20 text-accent' : 'bg-card text-muted'}"
          >
            <div class="flex size-8 items-center justify-center rounded-full {currentStep > step.number ? 'bg-accent' : currentStep === step.number ? 'bg-primary-foreground/20' : 'bg-border'}">
              {#if currentStep > step.number}
                <Check class="size-4" />
              {:else}
                <step.icon class="size-4" />
              {/if}
            </div>
            <span class="font-medium">{step.title}</span>
          </button>
          {#if i < steps.length - 1}
            <ChevronRight class="size-5 shrink-0 text-muted" />
          {/if}
        {/each}
      </div>
    </div>

    <!-- Step Content -->
    <div class="rounded-2xl border border-border bg-card p-6 md:p-8">
      
      <!-- Step 1: Basic Info -->
      {#if currentStep === 1}
        <div class="space-y-6">
          <div>
            <h2 class="text-xl font-semibold">Basic Information</h2>
            <p class="mt-1 text-sm text-muted">Define your agent's identity and purpose</p>
          </div>

          <div class="grid gap-6 md:grid-cols-2">
            <div>
              <label class="mb-2 block text-sm font-medium">Agent Name</label>
              <input
                type="text"
                bind:value={agentConfig.name}
                class="w-full rounded-lg border border-border bg-background px-4 py-3 focus:border-primary focus:outline-none"
                placeholder="My Voice Agent"
              />
            </div>

            <div>
              <label class="mb-2 block text-sm font-medium">Description</label>
              <input
                type="text"
                bind:value={agentConfig.description}
                class="w-full rounded-lg border border-border bg-background px-4 py-3 focus:border-primary focus:outline-none"
                placeholder="A helpful assistant for..."
              />
            </div>
          </div>

          <div>
            <label class="mb-3 block text-sm font-medium">Domain / Use Case</label>
            <div class="grid gap-3 md:grid-cols-3">
              {#each domains as domain}
                <button
                  onclick={() => agentConfig.domain = domain.id}
                  class="rounded-xl border p-4 text-left transition-all {agentConfig.domain === domain.id ? 'border-primary bg-primary/10' : 'border-border hover:border-primary/50'}"
                >
                  <div class="font-medium">{domain.name}</div>
                  <div class="mt-1 text-sm text-muted">{domain.description}</div>
                </button>
              {/each}
            </div>
          </div>

          <div>
            <label class="mb-3 block text-sm font-medium">Personality</label>
            <div class="grid grid-cols-2 gap-3 md:grid-cols-3">
              {#each personalities as personality}
                <button
                  onclick={() => agentConfig.personality = personality.id}
                  class="rounded-lg border px-4 py-3 text-left transition-all {agentConfig.personality === personality.id ? 'border-primary bg-primary/10' : 'border-border hover:border-primary/50'}"
                >
                  <div class="font-medium">{personality.name}</div>
                  <div class="text-xs text-muted">{personality.emoji}</div>
                </button>
              {/each}
            </div>
          </div>
        </div>
      {/if}

      <!-- Step 2: Voice & Language -->
      {#if currentStep === 2}
        <div class="space-y-6">
          <div>
            <h2 class="text-xl font-semibold">Voice & Language</h2>
            <p class="mt-1 text-sm text-muted">Configure how your agent sounds and speaks</p>
          </div>

          <div class="grid gap-6 md:grid-cols-2">
            <div>
              <label class="mb-2 block text-sm font-medium">Language</label>
              <select
                bind:value={agentConfig.language}
                class="w-full rounded-lg border border-border bg-background px-4 py-3 focus:border-primary focus:outline-none"
              >
                {#each langs as lang}
                  <option value={lang.id}>{lang.name}</option>
                {/each}
              </select>
            </div>

            <div>
              <label class="mb-2 block text-sm font-medium">Speech Speed</label>
              <div class="flex items-center gap-4">
                <input
                  type="range"
                  bind:value={agentConfig.speed}
                  min="0.5"
                  max="2"
                  step="0.1"
                  class="flex-1"
                />
                <span class="w-12 text-center font-mono">{agentConfig.speed}x</span>
              </div>
            </div>
          </div>

          <div>
            <label class="mb-3 block text-sm font-medium">Select Voice</label>
            <div class="grid gap-3 md:grid-cols-2 lg:grid-cols-3">
              {#each voices.filter(v => v.lang.id === agentConfig.language).slice(0, 9) as voice}
                <button
                  onclick={() => agentConfig.voice = voice.id}
                  class="flex items-center gap-3 rounded-lg border p-4 transition-all {agentConfig.voice === voice.id ? 'border-primary bg-primary/10' : 'border-border hover:border-primary/50'}"
                >
                  <div class="flex size-10 items-center justify-center rounded-full bg-primary/10">
                    <Volume2 class="size-5 text-primary" />
                  </div>
                  <div class="flex-1 text-left">
                    <div class="font-medium">{voice.name}</div>
                    <div class="text-xs text-muted">{voice.gender} - Grade {voice.overallGrade}</div>
                  </div>
                  {#if agentConfig.voice === voice.id}
                    <Check class="size-5 text-primary" />
                  {/if}
                </button>
              {/each}
            </div>
            {#if voices.filter(v => v.lang.id === agentConfig.language).length === 0}
              <p class="text-center text-muted">No voices available for this language. Using default voice.</p>
            {/if}
          </div>

          <div class="grid gap-6 md:grid-cols-2">
            <div>
              <label class="mb-2 block text-sm font-medium">Greeting Message</label>
              <textarea
                bind:value={agentConfig.greetingMessage}
                rows="3"
                class="w-full rounded-lg border border-border bg-background px-4 py-3 focus:border-primary focus:outline-none"
                placeholder="Hello! How can I help you today?"
              ></textarea>
            </div>

            <div>
              <label class="mb-2 block text-sm font-medium">Fallback Message</label>
              <textarea
                bind:value={agentConfig.fallbackMessage}
                rows="3"
                class="w-full rounded-lg border border-border bg-background px-4 py-3 focus:border-primary focus:outline-none"
                placeholder="I'm sorry, I didn't understand that..."
              ></textarea>
            </div>
          </div>
        </div>
      {/if}

      <!-- Step 3: Capabilities -->
      {#if currentStep === 3}
        <div class="space-y-6">
          <div>
            <h2 class="text-xl font-semibold">Agent Capabilities</h2>
            <p class="mt-1 text-sm text-muted">Enable the features your agent needs</p>
          </div>

          <div class="grid gap-4 md:grid-cols-2">
            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.speechRecognition}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <Mic class="size-5 text-primary" />
                  <span class="font-medium">Speech Recognition (ASR)</span>
                </div>
                <p class="mt-1 text-sm text-muted">Convert spoken audio to text with high accuracy</p>
              </div>
            </label>

            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.naturalLanguage}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <Brain class="size-5 text-primary" />
                  <span class="font-medium">Natural Language Understanding</span>
                </div>
                <p class="mt-1 text-sm text-muted">Understand intent and meaning from user messages</p>
              </div>
            </label>

            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.contextAware}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <Sparkles class="size-5 text-primary" />
                  <span class="font-medium">Context Awareness</span>
                </div>
                <p class="mt-1 text-sm text-muted">Remember conversation history and context</p>
              </div>
            </label>

            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.multiTurn}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <MessageSquare class="size-5 text-primary" />
                  <span class="font-medium">Multi-Turn Conversations</span>
                </div>
                <p class="mt-1 text-sm text-muted">Handle complex dialogues across multiple exchanges</p>
              </div>
            </label>

            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.sentimentAnalysis}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <Wand2 class="size-5 text-primary" />
                  <span class="font-medium">Sentiment Analysis</span>
                </div>
                <p class="mt-1 text-sm text-muted">Detect user emotions and adjust responses</p>
              </div>
            </label>

            <label class="flex items-start gap-4 rounded-xl border border-border p-4 transition-all hover:border-primary/50">
              <input
                type="checkbox"
                bind:checked={agentConfig.capabilities.entityExtraction}
                class="mt-1 size-5 rounded border-border"
              />
              <div>
                <div class="flex items-center gap-2">
                  <Settings class="size-5 text-primary" />
                  <span class="font-medium">Entity Extraction</span>
                </div>
                <p class="mt-1 text-sm text-muted">Extract key information like names, dates, numbers</p>
              </div>
            </label>
          </div>
        </div>
      {/if}

      <!-- Step 4: Intents & Responses -->
      {#if currentStep === 4}
        <div class="space-y-6">
          <div>
            <h2 class="text-xl font-semibold">Intents & Responses</h2>
            <p class="mt-1 text-sm text-muted">Define what your agent can understand and how it responds</p>
          </div>

          <!-- Existing Intents -->
          <div>
            <h3 class="mb-3 font-medium">Defined Intents</h3>
            <div class="space-y-3">
              {#each agentConfig.intents as intent, i}
                <div class="flex items-start gap-4 rounded-lg border border-border bg-background p-4">
                  <div class="flex-1">
                    <div class="font-medium text-primary">{intent.name}</div>
                    <div class="mt-1 text-sm text-muted">
                      Examples: {Array.isArray(intent.examples) ? intent.examples.join(", ") : intent.examples}
                    </div>
                    <div class="mt-1 text-sm">Response: {intent.response}</div>
                  </div>
                  <button
                    onclick={() => removeIntent(i)}
                    class="rounded-lg p-2 text-muted transition-all hover:bg-card hover:text-red-500"
                  >
                    <Trash2 class="size-4" />
                  </button>
                </div>
              {/each}
            </div>
          </div>

          <!-- Add New Intent -->
          <div class="rounded-xl border border-dashed border-border p-4">
            <h3 class="mb-3 font-medium">Add New Intent</h3>
            <div class="grid gap-4 md:grid-cols-3">
              <div>
                <label class="mb-1 block text-xs font-medium text-muted">Intent Name</label>
                <input
                  type="text"
                  bind:value={newIntent.name}
                  class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
                  placeholder="e.g., order_status"
                />
              </div>
              <div>
                <label class="mb-1 block text-xs font-medium text-muted">Example Phrases (comma separated)</label>
                <input
                  type="text"
                  bind:value={newIntent.examples}
                  class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
                  placeholder="e.g., where is my order, track order"
                />
              </div>
              <div>
                <label class="mb-1 block text-xs font-medium text-muted">Response</label>
                <input
                  type="text"
                  bind:value={newIntent.response}
                  class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
                  placeholder="e.g., Let me check your order status..."
                />
              </div>
            </div>
            <button
              onclick={addIntent}
              class="mt-3 flex items-center gap-2 rounded-lg bg-primary/10 px-4 py-2 text-sm font-medium text-primary transition-all hover:bg-primary/20"
            >
              <Plus class="size-4" />
              Add Intent
            </button>
          </div>

          <!-- Custom Responses -->
          <div>
            <h3 class="mb-3 font-medium">Custom Trigger Responses</h3>
            <div class="space-y-3">
              {#each agentConfig.customResponses as response, i}
                <div class="flex items-center gap-4 rounded-lg border border-border bg-background p-3">
                  <div class="flex-1 text-sm">
                    <span class="text-muted">Trigger:</span> {response.trigger}
                    <span class="mx-2">-></span>
                    <span class="text-muted">Response:</span> {response.response}
                  </div>
                  <button
                    onclick={() => removeCustomResponse(i)}
                    class="text-muted hover:text-red-500"
                  >
                    <Trash2 class="size-4" />
                  </button>
                </div>
              {/each}
            </div>
            <div class="mt-3 flex gap-2">
              <input
                type="text"
                bind:value={newCustomResponse.trigger}
                class="flex-1 rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
                placeholder="Trigger phrase..."
              />
              <input
                type="text"
                bind:value={newCustomResponse.response}
                class="flex-1 rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
                placeholder="Response..."
              />
              <button
                onclick={addCustomResponse}
                class="rounded-lg bg-card px-4 py-2 text-sm font-medium transition-all hover:bg-card/80"
              >
                Add
              </button>
            </div>
          </div>
        </div>
      {/if}

      <!-- Step 5: Review & Deploy -->
      {#if currentStep === 5}
        <div class="space-y-6">
          <div>
            <h2 class="text-xl font-semibold">Review & Deploy</h2>
            <p class="mt-1 text-sm text-muted">Review your agent configuration before deploying</p>
          </div>

          <div class="grid gap-6 md:grid-cols-2">
            <!-- Summary Card -->
            <div class="rounded-xl border border-border bg-background p-6">
              <h3 class="mb-4 text-lg font-semibold">Agent Summary</h3>
              <dl class="space-y-3">
                <div class="flex justify-between border-b border-border pb-2">
                  <dt class="text-muted">Name</dt>
                  <dd class="font-medium">{agentConfig.name}</dd>
                </div>
                <div class="flex justify-between border-b border-border pb-2">
                  <dt class="text-muted">Domain</dt>
                  <dd class="font-medium capitalize">{agentConfig.domain.replace("_", " ")}</dd>
                </div>
                <div class="flex justify-between border-b border-border pb-2">
                  <dt class="text-muted">Personality</dt>
                  <dd class="font-medium capitalize">{agentConfig.personality}</dd>
                </div>
                <div class="flex justify-between border-b border-border pb-2">
                  <dt class="text-muted">Voice</dt>
                  <dd class="font-medium">{getVoiceName(agentConfig.voice)}</dd>
                </div>
                <div class="flex justify-between border-b border-border pb-2">
                  <dt class="text-muted">Language</dt>
                  <dd class="font-medium">{getLangName(agentConfig.language)}</dd>
                </div>
                <div class="flex justify-between">
                  <dt class="text-muted">Intents</dt>
                  <dd class="font-medium">{agentConfig.intents.length} defined</dd>
                </div>
              </dl>
            </div>

            <!-- Capabilities Card -->
            <div class="rounded-xl border border-border bg-background p-6">
              <h3 class="mb-4 text-lg font-semibold">Enabled Capabilities</h3>
              <div class="space-y-2">
                {#each Object.entries(agentConfig.capabilities) as [key, enabled]}
                  <div class="flex items-center gap-2">
                    <div class="size-2 rounded-full {enabled ? 'bg-accent' : 'bg-muted'}"></div>
                    <span class="{enabled ? 'text-foreground' : 'text-muted'}">
                      {key.replace(/([A-Z])/g, ' $1').replace(/^./, str => str.toUpperCase())}
                    </span>
                  </div>
                {/each}
              </div>
            </div>
          </div>

          <!-- Configuration JSON -->
          <div class="rounded-xl border border-border bg-background p-6">
            <div class="mb-4 flex items-center justify-between">
              <h3 class="text-lg font-semibold">Configuration JSON</h3>
              <button
                onclick={() => {
                  navigator.clipboard.writeText(JSON.stringify(agentConfig, null, 2));
                  toaster.success("Configuration copied to clipboard");
                }}
                class="flex items-center gap-2 text-sm text-muted transition-all hover:text-foreground"
              >
                <Copy class="size-4" />
                Copy
              </button>
            </div>
            <pre class="max-h-48 overflow-auto rounded-lg bg-card p-4 text-xs">{JSON.stringify(agentConfig, null, 2)}</pre>
          </div>

          <!-- Deploy Actions -->
          <div class="flex flex-wrap items-center justify-between gap-4 rounded-xl border border-primary/20 bg-primary/5 p-6">
            <div>
              <h3 class="font-semibold">Ready to Deploy?</h3>
              <p class="text-sm text-muted">Save your configuration and test in the playground</p>
            </div>
            <div class="flex gap-3">
              <a
                href="/playground"
                class="flex items-center gap-2 rounded-lg border border-border px-4 py-2 font-medium transition-all hover:border-primary/50"
              >
                <Play class="size-4" />
                Test in Playground
              </a>
              <button
                onclick={saveAgent}
                disabled={isSaving}
                class="flex items-center gap-2 rounded-lg bg-primary px-6 py-2 font-semibold text-primary-foreground transition-all hover:bg-primary/90 disabled:opacity-50"
              >
                {#if isSaving}
                  <div class="size-4 animate-spin rounded-full border-2 border-primary-foreground border-t-transparent"></div>
                {:else}
                  <Save class="size-4" />
                {/if}
                {isSaving ? "Saving..." : "Save Agent"}
              </button>
            </div>
          </div>
        </div>
      {/if}

      <!-- Navigation Buttons -->
      <div class="mt-8 flex items-center justify-between border-t border-border pt-6">
        <button
          onclick={prevStep}
          disabled={currentStep === 1}
          class="flex items-center gap-2 rounded-lg px-4 py-2 text-muted transition-all hover:text-foreground disabled:opacity-50"
        >
          Previous
        </button>
        
        {#if currentStep < 5}
          <button
            onclick={nextStep}
            class="flex items-center gap-2 rounded-lg bg-primary px-6 py-2 font-semibold text-primary-foreground transition-all hover:bg-primary/90"
          >
            Next Step
            <ChevronRight class="size-4" />
          </button>
        {/if}
      </div>
    </div>
  </div>
</div>
