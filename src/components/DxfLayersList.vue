<template>
  <div class="root">
    <v-list dense>
      Layers
      <v-list-item v-if="layers !== null" tag="label">
        <v-checkbox :value="showAll" @input="_toggleAll" />

        <v-col> All layers </v-col>
      </v-list-item>
      <v-list-item
        v-if="layers !== null"
        v-for="layer in layers"
        :key="layer.name"
        tag="label"
      >
        <v-col>
          <v-icon :color="_getCssColor(layer.color)">mdi-label</v-icon>
        </v-col>
        <v-col>
          <v-checkbox
            :value="layer.isVisible"
            @input="(e) => _toggleLayer(layer, e)"
          />
        </v-col>
        <v-col>
          {{ layer.displayName }}
        </v-col>
      </v-list-item>
    </v-list>
  </div>
</template>

<script>
export default {
  name: "LayersList",

  props: {
    layers: {
      /* Expecting array of {name: string, color: number, isVisible: boolean} */
      type: Array,
      default: null,
    },
  },

  watch: {
    layers() {
      this.showAll = null;
    },
  },

  data() {
    return {
      showAll: null,
    };
  },

  methods: {
    _toggleLayer(layer, newState) {
      this.$emit("toggleLayer", layer, newState);
      this.showAll = null;
    },

    _toggleAll(newState) {
      this.showAll = newState;
      this.$emit("toggleAll", newState);
    },

    _getCssColor(value) {
      let s = value.toString(16);
      while (s.length < 6) {
        s = "0" + s;
      }
      return "#" + s;
    },
  },
};
</script>

<style scoped lang="less">
.root {
  height: 100%;
  max-height: 100%;
  width: 300px;
}
</style>
