<script>
import List, { Item, Text, Separator, Subheader, Meta } from '@smui/list'

export let groups = []
export let active = {}
</script>

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

<style>
:global(.mdc-drawer .mdc-list-item) {
  font-size: 15px !important;
  font-weight: normal !important;
  margin: 0 !important;
  padding-left: 12px !important;
  padding-right: 12px !important;
  border-radius: 0 !important;
}
</style>
