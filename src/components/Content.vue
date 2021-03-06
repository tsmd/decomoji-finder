<template>
  <VContent class="Content">
    <div
      :class="[
        '__list',
        {
          '-basic': ui.category.basic,
          '-explicit': ui.category.explicit,
          '-extra': ui.category.extra
        },
        `-${ui.iconSize}`
      ]"
    >
      <template v-for="category in categories">
        <div
          v-for="name in decomojis[category]"
          v-show="matched(name)"
          :key="name"
          :class="[
            '__item',
            `-${category}`,
            `-${ui.iconSize}`,
            { '-reacted': ui.reacted }
          ]"
        >
          <img
            :alt="name"
            :src="`/decomoji/${category}/${name}.png`"
            width="64"
            height="64"
            :class="`__icon -${ui.iconSize}`"
          />
          <p v-show="nameShows" class="__name">:{{ name }}:</p>
        </div>
      </template>
    </div>
  </VContent>
</template>

<script lang="ts">
import { DecomojiBasic } from '@/configs/DecomojiBasic'
import { DecomojiExplicit } from '@/configs/DecomojiExplicit'
import { DecomojiExtra } from '@/configs/DecomojiExtra'
import { DefaultIconSize } from '@/configs/DefaultIconSize'
import { CategoryId } from '@/models/CategoryId'
import { DecomojiItem } from '@/models/DecomojiItem'
import { UiViewModel } from '@/store/modules/ui/models'
import { Component, Vue } from 'vue-property-decorator'
import { Getter } from 'vuex-class'

@Component
export default class Content extends Vue {
  // viewModel を引き当てる
  @Getter('ui/viewModel') ui!: UiViewModel

  categories: CategoryId[] = ['basic', 'explicit', 'extra']

  decomojis = Object.freeze({
    basic: DecomojiBasic as DecomojiItem[],
    extra: DecomojiExtra as DecomojiItem[],
    explicit: DecomojiExplicit as DecomojiItem[]
  })

  /**
   * @get - ファイル名を表示するか否かを返す
   */
  get nameShows() {
    return this.ui.name && this.ui.iconSize === DefaultIconSize
  }

  /**
   * @method - 要素が検索クエリを正規表現にマッチするか否かを返す
   */
  matched(name: string) {
    try {
      return RegExp(this.ui.searchQuery).test(name)
    } catch (err) {
      console.log(err)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~vuetify/src/stylus/settings/_colors'
@import '~vuetify/src/stylus/settings/_variables'

.Content
  .__list
    display: grid
    grid-auto-rows: auto
    padding: 10px
    overflow-x: hidden
    &.-basic .__item.-basic
    &.-extra .__item.-extra
    &.-explicit .__item.-explicit
      display: block
    &.-l
      gap: 10px
      grid-template-columns: repeat(auto-fill, minmax(128px, 1fr))
    &.-m
      gap: 5px
      grid-template-columns: repeat(auto-fill, minmax(42px, 1fr))
    &.-s
      gap: 3px
      grid-template-columns: repeat(auto-fill, minmax(24px, 1fr))

  .__item
    display: none
    border: 1px solid transparent
    border-radius: 4px
    line-height: 1
    text-align: center
    &.-l
      padding: 10px
    &.-m
      padding: 5px
    &.-s
      padding: 3px

    .theme--light &
      background-color: rgb(245,244,245)
      &.-reacted
        border-color: #1d9bd1
        background-color: #ebf5fb
    .theme--dark &
      background-color: rgb(30,32,34)
      &.-reacted
        border-color: #1264a3
        background-color: #1264a3

  .__icon
    vertical-align: top
    &.-m
      width: 32px
      height: 32px
    &.-s
      width: 16px
      height: 16px

  .__name
    margin-top: 10px
    margin-bottom: 0
    line-height: 1.2
    word-break: break-all
</style>
