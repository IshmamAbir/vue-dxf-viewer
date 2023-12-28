<template></template>

<script setup>
import { DXFViewer } from "three-dxf-viewer";

let font = "fonts/helvetiker_regular.typeface.json";
let file = $event.target.files[0];
let dxf = await new DXFViewer().getFromFile(file, font);

screen.addDxf(dxf);
</script>
