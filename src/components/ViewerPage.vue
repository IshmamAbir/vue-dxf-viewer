<template>
  <div class="layersCol col-auto">
    <LayersList
      :layers="layers"
      @toggleLayer="_onToggleLayer"
      @toggleAll="_onToggleAll"
    />
  </div>

  <v-divider />

  <div class="col relative-position">
    <DxfTemplate
      ref="viewer"
      :dxfUrl="dxfUrl"
      @dxf-loaded="_onLoaded"
      @dxf-cleared="_onCleared"
      @dxf-message="_onMessage"
    />
  </div>
</template>

<script>
import DxfTemplate from "./DxfTemplate.vue";
import { DxfViewer as _DxfViewer } from "dxf-viewer";
import LayersList from "./DxfLayersList.vue";
export default {
  name: "ViewerPage",
  components: {
    DxfTemplate,
    LayersList,
  },
  props: {
    dxfUrl: String,
  },
  data() {
    return {
      layers: null,
    };
  },

  methods: {
    _onLoaded() {
      const layers = this.$refs.viewer.GetViewer().GetLayers();
      layers.forEach((lyr) => (lyr["isVisible"] = true));
      this.layers = layers;
    },

    _onCleared() {
      this.layers = null;
    },

    _onToggleLayer(layer, newState) {
      layer.isVisible = newState;
      this.$refs.viewer.GetViewer().ShowLayer(layer.name, newState);
    },

    _onToggleAll(newState) {
      if (this.layers) {
        for (const layer of this.layers) {
          if (layer.isVisible !== newState) {
            this._onToggleLayer(layer, newState);
          }
        }
      }
    },

    _onMessage(e) {
      let type = "info";
      switch (e.detail.level) {
        case _DxfViewer.MessageLevel.WARN:
          type = "warning";
          break;
        case _DxfViewer.MessageLevel.ERROR:
          type = "negative";
          break;
      }
      console.warn(type, e.detail.message);
    },
  },
};
</script>
