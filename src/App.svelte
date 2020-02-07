<script>
import groups from '../data/groups'
import instrumentsArray from '../data/instruments'
import { sites, tutorials, books } from '../data/resources'

import Grid from './components/Grid.svelte'

import IconButton, { Icon } from '@smui/icon-button'
import Drawer, { AppContent, Content, Header, Title, Subtitle, Scrim } from '@smui/drawer'
import List, { Item, Text, Separator, Subheader, Meta } from '@smui/list'

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

onMount(() => {
  if (location.hash) {
    try {
      const el = document.querySelector(decodeURIComponent(location.hash))

      if (el) {
        el.scrollIntoView()
      }
    } catch (e) {
    }
  }
})

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

<div class="drawer-frame-root">
  <Drawer variant={width < 1024 ? 'modal' : 'permanent'} bind:this={drawer} bind:open={drawerOpen}>
    <Content>
      <List>
        <Item href="#patterns">
          <Text>Pocket Patterns</Text>
          <Meta>({groups.reduce((acc, {patterns}) => acc + patterns.length, 0)})</Meta>
        </Item>
        <Separator/>
          {#each groups as group, index (index)}
            <Item href={group.hash} selected={ active[group.id] }>
              <Text>{index + 1}. {group.title}</Text>
              <Meta>({group.patterns.length})</Meta>
            </Item>
          {/each}
        <Separator/>
        <Item href="#resources">
          <Text>Resources</Text>
        </Item>
      </List>
    </Content>
  </Drawer>
  <Scrim/>
  <AppContent class="app-content">
    <main class="main-content">
      <IntersectionObserver on:intersecting={({detail}) => active['patterns'] = detail}>
        <h1
          id="patterns"
          style="font-family: Lora, sans-serif !important; font-size: 72px; word-spacing: -8px; letter-spacing: -2.5px; margin-right: 24px;">
          Pocket<span
          style="line-height: 84px; vertical-align: text-bottom; padding: 0 4px 0 20px;"
          class="logo"> <Logo size="66px"/> </span>Patterns
        </h1>

        <p style="font-size: 20px; line-height: 28px; margin-right: 24px;">Drum machine patterns based on
          <a href="https://shittyrecording.studio/" class="link" target="_blank">Shitty Recording Studio</a> collection.
        </p>
      </IntersectionObserver>

      <div style="margin: 48px 24px 0 24px;">
        <List>
          <Separator/>
        </List>
      </div>

        {#each groups as group, index (index)}
          <IntersectionObserver on:intersecting={({detail}) => active[group.id] = detail}>
            <h2
              id={group.id}
              style="font-family: Lora, sans-serif !important; font-size: 48px;; letter-spacing: 6px;">
              <a href={group.hash}>
                  {index + 1}. {group.title}
              </a>
            </h2>
              {#each group.patterns as pattern, index (index)}
                  {#if !pattern.part || (pattern.part === 'A')}
                    <div style="display: flex; height: 48px; margin-top: 48px;">
                      <h3
                        id={pattern.id}
                        style="flex-grow: 1; align-self: center; font-size: 24px; font-weight: normal; letter-spacing: 1px;">
                        <a href={pattern.hash}>{pattern.title}</a></h3>
                      <div style="align-self: center; margin-right: 4px; opacity: 0.6">
                        120
                      </div>
                      <div style="align-self: center; margin-right: 4px;">
                        <IconButton on:click={() => playing = !playing} class="mini-btn">
                          <IconSvg data={svg(metronome)} viewBox="0 0 24 24" size="24px"></IconSvg>
                        </IconButton>
                      </div>
                      <div style="align-self: center; margin-right: 24px;">
                        <IconButton on:click={() => playing = !playing} class="mini-btn">
                          <IconSvg data={svg(playing ? play_arrow : stop)} viewBox="0 0 24 24" size="24px"></IconSvg>
                        </IconButton>
                      </div>
                    </div>
                  {:else}
                    <div style="height: 24px;"/>
                  {/if}
                <div style="display: flex">
                  <div style="width: 24px; font-weight: bold;">
                      {pattern.part || ''}
                  </div>
                  <div style="flex-grow: 1; margin-right: 24px;">
                    <Grid {...{ pattern, instruments }}/>
                  </div>
                </div>
              {/each}
          </IntersectionObserver>
        {/each}

      <div style="margin: 48px 24px 0 24px;">
        <List>
          <Separator/>
        </List>
      </div>

      <IntersectionObserver on:intersecting={({detail}) => active['resources'] = detail}>
        <h2 id="resources"
            style="font-family: Lora, sans-serif !important; font-size: 48px;; letter-spacing: 6px;">
          <a href="#resources">
            Resources
          </a>
        </h2>

          {#each resources as {title, items}}
            <h3>{title}</h3>
            <ul style="margin-bottom: 48px; line-height: 36px; font-size: 20px;">
                {#each items.filter(({danger}) => !danger) as {link, title}}
                  <li><a class="link" href={link} target="_blank">{title}</a></li>
                {/each}
            </ul>
          {/each}
      </IntersectionObserver>

      <div style="margin: 48px 24px 0 24px;">
        <List>
          <Separator/>
        </List>
      </div>

      <p style="text-align: right; margin-right: 24px;">
        <span style="opacity: 0.87;">Made in Brno</span><span
        style="line-height: 12px; vertical-align: text-bottom; padding: 0 4px 0 8px;"
        class="logo"> <Logo size="24px"/> </span><span style="opacity: 0.87;">MMXX</span>
      </p>
    </main>
  </AppContent>
</div>
<style>
.drawer-frame-root {
  display: flex;
  height: 100vh;
}

* :global(.mdc-drawer--modal, .mdc-drawer-scrim) {
  /* This is not needed for a page-wide modal. */
  position: absolute;
}

* :global(.app-content) {
  flex: auto;
  overflow: auto;
  position: relative;
  flex-grow: 1;
}

.main-content {
  overflow: auto;
  padding: 48px;
  padding-bottom: 64px;
  height: 100%;
  box-sizing: border-box;
}

@media only screen and (max-width: 960px) {
  .main-content {
    padding: 16px;
    padding-top: 32px;
    padding-bottom: 24px;
  }
}

@media only screen and (max-width: 640px) {
  .main-content {
    padding: 8px;
    padding-top: 24px;
    padding-bottom: 24px;
  }
}

:global(body) {
  background: #484848;
  font-family: Roboto, sans-serif;
  color: #fff;
  padding: 24px;
  margin: 0;
}

h2 {
  margin-top: 64px;
  margin-bottom: 64px;
}

h3 {
  margin-top: 56px;
  margin-bottom: 12px;
  font-size: 24px;
  font-weight: normal;
  letter-spacing: 1px;
}

h1, h2, h3, p {
  margin-left: 24px;
}

h1 {
  margin-top: 0;
}

h2 {
  margin-top: 96px;
}

h3 {
  margin-top: 0;
  margin-bottom: 0;
}

:global(.mdc-drawer .mdc-list-item) {
  margin: 0 !important;
  padding-left: 12px !important;
  padding-right: 12px !important;
  border-radius: 0 !important;
}

:global(svg) {
  stroke-width: 0;
}

:global(.metro-btn) {
  padding-left: 8px;
  padding-right: 12px;
}

h2 a, h3 a {
  color: inherit;
  text-decoration: none;
}

h2 a:hover, h3 a:hover {
  text-decoration: underline;
}

:global(.mini-btn) {
  height: 40px !important;
  width: 40px !important;
  padding: 8px;
}

.link {
  color: var(--kick-color);
  text-decoration: none;
}

.link:hover {
  text-decoration: underline;
}

* :global(.mdc-list-item) {
  font-size: 15px !important;
  font-weight: normal !important;
}
</style>
