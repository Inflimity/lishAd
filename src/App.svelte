<script>
  import { 
    Megaphone, 
    Target, 
    Image as ImageIcon, 
    Copy, 
    Check, 
    Sparkles, 
    RefreshCw, 
    Eye, 
    Video, 
    Layout, 
    Code2, 
    Palette, 
    MonitorPlay 
  } from 'lucide-svelte';

  // State using runes
  let activeTab = $state('strategy');
  let generating = $state(false);
  let generatedImage = $state(null);
  let copyFeedback = $state('');
  let error = $state(null);

  // Constants
  const BRAND_COLORS = {
    green: '#228B22',
    cream: '#F5F5DC',
    darkGreen: '#1a6b1a'
  };

  const adCampaigns = [
    {
      id: 'lish-agency',
      title: 'LISH Agency - Full Stack Dev',
      primaryText: "Stop settling for 'good enough' web presence. At LISH, we build high-performance, full-stack solutions that turn visitors into customers. From custom PHP backends to sleek React frontends, we engineer growth.",
      headline: "Your Digital Identity, Engineered by LISH",
      cta: "Book a Consultation",
      targeting: "Business Owners, Tech Founders, Digital Marketing Managers (Ages 25-55)",
      videoScript: [
        { time: "0-2s", visual: "Solid Green screen with 'LISH' in Cream text. Sharp, clean entry.", audio: "Looking for a website that actually works?" },
        { time: "2-6s", visual: "Rapid scrolling of Pathfinder AI and MC Beverly Hills screenshots over Cream background.", audio: "We build intelligence-driven platforms and corporate identities." },
        { time: "6-10s", visual: "PHP and JavaScript code snippets appearing like digital architecture.", audio: "From custom backends to high-converting frontends." },
        { time: "10-15s", visual: "LISH logo with 'Let's Build' call to action. Solid Green & Cream.", audio: "LISH. Your growth, engineered. Click below." }
      ]
    },
    {
      id: 'pathfinder-ai',
      title: 'Pathfinder AI - Career Tool',
      primaryText: "The corporate world is a maze. Pathfinder AI is your map. Navigate your career with intelligence-driven insights tailored for professionals who want to reach the top faster.",
      headline: "Master Your Career Path with AI",
      cta: "Try Pathfinder AI",
      targeting: "Corporate Employees, HR Professionals, MBA Students, Career Switchers",
      videoScript: [
        { time: "0-3s", visual: "AI network nodes appearing in solid green on cream.", audio: "Stop guessing your next career move." },
        { time: "3-8s", visual: "Pathfinder AI dashboard preview showing career navigation features.", audio: "Get intelligence-driven insights to navigate the corporate maze." },
        { time: "8-12s", visual: "Success metrics and growth charts in solid green.", audio: "Reach your goals faster with Pathfinder AI." },
        { time: "12-15s", visual: "Pathfinder AI logo and 'Sign Up Free' button.", audio: "Start your journey today. Click learn more." }
      ]
    }
  ];

  let selectedAd = $state(adCampaigns[0]);

  const handleCopy = (text) => {
    navigator.clipboard.writeText(text).then(() => {
      copyFeedback = 'Copied!';
      setTimeout(() => copyFeedback = '', 2000);
    });
  };

  const generateImage = async () => {
    generating = true;
    error = null;
    const apiKey = ""; // API Key still empty as in original code

    const prompt = `A professional, minimalist high-resolution graphic for a full-stack development agency ad. Palette of solid forest green (#228B22) and vibrant cream (#F5F5DC). Clean UI components from high-end websites, sharp typography, and snippets of PHP and JavaScript code. No gradients, no shadows. High-quality corporate tech aesthetic, bright and airy. LISH logo integration.`;

    const fetchWithRetry = async (retries = 5, delay = 1000) => {
      try {
        const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/imagen-4.0-generate-001:predict?key=${apiKey}`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            instances: [{ prompt }],
            parameters: { sampleCount: 1 }
          })
        });

        if (!response.ok) throw new Error('Generation failed');
        const result = await response.json();
        return `data:image/png;base64,${result.predictions[0].bytesBase64Encoded}`;
      } catch (err) {
        if (retries > 0) {
          await new Promise(res => setTimeout(res, delay));
          return fetchWithRetry(retries - 1, delay * 2);
        }
        throw err;
      }
    };

    try {
      const imageUrl = await fetchWithRetry();
      generatedImage = imageUrl;
    } catch (err) {
      error = "Unable to generate image at this moment. Please try again.";
    } finally {
      generating = false;
    }
  };
</script>

<div class="min-h-screen bg-[#FDFDF8] text-gray-800 font-sans p-4 md:p-8">
  <!-- Header -->
  <header class="max-w-6xl mx-auto flex flex-col md:flex-row justify-between items-center mb-12 gap-4">
    <div class="flex items-center gap-3">
      <div 
        class="w-12 h-12 bg-[#228B22] rounded-lg flex items-center justify-center text-white font-bold text-2xl shadow-sm">
        L
      </div>
      <div>
        <h1 class="text-2xl font-bold text-[#1a6b1a]">LISH Ad Studio</h1>
        <p class="text-sm text-gray-500 italic">Musibau Bamidele Samson • Full-Stack Engineer</p>
      </div>
    </div>

    <nav class="flex bg-white rounded-full p-1 shadow-sm border border-gray-100 overflow-x-auto">
      {#each ['strategy', 'video', 'creative'] as tab}
        <button 
          onclick={() => activeTab = tab}
          class="px-6 py-2 rounded-full text-sm font-medium transition-all capitalize whitespace-nowrap {activeTab === tab ? 'bg-[#228B22] text-white shadow-md' : 'text-gray-500 hover:bg-gray-50'}"
        >
          {tab === 'video' ? 'Video Ad' : tab === 'creative' ? 'Static Creative' : 'Ad Strategy'}
        </button>
      {/each}
    </nav>
  </header>

  <main class="max-w-6xl mx-auto">
    {#if activeTab === 'strategy'}
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
        <div class="lg:col-span-7 space-y-6">
          <section class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
            <div class="flex items-center gap-2 mb-4">
              <Megaphone class="text-[#228B22] w-5 h-5" />
              <h2 class="text-lg font-semibold">Select Campaign Objective</h2>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              {#each adCampaigns as ad}
                <button 
                  onclick={() => selectedAd = ad}
                  class="p-4 rounded-xl text-left border-2 transition-all {selectedAd.id === ad.id ? 'border-[#228B22] bg-green-50' : 'border-gray-100 hover:border-gray-200'}"
                >
                  <h3 class="font-bold text-sm mb-1">{ad.title}</h3>
                  <p class="text-xs text-gray-500 line-clamp-2">{ad.primaryText}</p>
                </button>
              {/each}
            </div>
          </section>

          <section class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
            <div class="flex items-center justify-between mb-4">
              <div class="flex items-center gap-2">
                <Target class="text-[#228B22] w-5 h-5" />
                <h2 class="text-lg font-semibold">Ad Copy & Targeting</h2>
              </div>
              <button 
                onclick={() => handleCopy(`${selectedAd.primaryText}\n\n${selectedAd.headline}`)}
                class="text-xs text-[#228B22] hover:underline flex items-center gap-1"
              >
                {#if copyFeedback}
                  <Check size={14} />
                {:else}
                  <Copy size={14} />
                {/if}
                {copyFeedback || 'Copy All Text'}
              </button>
            </div>

            <div class="space-y-4">
              <div>
                <label class="text-xs font-bold text-gray-400 uppercase tracking-wider">Primary Text</label>
                <p class="mt-1 p-3 bg-gray-50 rounded-lg text-sm leading-relaxed border border-gray-100">
                  {selectedAd.primaryText}
                </p>
              </div>
              <div>
                <label class="text-xs font-bold text-gray-400 uppercase tracking-wider">Headline</label>
                <p class="mt-1 p-3 bg-gray-50 rounded-lg text-sm font-semibold border border-gray-100">
                  {selectedAd.headline}
                </p>
              </div>
              <div class="p-3 bg-[#F5F5DC] rounded-lg border border-[#e8e8c8]">
                <label class="text-xs font-bold text-green-800 uppercase tracking-wider">Targeting Recommendation</label>
                <p class="mt-1 text-sm text-green-900 font-medium">{selectedAd.targeting}</p>
              </div>
            </div>
          </section>
        </div>

        <div class="lg:col-span-5">
          <section class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 h-full">
            <div class="flex items-center gap-2 mb-4">
              <Palette class="text-[#228B22] w-5 h-5" />
              <h2 class="text-lg font-semibold">Branding Guardrails</h2>
            </div>
            <div class="space-y-6">
              <div class="flex items-center gap-4">
                <div class="w-16 h-16 rounded-xl bg-[#228B22] shadow-inner"></div>
                <div>
                  <h3 class="font-bold text-sm">Forest Green (#228B22)</h3>
                  <p class="text-xs text-gray-500">Represents growth and precision.</p>
                </div>
              </div>
              <div class="flex items-center gap-4">
                <div class="w-16 h-16 rounded-xl bg-[#F5F5DC] border border-gray-200"></div>
                <div>
                  <h3 class="font-bold text-sm">Solid Cream (#F5F5DC)</h3>
                  <p class="text-xs text-gray-500">Premium foundation color.</p>
                </div>
              </div>
              <div class="pt-4 border-t border-gray-100">
                <h4 class="text-sm font-bold mb-2">Technical Palette:</h4>
                <div class="flex gap-2 mb-4">
                  <span class="bg-[#228B22] text-white text-[10px] px-2 py-1 rounded">PHP</span>
                  <span class="bg-[#228B22] text-white text-[10px] px-2 py-1 rounded">C#</span>
                  <span class="bg-[#228B22] text-white text-[10px] px-2 py-1 rounded">React</span>
                </div>
                <ul class="text-xs space-y-2 text-gray-600">
                  <li class="flex items-center gap-2">
                    <Check size={14} class="text-[#228B22]" /> No Gradients
                  </li>
                  <li class="flex items-center gap-2">
                    <Check size={14} class="text-[#228B22]" /> No Shadows / Flat Design
                  </li>
                  <li class="flex items-center gap-2">
                    <Check size={14} class="text-[#228B22]" /> High-Visibility Content
                  </li>
                </ul>
              </div>
            </div>
          </section>
        </div>
      </div>
    {:else if activeTab === 'video'}
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
        <div class="lg:col-span-7">
          <section class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
            <div class="flex items-center gap-2 mb-6">
              <Video class="text-[#228B22] w-6 h-6" />
              <h2 class="text-xl font-bold">Video Ad Storyboard</h2>
            </div>

            <div class="space-y-4">
              {#each selectedAd.videoScript as scene, idx}
                <div class="flex gap-4 p-4 bg-gray-50 rounded-xl border border-gray-100 group">
                  <div class="w-16 h-16 bg-[#F5F5DC] rounded-lg border border-gray-200 flex-shrink-0 flex items-center justify-center font-bold text-[#228B22]">
                    {scene.time}
                  </div>
                  <div class="flex-1">
                    <h4 class="text-xs font-bold text-gray-400 uppercase mb-1">Visual Instruction</h4>
                    <p class="text-sm font-medium mb-2">{scene.visual}</p>
                    <h4 class="text-xs font-bold text-gray-400 uppercase mb-1">Audio/Voiceover</h4>
                    <p class="text-sm text-[#228B22] italic">"{scene.audio}"</p>
                  </div>
                </div>
              {/each}
            </div>
          </section>
        </div>

        <div class="lg:col-span-5">
          <section class="bg-[#228B22] text-white p-6 rounded-2xl shadow-lg">
            <div class="flex items-center gap-2 mb-4">
              <MonitorPlay class="w-5 h-5" />
              <h2 class="text-lg font-bold">Video Production Prompt</h2>
            </div>
            <p class="text-sm leading-relaxed mb-6 opacity-90">
              Copy this prompt to use with high-end AI video generators to create your perfect 15-second Facebook ad.
            </p>
            <div class="bg-[#1a6b1a] p-4 rounded-xl text-xs font-mono leading-relaxed mb-4">
              "A professional 4K video montage for a full-stack agency. Palette: solid forest green (#228B22) and cream (#F5F5DC). Smooth, flat-lay transitions over websites: Pathfinder AI, MC Beverly Hills, RHQ Services. No gradients, no shadows. Clean code snippets in PHP and JS appearing on solid cream panels. Minimalist, bright, and sharp aesthetic. Ends with LISH logo in bold green on cream."
            </div>
            <button 
              onclick={() => handleCopy("A professional 4K video montage for a full-stack agency. Palette: solid forest green (#228B22) and cream (#F5F5DC). Smooth, flat-lay transitions over websites: Pathfinder AI, MC Beverly Hills, RHQ Services. No gradients, no shadows. Clean code snippets in PHP and JS appearing on solid cream panels. Minimalist, bright, and sharp aesthetic. Ends with LISH logo in bold green on cream.")}
              class="w-full py-3 bg-white text-[#228B22] font-bold rounded-xl flex items-center justify-center gap-2 hover:bg-[#F5F5DC] transition-all"
            >
              <Copy size={16} /> Copy Video Prompt
            </button>
          </section>
        </div>
      </div>
    {:else if activeTab === 'creative'}
      <div class="flex flex-col md:flex-row gap-8">
        <div class="md:w-1/2 flex justify-center">
          <div class="w-full max-w-sm bg-white border border-gray-200 rounded-lg overflow-hidden shadow-xl self-start">
            <div class="p-3 flex items-center gap-2">
              <div class="w-8 h-8 bg-[#228B22] rounded-full flex items-center justify-center text-white text-xs font-bold">L</div>
              <div>
                <h4 class="text-sm font-bold">LISH Agency</h4>
                <p class="text-[10px] text-gray-500">Sponsored • Public</p>
              </div>
            </div>
            <div class="px-3 py-2">
              <p class="text-sm leading-snug line-clamp-3">{selectedAd.primaryText}</p>
            </div>
            <div class="bg-gray-100 aspect-[1.91/1] flex items-center justify-center relative">
              {#if generatedImage}
                <img src={generatedImage} alt="Ad Visual" class="w-full h-full object-cover" />
              {:else}
                <div class="text-center p-6">
                  <ImageIcon class="w-12 h-12 text-gray-300 mx-auto mb-2" />
                  <p class="text-xs text-gray-400">Static Carousel/Image Preview</p>
                </div>
              {/if}
              {#if generating}
                <div class="absolute inset-0 bg-white/80 flex flex-col items-center justify-center">
                  <RefreshCw class="w-8 h-8 text-[#228B22] animate-spin mb-2" />
                  <p class="text-xs font-bold text-[#228B22]">Generating Solid Asset...</p>
                </div>
              {/if}
            </div>
            <div class="p-3 bg-[#F5F5DC] flex justify-between items-center border-t border-gray-100">
              <div class="flex-1 pr-4">
                <p class="text-[10px] text-[#228B22] font-bold uppercase tracking-wider">LISH.COM.NG</p>
                <h4 class="text-sm font-bold text-gray-800 line-clamp-1">{selectedAd.headline}</h4>
              </div>
              <button class="bg-white border border-gray-300 px-4 py-1.5 rounded text-sm font-bold shadow-sm">
                {selectedAd.cta.split(' ')[0]}
              </button>
            </div>
          </div>
        </div>

        <div class="md:w-1/2 space-y-6">
          <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
            <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
              <Sparkles class="text-[#228B22]" size={20} />
              Static Creative Engine
            </h3>
            <p class="text-sm text-gray-500 mb-6 leading-relaxed">
              While the <strong>Video tab</strong> provides your motion strategy, use this tool to generate supporting static images for Facebook Carousel ads using your exact <strong>Green & Cream</strong> specifications.
            </p>

            <button 
              onclick={generateImage} 
              disabled={generating} 
              class="w-full py-4 rounded-xl font-bold flex items-center justify-center gap-2 transition-all {generating ? 'bg-gray-100 text-gray-400 cursor-not-allowed' : 'bg-[#228B22] text-white hover:bg-[#1a6b1a] shadow-lg hover:shadow-xl'}"
            >
              {generating ? 'Processing Branded Visual...' : 'Generate Branded Image'}
            </button>

            {#if error}
              <p class="mt-4 text-xs text-red-500 text-center">{error}</p>
            {/if}

            <div class="mt-8 grid grid-cols-3 gap-4 border-t border-gray-100 pt-8 text-center">
              <div>
                <div class="bg-gray-50 p-2 rounded-lg mb-2">
                  <Layout size={18} class="mx-auto text-gray-400" />
                </div>
                <p class="text-[10px] font-bold text-gray-400 uppercase">Single</p>
              </div>
              <div>
                <div class="bg-gray-50 p-2 rounded-lg mb-2">
                  <Code2 size={18} class="mx-auto text-gray-400" />
                </div>
                <p class="text-[10px] font-bold text-gray-400 uppercase">Carousel</p>
              </div>
              <div>
                <div class="bg-gray-50 p-2 rounded-lg mb-2">
                  <Eye size={18} class="mx-auto text-gray-400" />
                </div>
                <p class="text-[10px] font-bold text-gray-400 uppercase">Lead</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    {/if}
  </main>

  <footer class="max-w-6xl mx-auto mt-12 pb-8 text-center border-t border-gray-100 pt-8">
    <p class="text-xs text-gray-400 tracking-widest uppercase">LISH AGENCY • FULL-STACK SOLUTIONS</p>
  </footer>
</div>

<style>
  :global(body) {
    margin: 0;
  }
</style>
