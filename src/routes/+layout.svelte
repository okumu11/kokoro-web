<script lang="ts">
  import "../app.css";
  import { fade } from "svelte/transition";
  import { ExternalLink, Menu, X, Github, Mic, Play, Settings, FileText, Layers, Volume2 } from "lucide-svelte";
  import type { LayoutProps } from "./$types";
  import { onMount } from "svelte";
  import umami from "$lib/client/umami";
  import { VERSION } from "$lib/shared/version";
  import { page } from "$app/stores";

  let { children }: LayoutProps = $props();

  let isOpen = $state(false);
  let scrolled = $state(false);

  const navLinks = [
    { href: "/builder", label: "Agent Builder", icon: Settings },
    { href: "/voiceover", label: "Text to Voice", icon: Volume2 },
    { href: "/playground", label: "Playground", icon: Play },
    { href: "/deployments", label: "Deployments", icon: Layers },
    { href: "/architecture", label: "Architecture", icon: FileText },
  ];

  onMount(() => {
    umami.loadScript();
    umami.identify({ hostname: window.location.hostname });
    
    const handleScroll = () => {
      scrolled = window.scrollY > 10;
    };
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  });
</script>

<div class="min-h-screen bg-background text-foreground">
  <header
    class="fixed top-0 z-50 w-full transition-all duration-300 {scrolled ? 'border-b border-border bg-background/80 backdrop-blur-lg' : 'bg-transparent'}"
  >
    <div class="mx-auto flex max-w-7xl items-center justify-between px-4 py-4">
      <a href="/" class="flex items-center gap-3">
        <div class="flex size-10 items-center justify-center rounded-lg bg-primary/10">
          <Mic class="size-5 text-primary" />
        </div>
        <div>
          <div class="text-lg font-bold">VoiceForge</div>
          <div class="text-xs text-muted">Voice Agent Generator</div>
        </div>
      </a>
      
      <nav class="hidden items-center gap-1 md:flex">
        {#each navLinks as link}
          <a
            href={link.href}
            class="flex items-center gap-2 rounded-lg px-4 py-2 text-sm font-medium transition-all hover:bg-card {$page.url.pathname === link.href ? 'bg-card text-primary' : 'text-muted hover:text-foreground'}"
          >
            <link.icon class="size-4" />
            {link.label}
          </a>
        {/each}
      </nav>

      <div class="hidden items-center gap-3 md:flex">
        <a
          href="https://github.com/eduardolat/kokoro-web"
          target="_blank"
          class="flex items-center gap-2 rounded-lg px-3 py-2 text-sm text-muted transition-all hover:text-foreground"
        >
          <Github class="size-4" />
          <span>GitHub</span>
        </a>
        <a href="/builder" class="rounded-lg bg-primary px-4 py-2 text-sm font-semibold text-primary-foreground transition-all hover:bg-primary/90">
          Get Started
        </a>
      </div>

      <button
        class="rounded-lg p-2 transition-all hover:bg-card md:hidden"
        onclick={() => (isOpen = !isOpen)}
      >
        <Menu class="size-6" />
      </button>
    </div>
  </header>

  {#if isOpen}
    <div
      class="fixed inset-0 z-50 bg-background md:hidden"
      transition:fade={{ duration: 150 }}
    >
      <div class="flex h-full flex-col">
        <div class="flex items-center justify-between border-b border-border px-4 py-4">
          <a href="/" class="flex items-center gap-3" onclick={() => (isOpen = false)}>
            <div class="flex size-10 items-center justify-center rounded-lg bg-primary/10">
              <Mic class="size-5 text-primary" />
            </div>
            <div class="text-lg font-bold">VoiceForge</div>
          </a>
          <button
            class="rounded-lg p-2 transition-all hover:bg-card"
            onclick={() => (isOpen = !isOpen)}
          >
            <X class="size-6" />
          </button>
        </div>

        <nav class="flex-1 space-y-1 p-4">
          {#each navLinks as link}
            <a
              href={link.href}
              onclick={() => (isOpen = false)}
              class="flex items-center gap-3 rounded-lg px-4 py-3 text-base font-medium transition-all hover:bg-card {$page.url.pathname === link.href ? 'bg-card text-primary' : 'text-muted'}"
            >
              <link.icon class="size-5" />
              {link.label}
            </a>
          {/each}
          
          <div class="my-4 border-t border-border"></div>
          
          <a
            href="https://github.com/eduardolat/kokoro-web"
            target="_blank"
            class="flex items-center gap-3 rounded-lg px-4 py-3 text-base font-medium text-muted transition-all hover:bg-card hover:text-foreground"
          >
            <Github class="size-5" />
            GitHub
            <ExternalLink class="ml-auto size-4" />
          </a>
        </nav>

        <div class="border-t border-border p-4">
          <a href="/builder" onclick={() => (isOpen = false)} class="flex w-full items-center justify-center rounded-lg bg-primary px-4 py-3 font-semibold text-primary-foreground transition-all hover:bg-primary/90">
            Get Started
          </a>
        </div>
      </div>
    </div>
  {/if}

  <main class="pt-[72px]">
    {@render children()}
  </main>

  <footer class="border-t border-border bg-card/30 px-4 py-12">
    <div class="mx-auto max-w-7xl">
      <div class="grid gap-8 md:grid-cols-4">
        <div class="md:col-span-2">
          <div class="flex items-center gap-3">
            <div class="flex size-10 items-center justify-center rounded-lg bg-primary/10">
              <Mic class="size-5 text-primary" />
            </div>
            <div class="text-lg font-bold">VoiceForge</div>
          </div>
          <p class="mt-4 max-w-sm text-sm leading-relaxed text-muted">
            Build intelligent voice agents with speech recognition, natural language understanding, and seamless multi-platform deployment.
          </p>
          <p class="mt-4 text-xs text-muted-foreground">
            Powered by Kokoro TTS {VERSION}
          </p>
        </div>
        
        <div>
          <h3 class="mb-4 font-semibold">Platform</h3>
          <ul class="space-y-2 text-sm text-muted">
            <li><a href="/builder" class="hover:text-foreground">Agent Builder</a></li>
            <li><a href="/voiceover" class="hover:text-foreground">Text to Voice</a></li>
            <li><a href="/playground" class="hover:text-foreground">Voice Playground</a></li>
            <li><a href="/deployments" class="hover:text-foreground">Deployments</a></li>
            <li><a href="/architecture" class="hover:text-foreground">Architecture</a></li>
          </ul>
        </div>
        
        <div>
          <h3 class="mb-4 font-semibold">Resources</h3>
          <ul class="space-y-2 text-sm text-muted">
            <li>
              <a href="/api/v1/index.html" target="_blank" class="flex items-center gap-1 hover:text-foreground">
                API Documentation
                <ExternalLink class="size-3" />
              </a>
            </li>
            <li>
              <a href="https://github.com/eduardolat/kokoro-web" target="_blank" class="flex items-center gap-1 hover:text-foreground">
                GitHub
                <ExternalLink class="size-3" />
              </a>
            </li>
            <li>
              <a href="https://huggingface.co/hexgrad/Kokoro-82M" target="_blank" class="flex items-center gap-1 hover:text-foreground">
                Kokoro Model
                <ExternalLink class="size-3" />
              </a>
            </li>
          </ul>
        </div>
      </div>
      
      <div class="mt-8 flex flex-col items-center justify-between gap-4 border-t border-border pt-8 text-sm text-muted md:flex-row">
        <p>VoiceForge - Open Source Voice Agent Platform</p>
        <p>
          <a
            href="https://eduardo.lat?utm_source=voiceforge&utm_medium=web"
            target="_blank"
            class="hover:text-foreground"
          >
            Created by Eduardo Lat
          </a>
        </p>
      </div>
    </div>
  </footer>
</div>
