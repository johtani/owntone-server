<template>
  <div class="fd-page-with-tabs">
    <tabs-audiobooks />
    <content-with-heading>
      <template #options>
        <index-button-list :indices="albums.indices" />
      </template>
      <template #heading-left>
        <p class="title is-4" v-text="$t('page.audiobooks.albums.title')" />
        <p
          class="heading"
          v-text="$t('page.audiobooks.albums.count', { count: albums.count })"
        />
      </template>
      <template #content>
        <list-albums :albums="albums" />
      </template>
    </content-with-heading>
  </div>
</template>

<script>
import { GroupedList } from '@/lib/GroupedList'
import ContentWithHeading from '@/templates/ContentWithHeading.vue'
import IndexButtonList from '@/components/IndexButtonList.vue'
import ListAlbums from '@/components/ListAlbums.vue'
import TabsAudiobooks from '@/components/TabsAudiobooks.vue'
import webapi from '@/webapi'

const dataObject = {
  load(to) {
    return webapi.library_albums('audiobook')
  },

  set(vm, response) {
    vm.albums = new GroupedList(response.data, {
      index: { field: 'name_sort', type: String }
    })
  }
}

export default {
  name: 'PageAudiobooksAlbums',
  components: {
    TabsAudiobooks,
    ContentWithHeading,
    IndexButtonList,
    ListAlbums
  },

  beforeRouteEnter(to, from, next) {
    dataObject.load(to).then((response) => {
      next((vm) => dataObject.set(vm, response))
    })
  },

  beforeRouteUpdate(to, from, next) {
    const vm = this
    dataObject.load(to).then((response) => {
      dataObject.set(vm, response)
      next()
    })
  },

  data() {
    return {
      albums: new GroupedList()
    }
  }
}
</script>

<style></style>
