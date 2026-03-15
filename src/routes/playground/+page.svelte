<script lang="ts">
  import { onMount } from "svelte";
  import { 
    Mic, 
    MicOff,
    Play, 
    Square,
    Send,
    Volume2,
    Settings,
    RotateCcw,
    Loader2,
    User,
    Bot,
    ChevronDown,
    Zap
  } from "lucide-svelte";
  import { voices, langs, models } from "$lib/shared/resources";
  import { toaster } from "$lib/client/toaster";
  import { generate } from "../generate";
  import { profile, defaultProfile, loadProfile } from "../store.svelte";
  import AudioPlayer from "$lib/client/components/AudioPlayer.svelte";
  import { detectWebGPU } from "$lib/client/utils";

  // Conversation state
  let messages = $state<Array<{
    role: "user" | "assistant";
    content: string;
    audioUrl?: string;
    timestamp: Date;
  }>>([]);

  let userInput = $state("");
  let isProcessing = $state(false);
  let isListening = $state(false);
  let showSettings = $state(false);

  // Voice settings (synced with global profile)
  let selectedVoice = $state(profile.voiceFormula);
  let selectedLang = $state(profile.lang);
  let selectedModel = $state(profile.model);
  let speechSpeed = $state(profile.speed);
  let webgpuSupported = $state(false);

  // Sample conversations for testing
  const sampleConversations = [
    { user: "Hello, how are you?", assistant: "Hello! I'm doing great, thank you for asking. How can I help you today?" },
    { user: "What's the weather like?", assistant: "I'd be happy to help with weather information. Could you tell me which city you're interested in?" },
    { user: "Tell me a joke", assistant: "Why don't scientists trust atoms? Because they make up everything!" },
    { user: "What can you help me with?", assistant: "I can help you with a wide variety of tasks including answering questions, providing information, creative writing, and much more. What would you like assistance with?" }
  ];

  onMount(() => {
    webgpuSupported = detectWebGPU();
    // Add welcome message
    messages = [{
      role: "assistant",
      content: "Welcome to the Voice Playground! Send a message or click the microphone to start a conversation. I'll respond with synthesized speech.",
      timestamp: new Date()
    }];
  });

  async function handleSend() {
    if (!userInput.trim() || isProcessing) return;

    const userMessage = userInput.trim();
    userInput = "";

    // Add user message
    messages = [...messages, {
      role: "user",
      content: userMessage,
      timestamp: new Date()
    }];

    isProcessing = true;

    try {
      // Generate a response (simulated NLU for demo)
      const responseText = generateResponse(userMessage);

      // Update profile for TTS generation
      profile.text = responseText;
      profile.voiceFormula = selectedVoice;
      profile.lang = selectedLang;
      profile.model = selectedModel;
      profile.speed = speechSpeed;
      profile.executionPlace = "browser";
      profile.acceleration = webgpuSupported ? "webgpu" : "cpu";

      // Generate audio
      const audioUrl = await generate(profile);

      // Add assistant message with audio
      messages = [...messages, {
        role: "assistant",
        content: responseText,
        audioUrl,
        timestamp: new Date()
      }];

    } catch (error) {
      console.error("Error generating response:", error);
      toaster.error("Failed to generate response. Please try again.");
    } finally {
      isProcessing = false;
    }
  }

  function generateResponse(input: string): string {
    const lowered = input.toLowerCase();
    
    // Simple pattern matching for demo
    if (lowered.includes("hello") || lowered.includes("hi")) {
      return "Hello! It's great to hear from you. How can I assist you today?";
    }
    if (lowered.includes("weather")) {
      return "I'd love to help with weather information. Which city would you like to know about?";
    }
    if (lowered.includes("joke")) {
      const jokes = [
        "Why don't scientists trust atoms? Because they make up everything!",
        "What do you call a fish without eyes? A fsh!",
        "Why did the scarecrow win an award? Because he was outstanding in his field!"
      ];
      return jokes[Math.floor(Math.random() * jokes.length)];
    }
    if (lowered.includes("help")) {
      return "I'm here to help! I can answer questions, tell jokes, provide information, and have conversations. What would you like to talk about?";
    }
    if (lowered.includes("thank")) {
      return "You're welcome! Is there anything else I can help you with?";
    }
    if (lowered.includes("bye") || lowered.includes("goodbye")) {
      return "Goodbye! It was nice talking with you. Feel free to come back anytime!";
    }
    
    // Default response
    return `I heard you say: "${input}". That's interesting! Tell me more about what you'd like to discuss.`;
  }

  function loadSampleConversation() {
    const sample = sampleConversations[Math.floor(Math.random() * sampleConversations.length)];
    userInput = sample.user;
  }

  function clearConversation() {
    messages = [{
      role: "assistant",
      content: "Conversation cleared. Send a message to start a new conversation.",
      timestamp: new Date()
    }];
  }

  function toggleListening() {
    if (!('webkitSpeechRecognition' in window) && !('SpeechRecognition' in window)) {
      toaster.error("Speech recognition is not supported in your browser.");
      return;
    }
    
    isListening = !isListening;
    if (isListening) {
      // Start speech recognition
      const SpeechRecognition = (window as any).SpeechRecognition || (window as any).webkitSpeechRecognition;
      const recognition = new SpeechRecognition();
      recognition.continuous = false;
      recognition.interimResults = false;
      recognition.lang = selectedLang.replace("-", "_");

      recognition.onresult = (event: any) => {
        const transcript = event.results[0][0].transcript;
        userInput = transcript;
        isListening = false;
      };

      recognition.onerror = () => {
        isListening = false;
        toaster.error("Speech recognition failed. Please try again.");
      };

      recognition.onend = () => {
        isListening = false;
      };

      recognition.start();
    }
  }

  function handleKeyDown(event: KeyboardEvent) {
    if (event.key === "Enter" && !event.shiftKey) {
      event.preventDefault();
      handleSend();
    }
  }
</script>

<div class="flex h-[calc(100vh-72px)] bg-background">
  <!-- Main Chat Area -->
  <div class="flex flex-1 flex-col">
    <!-- Header -->
    <div class="flex items-center justify-between border-b border-border px-6 py-4">
      <div>
        <h1 class="text-xl font-bold">Voice Playground</h1>
        <p class="text-sm text-muted">Test your voice agent in real-time</p>
      </div>
      <div class="flex items-center gap-2">
        <button
          onclick={loadSampleConversation}
          class="flex items-center gap-2 rounded-lg border border-border px-3 py-2 text-sm transition-all hover:border-primary/50"
        >
          <Zap class="size-4" />
          Sample
        </button>
        <button
          onclick={clearConversation}
          class="flex items-center gap-2 rounded-lg border border-border px-3 py-2 text-sm transition-all hover:border-primary/50"
        >
          <RotateCcw class="size-4" />
          Clear
        </button>
        <button
          onclick={() => showSettings = !showSettings}
          class="flex items-center gap-2 rounded-lg px-3 py-2 text-sm transition-all {showSettings ? 'bg-primary text-primary-foreground' : 'border border-border hover:border-primary/50'}"
        >
          <Settings class="size-4" />
          Settings
        </button>
      </div>
    </div>

    <!-- Messages Area -->
    <div class="flex-1 overflow-y-auto p-6">
      <div class="mx-auto max-w-3xl space-y-6">
        {#each messages as message}
          <div class="flex gap-4 {message.role === 'user' ? 'flex-row-reverse' : ''}">
            <div class="flex size-10 shrink-0 items-center justify-center rounded-full {message.role === 'user' ? 'bg-primary' : 'bg-card'}">
              {#if message.role === 'user'}
                <User class="size-5 text-primary-foreground" />
              {:else}
                <Bot class="size-5 text-primary" />
              {/if}
            </div>
            <div class="max-w-[70%] space-y-2">
              <div class="rounded-2xl px-4 py-3 {message.role === 'user' ? 'bg-primary text-primary-foreground' : 'bg-card'}">
                <p class="leading-relaxed">{message.content}</p>
              </div>
              {#if message.audioUrl}
                <div class="rounded-xl border border-border bg-background p-2">
                  <AudioPlayer audioUrl={message.audioUrl} showSpectrogram={false} />
                </div>
              {/if}
              <div class="text-xs text-muted {message.role === 'user' ? 'text-right' : ''}">
                {message.timestamp.toLocaleTimeString()}
              </div>
            </div>
          </div>
        {/each}

        {#if isProcessing}
          <div class="flex gap-4">
            <div class="flex size-10 shrink-0 items-center justify-center rounded-full bg-card">
              <Bot class="size-5 text-primary" />
            </div>
            <div class="flex items-center gap-2 rounded-2xl bg-card px-4 py-3">
              <Loader2 class="size-4 animate-spin text-primary" />
              <span class="text-muted">Generating response...</span>
            </div>
          </div>
        {/if}
      </div>
    </div>

    <!-- Input Area -->
    <div class="border-t border-border p-4">
      <div class="mx-auto max-w-3xl">
        <div class="flex items-end gap-3">
          <button
            onclick={toggleListening}
            class="flex size-12 shrink-0 items-center justify-center rounded-full transition-all {isListening ? 'bg-red-500 text-white animate-pulse' : 'bg-card hover:bg-card/80'}"
          >
            {#if isListening}
              <MicOff class="size-5" />
            {:else}
              <Mic class="size-5" />
            {/if}
          </button>
          
          <div class="relative flex-1">
            <textarea
              bind:value={userInput}
              onkeydown={handleKeyDown}
              placeholder="Type your message or click the microphone..."
              rows="2"
              class="w-full resize-none rounded-xl border border-border bg-card px-4 py-3 pr-12 focus:border-primary focus:outline-none"
            ></textarea>
            <button
              onclick={handleSend}
              disabled={!userInput.trim() || isProcessing}
              class="absolute bottom-3 right-3 flex size-8 items-center justify-center rounded-lg bg-primary text-primary-foreground transition-all hover:bg-primary/90 disabled:opacity-50"
            >
              <Send class="size-4" />
            </button>
          </div>
        </div>
        
        <p class="mt-2 text-center text-xs text-muted">
          Press Enter to send, Shift+Enter for new line
        </p>
      </div>
    </div>
  </div>

  <!-- Settings Sidebar -->
  {#if showSettings}
    <div class="w-80 shrink-0 border-l border-border bg-card/30 p-6">
      <h2 class="mb-6 text-lg font-semibold">Voice Settings</h2>
      
      <div class="space-y-6">
        <!-- Language -->
        <div>
          <label class="mb-2 block text-sm font-medium">Language</label>
          <select
            bind:value={selectedLang}
            class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
          >
            {#each langs as lang}
              <option value={lang.id}>{lang.name}</option>
            {/each}
          </select>
        </div>

        <!-- Voice -->
        <div>
          <label class="mb-2 block text-sm font-medium">Voice</label>
          <div class="max-h-48 space-y-2 overflow-y-auto rounded-lg border border-border bg-background p-2">
            {#each voices.filter(v => v.lang.id === selectedLang) as voice}
              <button
                onclick={() => selectedVoice = voice.id}
                class="flex w-full items-center gap-3 rounded-lg px-3 py-2 text-left transition-all {selectedVoice === voice.id ? 'bg-primary/10 text-primary' : 'hover:bg-card'}"
              >
                <Volume2 class="size-4 shrink-0" />
                <div class="flex-1 overflow-hidden">
                  <div class="truncate text-sm font-medium">{voice.name}</div>
                  <div class="text-xs text-muted">{voice.gender}</div>
                </div>
              </button>
            {/each}
            {#if voices.filter(v => v.lang.id === selectedLang).length === 0}
              <p class="p-2 text-center text-sm text-muted">No voices for this language</p>
            {/if}
          </div>
        </div>

        <!-- Model -->
        <div>
          <label class="mb-2 block text-sm font-medium">Model</label>
          <select
            bind:value={selectedModel}
            class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm focus:border-primary focus:outline-none"
          >
            {#each models as model}
              <option value={model.id}>{model.size} - {model.quantization}</option>
            {/each}
          </select>
        </div>

        <!-- Speed -->
        <div>
          <label class="mb-2 block text-sm font-medium">Speech Speed: {speechSpeed}x</label>
          <input
            type="range"
            bind:value={speechSpeed}
            min="0.5"
            max="2"
            step="0.1"
            class="w-full"
          />
          <div class="mt-1 flex justify-between text-xs text-muted">
            <span>0.5x</span>
            <span>2x</span>
          </div>
        </div>

        <!-- Execution -->
        <div class="rounded-lg bg-background p-4">
          <div class="mb-2 text-sm font-medium">Execution Mode</div>
          <div class="text-xs text-muted">
            {#if webgpuSupported}
              Running on <span class="text-accent">WebGPU</span> for faster processing
            {:else}
              Running on <span class="text-primary">CPU</span> mode
            {/if}
          </div>
        </div>

        <!-- Quick Test -->
        <div>
          <label class="mb-2 block text-sm font-medium">Quick Voice Test</label>
          <button
            onclick={async () => {
              profile.text = "Hello! This is a test of the voice synthesis system.";
              profile.voiceFormula = selectedVoice;
              profile.lang = selectedLang;
              profile.speed = speechSpeed;
              profile.executionPlace = "browser";
              profile.acceleration = webgpuSupported ? "webgpu" : "cpu";
              
              try {
                const audioUrl = await generate(profile);
                messages = [...messages, {
                  role: "assistant",
                  content: "Voice test: Hello! This is a test of the voice synthesis system.",
                  audioUrl,
                  timestamp: new Date()
                }];
                toaster.success("Voice test generated!");
              } catch (e) {
                toaster.error("Failed to generate test audio");
              }
            }}
            class="flex w-full items-center justify-center gap-2 rounded-lg bg-primary px-4 py-2 font-medium text-primary-foreground transition-all hover:bg-primary/90"
          >
            <Play class="size-4" />
            Test Current Voice
          </button>
        </div>
      </div>
    </div>
  {/if}
</div>
