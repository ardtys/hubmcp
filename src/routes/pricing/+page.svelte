<script>
  import { onMount } from 'svelte';

  let mounted = $state(false);
  let scrollY = $state(0);
  let billingCycle = $state('monthly');
  let mobileMenuOpen = $state(false);
  let mouseX = $state(0);
  let mouseY = $state(0);
  let particles = $state([]);

  const plans = [
    {
      name: 'Free',
      desc: 'Perfect for trying things out',
      price: { monthly: 0, yearly: 0 },
      features: [
        '3 MCP connections',
        'Basic prompt templates',
        'Community support',
        'Standard uptime',
        '7-day data retention',
      ],
      limitations: [
        'No workflow bundles',
        'No priority support',
      ],
      cta: 'Get Started Free',
      popular: false,
      gradient: 'from-neutral-400 to-neutral-600'
    },
    {
      name: 'Pro',
      desc: 'For power users who need more',
      price: { monthly: 12, yearly: 10 },
      features: [
        'Unlimited connections',
        'All prompt templates',
        'Workflow bundles',
        'Priority email support',
        '99.9% uptime SLA',
        'Advanced analytics',
        '30-day data retention',
        'Custom MCP URLs',
      ],
      cta: 'Start Free Trial',
      popular: true,
      gradient: 'from-[#dc7c6a] to-[#f4a393]'
    },
    {
      name: 'Team',
      desc: 'For organizations and teams',
      price: { monthly: 29, yearly: 24 },
      features: [
        'Everything in Pro',
        'Shared team connections',
        'Team workflow library',
        'Admin controls & roles',
        'Audit logs',
        'SSO (SAML/OIDC)',
        'Dedicated support',
        '90-day data retention',
        'Custom integrations',
      ],
      cta: 'Contact Sales',
      popular: false,
      gradient: 'from-purple-400 to-pink-400'
    }
  ];

  const comparisonFeatures = [
    { name: 'MCP Connections', free: '3', pro: 'Unlimited', team: 'Unlimited' },
    { name: 'Prompt Templates', free: 'Basic', pro: 'All', team: 'All + Custom' },
    { name: 'Workflow Bundles', free: false, pro: true, team: true },
    { name: 'Analytics Dashboard', free: 'Basic', pro: 'Advanced', team: 'Advanced + Team' },
    { name: 'Uptime SLA', free: 'Standard', pro: '99.9%', team: '99.99%' },
    { name: 'Data Retention', free: '7 days', pro: '30 days', team: '90 days' },
    { name: 'Support', free: 'Community', pro: 'Priority Email', team: 'Dedicated' },
    { name: 'Team Members', free: '1', pro: '1', team: 'Unlimited' },
    { name: 'Shared Connections', free: false, pro: false, team: true },
    { name: 'Admin Controls', free: false, pro: false, team: true },
    { name: 'Audit Logs', free: false, pro: false, team: true },
    { name: 'SSO (SAML/OIDC)', free: false, pro: false, team: true },
    { name: 'Custom Integrations', free: false, pro: false, team: true },
    { name: 'API Access', free: false, pro: true, team: true },
  ];

  const faqs = [
    { q: 'Can I change plans later?', a: 'Yes! You can upgrade or downgrade your plan at any time. When upgrading, you\'ll be charged the prorated difference. When downgrading, the change takes effect at the end of your billing period.' },
    { q: 'What payment methods do you accept?', a: 'We accept all major credit cards (Visa, Mastercard, American Express), PayPal, and bank transfers for annual Team and Enterprise plans.' },
    { q: 'Is there a free trial?', a: 'Yes! Pro and Team plans come with a 14-day free trial. No credit card required to start. You can cancel anytime during the trial without being charged.' },
    { q: 'What happens if I exceed my connection limit on Free?', a: 'On the Free plan, you\'ll need to disconnect an existing integration before adding a new one. We\'ll never disconnect your integrations automatically - you stay in control.' },
    { q: 'Do you offer discounts for startups or non-profits?', a: 'Yes! We offer 50% off for qualified startups (under $1M funding), non-profits, and educational institutions. Contact us with proof of eligibility.' },
    { q: 'What is a "workflow bundle"?', a: 'Workflow bundles are pre-configured sets of integrations and prompts designed for specific use cases - like "Email Management" or "Project Planning". They help you get productive faster.' },
    { q: 'Can I self-host Hub MCP?', a: 'Enterprise customers can request on-premise deployment. Contact our sales team for details on self-hosted options and pricing.' },
    { q: 'How does billing work for Team plans?', a: 'Team plans are billed per seat. You can add or remove seats at any time. New seats are prorated for the remainder of the billing period.' },
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
      { threshold: 0.1 }
    );

    document.querySelectorAll('.animate-on-scroll').forEach((el) => {
      observer.observe(el);
    });

    return () => observer.disconnect();
  });
</script>

<svelte:window bind:scrollY onmousemove={handleMouseMove} />

<svelte:head>
  <title>Pricing - Hub MCP</title>
  <meta name="description" content="Simple, transparent pricing. Start free, upgrade when you need more. Early adopters get 3 months free on any paid plan." />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
</svelte:head>

<div class="min-h-screen relative overflow-hidden">
  <!-- Animated Background -->
  <div class="fixed inset-0 pointer-events-none">
    <!-- Gradient Orbs -->
    <div class="absolute top-0 left-1/4 w-[600px] h-[600px] bg-[#dc7c6a]/10 rounded-full blur-[120px] animate-float-slow"></div>
    <div class="absolute bottom-1/4 right-1/4 w-[500px] h-[500px] bg-purple-500/10 rounded-full blur-[100px] animate-float-medium"></div>
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
          <a href="/pricing" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-[#dc7c6a]">Pricing</a>
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
        <a href="/about" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">about</a>
        <a href="/integrations" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">integrations</a>
        <a href="/pricing" class="text-[#dc7c6a] transition-colors duration-300">pricing</a>
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
  <section class="relative pt-28 sm:pt-32 pb-8 sm:pb-12 px-4 sm:px-6 lg:px-12">
    <div class="max-w-4xl mx-auto text-center relative z-10">
      <div class="{mounted ? 'animate-fade-in-up' : 'opacity-0'}">
        <span class="inline-flex items-center gap-2 text-[#dc7c6a] font-mono text-sm mb-4 px-3 py-1 rounded-full bg-[#dc7c6a]/10 border border-[#dc7c6a]/20">
          <span class="w-2 h-2 rounded-full bg-[#dc7c6a] animate-pulse"></span>
          PRICING
        </span>
        <h1 class="text-3xl sm:text-4xl lg:text-6xl font-black text-neutral-100 leading-tight mb-6">
          Simple pricing.<br/>
          <span class="text-gradient">No surprises.</span>
        </h1>
        <p class="text-lg sm:text-xl text-neutral-400 max-w-2xl mx-auto">
          Start free. Upgrade when you need more. Early adopters get <span class="text-[#dc7c6a] font-semibold">3 months free</span> on any paid plan.
        </p>
      </div>

      <!-- Billing Toggle -->
      <div class="flex items-center justify-center gap-4 mt-8 sm:mt-10 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
        <span class="text-sm {billingCycle === 'monthly' ? 'text-neutral-200' : 'text-neutral-500'}">Monthly</span>
        <button
          class="w-14 h-7 rounded-full bg-[#1f1f1f] relative transition-colors border border-[#2e2e2e] hover:border-[#dc7c6a]/30"
          onclick={() => billingCycle = billingCycle === 'monthly' ? 'yearly' : 'monthly'}
          aria-label="Toggle billing cycle"
        >
          <div class="w-5 h-5 rounded-full bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] absolute top-0.5 transition-all duration-300 shadow-lg {billingCycle === 'yearly' ? 'left-8' : 'left-1'}"></div>
        </button>
        <span class="text-sm {billingCycle === 'yearly' ? 'text-neutral-200' : 'text-neutral-500'}">
          Yearly
          <span class="text-[#dc7c6a] text-xs ml-1 font-semibold px-2 py-0.5 bg-[#dc7c6a]/10 rounded-full">Save 20%</span>
        </span>
      </div>

      <!-- Money Back Guarantee -->
      <div class="mt-6 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 300ms">
        <div class="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-green-500/10 border border-green-500/20">
          <svg class="w-4 h-4 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
          </svg>
          <span class="text-green-400 text-sm font-medium">30-day money back guarantee</span>
        </div>
      </div>
    </div>
  </section>

  <!-- Pricing Cards -->
  <section class="relative pb-16 sm:pb-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-6xl mx-auto">
      <div class="grid md:grid-cols-3 gap-4 sm:gap-6">
        {#each plans as plan, i}
          <div class="animate-on-scroll transition-all duration-700" style="transition-delay: {i * 100}ms">
            <div class="relative group h-full">
              <!-- Glow Effect -->
              <div class="absolute inset-0 bg-gradient-to-r {plan.gradient} rounded-2xl opacity-0 group-hover:opacity-20 blur-xl transition-all duration-500 {plan.popular ? 'opacity-10' : ''}"></div>

              <div class="relative p-6 sm:p-8 rounded-2xl bg-[#141414]/80 backdrop-blur-sm border {plan.popular ? 'border-[#dc7c6a]/50 ring-1 ring-[#dc7c6a]/20' : 'border-[#1f1f1f]'} h-full flex flex-col hover:border-[#dc7c6a]/30 transition-all duration-300">
                {#if plan.popular}
                  <div class="absolute -top-3 left-1/2 -translate-x-1/2 px-4 py-1 bg-gradient-to-r from-[#dc7c6a] to-[#f4a393] text-white text-xs font-bold rounded-full shadow-lg shadow-[#dc7c6a]/30">
                    Most Popular
                  </div>
                {/if}

                <div class="mb-6">
                  <h3 class="text-xl font-bold bg-gradient-to-r {plan.gradient} bg-clip-text text-transparent">{plan.name}</h3>
                  <p class="text-neutral-500 text-sm mt-1">{plan.desc}</p>
                </div>

                <div class="mb-8">
                  <div class="flex items-baseline gap-1">
                    <span class="text-4xl sm:text-5xl font-bold text-neutral-100">
                      ${plan.price[billingCycle]}
                    </span>
                    {#if plan.price[billingCycle] > 0}
                      <span class="text-neutral-500">/mo</span>
                    {/if}
                  </div>
                  {#if plan.price[billingCycle] > 0}
                    <p class="text-neutral-600 text-sm mt-1">
                      {#if billingCycle === 'yearly'}
                        Billed annually (${plan.price.yearly * 12}/yr)
                      {:else}
                        Billed monthly
                      {/if}
                    </p>
                    {#if plan.name === 'Team'}
                      <p class="text-neutral-500 text-sm mt-1">per seat</p>
                    {/if}
                  {/if}
                </div>

                <ul class="space-y-3 mb-8 flex-1">
                  {#each plan.features as feature}
                    <li class="flex items-start gap-3 text-sm text-neutral-300">
                      <svg class="w-5 h-5 text-[#dc7c6a] flex-shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                      </svg>
                      {feature}
                    </li>
                  {/each}
                  {#if plan.limitations}
                    {#each plan.limitations as limitation}
                      <li class="flex items-start gap-3 text-sm text-neutral-600">
                        <svg class="w-5 h-5 text-neutral-700 flex-shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                        </svg>
                        {limitation}
                      </li>
                    {/each}
                  {/if}
                </ul>

                <a
                  href="/#waitlist"
                  class="block w-full py-3 text-center rounded-xl font-semibold transition-all duration-300 {plan.popular ? 'bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#c96b5a] hover:to-[#dc7c6a] text-white shadow-lg shadow-[#dc7c6a]/20' : 'bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 border border-[#2e2e2e]'}"
                >
                  {plan.cta}
                </a>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- Feature Comparison Table -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12 bg-[#0a0a0a]/80 backdrop-blur-sm">
    <div class="max-w-5xl mx-auto">
      <div class="text-center mb-8 sm:mb-12 animate-on-scroll transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">COMPARE PLANS</span>
        <h2 class="text-2xl sm:text-3xl font-bold text-neutral-100">
          Feature comparison
        </h2>
      </div>

      <div class="animate-on-scroll transition-all duration-700 overflow-x-auto">
        <table class="w-full min-w-[500px]">
          <thead>
            <tr class="border-b border-[#1f1f1f]">
              <th class="text-left py-4 px-4 text-neutral-400 font-medium text-sm">Feature</th>
              <th class="text-center py-4 px-4 text-neutral-200 font-semibold">Free</th>
              <th class="text-center py-4 px-4 text-[#dc7c6a] font-semibold">Pro</th>
              <th class="text-center py-4 px-4 text-neutral-200 font-semibold">Team</th>
            </tr>
          </thead>
          <tbody>
            {#each comparisonFeatures as feature, i}
              <tr class="border-b border-[#1f1f1f] hover:bg-[#141414] transition-colors">
                <td class="py-4 px-4 text-neutral-300 text-sm">{feature.name}</td>
                <td class="py-4 px-4 text-center">
                  {#if typeof feature.free === 'boolean'}
                    {#if feature.free}
                      <svg class="w-5 h-5 text-green-400 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                      </svg>
                    {:else}
                      <svg class="w-5 h-5 text-neutral-600 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                      </svg>
                    {/if}
                  {:else}
                    <span class="text-neutral-400 text-sm">{feature.free}</span>
                  {/if}
                </td>
                <td class="py-4 px-4 text-center">
                  {#if typeof feature.pro === 'boolean'}
                    {#if feature.pro}
                      <svg class="w-5 h-5 text-green-400 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                      </svg>
                    {:else}
                      <svg class="w-5 h-5 text-neutral-600 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                      </svg>
                    {/if}
                  {:else}
                    <span class="text-neutral-200 text-sm font-medium">{feature.pro}</span>
                  {/if}
                </td>
                <td class="py-4 px-4 text-center">
                  {#if typeof feature.team === 'boolean'}
                    {#if feature.team}
                      <svg class="w-5 h-5 text-green-400 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                      </svg>
                    {:else}
                      <svg class="w-5 h-5 text-neutral-600 mx-auto" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                      </svg>
                    {/if}
                  {:else}
                    <span class="text-neutral-200 text-sm font-medium">{feature.team}</span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </section>

  <!-- Enterprise -->
  <section class="relative py-16 sm:py-20 px-4 sm:px-6 lg:px-12">
    <div class="max-w-4xl mx-auto">
      <div class="animate-on-scroll transition-all duration-700">
        <div class="relative overflow-hidden p-6 sm:p-8 lg:p-12 rounded-3xl bg-gradient-to-br from-[#dc7c6a]/10 to-transparent border border-[#dc7c6a]/30">
          <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/5 via-transparent to-[#dc7c6a]/5 animate-shimmer"></div>
          <div class="relative grid lg:grid-cols-2 gap-8 items-center">
            <div>
              <span class="text-[#dc7c6a] font-mono text-sm mb-2 block">ENTERPRISE</span>
              <h2 class="text-2xl sm:text-3xl font-bold text-neutral-100 mb-4">
                Need something custom?
              </h2>
              <p class="text-neutral-400 mb-6 text-sm sm:text-base">
                For large organizations with specific requirements. Custom integrations, dedicated support, on-premise deployment, and more.
              </p>
              <ul class="space-y-3 text-sm text-neutral-400">
                {#each ['Custom integrations built for your stack', 'On-premise / VPC deployment options', 'Dedicated success manager', 'Custom SLA & priority support', 'Volume discounts available'] as item}
                  <li class="flex items-center gap-2">
                    <svg class="w-4 h-4 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                    </svg>
                    {item}
                  </li>
                {/each}
              </ul>
            </div>
            <div class="text-center lg:text-right">
              <p class="text-neutral-500 text-sm mb-4">Starting at</p>
              <p class="text-4xl sm:text-5xl font-bold bg-gradient-to-r from-[#dc7c6a] to-[#f4a393] bg-clip-text text-transparent mb-2">Custom</p>
              <p class="text-neutral-500 text-sm mb-6">pricing based on needs</p>
              <a href="mailto:enterprise@mcphub.io" class="inline-flex items-center gap-2 px-6 py-3 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-semibold rounded-xl transition-all duration-300 shadow-lg shadow-[#dc7c6a]/20 hover:shadow-xl hover:shadow-[#dc7c6a]/30">
                Contact Sales
                <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
                </svg>
              </a>
            </div>
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
          Frequently asked questions
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
                <div class="px-4 sm:px-5 pb-4 sm:pb-5 text-neutral-400 text-sm leading-relaxed">
                  {faq.a}
                </div>
              {/if}
            </div>
          </div>
        {/each}
      </div>

      <div class="text-center mt-8 sm:mt-12 animate-on-scroll transition-all duration-700">
        <p class="text-neutral-500 mb-4">Still have questions?</p>
        <a href="mailto:hello@mcphub.io" class="inline-flex items-center gap-2 text-[#dc7c6a] hover:text-[#f4a393] font-medium transition-colors">
          Contact us
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
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
        Join the waitlist and get 3 months free on any paid plan.
      </p>
      <div class="flex flex-col sm:flex-row items-center justify-center gap-4">
        <a href="/#waitlist" class="w-full sm:w-auto inline-flex items-center justify-center gap-2 px-6 py-3 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white font-semibold rounded-xl transition-all duration-300 hover:-translate-y-0.5 shadow-lg shadow-[#dc7c6a]/20">
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
