<script>
  import { onMount } from 'svelte';

  let mounted = $state(false);
  let scrollY = $state(0);
  let activeTab = $state('claude-desktop');
  let mobileMenuOpen = $state(false);
  let mouseX = $state(0);
  let mouseY = $state(0);
  let particles = $state([]);

  const steps = [
    {
      number: '01',
      title: 'Browse the Catalog',
      desc: 'Explore our catalog of 200+ MCP integrations. Each integration is categorized, searchable, and includes detailed documentation about what it can do.',
      details: [
        'Search by name, category, or use case',
        'Filter by availability status',
        'Read detailed capability descriptions',
        'View prompt templates and examples'
      ],
      visual: 'browse',
      gradient: 'from-blue-500 to-cyan-500'
    },
    {
      number: '02',
      title: 'Click Connect',
      desc: 'Found what you need? Click the connect button. You\'ll be redirected to authorize with OAuth - the same secure process you use when signing into apps with Google or GitHub.',
      details: [
        'One-click connection initiation',
        'Secure OAuth 2.0 authorization',
        'We never see or store your passwords',
        'Granular permission controls'
      ],
      visual: 'connect',
      gradient: 'from-purple-500 to-pink-500'
    },
    {
      number: '03',
      title: 'Copy Your MCP URL',
      desc: 'Once connected, you\'ll get a unique MCP URL. This URL is your personal endpoint that connects Claude to your authorized service. Simply copy it.',
      details: [
        'Unique URL per connection',
        'Encrypted and secure',
        'Works with any MCP client',
        'Revocable at any time'
      ],
      visual: 'copy',
      gradient: 'from-orange-500 to-red-500'
    },
    {
      number: '04',
      title: 'Add to Claude Desktop',
      desc: 'Paste your MCP URL into Claude Desktop settings. No JSON editing, no config files. Just paste and save. Claude will automatically discover your new integration.',
      details: [
        'Simple paste-and-save',
        'Automatic capability discovery',
        'No terminal commands needed',
        'Works immediately'
      ],
      visual: 'add',
      gradient: 'from-green-500 to-emerald-500'
    },
    {
      number: '05',
      title: 'Start Using AI',
      desc: 'That\'s it! Go back to Claude and start asking questions. "Summarize my unread emails." "Create a new Notion page." "Show me my GitHub PRs." Claude now has real-time access.',
      details: [
        'Natural language queries',
        'Real-time data access',
        'Context-aware responses',
        'Unlimited possibilities'
      ],
      visual: 'use',
      gradient: 'from-[#dc7c6a] to-[#f4a393]'
    }
  ];

  const useCases = [
    {
      title: 'Email Management',
      desc: 'Ask Claude to summarize your inbox, draft responses, find specific emails, or organize your messages. Works with Gmail, Outlook, and more.',
      examples: [
        '"Summarize the 5 most important emails I received today"',
        '"Draft a reply to John\'s project proposal"',
        '"Find all emails from last week about the budget"'
      ],
      icon: 'email',
      gradient: 'from-red-500/20 to-orange-500/20'
    },
    {
      title: 'Project Management',
      desc: 'Create tasks, update project status, generate reports, and manage your team\'s workflow through Notion, Trello, Linear, or Asana.',
      examples: [
        '"Create a new task in Linear for the auth bug"',
        '"What\'s the status of our Q4 roadmap?"',
        '"Add a meeting summary to the project doc"'
      ],
      icon: 'project',
      gradient: 'from-blue-500/20 to-cyan-500/20'
    },
    {
      title: 'Code & Development',
      desc: 'Review PRs, check CI status, manage issues, search code, and stay on top of your development workflow through GitHub or GitLab.',
      examples: [
        '"What are the open PRs that need my review?"',
        '"Create an issue for the login page bug"',
        '"Show me recent commits to the main branch"'
      ],
      icon: 'code',
      gradient: 'from-purple-500/20 to-pink-500/20'
    },
    {
      title: 'Team Communication',
      desc: 'Send Slack messages, read channel history, join meetings, and stay connected with your team without leaving Claude.',
      examples: [
        '"Send a message to #engineering about deployment"',
        '"What was discussed in #product yesterday?"',
        '"Schedule a team sync for tomorrow at 2pm"'
      ],
      icon: 'chat',
      gradient: 'from-green-500/20 to-emerald-500/20'
    },
    {
      title: 'Data & Analytics',
      desc: 'Query databases, generate reports, analyze spreadsheets, and get insights from your data using natural language.',
      examples: [
        '"What were our top 10 products last month?"',
        '"Generate a report of user signups by region"',
        '"Update the revenue forecast in Google Sheets"'
      ],
      icon: 'data',
      gradient: 'from-yellow-500/20 to-orange-500/20'
    },
    {
      title: 'File Management',
      desc: 'Search, read, and organize files across Google Drive, Dropbox, and other storage services directly through Claude.',
      examples: [
        '"Find the Q3 financial report"',
        '"Create a summary of the meeting notes folder"',
        '"Share the design specs with the team"'
      ],
      icon: 'file',
      gradient: 'from-indigo-500/20 to-purple-500/20'
    }
  ];

  const compatibleClients = [
    { name: 'Claude Desktop', desc: 'Official Claude app for Mac and Windows', status: 'supported', primary: true },
    { name: 'Claude.ai', desc: 'Web-based Claude interface', status: 'coming', primary: false },
    { name: 'VS Code Extension', desc: 'Claude in your code editor', status: 'coming', primary: false },
    { name: 'API Access', desc: 'For developers building with Claude', status: 'supported', primary: false },
  ];

  const technicalDetails = [
    {
      title: 'What happens when you connect?',
      content: 'When you click "Connect" on an integration, we initiate an OAuth 2.0 flow with the service provider (like Google or GitHub). You authorize Hub MCP to access your account with specific permissions. We receive an access token - never your password - which we securely store and use to make API calls on your behalf.'
    },
    {
      title: 'How does the MCP URL work?',
      content: 'Your MCP URL is a unique endpoint that routes to our hosted MCP server for that integration. When Claude needs data, it calls this URL, our server authenticates the request, uses your stored OAuth token to fetch the data, and returns it to Claude. All traffic is encrypted with TLS 1.3.'
    },
    {
      title: 'What data do we store?',
      content: 'We store your OAuth tokens (encrypted with AES-256), your Hub MCP account information, and basic usage logs for debugging. We do NOT store the content of your emails, documents, or any data fetched from your connected services. Data flows through our servers but is not persisted.'
    },
    {
      title: 'Can I revoke access?',
      content: 'Yes, instantly. Click "Disconnect" on any integration and we immediately delete your OAuth token. You can also revoke access from the service provider\'s settings (e.g., Google Account Security). Once revoked, Claude can no longer access that service.'
    }
  ];

  const faqs = [
    { q: 'Do I need to keep my computer running?', a: 'No! Unlike self-hosted MCP servers that require your computer to be on, Hub MCP runs in the cloud. Your integrations work 24/7, even when your computer is off.' },
    { q: 'What happens if Hub MCP goes down?', a: 'We maintain 99.9% uptime with redundant infrastructure across multiple regions. If there\'s ever an issue, your integrations will automatically reconnect once we\'re back online.' },
    { q: 'Can I use multiple integrations at once?', a: 'Absolutely! Connect as many integrations as you need. Claude can access all of them simultaneously, allowing powerful cross-service workflows.' },
    { q: 'Will this work with future MCP clients?', a: 'Yes! Hub MCP follows the MCP standard, so any client that supports MCP will work with our hosted servers. As new clients emerge, you won\'t need to change anything.' },
    { q: 'Is my data encrypted?', a: 'Yes. All data in transit uses TLS 1.3 encryption. OAuth tokens at rest are encrypted with AES-256. We follow industry best practices for security.' },
    { q: 'Can I use this for my business?', a: 'Yes! We offer Team and Enterprise plans with additional features like SSO, audit logs, admin controls, and dedicated support. Contact us for details.' }
  ];

  let openFaq = $state(-1);

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
  <title>How It Works - Hub MCP</title>
  <meta name="description" content="Learn how to connect Claude to your favorite apps in under 30 seconds. No terminal, no config files, no Docker. Step-by-step guide with examples." />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
</svelte:head>

<div class="min-h-screen relative overflow-hidden">
  <!-- Animated Background -->
  <div class="fixed inset-0 pointer-events-none">
    <!-- Gradient Orbs -->
    <div class="absolute top-0 left-1/4 w-[600px] h-[600px] bg-[#dc7c6a]/10 rounded-full blur-[120px] animate-float-slow"></div>
    <div class="absolute bottom-1/4 right-1/4 w-[500px] h-[500px] bg-green-500/10 rounded-full blur-[100px] animate-float-medium"></div>
    <div class="absolute top-1/2 left-1/2 w-[400px] h-[400px] bg-blue-500/8 rounded-full blur-[80px] animate-float-slow" style="animation-delay: -5s"></div>

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
          <a href="/about" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">About</a>
          <a href="/integrations" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">Integrations</a>
          <a href="/pricing" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">Pricing</a>
          <a href="/how-it-works" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-[#dc7c6a]">How It Works</a>
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
        <a href="/about" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">about</a>
        <a href="/integrations" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">integrations</a>
        <a href="/pricing" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">pricing</a>
        <a href="/how-it-works" class="text-[#dc7c6a] transition-colors duration-300">how it works</a>
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
          HOW IT WORKS
        </span>
        <h1 class="text-3xl sm:text-4xl lg:text-6xl font-black text-neutral-100 leading-tight mb-6">
          From zero to connected<br/>
          <span class="text-gradient">in 30 seconds.</span>
        </h1>
        <p class="text-lg sm:text-xl text-neutral-400 max-w-2xl mx-auto">
          No terminal. No config files. No Docker. Just click, connect, and start using Claude with your favorite apps.
        </p>
      </div>

      <!-- Quick Stats -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-3 sm:gap-6 max-w-3xl mx-auto mt-10 sm:mt-12 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
        {#each [{ value: '5', label: 'Simple Steps', color: 'from-blue-400 to-cyan-400' }, { value: '30s', label: 'Average Setup', color: 'from-[#dc7c6a] to-[#f4a393]' }, { value: '0', label: 'Code Required', color: 'from-green-400 to-emerald-400' }, { value: '24/7', label: 'Always On', color: 'from-purple-400 to-pink-400' }] as stat}
          <div class="relative group">
            <div class="absolute inset-0 bg-gradient-to-r {stat.color} rounded-xl opacity-0 group-hover:opacity-20 blur-xl transition-all duration-500"></div>
            <div class="relative text-center p-4 rounded-xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-300">
              <p class="text-2xl sm:text-3xl font-bold bg-gradient-to-r {stat.color} bg-clip-text text-transparent">{stat.value}</p>
              <p class="text-xs sm:text-sm text-neutral-500 mt-1">{stat.label}</p>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Steps -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-6xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">STEP BY STEP</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100">
          The complete setup guide.
        </h2>
      </div>

      {#each steps as step, i}
        <div class="animate-on-scroll transition-all duration-700 mb-16 sm:mb-24 last:mb-0">
          <div class="grid lg:grid-cols-2 gap-8 lg:gap-12 items-center {i % 2 === 1 ? 'lg:flex-row-reverse' : ''}">
            <div class="{i % 2 === 1 ? 'lg:order-2' : ''}">
              <div class="flex items-center gap-4 mb-4">
                <span class="text-4xl sm:text-5xl font-black bg-gradient-to-r {step.gradient} bg-clip-text text-transparent">{step.number}</span>
                <div class="h-px flex-1 bg-gradient-to-r {step.gradient} opacity-30"></div>
              </div>
              <h2 class="text-xl sm:text-2xl lg:text-3xl font-bold text-neutral-100 mb-4">{step.title}</h2>
              <p class="text-neutral-400 text-base sm:text-lg leading-relaxed mb-6">{step.desc}</p>
              <ul class="space-y-3">
                {#each step.details as detail}
                  <li class="flex items-center gap-3 text-neutral-300 text-sm sm:text-base">
                    <svg class="w-5 h-5 text-[#dc7c6a] flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                    </svg>
                    {detail}
                  </li>
                {/each}
              </ul>
            </div>

            <div class="{i % 2 === 1 ? 'lg:order-1' : ''}">
              <div class="relative group">
                <div class="absolute inset-0 bg-gradient-to-r {step.gradient} rounded-2xl opacity-0 group-hover:opacity-10 blur-xl transition-all duration-500"></div>
                <div class="relative bg-[#141414]/80 backdrop-blur-sm rounded-2xl border border-[#1f1f1f] p-4 sm:p-6 lg:p-8 hover:border-[#dc7c6a]/30 transition-all duration-300">
                  {#if step.visual === 'browse'}
                    <!-- Browse Visual -->
                    <div class="space-y-3">
                      <div class="flex items-center gap-2 mb-4">
                        <div class="flex gap-1.5">
                          <div class="w-3 h-3 rounded-full bg-red-500/50"></div>
                          <div class="w-3 h-3 rounded-full bg-yellow-500/50"></div>
                          <div class="w-3 h-3 rounded-full bg-green-500/50"></div>
                        </div>
                        <div class="flex-1 h-8 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e] flex items-center px-3">
                          <svg class="w-4 h-4 text-neutral-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" /></svg>
                          <span class="text-neutral-600 text-sm ml-2">Search integrations...</span>
                        </div>
                      </div>
                      {#each ['Gmail', 'Notion', 'Slack', 'GitHub'] as app, j}
                        <div class="flex items-center gap-3 p-3 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e] {j === 0 ? 'border-[#dc7c6a]/50 bg-[#dc7c6a]/5' : ''} transition-all duration-300">
                          <div class="w-10 h-10 rounded-lg bg-gradient-to-br from-[#dc7c6a]/20 to-[#dc7c6a]/5 flex items-center justify-center">
                            <span class="text-[#dc7c6a] text-xs font-bold">{app[0]}</span>
                          </div>
                          <div class="flex-1">
                            <p class="text-sm font-medium text-neutral-200">{app}</p>
                            <p class="text-xs text-neutral-600">Productivity</p>
                          </div>
                          <span class="text-xs px-2 py-1 rounded-full bg-green-500/10 text-green-400 border border-green-500/20">Available</span>
                        </div>
                      {/each}
                    </div>
                  {:else if step.visual === 'connect'}
                    <!-- Connect Visual -->
                    <div class="text-center py-4 sm:py-6">
                      <div class="w-16 h-16 sm:w-20 sm:h-20 rounded-2xl bg-gradient-to-br from-[#dc7c6a]/20 to-[#dc7c6a]/5 flex items-center justify-center mx-auto mb-6 border border-[#dc7c6a]/30">
                        <span class="text-[#dc7c6a] text-xl sm:text-2xl font-bold">G</span>
                      </div>
                      <p class="text-neutral-200 font-medium mb-2">Connect Gmail</p>
                      <p class="text-neutral-500 text-sm mb-6">Read, send, and organize emails</p>
                      <div class="space-y-3">
                        <button class="w-full px-6 py-3 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white rounded-xl font-medium flex items-center justify-center gap-2 shadow-lg shadow-[#dc7c6a]/20">
                          <svg class="w-5 h-5" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
                            <path d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
                            <path d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
                            <path d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
                          </svg>
                          Continue with Google
                        </button>
                        <p class="text-neutral-600 text-xs">Secure OAuth 2.0 • We never see your password</p>
                      </div>
                    </div>
                  {:else if step.visual === 'copy'}
                    <!-- Copy URL Visual -->
                    <div class="space-y-4">
                      <div class="flex items-center gap-2 text-green-400 mb-4">
                        <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span class="font-medium">Successfully connected!</span>
                      </div>
                      <div class="p-4 rounded-xl bg-[#0d0d0d] border border-[#2e2e2e]">
                        <p class="text-neutral-400 text-sm mb-3">Your unique MCP URL:</p>
                        <div class="flex items-center gap-2">
                          <code class="text-xs sm:text-sm text-[#dc7c6a] flex-1 truncate font-mono">mcp://mcphub.io/gmail/u8x7k2...</code>
                          <button class="px-3 py-1.5 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white text-xs rounded-lg shadow-lg shadow-[#dc7c6a]/20">
                            Copy
                          </button>
                        </div>
                      </div>
                      <div class="flex items-start gap-2 text-neutral-500 text-xs">
                        <svg class="w-4 h-4 flex-shrink-0 mt-0.5 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                        <span>Keep this URL private. Anyone with it can access your connected account through Claude.</span>
                      </div>
                    </div>
                  {:else if step.visual === 'add'}
                    <!-- Add to Claude Visual -->
                    <div class="space-y-4">
                      <div class="flex items-center gap-2 mb-4">
                        <div class="flex gap-1.5">
                          <div class="w-3 h-3 rounded-full bg-red-500/50"></div>
                          <div class="w-3 h-3 rounded-full bg-yellow-500/50"></div>
                          <div class="w-3 h-3 rounded-full bg-green-500/50"></div>
                        </div>
                        <span class="text-neutral-500 text-xs font-mono">Claude Desktop - Settings</span>
                      </div>
                      <div class="space-y-3">
                        <div class="p-3 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                          <p class="text-neutral-400 text-xs mb-2">MCP Servers</p>
                          <div class="space-y-2">
                            <div class="flex items-center gap-2 p-2 rounded bg-[#0d0d0d] border border-[#dc7c6a]/30 bg-[#dc7c6a]/5">
                              <div class="w-2 h-2 rounded-full bg-green-400 animate-pulse"></div>
                              <span class="text-xs text-neutral-300">Gmail (Hub MCP)</span>
                              <span class="text-xs text-green-400 ml-auto">Connected</span>
                            </div>
                            <div class="flex items-center gap-2 p-2 rounded bg-[#0d0d0d] border border-[#2e2e2e] border-dashed">
                              <svg class="w-4 h-4 text-neutral-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M12 4v16m8-8H4" />
                              </svg>
                              <span class="text-xs text-neutral-600">Add MCP server...</span>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  {:else}
                    <!-- Use Visual -->
                    <div class="space-y-4">
                      <div class="flex items-start gap-3">
                        <div class="w-8 h-8 rounded-full bg-[#2e2e2e] flex items-center justify-center flex-shrink-0">
                          <span class="text-xs text-neutral-400">You</span>
                        </div>
                        <div class="flex-1 p-3 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e]">
                          <p class="text-sm text-neutral-200">"Summarize my unread emails and flag any urgent ones"</p>
                        </div>
                      </div>
                      <div class="flex items-start gap-3">
                        <div class="w-8 h-8 rounded-full bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center flex-shrink-0 shadow-lg shadow-[#dc7c6a]/20">
                          <svg class="w-4 h-4 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                            <path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/>
                          </svg>
                        </div>
                        <div class="flex-1 p-3 rounded-lg bg-[#0d0d0d] border border-[#2e2e2e]">
                          <p class="text-sm text-neutral-300 mb-2">I found 3 unread emails:</p>
                          <ul class="text-xs text-neutral-500 space-y-1.5">
                            <li class="flex items-center gap-2">
                              <span class="w-2 h-2 rounded-full bg-red-400 animate-pulse"></span>
                              <span><strong class="text-red-400">Urgent:</strong> Server downtime alert from DevOps</span>
                            </li>
                            <li class="flex items-center gap-2">
                              <span class="w-2 h-2 rounded-full bg-yellow-400"></span>
                              <span>Meeting invite from Sarah for tomorrow</span>
                            </li>
                            <li class="flex items-center gap-2">
                              <span class="w-2 h-2 rounded-full bg-green-400"></span>
                              <span>Newsletter from Product Hunt</span>
                            </li>
                          </ul>
                        </div>
                      </div>
                    </div>
                  {/if}
                </div>
              </div>
            </div>
          </div>
        </div>
      {/each}
    </div>
  </section>

  <!-- Compatible Clients -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">COMPATIBILITY</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
          Works with your favorite clients.
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          Hub MCP follows the MCP standard, so it works with any compatible client.
        </p>
      </div>

      <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6">
        {#each compatibleClients as client, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 100}ms">
            <div class="relative group h-full">
              <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/20 to-transparent rounded-2xl opacity-0 group-hover:opacity-100 blur-xl transition-all duration-500 {client.primary ? 'opacity-50' : ''}"></div>
              <div class="relative p-4 sm:p-6 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border {client.primary ? 'border-[#dc7c6a]/50' : 'border-[#1f1f1f]'} h-full hover:border-[#dc7c6a]/30 transition-all duration-300 {client.primary ? 'ring-1 ring-[#dc7c6a]/20' : ''}">
                {#if client.primary}
                  <span class="text-xs px-2 py-1 rounded-full bg-[#dc7c6a]/10 text-[#dc7c6a] font-medium mb-3 inline-block border border-[#dc7c6a]/20">Recommended</span>
                {/if}
                <h3 class="font-bold text-neutral-100 mb-2 text-sm sm:text-base">{client.name}</h3>
                <p class="text-neutral-500 text-xs sm:text-sm mb-4">{client.desc}</p>
                <span class="text-xs px-2 py-1 rounded-full {client.status === 'supported' ? 'bg-green-500/10 text-green-400 border border-green-500/20' : 'bg-yellow-500/10 text-yellow-400 border border-yellow-500/20'}">
                  {client.status === 'supported' ? 'Supported' : 'Coming Soon'}
                </span>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Use Cases -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">USE CASES</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
          What can you do with Hub MCP?
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          Here are some popular ways people are using Claude with Hub MCP integrations.
        </p>
      </div>

      <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6">
        {#each useCases as useCase, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 100}ms">
            <div class="relative group p-4 sm:p-6 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] h-full hover:border-[#dc7c6a]/30 transition-all duration-500 overflow-hidden">
              <div class="absolute inset-0 bg-gradient-to-br {useCase.gradient} opacity-0 group-hover:opacity-100 transition-all duration-500"></div>
              <div class="relative">
                <div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-[#dc7c6a]/10 flex items-center justify-center mb-4 group-hover:scale-110 transition-transform duration-300 border border-[#dc7c6a]/20">
                  {#if useCase.icon === 'email'}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M21.75 6.75v10.5a2.25 2.25 0 01-2.25 2.25h-15a2.25 2.25 0 01-2.25-2.25V6.75m19.5 0A2.25 2.25 0 0019.5 4.5h-15a2.25 2.25 0 00-2.25 2.25m19.5 0v.243a2.25 2.25 0 01-1.07 1.916l-7.5 4.615a2.25 2.25 0 01-2.36 0L3.32 8.91a2.25 2.25 0 01-1.07-1.916V6.75" />
                    </svg>
                  {:else if useCase.icon === 'project'}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12h3.75M9 15h3.75M9 18h3.75m3 .75H18a2.25 2.25 0 002.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 00-1.123-.08m-5.801 0c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75 2.25 2.25 0 00-.1-.664m-5.8 0A2.251 2.251 0 0113.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m0 0H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V9.375c0-.621-.504-1.125-1.125-1.125H8.25zM6.75 12h.008v.008H6.75V12zm0 3h.008v.008H6.75V15zm0 3h.008v.008H6.75V18z" />
                    </svg>
                  {:else if useCase.icon === 'code'}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M17.25 6.75L22.5 12l-5.25 5.25m-10.5 0L1.5 12l5.25-5.25m7.5-3l-4.5 16.5" />
                    </svg>
                  {:else if useCase.icon === 'chat'}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M20.25 8.511c.884.284 1.5 1.128 1.5 2.097v4.286c0 1.136-.847 2.1-1.98 2.193-.34.027-.68.052-1.02.072v3.091l-3-3c-1.354 0-2.694-.055-4.02-.163a2.115 2.115 0 01-.825-.242m9.345-8.334a2.126 2.126 0 00-.476-.095 48.64 48.64 0 00-8.048 0c-1.131.094-1.976 1.057-1.976 2.192v4.286c0 .837.46 1.58 1.155 1.951m9.345-8.334V6.637c0-1.621-1.152-3.026-2.76-3.235A48.455 48.455 0 0011.25 3c-2.115 0-4.198.137-6.24.402-1.608.209-2.76 1.614-2.76 3.235v6.226c0 1.621 1.152 3.026 2.76 3.235.577.075 1.157.14 1.74.194V21l4.155-4.155" />
                    </svg>
                  {:else if useCase.icon === 'data'}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 3v11.25A2.25 2.25 0 006 16.5h2.25M3.75 3h-1.5m1.5 0h16.5m0 0h1.5m-1.5 0v11.25A2.25 2.25 0 0118 16.5h-2.25m-7.5 0h7.5m-7.5 0l-1 3m8.5-3l1 3m0 0l.5 1.5m-.5-1.5h-9.5m0 0l-.5 1.5m.75-9l3-3 2.148 2.148A12.061 12.061 0 0116.5 7.605" />
                    </svg>
                  {:else}
                    <svg class="w-5 h-5 sm:w-6 sm:h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 12.75V12A2.25 2.25 0 014.5 9.75h15A2.25 2.25 0 0121.75 12v.75m-8.69-6.44l-2.12-2.12a1.5 1.5 0 00-1.061-.44H4.5A2.25 2.25 0 002.25 6v12a2.25 2.25 0 002.25 2.25h15A2.25 2.25 0 0021.75 18V9a2.25 2.25 0 00-2.25-2.25h-5.379a1.5 1.5 0 01-1.06-.44z" />
                    </svg>
                  {/if}
                </div>
                <h3 class="text-base sm:text-lg font-bold text-neutral-100 mb-2">{useCase.title}</h3>
                <p class="text-neutral-400 text-xs sm:text-sm mb-4">{useCase.desc}</p>
                <div class="space-y-2">
                  {#each useCase.examples as example}
                    <div class="p-2 rounded-lg bg-[#0d0d0d] border border-[#2e2e2e]">
                      <p class="text-xs text-neutral-500 italic">{example}</p>
                    </div>
                  {/each}
                </div>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Technical Deep Dive -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-4xl mx-auto">
      <div class="text-center mb-12 sm:mb-16 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">UNDER THE HOOD</span>
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
          Technical deep dive.
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          For those who want to understand exactly how Hub MCP works.
        </p>
      </div>

      <div class="space-y-4 sm:space-y-6">
        {#each technicalDetails as detail, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 100}ms">
            <div class="relative group p-4 sm:p-6 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-300">
              <div class="absolute inset-0 bg-gradient-to-br from-[#dc7c6a]/5 to-transparent rounded-2xl opacity-0 group-hover:opacity-100 transition-all duration-500"></div>
              <div class="relative">
                <h3 class="font-bold text-neutral-100 mb-3 text-sm sm:text-base">{detail.title}</h3>
                <p class="text-neutral-400 text-xs sm:text-sm leading-relaxed">{detail.content}</p>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <!-- Architecture Diagram -->
      <div class="mt-8 sm:mt-12 animate-on-scroll transition-all duration-700">
        <div class="relative p-6 sm:p-8 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f]">
          <h3 class="font-bold text-neutral-100 mb-6 text-center">Architecture Overview</h3>
          <div class="flex flex-col md:flex-row items-center justify-between gap-4">
            <!-- Claude -->
            <div class="text-center">
              <div class="w-14 h-14 sm:w-16 sm:h-16 rounded-2xl bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center mx-auto mb-2 shadow-lg shadow-[#dc7c6a]/20">
                <svg class="w-6 h-6 sm:w-8 sm:h-8 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/>
                </svg>
              </div>
              <p class="text-xs sm:text-sm font-medium text-neutral-200">Claude</p>
              <p class="text-xs text-neutral-500">AI Assistant</p>
            </div>
            <!-- Arrow -->
            <div class="hidden md:flex items-center gap-2 text-[#dc7c6a]">
              <div class="w-12 h-px bg-gradient-to-r from-[#dc7c6a] to-[#dc7c6a]/50"></div>
              <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
              </svg>
            </div>
            <div class="md:hidden flex flex-col items-center gap-1 text-[#dc7c6a]">
              <div class="h-6 w-px bg-gradient-to-b from-[#dc7c6a] to-[#dc7c6a]/50"></div>
              <svg class="w-4 h-4 rotate-90" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
              </svg>
            </div>
            <!-- Hub MCP -->
            <div class="text-center">
              <div class="w-14 h-14 sm:w-16 sm:h-16 rounded-2xl bg-[#1a1a1a] border border-[#dc7c6a]/30 flex items-center justify-center mx-auto mb-2 p-2">
                <img src="/logo.png" alt="Hub MCP" class="w-full h-full object-contain rounded-lg" />
              </div>
              <p class="text-xs sm:text-sm font-medium text-neutral-200">Hub MCP</p>
              <p class="text-xs text-neutral-500">MCP Server Host</p>
            </div>
            <!-- Arrow -->
            <div class="hidden md:flex items-center gap-2 text-[#dc7c6a]">
              <div class="w-12 h-px bg-gradient-to-r from-[#dc7c6a]/50 to-[#dc7c6a]"></div>
              <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
              </svg>
            </div>
            <div class="md:hidden flex flex-col items-center gap-1 text-[#dc7c6a]">
              <div class="h-6 w-px bg-gradient-to-b from-[#dc7c6a]/50 to-[#dc7c6a]"></div>
              <svg class="w-4 h-4 rotate-90" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
              </svg>
            </div>
            <!-- Services -->
            <div class="text-center">
              <div class="flex gap-2 justify-center mb-2">
                {#each ['G', 'N', 'S'] as letter}
                  <div class="w-8 h-8 sm:w-10 sm:h-10 rounded-lg bg-[#1a1a1a] border border-[#2e2e2e] flex items-center justify-center">
                    <span class="text-neutral-400 text-xs">{letter}</span>
                  </div>
                {/each}
              </div>
              <p class="text-xs sm:text-sm font-medium text-neutral-200">Your Services</p>
              <p class="text-xs text-neutral-500">Gmail, Notion, Slack, etc.</p>
            </div>
          </div>
          <div class="mt-6 pt-6 border-t border-[#2e2e2e] text-center">
            <p class="text-xs text-neutral-500">All traffic encrypted with TLS 1.3 • OAuth tokens encrypted with AES-256</p>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- FAQ -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-3xl mx-auto">
      <div class="text-center mb-8 sm:mb-12 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">FAQ</span>
        <h2 class="text-2xl sm:text-3xl font-bold text-neutral-100">
          Common questions answered.
        </h2>
      </div>

      <div class="space-y-3 sm:space-y-4">
        {#each faqs as faq, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 50}ms">
            <div class="bg-[#141414]/80 backdrop-blur-sm rounded-xl border border-[#1f1f1f] overflow-hidden hover:border-[#dc7c6a]/30 transition-all duration-300">
              <button
                class="w-full flex items-center justify-between p-4 sm:p-5 text-left hover:bg-[#1a1a1a] transition-colors"
                onclick={() => openFaq = openFaq === i ? -1 : i}
                aria-expanded={openFaq === i}
                aria-label="Toggle FAQ answer"
              >
                <span class="font-medium text-neutral-200 pr-4 text-sm sm:text-base">{faq.q}</span>
                <svg
                  class="w-5 h-5 text-[#dc7c6a] transition-transform duration-300 flex-shrink-0 {openFaq === i ? 'rotate-180' : ''}"
                  fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"
                >
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
                </svg>
              </button>
              {#if openFaq === i}
                <div class="px-4 sm:px-5 pb-4 sm:pb-5 text-neutral-400 text-sm">
                  {faq.a}
                </div>
              {/if}
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- CTA -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-3xl mx-auto text-center animate-on-scroll transition-all duration-700">
      <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
        Ready to get started?
      </h2>
      <p class="text-neutral-400 text-base sm:text-lg mb-8">
        Join the waitlist and be first to connect Claude to everything. Early adopters get 3 months free.
      </p>
      <div class="flex flex-col sm:flex-row items-center justify-center gap-4">
        <a href="/#waitlist" class="w-full sm:w-auto inline-flex items-center justify-center gap-2 px-6 py-3 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-semibold rounded-xl transition-all duration-300 hover:-translate-y-0.5 shadow-lg shadow-[#dc7c6a]/20">
          Join the Waitlist
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
        <a href="/integrations" class="w-full sm:w-auto inline-flex items-center justify-center gap-2 px-6 py-3 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 font-semibold rounded-xl border border-[#2e2e2e] transition-all duration-300">
          Browse Integrations
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

  .animate-fade-in-up { animation: fade-in-up 0.8s ease-out forwards; }
  .animate-float-slow { animation: float-slow 20s ease-in-out infinite; }
  .animate-float-medium { animation: float-medium 15s ease-in-out infinite; }

  .animate-on-scroll { opacity: 1; transform: translateY(0); transition: all 0.7s ease-out; }
  .animate-on-scroll:global(.animate-in) { opacity: 1; transform: translateY(0); }

  :global(html) { scroll-behavior: smooth; }
</style>
