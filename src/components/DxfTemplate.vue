<template>
  <v-container class="canvasContainer" ref="canvasContainer">
    <!-- <v-inner-loading :showing="isLoading" color="primary" style="z-index: 10" /> -->
    <div v-if="progress !== null" class="progress">
      <v-progress-linear
        color="primary"
        :indeterminate="progress < 0"
        :value="progress"
      />
      <div v-if="progressText !== null" class="progressText">
        {{ progressText }}
      </div>
    </div>
    <div v-if="error !== null" class="error" :title="error">
      <v-icon name="warning" class="text-red" style="font-size: 4rem" /> Error
      occurred: {{ error }}
    </div>
  </v-container>
</template>

<script>
import { DxfViewer } from "dxf-viewer";
import * as three from "three";
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
          clearColor: new three.Color("#fff"),
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
      dxfViewer: null,
      error: null,
    };
  },

  watch: {
    async dxfUrl(dxfUrl) {
      if (dxfUrl !== null) {
        console.log("watch dxfurl => ", dxfUrl);
        await this.load(dxfUrl);
      } else {
        this.dxfViewer.clear();
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
          url,
          fonts: this.fonts,
          progressCbk: this.onProgress.bind(this),
          // workerFactory: DxfViewerWorker,
        });
        console.log("try dxfviewer-> ", this.dxfViewer);
      } catch (error) {
        console.warn(error);
        this.error = error.toString();
      } finally {
        this.isLoading = false;
        this.progressText = null;
        this.progress = null;
        this.curProgressPhase = null;
      }
    },

    getViewer() {
      return this.dxfViewer;
    },

    onProgress(phase, size, totalSize) {
      console.log("onProgress phase=> ", phase);
      if (phase !== this.curProgressPhase) {
        switch (phase) {
          case "font":
            this.progressText = "Fetching fonts...";
            break;
          case "fetch":
            this.progressText = "Fetching file...";
            break;
          case "parse":
            this.progressText = "Parsing file...";
            break;
          case "prepare":
            this.progressText = "Preparing rendering data...";
            break;
        }
        this.curProgressPhase = phase;
      }
      if (totalSize === null) {
        this.progress = -1;
      } else {
        this.progress = size / totalSize;
      }
    },
  },

  mounted() {
    this.dxfViewer = new DxfViewer(this.$refs.canvasContainer, this.options);
    // this.dxfViewer = new DxfViewer(this.$refs.canvasContainer);
    console.log("dxfviewer- ", this.dxfViewer);
    const subscribe = (eventName) => {
      this.dxfViewer.Subscribe(eventName, (e) =>
        this.$emit("dxf-" + eventName, e)
      );
    };
    for (const eventName of [
      "loaded",
      "cleared",
      "destroyed",
      "resized",
      "pointerdown",
      "pointerup",
      "viewChanged",
      "message",
    ]) {
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
