<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import questionShowcase from './data/questionShowcase.json'

type NavItem = {
  label: string
  target: string
}

type Stat = {
  value: string
  label: string
}

type BenchmarkResult = {
  name: string
  group: string
  score: number
  note: string
  tone: 'human' | 'leader' | 'open' | 'vision' | 'audio' | 'text' | 'muted'
}

type Author = {
  name: string
  markers: string[]
}

type Affiliation = {
  id: string
  label: string
}

type ShowcaseOption = {
  letter: string
  text: string
}

type ShowcaseExample = {
  questionId: string
  videoId: string
  videoUrl: string
  startTime: string
  endTime: string
  domain: string
  subdomain: string
  questionType: string[]
  channelName: string | null
  question: string
  answer: string
  correctOptionLetter: string
  correctOptionText: string
  options: ShowcaseOption[]
}

const navigation: NavItem[] = [
  { label: 'Abstract', target: '#abstract' },
  { label: 'Figure', target: '#figure-1' },
  { label: 'Benchmark', target: '#benchmark' },
  { label: 'Test Mini', target: '#test-mini' },
  { label: 'Examples', target: '#examples' },
  { label: 'Leaderboard', target: '#leaderboard' },
]

const colorModeSelectUi = {
  base: 'mode-select-trigger',
  value: 'mode-select-value',
  placeholder: 'mode-select-placeholder',
  leadingIcon: 'mode-select-icon',
  trailingIcon: 'mode-select-icon',
  itemLeadingIcon: 'mode-select-icon',
  content: 'mode-select-content',
  item: 'mode-select-item',
} as const

let previousScrollRestoration: ScrollRestoration | null = null

const activeSection = ref(navigation[0]?.target ?? '#abstract')
const showcaseExamples = questionShowcase as ShowcaseExample[]
const activeShowcaseIndex = ref(0)

const benchmarkStats: Stat[] = [
  { value: '20,000', label: 'MCQ QA pairs' },
  { value: '11,877', label: 'web videos' },
  { value: '578.4s', label: 'avg. duration' },
  { value: '10', label: 'domains' },
  { value: '35', label: 'subcategories' },
  { value: '13', label: 'skills' },
  { value: '20+', label: 'models tested' },
]

const testMiniStats: Stat[] = [
  { value: '5,000', label: 'labeled QA pairs' },
  { value: '2,841', label: 'video IDs' },
  { value: '10', label: 'domains' },
  { value: '30', label: 'subcategories' },
  { value: '11', label: 'skill types' },
  { value: '754.8s', label: 'avg. duration' },
  { value: '3.75', label: 'avg. skills per QA' },
  { value: '357.4s', label: 'avg. answer position' },
]

const authors: Author[] = [
  { name: 'Arushi Goel', markers: ['1', '†', '*'] },
  { name: 'Sreyan Ghosh', markers: ['1', '2', '†', '*'] },
  { name: 'Vatsal Agarwal', markers: ['2', '*'] },
  { name: 'Nishit Anand', markers: ['2', '*'] },
  { name: 'Kaousheik Jayakumar', markers: ['2', '‡'] },
  { name: 'Lasha Koroshinadze', markers: ['2', '‡'] },
  { name: 'Yao Xu', markers: ['1'] },
  { name: 'Katie Lyons', markers: ['1'] },
  { name: 'James Case', markers: ['1'] },
  { name: 'Karan Sapra', markers: ['1'] },
  { name: 'Kevin J. Shih', markers: ['1'] },
  { name: 'Siddharth Gururani', markers: ['1'] },
  { name: 'Abhinav Shrivastava', markers: ['2'] },
  { name: 'Ramani Duraiswami', markers: ['2'] },
  { name: 'Dinesh Manocha', markers: ['2'] },
  { name: 'Andrew Tao', markers: ['1'] },
  { name: 'Bryan Catanzaro', markers: ['1'] },
  { name: 'Mohammad Shoeybi', markers: ['1'] },
  { name: 'Wei Ping', markers: ['1'] },
]

const affiliations: Affiliation[] = [
  { id: '1', label: 'NVIDIA, USA' },
  { id: '2', label: 'University of Maryland, College Park, USA' },
]

const results: BenchmarkResult[] = [
  {
    name: 'Human',
    group: 'Reference upper bound',
    score: 84.3,
    note: 'Human evaluators remain well ahead of every evaluated model.',
    tone: 'human',
  },
  {
    name: 'Gemini Pro 3.1',
    group: 'Closed-source omni model',
    score: 82.67,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.7',
    group: 'Closed-source omni model',
    score: 82.5,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.6',
    group: 'Closed-source omni model',
    score: 82.5,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.5',
    group: 'Closed-source omni model',
    score: 81.9,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini 2.5 Pro',
    group: 'Closed-source omni model',
    score: 64.2,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash Lite',
    group: 'Closed-source omni model',
    score: 60.37,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'Gemini 2.5 Flash',
    group: 'Closed-source omni model',
    score: 55.8,
    note: 'Closed-source omni baseline on MMOU.',
    tone: 'leader',
  },
  {
    name: 'MiniCPM-o 4.5',
    group: 'Best open-source omni model',
    score: 46.8,
    note: 'The strongest open-source system still trails Gemini 2.5 Pro by 17.4 points.',
    tone: 'open',
  },
  {
    name: 'Qwen3-Omni-30B-A3B-Instruct',
    group: 'Open-source omni model',
    score: 46.0,
    note: 'The best Qwen end-to-end omni baseline lands just below MiniCPM-o 4.5.',
    tone: 'open',
  },
  {
    name: 'Qwen3-VL-32B-Instruct',
    group: 'Vision-only baseline',
    score: 41.21,
    note: 'Strong visual reasoning still falls short when audio carries essential evidence.',
    tone: 'vision',
  },
  {
    name: 'Qwen3-235B',
    group: 'Text-only baseline',
    score: 32.73,
    note: 'Language priors help, but they do not replace temporally grounded perception.',
    tone: 'text',
  },
  {
    name: 'Qwen3-VL-8B-Instruct',
    group: 'Vision-only baseline',
    score: 35.63,
    note: 'The smaller Qwen3-VL variant remains competitive but still trails end-to-end omni models.',
    tone: 'vision',
  },
  {
    name: 'Qwen3-Omni-30B-A3B',
    group: 'Audio-only baseline',
    score: 43.04,
    note: 'Audio alone also collapses on questions that need grounded visual context.',
    tone: 'audio',
  },
  {
    name: 'GPT-4.1',
    group: 'Text-only baseline',
    score: 33.9,
    note: 'A proprietary text-only model confirms the benchmark is not solvable from language priors alone.',
    tone: 'text',
  },
  {
    name: 'Qwen3-(VL+O-A) + Qwen3-235B',
    group: 'Best cascaded baseline',
    score: 33.1,
    note: 'Caption fusion underperforms end-to-end omni reasoning despite access to generated summaries.',
    tone: 'muted',
  },
  {
    name: 'Ming-Lite-Omni-1.5',
    group: 'Open-source omni model',
    score: 30.33,
    note: 'Open-source omni baseline on long-form audio-visual reasoning.',
    tone: 'open',
  },
  {
    name: 'Phi-4 Multimodal',
    group: 'Open-source omni model',
    score: 26.0,
    note: 'A mid-pack open-source omni baseline on long-form audio-visual reasoning.',
    tone: 'open',
  },
  {
    name: 'Qwen2.5-Omni-7B',
    group: 'Open-source omni model',
    score: 29.81,
    note: 'The compact Qwen omni baseline falls well behind larger end-to-end systems.',
    tone: 'open',
  },
  {
    name: 'Gemma 3n',
    group: 'Open-source omni model',
    score: 21.49,
    note: 'Another open-source omni baseline that remains far below the leading models.',
    tone: 'open',
  },
  {
    name: 'Qwen2.5-VL-7B-Instruct',
    group: 'Vision-only baseline',
    score: 28.36,
    note: 'An earlier vision-only Qwen baseline with a large gap to audio-visual systems.',
    tone: 'vision',
  },
  {
    name: 'Qwen3-(VL+O-A) + GPT-5.2',
    group: 'Cascaded baseline',
    score: 28.1,
    note: 'Even GPT-5.2 on fused captions does not match direct audio-visual models.',
    tone: 'muted',
  },
  {
    name: 'Video-LLaMA 2',
    group: 'Open-source omni model',
    score: 24.56,
    note: 'An earlier open-source omni baseline with limited long-video reasoning performance.',
    tone: 'open',
  },
  {
    name: 'OmniVinci',
    group: 'Open-source omni model',
    score: 24.15,
    note: 'A unified omni model that still struggles on MMOU overall.',
    tone: 'open',
  },
  {
    name: 'Baichuan-Omni-1.5',
    group: 'Open-source omni model',
    score: 27.2,
    note: 'The benchmark remains difficult for current open-source omni architectures.',
    tone: 'open',
  },
  {
    name: 'Qwen3-Omni-30B-A3B-Thinking',
    group: 'Open-source omni model',
    score: 19.4,
    note: 'The thinking variant underperforms the instruct model substantially on MMOU.',
    tone: 'open',
  },
  {
    name: 'Audio Flamingo 3',
    group: 'Audio-only reference',
    score: 15.4,
    note: 'Pure audio reasoning fails on most of MMOU.',
    tone: 'muted',
  },
]

const leaderboardResults = [...results].sort((left, right) => right.score - left.score)

const testMiniResults: BenchmarkResult[] = [
  {
    name: 'Gemini Pro 3.1',
    group: 'Closed-source omni model',
    score: 89.68,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.7',
    group: 'Closed-source omni model',
    score: 87.0,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.6',
    group: 'Closed-source omni model',
    score: 86.96,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash 3.5',
    group: 'Closed-source omni model',
    score: 86.68,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Human',
    group: 'Reference upper bound',
    score: 82.6,
    note: 'Human accuracy on the labeled 5K Test Mini split.',
    tone: 'human',
  },
  {
    name: 'Gemini 2.5 Pro',
    group: 'Closed-source omni model',
    score: 78.58,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Gemini Flash Lite',
    group: 'Closed-source omni model',
    score: 69.49,
    note: 'Closed-source omni baseline on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Gemini 2.5 Flash',
    group: 'Closed-source omni model',
    score: 66.21,
    note: 'Second-best closed-source result on Test Mini.',
    tone: 'leader',
  },
  {
    name: 'Qwen3-Omni-30B-Inst.',
    group: 'Open-source omni model',
    score: 54.1,
    note: 'Open-source omni baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'Qwen3-VL-32B-Inst.',
    group: 'Vision-only baseline',
    score: 52.19,
    note: 'Strong vision-only baseline on the labeled split.',
    tone: 'vision',
  },
  {
    name: 'Qwen3-235B (Cascaded)',
    group: 'Caption-to-text baseline',
    score: 47.91,
    note: 'Cascaded text-model baseline on Test Mini.',
    tone: 'text',
  },
  {
    name: 'Qwen3-VL-8B-Inst.',
    group: 'Vision-only baseline',
    score: 41.9,
    note: 'Smaller Qwen3-VL baseline on Test Mini.',
    tone: 'vision',
  },
  {
    name: 'GPT-4.1 mini',
    group: 'Caption-to-text baseline',
    score: 41.64,
    note: 'Closed-source text model evaluated through cascaded captions.',
    tone: 'text',
  },
  {
    name: 'Phi-4 Multimodal',
    group: 'Open-source omni model',
    score: 36.4,
    note: 'Open-source audio-visual baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'Qwen3-Omni-30B-Audio',
    group: 'Audio-only baseline',
    score: 36.06,
    note: 'Audio-only Qwen3-Omni setting on Test Mini.',
    tone: 'audio',
  },
  {
    name: 'Qwen3-Omni-30B-Think',
    group: 'Open-source omni model',
    score: 35.76,
    note: 'Thinking variant evaluated on Test Mini.',
    tone: 'open',
  },
  {
    name: 'Qwen2.5-VL-7B-Inst.',
    group: 'Vision-only baseline',
    score: 34.58,
    note: 'Earlier Qwen vision-language baseline on Test Mini.',
    tone: 'vision',
  },
  {
    name: 'MiniCPM-V-4.6',
    group: 'Open-source vision model',
    score: 32.9,
    note: 'Vision-only open-source baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'Gemma-3n-E4B-it',
    group: 'Open-source omni model',
    score: 32.06,
    note: 'Gemma audio-visual baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'VideoLLaMA2',
    group: 'Open-source video model',
    score: 28.4,
    note: 'Video baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'OmniVinci',
    group: 'Open-source omni model',
    score: 27.75,
    note: 'Unified omni baseline on Test Mini.',
    tone: 'open',
  },
  {
    name: 'Audio Flamingo 3',
    group: 'Audio-only reference',
    score: 16.81,
    note: 'Pure audio baseline on Test Mini.',
    tone: 'muted',
  },
]

const heroResults = results.filter((item) =>
  ['Human', 'Gemini 2.5 Pro', 'MiniCPM-o 4.5'].includes(item.name),
)

const activeShowcase = computed(
  () => showcaseExamples[activeShowcaseIndex.value] ?? showcaseExamples[0],
)


const openDataset = () => {
  if (typeof window !== 'undefined') {
    window.open('https://huggingface.co/datasets/nvidia/MMOU', '_blank', 'noopener,noreferrer')
  }
}

const openVideoDataset = () => {
  if (typeof window !== 'undefined') {
    window.open('https://huggingface.co/datasets/sonalkum/MMOU-Videos', '_blank', 'noopener,noreferrer')
  }
}

const updateActiveSection = () => {
  if (typeof window === 'undefined' || typeof document === 'undefined') {
    return
  }

  const probe = window.innerHeight * 0.32
  let currentTarget = navigation[0]?.target ?? '#abstract'

  for (const item of navigation) {
    const section = document.querySelector<HTMLElement>(item.target)

    if (!section) {
      continue
    }

    if (section.getBoundingClientRect().top <= probe) {
      currentTarget = item.target
    }
  }

  activeSection.value = currentTarget
}

const jumpTo = (target: string) => {
  if (typeof document === 'undefined') {
    return
  }

  activeSection.value = target

  document.querySelector(target)?.scrollIntoView({
    behavior: 'smooth',
    block: 'start',
  })
}

const scoreStyle = (score: number) => ({ width: `${score}%` })

const timestampToSeconds = (value: string) =>
  value
    .split(':')
    .map((part) => Number(part))
    .reduce((total, part) => total * 60 + (Number.isFinite(part) ? part : 0), 0)

const embedUrl = (item: ShowcaseExample) => {
  const start = timestampToSeconds(item.startTime)
  const params = [`start=${start}`, 'rel=0', 'modestbranding=1']

  return `https://www.youtube.com/embed/${item.videoId}?${params.join('&')}`
}

const resetInitialScroll = () => {
  if (typeof window === 'undefined') {
    return
  }

  if ('scrollRestoration' in window.history) {
    previousScrollRestoration = window.history.scrollRestoration
    window.history.scrollRestoration = 'manual'
  }

  if (window.location.hash) {
    return
  }

  requestAnimationFrame(() => {
    window.scrollTo({ top: 0, left: 0 })
    updateActiveSection()
  })
}

onMounted(() => {
  resetInitialScroll()
  updateActiveSection()
  window.addEventListener('scroll', updateActiveSection, { passive: true })
  window.addEventListener('resize', updateActiveSection)
})

onBeforeUnmount(() => {
  if (typeof window !== 'undefined' && previousScrollRestoration) {
    window.history.scrollRestoration = previousScrollRestoration
  }

  window.removeEventListener('scroll', updateActiveSection)
  window.removeEventListener('resize', updateActiveSection)
})
</script>

<template>
  <UApp>
    <div class="site-shell">
      <div class="ambient-backdrop" aria-hidden="true">
        <div class="ambient-backdrop__layer ambient-backdrop__layer--orbits"></div>
        <div class="ambient-backdrop__layer ambient-backdrop__layer--constellation"></div>
        <div class="ambient-backdrop__layer ambient-backdrop__layer--waveform"></div>
        <div class="ambient-backdrop__layer ambient-backdrop__layer--timeline"></div>
        <div class="ambient-backdrop__layer ambient-backdrop__layer--signals"></div>
      </div>

      <header class="fixed inset-x-0 top-3 z-[60] px-4 sm:px-6 lg:px-8">
        <div
          class="chrome-panel mx-auto flex max-w-7xl items-center justify-between gap-3 rounded-full px-4 py-2 sm:px-6">
          <button type="button" class="brand-button flex items-center gap-3 text-left" @click="jumpTo('#abstract')">
            <img src="/images/logo.webp" alt="MMOU logo" class="h-11 w-auto object-contain sm:h-12">
            <div class="hidden sm:block">
              <p class="text-xs uppercase tracking-[0.32em] text-[var(--muted)]">MMOU</p>
              <p class="text-sm font-semibold text-[var(--ink)]">Massive Multi-Task Omni Understanding and Reasoning</p>
            </div>
          </button>

          <nav class="hidden items-center gap-2 lg:flex">
            <button v-for="item in navigation" :key="item.target" type="button"
              :class="['nav-link', { 'nav-link-active': activeSection === item.target }]"
              :aria-current="activeSection === item.target ? 'page' : undefined" @click="jumpTo(item.target)">
              {{ item.label }}
            </button>
          </nav>

          <div class="flex items-center gap-2">
            <UColorModeSelect color="neutral" variant="outline" size="xs" class="mode-select w-[6.75rem] sm:w-[7.25rem]"
              :content="{ side: 'bottom', align: 'end', sideOffset: 10 }" :ui="colorModeSelectUi" />
          </div>
        </div>
      </header>

      <main class="pt-[5.75rem] sm:pt-24 lg:pt-28">
        <section id="abstract"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 pb-8 pt-4 sm:px-8 sm:pt-5 lg:scroll-mt-32 lg:px-12 lg:pb-10 lg:pt-6">
          <div class="mx-auto max-w-5xl">
            <UCard class="surface-card rounded-[34px] p-2">
              <div class="space-y-5 px-4 py-5 sm:px-6 sm:py-6">
                <div class="flex items-start gap-4">
                  <img src="/images/logo.webp" alt="MMOU logo" class="h-14 w-auto object-contain sm:h-16">
                  <div class="min-w-0">
                    <p class="text-[0.72rem] font-bold uppercase tracking-[0.3em] text-[var(--accent-strong)]">
                      MMOU
                    </p>
                    <h1 class="display-font mt-2 text-4xl leading-[0.96] tracking-[-0.04em] text-[var(--ink)] [overflow-wrap:anywhere] sm:text-5xl">
                      Massive Multi-Task Omni Understanding and Reasoning
                    </h1>
                  </div>
                </div>
                <div
                  class="rounded-[26px] border border-[var(--border)] bg-white/48 px-4 py-4 text-center shadow-[0_12px_36px_rgb(16_23_28_/_0.05)] dark:bg-[rgb(22_29_35_/_0.7)]">
                  <div class="flex flex-wrap items-center justify-center gap-x-3 gap-y-2 text-sm font-semibold text-[var(--ink)] sm:text-[1.02rem]">
                    <span v-for="author in authors" :key="author.name" class="whitespace-nowrap">
                      {{ author.name }}<sup class="ml-1 text-[0.72em] font-bold text-[var(--accent-strong)]">{{
                        author.markers.join(' ')
                      }}</sup>
                    </span>
                  </div>
                  <div class="mt-3 flex flex-wrap items-center justify-center gap-x-4 gap-y-1 text-xs font-medium text-[var(--muted)]">
                    <span v-for="affiliation in affiliations" :key="affiliation.id">
                      <span class="font-bold text-[var(--ink)]">{{ affiliation.id }}</span> {{ affiliation.label }}
                    </span>
                  </div>
                </div>
                <p class="text-[0.72rem] font-bold uppercase tracking-[0.3em] text-[var(--accent-strong)]">
                  Abstract
                </p>
                <p class="text-base/8 text-[var(--muted)] sm:text-lg/8">
                  MMOU evaluates multimodal large language models on long, complex real-world videos that require joint
                  reasoning over visual, audio, and textual signals. The benchmark contains 20,000 carefully curated
                  multiple-choice questions paired with 11,877 web-collected videos spanning 10 domains, 35 fine-grained
                  subcategories, and 13 fundamental skill categories across the 15K test and 5K Test Mini splits.
                  Across 20+ evaluated models, the best closed-source system
                  reaches 82.67% accuracy, the strongest open-source model reaches 46.8%, and humans reach 84.3%,
                  exposing persistent gaps in long-form omni-modal understanding.
                </p>
                <div class="flex flex-wrap gap-3">
                  <UButton color="neutral" variant="outline"
                    class="button-fx rounded-full border-[var(--border-strong)] bg-white/65 px-5 text-[var(--ink)]"
                    @click="openDataset">
                    Hugging Face Dataset
                  </UButton>
                  <UButton color="neutral" variant="outline"
                    class="button-fx rounded-full border-[var(--border-strong)] bg-white/65 px-5 text-[var(--ink)]"
                    @click="jumpTo('#figure-1')">
                    View Figure
                  </UButton>
                </div>
                <p class="text-sm/7 text-[var(--muted)]">
                  Dataset release on
                  <a href="https://huggingface.co/datasets/nvidia/MMOU" target="_blank" rel="noreferrer"
                    class="font-semibold text-[var(--accent-strong)] underline decoration-[0.08em] underline-offset-4">
                    Hugging Face
                  </a>
                  with 20,000 expert-authored QA pairs across 11,877 long-form web videos, including the 5K
                  labeled Test Mini split.
                </p>
              </div>
            </UCard>
          </div>
        </section>

        <section id="figure-1"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 py-8 sm:px-8 lg:scroll-mt-32 lg:px-12 lg:py-10">
          <div class="section-heading reveal">
            <UBadge color="neutral" variant="soft" class="section-badge">Figure</UBadge>
            <h2 class="display-font section-title">A single MMOU question can require audio, vision, and temporal reasoning together.</h2>
            <p class="section-copy">
              The main figure illustrates the benchmark format: long input video, temporally localized evidence,
              cross-modal reasoning requirements, and the performance gap between humans and current models.
            </p>
          </div>

          <UCard class="surface-card media-card mt-8 rounded-[34px] p-2">
            <figure class="space-y-4 px-2 py-2">
              <img src="/images/hero.webp"
                alt="Main MMOU figure showing a long video example, multi-skill reasoning requirements, and the performance gap between three representative models."
                class="w-full rounded-[26px] bg-white" loading="eager">
              <figcaption class="grid gap-4 px-2 pb-2 lg:grid-cols-[1.05fr_0.95fr] lg:items-start">
                <div>
                  <p class="text-2xl font-semibold tracking-[-0.03em] text-[var(--ink)]">Example task and top-line gap</p>
                  <p class="mt-2 text-sm/7 text-[var(--muted)]">
                    This example highlights why MMOU is difficult: answering one question can require tracking events
                    over time, grounding referenced objects and people, and using audio cues that are inseparable from
                    the visual context. Gemini 2.5 Pro still trails human performance by 20.1 points.
                  </p>
                </div>
                <div class="grid gap-2.5 sm:grid-cols-3">
                  <div v-for="item in heroResults" :key="item.name"
                    class="rounded-[22px] border border-[var(--border)] bg-white/72 px-4 py-4">
                    <div class="mb-2 flex items-center justify-between gap-3">
                      <p class="text-sm font-semibold text-[var(--ink)]">{{ item.name }}</p>
                      <p class="text-sm font-bold text-[var(--ink)]">{{ item.score }}%</p>
                    </div>
                    <div class="score-track">
                      <div :class="['score-fill', `score-fill--${item.tone}`]" :style="scoreStyle(item.score)" />
                    </div>
                  </div>
                </div>
              </figcaption>
            </figure>
          </UCard>
        </section>

        <section id="benchmark"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 py-8 sm:px-8 lg:scroll-mt-32 lg:px-12 lg:py-10">
          <div class="section-heading reveal">
            <UBadge color="neutral" variant="soft" class="section-badge">Benchmark</UBadge>
            <h2 class="display-font section-title">Benchmark overview</h2>
            <p class="section-copy">
              MMOU combines long-form video duration, broad domain coverage, multi-skill question design, and a
              deliberately hard 10-option evaluation setup in one benchmark for real audio-visual understanding. The
              figure below consolidates the benchmark's category mix, reasoning structure, answer placement, and
              duration profile.
            </p>
          </div>

          <div class="mt-8 grid gap-3 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
            <div v-for="stat in benchmarkStats" :key="stat.label" class="metric-pill reveal rounded-[24px] px-4 py-4">
              <p class="text-2xl font-extrabold tracking-[-0.04em] text-[var(--ink)]">{{ stat.value }}</p>
              <p class="mt-1 text-sm font-medium text-[var(--muted)]">{{ stat.label }}</p>
            </div>
          </div>

          <div class="mt-4">
            <UCard class="surface-card media-card rounded-[30px] p-2">
              <figure class="space-y-5 px-4 py-4">
                <img src="/images/domains_skills.png"
                  alt="Composite MMOU benchmark figure showing video category distribution, reasoning skill co-occurrence, relative answer position, question distribution across skills, and video duration distribution."
                  loading="lazy" class="w-full rounded-[24px] bg-white">
                <figcaption class="grid gap-4 lg:grid-cols-[1.05fr_0.95fr] lg:items-start">
                  <div>
                    <p class="text-xl font-semibold tracking-[-0.03em] text-[var(--ink)]">
                      Domains, skills, answer placement, and duration in one figure
                    </p>
                    <p class="mt-2 text-sm/7 text-[var(--muted)]">
                      The summary figure shows MMOU's 10-domain coverage, how reasoning skills overlap inside the same
                      question, where evidence tends to appear over a video's timeline, and how video lengths
                      distribute across the benchmark.
                    </p>
                  </div>
                  <div class="grid gap-3 sm:grid-cols-2">
                    <div class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                      <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Domains</p>
                      <p class="mt-2 text-sm/7 text-[var(--ink)]">
                        Coverage spans 10 major categories and 35 fine-grained subcategories, from academic lectures
                        and sports to music, news, pranks, and everyday footage.
                      </p>
                    </div>
                    <div class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                      <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Skill mix</p>
                      <p class="mt-2 text-sm/7 text-[var(--ink)]">
                        Each question is tagged with one or more of 13 skill types, so
                        compound reasoning is the norm rather than the exception.
                      </p>
                    </div>
                    <div class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                      <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Construction</p>
                      <p class="mt-2 text-sm/7 text-[var(--ink)]">
                        Eleven expert annotators author the open-ended QA pairs, which are then converted into
                        10-option multiple-choice items with nine hard distractors per question.
                      </p>
                    </div>
                    <div class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                      <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Timeline</p>
                      <p class="mt-2 text-sm/7 text-[var(--ink)]">
                        Answer evidence appears about 302 seconds into the video on average, while source clips range from 7 seconds
                        to 121 minutes and demand long-horizon context retention.
                      </p>
                    </div>
                  </div>
                </figcaption>
              </figure>
            </UCard>
          </div>
        </section>

        <section id="test-mini"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 py-8 sm:px-8 lg:scroll-mt-32 lg:px-12 lg:py-10">
          <div class="section-heading reveal">
            <UBadge color="neutral" variant="soft" class="section-badge">Test Mini</UBadge>
            <h2 class="display-font section-title">A public labeled 5K evaluation split</h2>
            <p class="section-copy">
              MMOU Test Mini is released for reproducible public evaluation. It follows the same 10-option
              multiple-choice format as MMOU and includes the ground-truth
              <code class="rounded-md border border-[var(--border)] bg-[var(--chip-bg)] px-1.5 py-0.5 text-[var(--ink)]">correct_option_letter</code>
              field for direct scoring.
            </p>
          </div>

          <div class="mt-8 grid gap-3 sm:grid-cols-2 lg:grid-cols-4">
            <div v-for="stat in testMiniStats" :key="stat.label" class="metric-pill reveal rounded-[24px] px-4 py-4">
              <p class="text-2xl font-extrabold tracking-[-0.04em] text-[var(--ink)]">{{ stat.value }}</p>
              <p class="mt-1 text-sm font-medium text-[var(--muted)]">{{ stat.label }}</p>
            </div>
          </div>

          <div class="mt-4 grid gap-4 lg:grid-cols-[0.86fr_1.14fr]">
            <UCard class="surface-card rounded-[30px] p-2">
              <div class="space-y-5 px-4 py-4">
                <div>
                  <p class="text-xl font-semibold tracking-[-0.03em] text-[var(--ink)]">Release format</p>
                  <p class="mt-2 text-sm/7 text-[var(--muted)]">
                    Test Mini covers the same 10 major domains as the full benchmark, with 2,841 video IDs, 30
                    subcategories, and 11 skill types. The questions are available from the MMOU dataset, and
                    corresponding available videos are hosted in the <span class="font-semibold text-[var(--ink)]">test</span>
                    split of the video dataset.
                  </p>
                </div>

                <div class="flex flex-wrap gap-3">
                  <UButton color="neutral" variant="outline"
                    class="button-fx rounded-full border-[var(--border-strong)] bg-white/65 px-5 text-[var(--ink)]"
                    @click="openDataset">
                    Test Mini Questions
                  </UButton>
                  <UButton color="neutral" variant="outline"
                    class="button-fx rounded-full border-[var(--border-strong)] bg-white/65 px-5 text-[var(--ink)]"
                    @click="openVideoDataset">
                    Test Videos
                  </UButton>
                </div>

                <div class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                  <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Load the split</p>
                  <pre class="mt-3 overflow-x-auto rounded-[18px] border border-[var(--border)] bg-black/70 px-4 py-4 text-xs leading-6 text-white"><code>from datasets import load_dataset

questions = load_dataset("nvidia/MMOU", "test", split="test")
videos = load_dataset("sonalkum/MMOU-Videos", split="test", streaming=True)</code></pre>
                </div>
              </div>
            </UCard>

            <UCard class="leaderboard-card rounded-[30px] p-2">
              <div class="space-y-4 px-4 py-4">
                <div>
                  <p class="text-xl font-semibold tracking-[-0.03em] text-[var(--ink)]">Test Mini overall accuracy</p>
                  <p class="mt-2 text-sm/7 text-[var(--muted)]">
                    Multiple-choice accuracy on the labeled 5K Test Mini split.
                  </p>
                </div>

                <div class="grid gap-3 md:grid-cols-2">
                  <div v-for="item in testMiniResults" :key="item.name"
                    class="rounded-[22px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-3">
                    <div class="flex items-center justify-between gap-3">
                      <div class="min-w-0">
                        <p class="truncate text-sm font-semibold text-[var(--ink)]">{{ item.name }}</p>
                        <p class="text-xs text-[var(--muted)]">{{ item.group }}</p>
                      </div>
                      <p class="text-base font-bold tracking-[-0.03em] text-[var(--ink)]">{{ item.score.toFixed(2) }}%</p>
                    </div>
                    <div class="mt-3 score-track">
                      <div :class="['score-fill', `score-fill--${item.tone}`]" :style="scoreStyle(item.score)" />
                    </div>
                  </div>
                </div>
              </div>
            </UCard>
          </div>
        </section>

        <section id="examples"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 py-8 sm:px-8 lg:scroll-mt-32 lg:px-12 lg:py-10">
          <div class="section-heading reveal">
            <UBadge color="neutral" variant="soft" class="section-badge">Examples</UBadge>
            <h2 class="display-font section-title">See what MMOU questions actually look like</h2>
            <p class="section-copy">
              These twelve examples show how MMOU pairs long-form video clips with 10-option multiple-choice
              questions, grounded answers, and evidence that can depend on audio, vision, and timing together.
            </p>
          </div>

          <div class="mt-6 grid gap-2 sm:grid-cols-2 lg:grid-cols-4 xl:grid-cols-6">
            <button
              v-for="(item, index) in showcaseExamples"
              :key="item.questionId"
              type="button"
              :class="['showcase-button', 'button-fx', 'h-full', 'text-left', { 'showcase-button-active': activeShowcaseIndex === index }]"
              @click="activeShowcaseIndex = index">
              <p class="text-[0.68rem] font-bold uppercase tracking-[0.24em] text-[var(--accent-strong)]">
                Example {{ String(index + 1).padStart(2, '0') }}
              </p>
              <p class="mt-1.5 text-sm font-semibold leading-5 text-[var(--ink)]">{{ item.domain }}</p>
              <p class="mt-0.5 text-xs leading-5 text-[var(--muted)]">{{ item.subdomain }}</p>
            </button>
          </div>

          <UCard v-if="activeShowcase" class="surface-card mt-5 rounded-[28px] p-2">
            <div class="grid gap-5 px-4 py-4 sm:px-5 sm:py-5 xl:grid-cols-[minmax(0,1.08fr)_minmax(20rem,0.92fr)] xl:items-start">
              <div class="space-y-4">
                <div>
                  <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Question</p>
                  <h3 class="mt-2 text-xl font-semibold tracking-[-0.03em] text-[var(--ink)] sm:text-2xl">
                    {{ activeShowcase.question }}
                  </h3>
                </div>

                <div>
                  <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Options</p>
                  <div class="mt-3 grid gap-2.5 md:grid-cols-2">
                    <div
                      v-for="option in activeShowcase.options"
                      :key="option.letter"
                      :class="['showcase-option', { 'showcase-option--correct': option.letter === activeShowcase.correctOptionLetter }]">
                      <div class="flex items-start gap-2.5">
                        <span class="showcase-option__letter">{{ option.letter }}</span>
                        <div>
                          <p class="text-sm/6 font-medium text-[var(--ink)]">{{ option.text }}</p>
                          <p v-if="option.letter === activeShowcase.correctOptionLetter"
                            class="mt-1.5 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--accent-strong)]">
                            Correct option
                          </p>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <div class="space-y-4">
                <div class="space-y-2">
                  <div class="flex flex-wrap gap-2">
                    <span class="rounded-full border border-[var(--border)] bg-[var(--chip-bg)] px-3 py-1 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--muted)]">
                      {{ activeShowcase.domain }}
                    </span>
                    <span class="rounded-full border border-[var(--border)] bg-[var(--chip-bg)] px-3 py-1 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--muted)]">
                      {{ activeShowcase.subdomain }}
                    </span>
                    <span v-if="activeShowcase.channelName"
                      class="rounded-full border border-[var(--border)] bg-[var(--chip-bg)] px-3 py-1 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--muted)]">
                      {{ activeShowcase.channelName }}
                    </span>
                    <span class="rounded-full border border-[var(--border)] bg-[var(--chip-bg)] px-3 py-1 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--muted)]">
                      {{ activeShowcase.startTime }}-{{ activeShowcase.endTime }}
                    </span>
                  </div>

                  <div v-if="activeShowcase.questionType.length" class="flex flex-wrap gap-2">
                    <span
                      v-for="type in activeShowcase.questionType"
                      :key="type"
                      class="rounded-full border border-[var(--border)] bg-[var(--chip-bg)] px-3 py-1 text-[0.68rem] font-semibold uppercase tracking-[0.16em] text-[var(--accent-strong)]">
                      {{ type }}
                    </span>
                  </div>
                </div>

                <div>
                  <div class="flex flex-wrap items-center justify-between gap-3">
                    <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Video clip</p>
                    <a :href="activeShowcase.videoUrl" target="_blank" rel="noreferrer"
                      class="text-sm font-semibold text-[var(--accent-strong)] hover:underline">
                      Open on YouTube
                    </a>
                  </div>
                  <div class="mt-3 aspect-video overflow-hidden rounded-[22px] border border-[var(--border)] bg-black">
                    <iframe
                      :src="embedUrl(activeShowcase)"
                      :title="`MMOU example ${activeShowcase.videoId}`"
                      class="h-full w-full"
                      loading="lazy"
                      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
                      allowfullscreen
                      referrerpolicy="strict-origin-when-cross-origin" />
                  </div>
                </div>

                <div class="rounded-[20px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                  <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[var(--muted)]">Ground-truth answer</p>
                  <p class="mt-2 text-sm/7 text-[var(--ink)]">{{ activeShowcase.answer }}</p>
                  <p class="mt-2 text-sm/6 text-[var(--muted)]">
                    Correct choice: {{ activeShowcase.correctOptionLetter }}. {{ activeShowcase.correctOptionText }}
                  </p>
                </div>
              </div>
            </div>
          </UCard>
        </section>

        <section id="leaderboard"
          class="mx-auto max-w-7xl scroll-mt-28 px-6 py-8 sm:px-8 lg:scroll-mt-32 lg:px-12 lg:py-10">
          <div>
            <div class="section-heading reveal">
              <UBadge color="neutral" variant="soft" class="section-badge">Leaderboard</UBadge>
              <h2 class="display-font section-title">
                Accuracy on MMOU
              </h2>
              <p class="section-copy">
                Human performance remains clearly ahead of the best models. The expanded leaderboard below
                covers closed-source, open-source, unimodal, cascaded, and text-only baselines evaluated on the 15K
                MMOU test split. Test Mini results are shown separately above.
              </p>
            </div>

            <UCard class="leaderboard-card mt-8 rounded-[32px] p-2">
              <div class="space-y-4 px-4 py-4 sm:px-5">
                <h3 class="text-3xl font-semibold tracking-[-0.04em] text-[var(--ink)]">
                  Best model accuracy remains below human performance.
                </h3>

                <div class="grid gap-3 xl:grid-cols-2">
                  <div v-for="item in leaderboardResults" :key="item.name"
                    class="rounded-[24px] border border-[var(--border)] bg-[var(--chip-bg)] px-4 py-4">
                    <div class="flex items-center justify-between gap-4">
                      <div>
                        <p class="text-base font-semibold text-[var(--ink)]">{{ item.name }}</p>
                        <p class="text-sm text-[var(--muted)]">{{ item.group }}</p>
                      </div>
                      <p class="text-xl font-bold tracking-[-0.03em] text-[var(--ink)]">{{ item.score }}%</p>
                    </div>
                    <div class="mt-3 score-track">
                      <div :class="['score-fill', `score-fill--${item.tone}`]" :style="scoreStyle(item.score)" />
                    </div>
                    <p class="mt-3 text-sm/7 text-[var(--muted)]">{{ item.note }}</p>
                  </div>
                </div>
              </div>
            </UCard>
          </div>
        </section>
      </main>

      <footer class="mx-auto max-w-7xl px-6 pb-8 sm:px-8 lg:px-12">
        <div
          class="footer-panel flex flex-col gap-4 rounded-[28px] px-5 py-5 sm:flex-row sm:items-center sm:justify-between">
          <div>
            <p class="text-xs uppercase tracking-[0.28em] text-[var(--muted)]">MMOU</p>
            <p class="mt-2 text-sm text-[var(--muted)]">
              Massive Multi-Task Omni Understanding and Reasoning for long and complex real-world videos.
            </p>
          </div>
          <div class="flex flex-wrap items-center gap-3">
            <UButton color="neutral" variant="outline"
              class="button-fx min-w-[8.75rem] justify-center whitespace-nowrap rounded-full border-[var(--border-strong)] bg-white/68 px-5 text-sm font-semibold text-[var(--ink)]"
              @click="jumpTo('#abstract')">
              Back to top
            </UButton>
          </div>
        </div>
      </footer>
    </div>
  </UApp>
</template>
