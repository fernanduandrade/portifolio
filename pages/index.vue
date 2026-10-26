<template>
  <div>
    <div class="container">
      <!-- Hero -->
      <section class="hero animate-in">
        <div class="hero-badge">
          <span class="hero-badge-dot"></span>
          {{ heroRole }}
        </div>
        <h1 class="hero-name">Fernando Andrade</h1>
        <p class="hero-lead">{{ heroLead }}</p>
        <p v-if="heroBio" class="hero-bio">{{ heroBio }}</p>
        <div class="hero-links">
          <a
            :href="locale === 'pt' ? '/curriculum/CV_PT.pdf' : '/curriculum/CV_EN.pdf'"
            class="hero-link hero-link-primary"
            :download="locale === 'pt' ? 'CV-PT-BR.pdf' : 'CV-EN.pdf'"
          >
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="7 10 12 15 17 10" />
              <line x1="12" y1="15" x2="12" y2="3" />
            </svg>
            {{ $t('home.cvTitle') }}
          </a>
          <a href="https://github.com/fernanduandrade" target="_blank" rel="noopener" class="hero-link">
            <IconGithub /> GitHub
          </a>
          <a href="https://linkedin.com/in/fernanduandrade" target="_blank" rel="noopener" class="hero-link">
            <IconLinkedin /> LinkedIn
          </a>
          <a href="/feed.xml" class="hero-link">
            <IconRss /> RSS
          </a>
          <a href="mailto:fernandu.contact@gmail.com" class="hero-link">
            <IconEmail /> Email
          </a>
        </div>
      </section>

      <!-- Latest Posts -->
      <section class="section">
        <div class="section-header">
          <div class="section-heading">
            <span class="section-index">01</span>
            <h2 class="section-title">{{ $t('home.latestPosts') }}</h2>
          </div>
          <NuxtLinkLocale to="/blog" class="view-all">{{ $t('home.viewAllPosts') }}</NuxtLinkLocale>
        </div>
        <div class="post-list">
          <NuxtLinkLocale
            v-if="featuredPost"
            :to="postHref(featuredPost)"
            class="post-featured animate-in"
            :aria-label="`${t('home.featuredPostLabel')} — ${featuredPost.title}`"
          >
            <div class="post-featured-top">
              <span class="post-featured-label">{{ $t('home.featuredPostLabel') }}</span>
              <span class="post-featured-date">{{ formatDate(featuredPost.date) }}</span>
            </div>
            <h3 class="post-featured-title">{{ featuredPost.title }}</h3>
            <p class="post-featured-desc">{{ featuredPost.description }}</p>
            <div class="post-featured-foot">
              <span v-if="featuredPost.category" class="post-tag" :class="featuredPost.category.toLowerCase()">
                {{ categoryLabel(featuredPost.category) }}
              </span>
              <span v-for="tag in (featuredPost.tags || []).slice(0, 3)" :key="tag" class="post-hashtag">#{{ tag }}</span>
              <span class="post-featured-cta">{{ $t('home.readArticle') }}</span>
            </div>
          </NuxtLinkLocale>
          <NuxtLinkLocale
            v-for="post in otherPosts"
            :key="post._path"
            :to="postHref(post)"
            class="post-item animate-in"
            :aria-label="post.title"
          >
            <span class="post-date">{{ formatDate(post.date) }}</span>
            <div class="post-info">
              <div class="post-title">{{ post.title }}</div>
              <div class="post-excerpt">{{ post.description }}</div>
            </div>
            <span v-if="post.category" class="post-tag" :class="post.category.toLowerCase()">
              {{ categoryLabel(post.category) }}
            </span>
          </NuxtLinkLocale>
        </div>
      </section>

      <!-- Projects -->
      <section class="section">
        <div class="section-header">
          <div class="section-heading">
            <span class="section-index">02</span>
            <h2 class="section-title">{{ $t('home.projects') }}</h2>
          </div>
          <NuxtLinkLocale to="/projects" class="view-all">{{ $t('home.viewAllProjects') }}</NuxtLinkLocale>
        </div>
        <div class="projects-grid">
          <a
            v-for="project in featuredProjects"
            :key="project.repository"
            :href="projectHref(project)"
            class="project-card animate-in"
            target="_blank"
            rel="noopener"
          >
            <div class="project-card-head">
              <div class="project-icon">{{ project.icon }}</div>
              <svg class="project-arrow" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
                <line x1="7" y1="17" x2="17" y2="7" />
                <polyline points="7 7 17 7 17 17" />
              </svg>
            </div>
            <div class="project-name">{{ project.name }}</div>
            <div class="project-desc">{{ project.description }}</div>
            <div class="tech-chips">
              <span v-for="tech in project.tech" :key="tech" class="tech-chip">{{ tech }}</span>
            </div>
          </a>
        </div>
      </section>

      <!-- Now -->
      <section class="section">
        <div class="section-header">
          <div class="section-heading">
            <span class="section-index">03</span>
            <h2 class="section-title">{{ $t('home.now') }}</h2>
          </div>
          <NuxtLinkLocale to="/now" class="view-all">{{ $t('home.moreOnNow') }}</NuxtLinkLocale>
        </div>
        <div class="now-item">
          <span class="now-dot"></span>
          <div>
            <div class="now-label">{{ t('now.sections.focus.title') }}</div>
            <p class="now-text">{{ t('now.sections.focus.content') }}</p>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { useGithubProjects } from '~/composables/useGithubProjects'

const { locale, t } = useI18n()
const siteBaseUrl = 'https://fernanduandrade.com'
const canonicalUrl = computed(() => {
  if (locale.value === 'pt') return `${siteBaseUrl}/pt`
  if (locale.value === 'es') return `${siteBaseUrl}/es`
  return `${siteBaseUrl}`
})

// SEO
useSeoMeta({
  title: t('home.seo.title'),
  description: t('home.seo.description'),
  ogTitle: t('home.seo.ogTitle'),
  ogDescription: t('home.seo.ogDescription'),
  ogType: t('home.seo.ogType'),
  ogUrl: canonicalUrl.value,
  canonical: canonicalUrl.value,
  twitterCard: t('home.seo.twitterCard'),
  twitterTitle: t('home.seo.ogTitle'),
  twitterDescription: t('home.seo.ogDescription'),
  meta: [
    {
      name: 'keywords',
      content:
        'software engineer portfolio, architecture, distributed systems, cloud, GIS, technical articles, projects, software blogs',
    },
  ],
})

// Fetch posts
const { data: posts } = await useAsyncData(`home-posts-${locale.value}`, () =>
  queryContent(`/${locale.value}/blog`)
    .sort({ date: -1 })
    .limit(3)
    .find()
)

const latestPosts = computed(() => posts.value || [])
const featuredPost = computed(() => latestPosts.value[0])
const otherPosts = computed(() => latestPosts.value.slice(1))

// Primeira frase da bio vira o destaque do hero; o restante fica como texto de apoio.
const heroRole = computed(() => t('hero.role').replace(/\.$/, ''))
const heroBioParts = computed(() => {
  const [lead, ...rest] = t('hero.bio').split(/(?<=\.)\s+/)
  return { lead, rest: rest.join(' ') }
})
const heroLead = computed(() => heroBioParts.value.lead)
const heroBio = computed(() => heroBioParts.value.rest)

const { data: githubProjects } = useGithubProjects()

const featuredProjects = computed(() => {
  return githubProjects.value ? githubProjects.value.slice(0, 3) : []
})

function projectHref(project) {
  return project.pageUrl || project.repository
}

function projectTech(project) {
  return project.tech.join(' · ')
}

function postHref(post) {
  if (!post?._path) return '/blog'
  return post._path
}

function formatDate(dateStr) {
  if (!dateStr) return ''
  const date = new Date(dateStr)
  const localeMap = { pt: 'pt-BR', en: 'en-US', es: 'es-ES' }
  return date.toLocaleDateString(localeMap[locale.value] || 'pt-BR', {
    timeZone: 'UTC',
    year: 'numeric',
    month: 'short',
    day: 'numeric',
  })
}

function formatCategory(category) {
  return category
    .toString()
    .trim()
    .replace(/([a-z])([A-Z])/g, '$1 $2')
    .replace(/[-_]/g, ' ')
    .replace(/\b\w/g, (char) => char.toUpperCase())
}

function categoryLabel(category) {
  if (!category) return ''
  const key = category.toString().trim().toLowerCase()
  const translation = t(`blog.categories.${key}`)
  if (translation && translation !== `blog.categories.${key}`) return translation
  return formatCategory(category)
}
</script>
