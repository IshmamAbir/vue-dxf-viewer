<template>
  <v-app>
    <v-container>
      <v-file-input
        v-if="selectedFile === null"
        accept=".dxf"
        density="compact"
        v-model="selectedFile"
        label="Select a DXF file"
        @update:modelValue="onFileChange"
        @click:clear="onFileClear"
      ></v-file-input>

      <!-- <viewer-page :dxfData="selectedFile"></viewer-page> -->
      <viewer-page :dxf-url="dxfUrl"></viewer-page>
    </v-container>
  </v-app>
</template>

<script>
import ViewerPage from "./components/ViewerPage.vue";
export default {
  name: "App",
  data() {
    return {
      selectedFile: null,
      dxfUrl: null,
      inputFile: null,
      inputUrl: null,
    };
  },
  components: {
    ViewerPage,
  },
  methods: {
    onFileClear() {
      if (this.inputFile) {
        this.inputFile = null;
        URL.revokeObjectURL(this.dxfUrl);
        this.dxfUrl = null;
        this.$vuetify.notify({
          type: "info",
          message: "File cleared",
        });
      }
    },
    onFileChange(file) {
      if (!file) {
        this._OnFileCleared();
        return;
      }
      if (this.dxfUrl) {
        URL.revokeObjectURL(this.dxfUrl);
      }

      this.inputFile = file[0];
      this.dxfUrl = URL.createObjectURL(this.inputFile);
    },
  },
  destroyed() {
    if (this.dxfUrl) {
      URL.revokeObjectURL(this.dxfUrl);
    }
  },
};
</script>
