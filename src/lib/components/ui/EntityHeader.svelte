<script lang="ts">
  import { Material } from 'mono-svelte'
  import Avatar from './Avatar.svelte'
  import PostBody from '../lemmy/post/PostBody.svelte'
  import LabelStat from './LabelStat.svelte'
  import { optimizeImageURL } from '../lemmy/post/helpers'

  export let avatar: string | undefined = undefined
  export let name: string
  export let bio: string | undefined = undefined
  export let banner: string | undefined = undefined
  export let stats: {
    name: string
    value: string
  }[] = []
</script>

<Material
  color="transparent"
  padding="none"
  rounding="xl"
  class="z-10 relative"
  {...$$restProps}
>
  {#if banner}
    <div class="max-h-36 overflow-visible">
      <img
        src={banner}
        class="w-full object-cover h-48 rounded-xl bg-white dark:bg-zinc-900"
        height="192"
        alt="User banner"
      />
    </div>
    <div
      class="absolute top-0 left-0 -z-10 object-cover h-full w-full rounded-xl
      overflow-hidden"
    >
      <img
        src={optimizeImageURL(banner, 64)}
        alt=""
        class="opacity-10 blur-2xl h-full w-full saturate-200"
      />
    </div>
  {/if}
  <div class="p-4 flex flex-col sm:flex-row gap-4">
    <div class="space-y-3 flex-1">
      <Avatar
        width={64}
        url={avatar}
        alt={name}
        class="ring-4 ring-slate-25 dark:ring-zinc-925 bg-slate-25 dark:bg-zinc-925"
      />
      <div>
        <span class="text-lg font-medium">
          {name}
        </span>
        <span
          class="flex items-center gap-0 text-sm text-slate-600 dark:text-zinc-400"
        >
          <slot name="nameDetail" />
        </span>
      </div>
      <div class="relative">
        {#if bio}
          <PostBody class="text-sm" view="list" body={bio} clickThrough />
        {/if}
      </div>
    </div>
    {#if $$slots.actions}
      <div class="space-y-3 flex flex-col" class:sm:pt-12={banner != undefined}>
        <slot name="actions" />
      </div>
    {/if}
  </div>
  <div class="space-y-3 p-4 pt-0">
    {#if stats.length > 0}
      <div class="text-sm flex flex-row flex-wrap gap-3">
        {#each stats as stat}
          <LabelStat content={stat.value} formatted label={stat.name} />
        {/each}
      </div>
    {/if}
    <slot />
  </div>
</Material>
