<script>
  import { onMount } from 'svelte';

  let mounted = $state(false);
  let scrollY = $state(0);
  let activeDemo = $state('gmail');
  let userInput = $state('');
  let chatHistory = $state([]);
  let isConnecting = $state(false);
  let connectionProgress = $state(0);
  let showConnectionModal = $state(false);
  let selectedPromptIndex = $state(null);
  let isProcessing = $state(false);
  let showDataPreview = $state(false);
  let liveStats = $state({ apiCalls: 0, responseTime: 0, tokensUsed: 0 });
  let activeTab = $state('chat');
  let connectionStatus = $state({});
  let showToast = $state(false);
  let toastMessage = $state('');
  let mouseX = $state(0);
  let mouseY = $state(0);
  let currentStep = $state(0);

  // Floating particles
  let particles = $state([]);

  const demos = {
    gmail: {
      name: 'Gmail',
      icon: { viewBox: '0 0 24 24', path: 'M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4l-8 5-8-5V6l8 5 8-5v2z', color: '#EA4335' },
      gradient: 'from-red-500 to-orange-500',
      prompts: [
        { text: 'Show me my unread emails from today', category: 'Read' },
        { text: 'Summarize the most important emails this week', category: 'Analyze' },
        { text: 'Draft a reply to the last email from John', category: 'Write' },
        { text: 'Find all emails with attachments from this month', category: 'Search' },
      ],
      responses: {
        'Show me my unread emails from today': {
          text: `Found **5 unread emails** from today:\n\n| From | Subject | Time | Priority |\n|------|---------|------|----------|\n| Sarah Chen | Q1 Roadmap Review | 9:45 AM | High |\n| GitHub | PR #234 merged | 10:23 AM | Normal |\n| John Miller | Contract Renewal | 11:15 AM | High |\n| HR Dept | Benefits Enrollment | 2:30 PM | Normal |\n| Newsletter | Weekly Tech Digest | 4:00 PM | Low |\n\nWould you like me to summarize any of these or draft a reply?`,
          data: { emails: 5, unread: 5, high_priority: 2 }
        },
        'Summarize the most important emails this week': {
          text: `Here's your **weekly email summary**:\n\n**High Priority (3 emails)**\n1. **Contract Renewal** from John - Needs response by Friday\n2. **Q1 Budget Approval** from Finance - Approved, no action needed\n3. **Client Meeting Request** from Sarah - Scheduled for Thursday 2PM\n\n**Key Stats This Week:**\n- Total emails: 47\n- Responded: 32\n- Pending: 8`,
          data: { total: 47, responded: 32, pending: 8 }
        },
        'Draft a reply to the last email from John': {
          text: `I've drafted a reply to John's email about **Contract Renewal**:\n\n---\n**To:** john.miller@company.com\n**Subject:** Re: Contract Renewal\n\nHi John,\n\nThank you for reaching out about the contract renewal. I've reviewed the terms and I'm happy to proceed.\n\nBest regards\n---\n\n**Actions:** Send as-is | Edit draft | Schedule send`,
          data: { draft_saved: true, words: 67 }
        },
        'Find all emails with attachments from this month': {
          text: `Found **12 emails** with attachments this month:\n\n**Documents (7)**\n- Q1_Report.pdf (2.3 MB) - from Finance\n- Contract_v2.docx (156 KB) - from Legal\n- Meeting_Notes.pdf (89 KB) - from Sarah\n\n**Total attachment size:** 12.4 MB`,
          data: { attachments: 12, total_size: '12.4 MB' }
        }
      },
      capabilities: ['Read emails', 'Send emails', 'Search inbox', 'Manage labels', 'Draft responses', 'Analyze patterns']
    },
    notion: {
      name: 'Notion',
      icon: { viewBox: '0 0 24 24', path: 'M4.459 4.208c.746.606 1.026.56 2.428.466l13.215-.793c.28 0 .047-.28-.046-.326L17.86 1.968c-.42-.326-.98-.7-2.055-.607L3.01 2.295c-.466.046-.56.28-.374.466l1.823 1.447zm.793 3.08v13.904c0 .747.373 1.027 1.214.98l14.523-.84c.841-.046.935-.56.935-1.167V6.354c0-.606-.233-.933-.748-.886l-15.177.887c-.56.047-.747.327-.747.933z', color: '#000000' },
      gradient: 'from-neutral-600 to-neutral-800',
      prompts: [
        { text: 'Create a project page for Website Redesign', category: 'Create' },
        { text: 'Show me all tasks assigned to me', category: 'Query' },
        { text: 'Add a new task to the Sprint Board', category: 'Update' },
        { text: 'Search for meeting notes from last week', category: 'Search' },
      ],
      responses: {
        'Create a project page for Website Redesign': {
          text: `Created new project page: **Website Redesign**\n\n**Page Structure:**\n\`\`\`\n📁 Website Redesign\n├── 📋 Overview (Status: In Progress)\n├── ✅ Tasks (7 items)\n├── 📅 Timeline\n└── 👥 Team\n\`\`\`\n\n[Open in Notion →]`,
          data: { page_id: 'abc123', tasks: 7 }
        },
        'Show me all tasks assigned to me': {
          text: `Found **8 tasks** assigned to you:\n\n**🔴 Overdue (1)** - Review PR feedback\n**🟡 Due Today (2)** - Finalize homepage design, Update component library\n**🟢 This Week (3)** - User testing, Standup, Sprint retro\n**📅 Upcoming (2)** - Q1 planning, Design review`,
          data: { total: 8, overdue: 1, today: 2 }
        },
        'Add a new task to the Sprint Board': {
          text: `Task added to **Sprint Board**:\n\n**New Task Created:**\n- Title: New Task\n- Status: To Do\n- Sprint: Current (Sprint 14)\n- Assignee: You`,
          data: { task_id: 'task_456', sprint: 14 }
        },
        'Search for meeting notes from last week': {
          text: `Found **4 meeting notes** from last week:\n\n**Monday** - Team Standup (15 min)\n**Tuesday** - Design Review (1 hr)\n**Wednesday** - Client Call (45 min)\n**Friday** - Sprint Retro (30 min)\n\n[View all notes →]`,
          data: { notes: 4, total_duration: '2h 30min' }
        }
      },
      capabilities: ['Create pages', 'Query databases', 'Update content', 'Search workspace', 'Manage properties', 'Sync calendars']
    },
    github: {
      name: 'GitHub',
      icon: { viewBox: '0 0 24 24', path: 'M12 2C6.477 2 2 6.477 2 12c0 4.42 2.865 8.166 6.839 9.489.5.092.682-.217.682-.482 0-.237-.008-.866-.013-1.7-2.782.603-3.369-1.342-3.369-1.342-.454-1.155-1.11-1.462-1.11-1.462-.908-.62.069-.608.069-.608 1.003.07 1.531 1.03 1.531 1.03.892 1.529 2.341 1.087 2.91.831.092-.646.35-1.086.636-1.336-2.22-.253-4.555-1.11-4.555-4.943 0-1.091.39-1.984 1.029-2.683-.103-.253-.446-1.27.098-2.647 0 0 .84-.268 2.75 1.026A9.578 9.578 0 0 1 12 6.836c.85.004 1.705.114 2.504.336 1.909-1.294 2.747-1.026 2.747-1.026.546 1.377.202 2.394.1 2.647.64.699 1.028 1.592 1.028 2.683 0 3.842-2.339 4.687-4.566 4.935.359.309.678.919.678 1.852 0 1.336-.012 2.415-.012 2.743 0 .267.18.578.688.48C19.138 20.163 22 16.418 22 12c0-5.523-4.477-10-10-10z', color: '#FFFFFF' },
      gradient: 'from-purple-600 to-pink-600',
      prompts: [
        { text: 'Show open PRs that need my review', category: 'Review' },
        { text: 'Create an issue for the login bug', category: 'Create' },
        { text: 'What were the last commits on main?', category: 'History' },
        { text: 'Check CI status for my latest PR', category: 'Status' },
      ],
      responses: {
        'Show open PRs that need my review': {
          text: `Found **3 PRs** awaiting your review:\n\n**#456** Add user authentication\n\`+342 -28 lines | 8 files\`\nAuthor: @sarah-dev | Tests: ✅ Passing\n\n**#455** Fix memory leak in dashboard\n\`+15 -8 lines | 2 files\`\nAuthor: @john-smith | Priority: Critical`,
          data: { prs: 3, critical: 1 }
        },
        'Create an issue for the login bug': {
          text: `Issue created: **#789**\n\n**🐛 Login Bug Report**\nLabels: bug, authentication, priority-high\n\n**Auto-assigned:** @you\n**Milestone:** v2.4.2\n\n[View Issue →]`,
          data: { issue_number: 789, labels: 3 }
        },
        'What were the last commits on main?': {
          text: `Last **5 commits** on \`main\`:\n\n\`a1b2c3d\` fix: resolve memory leak (#455) - @john-smith\n\`e4f5g6h\` feat: add OAuth providers (#456) - @sarah-dev\n\`i7j8k9l\` chore: update dependencies - @dependabot\n\nBranch status: 5 commits ahead of origin`,
          data: { commits: 5, behind: 0, ahead: 5 }
        },
        'Check CI status for my latest PR': {
          text: `CI Status for **PR #460**:\n\n**Pipeline:** ✅ All checks passed\n\n| Check | Status | Duration |\n|-------|--------|----------|\n| Build | ✅ Pass | 2m 34s |\n| Tests | ✅ Pass | 3m 12s |\n| Coverage | ✅ 87.3% | 12s |\n\n**Ready to merge** ✨`,
          data: { checks_passed: 6, coverage: 87.3 }
        }
      },
      capabilities: ['View repos', 'Manage issues', 'Review PRs', 'Search code', 'Check CI/CD', 'Create branches']
    },
    slack: {
      name: 'Slack',
      icon: { viewBox: '0 0 24 24', path: 'M5.042 15.165a2.528 2.528 0 0 1-2.52 2.523A2.528 2.528 0 0 1 0 15.165a2.527 2.527 0 0 1 2.522-2.52h2.52v2.52zM6.313 15.165a2.527 2.527 0 0 1 2.521-2.52 2.527 2.527 0 0 1 2.521 2.52v6.313A2.528 2.528 0 0 1 8.834 24a2.528 2.528 0 0 1-2.521-2.522v-6.313zM8.834 5.042a2.528 2.528 0 0 1-2.521-2.52A2.528 2.528 0 0 1 8.834 0a2.528 2.528 0 0 1 2.521 2.522v2.52H8.834z', color: '#4A154B' },
      gradient: 'from-purple-500 to-fuchsia-500',
      prompts: [
        { text: 'Send deployment update to #engineering', category: 'Send' },
        { text: 'Summarize unread messages in #general', category: 'Read' },
        { text: 'Find messages about the API changes', category: 'Search' },
        { text: 'Create a new channel for Q1 planning', category: 'Create' },
      ],
      responses: {
        'Send deployment update to #engineering': {
          text: `Message sent to **#engineering**:\n\n🚀 **Deployment Complete**\n\n• Version: v2.4.1\n• Environment: Production\n• Status: All systems operational ✅\n\n**Delivered to:** 24 members\n**Reactions:** 🎉 5, 👍 3, 🚀 2`,
          data: { delivered: 24, reactions: 10 }
        },
        'Summarize unread messages in #general': {
          text: `**#general** - 23 unread messages\n\n**Key Highlights:**\n1. 🎉 Company all-hands moved to Friday 3PM\n2. 🍕 Free lunch tomorrow\n3. 📢 New PTO policy announcement\n\n**Mentions of you:** 2`,
          data: { unread: 23, mentions: 2 }
        },
        'Find messages about the API changes': {
          text: `Found **12 messages** about "API changes":\n\n**#engineering** (7 messages)\n- @dev-lead: "New API versioning strategy..."\n- @backend: "Breaking changes in v2..."\n\n**#product** (3 messages)\n**DMs** (2 messages)`,
          data: { results: 12, channels: 3 }
        },
        'Create a new channel for Q1 planning': {
          text: `Channel created: **#q1-planning**\n\n**Settings:**\n- Type: Private\n- Purpose: Q1 2025 Planning & Strategy\n\n**Auto-invited:** 10 members\n\n[Open Channel →]`,
          data: { members: 10, type: 'private' }
        }
      },
      capabilities: ['Send messages', 'Read channels', 'Search history', 'Create channels', 'Manage threads', 'Set reminders']
    },
    calendar: {
      name: 'Google Calendar',
      icon: { viewBox: '0 0 24 24', path: 'M19 4h-1V2h-2v2H8V2H6v2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 16H5V10h14v10zm0-12H5V6h14v2zM7 12h5v5H7v-5z', color: '#4285F4' },
      gradient: 'from-blue-500 to-cyan-500',
      prompts: [
        { text: 'Schedule a team meeting for Tuesday 2PM', category: 'Create' },
        { text: 'What meetings do I have tomorrow?', category: 'View' },
        { text: 'Find a free slot for a 1-hour meeting this week', category: 'Find' },
        { text: 'Reschedule my 3PM meeting to Thursday', category: 'Update' },
      ],
      responses: {
        'Schedule a team meeting for Tuesday 2PM': {
          text: `Meeting scheduled! 📅\n\n**Team Sync Meeting**\n- 📆 Tuesday, March 4, 2025\n- ⏰ 2:00 PM - 3:00 PM\n- 📍 Zoom (link auto-generated)\n\n**Invitees (5):** ✅ 1 accepted, ⏳ 4 pending\n\n[Edit Event →]`,
          data: { attendees: 5, accepted: 1 }
        },
        'What meetings do I have tomorrow?': {
          text: `**Tomorrow's Schedule** - March 1, 2025\n\n| Time | Meeting | Duration |\n|------|---------|----------|\n| 9:00 AM | Daily Standup | 15 min |\n| 10:30 AM | Design Review | 1 hr |\n| 2:00 PM | Client Call | 45 min |\n| 4:00 PM | 1:1 with Manager | 30 min |\n\n**Summary:** 4 meetings, 2h 30min total`,
          data: { meetings: 4, total_hours: 2.5 }
        },
        'Find a free slot for a 1-hour meeting this week': {
          text: `Found **6 available slots** this week:\n\n**Monday** - 11:00 AM, 3:00 PM\n**Wednesday** - 9:00 AM, 2:00 PM\n**Friday** - 10:00 AM, 4:00 PM\n\n**Recommendation:** Wednesday 2PM (Most attendees available)`,
          data: { slots: 6, recommended: 'Wednesday 2PM' }
        },
        'Reschedule my 3PM meeting to Thursday': {
          text: `Meeting rescheduled! 🔄\n\n**Project Review**\n~~Wednesday 3:00 PM~~ → **Thursday 3:00 PM**\n\n✅ All attendees notified\n✅ No conflicts\n\n[Undo] [Edit Event →]`,
          data: { notified: 3, conflicts: 0 }
        }
      },
      capabilities: ['View events', 'Create events', 'Update events', 'Find availability', 'Send invites', 'Set reminders']
    },
    stripe: {
      name: 'Stripe',
      icon: { viewBox: '0 0 24 24', path: 'M13.976 9.15c-2.172-.806-3.356-1.426-3.356-2.409 0-.831.683-1.305 1.901-1.305 2.227 0 4.515.858 6.09 1.631l.89-5.494C18.252.975 15.697 0 12.165 0 9.667 0 7.589.654 6.104 1.872 4.56 3.147 3.757 4.992 3.757 7.218c0 4.039 2.467 5.76 6.476 7.219 2.585.92 3.445 1.574 3.445 2.583 0 .98-.84 1.545-2.354 1.545-1.875 0-4.965-.921-6.99-2.109l-.9 5.555C5.175 22.99 8.385 24 11.714 24c2.641 0 4.843-.624 6.328-1.813 1.664-1.305 2.525-3.236 2.525-5.732 0-4.128-2.524-5.851-6.591-7.305z', color: '#635BFF' },
      gradient: 'from-indigo-500 to-purple-500',
      prompts: [
        { text: 'Show revenue analytics for this month', category: 'Analytics' },
        { text: 'List recent failed payments', category: 'Payments' },
        { text: 'Create a new subscription plan', category: 'Create' },
        { text: 'Process a refund for order #12345', category: 'Refund' },
      ],
      responses: {
        'Show revenue analytics for this month': {
          text: `**Revenue Analytics - February 2025**\n\n| Metric | Value | Change |\n|--------|-------|--------|\n| Revenue | $127,450 | +18.5% 📈 |\n| Transactions | 1,284 | +12.3% |\n| New Customers | 342 | +24.2% |\n\n**Churn Rate:** 2.1% ↓`,
          data: { revenue: 127450, growth: 18.5 }
        },
        'List recent failed payments': {
          text: `**Failed Payments** - Last 7 days\n\nFound **8 failed payments** ($2,340 total)\n\n| Customer | Amount | Reason |\n|----------|--------|--------|\n| john@... | $299 | Card declined |\n| sarah@... | $99 | Insufficient funds |\n\n**Recovery Rate:** 62.5%`,
          data: { failed: 8, amount: 2340 }
        },
        'Create a new subscription plan': {
          text: `**New Plan Created** ✅\n\n**Startup Plan**\n- Price: $49/month\n- Trial: 14 days\n- Features: 10 members, 100GB, API access\n\n**Plan ID:** \`plan_startup_49\`\n\n[Edit Plan →]`,
          data: { plan_id: 'plan_startup_49', price: 49 }
        },
        'Process a refund for order #12345': {
          text: `**Refund Processed** ✅\n\n**Order #12345**\n- Amount: $149.00 → Refunded\n- Customer: john.doe@email.com\n- Processing: 5-10 business days\n\n✅ Customer notified\n✅ Accounting updated`,
          data: { refund_amount: 149, order_id: 12345 }
        }
      },
      capabilities: ['View analytics', 'Process refunds', 'Manage subscriptions', 'Create plans', 'Handle disputes', 'Export reports']
    }
  };

  let currentDemo = $derived(demos[activeDemo]);

  // Initialize particles
  function initParticles() {
    particles = Array.from({ length: 20 }, (_, i) => ({
      id: i,
      x: Math.random() * 100,
      y: Math.random() * 100,
      size: Math.random() * 4 + 2,
      duration: Math.random() * 20 + 10,
      delay: Math.random() * 5
    }));
  }

  async function simulateConnection(serviceKey) {
    const serviceName = demos[serviceKey].name;
    showConnectionModal = true;
    isConnecting = true;
    connectionProgress = 0;

    for (let i = 0; i <= 100; i += 2) {
      connectionProgress = i;
      await new Promise(r => setTimeout(r, 25));
    }

    connectionStatus = { ...connectionStatus, [serviceKey]: true };
    isConnecting = false;

    await new Promise(r => setTimeout(r, 400));
    showConnectionModal = false;

    showToastMessage(`${serviceName} connected successfully!`);
  }

  function showToastMessage(message) {
    toastMessage = message;
    showToast = true;
    setTimeout(() => showToast = false, 3000);
  }

  async function sendMessage(text) {
    if (!text.trim() || isProcessing) return;

    const messageText = text.trim();
    userInput = '';
    selectedPromptIndex = null;

    chatHistory = [...chatHistory, { type: 'user', content: messageText }];
    isProcessing = true;
    currentStep = 2;
    liveStats = { ...liveStats, apiCalls: liveStats.apiCalls + 1 };

    await new Promise(r => setTimeout(r, 600));
    currentStep = 3;

    const response = currentDemo.responses[messageText] || {
      text: `Processing: "${messageText}"\n\nThis is a demo response. In production, Claude would execute this action on ${currentDemo.name}.`,
      data: { demo: true }
    };

    await new Promise(r => setTimeout(r, 500));

    const newResponseTime = (Math.random() * 400 + 150).toFixed(0);
    const newTokens = liveStats.tokensUsed + Math.floor(response.text.length / 4);
    liveStats = { ...liveStats, responseTime: newResponseTime, tokensUsed: newTokens };

    chatHistory = [...chatHistory, { type: 'ai', content: response.text, data: response.data }];
    isProcessing = false;
    currentStep = 4;
    showDataPreview = true;

    setTimeout(() => currentStep = 0, 1500);
  }

  function selectPrompt(prompt, index) {
    selectedPromptIndex = index;
    userInput = prompt.text;
  }

  function handleKeydown(e) {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      sendMessage(userInput);
    }
  }

  function selectService(key) {
    activeDemo = key;
    chatHistory = [];
    showDataPreview = false;
    currentStep = 0;
    selectedPromptIndex = null;
    userInput = '';

    if (!connectionStatus[key]) {
      simulateConnection(key);
    }
  }

  function clearChat() {
    chatHistory = [];
    showDataPreview = false;
    currentStep = 0;
  }

  function handleMouseMove(e) {
    mouseX = e.clientX;
    mouseY = e.clientY;
  }

  onMount(() => {
    mounted = true;
    initParticles();

    setTimeout(() => {
      if (!connectionStatus[activeDemo]) {
        simulateConnection(activeDemo);
      }
    }, 300);
  });
</script>

<svelte:window bind:scrollY onmousemove={handleMouseMove} />

<svelte:head>
  <title>Interactive Demo - Hub MCP</title>
  <meta name="description" content="Try Hub MCP live! Interactive demo showing Claude connected to Gmail, Notion, GitHub, Slack and more." />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
</svelte:head>

<!-- Animated Background -->
<div class="fixed inset-0 overflow-hidden pointer-events-none">
  <!-- Gradient Orbs -->
  <div class="absolute top-1/4 -left-32 w-96 h-96 bg-[#dc7c6a]/20 rounded-full blur-3xl animate-float-slow"></div>
  <div class="absolute top-1/2 -right-32 w-80 h-80 bg-purple-500/15 rounded-full blur-3xl animate-float-medium"></div>
  <div class="absolute bottom-1/4 left-1/3 w-72 h-72 bg-blue-500/10 rounded-full blur-3xl animate-float-fast"></div>

  <!-- Grid Pattern -->
  <div class="absolute inset-0 bg-grid-pattern opacity-5"></div>

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

  <!-- Mouse follower glow -->
  <div
    class="absolute w-96 h-96 bg-[#dc7c6a]/5 rounded-full blur-3xl transition-all duration-1000 ease-out pointer-events-none"
    style="left: {mouseX - 192}px; top: {mouseY - 192}px;"
  ></div>
</div>

<!-- Toast Notification -->
{#if showToast}
  <div class="fixed top-24 right-6 z-[100] animate-slide-in">
    <div class="flex items-center gap-3 px-5 py-3 bg-gradient-to-r from-green-500/20 to-emerald-500/20 border border-green-500/40 rounded-2xl backdrop-blur-xl shadow-lg shadow-green-500/10">
      <div class="w-8 h-8 rounded-full bg-green-500/20 flex items-center justify-center">
        <svg class="w-4 h-4 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
        </svg>
      </div>
      <span class="text-green-300 text-sm font-medium">{toastMessage}</span>
    </div>
  </div>
{/if}

<!-- Connection Modal -->
{#if showConnectionModal}
  <div class="fixed inset-0 z-[90] flex items-center justify-center bg-black/70 backdrop-blur-md">
    <div class="relative bg-gradient-to-br from-[#1a1a1a] to-[#0d0d0d] border border-[#2e2e2e] rounded-3xl p-10 max-w-md w-full mx-4 animate-scale-in overflow-hidden">
      <!-- Animated background in modal -->
      <div class="absolute inset-0 overflow-hidden rounded-3xl">
        <div class="absolute -top-20 -right-20 w-40 h-40 bg-gradient-to-br {currentDemo.gradient} opacity-20 rounded-full blur-2xl animate-pulse"></div>
        <div class="absolute -bottom-20 -left-20 w-40 h-40 bg-[#dc7c6a]/20 rounded-full blur-2xl animate-pulse" style="animation-delay: 0.5s;"></div>
      </div>

      <div class="relative text-center">
        <div class="w-20 h-20 rounded-2xl mx-auto mb-6 flex items-center justify-center bg-gradient-to-br {currentDemo.gradient} shadow-lg shadow-{currentDemo.gradient.split('-')[1]}-500/30 animate-bounce-subtle">
          <svg class="w-10 h-10" viewBox={currentDemo.icon.viewBox} fill="white">
            <path d={currentDemo.icon.path} />
          </svg>
        </div>

        <h3 class="text-2xl font-bold text-white mb-2">Connecting to {currentDemo.name}</h3>
        <p class="text-neutral-400 text-sm mb-8">Establishing secure OAuth 2.0 connection...</p>

        <div class="relative h-3 bg-[#1f1f1f] rounded-full overflow-hidden mb-6">
          <div
            class="absolute inset-y-0 left-0 bg-gradient-to-r from-[#dc7c6a] via-[#f4a393] to-[#dc7c6a] rounded-full transition-all duration-100 animate-shimmer"
            style="width: {connectionProgress}%; background-size: 200% 100%;"
          ></div>
          <!-- Glow effect -->
          <div
            class="absolute inset-y-0 left-0 bg-gradient-to-r from-[#dc7c6a] to-[#f4a393] rounded-full blur-sm opacity-50"
            style="width: {connectionProgress}%;"
          ></div>
        </div>

        <div class="flex items-center justify-center gap-3">
          <div class="w-2 h-2 rounded-full {connectionProgress < 100 ? 'bg-yellow-400 animate-pulse' : 'bg-green-400'}"></div>
          <p class="text-sm text-neutral-400">
            {#if connectionProgress < 25}
              Initializing OAuth...
            {:else if connectionProgress < 50}
              Authenticating with {currentDemo.name}...
            {:else if connectionProgress < 75}
              Fetching permissions...
            {:else if connectionProgress < 100}
              Establishing secure connection...
            {:else}
              <span class="text-green-400 font-medium">Connected successfully!</span>
            {/if}
          </p>
        </div>
      </div>
    </div>
  </div>
{/if}

<div class="min-h-screen relative">
  <!-- Navigation -->
  <nav class="fixed top-0 left-0 right-0 z-50 px-6 lg:px-12 py-6 transition-all duration-500 {scrollY > 50 ? 'nav-scrolled' : ''}">
    <div class="max-w-7xl mx-auto flex items-center justify-between">
      <a href="/" class="flex items-center group">
        <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl group-hover:scale-110 transition-transform duration-300 shadow-lg shadow-[#dc7c6a]/30" />
      </a>

      <div class="flex items-center gap-8 font-mono text-sm">
        <a href="/about" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300 hidden md:block">about</a>
        <a href="/integrations" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300 hidden md:block">integrations</a>
        <a href="/pricing" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300 hidden md:block">pricing</a>
        <a href="/demo" class="text-[#dc7c6a] transition-colors duration-300 hidden md:block">demo</a>
        <a href="/#waitlist" class="relative px-4 py-2 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#c96b5a] hover:to-[#dc7c6a] text-white font-semibold rounded-lg transition-all duration-300 hover:-translate-y-0.5 text-sm shadow-lg shadow-[#dc7c6a]/30 overflow-hidden group">
          <span class="relative z-10">Join Waitlist</span>
          <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
        </a>
      </div>
    </div>
  </nav>

  <!-- Hero -->
  <section class="pt-32 pb-8 px-6 lg:px-12 relative">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-8 {mounted ? 'animate-fade-in-up' : 'opacity-0'}">
        <div class="inline-flex items-center gap-2 px-5 py-2.5 bg-gradient-to-r from-[#dc7c6a]/20 to-purple-500/20 border border-[#dc7c6a]/30 rounded-full mb-6 backdrop-blur-sm">
          <span class="relative flex h-2.5 w-2.5">
            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-green-400 opacity-75"></span>
            <span class="relative inline-flex rounded-full h-2.5 w-2.5 bg-green-400"></span>
          </span>
          <span class="text-[#dc7c6a] font-mono text-sm font-medium">INTERACTIVE PLAYGROUND</span>
        </div>
        <h1 class="text-5xl lg:text-6xl font-black text-white leading-tight mb-6">
          Try Hub MCP <span class="text-transparent bg-clip-text bg-gradient-to-r from-[#dc7c6a] to-[#f4a393] animate-gradient">Live</span>
        </h1>
        <p class="text-xl text-neutral-400 max-w-2xl mx-auto leading-relaxed">
          Experience how Claude connects to your services. Select a service, try the prompts, and see real responses.
        </p>
      </div>
    </div>
  </section>

  <!-- Live Stats Bar -->
  <section class="pb-8 px-6 lg:px-12">
    <div class="max-w-5xl mx-auto">
      <div class="relative flex items-center justify-center gap-6 lg:gap-10 p-5 rounded-2xl bg-gradient-to-r from-[#141414]/80 to-[#1a1a1a]/80 border border-[#2e2e2e] backdrop-blur-xl overflow-hidden">
        <!-- Animated border -->
        <div class="absolute inset-0 rounded-2xl bg-gradient-to-r from-[#dc7c6a]/20 via-purple-500/20 to-[#dc7c6a]/20 animate-gradient opacity-50"></div>
        <div class="absolute inset-[1px] rounded-2xl bg-gradient-to-r from-[#141414] to-[#1a1a1a]"></div>

        <div class="relative flex items-center gap-2">
          <div class="relative flex h-3 w-3">
            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-green-400 opacity-75"></span>
            <span class="relative inline-flex rounded-full h-3 w-3 bg-green-400 shadow-lg shadow-green-400/50"></span>
          </div>
          <span class="text-sm text-neutral-300 font-medium">Live Demo</span>
        </div>

        <div class="relative h-6 w-px bg-gradient-to-b from-transparent via-[#3e3e3e] to-transparent"></div>

        <div class="relative flex items-center gap-2">
          <div class="w-8 h-8 rounded-lg bg-[#dc7c6a]/10 flex items-center justify-center">
            <svg class="w-4 h-4 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
            </svg>
          </div>
          <div class="text-sm">
            <span class="text-neutral-500">API Calls</span>
            <span class="text-white font-mono font-bold ml-2">{liveStats.apiCalls}</span>
          </div>
        </div>

        <div class="relative h-6 w-px bg-gradient-to-b from-transparent via-[#3e3e3e] to-transparent hidden sm:block"></div>

        <div class="relative flex items-center gap-2 hidden sm:flex">
          <div class="w-8 h-8 rounded-lg bg-blue-500/10 flex items-center justify-center">
            <svg class="w-4 h-4 text-blue-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
          <div class="text-sm">
            <span class="text-neutral-500">Response</span>
            <span class="text-white font-mono font-bold ml-2">{liveStats.responseTime || '--'}ms</span>
          </div>
        </div>

        <div class="relative h-6 w-px bg-gradient-to-b from-transparent via-[#3e3e3e] to-transparent hidden lg:block"></div>

        <div class="relative flex items-center gap-2 hidden lg:flex">
          <div class="w-8 h-8 rounded-lg bg-purple-500/10 flex items-center justify-center">
            <svg class="w-4 h-4 text-purple-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M7 7h.01M7 3h5c.512 0 1.024.195 1.414.586l7 7a2 2 0 010 2.828l-7 7a2 2 0 01-2.828 0l-7-7A1.994 1.994 0 013 12V7a4 4 0 014-4z" />
            </svg>
          </div>
          <div class="text-sm">
            <span class="text-neutral-500">Tokens</span>
            <span class="text-white font-mono font-bold ml-2">{liveStats.tokensUsed}</span>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Main Demo Interface -->
  <section class="pb-20 px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="grid lg:grid-cols-12 gap-6">

        <!-- Service Selector Sidebar -->
        <div class="lg:col-span-3">
          <div class="p-5 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e] sticky top-28 backdrop-blur-xl">
            <h3 class="font-bold text-white mb-4 flex items-center gap-2">
              <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] flex items-center justify-center">
                <svg class="w-4 h-4 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                </svg>
              </div>
              Services
            </h3>
            <div class="space-y-2">
              {#each Object.entries(demos) as [key, demo]}
                <button
                  class="w-full flex items-center gap-3 p-3 rounded-xl transition-all duration-300 text-left group relative overflow-hidden {activeDemo === key ? 'bg-gradient-to-r from-[#dc7c6a]/20 to-purple-500/10 border border-[#dc7c6a]/40' : 'bg-[#0d0d0d]/50 border border-transparent hover:border-[#2e2e2e] hover:bg-[#1a1a1a]'}"
                  onclick={() => selectService(key)}
                >
                  {#if activeDemo === key}
                    <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/5 to-transparent animate-pulse"></div>
                  {/if}
                  <div class="relative w-10 h-10 rounded-xl flex items-center justify-center transition-all duration-300 group-hover:scale-110 bg-gradient-to-br {demo.gradient} shadow-lg">
                    <svg class="w-5 h-5" viewBox={demo.icon.viewBox} fill="white">
                      <path d={demo.icon.path} />
                    </svg>
                  </div>
                  <div class="relative flex-1 min-w-0">
                    <p class="font-semibold text-sm text-white truncate">{demo.name}</p>
                    <p class="text-xs text-neutral-500">{demo.capabilities.length} actions</p>
                  </div>
                  <div class="relative">
                    {#if connectionStatus[key]}
                      <div class="w-3 h-3 rounded-full bg-green-400 shadow-lg shadow-green-400/50"></div>
                    {:else if activeDemo === key}
                      <div class="w-3 h-3 rounded-full bg-yellow-400 animate-pulse shadow-lg shadow-yellow-400/50"></div>
                    {:else}
                      <div class="w-3 h-3 rounded-full bg-neutral-600"></div>
                    {/if}
                  </div>
                </button>
              {/each}
            </div>

            <!-- Quick Prompts -->
            <div class="mt-6 pt-6 border-t border-[#2e2e2e]">
              <h4 class="text-sm font-semibold text-neutral-300 mb-4 flex items-center gap-2">
                <svg class="w-4 h-4 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                Try these prompts
              </h4>
              <div class="space-y-2">
                {#each currentDemo.prompts as prompt, i}
                  <button
                    class="w-full text-left p-3 rounded-xl text-xs transition-all duration-300 border {selectedPromptIndex === i ? 'bg-gradient-to-r from-[#dc7c6a]/20 to-purple-500/10 border-[#dc7c6a]/40 text-white' : 'bg-[#0d0d0d]/50 border-[#1f1f1f] text-neutral-400 hover:border-[#2e2e2e] hover:text-neutral-200 hover:bg-[#1a1a1a]'}"
                    onclick={() => selectPrompt(prompt, i)}
                  >
                    <span class="inline-block px-2 py-1 rounded-lg bg-gradient-to-r {currentDemo.gradient} text-white text-[10px] font-semibold mr-2 shadow-sm">{prompt.category}</span>
                    <span class="line-clamp-1">{prompt.text}</span>
                  </button>
                {/each}
              </div>
            </div>
          </div>
        </div>

        <!-- Chat Interface -->
        <div class="lg:col-span-6">
          <div class="relative rounded-3xl bg-gradient-to-br from-[#141414] to-[#0a0a0a] border border-[#2e2e2e] overflow-hidden flex flex-col shadow-2xl shadow-black/50" style="height: 720px;">
            <!-- Glow effects -->
            <div class="absolute top-0 left-1/2 -translate-x-1/2 w-1/2 h-px bg-gradient-to-r from-transparent via-[#dc7c6a]/50 to-transparent"></div>
            <div class="absolute -top-20 left-1/2 -translate-x-1/2 w-40 h-40 bg-[#dc7c6a]/10 rounded-full blur-3xl"></div>

            <!-- Chat Header -->
            <div class="relative flex items-center justify-between px-6 py-5 border-b border-[#1f1f1f] flex-shrink-0 bg-gradient-to-r from-[#141414] to-[#1a1a1a]">
              <div class="flex items-center gap-4">
                <div class="relative w-12 h-12 rounded-2xl flex items-center justify-center shadow-lg shadow-[#dc7c6a]/30">
                  <img src="/logo.png" alt="Hub MCP" class="w-full h-full rounded-2xl" />
                  <div class="absolute -bottom-1 -right-1 w-4 h-4 rounded-full bg-gradient-to-br {currentDemo.gradient} border-2 border-[#141414] flex items-center justify-center">
                    <svg class="w-2 h-2" viewBox={currentDemo.icon.viewBox} fill="white">
                      <path d={currentDemo.icon.path} />
                    </svg>
                  </div>
                </div>
                <div>
                  <p class="font-bold text-white text-lg">Claude + {currentDemo.name}</p>
                  <div class="flex items-center gap-2">
                    {#if connectionStatus[activeDemo]}
                      <span class="flex h-2 w-2">
                        <span class="animate-ping absolute inline-flex h-2 w-2 rounded-full bg-green-400 opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-2 w-2 bg-green-400"></span>
                      </span>
                      <span class="text-xs text-green-400 font-medium">Connected</span>
                    {:else}
                      <span class="w-2 h-2 rounded-full bg-yellow-400 animate-pulse"></span>
                      <span class="text-xs text-yellow-400">Connecting...</span>
                    {/if}
                  </div>
                </div>
              </div>
              <button
                class="p-2.5 text-neutral-500 hover:text-white hover:bg-[#2a2a2a] rounded-xl transition-all duration-300"
                onclick={clearChat}
                title="Clear chat"
                aria-label="Clear chat"
              >
                <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
              </button>
            </div>

            <!-- Progress Steps -->
            {#if currentStep > 0}
              <div class="flex items-center justify-center gap-3 py-4 px-6 bg-gradient-to-r from-[#0d0d0d] to-[#141414] border-b border-[#1f1f1f] flex-shrink-0">
                {#each ['Connect', 'Send', 'Process', 'Done'] as step, i}
                  <div class="flex items-center gap-2">
                    <div class="relative w-8 h-8 rounded-full flex items-center justify-center text-xs font-bold transition-all duration-500 {currentStep > i + 1 ? 'bg-gradient-to-br from-green-400 to-emerald-500 text-white shadow-lg shadow-green-500/30' : currentStep === i + 1 ? 'bg-gradient-to-br from-[#dc7c6a] to-[#f4a393] text-white shadow-lg shadow-[#dc7c6a]/30 animate-pulse' : 'bg-[#1f1f1f] text-neutral-500'}">
                      {#if currentStep > i + 1}
                        <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="3">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                      {:else}
                        {i + 1}
                      {/if}
                    </div>
                    <span class="text-xs font-medium {currentStep >= i + 1 ? 'text-white' : 'text-neutral-600'}">{step}</span>
                    {#if i < 3}
                      <div class="w-8 h-0.5 rounded-full transition-all duration-500 {currentStep > i + 1 ? 'bg-gradient-to-r from-green-400 to-emerald-500' : 'bg-[#1f1f1f]'}"></div>
                    {/if}
                  </div>
                {/each}
              </div>
            {/if}

            <!-- Chat Messages -->
            <div class="flex-1 overflow-y-auto p-6 space-y-5 scroll-smooth">
              <!-- Welcome Message -->
              {#if chatHistory.length === 0}
                <div class="flex flex-col items-center justify-center h-full text-center py-8">
                  <div class="relative w-24 h-24 rounded-3xl mb-6 flex items-center justify-center bg-gradient-to-br {currentDemo.gradient} shadow-2xl">
                    <svg class="w-12 h-12" viewBox={currentDemo.icon.viewBox} fill="white">
                      <path d={currentDemo.icon.path} />
                    </svg>
                    <div class="absolute inset-0 rounded-3xl bg-gradient-to-br from-white/20 to-transparent"></div>
                  </div>
                  <h3 class="text-2xl font-bold text-white mb-3">Connected to {currentDemo.name}</h3>
                  <p class="text-neutral-400 max-w-sm mb-8 leading-relaxed">
                    Try asking Claude to interact with {currentDemo.name}. Select a prompt from the sidebar or type your own.
                  </p>
                  <div class="flex flex-wrap gap-2 justify-center max-w-md">
                    {#each currentDemo.capabilities.slice(0, 4) as cap}
                      <span class="px-4 py-2 rounded-xl bg-gradient-to-br from-[#1a1a1a] to-[#141414] border border-[#2e2e2e] text-xs text-neutral-300 font-medium">{cap}</span>
                    {/each}
                  </div>
                </div>
              {/if}

              <!-- Chat Messages -->
              {#each chatHistory as message}
                {#if message.type === 'user'}
                  <div class="flex justify-end animate-slide-up">
                    <div class="max-w-[85%] p-4 rounded-2xl rounded-tr-md bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] text-white shadow-lg shadow-[#dc7c6a]/20">
                      <p class="text-sm leading-relaxed">{message.content}</p>
                    </div>
                  </div>
                {:else}
                  <div class="flex items-start gap-3 animate-slide-up">
                    <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl flex-shrink-0 shadow-lg shadow-[#dc7c6a]/20" />
                    <div class="flex-1 min-w-0">
                      <div class="p-5 rounded-2xl rounded-tl-md bg-gradient-to-br from-[#1a1a1a] to-[#141414] border border-[#2e2e2e] text-neutral-200">
                        <div class="whitespace-pre-wrap text-sm leading-relaxed markdown-content">{@html message.content.replace(/\*\*(.*?)\*\*/g, '<strong class="text-white font-semibold">$1</strong>').replace(/`([^`]+)`/g, '<code class="px-2 py-0.5 rounded-lg bg-[#2a2a2a] text-[#f4a393] text-xs font-mono">$1</code>').replace(/\n/g, '<br>')}</div>
                      </div>
                      {#if message.data}
                        <div class="mt-3 flex items-center gap-2">
                          <button
                            class="flex items-center gap-2 px-4 py-2 rounded-xl bg-gradient-to-r from-[#1a1a1a] to-[#141414] border border-[#2e2e2e] text-xs text-neutral-300 hover:text-white hover:border-[#dc7c6a]/30 transition-all duration-300 hover:shadow-lg hover:shadow-[#dc7c6a]/10"
                            onclick={() => showDataPreview = !showDataPreview}
                            aria-label="View response data"
                          >
                            <svg class="w-4 h-4 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                              <path stroke-linecap="round" stroke-linejoin="round" d="M4 7v10c0 2.21 3.582 4 8 4s8-1.79 8-4V7M4 7c0 2.21 3.582 4 8 4s8-1.79 8-4M4 7c0-2.21 3.582-4 8-4s8 1.79 8 4" />
                            </svg>
                            View Data
                          </button>
                          <button
                            class="flex items-center gap-2 px-4 py-2 rounded-xl bg-gradient-to-r from-[#1a1a1a] to-[#141414] border border-[#2e2e2e] text-xs text-neutral-300 hover:text-white hover:border-purple-500/30 transition-all duration-300"
                            aria-label="Copy response"
                          >
                            <svg class="w-4 h-4 text-purple-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                              <path stroke-linecap="round" stroke-linejoin="round" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
                            </svg>
                            Copy
                          </button>
                        </div>
                      {/if}
                    </div>
                  </div>
                {/if}
              {/each}

              <!-- Typing Indicator -->
              {#if isProcessing}
                <div class="flex items-start gap-3 animate-slide-up">
                  <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl flex-shrink-0 shadow-lg shadow-[#dc7c6a]/20" />
                  <div class="p-5 rounded-2xl rounded-tl-md bg-gradient-to-br from-[#1a1a1a] to-[#141414] border border-[#2e2e2e]">
                    <div class="flex gap-2">
                      <div class="w-2.5 h-2.5 rounded-full bg-[#dc7c6a] animate-bounce" style="animation-delay: 0ms"></div>
                      <div class="w-2.5 h-2.5 rounded-full bg-[#dc7c6a] animate-bounce" style="animation-delay: 150ms"></div>
                      <div class="w-2.5 h-2.5 rounded-full bg-[#dc7c6a] animate-bounce" style="animation-delay: 300ms"></div>
                    </div>
                  </div>
                </div>
              {/if}
            </div>

            <!-- Chat Input -->
            <div class="relative p-5 border-t border-[#1f1f1f] flex-shrink-0 bg-gradient-to-r from-[#0d0d0d] to-[#141414]">
              <div class="flex items-end gap-3">
                <div class="flex-1 relative">
                  <textarea
                    bind:value={userInput}
                    onkeydown={handleKeydown}
                    placeholder="Type your message or select a prompt..."
                    rows="1"
                    disabled={isProcessing || !connectionStatus[activeDemo]}
                    class="w-full px-5 py-4 bg-[#1a1a1a] border border-[#2e2e2e] rounded-2xl text-white placeholder:text-neutral-600 focus:outline-none focus:border-[#dc7c6a] focus:ring-2 focus:ring-[#dc7c6a]/20 resize-none transition-all duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
                    style="min-height: 56px; max-height: 120px;"
                  ></textarea>
                </div>
                <button
                  class="relative p-4 bg-gradient-to-br from-[#dc7c6a] to-[#c96b5a] hover:from-[#f4a393] hover:to-[#dc7c6a] disabled:from-[#1f1f1f] disabled:to-[#1f1f1f] disabled:cursor-not-allowed text-white rounded-2xl transition-all duration-300 hover:-translate-y-0.5 hover:shadow-lg hover:shadow-[#dc7c6a]/30 disabled:translate-y-0 disabled:shadow-none flex-shrink-0 overflow-hidden"
                  disabled={!userInput.trim() || isProcessing || !connectionStatus[activeDemo]}
                  onclick={() => sendMessage(userInput)}
                  aria-label="Send message"
                >
                  <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] hover:translate-x-[100%] transition-transform duration-700"></div>
                  {#if isProcessing}
                    <svg class="w-5 h-5 animate-spin relative z-10" fill="none" viewBox="0 0 24 24">
                      <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                      <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
                    </svg>
                  {:else}
                    <svg class="w-5 h-5 relative z-10" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" />
                    </svg>
                  {/if}
                </button>
              </div>
              <p class="text-xs text-neutral-500 mt-3 text-center">
                Press Enter to send • This is a simulated demo
              </p>
            </div>
          </div>
        </div>

        <!-- Right Panel -->
        <div class="lg:col-span-3">
          <div class="space-y-4 sticky top-28">
            <!-- Tabs -->
            <div class="flex gap-1 p-1.5 rounded-2xl bg-gradient-to-r from-[#141414] to-[#0d0d0d] border border-[#2e2e2e]">
              {#each [['chat', 'Info'], ['data', 'Data'], ['code', 'Code']] as [key, label]}
                <button
                  class="flex-1 py-2.5 px-4 rounded-xl text-sm font-semibold transition-all duration-300 {activeTab === key ? 'bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] text-white shadow-lg shadow-[#dc7c6a]/30' : 'text-neutral-400 hover:text-white hover:bg-[#1a1a1a]'}"
                  onclick={() => activeTab = key}
                >
                  {label}
                </button>
              {/each}
            </div>

            <!-- Tab Content -->
            <div class="p-5 rounded-2xl bg-gradient-to-br from-[#141414] to-[#0d0d0d] border border-[#2e2e2e]">
              {#if activeTab === 'chat'}
                <h4 class="font-bold text-white mb-4 text-sm flex items-center gap-2">
                  <svg class="w-4 h-4 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                  </svg>
                  Capabilities
                </h4>
                <div class="space-y-2.5 mb-6">
                  {#each currentDemo.capabilities as cap}
                    <div class="flex items-center gap-3 text-sm group">
                      <div class="w-6 h-6 rounded-lg bg-green-500/10 flex items-center justify-center group-hover:bg-green-500/20 transition-colors">
                        <svg class="w-3.5 h-3.5 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                      </div>
                      <span class="text-neutral-300 group-hover:text-white transition-colors">{cap}</span>
                    </div>
                  {/each}
                </div>

                <h4 class="font-bold text-white mb-4 text-sm flex items-center gap-2">
                  <svg class="w-4 h-4 text-blue-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
                  </svg>
                  Connection
                </h4>
                <div class="space-y-3 text-sm">
                  <div class="flex justify-between items-center p-3 rounded-xl bg-[#0d0d0d] border border-[#1f1f1f]">
                    <span class="text-neutral-500">Status</span>
                    <span class="flex items-center gap-2 text-green-400 font-medium">
                      <span class="w-2 h-2 rounded-full bg-green-400"></span>
                      Connected
                    </span>
                  </div>
                  <div class="flex justify-between items-center p-3 rounded-xl bg-[#0d0d0d] border border-[#1f1f1f]">
                    <span class="text-neutral-500">Auth</span>
                    <span class="text-white font-medium">OAuth 2.0</span>
                  </div>
                  <div class="flex justify-between items-center p-3 rounded-xl bg-[#0d0d0d] border border-[#1f1f1f]">
                    <span class="text-neutral-500">Scopes</span>
                    <span class="text-white font-medium">Read, Write</span>
                  </div>
                </div>
              {:else if activeTab === 'data'}
                <h4 class="font-bold text-white mb-4 text-sm flex items-center gap-2">
                  <svg class="w-4 h-4 text-purple-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4 7v10c0 2.21 3.582 4 8 4s8-1.79 8-4V7M4 7c0 2.21 3.582 4 8 4s8-1.79 8-4M4 7c0-2.21 3.582-4 8-4s8 1.79 8 4" />
                  </svg>
                  Response Data
                </h4>
                {#if chatHistory.length > 0 && chatHistory[chatHistory.length - 1].data}
                  <pre class="text-xs text-neutral-300 bg-[#0d0d0d] p-4 rounded-xl overflow-auto max-h-80 border border-[#1f1f1f] font-mono"><code>{JSON.stringify(chatHistory[chatHistory.length - 1].data, null, 2)}</code></pre>
                {:else}
                  <div class="text-center py-8">
                    <div class="w-12 h-12 rounded-xl bg-[#1a1a1a] mx-auto mb-4 flex items-center justify-center">
                      <svg class="w-6 h-6 text-neutral-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4" />
                      </svg>
                    </div>
                    <p class="text-sm text-neutral-500">No data yet.<br/>Send a message to see the response data.</p>
                  </div>
                {/if}
              {:else}
                <h4 class="font-bold text-white mb-4 text-sm flex items-center gap-2">
                  <svg class="w-4 h-4 text-cyan-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4" />
                  </svg>
                  MCP Request
                </h4>
                <pre class="text-xs text-neutral-300 bg-[#0d0d0d] p-4 rounded-xl overflow-auto max-h-80 border border-[#1f1f1f] font-mono"><code class="text-cyan-300">{`{
  "mcp_server": "${currentDemo.name.toLowerCase().replace(' ', '_')}",
  "action": "execute",
  "params": {
    "query": "${userInput || 'your_prompt_here'}",
    "user_id": "demo_user"
  },
  "auth": {
    "type": "oauth2",
    "token": "••••••••"
  }
}`}</code></pre>
              {/if}
            </div>

            <!-- Help Card -->
            <div class="relative p-5 rounded-2xl overflow-hidden border border-[#dc7c6a]/30">
              <div class="absolute inset-0 bg-gradient-to-br from-[#dc7c6a]/10 via-purple-500/5 to-transparent"></div>
              <div class="relative">
                <h4 class="font-bold text-white mb-2 text-sm">Need help?</h4>
                <p class="text-xs text-neutral-400 mb-4 leading-relaxed">
                  This is an interactive demo. Try selecting different services and prompts to see how Hub MCP works.
                </p>
                <a href="/how-it-works" class="inline-flex items-center gap-2 text-sm text-[#dc7c6a] hover:text-[#f4a393] font-semibold transition-colors group">
                  Learn more
                  <svg class="w-4 h-4 group-hover:translate-x-1 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
                  </svg>
                </a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- CTA Section -->
  <section class="relative py-24 px-6 lg:px-12 overflow-hidden">
    <div class="absolute inset-0 bg-gradient-to-b from-transparent via-[#dc7c6a]/5 to-transparent"></div>
    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[600px] h-[600px] bg-[#dc7c6a]/10 rounded-full blur-3xl"></div>

    <div class="relative max-w-3xl mx-auto text-center">
      <h2 class="text-4xl lg:text-5xl font-black text-white mb-6">
        Ready to Try the <span class="text-transparent bg-clip-text bg-gradient-to-r from-[#dc7c6a] to-[#f4a393]">Real Thing</span>?
      </h2>
      <p class="text-neutral-400 text-xl mb-10 leading-relaxed">
        Join thousands of users on the waitlist. Get early access when we launch.
      </p>
      <div class="flex flex-col sm:flex-row gap-4 justify-center">
        <a href="/#waitlist" class="relative inline-flex items-center justify-center gap-3 px-10 py-5 bg-gradient-to-r from-[#dc7c6a] to-[#c96b5a] hover:from-[#f4a393] hover:to-[#dc7c6a] text-white font-bold rounded-2xl transition-all duration-300 hover:-translate-y-1 shadow-2xl shadow-[#dc7c6a]/30 overflow-hidden group text-lg">
          <span class="relative z-10">Join the Waitlist</span>
          <svg class="w-5 h-5 relative z-10 group-hover:translate-x-1 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
          <div class="absolute inset-0 bg-gradient-to-r from-white/0 via-white/20 to-white/0 translate-x-[-100%] group-hover:translate-x-[100%] transition-transform duration-700"></div>
        </a>
        <a href="/integrations" class="inline-flex items-center justify-center gap-2 px-10 py-5 bg-[#1a1a1a] hover:bg-[#242424] text-white font-bold rounded-2xl border border-[#2e2e2e] transition-all duration-300 hover:-translate-y-1 text-lg">
          View All Integrations
        </a>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="py-12 px-6 lg:px-12 border-t border-[#1a1a1a]">
    <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-6">
      <div class="flex items-center gap-3">
        <img src="/logo.png" alt="Hub MCP" class="w-10 h-10 rounded-xl shadow-lg shadow-[#dc7c6a]/20" />
        <span class="font-bold text-white text-lg">Hub MCP</span>
      </div>
      <div class="flex items-center gap-8 text-sm text-neutral-500">
        <a href="/about" class="hover:text-white transition-colors">About</a>
        <a href="/integrations" class="hover:text-white transition-colors">Integrations</a>
        <a href="/pricing" class="hover:text-white transition-colors">Pricing</a>
        <a href="/demo" class="text-[#dc7c6a]">Demo</a>
        <a href="mailto:hello@mcphub.io" class="hover:text-white transition-colors">Contact</a>
      </div>
      <p class="text-sm text-neutral-600">&copy; {new Date().getFullYear()} Hub MCP. All rights reserved.</p>
    </div>
  </footer>
</div>

<style>
  .nav-scrolled {
    background: rgba(10, 10, 10, 0.9);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  }

  .bg-grid-pattern {
    background-image:
      linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
    background-size: 50px 50px;
  }

  @keyframes fade-in-up {
    from { opacity: 0; transform: translateY(30px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes slide-up {
    from { opacity: 0; transform: translateY(15px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes slide-in {
    from { opacity: 0; transform: translateX(30px); }
    to { opacity: 1; transform: translateX(0); }
  }

  @keyframes scale-in {
    from { opacity: 0; transform: scale(0.9); }
    to { opacity: 1; transform: scale(1); }
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
    0%, 100% { transform: translate(0, 0) scale(1); opacity: 0.3; }
    25% { transform: translate(10px, -20px) scale(1.1); opacity: 0.5; }
    50% { transform: translate(-5px, -40px) scale(1); opacity: 0.3; }
    75% { transform: translate(-15px, -20px) scale(0.9); opacity: 0.5; }
  }

  @keyframes bounce-subtle {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-5px); }
  }

  @keyframes shimmer {
    0% { background-position: -200% 0; }
    100% { background-position: 200% 0; }
  }

  @keyframes gradient {
    0%, 100% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
  }

  .animate-fade-in-up { animation: fade-in-up 0.8s ease-out forwards; }
  .animate-slide-up { animation: slide-up 0.4s ease-out forwards; }
  .animate-slide-in { animation: slide-in 0.4s ease-out forwards; }
  .animate-scale-in { animation: scale-in 0.3s ease-out forwards; }
  .animate-float-slow { animation: float-slow 20s ease-in-out infinite; }
  .animate-float-medium { animation: float-medium 15s ease-in-out infinite; }
  .animate-float-fast { animation: float-fast 10s ease-in-out infinite; }
  .animate-bounce-subtle { animation: bounce-subtle 2s ease-in-out infinite; }
  .animate-shimmer { animation: shimmer 2s linear infinite; }
  .animate-gradient {
    background-size: 200% 200%;
    animation: gradient 3s ease infinite;
  }

  :global(html) { scroll-behavior: smooth; }

  .line-clamp-1 {
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }

  pre, code {
    font-family: 'JetBrains Mono', monospace;
  }

  .markdown-content :global(strong) {
    color: #ffffff;
    font-weight: 600;
  }

  .markdown-content :global(code) {
    font-family: 'JetBrains Mono', monospace;
  }

  /* Custom scrollbar */
  ::-webkit-scrollbar {
    width: 6px;
  }

  ::-webkit-scrollbar-track {
    background: #0d0d0d;
  }

  ::-webkit-scrollbar-thumb {
    background: #2e2e2e;
    border-radius: 3px;
  }

  ::-webkit-scrollbar-thumb:hover {
    background: #3e3e3e;
  }
</style>
