<template>
  <v-container>
    Layers List
    <v-row v-if="layers !== null">
      <v-col>
        <v-checkbox
          v-model="showAll"
          @update:model-value="_toggleAll"
          label="All Layers"
        ></v-checkbox>
      </v-col>
    </v-row>

    <v-row v-if="layers !== null">
      <v-col
        v-for="layer in layers"
        :key="layer.name"
        md="3"
        lg="3"
        sm="4"
        xs="6"
      >
        <!-- <v-icon :color="_getCssColor(layer.color)">mdi-label</v-icon> -->
        <v-checkbox
          density="compact"
          v-model="layer.isVisible"
          @update:model-value="(e) => _toggleLayer(layer, e)"
          :label="layer.displayName"
        >
          <template v-slot:prepend>
            <v-icon :color="_getCssColor(layer.color)" class="pl-5"
              >mdi-label</v-icon
            >
          </template>
        </v-checkbox>
      </v-col>
    </v-row>
  </v-container>
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

  data() {
    return {
      showAll: true,
    };
  },
  emits: ["toggleLayer", "toggleAll"],
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
