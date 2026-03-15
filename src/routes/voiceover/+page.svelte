<script lang="ts">
  import {
    Mic,
    Play,
    Download,
    Plus,
    Trash2,
    ChevronDown,
    ChevronUp,
    Wand2,
    FileText,
    Settings2,
    Loader2,
    Volume2,
    RotateCcw,
    Copy,
    CheckCheck,
    AlignLeft,
    SlidersHorizontal,
  } from "lucide-svelte";
  import { generate } from "../generate";
  import AudioPlayer from "$lib/client/components/AudioPlayer.svelte";
  import {
    voices,
    voicesByLang,
    langs,
    models,
    type LangId,
  } from "$lib/shared/resources";
  import { defaultProfile } from "../store.svelte";
  import { fade, slide } from "svelte/transition";

  // --- Types ---
  interface Segment {
    id: string;
    text: string;
    voice: string;
    lang: LangId;
    speed: number;
    audioUrl: string | null;
    generating: boolean;
    charCount: number;
  }

  // --- State ---
  let activeTab: "studio" | "batch" = $state("studio");

  // Studio mode state
  let studioText = $state(
    "Welcome to VoiceForge. Create stunning voiceovers with natural-sounding AI voices powered by Kokoro TTS.",
  );
  let studioVoice = $state("af_heart");
  let studioLang: LangId = $state("en-us");
  let studioSpeed = $state(1.0);
  let studioModel = $state("model_uint8");
  let studioFormat: "mp3" | "wav" = $state("mp3");
  let studioAudioUrl: string | null = $state(null);
  let studioGenerating = $state(false);
  let studioShowAdvanced = $state(false);
  let studioCopied = $state(false);

  // Batch mode state
  let segments: Segment[] = $state([
    {
      id: crypto.randomUUID(),
      text: "Hello and welcome to today's presentation.",
      voice: "af_heart",
      lang: "en-us",
      speed: 1.0,
      audioUrl: null,
      generating: false,
      charCount: 42,
    },
    {
      id: crypto.randomUUID(),
      text: "We will cover three key topics in this session.",
      voice: "am_fenrir",
      lang: "en-us",
      speed: 1.0,
      audioUrl: null,
      generating: false,
      charCount: 46,
    },
  ]);
  let batchGenerating = $state(false);
  let batchModel = $state("model_uint8");
  let batchFormat: "mp3" | "wav" = $state("mp3");

  // --- Derived ---
  let studioCharCount = $derived(studioText.length);
  let studioWordCount = $derived(
    studioText.trim() === "" ? 0 : studioText.trim().split(/\s+/).length,
  );
  let studioEstDuration = $derived(
    Math.round((studioWordCount / 150) * 60) / studioSpeed,
  );

  let filteredStudioVoices = $derived(
    voices.filter((v) => v.lang.id === studioLang),
  );

  // --- Helpers ---
  function getVoicesForLang(lang: LangId) {
    return voicesByLang[lang] ?? [];
  }

  function getFirstVoiceForLang(lang: LangId): string {
    const v = getVoicesForLang(lang);
    return v.length > 0 ? v[0].id : "af_heart";
  }

  function handleStudioLangChange() {
    studioVoice = getFirstVoiceForLang(studioLang);
    studioAudioUrl = null;
  }

  // --- Studio Actions ---
  async function generateStudio() {
    if (!studioText.trim() || studioGenerating) return;
    studioGenerating = true;
    studioAudioUrl = null;
    try {
      const url = await generate({
        ...defaultProfile,
        text: studioText,
        lang: studioLang,
        voiceMode: "simple",
        voiceFormula: studioVoice,
        model: studioModel as any,
        speed: studioSpeed,
        format: studioFormat,
        executionPlace: "browser",
        acceleration: "cpu",
      });
      studioAudioUrl = url;
    } catch (e) {
      console.error(e);
    } finally {
      studioGenerating = false;
    }
  }

  function clearStudio() {
    studioText = "";
    studioAudioUrl = null;
  }

  async function copyStudioText() {
    await navigator.clipboard.writeText(studioText);
    studioCopied = true;
    setTimeout(() => (studioCopied = false), 2000);
  }

  // --- Batch Actions ---
  function addSegment() {
    segments.push({
      id: crypto.randomUUID(),
      text: "",
      voice: "af_heart",
      lang: "en-us",
      speed: 1.0,
      audioUrl: null,
      generating: false,
      charCount: 0,
    });
  }

  function removeSegment(id: string) {
    segments = segments.filter((s) => s.id !== id);
  }

  function updateSegmentText(id: string, text: string) {
    const seg = segments.find((s) => s.id === id);
    if (seg) {
      seg.text = text;
      seg.charCount = text.length;
      seg.audioUrl = null;
    }
  }

  function updateSegmentLang(id: string, lang: LangId) {
    const seg = segments.find((s) => s.id === id);
    if (seg) {
      seg.lang = lang;
      seg.voice = getFirstVoiceForLang(lang);
      seg.audioUrl = null;
    }
  }

  async function generateSegment(id: string) {
    const seg = segments.find((s) => s.id === id);
    if (!seg || !seg.text.trim() || seg.generating) return;
    seg.generating = true;
    seg.audioUrl = null;
    try {
      const url = await generate({
        ...defaultProfile,
        text: seg.text,
        lang: seg.lang,
        voiceMode: "simple",
        voiceFormula: seg.voice,
        model: batchModel as any,
        speed: seg.speed,
        format: batchFormat,
        executionPlace: "browser",
        acceleration: "cpu",
      });
      seg.audioUrl = url;
    } catch (e) {
      console.error(e);
    } finally {
      seg.generating = false;
    }
  }

  async function generateAllSegments() {
    if (batchGenerating) return;
    batchGenerating = true;
    for (const seg of segments) {
      if (seg.text.trim()) {
        await generateSegment(seg.id);
      }
    }
    batchGenerating = false;
  }

  // --- Voice name lookup ---
  function getVoiceName(id: string): string {
    const v = voices.find((v) => v.id === id);
    return v ? `${v.name} (${v.overallGrade})` : id;
  }
</script>

<div class="min-h-screen bg-background text-foreground">
  <!-- Page Header -->
  <div class="border-b border-border bg-card/30">
    <div class="mx-auto max-w-7xl px-4 py-10 md:px-8">
      <div class="flex flex-col gap-4 md:flex-row md:items-end md:justify-between">
        <div>
          <div class="mb-3 flex items-center gap-3">
            <div class="flex size-12 items-center justify-center rounded-xl bg-primary/10">
              <Volume2 class="size-6 text-primary" />
            </div>
            <div>
              <h1 class="text-2xl font-bold md:text-3xl">Text to Voiceover</h1>
              <p class="text-sm text-muted">Generate professional voiceovers with Kokoro TTS</p>
            </div>
          </div>
          <div class="flex flex-wrap items-center gap-3 text-sm text-muted">
            <span class="flex items-center gap-1.5 rounded-full border border-border bg-card px-3 py-1">
              <span class="size-2 rounded-full bg-accent"></span>
              50+ voices
            </span>
            <span class="flex items-center gap-1.5 rounded-full border border-border bg-card px-3 py-1">
              <span class="size-2 rounded-full bg-primary"></span>
              8 languages
            </span>
            <span class="flex items-center gap-1.5 rounded-full border border-border bg-card px-3 py-1">
              <span class="size-2 rounded-full bg-primary"></span>
              Runs in browser
            </span>
          </div>
        </div>

        <!-- Tab Switcher -->
        <div class="flex rounded-xl border border-border bg-card p-1">
          <button
            onclick={() => (activeTab = "studio")}
            class="flex items-center gap-2 rounded-lg px-5 py-2.5 text-sm font-medium transition-all {activeTab === 'studio' ? 'bg-primary text-primary-foreground' : 'text-muted hover:text-foreground'}"
          >
            <Wand2 class="size-4" />
            Studio
          </button>
          <button
            onclick={() => (activeTab = "batch")}
            class="flex items-center gap-2 rounded-lg px-5 py-2.5 text-sm font-medium transition-all {activeTab === 'batch' ? 'bg-primary text-primary-foreground' : 'text-muted hover:text-foreground'}"
          >
            <AlignLeft class="size-4" />
            Batch
          </button>
        </div>
      </div>
    </div>
  </div>

  <div class="mx-auto max-w-7xl px-4 py-8 md:px-8">

    <!-- ========== STUDIO TAB ========== -->
    {#if activeTab === "studio"}
      <div transition:fade={{ duration: 150 }} class="grid gap-6 lg:grid-cols-[1fr_360px]">

        <!-- Left: Text Input -->
        <div class="flex flex-col gap-4">
          <!-- Toolbar -->
          <div class="flex items-center justify-between rounded-xl border border-border bg-card px-4 py-3">
            <div class="flex items-center gap-4 text-sm text-muted">
              <span><span class="font-semibold text-foreground">{studioCharCount}</span> chars</span>
              <span><span class="font-semibold text-foreground">{studioWordCount}</span> words</span>
              <span>
                ~<span class="font-semibold text-foreground">{studioEstDuration}s</span> audio
              </span>
            </div>
            <div class="flex items-center gap-2">
              <button
                onclick={copyStudioText}
                class="flex items-center gap-1.5 rounded-lg px-3 py-1.5 text-sm text-muted transition-all hover:bg-card hover:text-foreground"
                title="Copy text"
              >
                {#if studioCopied}
                  <CheckCheck class="size-4 text-accent" />
                {:else}
                  <Copy class="size-4" />
                {/if}
              </button>
              <button
                onclick={clearStudio}
                class="flex items-center gap-1.5 rounded-lg px-3 py-1.5 text-sm text-muted transition-all hover:bg-card hover:text-foreground"
                title="Clear text"
              >
                <RotateCcw class="size-4" />
              </button>
            </div>
          </div>

          <!-- Text Area -->
          <div class="relative rounded-xl border border-border bg-card transition-all focus-within:border-primary/50">
            <textarea
              bind:value={studioText}
              placeholder="Enter your voiceover script here..."
              rows="12"
              class="w-full resize-none bg-transparent p-5 text-base leading-relaxed outline-none placeholder:text-muted"
            ></textarea>
            <div class="absolute bottom-3 right-3 text-xs text-muted-foreground">
              {studioCharCount} / 5000
            </div>
          </div>

          <!-- Audio Player -->
          {#if studioAudioUrl}
            <div transition:slide={{ duration: 200 }}>
              <div class="mb-2 flex items-center gap-2 text-sm font-medium text-accent">
                <Volume2 class="size-4" />
                Generated Voiceover
              </div>
              <AudioPlayer audioUrl={studioAudioUrl} showSpectrogram={false} />
              <div class="mt-3 flex gap-2">
                <a
                  href={studioAudioUrl}
                  download="voiceover.{studioFormat}"
                  class="flex items-center gap-2 rounded-lg border border-border bg-card px-4 py-2 text-sm font-medium transition-all hover:border-primary/50 hover:text-primary"
                >
                  <Download class="size-4" />
                  Download {studioFormat.toUpperCase()}
                </a>
                <button
                  onclick={generateStudio}
                  class="flex items-center gap-2 rounded-lg border border-border bg-card px-4 py-2 text-sm font-medium transition-all hover:border-primary/50 hover:text-primary"
                >
                  <RotateCcw class="size-4" />
                  Regenerate
                </button>
              </div>
            </div>
          {/if}
        </div>

        <!-- Right: Settings Panel -->
        <div class="flex flex-col gap-4">

          <!-- Voice Settings Card -->
          <div class="rounded-xl border border-border bg-card p-5">
            <h3 class="mb-4 flex items-center gap-2 font-semibold">
              <Mic class="size-4 text-primary" />
              Voice Settings
            </h3>

            <div class="space-y-4">
              <!-- Language -->
              <div>
                <label class="mb-1.5 block text-sm font-medium text-muted">Language</label>
                <select
                  bind:value={studioLang}
                  onchange={handleStudioLangChange}
                  class="w-full rounded-lg border border-border bg-background px-3 py-2.5 text-sm outline-none transition-all focus:border-primary/50"
                >
                  {#each langs as lang}
                    <option value={lang.id}>{lang.name}</option>
                  {/each}
                </select>
              </div>

              <!-- Voice -->
              <div>
                <label class="mb-1.5 block text-sm font-medium text-muted">Voice</label>
                <select
                  bind:value={studioVoice}
                  class="w-full rounded-lg border border-border bg-background px-3 py-2.5 text-sm outline-none transition-all focus:border-primary/50"
                >
                  {#each filteredStudioVoices as v}
                    <option value={v.id}>
                      {v.name} — {v.gender} ({v.overallGrade})
                    </option>
                  {/each}
                </select>
                <!-- Voice preview chip -->
                {#if voices.find((v) => v.id === studioVoice)}
                  {@const selectedVoice = voices.find((v) => v.id === studioVoice)!}
                  <div class="mt-2 flex items-center gap-2">
                    <div class="flex size-8 items-center justify-center rounded-full bg-primary/10 text-xs font-bold text-primary">
                      {selectedVoice.name[0]}
                    </div>
                    <div class="text-xs text-muted">
                      <span class="font-medium text-foreground">{selectedVoice.name}</span>
                      &nbsp;·&nbsp;{selectedVoice.gender}&nbsp;·&nbsp;Grade: {selectedVoice.overallGrade}
                    </div>
                  </div>
                {/if}
              </div>

              <!-- Speed -->
              <div>
                <div class="mb-1.5 flex items-center justify-between">
                  <label class="text-sm font-medium text-muted">Speed</label>
                  <span class="text-sm font-semibold text-primary">{studioSpeed.toFixed(2)}x</span>
                </div>
                <input
                  type="range"
                  bind:value={studioSpeed}
                  min="0.5"
                  max="2.0"
                  step="0.05"
                  class="w-full accent-primary"
                />
                <div class="mt-1 flex justify-between text-xs text-muted">
                  <span>0.5x</span>
                  <span>1.0x</span>
                  <span>2.0x</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Advanced Settings (collapsible) -->
          <div class="rounded-xl border border-border bg-card">
            <button
              onclick={() => (studioShowAdvanced = !studioShowAdvanced)}
              class="flex w-full items-center justify-between p-5 text-sm font-semibold transition-all hover:text-primary"
            >
              <span class="flex items-center gap-2">
                <SlidersHorizontal class="size-4 text-primary" />
                Advanced Settings
              </span>
              {#if studioShowAdvanced}
                <ChevronUp class="size-4 text-muted" />
              {:else}
                <ChevronDown class="size-4 text-muted" />
              {/if}
            </button>

            {#if studioShowAdvanced}
              <div transition:slide={{ duration: 200 }} class="space-y-4 border-t border-border px-5 pb-5 pt-4">
                <!-- Model -->
                <div>
                  <label class="mb-1.5 block text-sm font-medium text-muted">Model Quality</label>
                  <select
                    bind:value={studioModel}
                    class="w-full rounded-lg border border-border bg-background px-3 py-2.5 text-sm outline-none transition-all focus:border-primary/50"
                  >
                    {#each models as m}
                      <option value={m.id}>{m.id} — {m.quantization} ({m.size})</option>
                    {/each}
                  </select>
                </div>

                <!-- Format -->
                <div>
                  <label class="mb-1.5 block text-sm font-medium text-muted">Output Format</label>
                  <div class="flex gap-2">
                    {#each (["mp3", "wav"] as const) as fmt}
                      <button
                        onclick={() => (studioFormat = fmt)}
                        class="flex-1 rounded-lg border py-2.5 text-sm font-medium transition-all {studioFormat === fmt ? 'border-primary bg-primary/10 text-primary' : 'border-border text-muted hover:border-primary/30'}"
                      >
                        {fmt.toUpperCase()}
                      </button>
                    {/each}
                  </div>
                </div>
              </div>
            {/if}
          </div>

          <!-- Generate Button -->
          <button
            onclick={generateStudio}
            disabled={studioGenerating || !studioText.trim()}
            class="flex w-full items-center justify-center gap-3 rounded-xl bg-primary py-4 font-semibold text-primary-foreground transition-all hover:bg-primary/90 disabled:cursor-not-allowed disabled:opacity-50"
          >
            {#if studioGenerating}
              <Loader2 class="size-5 animate-spin" />
              Generating...
            {:else}
              <Play class="size-5" />
              Generate Voiceover
            {/if}
          </button>

          <!-- Usage Tips -->
          <div class="rounded-xl border border-border bg-card/50 p-4">
            <p class="mb-2 text-xs font-semibold uppercase tracking-wider text-muted">Tips</p>
            <ul class="space-y-1.5 text-xs text-muted">
              <li class="flex items-start gap-2">
                <span class="mt-0.5 text-primary">•</span>
                Use punctuation for natural pauses
              </li>
              <li class="flex items-start gap-2">
                <span class="mt-0.5 text-primary">•</span>
                Grade A voices sound most natural
              </li>
              <li class="flex items-start gap-2">
                <span class="mt-0.5 text-primary">•</span>
                Lower speed for narration content
              </li>
              <li class="flex items-start gap-2">
                <span class="mt-0.5 text-primary">•</span>
                WAV format for highest quality
              </li>
            </ul>
          </div>
        </div>
      </div>
    {/if}

    <!-- ========== BATCH TAB ========== -->
    {#if activeTab === "batch"}
      <div transition:fade={{ duration: 150 }} class="flex flex-col gap-6">

        <!-- Batch Header Controls -->
        <div class="flex flex-col gap-4 rounded-xl border border-border bg-card p-5 md:flex-row md:items-end">
          <div class="flex-1">
            <h3 class="mb-1 font-semibold">Batch Voiceover Studio</h3>
            <p class="text-sm text-muted">Create multiple voice segments with individual settings. Perfect for multi-speaker scripts and long-form content.</p>
          </div>
          <div class="flex flex-wrap items-end gap-3">
            <!-- Global Model -->
            <div>
              <label class="mb-1 block text-xs font-medium text-muted">Model</label>
              <select
                bind:value={batchModel}
                class="rounded-lg border border-border bg-background px-3 py-2 text-sm outline-none focus:border-primary/50"
              >
                {#each models as m}
                  <option value={m.id}>{m.id} ({m.size})</option>
                {/each}
              </select>
            </div>
            <!-- Global Format -->
            <div>
              <label class="mb-1 block text-xs font-medium text-muted">Format</label>
              <div class="flex gap-1">
                {#each (["mp3", "wav"] as const) as fmt}
                  <button
                    onclick={() => (batchFormat = fmt)}
                    class="rounded-lg border px-3 py-2 text-sm font-medium transition-all {batchFormat === fmt ? 'border-primary bg-primary/10 text-primary' : 'border-border text-muted hover:border-primary/30'}"
                  >
                    {fmt.toUpperCase()}
                  </button>
                {/each}
              </div>
            </div>
            <!-- Generate All -->
            <button
              onclick={generateAllSegments}
              disabled={batchGenerating || segments.every((s) => !s.text.trim())}
              class="flex items-center gap-2 rounded-lg bg-primary px-5 py-2 font-semibold text-primary-foreground transition-all hover:bg-primary/90 disabled:cursor-not-allowed disabled:opacity-50"
            >
              {#if batchGenerating}
                <Loader2 class="size-4 animate-spin" />
                Generating All...
              {:else}
                <Play class="size-4" />
                Generate All
              {/if}
            </button>
          </div>
        </div>

        <!-- Segments List -->
        <div class="flex flex-col gap-3">
          {#each segments as seg, i (seg.id)}
            <div
              class="rounded-xl border border-border bg-card transition-all {seg.audioUrl ? 'border-accent/30' : ''}"
              transition:slide={{ duration: 200 }}
            >
              <!-- Segment Header -->
              <div class="flex items-center justify-between border-b border-border px-5 py-3">
                <div class="flex items-center gap-3">
                  <div class="flex size-7 items-center justify-center rounded-full bg-primary/10 text-xs font-bold text-primary">
                    {i + 1}
                  </div>
                  <span class="text-sm font-medium text-muted">
                    Segment {i + 1}
                    {#if seg.charCount > 0}
                      <span class="ml-2 text-xs">— {seg.charCount} chars</span>
                    {/if}
                  </span>
                  {#if seg.audioUrl}
                    <span class="flex items-center gap-1 text-xs text-accent">
                      <CheckCheck class="size-3" />
                      Ready
                    </span>
                  {/if}
                </div>
                <div class="flex items-center gap-2">
                  <button
                    onclick={() => generateSegment(seg.id)}
                    disabled={seg.generating || !seg.text.trim()}
                    class="flex items-center gap-1.5 rounded-lg bg-primary/10 px-3 py-1.5 text-xs font-medium text-primary transition-all hover:bg-primary/20 disabled:cursor-not-allowed disabled:opacity-50"
                  >
                    {#if seg.generating}
                      <Loader2 class="size-3 animate-spin" />
                      Generating
                    {:else}
                      <Play class="size-3" />
                      Generate
                    {/if}
                  </button>
                  <button
                    onclick={() => removeSegment(seg.id)}
                    class="rounded-lg p-1.5 text-muted transition-all hover:bg-card hover:text-red-500"
                    title="Remove segment"
                    disabled={segments.length === 1}
                  >
                    <Trash2 class="size-4" />
                  </button>
                </div>
              </div>

              <!-- Segment Body -->
              <div class="grid gap-4 p-5 md:grid-cols-[1fr_280px]">
                <!-- Text area -->
                <div>
                  <textarea
                    value={seg.text}
                    oninput={(e) => updateSegmentText(seg.id, (e.target as HTMLTextAreaElement).value)}
                    placeholder="Enter segment text..."
                    rows="4"
                    class="w-full resize-none rounded-lg border border-border bg-background p-3 text-sm leading-relaxed outline-none transition-all focus:border-primary/50 placeholder:text-muted"
                  ></textarea>
                </div>

                <!-- Voice controls -->
                <div class="space-y-3">
                  <!-- Language -->
                  <div>
                    <label class="mb-1 block text-xs font-medium text-muted">Language</label>
                    <select
                      value={seg.lang}
                      onchange={(e) => updateSegmentLang(seg.id, (e.target as HTMLSelectElement).value as LangId)}
                      class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm outline-none focus:border-primary/50"
                    >
                      {#each langs as lang}
                        <option value={lang.id}>{lang.name}</option>
                      {/each}
                    </select>
                  </div>

                  <!-- Voice -->
                  <div>
                    <label class="mb-1 block text-xs font-medium text-muted">Voice</label>
                    <select
                      value={seg.voice}
                      onchange={(e) => {
                        const s = segments.find((s) => s.id === seg.id);
                        if (s) { s.voice = (e.target as HTMLSelectElement).value; s.audioUrl = null; }
                      }}
                      class="w-full rounded-lg border border-border bg-background px-3 py-2 text-sm outline-none focus:border-primary/50"
                    >
                      {#each getVoicesForLang(seg.lang) as v}
                        <option value={v.id}>{v.name} — {v.gender} ({v.overallGrade})</option>
                      {/each}
                    </select>
                  </div>

                  <!-- Speed -->
                  <div>
                    <div class="mb-1 flex items-center justify-between">
                      <label class="text-xs font-medium text-muted">Speed</label>
                      <span class="text-xs font-semibold text-primary">{seg.speed.toFixed(2)}x</span>
                    </div>
                    <input
                      type="range"
                      value={seg.speed}
                      oninput={(e) => {
                        const s = segments.find((s) => s.id === seg.id);
                        if (s) { s.speed = parseFloat((e.target as HTMLInputElement).value); s.audioUrl = null; }
                      }}
                      min="0.5"
                      max="2.0"
                      step="0.05"
                      class="w-full accent-primary"
                    />
                  </div>
                </div>
              </div>

              <!-- Segment Audio Player -->
              {#if seg.audioUrl}
                <div transition:slide={{ duration: 200 }} class="border-t border-border p-5 pt-4">
                  <div class="mb-2 flex items-center justify-between">
                    <span class="text-xs font-medium text-accent">Segment Audio</span>
                    <a
                      href={seg.audioUrl}
                      download="segment-{i + 1}.{batchFormat}"
                      class="flex items-center gap-1 text-xs text-muted transition-all hover:text-primary"
                    >
                      <Download class="size-3" />
                      Download
                    </a>
                  </div>
                  <AudioPlayer audioUrl={seg.audioUrl} showSpectrogram={false} />
                </div>
              {/if}
            </div>
          {/each}
        </div>

        <!-- Add Segment Button -->
        <button
          onclick={addSegment}
          class="flex w-full items-center justify-center gap-2 rounded-xl border border-dashed border-border py-4 text-sm font-medium text-muted transition-all hover:border-primary/50 hover:text-primary"
        >
          <Plus class="size-4" />
          Add Segment
        </button>

        <!-- Batch Summary -->
        {#if segments.some((s) => s.audioUrl)}
          <div class="rounded-xl border border-accent/30 bg-accent/5 p-5">
            <div class="flex items-center gap-3">
              <CheckCheck class="size-5 text-accent" />
              <div>
                <p class="font-semibold text-accent">
                  {segments.filter((s) => s.audioUrl).length} of {segments.length} segments generated
                </p>
                <p class="text-sm text-muted">Download each segment individually using the download buttons above.</p>
              </div>
            </div>
          </div>
        {/if}
      </div>
    {/if}
  </div>
</div>
