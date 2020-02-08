<script>
import groups from '../data/groups'
import instrumentsArray from '../data/instruments'
import { sites, tutorials, books } from '../data/resources'

import List from './components/List.svelte'
import Grid from './components/Grid.svelte'

import IconButton, { Icon } from '@smui/icon-button'
import Drawer, { AppContent, Content, Header, Title, Subtitle, Scrim } from '@smui/drawer'

import Logo from './components/Logo.svelte'
import IconSvg from 'svelte-icon'
import { metronome, play_arrow, stop } from 'material-icons-bundle'

import { onMount } from 'svelte'
import IntersectionObserver from './components/IntersectionObserver.svelte'

const instruments = instrumentsArray.reduce((acc, [key, name, color]) => (acc[key] = ({ name, color }), acc), {})

let width
let drawer
let drawerOpen = true
let playing = true
let active = {}

for (const group of groups) {
  group.id = groupId(slug(group.title))
  group.hash = hash(group.id)
  active[group.id] = false

  for (const pattern of group.patterns) {
    pattern.id = patternId(slug(pattern.title))
    pattern.hash = hash(pattern.id)
  }
}

const resources = [
  { title: 'Tutorials', items: tutorials },
  { title: 'Books', items: books },
  { title: 'Sites', items: sites },
  { title: 'Source Code', items: [{ title: 'GitHub', link: 'https://github.com/darosh/pocket-patterns' }] }
]

window.addEventListener('hashchange', onHash, false)

onMount(onHash)

function onHash () {
  if (location.hash) {
    try {
      const el = document.querySelector(decodeURIComponent(location.hash))

      if (el) {
        el.scrollIntoView()
      }
    } catch (e) {
    }
  }
}

function svg (path) {
  return path[0] === '<' ? path : `<path d="${path}" />`
}

function slug (text) {
  return text
    .replace(/[^\w\s$*_+~.()'"!\-:@]/g, '')
    .trim()
    .replace(/[-\s]+/g, '-')
    .toLowerCase()
}

function groupId (text) {
  return `group-${text}`
}

function patternId (text) {
  return `pattern-${text}`
}

function hash (text) {
  return `#${text}`
}
</script>

<svelte:window bind:innerWidth={width}/>

<div>
  <Drawer variant={width < 1024 ? 'modal' : 'permanent'} bind:this={drawer} bind:open={drawerOpen}>
    <Content>
      <List groups={groups} active={active}/>
    </Content>
  </Drawer>
  <Scrim/>
  <AppContent class="app-content">
    <main class="main-content">
      <IntersectionObserver on:intersecting={({detail}) => active['patterns'] = detail}>
        <h1 id="patterns">Pocket<span> <Logo size="0.92em"/> </span>Patterns</h1>
        <p class="mdc-typography--body1">Drum machine patterns based on
          <a href="https://shittyrecording.studio/" class="link" target="_blank">Shitty Recording Studio</a> collection.
        </p>
      </IntersectionObserver>

      <div style="margin-top: 48px;" class="mdc-list-divider margin-side"/>

        {#each groups as group, index (index)}
          <IntersectionObserver on:intersecting={({detail}) => active[group.id] = detail}>
            <h2
              id={group.id}>
              <a href={group.hash}>
                  {index + 1}. {group.title}
              </a>
            </h2>
              {#each group.patterns as pattern, index (index)}
                  {#if !pattern.part || (pattern.part === 'A')}
                    <div style="display: flex; height: 48px; margin-top: 48px;">
                      <h3
                        id={pattern.id}>
                        <a href={pattern.hash}>{pattern.title}</a></h3>
                      <div style="align-self: center; margin-right: 4px; opacity: 0.6">
                        120
                      </div>
                      <div style="align-self: center; margin-right: 4px;">
                        <IconButton on:click={() => playing = !playing} class="mini-btn">
                          <IconSvg data={svg(metronome)} viewBox="0 0 24 24" size="24px"></IconSvg>
                        </IconButton>
                      </div>
                      <div style="align-self: center;">
                        <IconButton on:click={() => playing = !playing} class="mini-btn">
                          <IconSvg data={svg(playing ? play_arrow : stop)} viewBox="0 0 24 24" size="24px"></IconSvg>
                        </IconButton>
                      </div>
                    </div>
                  {:else}
                    <div style="height: 24px;"/>
                  {/if}
                <div style="display: flex">
                  <div class="part" style="width: 24px; padding-left: 4px; font-weight: bold; box-sizing: border-box;">
                      {pattern.part || ''}
                  </div>
                  <div style="flex-grow: 1;">
                    <Grid {...{ pattern, instruments }}/>
                  </div>
                </div>
              {/each}
          </IntersectionObserver>
        {/each}

      <div style="margin-top: 48px;" class="mdc-list-divider margin-side"/>

      <IntersectionObserver on:intersecting={({detail}) => active['resources'] = detail}>
        <h2 id="resources"
            style="font-family: Lora, sans-serif !important; font-size: 48px;; letter-spacing: 6px;">
          <a href="#resources">
            Resources
          </a>
        </h2>

          {#each resources as {title, items}}
            <h3>{title}</h3>
            <ul style="margin-bottom: 48px;" class="mdc-typography--body1">
                {#each items.filter(({danger}) => !danger) as {link, title}}
                  <li><a class="link" href={link} target="_blank">{title}</a></li>
                {/each}
            </ul>
          {/each}
      </IntersectionObserver>

      <div style="margin-top: 48px;" class="mdc-list-divider margin-side"/>

      <p class="mdc-typography--body1" style="text-align: right;">
        <span style="opacity: 0.87;">Made in Brno</span><span
        style="line-height: 12px; vertical-align: text-bottom; padding: 0 4px 0 8px;"
        class="logo"> <Logo size="24px"/> </span><span style="opacity: 0.87;">MMXX</span>
      </p>
    </main>
  </AppContent>
</div>
<style>
* :global(.mdc-drawer) {
  width: 216px;
}

* :global(.mdc-drawer, .mdc-drawer--modal, .mdc-drawer-scrim) {
  position: fixed;
}

@media only screen and (min-width: 1024px) {
  * :global(.app-content) {
    margin-left: 216px;
  }
}

.main-content {
  overflow: auto;
  padding: 48px 64px 64px 48px;
  height: 100%;
  box-sizing: border-box;
}

h1, h2, h3, p, .margin-side {
  margin-left: 24px;
}

h1 {
  margin-top: 0;
  font-family: Lora, sans-serif !important;
  font-size: 72px;
  word-spacing: -0.11em;
  letter-spacing: -0.035em;
}

h1 span {
  line-height: 1.175em;
  vertical-align: text-bottom;
  padding: 0 0.054em 0 0.26em;
}

h2 {
  margin-top: 96px;
  margin-bottom: 64px;
  font-family: Lora, sans-serif !important;
  font-size: 48px;
  letter-spacing: 0.13em;
}

h3 {
  margin-top: 0;
  margin-bottom: 0;
  flex-grow: 1;
  align-self: center;
  font-size: 24px;
  font-weight: normal;
  letter-spacing: 0.03em;
}

@media only screen and (max-width: 960px) {
  .main-content {
    padding: 32px 40px 24px 16px;
  }

  h1 {
    font-size: 64px;
  }

  h2 {
    font-size: 32px;
  }

  h3 {
    font-size: 22px !important;
  }
}

@media only screen and (max-width: 640px) {
  .main-content {
    padding: 24px 24px 24px 0;
  }

  h1 {
    font-size: 48px;
  }

  h2 {
    font-size: 28px;
  }

  h3 {
    font-size: 20px !important;
  }
}

@media only screen and (max-width: 360px) {
  .main-content {
    padding: 24px 4px;
  }

  .part {
    display: none;
  }

  h1, h2, h3, p, .margin-side {
    margin-left: 0;
  }

  h2 {
    font-size: 26px !important;
  }

  h3 {
    font-size: 18px !important;
  }
}

h2 a, h3 a {
  color: inherit;
  text-decoration: none;
}

h2 a:hover, h3 a:hover {
  text-decoration: underline;
}

:global(svg) {
  stroke-width: 0;
}

:global(.metro-btn) {
  padding-left: 8px;
  padding-right: 12px;
}

:global(.mini-btn) {
  height: 40px !important;
  width: 40px !important;
  padding: 8px;
}
</style>
