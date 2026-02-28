<script>
  import { onMount } from 'svelte';

  let mounted = $state(false);
  let scrollY = $state(0);
  let searchQuery = $state('');
  let selectedCategory = $state('All');
  let mobileMenuOpen = $state(false);
  let mouseX = $state(0);
  let mouseY = $state(0);
  let particles = $state([]);

  function handleMouseMove(e) {
    mouseX = e.clientX;
    mouseY = e.clientY;
  }

  const categories = ['All', 'Productivity', 'Development', 'Communication', 'Data', 'Social', 'Finance', 'Design'];

  // SVG icon paths for each integration
  const integrationIcons = {
    // Productivity
    'Gmail': { viewBox: '0 0 24 24', path: 'M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4l-8 5-8-5V6l8 5 8-5v2z', color: '#EA4335' },
    'Google Calendar': { viewBox: '0 0 24 24', path: 'M19 4h-1V2h-2v2H8V2H6v2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 16H5V10h14v10zm0-12H5V6h14v2zM7 12h5v5H7v-5z', color: '#4285F4' },
    'Google Drive': { viewBox: '0 0 24 24', path: 'M7.71 3.5L1.15 15l3.43 5.99L11 9.49 7.71 3.5zm12.58 11.5H8.57l-3.43 6h11.72l3.43-6zM22.85 15L15.29 2h-6.86l7.57 13h6.85z', color: '#34A853' },
    'Notion': { viewBox: '0 0 24 24', path: 'M4.459 4.208c.746.606 1.026.56 2.428.466l13.215-.793c.28 0 .047-.28-.046-.326L17.86 1.968c-.42-.326-.98-.7-2.055-.607L3.01 2.295c-.466.046-.56.28-.374.466l1.823 1.447zm.793 3.08v13.904c0 .747.373 1.027 1.214.98l14.523-.84c.841-.046.935-.56.935-1.167V6.354c0-.606-.233-.933-.748-.886l-15.177.887c-.56.047-.747.327-.747.933zm14.337.745c.093.42 0 .84-.42.888l-.7.14v10.264c-.608.327-1.168.514-1.635.514-.748 0-.935-.234-1.495-.933l-4.577-7.186v6.952l1.449.327s0 .84-1.168.84l-3.22.186c-.094-.186 0-.653.327-.746l.84-.233V9.854L7.822 9.76c-.094-.42.14-1.026.793-1.073l3.456-.233 4.764 7.279v-6.44l-1.215-.14c-.093-.514.28-.886.747-.933l3.222-.187zm-16.95-12.12L15.502.572c1.588-.14 1.961-.047 2.942.7l4.016 2.895c.654.467.842.514.842 1.027v14.858c0 .84-.327 1.353-1.402 1.446l-15.456.933c-.84.047-1.261-.093-1.728-.7l-2.849-3.828c-.514-.7-.7-1.214-.7-1.867V4.06c0-.793.373-1.447 1.354-1.594z', color: '#000000', darkColor: '#FFFFFF' },
    'Todoist': { viewBox: '0 0 24 24', path: 'M11.088 3.001L6.2 5.93 1.312 3.001v5.856l4.888 2.93 4.888-2.93V3.001zm11.6 0l-4.888 2.93-4.888-2.93v5.856l4.888 2.93 4.888-2.93V3.001zM11.088 14.713l-4.888 2.93-4.888-2.93v5.856l4.888 2.93 4.888-2.93v-5.856zm11.6 0l-4.888 2.93-4.888-2.93v5.856l4.888 2.93 4.888-2.93v-5.856z', color: '#E44332' },
    'Trello': { viewBox: '0 0 24 24', path: 'M21 3H3c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h18c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM10 17H6V7h4v10zm8-4h-4V7h4v6z', color: '#0079BF' },
    'Asana': { viewBox: '0 0 24 24', path: 'M12 2.5C9.5 2.5 7.5 4.5 7.5 7S9.5 11.5 12 11.5 16.5 9.5 16.5 7 14.5 2.5 12 2.5zM5.5 12.5C3 12.5 1 14.5 1 17s2 4.5 4.5 4.5S10 19.5 10 17s-2-4.5-4.5-4.5zm13 0c-2.5 0-4.5 2-4.5 4.5s2 4.5 4.5 4.5S23 19.5 23 17s-2-4.5-4.5-4.5z', color: '#F06A6A' },
    'Airtable': { viewBox: '0 0 24 24', path: 'M11.992 1.966L2.46 5.476c-.314.12-.32.562-.01.69l9.602 3.793c.252.1.53.1.782 0l9.603-3.793c.31-.128.304-.57-.01-.69l-9.533-3.51a1.02 1.02 0 0 0-.702 0zm.566 10.64l9.26-3.659c.324-.128.548-.432.548-.782V3.77l-9.807 3.872v5.003c0 .348-.346.588-.668.47l-9.26-3.66a.82.82 0 0 1-.547-.782V3.77l9.806 3.872v4.964z', color: '#18BFFF' },
    'Evernote': { viewBox: '0 0 24 24', path: 'M8.586 2.5c0-.276-.224-.5-.5-.5H4.5a2 2 0 0 0-2 2v3.586c0 .276.224.5.5.5h.586c.276 0 .5-.224.5-.5V4.5c0-.276.224-.5.5-.5h3.5c.276 0 .5-.224.5-.5V2.5zm8.914 3.414V4.5c0-.276.224-.5.5-.5h2.5c.276 0 .5.224.5.5v3.586c0 .276-.224.5-.5.5h-.586c-.276 0-.5-.224-.5-.5V5.914c0-.276-.224-.5-.5-.5h-1.414zM4 13.5v2.086c0 .276.224.5.5.5h3.586c.276 0 .5.224.5.5v.914c0 .276-.224.5-.5.5H4.5a2 2 0 0 1-2-2V13.5c0-.276.224-.5.5-.5h.5c.276 0 .5.224.5.5zm14 2.086V13.5c0-.276.224-.5.5-.5h.5c.276 0 .5.224.5.5V16a2 2 0 0 1-2 2h-3.586c-.276 0-.5-.224-.5-.5v-.914c0-.276.224-.5.5-.5h3.586c.276 0 .5-.224.5-.5z', color: '#00A82D' },
    'Dropbox': { viewBox: '0 0 24 24', path: 'M6 2l6 3.75L6 9.5 0 5.75 6 2zm12 0l6 3.75-6 3.75-6-3.75L18 2zM0 13.25L6 9.5l6 3.75L6 17 0 13.25zm18-3.75l6 3.75L18 17l-6-3.75 6-3.75zM6 18.25l6-3.75 6 3.75L12 22l-6-3.75z', color: '#0061FF' },
    'Microsoft Outlook': { viewBox: '0 0 24 24', path: 'M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.94-.49-7-3.85-7-7.93s3.06-7.44 7-7.93v15.86zm2-15.86c1.03.13 2 .45 2.87.93H13v-.93zM13 7h5.24c.25.31.48.65.68 1H13V7zm0 3h6.74c.08.33.15.66.19 1H13v-1zm0 3h6.93c-.04.34-.11.67-.19 1H13v-1zm0 3h5.92c-.2.35-.43.69-.68 1H13v-1zm0 3h2.87c-.87.48-1.84.8-2.87.93V19z', color: '#0078D4' },
    'Obsidian': { viewBox: '0 0 24 24', path: 'M12 2L4 6v12l8 4 8-4V6l-8-4zm0 2.18l5.74 2.87L12 9.93 6.26 7.05 12 4.18zM5.5 8.27l5.5 2.75v7.71l-5.5-2.75V8.27zm7 10.46V11.02l5.5-2.75v7.71l-5.5 2.75z', color: '#7C3AED' },

    // Development
    'GitHub': { viewBox: '0 0 24 24', path: 'M12 2C6.477 2 2 6.477 2 12c0 4.42 2.865 8.166 6.839 9.489.5.092.682-.217.682-.482 0-.237-.008-.866-.013-1.7-2.782.603-3.369-1.342-3.369-1.342-.454-1.155-1.11-1.462-1.11-1.462-.908-.62.069-.608.069-.608 1.003.07 1.531 1.03 1.531 1.03.892 1.529 2.341 1.087 2.91.831.092-.646.35-1.086.636-1.336-2.22-.253-4.555-1.11-4.555-4.943 0-1.091.39-1.984 1.029-2.683-.103-.253-.446-1.27.098-2.647 0 0 .84-.268 2.75 1.026A9.578 9.578 0 0 1 12 6.836c.85.004 1.705.114 2.504.336 1.909-1.294 2.747-1.026 2.747-1.026.546 1.377.202 2.394.1 2.647.64.699 1.028 1.592 1.028 2.683 0 3.842-2.339 4.687-4.566 4.935.359.309.678.919.678 1.852 0 1.336-.012 2.415-.012 2.743 0 .267.18.578.688.48C19.138 20.163 22 16.418 22 12c0-5.523-4.477-10-10-10z', color: '#181717', darkColor: '#FFFFFF' },
    'GitLab': { viewBox: '0 0 24 24', path: 'M22.65 14.39L12 22.13 1.35 14.39a.84.84 0 0 1-.3-.94l1.22-3.78 2.44-7.51A.42.42 0 0 1 4.82 2a.43.43 0 0 1 .58 0 .42.42 0 0 1 .11.18l2.44 7.49h8.1l2.44-7.51A.42.42 0 0 1 18.6 2a.43.43 0 0 1 .58 0 .42.42 0 0 1 .11.18l2.44 7.51L23 13.45a.84.84 0 0 1-.35.94z', color: '#FC6D26' },
    'Linear': { viewBox: '0 0 24 24', path: 'M3 12c0-4.97 4.03-9 9-9s9 4.03 9 9-4.03 9-9 9-9-4.03-9-9zm9 6.5c3.59 0 6.5-2.91 6.5-6.5S15.59 5.5 12 5.5 5.5 8.41 5.5 12s2.91 6.5 6.5 6.5z', color: '#5E6AD2' },
    'Jira': { viewBox: '0 0 24 24', path: 'M11.53 2c0 2.4 1.97 4.35 4.35 4.35h1.78v1.7c0 2.4 1.94 4.34 4.34 4.35V2.84a.84.84 0 0 0-.84-.84h-9.63zm-4.06 4.06c0 2.4 1.97 4.35 4.35 4.35h1.78v1.72c0 2.38 1.94 4.34 4.34 4.34V6.91a.84.84 0 0 0-.84-.84H7.47v-.01zM3.41 10.12c0 2.4 1.97 4.35 4.35 4.35h1.78v1.72c0 2.38 1.94 4.34 4.34 4.34V10.97a.85.85 0 0 0-.84-.84H3.41v-.01z', color: '#0052CC' },
    'Vercel': { viewBox: '0 0 24 24', path: 'M24 22.525H0l12-21.05 12 21.05z', color: '#000000', darkColor: '#FFFFFF' },
    'Supabase': { viewBox: '0 0 24 24', path: 'M21.362 9.354H12V.396a.396.396 0 0 0-.716-.233L2.203 12.424l-.401.562a1.04 1.04 0 0 0 .836 1.659H12v8.959a.396.396 0 0 0 .716.233l9.081-12.261.401-.562a1.04 1.04 0 0 0-.836-1.66z', color: '#3ECF8E' },
    'Railway': { viewBox: '0 0 24 24', path: 'M.113 10.27A12 12 0 0 0 0 12c0 4.947 2.996 9.192 7.27 11.018V10.27H.113zm9.157 12.903A12.005 12.005 0 0 0 12 24c.944 0 1.864-.109 2.745-.316a9.37 9.37 0 0 0-.015-.084H9.27v-.427zm6.46-1.02A11.999 11.999 0 0 0 24 12c0-.94-.108-1.854-.314-2.73H9.27v12.883h6.46zm8.157-11.04A12.005 12.005 0 0 0 12 0 11.98 11.98 0 0 0 .872 7.27h23.015z', color: '#0B0D0E', darkColor: '#FFFFFF' },
    'Netlify': { viewBox: '0 0 24 24', path: 'M16.934 8.519a1.044 1.044 0 0 1 .303.23l2.349-1.045-2.192-2.171-.491 2.954zM12.06 6.546a1.305 1.305 0 0 1 .209.574l3.497 1.482a1.044 1.044 0 0 1 .355-.177l.574-3.455-2.13-.472-2.505 2.048zm11.933 5.491l-3.748-3.724-.348 2.094 1.924 1.263c.071-.025.149-.039.23-.039.251 0 .469.145.576.356l1.366-.95zm-8.811-2.858a1.044 1.044 0 0 1-.209-.165l-3.173 1.412a.881.881 0 0 1-.009.14l3.687 2.375a1.044 1.044 0 0 1 .543-.238l-.839-3.524zM5.906 4.424l2.068 1.605 2.376-1.06-.007-.008-3.152-2.074-1.285 1.537zm15.39 9.081a1.029 1.029 0 0 1-.209.165l.839 3.524a1.044 1.044 0 0 1 .543.238l2.066-1.433-3.239-2.494zM10.1 18.5a1.044 1.044 0 0 1-.355-.177l-3.497 1.482a1.305 1.305 0 0 1-.209.574l2.505 2.048 2.13-.472-.574-3.455z', color: '#00C7B7' },
    'Sentry': { viewBox: '0 0 24 24', path: 'M13.91 2.505c-.873-1.448-2.972-1.448-3.844 0L6.53 8.969c1.86 1.058 3.19 2.97 3.49 5.201h2.96c.23-1.73 1.14-3.27 2.49-4.36l-1.56-2.71c-1.04.753-1.74 1.96-1.88 3.32H9.99c-.26-2.64-2.12-4.87-4.61-5.72l1.08-1.87c2.98 1.03 5.12 3.78 5.46 7.04h.09c.17-2.78 2.06-5.17 4.68-6.1l-1.55-2.69c-.25.04-.5.07-.76.07-.63 0-1.24-.12-1.82-.35l1.37-2.38z', color: '#362D59' },
    'CircleCI': { viewBox: '0 0 24 24', path: 'M12 12a3 3 0 1 1 0-6 3 3 0 0 1 0 6zm0-12C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0zm0 18a6 6 0 1 1 0-12 6 6 0 0 1 0 12z', color: '#343434', darkColor: '#FFFFFF' },

    // Communication
    'Slack': { viewBox: '0 0 24 24', path: 'M5.042 15.165a2.528 2.528 0 0 1-2.52 2.523A2.528 2.528 0 0 1 0 15.165a2.527 2.527 0 0 1 2.522-2.52h2.52v2.52zM6.313 15.165a2.527 2.527 0 0 1 2.521-2.52 2.527 2.527 0 0 1 2.521 2.52v6.313A2.528 2.528 0 0 1 8.834 24a2.528 2.528 0 0 1-2.521-2.522v-6.313zM8.834 5.042a2.528 2.528 0 0 1-2.521-2.52A2.528 2.528 0 0 1 8.834 0a2.528 2.528 0 0 1 2.521 2.522v2.52H8.834zM8.834 6.313a2.528 2.528 0 0 1 2.521 2.521 2.528 2.528 0 0 1-2.521 2.521H2.522A2.528 2.528 0 0 1 0 8.834a2.528 2.528 0 0 1 2.522-2.521h6.312zM18.956 8.834a2.528 2.528 0 0 1 2.522-2.521A2.528 2.528 0 0 1 24 8.834a2.528 2.528 0 0 1-2.522 2.521h-2.522V8.834zM17.688 8.834a2.528 2.528 0 0 1-2.523 2.521 2.527 2.527 0 0 1-2.52-2.521V2.522A2.527 2.527 0 0 1 15.165 0a2.528 2.528 0 0 1 2.523 2.522v6.312zM15.165 18.956a2.528 2.528 0 0 1 2.523 2.522A2.528 2.528 0 0 1 15.165 24a2.527 2.527 0 0 1-2.52-2.522v-2.522h2.52zM15.165 17.688a2.527 2.527 0 0 1-2.52-2.523 2.526 2.526 0 0 1 2.52-2.52h6.313A2.527 2.527 0 0 1 24 15.165a2.528 2.528 0 0 1-2.522 2.523h-6.313z', color: '#4A154B' },
    'Discord': { viewBox: '0 0 24 24', path: 'M20.317 4.37a19.791 19.791 0 0 0-4.885-1.515.074.074 0 0 0-.079.037c-.21.375-.444.864-.608 1.25a18.27 18.27 0 0 0-5.487 0 12.64 12.64 0 0 0-.617-1.25.077.077 0 0 0-.079-.037A19.736 19.736 0 0 0 3.677 4.37a.07.07 0 0 0-.032.027C.533 9.046-.32 13.58.099 18.057a.082.082 0 0 0 .031.057 19.9 19.9 0 0 0 5.993 3.03.078.078 0 0 0 .084-.028 14.09 14.09 0 0 0 1.226-1.994.076.076 0 0 0-.041-.106 13.107 13.107 0 0 1-1.872-.892.077.077 0 0 1-.008-.128 10.2 10.2 0 0 0 .372-.292.074.074 0 0 1 .077-.01c3.928 1.793 8.18 1.793 12.062 0a.074.074 0 0 1 .078.01c.12.098.246.198.373.292a.077.077 0 0 1-.006.127 12.299 12.299 0 0 1-1.873.892.077.077 0 0 0-.041.107c.36.698.772 1.362 1.225 1.993a.076.076 0 0 0 .084.028 19.839 19.839 0 0 0 6.002-3.03.077.077 0 0 0 .032-.054c.5-5.177-.838-9.674-3.549-13.66a.061.061 0 0 0-.031-.03zM8.02 15.33c-1.183 0-2.157-1.085-2.157-2.419 0-1.333.956-2.419 2.157-2.419 1.21 0 2.176 1.096 2.157 2.42 0 1.333-.956 2.418-2.157 2.418zm7.975 0c-1.183 0-2.157-1.085-2.157-2.419 0-1.333.955-2.419 2.157-2.419 1.21 0 2.176 1.096 2.157 2.42 0 1.333-.946 2.418-2.157 2.418z', color: '#5865F2' },
    'Microsoft Teams': { viewBox: '0 0 24 24', path: 'M20.625 8.073h-5.27V4.36c0-.196.16-.355.357-.355h4.556c.197 0 .357.16.357.355v3.713zm-5.27 1.428h5.805c.465 0 .84.377.84.84v6.148c0 1.863-1.51 3.373-3.373 3.373h-3.272v-10.36zm-2.143-6.446a2.678 2.678 0 1 0 0 5.357 2.678 2.678 0 0 0 0-5.357zm1.072 17.034a4.465 4.465 0 0 1-4.465-4.465V9.5h5.535v6.125c0 .196-.16.355-.357.355h-3.035v.446c0 1.365 1.107 2.473 2.472 2.473a2.47 2.47 0 0 0 2.322-1.625h1.528a4.002 4.002 0 0 1-4 3.814zM7.34 3.91a3.214 3.214 0 1 0 0 6.429 3.214 3.214 0 0 0 0-6.429zm3.75 6.962H2.518c-.285 0-.518.233-.518.518v6.145c0 2.33 1.884 4.214 4.214 4.214h1.126c2.33 0 4.214-1.884 4.214-4.214V11.39c0-.285-.233-.518-.465-.518z', color: '#6264A7' },
    'Zoom': { viewBox: '0 0 24 24', path: 'M4.585 6.836A2.586 2.586 0 0 0 2 9.42v5.16a2.586 2.586 0 0 0 2.585 2.585h8.717a2.586 2.586 0 0 0 2.586-2.585V9.42a2.586 2.586 0 0 0-2.586-2.585H4.585zm13.187 2.95l4.006-2.692a.345.345 0 0 1 .222-.08c.178 0 .345.14.345.318v9.336a.345.345 0 0 1-.345.345.345.345 0 0 1-.222-.08l-4.006-2.692v-4.455z', color: '#2D8CFF' },
    'Telegram': { viewBox: '0 0 24 24', path: 'M11.944 0A12 12 0 0 0 0 12a12 12 0 0 0 12 12 12 12 0 0 0 12-12A12 12 0 0 0 12 0a12 12 0 0 0-.056 0zm4.962 7.224c.1-.002.321.023.465.14a.506.506 0 0 1 .171.325c.016.093.036.306.02.472-.18 1.898-.962 6.502-1.36 8.627-.168.9-.499 1.201-.82 1.23-.696.065-1.225-.46-1.9-.902-1.056-.693-1.653-1.124-2.678-1.8-1.185-.78-.417-1.21.258-1.91.177-.184 3.247-2.977 3.307-3.23.007-.032.014-.15-.056-.212s-.174-.041-.249-.024c-.106.024-1.793 1.14-5.061 3.345-.48.33-.913.49-1.302.48-.428-.008-1.252-.241-1.865-.44-.752-.245-1.349-.374-1.297-.789.027-.216.325-.437.893-.663 3.498-1.524 5.83-2.529 6.998-3.014 3.332-1.386 4.025-1.627 4.476-1.635z', color: '#26A5E4' },
    'WhatsApp Business': { viewBox: '0 0 24 24', path: 'M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 0 1-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 0 1-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 0 1 2.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0 0 12.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 0 0 5.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 0 0-3.48-8.413z', color: '#25D366' },
    'Intercom': { viewBox: '0 0 24 24', path: 'M19.5 4.5h-15A1.5 1.5 0 0 0 3 6v15a1.5 1.5 0 0 0 1.5 1.5h15A1.5 1.5 0 0 0 21 21V6a1.5 1.5 0 0 0-1.5-1.5zM8 10.5a1 1 0 1 1 0-2 1 1 0 0 1 0 2zm4 0a1 1 0 1 1 0-2 1 1 0 0 1 0 2zm4 0a1 1 0 1 1 0-2 1 1 0 0 1 0 2zm-9.25 5.25a.75.75 0 0 1 .537-1.283 6.003 6.003 0 0 0 9.426 0 .75.75 0 0 1 .537 1.283 7.503 7.503 0 0 1-10.5 0z', color: '#1F8DED' },

    // Data
    'PostgreSQL': { viewBox: '0 0 24 24', path: 'M17.128 0a10.134 10.134 0 0 0-2.755.403l-.063.02a10.922 10.922 0 0 0-1.612-.133c-.828-.004-1.575.108-2.223.331a9.44 9.44 0 0 0-2.36-.404C5.612.17 3.417 1.16 2.252 2.925c-1.153 1.751-1.402 4.168-.702 6.936.19.747.431 1.51.715 2.28.247.672.523 1.34.82 1.961.32.671.657 1.274.984 1.79.266.42.55.82.835 1.178-.067.643-.015 1.275.157 1.927.277 1.044.75 1.946 1.399 2.646.695.75 1.539 1.215 2.505 1.358a3.73 3.73 0 0 0 .538.04c.463 0 .902-.086 1.318-.243a2.963 2.963 0 0 0 1.773-1.73c.165-.417.256-.847.278-1.29l.015-.159c.07-.077.137-.162.2-.248.26-.358.466-.803.583-1.331.072-.327.1-.672.08-1.028.4.025.778-.006 1.134-.095.453-.116.875-.31 1.254-.577l.099-.082.096.08c.463.42 1.05.676 1.708.738.065.006.13.01.196.01.555 0 1.054-.178 1.466-.52.446-.369.76-.891.913-1.512.063-.26.098-.538.106-.83.007-.26-.008-.548-.043-.859l-.008-.061.13-.097c.334-.25.619-.527.857-.832.5-.64.76-1.31.781-1.992.012-.385-.053-.76-.196-1.127-.086-.22-.2-.431-.34-.635a3.94 3.94 0 0 0-.28-.353c.14-.368.234-.74.283-1.113.102-.762-.004-1.49-.318-2.153-.27-.567-.672-1.063-1.195-1.46-.48-.365-1.054-.638-1.715-.811-.42-.111-.875-.176-1.36-.199z', color: '#4169E1' },
    'MongoDB': { viewBox: '0 0 24 24', path: 'M17.193 9.555c-1.264-5.58-4.252-7.414-4.573-8.115-.28-.394-.53-.954-.735-1.44-.036.495-.055.685-.523 1.184-.723.566-4.438 3.682-4.74 10.02-.282 5.912 4.27 9.435 4.888 9.884l.07.05A73.49 73.49 0 0 1 11.91 24h.481c.114-1.032.284-2.056.51-3.07.417-.296.604-.463.85-.693a11.342 11.342 0 0 0 3.639-8.464c.01-.814-.103-1.662-.197-2.218z', color: '#47A248' },
    'Google Sheets': { viewBox: '0 0 24 24', path: 'M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H5V5h14v14zM7 7h2v2H7V7zm0 4h2v2H7v-2zm0 4h2v2H7v-2zm4-8h6v2h-6V7zm0 4h6v2h-6v-2zm0 4h6v2h-6v-2z', color: '#0F9D58' },
    'Snowflake': { viewBox: '0 0 24 24', path: 'M12 0l1.09 3.27 2.91-1.64v3.27l3.27-1.09L17.64 7l3.27 1.09-1.64 2.91h3.27L21 12l1.54 1 3.27-1.09L20.18 17l1.64 2.91-3.27-1.09v3.27l-2.91-1.64L12 24l-1.09-3.27-2.91 1.64v-3.27l-3.27 1.09L6.36 17l-3.27-1.09 1.64-2.91H1.46L3 12l-1.54-1-3.27 1.09L3.82 7 2.18 4.09l3.27 1.09V2.09L8.36 3.73 12 0z', color: '#29B5E8' },
    'BigQuery': { viewBox: '0 0 24 24', path: 'M6 4v16h12V4H6zm10 14H8V6h8v12zM4 8h1v8H4V8zm15 0h1v8h-1V8zM9 8h6v2H9V8zm0 4h4v2H9v-2z', color: '#4285F4' },
    'Elasticsearch': { viewBox: '0 0 24 24', path: 'M13.394 0C8.37 0 4.04 3.123 2.186 7.5h18.272a5.997 5.997 0 0 0-5.936-5.16 5.97 5.97 0 0 0-1.128-2.34zM1.5 12c0 1.025.13 2.02.375 2.97h12.89a2.97 2.97 0 0 0 0-5.94H1.875C1.63 9.98 1.5 10.975 1.5 12zm.686 4.5C3.957 20.84 8.31 24 13.394 24c3.25 0 6.148-1.455 8.106-3.75H8.47c-2.876 0-5.312-1.856-6.284-4.5z', color: '#005571' },
    'Redis': { viewBox: '0 0 24 24', path: 'M10.5 2.661l.54.997-1.797.644 2.409.166.54.996-1.797.645 2.409.165.54.997-3.614 1.303-1.662-3.075 2.432-.838zm6.905 2.393c.085-.272-.086-.503-.358-.503h-2.081c-.272 0-.555.23-.64.503l-.864 2.754h3.08l.863-2.754zM5.164 6.08l-.84 2.68c-.087.278.082.504.36.504h1.982c.278 0 .566-.226.653-.504l.839-2.68c.087-.278-.082-.504-.36-.504H5.816c-.278 0-.566.226-.652.504zm15.594.6L12.006 10.5l-8.759-3.82C1.703 6.015.5 6.597.5 7.395v9.21c0 .798 1.203 1.38 2.747.715l8.759 3.82 8.752-3.82c1.544.665 2.747.083 2.747-.715v-9.21c0-.798-1.203-1.38-2.747-.715z', color: '#DC382D' },

    // Social
    'Twitter/X': { viewBox: '0 0 24 24', path: 'M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-5.214-6.817L4.99 21.75H1.68l7.73-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z', color: '#000000', darkColor: '#FFFFFF' },
    'Reddit': { viewBox: '0 0 24 24', path: 'M12 0A12 12 0 0 0 0 12a12 12 0 0 0 12 12 12 12 0 0 0 12-12A12 12 0 0 0 12 0zm5.01 4.744c.688 0 1.25.561 1.25 1.249a1.25 1.25 0 0 1-2.498.056l-2.597-.547-.8 3.747c1.824.07 3.48.632 4.674 1.488.308-.309.73-.491 1.207-.491.968 0 1.754.786 1.754 1.754 0 .716-.435 1.333-1.01 1.614a3.111 3.111 0 0 1 .042.52c0 2.694-3.13 4.87-7.004 4.87-3.874 0-7.004-2.176-7.004-4.87 0-.183.015-.366.043-.534A1.748 1.748 0 0 1 4.028 12c0-.968.786-1.754 1.754-1.754.463 0 .898.196 1.207.49 1.207-.883 2.878-1.43 4.744-1.487l.885-4.182a.342.342 0 0 1 .14-.197.35.35 0 0 1 .238-.042l2.906.617a1.214 1.214 0 0 1 1.108-.701zM9.25 12C8.561 12 8 12.562 8 13.25c0 .687.561 1.248 1.25 1.248.687 0 1.248-.561 1.248-1.249 0-.688-.561-1.249-1.249-1.249zm5.5 0c-.687 0-1.248.561-1.248 1.25 0 .687.561 1.248 1.249 1.248.688 0 1.249-.561 1.249-1.249 0-.687-.562-1.249-1.25-1.249zm-5.466 3.99a.327.327 0 0 0-.231.094.33.33 0 0 0 0 .463c.842.842 2.484.913 2.961.913.477 0 2.105-.056 2.961-.913a.361.361 0 0 0 .029-.463.33.33 0 0 0-.464 0c-.547.533-1.684.73-2.512.73-.828 0-1.979-.196-2.512-.73a.326.326 0 0 0-.232-.095z', color: '#FF4500' },
    'LinkedIn': { viewBox: '0 0 24 24', path: 'M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z', color: '#0A66C2' },
    'Instagram': { viewBox: '0 0 24 24', path: 'M12 0C8.74 0 8.333.015 7.053.072 5.775.132 4.905.333 4.14.63c-.789.306-1.459.717-2.126 1.384S.935 3.35.63 4.14C.333 4.905.131 5.775.072 7.053.012 8.333 0 8.74 0 12s.015 3.667.072 4.947c.06 1.277.261 2.148.558 2.913.306.788.717 1.459 1.384 2.126.667.666 1.336 1.079 2.126 1.384.766.296 1.636.499 2.913.558C8.333 23.988 8.74 24 12 24s3.667-.015 4.947-.072c1.277-.06 2.148-.262 2.913-.558.788-.306 1.459-.718 2.126-1.384.666-.667 1.079-1.335 1.384-2.126.296-.765.499-1.636.558-2.913.06-1.28.072-1.687.072-4.947s-.015-3.667-.072-4.947c-.06-1.277-.262-2.149-.558-2.913-.306-.789-.718-1.459-1.384-2.126C21.319 1.347 20.651.935 19.86.63c-.765-.297-1.636-.499-2.913-.558C15.667.012 15.26 0 12 0zm0 2.16c3.203 0 3.585.016 4.85.071 1.17.055 1.805.249 2.227.415.562.217.96.477 1.382.896.419.42.679.819.896 1.381.164.422.36 1.057.413 2.227.057 1.266.07 1.646.07 4.85s-.015 3.585-.074 4.85c-.061 1.17-.256 1.805-.421 2.227-.224.562-.479.96-.899 1.382-.419.419-.824.679-1.38.896-.42.164-1.065.36-2.235.413-1.274.057-1.649.07-4.859.07-3.211 0-3.586-.015-4.859-.074-1.171-.061-1.816-.256-2.236-.421-.569-.224-.96-.479-1.379-.899-.421-.419-.69-.824-.9-1.38-.165-.42-.359-1.065-.42-2.235-.045-1.26-.061-1.649-.061-4.844 0-3.196.016-3.586.061-4.861.061-1.17.255-1.814.42-2.234.21-.57.479-.96.9-1.381.419-.419.81-.689 1.379-.898.42-.166 1.051-.361 2.221-.421 1.275-.045 1.65-.06 4.859-.06l.045.03zm0 3.678c-3.405 0-6.162 2.76-6.162 6.162 0 3.405 2.76 6.162 6.162 6.162 3.405 0 6.162-2.76 6.162-6.162 0-3.405-2.757-6.162-6.162-6.162zM12 16c-2.21 0-4-1.79-4-4s1.79-4 4-4 4 1.79 4 4-1.79 4-4 4zm7.846-10.405c0 .795-.646 1.44-1.44 1.44-.795 0-1.44-.646-1.44-1.44 0-.794.646-1.439 1.44-1.439.793-.001 1.44.645 1.44 1.439z', color: '#E4405F' },
    'YouTube': { viewBox: '0 0 24 24', path: 'M23.498 6.186a3.016 3.016 0 0 0-2.122-2.136C19.505 3.545 12 3.545 12 3.545s-7.505 0-9.377.505A3.017 3.017 0 0 0 .502 6.186C0 8.07 0 12 0 12s0 3.93.502 5.814a3.016 3.016 0 0 0 2.122 2.136c1.871.505 9.376.505 9.376.505s7.505 0 9.377-.505a3.015 3.015 0 0 0 2.122-2.136C24 15.93 24 12 24 12s0-3.93-.502-5.814zM9.545 15.568V8.432L15.818 12l-6.273 3.568z', color: '#FF0000' },
    'Spotify': { viewBox: '0 0 24 24', path: 'M12 0C5.4 0 0 5.4 0 12s5.4 12 12 12 12-5.4 12-12S18.66 0 12 0zm5.521 17.34c-.24.359-.66.48-1.021.24-2.82-1.74-6.36-2.101-10.561-1.141-.418.122-.779-.179-.899-.539-.12-.421.18-.78.54-.9 4.56-1.021 8.52-.6 11.64 1.32.42.18.479.659.301 1.02zm1.44-3.3c-.301.42-.841.6-1.262.3-3.239-1.98-8.159-2.58-11.939-1.38-.479.12-1.02-.12-1.14-.6-.12-.48.12-1.021.6-1.141C9.6 9.9 15 10.561 18.72 12.84c.361.181.54.78.241 1.2zm.12-3.36C15.24 8.4 8.82 8.16 5.16 9.301c-.6.179-1.2-.181-1.38-.721-.18-.601.18-1.2.72-1.381 4.26-1.26 11.28-1.02 15.721 1.621.539.3.719 1.02.419 1.56-.299.421-1.02.599-1.559.3z', color: '#1DB954' },
    'TikTok': { viewBox: '0 0 24 24', path: 'M12.525.02c1.31-.02 2.61-.01 3.91-.02.08 1.53.63 3.09 1.75 4.17 1.12 1.11 2.7 1.62 4.24 1.79v4.03c-1.44-.05-2.89-.35-4.2-.97-.57-.26-1.1-.59-1.62-.93-.01 2.92.01 5.84-.02 8.75-.08 1.4-.54 2.79-1.35 3.94-1.31 1.92-3.58 3.17-5.91 3.21-1.43.08-2.86-.31-4.08-1.03-2.02-1.19-3.44-3.37-3.65-5.71-.02-.5-.03-1-.01-1.49.18-1.9 1.12-3.72 2.58-4.96 1.66-1.44 3.98-2.13 6.15-1.72.02 1.48-.04 2.96-.04 4.44-.99-.32-2.15-.23-3.02.37-.63.41-1.11 1.04-1.36 1.75-.21.51-.15 1.07-.14 1.61.24 1.64 1.82 3.02 3.5 2.87 1.12-.01 2.19-.66 2.77-1.61.19-.33.4-.67.41-1.06.1-1.79.06-3.57.07-5.36.01-4.03-.01-8.05.02-12.07z', color: '#000000', darkColor: '#FFFFFF' },

    // Finance
    'Stripe': { viewBox: '0 0 24 24', path: 'M13.976 9.15c-2.172-.806-3.356-1.426-3.356-2.409 0-.831.683-1.305 1.901-1.305 2.227 0 4.515.858 6.09 1.631l.89-5.494C18.252.975 15.697 0 12.165 0 9.667 0 7.589.654 6.104 1.872 4.56 3.147 3.757 4.992 3.757 7.218c0 4.039 2.467 5.76 6.476 7.219 2.585.92 3.445 1.574 3.445 2.583 0 .98-.84 1.545-2.354 1.545-1.875 0-4.965-.921-6.99-2.109l-.9 5.555C5.175 22.99 8.385 24 11.714 24c2.641 0 4.843-.624 6.328-1.813 1.664-1.305 2.525-3.236 2.525-5.732 0-4.128-2.524-5.851-6.591-7.305z', color: '#635BFF' },
    'QuickBooks': { viewBox: '0 0 24 24', path: 'M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0zM9.75 17.5c-1.933 0-3.5-1.567-3.5-3.5s1.567-3.5 3.5-3.5v-3c0-.276.224-.5.5-.5h1c.276 0 .5.224.5.5v3h2.5c.276 0 .5.224.5.5v1c0 .276-.224.5-.5.5h-2.5v1.5c0 .828.672 1.5 1.5 1.5h2.5c.276 0 .5.224.5.5v1c0 .276-.224.5-.5.5h-2.5z', color: '#2CA01C' },
    'Xero': { viewBox: '0 0 24 24', path: 'M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0zm4.328 16.742l-2.578-2.578L11.172 16.742a.969.969 0 1 1-1.37-1.37L12.38 12.793l-2.578-2.578a.969.969 0 0 1 1.37-1.37l2.578 2.578 2.578-2.578a.969.969 0 0 1 1.37 1.37L15.12 12.793l2.578 2.578a.969.969 0 1 1-1.37 1.371z', color: '#13B5EA' },
    'Plaid': { viewBox: '0 0 24 24', path: 'M20.168 10.182h-2.291v3.636h2.291v-3.636zm-4.582 0H8.414v3.636h7.172v-3.636zM3.832 6.545h2.291v3.637H3.832V6.545zm0 7.273h2.291v3.637H3.832v-3.637zm4.582-7.273h7.172v3.637H8.414V6.545zm0 7.273h7.172v3.637H8.414v-3.637zm11.754-7.273h-2.291v3.637h2.291V6.545zm0 7.273h-2.291v3.637h2.291v-3.637z', color: '#00D66E' },
    'HubSpot': { viewBox: '0 0 24 24', path: 'M18.164 7.93V5.084a2.198 2.198 0 0 0 1.267-1.984v-.066A2.198 2.198 0 0 0 17.235.838h-.067a2.198 2.198 0 0 0-2.196 2.196v.066c0 .87.505 1.62 1.238 1.977V7.93a5.637 5.637 0 0 0-2.824 1.395l-7.376-5.748a2.346 2.346 0 0 0 .07-.56A2.372 2.372 0 1 0 3.708 5.39c.267 0 .522-.047.76-.13l7.252 5.651a5.636 5.636 0 0 0-.157 6.27l-2.109 2.11a2.06 2.06 0 0 0-.597-.1 2.083 2.083 0 1 0 2.083 2.082c0-.209-.036-.41-.095-.6l2.078-2.079a5.654 5.654 0 1 0 5.241-10.664z', color: '#FF7A59' },
    'Salesforce': { viewBox: '0 0 24 24', path: 'M10.006 5.418a4.917 4.917 0 0 1 3.782-1.79 4.852 4.852 0 0 1 4.57 3.303 3.949 3.949 0 0 1 2.735 5.293 4.058 4.058 0 0 1-1.474 7.62H4.76a3.67 3.67 0 0 1-1.27-7.116 4.33 4.33 0 0 1-.094-.912A4.386 4.386 0 0 1 7.5 7.505a4.37 4.37 0 0 1 2.506 5.913z', color: '#00A1E0' },
    'PayPal': { viewBox: '0 0 24 24', path: 'M7.076 21.337H2.47a.641.641 0 0 1-.633-.74L4.944.901C5.026.382 5.474 0 5.998 0h7.46c2.57 0 4.578.543 5.69 1.81 1.01 1.15 1.304 2.42 1.012 4.287-.023.143-.047.288-.077.437-.983 5.05-4.349 6.797-8.647 6.797h-2.19c-.524 0-.968.382-1.05.9l-1.12 7.106zm14.146-14.42a3.35 3.35 0 0 0-.607-.541c-.013.076-.026.175-.041.254-.93 4.778-4.005 7.201-9.138 7.201h-2.19a.563.563 0 0 0-.556.479l-1.187 7.527h-.506l-.24 1.516a.56.56 0 0 0 .554.647h3.882c.46 0 .85-.334.922-.788.06-.26.76-4.852.816-5.09a.932.932 0 0 1 .923-.788h.58c3.76 0 6.705-1.528 7.565-5.946.36-1.847.174-3.388-.777-4.471z', color: '#00457C' },

    // Design
    'Figma': { viewBox: '0 0 24 24', path: 'M15.852 8.981h-4.588V0h4.588c2.476 0 4.49 2.014 4.49 4.49s-2.014 4.491-4.49 4.491zM12.735 7.51h3.117c1.665 0 3.019-1.355 3.019-3.019s-1.354-3.02-3.019-3.02h-3.117V7.51zm0 1.471H8.148c-2.476 0-4.49-2.014-4.49-4.49S5.672 0 8.148 0h4.588v8.981zm-4.587-7.51c-1.665 0-3.019 1.355-3.019 3.02s1.354 3.02 3.019 3.02h3.117V1.471H8.148zm4.587 15.019H8.148c-2.476 0-4.49-2.014-4.49-4.49s2.014-4.49 4.49-4.49h4.588v8.98zM8.148 8.981c-1.665 0-3.019 1.355-3.019 3.019s1.354 3.019 3.019 3.019h3.117V8.981H8.148zM8.172 24c-2.489 0-4.515-2.014-4.515-4.49s2.014-4.49 4.49-4.49h4.588v4.441c0 2.503-2.047 4.539-4.563 4.539zm-.024-7.51a3.023 3.023 0 0 0-3.019 3.02c0 1.665 1.365 3.019 3.044 3.019 1.705 0 3.093-1.376 3.093-3.068v-2.97H8.148zm7.704 0h-.098c-2.476 0-4.49-2.014-4.49-4.49s2.014-4.49 4.49-4.49h.098c2.476 0 4.49 2.014 4.49 4.49s-2.014 4.49-4.49 4.49zm-.098-7.509c-1.665 0-3.019 1.355-3.019 3.019s1.354 3.019 3.019 3.019h.098c1.665 0 3.019-1.355 3.019-3.019s-1.354-3.019-3.019-3.019h-.098z', color: '#F24E1E' },
    'Canva': { viewBox: '0 0 24 24', path: 'M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0zm5.894 17.703c-1.035.455-2.239.703-3.583.703-4.142 0-7.127-2.68-7.127-6.406 0-2.746 1.827-5.197 4.59-6.247 1.048-.396 2.164-.593 3.327-.593 2.042 0 3.736.605 4.769 1.703.435.464.653.983.653 1.553 0 .817-.49 1.364-1.266 1.364-.558 0-.98-.267-1.304-.817-.504-.871-1.323-1.312-2.432-1.312-2.096 0-3.58 1.634-3.58 3.947 0 2.377 1.576 4.027 3.831 4.027 1.06 0 2.001-.337 2.808-1.002.43-.357.856-.532 1.267-.532.708 0 1.229.5 1.229 1.178 0 .49-.232.95-.683 1.365-.605.56-1.43 1.069-2.499 1.069z', color: '#00C4CC' },
    'Adobe Creative Cloud': { viewBox: '0 0 24 24', path: 'M13.966 22.624l-1.69-4.281H8.122l3.892-9.144 5.662 13.425h-3.71zm8.033-12.97l-9.746 14.04a.78.78 0 0 1-.638.326H.779a.78.78 0 0 1-.649-1.214L11.624.33a.78.78 0 0 1 1.298 0l9.076 9.324z', color: '#FF0000' },
    'Miro': { viewBox: '0 0 24 24', path: 'M17.392 0H13.9L17 4.808 10.444 0H6.949l3.102 6.3L3.494 0H0l6.75 12L0 24h3.494l6.556-13.2L6.949 24h3.494L17 12.9 13.9 24h3.492L24 12 17.392 0z', color: '#FFD02F' },
    'Sketch': { viewBox: '0 0 24 24', path: 'M12 1.5L1.5 8.85 12 22.5l10.5-13.65L12 1.5zM4.77 8.745L12 3.27l7.23 5.475L12 19.665 4.77 8.745z', color: '#F7B500' },
  };

  // Helper function to get icon
  function getIcon(name) {
    return integrationIcons[name] || null;
  }

  const featuredIntegrations = [
    { name: 'Gmail', desc: 'Read, send, and organize emails with natural language', category: 'Productivity', status: 'available', popular: true },
    { name: 'Notion', desc: 'Create and manage pages, databases, and wikis', category: 'Productivity', status: 'available', popular: true },
    { name: 'GitHub', desc: 'Manage repos, issues, PRs, and code reviews', category: 'Development', status: 'available', popular: true },
    { name: 'Slack', desc: 'Send messages and interact with channels', category: 'Communication', status: 'available', popular: true },
  ];

  const integrations = [
    // Productivity
    { name: 'Gmail', desc: 'Read, send, and organize emails', category: 'Productivity', status: 'available', capabilities: ['Read emails', 'Send emails', 'Search inbox', 'Manage labels'] },
    { name: 'Google Calendar', desc: 'Manage events and schedules', category: 'Productivity', status: 'available', capabilities: ['View events', 'Create events', 'Update events', 'Check availability'] },
    { name: 'Google Drive', desc: 'Access and manage files', category: 'Productivity', status: 'available', capabilities: ['Search files', 'Read docs', 'Create files', 'Share files'] },
    { name: 'Notion', desc: 'Create and edit pages, databases', category: 'Productivity', status: 'available', capabilities: ['Create pages', 'Query databases', 'Update content', 'Search'] },
    { name: 'Todoist', desc: 'Manage tasks and projects', category: 'Productivity', status: 'available', capabilities: ['Create tasks', 'Complete tasks', 'View projects', 'Set reminders'] },
    { name: 'Trello', desc: 'Manage boards and cards', category: 'Productivity', status: 'available', capabilities: ['Create cards', 'Move cards', 'View boards', 'Add comments'] },
    { name: 'Asana', desc: 'Project management', category: 'Productivity', status: 'available', capabilities: ['Create tasks', 'Assign tasks', 'Track progress', 'View projects'] },
    { name: 'Airtable', desc: 'Query and update bases', category: 'Productivity', status: 'available', capabilities: ['Query records', 'Create records', 'Update fields', 'Search bases'] },
    { name: 'Evernote', desc: 'Notes and notebooks', category: 'Productivity', status: 'coming', capabilities: ['Search notes', 'Create notes', 'Organize notebooks'] },
    { name: 'Dropbox', desc: 'File storage and sharing', category: 'Productivity', status: 'available', capabilities: ['Upload files', 'Download files', 'Share links', 'Search'] },
    { name: 'Microsoft Outlook', desc: 'Email and calendar', category: 'Productivity', status: 'available', capabilities: ['Read emails', 'Send emails', 'Manage calendar', 'Search'] },
    { name: 'Obsidian', desc: 'Knowledge management', category: 'Productivity', status: 'coming', capabilities: ['Read notes', 'Create notes', 'Search vault'] },

    // Development
    { name: 'GitHub', desc: 'Repos, issues, pull requests', category: 'Development', status: 'available', capabilities: ['View repos', 'Manage issues', 'Review PRs', 'Search code'] },
    { name: 'GitLab', desc: 'DevOps platform', category: 'Development', status: 'available', capabilities: ['Manage repos', 'CI/CD pipelines', 'Issues', 'Merge requests'] },
    { name: 'Linear', desc: 'Issue tracking', category: 'Development', status: 'available', capabilities: ['Create issues', 'Update status', 'View sprints', 'Search'] },
    { name: 'Jira', desc: 'Project management', category: 'Development', status: 'available', capabilities: ['Create tickets', 'Update issues', 'View boards', 'Search'] },
    { name: 'Vercel', desc: 'Deployment platform', category: 'Development', status: 'available', capabilities: ['View deployments', 'Check logs', 'Manage projects'] },
    { name: 'Supabase', desc: 'Database and auth', category: 'Development', status: 'available', capabilities: ['Query data', 'Manage tables', 'View analytics'] },
    { name: 'Railway', desc: 'App deployment', category: 'Development', status: 'coming', capabilities: ['Deploy apps', 'View logs', 'Manage services'] },
    { name: 'Netlify', desc: 'Web hosting', category: 'Development', status: 'available', capabilities: ['View sites', 'Check builds', 'Manage deploys'] },
    { name: 'Sentry', desc: 'Error monitoring', category: 'Development', status: 'available', capabilities: ['View errors', 'Track issues', 'Performance data'] },
    { name: 'CircleCI', desc: 'CI/CD platform', category: 'Development', status: 'coming', capabilities: ['View pipelines', 'Trigger builds', 'Check status'] },

    // Communication
    { name: 'Slack', desc: 'Messages and channels', category: 'Communication', status: 'available', capabilities: ['Send messages', 'Read channels', 'Search', 'Manage channels'] },
    { name: 'Discord', desc: 'Servers and messages', category: 'Communication', status: 'available', capabilities: ['Send messages', 'Read channels', 'Manage servers'] },
    { name: 'Microsoft Teams', desc: 'Team collaboration', category: 'Communication', status: 'available', capabilities: ['Send messages', 'View chats', 'Manage teams'] },
    { name: 'Zoom', desc: 'Video meetings', category: 'Communication', status: 'coming', capabilities: ['Schedule meetings', 'View recordings', 'Manage participants'] },
    { name: 'Telegram', desc: 'Messaging', category: 'Communication', status: 'available', capabilities: ['Send messages', 'Read chats', 'Manage groups'] },
    { name: 'WhatsApp Business', desc: 'Business messaging', category: 'Communication', status: 'coming', capabilities: ['Send messages', 'View conversations'] },
    { name: 'Intercom', desc: 'Customer messaging', category: 'Communication', status: 'available', capabilities: ['View conversations', 'Send replies', 'Search users'] },

    // Data
    { name: 'PostgreSQL', desc: 'Database queries', category: 'Data', status: 'available', capabilities: ['Query data', 'Insert records', 'Update data', 'Schema info'] },
    { name: 'MongoDB', desc: 'Document database', category: 'Data', status: 'available', capabilities: ['Query docs', 'Insert docs', 'Update docs', 'Aggregations'] },
    { name: 'Google Sheets', desc: 'Spreadsheets', category: 'Data', status: 'available', capabilities: ['Read data', 'Write data', 'Create sheets', 'Formulas'] },
    { name: 'Snowflake', desc: 'Data warehouse', category: 'Data', status: 'available', capabilities: ['Query data', 'View schemas', 'Run analytics'] },
    { name: 'BigQuery', desc: 'Analytics', category: 'Data', status: 'available', capabilities: ['Run queries', 'View datasets', 'Export data'] },
    { name: 'Elasticsearch', desc: 'Search engine', category: 'Data', status: 'available', capabilities: ['Search docs', 'Index data', 'Aggregations'] },
    { name: 'Redis', desc: 'Cache and data store', category: 'Data', status: 'coming', capabilities: ['Get/set keys', 'View data', 'Manage cache'] },

    // Social
    { name: 'Twitter/X', desc: 'Post and read tweets', category: 'Social', status: 'available', capabilities: ['Post tweets', 'Read timeline', 'Search', 'Manage lists'] },
    { name: 'Reddit', desc: 'Browse and post', category: 'Social', status: 'available', capabilities: ['Browse subreddits', 'Post content', 'Comment', 'Search'] },
    { name: 'LinkedIn', desc: 'Professional network', category: 'Social', status: 'coming', capabilities: ['View posts', 'Share content', 'Manage connections'] },
    { name: 'Instagram', desc: 'Photo sharing', category: 'Social', status: 'coming', capabilities: ['View feed', 'Post content', 'Manage stories'] },
    { name: 'YouTube', desc: 'Video platform', category: 'Social', status: 'available', capabilities: ['Search videos', 'View analytics', 'Manage playlists'] },
    { name: 'Spotify', desc: 'Music streaming', category: 'Social', status: 'available', capabilities: ['Search music', 'Create playlists', 'View history'] },
    { name: 'TikTok', desc: 'Short video platform', category: 'Social', status: 'coming', capabilities: ['View analytics', 'Manage content'] },

    // Finance
    { name: 'Stripe', desc: 'Payments and billing', category: 'Finance', status: 'available', capabilities: ['View payments', 'Manage subscriptions', 'Refunds', 'Analytics'] },
    { name: 'QuickBooks', desc: 'Accounting', category: 'Finance', status: 'available', capabilities: ['View invoices', 'Track expenses', 'Reports'] },
    { name: 'Xero', desc: 'Accounting software', category: 'Finance', status: 'available', capabilities: ['Manage invoices', 'Track payments', 'Reports'] },
    { name: 'Plaid', desc: 'Banking data', category: 'Finance', status: 'available', capabilities: ['View accounts', 'Transaction history', 'Balance'] },
    { name: 'HubSpot', desc: 'CRM and marketing', category: 'Finance', status: 'available', capabilities: ['Manage contacts', 'Track deals', 'View analytics'] },
    { name: 'Salesforce', desc: 'CRM platform', category: 'Finance', status: 'available', capabilities: ['Manage leads', 'Track opportunities', 'Reports'] },
    { name: 'PayPal', desc: 'Payment processing', category: 'Finance', status: 'coming', capabilities: ['View transactions', 'Send payments', 'Invoices'] },

    // Design
    { name: 'Figma', desc: 'Design and prototyping', category: 'Design', status: 'available', capabilities: ['View files', 'Export assets', 'Comments', 'Components'] },
    { name: 'Canva', desc: 'Graphic design', category: 'Design', status: 'coming', capabilities: ['Create designs', 'Edit templates', 'Export'] },
    { name: 'Adobe Creative Cloud', desc: 'Creative tools', category: 'Design', status: 'coming', capabilities: ['Access files', 'Manage assets'] },
    { name: 'Miro', desc: 'Whiteboard collaboration', category: 'Design', status: 'available', capabilities: ['View boards', 'Add content', 'Collaborate'] },
    { name: 'Sketch', desc: 'Design tool', category: 'Design', status: 'coming', capabilities: ['View files', 'Export assets', 'Artboards'] },
  ];

  const stats = [
    { value: '200+', label: 'Total Integrations' },
    { value: '150+', label: 'Available Now' },
    { value: '50+', label: 'Coming Soon' },
    { value: '48h', label: 'Avg Request Time' },
  ];

  const categoryDescriptions = {
    'Productivity': 'Email, calendar, documents, and task management tools to boost your workflow.',
    'Development': 'Code repos, CI/CD, issue tracking, and deployment platforms for developers.',
    'Communication': 'Messaging, team chat, and collaboration tools to stay connected.',
    'Data': 'Databases, warehouses, and analytics platforms for data access.',
    'Social': 'Social media, content platforms, and community tools.',
    'Finance': 'Payments, accounting, CRM, and financial management.',
    'Design': 'Design tools, prototyping, and creative collaboration.',
  };

  const filteredIntegrations = $derived(
    integrations.filter(int => {
      const matchesSearch = int.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
                           int.desc.toLowerCase().includes(searchQuery.toLowerCase());
      const matchesCategory = selectedCategory === 'All' || int.category === selectedCategory;
      return matchesSearch && matchesCategory;
    })
  );

  const availableCount = $derived(filteredIntegrations.filter(i => i.status === 'available').length);
  const comingCount = $derived(filteredIntegrations.filter(i => i.status === 'coming').length);

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
  <title>Integrations - Hub MCP</title>
  <meta name="description" content="Browse 200+ MCP integrations. Connect Claude to Gmail, Notion, Slack, GitHub, and hundreds more. One click to connect." />
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
          <a href="/about" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-neutral-200 hover:text-[#dc7c6a] transition-colors">About</a>
          <a href="/integrations" onclick={() => mobileMenuOpen = false} class="text-2xl font-bold text-[#dc7c6a]">Integrations</a>
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
        <a href="/about" class="text-neutral-400 hover:text-[#dc7c6a] transition-colors duration-300">about</a>
        <a href="/integrations" class="text-[#dc7c6a] transition-colors duration-300">integrations</a>
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
  <section class="relative pt-28 sm:pt-32 pb-8 sm:pb-12 px-4 sm:px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-8 sm:mb-12 {mounted ? 'animate-fade-in-up' : 'opacity-0'}">
        <span class="inline-flex items-center gap-2 text-[#dc7c6a] font-mono text-sm mb-4 px-3 py-1 rounded-full bg-[#dc7c6a]/10 border border-[#dc7c6a]/20">
          <span class="w-2 h-2 rounded-full bg-[#dc7c6a] animate-pulse"></span>
          INTEGRATIONS
        </span>
        <h1 class="text-3xl sm:text-4xl lg:text-6xl font-black text-neutral-100 leading-tight mb-6">
          mcp<span class="text-[#dc7c6a]">:</span>catalog
        </h1>
        <p class="text-lg sm:text-xl text-neutral-400 max-w-2xl mx-auto">
          200+ integrations. One click to connect. Browse our complete catalog of MCP servers below.
        </p>
      </div>

      <!-- Stats -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-3 sm:gap-4 max-w-3xl mx-auto mb-8 sm:mb-12 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 100ms">
        {#each stats as stat, i}
          <div class="relative group">
            <div class="absolute inset-0 bg-gradient-to-r from-[#dc7c6a]/20 to-transparent rounded-xl opacity-0 group-hover:opacity-100 blur-xl transition-all duration-500"></div>
            <div class="relative text-center p-4 rounded-xl bg-[#141414]/80 backdrop-blur-sm border border-[#1f1f1f] hover:border-[#dc7c6a]/30 transition-all duration-300">
              <p class="text-xl sm:text-2xl font-bold text-[#dc7c6a]">{stat.value}</p>
              <p class="text-xs text-neutral-500 mt-1">{stat.label}</p>
            </div>
          </div>
        {/each}
      </div>

      <!-- Search and Filter -->
      <div class="max-w-3xl mx-auto mb-8 {mounted ? 'animate-fade-in-up' : 'opacity-0'}" style="animation-delay: 200ms">
        <!-- Search -->
        <div class="relative mb-6">
          <svg class="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-neutral-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
          <input
            type="text"
            placeholder="Search integrations by name or description..."
            bind:value={searchQuery}
            class="w-full pl-12 pr-4 py-4 bg-[#141414] border border-[#1f1f1f] rounded-xl focus:outline-none focus:border-[#dc7c6a] transition-colors text-neutral-200 placeholder:text-neutral-600"
          />
          {#if searchQuery}
            <button
              class="absolute right-4 top-1/2 -translate-y-1/2 text-neutral-500 hover:text-neutral-300"
              onclick={() => searchQuery = ''}
              aria-label="Clear search"
            >
              <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          {/if}
        </div>

        <!-- Categories -->
        <div class="flex flex-wrap gap-2 justify-center">
          {#each categories as category}
            <button
              class="px-4 py-2 rounded-lg text-sm font-medium transition-all duration-300 {selectedCategory === category ? 'bg-[#dc7c6a] text-white' : 'bg-[#141414] text-neutral-400 hover:text-neutral-200 border border-[#1f1f1f] hover:border-[#dc7c6a]/30'}"
              onclick={() => selectedCategory = category}
            >
              {category}
            </button>
          {/each}
        </div>

        <!-- Category Description -->
        {#if selectedCategory !== 'All'}
          <div class="mt-4 text-center">
            <p class="text-neutral-500 text-sm">{categoryDescriptions[selectedCategory]}</p>
          </div>
        {/if}
      </div>
    </div>
  </section>

  <!-- Featured Integrations -->
  {#if selectedCategory === 'All' && !searchQuery}
    <section class="pb-12 px-6 lg:px-12">
      <div class="max-w-7xl mx-auto">
        <div class="flex items-center gap-3 mb-6">
          <span class="text-[#dc7c6a] font-mono text-sm">MOST POPULAR</span>
          <div class="flex-1 h-px bg-[#1f1f1f]"></div>
        </div>
        <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-4">
          {#each featuredIntegrations as integration, i}
            {@const icon = getIcon(integration.name)}
            <div class="animate-on-scroll opacity-0 translate-y-4 transition-all duration-500" style="transition-delay: {i * 50}ms">
              <div class="group p-5 bg-gradient-to-br from-[#dc7c6a]/5 to-transparent rounded-xl border border-[#dc7c6a]/30 hover:border-[#dc7c6a]/50 transition-all duration-300 cursor-pointer h-full">
                <div class="flex items-start justify-between mb-3">
                  <div class="w-12 h-12 rounded-xl flex items-center justify-center group-hover:scale-110 transition-transform duration-300" style="background-color: {icon ? icon.color + '15' : 'rgba(220, 124, 106, 0.2)'}">
                    {#if icon}
                      <svg class="w-6 h-6" viewBox={icon.viewBox} fill={icon.darkColor || icon.color}>
                        <path d={icon.path} />
                      </svg>
                    {:else}
                      <span class="text-[#dc7c6a] font-bold">{integration.name[0]}</span>
                    {/if}
                  </div>
                  <span class="text-xs px-2 py-1 rounded-full bg-[#dc7c6a]/20 text-[#dc7c6a]">Popular</span>
                </div>
                <h3 class="font-bold text-neutral-100 group-hover:text-[#dc7c6a] transition-colors">{integration.name}</h3>
                <p class="text-sm text-neutral-400 mt-1">{integration.desc}</p>
              </div>
            </div>
          {/each}
        </div>
      </div>
    </section>
  {/if}

  <!-- Integrations Grid -->
  <section class="pb-20 px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="flex items-center justify-between mb-6">
        <div class="flex items-center gap-4">
          <p class="text-neutral-400 text-sm">
            <span class="text-neutral-200 font-medium">{filteredIntegrations.length}</span> integrations
            {#if selectedCategory !== 'All'}
              in <span class="text-[#dc7c6a]">{selectedCategory}</span>
            {/if}
          </p>
          <div class="flex items-center gap-3 text-xs">
            <span class="flex items-center gap-1.5">
              <span class="w-2 h-2 rounded-full bg-green-400"></span>
              <span class="text-neutral-500">{availableCount} available</span>
            </span>
            <span class="flex items-center gap-1.5">
              <span class="w-2 h-2 rounded-full bg-yellow-400"></span>
              <span class="text-neutral-500">{comingCount} coming</span>
            </span>
          </div>
        </div>
      </div>

      <div class="grid md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
        {#each filteredIntegrations as integration, i}
          {@const icon = getIcon(integration.name)}
          <div class="animate-on-scroll opacity-0 translate-y-4 transition-all duration-500" style="transition-delay: {Math.min(i * 30, 300)}ms">
            <div class="group p-5 bg-[#141414] rounded-xl border border-[#1f1f1f] hover:border-[#dc7c6a]/40 transition-all duration-300 cursor-pointer h-full">
              <div class="flex items-start justify-between mb-3">
                <div class="w-10 h-10 rounded-lg flex items-center justify-center group-hover:scale-110 transition-transform duration-300" style="background-color: {icon ? icon.color + '15' : 'rgba(220, 124, 106, 0.1)'}">
                  {#if icon}
                    <svg class="w-5 h-5" viewBox={icon.viewBox} fill={icon.darkColor || icon.color}>
                      <path d={icon.path} />
                    </svg>
                  {:else}
                    <span class="text-[#dc7c6a] text-sm font-bold">{integration.name[0]}</span>
                  {/if}
                </div>
                <span class="text-xs px-2 py-1 rounded-full {integration.status === 'available' ? 'bg-green-500/10 text-green-400' : 'bg-yellow-500/10 text-yellow-400'}">
                  {integration.status === 'available' ? 'Available' : 'Coming Soon'}
                </span>
              </div>
              <h3 class="font-semibold text-neutral-100 group-hover:text-[#dc7c6a] transition-colors">{integration.name}</h3>
              <p class="text-sm text-neutral-500 mt-1">{integration.desc}</p>

              <!-- Capabilities Preview -->
              {#if integration.capabilities}
                <div class="mt-3 pt-3 border-t border-[#1f1f1f]">
                  <div class="flex flex-wrap gap-1">
                    {#each integration.capabilities.slice(0, 3) as cap}
                      <span class="text-xs px-2 py-0.5 rounded bg-[#1a1a1a] text-neutral-500">{cap}</span>
                    {/each}
                    {#if integration.capabilities.length > 3}
                      <span class="text-xs px-2 py-0.5 rounded bg-[#1a1a1a] text-neutral-600">+{integration.capabilities.length - 3}</span>
                    {/if}
                  </div>
                </div>
              {/if}

              <p class="text-xs text-neutral-600 mt-3 font-mono">{integration.category}</p>
            </div>
          </div>
        {/each}
      </div>

      {#if filteredIntegrations.length === 0}
        <div class="text-center py-16">
          <div class="w-16 h-16 rounded-full bg-[#141414] flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-neutral-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
            </svg>
          </div>
          <p class="text-neutral-400 mb-2">No integrations found</p>
          <p class="text-neutral-600 text-sm">Try adjusting your search or filter</p>
          <button
            class="mt-4 px-4 py-2 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-300 rounded-lg text-sm transition-colors"
            onclick={() => { searchQuery = ''; selectedCategory = 'All'; }}
          >
            Clear filters
          </button>
        </div>
      {/if}
    </div>
  </section>

  <!-- Request Integration -->
  <section class="py-20 px-6 lg:px-12 bg-[#0a0a0a]">
    <div class="max-w-4xl mx-auto">
      <div class="grid md:grid-cols-2 gap-8 items-center">
        <div class="animate-on-scroll opacity-0 translate-y-8 transition-all duration-700">
          <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">REQUEST AN INTEGRATION</span>
          <h2 class="text-2xl lg:text-3xl font-bold text-neutral-100 mb-4">
            Don't see what you need?
          </h2>
          <p class="text-neutral-400 mb-6">
            We add new integrations every week based on user requests. Tell us what you need and we'll prioritize it. Most integrations are added within 48 hours.
          </p>
          <ul class="space-y-3 text-sm">
            <li class="flex items-center gap-3 text-neutral-400">
              <svg class="w-5 h-5 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
              48-hour average turnaround
            </li>
            <li class="flex items-center gap-3 text-neutral-400">
              <svg class="w-5 h-5 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
              Priority given to popular requests
            </li>
            <li class="flex items-center gap-3 text-neutral-400">
              <svg class="w-5 h-5 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
              We'll notify you when it's ready
            </li>
          </ul>
        </div>
        <div class="animate-on-scroll opacity-0 translate-y-8 transition-all duration-700 delay-100">
          <div class="p-6 rounded-2xl bg-[#141414] border border-[#1f1f1f]">
            <h3 class="font-bold text-neutral-200 mb-4">Request an Integration</h3>
            <div class="space-y-4">
              <input
                type="text"
                placeholder="Integration name (e.g., Notion)"
                class="w-full px-4 py-3 bg-[#0d0d0d] border border-[#2e2e2e] rounded-lg text-neutral-200 placeholder:text-neutral-600 focus:outline-none focus:border-[#dc7c6a]"
              />
              <textarea
                placeholder="What would you use it for? (optional)"
                rows="3"
                class="w-full px-4 py-3 bg-[#0d0d0d] border border-[#2e2e2e] rounded-lg text-neutral-200 placeholder:text-neutral-600 focus:outline-none focus:border-[#dc7c6a] resize-none"
              ></textarea>
              <button class="w-full py-3 bg-[#dc7c6a] hover:bg-[#c96b5a] text-white font-semibold rounded-lg transition-colors">
                Submit Request
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Community MCPs -->
  <section class="py-20 px-6 lg:px-12">
    <div class="max-w-7xl mx-auto">
      <div class="text-center mb-12 animate-on-scroll opacity-0 translate-y-8 transition-all duration-700">
        <span class="text-[#dc7c6a] font-mono text-sm mb-4 block">COMMUNITY</span>
        <h2 class="text-2xl lg:text-3xl font-bold text-neutral-100 mb-4">
          Build your own MCP? We'll host it.
        </h2>
        <p class="text-neutral-400 max-w-2xl mx-auto">
          Hub MCP supports community-built MCP servers. Build an integration for your favorite service and we'll host it for free on our infrastructure.
        </p>
      </div>

      <div class="grid md:grid-cols-3 gap-6">
        <div class="animate-on-scroll opacity-0 translate-y-8 transition-all duration-700 p-6 rounded-2xl bg-[#141414] border border-[#1f1f1f] text-center">
          <div class="w-12 h-12 rounded-xl bg-[#dc7c6a]/10 flex items-center justify-center mx-auto mb-4">
            <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M17.25 6.75L22.5 12l-5.25 5.25m-10.5 0L1.5 12l5.25-5.25m7.5-3l-4.5 16.5" />
            </svg>
          </div>
          <h3 class="font-bold text-neutral-100 mb-2">Open Source</h3>
          <p class="text-neutral-500 text-sm">All our hosted MCPs are open source. Fork, modify, contribute.</p>
        </div>
        <div class="animate-on-scroll opacity-0 translate-y-8 transition-all duration-700 delay-100 p-6 rounded-2xl bg-[#141414] border border-[#1f1f1f] text-center">
          <div class="w-12 h-12 rounded-xl bg-[#dc7c6a]/10 flex items-center justify-center mx-auto mb-4">
            <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M2.25 15a4.5 4.5 0 004.5 4.5H18a3.75 3.75 0 001.332-7.257 3 3 0 00-3.758-3.848 5.25 5.25 0 00-10.233 2.33A4.502 4.502 0 002.25 15z" />
            </svg>
          </div>
          <h3 class="font-bold text-neutral-100 mb-2">Free Hosting</h3>
          <p class="text-neutral-500 text-sm">We host community MCPs for free. No server costs for you.</p>
        </div>
        <div class="animate-on-scroll opacity-0 translate-y-8 transition-all duration-700 delay-200 p-6 rounded-2xl bg-[#141414] border border-[#1f1f1f] text-center">
          <div class="w-12 h-12 rounded-xl bg-[#dc7c6a]/10 flex items-center justify-center mx-auto mb-4">
            <svg class="w-6 h-6 text-[#dc7c6a]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M18 18.72a9.094 9.094 0 003.741-.479 3 3 0 00-4.682-2.72m.94 3.198l.001.031c0 .225-.012.447-.037.666A11.944 11.944 0 0112 21c-2.17 0-4.207-.576-5.963-1.584A6.062 6.062 0 016 18.719m12 0a5.971 5.971 0 00-.941-3.197m0 0A5.995 5.995 0 0012 12.75a5.995 5.995 0 00-5.058 2.772m0 0a3 3 0 00-4.681 2.72 8.986 8.986 0 003.74.477m.94-3.197a5.971 5.971 0 00-.94 3.197M15 6.75a3 3 0 11-6 0 3 3 0 016 0zm6 3a2.25 2.25 0 11-4.5 0 2.25 2.25 0 014.5 0zm-13.5 0a2.25 2.25 0 11-4.5 0 2.25 2.25 0 014.5 0z" />
            </svg>
          </div>
          <h3 class="font-bold text-neutral-100 mb-2">Community Credit</h3>
          <p class="text-neutral-500 text-sm">Get credited for your work. Build reputation in the MCP community.</p>
        </div>
      </div>

      <div class="text-center mt-8 animate-on-scroll opacity-0 translate-y-8 transition-all duration-700">
        <a href="mailto:community@mcphub.io" class="inline-flex items-center gap-2 px-6 py-3 bg-[#1a1a1a] hover:bg-[#242424] text-neutral-200 font-semibold rounded-xl border border-[#2e2e2e] transition-all duration-300">
          Submit Your MCP
          <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
      </div>
    </div>
  </section>

  <!-- CTA -->
  <section class="py-20 px-6 lg:px-12 bg-[#0a0a0a]">
    <div class="max-w-3xl mx-auto text-center animate-on-scroll opacity-0 translate-y-8 transition-all duration-700">
      <h2 class="text-3xl lg:text-4xl font-bold text-neutral-100 mb-4">
        Ready to connect Claude to everything?
      </h2>
      <p class="text-neutral-400 text-lg mb-8">
        Join the waitlist and be first to access all 200+ integrations.
      </p>
      <a href="/#waitlist" class="inline-flex items-center gap-2 px-6 py-3 bg-[#dc7c6a] hover:bg-[#c96b5a] text-white font-semibold rounded-xl transition-all duration-300 hover:-translate-y-0.5">
        Join the Waitlist
        <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
        </svg>
      </a>
    </div>
  </section>

  <!-- Footer -->
  <footer class="py-12 px-6 lg:px-12 border-t border-[#1a1a1a]">
    <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between gap-6">
      <div class="flex items-center gap-3">
        <img src="/logo.png" alt="Hub MCP" class="w-8 h-8 rounded-lg" />
        <span class="font-bold text-neutral-300">Hub MCP</span>
      </div>
      <div class="flex items-center gap-6 text-sm text-neutral-500">
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
    0%, 100% { transform: translate(0, 0) rotate(0deg); }
    25% { transform: translate(-25px, 25px) rotate(5deg); }
    50% { transform: translate(25px, -15px) rotate(-5deg); }
    75% { transform: translate(-15px, -25px) rotate(3deg); }
  }

  @keyframes float-particle {
    0%, 100% { transform: translate(0, 0); opacity: 0.3; }
    25% { transform: translate(10px, -20px); opacity: 0.6; }
    50% { transform: translate(-10px, -40px); opacity: 0.4; }
    75% { transform: translate(15px, -60px); opacity: 0.5; }
  }

  .animate-float-slow { animation: float-slow 25s ease-in-out infinite; }
  .animate-float-medium { animation: float-medium 18s ease-in-out infinite; }
  .animate-fade-in-up { animation: fade-in-up 0.8s ease-out forwards; }
  .animate-on-scroll { opacity: 0; transform: translateY(16px); transition: all 0.5s ease-out; }
  .animate-on-scroll:global(.animate-in) { opacity: 1; transform: translateY(0); }

  :global(html) { scroll-behavior: smooth; }
</style>
