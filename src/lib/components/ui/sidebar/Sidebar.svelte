<script lang="ts">
  import {
    ArrowLeftOnRectangle,
    ChevronDoubleLeft,
    Cog6Tooth,
    Home,
    Icon,
    Plus,
    UserCircle,
  } from 'svelte-hero-icons'
  import Button from '../../input/Button.svelte'
  import Avatar from '$lib/components/ui/Avatar.svelte'
  import { profile, profileData, setUserID } from '$lib/auth.js'
  import { goto } from '$app/navigation'
  import { page } from '$app/stores'
  import { slide } from 'svelte/transition'
  import SidebarButton from '$lib/components/ui/sidebar/SidebarButton.svelte'
  import CommunityList from '$lib/components/ui/sidebar/CommunityList.svelte'

  let expanded = true
</script>

<nav
  class="hidden sm:flex flex-col pl-4 pr-4 py-4 overflow-auto sticky top-16 bottom-0
  gap-1 max-h-[calc(100vh-4rem)] w-full bg-slate-100 dark:bg-black
  {expanded
    ? 'max-w-[25%] resize-x min-w-[12rem]'
    : 'w-max max-w-max min-w-max'}"
>
  <Button
    on:click={() => (expanded = !expanded)}
    class="w-max !p-2 hover:bg-slate-200"
  >
    <Icon
      src={ChevronDoubleLeft}
      mini
      size="16"
      class="transition-transform {expanded ? '' : 'rotate-180'}"
      title="Toggle expanded"
    />
  </Button>
  <SidebarButton href="/" {expanded}>
    <Icon src={Home} solid size="20" title="Frontpage" />
    <span class:hidden={!expanded}>Frontpage</span>
  </SidebarButton>
  <SidebarButton href="/settings" {expanded}>
    <Icon src={Cog6Tooth} solid size="20" title="Settings" />
    <span class:hidden={!expanded}>Settings</span>
  </SidebarButton>
  {#if $profileData.profiles.length >= 1}
    <hr class="border-slate-300 dark:border-zinc-800 my-1" />
    {#each $profileData.profiles as prof, index}
      <Button
        color="tertiary"
        alignment="left"
        on:click={() => {
          setUserID(prof.id)
          goto($page.url, {
            invalidateAll: true,
          })
        }}
        class="hover:bg-slate-200 {expanded ? '' : '!p-1.5'} {$profile?.id ==
        prof.id
          ? 'font-bold'
          : ''}"
      >
        <Icon
          src={UserCircle}
          mini={$profile?.id == prof.id}
          size="20"
          title={prof.username}
          class="text-blue-500"
          style="filter: hue-rotate({index * 50}deg)"
        />
        <span class:hidden={!expanded} class="flex flex-col gap-0">
          {prof.username}
          <span class="text-slate-500 dark:text-zinc-400 font-normal text-xs">
            {prof.instance}
          </span>
        </span>
      </Button>
    {/each}
  {/if}
  <hr class="border-slate-300 dark:border-zinc-800 my-1" />
  {#if $profile?.user}
    {#if $profile?.user.moderates.length > 0}
      <CommunityList
        {expanded}
        items={$profile.user.moderates.map((i) => i.community)}
      />
      <hr class="border-slate-300 dark:border-zinc-800 my-1" />
    {/if}

    <CommunityList
      {expanded}
      items={$profile.user.follows.map((i) => i.community)}
    />
    {#if $profile.user.follows.length == 0}
      <Button
        class="hover:bg-slate-200 {expanded ? '' : '!p-1.5'}"
        href="/communities"
        color="tertiary"
        alignment="left"
      >
        <Icon src={Plus} solid size="20" />
        <span class:hidden={!expanded}>Add communities</span>
      </Button>
    {/if}
  {:else}
    <Button
      class="hover:bg-slate-200 {expanded ? '' : '!p-1.5'}"
      href="/accounts"
      color="tertiary"
      alignment="left"
    >
      <Icon src={ArrowLeftOnRectangle} solid size="20" />
      <span class:hidden={!expanded}>Log in</span>
    </Button>
  {/if}
</nav>