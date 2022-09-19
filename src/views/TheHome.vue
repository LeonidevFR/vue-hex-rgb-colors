<template>
  <div class="w-full min-h-[150px]">
    <div
      class="w-full h-[600px] flex flex-col justify-start items-center pt-20"
    >
      <div class="text-2xl text-amber-400 mb-16">
        I want to convert
        <select v-model="fromValue" class="bg-transparent">
          <option value="hex" selected>Hex</option>
          <option value="rgb">Rgb</option>
        </select>
        to :
        <select v-model="toValue" class="bg-transparent">
          <option value="rgb" selected>Rgb</option>
          <option value="hex">Hex</option>
        </select>
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
        >Converted Hex : {{ convertedColor }}</span
      >
      <span class="text-xl text-amber-400">{{ colorPickerColor }}</span>
      <input
        type="color"
        v-model="colorPickerColor"
        class="w-[150px] h-[150px]"
      />
    </div>
  </div>
</template>
<script setup>
import { ref, watch } from "vue";

const userInput = ref("#");

watch(userInput, () => {
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
      console.log("input", input);
      userInput.value = input.join("");
    }
  }
});

const convertedColor = ref("#000");
const colorPickerColor = ref("#000");
const fromValue = ref("hex");
const toValue = ref("rgb");

function convertColors() {
  if (
    fromValue.value.toLowerCase() === "hex" &&
    toValue.value.toLowerCase() === "rgb" &&
    (userInput.value.split("#")[1].length === 3 ||
      userInput.value.split("#")[1].length === 6)
  ) {
    hexToRgb();
  } else {
    convertedColor.value = "#000";
    colorPickerColor.value = "#000";
  }
}

//TODO: handle opacity
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
</script>
<style lang=""></style>
