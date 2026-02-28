<script>
  import { onMount } from 'svelte';

  let mounted = $state(false);
  let scrollY = $state(0);
  let mobileMenuOpen = $state(false);
  let mouseX = $state(0);
  let mouseY = $state(0);
  let particles = $state([]);

  const values = [
    {
      title: 'Simplicity First',
      desc: 'We believe powerful tools should be simple to use. If it requires a tutorial, we\'re not done building it.',
      icon: 'simple',
      gradient: 'from-blue-500/20 to-cyan-500/20'
    },
    {
      title: 'User Obsession',
      desc: 'Every feature we build starts with a user problem. We ship fast, listen carefully, and iterate constantly.',
      icon: 'user',
      gradient: 'from-purple-500/20 to-pink-500/20'
    },
    {
      title: 'Transparency',
      desc: 'We\'re open about how we work, what we\'re building, and where we\'re headed. No surprises, no hidden agendas.',
      icon: 'transparent',
      gradient: 'from-orange-500/20 to-red-500/20'
    },
    {
      title: 'Quality Over Quantity',
      desc: 'We\'d rather do fewer things exceptionally well than many things poorly. Every integration is hand-tested.',
      icon: 'quality',
      gradient: 'from-green-500/20 to-emerald-500/20'
    },
  ];

  const milestones = [
    { date: 'Jan 2024', title: 'Idea Born', desc: 'Frustrated with MCP setup complexity, we started building Hub MCP in a small apartment.' },
    { date: 'Mar 2024', title: 'First Prototype', desc: 'Connected our first integration: Gmail. It worked in 30 seconds. We knew we were onto something.' },
    { date: 'Jun 2024', title: 'Private Beta', desc: 'Launched to 100 early users. The feedback was overwhelming - people wanted more integrations.' },
    { date: 'Sep 2024', title: '100 Integrations', desc: 'Hit our first major milestone. Users were connecting everything from Notion to Salesforce.' },
    { date: 'Dec 2024', title: '200+ Integrations', desc: 'Supported every major productivity tool. Community started building their own MCPs.' },
    { date: '2025', title: 'Public Launch', desc: 'Opening Hub MCP to everyone. Join the waitlist to be among the first.' },
  ];

  const securityFeatures = [
    { title: 'OAuth 2.0 Only', desc: 'We never see or store your passwords. All authentication happens directly with the service provider.', icon: 'key' },
    { title: 'End-to-End Encryption', desc: 'All data in transit is encrypted with TLS 1.3. Data at rest uses AES-256 encryption.', icon: 'lock' },
    { title: 'SOC 2 Type II', desc: 'We\'re undergoing SOC 2 certification to ensure enterprise-grade security compliance.', icon: 'badge' },
    { title: 'GDPR Compliant', desc: 'Full GDPR compliance with data portability, right to deletion, and transparent data processing.', icon: 'shield' },
    { title: 'Regular Audits', desc: 'Annual third-party security audits and continuous penetration testing.', icon: 'search' },
    { title: 'Instant Revocation', desc: 'Disconnect any integration instantly. We immediately revoke all access tokens.', icon: 'power' },
  ];

  const stats = [
    { value: '200+', label: 'Integrations', color: 'from-[#dc7c6a] to-[#f4a393]' },
    { value: '50K+', label: 'Beta Users', color: 'from-blue-400 to-cyan-400' },
    { value: '99.9%', label: 'Uptime', color: 'from-green-400 to-emerald-400' },
    { value: '< 30s', label: 'Avg Setup Time', color: 'from-purple-400 to-pink-400' },
  ];

  const techStack = [
    { name: 'Infrastructure', items: ['AWS', 'Kubernetes', 'CloudFlare'] },
    { name: 'Backend', items: ['Node.js', 'TypeScript', 'PostgreSQL'] },
    { name: 'Security', items: ['OAuth 2.0', 'TLS 1.3', 'AES-256'] },
    { name: 'Monitoring', items: ['Datadog', 'PagerDuty', 'Sentry'] },
  ];

  function handleMouseMove(e) {
    mouseX = e.clientX;
    mouseY = e.clientY;
  }

  onMount(() => {
    mounted = true;

    // Generate particles
    particles = Array.from({ length: 30 }, (_, i) => ({
      id: i,
      x: Math.random() * 100,
      y: Math.random() * 100,
      size: Math.random() * 3 + 1,
      duration: Math.random() * 20 + 15,
      delay: Math.random() * 10
    }));

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

    document.querySelectorAll('.animate-on-scroll').forEach((el) => {
      observer.observe(el);
    });

    return () => observer.disconnect();
  });
</script>

<svelte:window bind:scrollY onmousemove={handleMouseMove} />

<svelte:head>
  <title>About - Hub MCP</title>
  <meta name="description" content="Learn about Hub MCP, our mission to democratize AI integrations, our team, security practices, and the story behind the easiest way to connect Claude to everything." />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
</svelte:head>

<div class="min-h-screen relative overflow-hidden">
  <!-- Animated Background -->
  <div class="fixed inset-0 pointer-events-none">
    <!-- Gradient Orbs -->
    <div class="absolute top-0 left-1/4 w-[600px] h-[600px] bg-[#dc7c6a]/10 rounded-full blur-[120px] animate-float-slow"></div>
    <div class="absolute bottom-1/4 right-1/4 w-[500px] h-[500px] bg-blue-500/10 rounded-full blur-[100px] animate-float-medium"></div>
    <div class="absolute top-1/2 left-1/2 w-[400px] h-[400px] bg-purple-500/8 rounded-full blur-[80px] animate-float-slow" style="animation-delay: -5s"></div>

    <!-- Floating Particles -->
    {#each particles as particle}
      <div
        class="absolute rounded-full bg-[#dc7c6a]/30"
        style="
          left: {particle.x}%;
          top: {particle.y}%;
          width: {particle.size}px;
          height: {particle.size}px;
          animation: float-particle {particle.duration}s ease-in-out infinite;
          animation-delay: -{particle.delay}s;
        "
      ></div>
    {/each}

    <!-- Grid Pattern -->
    <div class="absolute inset-0 bg-[linear-gradient(rgba(220,124,106,0.03)_1px,transparent_1px),linear-gradient(90deg,rgba(220,124,106,0.03)_1px,transparent_1px)] bg-[size:60px_60px]"></div>
  </div>

  <!-- Mouse Follower Glow -->
  <div
    class="fixed w-[400px] h-[400px] rounded-full pointer-events-none z-0 transition-all duration-300 ease-out hidden lg:block"
    style="
      left: {mouseX - 200}px;
      top: {mouseY - 200}px;
      background: radial-gradient(circle, rgba(220,124,106,0.08) 0%, transparent 70%);
    "
  ></div>

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

        <nav class="flex flex-col gap-6 flex-1">
          <a href="/about" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-[#dc7c6a]">About</a>
          <a href="/integrations" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">Integrations</a>
          <a href="/pricing" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">Pricing</a>
          <a href="/how-it-works" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">How It Works</a>
          <a href="/demo" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">Demo</a>
        </nav>

        <a href="/#waitlist" onclick={() => mobileMenuOpen = false} class="w-full py-4 bg-[#dc7c6a] hover:bg-[#c96b5a] text-white font-semibold rounded-xl text-center transition-all duration-300">
          Join Waitlist
        </a>
      </div>
    </div>
  {/if}

  <!-- Navigation -->
  <nav class="fixed top-0 left-0 right-0 z-50 px-4 sm:px-6 lg:px-12 py-4 sm:py-6 transition-all duration-500 {scrollY > 100 ? 'nav-scrolled' : ''}">
    <div class="max-w-7xl mx-auto flex items-center justify-between">
      <a href="/" class="flex items-center group">
        <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl group-hover:scale-110 transition-transform duration-300 shadow-lg shadow-[#dc7c6a]/20" />
      </a>

      <!-- Desktop Navigation -->
      <div class="hidden lg:flex items-center gap-8 font-mono text-sm">
        <a href="/about" class="text-[#dc7c6a] transition-colors duration-300">about</a>
        <a href="/integrations" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">integrations</a>
        <a href="/pricing" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">pricing</a>
        <a href="/how-it-works" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">how it works</a>
        <a href="/demo" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">demo</a>
        <a href="/#waitlist" class="px-4 py-2 bg-[#dc7c6a] hover:bg-[#c96b5a] text-white font-semibold rounded-lg transition-all duration-300 hover:-translate-y-0.5 shadow-lg shadow-[#dc7c6a]/20">
          Join Waitlist
        </a>
      </div>

      <!-- Mobile Menu Button -->
      <button onclick={() => mobileMenuOpen = true} class="lg:hidden p-2 text-neutral-400 hover:text-white" aria-label="Open menu">
        <svg class="w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>
  </nav>

  <!-- Hero -->
  <section class="relative pt-28 sm:pt-32 pb-16 sm:pb-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-4xl mx-auto text-center relative z-10">
      <div class="{mounted ? 'animate-fade-in-up' : 'opacity-0'}">
        <span class="inline-flex items-center gap-2 text-[#dc7c6a] font-mono text-sm mb-4 px-3 py-1 rounded-full bg-[#dc7c6a]/10 border border-[#dc7c6a]/20">
          <span class="w-2 h-2 rounded-full bg-[#dc7c6a] animate-pulse"></span>
          ABOUT MCPHUB
        </span>
        <h1 class="text-3xl sm:text-4xl lg:text-6xl font-black text-neutral-100 leading-tight mb-6">
          Making AI integrations<br/>
          <span class="text-gradient">accessible to everyone.</span>
        </h1>
        <p class="text-lg sm:text-xl text-neutral-400 max-w-2xl mx-auto leading-relaxed">
          We're on a mission to remove the technical barriers between AI and the tools you use every day. No code required.
        </p>
      </div>

      <!-- Stats -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-3 sm:gap-6 max-w-3xl mx-auto mt-12 sm:mt-16 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
        {#each stats as stat, i}
          <div class="relative group">
            <div class="absolute inset-0 bg-gradient-to-r {stat.color} rounded-2xl opacity-0 group-hover:opacity-20 blur-xl transition-all duration-500"></div>
            <div class="relative text-center p-4 sm:p-6 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-300">
              <p class="text-2xl sm:text-3xl lg:text-4xl font-bold bg-gradient-to-r {stat.color} bg-clip-text text-transparent">{stat.value}</p>
              <p class="text-xs sm:text-sm text-neutral-500 mt-2">{stat.label}</p>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- What is MCP -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-7xl mx-auto">
      <div class="grid lg:grid-cols-2 gap-8 lg:gap-16 items-center">
        <div class="animate-on-scroll transition-all duration-700">
          <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">WHAT IS MCP?</span>
          <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-6">
            Model Context Protocol explained.
          </h2>
          <p class="text-neutral-400 text-base sm:text-lg leading-relaxed mb-6">
            <strong class="text-neutral-200">Model Context Protocol (MCP)</strong> is an open standard developed by Anthropic that allows AI assistants like Claude to securely connect to external tools and data sources.
          </p>
          <p class="text-neutral-400 text-base sm:text-lg leading-relaxed mb-6">
            Think of it as a universal adapter. Before MCP, each AI integration required custom development. With MCP, there's a single protocol that any service can implement.
          </p>
          <p class="text-neutral-400 text-base sm:text-lg leading-relaxed mb-6">
            The problem? Setting up MCP requires technical knowledge: running servers, configuring OAuth, editing JSON files, and often using Docker. That's where Hub MCP comes in.
          </p>
          <div class="p-4 rounded-xl bg-gradient-to-r from-[#dc7c6a]/10 to-transparent border border-[#dc7c6a]/30">
            <p class="text-neutral-300 text-sm">
              <span class="text-[#dc7c6a] font-semibold">Hub MCP handles the complexity.</span> We run the MCP servers, manage the OAuth flows, and give you a simple URL to paste into Claude. That's it.
            </p>
          </div>
        </div>
        <div class="animate-on-scroll transition-all duration-700 delay-100">
          <div class="bg-[#141414]/80 backdrop-blur-sm rounded-3xl border border-[#1f1f1f] p-6 sm:p-8 lg:p-10 hover:border-[#dc7c6a]/30 transition-all duration-500">
            <h3 class="font-bold text-neutral-200 mb-6 text-center">How MCP Works</h3>
            <div class="space-y-4 sm:space-y-6">
              <!-- Step 1 -->
              <div class="flex items-center gap-3 sm:gap-4">
                <div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center flex-shrink-0 text-white font-bold shadow-lg shadow-[#dc7c6a]/20">1</div>
                <div class="flex-1 p-3 sm:p-4 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                  <p class="text-sm text-neutral-300">You ask Claude a question</p>
                  <p class="text-xs text-neutral-500 mt-1">"What are my unread emails?"</p>
                </div>
              </div>
              <!-- Arrow -->
              <div class="flex justify-center">
                <svg class="w-6 h-6 text-[#dc7c6a]/50" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
                </svg>
              </div>
              <!-- Step 2 -->
              <div class="flex items-center gap-3 sm:gap-4">
                <div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-[#dc7c6a]/80 flex items-center justify-center flex-shrink-0 text-white font-bold">2</div>
                <div class="flex-1 p-3 sm:p-4 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                  <p class="text-sm text-neutral-300">Claude calls your MCP server</p>
                  <p class="text-xs text-neutral-500 mt-1">Hub MCP handles the connection</p>
                </div>
              </div>
              <!-- Arrow -->
              <div class="flex justify-center">
                <svg class="w-6 h-6 text-[#dc7c6a]/50" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
                </svg>
              </div>
              <!-- Step 3 -->
              <div class="flex items-center gap-3 sm:gap-4">
                <div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-[#dc7c6a]/60 flex items-center justify-center flex-shrink-0 text-white font-bold">3</div>
                <div class="flex-1 p-3 sm:p-4 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                  <p class="text-sm text-neutral-300">Data is fetched securely</p>
                  <p class="text-xs text-neutral-500 mt-1">OAuth token used, your password safe</p>
                </div>
              </div>
              <!-- Arrow -->
              <div class="flex justify-center">
                <svg class="w-6 h-6 text-[#dc7c6a]/50" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
                </svg>
              </div>
              <!-- Step 4 -->
              <div class="flex items-center gap-3 sm:gap-4">
                <div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-[#dc7c6a]/40 flex items-center justify-center flex-shrink-0 text-white font-bold">4</div>
                <div class="flex-1 p-3 sm:p-4 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                  <p class="text-sm text-neutral-300">Claude responds with context</p>
                  <p class="text-xs text-neutral-500 mt-1">"You have 3 unread emails..."</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- The Problem We Solve -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">THE PROBLEM</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
          MCP is powerful. Setup is painful.
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          Here's what setting up a single MCP integration looks like without Hub MCP.
        </p>
      </div>

      <div class="grid lg:grid-cols-2 gap-6 sm:gap-8">
        <!-- Without Hub MCP -->
        <div class="animate-on-scroll transition-all duration-700">
          <div class="relative group h-full">
            <div class="absolute inset-0 bg-gradient-to-br from-red-500/20 to-transparent rounded-2xl opacity-0 group-hover:opacity-100 blur-xl transition-all duration-500"></div>
            <div class="relative p-6 sm:p-8 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-red-500/30 h-full">
              <div class="flex items-center gap-3 mb-6">
                <div class="w-10 h-10 rounded-xl bg-red-500/10 flex items-center justify-center">
                  <svg class="w-5 h-5 text-red-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                  </svg>
                </div>
                <h3 class="text-lg sm:text-xl font-bold text-red-400">Without Hub MCP</h3>
              </div>
              <ol class="space-y-3 sm:space-y-4 text-sm">
                {#each ['Find the MCP repository on GitHub', 'Install Node.js, npm, and dependencies', 'Create OAuth app in Google/GitHub console', 'Configure environment variables', 'Run the MCP server locally', 'Edit Claude Desktop config.json', 'Debug connection errors', 'Restart Claude Desktop'] as step, i}
                  <li class="flex items-start gap-3">
                    <span class="w-6 h-6 rounded-full bg-red-500/10 flex items-center justify-center text-red-400 flex-shrink-0 text-xs">{i + 1}</span>
                    <span class="text-neutral-400">{step}</span>
                  </li>
                {/each}
              </ol>
              <div class="mt-6 pt-6 border-t border-[#2e2e2e]">
                <p class="text-neutral-500 text-sm">Estimated time: <span class="text-red-400 font-semibold">30-60 minutes</span></p>
              </div>
            </div>
          </div>
        </div>

        <!-- With Hub MCP -->
        <div class="animate-on-scroll transition-all duration-700 delay-100">
          <div class="relative group h-full">
            <div class="absolute inset-0 bg-gradient-to-br from-green-500/20 to-transparent rounded-2xl opacity-0 group-hover:opacity-100 blur-xl transition-all duration-500"></div>
            <div class="relative p-6 sm:p-8 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-green-500/30 h-full">
              <div class="flex items-center gap-3 mb-6">
                <div class="w-10 h-10 rounded-xl bg-green-500/10 flex items-center justify-center">
                  <svg class="w-5 h-5 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                  </svg>
                </div>
                <h3 class="text-lg sm:text-xl font-bold text-green-400">With Hub MCP</h3>
              </div>
              <ol class="space-y-3 sm:space-y-4 text-sm">
                {#each ['Click "Connect" on the integration', 'Authorize with OAuth (familiar process)', 'Copy your MCP URL', 'Paste into Claude Desktop'] as step, i}
                  <li class="flex items-start gap-3">
                    <span class="w-6 h-6 rounded-full bg-green-500/10 flex items-center justify-center text-green-400 flex-shrink-0 text-xs">{i + 1}</span>
                    <span class="text-neutral-400">{step}</span>
                  </li>
                {/each}
              </ol>
              <div class="mt-6 pt-6 border-t border-[#2e2e2e]">
                <p class="text-neutral-500 text-sm">Estimated time: <span class="text-green-400 font-semibold">30 seconds</span></p>
              </div>
              <div class="mt-4 p-4 rounded-xl bg-green-500/5 border border-green-500/20">
                <p class="text-green-400 text-sm font-medium">That's 99% less time and zero technical knowledge required.</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Security -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">SECURITY & PRIVACY</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
          Your data stays yours.
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          We take security seriously. Here's how we protect your data and maintain your privacy.
        </p>
      </div>

      <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6">
        {#each securityFeatures as feature, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 50}ms">
            <div class="relative group p-6 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-500 h-full">
              <div class="absolute inset-0 bg-gradient-to-br from-[#dc7c6a]/5 to-transparent rounded-2xl opacity-0 group-hover:opacity-100 transition-all duration-500"></div>
              <div class="relative">
                <div class="w-10 h-10 rounded-lg bg-gradient-to-br from-[#dc7c6a]/20 to-[#dc7c6a]/5 flex items-center justify-center mb-4 group-hover:scale-110 transition-transform duration-300">
                  <svg class="w-5 h-5 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
                  </svg>
                </div>
                <h3 class="text-base sm:text-lg font-bold text-neutral-100 mb-2">{feature.title}</h3>
                <p class="text-neutral-400 text-sm leading-relaxed">{feature.desc}</p>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <!-- Trust badges -->
      <div class="mt-12 sm:mt-16 text-center animate-on-scroll transition-all duration-700">
        <p class="text-neutral-500 text-sm mb-6">Trusted infrastructure powered by</p>
        <div class="flex flex-wrap items-center justify-center gap-4 sm:gap-8">
          {#each techStack as stack}
            <div class="text-center">
              <p class="text-neutral-400 font-medium text-sm mb-2">{stack.name}</p>
              <div class="flex items-center gap-2 flex-wrap justify-center">
                {#each stack.items as item}
                  <span class="px-3 py-1 rounded-full bg-[#1a1a1a] border border-[#2e2e2e] text-neutral-500 text-xs">{item}</span>
                {/each}
              </div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  </section>

  <!-- Values -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">OUR VALUES</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100">
          What drives us every day.
        </h2>
      </div>

      <div class="grid sm:grid-cols-2 gap-4 sm:gap-6">
        {#each values as value, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 100}ms">
            <div class="relative group p-6 sm:p-8 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-500 h-full overflow-hidden">
              <div class="absolute inset-0 bg-gradient-to-br {value.gradient} opacity-0 group-hover:opacity-100 transition-all duration-500"></div>
              <div class="relative">
                <div class="w-12 h-12 rounded-xl bg-[#dc7c6a]/10 flex items-center justify-center mb-4 group-hover:scale-110 transition-transform duration-300">
                  {#if value.icon === 'simple'}
                    <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12a7.5 7.5 0 0015 0m-15 0a7.5 7.5 0 1115 0m-15 0H3m16.5 0H21m-1.5 0H12m-8.457 3.077l1.41-.513m14.095-5.13l1.41-.513M5.106 17.785l1.15-.964m11.49-9.642l1.149-.964M7.501 19.795l.75-1.3m7.5-12.99l.75-1.3m-6.063 16.658l.26-1.477m2.605-14.772l.26-1.477m0 17.726l-.26-1.477M10.698 4.614l-.26-1.477M16.5 19.794l-.75-1.299M7.5 4.205L12 12" />
                    </svg>
                  {:else if value.icon === 'user'}
                    <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z" />
                    </svg>
                  {:else if value.icon === 'transparent'}
                    <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z" />
                      <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                    </svg>
                  {:else}
                    <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75M21 12c0 1.268-.63 2.39-1.593 3.068a3.745 3.745 0 01-1.043 3.296 3.745 3.745 0 01-3.296 1.043A3.745 3.745 0 0112 21c-1.268 0-2.39-.63-3.068-1.593a3.746 3.746 0 01-3.296-1.043 3.746 3.746 0 01-1.043-3.296A3.745 3.745 0 013 12c0-1.268.63-2.39 1.593-3.068a3.746 3.746 0 011.043-3.296 3.746 3.746 0 013.296-1.043A3.745 3.745 0 0112 3c1.268 0 2.39.63 3.068 1.593a3.746 3.746 0 013.296 1.043 3.746 3.746 0 011.043 3.296A3.745 3.745 0 0121 12z" />
                    </svg>
                  {/if}
                </div>
                <h3 class="text-lg sm:text-xl font-bold text-neutral-100 mb-3">{value.title}</h3>
                <p class="text-neutral-400 leading-relaxed">{value.desc}</p>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Timeline -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-4xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">OUR JOURNEY</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100">
          From idea to 200+ integrations.
        </h2>
      </div>

      <div class="relative">
        <div class="absolute left-4 md:left-1/2 top-0 bottom-0 w-px bg-gradient-to-b from-[#dc7c6a] via-[#dc7c6a]/50 to-[#1f1f1f] md:-translate-x-1/2"></div>

        {#each milestones as milestone, i}
          <div class="animate-on-scroll transition-all duration-700 relative mb-8 sm:mb-12 last:mb-0" style="transition-delay: {i * 100}ms">
            <div class="flex items-start gap-6 sm:gap-8 md:gap-0 {i % 2 === 0 ? 'md:flex-row' : 'md:flex-row-reverse'}">
              <div class="hidden md:block md:w-1/2 {i % 2 === 0 ? 'pr-12 text-right' : 'pl-12'}">
                <span class="text-[#dc7c6a] font-mono text-sm">{milestone.date}</span>
                <h3 class="text-lg sm:text-xl font-bold text-neutral-100 mt-1">{milestone.title}</h3>
                <p class="text-neutral-400 mt-2 text-sm sm:text-base">{milestone.desc}</p>
              </div>

              <div class="absolute left-4 md:left-1/2 w-4 h-4 rounded-full bg-[#dc7c6a] md:-translate-x-1/2 mt-1 ring-4 ring-[#0d0d0d] shadow-lg shadow-[#dc7c6a]/30"></div>

              <div class="md:hidden pl-10">
                <span class="text-[#dc7c6a] font-mono text-sm">{milestone.date}</span>
                <h3 class="text-base sm:text-lg font-bold text-neutral-100 mt-1">{milestone.title}</h3>
                <p class="text-neutral-400 text-sm mt-2">{milestone.desc}</p>
              </div>

              <div class="hidden md:block md:w-1/2"></div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- CTA -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-3xl mx-auto text-center animate-on-scroll transition-all duration-700">
      <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
        Join us on this journey.
      </h2>
      <p class="text-neutral-400 text-base sm:text-lg mb-8">
        Be part of the future of AI integrations. Early adopters get 3 months free.
      </p>
      <div class="flex flex-col sm:flex-row items-center justify-center gap-4">
        <a href="/#waitlist" class="w-full sm:w-auto inline-flex items-center justify-center gap-2 px-6 py-3 bg-[#dc7c6a] hover:bg-[#c96b5a] text-white font-semibold rounded-xl transition-all duration-300 hover:-translate-y-0.5 shadow-lg shadow-[#dc7c6a]/20">
          Join the Waitlist
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
        <a href="/how-it-works" class="w-full sm:w-auto inline-flex items-center justify-center gap-2 px-6 py-3 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 font-semibold rounded-xl border border-[#2e2e2e] transition-all duration-300">
          See How It Works
        </a>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="relative py-8 sm:py-12 px-4 sm:px-6 lg:px-12 border-t border-[#1a1a1a]">
    <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-6">
      <div class="flex items-center gap-3">
        <img src="/logo.png" alt="Hub MCP" class="w-8 h-8 rounded-lg" />
        <span class="font-bold text-neutral-300">Hub MCP</span>
      </div>
      <div class="flex items-center gap-4 sm:gap-6 text-sm text-neutral-500 flex-wrap justify-center">
        <a href="/about" class="hover:text-neutral-300 transition-colors">About</a>
        <a href="/integrations" class="hover:text-neutral-300 transition-colors">Integrations</a>
        <a href="/pricing" class="hover:text-neutral-300 transition-colors">Pricing</a>
        <a href="mailto:hello@mcphub.io" class="hover:text-neutral-300 transition-colors">Contact</a>
      </div>
      <p class="text-sm text-neutral-600">&copy; {new Date().getFullYear()} Hub MCP. All rights reserved.</p>
    </div>
  </footer>
</div>

<style>
  .text-gradient {
    background: linear-gradient(135deg, #f4a393 0%, #dc7c6a 50%, #c96b5a 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .nav-scrolled {
    background: rgba(13, 13, 13, 0.9);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  }

  @keyframes fade-in-up {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes float-slow {
    0%, 100% { transform: translate(0, 0) scale(1); }
    33% { transform: translate(30px, -30px) scale(1.05); }
    66% { transform: translate(-20px, 20px) scale(0.95); }
  }

  @keyframes float-medium {
    0%, 100% { transform: translate(0, 0) scale(1); }
    50% { transform: translate(-40px, -20px) scale(1.1); }
  }

  @keyframes float-particle {
    0%, 100% { transform: translate(0, 0); opacity: 0.3; }
    25% { transform: translate(10px, -20px); opacity: 0.6; }
    50% { transform: translate(-5px, -40px); opacity: 0.3; }
    75% { transform: translate(15px, -20px); opacity: 0.5; }
  }

  @keyframes shimmer {
    0% { transform: translateX(-100%); }
    100% { transform: translateX(100%); }
  }

  .animate-fade-in-up { animation: fade-in-up 0.8s ease-out forwards; }
  .animate-float-slow { animation: float-slow 20s ease-in-out infinite; }
  .animate-float-medium { animation: float-medium 15s ease-in-out infinite; }
  .animate-shimmer { animation: shimmer 3s ease-in-out infinite; }

  .animate-on-scroll { opacity: 1; transform: translateY(0); transition: all 0.7s ease-out; }
  .animate-on-scroll:global(.animate-in) { opacity: 1; transform: translateY(0); }

  :global(html) { scroll-behavior: smooth; }
</style>
