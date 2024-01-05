<template>
  <div class="canvasContainer" ref="canvasContainer">
    dxf-template
    <!-- <v-inner-loading :showing="isLoading" color="primary" style="z-index: 10" /> -->
    <div v-if="progress !== null" class="progress">
      <v-progress-linear color="primary" :indeterminate="progress !== null" />
      <div v-if="progressText !== null" class="progressText">
        {{ progressText }}
      </div>
    </div>
    <div v-if="error !== null" class="error" :title="error">
      <v-icon icon="mdi-alert" class="text-error" style="font-size: 3rem" />
      Error occurred: {{ error }}
    </div>
  </div>
</template>

<script>
import { DxfViewer } from "dxf-viewer";
import * as THREE from "three";
import { isProxy, toRaw } from "vue";
// import DxfViewerWorker from "worker-loader!./DxfViewerWorker.js";

export default {
  name: "DxfViewer",

  props: {
    dxfUrl: {
      default: null,
    },
    options: {
      default() {
        return {
          // canvasWidth: 200,
          // canvasHeight: 200,
          // pointSize: 50,
          clearColor: new THREE.Color("white"),
          autoResize: true,
          colorCorrection: true,
          sceneOptions: {
            wireframeMesh: true,
          },
        };
      },
    },
  },

  data() {
    return {
      isLoading: false,
      progress: null,
      fonts: null,
      progressText: null,
      curProgressPhase: null,
      // dxfViewer: null,
      error: null,
    };
  },

  watch: {
    async dxfUrl(dxfUrl) {
      if (dxfUrl !== null) {
        await this.load(dxfUrl);
      } else {
        this.dxfViewer.Clear();
        this.error = null;
        this.isLoading = false;
        this.progress = null;
      }
    },
  },

  methods: {
    async load(url) {
      this.isLoading = true;
      this.error = null;
      try {
        await this.dxfViewer.Load({
          url: url,
        });
        // if (isProxy(this.dxfViewer)) {
        //   this.dxfViewer = toRaw(this.dxfViewer);
        // }
      } catch (error) {
        this.error = error.toString();
      } finally {
        this.isLoading = false;
        this.progressText = null;
        this.progress = null;
        this.curProgressPhase = null;
      }
    },

    GetViewer() {
      return this.dxfViewer;
    },
  },
  emits: ["dxf-loaded", "dxf-cleared", "dxf-message"],
  mounted() {
    var proxy = new DxfViewer(this.$refs.canvasContainer, this.options);
    this.dxfViewer = proxy;
    if (isProxy(this.dxfViewer)) {
      this.dxfViewer = toRaw(this.dxfViewer);
    }
    const subscribe = (eventName) => {
      this.dxfViewer.Subscribe(eventName, (e) =>
        this.$emit("dxf-" + eventName, e)
      );
    };
    for (const eventName of ["loaded", "cleared", "message"]) {
      subscribe(eventName);
    }
  },

  destroyed() {
    this.dxfViewer.Destroy();
    this.dxfViewer = null;
  },
};
</script>

<style scoped lang="less">
.canvasContainer {
  position: relative;
  width: 100%;
  height: 100%;
  min-width: 100px;
  min-height: 100px;

  .progress {
    position: absolute;
    z-index: 20;
    width: 90%;
    margin: 20px 5%;

    .progressText {
      margin: 10px 20px;
      font-size: 14px;
      color: #262d33;
      text-align: center;
    }
  }

  .error {
    width: 100%;
    height: 100%;
    position: absolute;
    z-index: 20;
    padding: 30px;

    img {
      width: 24px;
      height: 24px;
      vertical-align: middle;
      margin: 4px;
    }
  }
}
</style>
