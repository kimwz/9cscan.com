<template>
  <v-menu offset-y v-if="items && value" v-model="isActive">
    <template v-slot:activator="{on, attrs}">
      <v-btn class="menu-down-btn" outlined color="grayButton" rounded v-bind="attrs" v-on="on">
        Season {{ currentItem.season }} - {{ currentItem.arenaType }}
        <v-icon color="grayButton" class="ml-1" small>mdi-chevron-down</v-icon>
      </v-btn>
    </template>

    <div style="background-color: white">
      <v-list class="arena-menu-down-list">
        <v-list-item v-for="(item, index) in items" :key="index" @click="select(item)">
          <v-list-item-title v-html="`Season ${item.season} - ${item.arenaType}${item.isCurrent ? ' (Current)' : ''}`"></v-list-item-title>
        </v-list-item>
      </v-list>
    </div>
  </v-menu>
</template>

<script>
import api from "@/api";

export default {
  name: 'ArenaSelect',
  components: {},
  mixins: [],
  data() {
    return {
      isActive: false,
      items: null,
      value: null,
    };
  },
  computed: {
    currentItem() {
      return this.items.find((item) => item.id === this.value);
    }
  },
  beforeDestroy() {
  },
  async created() {
    const defaultValue = this.$route.query.arena;
    const result = await api.getArenaSeasons();

    this.items = result.map((item) => ({ ...item, id: `${item.championshipId}-${item.round}` }));
    this.value = defaultValue || this.items[0].id;
    this.$emit('change', this.items.find((item) => item.id === this.value));
  },
  methods: {
    select(item) {
      if (item.id === this.value) return;

      this.value = item.id;
      this.$emit('change', item);
    }
  }
}
</script>

<style scoped lang="scss">
.arena-menu-down-list {
  max-height: 500px;
  background-color: #fff !important;
  .v-list-item {
    min-height: 36px;
    &:hover {
      background-color: #eee;
    }
    .v-list-item__icon {
      padding: 0px;
      margin: 6px 8px 0px -4px;
    }
    .v-list-item__title {
      color: #333 !important;
      text-align: left;
      font-size: 14px;

      b {
        color: var(--v-point-base);
      }
    }
  }
}
</style>
