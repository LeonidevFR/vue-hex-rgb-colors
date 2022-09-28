<template>
  <div class="w-full min-h-[150px]">
    <div
      class="w-full min-h-[600px] flex flex-col justify-start items-center pt-20"
    >
      <div class="text-2xl text-amber-400 mb-16">
        I want to convert
        <select v-model="fromValue" class="bg-transparent">
          <option value="hex" selected>Hex</option>
          <option value="rgb">Rgb</option>
        </select>
        to
        <span>{{ toValue }}</span>
      </div>
      <form
        class="flex flex-col justify-start items-center"
        @submit.prevent="convertColors"
      >
        <input
          v-model="userInput"
          type="text"
          placeholder="Hex to convert"
          class="text-2xl text-amber-400 bg-transparent border-2 border-amber-400 rounded-lg mb-10 px-4 h-[50px] focus:border-transparent focus:outline-none focus:border-amber-900"
        />
        <input type="submit" class="btn-primary w-40 mb-10" value="Convert !" />
      </form>
      <span class="text-2xl text-amber-400 mb-10"
        >Converted {{ toValue }} : {{ convertedColor }}</span
      >
      <span class="text-xl text-amber-400">{{ colorPickerColor }}</span>
      <input
        v-model="colorPickerOpacity"
        class="w-[250px] mb-4"
        type="range"
        min="0"
        max="1"
        step="0.1"
      />
      <div class="border-2 border-amber-400 rounded-lg mb-10 bg-white">
        <input
          type="color"
          v-model="colorPickerColor"
          class="w-[250px] h-[250px]"
          :style="{ opacity: colorPickerOpacity }"
        />
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, watch } from "vue";

const userInput = ref("#");

watch(userInput, () => {
  //If conversion is from Hex to RGB
  if (fromValue.value === "hex") {
    if (userInput.value.length === 0) {
      userInput.value = "#";
    }

    if (!userInput.value.includes("#")) {
      userInput.value = "#" + userInput.value;
    }

    if (userInput.value.includes("#")) {
      if (userInput.value.split("")[0] !== "#") {
        let input = userInput.value.split("").filter((el) => {
          return el !== "#";
        });
        input.unshift("#");
        userInput.value = input.join("");
      }
    }
    //If conversion is from RGB to Hex
  } else if (fromValue.value === "rgb") {
    if (!userInput.value.includes("rgb")) {
      let input = userInput.value.split("").filter((el) => {
        return el !== "(" && el !== ")";
      });
      if (userInput.value.length === 0) {
        userInput.value = "rgb(0,0,0)";
      } else {
        userInput.value = "rgb(" + input.join("") + ")";
      }
    }
  }
});

const convertedColor = ref("#000");
const colorPickerColor = ref("#000");
const colorPickerOpacity = ref("1");
const fromValue = ref("hex");
const toValue = ref("rgb");

watch(fromValue, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    userInput.value = "";
  }
  if (newVal === "rgb") {
    toValue.value = "hex";
  }
  if (newVal === "hex") {
    toValue.value = "rgb";
  }
});

function convertColors() {
  if (
    fromValue.value.toLowerCase() === "hex" &&
    toValue.value.toLowerCase() === "rgb" &&
    (userInput.value.split("#")[1].length === 3 ||
      userInput.value.split("#")[1].length === 6)
  ) {
    hexToRgb();
  } else if (
    fromValue.value.toLowerCase() === "rgb" &&
    toValue.value.toLowerCase() === "hex"
  ) {
    rgbToHex();
  } else {
    convertedColor.value = "#000";
    colorPickerColor.value = "#000";
  }
}

function hexToRgb() {
  let formattedHex = userInput.value.replace("#", "").split("");

  if (formattedHex.length === 3) {
    for (let i = 0; i < 3; i++) {
      formattedHex[i] = formattedHex[i] + formattedHex[i];
    }

    colorPickerColor.value = `#${formattedHex.join("")}`;

    for (let i = 0; i < 3; i++) {
      formattedHex[i] = parseInt(formattedHex[i], 16);
    }

    convertedColor.value = `rgb(${formattedHex.join(", ")})`;
  } else {
    let newFormattedHex = [];
    for (let i = 0; i < 3; i++) {
      let index = i * 2;
      newFormattedHex[i] = formattedHex[index] + formattedHex[index + 1];
    }

    for (let i = 0; i < 3; i++) {
      newFormattedHex[i] = parseInt(newFormattedHex[i], 16);
    }

    convertedColor.value = `rgb(${newFormattedHex.join(", ")})`;
    colorPickerColor.value = userInput.value;
  }
}

function rgbToHex() {
  let filteredRgb = userInput.value.split(",");
  filteredRgb[0] = filteredRgb[0].replace("rgb(", "");
  filteredRgb[2] = filteredRgb[2].replace(")", "");

  for (let i = 0; i < 3; i++) {
    let hex = parseInt(filteredRgb[i]).toString(16);
    if (hex.length < 2) {
      filteredRgb[i] = "0" + hex;
    } else {
      filteredRgb[i] = hex;
    }
  }

  let hex = `#${filteredRgb[0]}${filteredRgb[1]}${filteredRgb[2]}`;

  convertedColor.value = hex;
  colorPickerColor.value = hex;
}
</script>
<style lang=""></style>
