<script>
  import "../app.css";
  let { children } = $props();
</script>

<svelte:head>
  <title>Hub MCP — Every MCP. One Click. No Terminal.</title>
  <meta name="description" content="Connect Claude to Gmail, Notion, Slack, and 200+ other apps without touching a terminal. Hub MCP makes MCP servers accessible to everyone." />
  <meta name="keywords" content="MCP, Model Context Protocol, Claude, AI integrations, Anthropic, no-code" />

  <meta property="og:title" content="Hub MCP — Every MCP. One Click. No Terminal." />
  <meta property="og:description" content="Connect Claude to Gmail, Notion, Slack, and 200+ other apps without touching a terminal." />
  <meta property="og:type" content="website" />
  <meta property="og:url" content="https://mcphub.io" />

  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="Hub MCP — Every MCP. One Click. No Terminal." />
  <meta name="twitter:description" content="Connect Claude to Gmail, Notion, Slack, and 200+ other apps without touching a terminal." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet" />
</svelte:head>

{@render children()}
