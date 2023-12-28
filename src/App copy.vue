<template>
  <v-app>
    <v-container>
      <v-row>
        <v-col>
          <v-card>
            <v-card-title>DXF Viewer</v-card-title>
            <v-card-text>
              <v-file-input
                v-model="selectedFile"
                label="Select a DXF file"
                @change="onFileChange"
              ></v-file-input>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <v-row v-if="parsedData">
        <v-col>
          <v-card>
            <v-card-title>Parsed DXF Data</v-card-title>
            <v-card-text>
              <v-list>
                <v-list-item-group>
                  <v-list-item v-for="(value, key) in parsedData" :key="key">
                    <v-list-item-content>
                      <v-list-item-title>{{ key }}</v-list-item-title>
                      <v-list-item-subtitle>{{ value }}</v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-list>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import DxfParser from "dxf-parser";

export default {
  data() {
    return {
      selectedFile: null,
      parsedData: null,
    };
  },
  methods: {
    onFileChange(event) {
      console.log("Selected File:", event.target.files[0]);
      const file = event.target.files[0];
      if (file) {
        this.loadDxfFile(file);
      }
    },

    loadDxfFile(file) {
      const parser = new DxfParser();
      const reader = new FileReader();

      reader.onload = (event) => {
        const dxfData = parser.parseSync(event.target.result);
        this.parsedData = dxfData;
      };

      reader.readAsText(file);
    },
  },
};
</script>

<style>
/* Add your styles here */
</style>
