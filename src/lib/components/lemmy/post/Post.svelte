<script lang="ts">
  import type { PostView } from 'lemmy-js-client'
  import { isImage, isVideo } from '$lib/ui/image.js'
  import { getInstance } from '$lib/lemmy.js'
  import PostActions from '$lib/components/lemmy/post/PostActions.svelte'
  import { userSettings } from '$lib/settings.js'
  import PostLink from '$lib/components/lemmy/post/PostLink.svelte'
  import PostMeta from '$lib/components/lemmy/post/PostMeta.svelte'
  import { Material, toast } from 'mono-svelte'
  import Markdown from '$lib/components/markdown/Markdown.svelte'
  import ExpandableImage from '$lib/components/ui/ExpandableImage.svelte'
  import {
    bestImageURL,
    mediaType,
    postLink,
  } from '$lib/components/lemmy/post/helpers.js'
  import Empty from '$lib/components/helper/Empty.svelte'
  import { publishedToDate } from '$lib/components/util/date.js'
  import { Icon, VideoCamera } from 'svelte-hero-icons'
  import PostMedia from '$lib/components/lemmy/post/media/PostMedia.svelte'
  import PostMediaCompact from '$lib/components/lemmy/post/media/PostMediaCompact.svelte'
  import PostBody from './PostBody.svelte'
  import { profile } from '$lib/auth'
  import { goto } from '$app/navigation'

  export let post: PostView
  export let actions: boolean = true
  export let hideCommunity = false
  export let view = $userSettings.view

  $: type = mediaType(post.post.url, view)
  $: hideTitle =
    $userSettings.posts.deduplicateEmbed &&
    post.post.embed_title == post.post.name

  $: hideBody =
    $userSettings.posts.deduplicateEmbed &&
    post.post.embed_description == post.post.body
</script>

<Material
  color={view != 'card' ? 'none' : 'distinct'}
  padding="none"
  class="relative max-w-full min-w-0 w-full test group gap-2 flex flex-col
  {view != 'card' ? 'bg-transparent !border-0' : 'p-5'} {view == 'compact'
    ? 'py-4 list-type'
    : view == 'list'
      ? 'py-5 list-type'
      : 'py-5'} {$$props.class}"
  id={post.post.id}
>
  <PostMeta
    community={hideCommunity ? undefined : post.community}
    user={post.creator}
    published={publishedToDate(post.post.published)}
    badges={{
      deleted: post.post.deleted,
      removed: post.post.removed,
      locked: post.post.locked,
      featured: post.post.featured_local || post.post.featured_community,
      nsfw: post.post.nsfw,
      saved: post.saved,
      admin: post.creator_is_admin,
      moderator: post.creator_is_moderator,
    }}
    subscribed={$profile?.user?.follows
      .map((c) => c.community.id)
      .includes(post.community.id)
      ? 'Subscribed'
      : 'NotSubscribed'}
    id={post.post.id}
    title={hideTitle ? undefined : post.post.name}
    read={post.read}
    style="grid-area: meta;"
  >
    <slot name="badges" slot="badges" />
  </PostMeta>
  <div
    style="grid-area:embed"
    class={view == 'list' || view == 'compact' ? '' : 'contents'}
  >
    <PostMedia bind:post={post.post} {view} {type} />
  </div>
  {#if view == 'list' || view == 'compact'}
    <PostMediaCompact {view} bind:post={post.post} style="grid-area: media;" />
  {/if}
  {#if post.post.body && !post.post.nsfw && view != 'compact' && !hideBody}
    <PostBody body={post.post.body} {view} style="grid-area: body" />
  {/if}
  {#if actions}
    <PostActions
      bind:post
      on:edit={(e) => {
        toast({
          content: 'The post was edited successfully.',
          type: 'success',
        })
      }}
      style="grid-area: actions;"
    />
  {/if}
</Material>

<style>
  :global(.list-type) {
    display: grid;
    gap: 0.5rem;
    grid-template-areas: var(
      --template-areas,
      'meta media' 'title media' 'body media' 'embed embed' 'actions actions'
    );
    width: 100%;
    height: 100%;
    grid-auto-rows: minmax(-0.5rem, auto);
    grid-template-columns: var(--template-columns, 1fr auto);
  }
</style>
