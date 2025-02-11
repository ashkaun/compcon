<template>
  <v-container fluid px-5>
    <v-card>
      <v-card-title
        primary-title
        :style="{ backgroundColor: colors[item.ItemType.toLowerCase()].light }"
        class="white--text"
      >
        <div class="headline">{{ item.name }}</div>
        <v-chip
          disabled
          outline
          label
          color="white"
          class="text-uppercase ml-4"
        >
          {{ item.ItemType }}
          <span v-if="item.ItemType === 'WeaponMod'">
            &nbsp;– {{ item.applied_string }}
          </span>
        </v-chip>
        <v-chip
          v-if="item.source === 'GMS'"
          disabled
          outline
          label
          color="white"
          class="text-uppercase ml-2"
        >
          {{ item.source }}
        </v-chip>
        <v-chip
          v-else
          disabled
          outline
          label
          color="white"
          class="text-uppercase ml-2"
        >
          {{ item.source }} {{ item.license }}
          <span v-if="item.license_level">
            &nbsp;RANK {{ item.license_level }}
          </span>
          <span v-if="item.ItemType === 'Frame'">&nbsp;RANK 2</span>
        </v-chip>
      </v-card-title>
      <v-card-text>
        <p
          class="fluff-text"
          v-if="item.description"
          v-html="item.description"
        ></p>
        <p v-if="item.effect" class="effect-text" v-html="item.effect"></p>
      </v-card-text>
      <template v-if="item.ItemType === 'Frame'">
        <v-divider />
        <v-card-text>
          <v-subheader class="mb-0">Frame</v-subheader>
          <frame-statblock :frame="item" />
        </v-card-text>
      </template>
      <template v-if="item.ItemType === 'MechWeapon'">
        <v-divider />
        <v-card-text class="pt-0">
          <v-subheader class="mb-0">Weapon</v-subheader>
          <v-layout mb-2 py-2 px-1 align-center>
            <div class="pl-2 subheading">{{ item.mount }} {{ item.type }}</div>
            <v-divider vertical class="mx-3" />
            <range-element :range="item.range" />
            <v-divider vertical class="mx-3" />
            <damage-element :dmg="item.damage" />
          </v-layout>
        </v-card-text>
      </template>
      <template v-if="item.tags && item.tags.length">
        <v-divider />
        <v-card-text class="pt-0">
          <v-subheader class="mb-0">Tags</v-subheader>
          <v-layout class="pb-2">
            <item-tag
              v-for="(t, index) in item.tags"
              :key="t.id + index"
              :tag-obj="t"
            />
          </v-layout>
        </v-card-text>
      </template>
    </v-card>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import _ from 'lodash'
import { getStatic } from '@/mixins/static'
import {
  ItemTag,
  RangeElement,
  DamageElement,
  FrameStatblock,
} from '@/features/pilot_management/components/UI'
import colors from '@/features/_shared/UI/CCColors'
import { CompendiumItem } from '@/class'

export default Vue.extend({
  name: 'compendium-item',
  components: { ItemTag, RangeElement, DamageElement, FrameStatblock },
  data: () => ({
    colors: colors,
  }),
  computed: {
    validResults(): CompendiumItem[] {
      return _.flatten(
        _.values(
          _.pick(this.$store.state.datastore as object, [
            'Frames',
            'MechSystems',
            'MechWeapons',
            'WeaponMods',
          ])
        )
      )
    },
    item(): CompendiumItem | undefined {
      const { type, id } = this.$route.params
      return this.validResults.find(i => i.ItemType === type && i.ID === id)
    },
  },
  methods: {
    getStaticPath(path: string) {
      return getStatic(path)
    },
  },
})
</script>
