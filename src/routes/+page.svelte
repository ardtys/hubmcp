<script>
  import { onMount } from 'svelte';

  let currentTime = $state('');
  let scrollY = $state(0);
  let mounted = $state(false);
  let activeTestimonial = $state(0);
  let mobileMenuOpen = $state(false);
  let mouseX = $state(0);
  let mouseY = $state(0);
  let particles = $state([]);
  let contractCopied = $state(false);

  // New feature states
  let scrollProgress = $state(0);
  let showToast = $state(false);
  let toastMessage = $state('');
  let showFloatingCTA = $state(false);

  const contractAddress = 'Coming Soon...';

  function copyContract() {
    if (contractAddress === 'Coming Soon...') {
      showToastNotification('Contract address coming soon!');
      return;
    }
    navigator.clipboard.writeText(contractAddress);
    contractCopied = true;
    showToastNotification('Contract address copied!');
    setTimeout(() => contractCopied = false, 2000);
  }

  function showToastNotification(message) {
    toastMessage = message;
    showToast = true;
    setTimeout(() => showToast = false, 3000);
  }

  function updateScrollProgress() {
    const scrollTop = window.scrollY;
    const docHeight = document.documentElement.scrollHeight - window.innerHeight;
    scrollProgress = docHeight > 0 ? (scrollTop / docHeight) * 100 : 0;
    showFloatingCTA = scrollTop > 500;
  }

  const mcpServers = [
    { name: 'Gmail', desc: 'Read, send, organize emails', category: 'Productivity', gradient: 'from-red-500 to-orange-500' },
    { name: 'Notion', desc: 'Manage pages and databases', category: 'Productivity', gradient: 'from-neutral-600 to-neutral-800' },
    { name: 'GitHub', desc: 'Repos, issues, pull requests', category: 'Development', gradient: 'from-purple-600 to-pink-600' },
    { name: 'Slack', desc: 'Messages and channels', category: 'Communication', gradient: 'from-purple-500 to-fuchsia-500' },
    { name: 'Google Calendar', desc: 'Events and schedules', category: 'Productivity', gradient: 'from-blue-500 to-cyan-500' },
    { name: 'Google Drive', desc: 'Files and documents', category: 'Productivity', gradient: 'from-yellow-500 to-green-500' },
    { name: 'Discord', desc: 'Servers and messages', category: 'Communication', gradient: 'from-indigo-500 to-purple-500' },
    { name: 'Linear', desc: 'Issues and projects', category: 'Development', gradient: 'from-blue-600 to-violet-600' },
    { name: 'Figma', desc: 'Designs and comments', category: 'Design', gradient: 'from-pink-500 to-rose-500' },
  ];

  const stats = [
    { value: '200+', label: 'Integrations', icon: 'zap', color: 'from-orange-500 to-red-500' },
    { value: '10k+', label: 'Users', icon: 'users', color: 'from-blue-500 to-cyan-500' },
    { value: '99.9%', label: 'Uptime', icon: 'check', color: 'from-green-500 to-emerald-500' },
    { value: '< 30s', label: 'Setup time', icon: 'clock', color: 'from-purple-500 to-pink-500' },
  ];

  const steps = [
    { number: '01', title: 'Browse Catalog', desc: 'Explore 200+ MCP integrations. Find Gmail, Notion, GitHub, Slack, and hundreds more.', icon: 'search' },
    { number: '02', title: 'One-Click Connect', desc: 'Click connect, authorize with OAuth. No terminal, no config files, no Docker.', icon: 'plug' },
    { number: '03', title: 'Start Using Claude', desc: 'Go back to Claude and start asking. "Summarize my emails", "Create a Notion page".', icon: 'sparkles' }
  ];

  const features = [
    { title: 'Hosted MCP Servers', desc: 'We run the servers so you don\'t have to. No Docker, no localhost, no port forwarding.', icon: 'server' },
    { title: 'Works With Claude', desc: 'Compatible with Claude.ai, Claude Desktop, and any MCP-compatible client.', icon: 'check-circle' },
    { title: 'Prompt Templates', desc: 'Don\'t know what to ask Claude? We give you ready-made prompts for every integration.', icon: 'file-text' },
    { title: 'Workflow Bundles', desc: 'Pre-configured packs: Productivity Suite, Developer Kit, Sales Stack.', icon: 'package' },
    { title: 'Community MCPs', desc: 'Hundreds of community-built MCPs that Anthropic doesn\'t officially support.', icon: 'users' },
    { title: 'Enterprise Ready', desc: 'Team management, shared connections, audit logs, and SSO.', icon: 'building' }
  ];

  const testimonials = [
    { quote: "Hub MCP saved me hours of setup time. I connected Gmail and Notion in under a minute.", author: "Sarah Chen", role: "Product Manager", company: "TechCorp", avatar: "SC" },
    { quote: "Finally, MCP without the headache. Our entire team uses Hub MCP daily.", author: "Marcus Johnson", role: "Engineering Lead", company: "StartupXYZ", avatar: "MJ" },
    { quote: "The prompt templates are a game-changer. I actually know what to ask Claude now.", author: "Emily Rodriguez", role: "Content Strategist", company: "MediaFlow", avatar: "ER" }
  ];

  const comparison = [
    { feature: 'Setup time', mcphub: '< 30 seconds', traditional: '30+ minutes' },
    { feature: 'Technical skill', mcphub: 'None required', traditional: 'CLI, Docker, Config' },
    { feature: 'Server hosting', mcphub: 'We handle it', traditional: 'Self-hosted' },
    { feature: 'OAuth setup', mcphub: 'Automatic', traditional: 'Manual credentials' },
    { feature: 'Updates', mcphub: 'Automatic', traditional: 'Manual' },
    { feature: 'Support', mcphub: 'Included', traditional: 'Community only' },
  ];

  const faqs = [
    { q: 'What is MCP?', a: 'MCP (Model Context Protocol) is an open standard that lets AI assistants like Claude connect to external tools and data sources. Think of it as a universal plug that lets Claude talk to Gmail, Notion, GitHub, and hundreds of other apps.' },
    { q: 'Do I need to know how to code?', a: 'No. That\'s the whole point. Traditional MCP setup requires terminal commands, config files, and running servers locally. Hub MCP handles all of that. You just click "Connect" and authorize with OAuth.' },
    { q: 'Is my data safe?', a: 'Yes. We use OAuth for all connections, which means we never see or store your passwords. When you connect Gmail, for example, you authorize directly with Google.' },
    { q: 'What if the MCP I want isn\'t listed?', a: 'Request it! We monitor requests and add new MCPs within 48 hours when possible.' },
    { q: 'How is this different from Claude\'s built-in connectors?', a: 'Anthropic officially supports about 10 integrations. We support 200+, including all community-built MCPs.' },
    { q: 'Can I use Hub MCP with my team?', a: 'Yes! Our Team plan includes shared connections, team workflows, admin controls, and audit logs.' }
  ];

  let openFaq = $state(-1);

  function toggleFaq(index) {
    openFaq = openFaq === index ? -1 : index;
  }

  function initParticles() {
    particles = Array.from({ length: 30 }, (_, i) => ({
      id: i,
      x: Math.random() * 100,
      y: Math.random() * 100,
      size: Math.random() * 4 + 2,
      duration: Math.random() * 20 + 15,
      delay: Math.random() * 10
    }));
  }

  function handleMouseMove(e) {
    mouseX = e.clientX;
    mouseY = e.clientY;
  }

  onMount(() => {
    mounted = true;
    initParticles();
    updateScrollProgress();

    const updateTime = () => {
      const now = new Date();
      currentTime = now.toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit', second: '2-digit', hour12: true });
    };
    updateTime();
    const interval = setInterval(updateTime, 1000);

    const testimonialInterval = setInterval(() => {
      activeTestimonial = (activeTestimonial + 1) % testimonials.length;
    }, 5000);

    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            entry.target.classList.add('animate-in');
          }
        });
      },
      { threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
    );

    document.querySelectorAll('.animate-on-scroll').forEach((el) => observer.observe(el));

    window.addEventListener('scroll', updateScrollProgress);

    return () => {
      clearInterval(interval);
      clearInterval(testimonialInterval);
      observer.disconnect();
      window.removeEventListener('scroll', updateScrollProgress);
    };
  });
</script>

<svelte:window bind:scrollY onmousemove={handleMouseMove} />

<svelte:head>
  <title>Hub MCP - Connect Claude to Everything</title>
  <meta name="description" content="The easiest way to connect Claude to Gmail, Notion, Slack, and 200+ other apps. No terminal required." />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
</svelte:head>

<!-- Scroll Progress Bar -->
<div class="fixed top-0 left-0 w-full h-1 z-[100] bg-transparent">
  <div
    class="h-full bg-gradient-to-r from-[#dc7c6a] via-[#f4a393] to-[#dc7c6a] transition-all duration-150 ease-out shadow-lg shadow-[#dc7c6a]/50"
    style="width: {scrollProgress}%"
  ></div>
</div>

<!-- Toast Notification -->
{#if showToast}
  <div class="fixed bottom-24 left-1/2 -translate-x-1/2 z-[100] animate-toast-in">
    <div class="flex items-center gap-3 px-5 py-3 bg-[#1a1a1a] border border-[#dc7c6a]/30 rounded-xl shadow-2xl shadow-[#dc7c6a]/20">
      <div class="w-6 h-6 rounded-full bg-green-500/20 flex items-center justify-center">
        <svg class="w-4 h-4 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
        </svg>
      </div>
      <span class="text-white font-medium text-sm">{toastMessage}</span>
    </div>
  </div>
{/if}

<!-- Floating CTA Button -->
{#if showFloatingCTA}
  <a
    href="#waitlist"
    class="fixed bottom-6 right-6 z-[90] group animate-fade-in-up"
  >
    <div class="relative">
      <div class="absolute -inset-1 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] rounded-full blur-lg opacity-70 group-hover:opacity-100 transition-opacity duration-300 animate-pulse"></div>
      <div class="relative flex items-center gap-2 px-5 py-3 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-semibold rounded-full shadow-xl hover:shadow-2xl hover:shadow-[#dc7c6a]/40 transition-all duration-300 hover:-translate-y-1">
        <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
        </svg>
        <span class="hidden sm:inline">Join Waitlist</span>
      </div>
    </div>
  </a>
{/if}

<!-- Animated Background -->
<div class="fixed inset-0 overflow-hidden pointer-events-none">
  <!-- Gradient Orbs -->
  <div class="absolute top-0 -right-40 w-[600px] h-[600px] bg-[#dc7c6a]/20 rounded-full blur-[120px] animate-float-slow"></div>
  <div class="absolute top-1/3 -left-40 w-[500px] h-[500px] bg-purple-500/10 rounded-full blur-[100px] animate-float-medium"></div>
  <div class="absolute bottom-0 right-1/3 w-[400px] h-[400px] bg-blue-500/10 rounded-full blur-[80px] animate-float-fast"></div>

  <!-- Grid Pattern -->
  <div class="absolute inset-0 bg-grid-pattern opacity-[0.03]"></div>

  <!-- Floating Particles -->
  {#each particles as particle}
    <div
      class="absolute rounded-full bg-[#dc7c6a]/20"
      style="left: {particle.x}%; top: {particle.y}%; width: {particle.size}px; height: {particle.size}px; animation: float-particle {particle.duration}s ease-in-out infinite; animation-delay: -{particle.delay}s;"
    ></div>
  {/each}

  <!-- Mouse follower -->
  <div
    class="absolute w-[500px] h-[500px] bg-[#dc7c6a]/5 rounded-full blur-[100px] transition-all duration-1000 ease-out hidden lg:block"
    style="left: {mouseX - 250}px; top: {mouseY - 250}px;"
  ></div>
</div>

<!-- Mobile Menu Overlay -->
{#if mobileMenuOpen}
  <div class="fixed inset-0 z-[100] bg-black/95 backdrop-blur-xl lg:hidden">
    <div class="flex flex-col h-full p-6">
      <div class="flex items-center justify-between mb-12">
        <a href="/" class="flex items-center gap-3">
          <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl" />
        </a>
        <button onclick={() => mobileMenuOpen = false} class="p-2 text-neutral-400 hover:text-white" aria-label="Close menu">
          <svg class="w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
      <nav class="flex-1 flex flex-col gap-4">
        <a href="/about" class="text-2xl font-semibold text-white hover:text-[#dc7c6a] transition-colors py-3 border-b border-neutral-800" onclick={() => mobileMenuOpen = false}>About</a>
        <a href="/integrations" class="text-2xl font-semibold text-white hover:text-[#dc7c6a] transition-colors py-3 border-b border-neutral-800" onclick={() => mobileMenuOpen = false}>Integrations</a>
        <a href="/pricing" class="text-2xl font-semibold text-white hover:text-[#dc7c6a] transition-colors py-3 border-b border-neutral-800" onclick={() => mobileMenuOpen = false}>Pricing</a>
        <a href="/how-it-works" class="text-2xl font-semibold text-white hover:text-[#dc7c6a] transition-colors py-3 border-b border-neutral-800" onclick={() => mobileMenuOpen = false}>How It Works</a>
        <a href="/demo" class="text-2xl font-semibold text-white hover:text-[#dc7c6a] transition-colors py-3 border-b border-neutral-800" onclick={() => mobileMenuOpen = false}>Demo</a>
      </nav>
      <a href="#waitlist" onclick={() => mobileMenuOpen = false} class="w-full py-4 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-bold rounded-xl text-center text-lg">
        Join Waitlist
      </a>
    </div>
  </div>
{/if}

<div class="min-h-screen relative overflow-hidden">
  <!-- Navigation -->
  <nav class="fixed top-0 left-0 right-0 z-50 px-4 sm:px-6 lg:px-12 py-4 lg:py-6 transition-all duration-500 {scrollY > 50 ? 'nav-scrolled' : ''}">
    <div class="max-w-7xl mx-auto flex items-center justify-between">
      <a href="/" class="flex items-center group">
        <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl group-hover:scale-110 transition-transform duration-300 shadow-lg shadow-[#dc7c6a]/30" />
      </a>

      <!-- Desktop Nav -->
      <div class="hidden lg:flex items-center gap-8 font-mono text-sm">
        <a href="/about" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">about</a>
        <a href="/integrations" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">integrations</a>
        <a href="/pricing" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">pricing</a>
        <a href="/how-it-works" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">how it works</a>
        <a href="/demo" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">demo</a>
        <a href="#waitlist" class="relative px-4 py-2 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-semibold rounded-lg transition-all duration-300 hover:-translate-y-0.5 text-sm shadow-lg shadow-[#dc7c6a]/30 overflow-hidden group">
          <span class="relative z-10">Join Waitlist</span>
          <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
        </a>
      </div>

      <!-- Mobile Nav Button -->
      <button onclick={() => mobileMenuOpen = true} class="lg:hidden p-2 text-neutral-400 hover:text-white" aria-label="Open menu">
        <svg class="w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>
  </nav>

  <!-- Hero Section -->
  <section class="min-h-screen flex flex-col justify-center px-4 sm:px-6 lg:px-12 pt-20 lg:pt-24 pb-12 relative">
    <div class="max-w-7xl mx-auto w-full">
      <div class="grid lg:grid-cols-2 gap-8 lg:gap-16 items-center">
        <!-- Left Side -->
        <div class="space-y-6 lg:space-y-10 text-center lg:text-left">
          <!-- Badge -->
          <div class="{mounted ? 'animate-fade-in-up' : 'opacity-0'}">
            <span class="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-gradient-to-r from-[#dc7c6a]/20 to-purple-500/10 border border-[#dc7c6a]/30 text-[#dc7c6a] text-sm font-medium backdrop-blur-sm">
              <span class="relative flex h-2 w-2">
                <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-[#dc7c6a] opacity-75"></span>
                <span class="relative inline-flex rounded-full h-2 w-2 bg-[#dc7c6a]"></span>
              </span>
              Now in private beta
            </span>
          </div>

          <!-- Headline -->
          <div class="{mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 100ms">
            <h1 class="text-4xl sm:text-5xl lg:text-6xl xl:text-7xl font-black text-white leading-[1.1] tracking-tight">
              Connect Claude to<br/>
              <span class="text-transparent bg-clip-text bg-gradient-to-r from-[#f4a393] via-[#dc7c6a] to-[#c96b5a] animate-gradient">Everything.</span>
            </h1>
            <p class="mt-4 lg:mt-6 text-base sm:text-lg lg:text-xl text-neutral-400 leading-relaxed max-w-lg mx-auto lg:mx-0">
              The easiest way to use MCP. Connect Gmail, Notion, Slack, and 200+ other apps to Claude. No terminal required.
            </p>
          </div>

          <!-- CTA Buttons -->
          <div class="flex flex-col sm:flex-row gap-3 sm:gap-4 justify-center lg:justify-start {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
            <a href="#waitlist" class="relative px-6 py-3 lg:py-4 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#f4a393] hover:to-[#dc7c6a] text-white font-semibold rounded-xl transition-all duration-300 hover:shadow-xl hover:shadow-[#dc7c6a]/30 hover:-translate-y-0.5 text-center overflow-hidden group">
              <span class="relative z-10">Join Waitlist</span>
              <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
            </a>
            <a href="/how-it-works" class="px-6 py-3 lg:py-4 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 font-semibold rounded-xl border border-[#2e2e2e] transition-all duration-300 hover:-translate-y-0.5 flex items-center justify-center gap-2">
              <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
                <path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              See How It Works
            </a>
          </div>

        </div>

        <!-- Right Side - Stats -->
        <div class="space-y-4 lg:space-y-6 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
          <!-- Stats Grid -->
          <div class="grid grid-cols-2 gap-3 lg:gap-4">
            {#each stats as stat, i}
              <div class="relative p-4 lg:p-6 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] hover:border-[#dc7c6a]/40 transition-all duration-500 cursor-default group overflow-hidden">
                <div class="absolute top-0 right-0 w-24 h-24 bg-gradient-to-br {stat.color} opacity-5 rounded-full blur-2xl group-hover:opacity-10 transition-opacity"></div>
                <p class="text-2xl sm:text-3xl lg:text-4xl font-bold text-white group-hover:text-transparent group-hover:bg-clip-text group-hover:bg-gradient-to-r {stat.color} transition-all duration-300">{stat.value}</p>
                <p class="text-xs sm:text-sm text-neutral-500 mt-1 font-medium">{stat.label}</p>
              </div>
            {/each}
          </div>

          <!-- Integration Preview -->
          <div class="relative p-4 lg:p-6 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] overflow-hidden">
            <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/5 via-purple-500/5 to-transparent"></div>
            <p class="relative text-sm text-neutral-400 mb-3 lg:mb-4 font-medium">Popular integrations</p>
            <div class="relative flex flex-wrap gap-2">
              {#each ['Gmail', 'Notion', 'Slack', 'GitHub', 'Discord', 'Linear', 'Figma', 'Drive'] as app}
                <span class="px-3 py-1.5 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e] text-xs sm:text-sm text-neutral-400 font-mono hover:border-[#dc7c6a]/40 hover:text-[#dc7c6a] transition-all duration-300 cursor-pointer hover:shadow-lg hover:shadow-[#dc7c6a]/10">
                  {app}
                </span>
              {/each}
              <a href="/integrations" class="px-3 py-1.5 rounded-lg bg-gradient-to-r from-[#dc7c6a]/20 to-purple-500/10 border border-[#dc7c6a]/30 text-xs sm:text-sm text-[#dc7c6a] font-mono hover:from-[#dc7c6a]/30 transition-all duration-300">
                +192 more
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Scroll indicator -->
    <div class="absolute bottom-8 lg:bottom-12 left-1/2 -translate-x-1/2 {mounted ? 'animate-fade-in' : 'opacity-0'} hidden sm:block" style="animation-delay: 1000ms">
      <div class="scroll-indicator flex flex-col items-center gap-2 text-neutral-600">
        <span class="text-xs font-mono">scroll</span>
        <div class="w-px h-8 bg-gradient-to-b from-neutral-600 to-transparent"></div>
      </div>
    </div>
  </section>

  <!-- Problem Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 relative">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white mb-4">
          MCP is powerful.<br/>
          <span class="text-neutral-500">Setup shouldn't require a CS degree.</span>
        </h2>
        <p class="text-neutral-400 text-base lg:text-lg max-w-2xl mx-auto mt-4 lg:mt-6 px-4">
          Model Context Protocol lets Claude connect to almost anything. But until now, that meant terminal commands, JSON configs, and debugging Docker containers.
        </p>
      </div>

      <!-- Comparison -->
      <div class="grid lg:grid-cols-2 gap-6 lg:gap-8 max-w-5xl mx-auto">
        <!-- Without Hub MCP -->
        <div class="animate-on-scroll">
          <div class="flex items-center gap-2 mb-4">
            <span class="w-3 h-3 rounded-full bg-red-500 shadow-lg shadow-red-500/50"></span>
            <span class="text-sm font-semibold text-red-400">Without Hub MCP</span>
          </div>
          <div class="relative bg-[#0d0d0d] rounded-2xl border border-[#1f1f1f] p-4 lg:p-6 font-mono text-xs sm:text-sm overflow-hidden">
            <div class="absolute top-0 right-0 w-32 h-32 bg-red-500/5 rounded-full blur-2xl"></div>
            <div class="relative space-y-2 text-neutral-500">
              <p># Step 1: Clone the repo</p>
              <p class="text-green-400">$ git clone https://github.com/mcp/servers</p>
              <p># Step 2: Install dependencies</p>
              <p class="text-green-400">$ npm install</p>
              <p class="text-red-400">npm ERR! peer dependency conflict</p>
              <p># Step 3: Configure OAuth</p>
              <p class="text-green-400">$ cat config.json</p>
              <p class="text-neutral-400 break-all">{"{"}"client_id": "???", "secret": "???"{"}"}</p>
              <p class="text-red-400 mt-2">Error: Port 3000 already in use</p>
              <p class="text-neutral-600 mt-4">█ still debugging...</p>
            </div>
          </div>
          <p class="text-neutral-500 text-sm mt-4 text-center">Average setup time: 30+ minutes</p>
        </div>

        <!-- With Hub MCP -->
        <div class="animate-on-scroll">
          <div class="flex items-center gap-2 mb-4">
            <span class="w-3 h-3 rounded-full bg-green-500 shadow-lg shadow-green-500/50"></span>
            <span class="text-sm font-semibold text-green-400">With Hub MCP</span>
          </div>
          <div class="relative bg-[#141414] rounded-2xl border border-[#dc7c6a]/30 p-4 lg:p-6 overflow-hidden">
            <div class="absolute top-0 right-0 w-32 h-32 bg-[#dc7c6a]/10 rounded-full blur-2xl"></div>
            <div class="relative space-y-4">
              <div class="flex items-center gap-3 lg:gap-4 p-3 lg:p-4 bg-[#1a1a1a] rounded-xl border border-[#2e2e2e]">
                <div class="w-10 h-10 lg:w-12 lg:h-12 rounded-xl bg-gradient-to-br from-red-500 to-orange-500 flex items-center justify-center flex-shrink-0 shadow-lg">
                  <svg class="w-5 h-5 lg:w-6 lg:h-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                  </svg>
                </div>
                <div class="flex-1 min-w-0">
                  <p class="font-semibold text-white text-sm lg:text-base">Gmail</p>
                  <p class="text-xs lg:text-sm text-neutral-500 truncate">Read, send, organize emails</p>
                </div>
                <button class="px-3 lg:px-4 py-1.5 lg:py-2 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white rounded-lg text-xs lg:text-sm font-medium shadow-lg shadow-[#dc7c6a]/30">Connect</button>
              </div>
              <div class="flex items-center justify-center gap-2 text-green-400 py-2">
                <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                </svg>
                <span class="text-sm font-medium">Connected!</span>
              </div>
              <div class="p-3 lg:p-4 bg-[#1a1a1a] rounded-xl border border-[#2e2e2e]">
                <p class="text-xs lg:text-sm text-neutral-400 mb-2">Try asking Claude:</p>
                <p class="text-neutral-200 italic text-sm">"Summarize my unread emails from today"</p>
              </div>
            </div>
          </div>
          <p class="text-[#dc7c6a] text-sm mt-4 text-center font-medium">Setup time: ~30 seconds</p>
        </div>
      </div>
    </div>
  </section>

  <!-- How It Works Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a] relative">
    <div class="absolute inset-0 bg-gradient-to-b from-transparent via-[#dc7c6a]/5 to-transparent"></div>
    <div class="max-w-7xl mx-auto relative">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">HOW IT WORKS</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">Three steps. That's it.</h2>
        <p class="text-neutral-400 text-base lg:text-lg mt-4">No README to read. No dependencies to install.</p>
      </div>

      <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4 lg:gap-8">
        {#each steps as step, i}
          <div class="animate-on-scroll" style="transition-delay: {i * 100}ms">
            <div class="relative p-6 lg:p-8 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] hover:border-[#dc7c6a]/40 transition-all duration-500 group h-full overflow-hidden">
              <div class="absolute top-0 right-0 w-32 h-32 bg-[#dc7c6a]/5 rounded-full blur-2xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
              <span class="absolute top-4 right-4 text-5xl lg:text-6xl font-black text-[#1f1f1f] group-hover:text-[#dc7c6a]/20 transition-colors duration-500">{step.number}</span>
              <div class="relative z-10">
                <div class="w-12 h-12 lg:w-14 lg:h-14 rounded-2xl bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center mb-4 lg:mb-6 group-hover:scale-110 transition-transform duration-300 shadow-lg shadow-[#dc7c6a]/30">
                  {#if step.icon === 'search'}
                    <svg class="w-6 h-6 lg:w-7 lg:h-7 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                    </svg>
                  {:else if step.icon === 'plug'}
                    <svg class="w-6 h-6 lg:w-7 lg:h-7 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                    </svg>
                  {:else}
                    <svg class="w-6 h-6 lg:w-7 lg:h-7 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z" />
                    </svg>
                  {/if}
                </div>
                <h3 class="text-lg lg:text-xl font-bold text-white mb-2 lg:mb-3">{step.title}</h3>
                <p class="text-neutral-400 text-sm lg:text-base leading-relaxed">{step.desc}</p>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <div class="text-center mt-8 lg:mt-12 animate-on-scroll">
        <a href="/how-it-works" class="inline-flex items-center gap-2 text-[#dc7c6a] hover:text-[#f4a393] transition-colors duration-300 font-medium group">
          Learn more about how it works
          <svg class="w-4 h-4 group-hover:translate-x-1 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
      </div>
    </div>
  </section>

  <!-- Integrations Section -->
  <section id="integrations" class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 relative">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">INTEGRATIONS</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">
          mcp<span class="text-[#dc7c6a]">:</span>servers
        </h2>
        <p class="text-neutral-400 text-base lg:text-lg mt-4">Connect Claude to everything. 200+ integrations and counting.</p>
      </div>

      <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-3 lg:gap-4">
        {#each mcpServers as server, i}
          <div class="animate-on-scroll" style="transition-delay: {i * 50}ms">
            <div class="relative group p-4 lg:p-5 bg-gradient-to-br from-[#141414] to-[#0d0d0d] rounded-xl border border-[#2e2e2e] hover:border-[#dc7c6a]/40 transition-all duration-500 cursor-pointer overflow-hidden">
              <div class="absolute top-0 right-0 w-20 h-20 bg-gradient-to-br {server.gradient} opacity-5 group-hover:opacity-10 rounded-full blur-xl transition-opacity"></div>
              <div class="relative flex items-start gap-3 lg:gap-4">
                <div class="w-10 h-10 lg:w-12 lg:h-12 rounded-xl bg-gradient-to-br {server.gradient} flex items-center justify-center group-hover:scale-110 transition-transform duration-300 flex-shrink-0 shadow-lg">
                  <svg class="w-5 h-5 lg:w-6 lg:h-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                  </svg>
                </div>
                <div class="flex-1 min-w-0">
                  <div class="flex items-center justify-between mb-1">
                    <h3 class="font-semibold text-white group-hover:text-[#dc7c6a] transition-colors duration-300 text-sm lg:text-base">{server.name}</h3>
                    <span class="text-[10px] lg:text-xs text-neutral-600 font-mono">{server.category}</span>
                  </div>
                  <p class="text-xs lg:text-sm text-neutral-500">{server.desc}</p>
                </div>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <div class="text-center mt-8 lg:mt-12 animate-on-scroll">
        <a href="/integrations" class="inline-flex items-center gap-3 px-6 py-3 lg:py-4 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 font-semibold rounded-xl border border-[#2e2e2e] transition-all duration-300 hover:-translate-y-0.5 hover:shadow-xl">
          View all 200+ integrations
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
      </div>
    </div>
  </section>

  <!-- Features Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a] relative">
    <div class="absolute inset-0 bg-gradient-to-b from-transparent via-purple-500/5 to-transparent"></div>
    <div class="max-w-7xl mx-auto relative">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">FEATURES</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">Built for humans.</h2>
        <p class="text-neutral-400 text-base lg:text-lg mt-4">Everything you need to use MCPs without becoming a DevOps engineer.</p>
      </div>

      <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4 lg:gap-6">
        {#each features as feature, i}
          <div class="animate-on-scroll" style="transition-delay: {i * 50}ms">
            <div class="relative p-5 lg:p-6 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] hover:border-[#dc7c6a]/30 transition-all duration-500 h-full group overflow-hidden">
              <div class="absolute top-0 right-0 w-24 h-24 bg-[#dc7c6a]/5 rounded-full blur-2xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
              <div class="relative">
                <div class="w-10 h-10 lg:w-12 lg:h-12 rounded-xl bg-gradient-to-br from-[#dc7c6a]/20 to-purple-500/10 flex items-center justify-center mb-3 lg:mb-4 group-hover:scale-110 transition-transform duration-300">
                  <svg class="w-5 h-5 lg:w-6 lg:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                    {#if feature.icon === 'server'}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M5 12h14M5 12a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v4a2 2 0 01-2 2M5 12a2 2 0 00-2 2v4a2 2 0 002 2h14a2 2 0 002-2v-4a2 2 0 00-2-2m-2-4h.01M17 16h.01" />
                    {:else if feature.icon === 'check-circle'}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                    {:else if feature.icon === 'file-text'}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                    {:else if feature.icon === 'package'}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
                    {:else if feature.icon === 'users'}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z" />
                    {:else}
                      <path stroke-linecap="round" stroke-linejoin="round" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
                    {/if}
                  </svg>
                </div>
                <h3 class="text-base lg:text-lg font-bold text-white mb-2">{feature.title}</h3>
                <p class="text-neutral-400 text-xs lg:text-sm leading-relaxed">{feature.desc}</p>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Testimonials Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 relative">
    <div class="max-w-4xl mx-auto">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">TESTIMONIALS</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">Loved by users.</h2>
      </div>

      <div class="relative animate-on-scroll">
        <div class="relative bg-gradient-to-br from-[#141414] to-[#0d0d0d] rounded-3xl border border-[#2e2e2e] p-6 lg:p-12 overflow-hidden">
          <div class="absolute top-0 right-0 w-40 h-40 bg-[#dc7c6a]/10 rounded-full blur-3xl"></div>
          <div class="absolute bottom-0 left-0 w-32 h-32 bg-purple-500/10 rounded-full blur-2xl"></div>
          {#each testimonials as testimonial, i}
            <div class="relative transition-opacity duration-500 {activeTestimonial === i ? 'opacity-100' : 'opacity-0 absolute inset-0 p-6 lg:p-12'}">
              <svg class="w-8 h-8 lg:w-12 lg:h-12 text-[#dc7c6a]/30 mb-4 lg:mb-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M14.017 21v-7.391c0-5.704 3.731-9.57 8.983-10.609l.995 2.151c-2.432.917-3.995 3.638-3.995 5.849h4v10h-9.983zm-14.017 0v-7.391c0-5.704 3.748-9.57 9-10.609l.996 2.151c-2.433.917-3.996 3.638-3.996 5.849h3.983v10h-9.983z"/>
              </svg>
              <p class="text-lg sm:text-xl lg:text-2xl text-neutral-200 leading-relaxed mb-6 lg:mb-8">"{testimonial.quote}"</p>
              <div class="flex items-center gap-3 lg:gap-4">
                <div class="w-10 h-10 lg:w-12 lg:h-12 rounded-full bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center text-white font-bold text-sm lg:text-base shadow-lg shadow-[#dc7c6a]/30">{testimonial.avatar}</div>
                <div>
                  <p class="font-semibold text-white text-sm lg:text-base">{testimonial.author}</p>
                  <p class="text-xs lg:text-sm text-neutral-500">{testimonial.role} at {testimonial.company}</p>
                </div>
              </div>
            </div>
          {/each}
        </div>
        <div class="flex justify-center gap-2 mt-4 lg:mt-6">
          {#each testimonials as _, i}
            <button
              class="w-2 h-2 rounded-full transition-all duration-300 {activeTestimonial === i ? 'bg-[#dc7c6a] w-6' : 'bg-neutral-700 hover:bg-neutral-600'}"
              onclick={() => activeTestimonial = i}
              aria-label="Select testimonial {i + 1}"
            ></button>
          {/each}
        </div>
      </div>
    </div>
  </section>

  <!-- Comparison Table -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a] relative">
    <div class="max-w-4xl mx-auto">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">COMPARISON</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">Hub MCP vs Traditional</h2>
      </div>

      <div class="animate-on-scroll">
        <div class="bg-gradient-to-br from-[#141414] to-[#0d0d0d] rounded-2xl border border-[#2e2e2e] overflow-hidden">
          <div class="grid grid-cols-3 gap-2 lg:gap-4 p-3 lg:p-4 bg-[#1a1a1a] border-b border-[#2e2e2e]">
            <div class="text-neutral-500 font-medium text-xs lg:text-sm">Feature</div>
            <div class="text-[#dc7c6a] font-semibold text-xs lg:text-sm text-center">Hub MCP</div>
            <div class="text-neutral-500 font-medium text-xs lg:text-sm text-center">Traditional</div>
          </div>
          {#each comparison as row}
            <div class="grid grid-cols-3 gap-2 lg:gap-4 p-3 lg:p-4 border-b border-[#1f1f1f] last:border-0 hover:bg-[#1a1a1a] transition-colors duration-300">
              <div class="text-neutral-300 text-xs lg:text-sm">{row.feature}</div>
              <div class="text-green-400 text-xs lg:text-sm text-center font-medium">{row.mcphub}</div>
              <div class="text-neutral-500 text-xs lg:text-sm text-center">{row.traditional}</div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  </section>

  <!-- FAQ Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 relative">
    <div class="max-w-3xl mx-auto">
      <div class="text-center mb-12 lg:mb-16 animate-on-scroll">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">FAQ</span>
        <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold text-white">Questions? Covered.</h2>
      </div>

      <div class="space-y-3 lg:space-y-4 animate-on-scroll">
        {#each faqs as faq, i}
          <div class="bg-gradient-to-br from-[#141414] to-[#0d0d0d] rounded-xl border border-[#2e2e2e] overflow-hidden">
            <button
              class="w-full flex items-center justify-between p-4 lg:p-5 text-left hover:bg-[#1a1a1a] transition-colors duration-300"
              onclick={() => toggleFaq(i)}
            >
              <span class="font-semibold text-neutral-200 pr-4 text-sm lg:text-base">{faq.q}</span>
              <svg class="w-5 h-5 text-neutral-500 flex-shrink-0 transition-transform duration-300 {openFaq === i ? 'rotate-180' : ''}" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
              </svg>
            </button>
            {#if openFaq === i}
              <div class="px-4 lg:px-5 pb-4 lg:pb-5 text-neutral-400 text-sm lg:text-base leading-relaxed">{faq.a}</div>
            {/if}
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Waitlist Section -->
  <section id="waitlist" class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a] relative">
    <div class="max-w-3xl mx-auto">
      <div class="animate-on-scroll">
        <div class="relative p-8 lg:p-16 rounded-3xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] overflow-hidden">
          <div class="absolute top-0 right-0 w-64 h-64 bg-[#dc7c6a]/10 rounded-full blur-3xl"></div>
          <div class="absolute bottom-0 left-0 w-48 h-48 bg-purple-500/10 rounded-full blur-3xl"></div>

          <div class="relative z-10 text-center">
            <h2 class="text-2xl sm:text-3xl lg:text-5xl font-bold mb-4 text-white">Ready to connect?</h2>
            <p class="text-neutral-400 text-base lg:text-lg mb-8 lg:mb-10 max-w-md mx-auto">Join the waitlist and get early access to Hub MCP. Be the first to know when we launch.</p>

            <form class="flex flex-col sm:flex-row gap-3 max-w-md mx-auto">
              <input
                type="email"
                placeholder="you@email.com"
                class="flex-1 px-4 lg:px-5 py-3 lg:py-4 bg-[#0d0d0d] border border-[#2e2e2e] rounded-xl focus:outline-none focus:border-[#dc7c6a] focus:ring-2 focus:ring-[#dc7c6a]/20 transition-all duration-300 placeholder:text-neutral-600 font-mono text-sm"
              />
              <button type="submit" class="relative px-6 lg:px-8 py-3 lg:py-4 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#f4a393] hover:to-[#dc7c6a] text-white font-semibold rounded-xl transition-all duration-300 hover:shadow-xl hover:shadow-[#dc7c6a]/30 hover:-translate-y-0.5 whitespace-nowrap overflow-hidden group">
                <span class="relative z-10">Join Waitlist</span>
                <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
              </button>
            </form>

            <p class="mt-4 lg:mt-6 text-neutral-600 text-xs lg:text-sm">No spam. Unsubscribe anytime.</p>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Contract Address Section -->
  <section class="py-16 lg:py-24 px-4 sm:px-6 lg:px-12 relative overflow-hidden bg-[#0a0a0a]">
    <div class="absolute inset-0 bg-[linear-gradient(rgba(220,124,106,0.02)_1px,transparent_1px),linear-gradient(90deg,rgba(220,124,106,0.02)_1px,transparent_1px)] bg-[size:40px_40px]"></div>

    <div class="max-w-4xl mx-auto relative">
      <div class="animate-on-scroll text-center">
        <span class="inline-flex items-center gap-2 text-[#dc7c6a] font-mono text-sm mb-4 px-3 py-1 rounded-full bg-[#dc7c6a]/10 border border-[#dc7c6a]/20">
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M13.828 10.172a4 4 0 00-5.656 0l-4 4a4 4 0 105.656 5.656l1.102-1.101m-.758-4.899a4 4 0 005.656 0l4-4a4 4 0 00-5.656-5.656l-1.1 1.1" />
          </svg>
          CONTRACT ADDRESS
        </span>

        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-white mb-4">
          Official Contract
        </h2>
        <p class="text-neutral-400 mb-8 max-w-xl mx-auto">
          Verify and copy the official Hub MCP contract address below.
        </p>

        <!-- Contract Address Box -->
        <div class="relative group">
          <div class="absolute -inset-1 bg-gradient-to-r from-[#dc7c6a]/20 via-purple-500/10 to-[#dc7c6a]/20 rounded-2xl blur-lg opacity-0 group-hover:opacity-100 transition-all duration-500"></div>
          <div class="relative p-6 rounded-2xl bg-[#141414] border border-[#2e2e2e] hover:border-[#dc7c6a]/30 transition-all duration-300">
            <div class="flex flex-col sm:flex-row items-center gap-4">
              <div class="flex-1 w-full">
                <p class="text-xs text-neutral-500 mb-2 text-left">Contract Address (Coming Soon)</p>
                <div class="flex items-center gap-3 p-4 rounded-xl bg-[#0d0d0d] border border-[#1f1f1f]">
                  <code class="text-sm sm:text-base font-mono text-[#dc7c6a] break-all select-all">
                    {contractAddress}
                  </code>
                </div>
              </div>
              <button
                onclick={copyContract}
                class="flex-shrink-0 p-4 rounded-xl bg-[#dc7c6a] hover:bg-[#c96b5a] text-white transition-all duration-300 hover:scale-105"
                aria-label="Copy contract address"
              >
                {#if contractCopied}
                  <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                  </svg>
                {:else}
                  <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
                  </svg>
                {/if}
              </button>
            </div>
          </div>
        </div>

        <!-- Token Info -->
        <div class="flex flex-wrap items-center justify-center gap-4 mt-6 text-sm">
          <div class="flex items-center gap-2 px-4 py-2 rounded-full bg-[#1a1a1a] border border-[#2e2e2e]">
            <span class="text-neutral-400">Symbol:</span>
            <span class="text-white font-medium">$HUBMCP</span>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- CTA Section -->
  <section class="py-16 lg:py-32 px-4 sm:px-6 lg:px-12 relative overflow-hidden">
    <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/10 via-purple-500/5 to-[#dc7c6a]/10"></div>
    <div class="absolute top-0 left-1/4 w-96 h-96 bg-[#dc7c6a]/20 rounded-full blur-[120px]"></div>
    <div class="absolute bottom-0 right-1/4 w-96 h-96 bg-purple-500/10 rounded-full blur-[120px]"></div>

    <div class="max-w-5xl mx-auto relative">
      <div class="animate-on-scroll">
        <div class="relative p-8 lg:p-16 rounded-3xl bg-gradient-to-br from-[#1a1a1a] to-[#0d0d0d] border border-[#2e2e2e] overflow-hidden">
          <!-- Decorative elements -->
          <div class="absolute top-0 right-0 w-72 h-72 bg-gradient-to-br from-[#dc7c6a]/20 to-purple-500/10 rounded-full blur-3xl"></div>
          <div class="absolute bottom-0 left-0 w-56 h-56 bg-gradient-to-tr from-purple-500/10 to-[#dc7c6a]/10 rounded-full blur-3xl"></div>
          <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-full h-px bg-gradient-to-r from-transparent via-[#dc7c6a]/20 to-transparent"></div>

          <div class="relative z-10 text-center">
            <!-- Badge -->
            <div class="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-[#dc7c6a]/10 border border-[#dc7c6a]/30 mb-6 lg:mb-8">
              <span class="relative flex h-2 w-2">
                <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-[#dc7c6a] opacity-75"></span>
                <span class="relative inline-flex rounded-full h-2 w-2 bg-[#dc7c6a]"></span>
              </span>
              <span class="text-[#dc7c6a] text-sm font-medium">Limited Early Access</span>
            </div>

            <h2 class="text-3xl sm:text-4xl lg:text-6xl font-black text-white mb-4 lg:mb-6">
              Start Building<br/>
              <span class="text-transparent bg-clip-text bg-gradient-to-r from-[#f4a393] via-[#dc7c6a] to-[#c96b5a]">Today.</span>
            </h2>

            <p class="text-neutral-400 text-base lg:text-xl mb-8 lg:mb-10 max-w-2xl mx-auto leading-relaxed">
              Connect your first MCP server in under 30 seconds. No credit card required. Free forever for personal use.
            </p>

            <!-- CTA Buttons -->
            <div class="flex flex-col sm:flex-row gap-4 justify-center items-center">
              <a href="#waitlist" class="relative px-8 py-4 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#f4a393] hover:to-[#dc7c6a] text-white font-bold rounded-xl transition-all duration-300 hover:shadow-2xl hover:shadow-[#dc7c6a]/40 hover:-translate-y-1 text-lg overflow-hidden group">
                <span class="relative z-10 flex items-center gap-2">
                  Get Started Free
                  <svg class="w-5 h-5 group-hover:translate-x-1 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13 7l5 5m0 0l-5 5m5-5H6" />
                  </svg>
                </span>
                <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
              </a>

              <a href="/demo" class="px-8 py-4 bg-transparent text-neutral-300 hover:text-white font-semibold rounded-xl border border-[#2e2e2e] hover:border-[#dc7c6a]/40 transition-all duration-300 hover:-translate-y-1 flex items-center gap-2 group">
                <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
                  <path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                Watch Demo
              </a>
            </div>

          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="py-12 lg:py-16 px-4 sm:px-6 lg:px-12 border-t border-[#1a1a1a]">
    <div class="max-w-7xl mx-auto">
      <div class="grid sm:grid-cols-2 lg:grid-cols-4 gap-8 lg:gap-12 mb-8 lg:mb-12">
        <div class="sm:col-span-2 lg:col-span-1">
          <div class="flex items-center gap-3 mb-4">
            <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl shadow-lg shadow-[#dc7c6a]/20" />
            <span class="font-bold text-white text-lg">Hub MCP</span>
          </div>
          <p class="text-neutral-500 text-sm">The easiest way to connect Claude to everything.</p>
        </div>

        <div>
          <h4 class="font-semibold text-white mb-4">Product</h4>
          <ul class="space-y-2 text-sm text-neutral-500">
            <li><a href="/integrations" class="hover:text-[#dc7c6a] transition-colors">Integrations</a></li>
            <li><a href="/pricing" class="hover:text-[#dc7c6a] transition-colors">Pricing</a></li>
            <li><a href="/how-it-works" class="hover:text-[#dc7c6a] transition-colors">How It Works</a></li>
            <li><a href="/demo" class="hover:text-[#dc7c6a] transition-colors">Demo</a></li>
          </ul>
        </div>

        <div>
          <h4 class="font-semibold text-white mb-4">Company</h4>
          <ul class="space-y-2 text-sm text-neutral-500">
            <li><a href="/about" class="hover:text-[#dc7c6a] transition-colors">About</a></li>
            <li><a href="#" class="hover:text-[#dc7c6a] transition-colors">Blog</a></li>
            <li><a href="#" class="hover:text-[#dc7c6a] transition-colors">Careers</a></li>
            <li><a href="mailto:hello@mcphub.io" class="hover:text-[#dc7c6a] transition-colors">Contact</a></li>
          </ul>
        </div>

        <div>
          <h4 class="font-semibold text-white mb-4">Connect</h4>
          <div class="flex gap-4">
            <a href="https://x.com/hub_mcp" target="_blank" rel="noopener noreferrer" aria-label="Follow us on X" class="w-10 h-10 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e] flex items-center justify-center text-neutral-500 hover:text-[#dc7c6a] hover:border-[#dc7c6a]/40 transition-all">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-5.214-6.817L4.99 21.75H1.68l7.73-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z"/></svg>
            </a>
          </div>
        </div>
      </div>

      <div class="pt-6 lg:pt-8 border-t border-[#1f1f1f] flex flex-col sm:flex-row items-center justify-between gap-4">
        <p class="text-xs lg:text-sm text-neutral-600">&copy; {new Date().getFullYear()} Hub MCP. All rights reserved.</p>
        <div class="flex gap-6 text-xs lg:text-sm text-neutral-600">
          <a href="#" class="hover:text-[#dc7c6a] transition-colors">Privacy</a>
          <a href="#" class="hover:text-[#dc7c6a] transition-colors">Terms</a>
        </div>
      </div>
    </div>
  </footer>
</div>

<style>
  .bg-grid-pattern {
    background-image:
      linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
    background-size: 60px 60px;
  }

  .nav-scrolled {
    background: rgba(10, 10, 10, 0.9);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  }

  @keyframes toast-in {
    from {
      opacity: 0;
      transform: translateX(-50%) translateY(20px) scale(0.95);
    }
    to {
      opacity: 1;
      transform: translateX(-50%) translateY(0) scale(1);
    }
  }

  .animate-toast-in {
    animation: toast-in 0.3s ease-out forwards;
  }

  @keyframes fade-in-up {
    from { opacity: 0; transform: translateY(30px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes fade-in {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes float-slow {
    0%, 100% { transform: translate(0, 0) rotate(0deg); }
    33% { transform: translate(30px, -30px) rotate(5deg); }
    66% { transform: translate(-20px, 20px) rotate(-5deg); }
  }

  @keyframes float-medium {
    0%, 100% { transform: translate(0, 0); }
    50% { transform: translate(-40px, -40px); }
  }

  @keyframes float-fast {
    0%, 100% { transform: translate(0, 0); }
    25% { transform: translate(20px, -20px); }
    50% { transform: translate(0, -40px); }
    75% { transform: translate(-20px, -20px); }
  }

  @keyframes float-particle {
    0%, 100% { transform: translate(0, 0) scale(1); opacity: 0.2; }
    50% { transform: translate(-10px, -30px) scale(1.2); opacity: 0.4; }
  }

  @keyframes gradient {
    0%, 100% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
  }

  @keyframes scroll-float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }

  .animate-fade-in-up { animation: fade-in-up 0.8s ease-out forwards; }
  .animate-fade-in { animation: fade-in 0.8s ease-out forwards; }
  .animate-float-slow { animation: float-slow 20s ease-in-out infinite; }
  .animate-float-medium { animation: float-medium 15s ease-in-out infinite; }
  .animate-float-fast { animation: float-fast 10s ease-in-out infinite; }
  .animate-gradient { background-size: 200% 200%; animation: gradient 3s ease infinite; }

  .animate-on-scroll { opacity: 0; transform: translateY(32px); }
  .animate-on-scroll:global(.animate-in) { opacity: 1; transform: translateY(0); transition: all 0.7s ease-out; }

  .scroll-indicator { animation: fade-in 1s ease-out forwards, scroll-float 2s ease-in-out infinite; animation-delay: 1s, 1.5s; }

  :global(html) { scroll-behavior: smooth; }

  /* Custom scrollbar */
  ::-webkit-scrollbar { width: 6px; }
  ::-webkit-scrollbar-track { background: #0d0d0d; }
  ::-webkit-scrollbar-thumb { background: #2e2e2e; border-radius: 3px; }
  ::-webkit-scrollbar-thumb:hover { background: #3e3e3e; }
</style>
